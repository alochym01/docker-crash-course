# Learning Docker and Kubernetes

### Learning Docker
#### Docker Hub Account

-   sites:
    -   hub.docker.com
    -   id.docker.com
-   username/password: hadn4/dothihiep@
#### Install Docker

-   yum install -y docker
-   systemctl enable docker
-   systemctl start docker

#### Docker Version

```bash
Client: Docker Engine - Community
 Version:           19.03.2
 API version:       1.40
 Go version:        go1.12.8
 Git commit:        6a30dfc
 Built:             Thu Aug 29 05:28:55 2019
 OS/Arch:           linux/amd64
 Experimental:      false

Server: Docker Engine - Community
 Engine:
  Version:          19.03.2
  API version:      1.40 (minimum version 1.12)
  Go version:       go1.12.8
  Git commit:       6a30dfc
  Built:            Thu Aug 29 05:27:34 2019
  OS/Arch:          linux/amd64
  Experimental:     false
 containerd:
  Version:          1.2.6
  GitCommit:        894b81a4b802e4eb2a91d1ce216b8817763c29fb
 runc:
  Version:          1.0.0-rc8
  GitCommit:        425e105d5a03fabd737a126ad93d62a9eeede87f
 docker-init:
  Version:          0.18.0
  GitCommit:        fec3683
```

#### Docker Command Line

-   How it works
    ```bash
    docker run hello-world

                    Your Computer           Docker Hub Images

    docker run  --> Docker Client      -->  hello-world
                         ||            |        |
                    Docker Server   ----        |
                         ||                     |
                    Images Cached               |
                    -------------               |
                    |           |               |
                    |hello-world|   <------------
                    |           |
                    -------------
    ```
-   Syntax
    -   `docker run [OPTIONS] IMAGE[:TAG|@DIGEST] [COMMAND] [ARG...]`
        -   https://docs.docker.com/v17.12/edge/engine/reference/commandline/run/#options
        -   `docker run -p host_port:container_port image_id`
    -   `docker container`
        -   `docker exec -it container_id command_cli`
            -   docker -it container_id bash/redis-cli
        -   stop container_id
        -   start
    -   `docker images` list all cached images
    -   `docker ps` list all running containers
    -   `docker logs container_id` check logs of container_id
    -   `docker system prune`: delete all stop containers, build cached, ...
    -   `docker build .`: building image from `Dockerfile`
        -   tags for images from docker build for easy using
            -   `docker build -t alochym/redis:latest .`
            -   `docker run alochym/redis`

#### Creating Dockerfile

-   specify a base image
-   run command to install additional programs(yum install -y net-tools)
-   specify a command to run on container startup
-   example
    ```bash
    # specify base image
    FROM node:alpine

    # change working directory, a directory is auto created if not exist
    WORKDIR /mnt/alochym

    # Install packages dependancy
    COPY ./packages.json /mnt
    RUN npm install

    # Default command to run
    CMD ["npm", "start"]
    ```
### Learning Kubernetes
