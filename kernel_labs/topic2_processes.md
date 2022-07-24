## Intro
1. A process is a program in execution that groups the following resources together: address space, threads, shared mem regions, timers, sockets, semaphores, signal handlers, etc. 

2. Summary of a process's resources can be obtained from /proc/<pid>.

## Struct task\_struct (PCB of the linux kernel)
1. Processes and threads both are represented as task\_struct with flags differentiating both.

2. Threads
	a. It is a basic unit that the kernel process scheduler uses to allow applications to run the CPU. 
	b. Each thread has its own stack and together with register values it determines thread execution state. 
	c. All threads of a process share the process's resources.
	d. Kernel schedules threads not processes and user level threads are not visible in kernel. 
	
## Clone syscall
1. A new thread/process is created with clone(). The fork syscall and pthread\_create() use the clone call.

2. If the flags CLONE\_FILES | CLONE\_VM | CLONE\_FS are set, then a new thread is created, or else a new process is created. 

## Namespaces and containers
1. Containers utilize the namespace feature. It allows isolation of different resources, that would be globally visible. 

2. struct nsproxy is used to group types of resources to be partitioned. It supports IPC, networking, cgroup, mount, networking, PID and time namespaces. 

## Current process and context switch
1. A per CPU variable is used to store and retrieve the pointer to current struct task\_struct.
2. Before a context switch occurs, kernel transition must happen with syscall/interrupt. Userspace regs are saved on kernel stack and then at some point, schedulefunction must be called which can decide that a context switch must occur from T0 to T1

## Blocking and waking up tasks
1. Task states and transitions between them:
//insert diag here

2. Blocking the current thread
	a. set the current thread state to TASK_UINTERRUPTIBLE or TASK_INTERRUPTIBLE
	b. Add the task to a waiting queue
	c. Call the scheduler which will pick up a new task from the READY queue
	d. Do the context switch to the new task

3. Waking up a task
	a. Select a task from waiting queue
	b. Set the task state to TASK_READY
	c. Insert the task into the scheduler's READY queue
	d. On SMP system this is a complex operation: each processor has its own queue, queues need to be balanced, CPUs needs to be signaled  

## Preemption	
1. Non preemptive kernel
In this, tasks are not preempted while running in kernel mode (syscalls), so there are no synchronization issues.

2. Preemptive kernel
	a. Task can be preempted in kernel mode, requires using a special synchronization primitives: preempt_disable and preempt_enable.
	b. Preemption is disabled automatically when a spinlock is used.
	
## Process context
1. The kernel is executing in process context when it is running a system call.

2. In process context there is a well defined context and we can access the current process data with current

3. In process context we can sleep (wait on a condition).

4. In process context we can access the user-space (unless we are running in a kernel thread context).

## Kernel threads
1. Sometimes the kernel core or device drivers need to perform blocking operations and thus they need to run in process context.

2. Kernel threads are used exactly for this and are a special class of tasks that don't "userspace" resources (e.g. no address space or opened files).


