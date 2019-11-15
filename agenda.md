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
docker run --detach helloworld
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
        --volume mdb-vol:/data/db \
        mongo:latest 
    ```

    Visit [http://localhost:8081](http://localhost:8081) (mongo-express) dashboard and you will find the database "mdb-vol".


#### Initializing Mongo DB with data

Copy files into Mongo's container

```bash
docker container cp ./mongo/data/books.json mdb:/mongo/data/books
```

Open a shell in Mongo's container

```bash
docker exec -it mdb bash
```

Import data
```bash
mongoimport -h 0.0.0.0 --port 27017 --drop -c books --file=books.json
```

Display data through `mongo-express`


#### Let's use this data

