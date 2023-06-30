---
description: Boost configuration options available in UI
---

# UI Settings

## Configuration

```
[Graphql]
  ListenAddress = "127.0.0.1"
  Port = 8080

[Monitoring]
  MpoolAlertEpochs = 30
```

By default, the web UI listens on the localhost interface on port 8080. We recommend keeping the UI listening on localhost or some internal IP within your private network to avoid accidentally exposing it to the internet.&#x20;

You can access the web UI listening on the localhost interface on a remote server, you can open an SSH tunnel from your local machine:

```
ssh -L 8080:localhost:8080 myserver
```

## Settings Page

![Settings Page Screenshot](<../.gitbook/assets/Screenshot 2022-06-08 at 7.25.55 PM.png>)



| Parameter                    | Example     | Description                                                                                      |
| ---------------------------- | ----------- | ------------------------------------------------------------------------------------------------ |
| Price / epoch / Gib          | 500000000   | Asking price for a deal in atto fils. This price is per epoch per GiB of data in a deal          |
| Verified Price / epoch / Gib | 500000000   | Asking price for a verified deal in atto fils. This price is per epoch per GiB of data in a deal |
| Min Piece Size               | 256         | Minimum size of a piece that storage provider will accept in bytes                               |
| Max Piece Size               | 34359738368 | Maximum size of a piece that storage provider will accept in bytes                               |



