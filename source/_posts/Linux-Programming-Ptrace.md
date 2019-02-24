---
title: Linux-Programming-Ptrace
date: 2019-02-06 01:41:42
- Linux Programming
tags:
- system call
---

Why we need the ptrace ,because that we need some process as parent process to monitor the child processes behavior ,such as check if the system call is legal . Or do some debug for an exist program , the theory of the debugger like GDB .

<!--more-->

# Ptrace 

the proto system call in C 

```C
long ptrace(enum __ptrace_request request, pid_t pid,void * addr,void * data);
```

how to use the system call ?

first fork a new child process and then call the ptrace like this 

```C
ptrace(PTRACE_TRACEME,0,NULL,NULL);
```

then the child process will be the state called traceable , and then the process which call the ptrace with the PTRACE_TRACEME will be the tracee.

and in the parent process can use the ptrace call to get some info for the child process . like this 

```C
orig_eax = ptrace(PTRACE_PEEKUSER,child, 4 * ORIG_EAX,NULL);
```

after call the ptrace the parent will get the child process eax register value , the ptrace will return a word data of the child process specified with the ptrace request .

# In Golang 

we can use the API to do same behavior like this .

```GO
import(
  "os/exec"
  "syscall"
)
cmd := os.Command("ls","-al")
cmd.SysProcAttr = &syscall.SysProcAttr{
  Ptrace:true
}
cmd.Start()
var pid = cmd.Process.Pid
```

after these operations then the command child process will be tracee and we can use these golang api to operate the ptrace behavior 

```GO
PtraceAttach(pid int) (err error)
PtraceCont(pid int, signal int) (err error)
PtraceDetach(pid int) (err error)
PtraceGetEventMsg(pid int) (msg uint, err error)
PtraceGetRegs(pid int, regsout *PtraceRegs) (err error)
PtracePeekData(pid int, addr uintptr, out []byte) (count int, err error)
PtracePeekText(pid int, addr uintptr, out []byte) (count int, err error)
PtracePokeData(pid int, addr uintptr, data []byte) (count int, err error)
PtracePokeText(pid int, addr uintptr, data []byte) (count int, err error)
PtraceSetOptions(pid int, options int) (err error)
PtraceSetRegs(pid int, regs *PtraceRegs) (err error)
PtraceSingleStep(pid int) (err error)
PtraceSyscall(pid int, signal int) (err error)
```

the demo is that kill the child process 

```GO
PtraceSetOptions(pid,syscall.PTRACE_O_TRACEEXIT)
```

Aha is so easy isn't not ?