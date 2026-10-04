# 第二十三章：级数 —— 拉马努金的游乐场

> *"一个方程对我没有意义，除非它表达了上帝的一个思想。"*
> *—— 拉马努金（Srinivasa Ramanujan）*

---

## 23.1 收敛与发散 —— 无穷和的命运

### 部分和

级数 \\(\sum_{n=1}^{\infty} a_n\\) 的**部分和**：
\\[ S_N = a_1 + a_2 + \cdots + a_N \\]
若 \\(\lim_{N\to\infty} S_N = S\\) 存在，则级数**收敛**于 \\(S\\)，否则**发散**。

### 几何级数（最重要的级数）

\\[ \sum_{n=0}^{\infty} r^n = 1 + r + r^2 + \cdots = \frac{1}{1-r}, \quad |r| < 1 \\]

**证明**：\\(S_N = \frac{1-r^{N+1}}{1-r}\\)，\\(|r|<1\\) 时 \\(r^{N+1} \to 0\\)。∎

### p-级数

\\[ \sum_{n=1}^{\infty} \frac{1}{n^p} \begin{cases} \text{收敛} & p > 1 \\\\ \text{发散} & p \leq 1 \end{cases} \\]

特别地，**调和级数** \\(\sum \frac{1}{n}\\) 发散（\\(p=1\\)）！
**证明**（分组法）：
\\[ 1 + \frac{1}{2} + \left(\frac{1}{3}+\frac{1}{4}\right) + \left(\frac{1}{5}+\cdots+\frac{1}{8}\right) + \cdots > 1 + \frac{1}{2} + \frac{1}{2} + \frac{1}{2} + \cdots = \infty \\]

### 收敛判别法

| 方法 | 内容 | 适用 |
|---|---|---|
| 比较法 | \\(0 \leq a_n \leq b_n\\)，\\(\sum b_n\\) 收敛 ⟹ \\(\sum a_n\\) 收敛 | 通用 |
| 比值法 | \\(\lim \frac{a_{n+1}}{a_n} = L\\)：\\(L<1\\) 收敛，\\(L>1\\) 发散 | 含阶乘/指数 |
| 根值法 | \\(\lim \sqrt[n]{\|a_n\|} = L\\)：\\(L<1\\) 收敛 | 含 \\(n\\) 次幂 |
| 交错级数 | \\(a_n \downarrow 0\\) 则 \\(\sum (-1)^n a_n\\) 收敛 | 莱布尼茨 |
| 积分法 | \\(\sum f(n)\\) 与 \\(\int f\\) 同敛散 | \\(f\\) 单调递减 |

### Python：调和级数发散演示

```python
def harmonic_partial(N):
    return sum(1/n for n in range(1, N+1))

import math
for N in [100, 1000, 10000, 100000]:
    s = harmonic_partial(N)
    print(f"N={N:6d}: 部分和={s:.4f}, ln(N)+γ={math.log(N)+0.5772:.4f}")
# 部分和 ~ ln(N) + γ（欧拉常数），缓慢发散到无穷
```

---

## 23.2 幂级数 —— 函数的无穷多项式

### 收敛半径

\\[ \sum_{n=0}^{\infty} c_n (x-a)^n \\]
在 \\(|x - a| < R\\) 收敛，\\(|x-a| > R\\) 发散。
\\[ R = \frac{1}{\limsup \sqrt[n]{|c_n|}} \quad \text{（根值法）} \\]

### 重要幂级数（\\(a = 0\\)）

\\[ e^x = \sum_{n=0}^{\infty} \frac{x^n}{n!}, \quad R = \infty \\]

\\[ \frac{1}{1-x} = \sum_{n=0}^{\infty} x^n, \quad |x| < 1 \\]

\\[ \ln(1+x) = \sum_{n=1}^{\infty} \frac{(-1)^{n+1}x^n}{n}, \quad |x| < 1 \\]

\\[ \arctan x = \sum_{n=0}^{\infty} \frac{(-1)^n x^{2n+1}}{2n+1}, \quad |x| \leq 1 \\]

**莱布尼茨公式**（\\(x = 1\\) 代入 \\(\arctan\\)）：
\\[ \frac{\pi}{4} = 1 - \frac{1}{3} + \frac{1}{5} - \frac{1}{7} + \cdots \\]
收敛极慢（算 100 万项才得 5 位精度）——拉马努金对此嗤之以鼻，他有快得多的公式！

---

## 23.3 🌟 拉马努金的 1/π 公式 —— 本章高潮

### 背景

1914 年，拉马努金在论文 *"Modular Equations and Approximations to π"* 中给出了 **17 个** 1/π 级数公式，没有证明。哈代说："我从未见过这样的公式。"

### 公式一（每项 8 位精度！）

\\[ \frac{1}{\pi} = \frac{2\sqrt{2}}{9801} \sum_{k=0}^{\infty} \frac{(4k)! \, (1103 + 26390k)}{(k!)^4 \cdot 396^{4k}} \\]

**有多快？** 第一项（\\(k=0\\)）：
\\[ \frac{2\sqrt{2}}{9801} \times 1103 \approx 0.318309886\ldots \\]
而 \\(1/\pi \approx 0.31830988618379\ldots\\)
**第一项就对了 7 位小数！** 每加一项，多约 8 位。

### Python：见证奇迹

```python
import math
from math import factorial, sqrt

def ramanujan_pi(terms):
    """拉马努金 1/π 公式"""
    s = 0
    for k in range(terms):
        num = factorial(4*k) * (1103 + 26390*k)
        den = (factorial(k)**4) * (396**(4*k))
        s += num / den
    return 1 / ((2*sqrt(2)/9801) * s)

print("拉马努金公式算 π：")
for t in [1, 2, 3]:
    approx = ramanujan_pi(t)
    print(f"  {t} 项: {approx:.15f}")
print(f"  真值: {math.pi:.15f}")

# 对比莱布尼茨公式
def leibniz_pi(terms):
    return 4 * sum((-1)**n / (2*n+1) for n in range(terms))

print(f"\n莱布尼茨 100万项: {leibniz_pi(1000000):.10f}")
print(f"拉马努金   2 项 : {ramanujan_pi(2):.15f}")
```

### 公式二（Chudnovsky 算法的基础）

\\[ \frac{1}{\pi} = 12 \sum_{k=0}^{\infty} \frac{(-1)^k (6k)! \, (13591409 + 545140134k)}{(3k)! \, (k!)^3 \cdot 640320^{3k+3/2}} \\]

每项增加约 **14 位**！1989 年 Chudnovsky 兄弟用它算出 10 亿位 π，
至今仍是 π 计算的世界纪录算法之一。

### 为什么成立？（证明思路）

1. **超几何级数**：这些公式都是 \\({}_2F_1\\) 型超几何级数在特殊点的值
2. **模方程**：拉马努金发现了 7 阶模方程 \\( \alpha\beta = \text{...} \\)
3. **Clausen 恒等式**：把超几何级数的平方转化为 \\({}_3F_2\\)
4. **奇异值理论**：\\(e^{-\pi\sqrt{58}}\\) 等"奇异值"给出代数数

完整证明需要模形式理论（Borwein 兄弟 1987 年才给出严格证明）。
拉马努金**没有**现代模形式工具——他是凭直觉"看到"的。

> 💡 **给大学生的思考**：拉马努金的公式不是"猜"出来的。
> 他有系统的"模方程"方法，只是没写成现代数学的语言。
> 这告诉我们：**直觉和严格同样重要**。

### 更多拉马努金级数

**无穷乘积**（分拆函数的生成函数）：
\\[ \sum_{n=0}^{\infty} p(n) x^n = \prod_{k=1}^{\infty} \frac{1}{1 - x^k} \\]
其中 \\(p(n)\\) 是分拆数。拉马努金发现：
\\[ p(5n+4) \equiv 0 \pmod 5, \quad p(7n+5) \equiv 0 \pmod 7, \quad p(11n+6) \equiv 0 \pmod{11} \\]

**Rogers-Ramanujan 恒等式**：
\\[ \sum_{n=0}^{\infty} \frac{q^{n^2}}{(q;q)_n} = \prod_{n=1}^{\infty} \frac{1}{(1-q^{5n-1})(1-q^{5n-4})} \\]
其中 \\((q;q)_n = (1-q)(1-q^2)\cdots(1-q^n)\\)。
这个恒等式在统计物理（硬六边形模型）、共形场论中都有应用！

---

## 23.4 傅里叶级数入门 —— 把函数拆成波

### 核心思想

任何周期函数都可以分解为正弦/余弦波的叠加：
\\[ f(x) = \frac{a_0}{2} + \sum_{n=1}^{\infty} \left[ a_n \cos(nx) + b_n \sin(nx) \right] \\]

其中：
\\[ a_n = \frac{1}{\pi}\int_{-\pi}^{\pi} f(x)\cos(nx)\,dx, \quad b_n = \frac{1}{\pi}\int_{-\pi}^{\pi} f(x)\sin(nx)\,dx \\]

### 例：方波的傅里叶级数

\\[ f(x) = \begin{cases} 1 & 0 < x < \pi \\\\ -1 & -\pi < x < 0 \end{cases} \\]
\\[ f(x) = \frac{4}{\pi}\left( \sin x + \frac{\sin 3x}{3} + \frac{\sin 5x}{5} + \cdots \right) \\]

取 \\(x = \pi/2\\)：\\(1 = \frac{4}{\pi}(1 - \frac{1}{3} + \frac{1}{5} - \cdots)\\)，
又得到莱布尼茨公式！数学是圆的。

### Python：方波合成演示

```python
import math

def square_wave_fourier(x, terms):
    s = 0
    for n in range(terms):
        k = 2*n + 1  # 奇数项
        s += math.sin(k*x) / k
    return (4/math.pi) * s

# 测试 x=π/2（方波值为1）
for t in [1, 3, 10, 50]:
    print(f"{t} 项逼近: {square_wave_fourier(math.pi/2, t):.6f} (真值=1)")

# 吉布斯现象：间断点附近总有约9%的过冲，项数再多也不消失！
```

> 🌟 **拉马努金角**
> 拉马努金在 Lost Notebook 中研究了 **mock theta 函数**：
> \\[ f(q) = \sum_{n=0}^{\infty} \frac{q^{n^2}}{(-q;q)_n^2} \\]
> 它们"几乎"是模形式，但差一点。这种"差一点"的结构，
> 80 年后被 Zwegers（2002）用**调和 Maass 形式**严格化，
> 并在黑洞熵的计算（弦理论）中发挥作用！
> 拉马努金去世前一年（1919）在病床上写下这些函数时，
> 人类还不知道它们是什么。

---

## 📝 本章习题

**题 23.1** 用比值法判断 \\(\sum_{n=1}^{\infty} \frac{n!}{n^n}\\) 的收敛性。

**题 23.2** 求幂级数 \\(\sum_{n=0}^{\infty} \frac{x^n}{n!}\\) 的收敛半径。

**题 23.3** 用 Python 验证拉马努金 1/π 公式（1 项）的精度，计算与 \\(\pi\\) 的误差。

**题 23.4** 证明：若 \\(\sum a_n\\) 绝对收敛，则 \\(\sum a_n\\) 收敛。
（提示：\\(0 \leq a_n + |a_n| \leq 2|a_n|\\)）

**题 23.5** 求 \\(\sum_{n=1}^{\infty} \frac{1}{n(n+1)}\\) 的和。
提示：\\(\frac{1}{n(n+1)} = \frac{1}{n} - \frac{1}{n+1}\\)（裂项）。

**题 23.6**（思考）调和级数发散，但 \\(\sum \frac{(-1)^{n+1}}{n} = \ln 2\\) 收敛。
为什么加了正负号就从发散变收敛了？这说明了什么？

---

## ✅ 习题解答

### 题 23.1
\\[ \frac{a_{n+1}}{a_n} = \frac{(n+1)!/(n+1)^{n+1}}{n!/n^n} = \frac{n^n}{(n+1)^n} = \frac{1}{(1+1/n)^n} \to \frac{1}{e} < 1 \\]
故收敛。∎

### 题 23.2
\\(\sqrt[n]{|c_n|} = \sqrt[n]{1/n!} \to 0\\)（因为 \\(n!\\) 增长比指数快），
故 \\(R = \infty\\)。这是 \\(e^x\\) 的级数，全平面收敛。

### 题 23.3
```python
import math
from math import factorial, sqrt
s = factorial(0)*(1103+0) / (1 * 1)  # k=0 项
approx = 1 / ((2*sqrt(2)/9801) * s)
print(f"1项近似: {approx:.15f}")
print(f"真值:     {math.pi:.15f}")
print(f"误差: {abs(approx - math.pi):.2e}")
# 误差约 7.6e-08（7位精度！）
```

### 题 23.4
设 \\(b_n = a_n + |a_n|\\)，则 \\(0 \leq b_n \leq 2|a_n|\\)。
\\(\sum |a_n|\\) 收敛 ⟹ \\(\sum 2|a_n|\\) 收敛 ⟹ \\(\sum b_n\\) 收敛（比较法）。
又 \\(a_n = b_n - |a_n|\\)，两收敛级数之差收敛。∎

### 题 23.5
\\[ \sum_{n=1}^{N} \frac{1}{n(n+1)} = \sum_{n=1}^{N}\left(\frac{1}{n}-\frac{1}{n+1}\right) = 1 - \frac{1}{N+1} \to 1 \\]
这种" telescoping"（望远镜）级数是拉马努金最爱用的技巧之一。

### 题 23.6
**条件收敛 vs 绝对收敛**。
\\(\sum \frac{(-1)^{n+1}}{n}\\) 收敛（莱布尼茨），但 \\(\sum \frac{1}{n}\\) 发散。
说明：正负抵消可以"挽救"发散级数。
**深刻事实**（黎曼重排定理）：条件收敛级数重排求和顺序，可以得到**任意**实数！
这就是为什么绝对收敛如此重要——它保证求和与顺序无关。

---

## 🎯 本章小结

| 概念 | 核心 | 拉马努金联系 |
|---|---|---|
| 收敛判别 | 比值/根值/比较/积分 | 他凭直觉判断收敛 |
| 幂级数 | \\(R\\) 内可逐项微积分 | 1/π 公式的载体 |
| 拉马努金 1/π | 每项 8-14 位精度 | 模方程的奇迹 |
| 傅里叶级数 | 函数分解为波 | 调和分析的起点 |
| mock theta | "差一点"的模形式 | 黑洞熵、弦理论 |

**下一章**：线性代数——从解方程组到向量空间，数学中最有用的工具箱。
