# How to deploy SpringBoot WebAPI to AWS ECS

## 1. Create Spring Boot Web API application with VSCode

See this repo: https://github.com/luiscoco/SpringBoot_Sample2-created-WebAPI-with-VSCode

Set the application port to 80.

This is the **application.properties** file

```
# Server Configuration
server.port=80

# Logging
logging.level.org.springframework.web=INFO
logging.level.org.hibernate=ERROR

# Actuator Endpoints
management.endpoints.web.exposure.include=*
management.endpoint.health.show-details=always

# Actuator Info
info.app.name=My Spring Boot Application
info.app.description=A simple demo application
info.app.version=1.0.0
```

## 2. Create a Docker Image 

```
# Start with a base image containing Java runtime
FROM openjdk:11-jdk-slim as build

# Add Maintainer Info
LABEL maintainer="your_email@example.com"

# Add a volume pointing to /tmp
VOLUME /tmp

# Make port 80 available to the world outside this container
EXPOSE 80

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

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/36e8c7a9-86ec-4cc0-a82b-e0df750a9b42)

```
aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/x7p6e5r6
```

**IMPORTANT**: if you cannot enter in the AWS ECR follow these steps

- Delete **config.json** file in the following location:

  ![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/5d936873-cb7f-4391-94d3-73f2c29de864)

- Type the command:

```
docker login
```

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/b0006793-cbd6-4290-b92c-74fe5ba1065e)

- Delete the lowwercase "s" letter in the "credsStore": "desktop" the result is this word:  "credStore": "desktop"

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/9cc807e3-94dc-40c9-92d1-194bfd288a1c)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/9204df56-7122-492d-a6b7-36f776c85b05)

- Configure AWS account with access key and secret key

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/35647989-b216-4874-b676-7ddb9c1ad75f)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/eb6013e4-19e3-4e96-a291-9490ed96e383)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/9d6fd152-1dda-4737-aa81-9d6b4d9e011f)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/6a403ad2-8b0f-4523-90a4-99a63b0a9590)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/9c559b92-d8c8-4bab-b7c5-eccbcc5f3466)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/7838b037-2975-4604-9719-77d59e172dbf)

```
aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/x7p6e5r6
```

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/236a141b-6e4a-45a1-8234-4cd4ca094d44)

Before creating the docker image we should create the **JAR** file withe the following command:

```
mvn clean install
```

We verify we created the **target** folder

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/bc657d8d-5973-4a7e-ab7b-24d2e1ad23ba)

After creating the **JAR** file we run the application with this command:

```
java -jar .\target\demoapi-0.0.1-SNAPSHOT.jar
```

- Verify the application endpoints:

http://localhost:80/hello

http://localhost:80/actuator/health

- Now we can create the SpringBoot WebAPI Docker image with this command

```
docker build -t webapirepo .
```

- Rename the Docker image for pushing it to the AWE ECR repo

```
docker tag webapirepo:latest public.ecr.aws/x7p6e5r6/webapirepo:latest
```

- Also we can verify the application docker image running it in our local Docker Desktop with this command:

```
docker run -p 80:80 --name myapp-container webapirepo:latest
```

- Verify the application endpoints:

http://localhost:80/hello

http://localhost:80/actuator/health

## 4. Push the Docker image to AWS ECR

Push the Docker image to AWS ECR

```
docker push public.ecr.aws/x7p6e5r6/webapirepo:latest
```

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/a4b7081e-10a1-493a-adbc-54e65fe4f101)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/cf6fa17a-0a99-4466-b686-02f153f09763)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/760a0d23-8419-4480-b99e-84bed806b976)

## 5. Deploy Docker image from AWS ECR to AWS ECS

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/a05905db-bda1-402c-89ef-3137ddc1809a)

## 5.1. Create a AWS ECS cluster

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/acb0442d-cbf2-4dd4-8de8-6a937e37cbbe)

We set the cluster name and we leave the other options as they are

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/955d1d31-0433-497f-9017-4a79da12ad65)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/567b7b31-eb55-46dd-8eef-00dd6c72e5de)

See the new cluster in the list

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/239a1cb7-8756-47eb-86c3-868edb2e346d)

## 5.2. Create a Task Definition

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/d47edef6-e863-437a-982b-5f31a41341df)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/d7fdce8d-ba42-43e2-8c3c-288e5ac41052)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/e46fa7c4-3166-4cef-a52a-32955d62dde8)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/843f7676-13cb-4825-8f2b-3f7999f26491)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/c651f156-8961-413e-afe9-50ac34a38574)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/77dcbfa3-e5a7-4bc5-aee9-4057b18b5478)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/efe1d0b2-ae74-43be-9b46-4f1bde8d3de5)

## 5.3. Create a Service

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/b0dcff27-7eb5-4ecf-ae72-332ea3c3ef40)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/f18ec850-7332-405c-b6ce-df0f8e28b51a)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/b7252a62-8b30-4ad8-9524-ada5c3196eb0)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/e9d6b9e4-e1cf-4786-99ac-cf1ae1043746)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/93a29431-5d87-4522-8093-408a92a930b9)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/80a56e69-2a3a-4f63-a62b-b323fe5c3450)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/d7b705af-24ac-4d77-bf1b-1b5b4fa4fccb)



