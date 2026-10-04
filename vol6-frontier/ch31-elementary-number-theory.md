# 第三十一章：初等数论 —— 整数的秘密花园

> *"数学是科学的皇后，数论是数学的皇后。"*
> *—— 高斯（Carl Friedrich Gauss）*

---

## 31.1 整除与素数：数的"原子"

### 整除

\\(d \\mid n\\)（读作"d 整除 n"）\\(\\iff \\exists k \\in \\mathbb{Z}, n = dk\\)。

**带余除法**：\\(\\forall a \\in \\mathbb{Z}, d > 0, \\exists! q, r: a = qd + r, 0 \\le r < d\\)。

### 素数：乘法的原子

**素数** \\(p\\)：大于 1，且只有 1 和 \\(p\\) 两个因数。

**算术基本定理**（唯一因子分解）：
> 每个大于 1 的整数可唯一表示为素数之积（不计顺序）：
> \\[ n = p_1^{e_1} p_2^{e_2} \\cdots p_k^{e_k} \\]

**欧几里得证明素数无穷**（公元前 300 年，至今最美证明之一）：
假设素数只有 \\(p_1, \\ldots, p_n\\)。令 \\(N = p_1 p_2 \\cdots p_n + 1\\)。
\\(N\\) 的任一素因子 \\(q\\) 不能是 \\(p_i\\)（否则 \\(q \\mid 1\\)，矛盾）。
故有新素数 \\(q\\)，矛盾。∎

### Python：埃氏筛法

```python
def sieve(n):
    """埃拉托斯特尼筛法找素数 O(n log log n)"""
    is_prime = [True] * (n + 1)
    is_prime[0] = is_prime[1] = False
    for i in range(2, int(n**0.5) + 1):
        if is_prime[i]:
            for j in range(i*i, n+1, i):
                is_prime[j] = False
    return [i for i, p in enumerate(is_prime) if p]

primes = sieve(100)
print(f"100以内素数: {primes}")
print(f"共 {len(primes)} 个")
# 共 25 个
```

> 🌟 **拉马努金角**
> 拉马努金对素数分布有惊人直觉。他在 1919 年的论文中证明了：
> **对充分大的 \\(n\\)，\\(p_{n+1} - p_n < p_n^{5/8}\\)**
> （素数间隙的上界）。这比当时已知结果强得多！
>
> 他还研究了**高合成数**（highly composite numbers）——
> 因数个数创纪录的数：1, 2, 4, 6, 12, 24, 36, 48, 60, 120, …
> 他在 1915 年的论文《Highly composite numbers》长达 62 页，
> 给出了这类数的完整刻画。哈代说这篇论文"不同寻常"，
> 因为它展现了拉马努金**定义新概念**的能力，而不只是证明公式。

---

## 31.2 同余：时钟上的算术

### 定义

\\[ a \\equiv b \\pmod{m} \\iff m \\mid (a - b) \\]

**直观**："模 \\(m\\) 看，\\(a\\) 和 \\(b\\) 一样"。
时钟：\\(15 \\equiv 3 \\pmod{12}\\)（15 点就是下午 3 点）。

### 运算性质

若 \\(a \\equiv b\\)，\\(c \\equiv d \\pmod{m}\\)，则：
- \\(a + c \\equiv b + d\\)
- \\(ac \\equiv bd\\)

**威力**：求 \\(7^{100}\\) 的个位数？
\\[ 7^1 \\equiv 7, \\ 7^2 \\equiv 49 \\equiv 9, \\ 7^3 \\equiv 63 \\equiv 3, \\ 7^4 \\equiv 21 \\equiv 1 \\pmod{10} \\]
周期 4：\\(7^{100} = (7^4)^{25} \\equiv 1^{25} = 1 \\pmod{10}\\)。
**个位数是 1**！（不用算 \\(7^{100}\\) 这个 85 位数）

### Python：快速幂取模

```python
def powmod(a, e, m):
    """快速幂: a^e mod m, O(log e)"""
    r = 1
    a %= m
    while e:
        if e & 1:
            r = r * a % m
        a = a * a % m
        e >>= 1
    return r

print(f"7^100 mod 10 = {powmod(7, 100, 10)}")  # 1
# 算 2^1000000 mod 1000000007 (RSA 常用)
print(f"2^1000000 mod 1e9+7 = {powmod(2, 10**6, 10**9+7)}")
```

---

## 31.3 费马小定理与欧拉定理

### 费马小定理

> 若 \\(p\\) 素数，\\(p \\nmid a\\)，则
> \\[ a^{p-1} \\equiv 1 \\pmod{p} \\]

**群论证明**（第 27 章的应用）：
\\(\\mathbb{Z}_p^\\times\\) 是 \\(p-1\\) 阶群，\\(a^{|G|} = e\\) 即得。∎

**推论**：\\(a^p \\equiv a \\pmod{p}\\)（对一切 \\(a\\)）。

### 欧拉定理（推广）

\\(\\varphi(m)\\) = 小于 \\(m\\) 且与 \\(m\\) 互素的个数（欧拉函数）。
\\[ \\gcd(a, m) = 1 \\Rightarrow a^{\\varphi(m)} \\equiv 1 \\pmod{m} \\]

**RSA 密码**就建立在这上面！（\\(m = pq\\)，\\(\\varphi(m) = (p-1)(q-1)\\)）

### Python：验证费马小定理

```python
def is_prime_trial(n):
    if n < 2: return False
    for i in range(2, int(n**0.5)+1):
        if n % i == 0: return False
    return True

# 验证: 对素数 p, a^(p-1) ≡ 1 (mod p)
for p in [5, 7, 11, 13, 101]:
    assert is_prime_trial(p)
    for a in [2, 3, 5]:
        if a % p != 0:
            assert pow(a, p-1, p) == 1, f"失败: {a}^{p-1} mod {p}"
print("✓ 费马小定理验证通过")

# 欧拉函数
def euler_phi(n):
    r = n
    p = 2
    temp = n
    while p * p <= temp:
        if temp % p == 0:
            while temp % p == 0: temp //= p
            r -= r // p
        p += 1
    if temp > 1: r -= r // temp
    return r

print(f"φ(10) = {euler_phi(10)}")  # 4 (1,3,7,9)
print(f"φ(100) = {euler_phi(100)}")  # 40
```

> 🌟 **拉马努金角**
> 拉马努金发现了 \\(\\tau\\) 函数的同余性质：
> \\[ \\tau(n) \\equiv \\sigma_{11}(n) \\pmod{691} \\]
> 其中 \\(\\sigma_{11}(n) = \\sum_{d \\mid n} d^{11}\\)。
> **691 是什么？** 它是伯努利数 \\(B_{12} = \\frac{691}{2730}\\) 的分子！
> 这个同余式连接了模形式、伯努利数、\\(\\zeta\\) 函数——
> 是 20 世纪数论最深刻的结果之一，而拉马努金在 1916 年就"看"到了。
> （691 称为"拉马努金素数"之一）

---

## 31.4 中国剩余定理：韩信点兵

### 问题

> 韩信点兵：3 人一排余 2，5 人一排余 3，7 人一排余 2。问兵数？

即解同余方程组：
\\[ \\begin{cases} x \\equiv 2 \\pmod{3} \\\\ x \\equiv 3 \\pmod{5} \\\\ x \\equiv 2 \\pmod{7} \\end{cases} \\]

### 中国剩余定理

> 若 \\(m_1, \\ldots, m_k\\) 两两互素，则
> \\[ \\begin{cases} x \\equiv a_1 \\pmod{m_1} \\\\ \\vdots \\\\ x \\equiv a_k \\pmod{m_k} \\end{cases} \\]
> 在模 \\(M = m_1\\cdots m_k\\) 下有**唯一解**。

**构造解**：令 \\(M_i = M / m_i\\)，找 \\(t_i\\) 使 \\(M_i t_i \\equiv 1 \\pmod{m_i}\\)，
则 \\(x = \\sum a_i M_i t_i\\)。

### Python：解韩信点兵

```python
def crt(remainders, moduli):
    """中国剩余定理求解"""
    from math import gcd
    # 检查两两互素
    for i in range(len(moduli)):
        for j in range(i+1, len(moduli)):
            assert gcd(moduli[i], moduli[j]) == 1, "模数不互素"
    M = 1
    for m in moduli: M *= m
    x = 0
    for a, m in zip(remainders, moduli):
        Mi = M // m
        # 求 Mi 在 mod m 下的逆元
        ti = pow(Mi, -1, m)  # Python 3.8+
        x += a * Mi * ti
    return x % M

# 韩信点兵
ans = crt([2, 3, 2], [3, 5, 7])
print(f"兵数: {ans} (mod 105)")
# 验证
assert ans % 3 == 2 and ans % 5 == 3 and ans % 7 == 2
print("✓ 验证通过")
# 兵数: 23 (mod 105)
```

---

## 📝 本章习题

**题 31.1** 用欧几里得证明法说明：假设素数有限导出矛盾的关键一步是什么？
为什么 \\(N = p_1\\cdots p_n + 1\\) 的素因子一定是"新"的？

**题 31.2** 求 \\(3^{2026}\\) 的个位数。（提示：找 \\(3^k \\bmod 10\\) 的周期）

**题 31.3** 用费马小定理求 \\(2^{100} \\bmod 101\\)。（101 是素数）

**题 31.4** 解同余方程组：\\(x \\equiv 1 \\pmod{4}\\)，\\(x \\equiv 2 \\pmod{5}\\)。
用中国剩余定理给出最小正整数解。

**题 31.5**（拉马努金式）验证 \\(\\tau(2) = -24\\) 满足同余式
\\(\\tau(2) \\equiv \\sigma_{11}(2) \\pmod{691}\\)。
其中 \\(\\tau\\) 由 \\(\\sum \\tau(n)q^n = q\\prod_{n\\ge1}(1-q^n)^{24}\\) 定义，
\\(\\sigma_{11}(2) = 1^{11} + 2^{11}\\)。

---

## ✅ 习题解答

### 题 31.1 解答
**关键**：\\(N\\) 的任一素因子 \\(q\\)，若 \\(q = p_i\\)（某个已知素数），
则 \\(q \\mid p_1\\cdots p_n\\)，又 \\(q \\mid N = p_1\\cdots p_n + 1\\)，
故 \\(q \\mid (N - p_1\\cdots p_n) = 1\\)，即 \\(q \\mid 1\\)，不可能。
所以 \\(q\\) 不是任何 \\(p_i\\)，是"新"素数，与"素数只有 \\(n\\) 个"矛盾。∎

### 题 31.2 解答
\\(3^1 \\equiv 3\\)，\\(3^2 \\equiv 9\\)，\\(3^3 \\equiv 27 \\equiv 7\\)，\\(3^4 \\equiv 21 \\equiv 1 \\pmod{10}\\)
周期 4。\\(2026 = 4 \\times 506 + 2\\)，故
\\[ 3^{2026} = (3^4)^{506} \\cdot 3^2 \\equiv 1^{506} \\cdot 9 = 9 \\pmod{10} \\]
**个位数是 9**。

### 题 31.3 解答
101 是素数，\\(\\gcd(2, 101) = 1\\)，
由费马小定理：\\(2^{100} \\equiv 1 \\pmod{101}\\)。
**答案：1**。

```python
print(pow(2, 100, 101))  # 1
```

### 题 31.4 解答
\\(M = 20\\)，\\(M_1 = 5\\)，\\(M_2 = 4\\)。
- \\(5t_1 \\equiv 1 \\pmod{4} \\Rightarrow t_1 \\equiv 1 \\pmod{4}\\)，取 \\(t_1 = 1\\)
- \\(4t_2 \\equiv 1 \\pmod{5} \\Rightarrow t_2 \\equiv 4 \\pmod{5}\\)，取 \\(t_2 = 4\\)
\\[ x = 1 \\cdot 5 \\cdot 1 + 2 \\cdot 4 \\cdot 4 = 5 + 32 = 37 \\equiv 17 \\pmod{20} \\]
**最小正整数解：17**。
验证：\\(17 \\bmod 4 = 1\\) ✓，\\(17 \\bmod 5 = 2\\) ✓

### 题 31.5 解答
\\(\\sigma_{11}(2) = 1^{11} + 2^{11} = 1 + 2048 = 2049\\)。
\\(2049 \\bmod 691\\)：\\(2049 = 2 \\times 691 + 667\\)，故 \\(\\equiv 667\\)。
\\(\\tau(2) = -24 \\equiv 691 - 24 = 667 \\pmod{691}\\)。
\\[ -24 \\equiv 667 \\equiv 2049 = \\sigma_{11}(2) \\pmod{691} \\] ✓

> 🌟 这不是巧合！拉马努金猜想 \\(\\tau(n) \\equiv \\sigma_{11}(n) \\pmod{691}\\)
> 对**一切** \\(n\\) 成立。这后来成为模形式 \\(\\ell\\)-adic 表示理论的滥觞，
> 最终通向费马大定理的证明（怀尔斯）。一个同余式，牵动百年数学！

---

## 🎯 本章小结

| 概念 | 核心 | 通向 |
|---|---|---|
| 算术基本定理 | 唯一素因子分解 | 代数数论（第27章注记） |
| 同余 | \\(a \\equiv b \\pmod{m}\\) | 群论、密码学 |
| 费马小定理 | \\(a^{p-1} \\equiv 1\\) | 欧拉定理、RSA |
| 中国剩余定理 | 联立同余有唯一解 | 环论直和分解 |
| \\(\\tau\\) 同余 | \\(\\tau(n) \\equiv \\sigma_{11}(n) (691)\\) | 模形式、朗兰兹纲领 |

**下一章预告**：解析数论——用复分析研究素数。
黎曼 \\(\\zeta\\) 函数、素数定理、拉马努金 \\(\\tau\\) 函数的真面目。
