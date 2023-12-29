# How to deploy SpringBoot WebAPI to AWS ECS

## 1. Create Spring Boot Web API application with VSCode

See this repo: https://github.com/luiscoco/SpringBoot_Sample2-created-WebAPI-with-VSCode

## 2. Create a Docker Image 

```
# Start with a base image containing Java runtime
FROM openjdk:11-jdk-slim as build

# Add Maintainer Info
LABEL maintainer="your_email@example.com"

# Add a volume pointing to /tmp
VOLUME /tmp

# Make port 8080 available to the world outside this container
EXPOSE 8080

# The application's jar file
ARG JAR_FILE=target/demoapi-0.0.1-SNAPSHOT.jar

# Add the application's jar to the container
ADD ${JAR_FILE} demoapi.jar

# Run the jar file
ENTRYPOINT ["java","-Djava.security.egd=file:/dev/./urandom","-jar","/demoapi.jar"]
```

## 3. Create a AWS Elastic Container Registry ECR public repo

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/4f686ab5-6f9f-45e0-8aec-a7ac8c3eb187)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/1c013312-96c3-452e-9905-7488aa5ebe3a)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/88c80691-099b-4736-901a-d142d1859e1d)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/1ba6438b-e910-4214-8543-bf88efdf730c)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/7fbf63ba-1957-4358-82ff-90a9767c2eb7)



## 4. Push the Docker image to AWS ECR




## 5. Deploy Docker image from AWS ECR to AWS ECS


