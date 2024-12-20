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
为什么随机项的阶要比确定项的阶要高？因为临近随机数的抵消
[噪音时间表被认为是有害的 – Sander Dieleman --- Noise schedules considered harmful – Sander Dieleman](https://sander.ai/2024/06/14/noise-schedules.html)
**variance-preserving**:  isn’t always true in practice (for example, image pixels scaled to [−1,1] will have a lower variance), it’s often close enough that things still work well in practice.
**variance-exploding**:to grow quite large to be able to drown out all of the signal for large values of
 **sub-VP**:这种选择应该会导致通过数据和噪声之间的 input 空间的更直的路径，这反过来又减少了达到一定质量水平所需的采样步骤数（有关使用更少步骤进行采样的更多信息，请参阅[我之前的博客文章](https://sander.ai/2024/02/28/paradox.html)）。Stable Diffusion 3 使用这种方法
 https://sander.ai/images/some_schedules.png
 三个示例噪声计划的标准差（蓝色）和比例因子（橙色）：一个方差保留 （VP）、一个方差爆炸 （VE） 和一个子 VP。此外，还显示了损坏过程每个步骤的结果总标准差（绿色），假设干净信号具有单位方差。
 **用 logSNR 而不是时间来表示事物非常有用**：它是对被噪声掩盖的信息量的直接测量，因此更容易在不同设置之间进行比较：不同的模型、不同的噪声计划，以及跨 VP、VE 和 sub-VP 公式。
 这两种情况通常都不是均匀分布的。同样重要的是要考虑到这些分布不一定彼此相似：在感知上高度相关的噪声水平可能很容易让模型学习进行预测，反之亦然。特别困难的噪声级别可能值得关注以提高输出质量，但它们也可能非常困难以至于无法学习，在这种情况下，花费在它们上的任何努力都会白费。
 虽然预测准确性当然与学习任务的难度密切相关，但它不是一回事。采样过程中误差的累积也引入了不对称性，因为在采样过程早期（高噪声水平）发生的错误比后期（低噪声水平）更容易导致问题。这些细微的差异可以导致噪声水平之间的最佳平衡，看起来与训练时截然不同，我们稍后将看到。
 根据参数化的选择，损失函数中会强调或淡化不同的噪声水平
 ![[Pasted image 20241107113016.png]]
 ![[Pasted image 20241107113101.png]]
 ![[Pasted image 20241107113124.png]]
 在训练过程中，每个训练样本都会随机抽取一个时间步长 $x_0$ 。大多数方法采用均匀抽样时间步长（包括 DDPM），但有些方法，如 EDM [2](https://sander.ai/2024/06/14/noise-schedules.html#fn:elucidating) 和 Stable Diffusion 3 [5](https://sander.ai/2024/06/14/noise-schedules.html#fn:sd3) ，则选择不同的分布。这也就意味着，这也会影响噪声水平的平衡，因为某些水平将看到比其他水平更多的训练样本。
 ![[Pasted image 20241107113935.png]]
 ![[Pasted image 20241107114547.png]]
 ![[Pasted image 20241107114714.png]]
 You may have recognised this as the key idea behind [**importance sampling**](https://en.wikipedia.org/wiki/Importance_sampling), because that’s exactly what this is.
 ![[Pasted image 20241107114947.png]]
 ![[Pasted image 20241107115421.png]]
 当结合噪声调度与均匀时间步长采样以及均匀时间步长间隔时，正如通常所做的那样，隐含的假设是特定的噪声水平对训练和采样同等重要。这通常并非如此（参见第 2 节），EDM 论文也通过分别调整噪声水平分布 $p(σ)$ 和采样间隔来支持这一点。Kingma 与 Gao [14](https://sander.ai/2024/06/14/noise-schedules.html#fn:diffusion-elbo) 将这些选择表达为以对数信噪比（logSNR）为参数的加权函数，展示了它们最终如何变得截然不同（参见他们论文中的图 2）。
 这也使我们能够将各种扩散和扩散相关模型（例如，流匹配 [3](https://sander.ai/2024/06/14/noise-schedules.html#fn:flowmatching) / 矫正流 [4](https://sander.ai/2024/06/14/noise-schedules.html#fn:rectifiedflow) ，通过直接迭代进行反演 [15](https://sander.ai/2024/06/14/noise-schedules.html#fn:indi) ，……）视为同一想法的不同变体，这些变体具有不同的噪声水平权重、间距和缩放选择。我强烈建议您阅读 Kingma 和 Gao 的“理解扩散目标”论文的附录 D，其中对这些关系进行了很好的概述。在 EDM 论文的第 2 节和附录 C 中，Karras 等人进行了类似的练习，这也非常值得一读。前者将所有内容都表达为对数信噪比 λλ ，而后者使用标准差 σσ 。
5. Adaptive weighting mechanism is not quite followed