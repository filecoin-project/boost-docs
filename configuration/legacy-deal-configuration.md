---
description: Advanced configurations you can tune to optimize your legacy deal onboarding
---

# Legacy Deal configuration

### Dealmaking section

This section controls parameters for making storage and retrieval deals:

<pre class="language-toml"><code class="lang-toml"><strong>[LotusDealmaking]
</strong>  # When enabled, the miner can accept online deals
  #
  # type: bool
  # env var: LOTUS_LOTUSDEALMAKING_CONSIDERONLINESTORAGEDEALS
  #ConsiderOnlineStorageDeals = true

  # When enabled, the miner can accept offline deals
  #
  # type: bool
  # env var: LOTUS_LOTUSDEALMAKING_CONSIDEROFFLINESTORAGEDEALS
  #ConsiderOfflineStorageDeals = true

  # When enabled, the miner can accept retrieval deals
  #
  # type: bool
  # env var: LOTUS_LOTUSDEALMAKING_CONSIDERONLINERETRIEVALDEALS
  #ConsiderOnlineRetrievalDeals = true

  # When enabled, the miner can accept offline retrieval deals
  #
  # type: bool
  # env var: LOTUS_LOTUSDEALMAKING_CONSIDEROFFLINERETRIEVALDEALS
  #ConsiderOfflineRetrievalDeals = true

  # When enabled, the miner can accept verified deals
  #
  # type: bool
  # env var: LOTUS_LOTUSDEALMAKING_CONSIDERVERIFIEDSTORAGEDEALS
  #ConsiderVerifiedStorageDeals = true

  # When enabled, the miner can accept unverified deals
  #
  # type: bool
  # env var: LOTUS_LOTUSDEALMAKING_CONSIDERUNVERIFIEDSTORAGEDEALS
  #ConsiderUnverifiedStorageDeals = true

  # A list of Data CIDs to reject when making deals
  #
  # type: []cid.Cid
  # env var: LOTUS_LOTUSDEALMAKING_PIECECIDBLOCKLIST
  #PieceCidBlocklist = []

  # Maximum expected amount of time getting the deal into a sealed sector will take
  # This includes the time the deal will need to get transferred and published
  # before being assigned to a sector
  #
  # type: Duration
  # env var: LOTUS_LOTUSDEALMAKING_EXPECTEDSEALDURATION
  #ExpectedSealDuration = "24h0m0s"

  # Maximum amount of time proposed deal StartEpoch can be in future
  #
  # type: Duration
  # env var: LOTUS_LOTUSDEALMAKING_MAXDEALSTARTDELAY
  #MaxDealStartDelay = "336h0m0s"

  # When a deal is ready to publish, the amount of time to wait for more
  # deals to be ready to publish before publishing them all as a batch
  #
  # type: Duration
  # env var: LOTUS_LOTUSDEALMAKING_PUBLISHMSGPERIOD
  # PublishMsgPeriod = "40m0s"

  # The maximum number of deals to include in a single PublishStorageDeals
  # message
  #
  # type: uint64
  # env var: LOTUS_LOTUSDEALMAKING_MAXDEALSPERPUBLISHMSG
  #MaxDealsPerPublishMsg = 8

  # The maximum collateral that the provider will put up against a deal,
  # as a multiplier of the minimum collateral bound
  #
  # type: uint64
  # env var: LOTUS_LOTUSDEALMAKING_MAXPROVIDERCOLLATERALMULTIPLIER
  #MaxProviderCollateralMultiplier = 2

  # The maximum allowed disk usage size in bytes of staging deals not yet
  # passed to the sealing node by the markets service. 0 is unlimited.
  #
  # type: int64
  # env var: LOTUS_LOTUSDEALMAKING_MAXSTAGINGDEALSBYTES
  # MaxStagingDealsBytes = 100000000000

  # The maximum number of parallel online data transfers for storage deals
  #
  # type: uint64
  # env var: LOTUS_LOTUSDEALMAKING_SIMULTANEOUSTRANSFERSFORSTORAGE
  #SimultaneousTransfersForStorage = 20

  # The maximum number of simultaneous data transfers from any single client
  # for storage deals.
  # Unset by default (0), and values higher than SimultaneousTransfersForStorage
  # will have no effect; i.e. the total number of simultaneous data transfers
  # across all storage clients is bound by SimultaneousTransfersForStorage
  # regardless of this number.
  #
  # type: uint64
  # env var: LOTUS_LOTUSDEALMAKING_SIMULTANEOUSTRANSFERSFORSTORAGEPERCLIENT
  #SimultaneousTransfersForStoragePerClient = 0

  # The maximum number of parallel online data transfers for retrieval deals
  #
  # type: uint64
  # env var: LOTUS_LOTUSDEALMAKING_SIMULTANEOUSTRANSFERSFORRETRIEVAL
  #SimultaneousTransfersForRetrieval = 20

  # Minimum start epoch buffer to give time for sealing of sector with deal.
  #
  # type: uint64
  # env var: LOTUS_LOTUSDEALMAKING_STARTEPOCHSEALINGBUFFER
  #StartEpochSealingBuffer = 480

  # A command used for fine-grained evaluation of storage deals
  # see https://lotus.filecoin.io/storage-providers/advanced-configurations/market/#using-filters-for-fine-grained-storage-and-retrieval-deal-acceptance for more details
  #
  # type: string
  # env var: LOTUS_LOTUSDEALMAKING_FILTER
  #Filter = ""

  # A command used for fine-grained evaluation of retrieval deals
  # see https://lotus.filecoin.io/storage-providers/advanced-configurations/market/#using-filters-for-fine-grained-storage-and-retrieval-deal-acceptance for more details
  #
  # type: string
  # env var: LOTUS_LOTUSDEALMAKING_RETRIEVALFILTER
  #RetrievalFilter = ""

  [LotusDealmaking.RetrievalPricing]
    # env var: LOTUS_LOTUSDEALMAKING_RETRIEVALPRICING_STRATEGY
    #Strategy = "default"

    [LotusDealmaking.RetrievalPricing.Default]
      # env var: LOTUS_LOTUSDEALMAKING_RETRIEVALPRICING_DEFAULT_VERIFIEDDEALSFREETRANSFER
      #VerifiedDealsFreeTransfer = true

    [LotusDealmaking.RetrievalPricing.External]
      # env var: LOTUS_LOTUSDEALMAKING_RETRIEVALPRICING_EXTERNAL_PATH
      #Path = ""
</code></pre>

`ExpectedSealDuration` is an estimate of how long sealing will take and is used to reject deals whose start epoch might be earlier than the expected completion of sealing. It can be estimated by [benchmarking](https://lotus.filecoin.io/storage-providers/operate/benchmarks/) or by [pledging a sector](https://lotus.filecoin.io/storage-providers/operate/sector-pledging/).

{% hint style="warning" %}
The final value of `ExpectedSealDuration` should equal `(TIME_TO_SEAL_A_SECTOR + WaitDealsDelay) * 1.5`. This equation ensures that the miner does not commit to having the sector sealed too soon
{% endhint %}

`StartEpochSealingBuffer` allows `lotus-miner` to seal a sector before a certain epoch. For example: if the current epoch is 1000 and a deal within a sector must start on epoch 1500, then `lotus-miner` must wait until the current epoch is 1500 before it can start sealing that sector. However, if Boost sets `StartEpochSealingBuffer` to 500, the `lotus-miner` can start sealing the sector at epoch 1000.

If there are multiple deals in a sector, the deal with a start time closest to the current epoch is what `StartEpochSealingBuffer` will be based off. So, if the sector in our example has three deals that start on epoch 1000, 1200, and 1400, then `lotus-miner` will start sealing the sector at epoch 500.

#### Publishing several deals in one message

The `PublishStorageDeals` message can publish multiple deals in a single message. When a deal is ready to be published, Boost will wait up to `PublishMsgPeriod` for other deals to be ready before sending the `PublishStorageDeals` message.

However, once `MaxDealsPerPublishMsg` is ready, Boost will immediately publish all the deals.

For example, if `PublishMsgPeriod` is 1 hour:

* At 1:00 pm, deal 1 is ready to publish. Boost will wait until 2:00 pm for other deals to be ready before sending `PublishStorageDeals`.
* At 1:30 pm, Deal 2 is ready to publish
* At 1:45 pm, Deal 3 is ready to publish
* At 2:00pm, Boost publishes Deals 1, 2, and 3 in a single `PublishStorageDeals` message.

If `MaxDealsPerPublishMsg` is 2, then in the above example, when deal 2 is ready to be published at 1:30, Boost would immediately publish Deals 1 & 2 in a single `PublishStorageDeals` message. Deal 3 would be published in a subsequent `PublishStorageDeals` message.

{% hint style="danger" %}
If any of the deals in the `PublishStorageDeals` fails validation upon execution, or if the start epoch has passed, all deals will fail to be published
{% endhint %}

### Using filters for fine-grained storage and retrieval deal acceptance

Your use case might demand very precise and dynamic control over a combination of deal parameters.

Boost provides two IPC hooks allowing you to name a command to execute for every deal before the miner accepts it:

* `Filter` for storage deals.
* `RetrievalFilter` for retrieval deals.

The executed command receives a JSON representation of the deal parameters on standard input, and upon completion, its exit code is interpreted as:

* `0`: success, proceed with the deal.
* `non-0`: failure, reject the deal.

The most trivial filter rejecting any retrieval deal would be something like: `RetrievalFilter = "/bin/false"`. `/bin/false` is binary that immediately exits with a code of `1`.

[This Perl script](https://gist.github.com/ribasushi/53b7383aeb6e6f9b030210f4d64351d5/9bd6e898f94d20b50e7c7586dc8b8f3a45dab07c#file-dealfilter-pl) lets the miner deny specific clients and only accept deals that are set to start relatively soon.

You can also use a third party content policy framework like [CIDgravity](https://www.cidgravity.com/) or `bitscreen` by Murmuration Labs:

```shell
# grab filter program
go get -u -v github.com/Murmuration-Labs/bitscreen

# add it to both filters
Filter = "/path/to/go/bin/bitscreen"
RetrievalFilter = "/path/to/go/bin/bitscreen"
```
