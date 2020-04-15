## Install Raspbian and enable shh in Raspberry Pi 3

1. Download the `Raspbian` image from [here](https://www.raspberrypi.org/downloads/raspbian).

2. I am using `Etcher` for writing the image to the SD card. Download `Etcher` from [here](https://etcher.io).

3. Once the flash is complete, enable ssh in the Pi. For enabling SSH in the Pi, create a file named `ssh` in the root of the boot directory.
   ```bash
   cd /media/shovan/boot
   touch ssh
   cat ssh
   ```

   NOTE :- Mount path can be different in your setup.

4. To configure the Pi to connect to a WiFi network, create a file called `wpa_supplicant.conf` in the root of the boot directory with the below contents.
   ```config
   country=IN
   update_config=1
   ctrl_interface=/var/run/wpa_supplicant

   network={
      scan_ssid=1
      ssid="pify"
      psk="test@pify"
   }
   ```

   NOTE :- Some of the values in the configuration should be replaced in your configuration. Here is one example [file](https://github.com/shovanmaity/k3s-with-pi/blob/master/config/wpa_supplicant.conf).

5. Eject the SD card and insert it into the Pi. Power up the Pi and wait for a minute to boot.

6. From your laptop, use `Nmap` to scan your local network. If you don’t have `Nmap`, install it using `sudo apt-get install nmap`. Using `Nmap` you will be able to find the IP of your Pi.
   ```
   shovan@probot:~$ sudo nmap -sn 192.168.1.0/24
   [sudo] password for shovan:

   Starting Nmap 7.60 ( https://nmap.org ) at 2020-04-11 17:20 IST
   Nmap scan report for _gateway (192.168.1.1)
   Host is up (0.0025s latency).
   MAC Address: XX:XX:XX:XX:XX:XX (Unknown)
   Nmap scan report for 192.168.1.33
   Host is up (0.049s latency).
   MAC Address: XX:XX:XX:XX:XX:XX (OnePlus Tech (Shenzhen))
   Nmap scan report for 192.168.1.34
   Host is up (0.032s latency).
   MAC Address: XX:XX:XX:XX:XX:XX (Raspberry Pi Foundation)
   Nmap scan report for 192.168.1.35
   Host is up (0.010s latency).
   MAC Address: XX:XX:XX:XX:XX:XX (Raspberry Pi Foundation)
   Nmap scan report for 192.168.1.36
   Host is up (0.065s latency).
   MAC Address: XX:XX:XX:XX:XX:XX (Raspberry Pi Foundation)
   Nmap scan report for 192.168.1.37
   Host is up (-0.069s latency).
   MAC Address: XX:XX:XX:XX:XX:XX (Raspberry Pi Foundation)
   Nmap scan report for traefik.local (192.168.1.15)
   Host is up.
   Nmap scan report for pi-master.local (192.168.1.34)
   Host is up (0.020s latency).
   Nmap done: 256 IP addresses (7 hosts up) scanned in 6.07 seconds
   ```

   NOTE :- CIDR can be different for your network.

7. Once you find the IP address of your Pi, SSH into it as `pi` user with the default password `raspberry`.
   ```bash
   ssh pi@192.168.1.34

   ssh pi@192.168.1.35

   ssh pi@192.168.1.36

   ssh pi@192.168.1.37
   ```

   NOTE :- IP of Pi can be different in your network.
