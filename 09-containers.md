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



















