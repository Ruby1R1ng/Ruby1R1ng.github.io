# 《Data Shapley in One Training Run》

https://jiachen-t-wang.github.io/data-shapley.github.io/

目标：衡量训练数据里“每一条数据”到底对模型有多大贡献，而且尽量只用一次训练过程就算出来。

给训练集中的每个训练点 \(z_i\) 一个分数 \(\phi_{z_i}\)，表示它对模型表现的重要性。

- \(D^{tr} := \{z_i\}_{i=1}^N\)：训练集
  也就是总共有 \(N\) 条训练数据，每条叫 \(z_i\)
- \(\phi_{z_i}\)：第 \(i\) 条训练数据的价值分数

utility function

\[
U(S) := \text{Perf}(A(S))
\]

意思是：给你训练集的一个子集 \(S\)，先用学习算法 \(A\) 在这个子集上训练一个模型，再用 \(\text{Perf}\) 去评估这个模型表现。这个评估结果就叫 **utility（效用）**。

你可以把它翻译成：
> 如果我只拿子集 \(S\) 来训练，最后能带来多大“好处”？

这里各符号分别是：

- \(S\)：训练集的一个子集
- \(A(S)\)：在 \(S\) 上训练出来的模型
- \(\text{Perf}(\cdot)\)：模型表现，比如准确率、loss、perplexity
- \(U(S)\)：这个子集 \(S\) 的价值

Shapley value 的定义

\[
\phi_z(U) := \frac{1}{N} \sum_{k=1}^N \binom{N-1}{k-1}^{-1} \sum_{\substack{S \subseteq D-z, |S|=k-1}} \big[U(S \cup \{z\}) - U(S)\big]
\]

- \(z\)：某一条你想评估价值的数据
- \(D\)：全体训练数据
- \(D - z = D \setminus \{z\}\)：把 \(z\) 去掉后的其余数据
- \(S \subseteq D - z\)：从其余数据里随便拿一个子集 \(S\)
- \(|S| = k - 1\)：这个子集大小正好是 \(k-1\)
- \(S \cup \{z\}\)：把 \(z\) 加进这个子集里
- \(U(S \cup \{z\}) - U(S)\)：加入 \(z\) 之后，效用增加了多少，这叫 **边际贡献**

> 对于数据点 \(z\)，看它在各种不同“上下文”里加入时，能让模型表现提升多少；然后把这些提升做一个公平平均，这个平均值就是 \(z\) 的 Shapley value。
>
> ### 局部效用函数：公式 (1)

论文定义单步的局部效用：

\[
U^{(t)}(S; z^{(val)}) := \ell\big(\widetilde{w}_{t+1}(S), z^{(val)}\big) - \ell\big(w_t, z^{(val)}\big) \tag{1}
\]
> 在第 \(t\)步，如果我只用训练子集 \(S\) 来更新模型参数，那么更新前后，模型在某个验证样本 \(z^{(\mathrm{val})}\) 上的 loss 改变了多少？

其中

\[
\widetilde{w}_{t+1}(S) := w_t - \eta_t \sum_{z \in S} \nabla \ell(w_t, z)
\]

> 假设在第 \(t\) 步，我不是用整个 batch \(B_t\) 来更新，而是只用其中的子集 \(S\) 来更新，那更新后参数会变成什么？

这个“假想出来的参数”就叫 \(\widetilde{w}_{t+1}(S)\)。

