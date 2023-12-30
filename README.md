# How to deploy SpringBoot WebAPI to AWS ECS

## 1. Create Spring Boot Web API application with VSCode

See this repo: https://github.com/luiscoco/SpringBoot_Sample2-created-WebAPI-with-VSCode

We set, in the **application.properties** file, the application port to 80 

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

Before creating the docker image we should create the **JAR** file withe the following command:

```
mvn clean install
```

We confirm we created the **target** folder

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/bc657d8d-5973-4a7e-ab7b-24d2e1ad23ba)

After creating the **JAR** file we run the application with this command:

```
java -jar .\target\demoapi-0.0.1-SNAPSHOT.jar
```

We verify the application endpoints:

http://localhost:80/hello

http://localhost:80/actuator/health

## 2. Create a Docker file 

Create a Dockerfile and copy this content inside:

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

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/3eb904b1-e0a0-4804-b166-ef6919272419)

## 3. Create a AWS Elastic Container Registry ECR Public repo

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

- Delete the letter "s" the "credsStore": "desktop" the result is this word:  "credStore": "desktop"

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/9cc807e3-94dc-40c9-92d1-194bfd288a1c)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/9204df56-7122-492d-a6b7-36f776c85b05)

- Configure AWS account with access key and secret key

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/35647989-b216-4874-b676-7ddb9c1ad75f)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/eb6013e4-19e3-4e96-a291-9490ed96e383)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/9d6fd152-1dda-4737-aa81-9d6b4d9e011f)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/6a403ad2-8b0f-4523-90a4-99a63b0a9590)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/9c559b92-d8c8-4bab-b7c5-eccbcc5f3466)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/7838b037-2975-4604-9719-77d59e172dbf)

We try to enter now again in the AWS ECR with the following command:

```
aws ecr-public get-login-password --region us-east-1 | docker login --username AWS --password-stdin public.ecr.aws/x7p6e5r6
```

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/236a141b-6e4a-45a1-8234-4cd4ca094d44)

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

We press in the **Create cluster** button:

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/c579fe57-ed5f-4efa-b1c7-aa7a59770086)

We set the AWS Cluster name

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/02c3df1b-1f1d-4287-ba90-816f1673144b)

We leave the **Fargate** option checked and we press the **Create** button

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/c1931171-26dc-41b9-b5f2-2550d8f8c3c7)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/80d0961b-8a96-47d3-8af3-c7ae99b6db08)

**Note**: we selected the eu-north-1 region but the more advisable solution is select eu-west-3 because it is the nearest to our location in Spain.
But today I had some technical problems creating cluster in Paris region and I decided to slect the Stockholm one.

## 5.2. Create a Task Definition

We press the **Create new task definition** button

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/b4a14f9d-747c-4e02-9dcc-0cb623d578b4)

We select the infrastructure option **Fargate**

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/77ebcb86-84fe-46e6-b707-f25eb67ee179)

We leave the rest of options as they are by default

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/54fc665a-8bc4-43c2-8704-c5bdc3ffa4ac)

We have to set container image name

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/b05bbc17-d52b-4d1e-8a7a-1d039a10e70f)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/7b02a697-a096-47bb-82d6-b8683f943a4f)

We set the container URL and the port mapping

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/9f9f49c6-956e-40f2-93a4-b65b463211b8)

We leave the rest of options as they are by default and we press the ****

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/b36d0171-9611-42d7-9ad2-0a38e2d54f8e)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/4deef684-f035-4165-bce3-1b10776999cb)

## 5.3. Create a Service

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/08725d47-bfdf-4db9-a81c-c0ffc1ab40a3)

We select the launch type **Fargate**

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/5244210e-02be-4035-843d-4e5fe244929a)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/2ade5111-6231-4beb-93d2-fed68d04f54a)

We press the **Create** button

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/1cdc6913-fc69-4f1e-8714-23087aaa9918)

It takes few minutes to create the new Service

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/7aaf99f9-83db-4817-89e4-ed329c0620ae)



## 5.4. Allow inbound traffic on port 80

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/68680dd7-c2b1-4195-8a59-3972e67ef11f)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/1e02238c-74e1-4d7e-9d2a-19aae8450c0c)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/73a1b941-a805-4084-a105-638f7f00a626)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/3fd9b294-c2b1-4624-a5ce-87b7cb2e16ad)

## 5.5. Verify the application endpoints




