# vDSO-tools
Tools for analyzing and modifying a binary's vDSO section

This repository is going to start off as some research into the linux vDSO(virtual dynamic shared object) implemented in linux binaries
because I think the fact that there is a section of code which maps code from the kernel space into userspace for functions which are
common use during runtime is pretty interesting from more a security and engineering standpoint.

It starts off with a half finished rant I started with myself a few months ago and I'd like to turn it into some automated tools that
will help everyone get map to functions they know they shouldn't be allowed to and for some... to the functions that mayber their 
embeded system can save time and energy where the NEED to.
