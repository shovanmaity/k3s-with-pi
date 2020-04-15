## Configure master node for your Kubernetes cluster

1. SSH into one Pi and change the hostname `/etc/hostname`. After changing it will look like below (You can give a different hostname) -
   ```
   pi@raspberrypi:~ $ cat /etc/hostname
   pi-master
   ```

2. Change the `/etc/hosts` file accordingly. After changing it will look like below -
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

   NOTE :- Using node token we can add another worker node. If you want single node Kubernetes cluster then no need to get this token. 
