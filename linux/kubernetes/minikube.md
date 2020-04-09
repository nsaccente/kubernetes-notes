# tl;dr Kubernetes 

## Install Kubectl

> _Kubectl_ is the command line tool used to run commands against your Kubernetes cluster.

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

>_Minikube is a tool that runs a single-node Kubernetes cluster in a virtual machine on your personal computer._

1. Install either [KVM](https://www.linux-kvm.org/page/Main_Page) (which also
   uses QEMU) or [Virtualbox](https://www.virtualbox.org/wiki/Downloads). It's
   purely a matter of personal preference (but VirtualBox is superior IMO).
   1. If you don't want to use a VM, use the `--driver=none` flag:
      * Minikube also supports a `--driver=none` option that runs the Kubernetes
      components on the host and not in a VM. Using this driver requires
      [Docker](https://www.docker.com/products/docker-desktop) and a Linux
      environment but not a hypervisor. Do not install Docker using snap, as it
      will more than likely be outdated.
      * > **Caution**: The none VM driver can result in security and data loss
          issues. Before using --driver=none, consult 
          [this documentation](https://minikube.sigs.k8s.io/docs/drivers/none/) 
          for more information.
1. Now, install Minikube via
   ```
   curl -Lo minikube https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64 \
   && chmod +x minikube
   ```
1. Add minikube to your path
   ```
   sudo install minikube /usr/local/bin/ && rm ./minikube
   ```
1. Verify install replacing the `driver_name` with the hypervisor of your choice from 
   [this list](https://kubernetes.io/docs/setup/learning-environment/minikube/#specifying-the-vm-driver).
   
   1. ```
      minikube start --driver=<driver_name>
      ```
   1. Once `minikube start` finishes, run 
      ```
      minikube status
      
      # Your output should look something like this:
      #> host: Running
      #> kubelet: Running
      #> apiserver: Running
      #> kubeconfig: Configured
      ```
   1. 


## Resources
1. [Install and Set Up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-linux)

1. [Install Minikube](https://kubernetes.io/docs/tasks/tools/install-minikube/)

1. [Minikube with --driver=none](https://minikube.sigs.k8s.io/docs/drivers/none/)

1. [Specifying the VM Driver](https://kubernetes.io/docs/setup/learning-environment/minikube/#specifying-the-vm-driver)

1. 

> _One of the links broken? Make a [pull request](https://github.com/strickolas/tldr/pulls)!_