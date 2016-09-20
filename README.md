# Viggen Two Build Yocto release

<div id="mw-content-text" dir="ltr" class="mw-content-ltr" lang="en">

<span style="font-size:16pt;">ViggenTwo - Yocto fsl-4.1.15-1.2.0 Jethro Build Yocto release</span>

</div>

* * *

# <span class="toc" id="toctitle">Contents</span>

*   [<span class="tocnumber">1</span> <span class="toctext">Build host setup</span>](#Build host setup)
*   [<span class="tocnumber">2</span> <span class="toctext">V2-DevKit</span>](#V2-devkit)
    *   [<span class="tocnumber">2.1</span> <span class="toctext">Boot configuration</span>](#Boot configuration)
*   [<span class="tocnumber">3</span> <span class="toctext">Environment setup</span>](#Environment_setup)
    *   [<span class="tocnumber">3.1</span> <span class="toctext">Git and repo utility setup</span>](#Git_and_repo_utility_setup)
    *   [<span class="tocnumber">3.2</span> <span class="toctext">Yocto Jethro source download</span>](#Yocto_jethro_source_download)
    *   [<span class="tocnumber">3.3</span> <span class="toctext">Source patching</span>](#Source_patching)
*   [<span class="tocnumber">4</span> <span class="toctext">Yocto setup and build</span>](#Yocto_setup_and_build)
    *   [<span class="tocnumber">4.1</span> <span class="toctext">Local configuration tuning</span>](#local.conf_customizations)
    *   [<span class="tocnumber">4.2</span> <span class="toctext">Setup to build Yocto Minimal Image</span>](#Setup_to_build_yocto_minimal_image)
    *   [<span class="tocnumber">4.3</span> <span class="toctext">Setup to build Yocto X11 Image</span>](#Setup_to_build_yocto_x11_image)
    *   [<span class="tocnumber">4.4</span> <span class="toctext">Setup to build Yocto Java Test Image</span>](#Setup_to_build_yocto_java_test_image)
        *   [<span class="tocnumber">4.4.1</span> <span class="toctext">Prerequisites</span>](#Prerequisites_for_yocto_java_test_image)
        *   [<span class="tocnumber">4.4.2</span> <span class="toctext">Image build</span>](#Yocto_java_test_image_build)
    *   [<span class="tocnumber">4.5</span> <span class="toctext">Setup to build Yocto Qt4 Demo Image </span>](#Setup_to_build_yocto_qt4_demo_image)
    *   [<span class="tocnumber">4.6</span> <span class="toctext">Useful Bitbake commands</span>](#Useful_bitbake_commands)
*   [<span class="tocnumber">5</span> <span class="toctext">Build outputs</span>](#Build_outputs)
*   [<span class="tocnumber">6</span> <span class="toctext">Image deploy</span>](#Image_deploy)
	*   [<span class="tocnumber">6.1</span> <span class="toctext">eMMC programming</span>](#emmc_programming)
		*   [<span class="tocnumber">6.1.1</span> <span class="toctext">MFGTool download</span>](#Mfgtool_download)
		*   [<span class="tocnumber">6.1.2</span> <span class="toctext">Hardware setup</span>](#Hardware_setup)
		*   [<span class="tocnumber">6.1.3</span> <span class="toctext">MFGTool setup</span>](#Mfgtool_setup)
		*   [<span class="tocnumber">6.1.4</span> <span class="toctext">Image programming </span>](#Image_programming)

# <span class="mw-headline" id="Build_host_setup"><span class="mw-headline-number">1</span> Build host setup  </span>

In order to setup a Linux-based host check the following link and install all required packages on your machine:

[www.yoctoproject.org/docs/latest/yocto-project-qs/yocto-project-qs.html](http://www.yoctoproject.org/docs/latest/yocto-project-qs/yocto-project-qs.html)


For Ubuntu machine the following commands could be issued:

<pre>sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib build-essential chrpath socat libsdl1.2-dev xterm lib32ncurses5-dev
</pre>

<pre>
sudo apt-get install uuid-dev
</pre>

*Please note that Bluewind Yocto Project was tested with Ubuntu 15.10 only.*

# <span class="mw-headline" id="V2-devkit"><span class="mw-headline-number">2</span> V2-DevKit</span>

The V2-DevKit is designed specifically to accommodate the Viggen Two SOM and offers a wide range of connectivity through the standard radio and wired interfaces.
The board allows the developer to program and debug the Viggen TWO SOM through μUSB interface or standard JTAG connector, while the power supply is fed via a standard AC/DC adapter with
μUSB connector (mobile phone standard).

Please refer to the following picture for the connector's names.

![V2-Carrier](/asset/V2-carrier-LR.png)

## <span class="mw-headline" id="Boot_configuration"><span class="mw-headline-number">2.1</span> Boot configuration</span>

**SW4 DIP SWITCH**

|  **D1/MODE1** | **D2/MODE0**  |  **BOOT MODE**    |
| ------------- | ------------- | ----------------- |
|      OFF      |	   OFF 	    | Boot From Fuses   |
|      OFF      |	   ON       | Serial Downloader |
|      ON       |	   OFF      | Internal Boot     |
|      ON       |	   ON 	    | Reserved          |

**SW3 DIP SWITCH**

|  **D1**  |  **D2**  |  **D3**  |  **D4**  | **BOOT DEVICE** |
| -------- | -------- | -------- | -------- | --------------- |
|   OFF    |    OFF   |    OFF   |    OFF   |	QSPI          |
|   OFF    |	ON    |    ON    |    OFF   |	eMMC          |

# <span class="mw-headline" id="Environment_setup"><span class="mw-headline-number">3</span> Environment setup</span>

## <span class="mw-headline" id="Git_and_repo_utility_setup"><span class="mw-headline-number">3.1</span> Git and repo utility setup</span>

<pre>git config --global user.name "Your Name"
git config --global user.email "Your Email"

mkdir ~/bin (this step may not be needed if the bin folder already exists)
curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
export PATH=~/bin:$PATH
</pre>

## <span class="mw-headline" id="Yocto_jethro_source_download"><span class="mw-headline-number">3.2</span> Yocto Jethro source download</span>

<pre>mkdir bw-release-bsp
cd bw-release-bsp
repo init -u https://github.com/bluewind-embedded-systems/bw-arm-yocto-bsp.git -b imx-4.1.15-1.0.0_ga_viggentwo
repo sync
</pre>

## <span class="mw-headline" id="Source_patching"><span class="mw-headline-number">3.3</span> Source patching </span>

Qemo build could fail depending on used Linux flavour (e.g. Ubuntu 15.10 beta). 

In case of build failure due to the following error:
<pre>| ERROR: User requested feature sdl
|        configure was not able to find it.
|        Install SDL devel
</pre>

go to *bw-release-bsp/sources/poky/meta/recipes-devtools/qemu* and replace it with ![qemu.inc](/patch/qemu.inc)


For more info about this bug see the [Bug 8553 - qemu-native to build on Ubuntu 15.10](https://bugzilla.yoctoproject.org/show_bug.cgi?id=8553 "Title") 

# <span class="mw-headline" id="Yocto_setup_and_build"><span class="mw-headline-number">4</span> Yocto setup and build</span>

Yocto distribution allows the development of several image configurations, from light minimal to full-featured system image.

Following paragraphs depict required steps to build:

*   [Minimal](#Setup_to_build_yocto_minimal_image "Setup to build Yocto Minimal Image")
*   [X11](#Setup_to_build_yocto_x11_image "Setup to build Yocto X11 Image")
*   [Java](#Setup_to_build_yocto_java_test_image "Setup to build Yocto Java Test Image")

## <span class="mw-headline" id="local.conf_customizations"><span class="mw-headline-number">4.1</span> Local configuration tuning</span>

Several *local.conf* parameters can be tuned to properly optimize build resources: refer to the Yocto Reference Manual for detailed explanations.

Take the following hints as rule of thumb to boost the build process.

  - Edit you local.conf file:

<pre>gedit conf/local.conf
</pre>

  - Change parallel build and download directory. Set the build parameters to fully utilize your host machine's resources  

<pre>BB_NUMBER_THREADS = '4'
PARALLEL_MAKE = '-j 6'</pre>

BB_NUMBER_THREADS should be your host machine's number of threads minus 2 or same.  
PARALLEL_MAKE should be the number of threads your host machine has plus two.  

  - Change packages download directory. By default it is set to:  

<pre>DL_DIR ?= "${BSPDIR}/downloads/"
</pre>

change it to a global folder like  

<pre>DL_DIR ?= "/opt/yocto_dl/".
</pre>

  - Don't forget to create the folder and make it available without a sudo permissions.

## <span class="mw-headline" id="Setup_to_build_yocto_minimal_image"><span class="mw-headline-number">4.2</span> Setup to build Yocto Minimal Image</span>

Setup the environment and use build_var folder  
<span style="color:red">Please note: Run only once!</span>

<pre>cd bw-release-bsp
DISTRO=fsl-imx-fb MACHINE=imx6ulviggentwo source fsl-setup-release.sh -b build-minimal-viggentwo
bitbake core-image-minimal
</pre>

The full *fsl-setup-release.sh* is not needed in case of terminal or host restart. To properly setup and build Yocto, you should execute:

<pre>source setup-environment build-minimal-viggentwo
bitbake core-image-minimal
</pre>


## <span class="mw-headline" id="Setup_to_build_yocto_x11_image"><span class="mw-headline-number">4.3</span> Setup to build Yocto X11 Image</span>

Setup the environment and use build_var folder  
<span style="color:red">Please note: Run only once!</span>

<pre>DISTRO=fsl-imx-x11 MACHINE=imx6ulviggentwo source fsl-setup-release.sh -b build-x11-viggentwo
bitbake fsl-image-gui 
</pre>

In case of terminal or host restart please use *setup-environment* setup script.


## <span class="mw-headline" id="Setup_to_build_yocto_java_test_image"><span class="mw-headline-number">4.4</span> Setup to build Yocto Java Test Image</span>

### <span class="mw-headline" id="Prerequisites_for_yocto_java_test_image"><span class="mw-headline-number">4.4.1</span> Prerequisites</span>

Go to *bw-release-bsp/sources* , right click on a blank space of this folder and click on "Open in Terminal"

<pre> git clone git://git.yoctoproject.org/meta-java
</pre>

Close this Terminal now, we won't need it anymore.

In *bw-release-bsp/sources/poky/meta/classes* please create a new file named "remove-libtool.bbclass" with this content:
<pre>
# This class removes libtool .la files after do_install

REMOVE_LIBTOOL_LA ?= "1"

remove_libtool_la() {
   if [ "${REMOVE_LIBTOOL_LA}" != "0" ]; then
      find "${D}" -ignore_readdir_race -name "*.la" -delete
   fi
}

do_install[postfuncs] += "remove_libtool_la"
</pre>

Go to *bw-release-bsp/build-Java-test-Image-viggentwo/conf*, open bblayers.conf and add this line at the end of it:

<pre> BBLAYERS += " ${BSPDIR}/sources/meta-java "</pre>

### <span class="mw-headline" id="Yocto_java_test_image_build"><span class="mw-headline-number">4.4.2</span> Image build</span>

Setup the environment and use build_var folder  
<span style="color:red">Please note: Run only once!</span>

<pre>DISTRO=fsl-imx-x11 MACHINE=imx6ulviggentwo source fsl-setup-release.sh -b build-Java-test-Image-viggentwo
bitbake java-test-image
</pre>

In case of terminal or host restart please use *setup-environment* setup script.

## <span class="mw-headline" id="Setup_to_build_yocto_qt4_demo_image"><span class="mw-headline-number">4.5</span> Setup to build Yocto Qt4 Demo Image</span>

Setup the environment and use build_var folder  
<span style="color:red">Please note: Run only once!</span>

<pre>DISTRO=fsl-imx-x11 MACHINE=imx6ulviggentwo source fsl-setup-release.sh -b build-Qt4-demo-image
bitbake qt4e-demo-image
</pre>

In case of terminal or host restart please use *setup-environment* setup script.

## <span class="mw-headline" id="Useful_bitbake_commands"><span class="mw-headline-number">4.6</span> Useful Bitbake commands</span>

[Bitbake Cheat Sheet](http://elinux.org/Bitbake_Cheat_Sheet)

[Useful bitbake commands](https://community.freescale.com/docs/DOC-94953)

[i.MX Yocto Project: ltib versus bitbake](https://community.freescale.com/docs/DOC-94874)

# <span class="mw-headline" id="Build_outputs"><span class="mw-headline-number">5</span> Build outputs</span>

Images resulting from the build process are located in *tmp/deploy/images/imx6ulviggentwo/*.  

Looking at this folder you will find five main files:

* depending on your build version:

|   **Version**         |                 **Image Name**             |                         **How to use**                                 |
| --------------------- | ------------------------------------------ | ---------------------------------------------------------------------- |
| `Yocto Minimal Image` | core-image-minimal-imx6ulviggentwo.sdcard  | See the mfgtool guide, or use the command dd to mount it on a sd card. | 
|                       | core-image-minimal-imx6ulviggentwo.tar.bz2 | Used to create an root file system on the host.                        |
| `Yocto X11`           | fsl-image-gui-imx6ulviggentwo.sdcard       | See the mfgtool guide, or use the command dd to mount it on a sd card. |
|                       | fsl-image-gui-imx6ulviggentwo.tar.bz2      | Used to create an root file system on the host.                        |
| `Java Test Image`     | java-test-image-imx6ulviggentwo.sdcard 	 | See the mfgtool guide, or use the command dd to mount it on a sd card. |
|                       | java-test-image-imx6ulviggentwo.tar.bz2    | Used to create an root file system on the host.                        |
| `Yocto Qt4 Demo Image`| qt4e-demo-image-imx6ulviggentwo.sdcard     | See the mfgtool guide, or use the command dd to mount it on a sd card  |
|                       | qt4e-demo-image-imx6ulviggentwo.tar.bz2    | Used to create an root file system on the host.                        |

* Kernel and boot

|      **Image Name**        |       **How to use**        |
| -------------------------- | --------------------------- |
| zImage                     | Linux kernel image          |
| u-boot-emmc-2015.04-r0.img | u-boot built for eMMC boot. |

|    **Device Tree Name**     |                  **Boot Device**                         |
| --------------------------- | -------------------------------------------------------- |
| zImage-imx6ul-viggentwo.dtb |	Device tree with default setup of ViggenTwo eval board. |

# <span class="mw-headline" id="Image_deploy"><span class="mw-headline-number">6</span> Image deploy </span>

## <span class="mw-headline" id="emmc_programming"><span class="mw-headline-number">6.1</span> eMMC programming </span>

### <span class="mw-headline" id="Mfgtool_download"><span class="mw-headline-number">6.1.1</span> MFGTool download </span>

NXP's Manufacturing Tool can be used to flash the device.

Download the [mfgtool](/mfgtool/mfgtools.zip) and unzip it to whatever folder.


*Please note that Bluewind Yocto build was tested on Windows 10 Edu x64.*


### <span class="mw-headline" id="Hardware_setup"><span class="mw-headline-number">6.1.2</span> Hardware setup </span>

Please refer to the [V2-DevKit](#V2-devkit) block diagram for the connector's names used in the following instructions.

In order to download the images to the eMMC with MFGTOOL, it's necessary to change the boot option of the iMX processor from internal to serial downloader. 

  - Move pin  1 of SW4 in ON position and pin 2 of SW4 in OFF position.
  - Power on the board connecting a 5V 2A µUSB power supply adapter on PWRIN connector.
  - Connect the manufacturing host PC to the V2-DevKit through a µUSB cable. Connect the µUSB side to USB-OTG connector and the other side to PC’s USB host port.
  - Check if the ViggenTwo board is recognized as “HID compatible device”.

![HID-Device](/asset/HID-Device.png)

### <span class="mw-headline" id="Mfgtool_setup"><span class="mw-headline-number">6.1.3</span> MFGTool setup </span>

Rename the generated images [here](#Build_outputs) and copy them following the table:

|                **Original file name**                 |           **Renamed**        	  |       **Where to copy**          |
| ----------------------------------------------------- | ------------------------------- | -------------------------------- |
| u-boot-emmc-2015.04-r0.imx                            | u-boot-imx6ulviggentwo_emmc.imx | Profiles\Linux\OS Firmware\files |
| zImage--4.1.15-r0-imx6ulviggentwo-xxxxxxxxxxxxxx.bin  | zImage (Yes, without extension) |	Profiles\Linux\OS Firmware\files |
| zImage--4.1.15-r0-imx6ul-viggentwo-xxxxxxxxxxxxxx.dtb | zImage-imx6ul-viggentwo-emmc.dtb|	Profiles\Linux\OS Firmware\files |
| core-image-minimal-imx6ulviggentwo-xxxxxxxxxxxxxx.rootfs.tar.bz2 | rootfs.tar.bz2       | Profiles\Linux\OS Firmware\files |

**NOTE 1**: xxxxxxxxxxxxxx is the date of the day you compiled  
**NOTE 2**: core-image-minimal-imx6ulviggentwo-xxxxxxxxxxxxxx.rootfs.tar.bz2 is just an example, required files depend on your image build:  
java-test-image-imx6ulviggentwo-xxxxxxxxxxxxxx.rootfs.tar.bz2  
fsl-image-gui-imx6ulviggentwo-xxxxxxxxxxxxxx.rootfs.tar.bz2  
qt4e-demo-image-imx6ulviggentwo-xxxxxxxxxxxxxx.rootfs.tar.bz2  

### <span class="mw-headline" id="Image_programming"><span class="mw-headline-number">6.1.4</span> Image programming </span>

![MFGTOOLS](/asset/MFGTOOLS.png)

  - Search for the *mfgtool2-yocto-mx-viggentwo-sd2_eMMC.vbs* script and execute it.
  - Press the GUI "Start" button.
  - Wait until the end of process.
  - Detach the USB-Device cable.
  - Power off the board disconnecting the power supply cable.
  - Change the boot configuration into "Internal boot" by moving pin 1 of SW4 in OFF position and pin 2 of SW4 in ON position.
  - Connect a USB cable into "DEBUG UART uUSB Connector" to a terminal host PC.
  - Open a terminal client application (e.g. *Putty*, *minicom*, *TeraTerm*). Setup the proper COM port with 115200-8-N-1 standard settings.
  - Power on the board.

If the device has been properly flashed, boot process output and Linux console should be visible. Depending on the deployed images, proper 
GUI or graphical content could be displayed on the LCD screen.
