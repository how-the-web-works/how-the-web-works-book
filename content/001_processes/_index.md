---
title: Processes
weigh: 1
---

- [ ] Show and talk about the layers of code here
- [ ] Explain how the operating system abstracts away the processor and code that gets send there
- [ ] Explain how the language runtime abstract away the intricacies of the operating system and the difference between the languages 

When you start your computer, digital circuits initialize all the hardware, register all the peripherals and then load a special piece of software that will bootstrap the operating system.
The operating system itself has the kernel, that is responsible to manage access to peripherals, CPU and memory.
Then, it also has processes that have multiple different responsibilities.
On Linux for example there's the init process that serves as the parent for all the other processes.

But what is a process?
A process is an instance of a program.
That means that you can write a computer program and then ask the operating system to run multiple instances of it.
A process can be as simple as a “Hello World!” type of application or it can be as complex as a browser, a database or a web server.

In Unix systems, you have tools to analyze a process, check how much memory and CPU it is using, for how long it has been running, etc.
Processes can also be controlled by signals that the operating system sends to it.
There are different ways to send signals to your process.
If it's attached to standard input, you can send signals using CTRL + (command) for that.
You can also use the kill command to send signals to processes.
