Download Link: https://assignmentchef.com/product/solved-csce313-programming-assignment-6-implementing-ipc-mechanisms
<br>
<h1>Introduction</h1>

The last two programming assignments have made heavy use of the RequestChannel class, which was pre-written and given to you along with the dataserver code so that you could focus on learning synchronization techniques. In fact, it was possible to complete both PA4 and PA5 without even looking at either dataserver.cpp or reqchannel.cpp.

If you did look at them, you may have noticed that the RequestChannel class uses a mechanism called “named pipes” or “FIFOs” to communicate between the two sides of the channel. However, FIFOs are only one of several different IPC mechanisms, each of which have their own particular uses that make them suited to particular applications. In this programming assignment, we are going to expand our toolbox by learning about 2 “new” IPC mechanisms in addition to named pipes: <em>message queues </em>and <em>shared memory</em>, where the latter would in turn use <em>Kernel Semaphores</em>.

<h1>Background</h1>

<h2>Message Queues</h2>

While pipes provide a byte stream between two processes, message queues allow for the exchange of messages between processes. There are library functions (POSIX library, not STL) for message queue opening/creation, sending messages, receiving messages, closing the message queue, deleting the message queue, and modifying the message queue’s attributes. You may be able to use default attributes for this assignment, but those defaults vary by system. Visit the man pages for System V IPC (not the POSIX IPC) for how to check and

set default message queue attributes.

<h2>Shared Memory</h2>

Up until now there have been IPC mechanisms to provide byte streams and message passing, but what if something a little more versatile is needed? Shared memory is exactly what it sounds like: a segment of memory that can be read and modified (depending on its configuration) by multiple different processes. You will notice that there are no system calls for reading and writing the shared memory segment. This is because a shared memory segment is semantically identical to any other memory segment, such as can be obtained from malloc, except for the IPC and synchronization considerations. One can read and modify it using memset, strcpy, memcpy, or just about any other memory-reading/writing operations. This brings in synchronization concerns between the writer and reader, which we will have to solve using Kernel Semaphores.

1

<h2>Kernel Semaphores</h2>

You are already familiar with the concept of semaphores from PA4 and PA5, and even had to write your own. However, that semaphore was only “process-local” or “thread-shared,” since it was allocated within the address space of a given process. What if two different <strong>processes </strong>needed to synchronize on the same semaphore, a “process-shared” or kernel semaphore?

For this programming assignment, part of what you will need to do is fill in the methods of the KernelSemaphore class. KernelSemaphore implements the familiar Semaphore interface but is distinct from the process-local semaphore from PA4 and PA5.

<h1>The Assignment</h1>

<h2>Code</h2>

You have to start off of your code from PA4. We are assuming that you have a working PA4.

If that is not the case, please contact us for a working version of PA4 (if you get a working PA4 from us, you cannot later make up by submitting a late PA4). Please do not start based on PA5 because the select() at the heart of PA5 will not work with either shared memory or message queues.

You then have to make up 3 versions of your PA4 each using a separate IPC-method-based request channels: FIFO, message queue, and shared memory. Call these versions PA6 FIFO, PA6 MQ, PA6 SHM, respectively. You should have an abstract RequestChannel class and 3 sub-classes:

<ul>

 <li>FIFORequestChannel</li>

 <li>MQRequestChannel</li>

 <li>SHMRequestChannel</li>

</ul>

Here, the first one FIFOReqeustChannel would be taken directly from PA4 and you need to implement the others. The API of base RequestChannel class should be as follows:

<table width="624">

 <tbody>

  <tr>

   <td width="624">class RequestChannel { public :typedef enum {SERVER SIDE, CLIENT SIDE} Side ; typedef enum {READMODE, WRITEMODE} Mode;/* CONSTRUCTOR/DESTRUCTOR */RequestChannel (const string                  name , const Side           side );˜RequestChannel ();virtual string            cread ()=0;/* Blocking read of data from the channel. Returns a string of characters read from the channel. Returns NULL if read failed. */virtual int cwrite ( string msg)=0;/* Write the data to the channel. The function returns the number of characters written to the channel. */};</td>

  </tr>

 </tbody>

</table>

You will also need KernelSemaphore class for a properly functioning SHMRequestChannel, where the semaphore´s API should look like the following:

<table width="624">

 <tbody>

  <tr>

   <td width="624">class KernelSemaphore { private :/* INTERNAL DATA STRUCTURES */ public :/* CONSTRUCTOR/DESTRUCTOR            */KernelSemaphore (int val );˜KernelSemaphore (); // make sure to remove all allocated resourcesvoid P(); /* Acquire Lock*/void V(); /* Release Lock */};</td>

  </tr>

 </tbody>

</table>

<h2>0.1          Compiling and Running Your Code</h2>

There should be only 1 makefile to compile everything together.

Take an additional runtime argument option “-i” which should get one of:

<ul>

 <li>“f” for FIFO</li>

 <li>“q” for message queue</li>

 <li>“s” for shared memory</li>

</ul>

The following is an example command to run PA6:

./client -n &lt;requests/person&gt; -b &lt;bounded buffer size&gt;

-w &lt;number of request channels&gt; -i &lt;f|q|s&gt;

You must resolve the derived type of RequestChannel class in the runtime using polymorphism and run-time binding in C++.

<h2>0.2       Clean Up</h2>

You must clean up all IPC objects from the kernel memory and all temporary files you created for doing ftok(). Checking the /dev/mqueue and /dev/shm directories (or alternatively, running ipcs command in shell) will aid you in this clean-up process. In addition, you should clean all heap-allocated objects.

<h2>Report</h2>

<ul>

 <li>Gather timing data on the same set of <em>n,b,w </em>arguments on each of PA6 FIFO, PA6 MQ, and PA6 SHM.</li>

 <li>Present a performance comparison of the different IPC mechanisms based on this data, and attempt to provide explanation for any differences and similarities.</li>

 <li>Present the results in separate graphs using PA6 FIFO (i.e., PA4) as the baseline for comparison</li>

 <li>What is the maximum <em>w </em>and thus the max number of RequestChannels that you can use for each IPC? How much more can you go beyond the limit in PA4? (recall that the limit imposed by how many file descriptors each user can have. Now that we are not using file descriptors for MQ or SHM, has that situation changed?)</li>

 <li>What are some of the limits encountered by each class, either due to the specific implementation or to operating system limitations, and how does the program behave when it encounters them?</li>

 <li>Describe the clean-up activity you have done for each IPC</li>

</ul>


