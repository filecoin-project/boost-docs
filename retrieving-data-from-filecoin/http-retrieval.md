---
description: How to configure and use HTTP retrievals in Boost
---

# HTTP Retrieval

`booster-http` is a binary that can be run alongside the `boostd` process in order to serve retrievals over HTTP. This can be used to serve full _pieces_ and also block data in the form of CAR files or raw block bytes. This can be further extended using a [bifrost-gateway](https://github.com/ipfs/bifrost-gateway) server if a Storage Provider wishes to also serve plain files and directories.

HTTP retrievals currently have no in-built payment or permissioning mechanism. Storage Providers may wish to front the `booster-http` server with a reverse proxy such as [NGINX](https://nginx.org/en/docs/) to handle operational concerns like SSL, authentication and load balancing.

## Version History

* `booster-http` was introduced with Boost v1.2.0, able to serve full pieces using the /piece/ endpoint.
* In Boost v1.7.0 `booster-http` was extended to be able to serve CAR files, IPLD blocks and a full "trusted" gateway (files and directories).
* In Boost v2.1.0 `booster-http` was simplified to only serve "trustless" data: full pieces, CAR files and IPLD blocks; deferring optional "trusted" mode to [bifrost-gateway](https://github.com/ipfs/bifrost-gateway) for those Storage Providers who wish to offer this service to their users.

## Trustless vs Trusted

"**Trustless**" refers to a mode in which the client does not need to trust a data provider in order to have confidence in the integrity and authenticity of the data they are receiving. Instead, trust is achieved by content-addressing, by having the server deliver to the client everything they require in order to verify the data they are receiving.

"**Trusted**" refers to a mode in which the client must trust the data provider to deliver the correct data. This is the standard mode that most web servers operate in, and is a common method of interacting with IPFS gateways (including `bifrost-gateway`). In this mode, the client must trust that the server is delivering the correct data, and that the server is not maliciously modifying the data in transit. Typically, the client does not receive enough information in order to verify the relationship between the content received and the content-address (CID) requested, hence the need to "trust" the server.

Trustless retrievals are always preferable where there does not exist a trust relationship between parties. By serving data only in a trustless mode, Storage Providers can avoid accusations of data alteration, and clients can be confident that they are receiving the data they requested. It also reduces the risk of having an HTTP endpoint as being flagged for serving illicit content. Learn more at [Gateways and gatekeepers](https://blog.nft.storage/posts/2022-04-29-gateways-and-gatekeepers).

However, there are many circumstances in which a Storage Provider may wish to offer a trusted retrieval service, particularly due to convenience and interoperability with standard HTTP clients.

### Piece retrievals

When performing certain actions, such as replicating deals, it can be convenient to retrieve the entire Piece (with padding) to ensure CommP integrity. When piece retrievals are enabled with `booster-http` with the `--serve-pieces` flag (enabled by default), the `/piece/` endpoint is exposed.

```sh
curl 'http://{booster-http exposed url}/piece/bagaSomePieceCID' -o bagaSomePieceCID.piece
```

### Payload retrievals (CAR and raw)

`booster-http` supports the [IPFS Trustless Gateway](https://specs.ipfs.tech/http-gateways/trustless-gateway/) specification for both CAR and raw IPLD data contained within deal payloads. When `booster-http` is run with the `--serve-cars` flag (enabled by default), the `/ipfs/` endpoint is exposed.

To download a CAR file containing all the blocks linked under a given root CID, you can either pass an `Accept: application/vnd.ipld.car` header, or a `?format=car` query parameter to the `/ipfs/` endpoint. Likewise, to download raw single IPLD block bytes, you can either pass an `Accept: application/vnd.ipld.raw` header, or a `?format=raw` query parameter to the `/ipfs/` endpoint.

The `/ipfs/` endpoint must be followed by a CID and one of the indicators above, for example:

```sh
# Fetch a complete DAG
curl 'http://{booster-http exposed url}/ipfs/bafySomePayloadCID?format=car' -o bafySomePayloadCID.car
# Fetch an individual block's bytes
curl 'http://{booster-http exposed url}/ipfs/bafySomeBlockCID?format=raw' -o bafySomeBlockCID.block
```

Alternatively, using `Accept` header:

```sh
# Fetch a complete DAG
curl -H 'Accept: application/vnd.ipld.car' 'http://{booster-http exposed url}/ipfs/bafySomePayloadCID' -o bafySomePayloadCID.car
# Fetch an individual block's bytes
curl -H 'Accept: application/vnd.ipld.car' 'http://{booster-http exposed url}/ipfs/bafySomeBlockCID' -o bafySomeBlockCID.block
```

It is also possible to use [Lassie](https://github.com/filecoin-project/lassie), a Trustless retrieval client, to perform and verify retrievals from `booster-http`:

```sh
lassie fetch --provider 'http://{booster-http exposed url}' bafySomePayloadCID
```

See the [IPFS Trustless Gateway](https://specs.ipfs.tech/http-gateways/trustless-gateway/) specification for more information on the ranges of queries possible with this protocol; most of which are implemented by Lassie as a client.

### Trusted (plain file/directory) retrievals

For Storage Providers that wish to offer trusted access to payload data, in the same way that an IPFS Gateway bridges IPLD data to standard browser-accessible files and directories, [bifrost-gateway](https://github.com/ipfs/bifrost-gateway) can be used to translate `booster-http`'s trustless data into trusted data. This is a separate process that must be configured to communicate with `booster-http`.

See [Trusted HTTP Gateway Setup](https://boost.filecoin.io/http-retrieval/advanced-http-configuration#trusted-retrieval-setup) for full setup instructions.

```sh
curl 'http://{bifrost-gateway exposed url}/ipfs/{content ID}/{optional path to resource}' -o myimage.png
```

## Local Setup

SPs should try a local setup and test their HTTP retrievals before proceeding to run `booster-http` in production.

The `booster-http` binary is built and installed with `boostd` binary. If you are planning to run `booster-http` on a different node, you can build and install the new binary. Otherwise, skip to step 3.

1. Clone the Boost repo and checkout the same release as your `boostd` version

```sh
git clone https://github.com/filecoin-project/boost.git
cd boost
git checkout <release>
```

2. Build and install the new binary

```sh
make build
sudo make build
```

3. Collect the token information for lotus-miner and lotus daemon API

```sh
export ENV_FULLNODE_API_INFO=`lotus auth api-info --perm=admin`
export FULLNODE_API_INFO=`echo $ENV_FULLNODE_API_INFO | awk '{split($0,a,"="); print a[2]}'`
```

```sh
export ENV_MINER_API_INFO=`lotus-miner auth api-info --perm=admin`
export MINER_API_INFO=`echo $ENV_MINER_API_INFO | awk '{split($0,a,"="); print a[2]}'`
```

4. Start the `booster-http` server with the above details

```sh
booster-http run --api-lid="ws://<boostd-data IP>:8044" --api-fullnode=$FULLNODE_API_INFO --api-storage=$MINER_API_INFO
```

{% hint style="info" %}
You can run multiple `booster-http` processes on the same machine by using a different port for each instance with the `--port` flag. You can also run multiple instances of the `booster-http` on different machines.
{% endhint %}

## Running Public Boost HTTP Retrieval

By default, the `booster-http` server listens to `0.0.0.0` port `7777`. This can be changed with `--address` and `--port`; for example, you may wish to change the `--address` to `127.0.0.1` to restrict it to localhost-only.

When exposing `booster-http` to a public address for general retrievals, it is recommended that you run an HTTP reverse proxy such as [NGINX](https://nginx.org/en/docs/) to handle operational concerns such as:

* SSL
* Authentication and authorization
* Compression
* Load balancing
* Logging

`booster-http` can provide some of this functionality itself, but dedicated reverse proxy software is recommended for scalable production deployments. See [HTTP Reverse Proxy Setup](https://boost.filecoin.io/http-retrieval/http-reverse-proxy-setup) for more information and example configuration.

### Making HTTP Retrieval Discoverable

To enable public discovery of the Boost HTTP server, Storage Providers should set the domain root in boostd's `config.toml`. Under the `[DealMaking]` section, set `HTTPRetrievalMultiaddr` to the public domain root in multi-address format.

Example `config.toml` section:

```
[DealMaking]
  HTTPRetrievalMultiaddr = "/dns/foo.com/tcp/443/https"
```

Clients can determine if a Storage Provider offers HTTP retrieval by running:

```sh
boost provider retrieval-transports <miner id>
```

Clients can also check the HTTP URL scheme version and supported queries

```sh
# Version
curl 'http://{bifrost-gateway exposed url}/info'

# Supported queries
curl 'http://{bifrost-gateway exposed url}/'
```
