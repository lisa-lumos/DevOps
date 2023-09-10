# 9. Containers intro
When we are running multiple main processes/services in one computer, they use the same operating system, file system. If you make change to the configuration/binaries/libraries, it will affect all the processes. 

What we need is isolation. To isolate different services (such as Apache Tomcat, NGINX, mongoDB), we can use different computers. But this increases the cost. 

A better solution to isolation is containers. A container is lightweight, has its own filesystem, like a miniature OS. And you can have a virtualized network in one computer, which gives IP addresses to each containers in it. In your computer, the PID1 is systemd; In MongoDB container, the PID1 is the MongoDB process; in NGINX, the PID1 is the NGINX process; etc. 

Since containers are very lightweight, they can be archived, and become a container image. Which can then be shipped to production server. 

The Container Engine running on the OS makes this possible. The most famous container runtime environment is Docker. 

## Docker intro





























