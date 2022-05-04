---
description: >-
  This section details how to get started with Boost if you are a storage
  provider or as a client
---

# Getting started

The Boost source code repository is hosted at [github.com/filecoin-project/boost](https://github.com/filecoin-project/boost)

### Building and installing

#### Prerequisites

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
export export LIBRARY_PATH=$LIBRARY_PATH:/opt/homebrew/lib
git clone https://github.com/filecoin-project/boost
cd boost
make build
sudo make install
```
