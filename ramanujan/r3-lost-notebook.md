# R3 Lost Notebook：最后的宝藏

> *"这是拉马努金留给世界的最后一封信，写了 80 年才被读懂。"*
> *—— 本书作者*

---

## R3.1 发现史：1976 年的意外

### 被遗忘的盒子

拉马努金 1920 年去世后，他的手稿散落各处：
- 大部分 Notebook 到了马德拉斯大学
- 一些论文手稿到了剑桥

1976 年，宾州州立大学的 **George Andrews**（分拆理论专家）去剑桥三一学院图书馆查资料。

在 Ren 图书馆的一个盒子里，他发现了一叠 **100 多页**的手稿——字迹是拉马努金的，内容是从未发表过的 q 级数公式。

### 为什么叫 "Lost"？

- 不是"丢失后找回"，而是"**从未被整理发表**"
- 哈代去世（1947）时，这些手稿在他遗物中
- Watson（哈代的学生）研究过一部分，但二战打断了工作
- 在图书馆沉睡了 **50 多年**

1987 年，Narosa 出版社影印出版；1988 年，Springer 出版整理版。

```python
# Lost Notebook 时间线
events = [
    (1920, "拉马努金去世，手稿散落"),
    (1947, "哈代去世，手稿在其遗物中"),
    (1976, "Andrews 发现手稿"),
    (1987, "Narosa 影印版"),
    (1988, "Springer 整理版（Berndt 主编）"),
    (2002, "Bringmann-Ono 证明 mock theta 与调和 Maass 形式的关系"),
    (2012, "Folsom-Ono-Rhoades 系统理论建立"),
]
for year, event in events:
    print(f"{year}: {event}")
```

---

## R3.2 Mock Theta 函数：定义与例子

### 背景：什么是 Theta 函数？

**Jacobi theta 函数**：
\\[ \\vartheta(z; \\tau) = \\sum_{n=-\\infty}^{\\infty} e^{\\pi i n^2 \\tau + 2\\pi i n z} \\]

特点：
- 满足**模变换**（modular transformation）：\\(\\tau \\to -1/\\tau\\) 时有漂亮的函数方程
- 是**模形式**（modular form）的例子

### Mock Theta：拉马努金的发明

1920 年 1 月，拉马努金在给哈代的最后一封信中写道：

> "我发现了一些非常有趣的函数，我称之为 'mock theta 函数'。
> 它们在 \\(q\\) 的单位根处的行为像 theta 函数，
> 但整体上**不是**模形式……"

**现代定义**（Zwegers, 2002）：mock theta 函数是**调和 Maass 形式**的全纯部分。

### 例子 R3.2.1：第三阶 mock theta 函数 \\(f(q)\\)

\\[ f(q) = \\sum_{n=0}^{\\infty} \\frac{q^{n^2}}{(-q; q)_n^2} = 1 + q - 2q^2 + 3q^3 - 3q^4 + \\cdots \\]

- **来源**：1920 年 1 月致哈代信；Lost Notebook 第 1 页
- **性质**：
  - 在 \\(|q| < 1\\) 内全纯
  - 当 \\(q\\) 趋于单位根时，渐近行为类似模形式
  - 但**没有**整体的模变换公式（这就是 "mock" 的含义）

### 例子 R3.2.2：第五阶 mock theta 函数

拉马努金给了 10 个第五阶 mock theta 函数，分成两组：

\\[ f_0(q) = \\sum_{n=0}^{\\infty} \\frac{q^{n^2}}{(-q; q)_n}, \\quad f_1(q) = \\sum_{n=0}^{\\infty} \\frac{q^{n^2+n}}{(-q; q)_n} \\]

\\[ \\phi_0(q) = \\sum_{n=0}^{\\infty} q^{n^2}(-q; q^2)_n, \\quad \\phi_1(q) = \\sum_{n=0}^{\\infty} q^{(n+1)^2}(-q; q^2)_n \\]

还有 \\(\\psi_0, \\psi_1, \\chi_0, \\chi_1\\)。

- **来源**：Lost Notebook
- **mock theta 猜想**（拉马努金临终猜想，1987 年由 Hickerson 证明）：
  这 10 个函数之间有 6 组**线性关系**，系数是 theta 函数

```python
def mock_theta_f(q, terms=30):
    """第三阶 mock theta f(q) = sum q^{n^2} / (-q;q)_n^2"""
    def q_poch(a, q, n):
        p = 1.0
        for k in range(n):
            p *= (1 - a * q**k)
        return p
    s = 0.0
    for n in range(terms):
        s += (q ** (n*n)) / (q_poch(-q, q, n) ** 2)
    return s

# 数值实验：q = 0.5
q = 0.5
print(f"f({q}) ≈ {mock_theta_f(q):.10f}")
# 观察：级数收敛很快（q^{n^2} 衰减极快）
```

---

## R3.3 为什么等了 80 年？

### 障碍 1：没有定义

拉马努金**没有给出 mock theta 的严格定义**，只给了例子和一些性质描述。

数学家们花了 80 年才搞清楚他在说什么：
- 1930s–1970s：Watson、Selberg 等人研究例子，但无统一理论
- 2002：**Zwegers**（荷兰，Zagier 的学生）在博士论文中证明：
  mock theta 函数 = 调和 Maass 形式的"全纯部分"

### 障碍 2：需要新工具

理解 mock theta 需要：
- **Maass 形式**（1949 年才发明，比拉马努金晚 30 年！）
- **调和 Maass 形式**（Bruinier-Funke, 2004）
- 拉马努金在 1920 年**不可能**知道这些概念——他凭直觉走在了时代前面

> 💡 **这说明了什么？**
> 拉马努金的直觉超越了他的时代。他"看到"的函数，
> 需要 80 年后的数学工具才能严格描述。
> 这就像一个人描述了"智能手机"，但当时还没有"芯片"的概念。

### 突破：2002 年 Zwegers 的工作

**定理**（Zwegers, 2002）：
每个 mock theta 函数 \\(M(q)\\) 都可以补全为调和 Maass 形式 \\(\\widehat{M}\\)：

\\[ \\widehat{M}(\\tau) = M(q) + M^-(\\tau), \\quad q = e^{2\\pi i \\tau} \\]

其中 \\(M^-(\\tau)\\) 是**非全纯**的修正项（period integral）。

加上修正项后，\\(\\widehat{M}\\) 就有了漂亮的模变换！

---

## R3.4 现代意义：为什么 mock theta 重要？

### 应用 1：黑洞熵（弦理论）

2007 年，Dabholkar-Murthy-Zagier 发现：

**量子黑洞的熵**（Bekenstein-Hawking 熵的量子修正）由 mock theta 函数的傅里叶系数给出！

\\[ S_{BH} = \\log d(Q), \\quad d(Q) \\text{ 是 mock modular form 的系数} \\]

拉马努金临终前把玩的公式，90 年后用在了**量子引力**。

### 应用 2：月光猜想（Monstrous Moonshine）

Mock theta 函数与**魔群月光**（Monster Moonshine）有联系：
- 魔群（最大的散在单群）的表示维数
- 与 \\(j\\) 函数的傅里叶系数相关
- Mock 版本对应"umbral moonshine"（2012 年发现）

### 应用 3：拓扑学

Lawrence-Zagier（1999）发现 mock theta 函数与**3-流形的 Witten-Reshetikhin-Turaev 不变量**相关。

---

## R3.5 Lost Notebook 的其他宝藏

除了 mock theta，Lost Notebook 还有：

| 主题 | 内容 | 状态 |
|---|---|---|
| q 级数恒等式 | 100+ 个新的 q 级数公式 | 大部分已证明（Andrews-Berndt） |
| 椭圆积分 | 奇异模的高精度值 | 已整理 |
| 分拆同余式 | 新的 \\(p(n)\\) 同余式 | 部分仍开放 |
| 第六、七阶 mock theta | 更多例子 | 已分类（Gordon-McIntosh） |

---

## 📝 本章习题

**题 R3.1** 用 Python 计算 \\(f(0.5)\\)（第三阶 mock theta），取 10 项和 30 项，观察收敛速度。

**题 R3.2** 解释为什么 \\(q^{n^2}\\) 让级数收敛得比 \\(q^n\\) 快得多（提示：比较 \\(n=10\\) 时两者的值）。

**题 R3.3**（开放）拉马努金没有严格定义就"发现"了 mock theta 函数。这说明数学发现中"直觉"和"严格证明"各起什么作用？

**题 R3.4**（研究级）查阅 Zwegers 2002 年博士论文的引言，了解"调和 Maass 形式"的直观含义。用你自己的话解释为什么 mock theta 需要"非全纯修正项"。

---

## ✅ 习题解答

### 题 R3.1 解答
```python
# 见 R3.2 节代码
# 10 项：已收敛到约 1e-10 精度（因为 q^{100} 极小）
# 30 项：机器精度
# 结论：q^{n^2} 衰减是超指数的，收敛极快
```

### 题 R3.2 解答
\\(q = 0.5, n = 10\\) 时：
- \\(q^n = 0.5^{10} \\approx 0.001\\)
- \\(q^{n^2} = 0.5^{100} \\approx 8 \\times 10^{-31}\\)
\\(n^2\\) 增长比 \\(n\\) 快得多，所以 \\(q^{n^2}\\) 是**超指数衰减**。∎

### 题 R3.3 思路提示
- **直觉**：发现模式、猜测公式、指引方向（拉马努金的强项）
- **证明**：确认正确、建立联系、推广应用（哈代的强项）
- 两者缺一不可。拉马努金-哈代的合作是数学史上"直觉+严格"的典范。
- 现代数学：计算机可以辅助发现模式（实验数学），但证明仍需人类。

### 题 R3.4 思路提示
- Mock theta 在单位根处"几乎"是模形式，但差一点
- 非全纯修正项 \\(M^-(\\tau)\\) 补偿了这个"差一点"
- 直观：就像 \\(f(x) = |x|\\) 在 0 处不可导，加上修正项后变光滑
- 参考：Zagier《Ramanujan's mock theta functions and their applications》(2007, Séminaire Bourbaki)

---

## 🎯 本章小结

| 主题 | 核心 | 意义 |
|---|---|---|
| 发现 | 1976, Andrews, 三一学院 | 沉睡 50 年 |
| Mock theta | \\(f(q) = \\sum q^{n^2}/(-q;q)_n^2\\) | 无严格定义，凭直觉发现 |
| Zwegers 突破 | 调和 Maass 形式的全纯部分 | 2002，80 年后 |
| 黑洞熵 | Mock 系数 = 量子黑洞熵 | 弦理论应用 |
| 启示 | 直觉可以超越时代 | 实验数学的价值 |

**下一章预告**：R4 —— 1/π 公式与模方程，
拉马努金最快的 π 算法，Python 实测每项 8 位精度。
