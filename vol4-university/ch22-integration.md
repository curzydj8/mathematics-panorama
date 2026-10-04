# 第二十二章：积分学 —— 无穷求和的艺术

> *"上帝在积分，魔鬼在求导。"*
> *—— 数学系民间谚语（积分比求导难得多！）*

---

## 22.1 定积分的定义 —— 黎曼和

### 曲边梯形的面积

求 \\(y = x^2\\) 在 \\([0,1]\\) 与 \\(x\\) 轴围成的面积。

**黎曼和**：把 \\([0,1]\\) 分成 \\(n\\) 等份，每份宽 \\(\Delta x = 1/n\\)，
用右端点高度近似：
\\[ S_n = \sum_{i=1}^{n} f\left(\frac{i}{n}\right) \cdot \frac{1}{n} = \sum_{i=1}^{n} \left(\frac{i}{n}\right)^2 \cdot \frac{1}{n} = \frac{1}{n^3}\sum_{i=1}^{n} i^2 \\]

用 \\(\sum i^2 = \frac{n(n+1)(2n+1)}{6}\\)（第2章）：
\\[ S_n = \frac{(n+1)(2n+1)}{6n^2} \to \frac{1}{3} \quad (n \to \infty) \\]

### 定积分定义

\\[ \int_a^b f(x)\,dx = \lim_{n \to \infty} \sum_{i=1}^{n} f(\xi_i)\,\Delta x_i \\]
其中 \\(\Delta x_i \to 0\\)，\\(\xi_i\\) 是第 \\(i\\) 个小区间内任一点。

**几何意义**：曲边梯形的"精确"面积（\\(x\\) 轴上方为正，下方为负）。

### Python：黎曼和数值逼近

```python
def riemann_sum(f, a, b, n, method='right'):
    """黎曼和：method = 'left'/'right'/'mid'"""
    dx = (b - a) / n
    s = 0
    for i in range(n):
        if method == 'left':
            x = a + i * dx
        elif method == 'right':
            x = a + (i+1) * dx
        else:  # midpoint
            x = a + (i + 0.5) * dx
        s += f(x) * dx
    return s

f = lambda x: x**2
print("∫₀¹ x² dx 的黎曼和逼近（真值 1/3 ≈ 0.3333）：")
for n in [10, 100, 1000, 10000]:
    r = riemann_sum(f, 0, 1, n, 'right')
    m = riemann_sum(f, 0, 1, n, 'mid')
    print(f"  n={n:5d}: 右端点={r:.6f}, 中点={m:.8f}")
# 中点法则收敛更快！
```

---

## 22.2 牛顿-莱布尼茨公式 —— 微积分基本定理

### 定理

若 \\(F'(x) = f(x)\\)（\\(F\\) 是 \\(f\\) 的原函数），则：
\\[ \int_a^b f(x)\,dx = F(b) - F(a) \\]

**意义**：把"无穷求和"（积分）转化为"求反导数"——
这是整个微积分**最伟大的统一**。

### 证明思路

定义 \\(\Phi(x) = \int_a^x f(t)\,dt\\)，则 \\(\Phi'(x) = f(x)\\)。
于是 \\(\Phi(x) - F(x) = C\\)（导数相同的函数只差常数）。
由 \\(\Phi(a) = 0\\) 得 \\(C = -F(a)\\)，
故 \\(\int_a^b f = \Phi(b) = F(b) - F(a)\\)。∎

### 常用不定积分表

| \\(f(x)\\) | \\(\int f(x)dx\\) |
|---|---|
| \\(x^n\\)（\\(n \neq -1\\)） | \\(\frac{x^{n+1}}{n+1} + C\\) |
| \\(1/x\\) | \\(\ln\|x\| + C\\) |
| \\(e^x\\) | \\(e^x + C\\) |
| \\(\sin x\\) | \\(-\cos x + C\\) |
| \\(\cos x\\) | \\(\sin x + C\\) |
| \\(\frac{1}{1+x^2}\\) | \\(\arctan x + C\\) |
| \\(\frac{1}{\sqrt{1-x^2}}\\) | \\(\arcsin x + C\\) |

**例**：\\(\int_0^1 x^2 dx = \left[\frac{x^3}{3}\right]_0^1 = \frac{1}{3}\\)。
与 22.1 的黎曼和结果一致！✓

---

## 22.3 换元积分法

### 第一类换元（凑微分）

\\[ \int f(g(x)) \cdot g'(x)\,dx = \int f(u)\,du \quad (u = g(x)) \\]

**例**：\\(\int 2x \cdot e^{x^2} dx\\)。
令 \\(u = x^2\\)，则 \\(du = 2x\,dx\\)：
\\[ \int e^u\,du = e^u + C = e^{x^2} + C \\]

**例**：\\(\int_0^1 \frac{x}{\sqrt{1+x^2}}dx\\)。
令 \\(u = 1 + x^2\\)，\\(du = 2x\,dx\\)，\\(x:0\to1\\) 对应 \\(u:1\to2\\)：
\\[ \int_1^2 \frac{1}{2\sqrt{u}}\,du = \left[\sqrt{u}\right]_1^2 = \sqrt{2} - 1 \\]

### 第二类换元（三角代换）

| 被积函数含 | 令 |
|---|---|
| \\(\sqrt{a^2 - x^2}\\) | \\(x = a\sin t\\) |
| \\(\sqrt{a^2 + x^2}\\) | \\(x = a\tan t\\) |
| \\(\sqrt{x^2 - a^2}\\) | \\(x = a\sec t\\) |

**经典**：\\(\int \frac{dx}{\sqrt{1-x^2}} = \arcsin x + C\\)。
令 \\(x = \sin t\\)，\\(dx = \cos t\,dt\\)：
\\[ \int \frac{\cos t\,dt}{\cos t} = \int dt = t + C = \arcsin x + C \\]

---

## 22.4 分部积分法

\\[ \int u\,dv = uv - \int v\,du \\]

**来源**：乘积求导法则 \\((uv)' = u'v + uv'\\) 移项积分。

**口诀**："反对幂指三"（优先级：反三角 > 对数 > 幂 > 指数 > 三角，
排前面的设为 \\(u\\)）。

**例**：\\(\int x e^x dx\\)。
设 \\(u = x\\)，\\(dv = e^x dx\\)，则 \\(du = dx\\)，\\(v = e^x\\)：
\\[ \int x e^x dx = xe^x - \int e^x dx = xe^x - e^x + C = (x-1)e^x + C \\]

**例**：\\(\int_0^\pi x \sin x\,dx\\)。
\\(u = x\\)，\\(dv = \sin x\,dx\\)，\\(v = -\cos x\\)：
\\[ \left[-x\cos x\right]_0^\pi - \int_0^\pi (-\cos x)dx = \pi + \left[\sin x\right]_0^\pi = \pi \\]

### Python：数值积分验证（scipy）

```python
import math

def simpson(f, a, b, n=10000):
    """辛普森数值积分"""
    assert n % 2 == 0
    h = (b - a) / n
    s = f(a) + f(b)
    for i in range(1, n):
        x = a + i * h
        s += (4 if i % 2 == 1 else 2) * f(x)
    return s * h / 3

# 验证 ∫₀^π x·sin(x) dx = π
val = simpson(lambda x: x * math.sin(x), 0, math.pi)
print(f"数值积分 = {val:.10f}")
print(f"解析结果 = {math.pi:.10f}")
print(f"误差 = {abs(val - math.pi):.2e}")

# 验证 ∫₀¹ x² dx = 1/3
val2 = simpson(lambda x: x**2, 0, 1)
print(f"\n∫₀¹x²dx 数值 = {val2:.10f}, 解析 = {1/3:.10f}")
```

> 🌟 **拉马努金角**
> 拉马努金留下了许多"不可能"的积分公式。例如：
> \\[ \int_0^\infty \frac{\sin x}{x}\,dx = \frac{\pi}{2} \\]
> （狄利克雷积分，他用围道积分之外的初等方法"看"出了结果）
>
> 更神奇的是这个：
> \\[ \int_0^\infty \frac{dx}{1 + x^n} = \frac{\pi/n}{\sin(\pi/n)}, \quad n > 1 \\]
> 取 \\(n = 2\\)：\\(\int_0^\infty \frac{dx}{1+x^2} = \frac{\pi}{2}\\) ✓（\\(\arctan\\) 的结果）
> 取 \\(n = 4\\)：\\(\int_0^\infty \frac{dx}{1+x^4} = \frac{\pi}{2\sqrt{2}}\\)，
> 这个积分若用部分分式硬算，需要分解 \\(x^4+1 = (x^2+\sqrt{2}x+1)(x^2-\sqrt{2}x+1)\\)，
> 极其繁琐——但拉马努金一眼看穿 general formula！

---

## 📝 本章习题

**题 22.1** 用牛顿-莱布尼茨公式计算 \\(\int_1^2 \frac{1}{x} dx\\)。

**题 22.2** 求 \\(\int x^2 \ln x\,dx\\)（用分部积分）。

**题 22.3** 求 \\(\int_0^1 \frac{dx}{\sqrt{4-x^2}}\\)（提示：\\(x = 2\sin t\\)）。

**题 22.4** 证明：\\(\int_0^{\pi/2} \sin^n x\,dx = \int_0^{\pi/2} \cos^n x\,dx\\)。
提示：令 \\(u = \pi/2 - x\\)。

**题 22.5** 用 Python 的辛普森法验证 \\(\int_0^1 e^x dx = e - 1\\)。

**题 22.6**（思考）\\(\int \frac{dx}{x}\\) 和 \\(\int \frac{1}{x}dx\\) 有区别吗？
为什么答案是 \\(\ln|x| + C\\) 而不是 \\(\ln x + C\\)？

---

## ✅ 习题解答

### 题 22.1
\\[ \int_1^2 \frac{1}{x}dx = \left[\ln x\right]_1^2 = \ln 2 - \ln 1 = \ln 2 \\]

### 题 22.2
设 \\(u = \ln x\\)，\\(dv = x^2 dx\\)，则 \\(du = \frac{1}{x}dx\\)，\\(v = \frac{x^3}{3}\\)：
\\[ \int x^2\ln x\,dx = \frac{x^3}{3}\ln x - \int \frac{x^3}{3}\cdot\frac{1}{x}dx = \frac{x^3}{3}\ln x - \frac{1}{3}\int x^2 dx \\]
\\[ = \frac{x^3}{3}\ln x - \frac{x^3}{9} + C = \frac{x^3}{9}(3\ln x - 1) + C \\]

### 题 22.3
令 \\(x = 2\sin t\\)，\\(dx = 2\cos t\,dt\\)，\\(x:0\to1\\) 对应 \\(t:0\to\pi/6\\)：
\\[ \int_0^{\pi/6} \frac{2\cos t\,dt}{\sqrt{4-4\sin^2 t}} = \int_0^{\pi/6} \frac{2\cos t}{2\cos t}dt = \int_0^{\pi/6}dt = \frac{\pi}{6} \\]

### 题 22.4
令 \\(u = \frac{\pi}{2} - x\\)，则 \\(dx = -du\\)，
\\(x:0\to\pi/2\\) 对应 \\(u:\pi/2\to 0\\)：
\\[ \int_0^{\pi/2}\sin^n x\,dx = \int_{\pi/2}^{0}\sin^n\left(\frac{\pi}{2}-u\right)(-du) = \int_0^{\pi/2}\cos^n u\,du \\] ∎

### 题 22.5
```python
import math
def simpson(f, a, b, n=10000):
    h = (b - a) / n
    s = f(a) + f(b)
    for i in range(1, n):
        s += (4 if i % 2 else 2) * f(a + i*h)
    return s * h / 3
val = simpson(math.exp, 0, 1)
print(f"{val:.10f} vs {math.e - 1:.10f}")  # 1.7182818285 vs 1.7182818285 ✓
```

### 题 22.6
\\(\ln x\\) 只在 \\(x > 0\\) 有定义，但 \\(1/x\\) 在 \\(x < 0\\) 也有定义。
\\(x < 0\\) 时 \\(\int \frac{1}{x}dx = \ln(-x) + C\\)。
合起来就是 \\(\ln|x| + C\\)。这是初学者最常见的错误之一！

---

## 🎯 本章小结

| 概念 | 核心公式 | 意义 |
|---|---|---|
| 黎曼和 | \\(\lim\sum f(\xi_i)\Delta x_i\\) | 积分的原始定义 |
| 牛顿-莱布尼茨 | \\(\int_a^b f = F(b)-F(a)\\) | 微积分基本定理 |
| 换元法 | \\(\int f(g)g' = \int f(u)du\\) | 链式法则的逆 |
| 分部积分 | \\(\int u\,dv = uv - \int v\,du\\) | 乘积法则的逆 |

**下一章**：级数——拉马努金的游乐场。从收敛判别到 \\(1/\pi\\) 公式。
