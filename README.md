# Shared Memory
Illustration of shared memory on linux for inter process communication

One of the most powerful IPC techniques is outlined here in a very basic example. There is
sufficient code to demonstrate how strings (literals), integers, double and char types can be written
to a shared memory segment on a server process. The same shared memory is read from a separate client process obviously running on same host. 

The demo serves as a starting point, other forms of IPC will be added to various repositories.

This example uses cmake with;

**OS:** Ubuntu, Linux DESKTOP-2F28HHA 5.15.146.1-microsoft-standard-WSL2)\

**Compiler:** g++ version g++ 13.1.0. (with --std=c++23 set).



# Overview:

Shared memory is exactly as its name suggests. A segment of memory can be created by a server say, and then attached to by any number of processes. Once processes have attached to the same shared memory segment, they can write to and read from that memory in a highly efficient manner. 

There are caveats however. The manner in which memory is managed (written to/read from) is completely under the control of the developer. You get no shared data synchronistion for free, race conditions can happen, you manage your pointers; all memory management is up to you. Overwriting memory locations using incorrect pointer manipulation will cause debugging headaches. In summary, in its rawest form, shared memory gives you zero scope for error. 

In subsequent examples, I will provide illustrations of how shared memory can be used in conjunction with bounded ring buffers, semaphores and other synchronistion constructs to provide a powerful IPC mechanism. .


The following diagram illustrates best;

<img src="https://github.com/grahamers/shared_memory/assets/19392728/6a5c003d-a0fa-4bcb-9600-f3917eb57e7d" alt="image" width="500" height="250">


This demo will write to and read from shared memory (the block in blue, shared between the 2 processes above).  It will illustrate how pointer manipulation can dictate *exactly* where data is written to/read from. We'll see some interesting details regarding alignment. For debugging, 'gdb' with text interface (-tui)  will illustrate both the memory location & contents of shared memory.

The server will use shared memory to communicate data with a client using a contrived example, specifically;


"PI is defined as: 22/7==3.14286

goodbye" 

to a client, where the above is expressed (written/read) as; 

Breaking this down we have

![image](https://github.com/grahamers/IPC-shared_memory/assets/19392728/9bde6da5-d0f5-4ff3-a03d-b4b5d602b808)



There are 2 processes, server and client:

## Server

Create the shareed memory segment, attach to it and write data. At shutdown the memory is released back to the kernel. 

In summary, the linux API's used are as follows;

key_t ftok(const char *pathname, int proj_id);

This creates an IPC key_t which can be compared to a FD for shared memory.

int shmget(key_t key, size_t size, int shmflg);

Using the key created via ftok, get an identifier for the shared memory we will be creating/using.
Specify the size in bytes and flags such as IPC_CREATE (create segment if if doesnt exit),
IPC_EXCL (fail if segment already exists). 


Finally we attach to the newly created segments and get our hands on a void* pointer through which we can
more or less manipulate as we want.

 void *shmat(int shmid, const void *shmaddr, int shmflg);

 Using the identifier obtained via shmget, attach to memory at an optional specify a start address (otherwise nullptr for kernel
 assigned) and set of flags 'shmflg' which apply some offset logic to shmaddr if it was specified. 


![image](https://github.com/grahamers/IPC-shared-memory-segment/assets/19392728/1f0cc5d5-b008-4aaa-864c-c0f302025da8)



We can examine details of the newly created segment using the 'ipcs' command. In the output below
we can see a segment created by postgres as well as my server;

$ **ipcs -m**

------ Shared Memory Segments --------\
key        shmid      owner      perms      bytes      nattch     status\
0x00030471 0          postgres   600        56         6\
**0x0f20074e 7          gwalsh     666        1024       1**\


At shutdown, the server will detach and release the shared memory using;

int shmdt(const void *shmaddr);\
int shmctl(int shmid, int cmd, struct shmid_ds *buf);\



## Client


On the client side we will attach to segment created in server

![image](https://github.com/grahamers/IPC-shared-memory-segment/assets/19392728/43d606d8-d3b7-4d31-b2de-4e736dc60f54)


#Memory access in client and server.

The grid above illustrates the type and size of each of the datatypes that will be written server side and read from client side.
Running the client and serverk we see;

When the server side shared memory is setup we have 1024 bytes available, initialised to 0
via the call to memset in the server. 


## Memory layout
At this point we have a shared memory segment setup between client and server, zero initialised. It can be visualised
as;

![image](https://github.com/grahamers/IPC-shared-memory-segment/assets/19392728/7d5d7021-4ddb-48db-b69c-eb241f936792)




REFERENCES:

https://www.ibm.com/docs/en/ztpf/2020?topic=apis-shmgetallocate-shared-memory
