# PUAF-POC

A proof-of-concept &amp; explanation of Physical Use after Free class bugs, which allow for kernel exploitation.

# XNU: Memory Management

XNU is the kernel used by iOS, MacOS & other Apple operating systems. The kernel is responsible for a lot of things, but one of the most important things is memory management.

**Virtual Memory**

Processes in the OS, including the kernel, contains a virtual memory map. An executable file will define what we call a base address of the binary. 


If a MachO binary specifies a base address of 0x1000050000, then the memory will be allocated to the process and beyond.


This type of system won't work on phsyical memory. If two proccesses request the same base address, problems will occur.

**Physical Memory**

All physical memory begins at an address within a region of 0x800000000. Virtual Memory appears continous to a process. By continous, this means one single mapping of memory where each page is mapped consecutively.


Memory is divided into equally-sized pages. On newer devices, page sizes are 16KB or 4KB on older devices.

# Page Tables

Page Tables are a list of 64-bit addresses about the memory pages avaliable to a process. Userland processes address space is from 0x0 to 0x800000000. The translation between the physical and virtual memory address space is handled by the memory management unit (MMU). When a process trys to deference a pointer called 0x1000000000, MMU will need to searchc the corresponding page table for the address.

Page Tables contain three levels: Level 1, Level 2 & Level 3.

**Level 1:** Covers `0x1000000000` bytes of virtual memory

**Level 2:** Covers `0x2000000` bytes of virtual memory

**Level 3:** Covers `0x4000` bytes of memory

# Physical Use After Free

When a userland process requests virtual memory as readable and writeable, all page tables are updated to map in the corresponding physical address as r/w. The logic issue here is when the process attempts to deallocate the memory from userland, the kernel does not remove the mapping from the page tables, but instead removes it from the VM layer. The kernel's VM layer then thinks the corresponding physical pages are free to use, and adds addresses of the pages to a global free list. As a result, the process can r/w to some pages that can be reused by the kernel as kernel memory.


This PUAF-class bug is powerful. Modifying kernel memory from userspace is difficult. But, simply from this, we have not actually obtained powerful kernel r/w primitives.

# Exploitation Time

There are several exploits that can trigger a PUAF. 
Because the exploit only writes to certain pages, we have to land on a page that can we actually write to and that will be useful.


There are lots of ways to do this but I won't go over any of them.


At this point, by building the primitives to achieve kernel r/w, PUAF is a powerful-class bug, which resulted in several vulnerabilites as a result of it.

# Testing it yourself

ill write later

# Credits

[alfiecg24](https://github.com/alfiecg24) - Written the original explaination of PUAF. (tweaked his words to make it sound easier (hopefully))



