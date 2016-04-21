# Viggen Two Build Yocto release

<div id="mw-content-text" dir="ltr" class="mw-content-ltr" lang="en">

<span style="font-size:16pt;">ViggenTwo - Yocto fsl-4.1.15-1.1.0 Jethro Build Yocto release</span>

</div>

* * *

# <span class="toc" id="toctitle">Contents</span>

*   [<span class="tocnumber">1</span> <span class="toctext">Installing required packages</span>](#Installing_required_packages)
*   [<span class="tocnumber">2</span> <span class="toctext">Documentation</span>](#Documentation)
*   [<span class="tocnumber">3</span> <span class="toctext">Download Yocto Jethro for i.MX6UL Bluewind source</span>](#Download_Yocto_Jethro_for_i.MX6UL_Bluewind_source)
*   [<span class="tocnumber">5</span> <span class="toctext">Setup and build Yocto</span>](#Setup_and_build_Yocto)
    *   [<span class="tocnumber">5.1</span> <span class="toctext">Setup to build Yocto GUI/X</span>](#Setup_to_build_Yocto_GUI.2FX)
    *   [<span class="tocnumber">5.2</span> <span class="toctext">Setup to build Yocto QT5/Frame Buffer</span>](#Setup_to_build_Yocto_QT5.2FFrame_Buffer)
    *   [<span class="tocnumber">5.3</span> <span class="toctext">local.conf customizations</span>](#local.conf_customizations)
    *   [<span class="tocnumber">5.4</span> <span class="toctext">Build Results</span>](#Build_Results)
*   [<span class="tocnumber">6</span> <span class="toctext">Useful Bitbake commands</span>](#Useful_Bitbake_commands)

# <span class="mw-headline" id="Installing_required_packages"><span class="mw-headline-number">1</span> Installing required packages  
</span>

Follow the link below and install all required packages on your machine.

[www.yoctoproject.org/docs/latest/yocto-project-qs/yocto-project-qs.html](http://www.yoctoproject.org/docs/latest/yocto-project-qs/yocto-project-qs.html)

For example, if your building machine is an Ubuntu machine:

<pre>sudo apt-get install gawk wget git-core diffstat unzip texinfo gcc-multilib build-essential chrpath socat libsdl1.2-dev xterm lib32ncurses5-dev
</pre>

Bluewind Yocto build was tested with Ubuntu 15.10 only!

# <span class="mw-headline" id="Documentation"><span class="mw-headline-number">2</span> Documentation</span>

# <span class="mw-headline" id="Download_Yocto_Jethro_for_i.MX6UL_Bluewind_source"><span class="mw-headline-number">3</span> Download Yocto Jethro for i.MX6UL Bluewind source</span>

<pre>git config --global user.name "Your Name"
git config --global user.email "Your Email"

mkdir ~/bin (this step may not be needed if the bin folder already exists)
curl http://commondatastorage.googleapis.com/git-repo-downloads/repo > ~/bin/repo
chmod a+x ~/bin/repo
export PATH=~/bin:$PATH

mkdir bw-release-bsp
cd bw-release-bsp
repo init -u git@git.bwlocal.it:za/bw-arm-yocto-bsp.git -b imx-4.1.15-1.0.0_ga_viggentwo
repo sync
</pre>

# <span class="mw-headline" id="Setup_and_build_Yocto"><span class="mw-headline-number">5</span> Setup and build Yocto</span>

You can create an image for X11 or create an image for QT5/Embedded. Please select one of the following option:

*   [GUI/X](#Setup_to_build_Yocto_GUI.2FX "Setup to build Yocto GUI/X")
*   [QT/Embedded](#Setup_to_build_Yocto_QT5.2FFrame_Buffer "Setup to build Yocto QT5/Frame Buffer")

## <span class="mw-headline" id="Setup_to_build_Yocto_GUI.2FX"><span class="mw-headline-number">5.1</span> Setup to build Yocto GUI/X</span>

Setup the environment and use build_var folder  
<span style="color:red">Note: Run only once!</span>

<pre>DISTRO=fsl-imx-x11 MACHINE=imx6ulviggentwo source fsl-setup-release.sh -b build-x11-viggentwo
bitbake fsl-image-gui 
</pre>

<div class="note note-info">Comment:  
If you close your terminal and wish to build Yocto again, you should run (instead of fsl-setup-release.sh):  
</div>

<pre>source setup-environment build-x11-viggentwo
bitbake fsl-image-gui
</pre>

## <span class="mw-headline" id="Setup_to_build_Yocto_QT5.2FFrame_Buffer"><span class="mw-headline-number">5.2</span> Setup to build Yocto QT5/Frame Buffer</span>

Use the folowing commands to build and test QT over frame buffer.  
Setup the environment and use build_var folder  
<span style="color:red">Note: Run only once!</span>

<pre>DISTRO=fsl-imx-x11 MACHINE=imx6ulviggentwo source fsl-setup-release.sh -b build-qt-viggentwo
bitbake fsl-image-qt5
</pre>

<div class="note note-info">Comment:  
If you close your terminal and wish to build Yocto again, you should do:  
</div>

<pre>source setup-environment build-x11-viggentwo
bitbake fsl-image-qt5
</pre>

## <span class="mw-headline" id="local.conf_customizations"><span class="mw-headline-number">5.3</span> local.conf customizations</span>

Edit you local.conf file:

<pre>gedit conf/local.conf
</pre>

Change parallel build and download directory. Set the build parameters to fully utilize your host machine's resources  

<pre>BB_NUMBER_THREADS = '4'
PARALLEL_MAKE = '-j 6'</pre>

BB_NUMBER_THREADS should be your host machine's number of threads minus 2 or same.  
PARALLEL_MAKE should be the number of threads your host machine has plus two.  

Change packages download directory. By default it is set to:  

<pre>DL_DIR ?= "${BSPDIR}/downloads/"
</pre>

change it to a global folder like  

<pre>DL_DIR ?= "/opt/yocto_dl/".
</pre>

Don't forget to create the folder and make it available without a sudo permissions.

## <span class="mw-headline" id="Build_Results"><span class="mw-headline-number">5.4</span> Build Results</span>

The resulted images are located in tmp/deploy/images/imx6ulviggentwo/.  
Looking at tmp/deploy/images/imx6ulviggentwo/ you will find 6 main files that are linked to the actual file  

<table class="wikitable">

<tbody>

<tr>

<th scope="col">Image Name  
</th>

<th scope="col">How to use  
</th>

</tr>

<tr>

<td>fsl-image-gui-imx6ulviggentwo.ext4  
</td>

<td>Not in Use  
</td>

</tr>

<tr>

<td>fsl-image-gui-imx6ulviggentwo.sdcard</td>

<td>Not in Use  
</td>

</tr>

<tr>

<td>fsl-image-gui-imx6ulviggentwo.tar.bz2</td>

<td>Used to create an root file system on the host.</td>

</tr>

<tr>

<td>zImage</td>

<td>Linux kernel image</td>

</tr>

<tr>

<td>u-boot-emmc-2015.04-r0.img</td>

<td>u-boot built for eMMC boot.</td>

</tr>

<tr>

<th scope="col">Device Tree Name  
</th>

<th scope="col">Boot Device  
</th>

</tr>

<tr>

<td>zImage-imx6ul-viggentwo.dtb</td>

<td>Devive tree with deafault setup of ViggenTwo eval board.</td>

</tr>

</tbody>

</table>

# <span class="mw-headline" id="Useful_Bitbake_commands"><span class="mw-headline-number">6</span> Useful Bitbake commands</span>

[Bitbake Cheat Sheet](http://elinux.org/Bitbake_Cheat_Sheet)

[Useful bitbake commands](https://community.freescale.com/docs/DOC-94953)

[i.MX Yocto Project: ltib versus bitbake](https://community.freescale.com/docs/DOC-94874)