# VDSO patching for make do more user kernel be more like

 good reasons to do this.
 it gives you some persistence in a way that it will give you access to kernel space from applications that map this ABI specific vDSO
 it's fucking cool to do
 it will get you thinking about other things

 `#include <sys/auxv.h>
 `void *vdso = (uintptr_t) getauxval(AT_SYSINFO_EHDR);

 The "vDSO" (virtual dynamic shared object) is a small shared library
 that the kernel automatically maps into the address space of all
 user-space applications.

 the C library will take care of using any functionality that is available via the vDSO.

 There are some system calls the kernel provides that user-space code ends up using frequently, to the
 point that such calls can dominate overall performance. mainly because of how much overhead is involved
 in a context switch

 the vDSO function used to determine the preferred method of making a system call is named "__kernel_vsyscall"

 # vDSO duck punch primer
 maybe we gotta get time of day info for our app but don't wanna make the context switch for every call that requires
 a time of day query.

 The base address of the vDSO (if one exists) is passed by the kernel to each program in the initial auxiliary
 vector (see getauxval(3)), via the AT_SYSINFO_EHDR tag.

 the auxiliary vector is a mechanism that the kernel's ELF loader uses to pass information to user space when
 a program is executed.


                 Relevant vDSO tags to look at
 AT_SYSINFO
               The entry point to the system call function in the vDSO.  Not
               present/needed on all architectures (e.g., absent on x86-64).

 AT_SYSINFO_EHDR
               The address of a page containing the virtual Dynamic Shared
               Object (vDSO) that the kernel creates in order to provide fast
               implementations of certain system calls.

 the location of the vDSO varies as it is randomized, don't be a fool by trying to anticipate it's location.

 vDSO is a fully formed ELF image, you can do symbol lookups on it.  This allows new symbols to be added with newer kernel
 releases, and allows the C library to detect available functionality at run time when running under different kernel versions.
 Oftentimes the C library will do detection with the first call and then cache the result for subsequent calls.


 Typically the vDSO follows the naming convention of prefixing all symbols with "__vdso_" or "__kernel_" For example,
 the "gettimeofday" function is named "__vdso_gettimeofday".

 !!PSA!!
 Note that the vDSO that is used is based on the ABI of your user-
        space code and not the ABI of the kernel.  what i mean is... when
        you compile an i386 32-bit ELF binary, you'll get the same vDSO
        regardless of whether you run it under an i386 32-bit kernel or under
        an x86_64 64-bit kernel.



 x86_64 functions
        The table below lists the symbols exported by the vDSO.  All of these
        symbols are also available without the "__vdso_" prefix, but you
        should ignore those and stick to the names below.

         Our goal is to monkey pactch some functions into the VDSO that gets loaded into the user space of
         the app we are messing with (remember that it relies on the user space ABI like I said earlier)



        symbol                 version
        ---------------------------------
       __vdso_clock_gettime   LINUX_2.6
        __vdso_getcpu          LINUX_2.6
        __vdso_gettimeofday    LINUX_2.6
        __vdso_time            LINUX_2.6


