Below is an example **README.md** structure you can use to teach students about the **Docker build** command and how to work with **Dockerfiles**. It starts with the basics (building an image from a simple Dockerfile) and progresses to more advanced concepts (build arguments, environment variables, multi-stage builds, etc.). You can copy-paste this into a `README.md` file on GitHub, then add the corresponding folders/files for each lab.

---

# Docker Build and Dockerfile Labs

Welcome! In this repository, we will learn how to use the **Docker build** command and how to write **Dockerfiles**—from the very basics to more advanced features. Each lab folder contains instructions, code examples, and explanations to help you master Docker image creation.

## Table of Contents
1. [Lab 1: Hello Docker](#lab-1-hello-docker)
2. [Lab 2: Dockerfile Basics](#lab-2-dockerfile-basics)
3. [Lab 3: Building a Web Server Image](#lab-3-building-a-web-server-image)
4. [Lab 4: Using Environment Variables](#lab-4-using-environment-variables)
5. [Lab 5: Using Build Arguments](#lab-5-using-build-arguments)
6. [Lab 6: Multi-Stage Builds](#lab-6-multi-stage-builds)
7. [Lab 7: Docker Ignore Files](#lab-7-docker-ignore-files)
8. [Lab 8: Caching and Layer Management](#lab-8-caching-and-layer-management)
9. [Lab 9: Putting it All Together](#lab-9-putting-it-all-together)

---

## Lab 1: Hello Docker

**Goal**: Ensure everyone can run Docker commands and build a simple image.

1. **Create a simple text file**:
   - `echo "Hello Docker" > hello.txt`

2. **Build a Docker image using a base image like alpine**:
   ```bash
   docker run alpine cat /etc/os-release
   ```
   - This checks if Docker is running and pulls the Alpine image.

3. **Run a container**:
   ```bash
   docker run --name my-alpine alpine echo "Hello Docker"
   ```

4. **Clean up**:
   ```bash
   docker rm my-alpine
   docker rmi alpine
   ```

**Exercises**:
- Confirm Docker is correctly installed.
- Try other base images, e.g., `ubuntu` or `busybox`.

---

## Lab 2: Dockerfile Basics

**Goal**: Learn how to create your first Dockerfile and build an image.

1. **Create a new folder** called `lab-2-dockerfile-basics` and add a file named `Dockerfile`.
2. Inside the `Dockerfile`, add the following:
   ```dockerfile
   # Dockerfile
   FROM alpine:latest
   RUN echo "Hello from Dockerfile" > /hello.txt
   CMD ["cat", "/hello.txt"]
   ```
3. **Build the image**:
   ```bash
   cd lab-2-dockerfile-basics
   docker build -t hello-dockerfile .
   ```
4. **Run the container**:
   ```bash
   docker run --rm hello-dockerfile
   ```
   - You should see `Hello from Dockerfile`.

**Exercises**:
- Change the `CMD` to run a different command.  
- Add another line to the Dockerfile to install a package (e.g., `curl`).

---

## Lab 3: Building a Web Server Image

**Goal**: Use a slightly more advanced Dockerfile to serve a simple webpage.

1. **Create a new folder** called `lab-3-webserver`.
2. **Add an `index.html`** file:
   ```html
   <!DOCTYPE html>
   <html>
   <head>
       <title>My Docker Webserver</title>
   </head>
   <body>
       <h1>Hello from my Docker webserver!</h1>
   </body>
   </html>
   ```
3. **Create a `Dockerfile`**:
   ```dockerfile
   FROM nginx:alpine
   COPY index.html /usr/share/nginx/html/
   EXPOSE 80
   CMD ["nginx", "-g", "daemon off;"]
   ```
4. **Build** and **run**:
   ```bash
   docker build -t my-nginx .
   docker run -d -p 8080:80 my-nginx
   ```
   - Navigate to `http://localhost:8080` in your browser to see the page.

**Exercises**:
- Change the port from 80 to 8080 inside the container and update the `EXPOSE` instruction.
- Add a second HTML file and see if you can serve it by modifying `nginx.conf`.

---

## Lab 4: Using Environment Variables

**Goal**: Understand how to pass environment variables to Docker containers.

1. **Create a new folder** called `lab-4-env-vars`.
2. **Dockerfile**:
   ```dockerfile
   FROM alpine:latest
   ENV MY_VAR="DefaultValue"
   CMD ["sh", "-c", "echo The value of MY_VAR is: $MY_VAR"]
   ```
3. **Build**:
   ```bash
   docker build -t env-demo .
   ```
4. **Run without environment variable override**:
   ```bash
   docker run --rm env-demo
   ```
   - Output: `The value of MY_VAR is: DefaultValue`
5. **Run with environment variable override**:
   ```bash
   docker run --rm -e MY_VAR="Hello from outside" env-demo
   ```
   - Output: `The value of MY_VAR is: Hello from outside`

**Exercises**:
- Add multiple `ENV` instructions.
- Try setting environment variables from a file (`--env-file`).

---

## Lab 5: Using Build Arguments

**Goal**: Distinguish between **build-time** and **run-time** configurations by using `ARG` instructions.

1. **Create a new folder** called `lab-5-build-args`.
2. **Dockerfile**:
   ```dockerfile
   FROM alpine:latest
   ARG BUILD_VAR="No Build Arg Provided"
   RUN echo "BUILD_VAR is: $BUILD_VAR" > /build_var.txt
   CMD ["cat", "/build_var.txt"]
   ```
3. **Build**:
   ```bash
   docker build -t build-arg-demo .
   ```
4. **Override build arguments**:
   ```bash
   docker build \
     --build-arg BUILD_VAR="Build Arg Provided" \
     -t build-arg-demo-override .
   ```
5. **Check output**:
   ```bash
   docker run --rm build-arg-demo
   # Expect: BUILD_VAR is: No Build Arg Provided

   docker run --rm build-arg-demo-override
   # Expect: BUILD_VAR is: Build Arg Provided
   ```

**Exercises**:
- Add multiple `ARG` instructions.
- Use `ARG` and `ENV` together to see how each one is evaluated at build-time vs. run-time.

---

## Lab 6: Multi-Stage Builds

**Goal**: Reduce image size by using multi-stage builds.

1. **Create a new folder** called `lab-6-multi-stage`.
2. **Example**: Building a Go app (you can use any language with a build artifact).

   **Files**:
   - `main.go`:
     ```go
     package main

     import "fmt"

     func main() {
         fmt.Println("Hello from a multi-stage build!")
     }
     ```

   - `Dockerfile`:
     ```dockerfile
     # Stage 1: Build the application
     FROM golang:alpine AS build
     WORKDIR /app
     COPY main.go .
     RUN go build -o myapp main.go

     # Stage 2: Create a minimal runtime image
     FROM alpine:latest
     WORKDIR /app
     COPY --from=build /app/myapp .
     CMD ["./myapp"]
     ```
3. **Build** and **run**:
   ```bash
   docker build -t multi-stage-demo .
   docker run --rm multi-stage-demo
   ```
   - Output: `Hello from a multi-stage build!`

**Exercises**:
- Modify the code or try with a different programming language (e.g., Node.js).
- Add more stages to install dependencies, run unit tests, etc.

---

## Lab 7: Docker Ignore Files

**Goal**: Learn how to use `.dockerignore` to optimize builds.

1. **Create a `.dockerignore`** file:
   ```bash
   node_modules
   *.log
   .git
   ```
2. **Create a Dockerfile** that copies the entire context:
   ```dockerfile
   FROM alpine:latest
   COPY . /app
   CMD ["ls", "/app"]
   ```
3. **Observe** that ignored files won’t be included in the image build context.

**Exercises**:
- Add `.dockerignore` entries for your environment (e.g., `venv` in Python).
- Check `docker build` output for changes in the build context size.

---

## Lab 8: Caching and Layer Management

**Goal**: Understand Docker’s layer caching to speed up builds.

1. **Create a folder** `lab-8-caching`.
2. **Dockerfile** example:
   ```dockerfile
   FROM python:3.9-alpine

   # Install dependencies first (layer 1)
   RUN pip install --no-cache-dir flask

   # Copy application code after (layer 2)
   COPY app.py /app.py

   CMD ["python", "app.py"]
   ```
3. **Explanation**:  
   - If you change your `app.py`, Docker will only rebuild the last layer (copy).  
   - If you update dependencies (the `RUN pip install ...` line), Docker will rebuild from that layer down.

**Exercises**:
- Re-build the image with small changes in `app.py` vs. changes in the dependencies line and notice the difference in build times.

---

## Lab 9: Putting it All Together

**Goal**: Combine everything learned into a single project.

**Project**:
- Create a multi-stage Docker build that:
  1. Builds a small web or CLI application in one stage.
  2. Copies the final build artifact to a minimal base image.
  3. Uses environment variables to configure runtime settings.
  4. Uses a `.dockerignore` file to exclude unnecessary files.

Example steps:
1. **Structure**:
   ```
   .
   ├── .dockerignore
   ├── Dockerfile
   ├── src
   │   └── ...
   └── ...
   ```
2. **Dockerfile** snippet:
   ```dockerfile
   # Stage 1: build
   FROM node:14-alpine AS build
   WORKDIR /app
   COPY package*.json ./
   RUN npm install
   COPY . .
   RUN npm run build

   # Stage 2: runtime
   FROM nginx:alpine
   ENV NODE_ENV="production"
   COPY --from=build /app/dist /usr/share/nginx/html
   EXPOSE 80
   CMD ["nginx", "-g", "daemon off;"]
   ```
3. **Build** and **run**:
   ```bash
   docker build -t final-project .
   docker run -d -p 80:80 final-project
   ```
4. **Test**:  
   - Open `http://localhost` to see your web app.

**Exercises**:
- Add a `.dockerignore` to ignore any local logs or `node_modules`.
- Introduce a build argument for specifying `NODE_ENV`.
- Add a second stage to run tests before building the final image.



## Lab 10: Dockerizing a Java Application

**Goal**: Build and containerize a simple Java “Hello World” application using Docker.

### Steps

1. **Create a Folder**  
   Create a folder named `lab-10-java-app`. Inside it, you’ll store your Java source code and Dockerfile.

2. **Write a Simple Java Program**  
   Create a file called `Main.java`:
   ```java
   public class Main {
       public static void main(String[] args) {
           System.out.println("Hello from Dockerized Java!");
       }
   }
   ```

3. **Add a `pom.xml`** (for Maven)  
   If you’re using Maven, create a `pom.xml` with minimal configuration:
   ```xml
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
            http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>

       <groupId>com.example</groupId>
       <artifactId>java-docker-example</artifactId>
       <version>1.0-SNAPSHOT</version>

       <properties>
           <project.build.sourceEncoding>UTF-8</project.build.sourceEncoding>
           <maven.compiler.source>17</maven.compiler.source>
           <maven.compiler.target>17</maven.compiler.target>
       </properties>

       <dependencies>
           <!-- No dependencies needed for a simple Hello World -->
       </dependencies>
   </project>
   ```

4. **Create a Multi-Stage Dockerfile**  
   ```dockerfile
   # Stage 1: Build the Java application using Maven
   FROM maven:3.8.6-openjdk-17 AS build
   WORKDIR /app

   # Copy the Maven project files
   COPY pom.xml ./
   RUN mvn dependency:go-offline

   # Copy source code
   COPY src ./src

   # Build the JAR file
   RUN mvn package -DskipTests

   # Stage 2: Run the Java application
   FROM openjdk:17-jdk-alpine
   WORKDIR /app

   # Copy the JAR file from the build stage
   COPY --from=build /app/target/java-docker-example-1.0-SNAPSHOT.jar app.jar

   # Run the JAR
   CMD ["java", "-jar", "app.jar"]
   ```

5. **Build and Run**  
   ```bash
   cd lab-10-java-app
   docker build -t java-docker-example .
   docker run --rm java-docker-example
   ```
   - You should see the output:  
     **`Hello from Dockerized Java!`**

### Exercises
- **Add Dependencies**: Include a small library (e.g., `org.apache.commons:commons-lang3`) and use it in your `Main.java`.
- **Use a Different Base Image**: Try using a different Java base (e.g., `eclipse-temurin:17-alpine`).
- **Run vs. Build**: Experiment with changing the `mvn package` step to also run tests (remove `-DskipTests`).

---

## Lab 11: Dockerizing a Spring Boot Application

**Goal**: Build and containerize a **Spring Boot** application with Docker.

### Steps

1. **Generate a Spring Boot Project**  
   Use [Spring Initializr](https://start.spring.io/) or create a `pom.xml` manually. For this example, let’s assume you have a minimal Spring Boot project with a single `HelloController`.

2. **Project Structure Example**  
   ```
   lab-11-spring-boot/
   ├── src
   │   └── main
   │       └── java
   │           └── com
   │               └── example
   │                   └── demo
   │                       ├── DemoApplication.java
   │                       └── HelloController.java
   ├── pom.xml
   └── Dockerfile
   ```

3. **Sample `HelloController.java`**  
   ```java
   package com.example.demo;

   import org.springframework.web.bind.annotation.GetMapping;
   import org.springframework.web.bind.annotation.RestController;

   @RestController
   public class HelloController {

       @GetMapping("/")
       public String hello() {
           return "Hello from Spring Boot on Docker!";
       }
   }
   ```

4. **Sample `DemoApplication.java`**  
   ```java
   package com.example.demo;

   import org.springframework.boot.SpringApplication;
   import org.springframework.boot.autoconfigure.SpringBootApplication;

   @SpringBootApplication
   public class DemoApplication {
       public static void main(String[] args) {
           SpringApplication.run(DemoApplication.class, args);
       }
   }
   ```

5. **Minimal `pom.xml`**  
   ```xml
   <project xmlns="http://maven.apache.org/POM/4.0.0"
            xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
            xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
                                http://maven.apache.org/xsd/maven-4.0.0.xsd">
       <modelVersion>4.0.0</modelVersion>
       <groupId>com.example</groupId>
       <artifactId>demo</artifactId>
       <version>0.0.1-SNAPSHOT</version>
       <name>demo</name>
       <description>Demo project for Spring Boot</description>

       <parent>
           <groupId>org.springframework.boot</groupId>
           <artifactId>spring-boot-starter-parent</artifactId>
           <version>3.1.1</version>
           <relativePath/>
       </parent>

       <properties>
           <java.version>17</java.version>
       </properties>

       <dependencies>
           <dependency>
               <groupId>org.springframework.boot</groupId>
               <artifactId>spring-boot-starter-web</artifactId>
           </dependency>
       </dependencies>

       <build>
           <plugins>
               <plugin>
                   <groupId>org.springframework.boot</groupId>
                   <artifactId>spring-boot-maven-plugin</artifactId>
               </plugin>
           </plugins>
       </build>
   </project>
   ```

6. **Dockerfile** (Multi-Stage)  
   ```dockerfile
   # Stage 1: Build the Spring Boot application
   FROM maven:3.8.6-openjdk-17 AS build
   WORKDIR /app

   COPY pom.xml ./
   RUN mvn dependency:go-offline

   COPY src ./src
   RUN mvn package -DskipTests

   # Stage 2: Create the runtime image
   FROM openjdk:17-jdk-alpine
   WORKDIR /app

   # Copy the generated JAR from the build stage
   COPY --from=build /app/target/demo-0.0.1-SNAPSHOT.jar app.jar

   # Expose port 8080
   EXPOSE 8080

   # Run the JAR
   CMD ["java", "-jar", "app.jar"]
   ```

7. **Build and Run the Container**  
   ```bash
   cd lab-11-spring-boot
   docker build -t spring-boot-demo .
   docker run -d -p 8080:8080 spring-boot-demo
   ```
   - Go to `http://localhost:8080` (or the Docker host IP) in your browser.  
   - You should see: **`Hello from Spring Boot on Docker!`**

### Exercises
- **Enable Tests**: Remove the `-DskipTests` flag to run your tests during the build stage.  
- **Add Profiles**: Use Spring profiles to handle different environments (dev/prod). Pass `SPRING_PROFILES_ACTIVE=prod` with `-e` or in your Dockerfile.  
- **Optimize the Build**: Try [layers in Spring Boot](https://docs.spring.io/spring-boot/docs/current/maven-plugin/reference/htmlsingle/#repackaging-layers) to improve build times.  


---

## Contributing
Contributions are welcome! If you have any improvements or additional labs to suggest, please open a pull request or create an issue.

## License
This repository is licensed under the [MIT License](LICENSE).

---

## Feedback
If you have questions, reach out via [GitHub Issues](../../issues) or feel free to fork this repo and submit pull requests with your own enhancements.

Happy Docker-building!
