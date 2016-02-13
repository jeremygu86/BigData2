# Get started with Docker
### Wenxiao Jeremy Gu

https://hub.docker.com/
ID: jeremygu86@mgail.com


https://docs.docker.com/mac/
Tutorials 1-5
https://docs.docker.com/mac/step_four/

    install Docker software using the Docker Toolbox
    run a software image in a container
    browse for an image on Docker Hub
    create your own image and run it in a container
    create a Docker Hub account and an image repository
    create an image of your own
    push your image to Docker Hub for others to use


1. Docker Machine is a tool that lets you install Docker Engine on virtual hosts, and manage the hosts with docker-machine commands

2. Docker Engine: When people say “Docker” they typically mean Docker Engine, the client-server application made up of the Docker daemon, a REST API that specifies interfaces for interacting with the daemon, and a command line interface (CLI) client that talks to the daemon (through the REST API wrapper). 

Docker Engine accepts docker commands from the CLI, such as docker run <image>, docker ps to list running containers, docker images to list images, and so on.

3. Learn about images & containers

Docker Engine provides the core Docker technology that enables images and containers.

A container is a stripped-to-basics version of a Linux operating system. 

An image is software you load into a container. An image can start software as complex as a database, wait for you (or someone else) to add data, store the data for later use, and then wait for the next person.

docker run hello-world command has three parts

1) docker

2) image: tells Docker that we want to load image of hello-world into the container

3) run: creates and runs a container

    3.1) checked to see if you had the hello-world software image

    3.2) downloaded the image from the Docker Hub (more about the hub later)

    3.3) loaded the image into the container and “ran” it

The command lists running containers
    docker ps

The command lists all the images on your local system.
    docker images


1. Demo 1
https://gitlab0.bigdata220uw.mooo.com/jhenri/uwbd-instructions/blob/master/Docker%20Demo%201.md

1.1 
Using docker-machine to start a docker-engine 

Docker Engine to create images and containers
启动和关闭 docker-machine

    docker-machine start default
    docker-machine stop [名字]

# List machines
    docker-machine ls 

1.2 启动 container
Connect Docker to this machine. the “default” VM.
    docker-machine env default

Docker Machine is a tool that lets you install Docker Engine on virtual hosts, and manage the hosts with docker-machine commands
Using docker-machine commands, you can start, inspect, stop, and restart a managed host, upgrade the Docker client and daemon, and configure a Docker client to talk to your host.


1.3 Docker run

Docker runs processes in isolated containers. A container is a process which runs on a host.

Create an ubuntu machine
    docker run -it ubuntu:trusty bash
    cat /etc/lsb-release
    exit

1.4 new terminal to SSH to docker machine
Run the virtualbox GUI to see the machine called "dev". This is how we establish an ssh connection to it using docker-machine:
    docker-machine ssh default

File sharing between the physical host and the VM running docker-engine
ls /Users/

2. Demo 1
