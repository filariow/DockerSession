# Docker

## Agenda
0. Intro `[5 min]`

1. Install Docker `[2 min]`
    - Docker Engine (Linux)
    - Docker Desktop (Windows and Mac OSX: 10-20 min + restart. Do it before attending the event, please)

2. Containers `[5 min]`

3. What is Docker and reasons why it is so important `[5 min]`
    - It works on my machine (then we'll ship your machine)
    - Virtual Machines

4. Docker Hub `[5 min]`
    - What is it? -> Container Registry
    - Why do we need it?

5. Fundamentals `[20 min]`
    - Images
        - Dockerfile (simple and multistage)
    - Containers
        - creation speed
        - data deleted on container deletion
    - Volumes
        - Mounting local directories with container
    - Network (bridges)
    - cli commands: pull, run, stop, restart, rm, rmi, push, ps, logs, inspect, exec, container cp
    - container default naming convention and generation

6. Demo `[50 min]`
    - Run Helloworld `[5 min]`
        - Inspecting Dockerfile (Simple Dockerfile)
    - Run ASP.NET Core 3 hello-world `[25 min]`
        - Arguments: 
          - cli: pull, run, stop, rm, ps
          - Dockerfile: Multistage Dockerfile
          - Docker Hub: Microsoft .NET Core repo
        - Practice: start, stop, remove, and autoremove containers
    - Run Mongo DB `[20 min]`
        - Run Mongo DB (mongo)
        - Run mongoexpress and play with the database
        - Restart the Mongo DB container and lose all the data
        - Create a volume and inspect it
        - Run Mongo DB and store db data on created volume
        - Import data in mongo
          - copy files in mongo container (docker container cp)
          - open a bash shell in mongo container and run commands
            - import dataset

7.  Develop in containers (vscode) `[10 min]`
    -  Intro
      -  Why is it so important?
         -  It works on my machine
         -  Multiple versions of the same tool at the same time on the same machine without any compatibility problem
    -  Practice
      -  VS Code 2019 + Remote-Containers
      -  New ASP.NET app

8.  CI with GitHub and DockerHub: `[20 min]`
    - Sign Up in Docker Hub and GitHub
    - Push our first image to Docker Hub
    - Link to Docker Hub repo to GitHub Repo
      - Image autobuild on GitHub Trigger

9.  Docker Compose `[5 min]`

10. If we have more time :clock830:
       1.  CI/CD with Azure Pipelines: `[30 min]`
            -  Azure Portal
               -  Create a Resourse Group (RG)
               -  Create an Azure Container Registry (ACR)
            -  Azure DevOps
               -  Create a Project
               -  Create a Build Pipeline
               -  Trigger a Build Pipeline
            -  Publish from Container Registry to a web site
               -  Azure Portal: Create a new `Web App for Containers`
               -  Azure DevOps: Create a Release Pipeline

11. To be continued:
    1.  Container Orchestrators:
        - Docker Swarm
        - Kubernetes
    
    2. Service Meshes
        - Istio
        - Linkerd


## Useful links 

- [Docker in Action](https://www.manning.com/books/docker-in-action)
- [https://docs.docker.com](https://docs.docker.com)
- [https://docs.docker.com/engine/](https://docs.docker.com/engine/)
- [https://labs.play-with-docker.com](https://labs.play-with-docker.com)


## Demo

### Hello-world

1. Run your first container 

    ```bash
    docker run helloworld
    ```


### Hello-world ASP.NET

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

### Mongo DB

#### Starting and restarting container + volumes

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
    

#### Initializing Mongo DB with data

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


### Develop in container

#### Generate and run an ASP.NET Core MVC App

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


#### Using Mongo from ASP.NET Web App

1. Use git to clone the following repo and give a look to the history
    ```bash
    git clone https://github.com/FrancescoIlario/BooksApp.git
    git log --oneline --graph --decorate --all
    ```

1. Open in VS Code, build container, and run the WebApp `F5`

### Docker Hub

#### Push a docker image to Docker Hub

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

#### Set up autobuild (CI) from Github to Docker Hub

1. Open the [Docker Hub Repositories page](https://hub.docker.com/repositories) and select the `go-hello-world` repository

2. Select the `Builds` tab, then hit `Link to GitHub` and authorize `Docker Hub` to access your GitHub's repos.

3. Select thie Organization and the `go-hello-world` repository, the `master` branch as `Source`, `latest` as `Docker Tag` and finally hit `Save`

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


### Docker Compose

#### Intro

Compose is a tool for defining and running multi-container Docker applications. 
With Compose, you use a YAML file to configure your application’s services.
Then, with a single command, you create and start all the services from your configuration.
To learn more about all the features of Compose, see the list of features.

Using Compose is basically a three-step process:

1. Define your app’s environment with a Dockerfile so it can be reproduced anywhere.

2. Define the services that make up your app in docker-compose.yml so they can be run together in an isolated environment.

3. Run docker-compose up and Compose starts and runs your entire app.


#### Set up our Mongo-Express and Mongo with volume environment

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

#### Useful links

- [https://docs.docker.com/compose/](https://docs.docker.com/compose/)
- [https://docs.docker.com/compose/install/](https://docs.docker.com/compose/install/)