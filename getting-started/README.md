---
description: >-
  This section details how to get started with Boost if you are a storage
  provider or as a client
---

# Getting started

The Boost source code repository is hosted at [github.com/filecoin-project/boost](https://github.com/filecoin-project/boost)

### Building and installing

#### Prerequisites

{% hint style="warning" %}
Please make sure that installed version of **Go** following [https://go.dev/learn/](https://go.dev/learn/)

Please make sure that installed version of **Node <= 16.x**
{% endhint %}

Linux / Ubuntu

```
curl -sL https://deb.nodesource.com/setup_16.x | sudo -E bash -
sudo apt-get install -y nodejs
```

MacOS

```
brew install node
```

#### Linux

{% hint style="warning" %}
Depending on your architecture, you will want to export additional environment variables:

```
export RUSTFLAGS="-C target-cpu=native -g"
export FFI_BUILD_FROM_SOURCE=1
```
{% endhint %}

Please make sure that `node` installed before running the `make build`.

```
git clone https://github.com/filecoin-project/boost
cd boost
make build
sudo make install
```

#### MacOS

Please make sure that `node` installed before running the `make build`.

```
export LIBRARY_PATH=$LIBRARY_PATH:/opt/homebrew/lib
git clone https://github.com/filecoin-project/boost
cd boost
make build
sudo make install
```

### Upgrading Boost

#### Linux

1\. Make sure that Boost daemon is not running. Run the below commands to upgrade the binary.

```
cd boost
git pull
make build
sudo make install
```

2\. Start the boost daemon.

#### MacOS

1\. Make sure that Boost daemon is not running. Run the below commands to upgrade the binary.

```
export LIBRARY_PATH=$LIBRARY_PATH:/opt/homebrew/lib
cd boost
git pull
make build
sudo make install
```

2\. Start the boost daemon.
