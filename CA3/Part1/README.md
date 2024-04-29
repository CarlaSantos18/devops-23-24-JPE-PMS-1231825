# Class Assignment 3 Report

## Part 1 - Virtualization with Vagrant

For this first part of the assignment, I already set up the Virtual Machine using VirtualBox following the steps provided by the teacher
in **devops.pdf**.

However, the ubuntu version I used was **Ubuntu 22.04.4**, as I was having some issues with the older version mentioned 
in the instructions. I also used the **JDK 17** version instead of **JDK 8**.

The objective of this first part is to build and execute the previous applications we developed in the two previous
assignments, but inside the VM.

I started by connecting with my VM through ssh from my host machine (the following IP was established at the time of the 
creation of the new VM):
```
ssh carla@192.168.254.5
```

Now inside the VM, I cloned my personal repository into a new folder named "**MyRepo**".

Inside the folder "***CA1/basic**" I ran the command:

```bash
$ ./mvnw spring-boot:run
```

This will build and run the application used in CA1. There was no need to install maven since
I am using the bundled maven wrapper.

To make sure the application is working correctly, I went to the page located at **http://192.168.254.5:8080/**
on my host machine, and I could see the initial table displayed by the frontend.


To check the application which runs a chat server and chat clients, I navigated to directory
"**CA2/Part1/gradle_basic_demo**".

I needed to give the **execute** permission to the "gradlew" file by using:

```bash
$ chmod +x gradlew
```

Then I ran the command to build the application:
```bash
$ sudo ./gradlew build
```

To run the chat server on the VM, I ran the command:

```bash
$ java -cp build/libs/basic_demo-0.1.0.jar basic_demo.ChatServerApp 59001
```

To run the chat client on my host machine, I had to change the args from my **runClient** task inside the **build.gradle**
file on the cloned project, from:

```groovy
task runClient(type:JavaExec, dependsOn: classes){
    group = "DevOps"
    description = "Launches a chat client that connects to a server on localhost:59001 "
  
    classpath = sourceSets.main.runtimeClasspath

    main = 'basic_demo.ChatClientApp'

    args 'localhost', '59001'
}
```

To:

```groovy
task runClient(type:JavaExec, dependsOn: classes){
    group = "DevOps"
    description = "Launches a chat client that connects to a server on localhost:59001 "
  
    classpath = sourceSets.main.runtimeClasspath

    main = 'basic_demo.ChatClientApp'

    args '192.168.254.5', '59001'
}
```

This way, when I run the **runClient** task on my host machine, it will connect the chat client to the chat server 
running on the VM. The command used was:

```bash
$ ./gradlew runClient
```

These steps are required so we can test a real situation where a client connects with an external server.

Finally, I tested the application developed in "**CA2/Part2/react-and-spring-data-rest-basic-gradle**". After navigating
to that directory, I needed to give the **execute** permission to the "gradlew" file by using:

```bash
$ chmod +x  gradlew
```

Then I ran the build command with the **sudo** command:

```bash
$ sudo ./gradlew build
```

Again, there was no need to install Gradle on this VM since I was using the bundled gradle wrapper.

After a successful build, I proceeded to boot the application:

```bash
$ sudo ./gradlew bootRun
```

After this, I went to the **http://192.168.254.5:8080/** page again to check that everything was working.


At the end of this part of the assignment I tagged the repository with **ca3-part1**.