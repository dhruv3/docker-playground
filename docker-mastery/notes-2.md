# Section 8: Swarm Features
## Overlay
* Swarm has this new networking driver called OVERLAY aka Overlay Multi-Host Networking
* It creates a swarm wide bridge network. Only for intra-swarm comm between containers.
* Each service can be connected to multiple networks.
## Routing Mesh
* Magic of auto handling of requests is handled by the **routing mesh**.
* Routes ingress (incoming) packets for a Service to proper Task.
* Spans all nodes in Swarm.
* Routing Mesh load balances across all the nodes and listens to traffic.
* Containers and networks are a many-to-many relationship. A single container can be attached to many networks.
* This is stateless load balancing. 
  ```bash
  docker network create --driver overlay frontend
  docker network create --driver overlay backend

  docker service create --name vote  --network frontend --replicas 3 -p 80:80 bretfisher/examplevotingapp_vote

  docker service create --name redis —network frontend --replicas 1 redis:3.2

  docker service create --name worker —network frontend --replicas 1 bretfisher/examplevotingapp_worker:java
  docker service update --network-add backend worker
  OR
  docker service create --name worker —network frontend —network backend replicas 1 bretfisher/examplevotingapp_worker:java

  docker volume create --name db-data 
  docker service create --name db --mount type=volume,source=db-data,target=/var/lib/postgresql/data --network backend --replicas 1 postgres:9.4

  docker service create --name result --network backend --replicas 1 -p 80:5001 bretfisher/examplevotingapp_result
  ```
 * Solution to Create Multi-Service App
## Stacks: Production Grade Compose
* Docker adds a new layer of abstraction to Swarm called **Stacks**.
* Stacks accept Compose files as their declarative definition for services, networks, and volumes
```bash
 docker stack deploy -c example-voting-app.yml voteapp
```
* Version of the compose file should be 3 or higher
* "deploy" is the new key present in compose file. There are other keys like parallelism.

<img width="411" alt="Screen Shot 2020-02-05 at 12 34 27 PM" src="https://user-images.githubusercontent.com/13077629/73871684-e8785480-4813-11ea-9b00-30698f3c2bd4.png">

* Stacks manages all those objects for us, including overlay network per stack. Adds stack name to start of their name.
## Secrets
* It is a swarm specific feature and cannot be used without swarm
* To use secret with stack your version needs to be "3.1" or higher
* Only stored on disk on Manager nodes.

* **NOTE**: [Difference b/w Docker Stack and Docker Compose](https://vsupalov.com/difference-docker-compose-and-docker-stack/)

# Section 9: Swarm App Lifecycle
* If you have "docker-compose.yml" and "docker-compose.override.yml" then you run "docker-compose up" it will run former first and then overlay with latter.
  ```bash
  docker-compose -f docker-compose.yml -f docker-compose.test.yml up -d
  ```
* To run compose in test env. You mention the base compose file first and then test file.
* There is no such thing as "stack update" you just update service or whatever in backend.
* Docker Healthchecks

# Section 10: Container Registeries
* Docker Hub: It's really Docker Registry plus lightweight image building.
* Private Docker Registry: A private image registry for your network. `docker pull registry` to get started.
* Secure your Registry with TLS.
* "Secure by Default": Docker won't talk to registry without HTTPS. Except, localhost (127.0.0.0/8).
  ```bash
  # Run the registry image
  docker container run -d -p 5000:5000 --name registry registry
  # Re-tag an existing image and push it to your new registry
  docker tag hello-world 127.0.0.1:5000/hello-world
  docker push 127.0.0.1:5000/hello-world
  # Remove that image from local cache and pull it from new registry
  docker image remove hello-world
  docker image remove 127.0.0.1:5000/hello-world
  docker pull 127.0.0.1:5000/hello-world
  # Re-create registry using a bind mount and see how it stores data
  docker container run -d -p 5000:5000 --name registry -v $(pwd)/registry-data:/var/lib/registry registry
  ```
* Run a Private Docker Registry.

# Section 11: Docker in Prod
* Good Dockerfiles are more important than fancy orchestration.
* It's your new build and environment documentation.
* Anti-Pattern: Not storing unique data in volumes.
* Anti: Letting image builds pull FROM latest
* Anti: Letting image builds install latest packages
* Anti: Not changing defaults in container like you would on a VM
* Anti: Copying in environment config at image build
* 3-Node Swarm: Minimum for HA
* 10-Node Swarm: Separating Out Managers. 5 dedicated managers

# Section 12: What and Why K8S
* Orchestrator: takes your containers and runs them on your nodes(servers)
* Container Orchestration = Make many servers act like one
* Cloud providers will give you kube endpoints which you can use locally.
* Orchestration is there to monitor your resources and ensure your changes are automated. So you won't need it if your team is small.
* Swarm: Easier to deploy/manage
* Kubernetes: More features and flexibility
