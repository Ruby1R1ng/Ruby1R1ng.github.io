---
title: "Sigma_modification"
---

{{< katex >}}

#  Ioannou & Sun 教材《Robust Adaptive Control》第 8 章

## 一、为什么要做"leakage（泄漏）修正"

**出发点**：理想情况下（无噪声、无未建模动态），自适应律 \(\dot\theta = \gamma\varepsilon_1 u\) 能保证参数估计收敛、误差趋零。

**问题**：一旦存在扰动 \(\eta \neq 0\)（式 8.4.1），这个"纯积分型"更新律会让 \(\theta(t)\) **漂移到无穷**（parameter drift）。课本里 Lyapunov 分析给出了直观解释——
$$\dot V \le -|\varepsilon_1|(|\varepsilon_1| - d_0)$$，当 \(|\varepsilon_1| < d_0\) 时 \(\dot V\) 可能为正，
\(\tilde{\theta}\)  无界。

**解决思路**：在更新律里加一个把 \(\theta\) 往零拉的"泄漏项" \(-\gamma w\theta\)，把"纯积分"变成"泄漏积分"。只要 \(\theta\) 变得太大，这项就会主导，让 \(\dot V < 0\)，从而保证有界。

这就是所有 σ-modification 变体的共同思想。区别只在于 **\(w(t)\) 怎么选**。

## 二、三种经典变体

课本里列了三类，分别对应不同的 \(w(t)\) 选择：

### (a) Fixed σ-modification（固定 σ）[Ioannou & Kokotovic, 1984]

$$w(t) = \sigma > 0, \quad \forall t \geq 0$$

**最简单的选择——常数**。分析得到：

$$\dot V \leq -\alpha V + \underbrace{\frac{d_0^2}{2} + \frac{\sigma|\theta^*|^2}{2}}_{\text{常数残差}}$$

**结论**：
- ✅ 保证 \(\tilde\theta\) **指数收敛到残差集** \(D_\sigma\)，所有信号有界（UUB）
- ❌ **破坏了理想性质**：即使扰动消失（\(\eta=0\)），只要 \(\sigma>0\)，就**无法保证** \(\varepsilon_1, \dot\theta \to 0\)。稳态下会有非零估计误差，大小是 \(O(\sigma|\theta^*|^2)\)。

这是课本里明确指出的"主要缺点"（drawback），也是后面两种变体要改进的原因。

### (b) Switching-σ（切换 σ）[Narendra-style, 1987]

$$
w(t)=\sigma_s=\begin{cases}
0 & \lvert\theta\rvert < M_0 \\
\sigma_0 & \lvert\theta\rvert \geq M_0
\end{cases}
$$

**核心思想**：参数估计在可接受范围内时**不泄漏**，超出 \(M_0\) 才启动泄漏。

**结论**：
- ✅ 有界性仍然保证
- ✅ **保留理想性质**：当 \(\eta = 0\) 时，因为 \(-\sigma_s\tilde\theta\theta \le 0\)，仍能证出 \(\varepsilon_1, \dot\theta \in L_2\)，乃至 \(\to 0\)
- ❌ 需要先验知识：
\(M_0\) 必须 \(> |\theta^*|\)，否则退化成 fixed-σ

### (c) \(\varepsilon_1\)-modification [Narendra & Annaswamy, 1987]

$$w(t) = |\varepsilon_1|\nu_0$$

**核心思想**：让泄漏强度跟**误差**走。误差大时大力拉回，误差小时几乎不泄漏。理想情况下 \(\varepsilon_1 \to 0\)，泄漏自然消失。

**结论**：
- ✅ 有界性保证
- ⚠️ **理想性质只在 PE（持续激励）条件下恢复**：一般情况下仍无法保证 \(\varepsilon_1, \dot\theta \to 0\)；只有当输入
\(u\) 是持续激励信号时，
\(\varepsilon_1 \to 0\) 才成立，从而 \(w(t) \to 0\)，恢复理想性质。
- ✅ 不需要 \(M_0\) 这样的先验知识

## 三、一个对比总结表

| 变体 | \(w(t)\) | 需要先验信息 | 保证 UUB | 恢复渐近收敛（\(\eta=0\) 时）|
|------|--------|-------------|---------|-------------------------|
| Fixed σ | 常数 \(\sigma\) | 无 | ✅ | ❌ |
| Switching σ | 取决于 \(\|\theta\|\) 是否越界 | \(M_0 > \|\theta^*\|\) | ✅ | ✅（无条件）|
| \(\varepsilon_1\)-mod | \(\|\varepsilon_1\|\nu_0\) | 无 | ✅ | ⚠️（需 PE）|




#  Towards Understanding Convergence and Generalization of AdamW
<img width="1015" height="260" alt="image" src="https://github.com/user-attachments/assets/63b8663d-196d-4bcd-bb6e-e010c86c68c0" />
假设 随机梯度的ℓ∞-范数有界
<img width="762" height="128" alt="image" src="https://github.com/user-attachments/assets/5e0e8a9e-916e-4939-8277-e49b73d95615" />

# AdamW关于有界假设的改进

### 问题：原假设过强

$$\|h_k(\vartheta)\| \leq M_h, \qquad \forall \vartheta \in \mathbb{R}^{d_2}$$

因为 AdamW 没有投影，如果直接假设所有 \(\vartheta\) 上的随机方向全局有界，相当于提前假设了预测误差在全参数空间内有界。

### 方法一 先找一个估计值的紧集

1. 不再假设 \(h_k(\vartheta)\) 在全空间有界；
2. 先利用 AdamW 的归一化结构 \(m_k \oslash s_k\) 和 decoupled weight decay 证明 \(\hat{\theta}_k\) 有界；
3. 再只要求在证明得到的紧集 \(D_*\) 上，\(h_k(\theta)\)、\(w_{k+1}(\theta)\) 有界或二阶矩有界。

**对原始无投影 AdamW，如果不假设方向有界，也不修改算法，一般不可能得到一个不随 \(\beta_2 \rightarrow 1\) 发散的确定性界。**


<img width="466" height="289" alt="image" src="https://github.com/user-attachments/assets/8cb13227-1057-49c6-9c7d-7a593b0c0ad1" />

### 证明

<img width="468" height="914" alt="image" src="https://github.com/user-attachments/assets/0f08069f-7edf-4fc7-b946-e1bc101d86b0" />
<img width="452" height="271" alt="image" src="https://github.com/user-attachments/assets/d483ae88-0741-4528-abff-613f0e64ead3" />

<img width="741" height="332" alt="image" src="https://github.com/user-attachments/assets/760f9011-f153-48c1-95f7-c85475e62516" />


### 方法二 gradient clipping

大模型训练里常见做法是先算梯度，然后裁剪：

$$g_k^{\mathrm{clip}} = \begin{cases} g_k, & \|g_k\| \leq G \\ \dfrac{G}{\|g_k\|} g_k, & \|g_k\| > G \end{cases}$$

然后 AdamW 用

\(g_k^{\mathrm{clip}}\)

来更新 \(m_k, v_k\)。也就是说：

$$m_{k+1} = \beta_1 m_k + (1 - \beta_1) g_k^{\mathrm{clip}}$$

$$v_{k+1} = \beta_2 v_k + (1 - \beta_2) (g_k^{\mathrm{clip}} \odot g_k^{\mathrm{clip}})$$

它直接给出

\(\|g_k^{\mathrm{clip}}\| \leq G\)

如果多输出方向是

$$h_k = J_k^{\mathrm{T}} \ell_{k+1}$$

那么可以写成

$$\bar{h}_k = \mathcal{C}_G(h_k)$$

其中

$$\mathcal{C}_G(h) = \begin{cases} h, & \|h\| \leq G \\ \dfrac{G}{\|h\|} h, & \|h\| > G \end{cases}$$

于是自动有 \(\|\bar{h}_k\| \leq G\)














