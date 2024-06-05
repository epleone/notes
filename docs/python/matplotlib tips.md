---
tags: [matplotlib, python, tips]
---
# matplotlib tips


在有些linux环境下，opencv的显示可能不可用，需要使用plt可视化

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

