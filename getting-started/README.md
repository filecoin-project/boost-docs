---
description: >-
  This section details how to get started with Boost if you are a storage
  provider or as a client
---

# Getting started

The Boost source code repository is hosted at [github.com/filecoin-project/boost](https://github.com/filecoin-project/boost)

### Boost and Lotus compatibility Matrix

| Boost Version          | Lotus Version    |
| ---------------------- | ---------------- |
| v1.5.0                 | v1.18.0          |
| v1.5.1, v1.5.2, v1.5.3 | v1.18.0, v1.19.0 |
| v1.6.0, v1.6.1         | v1.20.x          |

### Building and installing

#### Prerequisites

{% hint style="warning" %}
Please make sure you have installed:\
\
**Go** - following [https://go.dev/learn/](https://go.dev/learn/)

**Rust** - following [https://www.rust-lang.org/tools/install](https://www.rust-lang.org/tools/install)

**Node 16.x**
{% endhint %}

**Linux / Ubuntu**

```
curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs
sudo apt install mesa-opencl-icd ocl-icd-opencl-dev gcc git bzr jq pkg-config curl clang build-essential hwloc libhwloc-dev wget -y
```

**MacOS**

```
brew install node@16
brew install bzr jq pkg-config hwloc coreutils
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
git checkout <Stable tag or branch>
make build
sudo make install
```

Please ignore any output or onscreen instruction during the `npm build` unless there is an error.

#### MacOS

```
export LIBRARY_PATH=$LIBRARY_PATH:/opt/homebrew/lib
git clone https://github.com/filecoin-project/boost
cd boost
git checkout <Stable tag or branch>
make build
sudo make install
```

Please ignore any output or onscreen instruction during the `npm build` unless there is an error.

#### **Calibration Network**

To build boost for calibnet, please complete the above pre-requisites and build using the following commands.

```
git clone https://github.com/filecoin-project/boost
cd boost
git checkout <Stable tag or branch>
make calibnet
```

### Upgrading Boost

#### Linux

1\. Make sure that Boost daemon is not running. Run the below commands to upgrade the binary.

```
cd boost
git checkout main
git pull
git checkout <New release>
make build
sudo make install
```

2\. Please ignore any onscreen instruction during the `npm build` unless there is an error.

3\. Start the boost daemon.

#### MacOS

1\. Make sure that Boost daemon is not running. Run the below commands to upgrade the binary.

```
export LIBRARY_PATH=$LIBRARY_PATH:/opt/homebrew/lib
cd boost
git checkout main
git pull
git checkout <New release>
make build
sudo make install
```

2\. Please ignore any onscreen instruction during the `npm build` unless there is an error.

3\. Start the boost daemon.
