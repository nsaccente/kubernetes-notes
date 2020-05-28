
# tl;dr Kubernetes 

## Prerequisites
* Be comfortable in [YAML](https://www.youtube.com/watch?time_continue=83&v=cdLNKUoMc6c&feature=emb_title) ✍ 
* Be comfortable with the concept of Docker containers and virtualization
  ([this is a great place to start](https://www.youtube.com/watch?v=TvnZTi_gaNc))

If you don't meet both of these prereq's, give this videos a watch, do your own
research, and come back later!

## Table of Contents
1. [Install Kubectl](#install-kubectl)




Welcome to a **No Nonsense** guide to Kubernetes. I promise to do my best to
introduce material in a logical manner, provide production-centric examples,
pepper in memes and gifs to keep things feeling fresh, and explain 
complicated material as simply as possible. 


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