# Docker

## Agenda
0. Intro `[5 min]`

1. Install Docker `[2 min]`
    - Docker Engine (Linux)
       - Collect links or commands
    - Docker Desktop (Windows and Mac OSX: 10-20 min + restart. Do it before attending the event, please)

2. Containers `[5 min]`

3. What is Docker and reasons why it is so important `[5 min]`
    - Virtual Machines
    - It works on my machine

4. Docker Hub `[5 min]`
    - What is it? -> Container Registry
    - Why do we need it?

5. Fundamentals `[20 min]`
    - Images
        - Dockerfile
    - Containers
        - creation speed
        - data deleted on container deletion
    - Volumes
        - Mounting local directories with container
    - bridges (network)
    - cli commands: pull, run, stop, rm, rmi, push, ps, logs, inspect (jsonpath), exec, container cp
    - container default naming convention and generation

6. Practice `[1 hour]`
    - Run Helloworld `[5 min]`
        - Inspecting Dockerfile (Simple Dockerfile)
    - Run ASP.NET Core 3 hello-world `[5 min]`
        - Port binding
        - Inspecting ASP.NET Core 3 hello-world Dockerfile (Multistage Dockerfile)
    - Run Mongo DB `[10 min]`
        - Run Mongo DB (mongo)
        - Run mongoexpress
        - Create a volume
        - Run Mongo DB with volume (for mongo's data)
        - Import data in mongo
          - copy files in mongo container (docker container cp)
          - open a shell in mongo container
          - import dataset

7.  Develop in containers (vscode) `[10 min]`
    -  Intro
      -  Why is it so important?
         -  It works on my machine
         -  Multiple versions of the same tool at the same time on the same machine without any incompatibility
    -  Practice
      -  VS Code 2019 + Remote-Containers
      -  New ASP.NET app
         -  

8.  CI/CD with Azure Pipelines: `[20 min]`
    - Build and deploy on a Container Registry (Azure CR or DockerHub)
    - Publish from Container Registry to a web site


## More on topic

- Docker in Action
- [https://docs.docker.com](https://docs.docker.com)
- [https://labs.play-with-docker.com](https://labs.play-with-docker.com)


## Practice

### Hello-world

```bash
docker run helloworld
```


### Hello-world ASP.NET

```bash
docker pull mcr.microsoft.com/dotnet/core/samples:aspnetapp

docker run \
  -it \
  --rm \
  --publish 8000:80 \
  --name aspnetcore_sample \
  mcr.microsoft.com/dotnet/core/samples:aspnetapp
```

### Mongo DB

#### Starting and restarting container + volumes

- Create a network
    ```bash
    docker network create mongo-net
    ```
- Mongo DB
    ```bash
    docker pull mongo:latest # [Not Mandatory] - https://hub.docker.com/_/mongo
    docker run \
        --detach \
        --rm \
        --network mongo-net \
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
        --network mongo-net \
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
    docker inspect mdb-vol  # here it is displayed the path
    ```

- Mongo DB + volume `mdb-vol`
    ```bash
    # Run Mongo linked to volume "mdb-vol"
    docker run \
        --detach \
        --rm \
        --network mongo-net \
        --name mdb \
        --publish 27017:27017 \
        --hostname mdb \
        --volume mdb-vol:/data/db \
        mongo:latest 

    docker ps
    docker inspect mdb
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
        --network mongo-net \
        --name mdb \
        --publish 27017:27017 \
        --hostname mdb \
        --volume mdb-vol:/data/db \
        mongo:latest 
    ```

    Visit [http://localhost:8081](http://localhost:8081) (mongo-express) dashboard and you will find the database "mdb-vol".


#### Initializing Mongo DB with data

Copy files into Mongo's container

```bash
docker container cp ./mongo/data/profiles.json mdb:/mongo/data
```

Open a shell in Mongo's container

```bash
docker exec -it mdb bash
```

Import data
```bash
mmongoimport --drop -c profiles --uri mongodb://0.0.0.0/samples profiles.json
```

Display data through `mongo-express`


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
    --publish 9001:80 \
    mcr.microsoft.com/dotnet/core/sdk:latest \
    bash

# in another window we can inspect the mounted volumes
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

1. Press `F1` (or `Ctrl+Shift+P`), select `> Remote-Containers: Reopen in Container`, and wait for the container to be prepared

1. Press `F1` (or `Ctrl+Shift+P`), select `> .NET: Generate Assets for Build and Debug`. 
   It will create a new directory `.vscode` with some configuration files.
   Inspect it if you want.

1. Press `F5` to run the application: as usual a browser will be opened and the web app home page will be shown. 
   In case it is request accept the risks from visiting a web page with a self-signed certificate.

1. Quit the debugging session


#### Developing in container

1. Use git to clone the following repo and give a look to the history
    ```bash
    git clone https://github.com/FrancescoIlario/BooksApp.git
    git log --oneline --graph --decorate --all
    ```

1. Open in VS Code, build container, and run the WebApp `F5`

