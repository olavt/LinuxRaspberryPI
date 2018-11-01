## Perform these steps to intstall / configure Linux and c container runtime (Moby) on a RaspBerry PI 3

### Prepare the Raspberry PI 3 Operating System on a Micro SD card

On a computer download the Linux operating system for Raspberry PI 3. I'm using Raspbian Stretch Lite, which you can dowload from [The Raspberry Pi Foundation Raspbian Download Page](https://www.raspberrypi.org/downloads/raspbian/).

Burn the downloaded operating system image to the Micro SD card. You will find more details in this [Installation Guide](https://www.raspberrypi.org/documentation/installation/installing-images/README.md)

### Enable SSH

SSH is diasabled by Raspbian by default. If you want to logon remotely using SSH, you need to ogon to the Raspberry PI using a screen / keyboard connected directly to the hardware and enable SSH using the raspi-config utility:

```
sudo raspi-config
```
You will find the SSH option under "5 Interfacing Options".

You may also want to change the local timezone. The "Change Timezone" option is under "4 Localisation Options".

### Update the Raspbian OS

```
$ sudo apt-get update
$ sudo apt-get upgrade
```

### Add a new user and remove the default user

It's s good idea to create a new user for you on the Raspberry PI and remove the default user.

Add a new user:
```
$ sudo adduser <new-username>
```

Add the new user to sudoers:
```
$ sudo usermod -aG sudo <new-username>
```

### Add required kernel command line parameters for Docker / Kubernetes

Edit `/boot/cmdline.txt`:

```
$ sudo nano /boot/cmdline.txt
```

Add the following to the end of the existing line:

```
cgroup_enable=cpuset cgroup_enable=memory cgroup_memory=1
```

### Configure Networking

To set static IP addresses for the Raspberry PI cluster nodes. Edit the /etc/dhcpcd.conf file:

```
$ sudo nano /etc/dhcpcd.conf
```

Add the following to the bottom of the file:

```
interface=eth0
static ip_address=192.168.1.11/24
static routers=192.168.1.1
static domain_name_servers=8.8.8.8
```
The addresses above are just examples. Replace with the approperiate addresses for your environment.

### Change hostname

Invoke the raspi-config utility to change the hostname:
```
sudo raspi-config
```

You will find the option to change hostname under "2 Network Options".

### Reboot the Raspberry PI
```
$ sudo reboot
```

### Logon using the new user

Logon to your Raspberry PI using the new user you created.

### Remove the default user

```
$ sudo userdel -r pi
```

## Install the container runtime (Moby)


### Download and install the moby-engine

```
$ curl -L https://aka.ms/moby-engine-armhf-latest -o moby_engine.deb && sudo dpkg -i ./moby_engine.deb
```

```
### Download and install the moby-cli
$ curl -L https://aka.ms/moby-cli-armhf-latest -o moby_cli.deb && sudo dpkg -i ./moby_cli.deb
```

### Run apt-get fix
```
$ sudo apt-get install -f
```