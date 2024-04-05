



本周对论文正文部分进行推导，接下来继续**完善**！！

### 回顾原始扩散模型：

参考文献： [Ho 等 - 2020 - Denoising Diffusion Probabilistic Models.pdf](img\Ho 等 - 2020 - Denoising Diffusion Probabilistic Models.pdf) 

 前向过程：
 $q(\mathbf{x}^n|\mathbf{x}^{n-1})=\mathcal{N}\left(\mathbf{x}^n;\sqrt{(1-\beta _n)}\mathbf{x}^{n-1},\beta _nI\right)$
 
 反向过程：
 $p(\mathbf{x}^{n-1}|\mathbf{x}^n){=}\mathcal{N}\left(\mathbf{x}^{n-1};\widetilde{\mu}(x  _n),\tilde{\beta} _nI\right),\quad\tilde{\mu}(x _n)=\frac1{\sqrt{\alpha} _n}\left(x _n-\frac{\beta _n}{\sqrt{1-\overline{\alpha}} _n}\in\right)$

### 时间序列的条件扩散模型：

参考文献：

 [Rasul 等 - Autoregressive Denoising Diffusion Models for Mult.pdf](img\Rasul 等 - Autoregressive Denoising Diffusion Models for Mult.pdf) 

 [Shen 和 Kwok - Non-autoregressive Conditional Diffusion Models fo.pdf](img\Shen 和 Kwok - Non-autoregressive Conditional Diffusion Models fo.pdf) 

 [Shen 等 - 2024 - MULTI-RESOLUTION DIFFUSION MODELS FOR TIME SERIES .pdf](img\Shen 等 - 2024 - MULTI-RESOLUTION DIFFUSION MODELS FOR TIME SERIES .pdf) 

通过对联合分布  $p _\theta(\mathbf{x} _t^{0:N})$  进行建模，以自回归的方式生成未来值，
 n 表示加噪次数(沿用原始 DDPM),   $\mathbf{x} _t^{0:N}=\{\mathbf{x}  _t^0\}\bigcup\{\mathbf{x} _t^n\} _{n=1,...N}$  ,  L 为历史窗口的长度
 
$$
p _{\theta}(\mathbf{x} _{t}^{0:N}|\mathbf{c})=p _{\theta}(\mathbf{x}  _{t}^{N})\prod _{n=1}^{N}p _{\theta}\left(\mathbf{x} _{t}^{n-1}|\mathbf{x} _{t}^{n},\mathbf{c}\right),\mathbf{c}=\mathcal{F}(\mathbf{x} _{t-L:t}^{0})\:,
$$

c 是条件，  $\mathcal{F}$  是以过去的观测值  $\mathbf{x} _{t-L:t}^0$ 作为输入的条件网络。相应地，第 n 步的逆向去噪过程如下式：

$$
p _{\theta}(\mathbf{x} _{t}^{n-1}|\mathbf{x} _{t}^{n},\mathbf{c})=\mathcal{N}(\mathbf{x} _{t}^{n-1};\mu _{\theta}(\mathbf{x} _{t}^{n},n|\mathbf{c}),\sigma _{n}^{2}\mathbf{I}),\quad n=N,N-1,...,1.
$$

通过反复运行此去噪步骤至到 k=1, 最终生成的样本为  $\hat{x} _t^0$  

### 我们的方法

而我们是通过对残差联合分布 $p _{\theta}(\mathbf{y} _{t}^{0:N})$ 进行建模，以自回归的方式生成未来残差值，
 n 表示加噪次数(沿用原始 DDPM), $\mathbf{y} _t^{0:N}=\{\mathbf{y} _t^0\}\bigcup\{\mathbf{y} _t^n\} _{n=1,...N}$, L 为历史窗口的长度
 
$$
p _{\theta}(\mathbf{y} _{t}^{0:N}|\mathbf{c})=p _{\theta}(\mathbf{y} _{t}^{N})\prod _{n=1}^{N}p _{\theta}\left(\mathbf{y} _{t}^{n-1}|\mathbf{y} _{t}^{n},\mathbf{c}\right),\quad\mathbf{c}=\mathcal{F}(\mathbf{x} _{t-L:t}^{0})\:,
$$

 c 是条件，  $\mathcal{F}$  是以过去的观测值   $\mathbf{x} _{t-L:t}^0$   **作为输入的条件网络(对应图中图像纹理预测器)**。相应地，第 n 步的逆向去噪过程如下式：

$$
p _{\theta}(\mathbf{y} _{t}^{n-1}|\mathbf{y} _{t}^{n},\mathbf{c})=\mathcal{N}(\mathbf{y} _{t}^{n-1};\mu _{\theta}(\mathbf{y} _{t}^{n},n|\mathbf{c}),\sigma _{n}^{2}\mathbf{I}),\quad n=N,N-1,\ldots,1.
$$

通过反复运行此去噪步骤至到 n=1, 最终生成 t 时刻的残差为 $\mathbf{y} _t^0;$ 最后将其预测的残差与上方时序网络输出均值图像进行叠加输出，得到最终预测值

$$
\hat{\mathbf{x}} _{t}^{0}=\mathcal{M}(\mathbf{x} _{t-L:t}^{0})\oplus\hat{\mathbf{y}} _{t}^{0}
$$

$\mathcal{M}$ 是以过去的观测值 $\mathbf{x} _{t-L:t}^0$  **作为输入的时序网络(对应图中图像均值预测器)**。

![1712157046177](img/1712157046177.png)


