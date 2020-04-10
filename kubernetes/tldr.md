# tl;dr Kubernetes

## Quick Links
* [Visit the Kubectl Wiki](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)
* [Read the Kubectl Book](https://kubectl.docs.kubernetes.io/)
* [Visit the Minikube Wiki](https://minikube.sigs.k8s.io/docs/)


## Table of Contents
1. [ Introduction ](#introduction)
1. [ Install Kubectl ](#install-kubectl)
1. [ Install Minikube ](#install-minikube)
1. [ Minikube Cookbook ](#minikube-cookbook)
1. [ Concepts ](#concepts)
   1. [ Objects ](#concepts-objects)
   1. [ Namespaces ](#concepts-namespaces)


----


<a name="introduction"></a>
## Introduction
Kubernetes (sometimes written as k8s) coordinates a highly available cluster
of computers to work as a single unit by automating the distribution and
scheduling of application containers. To accomplish this, Kubernetes uses two
_"resources"_:

  * __Master__: Resposible for managing the cluster; i.e. scheduling, scaling
    applications, rolling out updates, and maintaining applications' desired
    state.
  * __Worker__: A node is a VM or a physical computer that serves as a worker
    machine in a Kubernetes cluster. 
    
    
    Each node has a __Kubelet__, which is an agent
    for managing the node and communicating with the Kubernetes master. 

When you deploy applications on Kubernetes, you tell the master to start the application containers. The master schedules the containers to run on the cluster's nodes. The nodes communicate with the master using the Kubernetes API, which the master exposes. End users can also use the Kubernetes API directly to interact with the cluster.

>> **For production**: A Kubernetes cluster should have at least 3 workers. 

<a href="#top">Back to top</a>


----


<a name="install-kubectl"></a>
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

<a href="#top">Back to top</a>


----


<a name="install-minikube"></a>
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

<a href="#top">Back to top</a>


----


<a name="minikube-cookbook"></a>
## Minikube Cookbook

1. Start a local Kubernetes cluster using the `minikube` bootstrap command:
   ```bash
   minikube start
   ```

1. We will rely on `kubectl` to interact with the minikube cluster we've spun up.
   Let's use `kubectl run <name-whatever-you-want>`, which provides a way to run a Docker
   image on our Kubernetes cluster. 
   ```bash
   kubectl run hello-minikube --image=gcr.io/google_containers/echoserver:1.4 --port=8080
   ```

1. It's going to take some time for the pod to be downloaded and started, but we can check the 
   status of our pod using:
   ```bash
   kubectl get pod
   #> NAME             READY   STATUS              RESTARTS   AGE
   #> hello-minikube   0/1     ContainerCreating   0          4m33s
   #> back2thefuture   1/1     Running             0          8m16s
   ```
   As you can see by `hello-minikube`'s READY value of 0/1, it's NOT ready. The container named
   `back2thefuture` is what you should see when your container is up and running.

   <a href="#top">Back to top</a>


--- 

<a name="concepts"></a>
## Concepts 

<a name="concepts-objects"></a>
1. **Objects**
   1. _Objects_ are persistent entities that Kubernetes uses to represent the state 
      of the cluster; specifically:
      * What containerized applications are running (and on which nodes).
      * The resources available to those applications.
      * The policies around how those applications behave; e.g. restart policies, upgrades, 
        and fault-tolerance.

   1. Kubernetes will work to make sure that your application's current state is the same as 
      it's desired state. 
      * Every object has a **status** attribute, which describes the _current state_ of the object.
      * You can declare the **spec** attribute of the object when creating a Kubernetes object, 
      which describes the _desired state_ of the resource. 

   <a href="#top">Back to top</a>


<a name="concepts-namespaces"></a>
1. **Namespaces**
   1. A _namespace_ is a virtual cluster backed by a physical cluster. Think of it as a way for 
      two teams in the same organization to build separate applications on the same hardware 
      without the need to worry about interfering with the other team's applications accidently.
      There are a few things to keep in mind about namespaces:
      1. Namespaces provide a scope for names.
      1. Resource names must be unique within the namespace; but can be repeated across namespaces.
      1. You cannot nest namespaces.
      1. A Kubernetes resource can only exist in one namespace.
   
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
      
<a name="concepts-namespaces"></a>
1. **Labels and Selectors**

