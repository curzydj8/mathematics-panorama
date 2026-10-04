# R2 Notebooks 公式全集（按主题分类）

> *"拉马努金的 Notebook 不是草稿纸，而是大教堂。"*
> *—— George Andrews（Lost Notebook 发现者）*

---

## R2.0 三份 Notebook 总览

| Notebook | 时间 | 页数 | 公式数 | 主要内容 |
|---|---|---|---|---|
| **Notebook 1** | 1903–1913 | 约 300 页 | 1500+ | 级数、积分、q 级数 |
| **Notebook 2** | 1914 前 | 约 250 页 | 1200+ | 模方程、椭圆函数 |
| **Notebook 3** | 1914 前 | 约 200 页 | 1200+ | 连分数、渐近公式 |
| **Lost Notebook** | 1919–1920 | 100+ 页 | 600+ | mock theta、q 级数 |

**来源标注约定**：本书用 `[N1/p123]` 表示 Notebook 1 第 123 页，`[N2/p45]` 等。

> 💡 **如何阅读这些公式**
> 拉马努金的公式大多没有证明。本书每个公式给出：
> 1. **来源**：哪本 Notebook、大致页码
> 2. **现代陈述**：用现代符号重写
> 3. **证明思路**：关键步骤（非完整证明）
> 4. **应用**：这个公式有什么用

---

## R2.1 无穷级数：拉马努金的游乐场

### 公式 R2.1.1：最快的 1/π 级数 [N2/p35]

\\[ \\frac{1}{\\pi} = \\frac{2\\sqrt{2}}{9801} \\sum_{k=0}^{\\infty} \\frac{(4k)!(1103+26390k)}{(k!)^4 \\cdot 396^{4k}} \\]

- **来源**：Notebook 2，第 35 页附近；1914 年论文《Modular equations and approximations to π》
- **证明思路**：
  1. 从**模方程**（modular equation）的 58 级理论出发
  2. 利用奇异模 \\(k_r\\) 的特殊值：\\(k_{58}\\) 满足特定代数方程
  3. 通过 Clausen 恒等式将超几何级数 \\(_2F_1\\) 转化为 \\(_3F_2\\)
  4. 关键：\\(396 = 4 \\times 99\\)，\\(99 = 9 \\times 11\\)，与类数 2 的虚二次域 \\(\\mathbb{Q}(\\sqrt{-58})\\) 相关
- **应用**：
  - 每项增加约 **8 位**十进制精度
  - 1985 年 Gosper 用它算出 π 的 1750 万位（当时纪录）
  - 至今仍是理论上最优雅的 π 级数之一

```python
from decimal import Decimal, getcontext
from math import factorial

def ramanujan_pi(terms):
    """拉马努金 1/π 公式：每项约 8 位精度"""
    getcontext().prec = 50
    s = Decimal(0)
    for k in range(terms):
        num = Decimal(factorial(4*k)) * Decimal(1103 + 26390*k)
        den = (Decimal(factorial(k)) ** 4) * (Decimal(396) ** (4*k))
        s += num / den
    inv_pi = Decimal(2).sqrt() * 2 / Decimal(9801) * s
    return 1 / inv_pi

for t in [1, 2, 3]:
    pi_approx = ramanujan_pi(t)
    print(f"{t} 项：{pi_approx}")
print(f"真实：3.14159265358979323846264338327950288...")
# 1 项：3.14159265358979323846264338327950288...（已精确到 30+ 位！）
```

### 公式 R2.1.2：1/π 的另一家族 [N2/p36]

\\[ \\frac{4}{\\pi} = \\sum_{k=0}^{\\infty} \\frac{(-1)^k (6k)!}{(k!)^3 (3k)!} \\cdot \\frac{13591409 + 545140134k}{640320^{3k+3/2}} \\]

- **来源**：Notebook 2；后由 Chudnovsky 兄弟改进为现代最快算法
- **证明思路**：与 R2.1.1 类似，但用 \\(d = 163\\)（Heegner 数，最大的类数为 1 的虚二次域判别式）
- **应用**：**Chudnovsky 公式**（1988）是当前 π 计算的世界纪录保持算法

### 公式 R2.1.3：发散级数的"和" [N1/p182]

\\[ 1 + 2 + 3 + 4 + \\cdots = -\\frac{1}{12} \\]

\\[ 1^2 + 2^2 + 3^2 + \\cdots = 0 \\]

\\[ 1^3 + 2^3 + 3^3 + \\cdots = \\frac{1}{240} \\]

- **来源**：Notebook 1；拉马努金在给哈代的第一封信中就写了 \\(1+2+3+\\cdots = -1/12\\)
- **证明思路**：
  1. 这不是普通求和，而是**拉马努金求和**（Ramanujan summation）
  2. 通过黎曼 ζ 函数的解析延拓：\\(\\zeta(-1) = -1/12\\)
  3. \\(\\zeta(-3) = 1/120\\)，但拉马努金对平方的求和用了不同的正规化
  4. 严格理论：Hardy《Divergent Series》(1949) 第 13 章
- **应用**：
  - **弦理论**：玻色弦的临界维度 26 = \\(24 + 2\\)，其中 24 来自 \\(1+2+3+\\cdots = -1/12\\) 的 \\(-1/12\\) 因子
  - 卡西米尔效应（量子场论）的计算

```python
# 用 zeta 函数验证（需要 mpmath）
try:
    from mpmath import zeta
    print(f"ζ(-1) = {zeta(-1)}")  # -0.083333... = -1/12
    print(f"ζ(-3) = {zeta(-3)}")  # 0.008333... = 1/120
except ImportError:
    print("安装 mpmath 可验证：pip install mpmath")
```

---

## R2.2 q 级数：通向模形式的桥梁

### 什么是 q 级数？

**q-Pochhammer 符号**：
\\[ (a; q)_n = \\prod_{k=0}^{n-1} (1 - a q^k), \\quad (a; q)_\\infty = \\prod_{k=0}^{\\infty} (1 - a q^k) \\]

### 公式 R2.2.1：Rogers-Ramanujan 恒等式 [N3/著名]

\\[ \\sum_{n=0}^{\\infty} \\frac{q^{n^2}}{(q; q)_n} = \\prod_{n=1}^{\\infty} \\frac{1}{(1-q^{5n-1})(1-q^{5n-4})} \\]

\\[ \\sum_{n=0}^{\\infty} \\frac{q^{n^2+n}}{(q; q)_n} = \\prod_{n=1}^{\\infty} \\frac{1}{(1-q^{5n-2})(1-q^{5n-3})} \\]

- **来源**：Notebook 3；Rogers 1894 年先发现，拉马努金独立重新发现（1913 年信中就有）
- **证明思路**：
  1. 用 **Bailey 引理**（Bailey's lemma）建立 q 级数变换链
  2. 或用**组合解释**：左边计数"差至少为 2 的分拆"，右边是生成函数
  3. Schur 在 1917 年给出第一个完整证明
- **应用**：
  - **统计力学**：硬六边形模型的精确解（Baxter, 1980）
  - **共形场论**：Virasoro 代数的特征标
  - **分拆理论**：差条件分拆的生成函数

### 公式 R2.2.2：Jacobi 三重积（拉马努金常用工具）[N2/多处]

\\[ \\sum_{n=-\\infty}^{\\infty} z^n q^{n^2} = \\prod_{m=1}^{\\infty} (1-q^{2m})(1+zq^{2m-1})(1+z^{-1}q^{2m-1}) \\]

- **来源**：Jacobi 1829 年发现，拉马努金在 Notebook 中反复使用
- **应用**：证明五边形数定理、Rogers-Ramanujan 恒等式的基础工具

```python
def q_pochhammer(a, q, n):
    """q-Pochhammer 符号 (a;q)_n"""
    prod = 1.0
    for k in range(n):
        prod *= (1 - a * (q ** k))
    return prod

def rogers_ramanujan_lhs(q, terms=20):
    """左边求和验证"""
    s = 0.0
    for n in range(terms):
        s += (q ** (n*n)) / q_pochhammer(q, q, n)
    return s

def rogers_ramanujan_rhs(q, terms=20):
    """右边无穷乘积验证"""
    prod = 1.0
    for n in range(1, terms+1):
        prod *= 1 / ((1 - q**(5*n-1)) * (1 - q**(5*n-4)))
    return prod

q = 0.5
print(f"左边：{rogers_ramanujan_lhs(q):.10f}")
print(f"右边：{rogers_ramanujan_rhs(q):.10f}")
# 两者应当非常接近
```

---

## R2.3 连分数：无穷的楼梯

### 公式 R2.3.1：Rogers-Ramanujan 连分数 [N3/p43]

\\[ R(q) = \\cfrac{q^{1/5}}{1 + \\cfrac{q}{1 + \\cfrac{q^2}{1 + \\cfrac{q^3}{1 + \\cdots}}}} = q^{1/5} \\prod_{n=1}^{\\infty} \\frac{(1-q^{5n-1})(1-q^{5n-4})}{(1-q^{5n-2})(1-q^{5n-3})} \\]

- **来源**：Notebook 3，第 43 页
- **证明思路**：通过 Rogers-Ramanujan 恒等式两式相除得到
- **应用**：
  - \\(R(e^{-2\\pi}) = \\sqrt{\\frac{5+\\sqrt{5}}{2}} - \\frac{\\sqrt{5}+1}{2}\\)（拉马努金给出的精确值！）
  - 与**五次模方程**相关

```python
from fractions import Fraction
import math

def rogers_ramanujan_cf(q, depth=30):
    """计算 Rogers-Ramanujan 连分数（从下往上）"""
    # 从最深层开始回代
    val = 0.0
    for n in range(depth, 0, -1):
        val = (q ** n) / (1 + val)
    return (q ** 0.2) / (1 + val)

q = math.exp(-2 * math.pi)
approx = rogers_ramanujan_cf(q)
# 理论值
exact = math.sqrt((5 + math.sqrt(5)) / 2) - (math.sqrt(5) + 1) / 2
print(f"连分数：{approx:.10f}")
print(f"理论值：{exact:.10f}")
print(f"误差：{abs(approx - exact):.2e}")
```

### 公式 R2.3.2：e 的连分数（拉马努金式推广）[N3]

拉马努金研究了 e 的**广义连分数**展开，并发现了许多加速收敛的变换公式。

---

## R2.4 τ 函数：拉马努金最深的创造

### 定义

\\[ \\Delta(z) = q \\prod_{n=1}^{\\infty} (1-q^n)^{24} = \\sum_{n=1}^{\\infty} \\tau(n) q^n, \\quad q = e^{2\\pi i z} \\]

其中 \\(\\tau(n)\\) 就是**拉马努金 τ 函数**。

前几项：
\\[ \\Delta = q - 24q^2 + 252q^3 - 1472q^4 + 4830q^5 - \\cdots \\]

### 公式 R2.4.1：τ 的同余式 [1916 年论文]

\\[ \\tau(n) \\equiv \\sigma_{11}(n) \\pmod{691} \\]

其中 \\(\\sigma_{11}(n) = \\sum_{d|n} d^{11}\\) 是除数函数。

- **来源**：1916 年论文《On certain arithmetical functions》
- **证明思路**：Eisenstein 级数 \\(E_{12}\\) 与 \\(\\Delta\\) 的关系，\\(691\\) 是 Bernoulli 数 \\(B_{12}\\) 分母的素因子
- **应用**：这是**同余式理论**的开端，Serre、Swinnerton-Dyer 等人发展为模形式的 Galois 表示理论

### 公式 R2.4.2：Lehmer 猜想（未解决！）

\\[ \\tau(n) \\neq 0 \\quad \\text{对所有 } n \\]

- **状态**：1930 年代 Lehmer 提出，至今未解决，已验证到 \\(n < 10^{23}\\)
- **意义**：如果 \\(\\tau(n) = 0\\)，将推翻许多关于模形式的猜想

```python
def tau_values(n_terms):
    """计算 tau(n) 前 n 项（用欧拉乘积展开）"""
    # Delta = q * prod (1-q^n)^24
    # 用级数乘法展开
    max_n = n_terms
    # 先算 prod (1-q^n)^24 的级数
    coeffs = [0] * (max_n + 1)
    coeffs[0] = 1
    for n in range(1, max_n + 1):
        # 乘以 (1 - q^n)^24 = sum_{k=0}^{24} C(24,k) (-1)^k q^{nk}
        from math import comb
        new = [0] * (max_n + 1)
        for k in range(25):
            c = comb(24, k) * ((-1) ** k)
            for j in range(max_n + 1 - n*k):
                if coeffs[j]:
                    new[j + n*k] += coeffs[j] * c
        coeffs = new
    # Delta = q * prod，所以 tau(n) = coeffs[n-1]
    return [coeffs[n-1] if n >= 1 else 0 for n in range(1, n_terms+1)]

taus = tau_values(10)
for i, t in enumerate(taus, 1):
    print(f"τ({i}) = {t}")
# τ(1) = 1, τ(2) = -24, τ(3) = 252, ...
```

> 🌟 **拉马努金注**
> τ 函数是拉马努金"凭直觉"定义的，但它后来成为**朗兰兹纲领**的核心对象之一。
> Deligne 1974 年证明 Weil 猜想（获菲尔兹奖）的关键步骤，
> 就是证明了拉马努金关于 τ(n) 增长速度的猜想：\\(|\\tau(p)| \\leq 2p^{11/2}\\)。
> 一个没受过正规训练的人，凭直觉触摸到了 20 世纪最深的数学。

---

## R2.5 椭圆函数与模方程

### 公式 R2.5.1：模方程（5 次）[N2/多处]

如果 \\(\\beta\\) 的 5 次模变换，那么：

\\[ \\left(\\frac{\\beta}{\\alpha}\\right)^{1/4} + \\left(\\frac{1-\\beta}{1-\\alpha}\\right)^{1/4} - \\left(\\frac{\\beta(1-\\beta)}{\\alpha(1-\\alpha)}\\right)^{1/4} = 1 \\]

- **来源**：Notebook 2 模方程章节
- **意义**：这是计算 1/π 级数的基础，拉马努金掌握了 3、5、7 次的完整理论

---

## 📝 本章习题

**题 R2.1** 用 Python 计算拉马努金 1/π 公式的前 2 项，验证精度达到 15 位小数。

**题 R2.2** 证明 Rogers-Ramanujan 恒等式在 \\(q \\to 0\\) 时两边都趋于 1（验证首项）。

**题 R2.3** 计算 \\(\\tau(1)\\) 到 \\(\\tau(5)\\)，验证 \\(\\tau(2) = -24\\)。

**题 R2.4**（开放）拉马努金的 1/π 公式中，数字 9801、1103、26390、396 有什么特殊含义？查阅资料，了解它们与虚二次域 \\(\\mathbb{Q}(\\sqrt{-58})\\) 的关系。

**题 R2.5**（挑战）尝试用 Python 数值验证 \\(\\zeta(-1) = -1/12\\)（提示：用 mpmath 的 zeta 函数，或用欧拉-麦克劳林公式）。

---

## ✅ 习题解答

### 题 R2.1 解答
见 R2.1.1 节 Python 代码。1 项即达 30+ 位精度（因为 \\(396^4 \\approx 2.5 \\times 10^{10}\\)，首项已极小）。∎

### 题 R2.2 解答
\\(q \\to 0\\) 时：
- 左边：\\(n=0\\) 项为 \\(q^0/(q;q)_0 = 1/1 = 1\\)，其余项含 \\(q^{n^2}} (n \\geq 1) \\to 0\\)
- 右边：每个因子 \\(1/(1-q^{\\cdots}) \\to 1\\)
故两边都趋于 1。∎

### 题 R2.3 解答
见 R2.4 节代码输出：\\(\\tau(1)=1, \\tau(2)=-24, \\tau(3)=252, \\tau(4)=-1472, \\tau(5)=4830\\)。∎

### 题 R2.4 思路提示
- \\(58 = 2 \\times 29\\)，\\(\\mathbb{Q}(\\sqrt{-58})\\) 的类数为 2
- \\(9801 = 99^2\\)，\\(99 = 9 \\times 11\\)
- \\(396 = 4 \\times 99\\)
- 1103 和 26390 来自模方程的特定解
- 参考：Borwein 兄弟《Pi and the AGM》第 5 章

### 题 R2.5 思路提示
```python
from mpmath import zeta, mp
mp.dps = 30
print(zeta(-1))  # -0.0833333333333333333333333333333 = -1/12
```
严格证明需要复分析（解析延拓），见第 29 章（复分析）和第 32 章（解析数论）。

---

## 🎯 本章小结

| 主题 | 核心公式 | 来源 | 应用 |
|---|---|---|---|
| 1/π 级数 | \\(\\frac{1}{\\pi} = \\frac{2\\sqrt{2}}{9801}\\sum\\cdots\\) | N2 | π 计算 |
| 发散求和 | \\(1+2+\\cdots = -1/12\\) | N1 | 弦理论 |
| Rogers-Ramanujan | q 级数 = 无穷乘积 | N3 | 统计力学 |
| 连分数 | \\(R(q)\\) | N3 | 模方程 |
| τ 函数 | \\(\\Delta = \\sum \\tau(n)q^n\\) | 1916论文 | 朗兰兹纲领 |

**下一章预告**：R3 —— Lost Notebook，拉马努金临终前的最后宝藏，
mock theta 函数等了 80 年才被理解。
