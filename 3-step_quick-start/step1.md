[<< Return to Overview](README.md)

**STEP 1** | [STEP 2](step2.md) | [STEP 3](step3.md)

# Step 1

1. **Setup Vitis AI and run example applications with minimal dependencies**
2. Add Vitis AI Library and Model Zoo and run dependent sample applications
3. Build and run more comprehensive demonstration code
---

This step will cover installing the Vitis AI deployment/runtime environment and testing a simple application on the evaluation board.  Vitis AI can be broadly broken down into the following components:

 1. Docker containers of development & deployment environment
    - [https://hub.docker.com/r/xilinx/vitis-ai/tags](https://hub.docker.com/r/xilinx/vitis-ai/tags)
 2. Vitis AI GitHub repository for sources
    - [https://github.com/Xilinx/Vitis-AI](https://github.com/Xilinx/Vitis-AI)
 3. Linux install packages (\*.deb) for runtime dependencies

Here we will focus on the installation and use of items (1) and (2) above.  While subsequent steps will add the use of item (3).

## 1.1 Setup Vitis AI
As noted above, you'll need Docker installed on your Linux host environment; the community edition will suffice.  I recommend following instructions from [docs.docker.com/install](https://docs.docker.com/install/), but some instruction are also provided on the Vitis AI GitHub repo here:
 - [Vitis-AI@GitHub](https://github.com/Xilinx/Vitis-AI/tree/v1.0) / README.md > [Getting Started](https://github.com/Xilinx/Vitis-AI/tree/v1.0#getting-started) > [Install Docker](https://github.com/Xilinx/Vitis-AI/blob/v1.0/doc/install_docker/README.md)

### 1.1.1 Pull Docker Image
Because the Docker tool is linked to Docker Hub it will automatically download the required Vitis AI runtime image from the hub the first time Docker is used to run a container for that image.  However, I recommend downloading the image in advance to help separate any potential download issues from startup issues. The command to download/pull the v1.0 tag of the runtime image is as follows.
 ```
 $ docker pull xilinx/vitis-ai:runtime-1.0.0-cpu
 ```
### 1.1.2 Clone Repo
Since the Docker container handles most of what will be required for deployment (in terms of the required development environment), the only other action needed on the Linux host machine is to clone the Vitis AI repo from GitHub.
 ```
 $ git clone https://github.com/Xilinx/Vitis-AI.git
 $ cd Vitis-AI/
 $ git checkout v1.0
 ```
That's it! Vitis AI should now be setup on the Linux host.

## 1.2 Build Example Application
Vitis AI offers several different APIs through a collection of libraries.  A simplified diagram of how these APIs stack up is given below (a more detailed diagram can be found here: [Vitis-AI-Library@GitHub](https://github.com/Xilinx/Vitis-AI/tree/v1.0/Vitis-AI-Library) / README.md).

| Application |
|:---:|
Vitis AI Library: Model Libs (API1/API2)
Vitis AI Library: Base Libs
--> ***Vitis AI Runtime: Unified API (API0)*** <--
Vitis Runtime: Implementation API / Core Libs (DNNDK)
XRT (Xilinx RunTime)

We'll start by building an example application that uses the Unified API (API0) layer highlighted above.  This will keep things simple for now by avoiding dependencies on the higher-level libs.  Several such example applications are supplied as part of the Vitis AI repo and can be found here:
 - ZCU102: [github.com/Xilinx/Vitis-AI/tree/v1.0/mpsoc/vitis_ai_samples_zcu102](https://github.com/Xilinx/Vitis-AI/tree/v1.0/mpsoc/vitis_ai_samples_zcu102)
 - ZCU104: [github.com/Xilinx/Vitis-AI/tree/v1.0/mpsoc/vitis_ai_samples_zcu104](https://github.com/Xilinx/Vitis-AI/tree/v1.0/mpsoc/vitis_ai_samples_zcu104)

A table describing these (7) applications can also be found here: Vitis-AI@GitHub > README.md > [Programming with Vitis AI section](https://github.com/Xilinx/Vitis-AI/tree/v1.0#programming-with-vitis-ai).

### 1.2.1 Run Docker Container
The Docker image that was pulled from Docker Hub will be run to access the build environment (toolchain, libs, headers, etc.) and will also be given access to the local clone of the Vitis AI repo so it can reach the sources to build.  For convenience, a shell script is supplied with necessary commands to both launch the container and give it access to the local repo.  The script also runs Docker in interactive mode so you have a chance to interact with the container.  Launch the container by running the script from the root Vitis AI repo directory as shown below.

```
$ ./docker_run.sh xilinx/vitis-ai:runtime-1.0.0-cpu
```
If successful, the `pwd` should change from the Vitis AI repo root directory to the 'workspace' directory within the container.  For example:

```
$ pwd # before running Docker script
/home/user/Vitis-AI
$ ./docker_run.sh xilinx/vitis-ai:runtime-1.0.0-cpu
$ pwd # after running Docker script
/workspace
```
Optionally, the following can be noted from the contents of the docker_run.sh file.
 - The last `docker run` command (following the `else` statement) is the one actually used in this case.  The others commands are for other Docker Vitis AI images.
 - Several `-v` arguments specifying which host paths should be made accessible to the container .
 - In particular, the `-v $HERE:/workspace` maps the directory from which Docker was launched to the /workspace directory within the container.
 - The one (and only) command actually run by the script would look similar to:
   - `docker run --device=/dev/dri/renderD128 -v /opt/xilinx/dsa:/opt/xilinx/dsa -v /opt/xilinx/overlaybins:/opt/xilinx/overlaybins -e USER=shaun -e UID=1000 -e GID=1000 -v /home/user/Vitis-AI:/workspace -w /workspace -it --rm --network=host xilinx/vitis-ai:runtime-1.0.0-cpu`

Instructions on running Vitis-AI Docker containers and a link to the container images on Docker Hub are also provided in the Vitis AI repo: Vitis-AI@GitHub / doc / install_docker / [load_run_docker.md](https://github.com/Xilinx/Vitis-AI/blob/v1.0/doc/install_docker/load_run_docker.md)

### 1.2.2 Build Application
Since `/workspace` in the container is basically a symbolic link to the cloned Vitis AI repo, simply browse to the example code and run `make`.  For this tutorial we'll arbitrarily select one example that works with images and one that works with video.  Starting with the image example:
```
$ cd mpsoc/vitis_ai_samples_zcu102/resnet50
$ make
```
A new executable `resnet50` file should appear in that directory.  Now the video example:
```
$ cd ../video_analysis
$ make
```
Again, a new executable file should appear after Make has completed; this time named `video_analysis`.

Since the goal of this first step is mostly just to test the setup we won't linger too much on details of the applications being built but it's worth pointing out one important fact at this point - building a Vitis-AI application will always consist of at least these two components:
 1. Host application code
 2. Compiled network/model

In the case of these examples we're only compiling the host code and then combining that binary with the binary of a pre-compiled network.  The network binary was provided as part of the example and comes from the `model` directory.  For example:
 - /workspace/mpsoc/vitis_ai_samples_zcu102/resnet50/model/dpu_resnet50.elf
 - /workspace/mpsoc/vitis_ai_samples_zcu102/video_analysis/model/dpu_ssd.elf

Generating these binaries would require use of the Vitis AI development toolchain (i.e xilinx/vitis-ai:tools image) which is not covered in this tutorial.

Since the Docker container is no longer needed it should be closed before proceeding.
```
$ exit
```

## 1.3 Board Setup
In this section we'll prepare the board, which mostly consists of preparing the SD card, and then boot it up.

### 1.3.1 Create Board SD Card
This section covers burning a pre-compiled board image to an SD card and copying over the example applications.  First download the applicable board image from here: Vitis-AI@GitHub / mpsoc / README.md > [Board Images](https://github.com/Xilinx/Vitis-AI/tree/v1.0/mpsoc#board-images)

Then burn the image to an SD card at least 8GB in size.  On Windows this can be done with a free application such as one of the following:
 - [Win32DiskImager](https://sourceforge.net/projects/win32diskimager/)
 - [balenaEtcher](https://www.balena.io/etcher/)

Instructions using Etcher can be found in the [Vitis AI User Guide (UG1414 v1.0)](https://www.xilinx.com/support/documentation/sw_manuals/vitis_ai/1_0/ug1414-vitis-ai.pdf): Ch. 2 (Quick Start) > Setting Up the Evaluation Board > Flashing the OS Image to the SD Card

On Linux `dd` can be used (replacing *`<sdx>`* below with the appropriate block device):
```
$ gunzip -c xilinx-zcu104-dpu-v2019.2.img.gz | sudo dd of=/dev/<sdx> bs=1M
```
The resulting SD card will have two partitions: BOOT (FAT32) and ROOTFS (Ext4).  The BOOT partition contains bootloader firmware while the ROOTFS partition is the OS filesystem.

Now that the SD card is ready we just need to copy the following three items to the card from the host:

 1. Example applications
 2. Input data
 3. Vitis AI core libs

The BOOT partition will be used to hold these items mostly because it has more space than the ROOTFS partition, but it's also accessible from a Windows machine.

### 1.3.2 Add Example Applications to SD Card

To make this step as simple as possible we'll just copy the entire board-specific sample directory.  For example, from the root directory of the Vitis AI repo run:

```
$ cp -r mpsoc/vitis_ai_samples_zcu<102|104> <path-to-mounts>/BOOT/
```
For the ZCU102 board this would look something like:
```
$ cp -r mpsoc/vitis_ai_samples_zcu102 /media/user/BOOT/
```
### 1.3.3 Add Input Data to SD Card

The video_analysis example includes a sample video clip, but for the resnet50 example a dataset of images is expected.  An archive of sample images is provided by Xilinx and can be downloaded using a free Xilinx.com account from here:
 - [https://www.xilinx.com/bin/public/openDownload?filename=vitis_ai_library_r1.0_images.tar.gz](https://www.xilinx.com/bin/public/openDownload?filename=vitis_ai_library_r1.0_images.tar.gz)

Alternatively, you can navigate to the link in Vitis AI documentation: Vitis-AI@GitHub / Vitis-AI-Library / README.md > Quick Start > [Setting Up The Target](https://github.com/Xilinx/Vitis-AI/tree/v1.0/Vitis-AI-Library#setting-up-the-target) > 3. Installing AI Library Package > Download the demo image files...

Once you have the downloaded "vitis_ai_library_r1.0_images.tar.gz" file extract it on the host machine and then copy it to the SD card.  Here's an example on Linux:

```
$ cd /media/user/BOOT
$ tar -xaf ~/Downloads/vitis_ai_library_r1.0_images.tar.gz
```

### 1.3.3 Add Core Libs to SD Card

As was shown in the stack diagram in section 1.3 above, the only dependencies for an API0 example application should be the core libs and XRT.  Since XRT is pre-installed on the provided SD-card image we only need to take care of the core libs.

The Vitis AI Docker container includes these libs, but also includes a script to install them on a target system.  These can be copied out of the Docker container and into the SD card.  For example:

```
$ cd <Vitis-AI root repo directory>
$ ./docker_run.sh xilinx/vitis-ai:runtime-1.0.0-cpu
$ cp -r /opt/vitis_ai/xilinx_vai_board_package ./
$ exit
$ mv xilinx_vai_board_package /media/user/BOOT
```
| Note: |
|---|
The Docker run script also enables network access which in turn means files could be transferred to a board via ethernet cable directly from the Docker container. If you'd prefer this approach you can skip this step and follow instructions in [UG1414 (v1.0)](https://www.xilinx.com/support/documentation/sw_manuals/vitis_ai/1_0/ug1414-vitis-ai.pdf) once the board is running.  The applicable instructions are given in this section: *Ch.2 (Quick Start) > Setting Up The Eval Board > Installing Vitis AI Package on the Evaluation Board*.

Interested readers can view the contents of the `xilinx_vai_board_package/pkgs/lib` directory to view the core libs.  When this script is run on the board it will simply copy required files/libs to the appropriate locations on the board filesystem.

At this point the SD card should be ready and we can proceed to remaining board setup.

### 1.3.4 Finalize Board Setup

Complete board setup by:

 1. Inserting the SD card into the board SD slot
 2. Connecting board power
 3. Connecting the Micro-USB cable for board UART
    - For a Windows host you may need to install the appropriate USB to UART bridge driver on first-time use.  See [AR# 33569](https://www.xilinx.com/support/answers/33569.html) for more info.
    - ZCU102 boards have two Micro-USB ports so make sure you use the one labelled 'UART' (as oppposed to the one labelled 'JTAG')

If you have any optional hardware it can also be connected at this time.    

 - An Ethernet cable will provide a network connection between the board and the host computer.
 - A DisplayPort monitor and cable will provide graphical output.
 - A USB mouse and keyboard connected to the board through a USB hub (together with the monitor) will allow the board to be used standalone (i.e. without the need for a host).

Additional info on board setup can be found in [UG1414 (v1.0)](https://www.xilinx.com/support/documentation/sw_manuals/vitis_ai/1_0/ug1414-vitis-ai.pdf) here: *Ch.2 (Quick Start) > Setting Up The Eval Board*.

### 1.4 Running the Example

In this section we will run the board, install core libs, and finally run some examples.

### 1.4.1 Start the Board

First connect to the board over UART from the host machine.  If using the board standalone this isn't strictly necessary but still nice to have.  The host should use the first interface made available by the USB-to-UART bridge (e.g. Interface 0 on Windows) with the following parameters:
 - baud rate: 115200
 - data bit: 8
 - stop bit: 1
 - no parity

On Windows, open the Device Manager to map Ports > Interface 0 to an actual COM port #.  A program like PuTTY or Tera Term can be used to establish the connection.

Power on the board using the sliding power switch and you should immediately see output on the terminal.  For example:

```
Xilinx Zynq MP First Stage Boot Loader
Release 2019.2   Nov 22 2019  -  19:57:22
NOTICE:  ATF running on XCZU9EG/silicon v4/RTL5.1 at 0xfffea000
NOTICE:  BL31: Secure code at 0x0
NOTICE:  BL31: Non secure code at 0x10080000
NOTICE:  BL31: v2.0(release):xilinx-v2019.1-12-g713dace9
NOTICE:  BL31: Built : 11:09:52, Nov 22 2019
PMUFW:  v1.1


U-Boot 2019.01 (Nov 22 2019 - 11:11:36 +0000)

.
.
.

mount: /mnt: /dev/mmcblk0p1 already mounted on /run/media/mmcblk0p1.
attempting to run /mnt/init.sh
/mnt
root@xilinx-zcu102-2019_2:~#
```

Unless the board is connected to a network/host with a DHCP server, the boot process will be a little slow as U-boot and Linux timeout waiting for an address assignment.  If using the board standalone, you will eventually see a desktop interface appear on the DisplayPort monitor.

From here forward, you can use either the UART terminal interface to enter commands from the host or open the xfce4-terminal application (top left-most icon) from the desktop interface to enter commands directly on the board when used standalone.

### 1.4.1 Install Core Libs

Install core libs by browsing to the SD card and running the install script:

```
$ cd /mnt/xilinx_vai_board_package/
$ ./install.sh
```
This should result in some output confirming that installation completed.

From here onwards we'll also assume use of a ZCU102 board so syntax can be simplified from `zcu<102|104>` to just `zcu102`.  If you have a ZCU104 board just make the appropriate replacement.

### 1.4.2 Run Terminal Example

Now let's browse to the sample applications and run some examples.  If you don't have any of the optional hardware you'll be limited to the Python examples because all the other examples rely on a display output being available.  So let's start with Python Inception example.  Similar to the ResNet50 example, Inception requires sample input images.  So start by copying the sample images to the expected location:
```
$ cd /mnt/vitis_ai_samples_zcu102/
$ cp -r /mnt/vitis_ai_library_r1.0_images/samples/classification/images ./
```
Now the Inception example can be run:
```
$ cd inception_v1_mt_py/
$ python3 inception_v1.py 3 $(pwd)/dpuv2_rundir/
172.26 FPS
```
The argument '3' sets the number of threads to use and the last argument must be the absolute path to a directory containing a `meta.json` file.  You can also run any of the examples without arguments to get self-help info about how the example should be run.

| Note: |
|---|
The meta.json file provides runtime parameters and is generated by Vitis AI development tools not covered in this tutorial.

The Python examples only output the measured framerate (i.e. rate of frames processed by the DPU).  This is good for a terminal-only setup, but much less interesting that seeing graphical results.  The remaining examples will require the DisplayPort monitor.

### 1.4.2 Run Graphical Example

If you are using a terminal connection you will need to set an environment variable to tell X11 to target the local display.  If using the board standalone, you should skip this.
```
$ export DISPLAY=:0.0 # Not needed for standalone board use
```
Now let's run the ResNet50 example.  This also requires the sample images so if you haven't already copied these to the appropriate location refer to that step in the previous section.
```
$ cd /mnt/vitis_ai_samples_zcu102/resnet50
$ ./resnet50 dpuv2_rundir/
```
The program displays each image being processed on the monitor so you'll see several images quickly displayed.  On the terminal, you can review the results of the processing.  For example:
```
.
.
.

Image : 036.JPEG
top[0] prob = 0.970945  name = kite
top[1] prob = 0.017783  name = coucal
top[2] prob = 0.003968  name = vulture
top[3] prob = 0.001874  name = bittern
top[4] prob = 0.001460  name = goose

Image : 037.JPEG
top[0] prob = 0.887782  name = carton
top[1] prob = 0.020879  name = file, file cabinet, filing cabinet
top[2] prob = 0.016260  name = desk
top[3] prob = 0.012664  name = safe
top[4] prob = 0.012664  name = projector
```
As seen above, the last two images were calssified as kite and carton with probabilities of ~97% and ~89%, respectively.

Finally, we'll run a video example:
```
$ cd /mnt/vitis_ai_samples_zcu102/video_analysis
$ ./video_analysis video/structure.mp4 dpuv2_rundir/
```
This will display a traffic scene video for approximately 30 seconds.  The video shows the result of 3-class object detection (using SSD) by adding bounding boxes around the detected class (vehicle, cyclist, pedestrian).

### 1.4.3 Build and Run Other Examples

If you'd like to run other examples, you don't actually have to return to the host Docker environment to cross-compile them.  The provided SD card image is setup for self-hosting so you can compile examples locally.  Doing so requires just one minor change to the `Makefile`: comment out the `override SYSROOT` variable.  Here's an example:
```
$ cd /mnt/vitis_ai_samples_zcuzcu102/adas_detection
$ sed -i 's/^override SYSROOT/#&/' Makefile
$ make
$ ./adas_detection video/adas.avi dpuv2_rundir/
```
This runs an example similar to `video_analysis`, except Yolo-v3 is used.

This completes Step 1 of this tutorial and you're invited to explore the example code to see how the Vitis AI Unified API (API0) can be used for your own application.  For Step 2 we'll move up the software stack to higher level Vitis AI Library based examples.

**STEP 1** | [STEP 2](step2.md) | [STEP 3](step3.md)
