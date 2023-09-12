---
description: Direct data onboarding deals
---

# Direct Deals

## Set up a devnet to make Direct Deals

### Create images

#### Create Lotus image:

```
git clone https://github.com/filecoin-project/lotus lotus-direct-deal
cd lotus-direct-deal
git checkout nonsense/ddo-integration-and-allocation-and-claim-apis
docker build --build-arg FFI_BUILD_FROM_SOURCE=1 --build-arg GOFLAGS=-tags=debug -t filecoin/lotus-all-in-one:feat-ddo-integration-debug --target lotus-all-in-one -f Dockerfile .
```

#### Create Boost images:

```
git clone https://github.com/filecoin-project/boost boost-direct-deal
cd boost-direct-deal
git checkout nonsense/ddo
make docker/lotus ffi_from_source=1 build_lotus=0 lotus_version=feat-ddo-integration
make docker/lotus-miner ffi_from_source=1 build_lotus=0 lotus_version=feat-ddo-integration
make docker/boost docker/booster-http docker/booster-bitswap ffi_from_source=1 build_lotus=0 lotus_version=feat-ddo-integration
```

### Start docker devnet

```
cd docker/devnet
docker-compose up -d
```

### Setup notaries and grant datacap

1.  Open a terminal to the `boost` container\


    ```
    docker compose exec boost /bin/bash
    ```
2.  Setup notary and add balance to client market actor\


    ```
    export `lotus auth api-info --perm=admin`
    boost init
    lotus send --from=`lotus wallet default` `boost wallet default` 100
    NOTARY_1=`cat $BOOST_PATH/notary_1`
    lotus send $NOTARY_1 10
    lotus wallet list
    sleep 10
    boostx market-add 5
    lotus filplus grant-datacap --from=$NOTARY_1 `boost wallet default` 100000000
    ```
3.  Grant the datacap to the client\


    {% code overflow="wrap" %}
    ```
    lotus filplus grant-datacap --from=$NOTARY_1 `boost wallet default` 100000000
    ```
    {% endcode %}

## Make a deal

```
chunks=50
links=10
FILE=`boostx generate-rand-car -c=$chunks -l=$links -s=5120000 /app/public/ | awk '{print $NF}'`
PAYLOAD_CID=$(find "$FILE" | xargs -I{} basename {} | sed 's/\.car//')
COMMP_CID=`boostx commp $FILE 2> /dev/null | grep CID | cut -d: -f2 | xargs`
echo $PAYLOAD_CID
echo $COMMP_CID
boost allocate --miner=t01000 --piece-info=$COMMP_CID=8388608
boost list-allocations
boostd import-direct --client-addr=`boost wallet default` --allocation-id=2 $COMMP_CID /app/public/$PAYLOAD_CID.car
```
