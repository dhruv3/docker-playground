# First Alpine Linux Containers
[Source](https://training.play-with-docker.com/ops-s1-hello/)
* VMs vs Docker
  * The VM is a hardware abstraction: it takes physical CPUs and RAM from a host, and divides and shares it across several smaller virtual machines. There is an OS and application running inside the VM, but the virtualization software usually has no real knowledge of that.
  * A container is an application abstraction: the focus is really on the OS and the application, and not so much the hardware abstraction. Many customers actually use both VMs and containers today in their environments and, in fact, may run containers inside of VMs.
  * A VM has to emulate a full hardware stack, boot an operating system, and then launch your app - it’s a virtualized hardware environment. Docker containers function at the application layer so they skip most of the steps VMs require and just run what is required for the app.
* **Containers** - Running instances of Docker images — containers run the actual applications. A container includes an application and all of its dependencies. It shares the kernel with other containers, and runs as an isolated process in user space on the host OS. 
* **Docker daemon** - The background service running on the host that manages building, running and distributing Docker containers.

# Doing More With Docker Images
[Source](https://training.play-with-docker.com/ops-s1-images/)
## Creating Image using COMMIT
```bash
docker container diff <container ID>
```
* You should see a list of all the files that were added or changed to in the container when you installed figlet.
```bash
docker container commit CONTAINER_ID
```
* To create an image we need to “commit” this container. Commit creates an image locally on the system running the Docker engine.
* We can create a container, add all the libraries and binaries in it and then commit it in order to create an image.
## Creating Image using DOCKERFILE

<img width="697" alt="Screen Shot 2020-02-12 at 12 17 17 PM" src="https://user-images.githubusercontent.com/13077629/74364180-a1e5a580-4d91-11ea-977c-7a30115af6f2.png">

```bash
docker image build -t hello:v0.1 .
```
* Build a image using Dockerfile
```bash
docker container run hello:v0.1
```
* Start the container 

<img width="679" alt="Screen Shot 2020-02-12 at 12 36 46 PM" src="https://user-images.githubusercontent.com/13077629/74365775-5a144d80-4d94-11ea-9b59-a38188477271.png">

* Another important note about layers: each layer is immutable. As an image is created and successive layers are added, the new layers keep track of the changes from the layer below. When you start the container running there is an additional layer used to keep track of any changes that occur as the application runs (like the “hello.txt” file we created in the earlier exercises). 
* This design principle is important for both security and data management. If someone mistakenly or maliciously changes something in a running container, you can very easily revert back to its original state because the base layers cannot be changed.
