## Chapter 1. Welcome to Docker

Docker is an open source project for building, shipping and running software. It is a command-line programming, a background process, and a set off remote services that take a logistical approach to solving common software problems and simplify your experience installing, running, publishing and removing software. It accomplishes this by using an operating system technology called containers.

### 1.1.2 Containers

Historically, UNIX-style operating systems used the term jail to describe a modified runtime environment that limits the scope of resources that a jailed program can access. Now the preferred term is <span style="color:#CB0000">containers</span>.

The goal was expanded from limiting file system scope to isolating a process from all resources except explicitly allowed.

### 1.1.3 Containers are not virtualization.

<span style="color:#CB0000">Virtual Machines </span> provide virtual hardware or hardware on which an operating system and another programs can be installed. They take a long time to create and require significant resources.

<span style = "color: #CB0000"> Docker containers </span> don’t use any hardware virtualization. Containers are an operating system feature.

Docker doesn’t provide the container technology, but it specifically make it simpler to use.
![[Pasted image 20221213181446.png]]

A basic computer stack running two program that were started from CLI.

<span style="color: #CB0000"> CLI </span> - Command-line interface

Running Docker means running two programs in user space.

-   Docker Engine: that should always be running.
-   Docker CLI: that controls the engine by interface.

A running process is a child of the Docker Engine, wrapped with a container, and the delegate process is running in its own memory sub space of the user space.

The 10 major system features that Docker uses to build containers are:
<ul> 
	<li> <span style = "color: #CB0000"> PID  namespace</span>: process identifiers and capabilities; </li>
	<li> <span style = "color: #CB0000"> UTS namespace</span> : host and domain name; </li>
	<li> <span style = "color: #CB0000"> MNT </span>: file system access and structure; </li>
	<li> <span style = "color: #CB0000"> IPC namespace </span>: process communication over shared memory; </li>
	<li> <span style = "color: #CB0000"> NET namespace </span>: network access and structure; </li> 
	<li> <span style = "color: #CB0000"> chroot syscall </span>: controls the location of the file system root; </li>
	<li> <span style = "color: #CB0000"> cgroup </span>: resource protection; </li>
	<li> <span style = "color: #CB0000"> CAP drop </span>: operating system feature restrictions; </li>
	<li> <span style = "color: #CB0000"> Security Modules </span>: mandatory access controls. </li>
</ul>
A docker image is a bundle snapshot of all files that should be available to a program running inside a container. You can create as many containers from an image as you want. But when you do, containers that were started from the same image don’t share changes to their filesystem. When you distribute these images, the receiving computers creates containers from them.