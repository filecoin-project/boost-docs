# Boost Client

Boost comes with a client `boost` executable that can be used to send a deal proposal to a Boost server.

The client is intentionally minimal. It does not require a daemon process, and can be pointed at any public Filecoin API for on-chain operations. This means that users of the client do not need to run a Filecoin node that syncs the chain.

#### 1. Set the API endpoint environment variable

```
export FULLNODE_API_INFO=<filecoin API endpoint>
```

#### 2. Initialize the client

```
boost -vv init
```

The init command

* Creates a Boost client repo (at `~/.boost-client` by default)
* Generates a libp2p peer ID key
* Generates a wallet for on-chain operations and outputs the wallet address

#### 3. Add funds to the wallet

To make deals you will need to add funds to the wallet.

#### 4. Make a storage deal

```
boost -vv deal --http-url=http://myserver/my.car <other params>...
```
