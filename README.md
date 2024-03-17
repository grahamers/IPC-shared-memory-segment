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

Shared memory is exactly as its name suggests. A segment of memory can be created and attached to by 
any number of processes. Once the memory has been attached to processes can write to and read from
that memory in a highly efficient manner. There are caveats however. The manner in which memory is
managed (written to/read from) is completely under the control of the developer. There is ZERO scope 
for error. Overwriting memory locations using incorrect pointer manipulation will cause debugging
headaches. The same applies for reading shared memory. 

The followung diagram illustrates best;


![image](https://github.com/grahamers/shared_memory/assets/19392728/6a5c003d-a0fa-4bcb-9600-f3917eb57e7d)

This demo will write the following to shared memory;

"PI is defined as: 22/7==3.14286" 

where this is broken down into the following parts; 

"PI is defined as: "&nbsp;&nbsp;&nbsp;- char* (null terminated)\
22 &emsp; &emsp; &emsp; &emsp; &emsp;  &emsp;- integer\
'/'  &emsp; &emsp; &emsp; &emsp; &emsp;  &emsp;&nbsp;- char\
7 &emsp; &emsp; &emsp; &emsp; &emsp;  &emsp;&nbsp;&nbsp;- integer\
'='  &emsp; &emsp; &emsp; &emsp; &emsp;  &emsp;- char\
'='  &emsp; &emsp; &emsp; &emsp; &emsp;  &emsp;- char\
22/7.0&emsp; &emsp; &emsp; &emsp;  &emsp;- double (3.14286....)

Breaking this down we have,

19 chars l, "PI is defined as: "
int, 4 bytes 7
char, 1 byte '/'
int, 4 bytes 22
char, 1 byte '='
char, 1 byte '='
double 8 bytes 3.14157...





REFERENCES:

https://www.ibm.com/docs/en/ztpf/2020?topic=apis-shmgetallocate-shared-memory
