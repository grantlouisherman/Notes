# Latency Implications of Virtual Memory

### Source: https://rigtorp.se/virtual-memory/

## Glossary

### Translation Lookaside Buffer (TLB):
- is a memory cache that stores the recent translations of virtual memory addresses to physical memory addresses. It is used to reduce the time taken to access a user memory locatio
- These mappings are maintained for pages typically of size 4 KiB, 2/4 MiB or 1 GiB.
- Operating systems use paging to map virtual addresses to physical addresses efficiently. Each process maintains its own Page Table, which contains Page Table Entries (PTEs) responsible for translating virtual page numbers into physical frame numbers. This mechanism enables flexible memory allocation and prevents processes from interfering with each other’s memory space.
- When the CPU generates a virtual address, it first looks up the page number in the **TLB**.
    - TLB Hit: It is the situation when, the page number is found in the TLB, the corresponding frame number is retrieved instantly.
    - TLB Miss: It is the situation when, the page number is not found in the TLB, the CPU accesses the page table in main memory.
- TLB is used to reduce adequate memory access time as it is a high-speed associative cache.
    - EMAT = h x (c+m) + (1-h) x (c+ n * m + p)
        - **h** is the hit ratio of the TLB
        - **m** is the memory access time
        - **c** is the TLB access time
        - **n** represents the system level
### Linux transparent huge pages (THP)
    - Computer memory is allocated to processes as pages. Usually these pages are rather small, meaning that a process consuming a lot of memory will also be consuming a lot of pages. Searching through a multitude of pages can result in system slow downs, which is why some servers can benefit from enabling huge pages.

    - Huge pages is especially useful on systems like database servers. Processes like MySQL and PostgreSQL can make use of huge pages if they are enabled, and will put less strain on your RAM cache. In this tutorial, we will cover the step by step instructions to enable huge pages on a Linux system.

### How big is a memory page?
    - A Linux memory page is typically 4 kilobytes (4KB)
    - command to check **getconf PAGESIZE**
    - [Source](https://linuxconfig.org/how-to-enable-hugepages-on-linux)

### mmap
```
 void *mmap(size_t length;
                  void addr[length], size_t length, int prot, int flags,
                  int fd, off_t offset);
```
    - system call that maps files or devices directly into a process's virtual memory, allowing direct memory access (pointers) to file contents, bypassing traditional read/write calls for efficient, demand-paged I/O, and enabling shared memory between processes for faster communication.
    - [Source](https://man7.org/linux/man-pages/man2/mmap.2.html)

## Notes
- To minimize latency by the virutal mem abstrations you should:
    - Minimize page faults by pre-faulting, locking and pre-allocating needed memory. Disable swap.
    - Reduce TLB misses by minimizing your working set memory and utilizing huge pages.
    - Prevent TLB shootdowns by not modifying your programs page tables after startup.
    - Prevent stalls due to page cache writeback by not creating file backed writable memory mappings.
    - Disable Linux transparent huge pages (THP).
    - Disable Linux kernel samepage merging (KSM).
    - Disable Linux automatic NUMA balancing.
- Page Faults:
    - When reading or writing to file backed memory that is not in the page cache or to anonymous memory that has been swapped out, the kernel must first load the data from the underlying storage device.
    - This is called a major page fault and incurs a similar overhead as issuing a read or write system call.
    - If the page is already in the page cache you will still incur a minor page fault on first access after calling **mmap**, during which the page table is updated to point to the correct page
    - To avoid page faults you can pre-fault and disable page cache eviction of the needed memory using the mlock system call or the MAP_LOCKED and MAP_POPULATE flags to mmap.
    - You can also disable swap system wide to prevent anonymous memory from being swapped to disk
    ```
    You can monitor number of page faults using

    ps -eo min_flt,maj_flt,cmd
    or

    perf stat -e faults,minor-faults,major-faults
    ```
- TLB misses
    - The TLB has a limited number of entries and if a address is not found in the TLB or STLB, the page table data in the CPU caches or main memory needs to be referenced, this is called a **TLB miss**
    - You can minimize TLB misses by reducing your working set size, making sure to pack your data into as few pages as possible.
    - Additionally you can utilize larger page sizes than the default 4 KiB.
    - These larger pages are called huge pages7 and allows you to reference more data using fewer pages.
    ```
    TLB usage can be monitored using:

    perf stat -e dTLB-loads,dTLB-load-misses,iTLB-loads,iTLB-load-misses
    ```
- TLB shootdowns
    - Most processors do not provide coherence guarantees for TLB mappings. Instead the kernel provides this guarantee using a mechanism called a **TLB shootdown**.
    - It operates by sending inter-processor interrupts (IPIs) that runs kernel code to invalidate the stale TLB entries
    - TLB shootdowns causes each affected core to context switch into the kernel and thus causes latency spikes for the process running on the affected cores.
    - It will also cause TLB misses when a address with an invalidated page table entry is subsequently accessed.
    - Essentially the kernel clears out the buffer/cache every so often and it causes a slowdown
