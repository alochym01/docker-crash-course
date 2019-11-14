# Alpine Linux is a small, the default image base for the official container images

# The FROM instruction tells Docker which base you would like to use for your image
FROM alpine:latest

# who is maintaining it
LABEL maintainer="Do Nguyen Ha <alochym@gmail.com>"

# a brief description of what the image
LABEL description="This example Dockerfile installs NGINX."

# TODO
# ONBUILD

# The ENV instruction sets environment variables within the image both when it is built and
# when it is executed. These variables can be overridden when you launch your image.
# ENV username=admin database=wordpress tableprefix=wp

# sets the user name (or UID) and optionally the user group (or GID)
# to use when running the image and for any RUN, CMD and ENTRYPOINT
# USER nginx:nginx

# sets the working directory for any RUN, CMD, ENTRYPOINT, COPY and ADD
# If the WORKDIR doesn’t exist, it will be created
# WORKDIR /path/to/workdir

# The RUN instruction is where we interact with our image
# - executes command(s) in a new layer and creates a new image
# - install software
# - run scripts, commands, and other tasks.
# Installs nginx using Alpine Linux's package manager
# RUN apk add --update nginx && \
#     rm -rf /var/cache/apk/* && \
#     mkdir -p /tmp/nginx/
RUN apk add --update nginx

# Removes any temporary files and so on to keep the size of our image to a minimum
RUN rm -rf /var/cache/apk/*

# Creates a folder with a path of /tmp/nginx/
RUN mkdir -p /tmp/nginx/

# Copying files from the docker-files folder on the host we are building our image on
COPY conf/nginx.conf /etc/nginx/nginx.conf
COPY conf/default.conf /etc/nginx/conf.d/default.conf

# ADD works as same as COPY, but it also supports 2 other sources.
# - First, you can use a URL instead of a local file / directory.
# - Secondly, automatically extracts a tar file from the source directly into the destination.
ADD conf/html.tar.gz /usr/share/nginx/

# The image is executed, the port and protocol defined will be exposed at runtime
# Docker to open port 80 every time the image runs
EXPOSE 80/tcp

# Combine ENTRYPOINT + CMD
# What this means is that whenever we launch a container from our image
# - The nginx binary is executed, as we have defined that as our ENTRYPOINT
# -  Then whatever we have as the CMD is executed
# - Result: `nginx -g daemon off;`
# Example print nginx version and then exit
# ENTRYPOINT ["nginx"]
# CMD ["-v"]
# result => nginx -v

# ENTRYPOINT configures a container that will run as an executable
# use ENTRYPOINT by itself only if you wanted to have your container be executable
ENTRYPOINT ["nginx"]

# CMD sets default command and/or parameters,
# which can be overwritten from command line when docker container runs
CMD ["-g", "daemon off;"]
