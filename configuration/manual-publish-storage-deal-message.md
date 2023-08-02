---
description: >-
  This configuration parameter allows SPs to send PSD messages based on their
  requirements
---

# Manual Publish Storage Deal Message

By default boost publishes storage deals automatically once 8 deals are in the publish queue, or after 24 hours. However some SPs need to be able to control exactly which deals to publish and when. This new feature allows SPs to turn on manual PSD. Once it is turned on, Boost will no longer send any PSD messages unless explicitly prompted by the user. The feature can be turned on with the below config variable:

```
[Dealmaking]
        // When set to true, the user is responsible for publishing deals manually.
	// The values of MaxDealsPerPublishMsg and PublishMsgPeriod will be
	// ignored, and deals will remain in the pending state until manually published.
	ManualDealPublish bool

```

### Querying the deals pending to be published

The deals which have not been published can be queried using the Graphql endpoint of the Boost. The curl for the query:

`curl -X POST -H "Content-Type: application/json" -d '{"query":"query { dealPublish{ ManualPSD Deals {ID IsLegacy ClientAddress ProviderAddress CreatedAt PieceCid PieceSize ProviderCollateral StartEpoch EndEpoch ClientPeerID PublishCid Transfer { Type Size Params ClientID} Message }}}"}' http://localhost:8080/graphql/query | jq`

### Decision

SPs can create a decision script which queries the Graphql endpoint using the above curl and take a decision on which deal to be published.

### Publishing deals

Once the decision has been taken on which deals to be published, SPs can use the Grapqhql endpoint to Publish the deals.\
The mutation is:

`publishPendingDeals(ids: [ID!]!): [ID!]!`

which takes an array of deal UUIDs to be published and an error message is returned. The following curl can be used to publish the deal.

`curl -X POST -H "Content-Type: application/json" -d '{"query":"mutation {publishPendingDeals(ids: [\"d9f849f1-d5d8-4bfc-b034-2866bddfc8cb\", \"a8eb58ef-7381-4251-ae7a-1227c032c0b9\"])}"}' http://localhost:8080/graphql/query | jq`

**Successful output**

```
{
  "data": {
    "publishPendingDeals": [
      "d9f849f1-d5d8-4bfc-b034-2866bddfc8cb",
      "a8eb58ef-7381-4251-ae7a-1227c032c0b9"
    ]
  }
}
```

**Failure output**

```
{
  "errors": [
    {
      "message": "failed to get deal details from DB cc78d877-f69c-410b-80a7-b23f2fab8951: scanning deal row: sql: no rows in result set",
      "path": [
        "publishPendingDeals"
      ]
    }
  ],
  "data": null
}
```

### Publish all deals

The publish all button in UI would have the same functionality as before. Same would be true for the Graphql mutation `dealPublishNow`

### UI screenshots



<figure><img src="../.gitbook/assets/Screenshot 2023-07-19 at 11.09.14 AM.png" alt=""><figcaption><p>Publish Page in Boost UI</p></figcaption></figure>
