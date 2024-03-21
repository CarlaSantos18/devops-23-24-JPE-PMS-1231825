# Class Assignment 2 Report

## Part 1 - Gradle tasks

I started by downloading the Gradle Basic Demo from [here](https://bitbucket.org/pssmatos/gradle_basic_demo/src/master/).

I added it to the folder [CA2/Part1](https://github.com/CarlaSantos18/devops-23-24-JPE-PMS-1231825/tree/main/CA2/Part1).

For each step of the assignment, a new issue was created in my personal [repository](https://github.com/CarlaSantos18/devops-23-24-JPE-PMS-1231825/).

The commands shown next were run on Git Bash, inside the folder [CA2/Part1/gradle_basic_demo](https://github.com/CarlaSantos18/devops-23-24-JPE-PMS-1231825/tree/main/CA2/Part1/gradle_basic_demo/).

----

After testing the commands to build the basic demo, run the server and run the client, present in the [README](https://github.com/CarlaSantos18/devops-23-24-JPE-PMS-1231825/blob/main/CA2/Part1/gradle_basic_demo/README.md)
file of the Gradle Basic Demo, I made the first implementation of the new feature asked - **Add a new task to execute to server.**
To do this, I edited the [build.gradle](https://github.com/CarlaSantos18/devops-23-24-JPE-PMS-1231825/blob/main/CA2/Part1/gradle_basic_demo/build.gradle) file, adding the following code:

```groovy
task runServer(type:JavaExec, dependsOn: classes){
    group = "DevOps"
    description = "Launches a chat server on localhost:59001"

    classpath = sourceSets.main.runtimeClasspath

    mainClass = 'basic_demo.ChatServerApp'

    args '59001'
}
```
After that, to run the new task created, I ran the following command:

```bash
$ ./gradlew runServer
```

The server was successfully launched on localhost:59001, running until the process is manually stopped.

----
For the next step, it was asked to develop a **simple unit test and update the gradle script** so that it is able to run
the test.
The code for the test was provided and is as follows:

```java
package basic_demo;

import org.junit.Test;
import static org.junit.Assert.*;

public class AppTest {

    @Test
    public void testAppHasAGreeting() {
        App classUnderTest = new App();
        assertNotNull("app should have a greeting", classUnderTest.getGreeting());
    }
}
```

The following dependency was added to the gradle script:

```groovy
testImplementation 'junit:junit:4.12'
```

To make sure the test was running correctly, I ran the following command:

```bash
$ ./gradlew test
```

----
Next, it was asked that we create a new **task of type "Copy"** to be used to make a backup of the sources of the
application.

For that, I added the following code to the [build.gradle](https://github.com/CarlaSantos18/devops-23-24-JPE-PMS-1231825/blob/main/CA2/Part1/gradle_basic_demo/build.gradle) file:

```groovy
task makeCopy(type: Copy){
    group = "DevOps"
    description = "Creates a copy of the 'src' folder in a 'backup' folder"

    from 'src'
    into 'backup'
}
```
To run the new task, I used the following command:

```bash
$ ./gradlew makeCopy
```

This created a copy of the 'src' folder in a ['backup'](https://github.com/CarlaSantos18/devops-23-24-JPE-PMS-1231825/tree/main/CA2/Part1/gradle_basic_demo/backup) folder.

----
The last step of the assignment was to **create a new task of type "Zip"** to be used to create a zip file with the
sources of the application.

To achieve that, I added the following code to the [build.gradle](https://github.com/CarlaSantos18/devops-23-24-JPE-PMS-1231825/blob/main/CA2/Part1/gradle_basic_demo/build.gradle) file:

```groovy
task makeZip(type: Zip){
    group = "DevOps"
    description = "Creates a zip archive of the 'src' folder"

    archiveFileName = 'src.zip'
    destinationDirectory = file('zipFolder')
    from 'src'
}
```

To run the new task, I used the following command:

```bash
$ ./gradlew makeZip
```

This created a zip file named 'src.zip' in the ['zipFolder'](https://github.com/CarlaSantos18/devops-23-24-JPE-PMS-1231825/tree/main/CA2/Part1/gradle_basic_demo/zipFolder).

Finally, a new tag was created for the final version of the project, **ca2-part1**.