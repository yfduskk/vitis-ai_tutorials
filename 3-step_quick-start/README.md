# 3-Step Quick Start to Vitis AI on Edge Devices

This tutorial is intended to help users get started quickly with the deployment of [Vitis AI](https://www.xilinx.com/products/design-tools/vitis/vitis-ai.html) using edge devices.  In particular, a 3-step process will be used to deploy Vitis AI examples on either a [ZCU102](https://www.xilinx.com/products/boards-and-kits/ek-u1-zcu102-g.html) or [ZCU104](https://www.xilinx.com/products/boards-and-kits/zcu104.html) Xilinx evaluation board.  Because this tutorial is focusing on deployment, the development and compilation of neural networks (from a standard framework such as Caffe or TensorFlow) with Vitis AI is not covered.  Instead the Vitis AI Model Zoo is leveraged to test pre-compiled networks.  The 3 steps of this tutorial are summarized below.

1. [Setup Vitis AI and run example applications with minimal dependencies](step1.md)
2. [Add Vitis AI Library and Model Zoo and run dependent sample applications](step2.md)
3. [Build and run more comprehensive demonstration code](step3.md)

Prerequisites for this tutorial are as follows:
 - Linux host machine (or VM) with Docker installed
   - Docker installation instructions available here: [docs.docker.com/install](https://docs.docker.com/install/)
 - ZCU102 or ZCU104 evaluation board
 - 8GB (or greater) SD card
 - Micro-USB cable

One of the following is highly recommended (but not mandatory):
 - Host machine (Windows or Linux) with X11 server installed and Ethernet cable.  For example, Xming for Windows.
   - **OR**
 - DisplayPort monitor and cable

Additionally, to use the board and run the examples standalone (i.e. without host machine interaction) the following would be needed *in addition* to the DisplayPort monitor and cable:
 - USB hub, keyboard, and mouse
 - ZCU102 only: USB 3.0 A Female to Micro B Male OTG Cable/Dongle

| IMPORTANT NOTE: |
|---|
**This tutorial was written against the Vitis 2019.2 and Vitis AI 1.0 releases**

Continue to the tutorial using the links below.

[STEP 1](step1.md) | [STEP 2](step2.md) | [STEP 3](step3.md)
