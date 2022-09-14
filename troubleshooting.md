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

Export the `LOTUS_MARKET_PATH` variable on your lotus-miner node.

```
export LOTUS_MARKETS_PATH=<BOOST REPO PATH>
```

## Fix retrievals with error "failed to lookup index for mh"

The following error shows up when trying to retrieve the data from a storage provider.

{% code overflow="wrap" %}
```
ERROR: offer error: retrieval query offer errored: failed to fetch piece to retrieve from: getting pieces for cid Qmf1ykhUo63qB5dJ8KRyeths9MZfyxpVdT5xwnmoLKefz7: getting pieces containing block Qmf1ykhUo63qB5dJ8KRyeths92mfyxpVdT5xi1moLKefz7: failed to lookup index for mh 1220f7ce2d20772b959c1071868e9495712f12785b1710ee88752af120dd49338190, err: datastore: key not found
```
{% endcode %}

The error indicates that dagstore does not have a corresponding index shard for the piece containing the requested data. When a retrieval is requested, the dagstore on storage provider side is queried and a reverse look up is used to determine the key(piece CID). This key is then used to query the piece store to find the sector containing the data and byte offset.

If for any reason the shard is not registered with the dagstore then reverse look up to find the piece CID fails and the above error is seen. The most widely know reason for not having the shard registered with dagstore is the below error.

{% code overflow="wrap" %}
```
2022-02-21T20:06:03.950+1100 INFO markets loggers/loggers.go:20 storage provider event {"name": "ProviderEventFailed", "proposal CID": "bafyreihr743zllr2eckgfiweouiap7pgcjqa3mg3t75jjt7sfcpu", "state": "StorageDealError", "message": "error awaiting deal pre-commit: failed to set up called handler: called check error (h: 1570875): failed to look up deal on chain: deal 3964985 not found - deal may not have completed sealing before deal proposal start epoch, or deal may have been slashed"}
```
{% endcode %}

To fix the deals where retrievals are impacted by above error, user will need to register the shards manually with dagstore:

```
boostd dagstore register-shard <piece CID>
```

If you have multiple deals in such state then you will need to generate a list of registered pieces with piece store and then compare with the shards available in the dagstore to create a list of missing shards.

{% hint style="danger" %}
Please stop accepting any deals and ensure all current deals are handed off to the lotus-miner (sealer) subsystem before proceeding from here.
{% endhint %}

1\. Create a list of all registered pieces with piece store and count them

```
boostd pieces list-pieces |wc -l
boostd pieces list-pieces > piece_list.txt
```

2\. Create a list of registered shards and count them

```
boostd dagstore list-shards | awk '{print $1}' | sed 1d | wc -l
boostd dagstore list-shards | awk '{print $1}' | sed 1d > shard_list.txt
```

3\. Identify the missing shards

```
comm -13 <(sort shard_list.txt) <(sort piece_list.txt)
```

4\. If the piece count and shard count is similar or close to each other, we should manually identify the missing pieces and not use an automated procedure to register them. The reason for this approach is to avoid trying to register a shard for the deal that is still being sealed or is under process. These deals should automatically get registered with the dagstore once they finish sealing. If there is a huge difference in the number of the shards and pieces then proceed to the next step.

{% hint style="danger" %}
Please note that expired deals are not removed from the piece store. So, there are chances that you might see piece CIDs pointing to the sectors that no longer exists. Registering these sectors with dagtore will trigger an unseal job on a non existent sector. We would request you to verify that piece about to be registered is part of an active sector or not.
{% endhint %}

5\. Create a list of all sectors on `lotus-miner` and redirect the output to a file. Copy the output file to boost node to be used by the below command.&#x20;

```
lotus-miner sectors list | awk '{print $1 " " $2}' | grep -v ID > aclist.txt
```

6\. Generate a list of pieces not found in any active sector (output of step 5).

<pre data-overflow="wrap"><code><strong>for i in $(boostd pieces list-pieces); do sector_list=`boostd pieces piece-info $i | awk '{print $2}'| sed -ne '/SectorID/,$p' | grep -v SectorID`; for j in $sector_list; do grep -w $j aclist.txt > /dev/null; if [ $? -eq 0 ]; then break; else echo "Piece $i not found in any sector"; fi; done; done > pieces_without_sectors.txt</strong></code></pre>

7\. Create a list of shards to be registered with the dagstore.

```
comm -13 <(sort pieces_without_sectors.txt) <(sort <OUTPUT OF STEP 3 IN A FILE>)
```

8\. Register the shards with dagstore in an automated fashion.

{% code overflow="wrap" %}
```
for i in `cat <OUTPUT OF STEP 7 IN A FILE>` ; do boostd dagstore register-shard $i; done
```
{% endcode %}

Please note that each shard may take upto 3-5 minutes to get registered. So, the above command might take hours or days to complete depending upon the number of missing shards.
