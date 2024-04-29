---
tags: [matplotlib, python, tips]
---


# matplotlib tips

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

