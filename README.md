# Strace

The powerful yet underrated tool for Linux Troubleshooting.

In the world of Linux troubleshooting, there are many tools at your disposal, but some hidden gems can be your best friend. strace is one such gem. It acts like a translator, deciphering the conversations between a program and the operating system’s core (the kernel). This unveils what’s happening behind the scenes, helping you diagnose problems with file access, network connections etc and how your system uses resources.


# How to Use strace:

Using strace is straightforward. Simply replace <command> with the program you want to trace:

~~~
# strace <command>
~~~

For example, to unveil the system calls made by # ls /etc , use

~~~
# strace ls /etc
~~~

Example: Diagnosing File Permissions:

Imagine a program crashes while writing to a file. Running strace might reveal a line similar to :

~~~
write(3, "This is some data\n", 17) = -1 EACCES (Permission denied)
This pinpoints a permission issue, indicating the program (file descriptor 3 in this case) lacks the necessary write permissions for the target file.

Here’s a glimpse into what strace output might look like when listing the contents of the /etc directory:

access("/etc/passwd", F_OK) = 0  // Checks if file exists

open("/etc/passwd", O_RDONLY) = 3 // Opens the file for reading

getdents(3, /*中略*/, 2048) = 72 // Reads directory entries

... (repeated for other files in /etc)

close(3) = 0 // Closes the directory

exit_group(0) = 0 // Program exits successfully
~~~

# Understanding strace output:

At first glance, strace’s output might seem complex. But focus on key parts to get the big picture. Here are some common conversations (system calls) to watch for:

# File System Calls:

~~~
open(): This initiates the opening of a file for various operations (reading, writing, etc.). Errors here can indicate permission issues or incorrect file paths.

read(): This retrieves data from an opened file. Issues like “end-of-file” or unexpected read sizes can surface here.

write(): This writes data to an opened file. Errors like “permission denied” or “disk full” can be exposed through write() failures.

close(): This closes a previously opened file, ensuring proper resource management. Errors here can lead to resource leaks.

Network System Calls:

socket(): This creates a socket, the endpoint for network communication. Issues here can indicate problems with network protocols or socket types.

connect(): This attempts to establish a connection to a remote server on a specific port. Errors like “connection refused” or “connection timed out” can pinpoint network connectivity issues.

send() or sendto(): These calls are used to transmit data over a network socket. Errors here can indicate problems with data transmission or network congestion.

recv() or recvfrom(): These calls are used to receive data over a network socket. Errors like “connection reset by peer” or “socket error” can shed light on network communication problems.
~~~

# Process Management System Calls:

_fork(): This creates a new child process, replicating the parent process. Errors here can indicate resource limitations or issues with process creation._

_execve(): This executes a new program within an existing process. Errors here can point to problems with the executable file or missing dependencies._

_wait(): This allows a parent process to wait for the termination of its child process. Errors here can reveal issues with child process termination or synchronization._

# Beyond the Basics:

Let’s break down a more comprehensive strace command incorporating some advanced options:

~~~
# strace -Ttfxvo /tmp/test.strace -dd -s 256 ls /etc
~~~

_T: Show the time spent in system calls.This records the time difference between the beginning and the end of each system call._

_t: Prefix each line of the trace with the wall clock time._

_f: Trace child processes as they are created by currently traced processes, in short it shows forked processes._

_x: Print all non-ASCII strings in hexadecimal string format._

_v: Display verbose output, including dereferenced argument values._

_o /tmp/test.strace: Redirect strace output to the file /tmp/test.strace._

_-dd: Enable additional debugging information for strace itself._

_-s 256: Allocate 256 string size to print._

_ls /etc: The command you want to trace (listing the contents of /etc)._

_For more information and all the available arguments please check its man page `#man strace`_


# Use Cases for strace:

_Diagnosing File Permissions: Imagine a program crashes while writing to a file. Running strace might reveal a write() system call failing with “permission denied” error, indicating the program lacks the necessary write permissions._

_Debugging Network Issues: A program might be failing to connect to a web server. strace can help identify issues like connection timeouts or errors during data transmission._

_Uncovering Memory Leaks: If a program’s memory usage keeps increasing, strace can help pinpoint excessive malloc() or calloc() calls, indicating potential memory leaks._

_Understanding Complex Programs: By tracing system calls, you can gain insights into how a program interacts with the system, interacts with files and network resources, and performs various operations._

_Troubleshooting Security Issues: strace can be used to analyze the system calls made by suspicious programs, potentially revealing unauthorized file access or network activity._
