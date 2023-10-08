# 9. Containers intro
When we are running multiple main processes/services in one computer, they use the same operating system, file system. If you make change to the configuration/binaries/libraries, it will affect all the processes. 

What we need is isolation. To isolate different services (such as Apache Tomcat, NGINX, mongoDB), we can use different computers. But this increases the cost. 

A better solution to isolation is containers. A container is lightweight, has its own filesystem, like a miniature OS. And you can have a virtualized network in one computer, which gives IP addresses to each containers in it. In your computer, the PID1 is systemd; In MongoDB container, the PID1 is the MongoDB process; in NGINX, the PID1 is the NGINX process; etc. 

Since containers are very lightweight, they can be archived, and become a container image. Which can then be shipped to production server. 

The Container Engine running on the OS makes this possible. The most famous container runtime environment is Docker. 

## Docker intro
Docker is a open platform for developing/shipping/running applications. It enables you to separate your applications from your infrastructure. Provides the ability to package/run an app in a loosely isolated env, called a container. The isolation and security allow you to run many containers, simultaneously on a host. 

You can get image from registry, such as https://hub.docker.com. 

`docker build`: build our own images.

`docker pull`: pull from the official images

`docker run`: run the container from the image. 

### Docker hands on
Create a new dir, and put the Vagrantfile inside:
```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  config.vm.network "private_network", ip: "192.168.56.82"
  config.vm.network "public_network"
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
  end

  config.vm.provision "shell", inline: <<-SHELL
  sudo apt-get update
  sudo apt-get install \
      ca-certificates \
      curl \
      gnupg -y

  sudo install -m 0755 -d /etc/apt/keyrings
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
  sudo chmod a+r /etc/apt/keyrings/docker.gpg
  echo \
    "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
    "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  sudo apt-get update
  sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
  SHELL
end
```
This file contains Docker installation commands. 

Commands:
```console
vagrant up
vagrant ssh
sudo -i

# should show docker engine is running
systemctl status docker

# run the test image. Since it is not local, will get pulled from remote hub
docker run hello-world

# show available images on your system
docker images

# show running containers
docker ps

# show all the containers
docker ps -a

# Run a nginx container, give a name, 
# -d means run in the background, 
# -p means run at a given port, 
# 9080 is the host port, 80 is the container port. 
# this port mapping allows for accesses from outside of the internal network. 
docker run --name web01 -d -p 9080:80 nginx

# should show the image is running
docker ps

# can see its ip 172.17.0.2
docker inspect web01

# will show the nginx default page
curl http://172.17.0.2:80

# (access from outside of the vm)
# get the ip of this vm, find the bridge ip, 192.168.0.127
# then access it in the browser of the local machine 192.168.0.127:9080
# can see the html rendered
ip addr show
```

Build an image. 
```
# Building an Image
mkdir images
cd images/
vim Dockerfile
```
and paste below content, which is built on latest ubuntu image:
```
FROM ubuntu:latest AS BUILD_IMAGE
RUN apt update && apt install wget unzip -y
RUN wget https://www.tooplate.com/zip-templates/2128_tween_agency.zip
RUN unzip 2128_tween_agency.zip && cd 2128_tween_agency && tar -czf tween.tgz * && mv tween.tgz /root/tween.tgz

FROM ubuntu:latest
LABEL "project"="Marketing"
ENV DEBIAN_FRONTEND=noninteractive

RUN apt update && apt install apache2 git wget -y
COPY --from=BUILD_IMAGE /root/tween.tgz /var/www/html/
RUN cd /var/www/html/ && tar xzf tween.tgz
CMD ["/usr/sbin/apache2ctl", "-D", "FOREGROUND"]
VOLUME /var/log/apache2
WORKDIR /var/www/html/
EXPOSE 80
```

Then:
```
# Build Image, in the current dir. Will take time to build
docker build -t tesimg .

# should see the new image here
docker images

# Run container from our Image
# take the port automatically
docker run -P -d tesimg

# should show the image is running, and get the port number
docker ps

# find the ip addr of the vm, append the port number, to access from outside of vm
# shows the website hosted in a container
ip addr show


# clean up
# show all the running containers, and stop them
docker ps
docker stop web01 heuristic_hugle

# show all containers, and remove all of them
docker ps -a
docker rm heuristic_hugle web01 competent_gates elastic_ramanujan relaxed_sammet
clear

# show all the docker images, and remove all of them
docker images
docker rmi a54ee9c44b3b 6130c26b5558 057d51c0049c 825d55fb6340 12766a6745ee feb5d9fea6a5
```

## Vprofile Project on containers
Vagrantfile:
```ruby
Vagrant.configure("2") do |config|
  config.vm.box = "ubuntu/focal64"
  config.vm.network "private_network", ip: "192.168.56.82"
  config.vm.network "public_network"
  config.vm.provider "virtualbox" do |vb|
    vb.memory = "2048"
  end
  config.vm.provision "shell", inline: <<-SHELL
  sudo apt-get update
  sudo apt-get install \
      ca-certificates \
      curl \
      gnupg -y

  sudo install -m 0755 -d /etc/apt/keyrings
  curl -fsSL https://download.docker.com/linux/ubuntu/gpg | sudo gpg --dearmor -o /etc/apt/keyrings/docker.gpg
  sudo chmod a+r /etc/apt/keyrings/docker.gpg
  echo \
    "deb [arch="$(dpkg --print-architecture)" signed-by=/etc/apt/keyrings/docker.gpg] https://download.docker.com/linux/ubuntu \
    "$(. /etc/os-release && echo "$VERSION_CODENAME")" stable" | \
    sudo tee /etc/apt/sources.list.d/docker.list > /dev/null
  sudo apt-get update
  sudo apt-get install docker-ce docker-ce-cli containerd.io docker-buildx-plugin docker-compose-plugin -y
  sudo curl -L "https://github.com/docker/compose/releases/download/v2.1.1/docker-compose-$(uname -s)-$(uname -m)" -o /usr/local/bin/docker-compose

  chmod +x /usr/local/bin/docker-compose
  SHELL
end
```

Commands:
```console
# show all entries of vms on your local machine, stop them using vagrant halt
vagrant global-status --prune

# bring up this vm
vagrant up
vagrant ssh
sudo -i

# create "compose" dir
mkdir compose
cd compose/

# docker compose command, read from docker-compose.yml file, will show all options 
docker compose

# download docker-compos.yml file for vprofile project, 
# click raw view for this file in github to get the link for wget
wget https://raw.githubusercontent.com/devopshydclub/vprofile-project/docker/compose/docker-compose.yml
# paste the content into this file, if the link does not work
ls      # see the downloaded file
vim docker-compose.yml

# bring up all the containers
docker compose up -d

# check status of all containers
docker compose ps

# show all the images from where the containers are created
docker images

# get ip of vm
ip addr show

# Go to browser and enter vm-ip:80, to go to nginx container

# stop the containers
docker compose down

# rmv the containers, and all unused images
docker system prune -a
```

"docker-compose.yml":
```yml
version: '3.8'
services:
  vprodb:
    image: vprocontainers/vprofiledb # this is from uploade by vprocontainers account
    ports:
      - "3306:3306"
    volumes:
      - vprodbdata:/var/lib/mysql
    environment:
      - MYSQL_ROOT_PASSWORD=vprodbpass

  vprocache01:
    image: memcached # this is the official container for memcache
    ports:
      - "11211:11211"

  vpromq01:
    image: rabbitmq
    ports:
      - "15672:15672"
    environment:
      - RABBITMQ_DEFAULT_USER=guest
      - RABBITMQ_DEFAULT_PASS=guest

  vproapp:
    image: vprocontainers/vprofileapp # this is from uploade by vprocontainers account
    ports:
      - "8080:8080"
    volumes: 
      - vproappdata:/usr/local/tomcat/webapps

  vproweb:
    image: vprocontainers/vprofileweb # this is from uploade by vprocontainers account
    ports:
      - "80:80"
volumes:
  vprodbdata: {}
  vproappdata: {}
```

## Microservices
Difference between monolithic and microservices application. 

Monolithic: For example, a Java app, as a big service, running on Tomcat, it can have many sub service in it, such as a user interface, posts, chat, notification. If you want to make a change to any of the sub service, then you need to re-deploy the entire artifact. Andy everything needs to be redone in Java. 

Microservices: For example, for the same big service, UI microservice can be written in Java, chat microservice written in Node.js, notification microservice written in Python, etc. These microservices can be deployed on different servers, and will interact with each other through an API gateway (communicate with APIs). Thus we achieve isolation. All the applications can be developed independently, have their own languages, libraries, etc. 

We can use Docker to containerize them to reduce costs. That's why microservices and containers usually go side by side. 

### Microservices project - EMart App
In the front end, there is an API Gateway created with nginx, and listens at 3 end points: 
1. root /. When the user access the URL of the website, they will be redirected to root, which is an app written in Angular (the client app).
2. /spi. The client app will in turn connect to /api, which is served by an Emart api written in NodeJS, and connects to MongoDB. 
3. /webapi. This connects to the Books api, which is written in Java, and connects to a MySQL db. 

All of the microservices will be deployed as containers. EMart app represents an e-commerce app, like Amazon. An application can start like this, and can keep adding more microservices, such as another url for videos, for payment gateway, for cart, etc. 

The same vagrant file an be used as in prv section. 
```console
# Clone source code of Emart App
git clone https://github.com/devopshydclub/emartapp.git
cd emartapp/

# see thd docker-compose.yaml file in the dir
ls

# Bring up containers from docker-compose file
vim docker-compose.yaml   
docker compose up -d      # build and bring up images

docker compose ps         # see all the container running

docker ps -a

ip addr show

# Go to browser enter http://VMIp:80

# Clean up
docker compose down       # stop and rmv all containers
docker system prune -a
exit
exit
vagrant halt
```
