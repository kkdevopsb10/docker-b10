# Docker Flow

## Docker Commands

### Checking Docker Images and Containers
```sh
docker images
```
```sh
docker ps  # Lists running containers
```
```sh
docker container ls  # Alternative to 'docker ps'
```

### Running Containers
```sh
docker run -d -p 80:80 --name nginxcontainer nginx:latest
```
```sh
docker run -d -p 8080:8080 --name tomcatcontainer tomcat:latest
```

### Checking Open Ports
```sh
netstat -tunlap
```
If `netstat` is not available, install it:
```sh
sudo apt install net-tools
netstat -tunlap
```

### Inspecting a Running Container
```sh
docker exec tomcat ls /usr/local/tomcat/webapps   # No host folder in the latest version
```
```sh
docker exec tomcat java -version  # Check Java version inside the container
```

### Removing and Restarting a Container
```sh
docker rm -f tomcat  # Force remove the Tomcat container
```
```sh
docker run -d -p 8080:8080 --name tomcat tomcat:6.0.43-jre8
```
```sh
docker exec tomcat ls /usr/local/tomcat/webapps
```

---

# Docker Objects

### Summary of Docker Objects
- **Containers**: Running instances of images.
- **Images**: Read-only templates used to create containers.
- **Volumes**: Persistent storage for container data.
- **Networks**: Facilitate communication between containers and external systems.
- **Registries**: Storage and distribution points for images.
- **Dockerfile**: Defines how to build a Docker image.
- **Docker Compose**: Manages multi-container Docker applications.

---

## What is Docker Build Context?
In Docker, the **build context** is the set of files and directories that are available to the Docker engine when building a Docker image. The build context includes the `Dockerfile` and any files required for the image build process.

---

# Practical Session: Understanding Docker

## Step 1: Verify Docker Installation
```sh
docker --version
```

## Step 2: Clone a Sample Web Application
```sh
git clone https://github.com/kkeducation12345/maven-web-app-project-kk-funda.git
cd maven-web-app-project-kk-funda
```

## Step 3: Install Maven and Java
```sh
sudo apt install maven -y  # Installs Maven and JRE
java --version  # Verify Java installation
mvn -version  # Verify Maven installation
```

## Step 4: Build the Application Artifact
```sh
mvn clean package
```
Ensure your `pom.xml` contains a valid Maven build configuration:
```xml
<build>
    <plugins>
        <plugin>
            <groupId>org.apache.maven.plugins</groupId>
            <artifactId>maven-war-plugin</artifactId>
            <version>3.3.2</version> <!-- Use a stable version -->
        </plugin>
    </plugins>
</build>
```

## Step 5: Create a Dockerfile
```sh
vi Dockerfile
```
Add the following content to the Dockerfile:
```dockerfile
FROM tomcat:9.0.100
COPY target/maven-web-application.war /usr/local/tomcat/webapps/maven-web-application.war
```

## Step 6: Build the Docker Image
```sh
docker build -t kkeducation12345/java-web-app .
```

## Step 7: Verify Docker Images
```sh
docker images  # OR
```
```sh
docker image ls
```

## Step 8: Push Image to Docker Hub
**Before pushing, authenticate to Docker Hub:**
```sh
docker login -u kkeducation12345 -p password
```
Push the image:
```sh
docker push kkeducation12345/java-web-app
```

## Step 9: Run a Container from the Image
```sh
docker run -d -p 8080:8080 --name javawebapp kkeducation12345/java-web-app
```

## Step 10: Verify Running Containers
```sh
docker ps -a  # Lists all containers
```

---

# Accessing a Running Container
To enter the running container's shell:
```sh
docker exec -it javawebapp /bin/bash
```


