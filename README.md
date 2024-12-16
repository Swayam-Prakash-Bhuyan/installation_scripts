# Installation Scripts

This repository contains a collection of scripts for setting up essential tools and services required for software development and deployment. The scripts include installations for Jenkins, Docker, Docker Compose, OpenJDK, Maven, Nexus, Kubernetes, and other necessary components. It also provides configurations for running applications in Docker containers, building and pushing images, and using various security tools such as Trivy and OWASP Dependency Check.

## Table of Contents

1. [Installation of OpenJDK 17 and Maven](#installation-of-openjdk-17-and-maven)
2. [Installing Jenkins](#installing-jenkins)
3. [Setting Up Docker and Docker Compose](#setting-up-docker-and-docker-compose)
4. [Docker Commands and Operations](#docker-commands-and-operations)
5. [Dockerfile Example](#dockerfile-example)
6. [Trivy Installation and Commands](#trivy-installation-and-commands)
7. [OWASP Dependency Check](#owasp-dependency-check)
8. [Nexus Installation](#nexus-installation)
9. [Kubernetes Commands](#kubernetes-commands)
10. [SSH Configuration for Jenkins](#ssh-configuration-for-jenkins)

---

## Installation of OpenJDK 17 and Maven

To install OpenJDK 17 and Maven on a Debian-based system (like Ubuntu), run the following commands:

```bash
# Update and install OpenJDK 17 and Maven
sudo apt update -y
sudo apt install openjdk-17-jre -y
sudo apt install maven -y
```

## Installing Jenkins

Install Jenkins by following these steps:

```bash
# Add Jenkins repository and key
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

# Update and install Jenkins
sudo apt-get update -y 
sudo apt-get install jenkins -y

# Start and enable Jenkins service
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
sudo systemctl restart jenkins
```

## Setting Up Docker and Docker Compose

Install Docker and Docker Compose with the following steps:

```bash
# Install Docker dependencies
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg -y

# Add Docker GPG key and repository
sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

# Add Docker repository
echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

# Install Docker and Docker Compose
sudo apt update -y
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
sudo apt install docker-compose -y

# Restart Docker service
sudo systemctl restart docker

# Add the current user to the Docker group
sudo usermod -aG docker $USER
newgrp docker
```

## Docker Commands and Operations

Here are a few commonly used Docker commands:

```bash
# Check images
docker images

# Run SonarQube container
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community

# Build and run Java application container
docker build -t my-java-app .
docker run -p 8080:8080 my-java-app

# View running containers
docker ps

# Stop and remove containers
docker stop <container_name_or_id>
docker rm <container_name_or_id>

# Remove images
docker rmi <image_name_or_id>

# Clean up unused containers and images
docker container prune
docker image prune
```

## Dockerfile Example

Here's an example of a `Dockerfile` used to build a Java application:

```Dockerfile
FROM openjdk:8u151-jdk-alpine3.7

# Install dependencies and build the application
RUN sh "sudo apt get mvn install \
              mvn test \
              mvn package"

EXPOSE 8080

# Set working directory
ENV APP_HOME /usr/src/app

# Copy the built JAR into the container
COPY target/secretsanta-0.0.1-SNAPSHOT.jar $APP_HOME/app.jar

# Set the working directory
WORKDIR $APP_HOME

# Run the application
ENTRYPOINT exec java -jar app.jar
CMD ["java", "-jar", "app.jar"]
```

## Trivy Installation and Commands

To install and use Trivy for scanning Docker images and file systems for vulnerabilities:

```bash
# Install Trivy
sudo apt-get install wget apt-transport-https gnupg lsb-release -y
wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null
echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list
sudo apt-get update
sudo apt-get install trivy -y

# Scan Docker images
trivy image --format table -o sonartrivy.txt sonarqube:lts-community

# Scan the file system for vulnerabilities
trivy fs --format table -o filescan.txt --severity HIGH,CRITICAL .
```

## OWASP Dependency Check

This script ensures that all dependencies in the project are checked for vulnerabilities using the OWASP Dependency Check tool. Refer to the [OWASP Dependency Check documentation](https://owasp.org/www-project-dependency-check/) for usage.

## Nexus Installation

To install Nexus Repository Manager:

```bash
# Install OpenJDK 8 for Nexus
sudo apt install openjdk-8-jdk -y

# Download and extract Nexus
cd /opt
wget https://download.sonatype.com/nexus/3/nexus-3.64.0-04-unix.tar.gz
tar -xvf nexus-3.64.0-04-unix.tar.gz
adduser nexus
chown -R nexus:nexus sonatype-work/
chown -R nexus:nexus nexus-3.64.0-04/

# Start Nexus
/opt/nexus-3.64.0-04/bin/nexus start

# View Nexus status and logs
ps aux | grep nexus
cat /opt/sonatype-work/nexus3/admin.password
```

## Kubernetes Commands

Here are some useful Kubernetes commands for managing pods, deployments, and services:

```bash
# Get nodes
kubectl get nodes

# Apply configuration from YAML files
kubectl apply -f pod.yaml

# Get pods and services
kubectl get pods
kubectl get pods -o wide
kubectl get services

# Describe pod or service
kubectl describe pod <pod_name>
kubectl describe service <service_name>

# View logs of a pod
kubectl logs <pod_name>

# Execute a command in a pod container
kubectl exec -it <pod_name> -- /bin/bash
```

## SSH Configuration for Jenkins

To configure SSH for Jenkins, especially for managing remote nodes:

```bash
# Add slave node to known hosts
ssh-keyscan -H -p slave_host >> /var/lib/jenkins/.ssh/known_hosts
```

---

## Contributing

Feel free to fork the repository and create pull requests for any updates or improvements.

---

## License

This project is open-source and available under the MIT License.
