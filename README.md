# Certified Kubernetes Application Developer (CKAD) Notes

## Table of Contents
- [Getting Started](#getting-started)
   - [Prerequisites](#prerequisites)
   - [Install Kubectl](#install-kubectl)
   - [Install Minikube](#install-minikube)
   - [Additional Resources](#additional-resources)
- [The Basics](#the-basics)
  - [Pods](#pods)
  - [Labels, Selectors and Annotations](#labels-selectors-and-annotations)
  - [Replica Sets](#replica-sets)
  - [Deployments](#deployments)
  - [Namespaces](#namespaces)
  - [CronJobs](#cronjobs)
  - [Multi-Container Pod Design Patterns](#multi-container-pod-design-patterns)
- [Mastering Config-Fu](#mastering-config-fu)
  - [Commands and Arguments](#commands-and-arguments)
  - [Environment Variables, ConfigMaps, and Secrets](#environment-variables-configmaps-and-secrets)
  - [Security Contexts](#security-contexts)
  - [Service Accounts](#service-accounts)
  - [Resource Requests and Limits](#resource-requests-and-limits)
  - [Taints and Tolerations](#taints-and-tolerations)
  - [Node Selectors and Affinity](#node-selectors-and-affinity)
  - [Using Node Affinity with Taints and Tolerations](#using-node-affinity-with-taints-and-tolerations)
- [Observability](#observability)
  - [Status and Conditions](#status-and-conditions)
  - [Readiness Probes](#readiness-probes)
  - [Liveness Probes](#liveness-probes)
  - [Logging and Monitoring](#logging-and-monitoring)
- [Services and Networking](#services-and-networking)
  - [Services](#services)
  - [LoadBalancer Service](#loadbalancer-service)
  - [ClusterIP Service](#clusterip-service)
  - [NodePort Service](#nodeport-service)
  - [Ingress Controllers](#ingress-controllers)
  - [Ingress Resources](#ingress-resources)
  - [Network Policies](#network-policies)
- [State Persistence](#state-persistence)
  - [Volumes](#volumes)
  - [Persistent Volumes and Persistent Volume Claims](#persistent-volumes-and-persistent-volume-claims)
  - [Stateful Sets](#stateful-sets)
  - [Headless Services](#headless-services)

Welcome to the official unofficial tl;dr documentation for Kubernetes! These
are my notes from the Udemy course entitled:
[Kubernetes Certified Application Developer (CKAD) with Tests](https://www.udemy.com/share/1013BQAkMad15TTX4=/). 
This is a paid course, but almost all courses on Udemy go on sale for anywhere
between $9 - $13 USD. I wouldn't recommend paying more than that for a course
on Udemy, since they are constantly on sale. This guy does a great job of
explaining the material, and I couldn't recommend him enough! With that out of
the way, let's get started!

# Getting Started

## Prerequisites
* Be comfortable in [YAML](https://www.youtube.com/watch?time_continue=83&v=cdLNKUoMc6c&feature=emb_title) ✍ 
* Be comfortable with the concept of Docker containers and virtualization
  ([this is a great place to start](https://www.youtube.com/watch?v=TvnZTi_gaNc))

Welcome to a **No Nonsense** guide to Kubernetes. I promise to do my best to
introduce material in a logical manner, provide production-centric examples,
pepper in memes and gifs to keep things feeling fresh, and explain 
complicated material as simply as possible. If you don't meet both of these 
prereq's, give this videos a watch, do your own research, and come back later!


## Install Kubectl

> Kubectl is the command line tool used to run commands against your Kubernetes cluster.

You must use a kubectl version that is within one minor version difference of
your cluster. For example, a v1.2 client should work with v1.1, v1.2, and v1.3
master. Using the latest version of kubectl helps avoid unforeseen issues.


_[Add Windows or MacOS instructions with a pull request!](https://github.com/strickolas/tldr/pulls)_

<details open>
<summary>
<a class="btnfire small stroke"><em class="fas fa-chevron-circle-down"></em>&nbsp;&nbsp;Installation on Linux</a>    
</summary>

1. Install kubectl
    ```bash
    curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
    ```
    To download a specific version, replace the `$(curl -s
    https://storage.googleapis.com/kubernetes-release/release/stable.txt)` portion of
    the command with the specific version number.

1. Make the kubectl binary executable and move it into your path
    ```bash
    chmod +x ./kubectl && sudo mv ./kubectl /usr/local/bin/kubectl
    ```

1. Test your install:
    ```bash
    kubectl version --client
    ```

</details>

[Back to top](#quick-links)


----


## Install Minikube
> Minikube is a lightweight Kubernetes implementation that creates a VM on your
> local machine and deploys a simple cluster containing only one node.

_[Add Windows or MacOS instructions with a pull request!](https://github.com/strickolas/tldr/pulls)_

<details open>
<summary>
<a class="btnfire small stroke"><em class="fas fa-chevron-circle-down"></em>&nbsp;&nbsp;Installation on Linux</a>   
</summary> 

1. Install either [KVM](https://www.linux-kvm.org/page/Main_Page) (which also
   uses QEMU) or [Virtualbox](https://www.virtualbox.org/wiki/Downloads).

    * If you are a **RHEL/CentOS/Fedora user installing VirtualBox**, you might
      encounter:
      ```
      This system is currently not set up to build kernel modules.
      Please install the gcc make perl packages from your distribution.
      Please install the Linux kernel "header" files matching the current kernel
      for adding new hardware support to the system.
      The distribution packages containing the headers are probably:
      kernel-devel kernel-devel-5.3.7-301.fc31.x86_64

      There were problems setting up VirtualBox.  To re-start the set-up process, run
      /sbin/vboxconfig
      as root.  If your system is using EFI Secure Boot you may need to sign the
      kernel modules (vboxdrv, vboxnetflt, vboxnetadp, vboxpci) before you can load
      them. Please see your Linux system's documentation for more information.
      ```

      This may cause some extra complication, but I've found that the simplest
      solution is to just disable secure boot in your bios.

    * If you don't want to use a VM, use the `--driver=none` flag to run Kubernetes
        components directly on the host. Using this driver requires
        [Docker](https://www.docker.com/products/docker-desktop) and a Linux
        environment but not a hypervisor. Do not install Docker using snap, as it
        will more than likely be outdated.
        * > **Caution**: The `none` VM driver can result in security and data loss
        issues. Before using --driver=none, consult 
        [this documentation](https://minikube.sigs.k8s.io/docs/drivers/none/) 
        for more information.
        
1. Now, install Minikube via:
    ```bash
    curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
    && chmod +x minikube
    ```

1. Add minikube to your path:
    ```bash
    sudo install minikube /usr/local/bin/ && rm ./minikube
    ```

1. Verify install replacing the `driver_name` with the hypervisor of your choice
    from [this list](https://kubernetes.io/docs/setup/learning-environment/minikube/#specifying-the-vm-driver).
    Run the following:
    
    1. ```bash
        minikube start --driver=<driver_name>
        ```

    1. Once `minikube start` finishes, run:
        ```bash
        minikube status
        
        # Your output should look something like this:
        #> host: Running
        #> kubelet: Running
        #> apiserver: Running
        #> kubeconfig: Configured
        ```
        
    1. If something doesn't seem right, use the following to increase your 
        logging level:
        ```bash
        minikube -v=9 start
        ```

    1. When all else fails, you can always blow away your minikube environment using:
        ```bash
        minikube delete
        ``` 
    
    1. Stop minikube with:
        ```bash
        minikube stop
        ```
</details>

[Back to top](#quick-links)


---

## Additional Resources
* [Visit the Kubectl Wiki](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)
* [Read the Kubectl Book](https://kubectl.docs.kubernetes.io/)
* [Visit the Minikube Wiki](https://minikube.sigs.k8s.io/docs/)
# The Basics

![basicBasicBasic](https://media.giphy.com/media/xUOwG5IshvzNMdzR72/giphy.gif)

## Pods

There are **four** top level properties to a Kubernetes config yaml:
1. **apiVersion**: Each apiVersion supports different Kubernetes objects. For
   an exhaustive list, see Kubernetes'
   [docs](https://kubernetes.io/docs/reference/generated/kubernetes-api/v1.11/),
   but don't let it bog you down. See our `/cheatsheets/` file.
1. **kind**: The type of Kubernetes object.
1. **metadata**: Data that describes the K8s objects. 
1. **spec**: The _desired state_ of the K8s object. Kubernetes will work to make
  sure that the current state of the objects matches the desired state; in other
  words, that the **spec** matches the **status** of the object.

```yaml
# 1. Version of K8s API to use.
apiVersion: v1

# 2. Kind of object being created.
kind: Pod

# 3. Describes K8s the object; "name" and "labels" are a few of many valid keys.
metadata:
   name: myapp-pod
   labels:
      app: myapp
      type: front-end
      nonsensical-bologna: boop 
      # You can put any key/value pairs you want in labels.
      # While "app" and "type" seem like they may be important labels, they are
      # something that we will use to organize our objects. 
      
# 4. The desired state of the object which K8s will work to maintain.
spec:
   containers:
      - name: nginx-container
        image: nginx
```

The `kind: Pod` specifies that we are creating a **Pod**, which is a collection
of container(s) that share resources, have a single IP, and can share volumes.
Pods run on worker nodes, and are the smallest unit that Kubernetes can
understand. You will rarely manage pods yourself, and instead, will rely on one
of Kubernetes' control structures to manage them. **Pods run on worker nodes.**

[Back to top](#table-of-contents)


---


## Labels, Selectors and Annotations

```yaml
apiVersion: v1
kind: Pod
metadata:
   name: simple-webapp

   # 1
   labels:
      app: App1
      function: front-end

   # 2
   annotations:
      buildVersion: 1.2.3
      forAssistance:
         call: "555-555-5555"
         email: "jdoe@corp.com"
         allowsTexts: True
      ownedBy: 
         name: "Alex Yu"
         dept: "Psychotronics"
spec:
   containers:
      - name: nginx
        image: nginx
```

1. `labels` are used to help filter K8s objects in conjunction with a 
   **selector**. Labels are visible to Kubernetes.
   ```yaml
   kubectl get pods --selector app=App1
   # or
   kubectl get pods --selector function=front-end
   ```
1. `annotations`, on the other hand, are used to communicate information with
   other engineers. In our annotations section, we list a person to contact in
   case an engineer needs assistance, and who owns the application. This
   information is not visible to Kubernetes. 

[Back to top](#table-of-contents)


---


## Replica Sets

A **Replica Set** will make sure that a certain number of objects exist which
match a particular query. A ReplicaSet has 3 very important fields used to
define it's functionality. 
1. **template**: the Pod to be created. Does the data in `template` look 
   familiar? It should! It is copied directly from the above pod definition.
   **NOTE**: We don't specify an `apiVersion` or `kind` in template. This is
   because we always assume we are using `apiVersion: v1`, and `kind: Pod`.
1. **selector**: provides us with a tool to query for pods that we care about.
1. **replicas**: specifies the number of pods we want to exist at a given time.

```yaml
# Remember, every K8s object has apiVersion, kind, metadata, and a spec.
apiVersion: apps/v1
kind: ReplicaSet
metadata:
  name: myapp-repplicaset
  labels:
    app: myapp
    type: front-end
    guess_what: chicken_butt
spec:

  # 1. Provides a template of a pod we wish to create when needed.
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
        boop: blarp
    spec:
      containers:
        - name: nginx-container
          image: nginx

  # 2. This ReplicaSet will watch for all pods that have metadata.type: front-end
  selector: 
    matchLabels:
      type: front-end

  # 3. Watch for 3 objects matching the above selector. 
  replicas: 3
```

We can control ReplicaSets from the command line using the following commands.
1. We can create a ReplicaSet using our config with:
   ```bash
   kubectl create -f replicaset-def.yaml
   ```

1. We can list existing ReplicaSets using:
   ```bash
   kubectl get replicaset
   ```

1. We can describe a ReplicaSet using:
   ```bash
   kubectl describe replicaset myapp-replicaset
   ```

1. We can update a ReplicaSet using:
   ```bash
   kubectl replace -f replicaset-def.yaml
   ```

1. We can delete a ReplicaSet using:
   ```bash
   kubectl delete replicaset myapp-replicaset
   ```
   **BEWARE!** Deleting a ReplicaSet also deletes all underlying pods.

Let's say we end up needing more replicas than we currently have. We can scale
our ReplicaSet in one of 3 ways.

1. This is the preferred way if you want to update the yaml file. Simple change
   the number of replicas in the config from 3 to 6... or however many you want.
   ```bash
   kubectl replace -f replicaset-def.yaml
   ```

1. This is the preferred way if you don't want to change the yaml file, but
   want to make changes to the deployed object.
   ```bash
   kubectl scale --replicas=6 -f replicaset-def.yaml
   ```

1. This is the preferred way if you don't want to specify the file. Note that
   this requires the object type, followed by the object name.
   ```bash
   kubectl scale --replicas=6 replicaset myapp-replicaset
   ```

> **Pro Tip**: You can use `replicaset`, `rs`, and `replicasets` 
  interchangeably in the command line.

> **NOTE**: You may see **ReplicationController** being used, which is a K8s object that is
currently being phased out. The only difference is, a ReplicaSet has a selector
which gives the ability to manage pods that have been created before the
ReplicaSet was.

[Back to top](#table-of-contents)


---


## Deployments

ReplicaSets solve the problem of having enough Pods running to provide high
availability of our application; but it doesn't make it particularly easy to
upgrade over time. This is where **Deployments** come in. Deployments will
make sure that upgrades happen gradually (also known as a rolling upgrade, aka **rollout**).
Upgrading Pods gradually helps to ensure that users do not notice any downtime.
There is another update strategy known as `Recreate`, which you can find more
information about [here](https://kubernetes.io/docs/concepts/workloads/controllers/deployment/#recreate-deployment),
but it's rare that you will use this in production.
Deployments also make it easy to rollback to a previous version of software
if need be. The config for a Deployment is nearly identical to a ReplicaSet's
config, except we update the `kind` to `Deployment`.

```yaml
apiVersion: apps/v1
kind: Deployment
metadata:
  name: myapp-deployment
  labels:
    app: myapp
    type: front-end
    super: mario
spec:
  template:
    metadata:
      name: myapp-pod
      labels:
        app: myapp
        type: front-end
        boop: blarp
    spec:
      containers:
        - name: nginx-container
          image: nginx
  selector: 
    matchLabels:
      type: front-end
  replicas: 3
  ```

We have the following commands at our disposal to manipulate the Deployment:
```bash
# Create a Deployment from a yaml file.
kubectl create -f deployment-def.yaml

# Get a list of deployments.
kubectl get deployments

# Apply changes made to the original Deployment yaml file.
kubectl apply -f deployment-def.yaml

# Change the image used by an existing Deployment.
kubectl set image deployment/mydeployment-name nginx=nginx:1.9.1

# Get status of a rollout for an existing Deployment.
kubectl rollout status deployment/mydeployment-name

# View the rollout history for an existing Deployment
kubectl rollout history deployment/mydeployment-name

# Undo the latest rollout applied to a Deployment.
kubectl rollout undo deployment/mydeployment-name
```

* The Deployment automatically creates a ReplicaSet:
   ```bash
   kubectl get replicaset
   #> NAME                       DESIRED    CURRENT    READY    AGE
   #> myapp-deployment-abc123    3          3          3        2m
   ```

* The ReplicaSet created by the Deployment will subsequently create Pods:
   ```bash
   kubectl get pods
   #> NAME                             READY STATUS   RESTARTS  AGE
   #> myapp-deployment-abc123-xyz001   1/1   Running  0         2m
   #> myapp-deployment-abc123-xyz002   1/1   Running  0         2m
   #> myapp-deployment-abc123-xyz003   1/1   Running  0         2m
   ```

* Now that we seem to be working with a bunch of different K8s objects, we can
   utilize the following to make our lives a bit easier.
   ```bash
   kubectl get all
   ```

* You can format the output from `kubectl` using one of the following:
   * `-o json`: Output a JSON formatted API object.
   * `-o name`: Print only the resource name and nothing else.
   * `-o wide`: Output in the plain-text format with any additional information.
   * `-o yaml`: Output a YAML formatted API object.`

> **Pro Tip**: You can use the flag `--dry-run=client` when running a `kubectl`
  command and it will inform you whether the syntax of the command and config
  are correct, as well as if the resource is able to be created.

[Back to top](#table-of-contents)


---


## Namespaces

**Namespaces** are a way to organize K8s objects while enforcing policy,
increasing security, and preventing accidental manipulation of vital objects.
Kubernetes creates 3 namespaces when the cluster is created:
1. **Default** - this is where all Kubernetes objects are placed by default
1. **kube-system** - where K8s places all Pods and Services used by K8s to run,
   such as those required by the networking solution, the DNS service, etc.
1. **kube-public** - where K8s objects that should be available to all users
   are placed.

You can create your own Namespaces, for example, a namespace for `prod`
and another for `dev`. This way, while working in the development
environment, you don't accidentally interact with a K8s object that belongs
to the production namespace.

We can access K8s objects outside of the current namespace by referencing its 
fully qualified domain name as configured by Kubernetes. For example:
```python
# Access a service from within the namespace.
mysql.connect("db-service")                       

# Access a service from outside of namespace using that resource's full name.
mysql.connect("db-service.dev.svc.cluster.local")
# Object named db-service,
#      from the namespace dev,
#        a K8s object of type svc 
#             with the domain name cluster.local


# Playing with mnemonics to remember this for the CKAD exam:
# name.space.type.domain
# Nina Simone is a Taurus from Detroit
```

You can run any `kubectl` command against a namespace of your chosing by
specifying the namespace:
```bash
kubectl create -f pod-def.yaml --namespace=dev
```

We can also declare the namespace that a Kubernetes object should be placed in
via a metadata tag:
```yaml
apiVersion: v1
kind: Pod

metadata:
   name: myapp-pod
   namespace: dev
   labels:
      app: myapp
      type: front-end
      asdf: abc123
spec:
   containers:
      -name: nginx-container
       image: nginx
```

We can define a custom Namespace in one of two ways:

1. Via yaml 
   ```yaml
   apiVersion: v1
   kind: Namespace
   metadata:
      name: dev
   ```
   followed by the command to `kubectl`:
   ```
   kubectl create -f namespace-dev.yaml
   ```

2. ```
   kubectl create namespace dev
   ```

> **Pro Tip**: You can permanently change your default namespace with the following
 `kubectl config set-context $kubectl config current-context) --namespace=dev`

> **Pro Tip**: We can list all Pods, Deployments, ReplicaSets, etc, across all
  Namespaces via `kubectl get pods --all-namespaces`

[Back to top](#table-of-contents)


---


## Jobs

You may require a batch process to run on your K8s cluster, such as performing
a calculation. For example:
```yaml
apiVersion: v1
kind: Pod
metadata: 
   name: math-pod
spec:
   containers:
   -  name: math-add
      image: ubuntu
      command: ['expr', '3', '+', '2']
   restartPolicy: OnFailure
```
Take note of `restartPolicy` in the `spec` section. This is a policy that tells
Kubernetes what to do with the Pod when it dies. There are three possible 
values for `restartPolicy`:
   * `Always`: the default for Pod objects, meaning that a Pod will restart 
     forever (in theory);
   * `OnFailure`: causes the Pod to restart when it fails;
   * `Never`: if the Pod doesn't run the first time, don't try again.

This is a great option for one-off batches, but it fails to take advantage of
the scalability of Kubernetes. This is where **Jobs** come into play:
```yaml
apiVersion: batch/v1
kind: Job
metadata:
   name: math-add-job
spec:

   # 1. The template of the Pod to create.
   template:
      spec:
         containers:
         -  name: math-add
            image: ubuntu
            command: ['expr', '3', '+', '2']
         restartPolicy: OnFailure

   # 2. The number of Pod Completions (no failures or terminations) required.
   completions: 3

   # 3. The number of Pods to be running at once.
   parallelism: 3
```

1. Does `spec.template` look familiar? That's because it's a identical to the
   spec of the Pod we created above.
1. Remember the different `restartPolicy` values you might encounter? `Always`,
   `OnFailure`, and `Never`? Well, using `completions`, we can tell Kubernetes
   to make sure a certain number of Jobs run to completion.
1. Using the `parallelism` field, we tell Kubernetes how many Pods to have
   running at any given time.

We can use the following commands to manipulate Jobs:
```bash
# 1. Create the Job.
kubectl create -f job-def.yaml

# 2. View a list of running Jobs.
kubectl get jobs

# 3. View the output of the running job (if you printed it to stdout)
kubectl logs math-add-job-<your-pod-number>

# 4. Delete a running Job, and all the Pods it created.
kubectl delete job math-add-job
```

[Back to top](#table-of-contents)


---

## CronJobs

Now that you know how to run Jobs, let's learn schedule them to run 
periodically using [cron](https://en.wikipedia.org/wiki/Cron):
```yaml
apiVersion: batch/v1beta1
kind: CronJob
metadata:
   name: reporting-cron-job
spec:
   # 1. Cron schedule string
   schedule: "*/1 * * * *"

   # 2. Describe the job
   jobTemplate:
      spec:
         template:
            spec:
               containers:
               -  name: reporting-tool
                  image: reporting-tool
               restartPolicy: OnFailure
         completions: 3
         parallelism: 3
```
1. `schedule` takes the cron schedule string which you can build using the
   guide below, coupled with [this tool](https://crontab.guru/#*/1_*_*_*_*).
   This particular CronJob runs every minute.
   ```
   # ┌───────────── minute (0 - 59)
   # │ ┌───────────── hour (0 - 23)
   # │ │ ┌───────────── day of the month (1 - 31)
   # │ │ │ ┌───────────── month (1 - 12)
   # │ │ │ │ ┌───────────── day of the week (0 - 6) (Sunday to Saturday;
   # │ │ │ │ │                                   7 is also Sunday on some systems)
   # │ │ │ │ │
   # │ │ │ │ │
   # * * * * * <command to execute>
   ```

2. We describe the Job we are going to run, which is very reminiscent of the
   `spec` section of the Job yaml we wrote above.

[Back to top](#table-of-contents)


---


## Multi-Container Pod Design Patterns
So far, we have been working with a singleton Pods which encapsulate a single
container. Now, we will be working with Multi-Container Pods, which is
something you are more likely to encounter in the wild.

There are three common Multi-Container Pod patterns we will be discussing:

1. **The Sidecar Pattern**: deploying a container alongside the application
   to handle some minor task. For example, we may deploy a log-agent alongside
   a web server to collect logs and forward them to a central log server.
![sidecar_pattern.png](./.assets/sidecar_pattern.png)

   What if we have a central logging server, and a bunch of different
   applications logging to a central logging server using different logging
   formats? This is where the next pattern comes in...


1. **The Adapter Pattern**: deploy an additional container to allow services
   to cooperate that otherwise wouldn't be able to. This allows you to simply
   deploy a different sidecar container to _adapt_ to new situations as they 
   arise (e.g. change of backend). 

   For example, you may have three applications with logging agents that
   generate logs in completely different formats. An adapter container is
   deployed alongside those logging-agents to normalize the log data before
   sending it off to the central logging server.

   ![adapter_pattern.png](./.assets/adapter_pattern.png)

1. **The Ambassador Pattern**: an "ambassador container" is deployed, which
   essentially acts like a proxy that allows other containers to connect
   to a port on localhost while the ambassador container proxies the connection
   to the appropriate server. For example, say you have different logging 
   servers for development, testing, and production deployments. Using an
   ambassador, we can send all of our logging data to a given port over
   localhost, which wouldn't require changing the source code of the 
   application.

   ![ambassador_pattern.png](./.assets/ambassador_pattern.png)
  

[Back to top](#table-of-contents)


---



# Mastering Config-Fu

![configfu](https://media.giphy.com/media/3ov9k6pbxOR7X144h2/giphy.gif)

## Commands and Arguments
Let's say we have a Dockerfile that looks like this:
```Dockerfile
FROM Ubuntu

# the command that is run at startup. 
ENTRYPOINT ["sleep"]

# the arguments passed to the above command. 
CMD ["5"]
```

What if we wanted to change this behavior when we spin up the container in a
Pod? This is where the following come in:

1. **command**: overrides the ENTRYPOINT of the Dockerfile we are creating in a
   Pod.

1. **args**: overrides the CMD of the Dockerfile we are creating in a Pod.
```yaml
apiVersion: v1
kind: Pod
metadata: 
   name: my-ubuntu-pod
spec:
   containers:
      - name: my-ubuntu-container
        image: my-ubuntu-container
        command: ["sleep"]
        args: ["10"]
```

[Back to top](#table-of-contents)


---


## Environment Variables, ConfigMaps, and Secrets

We can pass **environment variables** into our container as key-value pairs
like so:
```yaml
apiVersion: v1
kind: Pod
metadata:
   name: simple-webapp-color
spec:
   containers:
      - name: simple-webapp-color
        image: simple-webapp-color

        # We pass environment values 
        env:
           - name: APP_COLOR
             value: pink
           - name: DO_NOT_DELETE
             value: /usr/bin
```

A **ConfigMap** is an abstraction that makes it easy to manage a lot of
environment variables at once. When we create a ConfigMap below, take note of
the fact that there is no `spec` key, but instead, a `data` key.
```yaml
apiVersion: v1
kind: ConfigMap
metadata: 
  name: app-config 
data: 
  APP_COLOR: blue
  APP_MODE: prod
  MY_NUMBER: 3.14
  FAV_PATH: "/usr/local/bin"
```

You can use the following with the `kubectl` tool:
```yaml
kubectl create -f configmap-def.yaml
kubectl get configmaps 
```

We can import the `data` from our ConfigMap like so:
```yaml
apiVersion: v1
kind: Pod
metadata:
   name: simple-webapp-color
   labels:
      name: simple-webapp-color
spec:
   containers:
      - name: simple-webapp-color
        image: simple-webapp-color
        
        # Use an existing ConfigMap named app-config
        envFrom:
           - configMapRef:
                name: app-config 
```

**Secrets** are very similar to a ConfigMap, except they are stored in an
encoded format. The values of `data.YOUR_KEY` must be encoded into base 64. To
learn how to do this, see the comments in the config below.
```yaml
apiVersion: v1
kind: Secret
metadata:
   name: app-secret
data:
   DB_Host: bXlzcWw=         # echo -n 'mysql' | base64
   DB_User: cm9vdA==         # echo -n 'root' | base64
   DB_Pass: cGFzc3dvcmQxMjM= # echo -n 'password123' | base64
```

You can decode a base64 string like so:
```bash
echo -n 'bXlzcWw=' | base64 --decode
```

We can view secrets in our cluster using:
```
kubectl get secrets
```

Now, let's give a container access to our Secret.
```yaml
apiVersion v1:
kind: Pod
metadata:
   name: simple-webapp-color
   labels:
      name:simple-webapp-color
spec:
   containers:
      -  name: simple-webapp-color
         image: simple-webapp-color

   # Name of the K8s object goes here.
   envFrom:
      - secretRef:
           name: app-secret
```

> **For production**: You may have noticed that base64 isn't actually doing
anything to keep our information safe, since it can be very easily decoded. 
Have a look at 
[this article](https://blog.aquasec.com/managing-kubernetes-secrets) 
to learn more about the shortcomings of Secrets, as well as how to use secrets
in a production environment. (tl;dr use
[AWS Secrets Manager](https://aws.amazon.com/secrets-manager/),
[Google Cloud Key Management Service](https://cloud.google.com/kms/), or
[Azure Key Vault](https://azure.microsoft.com/en-in/services/key-vault/)).

[Back to top](#table-of-contents)


---


## Security Contexts
Docker implements a set of security features which limits the abilities of the 
container's root user. This means that, by default, the root user within a 
container has a lot of Linux capabilities disabled, such as `CHOWN`, `DAC`,
`KILL`, `SETFCAP`, `SETPCAP`, `SETGID`, `SETUID`, `NETBIND`, `NET_RAW`,
`MAC_ADMIN`, `BROADCAST`, `NET_ADMIN`, `SYS_ADMIN`, and `SYS_CHROOT` to name a 
few. To view a full list of linux capabilities, run the following:
```bash
cat /usr/include/linux/capability.h
```

If you want to make changes to the capabilities a Docker container has, you can 
run one of the following:
```bash
docker run --cap-add MAC_ADMIN ubuntu  # adds a capability to a container
docker run --cap-drop KILL ubuntu      # removes a capability from a container
docker run --privileged ubuntu         # adds all capabilities to a container.
```
In Kubernetes, defining a set of enabled and disabled Linux capabilities is
called a **Security Context**. We can apply a security contexts like so:
``` yaml
apiVersion: v1
kind: Pod
metadata:
   name: mypod
spec:

   # 1
   securityContext:
      runAsUser: 1000
      runAsGroup: 3000
      fsGroup: 2000
      capabilities:
         add: ["MAC_ADMIN"]

   containers:
      - name: ubuntu
        image: ubuntu
        command: ["sleep", "3600"]
        
        # 2
        securityContext:
            capabilities:
                add: ["NET_ADMIN"]



      - name: some-other-thing 
        image: some-other-thing
        command: ["sleep", "3600"]

        # 3
        securityContext:
            allowPrivilegeEscalation: false
```
Take note of the following:
1. We are applying the following to all containers in the pod.
   * **runAsUser**: specifies that for any containers in the Pod, all processes
     will run with user ID 1000.
   * **runAsGroup**: specifies the primary group ID of 3000 for all processes
     within any containers of the Pod. If this field is omitted, the primary
     group ID of the containers will be root(0).
   * **fsGroup**: specifies a supplementary group that the containers belong 
     to. In this case, containers in this Pod belong to supplementary group ID
     2000.
   
2. We are applying the `NET_ADMIN` capability only to the `ubuntu` container.

3. We are disabling privilege escalation only to the `some-other-thing` 
   container.

As you might imagine, there's actually quite a lot to security contexts, which
you can read more about 
[here](https://kubernetes.io/docs/tasks/configure-pod-container/security-context/#set-the-security-context-for-a-container),
but this should do for a tl;dr article for now.

[Back to top](#table-of-contents)


---


## Service Accounts
Authentication, Authorization, RBAC, etc...
There are two types of accounts in K8s. A **user account** is used by admins,
developers and... well... users! A **ServiceAccount** is used by machine; such
as a system monitoring tool like Prometheus, or a build automation tool like 
Jenkins. Let's create a service account:

```bash
kubectl create serviceaccount my-serviceacct
```

Let's list the service accounts that exist within our NameSpace.

```bash
kubectl get serviceaccounts
#> NAME            SECRETS     AGE
#> default         1           10d
#> my-serviceacct  1           15s
```

Notice that there are two ServiceAccounts when we run the above command. Some
things to be aware of:
 * For every NameSpace in Kubernetes, a ServiceAccount named `default` is
   automatically created.
 * Each NameSpace has its own default ServiceAccount.
 * Whenever a Pod is created, the default ServiceAccount, and its token,
   are automagically mounted to that Pod as a VolumeMount. If you were to run
   ```bash
   kubectl describe pod some-application
   #> Name:
   #> Namespace:
   #> ... 
   #> Mounts:
   #>   /var/run/secrets/kubernetes.io/serviceaccount from default-token-abc123
   ```
   The `default` ServiceAccount is very limited, and only has permission to run
   basic K8s API queries.

This is because **whenever a new NameSpace is created, an accompanying `default`
service account is created. Each namespace has its own default ServiceAccount.**
Now, let's get the details of the ServiceAccount that we just created:

```bash
kubectl describe serviceaccount my-serviceacct
#> Name:                 my-serviceacct
#> Namespace:            default
#> Labels:               <none>
#> Annotations:          <none>
#> Image pull secrets:   <none>
#> Mountable secrets:    my-serviceacct-token-kbbdm
#> Tokens:               my-serviceacct-token-kbbdm
#> Events:               <none>
```

Notice there is a `Tokens` field with a single element by the name
`my-serviceacct-token-kbbdm`. This object is a Secret, which contains an API
token which will be used to authenticate applications wishing to utilize the
service account. We can get the API Token of `my-serviceacct-token-kbbdm` by
running the following command:
```bash
kubectl describe secret my-serviceacct-token-kbbdm
#> Name:          my-serviceacct-token-kbbdm
#> Namespace:     default
#> Labels:        <none>

#> Type:          kubernetes.io/service-account-token

#> Data
#> ====
#> ca.crt:        1025 bytes
#> namespace:     7 bytes
#> token:
#> jkhfdisuHfiweurfhEKfjheifuHWEFiuwehtjHEfKJHEFKLJhefKJefheL
#> AD1dY0UKn0wThAtThIsT0K3nIsT0ta11ymad3UPl0Lk33puRT0K3NzSaF3
#> DSAf98540fwoeifhjOEfowiej54o3iojFoiJ
```

We can attach this token as an authentication header to `curl`, or put this
token into a 3rd party application that interacts with our K8s cluster.
As said in one of the bullets above, whenever a Pod is created, the `default`
ServiceAccount is mounted as a Volume. We can change the ServiceAccount that 
gets mounted to a Pod like so:
```yaml
apiVersion: v1
kind: Pod
metadata:
   name: my-app
spec:
   containers:
      - name: my-app
        image: my-app

   # You can select one of the following two lines. They are mutually exclusive.
   serviceAccount: my-serviceacct
   automountServiceAccountToken: false
```
**NOTE**: You cannot change the ServiceAccount being used by an existing Pod,
however, you can update the ServiceAccount used by `containers` of Deployment
objects, thanks to rolling upgrade of the Pods associated with the Deployment.


[Back to top](#table-of-contents)


--- 


## Resource Requests and Limits 

Worker nodes have a limited pool of resources (CPU, memory and disk), so the
K8s scheduler schedules Pods in such a way to avoid starvation. We can include
a **resource request** in a container spec, so that when the scheduler tries to 
place a Pod on a node, it can determine what nodes can support the Pod. If none
of the nodes have enough resources to run the Pod, the Pod will maintain a
status of `Pending`.

If you know that your Pod will need more than the defaults, you can modify
these values. Since Docker containers have no limit to the amount of 
resources they consume on a node, we can impose **limits** on the container.
```yaml
apiVersion: v1
kind: Pod
metadata:
   name: myapp
   labels:
      name: myapp
spec:
   containers:
     - name: myapp
       image: myapp

       resources:
          # 1
          requests:
             memory: "1Gi"
             cpu: 1
            
          # 2
          limits:
             memory: "2Gi"
             cpu: 2
```
1. We request 1 Gibibyte (1024 bytes, as opposed to a Gigabyte, which is 1000
   bytes) and 1 vCPU cores (equivalent to 1 AWS vCPU, 1 GCP Core, 1 Azure Core,
   or 1 Hyperthread).

2. We restrict the container to 2 Gibibytes of memory and 2 vCPU cores. 
   * If a container attempts to consume more vCPU, Kubernetes throttles the vCPU.
   * Containers are allowed to consume more memory than the limit, but if they
     make a habit of overconsuming memory, the Pod will be terminated and
     restarted.


We can declare a **LimitRange** within a Namespace to create default
`resources.requests` and `resources.limits` for all Pods created within that
Namespace. For example:
```yaml
apiVersion: v1
kind: LimitRange
metadata:
   name: mem-limit-range
   namespace: dev
spec:
   limits:

    # 1
    - type: Container
    
    # 2
      defaultRequest:
         memory: 256Mi

    # 3
     default:
         memory: 512Mi
```
The above yaml reads as follows:
1. If a Container is created in the `dev` namespace without specifying its
   own request or limits;
1. then default memory request is created for that Container of 256 Mibibytes;
1. and a memory limit of 512 Mibibytes.


[Back to top](#table-of-contents)


--- 


## Taints and Tolerations

**Taints** and **tolerations** provides you finer control over where the K8s
scheduler places Pods. A taint is applied to a Node and essentially acts like
"Pod repellent," while tolerations are applied to Pods and act like "taint
immunity." The gif (pronounced `/ɡɪf/`) below should offer some insight.

![taints_and_tolerances](https://media.giphy.com/media/S3iEPVpE7sq41pVkS0/giphy.gif)

Above, you can see we have Node_1, Node_2, and Node_3. We also have 4 Pods we 
wish to schedue across the three nodes, Pod_A, Pod_B, Pod_C, and Pod_D. We have
applied a taint on Node_1, and a tolerance on Pod_D. Pod_A, Pod_B, and Pod_C 
are intolerant to the taint on Node_1, so they get scheduled across Node_2 and 
Node_3. Finally, Pod_D is tolerant to the taint on Node_1, so Pod_D gets 
scheduled on Node_1.

To apply a toleration to a Pod, you can add this information to a Pod config:
```yaml
apiVersion: v1
kind: Pod
metadata:
   name: myapp
spec:
   containers:
      - name: my-ubuntu
        image: my-ubuntu
   
   tolerations:
   - key: "app"
     operator: "Equal"
     value: "blue"
     effect: "NoSchedule"
```
See the 
[K8s docs](https://kubernetes.io/docs/concepts/scheduling-eviction/taint-and-toleration/#concepts)
for more yaml variants.

We can apply a taint to a node through the command line like so:
```bash
kubectl taint nodes <node-name> <key>=<value>:<taint-effect>
```
* `node-name` should be replaced with the name of the node you want to apply
  taint to.
* `key`, `value` should be replaced with tolerations that ignore the taint. For
  example, if we only wanted to apply Pods like above to be scheduled on the
  node, we would specify `app=blue`. 
* `taint-effect` defines what would happen to the Pod if they do not tolerate
  the taint. Replace with one of the following to apply an effect:
  1. `NoSchedule`: the Pods will not be scheduled on the Node
  1. `PreferNoSchedule`: K8s will try to avoid placing a Pod on the Node, but 
     this is not guaranteed.
  1. `NoExecute`: new Pods will not be scheduled on the Node, and existing Pods
     on the node, if any, will be evicted if they do not tolerate the taint 
     (these Pods may have been scheduled on the Node before the taint was 
     applied).

> **Fun Fact**: A Kubernetes cluster is composed of a single Master Node, and 
  _n_-many Worker Nodes. By default, no Pods can be scheduled on the Master
  Node, which is accomplished via a Taint. You can modify this behavior if you
  need (which I don't recommend), but you can view this taint via
  `kubectl describe node kubemaster | grep Taint`.

[Back to top](#table-of-contents)


---


## Node Selectors and Affinity

We can assign a label to a Node from the command line like so:

```bash
kubectl label nodes <node-name> <label-key>=<label-value>
```

* `node-name` should be replaced with the name of the node you want to attach
  the label to.
* `label-key` is the key you want to assign to the node.
* `label-value` is a value you want to assign to the node.

Let's say we have a single Node in our cluster that is a lot beefier than the
rest, and we want to prefer that a particular Pod runs on it to avoid sucking
up too many resources of the weaker nodes. We can apply a label on it like so:
```bash
kubectl label nodes my-big-node size=Large
```

Now, we can control where Pods are scheduled in another way, through the use of
a **nodeSelector**. 
```yaml
apiVersion: v1
kind: Pod
metadata:
   name: myapp-pod
spec:
   containers:
      - name: data-processor
        image: data-processor
   
   # 1
   nodeSelector:
      size: Large
```
1. The `nodeSelector` is scheduling this Pod on Nodes with the label 
   `size=Large`. This pod will get scheduled on `my-big-node` due to the
   kubectl command we ran above.


There are limitations to nodeSelectors. For example, we can't specify a Pod to
run on either a Large **OR** Medium Node. Likewise, we can't specify a Pod to 
run on any Node that **IS NOT** Small. This is where **nodeAffinity** comes 
into play.
```yaml
apiVersion: v1
kind: Pod
metadata:
   name: myapp-pod
spec:
   containers:
      - name: data-processor
        image: data-processor
   
   # 1 
   affinity:
      # 2 
      nodeAffinity:
         # 3
         requiredDuringSchedulingIgnoredDuringExecution:
            # 4
            nodeSelectorTerms:
               # 5 
               - matchExpressions:
                  - key: size
                    operator: In
                    values:
                       - Large 
``` 
This yaml file looks super complicated, but it provides the same functionality
as the yaml in the nodeSelector example above. Let's break this down:

1. Define an `affinity` section at the same level as `containers`.

1. Declare that we are going to be applying a `nodeAffinity`.

1. What if there are no Nodes that this could be scheduled to? What if someone
   changes the labels on the Node later on? This is handled via the type of
   nodeAffinity. There are a few types of nodeAffinity that can be used:

   * `requiredDuringSchedulingIgnoredDuringExecution`: the scheduler can only
     place the Pod on matching Nodes, and if no matching Nodes exist, the Pod
     will not be scheduled (thus, **required during scheduling**). If the label
     associated with the Node changes such that the nodeAffinity is no longer
     satisfied, the Pods will continue to run normally (thus 
     **ignored during execusion**).

   * `preferredDuringSchedulingIgnoredDuringExecution`: the scheduler will try
     to place the Pod on matching Nodes, and if no matching Nodes exists, the
     Pod will be scheduled on whatever Node can support it (thus 
     **preferred during scheduling**). If the label associated with the Node
     changes such that the nodeAffinity is no longer satisfied, the Pods will 
     continue to run normally (thus **ignored during execusion**).

   * `requiredDuringSchedulingRequiredDuringExecution`: the scheduler can only
     place the Pod on matching Nodes, and if no matching Nodes exist, the Pod
     will not be scheduled (thus, **required during scheduling**). If the label
     associated with the Node changes such that the nodeAffinity is no longer 
     satisfied, t      _blue-pod.yaml_
      ```yaml
      apiVersion: v1
      kind: Pod
      metadata:
         name: green-pod
      spec:
         containers:
            - name: my-blue-app
              image: my-blue-app
         
         tolerations:
         - key: "app"
           operator: "Equal"
           value: "blue"
           effect: "NoSchedule"
      ```he Pod will be evicted and attempted to be rescheduled.

1. `matchExpressions` are the labels we want to match on. Because we can match
    against multiple values, `values` is a list.

We can accomplish scheduling on a Large **OR** Medium Node by replacing section
5 with:
```yaml
- matchExpressions:
   key: size
   operator: In
   values:
      - Large
      - Medium
```

We can accomplish scheduling on any Node that is **NOT** Small by replacing
section 5 with:
```yaml
- matchExpressions:
  key: size
  operator: NotIn
  values:
     - Small
```

We can choose to only schedule Pods on Nodes which have a particular key by
replacing section 5 with:
```yaml
- matchExpressions:
   key: size
   operator: In
```

[Back to top](#table-of-contents)


---


## Using Node Affinity with Taints and Tolerations

Using Node Affinity, Taints, and Tolerations, we have finer control over where
Pods get scheduled. Refer to the gif below:

![nodeAffinityVsTaintsAndTolerations](https://media.giphy.com/media/eKmngsQDV42AiAeEWs/giphy.gif)

There's a lot going on here, so let's break it down:
* We have 5 Pods we want to schedule across 5 Nodes. 
* Three of the Pods and Nodes have metadata assigned to them which associates a color to them.
* We don't care where the uncolored Pods get scheduled.
* We don't care what gets schedule on the uncolored Nodes.
* **We want to make sure that exactly one Pod gets scheduled per Node.**
* Now let's take some action to schedule things nicely:
   1. We apply taints to the colored Nodes which looks something like this:
      ```bash
      kubectl taint nodes Blue-Node color=blue:NoSchedule
      kubectl taint nodes Red-Node color=red:NoSchedule
      kubectl taint nodes Green-Node color=green:NoSchedule
      ```
   1. Next, we apply tolerations to the colored Pods, which looks like:

      _green-pod.yaml_
      ```yaml
      apiVersion: v1
      kind: Pod
      metadata:
         name: green-pod
      spec:
         containers:
            - name: my-green-app
              image: my-green-app
         
         tolerations:
         - key: "color"
           operator: "Equal"
           value: "green"
           effect: "NoSchedule"
      ```

      _blue-pod.yaml_
      ```yaml
      apiVersion: v1
      kind: Pod
      metadata:
         name: green-pod
      spec:
         containers:
            - name: my-blue-app
             image: my-blue-app
         
         tolerations:
         - key: "color"
           operator: "Equal"
           value: "blue"
           effect: "NoSchedule"
      ```

      _red-pod.yaml_
      ```yaml
      apiVersion: v1
      kind: Pod
      metadata:
         name: green-pod
      spec:
         containers:
            - name: my-red-app
              image: my-red-app
         
         tolerations:
         - key: "color"
           operator: "Equal"
           value: "red"
           effect: "NoSchedule"
      ```

   1. Then, we attach labels to the colored Nodes for use with NodeAffinity:
      ```bash
      kubectl label nodes Blue-Node node-color=blue
      kubectl label nodes Red-Node node-color=red
      kubectl label nodes Green-Node node-color=green
      ```
   1. Finally, we add Node Affinities to the spec of the colored Pods like so:
      
      _green-pod.yaml_
      ```yaml
      ...
         affinity:
            nodeAffinity:
               requiredDuringSchedulingIgnoredDuringExecution:
                  nodeSelectorTerms:
                     - matchExpressions:
                        - key: color 
                          operator: In
                          values:
                             - green
      ```

      _blue-pod.yaml_
      ```yaml
      ...
         affinity:
            nodeAffinity:
               requiredDuringSchedulingIgnoredDuringExecution:
                  nodeSelectorTerms:
                     - matchExpressions:
                        - key: color 
                          operator: In
                          values:
                             - blue
      ```

      _red-pod.yaml_
      ```yaml
      ...
         affinity:
            nodeAffinity:
               requiredDuringSchedulingIgnoredDuringExecution:
                  nodeSelectorTerms:
                     - matchExpressions:
                        - key: color 
                          operator: In
                          values:
                             - red
      ```

   1. What results is a way to guarantee:
      * the green Pod is scheduled on the green Node.
      * the blue Pod is scheduled on the blue Node.
      * the red Pod is scheduled on the red Node.
      * the two uncolored Pods are not scheduled on any of the colored Nodes,
        but instead, are scheduled arbitrarily across the "Other" Nodes.

[Back to top](#table-of-contents)


---


# Observability

![paintingThatWatchesYou](https://i.pinimg.com/originals/36/17/bd/3617bd4b431b8f6634867bf077a864d2.gif)

## Status and Conditions

Every Pod has a **status**, which describes the state of the Pod. Pods enter
the following states in this exact order:
   1. `Pending`: When a Pod is first created, it is in a Pending state. This is
      when the scheduler tries to find a Node to place the Pod. If the scheduler
      can't find a Node to place the Pod on, it will be stuck in a Pending state.
      To figure out why the Pod is stuck in Pending, run 
      `kubectl describe pod <name-of-your-pod>`

   1. `ContainerCreating`: Once the Pod is scheduled, it enters the 
      ContainerCreating state, where the necessary images are pulled
      and containers are started.

   1. `Running`: Once all of the containers in a Pod are running, the Pod enters
      the Running state, which it continues to be until the program completes
      successfully, or is terminated.

**Conditions** compliment Pod status. They are an array of true or false values
that tell us about the state of a Pod. You can view the conditions of a Pod by
running `kubectl describe pod`. The (truncated) output will look something like
this:
```bash
Name:          nginx-abc123-asdf
Namespace:     default
Node:          kubenode2/192.168.1.103
Start Time:    Mon, 18 May, 2020 19:20:39 - 0400
Labels:        app=prod
               color=blue

...

Conditions:
   Type:         Status
   Initialized:  True
   Ready:        True
   PodScheduled: True
```

[Back to top](#table-of-contents)


---


## Readiness Probes

Here's a dangerous little scenario that can easily happen to anybody new to 
K8s. If we were to run `kubectl get pods`, we may get something like this:
```
NAME                  READY      STATUS       RESTARTS   AGE
nginx-abc123-asdf     1/1        Running      0          12m 
```
but when navigate to a web page being served by our Pod, we are greeted with 
this in our browser:

![thisSiteCantBeReached](https://encrypted-tbn1.gstatic.com/images?q=tbn%3AANd9GcRU0k3LmXSKfiYqTs14xEAHERENTP-uLQcw3d6qxdKP5PYJzfqa&usqp=CAU)

Why does this happen? According to `kubectl get pods`, our Pod is ready, and
its status is Running... What gives? Well, this is caused by erroneously
communicating the readiness of an application. Something like Nginx may take a 
couple of minutes to become accessible. Even though our Nginx Pod **is** 
Running, it is **not truly ready**.

To determine when a Pod is actually ready, we employ **Readiness Probes**
inside the container. As a developer of the appliation, you know what makes the
application _ready_. There are three types of Readiness Probes:

1. **HTTP Readiness Probe**: Checks if a specific path is resolvable.
   ```yaml
   ...
   spec:
      readinessProbe:
         httpGet:
            path: /api/ready
            port: 8080
   ```
   This checks if localhost:8080/api/ready is resolvable.

1. **TCP Test**: Checks if a particular TCP socket is listening.
   ```yaml
   ...
   spec: 
      readinessProbe:
         tcpSocket:
            port: 3306
   ```
   This checks to see if localhost:3306 is listening.

1. **Exec Test**: Execute a custom script within the container that exits with
    an exit code of 0.
   ```yaml
   ...
   spec:
      readinessProbe:
         exec:
            command:
            - cat
            - /app/is_ready.txt
   ```
   This checks to see if /app/is_ready.txt exists, and if it does, cat exits with 0.

There are some additional options we can add to our Readiness Probe.
```yaml
...
spec:
   readinessProbe:
      tcpSocket:
         port:3306

   # 1
   initialDelaySeconds: 10

   # 2
   periodSeconds: 5

   # 3
   failureThreshold: 8
```

1. `initialDelaySeconds`: describes how long to wait before running a Readiness
   Probe.

1. `periodSeconds`: describes how long to wait between Readiness Probe attempts.

1. `failureThreshold`: describes how many failed Readiness Probes to allow 
   before terminating the Pod. By default, a Pod will be terminated after 3
   attempts.

Having Pods configured with Readiness Probes that reflect true readiness is
important in developing production applications.

[Back to top](#table-of-contents)


---


## Liveness Probes

Let's say one of your interns deploys a vital web service with this line hidden
away:
```python
if today.month == "March" and today.day = 15:
   while True:
      doNothing()
```
This happens to be your intern's birthday, and they've decided that every year 
on their birthday, this container is going to seize up. Technically, the
application is still running, so there is no reason for Kubernetes to think
that there is anything wrong with the application. In fact, Kubernetes thinks
the application is alive and well!

This may not be a real world example, but applications can lock up due to
unforeseen edge cases and nasty bugs that made it past code review. This is
where **Liveness Probes** become useful. They are pretty similar to Readiness
probes --- both in yaml syntax, and function --- except they run throughout a
Pod's life to ensure the Pod is still... well... alive!

Just like with Readiness Probes, Liveness Probes come in 3 flavors: 
`HTTP Test`, `TCP Test`, and `Exec Test`. You can also define an
`initialDelaySeconds`, `periodSeconds`, and `failtureThreshold`, just as you
would for a Readiness Probe. I won't belabor the configs for each scenario
since they're essentially identical to Readiness Probes, but here's an example
of how you might use a Liveness Probe:
```yaml
apiVersion: v1
kind: Pod
metadata: 
   name: my-app
spec:
   containers:
      - name: nginx
        image: nginx
   
   readinessProbe:
      httpGet:
         path: /api/healthy
         port: 8080
      initialDelaySeconds: 15
      periodSeconds: 5
      failureThreshold: 8
   
   livenessProbe:
      httpGet:
         path: /api/healthy
         port: 8080
      initialDelaySeconds: 30
      periodSeconds: 15 
      failureThreshold: 4 
```

In this case, I'm allowing 4 failed liveness checks, issued every 15 seconds, 
30 seconds after the Pod's birth.

[Back to top](#table-of-contents)


---


## Logging and Monitoring

When you run containers directly on Docker, you can view the logs of a running
container via `docker logs -f <some-container-id>`. We can view the logs of a
container running within a Pod using `kubectl logs -f <name-of-pod>`. But what
if we are running a Pod that has multiple containers running with in? Simple!
We have to add the name of the container we are targeting like so:
`kubectl logs -f <name-of-pod> <name-of-container>`. This allows us to view the
logs of a specific container within the Pod.

Kubernetes comes with a **Metrics Server**. You can have one Metrics Server per
Kubernetes Cluster, but this is an in-memory monitoring solution, which means
you cannot view historical performance data. To utilize historic data, you may
use Prometheus, the ELK stack, Datadog, Dynatrace, etc. to accomplish this.

If you are running in minikube, you will need to enable the metrics server via:
```bash
minikube addons enable metrics-server
```

For all other environments, you will have to deploy the deployment file via:
```bash
# pull the project from git.
git clone https://github.com/kubernetes-incubator/metrics-server.git

# after locating the deployment file...
kubectl create -f deploy/1.8+
```

You can view resource consumpion of nodes via:
```bash
kubectl top node
#> NAME           CPU(cores)   CPU%    MEMORY(bytes)    Memory%
#> kubemaster     166m         8%      1330Mi           70%
#> kubenode1      35m          4%      1044Mi           50%
#> kubenode2      22m          2%      1048Mi           55%
```

And you can view resource consuption of Pods via:
```bash
kubectl top pod
#> NAME          CPU(cores)    CPU%    MEMORY(bytes)    Memory%
#> nginx         166m          8%      1330Mi           70%
#> redis         35m           4%      1044Mi           50%
```

[Back to top](#table-of-contents)


---


# Services and Networking

![networkToGetWork](https://media1.tenor.com/images/065bbd14deb33c2e4f21cf0f35a188b5/tenor.gif?itemid=12765145)

## Services 
A Kubernetes **Service** object enables communications with components within,
and outside of, the application. You can think of Services like doors; sure
there are other ways to get into the house (e.g. windows, trap doors, the
cimney), but the front door is your best bet! In the diagram below, take note
of the fact that Services really are the gatekeepers of communication. We see
users outside of the node accessing a Pod; we see Pods talking to eachother;
and we see a Pod reach outside of the Node to an external database.

![services_as_doors.png](./.assets/services_as_doors.png)

There are three kinds of Service objects:
1. **LoadBalancer**:  Exposes Pods externally using a cloud provider’s 
   load balancer.
1. **ClusterIP**: Service creates virtual IP within cluster to enable 
   communication between different services.
1. **NodePort**: Service makes an internal port accessible through a port on
   the Node.

We will elaborate on the three Service types, and create a respective object
for the following Pod config:

```yaml
apiVersion: v1
kind: Pod
metadata:
   name: myapp-pod
   labels:
      app: myapp
      type: back-end
spec:
   containers:
   -  name: nginx-container
      image: nginx
```

[Back to top](#table-of-contents)


---


## LoadBalancer Service

To reiterate, a **LoadBalancer** Service exposes Pods externally using a
cloud provider’s load balancer. 

![credit: Ahmet Alp Balkan](https://miro.medium.com/max/1400/1*P-10bQg_1VheU9DRlvHBTQ.png)

A LoadBalancer Service object will give you a single IP address that will
forward all traffic to your service, exposing it to the BBI (Big Bad Internet).
Your particular implementation of the LoadBalancer may vary depending on which
cloud provider you use, so I'll link you to the
[Kubernetes Documentation](https://kubernetes.io/docs/concepts/services-networking/#loadbalancer) for more info.


[Back to top](#table-of-contents)


---


## ClusterIP Service

To reiterate, a **ClusterIP** Service object creates a virtual IP within the
cluster to enable communication between different services.

![clusterip.png](./.assets/clusterip.png)

In the diagram above, we are routing `front-end` to `back-end`, and `back-end`
to `redis`; all enabled with the use of services. Let's create a ClusterIP
Service object that routes `front-end` to `back-end`.

```yaml
apiVersion: v1
kind: Service
metadata:
   name: back-end
spec:

   # 1. Specifies that we are creating a ClusterIP Service object,
   type: ClusterIP

   # 2. Port details.
   ports:
   -  targetPort: 80
      port: 80

   # 3. Use a selector to specify what Pods to target.
   selector:
      app: myapp
      type: back-end
```
1. Tell K8s that you want to make a ClusterIP Service object.
1. Tell the ClusterIP Service what ports it should care about; `port` specifies
   the port that the Service is listening on, and the `targetPort` specifies
   the port that the target Pod is listening on.
1. Using a selector with the labels of the Pod definition from above, we
   tell the NodePort to target `myapp-pod`. This will select all Pods matching
   this selector, and randomly route traffic to one of them. Of course, to
   learn how to change this random behavior, see
   [here](https://kubernetes.io/docs/concepts/services-networking/service/).

[Back to top](#table-of-contents)


---


## NodePort Service

To reiterate, a **NodePort** Service object makes an internal port accessible
through a port on the Node.

![node_port.png](./.assets/node_port.png)

In the diagram above, take notice of:
1. `targetPort`: field that refers to the Port of the Pod you wish to 
   target.
1. `port`: field that refers to the port on the service itself. The Service
   acts a lot like a virtual server, having it's own IP address for routing,
   which is the *cluster IP of the service*.
1. `nodePort`: which is the port being exposed from the Node. The default port
   range is 30000 - 32767, but this can be assigned to any ephemeral port.

Let's create a NodePort Service definition file for `myapp-pod`, referenced
in the [Services](#services) section.

```yaml
apiVersion: v1
kind: Service
metadata:
   name: myapp-service
spec:

   # 1. Specifies that we are creating a NodePort Service object.
   type: NodePort

   # 2. Port details as defined above.
   ports:
   -  targetPort: 80
      port: 80
      nodePort: 30008
   
   # 3. Use a selector to specify what Pods to target.
   selector:
      app: myapp
      type: back-end
```

1. Tell K8s that you want to make a NodePort Service object.
1. Tell the NodePort what ports it should care about.
1. Using a selector with the labels of the Pod definition from above, we
   tell the NodePort to target `myapp-pod`.

Now, from the command line:
```bash
kubectl create -f service-def.yaml
#> service "myapp-service" created

kubectl get services
#> NAME              TYPE        CLUSTER-IP        EXTERNAL-IP    PORT(S)           AGE
#> kubernetes        ClusterIP   10.96.0.1         <none>         443/TCP           22d
#> myapp-service     NodePort    10.106.127.123    <none>         80:30008/TCP      18m

curl https://<physical-ip-of-your-node>:30008
```

If there are multiple Pods that match the selector, Kubernetes will select
one at random to route the traffic to. This behavior can be changed, as
described 
[here](https://kubernetes.io/docs/concepts/services-networking/service/).

[Back to top](#table-of-contents)


---


## Ingress Controllers

Let's say you own a website that has several applications accessible through
different paths. For example, `google.com/voice`, `google.com/hangouts`, etc...
Normally, your browser would perform a DNS lookup for `google.com`, and route
all traffic to whatever IP resolves. You would need to utilize a NodePort to
expose a port, and then a series of LoadBalancers to route traffic to Pods in
a scalable fashion. If you are on GCP, Azure, or AWS, you must pay for each
LoadBalancer, not to mention the fact that you have to implement SSL/TLS
through each hop, configure firewall rules for each service, etc...

This is becoming a headache, but thankfully, you can manage all of this
directly from the Kubernetes cluster with the use of an **Ingress**.
Ingress helps your users access your application through a single externally-
accesible URL that you can configure to route to different services within your
cluster. Oh, and you can configure it to use SSL! 

![Credit: Ahmet Alp Balkan](https://miro.medium.com/max/1400/1*KIVa4hUVZxg-8Ncabo8pdg.png)

To begin setting up an Ingress, we must deploy an **Ingress Controller**, which
is the application responsible for handling the proxying for us. You can use 
Nginx, Contour, HAProxy, Traefik, Istio, or some other application, but we will
be using Nginx in this example. To configure an Ingress Controller, we require:
a Deployment that abstracts interacting with the Nginx Pod, a NodePort Service
to expose the Ingress Controller to the outside world, and a ServiceAccount to
provide the Ingress Controller the ability to modify the internal K8s network.
Let's start by creating the deployment:

```yaml
apiVersion: extensions/v1beta1
kind: Deployment
metadata:
   name: nginx-ingress-controller
spec:
   replicas: 1
   selector:
      matchLabels:
         name: nginx-ingress
   template:
      metadata:
         labels:
            name: nginx-ingress
      spec:
         containers:
         -  name: nginx-ingress-controller
            image: quay.io/kubernetes-ingress-controller/nginx-ingress-controller:0.21.0
         
         # 1. Set environment variables for Pod;
         env:
         -  name: POD_NAME
            valueFrom:
               fieldRef:
                  fieldPath: metadata.name
         - name: POD_NAMESPACE
            valueFrom:
               fieldRef:
                  fieldPath: metadata.namespace
               
         # 2. Command to run nginx server;
         args:
            - "/nginx-ingress-controller"
            - "--configmap-$(POD_NAMESPACE)/nginx-configuration"
         
         # 3. Specify the ports ued by the ingress controller.
         ports:
         -  name: http
            containerPort: 80
         -  name: https
            containerPorts: 443
```

1. We create some environment variables that will be visable to all containers
   in our Pod. We utilize the metadata from the Pods that will be created and
   assign them to `POD_NAME` and `POD_NAMESPACE`.

1. We run 
   `/nginx-ingress-controller --configmap-$POD_NAMESPACE)/nginx-configuration`
   on the Pod to run the nginx server, using the environment variables we
   defined in the lines above. We define `nginx-configuration` in a second, but
   this is essentially a file that stores configuration information about the
   Ingress Controller.

1. We specify the ports used by the Ingress Controller, which are your classic
   HTTP/S ports.

Now, we need to create a ConfigMap to pass information about how to configure
the Nginx server. We won't add much to it, but just know that if you ever need
to configure your Ingress Controller, this is the place to do it:

```yaml
apiVersion: v1
kind: ConfigMap
metadata:
   name: nginx-configuration 
```

Next, we need to create a NodePort Service object to route traffic that hits 
the node through TCP ports 443 or 80 to all Pods that match the given selector.

```yaml
apiVersion: v1
kind: Service
metadata:
   name: nginx-ingress
spec:
   type: NodePort
   ports:
   -  port: 80
      targetPort: 80
      protocol: TCP
      name: http
   -  port: 443
      targetPort: 443
      protocol: TCP
      name: https
   selector:
      name: nginx-ingress
```

Finally, we need to create a ServiceAccount with the correct roles and role 
bindings. This will allow the Ingress Controller to monitor the Kubernetes
cluster for Ingress Resources (which we will describe in the next section. 

```yaml
apiVersion: v1
kind: ServiceAccount
metadata:
   name: nginx-ingress-serviceaccount
```


[Back to top](#table-of-contents)


---


## Ingress Resources

Now, it's time to define **Ingress Resource** objects, which are sets of rules
and configurations that are applied to the Ingress Controller. Through the use
of Ingrss Resources, we can: forward all incoming traffic to a single 
application, route traffic to different applications based on the URL, route
users based on the domain name itself, etc... Let's start by defining an 
Ingress Resource to serve the Google Voice application.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata: 
   name: ingress-voice
spec:
   backend:
      serviceName: google-voice-service
      servicePort: 80
```

And now, we'll create another Ingress object to describe the path to our Google
Hangouts application.

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata: 
   name: ingress-hangouts
spec:
   backend:
      serviceName: google-hangouts-service
      servicePort: 80
```

Now that we've defined Ingress Resources for our Google Voice and Google
Hangouts apps, we need to tell the IngressController how to manage routing.
Let's build an Ingress Resource that routes traffic based on the URL; e.g.
google.com/voice, google.com/hangsouts, etc. :

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
   name: ingress-voice-or-hangouts
spec:
   rules:
   -  http:
         paths:
         -  path: /voice
            backend:
               serviceName: google-voice-service
               servicePort: 80
         -  path: /hangouts
            backend:
               serviceName: google-hangouts-service
               servicePort: 80
```

At this point we can hit the specified `backend` via their specified 
`serviceName` and `servicePort`. But let's say that instead, we wanted to be
able to route traffic based on the domain. For example, we want to launch or
Voice app through _voice.google.com_. Well, the good news is, we can simply
add another Ingress Resource taht has rules defined for host:

```yaml
apiVersion: extensions/v1beta1
kind: Ingress
metadata:
   name: ingress-voice-or-hangouts-2
spec:
   rules:
   -  host: voice.google.com
      http:
         paths:
         -  backend:
               serviceName: google-voice-service
               servicePort: 80
-  host: hangouts.google.com
   http:
      paths:
      -  backend:
            serviceName: google-hangouts-service
            servicePort: 80
```

Tada! It's that easy! If you go in your browser address bar right now and type
`https://google.com/voice`, then you'll notice that you are redirected to
`https://voice.google.com`. This is accomplished with the use of the
`rewrite-target` option. For more information, see 
[here](https://kubernetes.github.io/ingress-nginx/examples/rewrite/).


[Back to top](#table-of-contents)


---


## Network Policies

A **NetworkPolicy** is a way to control inbound or outbound traffic that Pods
can receive or send. A NetworkPolicy is another Kubernetes object that uses
labels and selectors to determine what Pods to target.

Let's say we have an API that sends logfiles to a database. Refer to the
Pod specs below:

```yaml
apiVersion: v1
kind: Pod
metadata:
   name: api-pod
   labels:
      role: api
spec:
   containers:
   -  name: python3
      image: python3
```

```yaml
apiVersion: v1
kind: Pod
metadata:
   name: log-db
   labels:
      role: db
spec:
   containers:
      -  name: mysql 
         image: mysql 
```

As you can see, we the two Pods we are concerned about are named `api-pod` and
`log-db`. Let's create a SecurityPolicy object to only allow ingress (inbound)
traffic to `log-db` from `api-pod` over port 3306/TCP.

```yaml
apiVersion: networking.k8s.io/v1
kind: NetworkPolicy
metadata:
   name: db-policy
spec:

   # 1. Select what Pod(s) to apply the SecurityPolicy to.
   podSelector:
      matchLabels:
         role: db
   
   # 2. Specify that you want to define an ingress policy.
   policyTypes:
   -  Ingress

   # 3. Allow inbound from Pods matching the selector over port 3306/TCP.
   ingress:
   -  from:
      -  podSelector:
            matchLabels:
               name: api-pod
      ports:
      -  protocol: TCP
         port: 3306
```

1. Utilize selectors to _select_ Pods with `role=db`.
1. Specify that you want to define an ingress policy.
1. Describe your ingress policy. You want to allow `ingress` traffic `from` all
   Pods that match the `podSelector`:`name=api-pod` over `port=3306` with
   protocol `TCP`.

Defining an egress (outbound) traffic rule is pretty much the same. The K8s
[docs](https://kubernetes.io/docs/concepts/services-networking/network-policies/)
describe this ad nauseum.

**NOTE**: Not all network solutions support NetworkPolicies. Kube-router,
Calico, Romana, and Weave-net, are a few network solutions that support K8s
NetworkPolicy objects. Read the K8s 
[docs](https://kubernetes.io/docs/concepts/cluster-administration/networking/)
for more info.


[Back to top](#table-of-contents)


---


# State Persistence

![futurama_cryogenics](https://images.medicaldaily.com/sites/medicaldaily.com/files/2016/12/01/fry-frozen.gif)

## Volumes

**Volumes** are a way to store data generated by Pods in such a way that the
data persists after the Pod is deleted. Let's have a look at a config:

```yaml
apiVersion: v1
kind: Pod
metadata:
   name: random-number-generator
spec:
   # 1. Create a Volume.
   volumes:
   -  name: data-volume
      hostPath:
         path: /data
         type: Directory

   containers:
   -  image: alpine
      name: alpine
      command: ["/bin/sh", "-c"]
      args: ["shuf -i 0-100 -n 1 >> /opt/number.out;"]

      # 2. Mount a Volume to /opt
      volumeMounts:
      -  mountPath: /opt
         name: data-volume
```
1. We create a Volume named `data-volume` configured to use a directory on the
   host machine located at `/opt`.

1. We mount the volume as the `/opt` directory inside the container. When `args`
   are run, the random number will be written to, and persist on, a volume on
   the host machine.

Mounting a Volume on the host is not recommended for use in a multi-node 
cluster, because in multi-node clusters, the data located at `/opt` will be
different depending on which node the Pod is sitting on. You can use a
distributed filesystem such as GlusterFS, CephFS, Hadoop, Azure Disk, Google
Persistent Disk, or AWS Elastic Block Store.



[Back to top](#table-of-contents)


---


## Persistent Volumes and Persistent Volume Claims

A **PersistentVolume** is a cluster-wide pool of storage volumes configured by
an administrator to be used by users deploying applications on the cluster. 
Let's look at a config for creating a PersistentVolume object:

```yaml
apiVersion: v1
kind: PersistentVolume
metadata:
   name: persistent-vol1
spec:
   accessModes:
   -  ReadWriteOnce
   capacity:
      storage: 1Gi
   hostPath:
      path: /tmp/data
   persistentVolumeReclaimPolicy: Delete
```
Not bad right? Take notice of `persistentVolumeReclaimPolicy`. This field tells
how Kubernetes should handle the PersistentVolume once a PersistentVolumeClaim
has been deleted. Your options are:
* **Retain**: requires data to be manually deleted by the administrator, and
  cannot be used by any other Pods.
* **Recycle**: performs a basic scrub (`rm -rf /tmp/data/*`) on the 
  PersistentVolume, making it available for a new claim.
* **Delete**: PersistentVolume is deleted, as well as the associated storage
  asset if applicable (AWS, Azure, GCP, etc...)

You can see the 
[K8s docs](https://kubernetes.io/docs/concepts/storage/persistent-volumes/)
to learn about how to configure the PersistentVolume, since just like
Volume objects, it is not recommended to use a non-distributed file system.

A user must create a **PersistentVolumeClaim** to use a PersistentVolume,
which Kubernetes will use to determine which PersistentVolume the Claim should
be placed on. Kubernetes takes access modes, volume modes, storage class, and 
selectors, into account to do this. Let's create a PersistentVolumeClaim:

```yaml
apiVersion: v1
kind: PersistentVolumeClaim
metadata:
   name: my-pvc
spec:
   accessModes:
   -  ReadWriteOnce
   resources:
      requests:
         storage: 600Mi
```

In the example above, we are requesting 600Mi. The only other PersistentVolume
we've defined is `persistent-vol1`, which has 1Gi of storage. Since this is the
only Volume available, the whole PersistentVolume is claimed and therefore, can
not be shared with any other Pods. This means that 400Mi of the PersistentVolume
will remain unusable.


[Back to top](#table-of-contents)


---


# Stateful Sets

**StatefulSets** are similar to Deployments, since they are scalable can
perform rolling updates, and rollbacks. StatefulSets allow us to deploy
Pods one at a time (e.g. master, worker1, worker2, etc...), and follow
an enumerated naming convention (e.g. mysql-0, mysql-1, etc...).
StatefulSets maintain a "sticky" identity, meaning that the master Pod will
always be at index 0, even if it died and comes back up. We may use a
StatefulSet if we require a consistent naming structure, or if we care about
the order the Pods come up in. Let's look at a configuration for a StatefulSet:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
   name: mysql
   labels:
      apps: mysql
spec:
   templace:
      metadata:
         labels:
            app: mysql
         spec:
            containers:
            -  name: mysql
               image: mysql
   
   # 1. A templetized PersistentVolumeClaim
   volumeClaimTemplates:
   -  metadata:
         name: my-pvc
      spec:
         accessModes:
         -  ReadWriteOnce
         resources:
            requests:
               storage: 600Mi
               
replicas: 3
selector:
   matchLabels:
      app: mysql
serviceName: mysql-headless

# 2. Policy that determines what to do with the PersistentVolume.
podManagementPolicy: OrderedReady

```
1. Instead of creating a PersistentVolumeClaim manually, and specifying it in
   the StatefulSet definition file, we move the entire PersistentVolumeClaim
   definition into the StatefulSet yaml. If one of the Pods in the StatefulSet
   fails, it will spin up a new Pod, and reattach it to the same 
   PersistentVolumeClaim (and therefore, the same PersistentVolume). Therefore,
   PersistentVolumeClaims executed through volumeClaimTemplates are a very
   stable way to provide persistent storage to Pod in a StatefulSet.

1. Looks a lot like a Deployment, don't it? The only field that should look foreign
is `podManagementPolicy`, which determines the way in which Pods are deployed.
This field determines the way that Pods are deployed, either one after another,
or in *Parallel*. To learn more about alternative podManagementPolicies, see
the [K8s doc](https://kubernetes.io/docs/concepts/workloads/controllers/statefulset/#pod-management-policies).



[Back to top](#table-of-contents)


---


## Headless Service

Let's say that we need a Service object that doesn't load balance requests, but
gives us a DNS entry to reach each Pod. This is where a **HeadlessService**
comes in handy. A HeadlessService creates DNS entries for each Pod, and a
subdomain. Let's create a HeadlessService:

```yaml
apiVersion: v1
kind: Service
metadata:
   name: mysql-headless
spec:
   ports:
   -  port: 3306
   selector:
      app: mysql
   clusterIP: None
```

Notice that `clusterIP` is set to `None`. This is what defines it as a 
HeadlessService. Now, let's incorporate this HeadlessService into a Pod spec:

```yaml
apiVersion: v1
kind: Pod
metadata:
   name: myapp-pod
   labels:
      app: mysql
spec:
   containers:
   -  name: mysql
      image: mysql

   # 1. Create a DNS record for the name of the Service.
   subdomain: mysql-headless

   # 2. Create a DNS record with the Pod Name.
   hostname: mysql-pod
```

1. You must specify a `subdomain` with a value the same as the name of the 
   Service. When you do that, it creates a DNS record for the name of the 
   Service to point to the Pod. 
1. Specifying the `hostname` will create a DNS record for each individual Pod.

If we were to use a HeadlessService on a Deployment, this would result in DNS
errors on all of the Pods since a Deployment creates Pods that are essentially
identical. HeadlessServices are extremely useful when interacting with
StatefulSets, since each StatefulSet will provide that enumerated naming
convention. Let's look at a Yaml that uses the HeadlessService described above
with a StatefulSet:

```yaml
apiVersion: apps/v1
kind: StatefulSet
metadata:
   name: mysql-deployment
   labels:
      app: mysql
spec:
   replicas: 3
   matchLabels:
      app: mysql
   template:
      metadata:
         name: myapp-pod
      labels:
         apps: mysql
      spec:
         containers:
         -  name: mysql
            image: mysql

   # 1 Declare the name of the Service to be used.
   serviceName: mysql-headless
```
1. We need to tell the StatefulSet object to make Pods it creates utilize a
   service of the name `mysql-headless`. This allows us to access individual
   Pods like so:
   * `mysql-0.mysql-headless.default.svc.cluster.local`
   * `mysql-1.mysql-headless.default.svc.cluster.local`
   * `mysql-2.mysql-headless.default.svc.cluster.local`


[Back to top](#table-of-contents)


