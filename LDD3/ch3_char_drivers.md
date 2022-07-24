## Major and minor number
  1. Char devices are accessed through names in the filesystem. Those names are called
special files or device files or simply nodes of the filesystem tree; they are convention-
ally located in the /dev directory. Special files for char drivers are identified by a “c”
in the first column of the output of ls –l. Block devices appear in /dev as well, but
they are identified by a “b.”
  2. Major number - same for entire HDD
     Minor number - 4 partitions have different minors
  3. The above functions allocate device numbers for your driver’s use, but they do not
tell the kernel anything about what you will actually do with those numbers. Before a
user-space program can access one of those device numbers, your driver needs to
connect them to its internal functions that implement the device’s operations

## Imp data structures used by drivers
  1. File operations
    - Is a part of the file structure, contains ptrs to operations performed by the device like open, read, write, ioctl, etc. 
  2. File structure
    -  Represents an open file. It is created by
the kernel on open and is passed to any function that operates on the file, until
the last close. After all instances of the file are closed, the kernel releases the data
structure. 

## The inode structure
  1. The inode structure is used by the kernel internally to represent files. Therefore, it is different from the file structure that represents an open file descriptor.
  2. There can be numerous file structures representing multiple open descriptors on a single file, but they all point to a single inode structure.

## Open method
  1. In most drivers, open should perform the following tasks:
• Check for device-specific errors (such as device-not-ready or similar hardware
problems)
• Initialize the device if it is being opened for the first time
• Update the f\_op pointer, if necessary
• Allocate and fill any data structure to be put in filp-\>private\_data

## Release method
  1. The release/close method must do the following:
    - deallocate anything that open allocated in filp-\>private\_data
    - shut down the device on last close


