# Kubernetes Hands-On 03.11.2017 Frankfurt cc

## Fragen

* Kubernetes wieder weg von DevOps


## Links

2 day workshop: https://github.com/kelseyhightower/kubernetes-the-hard-way

https://reactdom.com/blog/kubernetes-books

Ansible

http://blog.kubernetes.io/2017/05/kubespray-ansible-collaborative-kubernetes-ops.html

https://github.com/kubernetes-incubator/kubespray

## Kubernetes 101

* possible: 
** multi-cloud (Azure + AWS + x)
** hybrid (On-premise + Cloud)

Kubernetes: no guarantee, how long Containers are available (no WebSphere!)

Master-Node - always API server


### Pods

Pod - for local communication between Containers (named-pipe, Unix-socket)
--> no complete Webstack in one Pod
--> Sidecar:
** (Service mesh to communicate with Service on all nodes with same port)
** Eureka-connectivity for Ruby app


### Replication Controller / Deployment

Scheduler-Container also contains Replication Controller

Replication Controller = how many replicas should my service have

Deployment = Pod + Replication Controller


### Kubernetes manifests (Yaml)

```
spec
	resources
		requests:
			--> Bursts for short time is possible
			cpu: 100m (100 milli CPUs = 0,1 CPUs)
			memory: 100Mi (1:1 MB RAM) --> set appropriate Heap to Java-App! Attention - JVM has it´s own Memory-Management!!! Apps see not the 100MB Kubernetes limits, but the host complete RAM (Ansible/Helm-Chart has to set both parameters together)
			...
```


### Services

endpoint for Pods (--> Incoming requests) or external Endpoint (<-- Outgoing requests) - 2nd with mocking in staging

ENV or DNS


| Service |

   |  |
   |  |

|Pod| |Pod|

Routing Tables from ext IP to Pod-IPs or Routing Mesh


### Kubernetes Labels

mighty!

Kubernetes Labels --> mapped onto Docker label

```
  template:
    metadata:
      labels:
        app: confy
        role: database
```

--> for blue green deployments

### Hands-On Prerequisites

http://blog.nicolas-byl.eu/2017/03/13/javaland-countdown.html

Install kubectl - brew install kubectl
Install helm - brew install kubernetes-helm
Install deis - brew install deis

kubectl - nice wrapper around Kubernetes REST API

https://github.com/kubernetes/kops

Login to minikube VirtualBox: user: `docker` ; password: `tcuser` 

`minikube dashboard`  --> open dashboard

`kubectl apply -f lab1-kubectl/postgres-deployment.yml` --> app deployment (inherits create)


`kubectl get` --> show, what´s possible

`kubectl get po` --> show running Pods, per namespace


`kubectl get pods --all-namespaces -o wide` show every pod in all namespaces, don´t touch kube-system, if managed!

`kubectl get pods --all-namespaces -o yaml`

Get Services `kubectl get svc`


Standard inspect of deployed Pods

`kubectl describe pods confy-frontend-7685bd7747-6lwql`


#### Scaling

###### manual

`kubectl scale deployment confy-frontend --replicas=3`

`kubectl get po -w`

#### Access Pods from outside world

Container-Ports

```
spec:
  type: LoadBalancer
```

Node-Ports:

```
spec:
  type: NodePort
```

Get Kubernetes IP: `minikube ip` or Dashboard

`kubectl get svc` --> get Ports of apps

Call `http://192.168.99.100:32001/#/speakers/list` in Browser



## Helm

https://github.com/kubernetes/helm

Kubernetes Package Manager (like brew, chocolatey for OSses)

Repositories for Kubernetes Artifacts, incl. history / versioning

Tiller (Server)
Helm (Client CLI)

YAML-Templates

`helm init`

#### Lab 2

`helm install --name confy-queue --set rabbitmqUsername=confy,rabbitmqPassword=confy01 stable/rabbitmq`

stable means stable things in Kubernetes, incubator is more bleeding


Helm automatically makes all for you (Secrets, Claims for Volumes, Service, Deployment)

Secrets for password encryption, provided as environment variable:

```
==> v1/Secret
NAME                       TYPE    DATA  AGE
confy-database-postgresql  Opaque  1     1s
```

Volumes via Claims

```
==> v1/PersistentVolumeClaim
NAME                       STATUS  VOLUME                                    CAPACITY  ACCESS MODES  STORAGECLASS  AGE
confy-database-postgresql  Bound   pvc-fdeaab18-c092-11e7-913c-08002745cf84  8Gi       RWO           standard      1s
```

a Claims abstracts the concrete storage away - and moves it along with the Pods

Access-Modes - only (RW Only) for relational!, many (RW Many)




## Delete old apps

#### Deployments delete

`kubectl get deploy`

`kubectl delete deploy confy-frontend confy-database`

#### delete Replica sets / Pods

`kubectl get rs`

`kubectl get po`

--> automatically by delete deploy

#### Delete Services

`kubectl delete svc confy-database confy-frontend` 


## Logs

`kubectl logs PODName`

## Watching stuff

`brew install watch`

`watch kubectl get po -a`


## Databases

#### Classical Relational DBs

--> Leave it OUTSIDE of Kubernetes!


