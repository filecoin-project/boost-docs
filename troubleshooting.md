# Troubleshooting

## Inspect

The new inspect page in the Boost UI helps with debugging retrieval problems. It allows the user to check the following using a payload CID or piece CID:

* Verify if the piece has been correctly added to the Piece Store
* Validate if the piece is indexed in the DAG store
* Check for an unsealed copy of the piece
* Verify that the payload CID -> piece CID index has been created correctly

{% embed url="https://www.youtube.com/watch?v=KLH2_LzfTtw" %}

## Failed to connect to peer

If the client cannot connect to Boost running on a Storage provider, with an error similar to the following:

```
failed to connect to peer <peer id>: failed to dial <peer id>:
  * <multi-address> failed to negotiate security protocol:
    peer id mismatch: expected <peer id>,
    but remote key matches <different peer id>
```

The problem is that:

* The SP registered their peer id and address on chain.

&#x20;      eg "Register the peer id `123abcd` at address `ip4/123.456.12.345/tcp/1234`"

```
lotus-miner actor set-peer-id 123abcd
lotus-miner actor set-addrs ip4/123.456.12.345/tcp/1234
```

* The SP changed their peer id locally but didn't update the peer id on chain.
* The client wants to make a storage deal with peer `123abcd`. The client looks on chain for the address of peer `123abcd` and sees peer `123abcd` has registered an address `ip4/123.456.12.345/tcp/1234`.
* The client sends a deal proposal for peer `123abcd` to the SP at address `ip4/123.456.12.345/tcp/1234`.
* The SP has changed their peer ID, so the SP responds to the deal proposal request with an error: `peer id mismatch`

To fix the problem, the SP should register the new peer id on chain:

```
lotus-miner actor set-peer-id <new peer id>
```

## Update storage provider's on chain address

Clients would not be able to connect to Boost running on a Storage provider after an IP change. This happens as clients lookup the registered peer id and address on chain for a SP. When a SP changes their IP or address locally, they must update the same on chain.

The SP should register the new peer id on chain using the following lotus-miner command

{% code overflow="wrap" %}
```
lotus-miner actor set-addrs /ip4/<YOUR_PUBLIC_IP_ADDRESS_OF_BOOST_NODE>/tcp/<Boostd Port>
```
{% endcode %}

{% hint style="warning" %}
Please make sure to use the public IP and port of the Boost node and not `lotus-miner` node if your miner and `boostd` runs on a separate machine.
{% endhint %}

The on chain address change requires access to the worker key and thus the command lives in `lotus-miner` instead of Boost.&#x20;

## Error in _lotus-miner info_ output

After migrating to Boost, following error is seen when running `lotus-miner info` :

{% code overflow="wrap" %}
```
ERROR: fatal error calling 'Filecoin.MarketListIncompleteDeals': panic in rpc method 'Filecoin.MarketListIncompleteDeals': runtime error: invalid memory address or nil pointer dereference
```
{% endcode %}

#### Problem:

`lotus-miner` is making a call on `lotus-market` process which has been replaced by Boost, but `lotus-miner` is not aware of the new market process.

#### Solution:

Export the MARKETS\_API\_INFO variable on your lotus-miner node.

```
export MARKETS_API_INFO=<Boost token:api>
```
