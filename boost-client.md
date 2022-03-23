# Boost Client

Boost comes with a client `boost` executable that can be used to send a deal proposal to a Boost server.

The client is intentionally minimal. It does not require a daemon process, and can be pointed at any public Filecoin API for on-chain operations. This means that users of the client do not need to run a Filecoin node that syncs the chain.

1. Set the API endpoint environment variable

```
export FULLNODE_API_INFO=<filecoin API endpoint>
```

1. Initialize the client

```
boost -vv init
```

1. Make a storage deal

```
boost -vv deal --http-url=http://myserver/my.car <other params>...
```
