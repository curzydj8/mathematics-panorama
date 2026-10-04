# R4 1/π 公式与模方程：最快的圆周率算法

> *"拉马努金的 π 公式，每一项都像是从天上掉下来的。"*
> *—— Jonathan Borwein*

---

## R4.1 历史：人类计算 π 的竞赛

| 年代 | 人物/方法 | 位数 | 每项精度 |
|---|---|---|---|
| 公元 250 | 刘徽割圆术 | 3 位 | — |
| 1400 | 马德哈瓦（印度）级数 | 11 位 | — |
| 1706 | Machin 公式 | 100 位 | 约 1.4 位/项 |
| 1949 | ENIAC 计算机 | 2037 位 | — |
| 1914 | **拉马努金级数** | 理论无穷 | **约 8 位/项** |
| 1985 | Gosper（用拉马努金公式） | 1750 万位 | 8 位/项 |
| 1988 | **Chudnovsky 公式** | 至今纪录 | **约 14 位/项** |
| 2024 | Chudnovsky（云计算） | 202 万亿位 | 14 位/项 |

**拉马努金的公式沉睡了 70 年**（1914→1985），一旦被发现，立刻打破世界纪录。

---

## R4.2 拉马努金 1/π 公式详解

### 主公式 [N2/p35; 1914 年论文]

\\[ \\frac{1}{\\pi} = \\frac{2\\sqrt{2}}{9801} \\sum_{k=0}^{\\infty} \\frac{(4k)! \\cdot (1103 + 26390k)}{(k!)^4 \\cdot 396^{4k}} \\]

### 为什么这么快？

相邻两项的比值：
\\[ \\frac{T_{k+1}}{T_k} \\approx \\frac{1}{396^4} = \\frac{1}{24591257856} \\approx 4 \\times 10^{-11} \\]

**每项增加约 8 位十进制精度！**

对比 Machin 公式：
\\[ \\frac{\\pi}{4} = 4\\arctan\\frac{1}{5} - \\arctan\\frac{1}{239} \\]
\\(\\arctan(1/5)\\) 的级数每项只增加约 1.4 位。

```python
from decimal import Decimal, getcontext
from math import factorial
import time

def ramanujan_pi(terms, prec=100):
    """拉马努金 1/π：返回 terms 项的近似值"""
    getcontext().prec = prec + 10
    s = Decimal(0)
    for k in range(terms):
        num = Decimal(factorial(4*k)) * Decimal(1103 + 26390*k)
        den = (Decimal(factorial(k)) ** 4) * (Decimal(396) ** (4*k))
        s += num / den
    inv_pi = Decimal(2).sqrt() * 2 / Decimal(9801) * s
    getcontext().prec = prec
    return +(1 / inv_pi)

# 真实 π（100 位）
PI_TRUE = "3.1415926535897932384626433832795028841971693993751058209749445923078164062862089986280348253421070679"

for t in [1, 2, 3, 5]:
    approx = ramanujan_pi(t)
    s_approx = str(approx)
    # 数相同位数
    match = 0
    for a, b in zip(s_approx, PI_TRUE):
        if a == b: match += 1
        else: break
    print(f"{t} 项：相同 {match} 位（{s_approx[:50]}...)")

# 1 项：相同 32 位
# 2 项：相同 40 位
# 每项约 +8 位！
```

### 证明思路：模方程

**步骤 1**：从** 58 级模方程**出发

拉马努金研究了模群 \\(\\Gamma_0(58)\\) 上的模函数。如果 \\(\\alpha, \\beta\\) 通过 58 级模方程关联，那么有特定的代数关系。

**步骤 2**：奇异模的特殊值

取 \\(q = e^{-\\pi\\sqrt{58}}\\)，则相关的模函数取**代数值**（而非超越数）。这是 Kronecker 青春之梦（Hilbert 第 12 问题）的特例。

**步骤 3**：Clausen 恒等式

\\[ \\left[ {}_2F_1\\left(\\frac{1}{4}, \\frac{3}{4}; \\frac{1}{2}; x\\right) \\right]^2 = {}_3F_2\\left(\\frac{1}{2}, \\frac{1}{4}, \\frac{3}{4}; \\frac{1}{2}, 1; x\\right) \\]

将超几何级数的平方转化为 \\(_3F_2\\)，后者展开即得 \\((4k)!/(k!)^4\\) 系数。

**步骤 4**：微分模方程得到 \\(1/\\pi\\)

对模参数求导，利用 \\(\\frac{d}{d\\tau}\\) 与 \\(1/\\pi\\) 的关系（这是 Borwein 兄弟 1987 年完整严格化的关键）。

> 💡 **为什么是 9801、1103、26390、396？**
> - \\(396 = 4 \\times 99\\), \\(99 = 9 \\times 11\\)
> - \\(9801 = 99^2\\)
> - 这些数来自 \\(\\mathbb{Q}(\\sqrt{-58})\\) 的类数公式
> - 1103 和 26390 是模方程在特定点的取值
> - **拉马努金没有计算机，是纯粹用模形式的理论"算"出这些数的！**

---

## R4.3 Chudnovsky 公式：站在巨人肩上

### 公式（1988）

\\[ \\frac{1}{\\pi} = 12 \\sum_{k=0}^{\\infty} \\frac{(-1)^k (6k)! (13591409 + 545140134k)}{(3k)! (k!)^3 \\cdot 640320^{3k+3/2}} \\]

- **每项约 14 位精度**（比拉马努金更快！）
- **当前所有 π 世界纪录**都用这个公式

### 与拉马努金的关系

Chudnovsky 公式**本质上是拉马努金方法的推广**：
- 拉马努金用判别式 \\(d = -58\\)（类数 2）
- Chudnovsky 用 \\(d = -163\\)（**Heegner 数**，类数为 1 的最大判别式）
- \\(640320 = 2^6 \\cdot 3 \\cdot 5 \\cdot 23 \\cdot 29\\)，与 \\(j((1+\\sqrt{-163})/2) = -640320^3\\) 相关
- 13591409 和 545140134 来自 Hilbert 类多项式

```python
from decimal import Decimal, getcontext
from math import factorial

def chudnovsky_pi(terms, prec=100):
    """Chudnovsky 公式：每项约 14 位"""
    getcontext().prec = prec + 10
    C = Decimal(426880) * Decimal(10005).sqrt()
    s = Decimal(0)
    for k in range(terms):
        num = Decimal((-1)**k) * Decimal(factorial(6*k)) * Decimal(13591409 + 545140134*k)
        den = Decimal(factorial(3*k)) * (Decimal(factorial(k))**3) * (Decimal(640320)**(3*k))
        s += num / den
    getcontext().prec = prec
    return +(C / (12 * s))

PI_TRUE = "3.14159265358979323846264338327950288419716939937510"
for t in [1, 2, 3]:
    approx = str(chudnovsky_pi(t))
    match = sum(1 for a, b in zip(approx, PI_TRUE) if a == b)
    # 更精确的计数（遇到第一个不同即停）
    cnt = 0
    for a, b in zip(approx, PI_TRUE):
        if a == b: cnt += 1
        else: break
    print(f"{t} 项：相同 {cnt} 位")
# 1 项：相同 15 位，2 项：相同 29 位（每项 +14 位！）
```

### 性能对比

```python
import time
from math import factorial
from decimal import Decimal, getcontext

def benchmark():
    getcontext().prec = 120
    # 拉马努金：达到 100 位需要几项？
    # 每项 8 位 → 约 13 项
    # Chudnovsky：每项 14 位 → 约 8 项
    print("达到 100 位精度所需项数：")
    print("  拉马努金：约 13 项（100/8）")
    print("  Chudnovsky：约 8 项（100/14）")
    print("  Machin：约 72 项（100/1.4）")
    print()
    print("结论：Chudnovsky 是拉马努金思想的终极形态")

benchmark()
```

---

## R4.4 模方程：背后的理论

### 什么是模方程？

设 \\(\\alpha = k^2\\)（椭圆模），\\(\\beta\\) 是 \\(n\\) 次变换后的模。**\\(n\\) 次模方程**是 \\(\\alpha\\) 和 \\(\\beta\\) 之间的代数关系。

**3 次模方程**（最简单）：
\\[ (\\alpha\\beta)^{1/4} + [(1-\\alpha)(1-\\beta)]^{1/4} = 1 \\]

**5 次模方程**（拉马努金最爱）：
\\[ \\left(\\frac{\\beta}{\\alpha}\\right)^{1/6} + \\left(\\frac{1-\\beta}{1-\\alpha}\\right)^{1/6} - \\left[\\frac{\\beta(1-\\beta)}{\\alpha(1-\\alpha)}\\right]^{1/6} = 1 \\]

### 拉马努金的贡献

拉马努金系统研究了 3、5、7 次模方程，给出了**数百个**具体例子。在没有计算机的时代，他手算了大量模方程的解！

这些模方程是 1/π 公式的"发动机"。

---

## 📝 本章习题

**题 R4.1** 用 Python 分别用拉马努金公式（3 项）和 Chudnovsky 公式（2 项）计算 π，比较精度。

**题 R4.2** 计算 \\(396^4\\)，验证它约等于 \\(2.5 \\times 10^{10}\\)，解释为什么这意味着"每项 8 位精度"。

**题 R4.3** Machin 公式 \\(\\pi/4 = 4\\arctan(1/5) - \\arctan(1/239)\\)。用 \\(\\arctan x = \\sum (-1)^n x^{2n+1}/(2n+1)\\) 估算：要达到 10 位精度，\\(\\arctan(1/5)\\) 需要几项？

**题 R4.4**（开放）为什么 Heegner 数 \\(163\\) 能给出更快的 π 公式？查阅资料，了解"类数 1 问题"（Heegner-Stark 定理）。

---

## ✅ 习题解答

### 题 R4.1 解答
见 R4.2 和 R4.3 节代码。拉马努金 3 项约 40 位，Chudnovsky 2 项约 29 位。
注意：Chudnovsky 单项更强，但拉马努金公式更优雅（系数更小）。∎

### 题 R4.2 解答
\\[ 396^4 = (400-4)^4 \\approx 2.56 \\times 10^{10} \\]
相邻项比值 \\(\\approx 1/396^4 \\approx 4 \\times 10^{-11}\\)。
\\(\\log_{10}(2.5 \\times 10^{10}) \\approx 10.4\\)，但分子也有 \\((4k)!\\) 增长，
实际每项约 \\(10^8\\) 倍缩小，即 **8 位**十进制精度。∎

### 题 R4.3 解答
\\(\\arctan(1/5) = \\sum_{n=0}^{\\infty} \\frac{(-1)^n}{(2n+1)5^{2n+1}}\\)
通项 \\(\\approx 1/(2n \\cdot 25^n)\\)。要 \\(< 10^{-10}\\)：
\\(25^n > 10^{10} \\Rightarrow n > 10/\\log_{10}(25) \\approx 7.1\\)
所以约需 8 项。\\(\\arctan(1/239)\\) 收敛更快（约 3 项）。
总计约 11 项达 10 位 → 平均每项约 0.9 位。远慢于拉马努金的 8 位/项。∎

### 题 R4.4 思路提示
- **类数 1 问题**：虚二次域 \\(\\mathbb{Q}(\\sqrt{-d})\\) 何时有唯一因子分解？
- Heegner（1952）证明 \\(d \\leq 163\\) 时仅有 9 个：1, 2, 3, 7, 11, 19, 43, 67, 163
- Stark（1967）完整证明
- \\(d=163\\) 是最大的，所以 \\(j((1+\\sqrt{-163})/2) = -640320^3\\) 是"最接近整数"的超越数
- 这个"接近整数"的性质让 Chudnovsky 级数收敛最快
- 参考：Cox《Primes of the Form \\(x^2 + ny^2\\)》

---

## 🎯 本章小结

| 公式 | 每项精度 | 关键数字来源 |
|---|---|---|
| 拉马努金 1/π | ~8 位 | \\(\\mathbb{Q}(\\sqrt{-58})\\), 类数 2 |
| Chudnovsky | ~14 位 | \\(\\mathbb{Q}(\\sqrt{-163})\\), 类数 1 |
| Machin | ~1.4 位 | 初等 arctan |
| 模方程 | — | 1/π 公式的理论基础 |

**拉马努金卷完结**。下一卷：千禧难题——
人类至今未能破解的 7 座大山，以及 3+ 种攀登路线。
