[<< Return to Overview](README.md)

[STEP 1](step1.md) | **STEP 2** | [STEP 3](step3.md)

# Step 2

1. Setup Vitis AI and run example applications with minimal dependencies
2. **Add Vitis AI Library & Model Zoo and run dependent sample applications**
3. Build and run more comprehensive demonstration code

This step will cover installing the Vitis AI Library and Model Zoo on the targeted evaluation board.  It assumes you've already setup the board and SD card as provided in Step 1.  It will also cover running some simple demos that utilize the installed library and models.

Using the same diagram as in Step 1, the focus of this step is indicated below.

| Application |
|:---:|
--> ***Vitis AI Library: Model Libs (API1/API2)*** <--
Vitis AI Library: Base Libs
Vitis AI Runtime: Unified API (API0)
Vitis Runtime: Implementation API / Core Libs (DNNDK)
XRT (Xilinx RunTime)

| Note: |
|---|
This step provides a more comprehensive walkthrough of the same procedure given here: Vitis-AI@GitHub / [Vitis-AI-Library (README.md)](https://github.com/Xilinx/Vitis-AI/tree/v1.0/Vitis-AI-Library) > [Quick Start](https://github.com/Xilinx/Vitis-AI/tree/v1.0/Vitis-AI-Library#quick-start).

## 2.1 Download & Install Vitis AI Packages

### 2.1.1 Download Packages

The Vitis AI Library & Model Zoo are provided as Linux install packages (\*.deb).  Download these using links provided in Vitis AI GitHub documentation.  A free Xilinx.com account may be needed.  The links are found here:
 1. Vitis AI Model Package: Vitis-AI@GitHub / [Vitis-AI-Library (README.md)](https://github.com/Xilinx/Vitis-AI/tree/v1.0/Vitis-AI-Library) > [Setting Up the Target](https://github.com/Xilinx/Vitis-AI/tree/v1.0/Vitis-AI-Library#setting-up-the-target) > 2. Installing AI Model Package > Select ZCU102 or ZCU104 AI Model download link
 2. Vitis AI Library Package: Vitis-AI@GitHub / [Vitis-AI-Library (README.md)](https://github.com/Xilinx/Vitis-AI/tree/v1.0/Vitis-AI-Library) > [Setting Up the Target](https://github.com/Xilinx/Vitis-AI/tree/v1.0/Vitis-AI-Library#setting-up-the-target) > 3. Installing AI Library Package > Download the Vitis AI Library 1.0

Once downlaoded you should have the following two files:
 1. vitis_ai_model_<ZCU102|ZCU104>\_2019.2-r1.0.deb
 2. vitis_ai_library_2019.2-r1.0.deb

 These files will need to be transferred to the board either by copying to the SD card or over Ethernet (e.g. `scp`).  Here will simply copy the files to the SD card but in any case the capacity of the Ext4 partition on the card will first need to be increased to make room for the installation.  

### 2.1.2 Grow ROOTFS Partition

 The supplied SD card image is for a 4GB card which will not have enough free space to install the additional packages.  So we need to grow the Ext4 partition into the remaining unused space.  An example of doing this on Linux is given below.  If you prefer a GUI then this can be done with the GParted application.
 ```
 $ # Resize ROOTFS (2nd) partition
 $ sudo parted /dev/sdx print
 $ sudo parted /dev/sdx resizepart 2 100%
 $ # Resize Ext4 filesystem to match partition size
 $ sudo resize2fs -p /dev/sdd2
 ```
| Note: |
|---|
There are maybe commerical tools that can do this re-size on Windows but changing Ext4 partitions is generally better suited for a Linux machine.

### 2.1.3 Install Vitis AI Packages

There will not be enough space on the BOOT partition for the .deb files so a Linux host will also be needed for this step to copy the files to the ROOTFS filesystem.  For example:
```
$ sudo cp vitis_ai_model_ZCU102_2019.2-r1.0.deb /media/user/ROOTFS/home/root
$ sudo cp vitis_ai_library_2019.2-r1.0.deb /media/user/ROOTFS/home/root
$ sync
```
Now boot the board and using your terminal interface of choice (i.e. UART, SSH, or local/standalone) browse to the location of the packages and install them:
```
$ cd
$ dpkg -i vitis_ai_library_2019.2-r1.0.deb
$ dpkg -i vitis_ai_model_ZCU102_2019.2-r1.0.deb
```
This will install files to `/usr/share/vitis_ai_library` including the pre-compiled model zoo binaries in the `models` directory and example applications in the `demo` and `samples` directories.

In this case, the examples are provided pre-compiled so there's no need to build them.  However, source code and build scripts are provided should you want to make any changes and re-build.  Additionally, the same source can be found in the Git repo:
 - Vitis-AI@GitHub / Vitis-AI-Library / [demo](https://github.com/Xilinx/Vitis-AI/tree/v1.0/Vitis-AI-Library/demo)
 - Vitis-AI@GitHub / Vitis-AI-Library / [samples](https://github.com/Xilinx/Vitis-AI/tree/v1.0/Vitis-AI-Library/samples)

 ## 2.2 Run Vitis AI Library Example

 The Vitis AI Library package provides two types of examples:
  - **samples:** These examples provide a suite of simple performance tests for each sub-category (e.g. classification, segmentation, facedetect, etc.) in the folder.  Note that some of these examples rely on pre-built binaries; consequently, they are not all appropriate as reference code.
  - **demo:** These examples rely strictly on Vitis AI Library APIs so all the source code here can be re-used as reference code.  They cover a range of API levels from API0 all the way up to API2, in addition to some multi-task demos.

For a quick validation of our setup (i.e. correct use of installed packages) a 'sample' example will be more appropriate.  The 'demo' examples will be exercised in the final step (Step 3) of this tutorial.

 ### 2.2.1 Run Sample Image Application

For information on how to run any of the 'sample' applications a `readme` file is provided in each sub-directory. We'll start with an image based example: facedetect.  So for example:
```
$ cd /usr/share/vitis_ai_library/samples/facedetect
$ cat readme
Important:
  ./test_jpeg_facedetect, ./test_performance_facedetect, ./test_video_facedetect the first param followed must be followed with model name.
  Valid model name:
    densebox_320_320
    densebox_640_360

1, compile

    execute the following command:

        sh build.sh

2, copy the compiled executable file and test image to the development board.

   run the executable file.

    sample : ./test_jpeg_facedetect densebox_320_320 sample_facedetect.jpg
    output :
WARNING: Logging before InitGoogleLogging() is written to STDERR
I1108 22:41:20.277091  5071 process_result.hpp:57]  1.00007 0.19375 0.14375 0.14375 0.15625
I1108 22:41:20.277343  5071 process_result.hpp:57]  1.00007 0.53125 0.1 0.1375 0.1625


    sample : ./test_jpeg_facedetect densebox_640_360 sample_facedetect.jpg
    output :
WARNING: Logging before InitGoogleLogging() is written to STDERR
I1108 22:41:20.460816  5078 process_result.hpp:57]  0.996612 0.535937 0.075 0.117188 0.194444

```
Based on the `readme` we can test out face detection on an image using the pre-compiled densebox_320_320 model follows:
```
$ ./test_jpeg_facedetect densebox_320_320 sample_facedetect.jpg
WARNING: Logging before InitGoogleLogging() is written to STDERR
I0330 03:37:50.826640  2778 process_result.hpp:25]  1.00007 0.19375 0.14375 0.14375 0.15625
I0330 03:37:50.830754  2778 process_result.hpp:25]  1.00007 0.53125 0.1 0.1375 0.1625
```
As seen in the `readme` the 'WARNING' is expected and can be safely ignored.  The result provides the probability of a face being detected (~99%) and the coordinates of a bounding box for the detected face.  A new `sample_facedetect_result.jpg` is also created so results can be viewed back on the host.

TODO: View file from host by running Python server on board (python -m SimpleHTTPServer 8080)

Similarly, the performance test would be run as follows:
```
$ ./test_performance_facedetect densebox_320_320 test_performance_facedetect.list
WARNING: Logging before InitGoogleLogging() is written to STDERR
I0330 03:44:50.891253  2813 benchmark.hpp:163] writing report to <STDOUT>
I0330 03:44:50.891613  2813 benchmark.hpp:183] waiting for 0/30 seconds, 1 threads running
I0330 03:45:00.891741  2813 benchmark.hpp:183] waiting for 10/30 seconds, 1 threads running
I0330 03:45:10.891922  2813 benchmark.hpp:183] waiting for 20/30 seconds, 1 threads running
I0330 03:45:20.892177  2813 benchmark.hpp:192] waiting for threads terminated
FPS=386.133
E2E_MEAN=2588.76
DPU_MEAN=1346.91

```
For the performance test images are provided in the `images` folder.  These are only provided as part of the .deb package and not part of the Git source repo.  By default the performance test runs for 30 seconds and uses 1 thread.  This can be adjusted as follows (8 threads & 60s):
```
$ ./test_performance_facedetect densebox_320_320 test_performance_facedetect.list -t 8 -s 60
WARNING: Logging before InitGoogleLogging() is written to STDERR
I0330 03:45:52.939249  2820 benchmark.hpp:163] writing report to <STDOUT>
I0330 03:45:52.973646  2820 benchmark.hpp:183] waiting for 0/60 seconds, 8 threads running
.
.
.
FPS=1441.82
E2E_MEAN=5560.01
DPU_MEAN=3924.53
```
As one would expect this increases the measured FPS.

| Note: |
|---|
All face detect examples above use densebox_320_320 over densebox_640_360 because the provided sample image (sample_facedetect.jpg) is 386x450 and scaling to 320x320 causes less distortion.  In fact, if you run the same example with densebox_640_360 the detection results will not be accurate for the sample image.

### 2.2.1 Run Sample Video Application

Now lets move on to a face detect video example.  Since no sample video clip is provided with the Vitis AI installer package we'll use a clip that's openly available under the Creative Commons Attribution license: [sample_cafe.mp4](resource/sample_cafe.mp4)

| Note: |
|---|
The original video was clipped to a timeframe that included people in the foreground and increased to 60fps (from ~25fps).  This framerate was increased because the test application displays frames as fast as it processes them, which turns out to be >50fps in this case.  If the original framerate was used it would be difficult to see the results in the video because the playback is accelerated.

To run the test using the sample video clip simply download the clip from this tutorial and place it on the SD card under the `facedetect` directory.
```
./test_video_facedetect densebox_640_360 sample_cafe.mp4
```
The results are shown by displaying the original video with bounding boxes around detected faces.  Use <Ctrl+C> to exit the test at anytime.  The `densebox_640_360` model was selected because this matches the scale of the sample video clip.

Additional examples, including running the test application using video input from a USB webcam can be found in the Vitis AI Libraries documentation: Vitis-AI@GitHub / Vitis-AI-Library / README.md > [Running Vitis AI Library Examples](https://github.com/Xilinx/Vitis-AI/tree/v1.0/Vitis-AI-Library#running-vitis-ai-library-examples)

[STEP 1](step1.md) | **STEP 2** | [STEP 3](step3.md)
