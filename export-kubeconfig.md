## Export kubeconfig of your Kubernetes cluster to access it remotely

1. Kubeconfig present in master node in `/etc/rancher/k3s/k3s.yaml` file. Copy it in your local machine. Update the server IP.
    ```yaml
    apiVersion: v1
    clusters:
    - cluster:
        server: https://127.0.0.1:6443
    ```

    ```yaml
    apiVersion: v1
    clusters:
    - cluster:
        server: https://192.168.1.34:6443
    ```

2. Get node and pod using exported kubeconfig.
    ```bash
    shovan@probot:~$ kubectl get node --kubeconfig=kubeconfig
    NAME          STATUS   ROLES    AGE     VERSION
    pi-master     Ready    master   4h31m   v1.17.4+k3s1
    pi-worker-0   Ready    <none>   4h4m    v1.17.4+k3s1
    pi-worker-1   Ready    <none>   3h43m   v1.17.4+k3s1
    pi-worker-2   Ready    <none>   3h24m   v1.17.4+k3s1

    shovan@probot:~$ kubectl get pod -A --kubeconfig=kubeconfig
    NAMESPACE     NAME                                      READY   STATUS    RESTARTS   AGE
    kube-system   metrics-server-6d684c7b5-jv5gp            1/1     Running   0          4h31m
    kube-system   coredns-6c6bb68b64-7skmn                  1/1     Running   0          4h31m
    kube-system   local-path-provisioner-58fb86bdfd-7ffj7   1/1     Running   5          3h20m
    ```
