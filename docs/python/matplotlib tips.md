---
tags: [matplotlib, python, tips]
---
# matplotlib tips

[Tutorials — Matplotlib 3.9.0 documentation](https://matplotlib.org/stable/tutorials/index.html)

在有些linux环境下，opencv的显示可能不可用，需要使用plt可视化

## `plt`、`fig` 和 `ax`

- `plt` 是 `Matplotlib` 库的一个模块，用于创建、绘制和显示图形。使用 `plt`  可以创建 `fig`和 `ax`. 可以使用它来创建图形、设置图形属性、绘制图形内容等。

- `fig` 是 `Figure` 对象的一个实例，表示整个图形窗口，一个`fig`就是一个`windows`窗口。一个 `Figure` 对象可以包含多个子图（`Axes`），可以通过 `fig` 对象来设置图形窗口的属性，比如大小、标题等。

- `ax` 是 `Axes` 对象的一个实例，表示窗口(`fig`)中的一个子图。可以通过 `ax` 对象来设置子图的属性，绘制图形内容等。

简单来说， plt可以创建多个`窗口` (`Figure`)， 每个窗口里可以含有多个子图(`Axes`)。


``` python

fig = plt.figure()
ax = fig.add_subplot()


# 创建一张空的画布，返回画布实例
fig = plt.figure()  

# 创建只有一个子图的画布，返回一个Axes的子类
# 虽然没有显式地创建 fig 对象，但是在调用 plt.show() 后，Matplotlib 会隐式地创建一个 fig 对象，并将子图显示在这个图形窗口中。
ax = plt.subplot()  

# 将子图添加到指定的图形对象中
ax.set_figure(fig)



# 单个子图的画布，返回当前画布和子图
fig, ax = plt.subplots()  

# 多个子图的画布
fig, axs = plt.subplots(2, 2)  


# 分别为figure和ax设置标题，注意两者的位置是不同的
fig.suptitle('bold figure suptitle', fontsize=14, fontweight='bold')
ax.set_title('axes title')

# 设置x和y轴标签
ax.set_xlabel('xlabel')
ax.set_ylabel('ylabel')

```

函数 `subplots` 的定义是 `def subplots(nrows=1, ncols=1, *, ...)`

所以`plt.subplots(1, n)`  其实是一维数组。


``` python

# 单行子图
# fig, axs = plt.subplots(1, n) 
fig, axs = plt.subplots(n)  # 等价于plt.subplots(n, 1)，和上面方向垂直

# 清除子图
for ax in axs:
    ax.clear()


# 多行子图
fig, axs = plt.subplots(2, n)

for ax_row in axs:
    for ax in ax_row:
        ax.clear()

```

 当`axs `是一个二维的数组，当你使用 `for ax in axs `迭代时，`ax` 实际上是一个数组（`axs` 的一行），所以需要嵌套一层循环来访问单个的` ax`。


### 图像显示

``` python

import matplotlib.pyplot as plt

# 使用 plt.imshow 显示图像
plt.imshow(image)
plt.title("Using plt.imshow")
plt.show()

```

上面这段最简单的显示代码，其实是 `plt.imshow` 是一个全局函数，默认激活了`Figure` 和 `Axes`， 并在其中显示图像。

``` python

plt.imshow(image)
plt.title("Using plt.imshow")
plt.draw() 

# 暂停0.5s
plt.pause(0.5)

# 或者暂停等按键
key = plt.waitforbuttonpress()
    if key:
        # 检查按键
        # user_input = input()
		# if user_input == 'n':
		#     current_index = (current_index + 1) % len(paths)
		# elif user_input == 'p':
		#     current_index = (current_index - 1) % len(paths)
		# elif user_input == 'q':
		#     break
		# else:
		#     print("Invalid key! Use 'n', 'p', or 'q'.")
    pass

```

> [!tips] `plt.show 和 plt.draw`
> 
> 调用 `plt.show()` 时，程序会阻塞直到关闭所有图形窗口为止。这意味着在调用 `plt.show()` 后，程序会等待用户交互，直到用户关闭图形窗口才会继续执行后续代码。
> 
> 使用 `plt.draw()` 和 `plt.pause()` 的组合，会在更新图片后立即更新图形，而不会阻塞程序的执行，类似`opencv`的显示。



上面这段代码基本等价于opencv的显示图片了。问题是默认的`plt`只能创建一个窗口和一个子图，如果需要创建多窗口和多子图，则需要自己管理`Figure` 和 `Axes`


``` python

# 创建第一个 Figure 对象和子图, 只包含一个子图
fig1, ax1 = plt.subplots()
fig1.suptitle("Windows1", fontsize=14, fontweight='bold')
ax1.set_title("image1")


# 创建第二个窗口， 包含3个子图
fig2, ax2 = plt.subplots(1, 3)
ax2[0].set_title("image1")
ax2[1].set_title("image2")
ax2[2].set_title("image3")

# 创建第三个窗口， 包含2x3个子图
fig3, ax3 = plt.subplots(2, 3)
ax3[0, 1].set_title("image1")
ax3[0, 1].set_title("image2")
ax3[0, 2].set_title("image3")

```


> [!tips] 多`Figure`管理
> 当创建多个`Figure`对象，`plt`的函数如`plt.draw()` 会更新当前活动的 `Figure` 对象。
> 当前活动的 `Figure` 对象是最近创建或显示的那个。
> 通过 `plt.figure(fig.number)` 将某个 `Figure` 设置为当前活动的图形。

``` python

import matplotlib.pyplot as plt


# 创建第一个 Figure 对象和子图
fig1, ax1 = plt.subplots()
img1 = ax1.imshow(data1)
ax1.set_title("Figure 1: Random Data 1")

# 当前活动的Figure对象是 fig1

# 创建第二个 Figure 对象和子图
fig2, ax2 = plt.subplots()
img2 = ax2.imshow(data2)
ax2.set_title("Figure 2: Random Data 2")

# 当前活动的Figure对象是 fig2


# 将fig1 设置为当前活动的图形并刷新
plt.figure(fig1.number)
plt.draw()
plt.pause(0.5)

# 将fig2 设置为当前活动的图形并刷新
plt.figure(fig2.number)
plt.draw()
plt.pause(0.5)

```



## Backend

常见的错误有 `Matplotlib is currently using agg, which is a non-GUI backend, so cannot show the figure`

解决方案有：

``` python

# 方案一: 切换后端
import matplotlib
matplotlib.use('Agg')  # 使用Agg

import matplotlib.pyplot as plt


# 方案二: 保存图片
import matplotlib.pyplot as plt

# ...

plt.savefig("mygraph.png")

```



> [!quote]
> 1. [解决 Matplotlib is currently using agg, which is a non-GUI backend, so cannot show the figure-CSDN博客](https://blog.csdn.net/qi_yue_yu/article/details/103597831)
> 2. [python - "UserWarning: Matplotlib is currently using agg, which is a non-GUI backend, so cannot show the figure." when plotting figure with pyplot on Pycharm - Stack Overflow](https://stackoverflow.com/questions/56656777/userwarning-matplotlib-is-currently-using-agg-which-is-a-non-gui-backend-so)

## 基础



``` python

# 类似opencv的显示

import matplotlib.pyplot as plt

# 创建图形和子图
fig, axs = plt.subplots(1, 3)

# 函数用于显示图像
def display_image(mask, fwd_flow, bwd_flow):
	axs[0].imshow(mask)
	axs[0].set_title("mask")

	axs[1].imshow(fwd_flow)
	axs[1].set_title("fwd_flow")

	axs[2].imshow(bwd_flow)
	axs[2].set_title("bwd_flow")
	plt.draw()

for i in range(100):
	display_image(mask, flow_fwd_color, flow_bwd_color)
	
	key = plt.waitforbuttonpress()
	if key:
		# 检查按键
		# user_input = input()
		# if user_input == 'n':
		#     current_index = (current_index + 1) % len(paths)
		# elif user_input == 'p':
		#     current_index = (current_index - 1) % len(paths)
		# elif user_input == 'q':
		#     break
		# else:
		#     print("Invalid key! Use 'n', 'p', or 'q'.")

		# 清除子图
		for ax in axs:
			ax.clear()

	pass

plt.close(fig)

```


## 例子

```python
# 画手的3D骨架
def show3D(x, y, z, title='3D'):
        X = x.flatten()
        Y = y.flatten()
        Z = z.flatten()
        
        link_set = [
            [0, 17, 18, 19, 20],  # 大拇指
            [0, 13, 14, 15, 16],
            [0, 9, 10, 11, 12],
            [0, 5, 6, 7, 8],
            [0, 1, 2, 3, 4],
            [1, 5, 9, 13],
        ]

        color = ['y', 'b', 'g', 'r', 'm', 'c']  # 颜色
        fig = plt.figure(title)
        ax = plt.subplot(projection='3d')
        # ax.set_zlim(0, 12)
        for i in range(6):
            figure = ax.plot(X[link_set[i]], Y[link_set[i]], Z[link_set[i]], c=color[i])
        pass

show3D(uv[:, 0], uv[:, 1], d, "Hand2.5D")
show3D(xyz[:, 0], xyz[:, 1], xyz[:, 2], "Hand3D")

# 阻塞式运行，直到关闭窗口
plt.show()

```

也可以使用子图的形式画到一起 

``` python
# 合在一起，使用子图画手的3D骨架

def show3D(x, y, z, pos, title='3D'):
        X = x.flatten()
        Y = y.flatten()
        Z = z.flatten()

        link_set = [
            [0, 17, 18, 19, 20],  # 大拇指
            [0, 13, 14, 15, 16],
            [0, 9, 10, 11, 12],
            [0, 5, 6, 7, 8],
            [0, 1, 2, 3, 4],
            [1, 5, 9, 13],
        ]

        color = ['y', 'b', 'g', 'r', 'm', 'c']  # 颜色
        ax = fig.add_subplot(*pos, projection='3d')
        # 设置初始视角
	    ax.view_init(elev=-90, azim=-90)
        ax.set_title(title)
        for i in range(6):
            figure = ax.plot(X[link_set[i]], Y[link_set[i]], Z[link_set[i]], c=color[i])
        pass

fig = plt.figure()
show3D(uv[:, 0], uv[:, 1], d, [1, 2, 1], "Hand2.5D")
show3D(xyz[:, 0], xyz[:, 1], xyz[:, 2], [1, 2, 2], "Hand3D")
plt.show()
```

