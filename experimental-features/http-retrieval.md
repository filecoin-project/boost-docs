# HTTP Retrieval

Boost is introducing a new binary, `booster-http`, with release v1.2.0-rc2. This binary can be run alongside the `boostd` market process in order to serve retrievals over http.

Currently, there is no payment method or built-in security integrated in the new binary. It can be run with any stable release of `boostd` and can also be run on a separate machine from the `boostd` process.

### Trying It Out

To build and run `booster-http` :\
\
1\. Clone the main branch from the boost repo

```
git clone https://github.com/filecoin-project/boost.git
```

2\. Build the new binary

```
make booster-http
```

3\. Collect the token information for boost, lotus-miner and lotus daemon API

```
boostd auth api-info --perm=admin
```

```
lotus auth api-info --perm=admin
```

```
lotus-miner auth api-info --perm=admin
```

4\. Start the `booster-http` server with the above details

```
booster-http run --api-boost=$BOOST_API_INFO --api-fullnode=$FULLNODE_API_INFO --api-sealer=$MINER_API_INFO
```

5\. To download files from the new http-server locally, you can run the following commands.

```
# Download piece by piece CID
curl http://localhost:7777/piece/<piece cid> > /tmp/download.piece
# Download CAR by piece CID
curl http://localhost:7777/piece/<piece cid>.car > /tmp/download.car
# Download piece by payload CID (piece must be indexed)
curl http://localhost:7777/payload/<payload cid> > /tmp/download.payload
# Download CAR by payload CID
curl http://localhost:7777/payload/<payload cid>.car > /tmp/download.car
```

## Running Public Boost HTTP Retrieval

The Boost HTTP instance runs by default simply listening on localhost. If you want to make retrievals public, you'll want to run a reverse proxy such as NGINX to handle operational concerns like:

* SSL
* Authentication if you want it
* Load balancing if you want to run multiple instances of booster-http

While booster-http may get more operational features over time, the intent is that providers who want to scale their HTTP operations will handle most of operational concerns via software in front of booster-http rather than booster-http itself

### Making HTTP Retrieval Discoverable

If you are running Boost HTTP Retrieval publicly, you will want to set your domain root by editing Boost's `config.toml` and under the `[DealMaking]` section, set `HTTPRetrievalMultiaddr` to the public domain root you will serve HTTP retrievals.

Example `config.toml` section:

```
[DealMaking]
  HTTPRetrievalMultiaddr = "/dns/foo.com/tcp/443/https"
```

If `HTTPRetrievalMultiaddr` is set, anyone running boost client can determine if you offer a given piece over HTTP by running:

```
boost provider retrieval-transports <miner id>
```

If you have the piece and the storage provider have set `HTTPRetrievalMultiaddr`, you can download the piece or payload using the below URL:

```
# Download piece by piece CID
curl http://<HTTP URL>/piece/<piece cid> > /tmp/download.piece

# Download piece by payload CID (piece must be indexed)
curl http://<HTTP URL>/payload/<payload cid> > /tmp/download.payload
```

Which they can then `curl` to get the piece from you.
