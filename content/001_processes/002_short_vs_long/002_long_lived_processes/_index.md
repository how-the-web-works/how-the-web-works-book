---
title: Long Lived Processes
weight: 200
---

As opposed to short or one-off processes, long running processes are designed to run forever.
Normally processes of this type are built to live while a user, or a group of users, require some sort of functionality.
A good example of a long lived process is a spreadsheet application.
The user opens the application, uses it for an extended period of time and when he’s done, closes it.
Applications with user interfaces are long lived processes but there’s another kind of long lived process that we use everyday and are mostly unaware of: web servers.
These are the building blocks of the internet and provide content to the users.
We’ll talk about web servers in much more detail in the upcoming sections.

{{<
  figure
  src="002_users_interacting_with_process.png"
  caption="Illustration of a long running process that is interacting with two users."
>}}

The illustration above depicts two users interacting with a long running process.
This interaction could happen in different ways, depending on many different factors: data in and out, action the user is trying to achieve, etc.

What makes a long running process so different?
Normally their main method contains some kind of infinite loop that keeps it running until some exit condition is met:
the user clicked the exit button, picked the exit option or it received a terminate signal.

What makes long running processes really interesting is that they can hold optimizations and respond to inputs much faster.
There’s no startup or warm up delay, data can be cached in memory and even shared between different users.

On the other hand, because they run for a long time and sometimes interact with multiple users, they are also really hard to write.
They can contain memory leaks, which happens when data gets loaded and not cleaned up correctly.
There are many security concerns to ensure that users only access the data they should or to avoid malicious users from taking over the machine where the process is running in.
And there are also performance concerns because resources are not infinite.

To get a better understanding of how long running processes work, we’ll write a command line application that reads the zip code file used in the previous section and provides a mechanism where the user can query information from the file.
For this example we’ll interact with the user through the standard input and output.
We’ll discuss more about ways that processes communicate in the upcoming sections but for command line applications it’s very common to use standard I/O.

In Java, a simple way to read from the standard input is to use a `java.util.Scanner`.
For this example we’ll initialize it using the `java.io.Reader` from the system console, which will give us the correct character set configured:

```java
Scanner scanner = new Scanner(System.console().reader());
```
- [ ] link to code in GitHub

As mentioned before, a long running process generally starts with an infinite loop.
In this application, that loop is represented by the following line:

```java
while (true) {
  ...
}
```
- [ ] link to code in GitHub

Inside that loop, it executes the following steps:

1. Tell the user what (s)he can do
1. Wait and read the user’s choice
1. Then executes it

The first step is crucial, because it will guide the user throughout the whole process and expose what the application can do for her/him.
The following code is how this is done:

```java
System.out.println("What do you want to do?");
System.out.println(" Q (or q) - to quit");
System.out.println(" Z (or z) - to search for zipcode");
System.out.print("\n> ");
```
- [ ] link to code in GitHub

It prints to the console what the application can do and tells the user what (s)he needs to do next.
In this case, pick an option from a set: Q or Z.
The next line of code is step 2:

```java
String option = scanner.nextLine();
```
- [ ] link to code in GitHub

will block until the user types something and presses enter.
Then comes step 3.
Figure out what the user picked and execute it.
For this application there are only two options represented by two if conditions:

```java
if (option.equalsIgnoreCase("q")) {
  System.out.println("Good bye.");
  break;
}

if (option.equalsIgnoreCase("z")) {
  new ZipCodeFinder(getData(), scanner).findLoop();
}
```
- [ ] link to code in GitHub

We won’t dig too much into this code because it’s out of the scope of this book but feel free to explore and run it to see what it does.
The other important part of it that needs to be highlighted is the `getData` method.
As mentioned before, long running applications can do optimizations that some one-off applications can’t.
In this case the method that loads data from the file has the following code:

```java
private static List<AddressGroup> getData() throws IOException {
    if (addressGroups == null) {
        addressGroups = new AddressGroupReader("city_of_boston.csv")
            .readAddressGroups();
    }

    return addressGroups;
}
```
- [ ] link to code in GitHub

In this code, it first checks if the variable `addressGroup` is `null`.
If it is, then reads the data from the file and store in that variable.
That means that the second time it gets called, it won’t read the file again, it will only return the data already in memory.

The following is the output of running the application:

```text
What do you want to do?
 Q (or q) - to quit
 Z (or z) - to search for zipcode

> z
Read zip codes in 582ms
Type a number: 10
Found 4 zipcodes with 10
...
10700 total address groups found

What do you want to do?
 Q (or q) - to quit
 Z (or z) - to search for zipcode

> z
Type a number: 20
Found 1 zipcodes with 20
...
6743 total address groups found
```

The output shows the moment that the file was read into memory: `Read zip codes in 582ms`.
You can see that that line only appears once.
The second time you query the data it doesn’t load it again, since it’s already in memory, it just queries the data.
