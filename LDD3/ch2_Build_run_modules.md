## Kernel modules vs applications
  1. While most small and medium-sized applications perform a single task from begin-
ning to end, every kernel module just registers itself in order to serve future requests,
and its initialization function terminates immediately.
  2. Because no library is linked to modules, source files should never include the usual
header files, \<stdarg.h\> and very special situations being the only exceptions. Only
functions that are actually part of the kernel itself may be used in kernel modules

## User space and kernel space
  1. The operating system must account for independent operation of programs and protection against unauthorized access to resources. This nontrivial task is possible only if the CPU enforces protection of system software from the applications.

  2. In lower levels (user mode), CPU regulates the use of hardware resources.
  3. Syscalls allow kernel code to operate on behalf of processes.
  4. User and kernel mode have their own memory mapping and adderss mapping

## The current process
  1. Kernel code can refer to the current process by accessing the global item current, defined in \<asm/current.h\>, which yields a pointer to struct task\_struct, defined by \<linux/sched.h\>

## Compiling  and loading/unloading modules
  1. The files found in the Documentation/kbuild directory in the kernel source are required reading for understanding detail
  2. insmod : It loads the module code and data into kernel and resolves any unresolved symbol to kernel's symbol table. The insmod command modifies the in memory copy of the module instead of on disk module.
  3. modprobe :  It differs in that it will look at the module to be loaded to see
whether it references any symbols that are not currently defined in the kernel. If any
such references are found, modprobe looks for other modules in the current module
search path that define the relevant symbols. When modprobe finds those modules
(which are needed by the module being loaded), it loads them into the kernel as well.
If you use insmod in this situation instead, the command fails with an “unresolved
symbols” message left in the system logfile. 
  4. You have to compile your module for different kernel versions. Do checks with #ifdef constructs (in linux/version.h). Confine the checks to a header file

## Kernel symbol table
  1. The table contains the addresses of global kernel items—functions and
variables—that are needed to implement modularized drivers. When a module is
loaded, any symbol exported by the module becomes part of the kernel symbol table.
In the usual case, a module implements its own functionality without the need to
export any symbols.
  2. New modules can use symbols exported by your module, and you can stack new
modules on top of other modules. Module stacking is implemented in the main-
stream kernel sources as well: the msdos filesystem relies on symbols exported by the
fat module, and each input USB device module stacks on the usbcore and input modules.

## Init and shutdown
  1. Basic skeleton of init function
     ```c
        static int __init initialization_function(void)
        {
            /* Initialization code here */
        }
        module_init(initialization_function);
     ```
    - __init token  is a hint to the kernel that the given function is
used only at initialization time. The module loader drops the initialization function
after the module is loaded, making its memory available for other uses.
  2. Modules can register many different types of facilities, including different kinds of devices, filesystems, cryptographic transforms, and more. For each facility, there is a specific kernel function that accomplishes this registration. The arguments passed to the kernel registration functions are usually pointers to data structures describing the new facility and the name of the facility being registered.
  3. Most registration functions are prefixed with register\_, so another possible way to find them is to grep for register\_ in the kernel source. Also look for EXPORTSYMBOL
  4. Cleanup function - 
      ```c
     		static void __exit cleanup\_function(void)
				{
						/* Cleanup code here */
				}
					module_exit(cleanup_function); 
      ```

