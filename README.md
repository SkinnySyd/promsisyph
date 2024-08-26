# Kind Kubernetes

### Install Kind
Doc: https://kind.sigs.k8s.io/docs/user/quick-start

kind is a tool for running local Kubernetes clusters using Docker container “nodes”.

```sh
# For AMD64 / x86_64
[ $(uname -m) = x86_64 ] && curl -Lo ./kind https://kind.sigs.k8s.io/dl/v0.23.0/kind-linux-amd64

chmod +x ./kind
sudo mv ./kind /usr/local/bin/kind
```

### Install Kubectl
Doc: https://kubernetes.io/docs/tasks/tools/install-kubectl-linux/

- Download the latest release with the command:

```sh
curl -LO "https://dl.k8s.io/release/$(curl -L -s https://dl.k8s.io/release/stable.txt)/bin/linux/amd64/kubectl"
```

- Install kubectl
```sh
sudo install -o root -g root -m 0755 kubectl /usr/local/bin/kubectl

chmod +x kubectl
mkdir -p ~/.local/bin
mv ./kubectl ~/.local/bin/kubectl
```

- Test to ensure the version you installed is up-to-date:
```sh
kubectl version --client
```

### Create a Cluster 

```sh
# First we will create a cluster using:
kind create cluster

# Check cluster 
kind get clusters

# Get cluster info
kubectl cluster-info

# Check Nodes
kubectl get nodes

# Check All
kubectl get all -A
```


# Monitoring on Kubernetes 

- Task 1: Introduction 
By the end of Task 1, you will be able to create a single node kubernetes cluster using kind which is a tool for running local Kubernetes clusters using Docker containers as nodes on Ubuntu OS cloud workspace. 

- Task 2: Hands-on Kubernetes Deployment
By the end of Task 2, you will be able to create a Kubernetes deployment and service. 

- Task 3: Deploying Kubernetes Dashboard (Web-UI)
By the end of Task 3, you will be able to deploy and have an overview of a Kubernetes Dashboard (Web-UI) along with deploying a second deployment using dashboard.

- Task 4: Scraping Metrics using Prometheus
By the end of Task 4, you will be able to utilize an open-source monitoring solution Prometheus to scrape the key system metrics of our cluster.

- Task 5: Visualizing Metrics on Grafana Dashboards
By the end of Task 5, you will be able to use Grafana, an open-source analytics and interactive visualization web application to visualize the scraped metrics on community built custom Grafana dashboards.


## Hands-On Kubernetes Deployment
Creating Deployments

```sh
cd Monitoring-Kubernetes-Cluster/Deployment/

# Analyze YAML manifest:
cat deployment.yml

# Deploy the manifest:
kubectl apply -f deployment.yml

# Check deployment:
kubectl get deploy

# Check pods:
kubectl get pods

# Create service:
kubectl apply -f service.yml

# Check service:
kubectl get svc
```


## Deploying Kubernetes Dashboard (Web UI)

Dashboard is a web-based Kubernetes user interface. We can use Dashboard to get an overview of applications running on our cluster, as well as for creating or modifying individual Kubernetes resources.Dashboard also provides information on the state of Kubernetes resources in your cluster and on any errors that may have occurred.

### Configuration Steps:
1. Deploy the Kubernetes Dashboard UI:

```sh
kubectl apply -f https://raw.githubusercontent.com/kubernetes/dashboard/v2.7.0/aio/deploy/recommended.yaml

kubectl -n kubernetes-dashboard get pods
```

```sh
kubectl -n kubernetes-dashboard proxy

Visit http://localhost:8001/api/v1/namespaces/kubernetes-dashboard/services/https:kubernetes-dashboard:/proxy/
```

```sh
kubectl -n kubernetes-dashboard apply -f admin-role.yml
kubectl -n kubernetes-dashboard get secret admin-user-secret -o jsonpath="{.data.token}" | base64 -d
```


## Metric Server 
Cluster-wide aggregator of resource usage data.

## Metrics Server Installation Steps:

1. To install the Metric-Server, first download this components.yaml file locally using:
   
   `wget https://github.com/kubernetes-sigs/metrics-server/releases/latest/download/components.yaml`

2. Use any editor of your choice to open and modify the components.yaml file:

    `sudo nano components.yaml`

3. Modify the file in the vim editor (or your choice) and add this under deployment --> containers --> args:

    `--kubelet-insecure-tls`

#### Reference:
https://github.com/kubernetes-incubator/metrics-server


## Prometheus, kube-state-metrics, Metrics Server, Grafana Example


```sh
kubectl create namespace monitoring

kubectl create -f ./ -R

`kubectl get nodes --selector=kubernetes.io/role!=master -o jsonpath={.items[*].status.addresses[?\(@.type==\"InternalIP\"\)].address}`
   
Visit http://[NODEIP]:30000 to view Prometheus.

Visit http://[NODEIP]:30000 to view Grafana.

```

https://grafana.com/grafana/dashboards/


<hr>

### Grafana Dashboard

1. Visit http://[NODEIP]:32000 to view the Grafana dashboard.
2. Login with `admin` and `admin` for the username and password. You can reset the password on the next screen or click `Skip`.

#### Dashboards 

`Cluster Monitoring for Kubernetes` ->  `10000`

`Kubernetes Deployment Statefulset Daemonset metrics` -> `8588`

`Container Statistics` -> `10694`
