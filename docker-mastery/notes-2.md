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
