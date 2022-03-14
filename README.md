# ubuntu-server-nosnap

This repository contains the required files to successfully install a Ubuntu Server without [snap](https://snapcraft.io/).

Note that the `user-data` file in this repo is a very basic one, see the [autoinstall documentation](https://ubuntu.com/server/docs/install/autoinstall) more advanced customization.

## How to use

If you're using libvirt, first clone this repo, make any changes you want, then host the files over http. An easy way to do that would be:

```sh
$ python3 -m http.server 8080
```

For virt-install, add this to the kernel command line: `autoinstall ds=nocloud-net;s=http://_gateway:8080/`

For example:

```sh
$ virt-install \
--name ubuntu-server-20.04 \
--ram 2048 \
--disk pool=default,size=20,bus=virtio,format=qcow2 \
--vcpus 2 \
--os-type ubuntu20.04 \
--network network:default \
--graphics none \
--location /srv/iso/ubuntu-20.04.4-live-server-amd64.iso,kernel=casper/vmlinuz,initrd=casper/initrd \
--extra-args 'console=ttyS0,115200n8 serial autoinstall ds=nocloud-net;s=http://_gateway:8080/' \
```

Notes:
- `_gateway` is a [special host](https://www.freedesktop.org/software/systemd/man/systemd-resolved.service.html#Synthetic%20Records) resolved by `systemd-resolved`
- see the [cloud-init documentation](https://cloudinit.readthedocs.io/en/latest/topics/datasources/nocloud.html) for more information about the command line syntax

If you just want to test and don't mind the default settings I've provided, then you can use one of these strings to append instead:
- `autoinstall ds=nocloud-net;s=https://github.com/wpyoga/ubuntu-server-nosnap/raw/master/`
- `autoinstall ds=nocloud-net;s=https://raw.githubusercontent.com/wpyoga/ubuntu-server-nosnap/master/`

If you're installing on a bare metal, you can specify the extra command line arguments at the GRUB menu, or bake the autoinstall file directly to the ISO.

## How to bake it directly to the ISO

If you are pleased with the results and want to create your own custom ISO, see this project: https://github.com/covertsh/ubuntu-autoinstall-generator

It's not my project, I'm not afiliated with the creator, and I've never used it before. But based on my reading of the source code, it seems legit.

## How about Ubuntu Desktop?

This does not work on Ubuntu Desktop.

## How about netinstall?

I haven't tried this either.

## Motivation

I simply don't like snap. Just like I don't like Flatpak etc. It's a convoluted mess, and it takes up precious system resources. Also, Linux Mint developers (I'm a Linux Mint user) seem to [dislike snap](https://linuxmint-user-guide.readthedocs.io/en/latest/snap.html), albeit for other reasons.

So this repo has been created to help people install Ubuntu Server without snap.
