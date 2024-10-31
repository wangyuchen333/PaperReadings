[什么是扩散模型？|Lil'Log --- What are Diffusion Models? | Lil'Log](https://lilianweng.github.io/posts/2021-07-11-diffusion-models/)
reparameter conditioned unconditioned
too much formula

[通过估计数据分布的梯度进行生成建模 |杨松 --- Generative Modeling by Estimating Gradients of the Data Distribution | Yang Song](https://yang-song.net/blog/2021/score/)
the Variance Exploding SDE (VE SDE), the Variance Preserving SDE (VP SDE), and the sub-VP SDE.

[Miika Aittala: Elucidating the Design Space of Diffusion-Based Generative Models](https://www.youtube.com/watch?v=T0Qxzf0eaio&t=1036s)
noise schedule vs step length: two ways to get a refine image
sdeCODE dffusion parrallell

[Diffusion and Score-Based Generative Models - YouTube](https://www.youtube.com/watch?v=wMmqCMwuM2Q)
Intuition of Fisher divergence to Score matching

[Noise schedules considered harmful – Sander Dieleman](https://sander.ai/2024/06/14/noise-schedules.html)

[生成扩散模型漫谈（一）：DDPM = 拆楼 + 建楼 - 科学空间|Scientific Spaces](https://kexue.fm/archives/9119)
拆楼，重参数化，一步得出$x_t$
建楼，一步步的预测噪声
DDPM的采样每次都从一个随机噪声出发，需要重复迭代TT步来得到一个样本输出；朗之万采样则是从任意一个点出发，反复迭代无限步，理论上这个迭代无限步的过程中，就把所有数据样本都被生成过了。所以两者除了形式相似外，实质上是两个截然不同的模型。
![[Pasted image 20241031172232.png]]

[生成扩散模型漫谈（五）：一般框架之SDE篇 - 科学空间|Scientific Spaces](https://kexue.fm/archives/9209)
在连续时间的SDE视角之下，不同的TT是SDE不同的离散化程度的体现，它们会自动地导致相似的结果，我们不需要事先指定TT，而是根据实际情况下的精确度来取适当的TT进行数值计算。
为什么随机项的阶要比确定项的阶要高？这个还真不是那么容易解释，也是SDE比较让人迷惑的地方之一。简单来说，就是εε一直服从标准正态分布，如果随机项的权重也是O(Δt)O(Δt)，那么由于标准正态分布的均值为00、协方差为II，临近的随机效应会相互抵消掉，要放大到O(Δt−−−√)O(Δt)才能在长期结果中体现出随机效应的作用。