# Devops Workshop-2 Docker

One Day workshop on understanding Docker and Containers. Learn to run a container, inspect a container and understand
the isolation of processes. Create a Dockerfile, and build an image from a Dockerfile. Learn how to mount application
code using volume mount. Learn how to make your app data persistent across multiple containers. Learn how to create
multiple containers from a single image, run multiple containers using docker-compose, and more.

## Prerequisites

- Machine/VM with ubuntu 22.04
- [Docker](#install-docker)
- [DockerHub Account]( https://hub.docker.com )
- [Git]( https://www.atlassian.com/git/tutorials/install-git#linux )
    - [Create github account](https://github.com/signup)
    - [Setting up SSH key with GitHub for Ubuntu](https://medium.com/featurepreneur/setting-up-ssh-key-with-github-for-ubuntu-cd8f2fabf25b)
- [VS code IDE]( https://linuxize.com/post/how-to-install-visual-studio-code-on-ubuntu-20-04/ )
- Stable Internet Connection and any browser.

## Workshop environment setup

- Check if Git, Docker, and Docker Compose are installed in on the system. Open the terminal and run the following
  command
  ```shell
  $ git --version
  git version 2.25.1

  $ docker --version
  Docker version 20.10.17, build 100c701

  $ docker compose version
  Docker Compose version v2.6.0

  ```
- Open terminal and run following command to create a folder called workshop
   ```shell
   $ mkdir workshop
   ```
- Navigate to the folder workshop and clone the from your personal repo using git
   ```shell
   $ cd workshop
   ```
- Clone DevOps-Workshop2 repo && go inside DevOps-Workshop2 folder
   ```shell
   $ git clone git@github.com:UniCourt/DevOps-Workshop2.git
   $ cd DevOps-Workshop2
   ```
- To open folder in VS code editor
   ```shell
   $ cd ~/workshop/DevOps-Workshop2
   $ code .
   ```

##### Install Docker

- If docker is not installed in your Linux run the following command
   ```shell
   $ sudo apt-get update
   $ sudo apt-get install -y curl 
   $ curl -fsSL https://get.docker.com -o get-docker.sh
   $ sudo sh get-docker.sh
  ```
- Do docker login
   ```shell
   $ sudo docker login -u username --password-stdin
   ```

#### Docker without sudo

- To run docker commands as normal user without sudo, we need to create a group for docker and add the user to it.

1. Create the docker group
    ```shell
    $ sudo groupadd docker
    ```
2. Add your user to docker group
    ```shell
    $ sudo usermod -aG docker $USER
    ```
3. Activate the changes to groups:
    ```shell
   $ newgrp docker
    ```
4. Verify that you can run docker commands without sudo.
    ```shell
   $ docker images
    ```

## What will you learn by the end of this workshop?

- By the end of this workshop, you will learn what docker is and understand how to set up containers.
- You will be introduced to containerization concepts and why it is required.
- You will learn how to build and run your own Containers.
- You will learn how to run Multiple Services with Docker Compose.

## Schedule

| Time          | Topics                                                                     |
|---------------|----------------------------------------------------------------------------|
| 09:00 - 09:30 | [`What is Docker? Why Docker?`](./docs/docker_intro.md)                    |
| 09:45 - 10:00 | [`Simple Docker Commands`](./docs/simple_docker_commands.md)               |
| 10:00 - 10:45 | [`Writing a Dockerfile`](./docs/dockerfile_instructions.md)                |
| 10:45 - 11:30 | [`Creating Our application`](./docs/creating_our_app.md)                   |
| 11:30 - 12:00 | [`Updating and sharing the App`](./docs/updating_and_sharing_our_app.md)   |
| 12:00 - 01:00 | [`Persisting our DB and Bind Mounts`](./docs/persisting_our_app.md)        |
| 01:00 - 02:00 | [`Break`]                                                                  |
| 02:00 - 03:30 | [`Multi-Container Apps`](./docs/multi_container_app.md)                    |
| 03:00 - 04:00 | [`Using Docker Compose`](./docs/using_docker_compose.md)                   |
| 04:00 - 05:00 | [`Image Building Best Practices`](./docs/image_building_best_practices.md) |                        |
| 05:00 - 05:15 | [`Q & A`]                                                                  |
| 05:15 - 05:30 | [`Wrapping Up`]                                                            |
