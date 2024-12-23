---
description: >-
  This section details how to compile and install Boost if you are a storage
  provider or a client
---

# Installation

The Boost source code repository is hosted at [github.com/filecoin-project/boost](https://github.com/filecoin-project/boost)

### Boost compatibility matrix

| Boost Version          | Lotus Version                                                                                                                                                              | Golang Version                                                                                                                                                             | Curio Version  |
| ---------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | -------------- |
| < v2.0.0               | [https://boost.filecoin.io/v/v1/getting-started#boost-and-lotus-compatibility-matrix](https://boost.filecoin.io/v/v1/getting-started#boost-and-lotus-compatibility-matrix) | [https://boost.filecoin.io/v/v1/getting-started#boost-and-lotus-compatibility-matrix](https://boost.filecoin.io/v/v1/getting-started#boost-and-lotus-compatibility-matrix) | NA             |
| v2.0.0                 | v1.23.x                                                                                                                                                                    | 1.20.x                                                                                                                                                                     | NA             |
| v2.1.0, v2.1.1, v2.1.2 | v1.24.x, v1.25.x                                                                                                                                                           | 1.20.x                                                                                                                                                                     | NA             |
| v2.2.0                 | v1.26.0, v1.26.1                                                                                                                                                           | 1.21.7                                                                                                                                                                     | NA             |
| v2.3.0                 | v1.27.x, v1.28.x                                                                                                                                                           | 1.22.3                                                                                                                                                                     | 1.22.x, 1.23.x |
| v2.4.0                 | v1.30.0                                                                                                                                                                    | 1.22.3                                                                                                                                                                     | 1.24.2         |
| v2.4.1-rc1             | v1.32.0-rc1                                                                                                                                                                | 1.22.8                                                                                                                                                                     | TBD            |

### Building and Installing

#### Prerequisites

{% hint style="warning" %}
Please make sure you have installed:\
\
**Go** - following [https://go.dev/learn/](https://go.dev/learn/)

**Rust** - following [https://www.rust-lang.org/tools/install](https://www.rust-lang.org/tools/install)

**Node 20.x**
{% endhint %}

**Linux / Ubuntu**

```
curl -sL https://deb.nodesource.com/setup_20.x | sudo -E bash - 
sudo apt-get install -y nodejs
sudo apt install mesa-opencl-icd ocl-icd-opencl-dev gcc git bzr jq pkg-config curl clang build-essential hwloc libhwloc-dev wget -y
```

**macOS**

```
brew install node@20
brew install jq pkg-config hwloc coreutils
```

#### Linux

{% hint style="warning" %}
Depending on your architecture, you will want to export additional environment variables:

```
export RUSTFLAGS="-C target-cpu=native -g"
export FFI_BUILD_FROM_SOURCE=1
```
{% endhint %}

```
git clone https://github.com/filecoin-project/boost
cd boost
git checkout <stable tag or branch>
make clean build
sudo make install
```

Please ignore any output or onscreen instruction during the `npm build` unless there is an error.

#### macOS

```
export LIBRARY_PATH=$LIBRARY_PATH:/opt/homebrew/lib
git clone https://github.com/filecoin-project/boost
cd boost
git checkout <stable tag or branch>
make clean build
sudo make install
```

Please ignore any output or onscreen instruction during the `npm build` unless there is an error.

#### **Calibration Network**

To build boost for _calibnet_, please complete the above pre-requisites and build using the following commands.

```
git clone https://github.com/filecoin-project/boost
cd boost
git checkout <stable tag or branch>
make clean calibnet
```
