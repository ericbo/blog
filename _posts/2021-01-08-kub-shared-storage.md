---
title:  "Setting up Shared Storage on Multiple Kubernetes Nodes"
mathjax: false
layout: post
tags: [SMB, CIFS, Kubernetes]
---

# Overview
Over the holidays I spent much of my time working on my Homelab. My goal for 2021 is to move
all my containerized services over to a HA Kubernetes cluster. Currently this cluster is running
on a single bare-metal server. However in the future I would like this cluster to be spread
across multiple physical servers to prevent any downtime due to hardware upgrades or maintances.

The issue with having a cluster of servers running my containerized apps is sharing any persistent
data between them. Because we have multiple Kubernetes client nodes, with no guarentee as to which 
node will be running the container, I set out to find the simplest shared storage solution possible.



# Using a CIFS/SMB 3.1.1
Given that I already use SMB for sharing my media between my desktop, media server and other services
I figured it would be a great solution for solving my container storage problem. Given that there is
currently no segregation on my network, everything on my LAN is within the same subnet/vlan, it felt
slightly safer/easier to go with SMB over NFS. This is because SMB supports end-to-end encryption and 
supports authentication with very little effort.

# Setting up a SMB Share on Each Client Node
I will be assuming you already have an SMB share as most NAS/SAN solutions make it
relatively straightforward to do so. If it is your first time doing so, I recommened assigning a
single non-root user to that share and noting down that users UID and GUI. Typically your first non-root
user will have a UID/GUI of 1000/1000.

#### Installing dependencies & creating the mount location
I am using Ubuntu 20.04 in this example, however this process should be relativel the same for most
Linux/Unix based distributions.  First off we need to install the `cifs-utils` package and create the
mount location for the share. Ubuntu documentation tends to prefer using the `/media` folder for mounting
disk, however it is not uncommon for other distributions to use `/mnt`. Either folder works, I'll be using
`/media` for this example.

```bash
sudo apt-get update
sudo apt-get install -y cifs-utils
sudo mkdir /media/kube
```

#### Mounting the share on boot
By default, Ubuntu will read the `/etc/fstab` file during the boot process to mount all disks/shares. So 
to add my share I simply append the the following to the file:

```text
//192.168.0.200/kube /media/kube cifs uid=1000,gid=1000,credentials=/home/adminuser/.smb,iocharset=utf8,vers=3.1.1,noperm,seal 0 0
```

Be sure to replace the IP with you NAS/SAN IP address. Then replace the uid/gid of the NAS users uid/gid.
Finally replace `/home/adminuser` with the home path of your systems sudo enable user, if you are using 
ubuntu typically this is the user created during the initial install process.

#### Adding the user credentials
This can be skipped and the credentials flag above can be ommited if your SMB share does not require user
authentication. However, if you are using user authentication it is recommended that you add the username/password
to a folder with limited file rights. This can be done with the following commands:

```bash
cat <<EOT >> /home/adminuser/.smb
username=myuser
password=mypassword
EOT
chmod 600 /home/adminuser/.smb
```

Be sure to replace the filepath, username and password with your own ones respectively.

Now with each Kuberneties client node containing the same paths pointing to the same share, I was ready to start
mapping my volumes to that share.

## Running into SQL issues
The first issue I ran into was with the container service `linuxserver/heimdall`. When looking at
the logs for this container I saw multiple `database is locked` errors. This issues was fixed easily enough
by adding the `nobrl` flag to my fstab entry. This seemed to have solved the issue for that particular container.

## Running into MongoDB issues
The next container I tried to use was `linuxserver/unifi-controller`. Once again I ran into issues during the
container setup and the log outputs were giving me loads of MongoDB issues. It seemed the more I looked into
solving database related issues on an SMB share, the more it was discourage to do so. It was at this point that
I decided to abandon the idea of using SMB shares for my Kuberneties setup. This solution works for many containerized
servers, however I was aiming to find a single solution that I could set and forget.

# Going with HA block storage solution (Longhorn)
I would of prefered to keep my current Kuberneties cluster as simple as possible, however experiencing a few headaches
with SMB, I opted to go with a persistant storage solution offered by Rancher. I will not go over the setup process
of Longhorn in this post. However I will mention that I plan on adding two more client nodes to my Kuberneties cluster
that will be solely dedicated to providing storage to my cluster. These nodes will have iSCSI disks that will point to
my slower mechanical RAID array as most of my VMs have already consumed most of my flash storage.
