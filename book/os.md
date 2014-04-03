# OS
操作系统相关的工具方法，使用` require('os') `来调用它。

------

## API

### os.tmpdir
**os.tmpdir()** 返回系统默认的临时文件夹目录。

### os.endianness
**os.endianness()** 返回CPU的字节顺序，是大端**BE**或是**LE**小端。

### os.hostname
**os.hostname()** 返回操作系统的主机名。

### os.type
**os.type()** 返回操作系统名。

### platform
**os.platform()** 调用 **process.platform()** 方法，返回操作系统平台。

### os.arch
**os.arch()** 调用 **process.arch()**方法，返回CPU类型。

### os.release
**os.release()** 返回系统发行版本号。

### os.uptime
**os.uptime()** 返回系统的本次开机后已工作时间。

### os.loadavg
**os.loadavg()** 返回一个包含系统在1分钟、5分钟和15分钟平均负载的数组。它只在Linux/Unix系统下有效，在Windows下始终返回[0, 0, 0]。

平均负载记录了系统活动的繁忙程度，理想情况下，平均负载不应该大于电脑逻辑CPU的数量。 

### os.totalmem
**os.totalmem()**方法会返回系统总的内存大小，以字节B为单位。

### os.freemem
**os.freemem()**方法返回系统空闲内存的大小，以字节B为单位。

### os.cpus
**os.cpus()**方法返回一个包含系统每个CPU内核的信息的数组，它返回的是系统逻辑CPU个数的信息。如Intel i5双核处理器可能会返回4个CPU的信息(因为Intel的多线程技术，会模拟出一个CPU)。

### os.networkInterfaces
**os.networkInterfaces()**返回电脑网卡信息。

### os.EOL
> EOL: End-of-line   

**os.EOL**表示系统使用的是哪种行结束符。




