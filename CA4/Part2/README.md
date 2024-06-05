# Class Assignment 4 Report

## Part 2 - Containers with Docker

The goal for Part2 of this assignment is to create two images from two different Dockerfiles and then create the containers 
with a Docker-compose file, for the application developed in **CA2/Part2**.

In order to complete this assigment, I started by copying the contents of the **CA2/Part2** folder to the **CA4/Part2** folder into
this new folder where the Dockerfiles and the Docker-compose file will be created.

### First Dockerfile - Web

First, I created a Dockerfile for the web application with the following content:

```Dockerfile
FROM openjdk:17-jdk-slim
WORKDIR /app
COPY ./react-and-spring-data-rest-basic-gradle .
WORKDIR /app
RUN chmod +x gradlew
CMD ["./gradlew", "build"]
EXPOSE 8080
RUN ls -la
CMD ["java", "-jar", "dist/react-and-spring-data-rest-basic-0.0.1-SNAPSHOT.jar"]
```

This Dockerfile is responsible for building the web application. It copies the project to the container, runs the build command,
exposes the port 8080 and runs the application.

### Second Dockerfile - Database

Then, I created a Dockerfile for the database with the following content:

```Dockerfile
FROM ubuntu:focal
RUN apt-get update && \
    apt-get install -y wget openjdk-17-jdk-headless && \
    rm -rf /var/lib/apt/lists/* \
WORKDIR /opt/h2
RUN wget https://repo1.maven.org/maven2/com/h2database/h2/1.4.200/h2-1.4.200.jar -O h2.jar
EXPOSE 8082
EXPOSE 9092
CMD ["java", "-cp", "h2.jar", "org.h2.tools.Server", "-ifNotExists", "-web", "-webAllowOthers", "-webPort", "8082", "-tcp", "-tcpAllowOthers", "-tcpPort", "9092"]
```

This Dockerfile is responsible for building the database. It installs the necessary packages, downloads the H2 database, exposes the ports 8082 and 9092 and runs the database.

### Docker-compose file

Finally, I created a Docker-compose file with the following content:

```yaml
services:
  db:
    build:
      context: .
      dockerfile: Dockerfiledb
    container_name: CA4_Part2_db
    ports:
      - "8082:8082"
      - "9092:9092"
    volumes:
      - h2-data:/opt/h2-data
    networks:
        CA4_network:
            ipv4_address: 192.168.33.11

  web:
    build:
      context: .
      dockerfile: Dockerfileweb
    container_name: CA4_Part2_web
    ports:
      - "8080:8080"
    networks:
        CA4_network:
            ipv4_address: 192.168.33.10
    depends_on:
      - "db"

volumes:
  h2-data:
    driver: local

networks:
  CA4_network:
    ipam:
      driver: default
      config:
        - subnet: 192.168.33.0/24
```

After creating the Dockerfiles and the Docker-compose file, I built the images by running the following command:

```bash
docker-compose build
```

I checked the images were created by running the following command:

```bash
docker images
```

Then, I started the containers by running the following command:

```bash
docker-compose up
```

The containers were running correctly.

Finally, I tested the application on the browser by accessing the URL's:

```bash
http://localhost:8080
```

```bash
http://localhost:8082
```

The web application the database were working correctly.

Finally, I tagged each image and pushed them to Docker Hub.

```bash
docker tag 7c0808a398dd carlasantos1231825/part2-web:ca4-part2-web
docker push carlasantos1231825/part2-web:ca4-part2-web
```

```bash
docker tag f497991ca79f carlasantos1231825/part2-db:ca4-part2-db
docker push carlasantos1231825/part2-db:ca4-part2-db
```

To copy the database file to the volume, we should use an exec command to run the shell in the container:

```bash
docker-compose exec 'image-name' bash
```

Then, we can copy the file to the volume:

```bash
cp *db /opt/h2-data
```

Then we can go to the destination folder and verify if the file was copied correctly.


With this, the assignment was completed, and after commiting this README file, I tagged the repository as **ca4-part2**.

### Analysis of an alternative - Kubernetes

An alternative approach to this assignment would be to use Kubernetes. Kubernetes is a container orchestration platform 
that automates the deployment, scaling, and management of containerized applications.

The app could be deployed in a pod and the database in another pod. The app would then connect to the database using the
service name. This would allow for better separation of concerns and better management of the app and the database.

After pushing the docker images to Docker Hub, we would need to create a Kubernetes deployment file for the app and the
database. We would also need to create a service file for the app and the database.

Web app deployment file:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
   name: ca4-part2-web
spec:
   replicas: 1
   selector:
      matchLabels:
         app: ca4-part2-web
   template:
      metadata:
         labels:
            app: ca4-part2-web
      spec:
         containers:
            - name: ca4-part2-web
              image: carlasantos1231825/part2-web:ca4-part2-web
              ports:
                 - containerPort: 8080
                   
 ```

Web app service file:
```yaml
apiVersion: v1
kind: Service
metadata:
   name: ca4-part2-web
spec:
   type: NodePort
   ports:
      - port: 8080
        targetPort: 8080
        nodePort: 30080
   selector:
      app: ca4-part2-web
```

Database deployment file:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
   name: ca4-part2-db
spec:
   replicas: 1
   selector:
      matchLabels:
         app: ca4-part2-db
   template:
      metadata:
         labels:
            app: ca4-part2-db
      spec:
         containers:
            - name: ca4-part2-db
              image: carlasantos1231825/part2-db:ca4-part2-db
              ports:
                 - containerPort: 8082
                 - containerPort: 9092
```

Database service file:
```yaml
apiVersion: v1
kind: Service
metadata:
   name: ca4-part2-db
spec:
   type: NodePort
   ports:
      - port: 8082
        targetPort: 8082
        nodePort: 30082
      - port: 9092
        targetPort: 9092
        nodePort: 30092
   selector:
      app: ca4-part2-db
```

After creating the deployment and service files, we would need to apply them to the Kubernetes cluster by running the following command:

```bash
kubectl apply -f deployment-file.yaml
kubectl apply -f service-file.yaml
```

The app and the database would be running in the Kubernetes cluster and we could access the app by using the URL:

```bash
http://<node-ip>:30080
```

```bash
http://<node-ip>:30082
```

This alternative approach would allow for better management of the app and the database and would allow for better 
scalability and availability of the app.

It is not uncommon for Docker and Kubernetes to be used together. Docker is used to build the images and Kubernetes is 
used to deploy and manage the containers.



