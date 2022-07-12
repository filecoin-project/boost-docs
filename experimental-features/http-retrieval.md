# HTTP Retrieval

Boost is introducing a new binary, `boosted-http`, with release v1.2.0-rc2. This binary can be run alongside the `boostd` market process in order to serve retrievals over http.&#x20;

Currently, there is no payment method or security integrated in the new binary. It can be run with any stable release of `boostd` and can also be run on a separate machine from the `boostd` process.

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

4\. To download files from the new http-server locally, you can run the following commands.

```
# Download piece by piece CID
curl http://localhost:7777/piece/<piece cid> > /tmp/download.piece
# Download CAR by piece CID
curl http://localhost:7777/piece/<piece cid>.car > /tmp/download.car
# Download piece by payload CID (piece must be indexed)
curl http://localhost:7777/payload/<payload cid> > /tmp/download.piece
# Download CAR by payload CID
curl http://localhost:7777/payload/<payload cid>.car > /tmp/download.car
```
