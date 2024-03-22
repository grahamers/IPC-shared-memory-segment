# shared_memory
Illustration of shared memory on linux for inter process communication

One of the most powerful IPC techniques is illustrated here in a very basic example. There is
sufficient code to illustrate how strings (literals), integers, double and chars types can be written
to a shared memory segment on a server process. The same shared memory is read from client process
obviously running on same host. 

The demo serves as a starting point, other forms of IPC will be added to various repositories
however this example provides a cmake based linux (Linux DESKTOP-2F28HHA 5.15.146.1-microsoft-standard-WSL2)
example built on g++ version g++ 13.1.0.



Overview:

Shared memory is exactly as its name suggests. A segment of memory can be created by a server say, and then attached to by any number of processes. Once processes have attached to the same shared memory segment, they can write to and read from that memory in a highly efficient manner. 

There are caveats however. The manner in which memory is managed (written to/read from) is completely under the control of the developer. You get no shared data synchronistion, race conditions can happen, you manage your pointers; all memory management is up to you. Overwriting memory locations using incorrect pointer manipulation will cause debugging headaches. In summary, in its rawest form, shared memory gives you zero scope for error. 

In subsequent examples, I will provide examples, illustrating how shared memory can be used in conjunction with bounded ring buffers to provide a powerful IPC mechanism. .


The following diagram illustrates best;

<img src="https://github.com/grahamers/shared_memory/assets/19392728/6a5c003d-a0fa-4bcb-9600-f3917eb57e7d" alt="image" width="500" height="250">


This demo will write to and read from shared memory (the block in blue, shared between the 2 processes above).  It will illustrate how pointer manipulation can dictate *exactly* where data is written to/read from. We'll see some interesting details regarding alignment. For debugging, 'gdb' with text interface (-tui)  will illustrate both the memory location & contents of shared memory.

The server will use shared memory to communicate a contrived example, specifically;


"PI is defined as: 22/7==3.14286

goodbye" 

to a client, where the above is expressed (written/read) as; 

Breaking this down we have

![image](https://github.com/grahamers/IPC-shared_memory/assets/19392728/9bde6da5-d0f5-4ff3-a03d-b4b5d602b808)







REFERENCES:

https://www.ibm.com/docs/en/ztpf/2020?topic=apis-shmgetallocate-shared-memory
