# 从Fibonacci数列开始
数学上，斐波那契数列以递归的形式进行定义：
$$F_{0} = 0\\
F_{1} = 1\\
F_{n} = F_{n-1} + F_{n-2}$$

### 1. 递归计算
我们可以按照递归的方式计算F(n)的值，但是存在大量的重复计算，当N的数据过大时，会造成堆栈溢出问题，代码如下：

```python
def fib(n):
    if n == 0:
        return 0
    if n == 1 or n == 2:
        return 1
    return fib(n - 1) + fib(n - 2)
```
从规模上来说，如果需要计算F(4)的值，需要进行9次元素操作

设T(n)为计算n的时间复杂度，那么
$$T(n) = T(n-1) + T(n-2)+O(1)$$
一般情况，可以得出：$$T(n)< 2* T(n-1) + O(1)$$

粗略估算，$T（n） <  O（2^n）$，上述代码求解F(n)的计算，它的时间复杂度是$O(2^n)$，可以看出这是一个指数时间

### 2. 迭代方式
我们发现上述递归中，存在重复计算的情况，所以可以采用迭代方式记录每次求解出来的值

```python
def fib(n):
    fibs = [0] * (n + 1)
    fibs[1] = 1
    for i in range(2, n + 1):
        fibs[i] = fibs[n - 1] + fibs[n - 2]
    return fibs[n]
```
$时间复杂度：O(n)，
空间复杂度：O(n)$

上述代码中我们其实只需要F(n)的值，其他的值的内存空间其实也可以重复利用，优化后如下，

```python
def fib(n):
    f1 = 0
    f2 = 1
    while n:
        f1, f2 = f2, f1 + f2
        n -= 1
    return f1
```
$时间复杂度：O(n)，
空间复杂度：O(1)$

### 3. 矩阵解决问题
从定义开始：
$$F_{0} = 0\\
F_{1} = 1\\
F_{n} = F_{n-1} + F_{n-2}$$
转化为矩阵形式
$$\begin{pmatrix}
 F_{n+1}\\ 
 F_{n} 
\end{pmatrix}=
\begin{pmatrix}
 1&1\\ 
 1&0 
\end{pmatrix}*
\begin{pmatrix}
 F_{n}\\ 
 F_{n-1} 
\end{pmatrix}
$$
可以得出：
$$\begin{pmatrix}
 F_{n+1}\\ 
 F_{n} 
\end{pmatrix}=
\begin{pmatrix}
 1&1\\ 
 1&0 
\end{pmatrix}^{n} *
\begin{pmatrix}
 F_{1}\\ 
 F_{0} 
\end{pmatrix}
$$
我们设定
$$A=\begin{pmatrix}
 1&1\\ 
 1&0 
\end{pmatrix}$$
很显然可以变为如下：
$$A=\begin{pmatrix}
 F_{2}&F_{1}\\ 
 F_{1}&F_{0} 
\end{pmatrix}$$
通过数学归纳法可以推出以下公式：
$$A^{n}=\begin{pmatrix}
 F_{n+1}&F_{n}\\ 
 F_{n}&F_{n-1} 
\end{pmatrix}=
\begin{pmatrix}
 1&1\\ 
 1&0 
\end{pmatrix}^{n}$$
很显然计算F(n)的值，只需要进行矩阵的n次幂运算，取出结果矩阵第二行第一个元素值即可
$时间复杂度：O(n)，
空间复杂度：O(1)$

这里可以利用**快速幂运算**求解，假设计算A的N次幂,二阶矩阵的乘法满足结合律

设A,B,C都是任意的二阶矩阵，则
$$A(BC)=(AB)C$$

我们设定：$m=[\frac{n}{2}]$

* 当n为偶数: $A^{N}=A^{m}∗A^{m}$
* 当n为奇数: $A^{N}=A^{m}∗A^{m}∗A$

    相当于$A^{6}=A^3∗A^3，A^7=A^3∗A^3∗A$

这样可以减少计算次数，因为$A6=A∗A∗A∗A∗A∗A$这里有5个乘,$A6=（A∗A∗A）∗（A∗A∗A）$ 计算完$A*A*A$ 得到结果$A^3$，再乘以$A^3$ 这里用了3个乘

以下是普通数据的快速幂运算，运算改为矩阵乘法,ret改为单位矩阵即可

```python
def qpow(base, exp):
    if exp == 0:
        return 1
    ret = 1
    while exp:
        if exp & 1:
            ret = ret * base
        base = base * base
        exp >>= 1
    return ret
```
### 4. 矩阵再推导
我们可以设定：  $n=2m$
那么
$$A^{2m}=\begin{pmatrix}
 F_{2m+1}&F_{2m}\\ 
 F_{2m}&F_{2m-1} 
\end{pmatrix}=A^{m}*A^{m}$$
已知
$$
A^{m}=\begin{pmatrix}
 F_{m+1}&F_{m}\\ 
 F_{m}&F_{m-1} 
\end{pmatrix}
$$
所以：
$$
\begin{pmatrix}
 F_{m+1}&F_{m}\\ 
 F_{m}&F_{m-1} 
\end{pmatrix}* 
\begin{pmatrix}
 F_{m+1}&F_{m}\\ 
 F_{m}&F_{m-1} 
\end{pmatrix}=
\begin{pmatrix}
 F_{2m+1}&F_{2m}\\ 
 F_{2m}&F_{2m-1} 
\end{pmatrix}
$$
计算后可以得出：
$$
\begin{pmatrix}
 F_{2m+1}\\ 
 F_{2m} 
\end{pmatrix}=
\begin{pmatrix}
 F_{m+1}^{2}+F_{m}^{2}\\ 
 F_{m}*(F_{m+1}+F_{m-1}) 
\end{pmatrix}$$

这里需要注意一点 n 需要进行奇偶性判定：

* 当n为奇数时：$m=[\frac{n}{2}]，n=2*m+1$ 此时，
$$\begin{pmatrix}
 F_{n+1}\\ 
 F_{n} 
\end{pmatrix}=
\begin{pmatrix}
 F_{2m+2}\\ 
 F_{2m+1} 
\end{pmatrix}=
\begin{pmatrix}
 F_{m+1}*(F_{m+2}+F_{m})\\ 
F_{m+1}^{2}+F_{m}^{2}
\end{pmatrix}
$$
由于 $F_{m+2}=F_{m+1}+F_{m}$ ，因此，可以推导出
$$\begin{pmatrix}
 F_{n+1}\\ 
 F_{n} 
\end{pmatrix}=
\begin{pmatrix}
 F_{m+1}*(F_{m+1}+2*F_{m})\\ 
F_{m+1}^{2}+F_{m}^{2}
\end{pmatrix}
$$

* 当n为偶数时：$m=\frac{n}{2}，n=2*m$，此时
    $$\begin{pmatrix}
 F_{n+1}\\ 
 F_{n} 
\end{pmatrix}=
\begin{pmatrix}
 F_{2m+1}\\ 
 F_{2m} 
\end{pmatrix}=
\begin{pmatrix}
  F_{m+1}^{2}+F_{m}^{2}\\ 
 F_{m}*(F_{m+1}+F_{m-1}) 
\end{pmatrix}
$$
由于 $F_{m+2}=F_{m+1}+F_{m}$，因此，可以推导出：
$$\begin{pmatrix}
 F_{n+1}\\ 
 F_{n} 
\end{pmatrix}=
\begin{pmatrix}
 F_{m+1}^{2}+F_{m}^{2}\\ 
F_{m}*(2*F_{m+1}-F_{m})
\end{pmatrix}
$$

所以计算F(N)的值，只需要知道F(n/2+1)和F(n/2)即可

```python
def fib(n):
    if n < 1:
        return (1, 0)

    f_m_1, f_m = fib(n >> 1)
    if n & 1:
        return f_m_1 * (f_m_1 + 2 * f_m), f_m ** 2 + f_m_1 ** 2
    else:
        return f_m_1 ** 2 + f_m ** 2, f_m * (2 * f_m_1 - f_m)
```
$时间复杂度：O(\log_2 n)，
空间复杂度：O(1)$









