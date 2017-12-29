# Raspberrypi_PreemptRT
(Latest update 2017.12.29)

將Raspberry換成preempt RT patch的real time linux kernel，以下為linux kernel 4.14.7的例子

* 下載最新版[raspbian](https://www.raspberrypi.org/downloads/raspbian/)，可利用Win32DiskImager將img檔燒進SD卡
* 在raspberry pi環境下輸入以下指令可升級至最新kernel版本
```
sudo BRANCH=next rpi-update
```
* Host端的Ubuntu/Debian(64bits)環境下載linux kernel/cross compile tools，並開始cross-compile
```
git clone --depth=1 https://github.com/raspberrypi/linux
git clone https://github.com/raspberrypi/tools
cd linux
export KERNEL=kernel7
export PATH=$PATH:[your_raspberry_path]/tools/arm-bcm2708/gcc-linaro-arm-linux-gnueabihf-raspbian-x64/bin
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- bcm2709_defconfig
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- menuconfig
  - ( Enable _PREEMPT_RT_FULL_ ) Kernel Features => Preemption Model => Fully Preemptable Kernel (RT)
  - ( Enable _RCU_BOOST_ ) General Setup => RCU Subsystem => Make expert-level adjustments to RCU configuration
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- zImage modules dtbs
```
* 將cross-compile好的image和modules放到板子上
```
cp arch/arm/boot/zImage [sd_card_path]/boot/kernel7.img
make ARCH=arm CROSS_COMPILE=arm-linux-gnueabihf- INSTALL_MOD_PATH=[sd_card_path]/rootfs modules_install
```
* 如果只做到這樣，會發現real time版本非常不穩定，常常開機失敗或者當機，要添加以下內容至boot/cmdline.txt，參考https://wiki.linuxfoundation.org/realtime/documentation/known_limitations
  * dwc_otg.fiq_fsm_enable=0 dwc_otg.fiq_enable=0 dwc_otg.nak_holdoff=0




