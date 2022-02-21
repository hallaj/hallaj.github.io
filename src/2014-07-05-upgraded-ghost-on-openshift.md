---
title: Upgraded Ghost on OpenShift
date: 2014-07-05
tags:
  - ghost
  - openshift
---

I've upgraded my Ghost installation on OpenShift to 0.4.2 now and I thought I'd share the steps here, just in case if anyone ever needs it as a reference.

1. Clone your existing OpenShift repository:

    ```shell
    git clone <openshift_repository> <path>
    ```
1. Change directory to the checkout path:

    ```shell
    cd <path>
    ```
1. Add the Ghost OpenShift quickstart repository:

    ```shell
    git remote add ghost_openshift https://github.com/openshift-quickstart/openshift-ghost-quickstart.git
    ```
1. Pull the latest changes from it:

    ```shell
    git pull ghost_openshift master
    ```
1. Push the changes back into your Ghost repository in OpenShift:

    ```shell
    git push origin
    ```
Now, all you need to do is wait for it to finish building and updating itself. I mean, how it easy was it huh? ;)

Happy Ghosting :)
