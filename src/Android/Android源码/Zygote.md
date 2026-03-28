# Zygote

Zygote进程：是所有Java进程的父进程，所有App进程都是由Zygote进程fork生成的。

system server进程：是Zygote孵化的第一个进程。

Launcher进程：是Zygote孵化的第一个App进程

为什么采用Zygote孵化进程，而不是新建进程？

> 每个应用程序都运行在各自的虚拟机中，应用程序每次启动都需要重新初始化和启动虚拟机，耗时。
>
> Zygote进程在启动时会创建Java虚拟机，因此通过fork创建的进程可以直接拷贝Java虚拟机的实例，起到预加载资源和类的作用，从而缩短启动时间。

为什么system_server和Zygote使用Socket通信，不使用Binder通信？

> Zygote比service_manager先启动，没有service_manager可以注册，所以没法用Binder
>
> Zygote通过fork生成进程，多线程不允许使用fork，而Binder是多线程，会导致死锁

fork为什么不能用多线程？

> fork的子进程只能保留调用fork的线程，以及所有全局变量状态和pthread对象，其他线程在子进程会被终止。
>
> 1. 若复制过程有其他线程持有锁，由于线程终止，会导致锁被永久占用，无法释放，当再次访问该互斥量时会引发死锁。
> 2. 若复制过程有其他线程正在更新全局变量，由于线程终止，可能导致全局变量状态不一致。

https://haruue.moe/blog/2017/08/30/call-privileged-api-with-app-process/