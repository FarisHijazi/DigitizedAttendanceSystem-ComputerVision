# DigitizedAttendanceSystem-ComputerVision

Taking attendance using facial recognition - part of the COE485 Senior Design project for Term 192

## Developement documentation

This stuff is just info on what happened

### Installing `face_recognition`

If you're getting the error that `dlib` fails to build, [see this issue](https://github.com/ageitgey/face_recognition/issues/802#issuecomment-485256117)

```sh
pip install cmake
pip install Boost
pip --no-cache-dir install face_recognition

cd face_recognition || git clone https://github.com/ageitgey/face_recognition && cd face_recognition
python setup.py install
```

## what I did

I downloaded LFW (faced in the wild dataset)

## Thoughts

| requirements                          | rbpi | podeium PC (web app) | mobile app | nvidia Jetson |
| ------------------------------------- | ---- | -------------------- | ---------- | ------------- |
| automat-activate: no user interaction | yes  | no                   | no         | yes           |
| can override                          | no   | yes                  | yes        | somewhat      |
| show progress/feedback                | no   | yes                  | yes        | somewhat      |
| can infer                             | no   | yes                  | yes        | yes           |

## Justifying decisions

We need something that doesn't need retraining, rather we want something that will extract facial features. The reason is that, if we use a classifier that needs to retrain for each new class/subgroup, it will not scale at all.

We decided to go with edge-computing for now.
Later, we could have 2 products, one that works on the cloud and one that is completely edge based. For the version that is completely on the edge (has no cloud computing), it provides privacy, this is ideal for sensitive environments (such as if the campus was for females in Saudi Arabia). We can capitalize on this and give the customer the option to choose, either a one-time payment and privacy.

The open-source options we found are [OpenFace](https://github.com/TadasBaltrusaitis/OpenFace), and [face_recognition](https://github.com/ageitgey/face_recognition).

## Jetson setup

Most resources can be found on the NVidia website. [Here](https://developer.nvidia.com/linux-tegra-r215) is the files for linux tegra r215 (which is the model that's used in KFUPM (Jetson-TK1)).

Quick start guide can be found [here](https://developer.download.nvidia.com/embedded/L4T/r21_Release_v5.0/l4t_quick_start_guide.txt?fXFkzxP82W5EFz2OrVSXPfrOvQQH9leN1FgoBW60psvRS4e1sjbLGLxx0dumQXNTdkSpMvEbl_KPdq4Ute8pE8_HLrb9beOy4g08EO4HUBRWml23nH3oE81ePnkzz56nLWCYt0f8yEr---o1qzT_IxHzCvj6Kg).

You need to have a linux system, but I did this on Windows 10 using the [Windows Subsystem for Linux (WSL)](https://docs.microsoft.com/en-us/windows/wsl/install-win10). Once installed, just run a normal windows command prompt or powershell window and run the `bash` command to enter the linux subsystem.

All the following commands are expected to be run on a linux system.

1. Download all those files

    Note: put these files in a directory that's path contains NO SPACES!
    If the path contains spaces, the "apply_binaries.sh" script will FAIL

2. Verify

    Assuming the you downloaded the checksum files, use the following command to verify the downloaded files:

    ```sh
    sha1sum -c release_sha_hashes.txt r21.8.0-sources-sha1sum.txt
    ```

3. follow the instructions in the quick startup guide, but first run these commands to make things simpler:

    Define the following variables first before (change them depending on the versions that you have)

    ```sh
    BOARD='jetson-tk1'
    RELEASE_NAME="Tegra124_Linux_R21.8.0_armhf.tbz2"
    ```

    Then run the following commands to extract the files:  
    Note: if you're running a virtual machine, you should make sure that the USB device is directly connected to the VM, there should be a `device` tab. You can use the `lsusb` command to check, you should get one of the devices in the output to be something like: `Bus 001 Device 003: ID 0955:7140 NVidia Corp.`

    ```sh
    sudo tar xpf ${RELEASE_NAME}
    cd Linux_for_Tegra/rootfs/
    sudo tar xpf ../../Tegra_Linux_Sample-Root-Filesystem_R21.5.0_armhf.tbz2
    cd ../
    sudo ./apply_binaries.sh
    ```

### Issues

The flash.sh script wasn't working because it wasn't finding the nvidia Jetson through the USB.

```sh
$ sudo ./flash.sh ${BOARD} mmcblk0p1  #This will take less than 10 minutes.
copying bctfile(/mnt/c/Users/Faris/Desktop/DigitizedAttendanceSystem-ComputerVision/Jetson_TK1/Linux_for_Tegra/bootloader/ardbeg/BCT/PM375_Hynix_2GB_H5TC4G63AFR_H5TC4G63CFR_RDA_924MHz.cfg)... done.
copying bootloader(/mnt/c/Users/Faris/Desktop/DigitizedAttendanceSystem-ComputerVision/Jetson_TK1/Linux_for_Tegra/bootloader/ardbeg/u-boot.bin)... done.
        populating kernel to rootfs... done.
        populating jetson-tk1_extlinux.conf.emmc to rootfs... done.
done.
Making system.img...
losetup: cannot find an unused loop device: No such device
Cannot find loop device. Terminating..
```

We need to find/look for the USB device somehow, maybe the WSL doesn't have access to the USB ports.

```sh
$ sudo losetup --find
losetup: cannot find an unused loop device: No such device
```

Well it turns out that WSL does not support `/dev/loop*` ([see issue here](https://github.com/microsoft/WSL/issues/1691)) and looks like I'm gonna have to redo this work on a virtual machine (yay.. (; )

Finally after switching to a virtual machine, we can finally run the script.
