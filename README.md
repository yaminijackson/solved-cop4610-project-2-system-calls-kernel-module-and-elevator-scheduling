Download Link: https://assignmentchef.com/product/solved-cop4610-project-2-system-calls-kernel-module-and-elevator-scheduling
<br>
This project introduces you to the nuts and bolts of system calls, kernel programming, concurrency, and synchronization in the kernel. It is divided into three parts.

<h1>Restrictions</h1>

C programming language

Linux kernel version 4.14.12 <strong>Part 1: System-call Tracing</strong>

Write a program that uses exactly ten system calls. You will not receive points if the program contains more or fewer than ten. The system calls available to your machine can be found within

/usr/include/unistd.h. Further, you can use the command line tool, strace, to intercept and record the system calls called by a process.

Once you’ve written this program, execute the following commands:

$ gcc -o part1.x part1.c

$ strace -o log part1.x

Look at the log file to figure out how many system calls your program is calling, Notice that the outputs may differ if you run strace more than once on the same program. To reduce the length of the output from strace, try to minimize the use of other function calls (e.g. stdlib.h) in your program.

<h1>Part 2: Kernel Module</h1>

In Unix-like operating systems, time is sometimes specified to be the seconds since the Unix Epoch

(January 1st, 1970). You will create a kernel module called my_xtime that calls

current_kernel_time() and stores the value. current_kernel_time() holds the number of seconds and nanoseconds since the Epoch.

When my_xtime is loaded (using insmod), it should create a proc entry called /proc/timed. When my_xtime is unloaded (using rmmod), /proc/timed should be removed.

On each read you will use the proc interface to both print the current time as well as the amount of time that’s passed since the last call (if valid).

Example usage:

$ cat /proc/timed

current time: 1234567.000111222

$ sleep 5

$ cat /proc/timed

current time: 1234572.456123987

elapsed time: 5.456012765

$ sleep 3

$ cat /proc/timed

current time: 1234575.544212005 elapsed time: 3.088088018 <strong>Part 3: Elevator Scheduler</strong>

Your task is to implement a scheduling algorithm for a hotel elevator. Your elevator must track the number of passengers and the total weight. Elevator load consists of five types of people: adults, children, bellhops, and room service:

<ul>

 <li>An adult counts as 1 passenger unit and 1 weight unit</li>

 <li>A child counts as 1 passenger unit and ½ weight unit</li>

 <li>Room service counts as 2 passenger units and 2 weight units</li>

 <li>A bellhop counts as 2 passengers unit and 3 weight unit</li>

</ul>

Passengers will appear on a floor of their choosing and always know where they wish to go. You can assume most of the time when a passenger is on a floor other than the first, they will choose to go to the first floor. Passengers board the elevator in FIFO order. If a passenger can fit, the elevator must accept them unless the elevator is moving in the opposite direction from where they wish to go. Once they board the elevator, they may only get off when the elevator arrives at the destination. Passengers will wait on floors indefinitely.

<h2>Step 1: Kernel Module with an Elevator</h2>

Develop a representation of an elevator. In this project, you will be required to support having a maximum load of 15 weight units and 10 passenger units (neither can be exceeded at any point). The elevator must wait for 1.0 seconds when moving between floors, and it must wait for 0.5 seconds while loading/unloading passengers. The building has floor 1 being the minimum floor number and floor 10 being the maximum floor number. New passengers can arrive at any time and each floor needs to support an arbitrary number of them.

<h2>Step 2: /Proc</h2>

The module must provide a proc entry named /proc/elevator. Here, you will need to print the following (each labeled appropriately):

<ul>

 <li>The elevator’s movement state:</li>

</ul>

◦ IDLE: elevator is stopped on a floor because there are no more passengers to service

◦ LOADING: elevator is stopped on a floor to load and unload passengers

◦ UP: elevator is moving from a lower floor to a higher floor

◦ DOWN: elevator is moving from a higher floor to a lower floor

<ul>

 <li>The current floor the elevator is on</li>

 <li>The next floor the elevator intends to service</li>

 <li>The elevator’s current load (in terms of both passengers units and weight units)</li>

</ul>

You will also need to print the following for each floor of the building:

<ul>

 <li>The load of the waiting passengers</li>

 <li>The total number of passengers that have been serviced</li>

</ul>

<h2>Step 3: Add System Calls</h2>

Once you have a kernel module, you must modify the kernel by adding three system calls. These calls will be used by a user-space application to control your elevator and create passengers. You need to assign the system calls the following numbers:

<ul>

 <li>323 for start_elevator()</li>

 <li>324 for issue_request()</li>

 <li>325 for stop_elevator()</li>

</ul>

int start_elevator(void)

Description: Activates the elevator for service. From that point onward, the elevator exists and will begin to service requests. This system call will return 1 if the elevator is already active, and 0 for a successful elevator start. Initialize an elevator as follows:

<ul>

 <li>Direction: IDLE</li>

 <li>Current floor: 1</li>

 <li>Current load: 0 passenger units, 0 weight units</li>

</ul>

int issue_request(int passenger_type,                   int start_floor,                   int destination_floor)

Description: Creates a passenger of type passenger_type at start_floor that wishes to go to destination_floor. This function returns 1 if the request is not valid (one of the variables is out of range), and 0 otherwise. A passenger type can be translated to an int as follows:

<ul>

 <li>Adult = 0</li>

 <li>Child = 1</li>

 <li>Room service = 2 • Bellhop = 3</li>

</ul>

int stop_elevator(void)

Description: Deactivates the elevator. At this point, this elevator will process no more new requests (that is passengers waiting on floors). However, before an elevator ceases to exists, it must offload all of its current passengers. Only after the elevator is empty may it be deactivated. This function returns 1 if the elevator is already in the process of deactivating, and 0 otherwise.

<h2>Step 4: Test</h2>

Once you’ve implemented your system calls, you must interact with two provided user-space applications that will allow communication with your kernel module.

<strong>producer.c</strong>

This program will issue N random requests, specified by input.

<strong>consumer.c &lt;–start | –stop&gt;</strong>

This program expects one flag and on argument:

<ul>

 <li>If the flag is –start, then the program must start the elevator • If the flag is –stop, then the program must stop the elevator <strong>producer.c</strong> and <strong>consumer.c</strong> will be provided to you.</li>

</ul>

<h1>Extra Credit</h1>

The top five elevator schedulers will receive +5 points to their project 2 grade. The metric to optimize is the total number of passengers serviced. The next five schedulers will receive +2 points to their project 2 grade.

<h1>Project Submission Procedure</h1>

You will be required to schedule for a project demonstration. A week before the project is due, registration for the demonstration will open. You will be given 20 minutes to present your project. This demonstration will take place on the lab machine assigned to you. You will be required to demonstrate:

<ul>

 <li>Project source files</li>

</ul>

◦ System calls, module sources, user-space sources

<ul>

 <li>Project makefiles</li>

 <li>Successful run of Part 1</li>

 <li>Successful installation of Part 2</li>

 <li>Properly display xtime /proc/timed</li>

 <li>Successful remove of Part 2</li>

 <li>Successful installation of Part 3</li>

 <li>Execution of an arbitrary number of consumers and producers for 5 minutes</li>

 <li>Information stored in /proc/elevator once per second</li>

 <li>Successful removal of Part 3</li>

</ul>

Any demonstration failing to install a portion of the project successfully during their allotted time will receive a 0 for that portion of the project. Be absolutely sure that you can at least install and remove all kernel modules before attempting to demonstrate. As before, any project that fails to compile will also receive a 0 for that portion of the project.

The grader, may also choose to question you on project components. You should be able to demonstrate an understanding of the project implementation.