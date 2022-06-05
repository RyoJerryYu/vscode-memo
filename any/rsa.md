解释RSA

# 数论

## 什么叫质数，什么叫互质
关键：找到可以整除的数（最大公约数），能找到就是互质

## 有多少个不互质的数
对于整数 n ，在 (1, n] 中有多少个数与 n 互质？
互质的个数即为 

$$
\phi(n) = p_1^{k_1}p_2^{k_2}...p_r^{k_r}(1-\frac{1}{p_1})(1-\frac{1}{p_2})...(1-\frac{1}{p_r})
$$

a.k.a 欧拉函数

## 欧拉定理

若 a, n 互质，有
$$
a^{\phi(n)} \equiv 1 \pmod n
$$
即， a 的 phi(n) 次方被 n 除 余数为 1 。

## 模反元素

若 a 和 n 互质，一定能找到 b ，使

$$
ab \equiv 1 \pmod n
$$

即 ab - 1 被 n 整除。

欧拉定理可证明模反元素必定存在 a^{\phi(n)-1} 就是其中一个。

# RSA算法

## 过程

1. 取质数 p, q
2. 求 n = p*q
3. 求欧拉函数 \phi(n) = (p-1)(q-1)
4. 从 (1, \phi(n)) 中随机取 e ，要求 e 与 \phi(n) 互质
5. 计算 e 对 \phi(n) 的模反元素 d ，即 ed == 1 (mod \phi(n))
6. {n,e} 与 {n,d} 分别为公钥私钥（假设 e 公 d 私）

破解工作量在于将 n 分解为 质数 p q (因数分解)

## 加密解密

发送信息 m ，则求：

$$
m^e \equiv c \pmod n
$$

即

$$
c = m^e \bmod n
$$

发送 c

接收到 c 后，由于下面等式成立（后面再证明）

$$
c^d \equiv m \pmod n
$$

则有

$$
m = c^d \bmod n
$$

可得原文。

## 加解密正确性证明

$$
m^e \equiv c \pmod n \\
c = m^e - kn \\
相当于要证明 \\
(m^e - kn)^d \equiv m \pmod n \\
m^{ed} \equiv m \pmod n \\
\\
由于
ed \equiv 1 \pmod {\phi(n)} \\
则有
ed = h\phi(n)+1 \\
代入有
m^{h\phi(n)+1} \equiv m \pmod n
$$

则

麻烦，下略