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
