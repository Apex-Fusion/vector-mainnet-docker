# Apex vector public testnet relay and tooling

This docker compose file is starting following containers:

* vector-relay (standalone, prerequisite for dbsync and wallet-api)
* ogmios (requires vector-relay)
* postgres (standalone, prerequisite for dbsync and blockfrost)
* dbsync (requires vector-relay and postgres)
* blockfrost (requires postgres and consequently dbsync to keep it up to date)
* wallet-api (requires vector-relay)
* icarus (requires wallet-api)

The docker compose file is envisioned as example of available tooling and will start all of them in sequence.
Feel free to exclude/modify listed services as per your requirements, following the dependency comments.

For detals consult the docker compose file but at the time of writing, the followinge versions apply:

| Component    | Version      | Docker registry                      |
|--------------|--------------|--------------------------------------|
| vector-relay |       10.4.1 | ghcr.io/intersectmbo/cardano-node    |
| ogmios       |       v6.8.0 | cardanosolutions/ogmios              |
| postgres     | 14.10-alpine | postgres                             |
| dbsync       |     13.5.0.2 | ghcr.io/intersectmbo/cardano-db-sync |
| blockfrost   |       v1.7.0 | blockfrost/backend-ryo               |
| wallet-api   |   2023.12.18 | cardanofoundation/cardano-wallet     |
| icarus       |  v2023-04-14 | piotrstachyra/icarus                 |


## Prerequisites:

* Intel or AMD based linux system (this was tested on)
* docker with compose
* network


## Start procedure

Run:

```
docker compose up -d
```


## Apex node relay

This is a relay node connected to a running `vector-public-testnet` network. All `cardano-cli` commands apply as usual. For example:

To check the tip (at the moment it is about 10 min to sync, will definitely vary over time):

```
docker exec -it vector-public-testnet-tools-10_4_1-vector-relay-1 cardano-cli query tip --testnet-magic 3311 --socket-path /ipc/node.socket
```


## Ogmios API

For ogmios api consult the [online documentation](https://ogmios.dev/api/v5.6/).
To check ogmios http api point a browser to `localhost` port `1733`, for example:

```
http://localhost:1733/
```


## DbSync

DbSync is indexer created as ETL tool coprised of three components:

* running node relay (to track blockchain state)
* dbsync etl tool (to react to block events, parse them and store them to postgres database)
* postgres database

Credentials to access the postgres database are in `secrets/dbsync/` folder.


## Blockfrost

Blockfrost is an instant, highly optimized and accessible API as a Service that serves as an alternative access
to the Cardano blockchain and related networks, with extra features.

For blockfrost api consult the [online documentation](https://docs.blockfrost.io/).
To check the blockfrost point a browser to `localhost` port `3033`, for example:

```
http://localhost:3033
http://localhost:3033/epochs/latest
```

## Wallet API and Icarus

Wallet API provides an HTTP Application Programming Interface (API) and command-line interface (CLI) for
working with wallets. It also featuers a lightweight frontend web interface called Icarus.

For wallet-api consult the [online documentation](https://cardano-foundation.github.io/cardano-wallet/api/edge/).
To check the wallet-api point a browser to `localhost` port `8290`, for example:

```
http://localhost:8290/v2/network/information
http://localhost:8290/v2/network/clock
```

To check the icarus wallet-api ui point a browser to `localhost` port `4477` and then click `Connect` button, for example:

```
http://localhost:4477/
http://localhost:4477/network-info
```

Note that if you are also running a vector testnet compose setup on the same machine you can connect icarus 
from either vector or vector setup on either wallet-api by targetting the desired one. Default ports for them are
8190 for vector testnet and 8090 for vector testnet.


## Remove procedure

To remove containers and volumes, images will be left for fast restart:

```
docker compose down
docker volume rm \
  vector-public-testnet-tools-10_4_1_db-sync-data \
  vector-public-testnet-tools-10_4_1_node-db \
  vector-public-testnet-tools-10_4_1_node-ipc \
  vector-public-testnet-tools-10_4_1_postgres \
  vector-public-testnet-tools-10_4_1_wallet-api-data
```