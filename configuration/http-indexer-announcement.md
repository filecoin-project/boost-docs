---
description: Configure to publish IPNI announcements over HTTP
---

# HTTP indexer announcement

## Announce over HTTP

`IndexProvider.HttpPublisher.AnnounceOverHttp` must be set to `true` to enable the http announcements. Once HTTP announcements are enabled, the local-index provider will continue to announce over libp2p gossipsub along with HTTP for the specific indexers.

The advertisements are send to the indexer nodes defined in `DirectAnnounceURLs`. You can specify more than 1 URL to announce to multiple indexer nodes.

Once an IPNI node starts processing the advertisements, it will reach out to the Boost node to fetch the data. Thus, Boost node needs to specify a public IP and port which can be used by the indexer node to query for data.

```
[IndexProvider]
  # Enable set whether to enable indexing announcement to the network and expose endpoints that
  # allow indexer nodes to process announcements. Enabled by default.
  #
  # type: bool
  # env var: LOTUS_INDEXPROVIDER_ENABLE
  #Enable = true

  # EntriesCacheCapacity sets the maximum capacity to use for caching the indexing advertisement
  # entries. Defaults to 1024 if not specified. The cache is evicted using LRU policy. The
  # maximum storage used by the cache is a factor of EntriesCacheCapacity, EntriesChunkSize and
  # the length of multihashes being advertised. For example, advertising 128-bit long multihashes
  # with the default EntriesCacheCapacity, and EntriesChunkSize means the cache size can grow to
  # 256MiB when full.
  #
  # type: int
  # env var: LOTUS_INDEXPROVIDER_ENTRIESCACHECAPACITY
  #EntriesCacheCapacity = 1024

  # EntriesChunkSize sets the maximum number of multihashes to include in a single entries chunk.
  # Defaults to 16384 if not specified. Note that chunks are chained together for indexing
  # advertisements that include more multihashes than the configured EntriesChunkSize.
  #
  # type: int
  # env var: LOTUS_INDEXPROVIDER_ENTRIESCHUNKSIZE
  #EntriesChunkSize = 16384

  # TopicName sets the topic name on which the changes to the advertised content are announced.
  # If not explicitly specified, the topic name is automatically inferred from the network name
  # in following format: '/indexer/ingest/<network-name>'
  # Defaults to empty, which implies the topic name is inferred from network name.
  #
  # type: string
  # env var: LOTUS_INDEXPROVIDER_TOPICNAME
  #TopicName = ""

  # PurgeCacheOnStart sets whether to clear any cached entries chunks when the provider engine
  # starts. By default, the cache is rehydrated from previously cached entries stored in
  # datastore if any is present.
  #
  # type: bool
  # env var: LOTUS_INDEXPROVIDER_PURGECACHEONSTART
  #PurgeCacheOnStart = true

  [IndexProvider.Announce]
    # Make a direct announcement to a list of indexing nodes over http.
    # Note that announcements are already made over pubsub regardless
    # of this setting.
    #
    # type: bool
    # env var: LOTUS_INDEXPROVIDER_ANNOUNCE_ANNOUNCEOVERHTTP
    #AnnounceOverHttp = true

    # The list of URLs of indexing nodes to announce to.
    #
    # type: []string
    # env var: LOTUS_INDEXPROVIDER_ANNOUNCE_DIRECTANNOUNCEURLS
    #DirectAnnounceURLs = ["https://cid.contact/ingest/announce"]

  [IndexProvider.HttpPublisher]
    # If not enabled, requests are served over graphsync instead.
    #
    # type: bool
    # env var: LOTUS_INDEXPROVIDER_HTTPPUBLISHER_ENABLED
    #Enabled = true

    # Set the public hostname / IP for the index provider listener.
    # eg "82.129.73.111"
    # This is usually the same as the for the boost node.
    #
    # type: string
    # env var: LOTUS_INDEXPROVIDER_HTTPPUBLISHER_PUBLICHOSTNAME
    #PublicHostname = "82.129.73.111"

    # Set the port on which to listen for index provider requests over HTTP.
    # Note that this port must be open on the firewall.
    #
    # type: int
    # env var: LOTUS_INDEXPROVIDER_HTTPPUBLISHER_PORT
    #Port = 3401
```

