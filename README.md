# Kubernetes-Notes


## 1. Kubernetes vs Docker

### Docker:
- **Purpose**: Docker is a platform for developing, shipping, and running applications inside containers.
- **Key Features**:
  - **Containerization**: Creates isolated environments for applications.
  - **Portability**: Containers can run on any system—developer machines, testing environments, or production servers.
  - **Images**: Containers are created from read-only templates called Docker images, built from a Dockerfile.

### Kubernetes:
- **Purpose**: Kubernetes is a container orchestration platform that automates the deployment, scaling, and management of containerized applications.
- **Key Features**:
  - **Cluster Management**: Manages clusters of Docker containers.
  - **Automatic Scaling**: Automatically adjusts the number of running containers based on workload demands.
  - **Self-healing**: Restarts containers that fail or become unresponsive.
  - **Load Balancing**: Distributes traffic to containers across multiple instances for high availability and fault tolerance.

### Key Difference:
- Docker focuses on container creation and management, while Kubernetes manages and orchestrates containers across a cluster, handling tasks like scaling, deployment, and monitoring.

---

## 2. Kubernetes Architecture

Kubernetes follows a **client-server** architecture with several components that ensure the desired state of the cluster is maintained.

### Master Node (Control Plane):
- **API Server**: The entry point for all REST commands used to control the cluster. It exposes the Kubernetes API and handles communication with the cluster.
- **Scheduler**: Decides which node should run the pod based on available resources and constraints.
- **Controller Manager**: Manages the lifecycle of resources in the cluster (e.g., scaling, replication).
  - **Replication Controller**: Ensures a specified number of pod replicas are running.
  - **Deployment Controller**: Manages updates and rollouts of deployments.
- **etcd**: A distributed key-value store that holds all configuration and state data of the cluster.

### Worker Node:
- **Kubelet**: Ensures that containers in the pods are running and healthy.
- **Kube Proxy**: Handles network traffic routing, ensuring that requests are sent to the appropriate pod.
- **Container Runtime**: The software responsible for running containers (e.g., Docker, containerd).

---

## 3. Pods

A **Pod** is the smallest and simplest Kubernetes object. A pod can host one or more containers that share the same network, storage, and other resources.

### Key Features:
- **Single-container Pods**: A pod with only one container.
- **Multi-container Pods**: Containers in a pod can communicate over localhost and share storage volumes, which makes them ideal for co-located applications (e.g., a web server and a log shipper).
  
### Pod Lifecycle:
- **Pending**: The pod is being scheduled to a node.
- **Running**: The pod is running its containers.
- **Succeeded**: The containers in the pod have finished successfully.
- **Failed**: One or more containers have terminated with an error.
- **Unknown**: The pod's state cannot be determined.

---

## 4. Development with Kubernetes

### kubectl Commands:
- **`kubectl get pods`**: List all pods in the current namespace.
- **`kubectl describe pod <pod_name>`**: Show detailed information about a pod.
- **`kubectl apply -f <file>.yaml`**: Apply a configuration from a YAML file to create or update resources.
- **`kubectl delete -f <file>.yaml`**: Delete resources defined in a YAML file.

### Example Deployment Manifest:
```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: my-app
spec:
  replicas: 3
  selector:
    matchLabels:
      app: my-app
  template:
    metadata:
      labels:
        app: my-app
    spec:
      containers:
      - name: my-app-container
        image: my-app:v1
        ports:
        - containerPort: 8080


## 5. Services

- A **Service** is an abstraction that defines a set of pods and a policy by which to access them.
  - **ClusterIP**: Exposes the service on an internal IP.
  - **NodePort**: Exposes the service on a static port on each node's IP.
  - **LoadBalancer**: Exposes the service via an external load balancer.
  - **ExternalName**: Maps the service to an external DNS name.

---

## 6. Ingress

- **Ingress** is an API object that manages external access to services in a cluster, typically HTTP.
  - An **Ingress Controller** is required to manage the Ingress resources.
  - **Ingress Rules**: Define how to route traffic to different services based on URL paths or hostnames.
  - **Example**:
    ```yaml
    apiVersion: networking.k8s.io/v1
    kind: Ingress
    metadata:
      name: example-ingress
    spec:
      rules:
      - host: myapp.example.com
        http:
          paths:
          - path: /path
            pathType: Prefix
            backend:
              service:
                name: my-service
                port:
                  number: 80
    ```

---

## 7. RBAC (Role-Based Access Control)

- **RBAC** is used to regulate access to Kubernetes resources based on user roles.
  - **Role**: Defines permissions within a specific namespace.
  - **ClusterRole**: Defines permissions at the cluster level.
  - **RoleBinding**: Grants the permissions defined in a role to a user or set of users.
  - **ClusterRoleBinding**: Grants the permissions defined in a ClusterRole across the entire cluster.
  - Example:
    ```yaml
    kind: Role
    apiVersion: rbac.authorization.k8s.io/v1
    metadata:
      namespace: default
      name: pod-reader
    rules:
    - apiGroups: [""]
      resources: ["pods"]
      verbs: ["get", "list"]
    ```

---

## 8. Load Balancing in Kubernetes

- **Kubernetes Load Balancing**:
  - Kubernetes itself doesn’t provide a load balancing feature for traffic outside the cluster. You typically rely on **Ingress controllers** or an external load balancer (like AWS ELB, Google Cloud Load Balancer).
  - Inside the cluster, Kubernetes supports **Service Types** like `ClusterIP`, `NodePort`, and `LoadBalancer`, which handle internal load balancing across pods.

---

## 9. Storage in Kubernetes

- **Volumes**: Kubernetes abstracts storage into volumes, which can be mounted into containers.
  - Types of Volumes:
    - **emptyDir**: Temporary storage for the lifetime of the pod.
    - **PersistentVolume (PV)**: A piece of storage in the cluster that has been provisioned by an administrator.
    - **PersistentVolumeClaim (PVC)**: A request for storage by a user, bound to a PV.
  
- **StatefulSets**: Used for applications that require stable, unique network identifiers, persistent storage, and ordered deployment/scale-up.

---

## 10. Namespaces

- **Namespaces** provide a way to partition resources within a Kubernetes cluster.
  - Use namespaces to isolate different environments (e.g., development, staging, production) or teams.

---

## 11. Helm

- **Helm** is a package manager for Kubernetes that helps you define, install, and upgrade applications running in Kubernetes.
  - **Charts**: Helm packages for Kubernetes resources.
  - Helps to streamline deployments and manage configurations in production.

---

## 12. CI/CD with Kubernetes

- Kubernetes plays a crucial role in continuous deployment, automating the deployment of new code into production.
  - Integration with tools like **Jenkins**, **GitLab CI**, and **CircleCI**.
  - Use Kubernetes **Deployment** objects for automated rolling updates.

---

## 13. Monitoring and Logging

- **Prometheus** and **Grafana**: Prometheus for monitoring and Grafana for visualization.
- **ELK Stack (Elasticsearch, Logstash, Kibana)**: For centralized logging and troubleshooting.
- **Fluentd**: Log forwarding and aggregation.

---

## 14. Kubernetes Security Best Practices

- Use **RBAC** to control access.
- Ensure secrets are managed securely (e.g., using Kubernetes Secrets or an external secrets manager).
- Use **Network Policies** to define the communication rules between pods.
- Enable **PodSecurityPolicies** to enforce security standards on pod deployments.
- Always use **ReadOnlyRootFilesystem** and avoid running privileged containers.
