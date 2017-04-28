# Ubuntu Xenial

Need at least Ubuntu 16.10, Tested with 17.04.

If using VM, make sure to have at least 2 GB of RAM

Download all necessary packages and files
```
sudo apt-get install gcc-arm-linux-gnueabihf gcc-arm-linux-gnueabi libc6-dev-armhf-cross qemu-user-static qemu-system qemu-system-arm qemu linaro-image-tools

wget http://ports.ubuntu.com/ubuntu-ports/dists/xenial/main/installer-armhf/current/images/generic-lpae/netboot/initrd.gz

wget http://ports.ubuntu.com/ubuntu-ports/dists/xenial/main/installer-armhf/current/images/generic-lpae/netboot/vmlinuz
```

Create a disk image to boot from
```
qemu-img create -f raw armdisk.img 8G
```

Boot up system to install (Takes about 2 to 3 hours)
```
qemu-system-arm \
  -kernel vmlinuz \
  -initrd initrd.gz \
  -append "root=/dev/ram" \
  -no-reboot \
  -nographic \
  -m 1024 \
  -M virt \
  -drive format=raw,file=armdisk.img,index=0
```

Use fdisk to find offset. Use boot partition. Multiply block by block size (512).
Copy out the kernel and initrd images from boot.
```
fdisk -l armdisk.img

sudo mount -o loop,offset=1048576 armdisk.img /mnt/tmp

sudo cp /mnt/tmp/vmlinuz-4.4.0-72-generic-lpae boot/
sudo cp /mnt/tmp/initrd.img-4.4.0-72-generic-lpae boot/
sudo chown user:user boot/*
```

Boot up script to run new ARM QEMU system
```
#!/bin/sh
qemu-system-arm \
  -kernel boot/vmlinuz-4.4.0-72-generic-lpae \
  -initrd boot/initrd.img-4.4.0-72-generic-lpae \
  -append "root=/dev/vda2 rootfstype=ext4" \
  -no-reboot \
  -nographic \
  -m 1024 \
  -M virt \
  -monitor telnet:127.0.0.1:9000,server,nowait \
  -drive format=raw,file=armdisk.img,if=virtio \
  -net nic,model=virtio \
  -net user,id=mynet0,hostfwd=tcp::5555-:22
```