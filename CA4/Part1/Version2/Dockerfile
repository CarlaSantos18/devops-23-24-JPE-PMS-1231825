FROM gradle:jdk17 as builder
LABEL author="Carla Santos"
WORKDIR /ca4-part1-v2
COPY gradle_basic_demo/build/libs/basic_demo-0.1.0.jar /ca4-part1-v2/basic_demo-0.1.0.jar
EXPOSE 59001
ENTRYPOINT ["java", "-cp", "/ca4-part1-v2/basic_demo-0.1.0.jar", "basic_demo.ChatServerApp", "59001"]