# scheduler 2

书接上一回，上次我们分析了在 `cmd/kube-scheduler/` 下，从 `main()` 开始直到 `sched.Run()` 启动的过程。在 `cmd/kube-scheduler/app/server.go` 的 `Setup` 函数中，调用 `scheduler.New()` 实例化出 `sched` ，并在 `Run` 函数中调用 `sched.Run()` 启动循环。因此接下来我们将分析 `pkg/scheduler/` 这个包。

> 接下来如无特意标记， `.` 开头的相对路径都为从 `/pkg/scheduler/` 开始的相对路径。

## sched.Run()

我们开始看 `sched` 的这个 `Run` 方法。

为什么我们不先看 `New` 函数呢？我们点进 `New` 略微看一下，发现它一直都在做 `sched` 各个成员的初始化。单看 `sched` 看不出什么，但要是所有成员一个一个看内容又太多，容易弄晕自己。我们打算先把 `sched` 在做什么整体过一遍，在回来仔细看看每一个细节。

我们点进 `sched.Run(ctx)` ，发现这个函数很短小，只有三行代码：

```go ./scheduler.go
// Run begins watching and scheduling. It starts scheduling and blocked until the context is done.
func (sched *Scheduler) Run(ctx context.Context) {
	sched.SchedulingQueue.Run()
	wait.UntilWithContext(ctx, sched.scheduleOne, 0)
	sched.SchedulingQueue.Close()
}
```

注释已经明显写清楚这个函数会 block 了（当然了，之前在 `sched.Run(ctx)` 后面可是没有任何 wait 的）。
虽然我们一眼就能看出来最重要的是中间这行，而且最重要的还是 `sched.scheduleOne` 这个函数（为什么我知道他是个函数？鼠标放上去看看嘛）。但我们还是放慢脚步，看看前后两行在做什么。动动鼠标：

`sched.SchedulingQueue` ：
![[2022-04-13-18-51-12.png]]

`SchedulingQueue.Run()` ：
![[2022-04-13-18-52-10.png]]

`SchedulingQueue.Close()` ：
![[2022-04-13-18-52-37.png]]

很清晰了， `sched.SchedulingQueue` 是用来放还未被分配的 Pod 的，需要运行一些线程来维持，一前一后就是做这个的。

那么接下来我们来看 `sched.scheduleOne` …… 别急，我们先来看看 `wait.UntilWithContext` 做了些什么。从名字我们大概可以猜到，这个函数是循环去调用 `sched.scheduleOne` ，然后 `ctx` 关闭了就立即退出。我们主要看看他是怎么实现的，估计会很有参考价值。

## k8s 的 wait 包

点进 `wait.UntilWithContext` ……
`UntilWithContext` -> `JitterUntilWithContext` -> `JitterUntil` -> `BackoffUntil` 怎么一层套一层啊……

我们先别慌，上下一看其实都是语法糖，主要还是看一下 `BackoffUntil` ，我们先看注释：
> BackoffUntil loops until stop channel is closed, run f every duration given by BackoffManager.
> If sliding is true, the period is computed after f runs. If it is false then period includes the runtime for f.

翻译一下： `BackoffUntil` 会一直循环执行 `f` ，直到 `stopCh` 关闭。循环周期由 `BackoffManager` 管理，而 `sliding` 表示一个周期的时间内是否包含 `f` 的运行时间。

先看代码：

```go
func BackoffUntil(f func(), backoff BackoffManager, sliding bool, stopCh <-chan struct{}) {
	var t clock.Timer
	for {
		select {
		case <-stopCh:
			return
		default:
		}

		if !sliding {
			t = backoff.Backoff()
		}

		func() {
			defer runtime.HandleCrash()
			f()
		}()

		if sliding {
			t = backoff.Backoff()
		}

		// NOTE: b/c there is no priority selection in golang
		// it is possible for this to race, meaning we could
		// trigger t.C and stopCh, and t.C select falls through.
		// In order to mitigate we re-check stopCh at the beginning
		// of every loop to prevent extra executions of f().
		select {
		case <-stopCh:
			if !t.Stop() {
				<-t.C()
			}
			return
		case <-t.C():
		}
	}
}

```

发现是一整个 while 循环，循环内分三部分：
1. 前面一个 select ，是为了监听 `stopCh` ，如果关闭就退出循环。
2. 中间外围是 sliding 相关，如果 sliding 则在 `f` 执行后开始一个周期的计时，否则在 `f` 执行前计时。 `f` 执行后还会处理 Crash 。
3. 后面一个 select ，是为了等待周期计时。如果 `stopCh` 先被关闭，就会立即返回而不等待周期结束。

其中最让人疑惑的就是这个 `BackoffManager` 了。我们倒回去看看他是怎么来的。在 `JitterUntil` 中：

```go 
NewJitteredBackoffManager(period, jitterFactor, &clock.RealClock{})
```

