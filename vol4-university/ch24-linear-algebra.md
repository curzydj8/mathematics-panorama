# 第二十四章：线性代数 —— 向量与矩阵的世界

> *"上帝创造了整数，魔鬼创造了矩阵——但天使在用线性代数。"*
> *—— 改编自数学系格言*

---

## 24.1 向量空间 —— 抽象的舞台

### 从箭头到抽象

中学学过向量是"有大小有方向的箭头"。线性代数把它**抽象化**：

**向量空间** \\(V\\) 是满足以下公理的集合：
1. 加法封闭：\\(\mathbf{u}, \mathbf{v} \in V \Rightarrow \mathbf{u} + \mathbf{v} \in V\\)
2. 数乘封闭：\\(c \in \mathbb{F}, \mathbf{v} \in V \Rightarrow c\mathbf{v} \in V\\)
3. 加法交换律、结合律，零向量、负向量存在
4. 数乘分配律等

**例子**：
- \\(\mathbb{R}^n\\)：\\(n\\) 维实向量
- \\(M_{m \times n}\\)：\\(m \times n\\) 矩阵
- \\(P_n\\)：次数 \\(\leq n\\) 的多项式
- \\(C[a,b]\\)：\\([a,b]\\) 上的连续函数（无穷维！）

### 线性无关与基

**线性无关**：\\(c_1\mathbf{v}_1 + \cdots + c_n\mathbf{v}_n = \mathbf{0} \Rightarrow c_1 = \cdots = c_n = 0\\)。

**基**：线性无关 + 张成全空间。\\(\mathbb{R}^3\\) 的标准基：
\\[ \mathbf{e}_1 = (1,0,0), \quad \mathbf{e}_2 = (0,1,0), \quad \mathbf{e}_3 = (0,0,1) \\]

**维数**：基中向量的个数。\\(\dim \mathbb{R}^n = n\\)。

### Python：向量运算

```python
import numpy as np

u = np.array([1, 2, 3])
v = np.array([4, 5, 6])

print(f"u + v = {u + v}")
print(f"3u = {3 * u}")
print(f"点积 u·v = {np.dot(u, v)}")  # 1*4+2*5+3*6 = 32
print(f"|u| = {np.linalg.norm(u):.4f}")  # √(1+4+9) = √14

# 施密特正交化演示
a = np.array([1., 1., 0.])
b = np.array([1., 0., 1.])
# b 正交化：b' = b - proj_a(b)
b_orth = b - np.dot(b, a)/np.dot(a, a) * a
print(f"\n正交化后: {b_orth}")
print(f"验证正交: a·b' = {np.dot(a, b_orth):.2e} ≈ 0")
```

---

## 24.2 矩阵 —— 线性变换的化身

### 矩阵乘法

\\[ (AB)_{ij} = \sum_{k} A_{ik} B_{kj} \\]

**关键**：矩阵乘法**不满足交换律**！\\(AB \neq BA\\) 一般成立。

**几何意义**：矩阵 = 线性变换。\\(A\mathbf{x}\\) 是把向量 \\(\mathbf{x}\\) 做变换。

| 矩阵 | 变换 |
|---|---|
| \\(\begin{pmatrix} \cos\theta & -\sin\theta \\\\ \sin\theta & \cos\theta \end{pmatrix}\\) | 旋转 \\(\theta\\) |
| \\(\begin{pmatrix} 2 & 0 \\\\ 0 & 2 \end{pmatrix}\\) | 放大 2 倍 |
| \\(\begin{pmatrix} 1 & 0 \\\\ 0 & -1 \end{pmatrix}\\) | 关于 x 轴翻转 |

### 逆矩阵

若 \\(AB = BA = I\\)，则 \\(B = A^{-1}\\)。

**2×2 求逆公式**：
\\[ \begin{pmatrix} a & b \\\\ c & d \end{pmatrix}^{-1} = \frac{1}{ad-bc}\begin{pmatrix} d & -b \\\\ -c & a \end{pmatrix} \\]

**应用**：解线性方程组 \\(A\mathbf{x} = \mathbf{b}\\) ⟹ \\(\mathbf{x} = A^{-1}\mathbf{b}\\)。

### Python：矩阵运算与解方程

```python
import numpy as np

A = np.array([[2, 1], [1, 3]])
b = np.array([5, 10])

# 解 Ax = b
x = np.linalg.solve(A, b)
print(f"解: x = {x}")
print(f"验证 Ax = {A @ x}")

# 逆矩阵
A_inv = np.linalg.inv(A)
print(f"\nA⁻¹ =\n{A_inv}")
print(f"A⁻¹A =\n{A_inv @ A}")  # 应该是单位矩阵

# 旋转矩阵
theta = np.pi / 4  # 45度
R = np.array([[np.cos(theta), -np.sin(theta)],
              [np.sin(theta),  np.cos(theta)]])
v = np.array([1, 0])
print(f"\n(1,0) 旋转45° = {R @ v}")  # 应为 (√2/2, √2/2)
```

---

## 24.3 行列式 —— 体积的缩放因子

### 定义（2×2）

\\[ \det\begin{pmatrix} a & b \\\\ c & d \end{pmatrix} = ad - bc \\]

**几何意义**：\\(|\det A|\\) = 线性变换 \\(A\\) 把单位正方形变成的平行四边形**面积**。
\\(\det A < 0\\) 表示**翻转了定向**。

### 性质

1. \\(\det(AB) = \det(A)\det(B)\\)
2. \\(\det(A^T) = \det(A)\\)
3. \\(\det(A^{-1}) = 1/\det(A)\\)
4. \\(\det A = 0 \iff A\\) 不可逆 \\(\iff\\) 列向量线性相关

### 克拉默法则

\\(A\mathbf{x} = \mathbf{b}\\) 的解：
\\[ x_i = \frac{\det(A_i)}{\det(A)} \\]
其中 \\(A_i\\) 是把 \\(A\\) 的第 \\(i\\) 列换成 \\(\mathbf{b}\\)。

> 🌟 **拉马努金角**
> 拉马努金对行列式有惊人的公式。Notebook 第一卷中有：
> \\[ \det\begin{pmatrix}
> 1 & 1 & 1 \\\\ x & y & z \\\\ x^2 & y^2 & z^2
> \end{pmatrix} = (y-x)(z-x)(z-y) \\]
> 这是**范德蒙行列式**（\\(n=3\\) 特例）。一般形式：
> \\[ \det(V) = \prod_{1 \leq i < j \leq n} (x_j - x_i), \quad V_{ij} = x_j^{i-1} \\]
> 拉马努金用它来研究分拆恒等式。范德蒙行列式在插值多项式、
> 随机矩阵理论中都是核心工具。

---

## 24.4 特征值与特征向量 —— 矩阵的"DNA"

### 定义

若 \\(A\mathbf{v} = \lambda\mathbf{v}\\)（\\(\mathbf{v} \neq \mathbf{0}\\)），
则 \\(\lambda\\) 是**特征值**，\\(\mathbf{v}\\) 是**特征向量**。

**求法**：解特征方程 \\(\det(A - \lambda I) = 0\\)。

### 例

\\[ A = \begin{pmatrix} 4 & 1 \\\\ 2 & 3 \end{pmatrix} \\]
\\[ \det\begin{pmatrix} 4-\lambda & 1 \\\\ 2 & 3-\lambda \end{pmatrix} = (4-\lambda)(3-\lambda) - 2 = \lambda^2 - 7\lambda + 10 = 0 \\]
得 \\(\lambda_1 = 5\\)，\\(\lambda_2 = 2\\)。

\\(\lambda_1 = 5\\) 对应的特征向量：解 \\((A - 5I)\mathbf{v} = 0\\)，
\\(\begin{pmatrix} -1 & 1 \\\\ 2 & -2 \end{pmatrix}\\)，得 \\(\mathbf{v}_1 = (1,1)^T\\)。

### 为什么重要？

1. **对角化**：若 \\(A\\) 有 \\(n\\) 个线性无关特征向量，则 \\(A = PDP^{-1}\\)，
   \\(D\\) 对角。于是 \\(A^k = PD^kP^{-1}\\)（算矩阵幂超容易！）
2. **稳定性**：微分方程 \\(\dot{\mathbf{x}} = A\mathbf{x}\\) 的稳定性由特征值实部决定
3. **主成分分析（PCA）**：数据科学的核心，找协方差矩阵的最大特征值方向
4. **量子力学**：可观测量 = 厄米矩阵，测量值 = 特征值！

### Python：特征值分解与幂运算

```python
import numpy as np

A = np.array([[4, 1], [2, 3]])
eigenvalues, eigenvectors = np.linalg.eig(A)
print(f"特征值: {eigenvalues}")  # [5, 2]
print(f"特征向量:\n{eigenvectors}")

# 验证 Av = λv
for i in range(2):
    lam = eigenvalues[i]
    v = eigenvectors[:, i]
    print(f"\nλ={lam:.1f}: Av={A@v}, λv={lam*v}, 相等={np.allclose(A@v, lam*v)}")

# 用对角化算 A^10
D = np.diag(eigenvalues)
P = eigenvectors
P_inv = np.linalg.inv(P)
A10_fast = P @ np.diag(eigenvalues**10) @ P_inv
A10_direct = np.linalg.matrix_power(A, 10)
print(f"\n对角化算 A^10 误差: {np.max(np.abs(A10_fast - A10_direct)):.2e}")

# 斐波那契数列的矩阵法！
F = np.array([[1, 1], [1, 0]])
# F^n 的 [0,1] 元素就是 F_{n+1}
Fn = np.linalg.matrix_power(F, 20)
print(f"\n斐波那契 F_21 = {Fn[0,1]} (矩阵快速幂 O(log n)!)")
```

> 💡 **彩蛋**：斐波那契数列可以用矩阵快速幂 \\(O(\log n)\\) 求出，
> 比递推的 \\(O(n)\\) 快得多。这是线性代数在算法中的威力。

---

## 📝 本章习题

**题 24.1** 判断向量 \\((1,2,3), (4,5,6), (7,8,9)\\) 是否线性无关。

**题 24.2** 求矩阵 \\(\begin{pmatrix} 1 & 2 \\\\ 3 & 4 \end{pmatrix}\\) 的逆矩阵。

**题 24.3** 计算 \\(\det\begin{pmatrix} 1 & 2 & 3 \\\\ 0 & 1 & 4 \\\\ 5 & 6 & 0 \end{pmatrix}\\)。

**题 24.4** 求 \\(A = \begin{pmatrix} 2 & 1 \\\\ 1 & 2 \end{pmatrix}\\) 的特征值和特征向量。

**题 24.5** 用 Python 验证：若 \\(A\\) 可对角化为 \\(PDP^{-1}\\)，则 \\(A^3 = PD^3P^{-1}\\)。

**题 24.6**（思考）为什么矩阵乘法不满足交换律？从"变换复合"的角度解释。

---

## ✅ 习题解答

### 题 24.1
**线性相关**。因为第三个向量 = 第一个 + 第二个：
\\((7,8,9) = (1,2,3) + 2\times(3,3,3)\\)……更直接地：
行列式 \\(\det = 1(45-48) - 2(36-42) + 3(32-35) = -3 + 12 - 9 = 0\\)，
故线性相关。

### 题 24.2
\\(\det = 1\times 4 - 2\times 3 = -2\\)。
\\[ A^{-1} = \frac{1}{-2}\begin{pmatrix} 4 & -2 \\\\ -3 & 1 \end{pmatrix} = \begin{pmatrix} -2 & 1 \\\\ 1.5 & -0.5 \end{pmatrix} \\]

### 题 24.3
按第一行展开：
\\[ \det = 1\cdot\det\begin{pmatrix}1&4\\\\6&0\end{pmatrix} - 2\cdot\det\begin{pmatrix}0&4\\\\5&0\end{pmatrix} + 3\cdot\det\begin{pmatrix}0&1\\\\5&6\end{pmatrix} \\]
\\[ = 1(0-24) - 2(0-20) + 3(0-5) = -24 + 40 - 15 = 1 \\]

### 题 24.4
特征方程：\\(\det\begin{pmatrix}2-\lambda&1\\\\1&2-\lambda\end{pmatrix} = (2-\lambda)^2-1 = 0\\)，
得 \\(\lambda_1 = 3\\)，\\(\lambda_2 = 1\\)。
- \\(\lambda=3\\)：\\(\begin{pmatrix}-1&1\\\\1&-1\end{pmatrix}\\)，特征向量 \\((1,1)^T\\)
- \\(\lambda=1\\)：\\(\begin{pmatrix}1&1\\\\1&1\end{pmatrix}\\)，特征向量 \\((1,-1)^T\\)

### 题 24.5
```python
import numpy as np
A = np.array([[4.,1.],[2.,3.]])
lams, P = np.linalg.eig(A)
D = np.diag(lams)
print("A^3 直接:", np.linalg.matrix_power(A,3)[0])
print("PD^3P⁻¹:", (P @ np.diag(lams**3) @ np.linalg.inv(P))[0])
# 两者一致 ✓
```

### 题 24.6
矩阵代表变换，\\(AB\\) 表示"先做 \\(B\\) 再做 \\(A\\)"。
"先旋转再平移" ≠ "先平移再旋转"——顺序不同结果不同。
这就是不交换的几何本质。

---

## 🎯 本章小结

| 概念 | 核心 | 应用 |
|---|---|---|
| 向量空间 | 8 条公理 | 统一向量/矩阵/函数 |
| 矩阵乘法 | \\((AB)_{ij} = \sum_k A_{ik}B_{kj}\\) | 线性变换的复合 |
| 行列式 | \\(\det(AB) = \det A \det B\\) | 体积缩放、可逆性 |
| 特征值 | \\(\det(A-\lambda I) = 0\\) | 对角化、稳定性、PCA |

**下一章**：概率论——从赌博到大数定律，随机性中的确定性。
