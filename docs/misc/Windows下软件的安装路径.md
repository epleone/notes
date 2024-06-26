# Windows下软件的安装路径

简单说下这几个目录的区别：

- **_C:\Program Files_**，64位的软件安装目录，所有用户都能使用，程序安装到这里需要管理员权限。若程序数据也写在这个目录下，软件每次启动都会要求授予管理员权限，存在很大的安全隐患。
- **_C:\Program Files(x86)_**， 和上面的一样，只是该目录用于存放32位的应用。
- **_C:\Users\用户名\AppData\Roaming_**，通过环境变量 **%AppData%** 可以访问，这里用来存放当前登录用户所产生的数据，对其他的用户不可见。如果你在公司网络，加入了域，这个文件夹会通过网路同步，让你的数据在不同的计算机上漫游。该目录常被滥用。
- **_C:\Users\用户名\AppData\Local_**，通过环境变量 **%LocalAppData%** 可以访问，这个目录和上面的基本一样，不过仅限本地，加入了域也不会同步。一般情况下，如果不加入域，Roaming和Local并没有什么区别，但是需要网络同步的时候，尽量把不必要的数据留在本地。个人使用的软件可以装在**_C:\Users\用户名\AppData\Local\Programs_**_下面 。_
- **_C:\ProgramData_**，和AppData的区别在于，这里存放的数据是公用的，如果你希望软件的数据在所有的用户之间保持一致，那么程序生成的数据应当存放在这里。
- **_C:\Users\用户名_**，用户的家目录。Linux和MacOS用户应该很熟悉，所有个人数据都应该放在家目录下。一些跨平台软件喜欢在这里写数据，并生成一堆文件名以 **.** 开头的文件，这在Linux下是隐藏文件，但Windows文件管理器并不按这个逻辑处理，用户只能主动给所有点号开的文件加上隐藏属性，并文件管理器里设置成不显示。
- **_C:\Users\用户名\Documents_**，又称**_我的文档_**。看名字就知道，这里存放的是用户_主动保存_的数据。比如各种文稿，照片，音乐，视频，档案 之类的。有的软件会无脑把程序数据写在这里，导致文档目录混乱。有些数据(日志、配置、缓存数据)是给软件服务的，用户无法直接使用这些文件，那么这些数据就应该放到AppData下面去。

为什么有些软件的默认安装路径是C:\Users\用户名\AppData\Roaming? - root的回答 - 知乎 https://www.zhihu.com/question/548340950/answer/2637630471
