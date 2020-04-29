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
   Kubernetes (sometimes written as k8s) coordinates a highly available cluster
   of computers to work as a single unit by automating the distribution and
   scheduling of application containers. To accomplish this, Kubernetes uses two
   _"resources"_:

   * __Master__: Resposible for managing the cluster; i.e. scheduling, scaling
      applications, rolling out updates, and maintaining applications' desired
      state.
   *  __Worker Node__: A VM or a physical computer that serves as a worker
      machine in a Kubernetes cluster. 

      Each worker node has a __Kubelet__, which is an agent
      for managing the node and communicating with the Kubernetes master. 

   When you deploy applications on Kubernetes, you tell the master to start the
   application containers. The master schedules the containers to run on the
   cluster's nodes. The nodes communicate with the master using the Kubernetes API,
   which the master exposes. End users can also use the Kubernetes API directly to
   interact with the cluster.

>> **For production**: A Kubernetes cluster should have at least 3 workers. 

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

      1. If you are a **RHEL/CentOS/Fedora user installing VirtualBox**, you might encounter:
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
         
      1.  If something doesn't seem right, use the following to increase your logging level:
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

## Concepts 

   ### Worker Nodes
   1. A 

   1. A **worker node** (referred to simply as _node_ in the Kubernetes
      documentation, which is misleading since node is a very overloaded
      term) can either be a VM or physical machine in a Kubernetes
      cluster, which runs a slew of services to aid in management of pods.
      These services include:
      1. The **container runtime** is responsible for running containers.
      1. The **kube-proxy** service programs iptables rules to trap
         access to service IPs and redirect them to the correct backends.
      1. The **kubelet** is a service that is responsible for handling
         requests to create new pods via
         1. polling a directory for new pod configs to run
         1. polling a URL for new pod configs to download and run
         1. from the Kubernetes API server

   1. The worker node can be described with `kubectl describe node <name-of-your-node>` 
      and describes the following:
      1. **Networking stuff**
         1. `HostName`: hostname reported by the worker node’s kernel
         1. `ExternalIP`: the IP address of the worker node that is externally routable
         1. `InternalIP`: the IP address of the worker node that is routable within
            the cluster.

      1. **Conditions** describe the status of all `Running` worker nodes.
         * This is where we introduce **eviction**, which occurs when the status
         of `Ready` remains unknown for longer than `pod-eviction-timeout`
         (this can be changed, of course), causing all pods to be scheduled for
         deletion.

      1. **Capacity and Allocatable** describes the resouces available on the worker
         node: CPU, memory and the maximum number of pods that can be scheduled onto the node.

      1. **Info** describes general information about the node, such as kernel
         version, Kubernetes version (kubelet and kube-proxy version), Docker
         version (if used), and OS name.

   [Back to top](#quick-links)


---


   ### Pods

   1. A **pod** is a collection of containers that share resources, have a
      single IP, and can share volumes. A pod encapsulates 
      an application's container(s), storage resources, a unique IP, and options that govern 
      how the container(s) should run. **Pods run on worker nodes.**

   1. You can use one of the following pod composition patterns to fit the specific need of
      an application you're developing. You are not married to a single pattern.

      1. In _one-container-per-pod_, think of a Pod as a wrapper around a single container, 
      and Kubernetes will just manage the Pods rather than the containers directly.
      This pod pattern will be referred to as a `singleton pod`.

      1. In _multiple-containers-per-pod_, a Pod might encapsulate an application composed 
      of multiple co-located containers that are tightly coupled and need to share resources.
      This pod pattern will be referred to as a `multi-container pod`.

   1. You will seldom manually create pods in Kubernetes. Pods are disposable entities,
      which exists until the pod is terminated, deleted, fails, or is _evicted_.
      Typically, we let a _controller_ handle the creation of pods by giving it a 
      **Pod Template** (AKA Pod Config, Pod Recipe), i.e.:
      ```yaml
      apiVersion: v1
      kind: Pod
      metadata:
         name: myapp-pod
         labels:
            app: myapp
      spec:
         containers:
         - name: myapp-container
            image: busybox
            command: ['sh', '-c', 'echo Hello Kubernetes! && sleep 3600']
            restartPolicy: 
      ```

   1. Kubernetes will work to make sure that your application's current state is the same as 
      it's desired state using the following:
      * Every object has a `status` attribute, which describes the _current state_ of the object.
      * You can declare the `spec` attribute of the object when creating a Kubernetes object, 
      which describes the _desired state_ of the resource. 

   1. The `spec` may contain a `restartPolicy` with the possible values {`Always`, `OnFailure`,
      `Never`} (default=`Always`). Exited Containers that are restarted by the kubelet are restarted with an exponential back-off delay (10s, 20s, 40s …) capped at five minutes, and is reset after ten minutes of successful execution. 

   1. The _kubelet service_ that runs on the worker node hosting the pod has the ability to
      perform, and react to, one of three kinds of **probes** on a container:

      1. `livenessProbe`:  Indicates whether the Container is running. If the liveness probe fails, the kubelet kills the Container, and the Container is subjected to its `restartPolicy`. If a Container does not provide a liveness probe, the default state is Success.
         * When to use: If you’d like your Container to be killed and restarted if a probe fails, then specify a liveness probe, and specify a `restartPolicy` of `Always` or `OnFailure`.
      
      1. `readinessProbe`: Indicates whether the Container is ready to service requests. If the readiness probe fails, the endpoints controller removes the Pod’s IP address from the endpoints of all Services that match the Pod. If a Container does not provide a readiness probe, the default state is `Success`.
         * When to use: If you’d like to start sending traffic to a Pod only when a probe succeeds, specify a readiness probe. 

      1. `startupProbe`: Indicates whether the application within the Container is started. All other probes are disabled if a startup probe is provided, until it succeeds. If the startup probe fails, the kubelet kills the Container, and the Container is subjected to its `restartPolicy`. If a Container does not provide a startup probe, the default state is `Success`.
         * When to use: If your Container usually starts in more than `initialDelaySeconds` + `failureThreshold` × `periodSeconds`, you should specify a startup probe that checks the same endpoint as the liveness probe. You should set `failureThreshold` high enough to allow the Container to start, without changing the default values of the liveness probe. This helps to protect against deadlocks.
   
   1. **Init Containers** are containers that must run to completion, one at a time, before
      anything else is ran. Init Containers can be used for installing build-tools, compiling
      code from source, forcing the pod to wait before starting, cloning a Git repository into
      a volume, etc. Below is an example of InitContainers in a Pod Recipe.
      ```yaml
      apiVersion: v1
      kind: Pod
      metadata:
      name: myapp-pod
      labels:
         app: myapp
      spec:
      containers:
      - name: myapp-container
         image: busybox:1.28
         command: ['sh', '-c', 'echo The app is running! && sleep 3600']
      initContainers:
      - name: init-myservice
         image: busybox:1.28
         command: ['sh', '-c', "until nslookup myservice.$(cat /var/run/secrets/kubernetes.io/serviceaccount/namespace).svc.cluster.local; do echo waiting for myservice; sleep 2; done"]
      - name: init-mydb
         image: busybox:1.28
         command: ['sh', '-c', "until nslookup mydb.$(cat /var/run/secrets/kubernetes.io/serviceaccount/namespace).svc.cluster.local; do echo waiting for mydb; sleep 2; done"]
      ```
      If either `init-myservice` or `init-mydb` fail for any reason, Kubernetes will restart 
      the Pod. These two InitContainers must run to completion before `myapp-container` will
      start.

   1. Any container in a Pod can enable `privileged` mode, which is useful for containers
      attempting to manipulate the network stack, accessing devices, etc... It should be
      noted that whichever container runtime you use, whether it be Docker or a VM, must
      support the concept of a privileged container for this to work.

      >> **VULNERABILITY**: Privileged containers can be used for evil; see
      [Runtimes And the Curse of the Privileged Container](https://brauner.github.io/2019/02/12/privileged-containers.html).


   **Advanced Topics:** 
   * [Pod Readiness Gates](https://kubernetes.io/docs/concepts/workloads/pods/pod-lifecycle/#pod-readiness-gate)
   * [Pod Presets](https://kubernetes.io/docs/concepts/workloads/pods/podpreset/)
   * [Pod Topology Spread Constraits](https://kubernetes.io/docs/concepts/workloads/pods/pod-topology-spread-constraints/)
   * [Disruptions](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/)

   [Back to top](#quick-links)


--- 


   ### Namespaces 
 
   1. A **namespace** is a virtual cluster backed by a physical cluster. Think of it as 
      a way for two teams in the same organization to build separate applications on the
      same hardware without the need to worry about interfering with the other team's
      applications accidently.
   
   1. There are a few things to keep in mind about namespaces:
      * Namespaces provide a scope for names.
      * Resource names must be unique within the namespace; 
         but can be repeated across namespaces.
      * You cannot nest namespaces.
      * A Kubernetes resource can only exist in one namespace.


   1. View existing namespaces with the following command:
      ```bash
      kubectl get namespaces
      #> NAME              STATUS   AGE
      #> default           Active   154m
      #> kube-node-lease   Active   154m
      #> kube-public       Active   154m
      #> kube-system       Active   154m
      ```
      * **default** - The default namespace for objects with no other namespace.
      * **kube-system** - The namespace for objects created by the Kubernetes System.
      * **kube-public** - This namespace is created automatically and is readable by all users
            (including those not authenticated). This namespace is mostly reserved for 
            cluster usage, in case that some resources should be visible and readable 
            publicly throughout the whole cluster. This public aspect is purely 
            convention and is not mandatory.

   1. You can create a namespace with the following:
      ```bash
      kubectl create namespace <your-new-namespace>
      ```
      Your namespace name must:
         * contain at most 63 characters
         * contain only lowercase alphanumeric characters or ‘-’
         * start with an alphanumeric character
         * end with an alphanumeric character

      When you create a service, Kubernetes creates a corresponding DNS entry of the form
      `<service-name>.<namespace-name>.svc.cluster.local`, which means that if a container just uses
         `<service-name>`, it will resolve to the service which is local to a namespace. This is useful
      for using the same configuration across multiple namespaces such as Development, Stagin, and
      Production. If you want to reach across namespaces, you need to use the fully qualified domain
      name (FQDN).

   1. You can delete a namespace using the following, but be careful! This deletes everything in
      the namespace!
      ```bash
      kubectl delete namespace <your-old-namespace>
      ```
      * > **NOTE**: This delete is asynchronous, so you may see the namespace for a little while. 
                     Give it a second!

   1. It is not necessary to use multiple namespaces just to separate slightly different resources,
      such as different versions of the same software: use version labels to distinguish resources 
      within the same namespace. More on that later...

   1. Finally, you can specify the namespace to run a resource in with the `--namespace=<your_namespace>` flag.


   1. As if this isn't confusing enough already, not all objects exist within a namespace. Use the
      following command:
      ```bash
      # In a namespace
      kubectl api-resources --namespaced=true 

      # Not in a namespace
      kubectl api-resources --namespaced=false
      ```

   [Back to top](#quick-links)


--- 
      

   ### Labels and Selectors

   1. **Labels** are key-value pairs that are attached to objects, and are
      intended to be used to specify identifying attributes of objects that are meaningful and
      relevant to users. Use labels to organize, select, or filter
      subsets of objects. Labels can be attached upon the time of creation, and
      subsequently added and modified at any time.

      ```yaml
      "metadata": 
         "labels": 
            "key1" : "value1",
            "key2" : "value2"
      ```

   1. We can query objects using **label selectors**, in one of two ways with the `-l` flag:
      1. _Equality based_ selection uses `==` (which can be shortened to just `=`),
         and `!=` operators just as you might assume. Here's an example query:
         ```bash
         kubectl get pods -l environment=production,tier=!frontend
         ```
         Here, we are querying for all pods with an 'environment' key equal to
         'production', and a 'tier' key not equal to 'frontend' (this includes pods
         where the value of 'tier' is empty). 
      
      1. _Set based_ selection uses `in`, `notin`, and `exists`. Here's an exmple
         query:
         ```bash
         kubectl get pods -l 'environment in (production, qa)'
         ```
         Here, we are querying for all pods whose 'environment' is equal to either
         'production' or 'qa'. Set based expressions are generally more expressive
         than equality based, but take some more getting used to.


   1. Non-identifying information can be added to the metadata values as an
      **annotation**:
      ```yaml
      "metadata": 
         "annotations": 
            "key1": "value1",
            "key2": "value2"
      ```
      How you choose to annotations is purely a matter of taste, but here are some
      examples of what you could use them for:
         * Pointers to logging, monitoring, analytics, or audit repositories.
         * Phone or pager numbers of persons responsible.
         * A link to documentation for the running application.

   1. **Field selectors** let you select resources based on the value of one or
      more resouce fields. Similar to label selectors, we use _equality-based_
      operators (`=`, `==`, and `!=`), and can daisy-chain selectors
      together with commas. However, unlike label selectors, 
      **set-based operators will not work.**
      ```bash
      kubectl get pods --field-selector=status.phase!=Running,spec.restartPolicy=Always
      ```
      Some things to keep in mind about field selectors:
         1. Not all Kubernetes resources have support for field selectors.
         1. **All** resources support `metadata.name` and `metadata.namespace`.

         If you try touse a field selector on a Kubernetes resource that doesn't
         support it, Kubernetes will yell at you:
         ```bash
         kubectl get ingress --field-selector foo.bar=baz
         #> Error from server (BadRequest): Unable to find "ingresses" that match label selector "", field selector "foo.bar=baz": "foo.bar" is not a known field selector: only "metadata.name", "metadata.namespace"
         ```

   1. Here's a table of example labels you might use in production:
      | Key	                     | Description                                                               | Example          | Type   |
      |------------------------------|---------------------------------------------------------------------------|------------------|--------|
      |`app.kubernetes.io/name`      | name of the application                                                   | mysql            | string |
      |`app.kubernetes.io/instance`  | a unique name identifying the instance of an application                  | wordpress-abcxzy | string | 
      |`app.kubernetes.io/version`   | current version of the application (e.g. semantic version, revision hash) | 5.7.21           | string |
      |`app.kubernetes.io/component` | component within the architecture                                         | database         | string |
      |`app.kubernetes.io/part-of`   | name of a higher level application this one is part of                    | wordpress        | string |
      |`app.kubernetes.io/managed-by`| tool being used to manage the operation of an application                 | helm             | string |
      |`app.kubernetes.io/component` | The component within the architecture                                     | database         | string |
      |`app.kubernetes.io/part-of`   | The name of a higher level application this one is part of                | wordpress        | string |
      |`app.kubernetes.io/managed-by`| The tool being used to manage the operation of an application             | helm             | string |

   [Back to top](#quick-links)


---


   ### Replica Sets and Stateful Sets

   1. To provide high availability of 
   https://www.youtube.com/watch?v=GieXzb91I40