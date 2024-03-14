---
description: This is a step by step guide of how to make verified DDO deals with Boost
---

# How to onboard data using DDO deals

1.  First, you need to initialise a new Boost client and also set the endpoint for a public Filecoin node. In this example we are using [https://glif.io](https://glif.io/)\


    ```
    export FULLNODE_API_INFO=https://api.node.glif.io

    boost init
    ```


2.  The `init` command will output your new wallet address, and warn you that the market actor is not initialised.



    ```
    boost init

    boost/init_cmd.go:53    default wallet set      {"wallet": "f3wfbcudimjcqtfztfhoskgls5gmkfx3kb2ubpycgo7a2ru77temduoj2ottwzlxbrbzm4jycrtu45deawbluq"}
    boost/init_cmd.go:60    wallet balance  {"value": "0"}
    boost/init_cmd.go:65    market actor is not initialised, you must add funds to it in order to send online deals
    ```


3.  Now, you need to send some funds and Datacap to the wallet.


4.  You can confirm that the market actor has funds and Datacap by running `boost wallet list`.

    After that you need to generate a `car` file for data you want to store on Filecoin, and note down its `payload-cid.` We recommend using [`go-car`](https://github.com/ipld/go-car/releases/latest) CLI to generate the car file.



    ```
    boostx generate-rand-car -c=50 -l=$links -s=5120000 .
    Payload CID: bafykbzacedr7avw5yvxgjftkhfgzgbinq523csw3ir5hyukx2ulewaigyjdrm, written to: <$CWD>/bafykbzacedr7avw5yvxgjftkhfgzgbinq523csw3ir5hyukx2ulewaigyjdrm.car
    ```


5.  Then you need to calculate the `commp` and `piece size` for the generated `car` file:\
    boostx generate-rand-car -c=50 -l=$links -s=5120000 .\


    ```
    boostx commp bafykbzacedr7avw5yvxgjftkhfgzgbinq523csw3ir5hyukx2ulewaigyjdrm.car

    CommP CID:  baga6ea4seaqjpldhlgodxw2vjj6g46xra7jthe2g37kt7577ep5euxipkupfsly
    Piece size:  8388608
    Car file size:  7657847
    ```


6.  Create a new verified allocation for this piece using `boost` client. You can use other method to create allocations as long as piece details match the generated commP.\


    ```
    boost allocate --miner=t01013 --piece-info=baga6ea4seaqjpldhlgodxw2vjj6g46xra7jthe2g37kt7577ep5euxipkupfsly=8388608 --wallet t3tejq3lb3szsq7spvttqohsfpsju2jof2dbive2qujgz2idqaj2etuolzgbmro3owsmpuebmoghwxgt6ricvq

    about to send message with the following gas costs
    max fee:      0.00000000512550864 FIL (absolute maximum amount you are willing to pay to get your transaction confirmed)
    gas fee cap:  0.00000000000000012 FIL
    gas limit:    42712572
    gas premium:  0.000000000000000023 FIL
    basefee:      0.0000000000000001 FIL

    Proceed? Yes [y] / No [n]:
    y
    2024-03-11T18:11:59.794Z	INFO	boost	boost/direct_deal.go:112	submitted data cap allocation message	{"cid": "bafy2bzacecyxmx4uyuqfy6xdnlaba2aamlghcujt2asvqmkz6trilnttcouoi"}
    2024-03-11T18:11:59.794Z	INFO	boost	boost/direct_deal.go:113	waiting for message to be included in a block

    AllocationID  Client  Miner  PieceCid                                                          PieceSize  TermMin  TermMax Expiration  
    31825         1011    1013   baga6ea4seaqjpldhlgodxw2vjj6g46xra7jthe2g37kt7577ep5euxipkupfsly  8388608    518400   5256000  1601277
    ```


7.  Import the piece for the newly create allocation using `boostd` \


    ```
    boostd import-direct --client-addr=t3tejq3lb3szsq7spvttqohsfpsju2jof2dbive2qujgz2idqaj2etuolzgbmro3owsmpuebmoghwxgt6ricvq --allocation-id=31825 baga6ea4seaqjpldhlgodxw2vjj6g46xra7jthe2g37kt7577ep5euxipkupfsly ~/bafykbzacedr7avw5yvxgjftkhfgzgbinq523csw3ir5hyukx2ulewaigyjdrm.car

    Direct data import scheduled for execution
    ```


8. Watch the `boostd` UI to verify that the new DDO deal reaches "Complete" and "Claim Verified" state.
