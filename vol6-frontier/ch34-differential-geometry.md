# 第三十四章：微分几何 —— 弯曲的形状

> *"如果一只蚂蚁生活在球面上，它能发现自己不在平面上吗？"*
> *—— 语出本书作者，致敬高斯*

---

## 34.1 平面曲线的曲率：弯得多厉害？

### 弧长参数

曲线 \\(\\mathbf{r}(t)\\)，弧长 \\(s(t) = \\int_{t_0}^{t} |\\mathbf{r}'(u)|\\,du\\)。
用 \\(s\\) 作参数：\\(|\\mathbf{r}'(s)| = 1\\)（单位速度）。

### 曲率定义

\\[ \\kappa(s) = \\left|\\frac{d\\mathbf{T}}{ds}\\right|, \\quad \\mathbf{T} = \\mathbf{r}'(s) \\text{（单位切向量）} \\]

**直观**：切向量转得越快，曲率越大。直线 \\(\\kappa = 0\\)；
半径 \\(R\\) 的圆 \\(\\kappa = \\frac{1}{R}\\)（恒定）。

### 计算公式（任意参数）

\\[ \\kappa(t) = \\frac{|\\mathbf{r}'(t) \\times \\mathbf{r}''(t)|}{|\\mathbf{r}'(t)|^3} \\]
（平面情形：\\(\\kappa = \\frac{|x'y'' - y'x''|}{(x'^2 + y'^2)^{3/2}}\\)）

**例子**：抛物线 \\(y = x^2\\) 在原点处。
\\(\\mathbf{r}(t) = (t, t^2)\\)，\\(\\mathbf{r}' = (1, 2t)\\)，\\(\\mathbf{r}'' = (0, 2)\\)
\\[ \\kappa(0) = \\frac{|1 \\cdot 2 - 0|}{1^{3/2}} = 2 \\]
顶点处曲率 2（密切圆半径 \\(\\frac{1}{2}\\)）。

### Python：数值计算曲率

```python
import numpy as np

def curvature(x, y, t):
    """数值计算平面曲线曲率"""
    dx = np.gradient(x, t)
    dy = np.gradient(y, t)
    ddx = np.gradient(dx, t)
    ddy = np.gradient(dy, t)
    return np.abs(dx*ddy - dy*ddx) / (dx**2 + dy**2)**1.5

# 抛物线 y = x^2
t = np.linspace(-2, 2, 1000)
x, y = t, t**2
k = curvature(x, y, t)
idx = np.argmin(np.abs(t))
print(f"原点处曲率 ≈ {k[idx]:.4f} (理论值 2)")
# 原点处曲率 ≈ 2.0000

# 单位圆: 曲率恒为 1
t2 = np.linspace(0, 2*np.pi, 1000)
k2 = curvature(np.cos(t2), np.sin(t2), t2)
print(f"圆的曲率: 均值={k2[100:-100].mean():.4f} (理论值 1)")
```

> 🌟 **拉马努金角**
> 拉马努金研究过**摆线**（cycloid）——最速降线：
> \\[ x = a(\\theta - \\sin\\theta), \\quad y = a(1 - \\cos\\theta) \\]
> 摆线的曲率：\\(\\kappa = \\frac{1}{4a\\sin(\\theta/2)}\\)。
>
> 更深刻的是，他在研究**椭圆积分**时，
> 本质上在处理椭圆曲线的"弧长"——
> 第一类椭圆积分 \\(\\int \\frac{dx}{\\sqrt{(1-x^2)(1-k^2x^2)}}\\)
> 正是某种曲线的弧长函数。微分几何的"长度"概念，
> 在拉马努金手中变成了模形式的源泉。

---

## 34.2 曲面的第一基本形式：内蕴几何

### 参数曲面

\\(\\mathbf{r}(u, v)\\)，切向量 \\(\\mathbf{r}_u, \\mathbf{r}_v\\)。

### 第一基本形式

\\[ I = E\\,du^2 + 2F\\,du\\,dv + G\\,dv^2 \\]
其中
\\[ E = \\mathbf{r}_u \\cdot \\mathbf{r}_u, \\quad F = \\mathbf{r}_u \\cdot \\mathbf{r}_v, \\quad G = \\mathbf{r}_v \\cdot \\mathbf{r}_v \\]

**意义**：\\(I\\) 告诉你曲面上**如何量长度、角度、面积**——
这是曲面的"内蕴"几何（住在曲面上的人能测量的全部）。

曲线的长度：\\(L = \\int \\sqrt{E(u')^2 + 2Fu'v' + G(v')^2}\\,dt\\)
面积元：\\(dA = \\sqrt{EG - F^2}\\,du\\,dv\\)

### 例子：球面

\\(\\mathbf{r}(\\theta, \\phi) = (\\sin\\theta\\cos\\phi, \\sin\\theta\\sin\\phi, \\cos\\theta)\\)
\\[ E = 1, \\quad F = 0, \\quad G = \\sin^2\\theta \\]
\\[ I = d\\theta^2 + \\sin^2\\theta\\,d\\phi^2 \\]

大圆弧长、球面面积 \\(4\\pi R^2\\) 都从此算出。

### Python：球面测地线（大圆）

```python
import numpy as np

def sphere_geodesic_length(theta1, phi1, theta2, phi2, R=1):
    """球面上两点的大圆距离"""
    # 球面余弦定理
    cos_c = (np.sin(theta1)*np.sin(theta2)*np.cos(phi1-phi2)
             + np.cos(theta1)*np.cos(theta2))
    cos_c = np.clip(cos_c, -1, 1)
    return R * np.arccos(cos_c)

# 北京 (40°N, 116°E) 到 纽约 (41°N, 74°W) 的大圆距离
#  colatitude = 90 - latitude
import math
R_earth = 6371  # km
d = sphere_geodesic_length(
    math.radians(50), math.radians(116),
    math.radians(49), math.radians(-74),
    R_earth
)
print(f"北京-纽约大圆距离 ≈ {d:.0f} km")
# 约 11000 km (实际航线约 11000km ✓)
```

---

## 34.3 高斯曲率与绝妙定理

### 第二基本形式（简述）

\\(II = L\\,du^2 + 2M\\,du\\,dv + N\\,dv^2\\)，度量曲面"如何弯曲进三维空间"。

### 高斯曲率

\\[ K = \\frac{LN - M^2}{EG - F^2} \\]

- 球面（半径 \\(R\\)）：\\(K = \\frac{1}{R^2} > 0\\)（正曲率）
- 平面：\\(K = 0\\)
- 马鞍面 \\(z = x^2 - y^2\\)：\\(K < 0\\)（负曲率）

### Theorema Egregium（绝妙定理，高斯 1827）

> **高斯曲率只依赖第一基本形式**——它是**内蕴**的！

**震撼含义**：住在曲面上的人（只能测长度角度），
**能**发现自己所在的曲面是弯曲的！
把球面展平成平面而不拉伸——**不可能**（故地图必有变形）。

**例子**：圆柱面 \\(K = 0\\)（可展平成平面），
球面 \\(K > 0\\)（不可展平）。这就是为什么世界地图总有扭曲。

### Python：可视化高斯曲率符号

```python
import numpy as np

def gauss_curvature_z_fxy(f, x, y, h=1e-5):
    """z = f(x,y) 的图在 (x,y) 处的高斯曲率"""
    # 数值二阶导
    fxx = (f(x+h,y) - 2*f(x,y) + f(x-h,y)) / h**2
    fyy = (f(x,y+h) - 2*f(x,y) + f(x,y-h)) / h**2
    fxy = (f(x+h,y+h) - f(x+h,y-h) - f(x-h,y+h) + f(x-h,y-h)) / (4*h**2)
    fx = (f(x+h,y) - f(x-h,y)) / (2*h)
    fy = (f(x,y+h) - f(x,y-h)) / (2*h)
    K = (fxx*fyy - fxy**2) / (1 + fx**2 + fy**2)**2
    return K

# 球冠 z = sqrt(1-x^2-y^2) 在原点: K = 1 (正)
f_sphere = lambda x, y: (1 - x**2 - y**2)**0.5 if x**2+y**2 < 1 else 0
print(f"球面 K ≈ {gauss_curvature_z_fxy(f_sphere, 0, 0):.3f} (理论 1)")

# 马鞍 z = x^2 - y^2 在原点: K < 0
f_saddle = lambda x, y: x**2 - y**2
print(f"马鞍 K ≈ {gauss_curvature_z_fxy(f_saddle, 0, 0):.3f} (理论 -4)")
```

---

## 📝 本章习题

**题 34.1** 求星形线 \\(x = \\cos^3 t, y = \\sin^3 t\\) 在 \\(t = \\pi/4\\) 处的曲率。

**题 34.2** 证明：半径 \\(R\\) 的圆，曲率恒为 \\(1/R\\)。（用定义直接算）

**题 34.3** 求圆柱面 \\(\\mathbf{r}(\\theta, z) = (\\cos\\theta, \\sin\\theta, z)\\) 的第一基本形式，
并说明为什么它与平面"内蕴相同"（\\(K = 0\\)）。

**题 34.4** 用 Python 计算悬链线 \\(y = \\cosh x\\) 在 \\(x = 0\\) 处的曲率，
并解释为什么悬链线是"最稳"的拱形。

**题 34.5**（思考）蚂蚁在球面上画一个"三角形"（三段大圆弧），
内角和是多少？这与高斯-博内定理 \\(\\int K\\,dA = 2\\pi - \\sum\\)外角
有何关系？

---

## ✅ 习题解答

### 题 34.1 解答
\\(x' = -3\\cos^2 t\\sin t\\)，\\(y' = 3\\sin^2 t\\cos t\\)
\\(x'' = 6\\cos t\\sin^2 t - 3\\cos^3 t\\)，\\(y'' = 6\\sin t\\cos^2 t - 3\\sin^3 t\\)

在 \\(t = \\pi/4\\)：\\(\\cos = \\sin = \\frac{\\sqrt{2}}{2}\\)
\\[ x' = -3\\cdot\\frac{1}{2}\\cdot\\frac{\\sqrt{2}}{2} = -\\frac{3\\sqrt{2}}{4}, \\quad y' = \\frac{3\\sqrt{2}}{4} \\]
\\[ x'^2 + y'^2 = \\frac{9}{8} + \\frac{9}{8} = \\frac{9}{4} \\]
经计算（略去繁琐代数）：
\\[ \\kappa = \\frac{|x'y'' - y'x''|}{(x'^2+y'^2)^{3/2}} = \\frac{2}{3} \\]
（星形线在 \\(\\pi/4\\) 处曲率为 \\(2/3\\)）

```python
import numpy as np
t = np.pi/4
h = 1e-7
def xp(t): return -3*np.cos(t)**2*np.sin(t)
def yp(t): return 3*np.sin(t)**2*np.cos(t)
# 数值验证
x1, y1 = xp(t), yp(t)
x2 = (xp(t+h)-xp(t-h))/(2*h); y2 = (yp(t+h)-yp(t-h))/(2*h)
k = abs(x1*y2 - y1*x2)/(x1**2+y1**2)**1.5
print(f"κ ≈ {k:.4f}")  # 0.6667
```

### 题 34.2 解答
\\(\\mathbf{r}(t) = (R\\cos t, R\\sin t)\\)（\\(t\\) 为圆心角，非弧长参数）。
\\(\\mathbf{r}' = (-R\\sin t, R\\cos t)\\)，\\(|\\mathbf{r}'| = R\\)，
弧长 \\(s = Rt\\)，\\(\\frac{dt}{ds} = \\frac{1}{R}\\)。
单位切向量 \\(\\mathbf{T} = (-\\sin t, \\cos t)\\)
\\[ \\frac{d\\mathbf{T}}{ds} = \\frac{d\\mathbf{T}}{dt}\\frac{dt}{ds} = (-\\cos t, -\\sin t) \\cdot \\frac{1}{R} \\]
\\[ \\kappa = \\left|\\frac{d\\mathbf{T}}{ds}\\right| = \\frac{1}{R} \\] ∎
**曲率 = 半径的倒数**——圆越小越弯，完美符合直觉。

### 题 34.3 解答
\\(\\mathbf{r}_\\theta = (-\\sin\\theta, \\cos\\theta, 0)\\)，\\(\\mathbf{r}_z = (0, 0, 1)\\)
\\[ E = 1, \\quad F = 0, \\quad G = 1 \\]
\\[ I = d\\theta^2 + dz^2 \\]
这与平面极坐标（\\(d\\theta\\) 换成 \\(dx\\)）形式相同！
故圆柱面与平面**局部等距**（把纸卷成筒，长度角度不变），
\\(K = 0\\)。这就是"可展曲面"。∎

### 题 34.4 解答
```python
import numpy as np
def curvature_y(x, y):
    h = 1e-7
    yp = (y(x+h)-y(x-h))/(2*h)
    ypp = (y(x+h)-2*y(x)+y(x-h))/h**2
    return abs(ypp)/(1+yp**2)**1.5

k = curvature_y(0, np.cosh)
print(f"κ(0) = {k:.4f}")  # 1.0000
```
\\(y = \\cosh x\\)，\\(y' = \\sinh x\\)，\\(y'' = \\cosh x\\)，
\\(\\kappa(0) = \\frac{\\cosh 0}{(1+0)^{3/2}} = 1\\)。

**为什么最稳**：悬链线是"悬挂链条"的形状，处处只有拉力无弯矩。
倒过来就是拱形——压力沿切线传递，无剪切破坏。
圣路易斯大拱门就是悬链线（倒置）！

### 题 34.5 思路提示
球面三角形（如八分之一球面：三个直角）内角和 \\(= \\frac{3\\pi}{2} > \\pi\\)！

**高斯-博内**：\\(\\int_T K\\,dA + \\sum\\)外角 \\(= 2\\pi\\)
球面 \\(K = 1/R^2\\)，八分之一球面面积 \\(= \\frac{4\\pi R^2}{8} = \\frac{\\pi R^2}{2}\\)
\\(\\int K\\,dA = \\frac{\\pi}{2}\\)，三个外角各 \\(\\frac{\\pi}{2}\\)，
\\(\\frac{\\pi}{2} + \\frac{3\\pi}{2} = 2\\pi\\) ✓

**深意**：三角形内角和 \\(> \\pi\\) 的"超出的部分" = 曲率积分。
**曲率是"内角和偏离 \\(\\pi\\)"的密度**。这是整体微分几何的起点，
通向陈省身的高斯-博内-陈定理。

---

## 🎯 本章小结

| 概念 | 公式 | 意义 |
|---|---|---|
| 曲率 | \\(\\kappa = \\|d\\mathbf{T}/ds\\|\\) | 弯曲程度，圆为 \\(1/R\\) |
| 第一基本形式 | \\(I = E\\,du^2 + 2F\\,du\\,dv + G\\,dv^2\\) | 内蕴几何：长度角度面积 |
| 高斯曲率 | \\(K = \\frac{LN-M^2}{EG-F^2}\\) | 正/零/负：球/平/鞍 |
| 绝妙定理 | \\(K\\) 内蕴 | 蚂蚁能发现球面弯曲 |

**下一章预告**：组合数学——生成函数、鸽巢原理、容斥原理。
拉马努金的分拆函数 \\(p(n)\\) 在此登场！
