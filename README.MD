## Real-time kernel for Raspberry Pi

This repository holds a fork of Raspberry Pi Linux kernel patched with rt-patch and configured as a fully preemtible kernel. Instructions on compiling, setting up and testing the kernel are provided below.

### Cross-compiling the rt kernel on Linux

Download Raspberry Pi tools:
```
git clone https://github.com/raspberrypi/tools.git
```

Download kernel sources and switch to rt branch:
```
git clone https://github.com/emlid/linux-rt-rpi.git
cd linux-rt-rpi
git checkout rpi-3.18.7-rt1
```

Export the following variables to specify cross-compilation options:
```
export ARCH=arm 
export CROSS_COMPILE=~/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian/bin/arm-linux-gnueabihf-
```

For Raspberry Pi:
```
make bcmrpi_rt_defconfig
```

For Raspberry Pi 2:
```
make bcm2709_rt_defconfig
```

Optional: make changes to the config
```
make menuconfig
```

Compile the kernel:
```
make -j5
```

Install modules, will result in "lib" folder with modules and firmware:
```
mkdir kernel-rt
INSTALL_MOD_PATH=kernel-rt make modules_install
```

### Setting up an SD card with the compiled rt kernel

Copy kernel:

For Raspberry Pi:
```
Copy arch/arm/boot/Image to /boot/kernel.img on SD card.
Copy arch/arm/boot/dts/bcm2708-rpi-b.dtb to /boot/bcm2708-rpi-b.dtb on SD card
Copy arch/arm/boot/dts/bcm2708-rpi-b-plus.dtb to /boot/bcm2708-rpi-b-plus.dtb on SD card
Copy (merge if necessary) kernel-rt/lib to / on SD card.
```

For Raspberry Pi 2:
```
Copy arch/arm/boot/Image to /boot/kernel7.img on SD card.
Copy arch/arm/boot/dts/bcm2709-rpi-2-b.dtb to /boot/bcm2709-rpi-2-b.dtb on SD card
Copy (merge if necessary) kernel-rt/lib to / on SD card.
```

Now you can insert an SD card into your Raspberry Pi and check if it boots.
If something is wrong it is recommended to observe serial console.

### Testing real-time capabilites using cyclictest utility

Installing cyclictest utility on Raspberry Pi:
```
git clone git://git.kernel.org/pub/scm/linux/kernel/git/clrkwllms/rt-tests.git 
cd rt-tests
make all
cp ./cyclictest /usr/bin/
cd ~
```

Testing real-time:
```
sudo cyclictest -l1000000 -m -n -a0 -t1 -p99 -i400 -h400 -q
```

