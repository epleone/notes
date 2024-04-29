---
tags: [todo]
---

concurrent.futures是python 3.7的标准库。
concurrent提供了两种并发模型，一个是多线程ThreadPoolExecutor，一个是多进程ProcessPoolExecutor。他们相当于multithreading或者multiprocess的更高级封装。


对于IO密集型任务宜使用多线程模型。对于计算密集型任务应该使用多进程模型。

为什么要这样选择呢？是因为Python GIL的存在让Python虚拟机在进行运算时无法有效利用多核心。对于纯计算任务，它永远最多只能榨干单个CPU核心。如果要突破这个瓶颈，就必须fork出多个子进程来分担计算任务。而对于IO密集型任务，CPU使用率往往是极低的，使用多线程虽然会加倍CPU使用率，但是还远远到不了饱和(100%)的地步，在单核心可以应付整体计算的前提下，自然是应该选择资源占用少的模式，也就是多线程模式。


# 多线程ThreadPoolExecutor


![](images/v2-1d2372eb62523cea6e53d7e27f8c6961_720w.webp)

  

# 多进程ProcessPoolExecutor


![](images/v2-39cfc279641f985eb94a5e22fae77be2_720w.webp.jpg)


# 代码示例


多进程模式在代码的编写上和多线程没有多大差异，仅仅是换了一个类名，其它都一摸一样。
```python
ThreadPoolExecutor <--> ProcessPoolExecutor
```


```python
import time
import random
from concurrent.futures import ProcessPoolExecutor, as_completed
from tqdm import tqdm

# 定义一个任务函数
def task(name):
    # 模拟任务执行时间
    time.sleep(random.uniform(1, 3))
    return name

# 使用 executor.submit
if __name__ == '__main__':
    # 创建进程池
    with ProcessPoolExecutor() as executor:
        # 定义任务列表
        tasks = ["Task 1", "Task 2", "Task 3"]

        # 使用tqdm创建进度条，并遍历任务列表
        with tqdm(total=len(tasks)) as progress_bar:
            # 提交任务并获得Future对象列表
            futures = [executor.submit(task, task_name) for task_name in tasks]

            # 迭代处理已完成的任务
            # `as_completed`函数用于迭代一个由`concurrent.futures.Future`对象组成的可迭代对象，并在每个`Future`对象完成时返回。
            for future in as_completed(futures):
                result = future.result()
                progress_bar.set_description(result)  # 更新进度条描述
                progress_bar.update(1)  # 更新进度条

# 使用 executor.map
if __name__ == '__main__':
    with ProcessPoolExecutor() as executor:
        tasks = ["Task 1", "Task 2", "Task 3"]

        # 使用tqdm创建进度条，并遍历任务列表
        with tqdm(total=len(tasks)) as progress_bar:
            # 使用executor.map方法提交任务，并获取结果迭代器
            results = executor.map(task, tasks)
            
            # 遍历结果迭代器
            for result in results:
                progress_bar.set_description(result)  # 更新进度条描述
                progress_bar.update(1)  # 更新进度条

```


`executor.map`和`executor.submit`是`concurrent.futures`模块中的两种不同的任务提交方法，它们有以下区别：

1. 返回结果的方式：
   - `executor.map`方法会按照任务的顺序提交，并返回一个与任务列表对应的结果迭代器。这意味着你可以使用迭代器直接获取任务的结果。
   - `executor.submit`方法会立即返回一个`concurrent.futures.Future`对象，表示任务的未来结果。你可以通过`Future`对象的`result()`方法获取任务的结果，或使用`as_completed()`方法或`wait()`方法等来获取已完成的任务结果。

2. 使用方式：
   - `executor.map`方法接受一个可迭代的任务列表作为输入，并自动将每个任务提交给执行器。你不需要显式地迭代任务列表或调用`submit`方法。
   - `executor.submit`方法需要显式地调用，针对每个任务创建一个`Future`对象。你需要将这些`Future`对象存储起来，并在需要时使用`result()`方法获取结果。

3. 异常处理：
   - `executor.map`方法对于每个任务都会自动处理异常，并将异常捕获在结果迭代器中。如果某个任务引发了异常，你需要在迭代结果时处理异常，否则异常将在迭代时被抛出。
   - `executor.submit`方法返回的`Future`对象可以通过`result()`方法获取结果，并在该方法中处理异常。你可以使用`try/except`块来捕获并处理每个任务的异常。

总体来说，`executor.map`方法更加简洁和方便，适用于任务量较大且顺序执行的场景。而`executor.submit`方法则更加灵活，适用于需要对每个任务进行个别处理、异常处理或需要获取任务的`Future`对象的场景。


根据你的具体需求和任务特点，选择合适的方法来提交任务和处理结果。

#  rich progress and multiprocessing

使用rich来实时显示多进程的进度
[rich progress and multiprocessing | Of Last Importance](https://www.deanmontgomery.com/2022/03/24/rich-progress-and-multiprocessing/)

``` python
import multiprocessing
import random
from concurrent.futures import ProcessPoolExecutor
from time import sleep

from rich import progress


def long_running_fn(progress, task_id):
    len_of_task = random.randint(3, 20)  # take some random length of time
    for n in range(0, len_of_task):
        sleep(1)  # sleep for a bit to simulate work
        progress[task_id] = {"progress": n + 1, "total": len_of_task}


if __name__ == "__main__":
    n_workers = 8  # set this to the number of cores you have on your machine

    with progress.Progress(
        "[progress.description]{task.description}",
        progress.BarColumn(),
        "[progress.percentage]{task.percentage:>3.0f}%",
        progress.TimeRemainingColumn(),
        progress.TimeElapsedColumn(),
        refresh_per_second=1,  # bit slower updates
    ) as progress:
        futures = []  # keep track of the jobs
        with multiprocessing.Manager() as manager:
            # this is the key - we share some state between our 
            # main process and our worker functions
            _progress = manager.dict()
            overall_progress_task = progress.add_task("[green]All jobs progress:")

            with ProcessPoolExecutor(max_workers=n_workers) as executor:
                for n in range(0, 20):  # iterate over the jobs we need to run
                    # set visible false so we don't have a lot of bars all at once:
                    task_id = progress.add_task(f"task {n}", visible=False)
                    futures.append(executor.submit(long_running_fn, _progress, task_id))

                # monitor the progress:
                while (n_finished := sum([future.done() for future in futures])) < len(
                    futures
                ):
                    progress.update(
                        overall_progress_task, completed=n_finished, total=len(futures)
                    )
                    for task_id, update_data in _progress.items():
                        latest = update_data["progress"]
                        total = update_data["total"]
                        # update the progress bar for this task:
                        progress.update(
                            task_id,
                            completed=latest,
                            total=total,
                            visible=latest < total,
                        )

                # raise any errors:
                for future in futures:
                    future.result()
```


## TODO: 待整理

[Python concurrent.future 使用教程及源码初剖 - 知乎](https://zhuanlan.zhihu.com/p/31544936)
[如何愉快的使用concurrent.futures - 简书](https://www.jianshu.com/p/36bf441034b9)
[Python多进程和多线程（跑满CPU）\_python进程数超过cpu核数\_fengkuangshine的博客-CSDN博客](https://blog.csdn.net/qq_40317897/article/details/89921083?utm_medium=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.control&depth_1-utm_source=distribute.pc_relevant.none-task-blog-BlogCommendFromMachineLearnPai2-2.control)
