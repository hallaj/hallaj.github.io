---
title: minikube on Fedora 34 with podman
date: 2021-05-15
tags:
  - fedora
  - minikube
  - podman
---

A while ago, I had encountered issues while trying to run [minikube](https://minikube.sigs.k8s.io/docs/) on [Fedora 33](https://getfedora.org/) with [podman](https://podman.io/). Strangely enough I did not keep notes on what I did to get it to work and when I did a fresh installation on [Fedora 34](https://getfedora.org/), the old error haunted me back again.

Leaning from the past mistake, I suppose it's time to start taking notes on what's the error encountered and what did I do to fix it.

Below are the steps I've taken to get it installed.

1. Step 1; Follow the installation guide of minikube from their [Get started!](https://minikube.sigs.k8s.io/docs/start/) page.
1. Step 2; Ensure podman's installed on our Fedora 34
    ```
    $ sudo dnf -y install podman
    ```
1. Step 3; Since [minikube](https://minikube.sigs.k8s.io/docs/) uses `podman` with passwordless `sudo`, we'll need to ensure our user has the permission to run `podman` without sudo`sudo`. The way I do it is to create a new `podman` group and have my user added to it.
    ```
    $ sudo groupadd podman
    $ sudo gpasswd -a $USER podman
    $ newgrp podman  # use this if you're lazy to re-login to enable your new group
    $ sudo cat <<eof >/etc/sudoers.d/podman
    %podman ALL=(ALL) NOPASSWD: /usr/bin/podman
    eof
    ```
1. Step 4; By using the guide from [minikube's podman driver](https://minikube.sigs.k8s.io/docs/drivers/podman/), the recommended way to start `minikube` is to execute `minikube start --driver=podman --container-runtime=cri-o`. Unfortunately for me, if I use just that perimeter, somehow the cluster would not launch.
    ```
    $ minikube start --driver=podman --container-runtime=cri-o
    😄  minikube v1.20.0 on Fedora 34
    ✨  Using the podman driver based on user configuration
    ..
    ..
    stderr:
            [WARNING Swap]: running with swap on is not supported. Please disable swap
            [WARNING Service-Kubelet]: kubelet service is not enabled, please run 'systemctl enable kubelet.service'
    error execution phase wait-control-plane: couldn't initialize a Kubernetes cluster
    To see the stack trace of this error execute with --v=5 or higher

    💡  Suggestion: Check output of 'journalctl -xeu kubelet', try passing --extra-config=kubelet.cgroup-driver=systemd to minikube start
    🍿  Related issue: https://github.com/kubernetes/minikube/issues/4172
    ```
    After some digging, it would appear that because we're running `btrfs`, the mapper device was carried over into podman but couldn't be located. Thankfully, `minikube` has something to counter that - `"LocalStorageCapacityIsolation=false"`, so if we use the following, we should be able to deploy a minikube cluster without issues
    ```
    $ minikube start --driver=podman --container-runtime=cri-o --feature-gates="LocalStorageCapacityIsolation=false"
    😄  minikube v1.20.0 on Fedora 34
    ..
    ..
    🌟  Enabled addons: storage-provisioner, default-storageclass
    🏄  Done! kubectl is now configured to use "minikube" cluster and "default" namespace by default
    ```
    You could even confirm by running `kubectl get pods -A` to confirm it's running
    ```
    $ kubectl get pods -A
    NAMESPACE     NAME                               READY   STATUS    RESTARTS   AGE
    kube-system   coredns-74ff55c5b-hjfct            1/1     Running   0          40s
    kube-system   etcd-minikube                      0/1     Running   0          50s
    kube-system   kindnet-9g6bn                      1/1     Running   0          40s
    kube-system   kube-apiserver-minikube            1/1     Running   0          50s
    kube-system   kube-controller-manager-minikube   0/1     Running   0          50s
    kube-system   kube-proxy-fkmjz                   1/1     Running   0          40s
    kube-system   kube-scheduler-minikube            0/1     Running   0          50s
    kube-system   storage-provisioner                1/1     Running   0          55s
    ```
