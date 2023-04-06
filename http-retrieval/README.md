---
description: How to configure and use HTTP retrievals in Boost
---

# HTTP Retrieval

Boost introduced a new binary, `booster-http`, with release v1.2.0. This binary can be run alongside the `boostd` market process in order to serve retrievals over http.

Currently, there is no payment method or built-in security integrated in the new binary. It can be run with any stable release of `boostd` and can also be run on a separate machine from the `boostd` process.

With release v1.7.0-rc1, `booster-http` now supports an [IPFS gateway](https://docs.ipfs.tech/concepts/ipfs-gateway/#overview) like retrievals from Filecoin SP. Clients can query the SP's HTTP retrieval URL in below formats.

1. To retrieve a full piece

```
curl http://{SP's http retrieval URL}/piece/bagaSomePieceCID -o $HOME/download.piece 
```

2. To retrieve files and blocks&#x20;

```
http://{SP's http retrieval URL}/ipfs/{content ID}/{optional path to resource}
```

### Local Setup

SPs should try a local setup and test their HTTP retrievals before proceeding to run `booster-http` in production.

To build and run `booster-http` :\
\
1\. Clone the main branch from the boost repo

```
git clone https://github.com/filecoin-project/boost.git
cd boost
git checkout <release>
```

2\. Build the new binary

```
make booster-http
```

3\. Collect the token information for boost, lotus-miner and lotus daemon API

```
export ENV_BOOST_API_INFO=`boostd auth api-info --perm=admin`
export BOOST_API_INFO=echo $ENV_BOOST_API_INFO | awk '{split($0,a,"="); print a[2]}'
```

```
export ENV_FULLNODE_API_INFO=`lotus auth api-info --perm=admin`
export FULLNODE_API_INFO=echo $ENV_BOOST_API_INFO | awk '{split($0,a,"="); print a[2]}'
```

```
export ENV_MINER_API_INFO=`lotus-miner auth api-info --perm=admin`
export MINER_API_INFO=echo $ENV_BOOST_API_INFO | awk '{split($0,a,"="); print a[2]}'
```

4\. Start the `booster-http` server with the above details

```
booster-http run --api-boost=$BOOST_API_INFO --api-fullnode=$FULLNODE_API_INFO --api-storage=$MINER_API_INFO
```

{% hint style="info" %}
You can also run multiple processes on the same machine but you would need to use different port for each `booster-http` instance by specifying the value using the `--port` flag. You can run multiple instances of the `booster-http` spread over multiple machines.&#x20;
{% endhint %}

5\. To download files from the new http-server locally, you use the following endpoints:

```
# car file
curl -s -H "Accept:application/vnd.ipld.car;" -o $HOME/download.car http://localhost:7777/ipfs/bafykbzacecyrmvxl7shftg5qpebpkrdtdzihjnoymc7voyiyomvaydomddxcw
# raw, root block
curl -s -H "Accept:application/vnd.ipld.raw;" -o $HOME/download.raw http://localhost:7777/ipfs/bafykbzacecyrmvxl7shftg5qpebpkrdtdzihjnoymc7voyiyomvaydomddxcw
# raw content
curl -s -o $HOME/download.raw http://localhost:7777/ipfs/bafykbzacecyrmvxl7shftg5qpebpkrdtdzihjnoymc7voyiyomvaydomddxcw
# whole piece
curl -o $HOME/download.piece http://localhost:7777/piece/baga6ea4seaqexju64hfftlf5xsvyjahfv2ly3czi2lhrmhleg7xshyfmpcfj2ly
```

## Running Public Boost HTTP Retrieval

The booster-http server listens on localhost. To expose the server publically, SPs should run a reverse proxy such as [NGINX](https://nginx.org/en/docs/) to handle operational concerns like:

* SSL
* Authentication
* Load balancing

While booster-http may get more operational features over time, the intent is that providers who want to scale their HTTP operations will handle most of operational concerns via software in front of booster-http. You can setup a simple NGINX proxy using the [example provided](serving-files-with-booster-http.md#protecting-booster-http-with-nginx) in [serving-files-with-booster-http.md](serving-files-with-booster-http.md "mention")

### Making HTTP Retrieval Discoverable

To enable public discovery of the Boost HTTP server, SPs should set the domain root in boostd's `config.toml`: Under the `[DealMaking]` section, set `HTTPRetrievalMultiaddr` to the public domain root in multi-address format.

Example `config.toml` section:

```
[DealMaking]
  HTTPRetrievalMultiaddr = "/dns/foo.com/tcp/443/https"
```

Clients can determine if an SP offers HTTP retrieval by running:

```
boost provider retrieval-transports <miner id>
```

Clients can check the HTTP URL scheme version and supported queries

```
// Supported queries
curl https://foo.com/index

// Version
curl https://foo.com/info
```

Clients can download a piece using the domain root configured by the SP:

```
# Download a piece by its CID
curl https://foo.com/piece/bagaSomePieceCID -o $HOME/download.piece
```

