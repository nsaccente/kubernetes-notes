# tl;dr Kubernetes

## Quick Links
* [Visit the Kubectl Wiki](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)
* [Visit the Minikube Wiki](https://minikube.sigs.k8s.io/docs/)


## Table of Contents
1. [ Introduction ](#introduction)
1. [ Install Kubectl ](#install-kubectl)
1. [ Install Minikube ](#install-minikube)
1. [ Minikube Cookbook ](#minikube-cookbook)


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
    machine in a Kubernetes cluster. Each node has a __Kubelet__, which is an agent
    for managing the node and communicating with the Kubernetes master. 

When you deploy applications on Kubernetes, you tell the master to start the application containers. The master schedules the containers to run on the cluster's nodes. The nodes communicate with the master using the Kubernetes API, which the master exposes. End users can also use the Kubernetes API directly to interact with the cluster.

>> **For production**: A Kubernetes cluster should have at least 3 workers. 

<a href="#top">Back to top</a>


----


<a name="install-kubectl"></a>
## Install Kubectl

**[Visit the Kubectl Wiki](https://kubernetes.io/docs/reference/generated/kubectl/kubectl-commands)**
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
   image on our Kubernetes cluster. We are pulling version 1.4 of the image at specified link,
   and telling the container to serve on port 8080.
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
   
1. 
