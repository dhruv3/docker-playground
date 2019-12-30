# Using Docker At Your Own Pace

* Cover these stuff about Docker:
  * Pull an image
  * Run a container from it, exit the session
  * Check the container state (ps)
  * Run a new bash inside the container (exec)
  * Stop and remove the container
  * Run a custom entrypoint command
  * Remove the image - clean up
* Write your own `docker-compose.yml` file, and start using it. Get comfortable starting and stopping the container stack. **Try using these dockerized services while working on your app locally**.
* Create an automated example development stack which comes up with a single command. A completely functional state of your development environment. Think of this as a “blueprint” or “functional README section” on setting this project up for development.
* At this point, you’ll have two different docker-compose files: one to bring up a complete stack (adjust your Dockerfile, to make sure it waits for the database and other services to be up). And one to only start the backing services, so you can run a development server locally, out of Docker.

# But What is Docker's Actual Job?
* Docker has three main areas of responsibility:
  * Packaging apps
  * Distributing those packaged apps
  * Running workloads
* Packaging:
  * When you are building your Docker image, you are effectively packaging your app together with its complete environment.
  * A Dockerfile, defines a sequence of commands and settings, which build a new Docker image.
* Distributing:
  * Docker push to upload the image (or just the new image layers) to a Docker registry of your choice.
* Running:
  * Dockerized app shouldn’t be bothered by where it runs as long as Docker is installed.

# 6 Docker Basics You Should Completely Grasp When Getting Started
* Containers:
  * Containers start faster and have less resource overhead compared to VMs.
  
