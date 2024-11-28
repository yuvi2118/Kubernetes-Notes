# Kubernetes-Notes


## 1. Kubernetes vs Docker

### Docker:
- A platform for developing, shipping, and running applications inside containers.
- Containers are lightweight, portable, and provide consistency across development, testing, and production environments.

### Kubernetes:
- A container orchestration platform that automates the deployment, scaling, and operation of containerized applications.
- While Docker manages individual containers, Kubernetes manages clusters of containers, providing features like high availability, scaling, and resource management.

### Key Difference:
- Docker is used for containerizing applications, while Kubernetes manages and orchestrates containers at scale.

---

## 2. Kubernetes Architecture

Kubernetes is made up of several key components that work together to maintain the cluster.

### Master Node: The control plane responsible for managing the cluster.
- **API Server**: The entry point for all REST commands used to control the cluster.
- **Controller Manager**: Responsible for maintaining the desired state of the cluster, such as replication and scaling.
- **Scheduler**: Decides which node will run a pod based on resource availability.
- **etcd**: A distributed key-value store for storing all cluster data, such as configurations and state.

### Worker Nodes: Nodes that run the containerized applications.
- **Kubelet**: An agent that ensures containers are running on the node as expected.
- **Kube Proxy**: Maintains network rules for pod communication and load balancing.
- **Container Runtime**: Software (like Docker, containerd, etc.) used to run containers.

---

## 3. Pods

- A **Pod** is the smallest deployable unit in Kubernetes, and it contains one or more containers (typically one).
- Pods share the same network, storage, and lifecycle.
- **Use Case**: A pod can be used to group containers that need to share resources, like a front-end and a back-end service in a single container group.

---

## 4. Development with Kubernetes

- **kubectl**: The command-line interface to interact with Kubernetes clusters.
  - Common commands:
    - `kubectl get pods`: List pods.
    - `kubectl create -f <file>.yaml`: Create resources from a YAML file.
    - `kubectl apply -f <file>.yaml`: Update resources.

- **Kubernetes Manifests**: Describes the desired state of the system, including deployments, services, pods, and more, typically defined in YAML files.
  - Example:
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
          - name: my-app
            image: my-app:1.0
            ports:
            - containerPort: 8080
    ```

---

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
