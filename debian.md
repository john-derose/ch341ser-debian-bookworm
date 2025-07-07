# CH341SER receipe for Debian 12 (bookworm)

## prerequisites
sudo su -
apt install -y dwarves
git clone https://github.com/john-derose/ch341ser-debian-bookworm.git

## build the module
cd ch341ser-debian-bookworm/
ln -s /sys/kernel/btf/vmlinux /usr/lib/modules/6.8.12-11-pve/build/  # if needed
make clean
make

## (optional) Secure Boot; sign the module
MOKDIR=/root/mok ; /usr/src/linux-headers-6.8.12-11-pve/scripts/sign-file \
  sha256 $MOKDIR/MOK.priv $MOKDIR/MOK.crt ch34x.ko

## load the module
modprobe -r ch34x  # if loaded
cp ch34x.ko /lib/modules/6.8.12-11-pve/kernel/drivers/usb/serial/ch34x.ko
depmod -a
modprobe ch34x

## confirm load
modinfo ch34x | grep filename
dmesg | grep ch34x
lsmod | grep ch34x

## confirm usbserial instance; plug-in device... be sure to use a data cable!
ls /dev/ttyUSB*

