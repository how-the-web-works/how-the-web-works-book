---
title: Hello Process
weight: 100
---

To illustrate this, let's write an application that generates random numbers and prints them to standard output in an infinite loop until it receives a SIGTERM signal. SIGTERM means that the application should terminate.
In Java, the following is an infinite loop that will print a random number and then sleep for a second:

```java
Random random = new Random();
while (true) { // Infinite loop
  int next = random.nextInt(1000);
  System.out.printf("Next random number was: %d\n", next);
  Thread.sleep(1000);
}
```
- [ ] link to code

In Java, there’s no way to catch specific signals sent to the process. These are captured by the JVM which decides what to do. The only thing you can do is run a piece of code if the process is terminating. To do that, you register a shutdown hook thread like the following:

```java
Runtime.getRuntime().addShutdownHook(new Thread() {
  @Override
  public void run() {
    System.out.println("Process is shutting down...");
  }
});
```
- [ ] link to code

## Running your application

- [ ] show how to start the application

Now that you have your application code ready, you can run it and start playing with it. If you run the code as described above, you’ll see some output like this:

```shell
$ ./run.sh chapter-01/random_printer/
Next random number was: 487
Next random number was: 715
Next random number was: 9
Next random number was: 563
Next random number was: 439
Next random number was: 534
...
```

## Terminating your application

The application is running and printing random numbers. If you, form the same terminal you launched your application from, press CTRL+C, you’ll see the following output:

```text
Next random number was: 61
Next random number was: 789
Next random number was: 47
^CProcess is shutting down...
```

The “^C” represents the moment that I pressed CTRL+C. When you do this, the process receives a SIGTERM signal, which is a polite ask from the system to the process to start shutting itself down. In Java, that means the JVM captured the signal and starts to execute all the registered shutdown hooks. That explains the “Process is shutting down...” text printed just before the process finishes and control returns to the user.
Another way to send a signal to your process is to use the kill shell builtin. To use kill you need to know the process ID, also known as the PID. To find the PID of a process you can use the process status (ps) shell builtin. Since the process blocks your terminal, you can start a second terminal session and run ps to find the processes started by your user. Here is a sample output of process status in my computer (IDs will be different from what you see):

```shell
$ ps
  PID TTY           TIME CMD
 1617 ttys002    0:00.01 /bin/bash ./run.sh chapter-01/random_printer/
 1632 ttys002    0:00.29 /usr/bin/java -classpath /Users/visola/git/how-th
98500 ttys002    0:00.23 /bin/bash -l
  576 ttys003    0:00.09 /bin/bash -l
```

There are four processes listed:
- 2 x `/bin/bash` - my terminal sessions
- `/bin/bash ./run.sh` - the script that is running my process
- `/usr/bin/java` - the JVM running my code

The first column is the PID, the unique identifier for my processes. To send the SIGTERM signal to your process using kill, you can run kill ${PID}. Kill is silent and won’t tell you anything:

```shell
$ kill 1632
```

But in your other terminal session, the one that was running your application, now you’ll see the following:

```text
Next random number was: 929
Next random number was: 191
Process is shutting down...
```

As before, the JVM received the SIGTERM signal and ran the shutdown hook.

Sometimes, processes get stuck executing a shutdown hook and don’t terminate properly. The following is a sample shutdown hook that would get a process stuck:

```java
Runtime.getRuntime().addShutdownHook(new Thread() {
  @Override
  public void run() {
    System.out.println("Process is shutting down...");
    try {
      while (true) {
        System.out.println("I'm not going to die...");
        Thread.sleep(500);
      }
    } catch (Exception e) {
      // Ignore exception
    }
  }
});

```
- [ ] link to code

Running the above process and then pressing CTRL+C (or sending a SIGTERM using kill) would result in the following output:

```text
Next random number was: 366
Next random number was: 36
Process is shutting down...
I'm not going to die...
I'm not going to die...
Next random number was: 173
I'm not going to die...
I'm not going to die...
Next random number was: 699
I'm not going to die...
...
```

As you can see, the process refuses to terminate. For cases like these, there’s a special signal that can be sent to immediately terminate it. You can use kill -9 ${PID} to send a SIGKILL. With that, you can see that the JVM again intercepts the signal and then force terminate your process. The output looks like the following:

```text
...
Next random number was: 536
I'm not going to die...
I'm not going to die...
Killed: 9
```

“Killed: 9” comes from the JVM hard stopping all your threads and exiting.

- [ ] Research how similar behavior can be achieved in Windows
- [ ] Show how to analyze a process
