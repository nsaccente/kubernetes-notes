# tl;dr Kubernetes

> **Prerequisites**:
> * Be comfortable with YAML ✍️ 
>   * [YAML in One Video](https://www.youtube.com/watch?time_continue=83&v=cdLNKUoMc6c&feature=emb_title)

## Quick Links
* [Visit the Kubectl Wiki](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)
* [Read the Kubectl Book](https://kubectl.docs.kubernetes.io/)
* [Visit the Minikube Wiki](https://minikube.sigs.k8s.io/docs/)


## Table of Contents
   * [Introduction](#introduction)
   * [Install Kubectl](#install-kubectl)
   * [Install Minikube](#install-minikube)
   * [Minikube Cookbook](#minikube-cookbook)
   * [Concepts](#concepts)
      * [Worker Nodes](#worker-nodes)
      * [Pods](#pods)
      * [Namespaces](#namespaces)
      * [Labels and Selectors](#labels-and-selectors)


----

   
## Introduction
   Welcome to a **No Nonsense** guide to Kubernetes. I promise to do my best to
   introduce material in a logical manner, provide production-centric examples,
   pepper in memes and gifs to keep things feeling fresh, and explain 
   complicated material as simply as possible. If there's one thing I hate, 
   it's fluff.

   ![KubernetesComic](https://pbs.twimg.com/media/EDrZEKCWwAAG_Ty.jpg)

   [Back to top](#quick-links)


----


## Install Kubectl

   **[Visit the Kubectl Wiki](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)**

   **[Read the Kubectl Book](https://kubectl.docs.kubernetes.io/)**

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

   **[Visit the Minikube Wiki](https://minikube.sigs.k8s.io/docs/)**

   > Minikube is a lightweight Kubernetes implementation that creates a VM on your
   > local machine and deploys a simple cluster containing only one node.


   _[Add Windows or MacOS instructions with a pull request!](https://github.com/strickolas/tldr/pulls)_

   <details open>
   <summary>
   <a class="btnfire small stroke"><em class="fas fa-chevron-circle-down"></em>&nbsp;&nbsp;Installation on Linux</a>   
   </summary> 

   1. Install either [KVM](https://www.linux-kvm.org/page/Main_Page) (which also
      uses QEMU) or [Virtualbox](https://www.virtualbox.org/wiki/Downloads).

      1. If you are a **RHEL/CentOS/Fedora user installing VirtualBox**, you might
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

      1. If you don't want to use a VM, use the `--driver=none` flag to run Kubernetes
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


# Now that Your Work Environment is set up, Let's Get Started!

![Alt Text](https://media1.giphy.com/media/gHtvqib1yFvNbsjwCs/giphy.gif)


---


# The Basics

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
#1 Version of K8s API to use.
apiVersion: v1

#2 Kind of object being created.
kind: Pod

#3 Describes K8s the object; "name" and "labels" are a few of many valid keys.
metadata:
   name: myapp-pod
   labels:
      app: myapp
      type: front-end
      nonsensical-bologna: boop 
      # You can put any key/value pairs you want in labels.
      # While "app" and "type" seem like they may be important labels, they are
      # something that we will use to organize our objects. 
      
#4 The desired state of the object which K8s will work to maintain.
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

[Back to top](#quick-links)


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

  #1 Provides a template of a pod we wish to create when needed.
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

  #2 This ReplicaSet will watch for all pods that have metadata.type: front-end
  selector: 
    matchLabels:
      type: front-end

  #3 Watch for 3 objects matching the above selector. 
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

[Back to top](#quick-links)


---


## Deployments

ReplicaSets solve the problem of having enough Pods running to provide high
availability of our application; but it doesn't make it particularly easy to
upgrade over time. This is where **Deployments** come in. Deployments will
make sure that upgrades happen gradually (also known as a rolling upgrade).
Upgrading pods gradually help to ensure that users do not notice any downtime.
Deployments also make it easy to rollback to a previous versions of software
if need be. The config for a Deployment is nearly identical to a ReplicaSet's
config, except for:
1. We update the `kind` to `Deployment`.
1. We update the name to reflect the fact that it's a Deployment.

```yaml
# Remember, every K8s object has apiVersion, kind, metadata, and a spec.
apiVersion: apps/v1

# 1. This config is nearly identical to ReplicaSet, except for the kind.
kind: Deployment
metadata:

  # 2. We also named it appropriately.
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

1. We can create our Deployment with:
   ```bash
   kubectl create -f deployment-def.yaml
   ```

1. The Deployment automatically creates a ReplicaSet:
   ```bash
   kubectl get replicaset
   # NAME                       DESIRED    CURRENT    READY    AGE
   # myapp-deployment-abc123    3          3          3        2m
   ```

1. The ReplicaSet created by the Deployment will subsequently create Pods:
   ```bash
   kubectl get pods
   # NAME                             READY STATUS   RESTARTS  AGE
   # myapp-deployment-abc123-xyz001   1/1   Running  0         2m
   # myapp-deployment-abc123-xyz002   1/1   Running  0         2m
   # myapp-deployment-abc123-xyz003   1/1   Running  0         2m
   ```

1. Now that we seem to be working with a bunch of different K8s objects, we can
   utilize the following to make our lives a bit easier.
   ```bash
   kubectl get all
   ```

1. You can format the output from `kubectl` using one of the following:
   * `-o json`: Output a JSON formatted API object.
   * `-o name`: Print only the resource name and nothing else.
   * `-o wide`: Output in the plain-text format with any additional information.
   * `-o yaml`: Output a YAML formatted API object.`

> **Pro Tip**: You can use the flag `--dry-run` when executing a `kubectl`
  command and it will inform you whether the syntax of the command and config
  are correct, as well as if the resource is able to be created.

[Back to top](#quick-links)


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
```java
mysql.connect("db-service") // Accessing the service from within the namespace.
mysql.connect("db-service.dev.svc.cluster.local") // Accessing a service outside of namespace.
//             db-service                          Get me a K8s object called _,
//                        dev                      from the namespace _,
//                            svc                  of type _,
//                                cluster.local    and with the domain name.
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

[Back to top](#quick-links)


---


![configfu](https://media.giphy.com/media/3ov9k6pbxOR7X144h2/giphy.gif)

# Mastering Config-Fu

## Commands and Arguents
Let's say we have a Dockerfile that looks like this:
```Dockerfile
FROM Ubuntu
# the command that is run at startup. 
ENTRYPOINT ["sleep"]

# the arguments passed to the command. 
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

[Back to top](#quick-links)


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
        
        # Use the ConfigMap named app-config
        envFrom:
           - configMapRef:
                name: app-config 
```

**Secrets** is very similar to a ConfigMap, except they are stored in an
encoded format.
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