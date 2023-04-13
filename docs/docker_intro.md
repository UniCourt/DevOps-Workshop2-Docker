# What is Docker?

Docker is an open-source software platform that helps you simplify the process of creating, managing, running, and
distributing your applications, in other words it is a container management service. With Docker, you can package your
application along with all its dependencies into a container. Containers allow your applications to be deployed easily
and uniformly

The company started as a platform as a service that was built on Linux containers. To help make and manage the
containers, they built an in-house tool that they nicknamed “Docker,” which is how the technology was born. The first
version was released in 2013. and since then, it has become the buzzword for modern world development, especially in the
face of Agile-based projects.

## Video Session: Introduction to Docker and Containers

[![Video Session: Introduction to Docker and Containers](https://i3.ytimg.com/vi/JSLpG_spOBM/hqdefault.jpg)](https://www.youtube.com/watch?v=JSLpG_spOBM&ab_channel=RyanSchachte)

---

## Features of Docker

- Docker has the ability to reduce the size of development by providing a smaller footprint of the operating system via
  containers.

- With containers, it becomes easier for teams across different units, such as development, QA and Operations to work
  seamlessly across applications.

- You can deploy Docker containers anywhere, on any physical and virtual machines and even on the cloud.

- Since Docker containers are pretty lightweight, they are very easily scalable.

## What is a container?

Containers are instances of Docker images that can be run using the Docker run command. The basic purpose of Docker is
to run containers.

- is a runnable instance of an image. You can create, start, stop, move, or delete a container using the DockerAPI or
  CLI.
- can be run on local machines, virtual machines or deployed to the cloud.
- is portable (can be run on any OS)
- Containers are isolated from each other and run their own software, binaries, and configurations.

## What is a container image?

When running a container, it uses an isolated filesystem. This custom filesystem is provided by a container image. Since
the image contains the container’s filesystem, it must contain everything needed to run an application - all
dependencies, configuration, scripts, binaries, etc. The image also contains other configuration for the container, such
as environment variables, a default command to run, and other metadata.

## Components of Docker

### Docker Engine

When people refer to Docker, they're typically referring to Docker Engine.

Docker Engine is the underlying open source containerization technology for building, managing, and running
containerized applications. It's a client-server application with the following components:

- Docker daemon (called dockerd) is a service that runs in the background that listens for Docker Engine API requests
  and manages Docker objects like images and containers.
- Docker Engine API is a RESTful API that's used to interact with Docker daemon.
- Docker client (called docker) is the command line interface used for interacting with Docker daemon. So, when you use
  a command like docker build, you're using Docker client, which in turn leverages Docker Engine API to communicate with
  Docker daemon.

#### Docker architecture

![docker_architecture](./img/docker_arch.svg)

---
[Home](../README.md)
