---
topic: docker
title: Tutorial1 - Hello-world Docker example
---

This short tutorial gives you a very brief idea on how docker containers are launched. Although Docker is not typically used to run workloads on HPC systems, Docker images are commonly used as the starting point for building and distributing scientific containers. Understanding the basics of Docker will therefore make it easier to work with Apptainer containers later in this course. Docker is one of the most widely used and mature container platforms and provides a useful introduction to container concepts that also apply to Apptainer.

## Learning Objectives
In this tutorial, you will learn to: 
 - Launch a simple Docker container using the hello-world image
 - Explain the difference between a container image and a running container
 - Understand what happens behind the scenes when a Docker container is created and executed

## Connnect to course VM

We have provided a build environment in cPouta for the course.

Start by downloading ssh key:

```bash
wget  <url>
```

Uncompress the file. When prompted, enter password. Password provided in zoom chat.

```bash
unzip xxx
```

You can now use the key to login to the pouta. Replace courseXX with your assigned course username (see the HedgeDoc document).

```bash
ssh  -i course_key courseXX@<VM-IP-address>
```

## Launch a hello-world Container 

Let’s start with a simple *hello-world* container by running the following command on linux terminal:

```bash
sudo docker run hello-world
```
When a `docker run` command is issued *via* commandline interface (CLI), docker client contacts Docker daemon to check whether an image named, "hello-world" exists locally. If the image is not found locally, docker daemon pulls "hello-world" official image from DockerHub. Once docker image is available locally, `docker run` command creates a new container from that image.

#### What is the terminal output from the above `docker run` command? #####

  ```bash
  Unable to find image 'hello-world:latest' locally
  latest: Pulling from library/hello-world
  78445dd45222: Pull complete
  Digest: sha256:c5515758d4c5e1e838e9cd307f6c6a0d620b5e07e6f927b07d05f6d12a1ac8d7
  Status: Downloaded newer image for hello-world:latest

 Hello from Docker!
  This message shows that your installation appears to be working correctly.
 ```
 
Note that `docker run` creates a container, executes the command in it and stops the container when it is done.

#### What will happen if you run the above command again? Did you observe any change in terminal output now?

Docker image has already been downloaded locally and therefore docker can run the container straight away.

#### What is the default image tag used in `hello-world` example?

By default, an image is pulled with `latest` tag if available. It is possible to pull an image with a specified tag and is actually a good practice to use specific tag name for reproducible research. Tag provides a version control-like mechanism for docker images.

Congratulations, you have run a “Hello-World” Docker successfully !!!


