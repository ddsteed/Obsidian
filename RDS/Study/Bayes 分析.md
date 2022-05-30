```ActivityHistory
/
```
Bayes 分析，本质上是一种[[Probability | 概率]]分析方法。它的理论依据来自于 [[Probability#Bayes's Theorem|Bayes 定理]]，
$$
p(A|B) = \frac{p(A)p(B|A)}{p(B)}
$$

## 解释
Bayes 分析处理的是多个事件同时发生时的概率，当然，我们也可以用来处理单一事件发生的概率。显然，如果已经知道一个事件的发生概率，就不用 Bayes 分析了。如果不知道，我们可以先预估一个，然后根据了解的信息逐步增多，来修正初始的猜测值。这实际上也是现实世界中，我们处理问题的真实做法。

要做到这一点，我们得换种方式来理解 Bayes 定理。

### The diachronic interpretation
按习惯，重新书写 Bayes 定理的形式，
$$
p(H|D) = \frac{p(H)p(D|H)}{p(D)}
$$
这里，
1.  $p(H)$ 表示我们知道的信息（或者说数据）有限，甚至一无所知，先验性地猜测某一假设发生的概率值。有时我们称之为 the prior probability，或者 prior;
1.  $p(H|D)$ 为我们更新信息（数据）之后，对假设发生的重新估值，称为 posterior;
1.  $p(D|H)$ 为在该假设下，信息（数据）发生的概率，称为 likelihood;
1.  $p(D)$ 为任何情况下信息（数据）发生的总概率。
