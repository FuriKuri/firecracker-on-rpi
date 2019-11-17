# Firecracker on Raspberry Pi

The step are based on the blog post from from [CloudKernels](https://blog.cloudkernels.net/posts/firecracker-rpi4/). I had to build firecracker on the original repository, otherwise I had problems that firectl could not communicate with firecracker.

## Hardware

Important: You need a micro HDMI cabel/adapter, not a mini HDMI. You also need a USB C cable for power.

* Raspberry Pi 4 Modell B 4GB [link](https://www.amazon.de/gp/product/B07TC2BK1X)
* SD card [link](https://www.amazon.de/gp/product/B013UDL5RU)
* Micro-HDMI to HDMI [link](https://www.amazon.de/gp/product/B014I8U33I)
* USB C Cable to USB Typ A [link](https://www.amazon.de/dp/B01GGKYR2O)

## Preparations
### Install OS

I used a prepared image with ubuntu and kvm enabled from [CloudKernels](https://blog.cloudkernels.net/posts/rpi4-64bit-image/). For an easy installation you can use [flash](https://github.com/hypriot/flash). Just insert your SD card and run the following command from your command line.

```
$ wget https://cloudkernels.net/ubuntu-18.04.3-preinstalled-server-arm64+raspi4+kvm.img.xz
$ xz -d https://cloudkernels.net/ubuntu-18.04.3-preinstalled-server-arm64+raspi4+kvm.img.xz
$ flash ubuntu-18.04.3-preinstalled-server-arm64+raspi4+kvm.img
```

### Prepare Firecracker

You need to build the firecracker and firectl binary. I added the git project as submodules. This will make sure that the two versions are working. To build the binaries you need golang and docker installed.

Login to your raspberry with username **ubuntu** and password **ubuntu**.

```
ubuntu@ubuntu:~$ sudo -i

root@ubuntu:~# snap install docker
root@ubuntu:~# snap install go --classic

root@ubuntu:~# git clone --recurse-submodules https://github.com/furikuri/firecracker-on-rpi

# Build firecracker
root@ubuntu:~# cd firecracker-on-rpi/firecracker
root@ubuntu:~/firecracker-on-rpi/firecracker# ./tools/devtool build
root@ubuntu:~/firecracker-on-rpi/firecracker# cp ./build/cargo_target/aarch64-unknown-linux-musl/debug/firecracker /usr/local/bin/

# Build firectl
root@ubuntu:~# cd       # switch to home directory
root@ubuntu:~# cd firecracker-on-rpi/firectl
root@ubuntu:~/firecracker-on-rpi/firectl# go build
root@ubuntu:~/firecracker-on-rpi/firectl# cp ./firectl /usr/local/bin/

```

### Using firecracker

```
root@ubuntu:~# cd       # switch to home directory
root@ubuntu:~# wget https://s3.amazonaws.com/spec.ccfc.min/img/aarch64/ubuntu_with_ssh/kernel/vmlinux.bin
root@ubuntu:~# wget https://s3.amazonaws.com/spec.ccfc.min/img/aarch64/ubuntu_with_ssh/fsfiles/xenial.rootfs.ext4
root@ubuntu:~# firectl --kernel=vmlinux.bin --root-drive=xenial.rootfs.ext4
```