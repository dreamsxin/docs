# Building MindSpore Lite

<!-- TOC -->

- [Building MindSpore Lite](#building-mindspore-lite)
    - [Linux Environment Compilation](#linux-environment-compilation)
        - [Environment Requirements](#environment-requirements)
        - [Compilation Options](#compilation-options)
        - [Compilation Example](#compilation-example)
        - [Output Description](#output-description)
            - [Description of Converter's Directory Structure](#description-of-converters-directory-structure)
            - [Description of Runtime and Other tools' Directory Structure](#description-of-runtime-and-other-tools-directory-structure)
            - [Description of Imageprocess's Directory Structure](#description-of-imageprocesss-directory-structure)

<!-- /TOC -->

<a href="https://gitee.com/mindspore/docs/blob/master/tutorials/lite/source_en/use/build.md" target="_blank"><img src="../_static/logo_source.png"></a>

This chapter introduces how to quickly compile MindSpore Lite, which includes the following modules:

| Module | Support Platform | Description |
| --- | ---- | ---- |
| converter | Linux | Model Conversion Tool |
| runtime(cpp, java) | Linux, Android | Model Inference Framework(cpp, java) |
| benchmark | Linux, Android | Benchmarking Tool |
| imageprocess | Linux, Android | Image Processing Library |

## Linux Environment Compilation

### Environment Requirements

- The compilation environment supports Linux x86_64 only. Ubuntu 18.04.02 LTS is recommended.

- Compilation dependencies of runtime(cpp), benchmark:
    - [CMake](https://cmake.org/download/) >= 3.14.1
    - [GCC](https://gcc.gnu.org/releases.html) >= 7.3.0
    - [Android_NDK r20b](https://dl.google.com/android/repository/android-ndk-r20b-linux-x86_64.zip)
    - [Git](https://git-scm.com/downloads) >= 2.28.0

- Compilation dependencies of converter:
    - [CMake](https://cmake.org/download/) >= 3.14.1
    - [GCC](https://gcc.gnu.org/releases.html) >= 7.3.0
    - [Android_NDK r20b](https://dl.google.com/android/repository/android-ndk-r20b-linux-x86_64.zip)
    - [Git](https://git-scm.com/downloads) >= 2.28.0
    - [Autoconf](http://ftp.gnu.org/gnu/autoconf/) >= 2.69
    - [Libtool](https://www.gnu.org/software/libtool/) >= 2.4.6
    - [LibreSSL](http://www.libressl.org/) >= 3.1.3
    - [Automake](https://www.gnu.org/software/automake/) >= 1.11.6
    - [Libevent](https://libevent.org) >= 2.0
    - [M4](https://www.gnu.org/software/m4/m4.html) >= 1.4.18
    - [OpenSSL](https://www.openssl.org/) >= 1.1.1
    - [Python](https://www.python.org/) >= 3.7.5

- Compilation dependencies of runtime(java)
    - [CMake](https://cmake.org/download/) >= 3.14.1
    - [GCC](https://gcc.gnu.org/releases.html) >= 7.3.0
    - [Android_NDK](https://dl.google.com/android/repository/android-ndk-r20b-linux-x86_64.zip) >= r20
    - [Git](https://git-scm.com/downloads) >= 2.28.0
    - [Android_SDK](https://developer.android.com/studio/releases/platform-tools?hl=zh-cn#downloads) >= 30
    - [Gradle](https://gradle.org/releases/) >= 6.6.1
    - [JDK](https://www.oracle.com/cn/java/technologies/javase/javase-jdk8-downloads.html) >= 1.8

> - To install and use `Android_NDK`, you need to configure environment variables. The command example is `export ANDROID_NDK={$NDK_PATH}/android-ndk-r20b`.
> - Android SDK Tools need install Android SDK Build Tools.
> - In the `build.sh` script, run the `git clone` command to obtain the code in the third-party dependency library. Ensure that the network settings of Git are correct.

### Compilation Options

MindSpore Lite provides a compilation script `build.sh` for one-click compilation, located in the root directory of MindSpore. This script can be used to compile the code of training and inference. The following describes the compilation options of MindSpore Lite.

| Parameter  |  Parameter Description  | Value Range | Mandatory or Not |
| -------- | ----- | ---- | ---- |
| -I | Selects an applicable architecture. This option is required when compile MindSpore Lite. | arm64, arm32, or x86_64 | No |
| -d | If this parameter is set, the debug version is compiled. Otherwise, the release version is compiled. | None | No |
| -i | If this parameter is set, incremental compilation is performed. Otherwise, full compilation is performed. | None | No |
| -j[n] | Sets the number of threads used during compilation. Otherwise, the number of threads is set to 8 by default. | Integer | No |
| -e | In the Arm architecture, select the backend operator and set the `gpu` parameter. The built-in GPU operator of the framework is compiled at the same time. | GPU | No |
| -h | Displays the compilation help information. | None | No |
| -n | Specifies to compile the lightweight image processing module. | lite_cv | No |
| -A | Language used by mindspore lite, default cpp. If the parameter is set java，the AAR is compiled. | cpp, java | No |
| -C | If this parameter is set, the converter is compiled, default on. | on, off | No |
| -o | If this parameter is set, the benchmark is compiled, default on. | on, off | No |
| -t | If this parameter is set, the testcase is compiled, default off. | on, off | No |

> When the `-I` parameter changes, such as `-I x86_64` is converted to `-I arm64`, adding `-i` for parameter compilation does not take effect.

### Compilation Example

First, download source code from the MindSpore code repository.

```bash
git clone https://gitee.com/mindspore/mindspore.git
```

Then, run the following commands in the root directory of the source code to compile MindSpore Lite of different versions:

- Debug version of the x86_64 architecture:

    ```bash
    bash build.sh -I x86_64 -d
    ```

- Release version of the x86_64 architecture, with the number of threads set:

    ```bash
    bash build.sh -I x86_64 -j32
    ```

- Release version of the x86_64 architecture, with the testcase compiled:

    ```bash
    bash build.sh -I x86_64 -t on
    ```

- Release version of the Arm 64-bit architecture in incremental compilation mode, with the number of threads set:

    ```bash
    bash build.sh -I arm64 -i -j32
    ```

- Release version of the Arm 64-bit architecture in incremental compilation mode, with the built-in GPU operator compiled:

    ```bash
    bash build.sh -I arm64 -e gpu
    ```

- Compile ARM64 with image preprocessing module:

    ```bash
    bash build.sh -I arm64 -n lite_cv
    ```

- Compile MindSpore Lite AAR in incremental compilation mode:

  ```bash
  bash build.sh -A java -i
  ```
  
  > Turn on incremental compilation mode. If the arm64 or arm32 runtime already exists in the `mindspore/output/` directory, the corresponding version of the runtime will not be recompiled.

- Release version of the x86_64 architecture, with the converter compiled:

    ```bash
    bash build.sh -I x86_64 -C on
    ```

- Release version of the x86_64 architecture, with the benchmark compiled:

    ```bash
    bash build.sh -I x86_64 -o on
    ```


### Output Description

After the compilation is complete, go to the `mindspore/output` directory of the source code to view the file generated after compilation. The file is divided into three parts.

- `mindspore-lite-{version}-converter-{os}.tar.gz`: Contains model conversion tool.
- `mindspore-lite-{version}-runtime-{os}-{device}.tar.gz`: Contains model inference framework, benchmarking tool and performance analysis tool.
- `mindspore-lite-{version}-minddata-{os}-{device}.tar.gz`: Contains image processing library ImageProcess.

> version: version of the output, consistent with that of the MindSpore.
>
> device: Currently divided into cpu (built-in CPU operator) and gpu (built-in CPU and GPU operator).
>
> os: Operating system on which the output will be deployed.

Execute the decompression command to obtain the compiled output:

```bash
tar -xvf mindspore-lite-{version}-converter-{os}.tar.gz
tar -xvf mindspore-lite-{version}-runtime-{os}-{device}.tar.gz
tar -xvf mindspore-lite-{version}-minddata-{os}-{device}.tar.gz
unzip mindspore-lite-maven-{version}.zip
```

#### Description of Converter's Directory Structure

The conversion tool is only available under the `-I x86_64` compilation option, and the content includes the following parts:

```text
|
├── mindspore-lite-{version}-converter-{os}
│   └── converter # Model conversion Ttool
│   └── lib # The dynamic link library that converter depends
│   └── third_party # Header files and libraries of third party libraries
│       ├── glog # Dynamic library of Glog
```

#### Description of Runtime and Other tools' Directory Structure

The inference framework can be obtained under `-I x86_64`, `-I arm64` and `-I arm32` compilation options, and the content includes the following parts:

- When the compilation option is `-I x86_64`:

    ```text
    |
    ├── mindspore-lite-{version}-runtime-x86-cpu
    │   └── benchmark # Benchmarking Tool
    │   └── lib # Inference framework dynamic library
    │       ├── libmindspore-lite.so # Dynamic library of infernece framework in MindSpore Lite
    │   └── third_party # Header files and libraries of third party libraries
    │       ├── flatbuffers # Header files of FlatBuffers
    │   └── include # Header files of inference framework
    ```

- When the compilation option is `-I arm64`:  

    ```text
    |
    ├── mindspore-lite-{version}-runtime-arm64-cpu
    │   └── benchmark # Benchmarking Tool
    │   └── lib # Inference framework dynamic library
    │       ├── libmindspore-lite.so # Dynamic library of infernece framework in MindSpore Lite
    │       ├── libmindspore-lite-fp16.so # Operator performance optimization library support float16 in MindSpore Lite
    │       ├── libmindspore-lite-optimize.so # Operator performance optimization library support dotprod instruction in MindSpore Lite  
    │   └── third_party # Header files and libraries of third party libraries
    │       ├── flatbuffers # Header files of FlatBuffers
    │   └── include # Header files of inference framework
    ```

- When the compilation option is `-I arm32`:  

    ```text
    |
    ├── mindspore-lite-{version}-runtime-arm32-cpu
    │   └── benchmark # Benchmarking Tool
    │   └── lib # Inference framework dynamic library
    │       ├── libmindspore-lite.so # Dynamic library of infernece framework in MindSpore Lite
    │   └── third_party # Header files and libraries of third party libraries
    │       ├── flatbuffers # Header files of FlatBuffers
    │   └── include # Header files of inference framework
    ```

> 1. `libmindspore-lite-optimize.so` only exists in the output package of runtime-arm64 and is only used on ARMv8.2 and CPUs that support dotprod instruction.
> 2. `libmindspore-lite-fp16.so` only exists in the output package of runtime-arm64 and is only used on ARMv8.2 and CPUs that support fp16.
> 3. Compile ARM64 to get the inference framework output of arm64-cpu by default, if you add `-e gpu`, you will get the inference framework output of arm64-gpu, and the package name is `mindspore-lite-{version}-runtime-arm64-gpu.tar.gz`, compiling ARM32 is in the same way.
> 4. Before running the tools in the converter, benchmark directory, you need to configure environment variables, and configure the path where the dynamic libraries of MindSpore Lite and Protobuf are located to the path where the system searches for dynamic libraries.

Configure converter:

```bash
export LD_LIBRARY_PATH=./output/mindspore-lite-{version}-converter-ubuntu/lib:./output/mindspore-lite-{version}-converter-ubuntu/third_party/glog/lib:${LD_LIBRARY_PATH}
```

Configure benchmark:

```bash
export LD_LIBRARY_PATH= ./output/mindspore-lite-{version}-runtime-x86-cpu/lib:${LD_LIBRARY_PATH}
```

- When the compilation option is `-A java`:

  ```text
  |
  ├── mindspore-lite-maven-{version}
  │   └── mindspore
  │       └── mindspore-lite
  |           └── {version}
  │               ├── mindspore-lite-{version}.aar # MindSpore Lite runtime aar
  ```

#### Description of Imageprocess's Directory Structure

The image processing library is only available under the `-I arm64 -n lite_cv` compilation option, and the content includes the following parts:

```text
|
├── mindspore-lite-{version}-minddata-{os}-{device}
│   └── benchmark # Benchmarking Tool
│   └── include # Head file(Only show files related to image processing)
│       ├── lite_cv # Image processing library header file
│           ├── image_process.h # Image processing function header file
│           ├── lite_mat.h # Image data class structure header file
│   └── lib # Dynamic library(Only show files related to image processing)
│       ├── libminddata-lite.so # Image processing dynamic library
│   └── third_party # Third-party Iibrary header files and libraries
│       ├── flatbuffers # Header files of FlatBuffers
```
