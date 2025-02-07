---

# MediaPipe Setup Instructions

This document provides step-by-step instructions for setting up the MediaPipe repository on macOS. Follow the sections below to prepare your environment, install dependencies, and run the Hello World example.

---

## Prerequisites

Before starting, ensure that you have the following installed:

- **Homebrew**
- **Xcode and Command Line Tools**  
  Install by running:
  ```bash
  xcode-select --install
  ```
- **Bazelisk**  
  Follow the [official Bazel documentation](https://docs.bazel.build/) to install Bazelisk.

---

## Clone the MediaPipe Repository

Clone the repository using a shallow clone:

```bash
git clone --depth 1 https://github.com/google/mediapipe.git
cd mediapipe
```

---

## Install OpenCV and FFmpeg

You have two options to install OpenCV and FFmpeg.

### Option 1: Using Homebrew

1. **Install OpenCV 3 libraries:**
   ```bash
   brew install opencv@3
   ```
2. **Uninstall glog (known issue with dependency):**
   ```bash
   brew uninstall --ignore-dependencies glog
   ```

*Note: FFmpeg will be installed via OpenCV.*

### Option 2: Using MacPorts

1. **Install OpenCV libraries:**
   ```bash
   port install opencv
   ```
2. **Edit the following configuration files:**  
   Modify `WORKSPACE`, `opencv_macos.BUILD`, and `ffmpeg_macos.BUILD` to include:

   ```python
   new_local_repository(
       name = "macos_opencv",
       build_file = "@//third_party:opencv_macos.BUILD",
       path = "/opt",
   )

   new_local_repository(
       name = "macos_ffmpeg",
       build_file = "@//third_party:ffmpeg_macos.BUILD",
       path = "/opt",
   )

   cc_library(
       name = "opencv",
       srcs = glob(
           [
               "local/lib/libopencv_core.dylib",
               "local/lib/libopencv_highgui.dylib",
               "local/lib/libopencv_imgcodecs.dylib",
               "local/lib/libopencv_imgproc.dylib",
               "local/lib/libopencv_video.dylib",
               "local/lib/libopencv_videoio.dylib",
           ],
       ),
       hdrs = glob(["local/include/opencv2/**/*.h*"]),
       includes = ["local/include/"],
       linkstatic = 1,
       visibility = ["//visibility:public"],
   )

   cc_library(
       name = "libffmpeg",
       srcs = glob(
           [
               "local/lib/libav*.dylib",
           ],
       ),
       hdrs = glob(["local/include/libav*/*.h"]),
       includes = ["local/include/"],
       linkopts = [
           "-lavcodec",
           "-lavformat",
           "-lavutil",
       ],
       linkstatic = 1,
       visibility = ["//visibility:public"],
   )
   ```

---

## Python Setup

Ensure that Python 3 and the `six` library are installed:

1. **Install Python 3:**
   ```bash
   brew install python
   ```
2. **Link Python 3.7 (if necessary):**
   ```bash
   sudo ln -s -f /usr/local/bin/python3.7 /usr/local/bin/python
   ```
3. **Verify the Python version:**
   ```bash
   python --version
   # Expected output: Python 3.7.4 (or similar)
   ```
4. **Install the Python "six" library:**
   ```bash
   pip3 install --user six
   ```

---

## Run the Hello World! Example

Follow these steps to run the C++ Hello World example:

1. **Set the GLOG environment variable:**
   ```bash
   export GLOG_logtostderr=1
   ```
2. **Run the example with Bazel (note the flag to disable GPU as desktop GPU is currently unsupported):**
   ```bash
   bazel run --define MEDIAPIPE_DISABLE_GPU=1 \
       mediapipe/examples/desktop/hello_world:hello_world
   ```

**Expected output:**

```
Hello World!
Hello World!
Hello World!
Hello World!
Hello World!
Hello World!
Hello World!
Hello World!
Hello World!
Hello World!
```

---

## Conclusion

Following these steps will set up the MediaPipe environment on your macOS system and run a simple Hello World example. For additional help or troubleshooting, refer to the official MediaPipe documentation and community resources.

---
