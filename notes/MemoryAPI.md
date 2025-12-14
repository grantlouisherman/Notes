## Memory API - Virtualization Of Memory

## Types of memory
- The first is called **Stack** memory and allocations and deallocations of it are managed implicitly by the compiler. It is sometimes called **automatic memory**
    - An example could be:
    ```
    void func(){
        int x; -> this declares an intger on the stack
    }

    ```
- The **heap memory** is where all allocations and dallocations are explicitly handled by the programmer:
    - An example could be:
    ```
    void func(){
    int* x = (int*)malloc(sizeof(int));
    }
    ```
## Address Space
- Abstraction of physical memory is called **Address Space**
- **Address Space** is the running programs view of memory in the system
- The address space of a process contrains all of the memory state of the running programs
- While its running the program uses:
    - **Stack** - to keep track of where is is in the function call chain as well as allocate local variables and pass parameters and return values to and from routines
    - **Heap** - used for dynamically allocated, user-managed memory, such as that you might receive from a call to **malloc** in C or **new** in a C++/Java object


