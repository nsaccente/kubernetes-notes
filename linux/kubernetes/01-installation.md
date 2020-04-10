# tl;dr Kubernetes 







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







## Install Kubectl

> Kubectl is the command line tool used to run commands against your Kubernetes cluster.

You must use a kubectl version that is within one minor version difference of
your cluster. For example, a v1.2 client should work with v1.1, v1.2, and v1.3
master. Using the latest version of kubectl helps avoid unforeseen issues.


1. Install kubectl
   ```
   curl -LO https://storage.googleapis.com/kubernetes-release/release/`curl -s https://storage.googleapis.com/kubernetes-release/release/stable.txt`/bin/linux/amd64/kubectl
   ```
   To download a specific version, replace the `$(curl -s
   https://storage.googleapis.com/kubernetes-release/release/stable.txt)` portion of
   the command with the specific version number.

1. Make the kubectl binary executable and move it into your path
   ```
   chmod +x ./kubectl && sudo mv ./kubectl /usr/local/bin/kubectl
   ```

1. Test your install:
   ```
   kubectl version --client
   ```







## Install Minikube

> Minikube is a lightweight Kubernetes implementation that creates a VM on your
> local machine and deploys a simple cluster containing only one node.


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
   ```
   curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
   && chmod +x minikube
   ```
1. Add minikube to your path:
   ```
   sudo install minikube /usr/local/bin/ && rm ./minikube
   ```
1. Verify install replacing the `driver_name` with the hypervisor of your choice
   from 
   [this list](https://kubernetes.io/docs/setup/learning-environment/minikube/#specifying-the-vm-driver). Run the following:
   
   1. ```
      minikube start --driver=<driver_name>
      ```
   1. Once `minikube start` finishes, run:
      ```
      minikube status
      
      # Your output should look something like this:
      #> host: Running
      #> kubelet: Running
      #> apiserver: Running
      #> kubeconfig: Configured
      ```
   1.  If something doesn't seem right, use the following to increase your logging level:
       ```
       minikube -v=9 start
       ```
   1. When all else fails, you can always blow away your minikube environment using:
      ```
      minikube delete
      ``` 
   
   1. Stop minikube with:
      ```
      minikube stop
      ```
   

   








## Resources
1. [Install and Set Up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-linux)

1. [Install Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)

1. [Minikube with --driver=none](https://minikube.sigs.k8s.io/docs/drivers/none/)

1. [Specifying the VM Driver](https://kubernetes.io/docs/setup/learning-environment/minikube/#specifying-the-vm-driver)

1. [Blow Away Your MiniKube Env](https://stackoverflow.com/questions/50769737/troubleshooting-minikube)

> _One of the links broken? Make a [pull request](https://github.com/strickolas/tldr/pulls)!_