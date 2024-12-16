#!/bin/bash

# Update and install OpenJDK 17
sudo apt update -y
sudo apt install openjdk-17-jre -y
sudo apt install maven -y
# Install Jenkins
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io-2023.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null
sudo apt-get update -y 
sudo apt-get install jenkins -y

# Start and enable Jenkins service
sudo systemctl enable jenkins
sudo systemctl start jenkins
sudo systemctl status jenkins
sudo systemctl restart jenkins
# Install Docker dependencies
sudo apt-get update
sudo apt-get install ca-certificates curl gnupg

sudo install -m 0755 -d /etc/apt/keyrings
curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
sudo chmod a+r /etc/apt/keyrings/docker.gpg

echo "deb [arch=$(dpkg --print-architecture) signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu $(. /etc/os-release && echo "$VERSION_CODENAME") stable" | sudo tee /etc/apt/sources.list.d/docker.list > /dev/null

sudo apt update

# Install Docker
sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y

# Install Docker Compose
sudo apt install docker-compose

# Restart Docker service
sudo systemctl docker restart

# Add current user to the docker group
sudo usermod -aG docker $USER
newgrp docker
ls -l /var/run/docker.sock

# Adjust Docker socket permissions
sudo chmod 666 /var/run/docker.sock (brute)
sudo systemctl restart docker

#### Docker Commands
# Check images a Docker container
docker images
# Run SonarQube in a Docker container
docker run -d --name sonar -p 9000:9000 sonarqube:lts-community
docker build -t my-java-app . (path)
docker run -p 8080:8080 my-java-app
docker run -d -p 8080:80 my-python-app
docker ps
docker ps -a

docker rm <container_name_or_id>
docker rmi <image_name_or_id>
docker container prune
docker image prune

docker stop <container_name_or_id>
docker build -t your_image_name .(path)

# Dockerfile
FROM openjdk:8u151-jdk-alpine3.7

RUN sh "sudo apt get mvn install \
              mvn test\
			  mvn package"

EXPOSE 8080

ENV APP_HOME /usr/src/app

COPY target/secretsanta-0.0.1-SNAPSHOT.jar $APP_HOME/app.jar

WORKDIR $APP_HOME

ENTRYPOINT exec java -jar app.
CMD ["java","-jar","app"]
/usr/lib/jvm/java-17-openjdk-amd64

docker login
docker tag swayam-secret:v3 swayam0/swayam-secret:v3
docker push swayam0/swayam-secret:v3

#Docker Multi Stage

#Docker Volume

docker run -d --mount source=swayam,target=/app demo-python (example)

docker volume create mongodb_data
docker volume create mongoexpress_config

docker run -d --network mongo-network --name mongodb \
    -e MONGO_INITDB_ROOT_USERNAME=admin \
    -e MONGO_INITDB_ROOT_PASSWORD=admin \
    mongo

docker run -d --network mongo-network --name mongoexpress \
    -e ME_CONFIG_MONGODB_SERVER=mongodb \
    -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin \
    -e ME_CONFIG_MONGODB_ADMINPASSWORD=admin \
    -e ME_CONFIG_BASICAUTH_USERNAME=admin2 \
    -e ME_CONFIG_BASICAUTH_PASSWORD=admin2 \
    -p 8081:8081 \
    mongo-express



#Docker Networking
docker network create --driver bridge mongo-network
docker run -d --network mongo-network --name mongodb \
	-e MONGO_INITDB_ROOT_USERNAME=admin \
	-e MONGO_INITDB_ROOT_PASSWORD=admin \
	mongo
	
docker run -d --network mongo-network --name mongoexpress \
  -e ME_CONFIG_MONGODB_SERVER=mongodb \
  -e ME_CONFIG_MONGODB_ADMINUSERNAME=admin \
  -e ME_CONFIG_MONGODB_ADMINPASSWORD=admin \
  -e ME_CONFIG_BASICAUTH_USERNAME=admin2\
  -e ME_CONFIG_BASICAUTH_PASSWORD=admin2\
  -p 8081:8081 \
  mongo-express

docker exec -it container_id bin/bash
# Docker with Jenkins plugins
Docker Pipeline
docker-build-step
Docker
CloudBees Docker Build and Publish plugin


#DOCKER Multi Stage

############################
###		SINGLE           ###
############################
docker build -t your_image_name -f dockerfile .(path)

FROM ubuntu

# Install dependencies
RUN apt update && \
    apt install -y openjdk-17-jre maven

# Set working directory
WORKDIR /usr/src/app

# Copy Maven project files
COPY . /usr/src/app

# Build the Maven project
RUN mvn package -Dmaven.test.skip=true

# Define environment variables
ENV APP_HOME /usr/src/app

# Copy the built JAR file
COPY target/secretsanta-0.0.1-SNAPSHOT.jar $APP_HOME/app.jar

# Expose port
EXPOSE 8080

# Run the application
CMD ["java", "-jar", "app.jar"]

#################################
Multi
##################################
# Stage 1: Build the application
FROM ubuntu

# Install dependencies
RUN apt update && \
    apt install -y openjdk-17-jre maven

# Set working directory
WORKDIR /usr/src/app

# Copy Maven project files
COPY . /usr/src/app

# Build the Maven project
RUN mvn package -Dmaven.test.skip=true

# Stage 2: Create a lightweight image to run the application
FROM gcr.io/distroless/java17-debian11

WORKDIR /usr/src/app

# Copy the built JAR from the previous stage
COPY --from=build /usr/src/app/target/secretsanta-0.0.1-SNAPSHOT.jar app.jar

# Expose port
EXPOSE 8080

# Run the application
CMD ["java","-jar","app.jar"]


# Trivy commands


sudo apt-get install wget apt-transport-https gnupg lsb-release

wget -qO - https://aquasecurity.github.io/trivy-repo/deb/public.key | gpg --dearmor | sudo tee /usr/share/keyrings/trivy.gpg > /dev/null

echo "deb [signed-by=/usr/share/keyrings/trivy.gpg] https://aquasecurity.github.io/trivy-repo/deb $(lsb_release -sc) main" | sudo tee -a /etc/apt/sources.list.d/trivy.list

sudo apt-get update

sudo apt-get install trivy -y

trivy repo --format table -o report.txt https://github.com/jaiswaladi2468/e-kart.git
trivy fs --format json -o jsonreport.json . 
trivy fs --format table -o jsonreport.txt . 
 
trivy image --format table -o sonartrivy.txt sonarqube:lts-community

trivy fs --format table -o filescan.txt --severity HIGH,CRITICAL .

trivy fs --format table -o filescanv.txt --scanners vuln,misconfig --severity HIGH,CRITICAL .

#OWASP Dependency Chect



#ssh
Run this inside you master node (not in slave node)
ssh-keyscan -H -p slave_host >> /var/lib/jenkins/.ssh/known_hosts

/opt/jenkins-agent


#install nexus
sudo apt update
sudo apt install openjdk-8-jdk -y
cd /opt
wget https://download.sonatype.com/nexus/3/nexus-3.64.0-04-unix.tar.gz
tar -xvf nexus-3.64.0-04-unix.tar.gz
adduser nexus
chown -R nexus:nexus sonatype-work/
chown -R nexus:nexus nexus-3.64.0-04/
sed -i 's/"nexus"/nexus/' /opt/nexus-3.64.0-04/bin/nexus.rc

rm -rf nexus-3.64.0-04-unix.tar.gz 
#vi nexus-3.64.0-04/bin/nexus.rc
#in ""  put nexus  --->"nexus"

/opt/nexus-3.64.0-04/bin/nexus start
ps aux | grep nexus

cat /opt/sonatype-work/nexus3/admin.password 

cat /opt/sonatype-work/nexus3/log/nexus.log
config file provider
pipeline maven integration
==============================================================================================================

#Kubernetes
kubectl get nodes
kubectl apply -f pod.yaml
kubectl get pods
kubectl get pods -o wide
kubectl delete pod <pod name>
kubectl get pods -l team=integration
kubectl logs <pod name>
kubectl describe pod <pod name>
kubectl exec -n <NAMESPACE> <POD_NAME> --container <CONTAINER_NAME> -it -- /bin/bash
kubectl get deploy
kubectl get all -A
kubectl get rs
kubectl get pods -w
kubectl apply -f deployment.yaml
kubectl port-forward service/nginx-service 8083:8082 [debug]
kubectl logs -f <pod-name>
kubectl get endpoints
kubectl describe service/nginx-service
