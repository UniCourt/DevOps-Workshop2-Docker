# Dockerfile Instructions
Docker can build images automatically by reading the instructions from a Dockerfile. A Dockerfile is a text document that contains all the commands a user could call on the command line to assemble an image. This page describes the commands you can use in a Dockerfile.

### Format of docker file
```yaml
# Comment
INSTRUCTION arguments
```
The instruction is not case-sensitive. However, convention is for them to be UPPERCASE to distinguish them from arguments more easily.

---
## FROM Instruction
The FROM instruction initializes a new build stage and sets the Base Image for subsequent instructions. As such, a valid Dockerfile must start with a FROM instruction. The image can be any valid image – it is especially easy to start by pulling an image from the Public Repositories.

## RUN Instruction
RUN has 2 forms:
```yaml
RUN <command> (shell form, the command is run in a shell, which by default is /bin/sh -c)
RUN ["executable", "param1", "param2"] (exec form)
```

The RUN instruction will execute any commands in a new layer on top of the current image and commit the results. The resulting committed image will be used for the next step in the Dockerfile.

Layering RUN instructions and generating commits conforms to the core concepts of Docker where commits are cheap and containers can be created from any point in an image’s history, much like source control.

#### Create an image with run instruction
```yaml
FROM alpine:3.15
RUN apk add --update 
RUN apk add curl
RUN rm -rf /var/cache/apk/
```
OR
```yaml
FROM alpine:3.15
RUN apk add --update && \
	apk add curl  && \  
	rm -rf /var/cache/apk/
```

#### Build the Docker image
```shell
$ docker image build -t run:1.0 .
```

#### Check the history of the Docker image
```shell
$ docker image history run:1.0
```

## CMD Instruction
The main purpose of a CMD is to provide default command for an executing container. These defaults can include an executable, or they can omit the executable, in which case you must specify an ENTRYPOINT instruction as well.
#### Create an image with CMD instruction

```yaml
FROM alpine:3.15
RUN apk update
CMD ["top"]
```

#### Build the Docker image
```shell
$ docker image build -t cmd:1.0 .
```

#### Check the history of the Docker image
```shell
$ docker image history cmd:1.0
```
---
## ENTRYPOINT Instruction
The ENTRYPOINT instruction make your container run as an executable.
ENTRYPOINT can be configured in two forms:

1. Exec Form
   ```yaml
    ENTRYPOINT [“executable”, “param1”, “param2”]
    ```
2. Shell Form
    ```yaml
    ENTRYPOINT command param1 param2
    ```

If an image has an ENTRYPOINT if you pass an argument it, while running container it wont override the existing entrypoint, it will append what you passed with the entrypoint.To override the existing ENTRYPOINT you should user –entrypoint flag when running container.
#### Create an image with CMD instruction

```yaml
FROM alpine:3.15
ENTRYPOINT ["/bin/echo", "Hi, your ENTRYPOINT instruction in Exec Form !"]
```

#### Build the Docker image
```shell
$ docker image build -t entrypnt:1.0 .
```

#### Check the history of the Docker image
```shell
$ docker image history entrypnt:1.0
```

#### Verify the image
```shell
$ docker image ls


REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
entrypnt            1.0                 1d06f06c2062        2 minutes ago       4MB
alpine              3.5                 f80194ae2e0c        7 months ago        4MB
```

#### Create and Run the container
```shell
$ docker container run entrypoint:v1

Hi, your ENTRYPOINT instruction in Exec Form !
```
---
## COPY Instruction
The COPY instruction copies files or directories from source and adds them to the filesystem of the container at destination .
#### Create an image with COPY instruction

```yaml
FROM alpine:3.15
RUN apk update
COPY index.html /usr/share/nginx/html/
ENTRYPOINT ["nginx", "-g", "daemon off;"]
```

#### Create index.html
```shell
$ echo "Welcome to UniCourt Devops Docker workshop!" > index.html
```

#### Build the Docker image
```shell
$ docker image build -t cpy:1.0 .
```
#### Verify the image
```shell
$ docker image ls

REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
cpy                 1.0                 2e0dk89yuiw0        2 minutes ago       4MB
alpine              3.5                 f80194ae2e0c        7 months ago        4MB
```

#### Run the container
```shell
$ docker container run -d --rm --name myapp1 -p 80:80 cpy:1.0
```

#### Verifying the output
```shell
$ curl localhost
 
Welcome to UniCourt Devops Docker workshop!
```
---
## WORKDIR Instruction
The WORKDIR directive in Dockerfile defines the working directory for the rest of the instructions in the Dockerfile. The WORKDIR instruction wont create a new layer in the image but will add metadata to the image config. If the WORKDIR doesn’t exist, it will be created even if it’s not used in any subsequent Dockerfile instruction. you can have multiple WORKDIR in same Dockerfile. If a relative path is provided, it will be relative to the previous WORKDIR instruction.
```yaml
WORKDIR /path/to/workdir
```

#### Create an image with WORKDIR instruction

```yaml
FROM alpine:3.15
WORKDIR /my_app
CMD pwd
```

#### Build the Docker image
```shell
$ docker image build -t wkdir:1.0 .
```
#### Verify the image
```shell
$ docker image ls

REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
wkdir               1.0                 2e0dk89yuiw0        2 minutes ago       4MB
alpine              3.5                 f80194ae2e0c        7 months ago        4MB
```

#### Run the container
```shell
$ docker container run wkdir:1.0

/my_app
```

---
## ENV Instruction
The ENV instruction in Dockerfile sets the environment variable for your container when you start. The default value can be overridden by passing --env <key>=<value> when you start the container.
```yaml
ENV <key>=<value> ...
```

#### Create an image with ENV instruction

```yaml
FROM alpine:3.15
ENV WELCOME_MESSAGE="Welcome to Docker World"

CMD ["sh", "-c", "echo $WELCOME_MESSAGE"]
```

#### Build the Docker image
```shell
$ docker image build -t env:1.0 .
```
#### Verify the image
```shell
$ docker image ls

REPOSITORY          TAG                 IMAGE ID            CREATED             SIZE
env                 1.0                 2e0dk89yuiw0        2 minutes ago       4MB
alpine              3.5                 f80194ae2e0c        7 months ago        4MB
```

#### Run the container
```shell
$ docker container run env:1.0

Welcome to Docker World
```
---
[Home](../README.md)



