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
Please make sure that installed version of `node` ≤ 16.x
{% endhint %}

Linux

```
sudo apt install node
```

MacOS

```
brew install node
```

#### Linux

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
