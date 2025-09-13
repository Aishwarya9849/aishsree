create a docker file to build a docker image for sams using maven then
 build the docker image run the container and confirm 
 that sams is accessible at http://localhost:8090
and push the Docker image to Docker Hub 

SEE THIS IN CODE VIEW SO ANSWER APPEAR SMALL
steps:
# Docker Instructions for SAMS using Maven

## Step 1: Create Dockerfile
Inside your project root (where your pom.xml is), create a file named `Dockerfile` with this content:
---------------------------------------------------
# Stage 1: Build the JAR using Maven
FROM maven:3.9.5-eclipse-temurin-17 AS build
WORKDIR /app
COPY pom.xml .
COPY src ./src
RUN mvn clean package -DskipTests

# Stage 2: Run the JAR with Java
FROM openjdk:17-jdk-slim
WORKDIR /app
COPY --from=build /app/target/*.jar sams.jar

EXPOSE 8090
ENTRYPOINT ["java", "-jar", "sams.jar"]
---------------------------------------------------
Explanation (important):
- Multi-stage build: First stage uses Maven to compile your project and create a JAR. Second stage runs only the JAR in a lighter Java image (saves space).
- `EXPOSE 8090` ensures the container listens on port 8090.
---------------------------------------------------
## Step 2: Build the Docker Image
Run this command from the folder containing your Dockerfile:

    docker build -t sams-app .
---------------------------------------------------
## Step 3: Run the Container
Run it mapping container port 8090 → host port 8090:

    docker run -d -p 8090:8090 --name sams-container sams-app

Check running containers:

    docker ps

---------------------------------------------------

## Step 4: Verify Application
Open in browser:

    http://localhost:8090

or check logs if needed:

    docker logs sams-container

---------------------------------------------------

## Step 5: Push to Docker Hub
1. Login to Docker Hub:
   
       docker login

2. Tag the image:
   
       docker tag sams-app your-dockerhub-username/sams-app:latest

3. Push the image:
   
       docker push your-dockerhub-username/sams-app:latest

---------------------------------------------------

## Summary of Commands
    docker build -t sams-app .
    docker run -d -p 8090:8090 --name sams-container sams-app
    docker ps
    docker logs sams-container
    docker login
    docker tag sams-app your-dockerhub-username/sams-app:latest
    docker push your-dockerhub-username/sams-app:latest

---------------------------------------------------

