# 第二十五章：概率论 —— 随机性中的确定性

> *"上帝不掷骰子。"*
> *—— 爱因斯坦（但量子力学说：上帝不仅掷骰子，还把骰子扔到看不见的地方）*

---

## 25.1 概率的定义 —— 从赌博到公理

### 古典概型

17 世纪，帕斯卡与费马在赌博问题通信中创立概率论。

**古典定义**：若试验有 \\(n\\) 个等可能基本事件，事件 \\(A\\) 含 \\(k\\) 个，
则 \\[ P(A) = \frac{k}{n} \\]

**例**：掷两枚骰子，点数和为 7 的概率？
36 个等可能结果，和为 7 的有：(1,6),(2,5),(3,4),(4,3),(5,2),(6,1)，共 6 个。
\\[ P = \frac{6}{36} = \frac{1}{6} \\]

### 柯尔莫哥洛夫公理（1933）

1. \\(0 \leq P(A) \leq 1\\)
2. \\(P(\Omega) = 1\\)（必然事件）
3. **可列可加性**：互斥事件 \\(A_i\\)，\\(P\left(\bigcup A_i\right) = \sum P(A_i)\\)

从这 3 条可以推出一切：\\(P(\bar{A}) = 1 - P(A)\\)，
加法公式 \\(P(A \cup B) = P(A) + P(B) - P(A \cap B)\\)，等等。

### 条件概率与贝叶斯

\\[ P(A|B) = \frac{P(A \cap B)}{P(B)} \\]

**贝叶斯定理**：
\\[ P(A|B) = \frac{P(B|A) \cdot P(A)}{P(B)} \\]

**经典应用**（疾病检测）：
某病发病率 1%，检测准确率 99%（患病测出阳性 99%，未患病测出阴性 99%）。
若你测出阳性，真患病的概率？
\\[ P(\text{病}|\text{阳}) = \frac{0.99 \times 0.01}{0.99 \times 0.01 + 0.01 \times 0.99} = \frac{0.0099}{0.0198} = 50\% \\]
**阳性了也只有 50% 真患病！**——因为基数（1%）太小。这就是"基础概率谬误"。

### Python：蒙特卡洛验证

```python
import random

def estimate_pi(n=1000000):
    """蒙特卡洛估算 π：随机点落在圆内的比例"""
    inside = 0
    for _ in range(n):
        x, y = random.random(), random.random()
        if x*x + y*y <= 1:
            inside += 1
    return 4 * inside / n

import math
for n in [1000, 10000, 100000, 1000000]:
    est = estimate_pi(n)
    print(f"n={n:7d}: π≈{est:.5f}, 误差={abs(est-math.pi):.5f}")

# 疾病检测贝叶斯模拟
def bayes_sim(n=100000):
    sick_and_pos = 0
    pos = 0
    for _ in range(n):
        sick = random.random() < 0.01
        if sick:
            test_pos = random.random() < 0.99
        else:
            test_pos = random.random() < 0.01
        if test_pos:
            pos += 1
            if sick:
                sick_and_pos += 1
    return sick_and_pos / pos

print(f"\n贝叶斯模拟 P(病|阳) ≈ {bayes_sim():.3f} (理论 0.5)")
```

---

## 25.2 随机变量与分布

### 离散型

| 分布 | \\(P(X=k)\\) | 期望 | 方差 | 场景 |
|---|---|---|---|---|
| 两点分布 | \\(p^k(1-p)^{1-k}\\) | \\(p\\) | \\(p(1-p)\\) | 抛一次硬币 |
| 二项分布 \\(B(n,p)\\) | \\(\binom{n}{k}p^k(1-p)^{n-k}\\) | \\(np\\) | \\(np(1-p)\\) | 抛 \\(n\\) 次硬币 |
| 泊松分布 | \\(\frac{\lambda^k}{k!}e^{-\lambda}\\) | \\(\lambda\\) | \\(\lambda\\) | 稀有事件计数 |
| 几何分布 | \\((1-p)^{k-1}p\\) | \\(1/p\\) | \\((1-p)/p^2\\) | 首次成功试验数 |

### 连续型

**均匀分布** \\(U[a,b]\\)：\\(f(x) = \frac{1}{b-a}\\)，\\(E = \frac{a+b}{2}\\)。

**正态分布** \\(N(\mu, \sigma^2)\\)（最重要的分布！）：
\\[ f(x) = \frac{1}{\sqrt{2\pi}\sigma} e^{-\frac{(x-\mu)^2}{2\sigma^2}} \\]

**指数分布**：\\(f(x) = \lambda e^{-\lambda x}\\)（\\(x > 0\\)），无记忆性。

### Python：分布可视化数据

```python
import math, random

def normal_pdf(x, mu=0, sigma=1):
    return math.exp(-(x-mu)**2/(2*sigma**2)) / (math.sqrt(2*math.pi)*sigma)

# 68-95-99.7 法则验证
def empirical_rule(n=1000000):
    count1 = count2 = count3 = 0
    for _ in range(n):
        x = random.gauss(0, 1)  # 标准正态
        if abs(x) < 1: count1 += 1
        if abs(x) < 2: count2 += 1
        if abs(x) < 3: count3 += 1
    print(f"P(|X|<1) = {count1/n:.4f} (理论 0.6827)")
    print(f"P(|X|<2) = {count2/n:.4f} (理论 0.9545)")
    print(f"P(|X|<3) = {count3/n:.4f} (理论 0.9973)")

empirical_rule(200000)

# 二项分布逼近正态（中心极限定理的直观）
def binomial(n, p):
    return sum(1 for _ in range(n) if random.random() < p)

import statistics
samples = [binomial(100, 0.5) for _ in range(10000)]
print(f"\nB(100,0.5): 均值={statistics.mean(samples):.2f} (理论50), "
      f"标准差={statistics.stdev(samples):.2f} (理论5)")
```

> 🌟 **拉马努金角**
> 拉马努金对"随机性"有独特见解。他研究**分拆数** \\(p(n)\\) 的渐近公式：
> \\[ p(n) \sim \frac{1}{4n\sqrt{3}} \exp\left(\pi\sqrt{\frac{2n}{3}}\right) \\]
> （哈代-拉马努金公式，1918）
>
> 更神奇的是，他发现分拆数满足同余式：
> \\(p(5n+4) \equiv 0 \pmod 5\\)
> 这看起来像"随机"的整除性，背后却是模形式的深刻结构。
>
> 现代概率论中，**随机分拆**（Plancherel 测度）的极限形状
> 由 Vershik-Kerov-Logan-Shepp 发现，与拉马努金的模形式理论
> 有深刻联系——随机性与确定性在此交汇。

---

## 25.3 期望与方差 —— 随机变量的"均值"与"波动"

### 期望

离散：\\[ E[X] = \sum x_i P(X = x_i) \\]
连续：\\[ E[X] = \int_{-\infty}^{\infty} x f(x)\,dx \\]

**性质**：
- \\(E[aX + b] = aE[X] + b\\)
- \\(E[X + Y] = E[X] + E[Y]\\)（**无需独立！**）
- \\(X, Y\\) 独立时：\\(E[XY] = E[X]E[Y]\\)

### 方差

\\[ \text{Var}(X) = E[(X - E[X])^2] = E[X^2] - (E[X])^2 \\]
标准差 \\(\sigma = \sqrt{\text{Var}(X)}\\)。

**性质**：\\(\text{Var}(aX+b) = a^2\text{Var}(X)\\)；
独立时 \\(\text{Var}(X+Y) = \text{Var}(X) + \text{Var}(Y)\\)。

### 协方差与相关系数

\\[ \text{Cov}(X,Y) = E[(X-E[X])(Y-E[Y])] = E[XY] - E[X]E[Y] \\]
\\[ \rho = \frac{\text{Cov}(X,Y)}{\sigma_X \sigma_Y} \in [-1, 1] \\]

\\(\rho = 1\\) 完全正相关，\\(\rho = -1\\) 完全负相关，\\(\rho = 0\\) 不相关。
**注意**：不相关 ⇏ 独立（独立 ⟹ 不相关，反之不成立）。

---

## 25.4 大数定律与中心极限定理 —— 概率论的两座丰碑

### 大数定律（弱）

\\[ \bar{X}_n = \frac{X_1 + \cdots + X_n}{n} \xrightarrow{P} \mu \\]
即：\\(\forall \varepsilon > 0, \lim_{n\to\infty} P(|\bar{X}_n - \mu| > \varepsilon) = 0\\)。

**人话**：样本均值依概率收敛到期望——**频率稳定性的理论保证**。
抛硬币 10000 次，正面频率 ≈ 50%。

**证明**（切比雪夫不等式）：\\(P(|X-\mu| > \varepsilon) \leq \frac{\sigma^2}{\varepsilon^2}\\)，
应用于 \\(\bar{X}_n\\)（方差 \\(\sigma^2/n\\)）即得。

### 中心极限定理（CLT）

\\[ \frac{\bar{X}_n - \mu}{\sigma/\sqrt{n}} \xrightarrow{d} N(0,1) \\]

**人话**：大量独立随机变量之和，近似服从**正态分布**——
无论原来是什么分布！这是正态分布"无处不在"的根本原因。

### Python：见证大数定律与 CLT

```python
import random, math, statistics

# 大数定律：抛硬币频率 → 0.5
print("大数定律演示（抛硬币）：")
heads = 0
for n in [100, 1000, 10000, 100000]:
    for _ in range(n - heads):  # 继续抛
        pass
    # 重新模拟
    heads = sum(1 for _ in range(n) if random.random() < 0.5)
    print(f"  n={n:6d}: 正面频率 = {heads/n:.4f}")

# 中心极限定理：均匀分布之和 → 正态
print("\n中心极限定理演示（12个均匀分布之和）：")
def clt_sample():
    return sum(random.random() for _ in range(12)) - 6  # 近似 N(0,1)

samples = [clt_sample() for _ in range(50000)]
print(f"  均值 = {statistics.mean(samples):.4f} (理论 0)")
print(f"  方差 = {statistics.variance(samples):.4f} (理论 1)")
# 落在 [-1,1] 的比例
p = sum(1 for s in samples if abs(s) < 1) / len(samples)
print(f"  P(|X|<1) = {p:.4f} (正态理论 0.6827)")
```

---

## 📝 本章习题

**题 25.1** 掷三枚硬币，求至少出现一次正面的概率（用对立事件）。

**题 25.2** 某测试 95% 准确率，疾病发病率 0.1%。若检测阳性，求真患病概率（贝叶斯）。

**题 25.3** 设 \\(X \sim B(10, 0.3)\\)，求 \\(E[X]\\)，\\(\text{Var}(X)\\)，\\(P(X = 3)\\)。

**题 25.4** 证明：\\(E[aX + b] = aE[X] + b\\)（离散情形）。

**题 25.5** 用切比雪夫不等式估计：抛 10000 次硬币，正面频率与 0.5 之差超过 0.02 的概率上界。

**题 25.6**（思考）为什么"独立 ⟹ 不相关"但"不相关 ⇏ 独立"？举一个不相关但不独立的例子。
提示：设 \\(X \sim U[-1,1]\\)，\\(Y = X^2\\)。

---

## ✅ 习题解答

### 题 25.1
\\(P(\text{至少一次正面}) = 1 - P(\text{全反面}) = 1 - (1/2)^3 = 7/8\\)。

### 题 25.2
\\[ P(\text{病}|\text{阳}) = \frac{0.95 \times 0.001}{0.95 \times 0.001 + 0.05 \times 0.999} = \frac{0.00095}{0.0509} \approx 1.87\% \\]
**即使 95% 准确率，阳性了也只有 1.87% 真患病！**——罕见病的检测悖论。

### 题 25.3
\\(E[X] = np = 3\\)，\\(\text{Var}(X) = np(1-p) = 2.1\\)。
\\[ P(X=3) = \binom{10}{3}(0.3)^3(0.7)^7 = 120 \times 0.027 \times 0.08235 \approx 0.2668 \\]

### 题 25.4
\\[ E[aX+b] = \sum (ax_i + b)P(X=x_i) = a\sum x_iP(X=x_i) + b\sum P(X=x_i) = aE[X] + b \\] ∎

### 题 25.5
\\(\bar{X}_{10000}\\) 的方差 \\(= \frac{0.25}{10000} = 2.5\times 10^{-5}\\)。
\\[ P(|\bar{X} - 0.5| > 0.02) \leq \frac{2.5\times 10^{-5}}{0.02^2} = 0.0625 \\]
即概率不超过 6.25%。（实际用 CLT 算约 0%，切比雪夫界很粗糙但普适。）

### 题 25.6
\\(X \sim U[-1,1]\\)，\\(Y = X^2\\)。
\\(E[X] = 0\\)，\\(E[XY] = E[X^3] = 0\\)（奇函数对称区间积分为 0），
故 \\(\text{Cov}(X,Y) = 0 - 0 = 0\\)，不相关。
但显然不独立：\\(Y\\) 完全由 \\(X\\) 决定！
**教训**：相关系数只度量**线性**关系。

---

## 🎯 本章小结

| 概念 | 核心公式 | 意义 |
|---|---|---|
| 贝叶斯 | \\(P(A\|B) = \frac{P(B\|A)P(A)}{P(B)}\\) | 逆概率推理 |
| 期望方差 | \\(E[X]\\), \\(\text{Var} = E[X^2]-E[X]^2\\) | 随机变量的特征数 |
| 大数定律 | \\(\bar{X}_n \xrightarrow{P} \mu\\) | 频率稳定性的保证 |
| 中心极限 | 和 \\(\xrightarrow{d}\\) 正态 | 正态无处不在的原因 |

**第四卷完**。下一卷（第五卷）：实分析、抽象代数、拓扑——数学系的硬核核心。
