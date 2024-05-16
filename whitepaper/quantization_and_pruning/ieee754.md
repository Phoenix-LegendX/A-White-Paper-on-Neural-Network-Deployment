---
description: source from https://www.hello-algo.com/🚀
---

# 🖕 IEEE754标准

## 浮点数编码

记一个 32 比特长度的二进制数为：

$$
b_{31} b_{30} b_{29} \ldots b_2 b_1 b_0
$$

根据 IEEE 754 标准，32-bit 长度的 `float` 由以下三个部分构成。

* 符号位 $$\mathrm{S}$$ ：占 1 位 ，对应 $$b_{31}$$。
* 指数位 $$\mathrm{E}$$ ：占 8 位 ，对应 $$b_{30} b_{29} \ldots b_{23}$$。
* 分数位 $$\mathrm{N}$$ ：占 23 位 ，对应 $$b_{22} b_{21} \ldots b_0$$。

二进制数 `float` 对应值的计算方法为：

$$
\text {val} = (-1)^{b_{31}} \times 2^{\left(b_{30} b_{29} \ldots b_{23}\right)_2-127} \times\left(1 . b_{22} b_{21} \ldots b_0\right)_2
$$

转化到十进制下的计算公式为：

$$
\text {val}=(-1)^{\mathrm{S}} \times 2^{\mathrm{E} -127} \times (1 + \mathrm{N})
$$

其中各项的取值范围为：

$$
\begin{aligned} \mathrm{S} \in & \{ 0, 1\}, \quad \mathrm{E} \in \{ 1, 2, \dots, 254 \} \newline (1 + \mathrm{N}) = & (1 + \sum_{i=1}^{23} b_{23-i} 2^{-i}) \subset [1, 2 - 2^{-23}] \end{aligned}
$$

<figure><img src="../../.gitbook/assets/图片 (57).png" alt="" width="563"><figcaption></figcaption></figure>

观察上图，给定一个示例数据 $$\mathrm{S} = 0$$ ， $$\mathrm{E} = 124$$ ，$$\mathrm{N} = 2^{-2} + 2^{-3} = 0.375$$ ，则有：

$$
\text { val } = (-1)^0 \times 2^{124 - 127} \times (1 + 0.375) = 0.171875
$$

现在我们可以回答最初的问题：**`float` 的表示方式包含指数位，导致其取值范围远大于 `int`** 。根据以上计算，`float` 可表示的最大正数为 $$2^{254 - 127} \times (2 - 2^{-23}) \approx 3.4 \times 10^{38}$$ ，($$2^{e-1}-1=127$$)切换符号位便可得到最小负数。

<mark style="color:red;">**尽管浮点数**</mark><mark style="color:red;">** **</mark><mark style="color:red;">**`float`**</mark><mark style="color:red;">** **</mark><mark style="color:red;">**扩展了取值范围，但其副作用是牺牲了精度**</mark><mark style="color:red;">。整数类型</mark> <mark style="color:red;"></mark><mark style="color:red;">`int`</mark> <mark style="color:red;"></mark><mark style="color:red;">将全部 32 比特用于表示数字，数字是均匀分布的；而由于指数位的存在，浮点数</mark> <mark style="color:red;"></mark><mark style="color:red;">`float`</mark> <mark style="color:red;"></mark><mark style="color:red;">的数值越大，相邻两个数字之间的差值就会趋向越大。</mark>

如下表所示，指数位 $$\mathrm{E} = 0$$ 和 $$\mathrm{E} = 255$$ 具有特殊含义，**用于表示零、无穷大、**$$\mathrm{NaN}$$ **等**。

| 指数位 E                | 分数位 N = 0      | 分数位 N != 0       | 计算公式                                                                     |
| -------------------- | -------------- | ---------------- | ------------------------------------------------------------------------ |
| $$0$$                | $$\pm 0$$      | 次正规数             | $$(-1)^{\mathrm{S}} \times 2^{-126} \times (0.\mathrm{N})$$              |
| $$1, 2, \dots, 254$$ | 正规数            | 正规数              | $$(-1)^{\mathrm{S}} \times 2^{(\mathrm{E} -127)} \times (1.\mathrm{N})$$ |
| $$255$$              | $$\pm \infty$$ | $$\mathrm{NaN}$$ |                                                                          |

值得说明的是，次正规数显著提升了浮点数的精度。最小正正规数为 $$2^{-126}$$ ，最小正次正规数为 $$2^{-126} \times 2^{-23}$$。双精度 `double` 也采用类似于 `float` 的表示方法，在此不做赘述。

