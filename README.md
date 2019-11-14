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
-   add docker group and docker user - ubuntu only
    -   sudo groupadd docker
    -   sudo usermod -aG docker $USER

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

-   [Docker cheat sheet](http://dockerlabs.collabnix.com/docker/cheatsheet/)
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
        -   `docker run -v /app/node_modules -v $(pwd):/app image_id`
    -   `docker container`
        -   `docker exec -it container_id command_cli`
            -   docker -it container_id bash/redis-cli
        -   stop container_id
        -   start
    -   `docker images` list all cached images
        -   `pull`: download remote images
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
    ```yaml
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
-   Multiple phase to build Dockerfile
    ```yaml
    # start phase 1
    FROM node:alpine as builder

    WORKDIR '/app'

    COPY package.json .

    RUN npm install

    COPY . .
    RUN npm run build # result save in /app/build
    # end phase 1

    # start phase 2
    FROM nginx:latest

    COPY --from=builder /app/build /usr/share/nginx/html
    # end phase 2
    ```
#### Creating Docker-compose file

-   speecify version
-   define service_1
-   define service_2
-   example
    ```yaml
    version: '3'

    services:
        web:
            build:
                context: .
                dockerfile: Dockerfile.dev # Dockerfile.dev should be same directory with docker-compose.yaml
            # build: . # default Dockerfile is used, Dockerfile should be same directory with docker-compose.yaml
            ports:
                -   "3000:3000"
            volumes:
                -   /app/node_modules
                -   .:/app # map current directory to /app folder inside docker container
        tests:
            build:
                context: .
                dockerfile: Dockerfile.dev
            volumes:
                -   /app/node_modules
                -   .:/app
            command: ["npm", "run", "tests"]
    ```
#### TravisCI

-   Tell Travis to run a docker
-   Building our images using Docker
-   Tell Travis to run test case
-   Tell Travis deploy our code to server
-   `.travis.yml`
    ```yaml
    sudo: required

    services:
        -   docker

    before_install:
        -   docker build -t alochym/docker-github -f Dockerfile.dev .

    # running test cases
    script:
        -   docker run alochym/docker-github npm run test -- --converage

    # deploy code
    deploy:
        provider: elasticbeantalk
        app: :docker:
        env: "docker-env"
        on:
            # github branch
            master
    ```

#### Building Container Images

![image build](https://raw.githubusercontent.com/alochym01/docker-crash-course/master/cl-cloud-native-container-design-whitepaper_Image2_v1.png)

-   [https://docs.openshift.com/enterprise/3.0/creating_images/guidelines.html](https://docs.openshift.com/enterprise/3.0/creating_images/guidelines.html)
-   [https://www.redhat.com/en/resources/cloud-native-container-design-whitepaper](https://www.redhat.com/en/resources/cloud-native-container-design-whitepaper)
-   [https://gotocon.com/dl/goto-berlin-2015/slides/MatthiasLbken_PatternsInAContainerizedWorld.pdf](https://gotocon.com/dl/goto-berlin-2015/slides/MatthiasLbken_PatternsInAContainerizedWorld.pdf)
-   process healthy
-   metrics
-   tracing
-   logs
-   readiness
-   liveness

##### Steps

-   Create Dockerfile
-   Build image using cli: `docker image build --tag alochym/nginx-hello:v1 .`
    -   Create docker image with tag name alochym/nginx-hello
-   Check built image using cli `docker image ls`
-   Inspect docker image using cli `docker image inspect -f {{.Config.Labels}} alochym/nginx-hello:v1`
-   Runing docker image using cli `docker container run -d --name nginx-hello -p 8080:80 alochym/nginx-hello`
-   Check docker container running using cli `docker container ls -a`
-   Stop docker container using cli `docker container stop container_id`
-   Remove docker container using cli `docker container rm container_id`

#### Storing and Distributing Images

-   Setup automated build using WEBHOOK
-   Docker hub
    -   Create Repository
    -   Create Automated Build
    -   Create Organization
-   [https://docs.docker.com/docker-hub/builds/](https://docs.docker.com/docker-hub/builds/)
-   [https://medium.com/better-programming/github-actions-for-docker-eaf22bbcc879](https://medium.com/better-programming/github-actions-for-docker-eaf22bbcc879)
-   [https://www.youtube.com/watch?v=E1OunoCyuhY](https://www.youtube.com/watch?v=E1OunoCyuhY)
### Learning Kubernetes

-   [Load Balance Ingress](https://metallb.universe.tf/)

### Networking

-   https://blog.dave.tf/post/jumbo-frames/
