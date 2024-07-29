---
description: >-
  This page contains all Boost API definitions. Interfaces defined here are
  exposed as JSON-RPC 2.0 endpoints by the boostd daemon.
---

# JSON-RPC API

## Go JSON-RPC client

To use the Boost Go client, the Go RPC-API library can be used to interact with the Boost API node.

1. Import the necessary Go module:

```
go get github.com/filecoin-project/go-jsonrpc
```

1. Create the following script:

```go
package main

import (
    "context"
    "fmt"
    "log"
    "net/http"

    jsonrpc "github.com/filecoin-project/go-jsonrpc"
    boostapi "github.com/filecoin-project/boost/api"
)

func main() {
    authToken := "<value found in ~/.boost/token>"
    headers := http.Header{"Authorization": []string{"Bearer " + authToken}}
    addr := "127.0.0.1:1288"

    var api boostapi.BoostStruct
    closer, err := jsonrpc.NewMergeClient(context.Background(), "ws://"+addr+"/rpc/v0", "Filecoin", []interface{}{&api.Internal, &api.CommonStruct.Internal}, headers)
    if err != nil {
        log.Fatalf("connecting with boost failed: %s", err)
    }
    defer closer()

    // Now you can call any API you're interested in.
    netAddrs, err := api.NetAddrsListen(context.Background())
    if err != nil {
      log.Fatalf("calling netAddrsListen: %s", err)
    }
    fmt.Printf("Boost is listening on: %s", netAddrs.Addrs[0])
}
```

1. Run `go mod init` to set up your `go.mod` file
2. You should now be able to interact with the Boost API.

## Python JSON-RPC client

The JSON-RPC API can also be communicated with programmatically from other languages. Here is an example written in Python. Note that the `method` must be prefixed with `Filecoin.`

```
import requests
import json

def main():
    url = "http://localhost:3051/rpc/v0"
    headers = {'content-type': 'application/json', "Authorization": "Bearer <token>"}
    payload = {
        "method": "Filecoin.BoostOfflineDealWithData",
        "params": [
            "<deal-uuid>",
            "<file-path>",
            True
        ],
        "jsonrpc": "2.0",
        "id": 1,
    }
    response = requests.post(url, data=json.dumps(payload), headers=headers)
    print(response.text)

if __name__ == "__main__":
    main()
```

## Groups
* [Uncategorized](#uncategorized)
  * [Discover](#discover)
* [Auth](#auth)
  * [AuthNew](#authnew)
  * [AuthVerify](#authverify)
* [Blockstore](#blockstore)
  * [BlockstoreGet](#blockstoreget)
  * [BlockstoreGetSize](#blockstoregetsize)
  * [BlockstoreHas](#blockstorehas)
* [Boost](#boost)
  * [BoostDeal](#boostdeal)
  * [BoostDealBySignedProposalCid](#boostdealbysignedproposalcid)
  * [BoostDirectDeal](#boostdirectdeal)
  * [BoostDummyDeal](#boostdummydeal)
  * [BoostIndexerAnnounceAllDeals](#boostindexerannouncealldeals)
  * [BoostIndexerAnnounceDeal](#boostindexerannouncedeal)
  * [BoostIndexerAnnounceDealRemoved](#boostindexerannouncedealremoved)
  * [BoostIndexerAnnounceLatest](#boostindexerannouncelatest)
  * [BoostIndexerAnnounceLatestHttp](#boostindexerannouncelatesthttp)
  * [BoostIndexerAnnounceLegacyDeal](#boostindexerannouncelegacydeal)
  * [BoostIndexerListMultihashes](#boostindexerlistmultihashes)
  * [BoostLegacyDealByProposalCid](#boostlegacydealbyproposalcid)
  * [BoostOfflineDealWithData](#boostofflinedealwithdata)
* [Log](#log)
  * [LogList](#loglist)
  * [LogSetLevel](#logsetlevel)
* [Market](#market)
  * [MarketGetAsk](#marketgetask)
* [Net](#net)
  * [ID](#id)
  * [NetAddrsListen](#netaddrslisten)
  * [NetAgentVersion](#netagentversion)
  * [NetAutoNatStatus](#netautonatstatus)
  * [NetBandwidthStats](#netbandwidthstats)
  * [NetBandwidthStatsByPeer](#netbandwidthstatsbypeer)
  * [NetBandwidthStatsByProtocol](#netbandwidthstatsbyprotocol)
  * [NetBlockAdd](#netblockadd)
  * [NetBlockList](#netblocklist)
  * [NetBlockRemove](#netblockremove)
  * [NetConnect](#netconnect)
  * [NetConnectedness](#netconnectedness)
  * [NetDisconnect](#netdisconnect)
  * [NetFindPeer](#netfindpeer)
  * [NetLimit](#netlimit)
  * [NetPeerInfo](#netpeerinfo)
  * [NetPeers](#netpeers)
  * [NetPing](#netping)
  * [NetProtectAdd](#netprotectadd)
  * [NetProtectList](#netprotectlist)
  * [NetProtectRemove](#netprotectremove)
  * [NetPubsubScores](#netpubsubscores)
  * [NetSetLimit](#netsetlimit)
  * [NetStat](#netstat)
* [Backup](#backup)
  * [OnlineBackup](#onlinebackup)
* [PieceDirectory](#piecedirectory)
  * [PdBuildIndexForPieceCid](#pdbuildindexforpiececid)
  * [PdCleanup](#pdcleanup)
  * [PdRemoveDealForPiece](#pdremovedealforpiece)
## 

## Uncategorized



### Discover


Perms: read

Inputs: `null`

Response:
```json
{
  "info": {
    "title": "Boost RPC API",
    "version": "1.2.1/generated=2020-11-22T08:22:42-06:00"
  },
  "methods": [],
  "openrpc": "1.2.6"
}
```

## Auth


### AuthNew


Perms: admin

Inputs:
```json
[
  [
    "write"
  ]
]
```

Response: `"Ynl0ZSBhcnJheQ=="`

### AuthVerify


Perms: read

Inputs:
```json
[
  "string value"
]
```

Response:
```json
[
  "write"
]
```

## Blockstore


### BlockstoreGet
There are not yet any comments for this method.

Perms: read

Inputs:
```json
[
  null
]
```

Response: `"Ynl0ZSBhcnJheQ=="`

### BlockstoreGetSize


Perms: read

Inputs:
```json
[
  null
]
```

Response: `123`

### BlockstoreHas


Perms: read

Inputs:
```json
[
  null
]
```

Response: `true`

## Boost


### BoostDeal


Perms: admin

Inputs:
```json
[
  "07070707-0707-0707-0707-070707070707"
]
```

Response:
```json
{
  "DealUuid": "07070707-0707-0707-0707-070707070707",
  "CreatedAt": "0001-01-01T00:00:00Z",
  "ClientDealProposal": {
    "Proposal": {
      "PieceCID": null,
      "PieceSize": 1032,
      "VerifiedDeal": true,
      "Client": "f01234",
      "Provider": "f01234",
      "Label": "",
      "StartEpoch": 10101,
      "EndEpoch": 10101,
      "StoragePricePerEpoch": "0",
      "ProviderCollateral": "0",
      "ClientCollateral": "0"
    },
    "ClientSignature": {
      "Type": 2,
      "Data": "Ynl0ZSBhcnJheQ=="
    }
  },
  "IsOffline": true,
  "CleanupData": true,
  "ClientPeerID": "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf",
  "DealDataRoot": null,
  "InboundFilePath": "string value",
  "Transfer": {
    "Type": "string value",
    "ClientID": "string value",
    "Params": "Ynl0ZSBhcnJheQ==",
    "Size": 42
  },
  "ChainDealID": 5432,
  "PublishCID": null,
  "SectorID": 9,
  "Offset": 1032,
  "Length": 1032,
  "Checkpoint": 1,
  "CheckpointAt": "0001-01-01T00:00:00Z",
  "Err": "string value",
  "Retry": "auto",
  "NBytesReceived": 9,
  "FastRetrieval": true,
  "AnnounceToIPNI": true
}
```

### BoostDealBySignedProposalCid


Perms: admin

Inputs:
```json
[
  null
]
```

Response:
```json
{
  "DealUuid": "07070707-0707-0707-0707-070707070707",
  "CreatedAt": "0001-01-01T00:00:00Z",
  "ClientDealProposal": {
    "Proposal": {
      "PieceCID": null,
      "PieceSize": 1032,
      "VerifiedDeal": true,
      "Client": "f01234",
      "Provider": "f01234",
      "Label": "",
      "StartEpoch": 10101,
      "EndEpoch": 10101,
      "StoragePricePerEpoch": "0",
      "ProviderCollateral": "0",
      "ClientCollateral": "0"
    },
    "ClientSignature": {
      "Type": 2,
      "Data": "Ynl0ZSBhcnJheQ=="
    }
  },
  "IsOffline": true,
  "CleanupData": true,
  "ClientPeerID": "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf",
  "DealDataRoot": null,
  "InboundFilePath": "string value",
  "Transfer": {
    "Type": "string value",
    "ClientID": "string value",
    "Params": "Ynl0ZSBhcnJheQ==",
    "Size": 42
  },
  "ChainDealID": 5432,
  "PublishCID": null,
  "SectorID": 9,
  "Offset": 1032,
  "Length": 1032,
  "Checkpoint": 1,
  "CheckpointAt": "0001-01-01T00:00:00Z",
  "Err": "string value",
  "Retry": "auto",
  "NBytesReceived": 9,
  "FastRetrieval": true,
  "AnnounceToIPNI": true
}
```

### BoostDirectDeal


Perms: admin

Inputs:
```json
[
  {
    "DealUUID": "07070707-0707-0707-0707-070707070707",
    "AllocationID": 0,
    "PieceCid": null,
    "ClientAddr": "f01234",
    "StartEpoch": 10101,
    "EndEpoch": 10101,
    "FilePath": "string value",
    "DeleteAfterImport": true,
    "RemoveUnsealedCopy": true,
    "SkipIPNIAnnounce": true
  }
]
```

Response:
```json
{
  "Accepted": true,
  "Reason": "string value"
}
```

### BoostDummyDeal


Perms: admin

Inputs:
```json
[
  {
    "DealUUID": "07070707-0707-0707-0707-070707070707",
    "IsOffline": true,
    "ClientDealProposal": {
      "Proposal": {
        "PieceCID": null,
        "PieceSize": 1032,
        "VerifiedDeal": true,
        "Client": "f01234",
        "Provider": "f01234",
        "Label": "",
        "StartEpoch": 10101,
        "EndEpoch": 10101,
        "StoragePricePerEpoch": "0",
        "ProviderCollateral": "0",
        "ClientCollateral": "0"
      },
      "ClientSignature": {
        "Type": 2,
        "Data": "Ynl0ZSBhcnJheQ=="
      }
    },
    "DealDataRoot": null,
    "Transfer": {
      "Type": "string value",
      "ClientID": "string value",
      "Params": "Ynl0ZSBhcnJheQ==",
      "Size": 42
    },
    "RemoveUnsealedCopy": true,
    "SkipIPNIAnnounce": true
  }
]
```

Response:
```json
{
  "Accepted": true,
  "Reason": "string value"
}
```

### BoostIndexerAnnounceAllDeals
There are not yet any comments for this method.

Perms: admin

Inputs: `null`

Response: `{}`

### BoostIndexerAnnounceDeal


Perms: admin

Inputs:
```json
[
  {
    "DealUuid": "07070707-0707-0707-0707-070707070707",
    "CreatedAt": "0001-01-01T00:00:00Z",
    "ClientDealProposal": {
      "Proposal": {
        "PieceCID": null,
        "PieceSize": 1032,
        "VerifiedDeal": true,
        "Client": "f01234",
        "Provider": "f01234",
        "Label": "",
        "StartEpoch": 10101,
        "EndEpoch": 10101,
        "StoragePricePerEpoch": "0",
        "ProviderCollateral": "0",
        "ClientCollateral": "0"
      },
      "ClientSignature": {
        "Type": 2,
        "Data": "Ynl0ZSBhcnJheQ=="
      }
    },
    "IsOffline": true,
    "CleanupData": true,
    "ClientPeerID": "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf",
    "DealDataRoot": null,
    "InboundFilePath": "string value",
    "Transfer": {
      "Type": "string value",
      "ClientID": "string value",
      "Params": "Ynl0ZSBhcnJheQ==",
      "Size": 42
    },
    "ChainDealID": 5432,
    "PublishCID": null,
    "SectorID": 9,
    "Offset": 1032,
    "Length": 1032,
    "Checkpoint": 1,
    "CheckpointAt": "0001-01-01T00:00:00Z",
    "Err": "string value",
    "Retry": "auto",
    "NBytesReceived": 9,
    "FastRetrieval": true,
    "AnnounceToIPNI": true
  }
]
```

Response: `null`

### BoostIndexerAnnounceDealRemoved


Perms: admin

Inputs:
```json
[
  null
]
```

Response: `null`

### BoostIndexerAnnounceLatest


Perms: admin

Inputs: `null`

Response: `null`

### BoostIndexerAnnounceLatestHttp


Perms: admin

Inputs:
```json
[
  [
    "string value"
  ]
]
```

Response: `null`

### BoostIndexerAnnounceLegacyDeal


Perms: admin

Inputs:
```json
[
  null
]
```

Response: `null`

### BoostIndexerListMultihashes


Perms: admin

Inputs:
```json
[
  "Ynl0ZSBhcnJheQ=="
]
```

Response:
```json
[
  "Bw=="
]
```

### BoostLegacyDealByProposalCid


Perms: admin

Inputs:
```json
[
  null
]
```

Response:
```json
{
  "Proposal": {
    "PieceCID": null,
    "PieceSize": 1032,
    "VerifiedDeal": true,
    "Client": "f01234",
    "Provider": "f01234",
    "Label": "",
    "StartEpoch": 10101,
    "EndEpoch": 10101,
    "StoragePricePerEpoch": "0",
    "ProviderCollateral": "0",
    "ClientCollateral": "0"
  },
  "ClientSignature": {
    "Type": 2,
    "Data": "Ynl0ZSBhcnJheQ=="
  },
  "ProposalCid": null,
  "AddFundsCid": null,
  "PublishCid": null,
  "Miner": "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf",
  "Client": "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf",
  "State": 42,
  "PiecePath": "",
  "MetadataPath": "",
  "SlashEpoch": 10101,
  "FastRetrieval": true,
  "Message": "string value",
  "FundsReserved": "0",
  "Ref": {
    "TransferType": "string value",
    "Root": null,
    "PieceCid": null,
    "PieceSize": 1024,
    "RawBlockSize": 42
  },
  "AvailableForRetrieval": true,
  "DealID": 5432,
  "CreationTime": "0001-01-01T00:00:00Z",
  "TransferChannelId": {
    "Initiator": "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf",
    "Responder": "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf",
    "ID": 3
  },
  "SectorNumber": 9,
  "InboundCAR": "string value"
}
```

### BoostOfflineDealWithData


Perms: admin

Inputs:
```json
[
  "07070707-0707-0707-0707-070707070707",
  "string value",
  true
]
```

Response:
```json
{
  "Accepted": true,
  "Reason": "string value"
}
```


## Log


### LogList


Perms: write

Inputs: `null`

Response:
```json
[
  "string value"
]
```

### LogSetLevel


Perms: write

Inputs:
```json
[
  "string value",
  "string value"
]
```

Response: `{}`

## Market


### MarketGetAsk


Perms: read

Inputs: `null`

Response:
```json
{
  "Ask": {
    "Price": "0",
    "VerifiedPrice": "0",
    "MinPieceSize": 1032,
    "MaxPieceSize": 1032,
    "Miner": "f01234",
    "Timestamp": 10101,
    "Expiry": 10101,
    "SeqNo": 42
  },
  "Signature": {
    "Type": 2,
    "Data": "Ynl0ZSBhcnJheQ=="
  }
}
```

## Net

### ID


Perms: read

Inputs: `null`

Response: `"12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf"`


### NetAddrsListen


Perms: read

Inputs: `null`

Response:
```json
{
  "ID": "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf",
  "Addrs": [
    "/ip4/52.36.61.156/tcp/1347/p2p/12D3KooWFETiESTf1v4PGUvtnxMAcEFMzLZbJGg4tjWfGEimYior"
  ]
}
```

### NetAgentVersion


Perms: read

Inputs:
```json
[
  "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf"
]
```

Response: `"string value"`

### NetAutoNatStatus


Perms: read

Inputs: `null`

Response:
```json
{
  "Reachability": 1,
  "PublicAddrs": [
    "string value"
  ]
}
```

### NetBandwidthStats


Perms: read

Inputs: `null`

Response:
```json
{
  "TotalIn": 9,
  "TotalOut": 9,
  "RateIn": 12.3,
  "RateOut": 12.3
}
```

### NetBandwidthStatsByPeer


Perms: read

Inputs: `null`

Response:
```json
{
  "12D3KooWSXmXLJmBR1M7i9RW9GQPNUhZSzXKzxDHWtAgNuJAbyEJ": {
    "TotalIn": 174000,
    "TotalOut": 12500,
    "RateIn": 100,
    "RateOut": 50
  }
}
```

### NetBandwidthStatsByProtocol


Perms: read

Inputs: `null`

Response:
```json
{
  "/fil/hello/1.0.0": {
    "TotalIn": 174000,
    "TotalOut": 12500,
    "RateIn": 100,
    "RateOut": 50
  }
}
```

### NetBlockAdd


Perms: admin

Inputs:
```json
[
  {
    "Peers": [
      "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf"
    ],
    "IPAddrs": [
      "string value"
    ],
    "IPSubnets": [
      "string value"
    ]
  }
]
```

Response: `{}`

### NetBlockList


Perms: read

Inputs: `null`

Response:
```json
{
  "Peers": [
    "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf"
  ],
  "IPAddrs": [
    "string value"
  ],
  "IPSubnets": [
    "string value"
  ]
}
```

### NetBlockRemove


Perms: admin

Inputs:
```json
[
  {
    "Peers": [
      "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf"
    ],
    "IPAddrs": [
      "string value"
    ],
    "IPSubnets": [
      "string value"
    ]
  }
]
```

Response: `{}`

### NetConnect


Perms: write

Inputs:
```json
[
  {
    "ID": "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf",
    "Addrs": [
      "/ip4/52.36.61.156/tcp/1347/p2p/12D3KooWFETiESTf1v4PGUvtnxMAcEFMzLZbJGg4tjWfGEimYior"
    ]
  }
]
```

Response: `{}`

### NetConnectedness


Perms: read

Inputs:
```json
[
  "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf"
]
```

Response: `1`

### NetDisconnect


Perms: write

Inputs:
```json
[
  "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf"
]
```

Response: `{}`

### NetFindPeer


Perms: read

Inputs:
```json
[
  "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf"
]
```

Response:
```json
{
  "ID": "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf",
  "Addrs": [
    "/ip4/52.36.61.156/tcp/1347/p2p/12D3KooWFETiESTf1v4PGUvtnxMAcEFMzLZbJGg4tjWfGEimYior"
  ]
}
```

### NetLimit


Perms: read

Inputs:
```json
[
  "string value"
]
```

Response:
```json
{
  "Memory": 9,
  "Streams": 123,
  "StreamsInbound": 123,
  "StreamsOutbound": 123,
  "Conns": 123,
  "ConnsInbound": 123,
  "ConnsOutbound": 123,
  "FD": 123
}
```

### NetPeerInfo


Perms: read

Inputs:
```json
[
  "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf"
]
```

Response:
```json
{
  "ID": "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf",
  "Agent": "string value",
  "Addrs": [
    "string value"
  ],
  "Protocols": [
    "string value"
  ],
  "ConnMgrMeta": {
    "FirstSeen": "0001-01-01T00:00:00Z",
    "Value": 123,
    "Tags": {
      "name": 42
    },
    "Conns": {
      "name": "2021-03-08T22:52:18Z"
    }
  }
}
```

### NetPeers


Perms: read

Inputs: `null`

Response:
```json
[
  {
    "ID": "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf",
    "Addrs": [
      "/ip4/52.36.61.156/tcp/1347/p2p/12D3KooWFETiESTf1v4PGUvtnxMAcEFMzLZbJGg4tjWfGEimYior"
    ]
  }
]
```

### NetPing


Perms: read

Inputs:
```json
[
  "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf"
]
```

Response: `60000000000`

### NetProtectAdd


Perms: admin

Inputs:
```json
[
  [
    "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf"
  ]
]
```

Response: `{}`

### NetProtectList


Perms: read

Inputs: `null`

Response:
```json
[
  "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf"
]
```

### NetProtectRemove


Perms: admin

Inputs:
```json
[
  [
    "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf"
  ]
]
```

Response: `{}`

### NetPubsubScores


Perms: read

Inputs: `null`

Response:
```json
[
  {
    "ID": "12D3KooWGzxzKZYveHXtpG6AsrUJBcWxHBFS2HsEoGTxrMLvKXtf",
    "Score": {
      "Score": 12.3,
      "Topics": {
        "/blocks": {
          "TimeInMesh": 60000000000,
          "FirstMessageDeliveries": 122,
          "MeshMessageDeliveries": 1234,
          "InvalidMessageDeliveries": 3
        }
      },
      "AppSpecificScore": 12.3,
      "IPColocationFactor": 12.3,
      "BehaviourPenalty": 12.3
    }
  }
]
```

### NetSetLimit


Perms: admin

Inputs:
```json
[
  "string value",
  {
    "Memory": 9,
    "Streams": 123,
    "StreamsInbound": 123,
    "StreamsOutbound": 123,
    "Conns": 123,
    "ConnsInbound": 123,
    "ConnsOutbound": 123,
    "FD": 123
  }
]
```

Response: `{}`

### NetStat


Perms: read

Inputs:
```json
[
  "string value"
]
```

Response:
```json
{
  "System": {
    "NumStreamsInbound": 123,
    "NumStreamsOutbound": 123,
    "NumConnsInbound": 123,
    "NumConnsOutbound": 123,
    "NumFD": 123,
    "Memory": 9
  },
  "Transient": {
    "NumStreamsInbound": 123,
    "NumStreamsOutbound": 123,
    "NumConnsInbound": 123,
    "NumConnsOutbound": 123,
    "NumFD": 123,
    "Memory": 9
  },
  "Services": {
    "abc": {
      "NumStreamsInbound": 1,
      "NumStreamsOutbound": 2,
      "NumConnsInbound": 3,
      "NumConnsOutbound": 4,
      "NumFD": 5,
      "Memory": 123
    }
  },
  "Protocols": {
    "abc": {
      "NumStreamsInbound": 1,
      "NumStreamsOutbound": 2,
      "NumConnsInbound": 3,
      "NumConnsOutbound": 4,
      "NumFD": 5,
      "Memory": 123
    }
  },
  "Peers": {
    "abc": {
      "NumStreamsInbound": 1,
      "NumStreamsOutbound": 2,
      "NumConnsInbound": 3,
      "NumConnsOutbound": 4,
      "NumFD": 5,
      "Memory": 123
    }
  }
}
```

## Backup


### OnlineBackup
There are not yet any comments for this method.

Perms: admin

Inputs:
```json
[
  "string value"
]
```

Response: `{}`

## PieceDirectory


### PdBuildIndexForPieceCid
There are not yet any comments for this method.

Perms: admin

Inputs:
```json
[
  null
]
```

Response: `{}`

### PdCleanup


Perms: admin

Inputs: `null`

Response: `{}`

### PdRemoveDealForPiece


Perms: admin

Inputs:
```json
[
  null,
  "string value"
]
```

Response: `{}`

