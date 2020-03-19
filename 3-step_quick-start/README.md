# 3-Step Quick Start to Vitis AI on Edge Devices
This tutorial is intended to help users get started quickly with the deployment of [Vitis AI](https://www.xilinx.com/products/design-tools/vitis/vitis-ai.html) using edge devices.  In particular, a 3-step process will be used to deploy Vitis AI examples on either a [ZCU102](https://www.xilinx.com/products/boards-and-kits/ek-u1-zcu102-g.html) or [ZCU104](https://www.xilinx.com/products/boards-and-kits/zcu104.html) Xilinx evaluation board.  Because this tutorial is focusing on deployment, the development and compilation of neural networks (from a standard framework such as Caffe or TensorFlow) with Vitis AI is not covered.  Instead the Vitis AI Model Zoo is leveraged to test pre-compiled networks.  The 3 steps of this tutorial are summarized below:

 1. Setup Vitis AI and run example applications with minimal dependencies
 2. Add Vitis AI Library and Model Zoo and run dependent sample applications
 3. Build and run more comprehensive demonstration code 

*NOTE: This tutorial was written against the Vitis 2019.2 and Vitis AI 1.0 releases*
# Step 1
This step will cover installing the Vitis AI deployment/runtime environment and testing a simple application on the evaluation board.  Vitis AI can be broadly broken down into the following components:

 1. Docker containers of development & deployment environment
    - [https://hub.docker.com/r/xilinx/vitis-ai/tags](https://hub.docker.com/r/xilinx/vitis-ai/tags)
 2. GitHub repository for sources
    - [https://github.com/Xilinx/Vitis-AI](https://github.com/Xilinx/Vitis-AI)
 4. Linux install packages (*.deb) for runtime dependencies

Here we will focus on the installation and use of items (1) and (2) above.  While subsequent steps will add the use of item (3).

## Setup Vitis AI
