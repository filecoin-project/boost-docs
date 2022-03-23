# What is Boost?

Boost is a tool for Storage Providers to manage data storage and retrievals on filecoin. It replaces the go-fil-markets package in lotus with a standalone binary that runs alongside a lotus daemon and lotus miner. Boost exposes libp2p interfaces for making storage and retrieval deals, a web interface for managing storage deals, and a graphql interface for accessing and updating real time deal information.

![](<.gitbook/assets/Boost Interfaces.png>)

## Boost Features

### Make storage deals with HTTP data transfer

Boost supports multiple options for data transfer when making storage deals, including HTTP. Clients can host their CAR file on an http server, such as S3, and provide that url when proposing the storage deal. Once accepted, Boost will automatically fetch the CAR file from the specified url. See the [boost-client.md](boost-client.md "mention") docs for details on how to do this.

### Web UI

Boost comes with a web interface that can be used to manage deals, watch disk usage, monitor funds, adjust settings and more.

![Boost Web UI](<.gitbook/assets/Web UI>)

### Backwards compatibility with go-fil-markets

Boost supports the same endpoints as go-fil-markets for making storage and retrieval deals, getting the storage and retrieval ask, and getting the status of ongoing deals.

### A client for proposing deals that doesn't require a full node

Boost comes with a client that can be used to make storage deals, and can be configured to point at a public Filecoin API endpoint. That means clients don't need to run a Filecoin node or sync from chain. See the [boost-client.md](boost-client.md "mention") for details.
