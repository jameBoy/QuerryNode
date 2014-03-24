# SIGNAL 信号量


### List

```
     *    SIGHUP       terminate process    终端检测到一个链接断开，将此信号发送给与该终端相关的控制进程
     *    SIGINT       terminate process    用户按下断键(DELETE或Ctrl+C)
     *    SIGQUIT      create core image    用户按下退出键(一般为Ctrl+\)
     *    SIGILL       create core image    进程执行一条非法硬件指令
     *    SIGTRAP      create core image    指示一个实现定义的硬件出错，常用来作断点debug
     *    SIGABRT      create core image    进程异常终止
     *    SIGFPE       create core image    算术异常，如除以0等
     *    SIGKILL      terminate process    杀死程序
     *    SIGBUS       create core image    实现定义的硬件故障，如内存故障等
     *    SIGSEGV      create core image    非法内存引用
     *    SIGSYS       create core image    无效系统调用
     *    SIGPIPE      terminate process    写到管道时读进程已终止
     *    SIGALRM      terminate process    计时器超时
     *    SIGTERM      terminate process    系统默认终止信号
     *    SIGURG       discard signal       系统通知进程有紧急情况
     *    SIGSTOP      stop process         停止信号，用于作业控制停止一个进程
     *    SIGTSTP      stop process         交互停止信号，当用户在终端按下挂起键(如Ctrl+Z)时
     *    SIGCONT      discard signal       停止状态进程继续运行的信号
     *    SIGCHLD      discard signal       子进程终止或停止时，发送给父进程
     *    SIGTTIN      stop process         后台进程试图读取控制终端
     *    SIGTTOU      stop process         后台进程试图写入控制终端
     *    SIGIO        discard signal       指示一个异步IO事件
     *    SIGXCPU      terminate process    进程超过了其软CPU时间限制
     *    SIGXFSZ      terminate process    进程超过了其软文件长度限制
     *    SIGUSR1      terminate process    用户定义信号1
     *    SIGUSR2      terminate process    用户定义信号2
```
