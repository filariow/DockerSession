# Containers with Docker  <!-- omit in toc --> 

- [Agenda](#agenda)
- [Theory](#theory)
  - [Containers](#containers)
    - [Intro](#intro)
    - [Pros](#pros)
    - [Use Cases](#use-cases)
    - [Containers Vs Virtual Machine](#containers-vs-virtual-machine)
    - [Details](#details)
      - [Linux Container Project (LXC)](#linux-container-project-lxc)
      - [History](#history)
    - [References and useful links](#references-and-useful-links)
  - [Docker](#docker)
    - [How does Docker work?](#how-does-docker-work)
    - [Advantages of Docker containers](#advantages-of-docker-containers)
      - [Modularity](#modularity)
      - [Layers and image version control](#layers-and-image-version-control)
      - [Rollback](#rollback)
      - [Rapid deployment](#rapid-deployment)
      - [Limitations to using Docker](#limitations-to-using-docker)
    - [References and useful links](#references-and-useful-links-1)
  - [Docker Hub](#docker-hub)
    - [References and useful links](#references-and-useful-links-2)
- [Fundamentals](#fundamentals)
  - [Images](#images)
    - [FROM](#from)
    - [ARG](#arg)
    - [WORKDIR](#workdir)
    - [COPY](#copy)
    - [ADD](#add)
      - [Differences with COPY](#differences-with-copy)
    - [ENV](#env)
    - [EXPOSE](#expose)
    - [RUN](#run)
    - [CMD](#cmd)
    - [ENTRYPOINT](#entrypoint)
    - [Understand how CMD and ENTRYPOINT interact](#understand-how-cmd-and-entrypoint-interact)
    - [\# comment](#-comment)
    - [Useful Links and references](#useful-links-and-references)
  - [Containers](#containers-1)
    - [cli](#cli)
    - [Useful Links and references](#useful-links-and-references-1)
  - [Storage](#storage)
    - [Volumes](#volumes)
    - [Bind mounts](#bind-mounts)
    - [tmpfs](#tmpfs)
    - [cli](#cli-1)
    - [Useful links and references](#useful-links-and-references)
  - [Network](#network)
    - [Network drivers](#network-drivers)
    - [Bridge](#bridge)
    - [Host](#host)
    - [Overlay](#overlay)
    - [Macvlan](#macvlan)
    - [None](#none)
    - [Network driver summary](#network-driver-summary)
- [Hands-On](#hands-on)
  - [Docker Hello-world](#docker-hello-world)
  - [Your first helloworld](#your-first-helloworld)
  - [Hello-world ASP.NET](#hello-world-aspnet)
  - [Mongo DB](#mongo-db)
    - [Starting and restarting container + volumes](#starting-and-restarting-container--volumes)
    - [Initializing Mongo DB with data](#initializing-mongo-db-with-data)
  - [Develop in container](#develop-in-container)
    - [Generate and run an ASP.NET Core MVC App](#generate-and-run-an-aspnet-core-mvc-app)
    - [Using Mongo from ASP.NET Web App](#using-mongo-from-aspnet-web-app)
  - [Docker Hub](#docker-hub-1)
    - [Push a docker image to Docker Hub](#push-a-docker-image-to-docker-hub)
    - [Set up auto-build (CI) from Github to Docker Hub](#set-up-auto-build-ci-from-github-to-docker-hub)
  - [Docker Compose](#docker-compose)
    - [Intro](#intro-1)
    - [Set up our Mongo-Express and Mongo with volume environment](#set-up-our-mongo-express-and-mongo-with-volume-environment)
    - [Useful links](#useful-links)

# Agenda
1. Intro `[5 min]`

2. Install Docker `[2 min]`
    - Docker Engine (Linux)
    - Docker Desktop (Windows and Mac OSX: 10-20 min + restart. Do it before attending the event, please)

3. Containers `[10 min]`

4. What is Docker `[10 min]`

5. Docker Hub `[5 min]`

6. Fundamentals `[20 min]`
    - Images
        - Dockerfile (simple and multistage)
        - cli: pull, rmi, push, inspect
    - Containers
        - instances created on an image specification
        - data deleted on container deletion
        - default naming convention 
        - cli: run, exec, stop, restart, rm, ps, logs, inspect
          - container: commit, cp, diff, rename, top
    - Volumes
        - Mounting local directories with container
        - cli: volume: create,inspect, ls, prune, rm
    - Network
      - cli: network: connect, create, disconnect, inspect, ls, prune, rm

7. Hands-On `[50 min]`
    - Run Hello World `[5 min]`
        - Inspecting Dockerfile (Simple Dockerfile)
    - Run ASP.NET Core 3 hello-world `[25 min]`
        - Arguments: 
          - cli: pull, run, stop, rm, ps
          - Dockerfile: Multistage Dockerfile
          - Docker Hub: Microsoft .NET Core repo
        - Practice: start, stop, remove, and auto-remove containers
    - Run Mongo DB `[20 min]`
        - Run Mongo DB (mongo)
        - Run Mongo Express and play with the database
        - Restart the Mongo DB container and lose all the data
        - Create a volume and inspect it
        - Run Mongo DB and store db data on created volume
        - Import data in mongo
          - copy files in mongo container (docker container cp)
          - open a bash shell in mongo container and run commands
            - import data-set

8.  Develop in containers (vscode) `[10 min]`
    -  Intro
      -  Why is it so important?
         -  It works on my machine
         -  Multiple versions of the same tool at the same time on the same machine without any compatibility problem
    -  Practice
      -  VS Code 2019 + Remote-Containers
      -  New ASP.NET app

9.  CI with GitHub and DockerHub: `[20 min]`
    - Sign Up in Docker Hub and GitHub
    - Push our first image to Docker Hub
    - Link to Docker Hub repo to GitHub Repo
      - Image auto-build on GitHub Trigger

10. Docker Compose `[5 min]`

11. If we have more time :clock830:
       1.  CI/CD with Azure Pipelines: `[30 min]`
            -  Azure Portal
               -  Create a Resource Group (RG)
               -  Create an Azure Container Registry (ACR)
            -  Azure DevOps
               -  Create a Project
               -  Create a Build Pipeline
               -  Trigger a Build Pipeline
            -  Publish from Container Registry to a web site
               -  Azure Portal: Create a new `Web App for Containers`
               -  Azure DevOps: Create a Release Pipeline

12. To be continued:
    1.  Container Orchestrators:
        - Docker Swarm
        - Kubernetes
    
    2. Service Meshes
        - Istio
        - Linkerd


## Useful links  <!-- omit in toc --> 

- [Docker in Action](https://www.manning.com/books/docker-in-action)
- [https://docs.docker.com](https://docs.docker.com)
- [https://docs.docker.com/engine/](https://docs.docker.com/engine/)
- [https://labs.play-with-docker.com](https://labs.play-with-docker.com)

# Theory

## Containers

### Intro 

A Linux® container is a set of one or more processes that are isolated from the rest of the system. 
All the files necessary to run them are provided from a distinct image, meaning that Linux containers are portable and consistent as they move from development, to testing, and finally to production. 

When we talk about containers we are not talking about virtualization.
Indeed, *Virtualization* lets your operating systems run simultaneously on a single hardware system, meanwhile *containers* share the same operating system kernel and isolate the application processes from the rest of the system. 
For example: ARM Linux systems run ARM Linux containers, x86 Linux systems run x86 Linux containers, x86 Windows systems run x86 Windows containers.
Linux containers are extremely portable, but they must be compatible with the underlying system.

![image](https://www.redhat.com/cms/managed-files/virtualization-vs-containers.png)

### Pros

- **Lightweight**: Containers are very small with respect to  other virtualization technologies, like virtual machines, because they do not include OS images.
- **Increased portability**: Containerized applications can be easily deployed to multiple different Operating Systems and hardware platforms, avoiding in this way the common "it works on my machine!" scenario.
- **More consistent operation**: DevOps teams know applications in containers will run the same, regardless of where they are deployed.
- **Greater efficiency**: Containers allow applications to be more rapidly deployed, patched, or scaled.
- **Better application development**: Containers support agile and DevOps efforts to accelerate development, test, and production cycles.


### Use Cases

- **Development environment**: Containers permits to create for project development environment that can be shared among developers.
- **Microservices architectures**: Distributed applications and microservices can be more easily isolated, deployed, and scaled using individual container building blocks.
- **Programs Incompatibility**: Easily use incompatible programs -or version of one program- on the same machine isolating the programs in containers.
- **High scalability requirement scenarios**: Scalability is one of the greatest benefits of containers.
    With proper orchestration, containers are a great solution that allows you to rapidly scale to meet your customers’ needs while avoiding excess infrastructure in quiet times.
- **Portability requirement scenarios**: Containerized applications can be deployed on whichever cloud vendors you need to deploy to.
- **Simple installation of complex program**: Complex program can be containerized avoiding the Operators to fight against strange installation/configuration problems that may appear in non-isolated scenarios.

### Containers Vs Virtual Machine

Containers and virtual machines have similar resource isolation and allocation benefits, but function differently because containers virtualize the operating system instead of hardware.
Containers are more portable and efficient.

### Details

#### Linux Container Project (LXC)

The [Linux Containers project (LXC)](https://linuxcontainers.org/) is an open source container platform that provides a set of tools, templates, libraries, and language bindings.
LXC has a simple command line interface that improves the user experience when starting containers.

LXC offers an operating-system level virtualization environment that is available to be installed on many Linux-based systems.
Your Linux distribution may have it available through its package repository.

#### History

The idea of what we now call container technology first appeared in 2000 as [FreeBSD jails](https://www.freebsd.org/doc/handbook/jails.html), a technology that allows the partitioning of a [FreeBSD](https://www.freebsd.org/) system into multiple subsystems, or jails.
Jails were developed as safe environments that a system administrator could share with multiple users inside or outside of an organization.

In 2001, an implementation of an isolated environment made its way into Linux, by way of Jacques Gélinas’ [VServer project](http://linux-vserver.org/Welcome_to_Linux-VServer.org).
Once this foundation was set for multiple controlled userspaces in Linux, pieces began to fall into place to form what is today’s Linux container.

Very quickly, more technologies combined to make this isolated approach a reality.
Control groups ([cgroups](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Resource_Management_Guide/ch01.html)) is a kernel feature that controls and limits resource usage for a process or groups of processes.
And [systemd](https://www.freedesktop.org/wiki/Software/systemd/), an initialization system that sets up the userspace and manages their processes, is used by cgroups to provide greater control over these isolated processes.
Both of these technologies, while adding overall control for Linux, were the framework for how environments could be successful in staying separated.

In 2008, Docker came onto the scene ([by way of dotCloud](https://blog.docker.com/2013/10/dotcloud-is-becoming-docker-inc/)) with their eponymous container technology. 
The docker technology added a lot of new concepts and tools—a simple command line interface for running and building new layered images, a server daemon, a library of pre-built container images, and the concept of a registry server.
Combined, these technologies allowed users to quickly build new layered containers and easily share them with others.

### References and useful links

- [https://www.cncf.io/](https://www.cncf.io/)
- [https://www.netapp.com/us/info/what-are-containers.aspx](https://www.netapp.com/us/info/what-are-containers.aspx)
- [https://www.opencontainers.org/](https://www.opencontainers.org/)
- [https://linuxcontainers.org/](https://linuxcontainers.org/)
- [https://www.freebsd.org/doc/handbook/jails.html](https://www.freebsd.org/doc/handbook/jails.html)


## Docker

The word "DOCKER" refers to several things.

1. The IT software "Docker” is containerization technology that enables the creation and use of Linux® containers.
2. The [open source Docker community](https://forums.docker.com/) works to improve these technologies to benefit all users—freely.
3. The company, [Docker Inc.](https://www.docker.com/), builds on the work of the Docker community, makes it more secure, and shares those advancements back to the greater community. It then supports the improved and hardened technologies for enterprise customers.

### How does Docker work?

The Docker technology uses the Linux kernel and features of the kernel, like [cgroups](https://access.redhat.com/documentation/en-US/Red_Hat_Enterprise_Linux/6/html/Resource_Management_Guide/ch01.html) and namespaces, to segregate processes so they can run independently.

Docker technology was initially built on top of the LXC technology—what most people associate with "traditional” Linux containers—though it’s since moved away from that dependency.
Now Docker is based on their [containerd](https://github.com/containerd/containerd) project.

containerd is an industry-standard container runtime with an emphasis on simplicity, robustness and portability. It is available as a daemon for Linux and Windows, which can manage the complete container lifecycle of its host system: image transfer and storage, container execution and supervision, low-level storage and network attachments, etc.

The Docker technology brings more than the ability to run containers—it also eases the process of creating and building containers, shipping images, and versioning of images (among other things).

![image](https://www.redhat.com/cms/managed-files/traditional-linux-containers-vs-docker_0.png)

Traditional Linux containers use an init system that can manage multiple processes.
This means entire applications can run as one.
The Docker technology encourages applications to be broken down into their separate processes and provides the tools to do that.

### Advantages of Docker containers

#### Modularity

The Docker approach to containerization is focused on the ability to take down a part of an application, to update or repair, without unnecessarily taking down the whole app. 
In addition to this microservices-based approach, you can share processes amongst multiple apps in much the same way that service-oriented architecture (SOA) works.

#### Layers and image version control

Each Docker image file is made up of a series of layers.
These layers are combined into a single image.
A layer is created when the image changes.
Every time a user specifies a command, such as run or copy, a new layer gets created.

Docker reuses these layers for new container builds, which makes the build process much faster.
Intermediate changes are shared between images, further improving speed, size, and efficiency.
Inherent to layering is version control.
Every time there’s a new change, you essentially have a built-in changelog—full control over your container images.

#### Rollback

Perhaps the best part about layering is the ability to roll back.
Every image has layers.
Don’t like the current iteration of an image?
Roll it back to the previous version.
This supports an agile development approach and helps make continuous integration and deployment (CI/CD) a reality from a tools perspective.

#### Rapid deployment

Docker-based containers can reduce deployment to seconds.
By creating a container for each process, you can quickly share those similar processes with new apps.
And, since an OS doesn’t need to boot to add or move a container, deployment times are substantially shorter.
On top of this, with the speed of deployment, you can easily and cost-effectively create and destroy data created by your containers without concern.

So, Docker technology is a more granular, controllable, microservices-based approach that places greater value on efficiency.

#### Limitations to using Docker

Docker, by itself, is very good at managing single containers. 
When you start using more and more containers and containerized apps, broken down into hundreds of pieces, management and orchestration can get very difficult.
Eventually, you need to take a step back and group containers to deliver services—networking, security, telemetry, etc.— across all of your containers.
That's where **Kubernetes** comes in.

### References and useful links

- [https://www.redhat.com/en/topics/containers/what-is-docker](https://www.redhat.com/en/topics/containers/what-is-docker)
- [https://www.docker.com/](https://www.docker.com/)
- [https://containerd.io/](https://containerd.io/)


## Docker Hub

[Docker Hub](https://hub.docker.com/) is a service provided by Docker for finding and sharing container images with your team.
It provides the following major features:

- Repositories: Push and pull container images.
- Teams & Organizations: Manage access to private repositories of container images.
- Official Images: Pull and use high-quality container images provided by Docker.
- Publisher Images: Pull and use high- quality container images provided by external vendors. Certified images also include support and guarantee compatibility with Docker Enterprise.
- Builds: Automatically build container images from GitHub and Bitbucket and push them to Docker Hub.
- Webhooks: Trigger actions after a successful push to a repository to integrate Docker Hub with other services.

### References and useful links

- [https://hub.docker.com/](https://hub.docker.com/)
- [https://docs.docker.com/docker-hub/](https://docs.docker.com/docker-hub/)
- [https://www.docker.com/products/docker-hub](https://www.docker.com/products/docker-hub)


# Fundamentals

## Images

Docker images are build starting from a `Dockerfile`.
Dockerfiles are simple text files with a list of subsequent commands.

Let's introduce some important keywords of Dockerfiles


### FROM 

```docker
FROM <image>[:<tag>] [AS <name>]
```


### ARG

```docker
ARG <name>[=<default value>]
```

The ARG instruction defines a variable that users can pass at build-time to the builder with the docker build command using the `--build-arg <varname>=<value>` flag.

> :warning: Warning:
> It is not recommended to use build-time variables for passing secrets like github keys, user credentials etc. Build-time variable values are visible to any user of the image with the docker history command.

ARG is the only instruction that may precede FROM in the Dockerfile.


### WORKDIR

```docker
WORKDIR /path/to/workdir
```

The WORKDIR instruction sets the working directory for any RUN, CMD, ENTRYPOINT, COPY and ADD instructions that follow it in the Dockerfile. 
If the WORKDIR doesn’t exist, it will be created even if it’s not used in any subsequent Dockerfile instruction.


### COPY

```docker
COPY [--chown=<user>:<group>] <src>... <dest>
```

The COPY instruction copies new files or directories from `<src>` and adds them to the filesystem of the container at the path `<dest>`.


### ADD

```docker
ADD [--chown=<user>:<group>] <src>... <dest>
```

The ADD instruction copies new files, directories or remote file URLs from `<src>` and adds them to the filesystem of the image at the path `<dest>`.

#### Differences with COPY

COPY takes in a src and destination.
It only lets you copy in a local file or directory from your host (the machine building the Docker image) into the Docker image itself.

ADD lets you do that too, but it also supports 2 other sources.
First, you can use a URL instead of a local file / directory. 
Secondly, you can extract a tar file from the source directly into the destination.

If you’re copying in local files to your Docker image, always use COPY because it’s more explicit.


### ENV

```docker
ENV <key> <value>
ENV <key>=<value> ...
```

The ENV instruction sets the environment variable `<key>` to the value `<value>`.
This value will be in the environment for all subsequent instructions in the build stage and can be replaced inline in many as well.

The environment variables set using ENV will persist when a container is run from the resulting image. 
You can view the values using docker inspect, and change them using docker run `--env <key>=<value>`.


### EXPOSE

```docker
EXPOSE <port> [<port>/<protocol>...]
```

The EXPOSE instruction informs Docker that the container listens on the specified network ports at runtime.
You can specify whether the port listens on TCP or UDP, and the default is TCP if the protocol is not specified.

The EXPOSE instruction does not actually publish the port. 
It functions as a type of documentation between the person who builds the image and the person who runs the container, about which ports are intended to be published.

To actually publish the port when running the container, use the -p flag on docker run to publish and map one or more ports, or the -P flag to publish all exposed ports and map them to high-order ports.

### RUN

```docker
RUN <command> # (shell form, the command is run in a shell, which by default is /bin/sh -c on Linux or cmd /S /C on Windows)

RUN ["executable", "param1", "param2"]  # (exec form)
```

The RUN instruction will execute any commands in a new layer on top of the current image and commit the results.
The resulting committed image will be used for the next step in the Dockerfile.

Layering RUN instructions and generating commits conforms to the core concepts of Docker where commits are cheap and containers can be created from any point in an image’s history, much like source control.

The exec form makes it possible to avoid shell string munging, and to RUN commands using a base image that does not contain the specified shell executable.

The default shell for the shell form can be changed using the SHELL command.

In the *shell* form you can use a `\` (backslash) to continue a single RUN instruction onto the next line. 

### CMD

```docker
CMD ["executable","param1","param2"] # (exec form, this is the preferred form)
CMD ["param1","param2"] # (as default parameters to ENTRYPOINT)
CMD command param1 param2 # (shell form)
```

There can only be one `CMD` instruction in a Dockerfile.
If you list more than one `CMD` then only the last `CMD` will take effect.

The main purpose of a `CMD` is to provide defaults for an executing container.


### ENTRYPOINT

```docker
ENTRYPOINT ["executable", "param1", "param2"] # (exec form, preferred)
ENTRYPOINT command param1 param2 (shell form)
```

An `ENTRYPOINT` allows you to configure a container that will run as an executable.

Command line arguments to docker run `<image>` will be appended after all elements in an exec form `ENTRYPOINT`, and will override all elements specified using `CMD`.
This allows arguments to be passed to the entry point, i.e., docker run `<image>` -d will pass the -d argument to the entry point.
You can override the `ENTRYPOINT` instruction using the docker run `--entrypoint` flag.

Only the last ENTRYPOINT instruction in the Dockerfile will have an effect.


### Understand how CMD and ENTRYPOINT interact

Both CMD and ENTRYPOINT instructions define what command gets executed when running a container.
There are few rules that describe their co-operation.

1. Dockerfile should specify at least one of CMD or ENTRYPOINT commands.

2. ENTRYPOINT should be defined when using the container as an executable.

3. CMD should be used as a way of defining default arguments for an ENTRYPOINT command or for executing an ad-hoc command in a container.

4. CMD will be overridden when running the container with alternative arguments.


### \# comment

```docker
# Use a # to add comments!
```


### Useful Links and references

- [https://docs.docker.com/engine/reference/builder/](https://docs.docker.com/engine/reference/builder/)

## Containers

A container is a standard unit of software that packages up code and all its dependencies so the application runs quickly and reliably from one computing environment to another. 
A Docker container image is a lightweight, standalone, executable package of software that includes everything needed to run an application: code, runtime, system tools, system libraries and settings.

Container images become containers at runtime and in the case of Docker containers - images become containers when they run on Docker Engine.
Available for both Linux and Windows-based applications, containerized software will always run the same, regardless of the infrastructure.
Containers isolate software from its environment and ensure that it works uniformly despite differences for instance between development and staging.

When you create a container an human-readable id is assigned or by you or by docker.
In this second case, it will have the `ADJECTIVE_NOUN` form, like `happy_tharp`, `priceless_johnson`, `youthful_agnesi`.


### cli

```
docker run [-it | -d] [--name <human-readable_name>] [(-p port)+] IMAGE [COMMAND] [ARG...]
docker exec [-it] CONTAINER COMMAND [ARG...]
docker restart CONTAINER [CONTAINER...]
docker stop CONTAINER [CONTAINER...]
docker rm CONTAINER [CONTAINER...]
docker ps
docker logs CONTAINER
docker inspect NAME|ID [NAME|ID...]

docker cp CONTAINER:SRC_PATH DEST_PATH|-
docker cp SRC_PATH|- CONTAINER:DEST_PATH

docker commit CONTAINER [REPOSITORY[:TAG]]
docker diff CONTAINER 
docker rename CONTAINER NEW_NAME
docker top CONTAINER
```

### Useful Links and references

- [https://www.docker.com/resources/what-container](https://www.docker.com/resources/what-container)
- [https://docs.docker.com/engine/reference/commandline/](https://docs.docker.com/engine/reference/commandline/)


## Storage
Whenever a container is restarted the data stored in it will be lost.
To store persist date, it is necessary to write out of the container.

Docker has two options for containers to store files in the host machine, so that the files are persisted even after the container stops: `volumes`, and `bind mounts`.
If you’re running Docker on Linux you can also use a `tmpfs` mount.
If you’re running Docker on Windows you can also use a named pipe.

![image](https://docs.docker.com/storage/images/types-of-mounts.png)


### Volumes

Volumes are stored in a part of the host filesystem which is *managed* by Docker (`/var/lib/docker/volumes/` on Linux).
Non-Docker processes should not modify this part of the filesystem.
Volumes are the best way to persist data in Docker.


### Bind mounts 

`Bind mounts` may be stored anywhere on the host system.
They may even be important system files or directories.
Non-Docker processes on the Docker host or a Docker container can modify them at any time.


### tmpfs 
`tmpfs` mounts are stored in the host system’s memory only, and are never written to the host system’s filesystem.


### cli
```bash
docker volume create <volume-name>      # To create a new volume
docker volume ls                        # To list all volumes
docker volume inspect <volume-name>     # To inspect a volume
docker volume rm <volume-name>          # To delete a volume
docker volume prune                     # To remove all unused volumes and free up space
```

### Useful links and references

- [https://docs.docker.com/storage/](https://docs.docker.com/storage/)


## Network

One of the reasons Docker containers and services are so powerful is that you can connect them together, or connect them to non-Docker workloads.
Docker containers and services do not even need to be aware that they are deployed on Docker, or whether their peers are also Docker workloads or not.

### Network drivers
Docker’s networking subsystem is pluggable, using drivers.
Several drivers exist by default, and provide core networking functionality.

### Bridge
It is the default network driver. 
Bridge networks are usually used when your applications run in standalone containers that need to communicate.

In terms of Docker, a bridge network uses a software bridge which allows containers connected to the same bridge network to communicate, while providing isolation from containers which are not connected to that bridge network.
The Docker bridge driver automatically installs rules in the host machine so that containers on different bridge networks cannot communicate directly with each other.


### Host
For standalone containers, remove network isolation between the container and the Docker host, and use the host’s networking directly.

### Overlay
Overlay networks connect multiple Docker daemons together and enable swarm services to communicate with each other.

### Macvlan
Macvlan networks allow you to assign a MAC address to a container, making it appear as a physical device on your network.

### None
 For this container, disable all networking. Usually used in conjunction with a custom network driver.

### Network driver summary

- User-defined `bridge networks` are best when you need multiple containers to communicate on the same Docker host.
    
- `Host networks` are best when the network stack should not be isolated from the Docker host, but you want other aspects of the container to be isolated.

- `Overlay networks` are best when you need containers running on different Docker hosts to communicate, or when multiple applications work together using swarm services.

- `Macvlan networks` are best when you are migrating from a VM setup or need your containers to look like physical hosts on your network, each with a unique MAC address.

- `Third-party network` plugins allow you to integrate Docker with specialized network stacks.


- cli: network: connect, create, disconnect, inspect, ls, prune, rm

# Hands-On

## Docker Hello-world

1. Run your first container 

    ```bash
    docker run helloworld
    ```

## Your first helloworld

1. Open VS Code and open a new folder `mfhwc` 

2. Create a `main.sh` file with the following content

    ```bash
    #!/bin/bash

    echo "Hello World!"
    ```

3. Create a Dockerfile with the following requirements:
   - uses the `ubuntu:latest` image
   - works in the directory `/helloworld`
   - uses the `bash` command to run the `main.sh` file

    <br>
    <details>
    <summary>SOLUTION: Click to expand the solution</summary>   
    <p>
    
    ```docker
    FROM ubuntu:latest
    WORKDIR /helloworld
    COPY main.sh .
    ENTRYPOINT ["bash", "./main.sh"]
    ```
    
    </p>
    </details>  

4. Build the image and run it with respect to the following requirements
   - the image must have the name `mfhwc`
   - the image must have the tag `1.0` (it's stable!)
   - the image must have the tag `latest`

    <br>
    <details>
    <summary>SOLUTION: Click to expand the solution</summary>   
    <p>
    
    ```bash
    docker build -t mfhwc:1.0 -t mfhwc:latest .
    docker run mfhwc # or mfhwc:latest or mfhwc:1.0
    ```
    
    </p>
    </details>  


## Hello-world ASP.NET

1. Let look at [Microsoft .NET Core Samples on DockerHub](https://hub.docker.com/_/microsoft-dotnet-core-samples/)

2. Inspect the [ASP.NET Multistage Dockerfile](https://github.com/dotnet/dotnet-docker/blob/master/samples/aspnetapp/Dockerfile)

3. Pull the image and run a new container
    ```bash
    docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp

    docker run \
        -it \
        --publish 8000:80 \
        --name aspnetcore_sample \
        mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ```

4. Let look at the running containers

    ```bash
    docker ps
    ```

5. Open a browser and go to [localhost:8000](localhost:8000).

6. In the console hit `Ctrl+C` to stop the container, or use in another terminal the following command

    ```bash
    docker stop aspnetcore_sample
    ```

7. The container has been stopped but it is not removed.
   We can still find it using the following command
   ```bash
   docker ps --all | grep aspnetcore_sample
   ```

   If we try again to run another container with the same name it will give us an error (Try it!).
   
8. Remove the container with the following command.

   ```bash
   docker rm aspnetcore_sample
   ```

9. It you want a container to be removed when stopped add the parameter `--rm` at the run command.

10. PRACTICE: Relaunch the container with the `--rm` parameter, stop it and look for it in the list of running containers

    <details>
    <summary>SOLUTION: Click to expand the solution</summary>   
    <p>
    
    ```bash
    docker run \
        --rm \
        -it \
        --publish 8000:80 \
        --name aspnetcore_sample \
        mcr.microsoft.com/dotnet/core/samples:aspnetapp
    ```
    
    </p>
    
      1. Stop the running container using `Ctrl+C` or `docker stop aspnetcore_sample` 
      2. Inspect the list of running containers filtering for the container name and wait for no results
    
    
    <p>
    
    ```bash
    docker ps --all | grep aspnetcore_sample
    ```
    
    </p>
    </details>  

## Mongo DB

### Starting and restarting container + volumes

- Create a network
    ```bash
    docker network create mdb-net
    ```
- Mongo DB
    ```bash
    docker pull mongo:latest # [Not Mandatory] - https://hub.docker.com/_/mongo
    docker run \
        --detach \
        --rm \
        --network mdb-net \
        --name mdb \
        --publish 27017:27017 \
        --hostname mdb \
        mongo:latest

    docker ps
    ```
- Mongo DB Express
    ```bash
    docker pull mongo-express:latest # [Not Mandatory] - https://hub.docker.com/_/mongo-express
    docker run \
        --detach \
        --rm \
        --network mdb-net \
        --name mdb-exp \
        --env ME_CONFIG_MONGODB_SERVER=mdb \
        --publish 8081:8081 \
        mongo-express

    docker ps
    ```

    Create a new database `new-db`.
    Where is our database data stored?
    Inside the container.

- Stop the `mongo` container
    ```bash
    docker stop mdb
    docker rm mdb
    docker ps
    ```
    
    Where are our db data now? Lost!

- Create a volume
    ```bash
    docker volume create mdb-vol
    # let's inspect the volume, like the path where data are stored in local machine
    docker inspect mdb-vol  
    ```

- Mongo DB + volume `mdb-vol`
    ```bash
    # Run Mongo linked to volume "mdb-vol"
    docker run \
        --detach \
        --rm \
        --network mdb-net \
        --name mdb \
        --publish 27017:27017 \
        --hostname mdb \
        --volume mdb-vol:/data/db \
        mongo:latest 

    docker ps
    docker inspect mdb

    # restart mongo express
    docker restart mdb-exp
    ```
    
    Create a new database `new-db`.
    Where is our database data stored?
    Inside the `mdb-vol` volume.

    ```bash
    # Delete mongo's container
    docker stop mdb
    docker rm mdb

    # Run Mongo again linked to volume "mdb-vol"
    docker run \
        --detach \
        --rm \
        --network mdb-net \
        --name mdb \
        --publish 27017:27017 \
        --hostname mdb \
        --volume mdb-vol:/data/db \
        mongo:latest 

    # restart mongo express
    docker restart mdb-exp
    ```

    Visit [http://localhost:8081](http://localhost:8081) (mongo-express) dashboard and you will find the database "mdb-vol".
    

### Initializing Mongo DB with data

Copy files into Mongo's container

```bash
docker container cp ./mongo/data/products.json mdb:/mongo/data
```

Open a shell in Mongo's container

```bash
docker exec -it mdb bash
```

Import data
```bash
mongoimport --drop -c products --uri mongodb://localhost/samples products.json
```

Display data through `mongo-express` opening the database `samples` and the collection `products`


## Develop in container

### Generate and run an ASP.NET Core MVC App

1. Create a new ASP.NET Core app using the dotnet tools from `mcr.microsoft.com/dotnet/core/sdk`

    ```bash
    mkdir dotnet

    docker run \
        --rm \
        -it \
        --name dotnet-sdk \
        --volume $(realpath .)/dotnet:/dotnet \
        mcr.microsoft.com/dotnet/core/sdk:latest \
        bash

    # in another terminal we can inspect the mounted volumes
    docker inspect -f '{{ .Mounts }}' dotnet-sdk

    cd dotnet
    dotnet new mvc -o BooksApp
    ```

1. Open Visual Studio Code and open the `dotnet` folder (Ctrl+K, Ctrl+O)

1. On Linux open a shell and fix files permissions:

    ```bash
    sudo chown $(id -un):$(id -un) -R ./dotnet
    ```

1. Press `F1` (or `Ctrl+Shift+P`), select `> Remote-Containers: Add Development Container Configuration Files...`, and finally select `C# (.NET Core Latest)`.

1. Let inspect and customize the generated `devcontainer.json` and `Dockerfile` files
   1. On Linux, uncomment `L22`: 
        ```json
        "runArgs": [ "-u", "vscode" ],
        ```
        > In a fresh terminal run `id` and if your `UID` or `GUID` 
        > is different from `1000` update the Dockerfile `L13` and `L14`

        > :warning: REBUILD CONTAINER :warning:

2. Press `F1` (or `Ctrl+Shift+P`), select `> Remote-Containers: Reopen in Container`, and wait for the container to be prepared

3. Press `F1` (or `Ctrl+Shift+P`), select `> .NET: Generate Assets for Build and Debug`. 
   It will create a new directory `.vscode` with some configuration files.
   Inspect it if you want.

4. Press `F5` to run the application: as usual a browser will be opened and the web app home page will be shown. 
   In case it is request accept the risks from visiting a web page with a self-signed certificate.

5. Quit the debugging session


### Using Mongo from ASP.NET Web App

1. Use git to clone the following repo and give a look to the history
    ```bash
    git clone https://github.com/FrancescoIlario/BooksApp.git
    git log --oneline --graph --decorate --all
    ```

1. Open in VS Code, build container, and run the WebApp `F5`

## Docker Hub

### Push a docker image to Docker Hub

1. Register on [Docker Hub](https://hub.docker.com/)

2. Open the [Docker Hub Repositories page](https://hub.docker.com/repositories) and create a new repository

3. Set `go-hello-world` as name

4. If you still have not a [GitHub account](https://github.com/join?source=header), it's time to create one!

5. Fork the project [https://github.com/FrancescoIlario/go-hello-world-web-app.git](https://github.com/FrancescoIlario/go-hello-world-web-app.git) and clone

    ```bash
    git clone https://github.com/[YOUR_ACCOUNT_NAME]/go-hello-world-web-app.git
    ```

6. Change directory to the cloned project one
    ```bash
    cd go-hello-world-web-app
    ```

7.  Authenticate with docker hub and push the image to your repository
    ```bash
    docker login
    docker push [YOUR_DOCKERHUB_ACCOUNT]/go-hello-world:latest
    ```

8. Now you can download it from Docker Hub and run a container
    ```bash
    docker pull [YOUR_DOCKERHUB_ACCOUNT]/go-hello-world:latest
    docker run \
        --rm \
        -d \
        -p 8080:8080 \
        --name go-hello-world \
        [YOUR_DOCKERHUB_ACCOUNT]/go-hello-world:latest
    ```
9. Navigate to [localhost:8080](localhost:8080) to test the web app and finally stop the container via the following command. 
   Thanks to "--rm" argument the container will be automagically deleted.

    ```bash
    docker stop go-hello-world
    ```

### Set up auto-build (CI) from Github to Docker Hub

1. Open the [Docker Hub Repositories page](https://hub.docker.com/repositories) and select the `go-hello-world` repository

2. Select the `Builds` tab, then hit `Link to GitHub` and authorize `Docker Hub` to access your GitHub's repos.

3. Select this Organization and the `go-hello-world` repository, the `master` branch as `Source`, `latest` as `Docker Tag` and finally hit `Save`

4. Open with code the cloned repo, do some changes to the `static/index.html` file, commit it and push.
   A build will be scheduled, and your image will be updated!

5. You can now pull the new image and run it!

    ```bash
    docker pull [YOUR_DOCKERHUB_ACCOUNT]/go-hello-world:latest
    docker run \
        --rm \
        -d \
        -p 8080:8080 \
        --name go-hello-world \
        [YOUR_DOCKERHUB_ACCOUNT]/go-hello-world:latest
    ```

    > If caching prevent you from downloading the new one, try deleting the cached image with
    >
    > `docker rmi [YOUR_DOCKERHUB_ACCOUNT]/go-hello-world:latest`


## Docker Compose

### Intro

Compose is a tool for defining and running multi-container Docker applications. 
With Compose, you use a YAML file to configure your application’s services.
Then, with a single command, you create and start all the services from your configuration.
To learn more about all the features of Compose, see the list of features.

Using Compose is basically a three-step process:

1. Define your app’s environment with a Dockerfile so it can be reproduced anywhere.

2. Define the services that make up your app in docker-compose.yml so they can be run together in an isolated environment.

3. Run docker-compose up and Compose starts and runs your entire app.


### Set up our Mongo-Express and Mongo with volume environment

1. Let inspect the file `mongo/compose/docker-compose.yaml`
    - it declares two services 
        - `mdb`
        - `mdb-exp`
    - a volume `mdb-vol`
    - a network `mdb-net`
  
2. Using Docker Compose we can declare multi containers setups in a single turn.

3. Open a shell and change working directory to `mongo/compose` the run docker compose

    ```bash
    docker-compose up # use the "-d / --detached" parameter to launch in detached mode
    ```

### Useful links

- [https://docs.docker.com/compose/](https://docs.docker.com/compose/)
- [https://docs.docker.com/compose/install/](https://docs.docker.com/compose/install/)