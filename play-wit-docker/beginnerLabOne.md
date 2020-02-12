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

* 
