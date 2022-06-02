# Docker Getting Started Tutorial

This tutorial has been written with the intent of helping folks get up and running
with containers and is designed to work with Docker Desktop. While not going too much 
into depth, it covers the following topics:

- Running your first container
- Building containers
- Learning what containers are running and removing them
- Using volumes to persist data
- Using bind mounts to support development
- Using container networking to support multi-container applications
- Using Docker Compose to simplify the definition and sharing of applications
- Using image layer caching to speed up builds and reduce push/pull size
- Using multi-stage builds to separate build-time and runtime dependencies

## Getting Started

If you wish to run the tutorial, you can use the following command after installing Docker Desktop:

```bash
cd app
docker run -d -p 80:80 docker/docker-getting-started
```

Once it has started, you can open your browser to [http://localhost](http://localhost).

## Development

This project has a `docker-compose.yml` file, which will start the mkdocs application on your
local machine and help you see changes instantly.

```bash
docker-compose up
```

To rebuild 
```bash
docker build -t docker-getting-started .
```

start the new container
```bash
docker run -dp 3000:3000 docker-getting-started
```

create volume
```bash
docker volume create todo-db
```

start app with db volume 
```bash
docker run -dp 3000:3000 -v todo-db:/etc/todos docker-getting-started
```

Start a dev-mode container

To run our container to support a development workflow, we will do the following:

    Mount our source code into the container
    Install all dependencies, including the “dev” dependencies
    Start nodemon to watch for filesystem changes


    Make sure you don’t have any previous getting-started containers running.

    Run the following command from the app directory. We’ll explain what’s going on afterwards:

```bash
 docker run -dp 3000:3000 \
     -w /app -v "$(pwd):/app" \
     node:12-alpine \
     sh -c "yarn install && yarn run dev"
```

Start MySQL

There are two ways to put a container on a network: 1) Assign it at start or 2) connect an existing container. For now, we will create the network first and attach the MySQL container at startup.

    Create the network.
```bash
 docker network create todo-app
```

Start a MySQL container and attach it to the network. We’re also going to define a few environment variables that the database will use to initialize the database (see the “Environment Variables” section in the MySQL Docker Hub listing).

```bash
 docker run -d \
     --network todo-app --network-alias mysql \
     -v todo-mysql-data:/var/lib/mysql \
     -e MYSQL_ROOT_PASSWORD=secret \
     -e MYSQL_DATABASE=todos \
     mysql:5.7
```

To confirm we have the database up and running, connect to the database and verify it connects.

```bash
 docker exec -it <mysql-container-id> mysql -u root -p

```
When the password prompt comes up, type in secret. In the MySQL shell, list the databases and verify you see the todos database.

```MySQL
 MYSQL> SHOW DATABASES;
```
You should see output that looks like this:

 +--------------------+
 | Database           |
 +--------------------+
 | information_schema |
 | mysql              |
 | performance_schema |
 | sys                |
 | todos              |
 +--------------------+
 5 rows in set (0.00 sec)

