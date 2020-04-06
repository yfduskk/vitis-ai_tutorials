[<< Return to Overview](README.md)

[STEP 1](step1.md) | [STEP 2](step2.md) | **STEP 3**

# Step 3

1. Setup Vitis AI and run example applications with minimal dependencies
2. Add Vitis AI Library and Model Zoo and run dependent sample applications
3. **Build and run more comprehensive demonstration code**
---

This final step of the tutorial will cover running the example applications under the `demo` directory that's provided by the Vitis AI install package.

These examples are a good reference point for source code because they don't rely on any pre-compiled libs.  That is, all the code is entirely re-useable for your own application.

## 3.1 Demo Examples

For each of the examples in the `demo` directory a `readme` file is provided with instructions on running the example.  Below we'll walk through this for one example and the end with some general information about each example.

### 3.1.1 Run Demo Examples

Here we'll cover the 'seg_and_pose_detect' multi-task demo that processes multiple video channels.  The videos will come from the demo video archive:
 - [vitis_ai_library_r1.0_video.tar.gz](https://www.xilinx.com/bin/public/openDownload?filename=vitis_ai_library_r1.0_video.tar.gz).  

Alternatively, you can navigate to the download link in Vitis AI documentation: Vitis-AI@GitHub / Vitis-AI-Library / README.md > Quick Start > [Setting Up The Target](https://github.com/Xilinx/Vitis-AI/tree/v1.0/Vitis-AI-Library#setting-up-the-target) > 3. Installing AI Library Package > Download the demo video files...

This archive needs to be transferred to the SD card and extracted.  Here we assume it's been extracted to the home directory (e.g. /home/user/).

```
$ cd /usr/share/vitis_ai_library/demo/seg_and_pose_detect/
$ cat readme
$ sh build.sh
$ ./seg_and_pose_detect_x  ~/vitis_ai_library_r1.0/demo/seg_and_pose_detect/seg_960_540.avi  ~/vitis_ai_library_r1.0/demo/seg_and_pose_detect/pose_960_540.avi  -t 3 -t 3 > /dev/null 2>&1
```

| Note: |
|---|
The two multi-task demo examples provide two execution modes as described in the `readme`: DRM or X.  If you are using the board standalone or doing X-forwarding to the host machine then you should use the X mode command as was used above.  If you're connected to the board from the host (i.e. UART or SSH) then you can choose either mode but DRM will require stopping the desktop interface and render results to the display directly rather than through an X (desktop) window.

This results in two windows opening.  One will display the original input and then the same input with segmentation results visible.  The other window will be similar but for the pose detection channel.  Quit the application at anytime with <Ctrl+C>.

### 3.1.2 Notes on Demo Examples

|Demo | Description | API | Input |
|---|---|---|---|
|classification|ResNet50|API0|JPG file provided with example|
|seg_and_pose_detect|multi-channel and multi-network|API1 & multi-task|AVI videos provided in demo video archive (see previous section for link)|
|segs_and_roadline_detect|multi-channel and multi-network|API1 & multi-task|AVI videos provided in demo video archive (see previous section for link)|
|yolov3|ADAS example (classifies cars, bicycles, pedestrians)|API2|JPG file provided with example|

*BONUS NOTE: In the process of writing this I came across a neat way to view image results from the host machine: `python -m SimpleHTTPServer 8080`.  This will start a Python based http server on the board so you can view files using a host web browser.*

[STEP 1](step1.md) | [STEP 2](step2.md) | **STEP 3**
