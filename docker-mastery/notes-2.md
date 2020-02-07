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

# Section 13: K8S install and first pods
* Kubectl: CLI to configure Kubernetes and manage apps. This is the official way to talk to k8s
* Node: Single server in the Kubernetes cluster
* Kubelet: Kubernetes agent running on nodes. This allows the node to talk to the k8s master. Docker had Swarm built in so it didn't need an agent to talk to it
* Control Plane: Set of containers that manage the cluster. Equivalent to Manager in Swarm. Also called "master".
* [Kubernetes Components](https://kubernetes.io/docs/concepts/overview/components/#master-components)
## Kubernetes Container Abstractions
* In k8s we never deploy container directly but use pods to deploy them.
* Pod: one or more containers running together on one Node. Basic unit of deployment. Containers are always in pods
* Controller: For creating/updating pods and other objects. Many types of Controllers inc. Deployment, ReplicaSet, StatefulSet, DaemonSet, Job, CronJob, etc.
* So controller is a differencing engine that checks wheter pod is doing what you said it should do. 
* Service: network endpoint to connect to a pod. Service is used by everyone to connect to a pod.
* Namespace: Filtered group of objects in cluster.
  ```bash
  kubectl run #(changing to be only for pod creation)
  kubectl create #(create some resources via CLI or YAML)
  kubectl apply #(create/update anything via YAML)
  # Creating Pods with kubectl
  kubectl version
  # Two ways to deploy Pods (containers): Via commands, or via YAML
  kubectl create deployment my-nginx --image nginx
  ```
* **create** command uses a deployment controller creates a replica set controller and then that creates the pod. Therefore our pod name has weird chars at the end.
<img width="442" alt="Screen Shot 2020-02-06 at 2 44 11 PM" src="https://user-images.githubusercontent.com/13077629/73977063-34e49280-48ef-11ea-9bb6-f44a0daacee9.png">

* Scaling using ReplicaSets:
  ```bash
  kubectl scale deploy/my-apache --replicas 2
  kubectl scale deployment my-apache --replicas 2
  ```
# Section 14: Exposing K8S ports
* Exposing Containers: It's about taking your pods and creating a persistant endpoint with which you can talk to. You can expose them internally in the cluster or outside your cluster.
* `kubectl expose` creates a service for existing pods. A **service** is a stable address for pod(s).
* CoreDNS allows us to resolve services by name.
* There are different types of services
  * ClusterIP (default)
  * NodePort
  * LoadBalancer
  * ExternalName
* **ClusterIP**: It's only about one set of pods in a cluster talking to another set of pods in the same cluster. So it's only good in a cluster and that's it.
* **NodePort**: High port allocated on each node. Port is open on every node’s IP.
* **LoadBalancer**: Creates NodePort+ClusterIP services, tells LB to send to NodePort. Only available when infra provider gives you a LB (AWS ELB). It gives kuber the ability to talk to external links.
* **ExternalName**: Not used for Pods, but for giving pods a DNS name to use for something outside Kubernetes.
  ```bash
  # Create a ClusterIP service
  kubectl expose deployment/httpenv --port 8888
  ```
* Creates a ClusterIP service. running this command does not affect the pods. it will create a ClusterIP service in front of the pod. 
  ```bash
  # Inspecting ClusterIP Service
  kubectl run --generator=run-pod/v1 tmp-shell --rm -it --image bretfisher/netshoot -- bash
  curl httpenv:8888
  ```
* **generator**: it is the template we want run command to use. We are using "run-pod" template. " -- " run whatever command is present after this "double dash". "tmp-shell" it is the part of DNS name for this service.
  ```bash
  # Create a NodePort Service
  kubectl expose deployment/httpenv --port 8888 --name httpenvnp --type NodePort
  ```
  ```bash
  # Add a LoadBalancer Service
  kubectl expose deployment/httpenv --port 8888 --name httpenvlb --type LoadBalancer 
  curl localhost:8888
  ```
# Section 15: K8S Management Techniques
* Helper templates called "generators".
* Every resource in Kubernetes has a specification or "spec".
  ```bash
  kubectl create deployment sample --image nginx --dry-run -o yaml
  ```
* You can output those templates with `--dry-run -o yaml`
  ```bash
  kubectl create job test --image nginx --dry-run -o yaml
  ```
* Used for creating pods that will only run once.
<img width="1084" alt="Screen Shot 2020-02-07 at 12 43 29 PM" src="https://user-images.githubusercontent.com/13077629/74056397-8f412a00-49a7-11ea-9535-ce6d72e13581.png">
