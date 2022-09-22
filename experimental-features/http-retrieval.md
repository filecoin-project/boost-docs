---
description: How to configure and use HTTP retrievals in Boost
---

# HTTP Retrieval

Boost is introducing a new binary, `booster-http`, with release v1.2.0. This binary can be run alongside the `boostd` market process in order to serve retrievals over http.

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
booster-http run --api-boost=$BOOST_API_INFO --api-fullnode=$FULLNODE_API_INFO --api-storage=$MINER_API_INFO
```

5\. To download files from the new http-server locally, you use the following endpoints:

```
# Download a piece by its PieceCID                                                                                                                                                                                                                                                                                                                          
# Randomly selects the first deal/sector matching the CID                                                                                                                                                                                                                                                                                                   
# This will transfer the entire piece including all its (compressible) padding                                                                                                                                                                                                                                                           
curl http://localhost:7777/piece/bagaSomePieceCID -o $HOME/download.piece                                                                                                                                                                                                                                                                                   
                                                                                                                                                                                                                                                                                                                                                            
# Like above but download only the CAR portion contained within the selected piece
 # Relies on payload size information inferred during deal indexing                                                                                                                                                                                                       
# If you require bit-for-bit piece identity, the method above is preferred                                                                                                                                                                                                                                                                                  
curl http://localhost:7777/piece/bagaSomePieceCID.car -o $HOME/download.car                                                                                                                                                                                                                                                                                 
                                                                                                                                                                                                                                                                                                                                                            
# Download a random piece containing a CAR containing the specified payload CID                                                                                                                                                                                                                                                                
# A CAR file would match if it contains the requested CID at any (non-root) position                                                                                                                                                                                                                                       
# Relies on payload contents information inferred during deal indexing                                                                                                                                                                                                                                                                                      
curl http://localhost:7777/payload/bafySomePayloadCid -o $HOME/download.piece                                                                                                                                                                                                                                                                               
                                                                                                                                                                                                                                                                                                                                                            
# Like above but download only the CAR portion contained within the selected piece                                                                                                                                                                                                                                                                          
curl http://localhost:7777/payload/bafySomePayloadCid.car -o $HOME/download.car         
```

## Running Public Boost HTTP Retrieval

The booster-http server listens on localhost. To expose the server publically, SPs should run a reverse proxy such as NGINX to handle operational concerns like:

* SSL
* Authentication
* Load balancing

While booster-http may get more operational features over time, the intent is that providers who want to scale their HTTP operations will handle most of operational concerns via software in front of booster-http.

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

Clients can download a piece using the domain root configured by the SP:

```
# Download piece by its CID
curl https://foo.com/piece/bagaSomePieceCID -o $HOME/download.piece

# Download random piece containing specified payload CID (piece must be indexed)
curl https://foo.com/payload/bafySomePayloadCid -o $HOME/download.piece
```
