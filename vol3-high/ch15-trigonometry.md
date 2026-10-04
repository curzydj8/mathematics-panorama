# 第十五章：三角函数 —— 圆与波的语言

> *"哪里有振动，哪里就有三角函数。"*
> *—— 改编自傅里叶*

---

## 15.1 从角度到弧度：更自然的度量

### 角度制 vs 弧度制

- **角度制**：一周 = \\(360°\\)（巴比伦 60 进制的遗产）
- **弧度制**：弧长 = 半径时的圆心角 = **1 弧度**

\\[ 360° = 2\pi \text{ 弧度} \\]

换算公式：
\\[ \text{弧度} = \text{角度} \times \frac{\pi}{180}, \quad \text{角度} = \text{弧度} \times \frac{180}{\pi} \\]

| 角度 | 0° | 30° | 45° | 60° | 90° | 180° | 360° |
|---|---|---|---|---|---|---|---|
| 弧度 | 0 | \\(\frac{\pi}{6}\\) | \\(\frac{\pi}{4}\\) | \\(\frac{\pi}{3}\\) | \\(\frac{\pi}{2}\\) | \\(\pi\\) | \\(2\pi\\) |

> 💡 **为什么数学家偏爱弧度？**
> 因为 \\(\lim_{x \to 0} \frac{\sin x}{x} = 1\\) 只在弧度制下成立！
> 弧度让微积分公式变得简洁（第 17、20 章）。

### 扇形公式（弧度制的威力）

\\[ \text{弧长 } l = r\theta, \quad \text{面积 } S = \frac{1}{2}r^2\theta \\]

对比角度制：\\(l = \frac{\theta}{360} \cdot 2\pi r\\)——多麻烦！

```python
import math
def sector(r, theta_deg):
    theta = math.radians(theta_deg)
    return r * theta, 0.5 * r**2 * theta

l, s = sector(5, 60)
print(f"半径5，60°扇形：弧长={l:.3f}，面积={s:.3f}")
# 弧长=5.236，面积=13.090
```

---

## 15.2 单位圆：三角函数的故乡

### 定义

在单位圆（半径为 1）上，角 \\(\theta\\) 的终边与圆交于点 \\(P\\)：

\\[ \cos\theta = x_P, \quad \sin\theta = y_P, \quad \tan\theta = \frac{y_P}{x_P} = \frac{\sin\theta}{\cos\theta} \\]

### 同角基本关系

\\[ \sin^2\theta + \cos^2\theta = 1 \quad \text{（勾股定理！）} \\]
\\[ \tan\theta = \frac{\sin\theta}{\cos\theta}, \quad \cot\theta = \frac{\cos\theta}{\sin\theta} \\]
\\[ 1 + \tan^2\theta = \sec^2\theta, \quad 1 + \cot^2\theta = \csc^2\theta \\]

**证明** \\(\sin^2\theta + \cos^2\theta = 1\\)：
点 \\(P(\cos\theta, \sin\theta)\\) 在单位圆 \\(x^2 + y^2 = 1\\) 上，
代入即得。∎

### 诱导公式：口诀"奇变偶不变，符号看象限"

\\[ \sin\left(\frac{\pi}{2} - \theta\right) = \cos\theta, \quad \cos\left(\frac{\pi}{2} - \theta\right) = \sin\theta \\]
\\[ \sin(\pi - \theta) = \sin\theta, \quad \cos(\pi - \theta) = -\cos\theta \\]
\\[ \sin(\pi + \theta) = -\sin\theta, \quad \cos(\pi + \theta) = -\cos\theta \\]
\\[ \sin(-\theta) = -\sin\theta \text{（奇函数）}, \quad \cos(-\theta) = \cos\theta \text{（偶函数）} \\]

> 🌟 **拉马努金角**
> 拉马努金痴迷于三角函数的无穷乘积。他发现了：
> \\[ \frac{\sin x}{x} = \prod_{n=1}^{\infty} \left(1 - \frac{x^2}{n^2\pi^2}\right) \\]
> 这是欧拉的经典结果，但拉马努金用它推导出了 \\(\zeta(2) = \pi^2/6\\) 的新证明！
> 更神奇的是，他把 \\(\sin\\) 推广到" mock theta 函数"——
> 在他去世前一年写下的最后公式，80 年后才被数学家完全理解。

---

## 15.3 两角和差公式：三角恒等式的皇冠

### 核心公式

\\[ \sin(\alpha + \beta) = \sin\alpha\cos\beta + \cos\alpha\sin\beta \\]
\\[ \cos(\alpha + \beta) = \cos\alpha\cos\beta - \sin\alpha\sin\beta \\]
\\[ \tan(\alpha + \beta) = \frac{\tan\alpha + \tan\beta}{1 - \tan\alpha\tan\beta} \\]

**推导** \\(\cos(\alpha - \beta)\\)（向量法，最优雅）：
单位圆上两点 \\(A(\cos\alpha, \sin\alpha)\\)，\\(B(\cos\beta, \sin\beta)\\)，
夹角为 \\(\alpha - \beta\\)。由余弦定理和距离公式：
\\[ |AB|^2 = (\cos\alpha-\cos\beta)^2 + (\sin\alpha-\sin\beta)^2 = 2 - 2\cos(\alpha-\beta) \\]
展开左边：\\(2 - 2(\cos\alpha\cos\beta + \sin\alpha\sin\beta)\\)，
对比得：\\[ \cos(\alpha-\beta) = \cos\alpha\cos\beta + \sin\alpha\sin\beta \\] ∎

### 倍角公式

\\[ \sin 2\theta = 2\sin\theta\cos\theta \\]
\\[ \cos 2\theta = \cos^2\theta - \sin^2\theta = 2\cos^2\theta - 1 = 1 - 2\sin^2\theta \\]
\\[ \tan 2\theta = \frac{2\tan\theta}{1 - \tan^2\theta} \\]

### 和差化积（背诵版）

\\[ \sin\alpha + \sin\beta = 2\sin\frac{\alpha+\beta}{2}\cos\frac{\alpha-\beta}{2} \\]
\\[ \cos\alpha + \cos\beta = 2\cos\frac{\alpha+\beta}{2}\cos\frac{\alpha-\beta}{2} \\]

```python
import math
def verify_identities():
    """数值验证三角恒等式"""
    for a_deg, b_deg in [(30, 45), (60, 120), (15, 75)]:
        a, b = math.radians(a_deg), math.radians(b_deg)
        # sin(a+b)
        lhs = math.sin(a + b)
        rhs = math.sin(a)*math.cos(b) + math.cos(a)*math.sin(b)
        assert abs(lhs - rhs) < 1e-12, f"失败 @{a_deg},{b_deg}"
        # cos(2a)
        assert abs(math.cos(2*a) - (2*math.cos(a)**2-1)) < 1e-12
    print("✓ 两角和、倍角公式数值验证通过")

verify_identities()
```

---

## 15.4 正弦定理与余弦定理：解三角形的万能钥匙

### 正弦定理

\\[ \frac{a}{\sin A} = \frac{b}{\sin B} = \frac{c}{\sin C} = 2R \\]

其中 \\(R\\) 是三角形外接圆半径。

**证明**：作外接圆，直径 \\(2R\\) 所对圆周角为直角，
\\(a = 2R\sin A\\)。∎

### 余弦定理（勾股定理的推广！）

\\[ c^2 = a^2 + b^2 - 2ab\cos C \\]

当 \\(C = 90°\\) 时，\\(\cos C = 0\\)，退化为勾股定理。∎

### 三角形面积公式

\\[ S = \frac{1}{2}ab\sin C = \frac{abc}{4R} = 2R^2\sin A\sin B\sin C \\]

```python
import math
def solve_triangle(a, b, C_deg):
    """已知两边一夹角，求第三边和面积"""
    C = math.radians(C_deg)
    c = math.sqrt(a**2 + b**2 - 2*a*b*math.cos(C))
    S = 0.5 * a * b * math.sin(C)
    return c, S

c, S = solve_triangle(5, 7, 60)
print(f"c={c:.3f}, 面积={S:.3f}")
# c=6.083, 面积=15.155
```

---

## 📝 本章习题

**题 15.1** 把 \\(135°\\) 化为弧度，\\(\frac{5\pi}{6}\\) 化为角度。

**题 15.2** 已知 \\(\sin\theta = \frac{3}{5}\\)，\\(\theta\\) 在第二象限，求 \\(\cos\theta\\) 和 \\(\tan\theta\\)。

**题 15.3** 化简：\\(\sin 75°\\)（用 \\(75° = 45° + 30°\\)）。

**题 15.4** 在 \\(\triangle ABC\\) 中，\\(a=3\\)，\\(b=4\\)，\\(C=90°\\)，求 \\(c\\) 和外接圆半径 \\(R\\)。

**题 15.5** 证明：\\(\sin 3\theta = 3\sin\theta - 4\sin^3\theta\\)。
提示：\\(3\theta = 2\theta + \theta\\)。

**题 15.6**（思考）为什么 \\(\sin\\) 是奇函数而 \\(\cos\\) 是偶函数？从单位圆的几何对称性解释。

---

## ✅ 习题解答

### 题 15.1 解答
\\[ 135° = 135 \times \frac{\pi}{180} = \frac{3\pi}{4} \\]
\\[ \frac{5\pi}{6} = \frac{5\pi}{6} \times \frac{180}{\pi} = 150° \\]

### 题 15.2 解答
\\(\sin^2\theta + \cos^2\theta = 1 \Rightarrow \cos^2\theta = 1 - \frac{9}{25} = \frac{16}{25}\\)。
第二象限 \\(\cos\theta < 0\\)，所以 \\(\cos\theta = -\frac{4}{5}\\)。
\\[ \tan\theta = \frac{\sin\theta}{\cos\theta} = \frac{3/5}{-4/5} = -\frac{3}{4} \\] ∎

### 题 15.3 解答
\\[ \sin 75° = \sin(45°+30°) = \sin45°\cos30° + \cos45°\sin30° \\]
\\[ = \frac{\sqrt{2}}{2}\cdot\frac{\sqrt{3}}{2} + \frac{\sqrt{2}}{2}\cdot\frac{1}{2} = \frac{\sqrt{6}+\sqrt{2}}{4} \\] ∎

### 题 15.4 解答
\\(c = \sqrt{3^2+4^2} = 5\\)（勾股定理）。
由正弦定理 \\(\frac{c}{\sin C} = 2R\\)，\\(C=90°\\)，\\(\sin C = 1\\)，
\\[ 2R = 5 \Rightarrow R = 2.5 \\] ∎

### 题 15.5 解答
\\[ \sin 3\theta = \sin(2\theta+\theta) = \sin2\theta\cos\theta + \cos2\theta\sin\theta \\]
\\[ = (2\sin\theta\cos\theta)\cos\theta + (1-2\sin^2\theta)\sin\theta \\]
\\[ = 2\sin\theta\cos^2\theta + \sin\theta - 2\sin^3\theta \\]
\\[ = 2\sin\theta(1-\sin^2\theta) + \sin\theta - 2\sin^3\theta \\]
\\[ = 3\sin\theta - 4\sin^3\theta \\] ∎

> 💡 这是**三倍角公式**。拉马努金研究过一般的 \\(n\\) 倍角公式与切比雪夫多项式的关系。

### 题 15.6 思路
单位圆关于 y 轴对称：角 \\(\theta\\) 和 \\(-\theta\\) 的终边关于 x 轴对称。
所以 y 坐标变号（\\(\sin\\) 奇），x 坐标不变（\\(\cos\\) 偶）。
**代数性质源于几何对称性**——这是数学中反复出现的主题。

---

## 🎯 本章小结

| 概念 | 核心公式 | 通向 |
|---|---|---|
| 弧度制 | \\(l = r\theta\\) | 微积分（第20章）、傅里叶分析 |
| 同角关系 | \\(\sin^2+\cos^2=1\\) | 三角恒等式体系 |
| 两角和 | \\(\sin(\alpha+\beta)\\) 展开 | 复数欧拉公式（第29章） |
| 正弦定理 | \\(\frac{a}{\sin A} = 2R\\) | 解三角形、球面三角 |
| 余弦定理 | \\(c^2=a^2+b^2-2ab\cos C\\) | 向量内积（第24章） |

**下一章预告**：数列与数学归纳法——从高斯求和到严格的证明艺术，
我们将正式掌握"多米诺骨牌"式的推理，证明 \\(1^2+2^2+\cdots+n^2\\) 公式。
