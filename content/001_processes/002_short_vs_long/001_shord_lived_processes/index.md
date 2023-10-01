---
title: Short Lived Processes
weight: 100
---

Short lived processes, or one-off processes are designed to start, do some work and then exit.
This type of processes are normally used to do batch processing like when you have some data that needs be loaded, processed and then written to some other place.
For example, banks use this type of jobs to transfer money between institutions. The following is an illustration that shows how this system works:

{{<
  figure
  src="001_bank_batch_processing.png"
  caption="Bank 1 uses a one-off process to transfer data to Bank 2, which uses another one-off job to load data into its own database."
>}}

One bank (Bank 1) extracts data from one database: how much money each customer transferred to some other bank (Bank 2).
The process (Process 1) reads the data from the database, generates some files that are transferred to the other bank.
Bank 2 receives the files and initiates its own process (Process 2) to load the data into its database.
Banks use this to transfer millions of dollars around the world everyday.

To understand better how a one-off process works, lets write an application that will count all the different zip codes that exists in a file and then write it to another file.
This application will start, read a CSV file line-by-line, collect all the ZIP codes storing them uniquely in memory and a count for each.
After it finishes reading all the lines, it will print all the codes found and their count into a new file.

For this example we'll be using the data from http://results.openaddresses.io, and the list of zip codes for Boston, Massachussetts.
I have a copy of the data in the file city_of_boston.csv in [here](https://github.com/how-the-web-works/how-web-servers-work-java-code/tree/main/data).

The zip code counter starts by loading the data file using a CSV reader.
You can use any of the CSV libraries available out there, like [Apache Commons CSV](https://commons.apache.org/proper/commons-csv/).
I wrote a version of it and added it to the shared code library in the sample code, you can see the code [in GitHub](https://github.com/how-the-web-works/how-web-servers-work-java-code/blob/main/shared_code/src/main/java/org/visola/howthewebworks/serverside/datareader/CSVReader.java).

To count the zip codes, first we create a `java.util.TreeMap` to keep track of them uniquely.
The TreeMap will also keep them sorted, which will be helpful when printing the results later.
Here is the code that reads the CSV and add them to the TreeMap:

```java
String [] line;
Map<String, Integer> countsByZipCode = new TreeMap<>();
while ((line = reader.readRecord()) != null) {
  String zipCode = line[8];
  if (zipCode.trim().isEmpty()) {
    continue;
  }

  Integer count = countsByZipCode.getOrDefault(zipCode, 0);
  count++;
  countsByZipCode.put(zipCode, count);
}
```
- [ ] link to code in GitHub

After adding all zip codes to the TreeMap, we can iterate over it and print the results to standard output:

```java
System.out.println("ZIP_CODE,COUNT");
for (Entry<String, Integer> entry : countsByZipCode.entrySet()) {
  System.out.printf("%s,%d\n", entry.getKey(), entry.getValue());
}
```
- [ ] link to code in GitHub

With the code ready, you can just run your application to see it go off, read the CSV and print the results.
This is a sample output:

```shell
$ ./run.sh java/chapter-01/zip_code_counter/
ZIP_CODE,COUNT
02108,3619
02109,3239
02110,2539
...
02210,1303
02215,10582
02467,147
```

But I said we wanted to write the output to a file. And you can if you want.
Operating systems have many tools to redirect output to some other place.
In this case, in a posix compliant system (like Linux or Mac OS), you could just redirect the output to a file, like the following:

```shell
$ ./run.sh java/chapter-01/zip_code_counter/ > zip_count.csv
```

And then you’ll get the CSV file in your directory:

```shell
$ ls -lah
...
-rw-r--r--   1 visola  staff   368B May 16 07:41 zip_count.csv
```

When writing your one-off process code you should take many things into consideration:
- How you’re going to run it?
- How frequently are you going to run it?
- Who is going to maintain it?
- How much external dependencies it has?
- How are you going to build it?
- How frequently will it change?

When you keep these questions in mind, you can easily decide if you’re going to write the feature in your code, use some library or combine multiple tools.
It will also help deciding what language you’re going to use to write it, what build system and many other implementation details.
