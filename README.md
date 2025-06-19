# Tutorial 2: Kernel Programming by Adding a System Call in Xv6

A comprehensive guide to adding custom system calls in the Xv6 operating system.

## Overview

This guide demonstrates how to implement a custom system call called `getcourse`, which returns 3221 from the kernel (the number for our OS course). 
Adding a new system call in Xv6 requires modifying five key files in the operating system.

## Prerequisites

- [Set up Xv6](https://github.com/hamzehkhazaei/EECS3221_Xv6-Setup) 

## Files to Modify

To add a new system call in Xv6, you need to modify the following files:

1. `syscall.h` - System call number definitions
2. `syscall.c` - System call dispatch table
3. `sysproc.c` - System call implementation
4. `usys.S` - User-space system call stubs
5. `user.h` - User-space function declarations

## Step-by-Step Implementation

### Step 1: Define System Call Number

Open `syscall.h` and add a new system call number. Since there are 21 existing system calls, we'll use number 22:

```c
#define SYS_getcourseno 22
```
<img src="source/syscallh.png" alt="Description" style="max-width:100%; width:300px;">


### Step 2: Add Function Pointer to Dispatch Table

In `syscall.c`, add the function pointer to the system call dispatch table:

```c
[SYS_getcourseno] sys_getcourseno,
```
<img src="source/syscall1.png" alt="Description" style="max-width:100%; width:300px;">


Also, add the function prototype in the same file:

```c
extern int sys_getcourseno(void);
```
<img src="source/syscall2.png" alt="Description" style="max-width:100%; width:300px;">

### Step 3: Implement the System Call Function

Open `sysproc.c` and add the implementation of your system call:

```c
// Return the code of our OS course
int
sys_getcourseno(void)
{
    return 3221;
}
```

### Step 4: Add User-Space Assembly Stub

In `usys.S`, add the assembly stub that will be called from user programs:

```assembly
SYSCALL(getcourseno)
```

### Step 5: Add User-Space Function Declaration

In `user.h`, add the function declaration that user programs will use:

```c
int getcourseno(void);
```

## Testing the System Call

Create a test user program to verify that your system call works correctly:

```c
#include "types.h"
#include "stat.h"
#include "user.h"

int main(void)
{
    printf(1, "Note: The OS course number at York University is: %d\n", getcourseno());
    exit();
}
```

### Adding the Test Program

To include your test program in Xv6:

1. Save the test program as `testgetcourseno.c` in the Xv6 directory
2. Add `_testgetcourseno\` to the `UPROGS` section in the `Makefile`
3. Recompile and run Xv6

## Building and Running

1. Compile Xv6:
   ```bash
   make
   ```

2. Run Xv6 in QEMU:
   ```bash
   make qemu-nox
   ```

3. In the Xv6 shell, run your test program:
   ```bash
   testcourseno
   ```

Expected output:
   ```bash
   Note: The OS course number at York University is: 3221
   ```

## Understanding the Process

### System Call Flow

1. **User Program Call**: User program calls `getcourseno()`
2. **Assembly Stub**: `usys.S` stub puts system call number in register
3. **Kernel Trap**: CPU traps to kernel mode
4. **Dispatch**: `syscall()` function looks up system call number 22
5. **Execution**: `sys_getyear()` function executes and returns 1975
6. **Return**: Value is returned to the user program

### Key Concepts

- **System Call Numbers**: Each system call has a unique number used for dispatch
- **Function Pointers**: The kernel uses a table of function pointers for efficient dispatch
- **User/Kernel Interface**: Assembly stubs bridge user-space and kernel-space
- **Return Values**: System calls can return integer values to user programs

## Common Pitfalls

- **Forgetting to update all 5 files**: Missing any file will cause compilation errors
- **Incorrect system call numbers**: Using duplicate numbers will cause conflicts
- **Missing function prototypes**: Forgetting `extern` declarations in `syscall.c`
- **Assembly syntax**: Incorrect syntax in `usys.S` will cause build failures

## Debugging Tips

1. **Use `make qemu-nox-gdb`** for GDB debugging
2. **Check compilation errors** carefully - they usually point to missing modifications
3. **Verify system call numbers** are unique and consistent across files
4. **Test incrementally** - add print statements to verify execution flow

## Additional Resources

- [Xv6 Book](https://pdos.csail.mit.edu/6.828/2018/xv6/book-rev11.pdf) - Comprehensive guide to Xv6
- [MIT 6.828 Course](https://pdos.csail.mit.edu/6.828/) - Operating systems engineering course
- [Xv6 Source Code](https://github.com/mit-pdos/xv6-public) - Official Xv6 repository

## Conclusion

Adding system calls to xv6 is a fundamental exercise in understanding the internals of an operating system. 
The process involves creating bridges between user-space applications and kernel functionality, demonstrating the layered architecture of modern operating systems.

This implementation provides a foundation for more complex system calls that can interact with process management, file systems, and hardware resources.

**Instructor:** Hamzeh Khazaei
