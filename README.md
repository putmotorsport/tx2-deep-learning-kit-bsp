# Work notes

## known bugs
- [X] <s>USB not working</s>
- [ ] GPU not working
- [ ] no OV5640 camera driver



## Getting resources
* [Linux for Tegra 32.3.1](https://developer.nvidia.com/embedded/linux-tegra)

* clone repo  
```
git clone git@github.com:putmotorsport/tx2-deep-learning-kit-bsp.git
cd tx2-deep-learning-kit-bsp
export TX2_DIR=$(pwd)
```

* download Tegra186_Linux_R32.3.1 and Unpack this archive  
```
wget "https://developer.nvidia.com/embedded/dlc/r32-3-1_Release_v1.0/t186ref_release_aarch64/Tegra186_Linux_R32.3.1_aarch64.tbz2"  
sudo tar xpf Tegra186_Linux_R32.3.1_aarch64.tbz2
```

* download the root filesystem
```
wget "https://developer.nvidia.com/embedded/dlc/r32-3-1_Release_v1.0/t186ref_release_aarch64/Tegra_Linux_Sample-Root-Filesystem_R32.3.1_aarch64.tbz2"
sudo tar xpf Tegra_Linux_Sample-Root-Filesystem_R32.3.1_aarch64.tbz2 -C ./Linux_for_Tegra/rootfs
```

## Kernel compilation
* cross-compiler
```
wget "http://releases.linaro.org/components/toolchain/binaries/7.3-2018.05/aarch64-linux-gnu/gcc-linaro-7.3.1-2018.05-x86_64_aarch64-linux-gnu.tar.xz"
mkdir ./toolchain
tar xpf gcc-linaro-7.3.1-2018.05-x86_64_aarch64-linux-gnu.tar.xz -C ./toolchain
```

* build directory and environment
```
export ARCH=arm64
export PATH=${PWD}/toolchain/gcc-linaro-7.3.1-2018.05-x86_64_aarch64-linux-gnu/bin/:$PATH
export CROSS_COMPILE=aarch64-linux-gnu-
```

* compilation settings
```
cd $TX2_DIR/kernel/kernel-4.9/
make tegra_defconfig LOCALVERSION="-antmicro"
```

* Compile the kernel
```
make -j<nproc> LOCALVERSION="-antmicro"
make -j<nproc> modules LOCALVERSION="-antmicro
cp .config kernel_config
```

* Replace the original Image and devicetree with compiled ones
```
cd $TX2_DIR
sudo cp kernel/kernel-4.9/arch/arm64/boot/Image Linux_for_Tegra/kernel/
sudo cp kernel/kernel-4.9/arch/arm64/boot/dts/tegra186-quill-p3310-1000-c03-00-base.dtb Linux_for_Tegra/kernel/dtb/tegra186-quill-p3310-1000-c03-00-base.dtb
cd Linux_for_Tegra
sudo ./apply_binaries.sh
```

* Install the BSP on the TX2
```
sudo ./flash.sh jetson-tx2 mmcblk0p1
```
