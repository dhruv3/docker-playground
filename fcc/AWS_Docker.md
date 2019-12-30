# An in-depth introduction to Docker on AWS
[Link](https://www.freecodecamp.org/news/an-in-depth-introduction-to-docker-on-aws-f373ff97da0e/)

## What is virtualization?
* Virtualization is the division of physical computer and networking resources into smaller, more flexible units, 
presenting these smaller units to users as though each was a discrete resource.
![virtualization](https://user-images.githubusercontent.com/13077629/61971551-f74c9780-afa4-11e9-9bfc-8c60afcb0a9f.png)

* As soon as your virtual server completes its task or becomes unnecessary, you can instantly delete it. 
This will free up the resources it was using for the next task in the queue.
* Containers like Docker, on the other hand, **aren’t standalone virtual machines** but are modified file systems 
sharing the operating system kernel of their physical host.

## Containers
* Containers are extremely lightweight virtual servers that, as you can see from the figure, rather 
than running as full operating systems, share the underlying kernel of their host OS.
![container-def](https://user-images.githubusercontent.com/13077629/61972061-3cbd9480-afa6-11e9-98c5-fa83d02174e2.png)
* The script-friendly container design makes it easy to automate and remotely manage complex clusters of containers, 
often deployed as microservices.

## Docker
* Docker container is an image whose behavior is defined by a script.
* What is an image?
  * It’s a software file containing a snapshot of a full operating system file system.
  * An image might consist of just a base operating system like Ubuntu. 
  But an image could also include additional layers with software applications like web servers and databases. 
* An image is launched as a container, an extra writable layer is automatically added into which the record of 
any ongoing system activity is saved.
![image-container](https://user-images.githubusercontent.com/13077629/61974616-97f28580-afac-11e9-8294-9a829d1eafcb.png)

## AWS
### AWS ECS
Amazon’s EC2 Container Service (ECS) leverages specially configured EC2 instances as hosts for integrated Docker containers. You don’t have to get your hands dirty on the EC2 instance itself, as you can provision and administrate your containers through the ECS framework.

### AWS Elastic Beanstalk
AWS Elastic Beanstalk effectively sits on top of ECS. It allows you to deploy your application across all the AWS resources normally used by ECS, but with virtually all of the logistics neatly abstracted away. Effectively, all you need in order to launch a fully scalable, complex microservices environment is a declarative JSON-formatted script in a file called Dockerrun.aws.json. You can either upload your script to the GUI or, from an initialized local directory using the AWS Beanstalk CLI.

## Docker Stuff
```bash
systemctl start docker
docker run hello-world
```
* First statement will launch the Docker daemon.
* Second statement following things happen:
 * The Docker client contacted the Docker daemon which proceeded to download the hello-world image from the repository. 
 * The image is converted to a running container by the docker run command whose output is streamed to our command line shell — the Docker client.
 
Definitions:
* Docker client — the command line shell activated by running docker commands
* Docker daemon — the local Docker process we started just before with the `systemctl` command
* Image — a file containing the data that will be used to make up an operating system


