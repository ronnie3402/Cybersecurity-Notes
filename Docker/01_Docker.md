## **Docker Master Study Notes: The Ultimate One-Shot Guide** 

## **Overview** 

This comprehensive guide covers Docker end-to-end, moving from fundamental concepts to advanced practical implementations. It explains why Docker is essential in modern software development, the architectural differences between Docker and Virtual Machines (VMs), details the Copy-on-Write (CoW) layer mechanism, and covers crucial workflows such as port binding, detached mode execution, and troubleshooting. 

The notes also include installation procedures for Linux and Windows, service management, complete Docker removal procedures, and a step-by-step breakdown of containerizing applications using Docker Compose. In addition, they explain Dockerfiles, persistent storage using Volumes, Docker Networks, and practical multi-container deployments involving a Node.js application and MongoDB. 

## **Section 1: Installation & Service Management** 

## **Kali Linux Installation & Permissions** 

The following steps install Docker on Kali Linux and configure the system so that Docker commands can be executed without using `sudo` . 

## **Step 1: Update the System** 

Update package repositories and upgrade installed packages. 

```
sudo apt update && sudo apt upgrade -y
```

## **Step 2: Install Docker Engine** 

Install Docker Engine from Kali Linux repositories. 

```
sudo apt install docker.io -y
```

## **Step 3: Add User to the Docker Group** 

By default, Docker commands require administrative privileges. To allow the current user to execute Docker commands without `sudo` , add the user to the Docker group. 

```
sudo usermod -aG docker $USER
```

## **Step 4: Activate New Group Permissions** 

Apply the new group membership without rebooting. 

```
newgrp docker
```

If permission errors still occur after executing the above command, close the current terminal session and open a new one. 

## **Linux Deep Clean Docker (Complete Removal)** 

To completely remove Docker and all associated data from a Linux system (such as Kali Linux or Ubuntu), follow the steps below. 

**Warning:** These steps permanently remove Docker, all containers, images, volumes, networks, and configuration files. 

## **Step 1: Stop Docker Services** 

Before uninstalling Docker, stop all Docker-related services. 

```
sudo systemctl stop docker.service
sudo systemctl stop docker.socket
```

Disable automatic startup: 

```
sudo systemctl disable docker.service
sudo systemctl disable docker.socket
```

## **Step 2: Remove Docker Packages** 

Remove Docker Engine and related packages. 

```
sudo apt purge docker.io docker-doc docker-compose docker-compose-v2
podman-docker containerd runc -y
```

Remove unused dependencies. 

```
sudo apt autoremove -y
```

## **Step 3: Delete Docker Data Directories** 

Docker stores images, containers, volumes, and networks inside `/var/lib/docker` . 

Remove all Docker data: 

```
sudo rm -rf /var/lib/docker
sudo rm -rf /var/lib/containerd
```

## **Step 4: Remove User Configuration Files** 

Delete Docker configuration files stored in the user's home directory. 

```
rm -rf ~/.docker
```

## **Step 5: Remove Docker Group (Optional)** 

If Docker will no longer be used on the system, remove the Docker group. 

```
sudo groupdel docker
```

## **Step 6: Verify Complete Removal** 

Check whether Docker has been completely removed. 

```
docker --version
```

Expected output: 

```
Command 'docker' not found
```

If this message appears, Docker has been successfully removed from the system. 

## **Manual Startup vs Auto-Start (Systemd Service Control)** 

Docker services can be manually controlled to conserve background system resources. 

## **Disable Automatic Startup at Boot** 

Disable Docker from automatically starting whenever the system boots. 

```
sudo systemctl disable docker
```

## **Start Docker Manually** 

Start Docker only when required. 

```
sudo systemctl start docker
```

## **Completely Stop Docker (Deep Sleep Mode)** 

```
sudo systemctl stop docker.service
sudo systemctl stop docker.socket
```

## **Explanation** 

`docker.socket` acts as a background listener. Even if the Docker service itself is stopped, executing any Docker command can automatically restart Docker through this socket. 

Therefore, if complete shutdown is required to save resources, both `docker.service` and `docker.socket` must be stopped. 

## **Docker Installation on Windows 11** 

## **System Requirements** 

- Windows 11 64-bit edition. 

- Hardware virtualization enabled in BIOS/UEFI. 

- WSL2 (Windows Subsystem for Linux version 2) enabled. 

- At least 4 GB RAM (8 GB or more recommended). 

## **Step 1: Enable WSL2** 

Open PowerShell as Administrator and execute: 

```
wsl --install
```

Restart the system if prompted. 

Verify the installation: 

```
wsl --status
```

## **Step 2: Enable Hardware Virtualization** 

Ensure virtualization technologies such as Intel VT-x or AMD-V are enabled from BIOS/UEFI. 

Virtualization status can be checked through: 

## **Task Manager → Performance → CPU → Virtualization** 

The status should display **Enabled** . 

## **Step 3: Download Docker Desktop** 

Download Docker Desktop and run the installer. 

## **Step 4: Install Docker Desktop** 

During installation: 

- Enable **Use WSL 2 instead of Hyper-V** if prompted. 

- Allow Docker to install required WSL components. 

- Restart the system after installation. 

## **Step 5: Verify the Installation** 

Open PowerShell or Command Prompt and execute: 

```
docker --version
docker compose version
```

## **Step 6: Run the First Test Container** 

Verify Docker functionality by running: 

```
docker run hello-world
```

Docker will automatically download the image, create a container, execute it, and display a success message. 

## **Windows 11 Deep Clean (Complete Removal)** 

To completely remove Docker Desktop and all associated data from Windows: 

## **Step 1: Exit Docker Desktop** 

Right-click the Docker Desktop icon from the system tray and select: 

## **Quit Docker Desktop** 

## **Step 2: Uninstall Docker Desktop** 

Navigate to: 

**Settings → Apps → Installed Apps → Docker Desktop → Uninstall** 

## **Step 3: Remove WSL Data** 

Open PowerShell as Administrator and execute: 

```
wsl --unregister docker-desktop
wsl --unregister docker-desktop-data
```

This permanently removes Docker's internal Linux environments. 

## **Step 4: Remove User Configuration Files** 

Navigate to: 

```
%USERPROFILE%
```

Delete the hidden folder: 

```
.docker
```

This removes all Docker Desktop configurations and cached settings. 

## **Section 2: Core Concepts & Architecture** 

## **1. What Exactly is Docker and Why Do We Need It?** 

Imagine developing a SOC analyst tool or a Node.js application on a machine where Node.js v16 and MongoDB v4.2 are installed. 

When this application is shared with another team member using a different operating system such as Windows, macOS, or another Linux distribution, they must manually install all required dependencies. This process often introduces version conflicts, OS incompatibilities, and the classic problem: 

"It works on my machine." 

Docker solves this problem by packaging the application and all its dependencies, libraries, and configurations into a single executable unit called a **Container** . 

As a result, the same application runs identically on every machine regardless of the underlying operating system. 

For example, instead of manually installing MongoDB locally, a developer can simply pull and run a MongoDB container. 

Docker can package anything—from a custom threat intelligence script to a complete web application—ensuring identical behavior across development, testing, and production environments. 

## **Technical Definition** 

Docker is an open-source platform that automates the deployment of applications inside lightweight, portable, self-sufficient containers using OS-level virtualization. 

## **Important Points** 

- Standardizes local development environments across teams. 

- Eliminates manual dependency installation errors. 

- Solves the "it works on my machine" problem. 

- Allows multiple versions of the same technology (for example, MySQL 8.0 and MySQL Latest) to run simultaneously on the same host without conflicts. 

- Ensures portability across Windows, Linux, macOS, and cloud servers. 

## **2. Docker Images vs Docker Containers** 

The relationship between Docker Images and Docker Containers is similar to the relationship between a **Class and an Object** in Object-Oriented Programming (OOP). 

## **Docker Image** 

A Docker Image is a **blueprint** , template, or static snapshot that contains everything required to run an application. 

It includes: 

- Application source code. 

- Required libraries. 

- Dependencies. 

- Runtime environment. 

- Configuration files. 

- Environment settings. 

An image itself does not consume CPU or RAM because it is not running. It simply acts as a reusable blueprint. 

For example: 

```
docker pull ubuntu
```

The above command downloads the Ubuntu image from Docker Hub. 

Another example: 

- `ubuntu` 

- `mysql` 

- `node` 

- `nginx` 

These are all Docker images available on Docker Hub. 

Images are typically shared among team members rather than sharing containers. 

## **Important Points** 

- Images are immutable (read-only). 

- Images are portable and reusable. 

- Images serve as blueprints for containers. 

- Multiple containers can be created from the same image. 

## **Docker Container** 

A Docker Container is a **running instance** of an image. 

Once an image is executed, Docker creates a live, isolated environment called a container. 

Unlike images, containers consume system resources such as: 

- CPU 

- RAM 

- Network resources 

- Storage 

Example: 

```
docker run -it ubuntu /bin/bash
```

When this command is executed, Docker creates and starts a live Ubuntu container based on the Ubuntu image. 

Containers are: 

- Lightweight. 

- Isolated. 

- Portable. 

- Fast to start. 

## **Important Points** 

- Containers are running instances of images. 

- Containers are temporary by default. 

- Multiple containers can be created from a single image. 

- Containers provide process-level isolation. 

## **3. Docker Containers vs Virtual Machines (VMs)** 

Docker and Virtual Machines both provide isolation, but they achieve it differently. 

## **Virtual Machines (VMs)** 

Virtual Machines perform virtualization at the **hardware level** . 

Each VM contains: 

- A complete Guest Operating System. 

- Its own kernel. 

- System libraries. 

- Application binaries. 

A hypervisor manages these virtual machines. 

Examples of hypervisors: 

- VMware Workstation 

- VirtualBox 

- Hyper-V 

## **Resource Allocation in VMs** 

A VM permanently reserves host resources. 

For example, if a security lab VM is assigned: 

- 4 GB RAM 

- 2 CPU cores 

- 50 GB storage 

then those resources remain allocated even when the guest OS is idle. 

Because each VM runs a complete operating system: 

- Boot times are slower. 

- Resource usage is higher. 

- Storage consumption is larger. 

## **VM Characteristics** 

- Heavyweight. 

- Hardware-level isolation. 

- Requires a hypervisor. 

- Slow startup times. 

- Strong security isolation (Hard Isolation). 

## **Docker Containers** 

Docker virtualizes only the **Application Layer** . 

Instead of running separate guest operating systems, containers share the host machine's kernel. 

Only the application and its required dependencies are packaged. 

For example, if a container needs only 50 MB RAM, it consumes approximately that amount. 

When the container stops, those resources are immediately released. 

Because containers share the host kernel: 

- Startup time is almost instantaneous. 

- Resource consumption is minimal. 

- Containers are significantly lighter than VMs. 

## **Docker Characteristics** 

- Lightweight. 

- Fast startup. 

- Uses Docker Engine instead of a hypervisor. 

- Shares the host OS kernel. 

- Provides application-level isolation (Soft Isolation). 

## **Comparison: Docker vs Virtual Machines** 

**Feature Virtual Machines Docker Containers** Virtualization Level Hardware Level Application Level Guest OS Required Not Required 

### VM Resource Allocation

A Virtual Machine (VM) permanently reserves hardware resources such as RAM, CPU, and storage from the host computer. For example, if a security lab VM is allocated 4 GB of RAM, the host system dedicates the entire 4 GB to that VM, even when the guest operating system is idle.

### Docker Efficiency

Docker virtualizes only the application layer and shares the host machine's kernel. If a container requires only 50 MB of RAM, it consumes only that amount. Once the container stops, the allocated resources are immediately released back to the host system.

|**Feature**|**Virtual Machines**|**Docker Containers**|
|---|---|---|
|Hypervisor|Required|Not Required|
|Boot Time|Slow|Very Fast|
|Resource Usage|High|Low|
|Size|Large|Small|
|Isolation|Hard Isolation|Soft Isolation|
|Startup Speed|Minutes|Seconds|
|Kernel|Separate Kernel per VM|Shared Host Kernel|



## **Security Consideration** 

Docker provides excellent isolation for applications. 

However, because containers share the host kernel, completely untrusted or highly dangerous malware should generally be detonated inside hardware-isolated Virtual Machines rather than Docker containers. 

## **4. Docker Architecture and Image Layers** 

A Docker image is not a single solid file. 

Instead, it is composed of multiple smaller layers stacked on top of each other. 

This layered architecture is often explained using the **Onion Model** or **Burger Model** . 

Example: 

```
Custom Application Code
───────────────────────
Application Dependencies
───────────────────────
Runtime Environment
───────────────────────
Base Operating System
```

or: 

```
Sauce  → Custom Code
Patty  → Python/Node Runtime
Bun    → Base OS
```

Each layer represents a specific modification to the image. 

Examples: 

- Base OS Layer. 

- Python Layer. 

- Node.js Layer. 

- Application Dependency Layer. 

- Custom Application Code Layer. 

## **Layer Caching** 

Docker intelligently caches layers. 

Suppose an image consists of five layers. 

If only the application code changes, Docker does not rebuild or download every layer again. 

Instead: 

- Existing unchanged layers are reused from cache. 

- Only modified layers are rebuilt. 

This significantly reduces: 

- Build time. 

- Download size. 

- Storage requirements. 

Example: 

If only a few lines of application code change, Docker will not re-download Ubuntu, Python, or Node.js layers. 

Only the changed application layer is rebuilt. 

## **Immutable (Read-Only) Layers** 

All image layers are immutable. 

This means they cannot be modified once created. 

Therefore: 

- Base image layers remain unchanged. 

- Existing layers are reused safely. 

- Images remain consistent and reproducible. 

## **Container Writeable Layer** 

As long as you remain at the image level, everything is read-only. 

The moment: 

```
docker run <image>
```

is executed, Docker creates a thin writeable layer on top of the read-only image layers. 

All runtime modifications occur inside this top writeable layer. 

Examples: 

- Creating files. 

- Editing files. 

- Installing packages. 

- Writing logs. 

## **5. Copy-on-Write (CoW) Mechanism** 

Docker uses a mechanism known as **Copy-on-Write (CoW)** . 

Suppose a container wants to modify a file stored in a read-only image layer. 

Docker does not modify the original file. 

Instead: 

1. Docker copies the original file from the read-only layer. 

2. The copied file is placed into the top writeable layer. 

3. All modifications occur only on this copied version. 

The original image remains untouched. 

This mechanism ensures: 

- Data consistency. 

- Efficient storage usage. 

- Fast container creation. 

- Safe image reuse. 

## **Ephemeral Nature of Containers** 

The writeable layer exists only while the container exists. 

When a container is removed: 

```
docker rm <container_id>
```

the entire writeable layer is destroyed. 

Consequently, all data stored exclusively inside the container is permanently lost. 

This temporary nature is referred to as the **Ephemeral Nature of Containers** . 

For persistent data storage, Docker Volumes should be used. 

## **6. Port Binding (Port Mapping)** 

## **Concept** 

By default, Docker containers are completely isolated from the outside world. If an application is running inside a container, users on the host machine cannot access it directly. 

For example, suppose a threat intelligence dashboard, web server, or database server is running inside a container. Even though the application is running successfully, it remains inaccessible from the host system because containers operate inside their own isolated network namespace. 

Port Binding creates a communication channel between the host machine and the container by mapping a port on the host system to a port inside the container. 

It can be visualized as creating a doorway between the outside world and an otherwise isolated room. 

## **Syntax** 

```
docker run -p <host_port>:<container_port> <image>
```

## **Example 1** 

```
docker run -d -p 8080:80 nginx
```

## **Explanation** 

- `8080` → Port on the host machine. 

- `80` → Port inside the container where Nginx is listening. 

- `-d` → Runs the container in detached (background) mode. 

Any request sent to: 

```
http://localhost:8080
```

will automatically be forwarded to port `80` inside the Nginx container. 

## **Example 2** 

```
docker run -p 8080:3306 mysql
```

## **Explanation** 

- `8080` → Host machine port. 

- `3306` → MySQL's internal container port. 

Any request arriving at: 

```
localhost:8080
```

will be forwarded by Docker to port `3306` inside the MySQL container. 

## **Important Points** 

- Containers are isolated by default. 

- Port mapping exposes container services to external systems. 

- Multiple containers cannot use the same host port simultaneously. 

- Internal container ports can remain unchanged while host ports can be customized. 

## **7. Docker Volumes (Data Persistence)** 

## **Why Are Volumes Needed?** 

One of Docker's most important characteristics is that containers are **ephemeral** . 

This means that when a container is deleted, everything stored inside its writeable layer is also deleted. 

For stateless applications this may not be a problem. 

However, for applications such as: 

- Databases 

- Log servers 

- SIEM platforms 

- File servers 

losing data is unacceptable. 

Docker Volumes solve this problem. 

## **Concept** 

A Docker Volume is a dedicated storage area located on the host machine that is mounted inside a container. 

Because the data physically resides outside the container, it remains safe even if the container is stopped, deleted, or recreated. 

The volume can later be attached to a new container, allowing previously stored data to be reused. 

## **Types of Volumes** 

## **1. Named Volumes** 

Docker automatically creates and manages storage locations. 

Named volumes are generally preferred in production environments. 

Create a named volume: 

```
docker volume create mydata
```

Example: 

```
docker run -d -v mydata:/var/lib/mysql mysql
```

In this example: 

- `mydata` → Docker-managed volume. 

- `/var/lib/mysql` → MySQL data directory inside the container. 

## **2. Bind Mounts** 

Bind mounts map a specific directory from the host machine into the container. 

Example on Linux/Kali: 

```
docker run -v /home/kali/folder:/app ubuntu
```

Example on Windows: 

```
docker run -v C:\Users\Username\Folder:/app ubuntu
```

Examples of host folders: 

- `/Desktop/data` 

- `/home/kali/logs` 

- `C:\Users\Username\Documents` 

## **Important Points** 

- Volumes provide persistent storage. 

- Data survives container deletion. 

- Named volumes are preferred for production environments. 

- Bind mounts provide direct access to host directories. 

- 

- Volumes are essential for databases. 

## **8. Docker Networking** 

## **Why Is Networking Needed?** 

Modern applications rarely consist of a single container. 

For example: 

- Web Server Container 

- Application Container 

- Database Container 

- Cache Container 

These containers must communicate with one another. 

Docker networking allows isolated containers to communicate securely. 

## **Concept** 

Docker creates isolated virtual networks. 

Containers attached to the same network can communicate with each other without exposing their internal services to the host machine. 

For example: 

A Node.js application container can communicate with a MongoDB container through a private Docker network without exposing MongoDB to external users. 

## **Creating a Custom Network** 

```
docker network create mongo-network
```

Containers connected to this network can communicate using container names instead of IP addresses. 

## **Docker Network Types** 

## **1. Bridge Network (Default)** 

The default network driver. 

Containers connected to the same bridge network can communicate with each other. 

Custom bridge networks additionally provide automatic DNS resolution. 

Example: 

```
docker network create my-network
```

## **2. Host Network** 

The container removes network isolation and directly uses the host machine's networking stack. 

Characteristics: 

- No NAT. 

- No port mapping required. 

- Highest networking performance. 

## **3. None Network** 

Provides complete network isolation. 

The container receives no network connectivity. 

This mode is useful for: 

- Sandboxed malware analysis. 

- Highly restricted environments. 

- Security research. 

## **Important Points** 

- Containers communicate securely using networks. 

- Custom bridge networks are preferred for multi-container applications. 

- Containers on the same network can resolve each other using container names. 

- Internal communication does not require exposing ports externally. 

## **Section 3: Container Lifecycle & Background Management** 

## **9. Detached Mode (Background Execution)** 

Many applications such as: 

- Web servers 

- Databases 

- Automated scanners 

- SIEM tools 

must continue running in the background. 

Docker provides Detached Mode for this purpose. 

Detached mode allows a container to run in the background without blocking the current terminal session. 

## **Command** 

```
docker run -d --name my_scanner ubuntu sleep 1000
```

## **Explanation** 

- `-d` → Detached mode. 

- `--name my_scanner` → Assigns a custom name. 

- `ubuntu` → Image used. 

- `sleep 1000` → Keeps the container alive. 

## **Output** 

The terminal will not remain occupied. 

Instead, Docker displays a long container ID and immediately returns control back to the shell, allowing additional commands to be executed. 

Example output: 

```
8e89dbe2e0c53f89b0...
```

## **10. Viewing Running Containers** 

To view currently running containers: 

```
docker ps
```

This command displays: 

- Container ID 

- Image 

- Command 

- Status 

- Ports 

- Names 

To display all containers, including stopped containers: 

```
docker ps -a
```

## **11. Bringing a Background Container to the** 

## **Foreground (Attach)** 

Suppose a container is already running in detached mode and you want to observe its live execution. 

Docker provides the `attach` command. 

## **Step 1** 

Identify the container: 

```
docker ps
```

## **Step 2** 

Attach to the container: 

```
docker attach my_scanner
```

The terminal will connect directly to the container's primary running process. 

You will now see the container's live output. 

## **12. Safely Detaching Without Stopping the Container** 

When attached to a container, do **not** use: 

```
exit
```

or 

```
Ctrl + C
```

Using these may terminate the main process and stop the container. 

To safely leave the container while keeping it running in the background: 

## **Step 1** 

Press: 

```
Ctrl + P
```

## **Step 2** 

Immediately press: 

```
Ctrl + Q
```

You will return to the host terminal while the container continues running in the background. 

This keyboard sequence is considered the safest method for leaving an attached container session. 

## **13. Viewing Container Logs** 

Often it is unnecessary to attach to a running container. 

Instead, logs can be viewed directly. 

Display container logs: 

```
docker logs <container_id>
```

View logs continuously in real time: 

```
docker logs -f <container_id>
```

The `-f` flag means: 

```
Follow logs continuously.
```

This is one of the most useful troubleshooting techniques for background containers. 

Typical use cases: 

- Monitoring application output. 

- Debugging errors. 

- Verifying startup processes. 

- 

- Monitoring automated tasks. 

## **14. Executing Commands Inside a Running Container** 

Frequently, administrators need to inspect or troubleshoot a running container. 

Docker provides the `exec` command. 

Open an interactive shell: 

```
docker exec -it <container_id> /bin/bash
```

Explanation: 

- `exec` → Execute command inside a running container. 

- `-i` → Interactive mode. 

- `-t` → Allocate terminal. 

- `/bin/bash` → Start Bash shell. 

This command is extremely useful for: 

- Troubleshooting applications. 

- Verifying files. 

- Installing temporary tools. 

- Inspecting logs and configurations. 

- Performing live debugging. 

## **Section 4: Practical Demo – Containerizing a Node.js Application with MongoDB** 

This practical demonstration shows how to containerize a Node.js application along with a MongoDB database using Docker. The workflow demonstrates how multiple containers can work together using Docker Networks, Docker Volumes, Dockerfiles, and Docker Compose. 

## **Step 1: Setting Up MongoDB and Mongo Express Using Docker** 

In this demonstration, two separate containers are used: 

1. **MongoDB Container** – Stores application data. 

2. **Mongo Express Container** – Provides a web-based graphical interface for managing MongoDB. 

Since both containers need to communicate with each other, they must be connected to the same Docker network. 

## **Creating a Custom Docker Network** 

Create a dedicated network: 

```
docker network create mongo-network
```

## **Why Create a Custom Network?** 

A custom network allows containers to: 

- Communicate securely. 

- Resolve each other using container names instead of IP addresses. 

- Remain isolated from unrelated containers. 

- Avoid exposing internal services to the external host. 

## **Running the MongoDB Container** 

The MongoDB container is started in detached mode and connected to the custom network. 

Example command: 

```
docker run -d \
-p 27017:27017 \
--name mongodb \
--network mongo-network \
-e MONGO_INITDB_ROOT_USERNAME=admin \
-e MONGO_INITDB_ROOT_PASSWORD=password \
mongo
```

## **Explanation** 

- `-d` → Run container in background. 

- `-p 27017:27017` → Expose MongoDB port. 

- `--name mongodb` → Assign container name. 

- `--network mongo-network` → Connect container to custom network. 

- `-e` → Pass environment variables. 

- `MONGO_INITDB_ROOT_USERNAME` → Create root username. 

- `MONGO_INITDB_ROOT_PASSWORD` → Create root password. 

## **`-e` Environment Variables ( )** 

Environment variables are dynamic configuration values passed into containers during runtime. 

They allow sensitive values to be configured externally without hardcoding them into the application. 

## Examples: 

- Database usernames. 

- Database passwords. 

- API keys. 

- Secret keys. 

- Configuration options. 

Example: 

- `-e MONGO_INITDB_ROOT_USERNAME=admin` 

```
-e MONGO_INITDB_ROOT_PASSWORD=password
```

## **Running Mongo Express Container** 

Mongo Express is a web-based administration interface for MongoDB. 

Run Mongo Express: 

```
docker run -d \
-p 8081:8081 \
--name mongo-express \
--network mongo-network \
-e ME_CONFIG_MONGODB_ADMINUSERNAME=admin \
-e ME_CONFIG_MONGODB_ADMINPASSWORD=password \
-e ME_CONFIG_MONGODB_SERVER=mongodb \
mongo-express
```

## **Explanation** 

- `8081` → Port exposed to host. 

- `ME_CONFIG_MONGODB_SERVER=mongodb` → Connects Mongo Express to the MongoDB container. 

- Since both containers are connected to `mongo-network` , Docker automatically resolves the container name `mongodb` . 

Access Mongo Express through: 

```
http://localhost:8081
```

## **Step 2: Automating the Environment Using Docker Compose** 

Typing long Docker commands repeatedly is inefficient and difficult to maintain. 

Docker Compose solves this problem. 

Docker Compose allows developers to define and manage multiple containers using a single YAML file. 

Instead of executing long commands manually, the entire infrastructure is described inside a configuration file. 

## **Docker Compose** 

Docker Compose is a tool used to define and run multi-container Docker applications. 

The configuration is stored inside a YAML file, typically: 

```
docker-compose.yml
```

or 

```
compose.yml
```

## **YAML (.yml)** 

YAML stands for: 

## **Yet Another Markup Language** 

It is a human-readable data serialization language used to define multi-container architectures. 

## **Example Docker Compose File** 

Example: 

```
version: '3'
services:
  mongo:
    image: mongo
    ports:
      - "27017:27017"
    environment:
      MONGO_INITDB_ROOT_USERNAME: admin
      MONGO_INITDB_ROOT_PASSWORD: password
    volumes:
      - mongo-data:/data/db
  mongo-express:
    image: mongo-express
    ports:
      - "8081:8081"
    environment:
      ME_CONFIG_MONGODB_ADMINUSERNAME: admin
      ME_CONFIG_MONGODB_ADMINPASSWORD: password
      ME_CONFIG_MONGODB_SERVER: mongo
volumes:
  mongo-data:
```

## **Persistent Storage in Docker Compose** 

The following line: 

- `mongo-data:/data/db` 

creates a persistent volume. 

This ensures that database data remains available even if the MongoDB container is removed. 

## **Starting Multi-Container Infrastructure** 

Start the entire environment: 

```
docker-compose -f mongodb.yml up -d
```

or 

```
docker compose up -d
```

## **Explanation** 

- `up` → Create and start containers. 

- `-d` → Run in detached mode. 

- `-f` → Specify a custom compose file. 

## **Stopping the Environment** 

Stop and remove all services defined in the Compose file: 

```
docker-compose down
```

or 

```
docker compose down
```

## **Step 3: Creating a Dockerfile for a Custom Node.js Application** 

Applications often require their own custom Docker images. 

A Docker image is created using a file called a **Dockerfile** . 

## **Dockerfile** 

A Dockerfile is a plain text file containing sequential instructions that Docker uses to build an image layer by layer. 

Each instruction creates a new image layer. 

## **Example Dockerfile** 

```
FROM node:latest
```

```
WORKDIR /app
COPY . .
```

```
RUN npm install
EXPOSE 3000
CMD ["node", "server.js"]
```

## **Dockerfile Instructions** 

## **`FROM`** 

Specifies the base image. 

Example: 

```
FROM node:latest
```

This image already contains: 

- Linux OS layer. 

- Node.js runtime. 

- Node package manager (npm). 

## **`WORKDIR`** 

Sets the working directory inside the container. 

Example: 

```
WORKDIR /app
```

All subsequent commands execute relative to this directory. 

## **`COPY`** 

Copies files from the host system into the image. 

Example: 

```
COPY . .
```

Explanation: 

- First `.` → Current directory on host. 

- Second `.` → Current working directory inside container. 

## **`RUN`** 

Executes commands during the image build process. 

Example: 

```
RUN npm install
```

This installs all Node.js dependencies during image creation. 

## **Important Point** 

`RUN` executes commands while building the image. 

## **`EXPOSE`** 

Documents which port the application uses. 

Example: 

```
EXPOSE 3000
```

## **`CMD`** 

Specifies the default command executed when a container starts. 

Example: 

```
CMD ["node", "server.js"]
```

## **Important Points** 

- Defines the container's main process. 

- Only one active `CMD` instruction should exist. 

- `CMD` executes when the container starts. 

## **RUN vs CMD** 

**Feature RUN** 

## **CMD** 

Execution Time During image build During container startup Purpose Install packages or configure image Start application Frequency Multiple allowed Typically one active instruction 

Example: 

```
RUN apt update
RUN apt install -y python3
CMD ["python3", "app.py"]
```

## **Step 4: Building the Custom Image** 

After creating the Dockerfile, build the image. 

Command: 

```
docker build -t test-app:1.0 .
```

## **Explanation** 

- `build` → Build image. 

- `-t` → Assign image tag. 

- `test-app` → Repository name. 

- `1.0` → Image version (tag). 

- `.` → Current directory acts as build context. 

## **Docker Tags** 

Tags identify specific versions of images. 

Examples: 

```
mysql:8.0
mysql:latest
ubuntu:22.04
```

```
ubuntu:latest
```

Tags allow developers to: 

- Track versions. 

- Roll back deployments. 

- Maintain consistency across environments. 

## **Step 5: Sharing Images Through Docker Hub** 

## **Docker Hub** 

Docker Hub is a centralized cloud repository where developers upload and download Docker images. 

It functions similarly to GitHub, but for Docker images. 

Developers can: 

- Push images. 

- Pull images. 

- Share images publicly. 

- Share images privately. 

- Version images. 

## **Logging Into Docker Hub** 

Authenticate from the terminal: 

```
docker login
```

Docker will prompt for: 

- Username. 

- Password or Access Token. 

## **Pushing an Image** 

Upload an image: 

```
docker push username/test-app
```

Team members can later download the image using: 

```
docker pull username/test-app
```

This ensures every team member runs exactly the same environment. 

## **Important Terms and Definitions** 

## **`dockerd` Docker Daemon ( )** 

The Docker Daemon is the core background service running on the host machine. 

It is responsible for: 

- Building Docker images. 

- Running containers. 

- Managing networks. 

- Managing volumes. 

- Pulling and pushing images. 

- Communicating with the Docker API. 

Without the Docker Daemon running, Docker commands cannot execute. 

## **Docker Hub** 

Docker Hub is a centralized, cloud-based repository where developers can upload (push) and download (pull) Docker images. 

It can be thought of as GitHub for Docker images. 

## Features: 

- Public repositories. 

- Private repositories. 

- Image versioning. 

- Team collaboration. 

- Automated image builds. 

## Examples: 

```
ubuntu
nginx
mysql
mongo
node
```

Images can be downloaded directly from Docker Hub: 

```
docker pull ubuntu
```

## **Docker Image** 

A Docker Image is a read-only blueprint used to create containers. 

It contains: 

- Application code. 

- Dependencies. 

- Libraries. 

- Runtime. 

- Environment configurations. 

Images are immutable and reusable. 

## **Docker Container** 

A Docker Container is a running instance of an image. 

Containers are: 

- Lightweight. 

- Portable. 

- Isolated. 

- Fast to start. 

Containers consume: 

- CPU. 

- RAM. 

- Storage. 

- Network resources. 

## **Tags** 

Tags are labels used to identify different versions or variants of the same image. 

Examples: 

```
mysql:8.0
mysql:latest
```

```
ubuntu:22.04
ubuntu:latest
node:18
```

Tags allow developers to: 

- Track versions. 

- Roll back deployments. 

- Maintain consistency. 

- Avoid unexpected updates. 

## **`-d` Detached Mode ( )** 

Detached Mode runs containers in the background. 

Example: 

```
docker run -d nginx
```

Advantages: 

- Frees the terminal. 

- Allows long-running services. 

- Ideal for databases, web servers, and automation tools. 

## **`-it` Interactive Mode ( )** 

Interactive mode opens a terminal session inside a container. 

Example: 

```
docker run -it ubuntu /bin/bash
```

Flags: 

- `-i` → Interactive mode. 

- `-t` → Allocate pseudo-terminal. 

## **`-e` Environment Variables ( )** 

Environment variables are runtime configuration values passed into containers. 

Examples: 

- `-e MYSQL_ROOT_PASSWORD=password` 

- `-e MONGO_INITDB_ROOT_USERNAME=admin` 

They are commonly used to configure: 

- Usernames. 

- Passwords. 

- Secret keys. 

- API tokens. 

- Database configurations. 

## **Dockerfile** 

A Dockerfile is a plain text file containing instructions used by Docker to assemble a custom image layer by layer. 

Example instructions: 

```
FROM
RUN
COPY
WORKDIR
EXPOSE
CMD
```

## **YAML (** **`.yml` )** 

YAML stands for: 

## **Yet Another Markup Language** 

It is a human-readable data serialization language used to define Docker Compose configurations. 

Example file: 

```
docker-compose.yml
```

## **Docker Compose** 

Docker Compose is a tool used to define and manage multi-container applications through a single YAML configuration file. 

Example: 

```
docker compose up -d
```

Compose simplifies deployment of complex environments consisting of multiple interconnected containers. 

## **Docker Volume** 

A Docker Volume is persistent storage managed by Docker. 

Data stored inside a volume survives container deletion. 

Example: 

```
docker volume create mydata
```

## **Docker Network** 

A Docker Network allows containers to communicate securely with each other. 

Example: 

```
docker network create my-network
```

Common network drivers: 

- Bridge. 

- Host. 

- None. 

## **Section 5: Master CLI Command Cheat Sheet** 

## **`-v` Path Convention Differences for Volume Mapping ( )** 

## **Windows** 

```
docker run -v C:\Users\Username\Folder:/app ubuntu
```

## **Linux / Kali** 

```
docker run -v /home/kali/folder:/app ubuntu
```

**Command** 

**Command Purpose** `docker pull <image>` Downloads an image from 

**Notes / Flags** 

Example: `docker pull` 

## **Command** 

## **Purpose** 

Docker Hub. Lists all downloaded images. Creates and starts a container. 

```
docker images
```

```
docker run <image>
```

> `docker run -it` Opens an interactive shell 

> `<image> /bin/bash` inside a container. `docker run -d <image>`[Runs a container in detached ] mode. 

Automatically removes the container after completion. 

```
docker run --rm
<image>
```

Displays running containers. 

```
docker ps
```

Displays all containers. Starts an existing stopped container. 

```
docker ps -a
```

```
docker start <id>
```

Gracefully stops a running container. Restarts a container. Removes an image. 

```
docker stop <id>
docker restart <id>
docker rmi <image>
```

`docker rmi -f <image>` Forcefully removes an image. 

## **Notes / Flags** 

```
ubuntu:latest
```

Newer syntax: `docker image ls` Automatically pulls image if not present locally. 

`-i` = Interactive, `-t` = Terminal Executes container in background. 

Useful for temporary tasks and testing. 

Shows ID, ports, status, names, and image information. Includes stopped containers. 

Container state is preserved. 

Avoid using `systemctl` for individual containers. 

Stops and starts the container. Containers using the image must be removed first. 

Ignores stopped-container dependencies. 

```md
| Command | Purpose | Notes / Flags |
|----------|---------|---------------|
| `docker rm <id>` | Permanently removes a stopped container. | Deletes the writable container layer. |
| `docker logs <id>` | Displays container logs. | Useful for troubleshooting. |
| `docker logs -f <id>` | Continuously streams container logs. | Recommended for monitoring background tasks. |
| `docker exec -it <id> /bin/bash` | Opens a shell inside a running container. | Useful for troubleshooting and debugging. |
| `docker network create <name>` | Creates a custom Docker network. | Enables secure container communication. |
| `docker volume create <name>` | Creates a named volume. | Used for persistent storage. |
| `docker-compose up -d` | Starts services defined in a Compose file. | Legacy syntax. |
| `docker compose up -d` | Starts services using modern Compose syntax. | Recommended syntax. |
| `docker-compose down` | Stops and removes Compose-managed containers. | Legacy syntax. |
| `docker compose down` | Stops and removes Compose-managed containers. | Recommended syntax. |
| `docker build -t <name>:<tag> .` | Builds an image from a Dockerfile. | `.` refers to the current directory as the build context. |
| `docker login` | Authenticates with Docker Hub. | Required before pushing images. |
| `docker push <repository>` | Uploads an image to Docker Hub. | Example: `docker push username/test-app` |
```

**Command** 

**Notes / Flags** 

**Purpose** 

Hub. 

## **Key Takeaways** 

## **Standardization** 

Docker solves the classic: 

"It works on my machine" 

problem by packaging applications and dependencies into standardized, portable containers. 

## **Resource Efficiency** 

Unlike Virtual Machines, Docker shares the host operating system kernel. 

This makes containers: 

- Lightweight. 

- Fast. 

- Resource efficient. 

- Highly scalable. 

## **Application-Level Virtualization** 

Docker virtualizes only the application layer instead of virtualizing complete hardware stacks. 

This significantly reduces overhead. 

## **Infrastructure as Code** 

Dockerfile and Docker Compose enable infrastructure to be defined entirely in code. 

Benefits include: 

- Repeatable deployments. 

- Version-controlled infrastructure. 

- Easy collaboration. 

- Automation. 

## **Ephemeral Nature** 

Containers are disposable. 

Data stored inside the container's writable layer is lost when the container is deleted. 

Persistent storage should always be implemented using Docker Volumes. 

## **Security Isolation** 

Docker provides strong application isolation. 

However, containers share the host kernel. 

For analyzing highly dangerous or completely unknown malware, hardware-isolated Virtual Machines remain safer because they provide hard isolation. 

## **Networking** 

Docker Networks allow containers to communicate securely without unnecessarily exposing internal services. 

## **Data Persistence** 

Critical application data such as: 

- Database records. 

- Log files. 

- Uploaded files. 

should always be stored using Volumes. 

## **Exam / Interview Questions** 

## **1. What is Docker?** 

## **Expected Answer:** 

Docker is an open-source platform that packages applications and their dependencies into lightweight, portable containers using OS-level virtualization. 

## **2. Why do we need Docker?** 

## **Expected Answer:** 

Docker eliminates environment inconsistencies and solves the "It works on my machine" problem by ensuring applications run identically across different systems. 

## **3. What is the difference between a Docker Image and a Docker Container?** 

## **Expected Answer:** 

An Image is a static, read-only blueprint, whereas a Container is a running instance of that image. 

## **4. What is the core difference between a Docker Container** 

## **and a Virtual Machine?** 

## **Expected Answer:** 

Virtual Machines virtualize hardware and require a complete guest operating system. 

Docker virtualizes the application layer and shares the host OS kernel, resulting in significantly lower overhead and much faster startup times. 

## **5. Explain the Copy-on-Write (CoW) mechanism.** 

**Expected Answer:** 

Docker images are composed of read-only layers. When a container modifies a file, Docker copies the file into a writable layer and performs changes there, leaving the original image unchanged. 

## **6. What is a Docker Volume?** 

## **Expected Answer:** 

A Docker Volume is persistent storage managed by Docker that survives container deletion. 

## **7. What happens if a database container crashes and no volume was configured?** 

## **Expected Answer:** 

All data stored inside the container's writable layer will be permanently lost once the container is removed. 

## **8. What is Port Binding?** 

## **Expected Answer:** 

Port binding maps a port on the host machine to a port inside the container, enabling external traffic to access containerized applications. 

Syntax: 

- `-p <host_port>:<container_port>` 

## **9. How do two isolated containers communicate with each other?** 

## **Expected Answer:** 

Containers communicate by being attached to the same Docker Network, typically a custom bridge network. 

## **10. Explain the difference between RUN and CMD in a Dockerfile.** 

## **Expected Answer:** 

`RUN` executes commands during image build time, while `CMD` specifies the default command executed when a container starts. 

## **11. How do you safely detach from a running container without stopping it?** 

## **Expected Answer:** 

Use: 

```
Ctrl + P
Ctrl + Q
```

## **Revision Summary** 

- **Docker = Image + Container** . Images are blueprints; containers are running instances. 

- Docker solves the **"It works on my machine"** problem. 

- Containers are lightweight because they share the host kernel. 

- Docker Images consist of multiple read-only layers. 

- Docker uses the **Copy-on-Write (CoW)** mechanism for efficient storage. 

- Containers are ephemeral; use Volumes for persistent storage. 

- Use Port Binding ( `-p` ) to expose applications. 

- Use Docker Networks to securely connect multiple containers. 

- Use Dockerfiles to create custom images. 

- Use Docker Compose to manage multi-container applications. 

- Use `docker logs` and `docker exec` for troubleshooting. 

- Use Docker Hub to share and distribute images. 

- For highly dangerous malware analysis, prefer Virtual Machines because they provide stronger isolation than containers. 

