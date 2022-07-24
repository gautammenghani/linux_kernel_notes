## Debugging via querying
  1. A lot of printk messages can slow the system, so use tools like ps,netstat.
  2. A few techniques are available to driver developers for querying the system: creating a file in the /proc filesystem, using the ioctl driver method, and exporting attributes via sysfs.

## /proc file system
  1. Used by kernel to export kernel information to the world. Used by ps, top, etc.  Your driver can use this but is discouraged as its purpose was to provide info about running processes. Instead use sysfs
  2. To create your own proc file
    - Define read\_proc function  
    - Connect it to an entry in /proc hierarchy with create\_proc\_read\_entry
    - Remove the entry when module is unloaded remove\_proc\_entry
  3. This is not suited for large output, use seq\_file interface 

## The ioctl method
  1. It is a system call that acts on a file descriptor; it receives a number that identifies a command to be performed and (optionally) another argument, usually a pointer. As an alternative to using the /proc filesystem, you can implement a few ioctl commands tailored for debugging. These commands can copy relevant data structures from the driver to user space where you can examine them. Using ioctl this way to get information is somewhat more difficult than using /proc, because you need another program to issue the ioctl and display the results. This program must be written, compiled, and kept in sync with the module you’re testing. On the other hand, the driver-side code can be easier than what is needed to implement a /proc file.

## Debugging by watching
  1. Watch the driver's behaviour or use tools like gdb, strace, etc

## Oops messages
  1. Most bugs show themselves in NULL pointer dereferences or by the use of other incorrect pointer values. The usual outcome of such bugs is an oops message. Almost any address used by the processor is a virtual address and is mapped to physical addresses through a complex structure of page tables (the exceptions are physical addresses used with the memory management subsystem itself). When an invalid pointer is dereferenced, the paging mechanism fails to map the pointer to a physical address, and the processor signals a page fault to the operating system. If the address is not valid, the kernel is not able to “page in” the missing address; it (usually) generates an oops if this happens while the processor is in supervisor mode.

## System hangs
  1. sysrq is useful for system hangs, can do a variety of things like reboot, mount disks in read only mode, etc. - has to be explicitly enabled
    - echo 0 > /proc/sys/kernel/sysrq
  2. If you are experiencing a “live hang,” in which your driver is stuck in a loop but the system as a whole is still functioning, there are a couple of techniques worth knowing. Often, the SysRq p function points the finger directly at the guilty routine. 

## GDB
  1. gdb is not able to modify kernel data; it expects to be running a program to be debugged under its own control before playing with its memory image. It is also not possible to set breakpoints or watchpoints, or to single-step through kernel functions.
  2. Since modules are not part of the vmlinux image passed to gdb, the debugger knows nothing about them. Fortunately, as of kernel 2.6.7, it is possible to teach gdb what it needs to know to examine loadable modules.

## kdb
  1. Kernel debugger - needs to be applied as a patch separately

## User mode linux port
  1. It does not run on a new type of hardware, however; instead, it runs on a virtual machine implemented on the Linux system call interface. Thus, UML allows the Linux kernel to run as a separate, user-mode process on a Linux system.

## Linux trace toolkit
  1. The Linux Trace Toolkit (LTT) is a kernel patch and a set of related utilities that allow the tracing of events in the kernel. The trace includes timing information and can create a reasonably complete picture of what happened over a given period of time. Thus, it can be used not only for debugging but also for tracking down performance problems.
