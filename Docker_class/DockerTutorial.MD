# Docker Tutorial

- **Author: Wenxiao Jeremy Gu** 

- <jeremygu86@gmail.com>

- Based on class notes and Google search.

- **Latest update:** Feb 14, 2016


### Part 1 Example 



#### 1.1 Homework review for R.K

# Yelp data import instructions for windows 7 and docker 1.9.0

## Clone the Git repository 

HTTPS git clone git@gitlab0.bigdata220uw.mooo.com:ranjithk/uw-bd220-spark-datacleaning-assignment.git
	
## Steps to Create Docker VM

Once the git repository is downloaded, right click "Docker Quickstart UW vm0" short cut and point the target to the location of file "start_docker_190.sh". This shell file is also part of git repository

Double click "Docker Quickstart UW vm0" to download docker 1.9.0 and install. This will open up git bash window.

## Commands to run on Git Bash

### Class notes for setting up dockerized spark
https://gitlab0.bigdata220uw.mooo.com/jhenri/uwbd-instructions/blob/master/Running%20Dockerized%20Spark.md

### Class notes Docker Demo 1 (not useful)
https://gitlab0.bigdata220uw.mooo.com/jhenri/uwbd-instructions/blob/master/Docker%20Demo%201.md

### Class notes Docker build
https://gitlab0.bigdata220uw.mooo.com/jhenri/uwbd-instructions/blob/master/How%20to%20docker%20build.md

### Create a docker-machine (in **~/.docker/machine/machines**)

```
    docker-machine create --driver virtualbox --virtualbox-boot2docker-url https://github.com/boot2docker/boot2docker/releases/download/v1.9.0/boot2docker.iso --engine-opt dns=8.8.8.8 --virtualbox-cpu-count=4 --virtualbox-memory=4096 --virtualbox-disk-size=20000 vm0
```
### Create spark container
```
    docker-machine start vm0
	eval $(docker-machine env vm0)
	docker rm sandbox1; docker run -it -p 8088:8088 -p 8042:8042 --name sandbox1 sequenceiq/spark:1.6.0 bash
```
## Commands to run on Git Bash once logged in to container

### Make directory to save data
	mkdir /UW

## Commands to Import yelp data set to the container on a separate git bash

Open another git bash using the "Docker Quickstart UW vm0" short cut and the launcher shell script - takes couple of minutes

	cd <directory where git repository is downloaded>
	docker cp yelp_academic_dataset_business.json  sandbox1:/UW/

## Commands to run on docker container

### Use the original gitbash shell where you are already logged in to the container and confirm the files have been copied

	ls /UW

### Create a directory in Hadoop fs to put the data files

	hadoop fs -mkdir /UW

### Put the folder and files to the hadoop directory - takes a minute or so

	hadoop fs -put /UW/yelp_academic_dataset_business.json /UW/

### Verify that the files are copied

	hadoop fs -ls /UW/

### Reduce logging

	cat "$SPARK_HOME/conf/log4j.properties.template" | perl -ne '$_ =~ s@rootCategory\=INFO@rootCategory=ERROR@; print $_;' > "$SPARK_HOME/conf/log4j.properties"

### Launch Spark shell

	spark-shell --packages com.databricks:spark-csv_2.10:1.3.0

## Spark Commands

### Set the path to the jsone file in a variable

		val path="/UW/yelp_academic_dataset_business.json"
		
### load data in file

		val business=sqlContext.read.json(path)
		
### Check Count -- Output should be 61184

		business.count()
		
### Register as a temporary table

		business.registerTempTable("business")
		
### Filter restaurants and remove newline - Example Based Cleaning

		val restaurants=sqlContext.sql("select business_id,translate(full_address, '\n',' ') full_address,hours,open,categories,city,review_count, name,neighborhoods,longitude,state,stars,latitude,attributes from business where array_contains(categories,'Nightlife') or array_contains(categories,'Restaurants') or array_contains(categories,'Pubs') or array_contains(categories,'Bars') or name like '%pizza%' or name like '%restaurant%' or name like '%bar%' or name like '%pub%' or name like '%cafe%' or name like '%rice%' or name like '%taste%' or name like '%food%' or name like '%eats%' or name like '%bread%' or name like '%tap room%' or name like '%tavern%' or name like '%lounge%' or name like '%pizzeria%'")
		
### Register restaurant as a table

		restaurants.registerTempTable("restaurants")
		
### Rank the restaurants - Ranking to help sorting

		val rankedrest=sqlContext.sql("select name, city, state, review_count, stars, rank() over(order by review_count desc), rank() over(order by stars desc) from restaurants")
		
### show top 20

		rankedrest.show()
		
### Top restaurants should be identified using a combination of review count and average review. A proper way of ranking would have been to use a probability theorem or some other algorithm. Due to lack of time restricting to a simple aggregate function - Avg

		val avgreviewcount=rankedrest.agg(avg($"review_count"))

### An attempt to store this value in a broadcast variable and use it to filter the RDD - This didnt work. Spent lot of time to figure this with no luck

		val avgreviewcount=sc.broadcast(rankedrest.agg(avg($"review_count")))

### Find the average value - It should be 47.96 after rounding

		avgreviewcount.value.show()

### Hard coding the value for Aggregate based filter Sort and Name the rank columns for better readability.

		val toprestaurants=rankedrest.filter($"review_count" > 48).orderBy($"_c6",$"_c5").select($"name",$"city",$"state",$"review_count",$"stars",$"_c6".as("StarRank"),$"_c5".as("CountRank"))
		
### Save the data in to a csv file

		toprestaurants.write.format("com.databricks.spark.csv").option("header" ,"true").mode("overwrite").save("/UW/yelp_top_restaurants.csv")
		
### List file in Hadoop -- Open issue file size is zero

	hadoop fs -ls /UW
	
### Show the top 20 rows of toprestaurants

		toprestaurants.show()
		
## Earl of Sandwich seems to be best one in the data set!





#### 1.2 Homework review for C.Z	











[Reference: Docker Documentation](https://docs.docker.com/mac/)


Tutorials 1-5
https://docs.docker.com/mac/step_four/
https://hub.docker.com/
ID: jeremygu86@mgail.com


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
	docker-machine env [名字]
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
