# Pattern-defeating Quicksort

## Abstract

A new solution for the Dutch national flag problem is proposed, requiring no three-way comparisons, which gives quicksort a proper
worst-case runtime of $O(nk)$ for inputs with $k$ distinct elements. 
This is used together with other known and novel techniques to construct a hybrid sort that is never significantly slower than 
regular quicksort while speeding up drastically for many input distributions.

这篇文章中提出了解决荷兰国旗问题的一个新方案。这个方案不需要三向比较，因此可以保证输入 $k$ 种不同元素，快速排序的最坏时间复杂度为 $O(nk)$ 。
这个方案与其他已知的技术一起使用，构造出一个混合排序算法，这个算法在大多数输入分布下的速度都不会比普通的快速排序慢，而且在许多种输入分布下的速度会大幅提升。

> Dutch national flag problem
> 荷兰国旗问题：给 $n$ 个红白蓝单色小球，要求对小球按荷兰国旗颜色顺序排列，且相同颜色的球排在一起。
> 实际上是计数排序的案例，一般用双指针三向比较。

## Introduction

Arguably the most used hybrid sorting algorithm at the time of writing is
introsort[12]. A combination of insertion sort, heapsort[19] and quicksort[10],
it is very fast and can be seen as a truly hybrid algorithm. The algorithm performs introspection and decides when to change strategy using some very simple
heuristics. If the recursion depth becomes too deep, it switches to heapsort, and
if the partition size becomes too small it switches to insertion sort.

在写这篇文章的时候，最常用的混合排序算法可能是 introsort[12] 。这是插入排序、堆排序[19] 和快速排序[10] 的结合，非常快速，可以看作是一个真正的混合算法。这个算法通过一些简单的启发式方法，进行自我检查，决定何时切换策略。如果递归深度过深，就切换到堆排序；如果分区大小过小，就切换到插入排序。

> Introsort
> 内省排序：一开始使用快速排序，当递归深度过深时，切换到堆排序。
> 能在常规快速排序的基础上，保证最坏情况下的时间复杂度为 $O(n\log n)$ 。
> 论文中提到的 introsort 是带上插入排序的版本：当分区大小过小时，切换到插入排序能减少递归消耗，且插入排序在小规模数据上的性能很好。

The goal of pattern-defeating quicksort (or pdqsort) is to improve on introsort’s heuristics to create a hybrid sorting algorithm with several desirable properties. It maintains quicksort’s logarithmic memory usage and fast realworld average case, effectively recognizes and combats worst case behavior (deterministically), and runs in linear time for a few common patterns. It also unavoidably inherits in-place quicksort’s instability, so pdqsort can not be used in situations where stability is needed.

Pattern-defeating quicksort（又称 pdqsort ）的目标是在 introsort 基础上改进，创建一个具有多个理想属性的混合排序算法。它保持了快速排序的对数级空间复杂度和实际应用场景中快速的平均耗时，有效地识别和抵御最坏情况行为（确定性），并在一些常见模式下以线性时间运行。它也不可避免地继承了原地 quicksort 的不稳定性，因此 pdqsort 不适用于需要稳定性的情况。

In Section 2 we will explore a quick overview of pattern-defeating quicksort
and related work, in Section 3 we propose our new solution for the Dutch national flag problem and prove its O(nk) worst-case time for inputs with k distinct
elements, Section 4 describes other novel techniques used in pdqsort while Section 5 describes various previously known ones. Our final section consists of an
empirical performance evaluation of pdqsort.

在第 2 节中，我们将对 pdqsort 和相关工作进行一个快速概述，在第 3 节中，我们将提出我们关于荷兰国旗问题的新解决方案，为具有 k 种不同元素的输入证明其 O(nk) 的最坏情况时间，第 4 节描述了 pdqsort 中使用的其他新颖技术，第 5 节描述了各种以前已知的技术。我们的最后一节是 pdqsort 的实证性能评估。

This paper comes with an open source state of the art C++ implementation[14].
The implementation is fully compatible with std::sort and is released under a
permissive license. Standard library writers are invited to evaluate and adopt the
implementation as their generic unstable sorting algorithm. At the time of writing the Rust programming language has adopted pdqsort for sort unstable
in their standard library thanks to a porting effort by Stjepan Glavina. The
implementation is also available in the C++ Boost.Sort library.

本文附带了一个开源的最先进的 C++ 实现[14]。该实现完全兼容 std::sort，并在许可证下发布。标准库作者被邀请评估并采用该实现作为其通用的不稳定排序算法。在撰写本文时，感谢 Stjepan Glavina 的移植工作，Rust 编程语言已经将 pdqsort 用于其标准库中的不稳定排序。该实现还可在 C++ Boost.Sort 库中找到。

## Overview and related work

Pattern-defeating quicksort is a hybrid sort consisting of quicksort[10], insertion
sort and a fallback sort. In this paper we use heapsort as our fallback sort, but
really any O(n log n) worst case sort can be used - it’s exceptionally rare that
the heuristics end up switching to the fallback. Each recursive call of pdqsort
chooses either to fall back, use insertion sort or partition and recurse.

Pattern-defeating quicksort 是由 quicksort[10]、插入排序和 fallback 组成的混合排序。在本文中，我们使用堆排序作为 fallback ，但实际上可以使用任何最坏情况为 O(n log n) 的排序——使用 fallback 的情况非常罕见。pdqsort 的每个递归调用都从 fallback 、使用插入排序、分区并递归中三选一。

> 分区指快排中的分区步骤，分区并递归实际上就是快排的递归过程。

Insertion sort is used for small recursive calls as despite its $O(n^2)$ worst case
it has great constant factors and outperforms quicksort for small n. We later
discuss (not novel but nevertheless important) techniques to properly implement
insertion sort for usage in a hybrid algorithm. We have tried to use small sorting
networks akin to Codish’s[5] approach as an alternative base case but were unable
to beat insertion sort. We conjecture that the small code size of insertion sort
has sufficient positive cache effects to offset the slower algorithm when used in
a hybrid sort.

插入排序用于小的递归调用，尽管它的最坏情况为 $O(n^2)$ ，但它具有很好的常数因子，并且对于 n 比较小时性能优于快速排序。我们稍后将讨论（虽然不是新颖的，但仍然重要）正确实现插入排序以用于混合算法的技术。我们尝试过使用类似 Codish 的[5]方法的小型排序网络作为替代的基本情况，但无法击败插入排序。我们推测，插入排序的小代码大小具有足够的正缓存效应，以抵消在混合排序中使用时算法速度较慢的情况。

For partitioning we use a novel scheme that indirectly ends up performing tripartite partitioning. This is used in conjunction with the very important technique from BlockQuicksort[7] that greatly speeds up partitioning with
branchless comparison functions. In a sense our partitioning scheme is similar to
Yaroslavskiy’s dual-pivot quicksort[18] from the perspective of equal elements.
We did consider dual- and multi-pivot[2] variants of quicksort but chose to stick
to traditional partitioning for simplicity, applicability of the techniques described
here, and due to the massive speedup from BlockQuicksort, which does not trivially extend to multiple pivots (see IPS4o[3] for that).

对于分区，我们使用了一种新颖的方案，该方案间接地最终执行三分区分区。这与非常重要的 BlockQuicksort[7] 中的技术结合使用，该技术大大加快了使用无分支比较函数的分区。从相等元素的角度来看，我们的分区方案与 Yaroslavskiy 的双轴快速排序[18]类似。我们确实考虑了快速排序的双轴和多轴[2]变体，但选择保持简单性，适用于这里描述的技术，以及由于 BlockQuicksort 带来的巨大加速，使得在此之上扩展到多个轴带来的加速并不明显（有关该问题，请参阅 IPS4o[3]）。

> BlockQuicksort
>

> dual-pivot quicksort
> 

> pdqsort 的核心在此，使用了新的分区方案，最终形成三个分区。

We use the well-known median-of-3 pivot selection scheme, with John Tukey’s
ninther[17] for sufficiently large inputs, which Kurosawa[11] finds has a nearidentical number of comparisons to selecting the true median, but is significantly
simpler.

我们使用众所周知的中值法选择轴，对于足够大的输入，使用 John Tukey 的 ninther[17]，Kurosawa[11] 发现，它与选择真正的中值的比较次数几乎相同，但更简单。

> median-of-3 pivot selection scheme
>

> ninther
>

Finally, we beat patterns with two novel additions. We diverge from introsort
by no longer simply using the call depth to switch to a fallback. Instead we
define a concept of a bad partition, and track those instead. This results in a
more precise heuristic on bad sorting behavior, and consequently fallback usage.
Whenever we detect a bad partition we also swap a couple well-selected elements,
which not only breaks up common patterns and introduces ’noise’ similar to how
a random quicksort would behave, it introduces new pivot candidates in the
selection pool. We also use a technique (not known to us in previous literature)
due to Howard Hinnant to optimistically handle ascending/descending sequences
with very little overhead.

最后，我们通过两个新的要素来击败传统。我们不再像 introsort 一样，简单地使用调用深度来切换到 fallback 。相反，我们定义了一个坏分区的概念，并跟踪这些分区。这是我们能够更精确地处理坏排序现象，以及其导致的 fallback 的使用。每当我们检测到一个坏分区时，我们还会交换几个精心选择的元素，这不仅打破了常见的模式并引入了类似于随机快速排序的行为的“噪声”，而且还在选择池中引入了新的轴候选人。我们还使用了 Howard Hinnant 的一种技术（在以前的文献中不知道）来乐观地处理升序/降序序列，开销很小。

## A faster solution for the Dutch national flag problem

A naive quicksort implementation might trigger the $Θ(n^2)$ worst case on the
all-equal input distribution by placing equal comparing elements in the same
partition. A smarter implementation either always or never swaps equal elements,
resulting in average case performance as equal elements will be distributed evenly
across the partitions. However, an input with many equal comparing elements
is rather common1, and we can do better. Handling equal elements efficiently
requires tripartite partitioning, which is equivalent to Dijkstra’s Dutch national
flag problem[6].

一个原始的快速排序实现可能会在所有相等的输入分布上触发 $Θ(n^2)$ 的最坏情况，因为它会将相等的比较元素放在同一个分区中。一个更聪明的实现是总是或从不交换相等的元素，从而导致平均情况下性能，因为相等的元素将均匀地分布在分区中。然而，一个包含许多相等比较元素的输入是相当常见的1，我们可以做得更好。高效地处理相等元素需要三分区分区，这相当于 Dijkstra 的荷兰国旗问题[6]。

Pattern-defeating quicksort uses the fast
’approaching pointers’ method[4] for partitioning. Two indices are initialized, i at the start
and j at the end of the sequence. i is incremented and j is decremented while maintaining an invariant, and when both invariants
are invalidated the elements at the pointers
are swapped, restoring the invariant. The algorithm ends when the pointers cross. Implementers must take great care, as this algorithm is conceptually simple, but is very easy
to get wrong.

Pattern-defeating quicksort 使用快速的 ’接近指针’ 方法[4]进行分区。初始化两个索引，i 在序列的开始处，j 在序列的末尾。i 在维持不变量的情况下递增，j 在维持不变量的情况下递减，当两个不变量都被破坏时，指针处的元素被交换，恢复不变量。当指针交叉时，算法结束。实现者必须非常小心，因为这个算法在概念上很简单，但很容易出错。

Bentley and McIlroy describe an invariant
for partitioning that swaps equal elements to
the edges of the partition, and swaps them
back into the middle after partitioning. This
is efficient when there are many equal elements, but has a significant drawback. Every element needs to be explicitly checked for
equality to the pivot before swapping, costing another comparison. This happens regardless of whether there are many equal elements,
costing performance in the average case.

Bentley 和 McIlroy 描述了一个分区的不变量，它将相等的元素交换到分区的边缘，并在分区后将它们交换回中间。当有许多相等的元素时，这是有效的，但有一个显着的缺点。在交换之前，每个元素都需要明确检查与轴的相等性，这需要另一个比较。这种情况无论是否有许多相等的元素都会发生，从而在平均情况下损失性能。

Unlike previous algorithms, pdqsort’s partitioning scheme is not self contained. It uses
two separate partition functions, one that
groups elements equal to the pivot in the
left partition (partition left), and one that
groups elements equal to the pivot in the
right partition (partition right). Note that
both partition functions can always be implemented using a single comparison per element
as a < b ⇔ a  b and a ≮ b ⇔ a ≥ b.

与以前的算法不同，pdqsort 的分区方案不是自包含的。它使用两个单独的分区函数，一个将等于轴的元素分组到左分区（分区左）中，另一个将等于轴的元素分组到右分区（分区右）中。请注意，两个分区函数都可以使用每个元素一次比较来实现，因为 $a < b \Leftrightarrow a \ngeqslant b$ 而 $a \nless b \Leftrightarrow a \geq b$ 。

For brevity we will be using a simplified,
incomplete C++ implementation to illustrate pdqsort. It only supports int and
compares using comparison operators. It is however trivial to extend this to
arbitrary types and custom comparator functions. To pass subsequences2 around,
the C++ convention is used of one pointer at the start, and one pointer at onepast-the-end. For the exact details refer to the full implementation[14].

为了简洁起见，我们将使用简化的、不完整的 C++ 实现来说明 pdqsort。它只支持 int 并使用比较运算符进行比较。然而，将其扩展到任意类型和自定义比较器函数是微不足道的。为了传递子序列2，使用了 C++ 的惯例，即一个指针在开始处，一个指针在结束后。有关详细信息，请参阅完整实现[14]。

> 即对于序列 s ，一个指针指向 s[0] ，一个指针指向 s[len(s)] 。

Both partition functions assume the pivot is the first element, and that it has
been selected as a median of at least three elements in the subsequence. This
saves a bound check in the first iteration.

两个分区函数都假定轴是第一个元素，并且它已被选择为子序列中至少三个元素的中位数。这样可以节省第一次迭代中的边界检查。

> 此处省略第三章接下来的内容


## Other novel techniques

### Preventing quicksort's $O(n^2)$ worst case

Pattern-defeating quicksort calls any partition operation which is more unbalanced than p (where p is the percentile of the pivot, e.g. $\frac{1}{2}$ for a perfect partition)
a bad partition. Initially, it sets a counter to log n. Every time it encounters a
bad partition, it decrements the counter before recursing5. If at the start of a
recursive call the counter is 0 it uses heapsort to sort this subsequence, rather
than quicksort.

在Pattern-defeating quicksort 中，我们称所有比 p（其中 p 是轴的百分位数，例如，完美分区的 $\frac{1}{2}$ ）更不平衡的分区操作为坏分区。最初，它将计数器设置为 log n。每次遇到一个不良分区时，它在递归之前将计数器减 1。如果在递归调用的开始时计数器为 0，则使用堆排序来对此子序列进行排序，而不是快速排序。

Lemma 5. At most O(n log n) time is spent in pdqsort on bad partitions.

引理 5。在 pdqsort 中，最多花费 O(n log n) 的时间在坏分区上。

Proof. Due to the counter ticking down, after log n levels that contain a bad
partition the call tree terminates in heapsort. At each level we may do at most
O(n) work, giving a runtime of O(n log n).

证明。由于计数器在下降，因此在包含坏分区的 log n 层之后，调用树在堆排序中终止。在每个级别上，我们最多可以做 O(n) 的工作，从而给出运行时间为 O(n log n)。

Lemma 6. At most O(n log n) time is spent in pdqsort on good partitions.

引理 6。在 pdqsort 中，最多花费 O(n log n) 的时间在好分区上。

Proof. Consider a scenario where quicksort’s partition operation always puts pn
elements in the left partition, and (1 − p)n in the right. This consistently forms
the worst possible good partition. Its runtime can be described with the following
recurrence relation:
T (n, p) = n + T (pn, p) + T ((1 − p)n, p)
For any p ∈ (0, 1) the Akra-Bazzi[1] theorem shows Θ(T (n, p)) = Θ(n log n).

证明。考虑一个场景，其中快速排序的分区操作始终将 pn 个元素放在左分区中，而 (1 − p)n 则放在右分区中。这始终形成最坏的好分区。它的运行时间可以用以下递归关系描述：
T (n, p) = n + T (pn, p) + T ((1 − p)n, p)
对于任何 p ∈ (0, 1)，Akra-Bazzi[1] 定理表明 Θ(T (n, p)) = Θ(n log n)。

Theorem 2. Pattern-defeating quicksort has complexity O(n log n).

定理 2。Pattern-defeating quicksort 的复杂度为 O(n log n)。

Proof. Pattern-defeating quicksort spends O(n log n) time on good partitions,
bad partitions, and degenerate cases (due to heapsort also being O(n log n)).
These three cases exhaustively enumerate any recursive call to pdqsort, thus
pattern-defeating quicksort has complexity O(n log n).

证明。Pattern-defeating quicksort 在好分区、坏分区和退化情况（由于堆排序也是 O(n log n)）上花费 O(n log n) 的时间。这三种情况完整地枚举了对 pdqsort 的任何递归调用，因此 pattern-defeating quicksort 的复杂度为 O(n log n)。

We have proven that for any choice of p ∈ (0, 1) the complexity of patterndefeating quicksort is O(n log n). However, this does not tell use what a good
choice for p is.

我们已经证明了对于任何 p ∈ (0, 1) 的选择，pattern-defeating quicksort 的复杂度都是 O(n log n)。但是，这并不能告诉我们 p 的一个好选择。

Yuval Filmus[8] solves above recurrence, allowing us to study the slowdown
of quicksort compared to the optimal case of $p = \frac{1}{2}$ . He finds that the solution is
$$
\lim_{n \to \infty} \frac{T(n, p)}{T(n, \frac{1}{2})} = \frac{1}{H(p)}
$$
where H is Shannon’s binary entropy function:
$$
H(p) = −p \log_2(p) − (1 − p) \log_2(1 − p)
$$

Yuval Filmus[8] 解决了上述递归，允许我们研究快速排序相对于最优情况 $p = \frac{1}{2}$ 的放缓。他发现解决方案是
$$
\lim_{n \to \in \infty} \frac{T(n, p)}{T(n, \frac{1}{2})} = \frac{1}{H(p)}
$$
其中 H 是 Shannon 的二进制熵函数：
$$
H(p) = −p \log_2(p) − (1 − p) \log_2(1 − p)
$$

Plotting this function gives us a look at quicksort’s fundamental performance
characteristics:

绘制此函数可以让我们看到快速排序的基本性能特征：

> 此处缺图

From benchmarks we’ve found that heapsort is roughly about twice as slow
as quicksort for sorting randomly shuffled data. If we then choose p such that
$H(p)^{−1} = 2$ a bad partition becomes roughly synonymous with ‘worse than
heapsort’.

从基准测试中我们发现，对于随机洗牌的数据，堆排序大约比快速排序慢两倍。如果我们选择 p 使得 $H(p)^{−1} = 2$，那么坏分区就大约等同于“比堆排序更糟糕”。

The advantage of this scheme is that p can be tweaked if the architecture
changes, or you have a different worst-case sorting algorithm instead of heapsort.
We have chosen p = 0.125 as the cutoff value for bad partitions for two reasons: it’s reasonably close to being twice as slow as the average sorting operation
and it can be computed using a simple bitshift on any platform.

这种方案的优点是，如果架构发生变化，或者你有一个不同的最坏情况排序算法而不是堆排序，p 可以被调整。我们选择 p = 0.125 作为坏分区的截止值有两个原因：它与平均排序操作相比，速度相当接近两倍，而且它可以在任何平台上使用简单的位移计算。

Using this scheme as opposed to introsort’s static logarithmic recursive call
limit for preventing the worst case is more precise. While testing we noticed that
introsort (and to a lesser extent pdqsort) often have a rough start while sorting
an input with a bad pattern, but after some partitions the pattern is broken
up. Our scheme then procedes to use the now fast quicksort for the rest of the
sorting whereas introsort too heavily weighs the bad start and degenerates to
heapsort.

与 introsort 的静态对数递归调用限制相比，使用这种方案可以防止更精确地最坏情况。在测试时，我们注意到 introsort（以及 pdqsort）在对具有坏模式的输入进行排序时通常会有一个粗糙的开始，但在一些分区之后，模式被打破。然后，我们的方案继续使用现在快速排序进行剩余的排序，而 introsort 则过度考虑了坏的开始并退化为堆排序。

### Introducing fresh pivot candidates on bad partitions

Some input patterns form some self-similar structure after partitioning. This can
cause a similar pivot to be repeatedly chosen. We want to eliminate this. The
reason for this can be found in Figure 4 as well. The difference between a good
and mediocre pivot is small, so repeatedly choosing a good pivot has a relatively
small payoff. The difference between a mediocre and bad pivot is massive. An
extreme example is the traditional $O(n^2)$ worst case: repeatedly partitioning
without real progress.

一些输入模式在分区后形成一些自相似结构。这可能导致重复选择类似的枢轴。我们想消除这种情况。这个原因可以在图 4 中找到。良好和一般枢轴之间的差异很小，因此重复选择良好的枢轴具有相对较小的回报。一般和坏枢轴之间的差异是巨大的。一个极端的例子是传统的 $O(n^2)$ 最坏情况：重复分区而没有实际进展。

The classical way to deal with this is by randomizing pivot selection (also
known as randomized quicksort). However, this has multiple disadvantages. Sorting is not deterministic, the access patterns are unpredictable and extra runtime
is required to generate random numbers. We also destroy beneficial patterns, e.g.
the technique in section 5.2 would no longer work for descending patterns and
performance on ’mostly sorted’ input patterns would also degrade.

处理这种情况的经典方法是随机化枢轴选择（也称为随机化快速排序）。然而，这有多个缺点。排序不是确定性的，访问模式是不可预测的，还需要额外的运行时间来生成随机数。我们也破坏了有益的模式，例如第 5.2 节中的技术将不再适用于降序模式，对“大多数排序”输入模式的性能也会降低。

Pattern-defeating quicksort takes a different approach. After partitioning we
check if the partition was bad. If it was, we swap our pivot candidates for others.
In our implementation pdqsort chooses the median of the first, middle and last
element in a subsequence as the pivot, and swaps the first and last candidate for
ones found at the 25% and 75% percentile after encountering a bad partition.
When our partition is big enough that we would be using Tukey’s ninther for
pivot selection we also swap the ninther candidates for ones at roughly the 25%
and 75% percentile of the partition.

Pattern-defeating quicksort 采用了不同的方法。分区后，我们检查分区是否是坏的。如果是，我们将枢轴候选人交换为其他人。在我们的实现中，pdqsort 选择子序列中的第一个、中间和最后一个元素的中位数作为枢轴，并在遇到坏分区后将第一个和最后一个候选人交换为在 25% 和 75% 百分位数找到的候选人。当我们的分区足够大，以至于我们将使用 Tukey 的 ninther 作为枢轴选择时，我们还将 ninther 候选人交换为在分区的大约 25% 和 75% 百分位数的候选人。

With this scheme pattern-defeating quicksort is still fully deterministic, and
with minimal overhead breaks up many of the patterns that regular quicksort
struggles with. If the downsides of non-determinism do not scare you and you like
the guarantees that randomized quicksort provides (e.g. protection against DoS
attacks) you can also swap out the pivot candidates with random candidates.
It’s still a good idea to only do this after a bad partition to prevent breaking up
beneficial patterns.

使用这种方案，pattern-defeating quicksort 仍然是完全确定性的，并且具有最小的开销，可以打破许多常规快速排序所遇到的模式。如果非确定性的缺点不吓唬你，并且你喜欢随机化快速排序提供的保证（例如，防止 DoS 攻击），你也可以用随机候选人替换枢轴候选人。在坏分区后只做这一点可以防止打破有益的模式。

## Previously known techniques
### Insertion sort

> TODO~~~~


> https://arxiv.org/pdf/2106.05123.pdf

