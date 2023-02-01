# 预选与优选

上一篇中我们分析了 `Schedule()` 的基本结构，主要分四步。我们这次就来分析其中最重要的第二步和第三步：预选与优选。

## g.findNodesThatFitPod

`g.findNodesThatFitPod()` 就是第二步的预选过程，函数签名有点长，主要是接受一个 pod ，返回 node 列表：

```go 
func (g *genericScheduler) findNodesThatFitPod(
    ctx context.Context, 
    extenders []framework.Extender, 
    fwk framework.Framework, 
    state *framework.CycleState, 
    pod *v1.Pod，
) ([]*v1.Node, framework.Diagnosis, error)
```

我们前一篇里说，第二步负责把第一步得到的 node 进行过滤，可乍眼一看好像没输入任何 node 信息啊？别慌，其实是直接从实例成员里拿到了：

```go
allNodes, err := g.nodeInfoSnapshot.NodeInfos().List()
```

我们看到这个 `g.nodeInfoSnapshot` 里就是第一步从 cache 里保存的信息。

其实这个 `findNodesThatFitPod()` 并不复杂，我们把它简化一下：

```go ./generic_scheduler.go
// Run "prefilter" plugins.
s := fwk.RunPreFilterPlugins(ctx, state, pod)
allNodes, err := g.nodeInfoSnapshot.NodeInfos().List()

// "NominatedNodeName" can potentially be set in a previous scheduling cycle as a result of preemption.
// This node is likely the only candidate that will fit the pod, and hence we try it first before iterating over all nodes.
if len(pod.Status.NominatedNodeName) > 0 && feature.DefaultFeatureGate.Enabled(features.PreferNominatedNode) {
    feasibleNodes, err := g.evaluateNominatedNode(ctx, extenders, pod, fwk, state, diagnosis)
}

feasibleNodes, err := g.findNodesThatPassFilters(ctx, fwk, state, pod, diagnosis, allNodes)

feasibleNodes, err = findNodesThatPassExtenders(extenders, pod, feasibleNodes, diagnosis.NodeToStatusMap)

return feasibleNodes, diagnosis, nil
```

每步大概在做什么其实函数名都写得很清楚了。可以知道预选最重要的一步就是 `g.findNodesThatPassFilters()` 。我们直接点进去看：

```go ./generic_scheduler.go
// findNodesThatPassFilters finds the nodes that fit the filter plugins.
func (g *genericScheduler) findNodesThatPassFilters(
	ctx context.Context,
	fwk framework.Framework,
	state *framework.CycleState,
	pod *v1.Pod,
	diagnosis framework.Diagnosis,
	nodes []*framework.NodeInfo) ([]*v1.Node, error) {
	numNodesToFind := g.numFeasibleNodesToFind(int32(len(nodes)))

	// Create feasible list with enough space to avoid growing it
	// and allow assigning.
	feasibleNodes := make([]*v1.Node, numNodesToFind)

	errCh := parallelize.NewErrorChannel()
	var statusesLock sync.Mutex
	var feasibleNodesLen int32
	ctx, cancel := context.WithCancel(ctx)
	checkNode := func(i int) {
		// We check the nodes starting from where we left off in the previous scheduling cycle,
		// this is to make sure all nodes have the same chance of being examined across pods.
		nodeInfo := nodes[(g.nextStartNodeIndex+i)%len(nodes)]
		status := fwk.RunFilterPluginsWithNominatedPods(ctx, state, pod, nodeInfo)
		if status.Code() == framework.Error {
			errCh.SendErrorWithCancel(status.AsError(), cancel)
			return
		}
		if status.IsSuccess() {
			length := atomic.AddInt32(&feasibleNodesLen, 1)
			if length > numNodesToFind {
				cancel()
				atomic.AddInt32(&feasibleNodesLen, -1)
			} else {
				feasibleNodes[length-1] = nodeInfo.Node()
			}
		} else {
			statusesLock.Lock()
			diagnosis.NodeToStatusMap[nodeInfo.Node().Name] = status
			diagnosis.UnschedulablePlugins.Insert(status.FailedPlugin())
			statusesLock.Unlock()
		}
	}

	beginCheckNode := time.Now()
	statusCode := framework.Success
	defer func() {
		// We record Filter extension point latency here instead of in framework.go because framework.RunFilterPlugins
		// function is called for each node, whereas we want to have an overall latency for all nodes per scheduling cycle.
		// Note that this latency also includes latency for `addNominatedPods`, which calls framework.RunPreFilterAddPod.
		metrics.FrameworkExtensionPointDuration.WithLabelValues(runtime.Filter, statusCode.String(), fwk.ProfileName()).Observe(metrics.SinceInSeconds(beginCheckNode))
	}()

	// Stops searching for more nodes once the configured number of feasible nodes
	// are found.
	fwk.Parallelizer().Until(ctx, len(nodes), checkNode)
	processedNodes := int(feasibleNodesLen) + len(diagnosis.NodeToStatusMap)
	g.nextStartNodeIndex = (g.nextStartNodeIndex + processedNodes) % len(nodes)

	feasibleNodes = feasibleNodes[:feasibleNodesLen]
	if err := errCh.ReceiveError(); err != nil {
		statusCode = framework.Error
		return nil, err
	}
	return feasibleNodes, nil
}
```

numFeasibleNodesToFind 有点意思