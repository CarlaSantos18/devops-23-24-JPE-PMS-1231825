# Class Assignment 4 Report

## Part 1 - Containers with Docker

The goal for Part1 of this assignment is to practice creating Docker images and containers for the chat application 
developed in **CA2/Part1**.

### Version 1 - Build the chat server "inside" the Dockerfile

First, I created a Dockerfile with the following content:

```Dockerfile
#First stage: Build the application
FROM gradle:jdk17 as builder
LABEL author="Carla Santos"
WORKDIR /ca4-part1
RUN git clone https://bitbucket.org/pssmatos/gradle_basic_demo.git
WORKDIR /ca4-part1/gradle_basic_demo
RUN chmod +x gradlew
RUN ./gradlew build

#Second stage: Create the image
FROM openjdk:17-jdk-slim
WORKDIR /ca4-part1
COPY --from=builder /ca4-part1/gradle_basic_demo/build/libs/*.jar ca4-part1.jar
ENTRYPOINT ["java", "-cp", "ca4-part1.jar", "basic_demo.ChatServerApp", "59001"]
```

Then, to create an image, I ran the following command:

```bash
docker build -t ca4-part1 .
```

To run the image and create a container, I ran the following command:

```bash
docker run -p 59001:59001 ca4-part1
```
The server was running correctly.

Then, I tested the application on local machine by running the command:

```bash
./gradlew runClient
```

The chat application was working correctly.

To tag the image, first I searched for the image id:

```bash
docker images
```

Then, I tagged the image:

```bash
docker tag c0d541de3731 carlasantos1231825/ca4-part1:version1
```

Finally, I pushed the image to Docker Hub:

```bash
docker push carlasantos1231825/ca4-part1:version1
```

### Version 2 - Build the chat server in host computer and copy the jar file "into" the Dockerfile

I started by copying the **gradle_basic_demo** directory from **CA2/Part1** to the new directory
**CA4/Part1/Version2**.

Then, I created a new Dockerfile for this version with the following content:

```Dockerfile
FROM gradle:jdk17 as builder
LABEL author="Carla Santos"
WORKDIR /ca4-part1-v2
COPY gradle_basic_demo/build/libs/basic_demo-0.1.0.jar /ca4-part1-v2/basic_demo-0.1.0.jar
EXPOSE 59001
ENTRYPOINT ["java", "-cp", "/ca4-part1-v2/basic_demo-0.1.0.jar", "basic_demo.ChatServerApp", "59001"]
```

To create an image, I ran the following command:

```bash
docker build -t ca4-part1-v2 .
```

Then I built the application with the following command:

```bash
./gradlew build
```

And to run the image and create a container, I ran the following command:

```bash
docker run -p 59001:59001 ca4-part1-v2
```
The server was running correctly.

Finally, I tested the application on local machine by running the command:

```bash
./gradlew runClient
```

The chat application was working correctly.

To tag the image, first I searched for the image id:

```bash
docker images
```

Then, I tagged the image:

```bash
docker tag 5e9adc5df4f7 carlasantos1231825/ca4-part1-v2:version2
```

Finally, I pushed the image to Docker Hub:

```bash
docker push carlasantos1231825/ca4-part1-v2:version2
```

As everything was working correctly, I commited everything to the repository and tagged it as **ca4-part1**.
