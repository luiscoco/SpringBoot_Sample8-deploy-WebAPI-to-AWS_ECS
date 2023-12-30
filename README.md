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

- Create Docker image

```
docker build -t webapirepo .
```

- Set the Docker image name

```
docker tag webapirepo:latest public.ecr.aws/x7p6e5r6/webapirepo:latest
```

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

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/acb0442d-cbf2-4dd4-8de8-6a937e37cbbe)

We set the cluster name and we leave the other options as they are

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/955d1d31-0433-497f-9017-4a79da12ad65)

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/567b7b31-eb55-46dd-8eef-00dd6c72e5de)

See the new cluster in the list

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/239a1cb7-8756-47eb-86c3-868edb2e346d)

We create the Task Definition

![image](https://github.com/luiscoco/SpringBoot_Sample8-deploy-WebAPI-to-AWS_ECS/assets/32194879/d47edef6-e863-437a-982b-5f31a41341df)

