---
description: Salient features of Boost
---

# Features

## Make storage deals with HTTP data transfer

Boost supports multiple options for data transfer when making storage deals, including HTTP. Clients can host their CAR file on an HTTP server, such as S3, and provide that URL when proposing the storage deal. Once accepted, Boost will automatically fetch the CAR file from the specified URL.

See [Storing data on Filecoin](storing-data-on-filecoin.md) for more details.

## Web UI

Boost comes with a web interface that can be used to manage deals, watch disk usage, monitor funds, adjust settings and more.

![Boost Web UI](<.gitbook/assets/Screen Shot 2022-03-23 at 10.12.26 AM.png>)

## A client for proposing deals that doesn't require a Lotus node

Boost comes with a client that can be used to make storage deals, and can be configured to point at a public Filecoin API endpoint. That means clients don't need to run a Filecoin node or sync from chain.

See [Storing data on Filecoin](storing-data-on-filecoin.md) for more details.
