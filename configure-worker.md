## Configure worker node(s) for your Kubernetes cluster

This is required only if You want to create a multi-node Kubernetes cluster. You need to boot another Pi and follow the below steps. You can add as many worker nodes as you want. Only the hostname will be different.

1. SSH into one Pi and change the hostname `/etc/hostname`. After changing it will look like below (You can give a different hostname) -
   ```
   pi@raspberrypi:~ $ cat /etc/hostname
   pi-worker-0
   ```

2. Change the `/etc/hosts` file accordingly. After changing it will look like below -
   ```
   pi@pi-master:~ $ cat /etc/hosts
   127.0.0.1     localhost
   ::1           localhost ip6-localhost ip6-loopback
   ff02::1       ip6-allnodes
   ff02::2       ip6-allrouters

   127.0.1.1     pi-worker-0
   192.168.1.34  pi-master
   ```

   NOTE :- I am using a domain name for master IP. If IP of master got changed I need to update `/etc/hosts` file only.

3. Reboot the Pi and ssh again.

4. Install k3s agent.
   ```bash
   export K3S_URL="https://pi-master:6443"
   export K3S_TOKEN="K101d88e0cbe0f988377eac34e6d7fdf14de156836a63a1869b78385b7a2dba9cba::server:8ae7278428a3e50fa2fea2d376455444"
   curl -sfL https://get.k3s.io | sh -
   ```

   NOTE :- `K3S_TOKEN` will be your node token.

   ```
   pi@pi-worker-0:~ $ curl -sfL https://get.k3s.io | sh -
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
   [INFO]  Creating uninstall script /usr/local/bin/k3s-agent-uninstall.sh
   [INFO]  env: Creating environment file /etc/systemd/system/k3s-agent.service.env
   [INFO]  systemd: Creating service file /etc/systemd/system/k3s-agent.service
   [INFO]  systemd: Enabling k3s-agent unit
   Created symlink /etc/systemd/system/multi-user.target.wants/k3s-agent.service → /etc/systemd/system/k3s-agent.service.
   [INFO]  systemd: Starting k3s-agent
   ```

5. Verify the k3s-agent service using `sudo systemctl status k3s-agent --no-pager`

6. Verify the node by using `sudo k3s kubectl get node` from your Kubernetes master or from local machine.
   ``` 
   pi@pi-master:~ $ sudo k3s kubectl get node
   NAME          STATUS   ROLES    AGE   VERSION
   pi-master     Ready    master   3m   v1.17.4+k3s1
   pi-worker-0   Ready    <none>   12s   v1.17.4+k3s1
   ```
