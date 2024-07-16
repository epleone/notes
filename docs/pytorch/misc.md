# misc

## 多卡运行

在训了几个epoch后我注意到，在每个epoch之间都会有一次UserWarning的输出且有个短暂停顿，根据经验我猜到是因为Dataloader没有开persistent_workers，导致每个epoch都会重新启动多进程，很快我在`mmpose/datasets/builder.py`下找到了Dataloader的定义发现果然如此

  

作者：镜子  
链接：[https://zhuanlan.zhihu.com/p/536532432 ](https://zhuanlan.zhihu.com/p/536532432  ) 
