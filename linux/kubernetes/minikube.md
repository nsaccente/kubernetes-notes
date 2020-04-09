# tl;dr Kubernetes 

## Install Kubectl
>>>_Minikube is a tool that runs a single-node Kubernetes cluster in a virtual machine on your personal computer._

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

1


## Resources
1. [Install and Set Up kubectl](https://kubernetes.io/docs/tasks/tools/install-kubectl/#install-kubectl-on-linux)

1.  