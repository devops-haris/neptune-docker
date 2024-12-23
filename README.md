```markdown
# Docker Basics Lab

A beginner-friendly lab for learning Docker on AWS Ubuntu EC2. This guide walks through:

1. [Launch an Ubuntu AWS EC2 Instance](#section-1-launch-an-ubuntu-aws-ec2-instance)  
2. [Install Docker on Ubuntu](#section-2-install-docker-on-ubuntu)  
3. [Exercise 1: Hello World with Docker](#exercise-1-hello-world-with-docker)  
4. [Exercise 2: Exploring OS Containers (Ubuntu, Debian, Alpine)](#exercise-2-exploring-os-containers-ubuntu-debian-alpine)  
5. [Exercise 3: Working with `-it` (Interactive) Containers](#exercise-3-working-with--it-interactive-containers)  
6. [Exercise 4: Using `attach` and `exec`](#exercise-4-using-attach-and-exec)  
7. [Exercise 5: Understanding Docker Images and Layers](#exercise-5-understanding-docker-images-and-layers)  
8. [Exercise 6: Running an HTTP Server Container (Nginx or Apache)](#exercise-6-running-an-http-server-container-nginx-or-apache)  
9. [Exercise 7: Using `-d`, `-p`, and `--name`](#exercise-7-using--d---p-and---name)  
10. [Exercise 8: Working with Volumes and Persistent Data](#exercise-8-working-with-volumes-and-persistent-data)  
11. [Exercise 9: Running a MySQL Container](#exercise-9-running-a-mysql-container)  
12. [Exercise 10: Running a Postgres Container](#exercise-10-running-a-postgres-container)  
13. [Exercise 11: Running a Java Container](#exercise-11-running-a-java-container)  
14. [Exercise 12: Cleaning Up Containers and Images](#exercise-12-cleaning-up-containers-and-images)  
15. [Exercise 13: Docker Logs, Stats, and Top](#exercise-13-docker-logs-stats-and-top)  
16. [Exercise 14 (Optional Intro): Docker Compose](#exercise-14-optional-intro-docker-compose)  
17. [Exercise 15: Practice Project - Simple Multi-Container Setup](#exercise-15-practice-project---simple-multi-container-setup)  

---

## Section 1: Launch an Ubuntu AWS EC2 Instance

1. **Log in to AWS Console**  
   - Go to [AWS Management Console](https://aws.amazon.com/console/) and log in.

2. **Launch an EC2 Instance**  
   - Navigate to **EC2**.
   - Click **Launch Instances**.
   - Choose the **Ubuntu Server** AMI (latest LTS version).
   - Choose an instance type (e.g., `t2.micro`).
   - Configure a **Security Group**:
     - Allow **SSH** (port 22).
     - If needed for HTTP server, allow **HTTP** (port 80).
   - Launch the instance.

3. **SSH into the Instance**  
   - From your local terminal:
     ```bash
     ssh -i /path/to/your/key.pem ubuntu@<EC2-Public-IP>
     ```
   - Accept the prompt when asked.

---

## Section 2: Install Docker on Ubuntu

1. **Update packages**:
   ```bash
   sudo apt-get update
   ```

2. **Install prerequisites**:
   ```bash
   sudo apt-get install \
     ca-certificates \
     curl \
     gnupg \
     lsb-release -y
   ```

3. **Add Docker’s official GPG key**:
   ```bash
   curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /usr/share/keyrings/docker-archive-keyring.gpg
   ```

4. **Set up the stable repository**:
   ```bash
   echo \
     "deb [arch=$(dpkg --print-architecture) signed-by=/usr/share/keyrings/docker-archive-keyring.gpg] https://download.docker.com/linux/ubuntu \
     $(lsb_release -cs) stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
   ```

5. **Install Docker Engine**:
   ```bash
   sudo apt-get update
   sudo apt-get install docker-ce docker-ce-cli containerd.io -y
   ```

6. **Verify Docker installation**:
   ```bash
   docker --version
   ```
   - You should see the Docker version output.

(Optional) **Add your user to the Docker group** (to avoid `sudo`):
```bash
sudo usermod -aG docker $USER
```
Then **log out** and **log back in** for the changes to take effect.

---

## Exercise 1: Hello World with Docker

1. **Pull and run the hello-world image**:
   ```bash
   docker run hello-world
   ```
2. **Observe**:
   - Docker pulls the image if it’s not found locally.
   - The container prints a hello message, then exits.

**Key Takeaways**  
- `docker run` fetches the image automatically if not present locally.  
- Understand basic container lifecycle (start → print → exit).

---

## Exercise 2: Exploring OS Containers (Ubuntu, Debian, Alpine)

1. **Run an Ubuntu container interactively**:
   ```bash
   docker run -it ubuntu /bin/bash
   ```
   - You are now in the Ubuntu container’s shell.
   - Try `ls`, `pwd`, `cat /etc/os-release`.
   - Type `exit` to exit the container.

2. **Run a Debian container**:
   ```bash
   docker run -it debian /bin/bash
   ```
   - Similar commands as above.

3. **Run an Alpine container**:
   ```bash
   docker run -it alpine /bin/sh
   ```
   - Alpine has fewer default commands installed.

**Key Takeaways**  
- `docker run -it <image> /bin/bash|sh` for an interactive shell.  
- Different base images (Ubuntu, Debian, Alpine) vary in size and available packages.

---

## Exercise 3: Working with `-it` (Interactive) Containers

1. **What do `-i` and `-t` mean?**  
   - `-i` = keep STDIN open (interactive).  
   - `-t` = allocate a pseudo-TTY (terminal).

2. **Experiment**:
   - Run Ubuntu with only `-i`:
     ```bash
     docker run -i ubuntu /bin/bash
     ```
     - Notice the lack of a normal prompt.
   - Run Ubuntu with `-i -t`:
     ```bash
     docker run -it ubuntu /bin/bash
     ```
     - Now you have a proper interactive shell.

**Key Takeaways**  
- `-it` is commonly used together for an interactive terminal session.

---

## Exercise 4: Using `attach` and `exec`

1. **Start a container in the background**:
   ```bash
   docker run -d ubuntu sleep 300
   ```
   - The container will run and sleep for 300 seconds.

2. **List running containers**:
   ```bash
   docker ps
   ```

3. **Attach to the running container**:
   ```bash
   docker attach <container-id>
   ```
   - You’ll connect to its STDOUT (which might be silent while it sleeps).
   - Pressing `Ctrl+C` might stop the container, depending on the process.

4. **Use `exec` to open a shell**:
   ```bash
   docker exec -it <container-id> /bin/bash
   ```
   - This opens a new shell in the container.
   - Type `exit` to leave the shell without stopping the container.

5. **Stop the container**:
   ```bash
   docker stop <container-id>
   ```

**Key Takeaways**  
- `docker attach` connects to the main process.  
- `docker exec` starts a new process (often a shell) inside the container.

---

## Exercise 5: Understanding Docker Images and Layers

1. **List all images**:
   ```bash
   docker images
   ```

2. **Inspect an image**:
   ```bash
   docker inspect ubuntu
   ```
   - Shows JSON data about layers, env variables, etc.

3. **Pull multiple tags**:
   ```bash
   docker pull ubuntu:18.04
   docker pull ubuntu:20.04
   ```
   - Compare sizes with `docker images`.

**Key Takeaways**  
- Docker images are made of read-only layers.  
- Tagging (e.g., `ubuntu:18.04`) helps version images.

---

## Exercise 6: Running an HTTP Server Container (Nginx or Apache)

1. **Pull the Nginx (or Apache HTTPD) image**:
   ```bash
   docker pull nginx
   ```
   - Or:
     ```bash
     docker pull httpd
     ```

2. **Run the container**:
   ```bash
   docker run -d -p 80:80 --name webserver nginx
   ```

3. **Check logs**:
   ```bash
   docker logs webserver
   ```

4. **Access from the browser**:
   - Go to `http://<EC2-Public-IP>` to see the default web page.

**Key Takeaways**  
- `-p 80:80` maps container’s port 80 to host’s port 80.  
- `--name` assigns a friendly name to the container.  
- `-d` runs the container in detached mode.

---

## Exercise 7: Using `-d`, `-p`, and `--name`

1. **Try different port mappings**:
   ```bash
   docker run -d -p 8080:80 --name mynginx nginx
   ```
   - Access at `http://<EC2-Public-IP>:8080`.

2. **List running containers**:
   ```bash
   docker ps
   ```

3. **Stop and remove a container**:
   ```bash
   docker stop mynginx
   docker rm mynginx
   ```

4. **Re-run** to solidify knowledge.

**Key Takeaways**  
- `-d` (detached): runs container in background.  
- `-p HOST_PORT:CONTAINER_PORT`: port mapping.  
- `--name`: label containers for easy management.

---

## Exercise 8: Working with Volumes and Persistent Data

1. **Create a named volume**:
   ```bash
   docker volume create mydata
   ```

2. **Run a container using this volume**:
   ```bash
   docker run -d \
     -p 8080:80 \
     --name myweb \
     -v mydata:/usr/share/nginx/html \
     nginx
   ```

3. **Inspect the volume**:
   ```bash
   docker volume inspect mydata
   ```

4. **Place an `index.html` inside the volume**:
   ```bash
   docker exec -it myweb /bin/bash
   echo "<h1>Hello from Docker Volume</h1>" > /usr/share/nginx/html/index.html
   exit
   ```
   - Then visit `http://<EC2-Public-IP>:8080`.

**Key Takeaways**  
- Volumes keep data outside the container’s ephemeral filesystem.  
- Removing containers does not remove named volumes by default.

---

## Exercise 9: Running a MySQL Container

1. **Pull the MySQL image**:
   ```bash
   docker pull mysql:latest
   ```

2. **Run MySQL in a container**:
   ```bash
   docker run -d \
     --name mymysql \
     -e MYSQL_ROOT_PASSWORD=secret \
     -p 3306:3306 \
     mysql:latest
   ```

3. **Check logs**:
   ```bash
   docker logs mymysql
   ```

4. **Connect to MySQL shell**:
   ```bash
   docker exec -it mymysql mysql -u root -p
   ```
   - Enter `secret` when prompted.

**Key Takeaways**  
- `-e` sets environment variables (e.g., passwords).  
- MySQL requires a root password to start.  
- `docker exec` lets you run commands inside the container.

---

## Exercise 10: Running a Postgres Container

1. **Pull Postgres image**:
   ```bash
   docker pull postgres:latest
   ```

2. **Run Postgres**:
   ```bash
   docker run -d \
     --name mypostgres \
     -e POSTGRES_PASSWORD=secret \
     -p 5432:5432 \
     postgres:latest
   ```

3. **View logs**:
   ```bash
   docker logs mypostgres
   ```

4. **Connect to Postgres shell**:
   ```bash
   docker exec -it mypostgres psql -U postgres
   ```

**Key Takeaways**  
- Similar to MySQL setup, but using Postgres environment variables.  
- `psql` is the Postgres client.

---

## Exercise 11: Running a Java Container

1. **Pull an OpenJDK image**:
   ```bash
   docker pull openjdk:latest
   ```

2. **Check Java version**:
   ```bash
   docker run --rm openjdk:latest java -version
   ```
   - `--rm` removes the container after it exits.

3. **Run a simple Java app**:
   - Create a `Hello.java` on your host:
     ```java
     public class Hello {
       public static void main(String[] args) {
         System.out.println("Hello from Docker Java!");
       }
     }
     ```
   - Compile using the container:
     ```bash
     docker run --rm -v "$PWD":/usr/src/app -w /usr/src/app openjdk:latest javac Hello.java
     ```
   - Run it:
     ```bash
     docker run --rm -v "$PWD":/usr/src/app -w /usr/src/app openjdk:latest java Hello
     ```
   - You should see `Hello from Docker Java!`.

**Key Takeaways**  
- `-v $PWD:/usr/src/app` mounts current directory to the container.  
- `-w /usr/src/app` sets container working directory.  
- `--rm` cleans up the container on exit.

---

## Exercise 12: Cleaning Up Containers and Images

1. **List all containers (including stopped)**:
   ```bash
   docker ps -a
   ```

2. **Remove a specific container**:
   ```bash
   docker rm <container-id-or-name>
   ```

3. **Remove all stopped containers**:
   ```bash
   docker container prune
   ```

4. **List images**:
   ```bash
   docker images
   ```

5. **Remove an image**:
   ```bash
   docker rmi <image-id>
   ```

6. **Remove unused images**:
   ```bash
   docker image prune
   ```

**Key Takeaways**  
- Use `docker rm`, `docker rmi` to remove containers and images.  
- Prune commands (`container prune`, `image prune`) help reclaim space.

---

## Exercise 13: Docker Logs, Stats, and Top

1. **Logs**:
   - `docker logs -f <container-name>` to follow logs.

2. **Stats**:
   ```bash
   docker stats
   ```
   - Shows container CPU/memory usage.

3. **Top**:
   ```bash
   docker top <container-name>
   ```
   - Lists processes running inside the container.

**Key Takeaways**  
- Monitoring and debugging tools: logs, stats, top.  
- `-f` (follow) streams logs live.

---

## Exercise 14 (Optional Intro): Docker Compose

1. **Install Docker Compose**:
   ```bash
   sudo apt-get install docker-compose -y
   ```

2. **Create a `docker-compose.yml`** (example):
   ```yaml
   version: '3'
   services:
     web:
       image: nginx:alpine
       ports:
         - "80:80"
     db:
       image: mysql:5.7
       environment:
         - MYSQL_ROOT_PASSWORD=secret
   ```

3. **Start services**:
   ```bash
   docker-compose up -d
   ```

4. **Check**:
   - `docker-compose ps`
   - `docker-compose logs -f`

**Key Takeaways**  
- Compose orchestrates multi-container applications with a single configuration file.

---

## Exercise 15: Practice Project - Simple Multi-Container Setup

**Goal**: Combine a web container with a database container using Docker Compose.

1. **Create a `docker-compose.yml`**:
   ```yaml
   version: '3.8'
   services:
     web:
       image: node:14
       container_name: mynodeapp
       volumes:
         - ./app:/usr/src/app
       working_dir: /usr/src/app
       command: ["node", "index.js"]
       ports:
         - "3000:3000"
       depends_on:
         - db
     db:
       image: mysql:5.7
       container_name: mysql-db
       environment:
         - MYSQL_DATABASE=mydb
         - MYSQL_USER=user
         - MYSQL_PASSWORD=pass
         - MYSQL_ROOT_PASSWORD=secret
       volumes:
         - dbdata:/var/lib/mysql
   volumes:
     dbdata:
   ```

2. **Create an `index.js` file in `./app`**:
   ```js
   const express = require('express');
   const app = express();
   app.get('/', (req, res) => {
     res.send('Hello from Node + Docker + MySQL!');
   });
   app.listen(3000, () => {
     console.log('Server running on port 3000');
   });
   ```

3. **Run**:
   ```bash
   docker-compose up -d
   ```

4. **Test**:
   - Go to `http://<EC2-Public-IP>:3000` and see `Hello from Node + Docker + MySQL!`.

**Key Takeaways**  
- Multiple containers (Node + MySQL) managed with Docker Compose.  
- A volume `dbdata` for persistent database storage.

---

## Conclusion

You’ve covered:
- **AWS EC2** (Ubuntu) + **Docker** installation.
- Running basic containers (Ubuntu, Debian, Alpine).
- Working with interactive flags (`-i`, `-t`), commands (`attach`, `exec`).
- Running application containers (Nginx, MySQL, Postgres, Java).
- Exposing ports (`-p`), naming containers (`--name`), environment variables (`-e`).
- Persisting data with volumes (`-v`).
- Cleaning up with `prune`, `rmi`, `rm`.
- An intro to **Docker Compose** for multi-container setups.

Happy Dockering!
```
