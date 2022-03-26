# ubuntu-server-nosnap

This repository contains the required files to successfully install a Ubuntu Server without [snap](https://snapcraft.io/).

Note that the `user-data` file in this repo is a very basic one, see the [autoinstall documentation](https://ubuntu.com/server/docs/install/autoinstall) more advanced customization.

## How to use

The basic idea is tell the installer to fetch these 3 files as autoinstall config: `user-data`, `meta-data`, and `vendor-data`. It works as long as those 3 files are hosted in the same directory / same parent URL.

For example, you can download those files, then host them on your web server. Or, if you are installing Ubuntu Server on a virtual machine, you can host them temporarily on the virtual machine host. You can also directly use this repo, or clone it and point the installation to your own repo.

### Hosting on virtual machine host

The simplest way to do that would be to use Python's built-in HTTP server:

```sh
$ python3 -m http.server 8080
```

We use port 8080 so that the web server can run as a normal user.

Then, when installing, add this to the kernel command line: `autoinstall ds=nocloud-net;s=http://_gateway:8080/`

The special hostname `_gateway` is resolved by `systemd-resolved` in the virtual machine (not the host), and it points to the virtual machine's gateway. So the above example will only work if your virtual machine's network gateway is the host. If you have a custom network setup, this might not work, so just use the host machine's IP address like this: `autoinstall ds=nocloud-net;s=http://10.0.2.55:8080/`

So, if you're using virt-install, you can do this:

```sh
$ virt-install \
  --name my-ubuntu-server-20.04 \
  --ram 2048 \
  --disk pool=default,size=20,bus=virtio,format=qcow2 \
  --vcpus 2 \
  --os-type ubuntu20.04 \
  --network network:default \
  --graphics none \
  --location /srv/iso/ubuntu-20.04.4-live-server-amd64.iso,kernel=casper/vmlinuz,initrd=casper/initrd \
  --extra-args 'console=ttyS0,115200n8 serial autoinstall ds=nocloud-net;s=http://_gateway:8080/'
```

Note: see the [cloud-init documentation](https://cloudinit.readthedocs.io/en/latest/topics/datasources/nocloud.html) for more information about the command line syntax

### Get the files directly from GitHub

With this method, you can use this repo directly, or fork it and use your own repo. Using this repo as an example, append one of these to the kernel command line:
- `autoinstall ds=nocloud-net;s=https://github.com/wpyoga/ubuntu-server-nosnap/raw/master/`
- `autoinstall ds=nocloud-net;s=https://raw.githubusercontent.com/wpyoga/ubuntu-server-nosnap/master/`
- `autoinstall ds=nocloud-net;s=https://tinyurl.com/2p9e88a3/`
- `autoinstall ds=nocloud-net;s=https://tinyurl.com/ubuntu-server-nosnap/`

For example, if you use virt-install, you can do this:

```sh
$ virt-install \
  --name my-ubuntu-server-20.04 \
  --ram 2048 \
  --disk pool=default,size=20,bus=virtio,format=qcow2 \
  --vcpus 2 \
  --os-type ubuntu20.04 \
  --network network:default \
  --graphics none \
  --location /srv/iso/ubuntu-20.04.4-live-server-amd64.iso,kernel=casper/vmlinuz,initrd=casper/initrd \
  --extra-args 'console=ttyS0,115200n8 serial autoinstall ds=nocloud-net;s=https://github.com/wpyoga/ubuntu-server-nosnap/raw/master/'
```

If you are installing with video output, for example if you use Proxmox, or if you are installing on a physical server, then you can specify the extra command line arguments at the GRUB menu.

### Make a custom ISO

With this method, you don't need to modify the kernel command line. To make a custom ISO, see this project: https://github.com/covertsh/ubuntu-autoinstall-generator

Note: it's not my project, I'm not afiliated with the creator, and I've never used it before. But based on my reading of the source code, it seems legit.

## How about Ubuntu Desktop?

This does not work on Ubuntu Desktop.

## How about netinstall?

I haven't tried this.

## Motivation

I simply don't like snap. Just like I don't like Flatpak etc. It's a convoluted mess, and it takes up precious system resources. Also, Linux Mint developers (I'm a Linux Mint user) seem to [dislike snap](https://linuxmint-user-guide.readthedocs.io/en/latest/snap.html), albeit for other reasons.

So I created this repo to help people who wish to install Ubuntu Server without snap, to get all the benefits of Ubuntu (as opposed to Debian) and none of the drawbacks of snap.
