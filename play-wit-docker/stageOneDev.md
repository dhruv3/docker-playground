# Docker for Developers Stage 1

## Docker for Beginners - Linux

### Task 1: Run some simple Docker containers
* Three main ways to use containers:
  * **To run a single task**: This could be a shell script or a custom app.
  * **Interactively**: This connects you to the container similar to the way you SSH into a remote server.
  * **In the background**: For long-running services like websites and databases.
#### Run a single task in an Alpine Linux container
* In this step we’re going to start a new container and tell it to run the `hostname` command.
```bash
docker container run alpine hostname
```
* Docker keeps a container running as long as the process it started inside the container is still running. In this case the `hostname` process exits as soon as the output is written. This means the container stops. However, Docker doesn’t delete resources by default, so the container still exists in the `Exited` state.
* List all containers
```bash
docker container ls --all
```
* So you can run a container and it will execute whatever script is present in it. Therefore, you don't need to worry about actual script or config information.

#### Run an interactive Ubuntu container
* Run a Docker container and access its shell.
```bash
docker container run --interactive --tty --rm ubuntu bash
```
* `--rm` tells Docker to go ahead and remove the container when it’s done executing.
* We’re also telling the container to run `bash` as its main process (PID 1).
* `ls /` will list the contents of the root director in the container, `ps aux` will show running processes in the container, `cat /etc/issue` will show which Linux distro the container is running.
* The distribution of Linux inside the container does not need to match the distribution of Linux running on the Docker host.
* Interactive containers are useful when you are putting together your own image. You can run a container and verify all the steps you need to deploy your app, and capture them in a Dockerfile.

#### Run a background MySQL container
* Run a new MySQL container with the following command.
```bash
 docker container run \
 --detach \
 --name mydb \
 -e MYSQL_ROOT_PASSWORD=my-secret-pw \
 mysql:latest
```
* --detach will run the container in the background.
* --name will name it mydb.
* -e will use an environment variable to specify the root password (NOTE: This should never be done in production).
* This shows the logs from the MySQL Docker container.
```bash
docker container logs mydb
```
* Processes running inside the container.
```bash
docker container top mydb
```
* `docker container exec` allows you to run a command inside a container.
```bash
 docker exec -it mydb \
 mysql --user=root --password=$MYSQL_ROOT_PASSWORD --version
 ```
 * You can also use `docker container exec` to connect to a new shell process inside an already-running container. Executing the command below will give you an interactive shell (`sh`) inside your MySQL container.
 ```bash
 docker exec -it mydb sh
 ```
 
 ## Application Containerization and Microservice Orchestration
 * We will utilize Docker Compose for orchestration during the development.
 ### Step -1: Stage Setup
 ```bash
git clone https://github.com/ibnesayeed/linkextractor.git
cd linkextractor
git checkout demo
```
* Clone and checkout `demo` branch.
## Step 0: Basic Link Extractor Script
```bash
git checkout step0
tree
```
* Checkout the step0 branch and list files in it.
```bash
ls -l linkextractor.py
```
* Check the permissions on a file. If `x` flag is missin => file is not executable as a script(it can run as a python program).
## Step 1: Containerized Link Extractor Script
* We'll see the benefits of container in this step!
* Using a `Dockerfile` we can prepare a Docker image for this script.
```bash
docker image build -t linkextractor:step1 .
```
* This builds image from Dockerfile.
* This image should have all the necessary ingredients packaged in it to run the script anywhere on a machine that supports Docker.

## Step 2: Link Extractor Module with Full URI and Anchor Text
* Updated python script that does a lot more things.
* So far, we have learned how to containerize a script with its necessary dependencies to make it more portable. 

## Step 3: Link Extractor API Service
```docker
COPY       *.py /app/
RUN        chmod a+x *.py
```
* Making a script executable.
```bash
docker container run -d -p 5000:5000 --name=linkextractor linkextractor:step3
```
* We are mapping the port 5000 of the container with the 5000 of the host (using `-p 5000:5000` argument) to make it accessible from the host. We are also assigning a name (`--name=linkextractor`) to the container to make it easier to see logs and kill or remove the container.

## Step 4: Link Extractor API and Web Front End Services
* Two containers are run in this step.
* For the two containers to be able to talk to each other, we can either map their ports on the host machine and use that for request routing or we can place the containers in a single private network and access directly. 
* Docker network containers identify themselves using their names as hostnames to avoid hunting for their IP addresses in the private network.
```docker
version: '3'

services:
  api:
    image: linkextractor-api:step4-python
    build: ./api
    ports:
      - "5000:5000"
  web:
    image: php:7-apache
    ports:
      - "80:80"
    environment:
      - API_ENDPOINT=http://api:5000/api/
    volumes:
      - ./www:/var/www/html
```      
* We will supply an environment variable named API_ENDPOINT with the value http://api:5000/api/
* `api:5000` is being used because we will have a dynamic hostname entry in the private network for the API service matching its service name.
* `/var/www/html`: is the default web root for the Apache web server.

## Step 5: Redis Service for Caching
```bash
docker-compose exec redis redis-cli monitor
```
* We can use `docker-compose exec` followed by the redis service name and the Redis CLI’s monitor command.
* Redis assigns key value pair to store stuff.

## Step 6: Swap Python API Service with Ruby
* In a microservice architecture application swapping components with an equivalent one is easy as long as the expectations of consumers of the component are maintained.
