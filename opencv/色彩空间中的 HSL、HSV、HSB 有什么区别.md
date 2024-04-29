色彩空间中的 HSL、HSV、HSB 有什么区别？ - pourtheworld的回答 - 知乎 https://www.zhihu.com/question/22077462/answer/3139942407



本文将详细介绍**光波**如何产生，发射，被人眼所**感知**，并在实时渲染过程中以一组**刺激值**的形式参与运算。

---

主要参考引用：

1. [Scratch a Pixel](https://link.zhihu.com/?target=https%3A//www.scratchapixel.com/lessons/digital-imaging/colors/introduction.html)[[1]](#ref_1)
2. [Real-Time Rendering](https://link.zhihu.com/?target=https%3A//www.realtimerendering.com/)[[2]](#ref_2)。
3. [Cambridge in Colour](https://link.zhihu.com/?target=https%3A//www.cambridgeincolour.com/color-management-printing.htm)[[3]](#ref_3)。
4. [Color vision and colorimetry : theory and applications](https://link.zhihu.com/?target=http%3A//library.lol/main/DE9A15D97C096113BB9494C263B368E9)[[4]](#ref_4)。
5. [Color and Radiometry - Physically Based Rendering](https://link.zhihu.com/?target=https%3A//www.pbr-book.org/3ed-2018/Color_and_Radiometry)[[5]](#ref_5)。
6. [色彩科学](https://www.zhihu.com/column/c_1602295156237197312)[[6]](#ref_6)。

## Radiometry

### Light Wave

以下内容引用于 [RTR4 P293~297](https://link.zhihu.com/?target=https%3A//www.realtimerendering.com/) 、 [Photon - Wiki](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Photon) 、[Electromagnetic Radiation - Wiki](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Electromagnetic_radiation) 。

物体的**能量 (热能、电能、化学)** 会引起物体内部 **electric charges (电荷)** 的震荡，部分引起震荡的能量会转化为 **light energy (光能)**，并以 **light wave (光波)** 的形式辐射出去。

在**physical optics (物理光学)**中，**light wave (光波)** 对应的模型是 **electromagnetic transverse wave (电磁横波)**：即**电场**和**磁场**均**垂直**于其**传播**方向进行**震荡**的**波**，并携带了**radiant energy (辐射能量)**。

![](images/v2-ba8c28721a2a2e0b8273922b47888220_720w.webp)

Light Wave

其中，**electromagnetic transverse wave** 通过 **photon (光子)** 进行传播。

**photon** 作为一个不带 **charge** 的基本粒子，包含了 **light wave / radio wave** 等 **electromagnetic radiation (电磁辐射)**，是 **electromagnetic field (电磁场)** 的一个 **quantum (量子)** 。

众所周知，有两种方式可以产生 **electric field (电场)**：

- A **charged** particle (一个**带电荷**的粒子)。
- A **changing magnetic field** (一个**变化**的**磁场**)。

由于 **electromagnetic transverse wave** 自带一个 **changing magnetic field**，即使 **photon** 本身不带 **charge**，也可以产生一个 **electric field**。

另一个角度来看，产生 **electromagnetic transverse wave** 的**源物体**本身带有 **charged particle** ，但是这些 **particle** 并不跟随 **electromagnetic transverse wave** 一起传播 (这和用石头打水漂起水波，但石头没有一直跟随水波远去是一个道理，具体可见 [How can photos have an electric field without having a charge](https://link.zhihu.com/?target=https%3A//physics.stackexchange.com/questions/725572/how-can-photons-have-an-electric-field-without-having-a-charge)) 。

---

### Light Energy

以下内容引用于 [RTR4 P293~297](https://link.zhihu.com/?target=https%3A//www.realtimerendering.com/) 、 [Phase Velocity - Wiki](https://link.zhihu.com/?target=https%3A//en.wikipedia.org/wiki/Phase_velocity) 。

前文我们曾经讨论得出每个 **photon** 所携带的 **energy** 由 **light wave** 的 **wavelength λ\lambda (光波波长)** 和 **light velocity cc (光速)** 得到：

- **wavelength** 由不同的**震荡频率**引起，拥有**单个 wavelength** 的 **light** 被称为 **monochromatic light (单色光)**，对应的，拥有**多个 wavelength** 的 **light** 被称为 **polychromatic light (复色光)**。对应到图中，**wavelength** 可以是**任意点** (比如位于波峰的点) 经过一个 **phase** 的**长度**。
- **phase velocity (相速度)** 指跟踪 **light wave** 上**任意点**经过一个给定 **phase** 所需要的**时间**，将其视为**常量**。若 **light wave** 穿过的是**真空**，那么该速度接近于**光速**。

需要注意的是：

- $Q$是 **radiometry** 中 **energy** 的简写，在 **quantum mechanics (量子力学)** 中简写为 $E$ (为了不与 **irradiance** 混淆)。
- $h$ 为 **Planck's constant**， $\begin{aligned}h\approx6.626\times10^{-34}\text{m}^2\text{kg/s}\end{aligned}$ 。
- $c$ 为**光速**， $c=299472458 \text{m/s}$ 。

$Q=hcλ=hfQ=\frac{hc}{\lambda}=hf$ 

---

在 **energy** 的基础之上，前文还介绍并推导了 **flux (power)** 和 **irradiance (radiant exitance)** 等概念。

而在 **light wave** 传播的过程中，**irradiance (radiant exitance)** 与 **light wave** 的 **squared** **amplitude (振幅平方)** 成正比，具体原因可见 [How is the Energy Transported Related to the Amplitude?](https://link.zhihu.com/?target=https%3A//www.physicsclassroom.com/class/waves/Lesson-2/Energy-Transport-and-the-Amplitude-of-a-Wave) (简单来说可以类比**弹簧**)。

前文在以 **geometric optics (几何光学)** 的视角介绍 **radiometry (辐射度量学)** 之前我们提到一个假设，即 **light wave** 可以被**线性组合**，**total wave** 是多个 **component waves** 的组合。

这看起来似乎会导致一个悖论：由于 **irradiance** 与 **squared amplitude** 成正比，那么两个相同的 **light wave** 线性相加不是会导致**4倍**的 **irradiance**？

实际上， nn 个 除了 **phase** 以外完全相同的 **monochromatic waves** 可以通过两种方式进行**叠加**：**coherent addition (相干叠加)** 和 **incoherent addition (非相干叠加)** 。其中 **coherent addition** 又分为 **constructive interference (相长干涉)** 和 **destructive interference (相消干涉)**。假设每个 **wave** 的 **amplitude** 均为 aa ，**irradiance** 均为 ka2ka^2 ，则三种情况对应下图：

- **constructive interference** - 每个 **wave** 的波峰和波谷**相互叠加**。
- **destructive interference** - 每对 **wave** 的波峰和波谷一一**抵消**。
- **incoherent addition** - 每个 **phase** 的波峰波谷相互**错开**。

![](images/v2-61fcd8cfb904bb1e3d9ef4105e1085db_720w.webp)

constructive interference / destructive interference / incoherent addition

对于更为常见的 **incoherent addition**， nn 个 **amplitude** 为 α\alpha 的 **monochromatic waves** 在叠加过后的 **total amplitude** 实际上为原本的 n\sqrt{n} 倍，因此 **total irradiance** 为原本的 (n)2=n(\sqrt{n})^2=n 倍，这是我们在 **geometric optics** 中可以假设 **irradiance** 具有**线性叠加性**的原因。

特别的，对于 **[coherent addtion](https://www.zhihu.com/search?q=coherent%20addtion&search_source=Entity&hybrid_search_source=Entity&hybrid_search_extra=%7B%22sourceType%22%3A%22answer%22%2C%22sourceId%22%3A3139942407%7D)**，当 **light wave** 在空间中传播时，它们之间的**相位关系**从一个位置变化到一个位置，**constructive interference** 和 **destructive interference** 会来回**切换**，因此并不会**能量不守恒**。

![](images/v2-6c090de3b51087430abb94585ac65021_720w.webp)

The waves interfere constructively and destructively in different regions of space.

---

## Photometry

**Photometry (光度学)** 是通过 **human visual system (人眼观测系统)** 对 **visible electromagnetic radiation (可见电磁辐射)** 进行 **perception (感知)** 的研究，它也是 **Radiometry (辐射度量学)** 和 **Colorimetry (色度学)** 的**中间桥梁**。

任意的 **radiometric quantity** 都可以通过与 **photopic spectral luminous efficiency curve / spectral response curve** V(λ)V(\lambda) (**视见函数**，描述了**人眼**对于不同 **wavelength** 的 **relative sensitivity**) 进行 **product integration** 得到对应的 **photometric quantity** 。

关于 **radiometric quantity** 可见上文 [radiometry](https://zhuanlan.zhihu.com/p/643880936) ， 以下几个小节引用于 [photometric quantity](https://zhuanlan.zhihu.com/p/597710417) ，介绍 **Photometry** 如何通过 V(λ)V(\lambda) 将 **radiometric quantity** 转化为 **photometric quantity** ：

![](images/v2-5b43738074433ac963d3490e5c8bb911_720w.webp)

radiometric quantity - photometric quantity

---

### Luminous Flux

上一节我们提到，**radiometry** 认为 **total wave** **(polychromatic light wave)** 一般是多个 **component waves (monochromatic light wave)** 的组合，并且在 **geometric optics** 的视角下，可以**线性叠加**。

即，对于 **monochromatic light wave** 常见的 **incoherent addition**，其 **irradiance** 具有**线性叠加**性质。

由于人眼对于不同 **wavelength** 的 **light wave** 的 **sensitivity** 不同 (见下文 **Colorimetry - Color Perception** 部分)，首先需要对不同波长的 **radiant flux / power** 构建 **spectral data** (见下文 **Colorimetry - Spectral Data** 部分)，即**辐射通量密度** ϕR(λ)\phi_R(\lambda) 。

在 **photometry** 中，与 **radiant flux (辐射通量)** 相对应的概念为 **luminous flux (光通量)**，同样对其构建 **spectral data**，即 **luminous flux density (光通量密度)** ϕV(λ)\phi_V(\lambda) ：

ϕV(λ)=σmV(λ)ϕR(λ)\phi_\mathrm{V}\left(\lambda\right)=\sigma_\mathrm{m}V\left(\lambda\right)\phi_\mathrm{R}\left(\lambda\right) \\

与 **luminous flux density** 的定义相类似，对所有**波长域内**的 **radiant flux** 进行**积分** (离散角度看，实际上将每个 **light wave** 携带的 **radiant flux** 加权后相加)，得到 **total luminous flux (总光通量)** ：

- 其中 R_R 和 V_V 暗示通量分别是**辐射**相关和**视觉**相关的。
- **视见函数** V(λ)V(\lambda) 对应不同波长的 **radiometric quantity** 和 **photometric quantity** 的视见关系。
- σm\sigma_\mathrm{m} 将在下一节详细介绍。
- **luminous flux** 的单位为 lumen(lm)\text{lumen(lm)} 。

ΦV=∫0∞σmV(λ)ϕR(λ)dλ\Phi_\mathrm{V}=\int_0^\infty\sigma_\mathrm{m}V\left(\lambda\right)\phi_\mathrm{R}\left(\lambda\right)\mathrm{d}\lambda \\

**加权相加**的定义方式暗示了 **luminous flux** 的**线性叠加**性质，与 **radiometry** 在 **geometric optics** 视角下对于 **light wave** 具有的 **radiometric quantity** 其**线性叠加**性质不同，**photometric quantity** 具有的**线性叠加**性质来自于大量实验的总结 (可见下文 **Colorimetry - Grassmann Laws**) 。

---

### Luminous Intensity

在 **photometry** 中，与 **radiant intensity (辐射强度)** 相对应的概念为 **luminous intensity (发光强度)**。

即，**点光源**向某一方向发射，其 **unit solid angle** 内的 **luminous flux** ：

$IV=ΦVdΩI_{V}=\frac{\Phi_\mathrm{V}}{d\Omega}$




若一个光源发出频率为 540×1012Hz540\times10^{12}\text{Hz} ，并且在给定方向上的 **radiant intensity** 为 1683W/sr \frac{1}{683} \text{W/sr} ，那么该光源在该方向上的 **luminous intensity** 为 1 candela(cd = lm/sr)1 \text{ candela(cd = lm/sr)} 。

根据 **electromagnetic transverse wave** 的基本关系式，可以简单得出这个频率对应 **monochromatic light wave** 的 **wavelength** λ\lambda 为 555nm555 \text{nm} **绿光**：λ=cf=299792458m/s540×1012Hz≈555nm\lambda=\frac cf=\frac{299792458\mathrm{m/s}}{540\times10^{12}\mathrm{Hz}}\approx555\mathrm{nm} \\有了 **monochromatic green light wave** 对应的 **luminous intensity**，其它颜色光源的 **luminous intensity** 即可通过视见函数 V(λ)V(\lambda) 得到。

即 V(λ)V(\lambda) 表征的是相同 **radiant intensity** 下，**wavelength** 为 λ\lambda 的 **monochromatic light source** 相对于 **wavelength** 为 555nm555 \text{nm} 的 **monochromatic green light wave** 所发出的 **luminous intensity**。

自然这是一个相对的**比值**，在 **wavelength** 等于 555nm555 \text{nm} 时为 11 ：

![](images/v2-ace26856173cae4758fca8bc6a79ed82_720w.webp)

V(λ)

比如，某个 **wavelength** 为 λ\lambda 的 **monochromatic light source** 其某个方向上的 **radiant intensity** 为 Iλ\mathcal{I}_{\lambda} ，则它在这个方向上的 **luminous intensity** 为：

IV=V(λ)IλI555nm=1683W/srcd=(683cd⋅sr/W)×V(λ)IλI_V=V\left(\lambda\right)\frac{\mathcal{I}_{\lambda}}{\mathcal{I}_{555\text{nm}}=\frac1{683}\mathrm{W/sr}}\mathrm{cd}=\left(683\mathrm{cd}\cdot\mathrm{sr/W}\right)\times V\left(\lambda\right)\mathcal{I}_{\lambda} \\

其中 cd⋅sr\mathrm{cd}\cdot\mathrm{sr} 正是 **luminous flux** 的单位，即 lumen(lm)\text{lumen(lm)} 。

上一小节的 σm\sigma_\mathrm{m} 可记为：

σm=683cd⋅sr/W=683lm/W\sigma_\mathrm{m}=683\mathrm{cd}\cdot\mathrm{sr/W}=683\mathrm{lm/W} \\

---

与 **luminous flux** 的推导类似，对于一般光源，只要知道了它的 **radiant intensity** 对于 **wavelength** λ\lambda 的密度函数（**分布函数**） Iλ\mathcal{I}_{\lambda} ，则可以计算其总 **luminous intensity** IVI_V :

IV=∫0∞σmV(λ)I(λ)dλI_\mathrm{V}=\int_0^\infty\sigma_\mathrm{m}V\left(\lambda\right)\mathcal{I}\left(\lambda\right)\mathrm{d}\lambda \\

---

### Illuminance

在 **photometry** 中，与 **irradiance (辐射照度)** 相对应的概念为 **illuminance (光照度)**。

其推导与 **irradiance** 十分相似，可见前文，易得出单位为 lux(lx)\text{lux(lx)} 的 **illuminance** EVE_V :

EV=dΦVdA=IVcos⁡θr2E_\mathrm{V}=\frac{\mathrm{d}\Phi_\mathrm{V}}{\mathrm{d}A} =\frac{I_\text{V}\cos\theta}{r^2}\\

---

### Luminance

在 **photometry** 中，与 **luminance (光亮度)** 相对应的概念为 **radiance (辐亮度)**。

**Luminance** 描述了对于人眼来说，某光源 **spectral radiance (辐亮度分布)** 的 **brightness (明亮程度)。**

需要注意的是，**luminance** 也仅仅是 **radiometry** 的 **radiance** 转为真正人眼感知到的 **brightness** 的**中间量** (这也是 **Photometry** 为什么说是 **Radiometry** 和 **Colorimetry** 桥梁的部分原因)，下文会介绍 **luminance** 与 **lightness** 的**非线性**关系。

将单位为 nit\text{nit} 的 **luminance** 与 **spectral radiance** L(λ)L(\lambda) 联系起来：

LV=∫λσmL(λ)V(λ)dλL_V=\int_\lambda \sigma_\mathrm{m}L(\lambda)V(\lambda)\mathrm{d}\lambda \\

其推导与 **radiance** 十分相似，可见前文：

LV≜dIVdA⊥=dIVdAcos⁡θ=d2ΦVdΩdAcos⁡θL_\mathrm{V}\triangleq\frac{\mathrm{d}I_\mathrm{V}}{\mathrm{d}A^\perp}=\frac{\mathrm{d}I_\mathrm{V}}{\mathrm{d}A\cos\theta}=\frac{\mathrm{d}^2\Phi_\mathrm{V}}{\mathrm{d}\Omega\mathrm{d}A\cos\theta} \\

---

## Colorimetry

经过前两节对于 **Radiometry** 和 **Photometry** 的介绍，我们知道了 **light wave** 如何产生发射以及携带能量，并简单了解了 **electromagnetic transverse wave** 携带的 **radiometric quantity** 如何转化得到 **human visual system** 所感知的 **photometric quantity** 。

基于以上，本节将详细介绍 **spectral power distribution (光谱分布)** 在 **colorimetry** 中如何对人眼进行刺激并被感知，这种刺激可以被转换为线性 **color space (颜色空间)** 中的一组 **tristimulus value(刺激值)** 对屏幕的 **primary colors (基色)** 进行调节，最终屏幕上各个 **pixel** 发出的混合 **color light (色光)** 被人眼所感知。

---

### Spectral Data

以下内容引用于 [Spectral Power Distribution](https://link.zhihu.com/?target=https%3A//www.scratchapixel.com/lessons/digital-imaging/colors/introduction.html) 。

不论是 **spectral sensitivity** 、**spectral radiance** 、**spectral power** 、**spectral reflective** 等等，对于某个 类型的 **quantity** 在给定 **wavelength (frequency) range** 上的**数据集合**都可以被称为 **spectral data (spectral distribution)** 。

---

每个**光源**通常以其在 **visible spectrum** 中每个 **wavelength** 产生的 **color** 的 **range 和 intensity** 进行区分，这种类似于 **light signature** 的表现形式被称为 **spectral power distribution (光谱功率分布)** 。

**SPD** 原本描述了 **per unit wavelength** 、**per unit area** 的 **power (flux)**，简单来说就是不同 **wavelength** 的 **irradiance (radiant exitance)分布**的**集合**。

但更一般地说，**SPD** 可以指不同 **wavelength** 对于任意 **radiometric quantity** 或 **photometric quantity** 的分布(密度)函数，在下文中我们提到的 **SPD** 一般针对于 **spectral intensity** 或者是 **spectral radiance** 。

**SPD** 通常彼此之间差别很大，它被表示为一条**曲线**，该曲线表示每个特定光源从 **visible spectrum** 中发出每种**light color** 的 **intensity**。以下分别是 **incandescent light bulb (白炽灯泡)** 和 **fluorescent light bulb (荧光灯炮)** 对应的 **SPD**：

![](images/v2-70bba3eca44249b58a1d9b9d7ef186a7_720w.webp)

incandescent light bulb

![](images/v2-3820356fd635bed5b1403149d71fc911_720w.webp)

fluorescent light bulb

---

由于**太阳光**的 **SPD** 会因许多因素(包括污染)而变化，国际照明委员会定义了一个称为 **D65** 的标准，以代表在平均条件下的太阳平均 **SPD** :它对应的是西欧/北欧某个地方 **midday (正午)** 太阳的 **SPD** 。

**D65** 也被称为日光光源，它不是我们可以用光源精确重现的 **SPD** ，而是我们可以比较**现有** **SPD** 的**参考**：

![](images/v2-5ac903abddca686046fbbb6fb434a9fe_720w.webp)

comparison of the D65 SPD with the spectrum of the sun on a typical midday

---

类似于 **SPD** 的 **spectral data** 概念不仅限于**光源**，也可以用来定义**物体颜色**，但是对于物体，我们说的 **spectral reflectance curve** 。

在**光源**的情况下，**SPD** 定义**光源本身**的颜色及其强度。由于**物体**本身**不发光**，它们只**反射**光源**产生的光**，**spectral reflectance curve** 仅代表物体表面反射的光占照亮物体的**白光量**的**百分比**。

下图给出了黄油、番茄和生菜三种物体的 **spectral reflectance curve** ：

![](images/v2-a389d3f494ec7cba3e34bbbf25fa425e_720w.webp)

spectral reflectance curve of butter (A), tomato (B) and lettuce (C)

---

### Color Perception

以下内容引用于 [human eyes](https://link.zhihu.com/?target=https%3A//www.scratchapixel.com/lessons/digital-imaging/colors/introduction.html) 与 [human color perception](https://link.zhihu.com/?target=https%3A//www.cambridgeincolour.com/tutorials/color-perception.htm) 。

首先我们需要知道 **human vision system (人体视觉系统)** 如何对 **light stimuli (光刺激)** 作出反应。

眼睛的后部，**retina (视网膜)**，覆盖着光敏感受器，我们称之为**感光细胞：**

- **Cone cells** - 负责人体的 **color vision (彩色视觉)**，或者说是 **trichromatic vision (三色视觉)**，主要集中在 **retina** 的中心。
- **Rod cells** - 位于 **retina** 外缘，对**少量**的光**更敏感**。

![](images/v2-38df481e88ddfcff912fdf318a508324_720w.webp)

cone cells / rod cells

**Rod cells** 的作用在于，我们仅需要很少的光线就能辨别物体**形状**。在**弱光**条件下，**rod cells** 虽然对光敏感，但不能很好地感知颜色，并且负责 **color vision** 的 **cone cells** 没有足够的光来实现刺激。

**Cone cells** 有**三种类型**，每种类型对 **visible spectrum** 特定范围的 **wavelength** 非常敏感，具体可分为 **short (S)**, **medium (M)**, 和 **long (L) wavelength** (这里的**长短**概念针对的是 **cells** 可以受到刺激的特定**范围的大小**)。

下图说明了不同类型的 **cone cells** 对于整个 **visible spectrum** 特定范围的 **relative sensitivity**，这三条曲线被称为 **tristimulus functions (三色刺激方程)**：

![](images/v2-95f8cf34665a2dfb0aec1bcdcc1c2153_720w.webp)

tristimulus functions

---

### **Grassmann Laws**

以下引用于 [hue saturation luminance](https://link.zhihu.com/?target=https%3A//www.cambridgeincolour.com/tutorials/color-perception.htm) 。

介绍 **Grassmann Laws** 之前，我们首先介绍人眼能且仅能**感知颜色**的**三种特征**：**Hue (色相)** 、**Saturation (饱和度)** 、**Luminance (光亮度)**。

颜色的 **hue** 和 **saturation** 是两个独特的组成部分，使得它与 **achromatic light (无色光)** 区分开来 。从人眼视觉上描述一种颜色可能是非常**主观**的，但是每一个颜色都可以通过检查 **color spectrum** 来更**客观**地说明。

自然产生的颜色不只是一个 **wavelength** 的光，实际上包含了整个 **wavelength range**，通常可以由 **spectrum** 来表示。

---

**Hue** 描述了在当前 **color** 对应的 **wavelength range** 中，哪一个 **wavelength** 占据**主导**。

如下图所示，虽然 **wavelength range** 几乎包围了整个 **spectrum**，但以下物体的很可能被认为是 **bluish** 的：

![](images/v2-41051db916846ec516d1247712f59dfd_720w.webp)

bluish blue

如果在 **red** 和 **green** 的区域各自出现**波峰**，那么整个物体会显示成 **yellow (additive model)** 。

---

**Saturation** 是 **color** 的 **purity (纯度)**。一个 **high saturation** 的颜色将包含一个非常**窄**的波长集合，看起来比一个相似的，但 **low saturation** 的颜色更**明显**：

![](images/v2-6ed85f956f270e949abf25010dcc418b_720w.webp)

high saturation

![](images/v2-2cea542817f3ccee00743b1c0758b7d1_720w.webp)

low saturation

---

**Luminance** 作为一个 **Photometry** 的概念，在上文已经进行介绍。

但一般在 Colorimetry 中，一般会使用另外两个概念：

- **Brightness (亮度)** - 用于 **HSB (Hue 、Saturation 、Brightness)** 空间，又称 **Value** 。
- **Lightness (明度)** - 用于 **HSL (Hue 、Saturation 、Lightness)** 空间 。

以下引用于 [luminosity function](https://link.zhihu.com/?target=https%3A//www.cambridgeincolour.com/tutorials/color-perception.htm) 和 [brightness adaptation](https://link.zhihu.com/?target=https%3A//www.scratchapixel.com/lessons/digital-imaging/digital-images/display-image-to-screen.html) 。

如果需要表示**人眼**感知的**颜色亮度 brightness** ，可以使用 **lightness (明度)** 、**tone (色调)** 等颜色术语。

注意，由于人眼感知的颜色亮度是**主观**的，需要与 **photometry** 的 **luminance** 区分开来 **(客观物理量)** 。

某些颜色对于人眼会**更亮**，尤其是 **555 ~ 560 nm (介于黄绿之间)** 范围的颜色，描述人眼对不同波长光的平均 **visual sensitivity (视觉敏感度)**的函数称为 **luminosity function** ：

![](images/v2-bedb0f521dc217837877d5b2c0cfd382_720w.webp)

luminosity function

在 **scotopic vision (弱光)** 和 **photopic vision (明光)** 环境下的 **luminosity function** 不同。

在 **scotopic vision** 环境下，**rod cells** 对于 **500 nm** 左右的波长光更为敏感，对于长于 **640 nm** 左右的波长光则更不敏感。

---

除了**表示**人眼能感受到的 **brightness** 以外，我们还需要注意人眼对于 **brightness** 的**适应方式**。

类似于蜡烛这样的 **scotopic vision** 环境就足以让人眼分辨物体，自然处于室外的 **photopic vision** 同样可以，这意味着人眼可以适应相当大的 **light intensity range** 。

然而，在阳光之下点燃蜡烛发出的光很难对人眼**已经**感知到的 **brightness** 产生变化，这是因为人眼处理大范围照明水平的方式是通过改变其对场景**整体** **brightness** 的 **sensitivity**，这种快速适应 **light intensity levels** 的能力被称为 **brightness adaptation (亮度适应)** 。

这种对 **light intensity levels** 的**响应**是**非线性**的，即，与场景的明亮部分相比，人眼感知**较暗**的区域比实际亮得多(由于 **rod cells** 的存在，**human vision** 对黑暗区域的小亮度变化比明亮区域**更敏感**)。

比如，一个光源的 **luminance** 只有 **reference luminance** 的 **18%**，但其 **brightness** 大约是 **reference brightness** 的 **50%**。

这种 **human vision (人体主观视觉)** 对于 **luminance (物理客观亮度)** 的 **non-linear perceptual response (非线性响应)** 被称为 **lightness (明度)**：

lightness=luminance13lightness=luminance^{\frac13} \\

---

以下内容引用于 [Color Vision and Colorimetry: Theory and Applications](https://link.zhihu.com/?target=https%3A//spie.org/publications/pm105_31_grassmann_laws%3FSSO%3D1) 。

**Grassmann** 通过 **additional colors** 和 **color-matching** 等实验得出如下 **Grassmann laws**:

1. 人眼能且仅能感知颜色的**三种特征**，即：**Hue (色相)** 、**Saturation (饱和度)** 、**Luminance (光亮度)**。
2. 每种颜色都有 **complementary color (互补色)**，互补色混合后就变成 **colorless gray (无色的灰色)**。
3. 相同 **hue** 和 **saturation** 的两种**不同颜色**的光在混合时，产生具有**相同 hue** 和 **saturation** 的另一种颜色，而不依赖于它们的 **SPD (luminance/radiance)** 。
4. **Abney Law - 混合光**的 **luminance** 等于每个**色光** **luminance** 之**和**。

---

接下来我们用以上定义证明 **color** 三个特征的**线性叠加**性质，用于为下文 **color-matching** 的定义做准备。

以下证明引用于 [三基色理论、格拉斯曼定律与颜色空间的数学结构](https://zhuanlan.zhihu.com/p/599296976) 。

---

我们使用 **color** [C][\mathrm C] 表示 **color light** C\mathrm C 对于人眼的 **stimuli** (即 **color light** 对应的 **color**) 。

若人眼观测 **color light** C1\mathrm C_1 和 **color light** C2\mathrm C_2 相同，意味着 ～C1～C2\mathrm C_1～\mathrm C_2 ( C1\mathrm C_1 **match** C2\mathrm C_2 ) 。

但 **color light** 本身包含了 **color (colorimetry)** 以及 **SPD (radiometry / photometry)** 等多个特征。

因此若将所有 **color** 构成的集合记为 C\mathscr{C} ，并将所有 **color light** 构成的集合即为 L\mathscr{L} ，那么 C\mathscr{C} 可以看作是 L\mathscr{L} 关于等价关系 ∼\sim 的**商集** (即，**color** [C]\left[ \mathrm{C} \right] 可以看作是 **color light** C\mathrm{C} 关于 ∼\sim 的**等价类**) ：C=L/∼\mathscr{C}=\mathscr{L}/\sim \\

由 **Grassmann laws 1** 可知，描述 **color** [C][\mathrm C] 仅需要 **hue** 、**saturation** 、**luminance**，且 **luminance** 被 **photometry** 严格定义，因此仅需要再定义 **hue** 和 **saturation** 即可。

若我们定义 [C][\mathrm C] 的 **luminance** 为 LV([C])L_\mathrm{V}\left([\mathrm{C}]\right) ，那么根据 **Abney Law** 可得出 **luminance** 的**线性叠加性**：

LV([C1]+[C2])=LV([C1])+LV([C2])L_\mathrm{V}\left([\mathrm{C}_1]+[\mathrm{C}_2]\right)=L_\mathrm{V}\left([\mathrm{C}_1]\right)+L_\mathrm{V}\left([\mathrm{C}_2]\right) \\

对于 **color light** C\mathrm C ，假设其 **SPD (spectral radiance distribution)** 为 P(λ)P\left(\lambda\right) ，定义 αC\alpha \mathrm C ：将 C\mathrm C 的 **SPD** 放大为 αP(λ)\alpha P\left(\lambda\right) (在 **radiometry** 中，将多个 **monochromatic light wave** 的 **amplitude** 放大 α\sqrt{\alpha} 倍，注意和 **SPD** 里的 **amplitude** 是两回事) 但不改变其它特征得到的 **color light** 。

由于 αC\alpha \mathrm C 各个 **component color light** 相对比例并**没有发生变化**，只是 **radiance** 变为了 CC 的 α\alpha 倍，根据 **photometry** 中 **luminance** 的定义， αC\alpha \mathrm C 的 **luminance** 同样变为了 C\mathrm C 的 α\alpha 倍，但 αC\alpha \mathrm C 和 C\mathrm C 具有相同的 **hue** 和 **saturation**，根据 **Grassmann laws 3** 可得 [C][\mathrm C] 的**数乘**关系：

α[C]≜[αC],α≥0\alpha\left[\mathrm{C}\right]\triangleq\left[\alpha\mathrm{C}\right],\alpha\geq0 \\并以此得出后续**线性**关系：α([C1]+[C2])=α[C1]+α[C2],∀[C1],[C2]∈C,∀α∈R(α+β)[C]=α[C]+β[C],∀[C]∈C,∀α,β∈R\begin{aligned}\alpha\left(\left[\mathrm{C}_1\right]+\left[\mathrm{C}_2\right]\right)&=\alpha\left[\mathrm{C}_1\right]+\alpha\left[\mathrm{C}_2\right],\forall\left[\mathrm{C}_1\right],\left[\mathrm{C}_2\right]\in\mathscr{C},\forall\alpha\in\mathbb{R}\\\\\left(\alpha+\beta\right)\left[\mathrm{C}\right]&=\alpha\left[\mathrm{C}\right]+\beta\left[\mathrm{C}\right],\forall\left[\mathrm{C}\right]\in\mathscr{C},\forall\alpha,\beta\in\mathbb{R}\end{aligned} \\

---

### Primary Colors

以下内容引用于 [additive and subtractive color mixing](https://link.zhihu.com/?target=https%3A//www.cambridgeincolour.com/tutorials/color-perception.htm) 。

实际上，我们所有 **visible colors (可见颜色)**都可以通过使用 **three primary colors (三基色)**的某种组合，通过 **additive** 或 **subtractive** 的方法产生：

- **Additive processes** - 通过在**黑暗**的背景上**添加光**来创建颜色。对应光源**直接**射入人眼。
- **Subtractive processes** - 使用**颜料**或**染料**选择性地**阻挡白光**。对应光源经过物体**反射(吸收)**射入人眼。

![](images/v2-20b0cfcf17b8e3fd53de943ca901b705_720w.webp)

Additive processes / Subtractive processes

上图中三个圆圈**外缘**的颜色被称为 **primary colors**，在左右两张图中不同：

- **显示器**一类的设备通过释放 **primary colors** 产生 **additive colors**，这就是为什么几乎所有的显示器都使用**red 、green 、blue** 组合的 **pixel** 。
- **打印机**一类设备使用**颜料**或**染料**来吸收光线并产生 **subtractive colors**，这也是为什么大多数彩色打印机至少使用 **cyan 、magenta 、yellow** 的 **ink** 。

![](images/v2-93af5a12cc9b3a63efede6cb64c0607f_720w.webp)

Additive / Subtractive Color Mixing

---

### Color Matching

以下内容引用于 [Color vision and colorimetry : theory and applications](https://link.zhihu.com/?target=http%3A//library.lol/main/DE9A15D97C096113BB9494C263B368E9) 。

在 **interactive rates** 的情形下使用完整的 **SPD** 进行渲染非常笨拙且不切实际，因此在计算机图形学的实时渲染领域，研究该类问题的框架一般建立在寻找良好的 **basis functions** 来表示 **SPD** 的基础之上，尤其是以 **XYZ** 三元组和 **RGB** 三元组作为 **coefficients** 来表示完整 **SPD**。

---

任何一种 **color light (色光)** 总可以看作由不同 **wavelength** 的 **monochromatic color light (单色光)** 乘上各自 **coefficients** 混合得到 (类似于**傅里叶变换**中，通过基函数乘以投影系数复原得到原函数的思想)。

将上述傅里叶变换思想与 **primary colors theory (三基色理论)** 相结合，如果可以证明三种 **primary colors light** **(基色光)** 可以混合产生所有人眼可见的 **monochromatic color light** ，相当于证明三种 **primary colors light** 可以混合产生**所有 color lights (色光)** 。

因此，为了证明三种 **primary colors light** 可以混合产生所有人眼可见的 **monochromatic color light**，研究者们进行了多次 **color-matching experiments (颜色匹配实验)**：

- **Wright** 以 **650 nm** 的红光、**530 nm** 的绿光和 **460 nm** 的蓝光作为三基色进行了颜色匹配实验。
- **Guild** 以 **630 nm** 的红光、**540 nm** 的绿光和 **460 nm** 的蓝光作为三基色再次进行了颜色匹配实验。

具体实验过程如下：

![](images/v2-ecc15028d22d5de8144eda8ef8cf5dab_720w.webp)

color matching experiment

- 一块光屏被隔板分割成左右两个区域，右侧被单色光照亮的区域为 **reference field (参考域)**，左侧被三基色混合色光照亮的区域为 **matched field (匹配域)**。
- 整个被照亮的区域在光屏上的面积很小，小到这个区域相对观察者的视角仅为**2°**，这能保证此区域投射的光线大致落在观察者 **retina (视网膜)** 的 **fovea (中央凹)**，这是为了保证实验只有 **cone cells** 参与。
- 实验时，先设定右侧**单色光源**的波长为 λ\lambda，同时调节光源功率使得 **reference field** 的 **radiance** 为 LRefL_\mathrm{Ref} 。
- 接着不断调节左侧**三基色光源**的 **radiance**，直至 **matched field** 的颜色看起来和 **reference field** 的完全一致，记录下此时左侧三基色光源的 **relative radiance** r¯(λ)\bar{r}\left(\lambda\right)、g¯(λ)\bar{g}\left(\lambda\right) 和 b¯(λ)\bar{b}\left(\lambda\right) 。
- 对不同的 λ\lambda 不断重复上述实验过程，就可以得到 r¯(λ)\bar{r}\left(\lambda\right)、g¯(λ)\bar{g}\left(\lambda\right) 和 b¯(λ)\bar{b}\left(\lambda\right) 的函数图像，对应的函数被称为 **color-matching function (颜色匹配函数)**。

由于 **Wright** 和 **Guild** 所采用的三基色不统一，实验数据无法直接对比，**CIE** 提议以 **700 nm** 的红光、**546.1 nm** 的绿光和 **435.8 nm** 的蓝光作为三基色，并导出了由 **Wright** 和 **Guild** 的数据给出的 **color-matching function：**

![](images/v2-64fd751fad39bd7fb71e807b4c578e3e_720w.webp)

Color-matching functions (CIE 1931)

---

以下引用于 [色度图、普朗克轨迹与色温](https://zhuanlan.zhihu.com/p/601350463) 。

接下来，我们从以下两个角度出发，分析 **color-matching function** 的含义:

- 保持三基色光源 **hue 、saturation** 不变，调节 **relative radiance** 匹配 **luminance** 。
- 保持三基色光源 **radiance** 不变，调节线性颜色空间 **hue 、saturation** 的**展开系数**匹配 **luminance。**

---

在 **color-matching experiment** 过程中：

- **reference field** 的**参考光源**的 **radiance** 保持不变，通过调整 **wavelength** 来调整 **luminance** 。
- **matched field** 的**三基色光源**调整 **radiance**，使得左右两边的 **hue** 、**saturation** 、**luminance** 一致。

右侧 **reference field** 内**参考光源**的 **radiance** 始终保持为 LRefL_{\mathrm{Ref}} (不论当前 **monochromatic light** 的 **wavelength** 是多少，始终为**常量**)。那么，假设此时右侧光源的 **wavelength** 为 λ\lambda ，则根据上文 **photometry** 介绍的视见函数 V(λ)V(\lambda) 得出 **luminance**。

即， LV,Ref(λ)L_{\mathrm{V,Ref}}\left(\lambda\right) 表明右侧光源 **radiance** 不变，**luminance** 随着 λ\lambda 的变化而变化：

LV,Ref(λ)=σmV(λ)LRefL_{\mathrm{V,Ref}}\left(\lambda\right)=\sigma_{\mathrm{m}}V\left(\lambda\right)L_{\mathrm{Ref}}\\通过调节**三基色光源**的 **radiance**，使得 **matched field** 内叠加后 **color light** 的特征 **(hue 、saturation 、luminance)** 和 **reference field** 内**参考光源**的完全一致，记此时**三基色光源**的 **radiance** 分别为 LR(λ)L_{\mathrm{R}}\left(\lambda\right) 、LG(λ)L_{\mathrm{G}}\left(\lambda\right) 和 LB(λ)L_{\mathrm{B}}\left(\lambda\right)。那么，三基色光源的 **luminance** 分别为：

LV,R(λ)=σmVRLR(λ)LV,G(λ)=σmVGLG(λ)LV,B(λ)=σmVBLB(λ)\begin{aligned}L_\mathrm{V,R}\left(\lambda\right)&=\sigma_\mathrm{m}V_\mathrm{R}L_\mathrm{R}\left(\lambda\right)\\L_\mathrm{V,G}\left(\lambda\right)&=\sigma_\mathrm{m}V_\mathrm{G}L_\mathrm{G}\left(\lambda\right)\\L_\mathrm{V,B}\left(\lambda\right)&=\sigma_\mathrm{m}V_\mathrm{B}L_\mathrm{B}\left(\lambda\right)\end{aligned} \\根据 **Abney Law**，易得 **matched field** 内的 **luminance** 为 ：

LV,R(λ)+LV,G(λ)+LV,B(λ)L_{\mathrm{V,R}}\left(\lambda\right)+L_{\mathrm{V,G}}\left(\lambda\right)+L_{\mathrm{V,B}}\left(\lambda\right) \\

当 **matched field** 与 **reference field** 匹配后，两边 **luminance** 相等：

LV,Ref(λ)=LV,R(λ)+LV,G(λ)+LV,B(λ)L_{\mathrm{V,Ref}}\left(\lambda\right)=L_{\mathrm{V,R}}\left(\lambda\right)+L_{\mathrm{V,G}}\left(\lambda\right)+L_{\mathrm{V,B}}\left(\lambda\right) \\ 此时，定义 **color-matching function** 为：

r¯(λ)=kR−1LR(λ)LRef,g¯(λ)=kG−1LG(λ)LRef,b¯(λ)=kB−1LB(λ)LRef.\bar{r}\left(\lambda\right)=k_{\mathrm{R}}^{-1}\frac{L_{\mathrm{R}}\left(\lambda\right)}{L_{\mathrm{Ref}}},\quad\bar{g}\left(\lambda\right)=k_{\mathrm{G}}^{-1}\frac{L_{\mathrm{G}}\left(\lambda\right)}{L_{\mathrm{Ref}}},\quad\bar{b}\left(\lambda\right)=k_{\mathrm{B}}^{-1}\frac{L_{\mathrm{B}}\left(\lambda\right)}{L_{\mathrm{Ref}}}. \\忽略**常量系数**，可以看到 r¯(λ) \bar{r}\left(\lambda\right)\ 正是**三基色光源** **radiance** 比上**参考光源** **radiance** 的**比值**，即 **relative radiance (相对辐亮度)**。

注意，当且仅当 **luminance** 匹配成功时，**hue 、saturation** 才匹配成功。而当前的三个基色光源为**单色光**，根据上文定义，单色光的 **hue 、saturation** 是不会随着 **radiance** 的调节而发生改变的 (单色光 **color spectrum** **主导**的波峰和**纯度**都不会改变)，但是分别乘上**调节系数**得到的混合结果则正好与 **reference light** 内的 **hue 、saturation** 完全一致，这暗示了 **color-matching** 的另一层含义。

---

假设 **CIE 1931** 的**三基色光源**的**三基色**为 [R]\left[\mathrm{R}\right]、[G]\left[\mathrm{G}\right]、[B]\left[\mathrm{B}\right]：

- [R]\left[\mathrm{R}\right] 代表 **700 nm** 的红色。
- [G]\left[\mathrm{G}\right] 代表 **546.1 nm** 的绿色。
- [B]\left[\mathrm{B}\right] 代表 **435.8 nm** 的蓝色。

这只指定了[R]\left[\mathrm{R}\right]、[G]\left[\mathrm{G}\right]、[B]\left[\mathrm{B}\right]三种颜色的 **hue** 和 **saturation**，而并没有指定它们的 **luminance** 。

我们现在指定它们的 **luminance** 为：

LV([R])=σmkRVRLRefLV([G])=σmkGVGLRefLV([B])=σmkBVBLRef\begin{aligned}L_\mathrm{V}\left([\mathrm{R}]\right)&=\sigma_\mathrm{m}k_\mathrm{R}V_\mathrm{R}L_\mathrm{Ref}\\L_\mathrm{V}\left([\mathrm{G}]\right)&=\sigma_\mathrm{m}k_\mathrm{G}V_\mathrm{G}L_\mathrm{Ref}\\L_\mathrm{V}\left([\mathrm{B}]\right)&=\sigma_\mathrm{m}k_\mathrm{B}V_\mathrm{B}L_\mathrm{Ref}\end{aligned} \\需要注意的是，与 **color-matching experiment** 不同，三种颜色的 **radiance** 并不是三个**基色光**各自的 LR(λ)L_{\mathrm{R}}\left(\lambda\right) 、LG(λ)L_{\mathrm{G}}\left(\lambda\right) 和 LB(λ)L_{\mathrm{B}}\left(\lambda\right) ，而是统一定义为了 LRefL_\mathrm{Ref} 。

这是为了消除由于 **radiance 不同**而对三基色**混合**生成颜色的 **hue** 和 **saturation** 产生的影响。

将 **wavelength** 为 λ\lambda 、**radiance** 为 LRefL_{\mathrm{Ref}} 的 **monochromatic light** 的 **color** 记为 [M(λ)]\left[\mathrm{M\left(\lambda\right)}\right] ，那么 [M(λ)]\left[\mathrm{M\left(\lambda\right)}\right] 可以在由**基向量** [R]\left[\mathrm{R}\right]、[G]\left[\mathrm{G}\right]、[B]\left[\mathrm{B}\right] 构成的线性 **chromaticity space (色彩空间)** C\mathscr{C} 中展开 (**color space** 的展开等式应同时满足 **hue 、saturation 、luminance** 均相等，而此处的 **chromaticity space** 仅考虑 **hue 、saturation**) 。

**Maxwell** 使用一个**等边三角形**来表示这个线性 **chromaticity space** C\mathscr{C} ，在他的 **trichromatic theory (三色理论)** 中，三个 **primary colors** RR GG BB 分别位于三角形的三个**顶点**，任何其他 **color** 都是由对应点到三角形每条边的**距离** rr gg bb 来表示 。

![](images/v2-941c2f090ba2743a592de79c6d4eb115_720w.webp)

Maxwell triangle

那么 [M(λ)]\left[\mathrm{M\left(\lambda\right)}\right] 可以表示为：

[M(λ)]=R(λ)[R]+G(λ)[G]+B(λ)[B]\left[\mathrm M\left(\lambda\right)\right]=R\left(\lambda\right)\left[\mathrm R\right]+G\left(\lambda\right)\left[\mathrm G\right]+B\left(\lambda\right)\left[\mathrm B\right] \\其中 R(λ)R\left(\lambda\right) 、G(λ)G\left(\lambda\right) 和 B(λ)B\left(\lambda\right) 为**展开系数**，混合产生 [M(λ)]\left[\mathrm{M\left(\lambda\right)}\right] 的**三基色**的 **luminance** 分别为 ：

LV(R(λ)[R])=R(λ)LV([R])=R(λ)σmkRVRLRefLV(G(λ)[G])=G(λ)LV([G])=G(λ)σmkGVGLRefLV(B(λ)[B])=B(λ)LV([B])=B(λ)σmkBVBLRef\begin{aligned}L_\mathrm{V}\left(R\left(\lambda\right)\left[\mathrm{R}\right]\right)&=R\left(\lambda\right)L_\mathrm{V}\left(\left[\mathrm{R}\right]\right)=R\left(\lambda\right)\sigma_\mathrm{m}k_\mathrm{R}V_\mathrm{R}L_\mathrm{Ref}\\L_\mathrm{V}\left(G\left(\lambda\right)\left[\mathrm{G}\right]\right)&=G\left(\lambda\right)L_\mathrm{V}\left(\left[\mathrm{G}\right]\right)=G\left(\lambda\right)\sigma_\mathrm{m}k_\mathrm{G}V_\mathrm{G}L_\mathrm{Ref}\\L_\mathrm{V}\left(B\left(\lambda\right)\left[\mathrm{B}\right]\right)&=B\left(\lambda\right)L_\mathrm{V}\left(\left[\mathrm{B}\right]\right)=B\left(\lambda\right)\sigma_\mathrm{m}k_\mathrm{B}V_\mathrm{B}L_\mathrm{Ref}\end{aligned} \\

上一部分，我们已知**三基色光源**的 **luminance** 为：

LV,R(λ)=σmVRLR(λ)LV,G(λ)=σmVGLG(λ)LV,B(λ)=σmVBLB(λ)\begin{aligned}L_\mathrm{V,R}\left(\lambda\right)&=\sigma_\mathrm{m}V_\mathrm{R}L_\mathrm{R}\left(\lambda\right)\\L_\mathrm{V,G}\left(\lambda\right)&=\sigma_\mathrm{m}V_\mathrm{G}L_\mathrm{G}\left(\lambda\right)\\L_\mathrm{V,B}\left(\lambda\right)&=\sigma_\mathrm{m}V_\mathrm{B}L_\mathrm{B}\left(\lambda\right)\end{aligned} \\

联立可求得 R(λ)R\left(\lambda\right) 、G(λ)G\left(\lambda\right) 和 B(λ)B\left(\lambda\right) 为：

R(λ)=kR−1LR(λ)LRef,G(λ)=kG−1LG(λ)LRef,B(λ)=kB−1LB(λ)LRefR\left(\lambda\right)=k_{\mathrm{R}}^{-1}\frac{L_{\mathrm{R}}\left(\lambda\right)}{L_{\mathrm{Ref}}},\quad G\left(\lambda\right)=k_{\mathrm{G}}^{-1}\frac{L_{\mathrm{G}}\left(\lambda\right)}{L_{\mathrm{Ref}}},\quad B\left(\lambda\right)=k_{\mathrm{B}}^{-1}\frac{L_{\mathrm{B}}\left(\lambda\right)}{L_{\mathrm{Ref}}} \\易得**三基色光源 radiance** 比上**参考光源 radiance** 的 **relative radiance** r¯(λ)g¯(λ)b¯(λ)\bar{r}\left(\lambda\right)\bar{g}\left(\lambda\right)\bar{b}\left(\lambda\right) ，等于**参考光源 color** [M(λ)]\left[\mathrm{M\left(\lambda\right)}\right] 在由 [R]\left[\mathrm{R}\right]、[G]\left[\mathrm{G}\right]、[B]\left[\mathrm{B}\right] **三基色**组成的**颜色空间**展开后对应的**展开系数** R(λ)R\left(\lambda\right) G(λ)G\left(\lambda\right) B(λ)B\left(\lambda\right) ：

R(λ)=r¯(λ),G(λ)=g¯(λ),B(λ)=b¯(λ)R\left(\lambda\right)=\bar{r}\left(\lambda\right),\quad G\left(\lambda\right)=\bar{g}\left(\lambda\right),\quad B\left(\lambda\right)=\bar{b}\left(\lambda\right) \\

---

至此，让我们对比一下上文 **Color Perception** 提到的，不同类型的 **cone cells** 对于整个 **visible spectrum** 特定范围的 **relative sensitivity**，即 **tristimulus functions** ：

![](images/v2-0cf264e6a46157399589d03ac38aa149_720w.webp)

tristimulus functions

再对比一下 **color-matching function** ：

![](images/v2-308fce2d664355f5ee6d5db541d4fcd0_720w.webp)

color-matching function r g b

可以看到，**color-matching function (颜色匹配方程)** 是对 **tristimulus functions (人眼刺激方程)** 的**模拟**。它可以模拟人眼感受颜色的刺激对应的 **tristimulus value (刺激值)**。

即**任意参考光源** **color light** 的 **color** [C][\mathrm C] 都可以由**三基色光源 monochromatic light** 的 **primary colors** [R]\left[\mathrm{R}\right]、[G]\left[\mathrm{G}\right]、[B]\left[\mathrm{B}\right] 乘上 **tristimulus value** RR GG BB 混合后得到，并被人眼所正确感知 ：

[C]=R[R]+G[G]+B[B]\mathrm{[C]}=R\left[\mathrm{R}\right]+G\left[\mathrm{G}\right]+B\left[\mathrm{B}\right] \\

---

对于一般的 **color light** C\mathrm{C} 使用 **spectral radiance** L(λ)L(\lambda) 来计算它的 **tristimulus value 。**

假设 **color light** C\mathrm{C} 由 **wavelength** 为 λ1\lambda_1、λ2\lambda_2 和 λ3\lambda_3 且 **radiance** 为 L1L_1、L2L_2 和 L3L_3 的 **monochromatic light** 混合而成，**color** 可以分别表示为 [M(λ1)]\left[\mathrm{M}\left(\lambda_{1}\right)\right] [M(λ2)]\left[\mathrm{M}\left(\lambda_{2}\right)\right] [M(λ3)]\left[\mathrm{M}\left(\lambda_{3}\right)\right] ，其**展开系数**为 L1LRef\frac{L_{1}}{L_{\mathrm{Ref}}} L2LRef\frac{L_{2}}{L_{\mathrm{Ref}}} L3LRef\frac{L_{3}}{L_{\mathrm{Ref}}} 。

那么 **color light** C\mathrm{C} 的 **color** [C][\mathrm C] 则为：

[C]=L1LRef[M(λ1)]+L2LRef[M(λ2)]+L3LRef[M(λ3)][\mathrm{C}]=\frac{L_{1}}{L_{\mathrm{Ref}}}[\mathrm{M}\left(\lambda_{1}\right)]+\frac{L_{2}}{L_{\mathrm{Ref}}}[\mathrm{M}\left(\lambda_{2}\right)]+\frac{L_{3}}{L_{\mathrm{Ref}}}[\mathrm{M}\left(\lambda_{3}\right)] \\根据上述推导结果：

[M(λ)]=R(λ)[R]+G(λ)[G]+B(λ)[B]\left[\mathrm M\left(\lambda\right)\right]=R\left(\lambda\right)\left[\mathrm R\right]+G\left(\lambda\right)\left[\mathrm G\right]+B\left(\lambda\right)\left[\mathrm B\right] \\R(λ)=r¯(λ),G(λ)=g¯(λ),B(λ)=b¯(λ)R\left(\lambda\right)=\bar{r}\left(\lambda\right),\quad G\left(\lambda\right)=\bar{g}\left(\lambda\right),\quad B\left(\lambda\right)=\bar{b}\left(\lambda\right) \\易得：

[M(λ1)]=r¯(λ1)[R]+g¯(λ1)[G]+b¯(λ1)[B][M(λ2)]=r¯(λ2)[R]+g¯(λ2)[G]+b¯(λ2)[B][M(λ3)]=r¯(λ3)[R]+g¯(λ3)[G]+b¯(λ3)[B]\begin{gathered} \mathrm{[M}\left(\lambda_{1}\right)] =\bar{r}\left(\lambda_1\right)\left[\mathrm{R}\right]+\bar{g}\left(\lambda_1\right)\left[\mathrm{G}\right]+\bar{b}\left(\lambda_1\right)\left[\mathrm{B}\right] \\ \mathrm{[M}\left(\lambda_{2}\right)] =\bar{r}\left(\lambda_2\right)\left[\mathrm{R}\right]+\bar{g}\left(\lambda_2\right)\left[\mathrm{G}\right]+\bar{b}\left(\lambda_2\right)\left[\mathrm{B}\right] \\ \mathrm{[M}\left(\lambda_{3}\right)] =\bar{r}\left(\lambda_3\right)\left[\mathrm{R}\right]+\bar{g}\left(\lambda_3\right)\left[\mathrm{G}\right]+\bar{b}\left(\lambda_3\right)\left[\mathrm{B}\right] \end{gathered} \\由 [C]=R[R]+G[G]+B[B]\mathrm{[C]}=R\left[\mathrm{R}\right]+G\left[\mathrm{G}\right]+B\left[\mathrm{B}\right] ，整理后可得 **color** [C][\mathrm C] 对应的三个 **tristimulus value** RR GG BB 分别为：

R=L1LRefr¯(λ1)+L2LRefr¯(λ2)+L3LRefr¯(λ3)G=L1LRefg¯(λ1)+L2LRefg¯(λ2)+L3LRefg¯(λ3)B=L1LRefb¯(λ1)+L2LRefb¯(λ2)+L3LRefb¯(λ3)\begin{aligned}R&=\frac{L_1}{L_\mathrm{Ref}}\bar{r}\left(\lambda_1\right)+\frac{L_2}{L_\mathrm{Ref}}\bar{r}\left(\lambda_2\right)+\frac{L_3}{L_\mathrm{Ref}}\bar{r}\left(\lambda_3\right)\\G&=\frac{L_1}{L_\mathrm{Ref}}\bar{g}\left(\lambda_1\right)+\frac{L_2}{L_\mathrm{Ref}}\bar{g}\left(\lambda_2\right)+\frac{L_3}{L_\mathrm{Ref}}\bar{g}\left(\lambda_3\right)\\B&=\frac{L_1}{L_\mathrm{Ref}}\bar{b}\left(\lambda_1\right)+\frac{L_2}{L_\mathrm{Ref}}\bar{b}\left(\lambda_2\right)+\frac{L_3}{L_\mathrm{Ref}}\bar{b}\left(\lambda_3\right)\end{aligned} \\推广到 **color light** C\mathrm{C} 由 NN 种 **monochromatic light** 混合的情况，易得 **tristimulus value** 的计算公式：

R=1LRef∑i=1NLir¯(λi),G=1LRef∑i=1NLig¯(λi),G=1LRef∑i=1NLib¯(λi)R=\frac{1}{L_{\mathrm{Ref}}}\sum_{i=1}^{N}L_{i}\bar{r}\left(\lambda_{i}\right),\quad G=\frac{1}{L_{\mathrm{Ref}}}\sum_{i=1}^{N}L_{i}\bar{g}\left(\lambda_{i}\right),\quad G=\frac{1}{L_{\mathrm{Ref}}}\sum_{i=1}^{N}L_{i}\bar{b}\left(\lambda_{i}\right) \\对于连续域的 **spectral radiance** L(λ)L(\lambda) :

R=1LRef∫0∞r¯(λ)L(λ)dλG=1LRef∫0∞g¯(λ)L(λ)dλB=1LRef∫0∞b¯(λ)L(λ)dλ\begin{gathered} \text{R} =\frac1{L_\mathrm{Ref}}\int_0^\infty\bar{r}\left(\lambda\right)L\left(\lambda\right)\mathrm{d}\lambda \\ \text{G} =\frac{1}{L_{\mathrm{Ref}}}\int_{0}^{\infty}\bar{g}\left(\lambda\right)L\left(\lambda\right)\mathrm{d}\lambda \\ \text{B} =\frac1{L_{\mathrm{Ref}}}\int_{0}^{\infty}\bar{b}\left(\lambda\right)L\left(\lambda\right)\mathrm{d}\lambda \end{gathered} \\

对于任意一个 **color light**，无论它是物体**自身发出**的，还是物体**反射/透射**的光，都可以通过物理上的方法得到对应的 **spectral radiance** L(λ)L(\lambda) ，进而计算出它的 **tristimulus value** 。

但是反过来，对于一组 **tristimulus value** ，存在无穷多种不同 **spectral radiance** L(λ)L(\lambda) 的 **color light** 与之对应，这些 **color light** 在 **color-matching experiment** 中的表现完全相同，这被称为 **metamerism (同色异谱)** 现象。

---

### xyY Color Space

以下内容引用于 [RTR4 P272~279](https://link.zhihu.com/?target=https%3A//www.realtimerendering.com/) 、[The xyY Color Space](https://link.zhihu.com/?target=https%3A//www.scratchapixel.com/lessons/digital-imaging/colors/color-space.html) 。

**CIE 1931** 在 **color-matching experiment** 中得到的 **color-matching function** 由于在 r¯(λ)\bar{r}\left(\lambda\right) 和 g¯(λ)\bar{g}\left(\lambda\right) 存在**负数**区域，因此依旧不能直接表示所有的 **visible colors** 。

因此，**CIE** 根据预设原则，将原 **color-matching function** r¯(λ)\bar{r}\left(\lambda\right) g¯(λ)\bar{g}\left(\lambda\right) b¯(λ)\bar{b}\left(\lambda\right) 经过**线性组合**，得到了新的三个假想光源，光源的 **color-matching function** x¯(λ)\bar{x}\left(\lambda\right) y¯(λ)\bar{y}\left(\lambda\right) z¯(λ)\bar{z}\left(\lambda\right) 对所有 **visible wavelength** 均为**正值** ：

![](images/v2-b81f868013c0e566dcbc976678ebbf3e_720w.webp)

color-matching function x y z

其中最重要的两条原则为，将任一人眼可见的颜色 [C]\left[\mathrm{C}\right] 在 [X]\left[\mathrm{X}\right]**、**[Y]\left[\mathrm{Y}\right]**、**[Z]\left[\mathrm{Z}\right] 展开，且 XX**、**YY**、**ZZ 均为**正值**。以及对应基色 [Y]\left[\mathrm{Y}\right] 的刺激值 YY 应该表示颜色 [C]\left[\mathrm{C}\right] 的 **luminance** ：

[C]=X[X]+Y[Y]+Z[Z]\mathrm[C]=X\left[\mathrm X\right]+Y\left[\mathrm Y\right]+Z\left[\mathrm Z\right] \\

类似的，对于任意 **color light** ，通过物理的方法得到其连续域的 **spectral radiance** L(λ)L(\lambda) ，并计算得到对应的 XX**、**YY**、**ZZ **tristimulus value** :

X=1LRef∫0∞x¯(λ)L(λ)dλY=1LRef∫0∞y¯(λ)L(λ)dλZ=1LRef∫0∞z¯(λ)L(λ)dλ\begin{aligned}X&=\frac1{L_{\mathrm{Ref}}}\int_0^\infty\bar{x}\left(\lambda\right)\mathcal{L}\left(\lambda\right)\mathrm{d}\lambda\\Y&=\frac1{L_{\mathrm{Ref}}}\int_0^\infty\bar{y}\left(\lambda\right)\mathcal{L}\left(\lambda\right)\mathrm{d}\lambda\\Z&=\frac1{L_{\mathrm{Ref}}}\int_0^\infty\bar{z}\left(\lambda\right)\mathcal{L}\left(\lambda\right)\mathrm{d}\lambda\end{aligned} \\XX **、**YY**、**ZZ 三个 **tristimulus value** 作为 **CIE XYZ space** 中的**权重值**，定义了具体的 **color** 。

根据上述的两个原则，**CIE XYZ space** 的 **color** 可以很容易被分为 **chromaticity (hue 、saturation)** 和 l**uminance** 两个部分。为此，**CIE** 将 **XYZ space** 的 **color** 投影到 X+Y+Z=1X+Y+Z=1 的平面上，该平面定义了一个 **2D chromaticity space**，并得到了一组新的 xx yy zz **tristimulus value**：

x=XX+Y+Zy=YX+Y+Zz=ZX+Y+Z=1−x−y\begin{aligned} &\begin{aligned}x=\frac{X}{X+Y+Z}\end{aligned} \\ &y=\frac Y{X+Y+Z} \\ &z=\frac{Z}{X+Y+Z}=1-x-y \end{aligned} \\

![](images/v2-237c19839dee15a2280e9a543c7f2585_720w.webp)

XYZ space project to X+Y+Z=1 plane

将投影后的 **CIE 1931 chromaticity diagram** 单独取出，其中 xx 和 yy 作为该图的 **chromaticity coordinates** ，而 zz 没有额外的信息，可以通过前两个坐标计算得出。

下图 **chromaticity diagram** 表示了电视或者电脑屏幕等设备的 **gamut (色域)**，即当前**色彩空间**能够**表示**或**重现**的所有**颜色范围**：

![](images/v2-2d95310b44bb8bdca7cf8b2af1c36b16_720w.webp)

CIE 1932 chromaticity diagram

**CIE chromaticity diagram** 使用xx 和 yy 两个维度解决了人眼感知 **color** 的 **hue 、saturation** 两个特征，而 **color** 另外一个 **luminance** 则通过第三个维度 Y Y 进行表示，这些定义最终定义了 **xyY color space** 。

---

但是，无论是 XX**、**YY**、**ZZ 还是 xx**、**yy**、**YY， 这两组 **tristimulus value** 定义的 **color** 均不能在屏幕上直接展示。

实际上，**CIE XYZ space** 的优势在于它可以表示所有 **human vision** 可以感知到的 **color**，换句话说，其它色彩空间的 **gamut** 一定比 **CIE XYZ space** 的 **gamut** 范围**更小**。

在下一小节中，我们将介绍如何将 **CIE XYZ space** 中的 **XYZ color** 转换为 **CIE RGB space** 中的 **RGB color**。

---

### RGB Color Space

以下内容引用于 [RTR4 P272~279](https://link.zhihu.com/?target=https%3A//www.realtimerendering.com/) 、[RGB Color Space](https://link.zhihu.com/?target=https%3A//www.scratchapixel.com/lessons/digital-imaging/colors/color-space.html) 、[What is White?](https://link.zhihu.com/?target=https%3A//www.scratchapixel.com/lessons/digital-imaging/colors/introduction.html) 、[PBR color space conversion](https://link.zhihu.com/?target=https%3A//www.pbr-book.org/3ed-2018/Color_and_Radiometry/The_SampledSpectrum_Class%23sec%3Acolor-xyz) 。

对于大多数类似于显示器屏幕等设备，其显示原理都是基于 **RGB color model**：每一个屏幕上的 **pixel** 由 **red、green、blue** 三种 **color light** 经过调节后混合组成 (类似于 **color-matching experiment**) 。

**RGB color space** 的思想遵循于 **human vision** 对于 **color** 的感知原则，其中的 **color** 可以表示为 **primary colors (red、green、blue)** 对应三个 **quantities (0~1)** 组成的**三元组**。

整个 **RGB color space** 可以被表示为下图中的 **cube** ，三个顶点表示了三个 **primary colors**：

![](images/v2-d28e111e9eb757967346671f9f0ce85a_720w.webp)

RGB color space

虽然 **RGB color space** 非常直观且容易理解，但是它对应的 **gamut** 比起 **XYZ color space** 来相当受限。

任意 **RGB color space** 的 **gamut** 可以被表示为包含在 **XYZ's gamut** 中的一个**三角形**，与 **Maxwell Triangle** 类似，**gamut** 的三个角对应了三个 **primary colors**，是屏幕可以展示出来的 **most saturated** 的 **red** 、**green** 、**blue** ：

![](images/v2-84e10b6360a334cbf33f176e5224a807_720w.webp)

CIE 1931 chromaticity diagram with sRGB gamut

---

其中特别要注意的是每个 **RGB color space** 的 三角形区域 **gamut** 中间的 **white point**，它表示了当前 **display system** 的 **RGB tristimulus value 相等**时产生的 **chromaticity (hue 、saturation)** 。

与我们之前提到拥有**独立 wavelength** 的 **monochromatic color light** 不同，**white** 出现于我们眼睛的三种对颜色敏感的 **cone cells** 受到**相同数量**的光刺激时，所被感知得到的 **color** 。

由于 **white light** 由 **visible spectrum** 上相同数量的 **color light** 所组成，**CIE** 提供了一个被称为 **E illuminant** 的 **equi-energy SPD** (对于每个 **wavelength internal** 都拥有相同的 **spectral irradiance**) ：

![](images/v2-8103e307e168fa07d11ecf0e3a4f5e1b_720w.webp)

illuminant E

对于 **CIE RGB color space** 来说，**white point** 被视为 **E illuminant** 。

---

在一个 **color space** 中定义的 **color** 转换到另一个 **color space** 可以简单地看作是将3D空间中的一个点从一个位置**移动**到另一个位置，这种**线性转换**让我们自然而然联想到**矩阵乘法**。

**CIE XYZ space** 与 **CIE RGB space** 之间 **color** 的转换通过一个 **3x3** 的矩阵实现，已知 **CIE RGB space gamut** 的三个 **primary colors point** 和 **white point** 在 **CIE XYZ space** 的投影平面 **CIE xyY space** 中的坐标位置：

![](images/v2-79a5e168e173a95fddda9fdb5a6e065e_720w.webp)

RGB gamut vertices and white point - xyY coordinates

易得转换矩阵，在 **PBR** 中实现如下：

```cpp
inline void XYZToRGB(const Float xyz[3], Float rgb[3]) {
    rgb[0] =  3.240479f*xyz[0] - 1.537150f*xyz[1] - 0.498535f*xyz[2];
    rgb[1] = -0.969256f*xyz[0] + 1.875991f*xyz[1] + 0.041556f*xyz[2];
    rgb[2] =  0.055648f*xyz[0] - 0.204043f*xyz[1] + 1.057311f*xyz[2];
}
inline void RGBToXYZ(const Float rgb[3], Float xyz[3]) {
    xyz[0] = 0.412453f*rgb[0] + 0.357580f*rgb[1] + 0.180423f*rgb[2];
    xyz[1] = 0.212671f*rgb[0] + 0.715160f*rgb[1] + 0.072169f*rgb[2];
    xyz[2] = 0.019334f*rgb[0] + 0.119193f*rgb[1] + 0.950227f*rgb[2];
}
```

除此之外，还有一个常见的转换是 **RGB color** 转为 **grayscale luminance value**，由于 **luminance** 本身就是 **XYZ color** 中的 **Y** ，转换自然十分简单：

Y=0.2126R+0.7152G+0.0722BY=0.2126R+0.7152G+0.0722B \\

---

## 总结展望

原定于本文最后一部分关于 **linear color space** 到 **gamma space** 的转换 (包括 **gamma encoding** 以及 **gamma decoding**) 的内容，经过笔者考虑将放在后续 **tone-mapping** 分析中。

主要参考：[Understanding Gamma Correction](https://link.zhihu.com/?target=https%3A//www.cambridgeincolour.com/tutorials/gamma-correction.htm) 、[What and Why is Gamma Correction in Photo Images?](https://link.zhihu.com/?target=https%3A//www.cambridgeincolour.com/tutorials/gamma-correction.htm) 、[Digital Images: from File to Screen](https://link.zhihu.com/?target=https%3A//www.scratchapixel.com/lessons/digital-imaging/digital-images/display-image-to-screen.html) 。

## 参考

1. [^](#ref_1_0)Scratch a Pixel [https://www.scratchapixel.com/lessons/digital-imaging/colors/introduction.html](https://www.scratchapixel.com/lessons/digital-imaging/colors/introduction.html)
2. [^](#ref_2_0)Real-Time Rendering [https://www.realtimerendering.com/](https://www.realtimerendering.com/)
3. [^](#ref_3_0)Cambridge in Colour [https://www.cambridgeincolour.com/color-management-printing.htm](https://www.cambridgeincolour.com/color-management-printing.htm)
4. [^](#ref_4_0)Color vision and colorimetry : theory and applications [http://library.lol/main/DE9A15D97C096113BB9494C263B368E9](http://library.lol/main/DE9A15D97C096113BB9494C263B368E9)
5. [^](#ref_5_0)Color and Radiometry - Physically Based Rendering [https://www.pbr-book.org/3ed-2018/Color_and_Radiometry](https://www.pbr-book.org/3ed-2018/Color_and_Radiometry)
6. [^](#ref_6_0)色彩科学 [https://www.zhihu.com/column/c_1602295156237197312](https://www.zhihu.com/column/c_1602295156237197312)
