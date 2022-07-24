## Implementation in kernel
1. Syscalls are an interface for userspace apps to use kernel services.
2. Syscall entry point saves registers (having userspace data) on the stack and continue with syscall dispatcher.
3. During the user mode -> kernel transition, the stack also switches from user stack to kernel stack.
4. Summary of syscall execution:
	a. Application sets up syscall no and params and issues a trap instrn
	b. Execution mode goes from user -> kernel, CPU switches to kernel stack, 	the user stack and return address to userspace is saved on kernel stack. 
	c. Syscall dispatcher identifies the syscall function and executes it.
	d. Userspace regs are restored and execution is switched back to user (IRET instrn)
	e. Userspace appln resumes

## Syscall params
1. Since params are setup by userspace, kernel must verify them.
2. Pointers should not point to kernel space and should be valid.
3. Linux has APIs like copy\_to_\user() to check pointers from userspace. The instrns that access userspace are recorded in an exception table. In case of page fault, the faulting address is checked against this table. 

## Virtual dynamic shared object
1. x86 has 2 ways to issue syscalls : int 0x80 and sysenter. They are not on every system, so we need VDSO
2. VDSO is set of instrns to issue the syscal is generated by kernel in ELF shared obj, and this memory area is mapped to end of user space. 
3. libc searches for VDSO, and if present, uses it to issue syscalls. 

	