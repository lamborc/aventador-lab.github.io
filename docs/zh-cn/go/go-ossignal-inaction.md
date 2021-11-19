# os/signal

## OS signal 

信号是事件发生时对进程的通知机制。有时也称之为软件中断。信号与硬件中断的相似之处在于打断了程序执行的正常流程，大多数情况下，无法预测信号到达的精确时间

> 兼容性问题：信号的概念来自于 Unix-like 系统。Windows 下只支持 os.SIGINT 信号。\
> 程序无法捕获信号 SIGKILL 和 SIGSTOP （终止和暂停进程），因此 os/signal 包对这两个信号无效。

## Go 程序对信号的默认行为

Go 语言实现了自己的运行时，因此，对信号的默认处理方式和普通的 C 程序不太一样。

  - SIGBUS（总线错误）, SIGFPE（算术错误）和 SIGSEGV（段错误）称为同步信号，它们在程序执行错误时触发，而不是通过 os.Process.Kill 之类的触发。通常，Go 程序会将这类信号转为 run-time panic。
  - SIGHUP（挂起）, SIGINT（中断）或 SIGTERM（终止）默认会使得程序退出。
  - SIGQUIT, SIGILL, SIGTRAP, SIGABRT, SIGSTKFLT, SIGEMT 或 SIGSYS 默认会使得程序退出，同时生成 stack dump。
  - SIGTSTP, SIGTTIN 或 SIGTTOU，这是 shell 使用的，作业控制的信号，执行系统默认的行为。
  - SIGPROF（性能分析定时器，记录 CPU 时间，包括用户态和内核态）， Go 运行时使用该信号实现 runtime.CPUProfile。
  - 其他信号，Go 捕获了，但没有做任何处理

信号可以被忽略或通过掩码阻塞（屏蔽字 mask）。忽略信号通过 signal.Ignore，没有导出 API 可以直接修改阻塞掩码，虽然 Go 内部有实现 sigprocmask 等。Go 中的信号被 runtime 控制，在使用时和 C 是不太一样的

## signal API

### Notify 函数

func Notify(c chan<- os.Signal, sig ...os.Signal)

> 类似于绑定信号处理程序。将输入信号转发到 chan c。如果没有列出要传递的信号，会将所有输入信号传递到 c；否则只传递列出的输入信号

