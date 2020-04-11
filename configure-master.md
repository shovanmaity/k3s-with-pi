## Configure master node for your Kubernetes cluster

1. SSH into one Pi and change the hostname `/etc/hostname`. After changing it will look like below -
   ```
    pi@raspberrypi:~ $ cat /etc/hostname 
    pi-master
   ```

2. Also change the `/etc/hosts` file. After changing it will look like below -
   ```
    pi@pi-master:~ $ cat /etc/hosts
    127.0.0.1   localhost
    ::1         localhost ip6-localhost ip6-loopback
    ff02::1     ip6-allnodes
    ff02::2     ip6-allrouters

    127.0.1.1   pi-master
   ```
3. Reboot the Pi and ssh again.

4. Install k3s. I am installing k3s without `traefik` and `servicelb`
   ```bash
    export INSTALL_K3S_EXEC=" --no-deploy servicelb --no-deploy traefik"
    curl -sfL https://get.k3s.io | sh -
   ```

   ```
    pi@pi-master:~ $ curl -sfL https://get.k3s.io | sh -
    [INFO]  Finding latest release
    [INFO]  Using v1.17.4+k3s1 as release
    [INFO]  Downloading hash https://github.com/rancher/k3s/releases/download/v1.17.4+k3s1/sha256sum-arm.txt
    [INFO]  Downloading binary https://github.com/rancher/k3s/releases/download/v1.17.4+k3s1/k3s-armhf
    [INFO]  Verifying binary download
    [INFO]  Installing k3s to /usr/local/bin/k3s
    [INFO]  Creating /usr/local/bin/kubectl symlink to k3s
    [INFO]  Creating /usr/local/bin/crictl symlink to k3s
    [INFO]  Creating /usr/local/bin/ctr symlink to k3s
    [INFO]  Creating killall script /usr/local/bin/k3s-killall.sh
    [INFO]  Creating uninstall script /usr/local/bin/k3s-uninstall.sh
    [INFO]  env: Creating environment file /etc/systemd/system/k3s.service.env
    [INFO]  systemd: Creating service file /etc/systemd/system/k3s.service
    [INFO]  systemd: Enabling k3s unit
    Created symlink /etc/systemd/system/multi-user.target.wants/k3s.service → /etc/systemd/system/k3s.service.
    [INFO]  systemd: Starting k3s
   ```

5. Verify the k3s service using `sudo systemctl status k3s --no-pager`
   ```
    pi@pi-master:~ $ sudo systemctl status k3s --no-pager
    ● k3s.service - Lightweight Kubernetes
    Loaded: loaded (/etc/systemd/system/k3s.service; enabled; vendor preset: enabled)
    Active: active (running) since Sat 2020-04-11 09:11:13 BST; 4h 17min ago
        Docs: https://k3s.io
    Process: 846 ExecStartPre=/sbin/modprobe br_netfilter (code=exited, status=0/SUCCESS)
    Process: 848 ExecStartPre=/sbin/modprobe overlay (code=exited, status=0/SUCCESS)
    Main PID: 850 (k3s-server)
        Tasks: 48
    Memory: 384.4M
    CGroup: /system.slice/k3s.service
            ├─ 850 /usr/local/bin/k3s server --no-deploy servicelb --no-deploy traefik
            ├─ 906 containerd -c /var/lib/rancher/k3s/agent/etc/containerd/config.toml -a /run/k3s/containerd/contai…
            ├─1300 /var/lib/rancher/k3s/data/0d6b3c3bdce515346f525129585a7d743d4b317feac3b9c71df2fbffbb009b04/bin/co…
            ├─1331 /var/lib/rancher/k3s/data/0d6b3c3bdce515346f525129585a7d743d4b317feac3b9c71df2fbffbb009b04/bin/co…
            ├─1379 /pause
            ├─1396 /pause
            ├─1694 /metrics-server
            └─1763 /coredns -conf /etc/coredns/Corefile

    Apr 11 13:21:19 pi-master k3s[850]: E0411 13:21:19.621182     850 machine.go:331] failed to get cache infor…irectory
    Apr 11 13:21:27 pi-master k3s[850]: I0411 13:21:27.885249     850 controller.go:107] OpenAPI AggregationCon…s.k8s.io
    Apr 11 13:22:27 pi-master k3s[850]: I0411 13:22:27.915193     850 controller.go:107] OpenAPI AggregationCon…s.k8s.io
    Apr 11 13:23:27 pi-master k3s[850]: I0411 13:23:27.980223     850 controller.go:107] OpenAPI AggregationCon…s.k8s.io
    Apr 11 13:24:28 pi-master k3s[850]: I0411 13:24:28.009842     850 controller.go:107] OpenAPI AggregationCon…s.k8s.io
    Apr 11 13:25:28 pi-master k3s[850]: I0411 13:25:28.039827     850 controller.go:107] OpenAPI AggregationCon…s.k8s.io
    Apr 11 13:26:19 pi-master k3s[850]: E0411 13:26:19.617286     850 machine.go:331] failed to get cache infor…irectory
    Apr 11 13:26:28 pi-master k3s[850]: I0411 13:26:28.103989     850 controller.go:107] OpenAPI AggregationCon…s.k8s.io
    Apr 11 13:27:28 pi-master k3s[850]: I0411 13:27:28.133388     850 controller.go:107] OpenAPI AggregationCon…s.k8s.io
    Apr 11 13:28:28 pi-master k3s[850]: I0411 13:28:28.204906     850 controller.go:107] OpenAPI AggregationCon…s.k8s.io
    Hint: Some lines were ellipsized, use -l to show in full.
   ```

6. Verify the node by using `sudo k3s kubectl get node`.
   ``` 
    pi@pi-master:~ $ sudo k3s kubectl get node
    NAME        STATUS   ROLES    AGE   VERSION
    pi-master   Ready    master   25s   v1.17.4+k3s1
   ```
7. Get the node token by using `sudo cat /var/lib/rancher/k3s/server/node-token`
   ```
    pi@pi-master:~ $ sudo cat /var/lib/rancher/k3s/server/node-token
    K101d88e0cbe0f988377eac34e6d7fdf14de156836a63a1869b78385b7a2dba9cba::server:8ae7278428a3e50fa2fea2d376455444
   ```

8. Get the kubeconfig by using `sudo cat /etc/rancher/k3s/k3s.yaml`
   ```yaml
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: {cert-data}
        server: https://127.0.0.1:6443
    name: default
    contexts:
    - context:
        cluster: default
        user: default
    name: default
    current-context: default
    kind: Config
    preferences: {}
    users:
    - name: default
    user:
        password: {password}
        username: admin
   ```

9. You can copy it in your local machine. Update the server IP. After update it will look like this -
   ```yaml
    apiVersion: v1
    clusters:
    - cluster:
        certificate-authority-data: {cert-data}
        server: https://192.168.1.34:6443
    name: default
    contexts:
    - context:
        cluster: default
        user: default
    name: default
    current-context: default
    kind: Config
    preferences: {}
    users:
    - name: default
    user:
        password: {password}
        username: admin
   ```

10. Then try to get node and pod using this kubeconfig.
    ```bash
    shovan@probot:~/Downloads$ kubectl get node --kubeconfig=kubeconfig
    NAME          STATUS   ROLES    AGE     VERSION
    pi-worker-0   Ready    <none>   4h4m    v1.17.4+k3s1
    pi-worker-1   Ready    <none>   3h43m   v1.17.4+k3s1
    pi-master     Ready    master   4h31m   v1.17.4+k3s1
    pi-worker-2   Ready    <none>   3h24m   v1.17.4+k3s1

    shovan@probot:~/Downloads$ kubectl get pod -A --kubeconfig=kubeconfig
    NAMESPACE     NAME                                      READY   STATUS    RESTARTS   AGE
    kube-system   metrics-server-6d684c7b5-jv5gp            1/1     Running   0          4h31m
    kube-system   coredns-6c6bb68b64-7skmn                  1/1     Running   0          4h31m
    kube-system   local-path-provisioner-58fb86bdfd-7ffj7   1/1     Running   5          3h20m
    ```
