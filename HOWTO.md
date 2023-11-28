# HOWTO Windows WSL2

## Summary

[0]: https://github.com/simoninns/DomesdayDuplicator/wiki

This HowTo is intended as a concise sequence of steps to guide you through the process of aquiring and preparing the hardware and software. As a consequence, it provides very little background information, and instead focuses on consolidating the documentation guides. You are strongly encouraged to read the [Wiki][0] for further information. While this guide is targeted at Windows with WSL2, it should be easily adpated by the reader for Linux.

## 1. PCB Fabrication and Assembly

[1.1]: https://www.pcbway.com/project/shareproject/DoomsDayDuplicator_Rev_3.html
[1.2]: https://docs.google.com/document/d/1k2bPPwHPoG7xXpS1NCYEe3w_jD_ts0yRCp-2aQ6QoKY

Thanks to Harry Munday for sharing a turn-key fabrication and assembly project on PCBWay.

  - [Domes Day Duplicator Rev 3 - Shared Project - PCBWay][1.1]

Assembly instructions are located on [Google Docs][1.2], summarised below.

  - From the PCBWay shared project page, choose: `PCB+Assembly` -> `Add to cart`.

    (all the default selections are made for you as part of the turn-key service, nothing else special is required)
    
  - Quantity: `1`
  - Contains Sensitive components/parts? `NO`
  - Do you accept alternatives/substitutes made in China? `YES`
  - Number of Unique Parts: `30`
  - Number of SMD Parts: `59`
  - Number of BGA/QFP Parts: `0`
  - Number of Through-Hole Parts: `5`

## 2. DE0-Nano & EZ-USB FX3 

[2.1]: https://www.digikey.com.au/en/products/detail/terasic-inc/P0082/2625112
[2.2]: https://www.digikey.com.au/en/products/detail/infineon-technologies/CYUSB3KIT-003/4989179

The remaining two boards can be purchased from DigiKey.

  - [Terasic DE0-Nano FPGA board][2.1]
  - [Infineon Cypress EZ-USB FX3 board][2.2]

## 3. Install DE0-Nano development software

[3.1]: https://de0-nano.terasic.com/cd
[3.2]: https://download.terasic.com/downloads/cd-rom/de0-nano/DE0-Nano_v.1.2.8_SystemCD.zip
[3.3]: https://www.intel.com/content/www/us/en/collections/products/fpga/software/downloads.html
[3.4]: https://www.intel.com/content/www/us/en/software-kit/785086/intel-quartus-prime-lite-edition-design-software-version-22-1-2-for-windows.html

Download the DE0-Nano CD-ROM from Terasic.

  - [DE0-Nano CD-ROM][3.1] or [DE0-Nano_v.1.2.8_SystemCD.zip][3.2]
  - The downloaded CD-ROM ZIP file doesn't require installing and can just be unzipped to a location of your choosing.

Download the Quartus Prime Lite Edition software from Intel.

  - [FPGA Software Download Center][3.3] or [Quartus Prime Lite Edition for Windows][3.4]
  - The downloaded Quartus Lite TARBALL can be untarred and then the Quartus Lite Setup Windows EXE should then be installed.

## 4. Install EZ-USB FX3 development software

[4.1]: https://www.cypress.com/go/SuperSpeedExplorerKit
[4.2]: https://itools.infineon.com/archive/SuperSpeedExplorerKitSetup_RevSS.exe
[4.3]: https://www.cypress.com/go/FX3SDK
[4.4]: https://itoolspriv.infineon.com/itbhs/api/packages/com.ifx.tb.tool.ezusbfx3sdk/Versions/1.3.4/artifacts/FX3SDKSetup_1.3.4.exe/download

Download the Super Speed Explorer Kit software from Infineon.

  - [EZ-USB FX3 Super Speed Explorer Kit][4.1] or [SuperSpeedExplorerKitSetup_RevSS.exe][4.2]
  - After the installation is complete the `Cypress Update Manager` will prompt you to update the SDK from `1.3.3` to `1.3.4`.
  - If it doesn't run, you should run it manually from the Windows Start Menu.
  - Alternatively, you can download the SDK and install it seperarely.
  - [EZ-USB FX3 SDK][4.3] or [FX3SDKSetup_1.3.4.exe][4.4]

## 5. Install WSL2 on Windows

[5]: https://learn.microsoft.com/en-us/windows/wsl/install

If you don't already have WSL2 installed, the following guide is provided by Microsoft.

  - [Install WSL][5] and upgrade it to WSL2.
```
    wsl --list --online
    wsl --install -d Ubuntu-22.04
    wsl --list -v
    wsl --set-version Ubuntu-22.04 2
    wsl --shutdown

    wsl
    sudo apt-get update
    sudo apt-get upgrade
    sudo apt install git
```

## 6. Clone the Domesday Duplicator GIT repository

Clone the Domesday Duplicator GIT repo from a WSL2 prompt.

```
    wsl
    git clone https://github.com/simoninns/DomesdayDuplicator.git
```

## 7. Build and flash the DE0-Nano firmware

Connect the DE0-Nano board to your PC using the USB cable.
  - Ensure the driver is loaded in the Windows `Device Manager`.
  - If not, click `Update Driver` -> `Locate Driver Manually`, then choose the location where Quartus was installed, e.g.
    `C:\IntelFPGA_Lite\22.1std\quartus\drivers\usb-blaster`

Modify the following files in your local clone of the GIT repo.
```
    DE0-NANO/DomesdayDuplicator/DomesdayDuplicator.cof
    DE0-NANO/DomesdayDuplicator/DomesdayDuplicator_write_sof.cdf
    DE0-NANO/DomesdayDuplicator/DomesdayDuplicator_write_jic.cdf
```
  - Update the file path location in each file to suite your environment, e.g. change:
```
    /home/sdi/github/DomesdayDuplicator/DE0-NANO/DomesdayDuplicator/
    /Users/MyUser/DomesdayDuplicator/DE0-NANO/DomesdayDuplicator/
```

Run Intel Quartus Prime.
  - `Intel FPGA Lite Edition` -> `Quartus Prime`

Open the project and compile it.
  - `File` -> `Open Project` -> locate the project file `DomesdayDuplicator.qpf`.
  - `Processing` -> `Start Compilation`.

Temporarily flash the firmware.
  - `Project Navigator` choose `Files` from the dropdown.
  - From the Navigator, double click the `DomesdayDuplicator_write_sof.cdf` file.
  - This will bring up the `Programmer` window with configuration pre-populated.
  - Click the `Start` button to flash the firmware.
  - Close the programmer window after the progress is successful.

Permanently flash the firmware.
  - `File` -> `Convert Programming Files`.
  - This will bring up the `Convert Programming File` window.
  - Click `Open Conversion Setup Data`, then choose the `DomesdayDuplicator.cof` file.
  - This will pre-populate the configuration.
  - Click the `Generate` button at the bottom of the window.
  - Close the window after the conversion is successful.
  - `Project Navigator` choose `Files`.
  - From the Navigator, double click the `DomesdayDuplicator_write_jic.cdf` file.
  - This will bring up the `Programmer` window with configuration pre-populated.
  - Click the `Start` button to flash the firmware.
  - Close the programmer window after the progress is successful.

Close Intel Quartus and disconnect the DE0-Nano board from your PC.

## 8. Build and flash the EZ-USB FX3 firmware

Connect the EZ-USB FX3 board to your PC using the USB cable.
  - Ensure the driver is loaded in the Windows `Device Manager`.
  - If not, click `Update Driver` -> `Locate Driver Manually`, then choose the location where the Speed Explorer Kit software was installed, e.g.
    `C:\Program Files (x86)\Cypress\SuperSpeed Explorer Kit\1.0\Driver`

Modify the following file in your local clone of the GIT repo.
```
    FX3-Firmware/GPIF_II/DomesdayDuplicator.cydsn/DomesdayDuplicator.cyfx
```
  - Update the file path location to suite your environment, e.g. change:
```
    D:\simon\Documents\GitHub\DomesdayDuplicator\FX3-Firmware\domesdayDuplicator
    C:\Users\MyUser\DomesdayDuplicator\FX3-Firmware\GPIF_II\DomesdayDuplicator.cydsn
```
  - Confirm the template file is located where you have installed Cypress GPIF II Designer.
```
    C:\Program Files (x86)\Cypress\EZ-USB FX3 SDK\1.3\GPIFII Designer\inputs\outputtemplates\cygpif2cheadertemplate.tpl
```
  - If not, update the path in the `DomesdayDuplicator.cyfx` file also.

Run Cypress GPIF II Designer to generate the header file.
  - `Cypress` -> `GPIF II Designer`

Open the project and compile it.
  - `File` -> `Open Project` -> locate the project file `DomesdayDuplicator.cyfx`.
  - `Build` -> `Build Project`.
  - The `domesdayDuplicatorGpif.h` header file should have been generated in the `DomesdayDuplicator.cydsn` directory.
  - Close the Cypress GPIF II Designer.
  - Copy the generated `domesdayDuplicatorGpif.h` header file to the `FX3-Firmware/domesdayDuplicator` directory.
  - The generated header file should be the same as the existing one in your local GIT repo, so you may skip this step if you choose.

Sanitise the EZ-USB FX3 SDK directory.
  - If you installed the EZ-USB FX3 SDK in the default location it will contain characters in the path that will be painful to deal with during the compilation.
```
    C:\Program Files (x86)\Cypress\EZ-USB FX3 SDK\1.3
```
  - It will be easier to simply copy the entire directory and all it's subfolders to a new location without any spaces or parentheses, and relative to the the local GIT repo, e.g.
```
    C:\Users\MyUser\EZ-USB-FX3\EZ-USB-FX3-SDK\1.3
```
  - After you copy the directory, you should also rename the `1.3/ARM GCC` directory to remove the space from it's name. i.e. `1.3/ARM_GCC`.

Update the `makefile` in the local GIT repo.
```
    FX3-Firmware/domesdayDuplicator/makefile
```
  - Modify each command to reference the Windows `.exe` equivalent.
  - `arm-none-eabi-gcc` -> `arm-none-eabi-gcc.exe` (there are several)
  - `elf2img` -> `elf2img.exe`

If you are following along with the `HOWTO-COMPILE.txt` in the GIT repo, you will not need to compile the `elf2img`, as this is already provided in the EZ-USB FX3 SDK for Windows.

Compile the firmware from a WSL2 prompt.
```
    wsl
    export PATH=$PATH:/mnt/c/Users/MyUser/EZ-USB-FX3/EZ-USB-FX3-SDK/1.3/ARM_GCC/bin/:/mnt/c/Users/MyUser/EZ-USB-FX3/EZ-USB-FX3-SDK/1.3/util/elf2img/
    export FX3_INSTALL_PATH=../../../EZ-USB-FX3/EZ-USB-FX3-SDK/1.3
    export ARMGCC_VERSION=4.8.1

    cd /mnt/c/Users/MyUser/DomesdayDuplicator/FX3-Firmware/domesdayDuplicator
    make all
```
  - The `domesdayDuplicator.img` file should have been generated (along with a number of other intermediary files).

Run Cypress USB Control Center.
  - `Cypress` -> `Control Center`
  - To prepare the device for programming, disconnect the EZ-USB FX3 board from the PC.
  - Close/short jumper J4 (PMODE) on the EZ-USB FX3 board.

Temporarily flash the firmware.
  - Ensure that jumper J4 (PMODE) is closed/short on the EZ-USB FX3 board.
  - Disconnect and then reconnect the EZ-USB FX3 board to the PC again using the USB cable.
  - Highlight the EZ-USB FX3 `BootProgrammer` device at the top of the USB Control Center window.
  - Click `Program` -> `FX3` -> `RAM`.
  - Choose the `domesdayDuplicator.img` file.
  - Programming should complete quite quickly, a success message will appear in the lower left corner of the USB Control Center window.

Permanently flash the firmware.
  - Ensure that jumper J4 (PMODE) is closed/short on the EZ-USB FX3 board.
  - Disconnect and then reconnect the EZ-USB FX3 board to the PC again using the USB cable.
  - Highlight the EZ-USB FX3 `BootProgrammer` device at the top of the USB Control Center window.
  - Click `Program` -> `FX3` -> `I2C EEPROM`.
  - Choose the `domesdayDuplicator.img` file.
  - Wait for programming to complete, a success message will appear in the lower left corner of the USB Control Center window.

Close Cypress USB Control Center.
  - Disconnect the EZ-USB FX3 board from your PC
  - Remove the jumper from J4 (PMODE).
  - When you next connect the EZ-USB FX3 to the PC, Windows will recognise it as a new `Domesday Duplicator` device.

## 9. Build the Domesday Duplicator software

[9.1]: https://github.com/simoninns/DomesdayDuplicator/wiki/User-Guide
[9.2]: https://forum.qt.io/topic/132610/qtcreator-6-0-can-t-start/53
[9.3]: https://github.com/microsoft/WSL/issues/3023
[9.4]: https://learn.microsoft.com/en-us/windows/wsl/connect-usb
[9.5]: https://github.com/dorssel/usbipd-win/wiki/WSL-support
[9.6]: https://github.com/dorssel/usbipd-win/releases

The [User-Guide][9.1] provides background and further information.

Compile the software from a WSL2 prompt.
```
    wsl
    sudo apt install --no-install-recommends git cmake libgl-dev qt6-base-dev libqt6serialport6-dev libusb-1.0-0-dev qt6-multimedia-dev build-essential

    cd /mnt/c/Users/MyUser/DomesdayDuplicator/Linux-Application
    cmake -DCMAKE_BUILD_TYPE=RelWithDebInfo -DCMAKE_INSTALL_PREFIX=/home/myuser .
    make -j8
    make install
```
  - If you see the error `error while loading shared libraries: libQt6Core.so.6`, this is a known issue on WSL. 
Further discussion is available on the [Qt forum][9.2] and the [WSL GitHub forum][9.3].
  - You will need to locate the shared library (not the symlink) and strip the ABI tag from it.
```
    find /usr -name libQt6Core*
    sudo strip --remove-section=.note.ABI-tag /usr/lib/x86_64-linux-gnu/libQt6Core.so.6.2.4
```

Enable USB device support for WSL2.
  - Further discussion about USB device support for WSL2 is provided by [Microsoft][9.4] and available on [GitHub][9.5]. 
  - Download and install the [USBIPD-WIN][9.6] MSI package.
  - Connect the Domesday Duplicator (or the EZ-USB FX3 board) to your PC with a USB cable.
  - From an elevated Windows command prompt, run the `usbipd` command to locate the device and attach it to WSL.
```
    usbipd wsl list
    usbipd wsl attach --busid 1-13
```
  - Enable the device from Linux.
```
    sudo apt install linux-tools-generic
    sudo update-alternatives --install /usr/local/bin/usbip usbip /usr/lib/linux-tools/*-generic/usbip 20

    lsusb
```
  - Provide device permission from Linux.
```
    sudo vi /etc/udev/rules.d/40-domesdayduplicator.rules
```
```
    # 1d50:603b - Domesday Duplicator
    SUBSYSTEM=="usb", ATTRS{idVendor}=="1d50", ATTRS{idProduct}=="603b", MODE="0666"
```
  - Reload the configuration.
```
    sudo udevadm control --reload-rules
```
Run the Domesday Duplicator application.
  - Ensure that you have upgraded to WSL2 as Linux windowing support is not available on WSL1 (see [Section 5](#5-install-wsl2-on-windows) earlier). 
```
    /home/myuser/bin/DomesdayDuplicator &
```
  - You should see a message in the lower status bar of the window that the USB device is connected. 
  - If you see the warning `Could not find the Qt platform plugin "wayland"`, you can either ignore it or install Qt6 support for Wayland. 
```
    sudo apt install qt6-wayland
```
Congratulations, you've made it.
