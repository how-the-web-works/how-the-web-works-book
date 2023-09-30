---
title: Hello Process
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
