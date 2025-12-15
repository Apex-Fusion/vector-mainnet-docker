# Apex vector relay and tooling repository

This repository contains docker compose setup to launch apex vector network components.

Notes:

* There are multiple versions of tool sets used, see the below table for details.
* Secrets stored in the respository (database access credentials) are for example purposes only and should
*NOT* be used in any real scenario unaltered. They are set only for completeness so that a local setup can
be spun up directly.
* Full set of genesis, topology and configuration files are within each tool version subfolders.
* Details for each of the versions of the tool sets are in separate README.md files in appropriate subfolders.


# Versions of the tool sets

For detals consult the docker compose file but at the time of writing, the followinge versions apply:

|      Network     |    Folder   |  vector-relay  | ogmios | db-sync  |   postgres   | blockfrost | wallet-api |   icarus    |
|------------------|-------------|----------------|--------|----------|--------------|------------|------------|-------------|
| mainnet          | node-10.1.4 |     10.1.4     | v6.8.0 | 13.5.0.2 | 14.10-alpine |   v1.7.0   | 2023.12.18 | v2023-04-14 |
| mainnet          | node-10.1.4 |     10.1.4     | v6.8.0 | 13.5.0.2 | 14.10-alpine |   v1.7.0   | 2023.12.18 | v2023-04-14 |

Docker compose file is starting following containers:

* vector-relay (standalone, prerequisite for dbsync and wallet-api)
* ogmios (requires vector-relay)
* postgres (standalone, prerequisite for dbsync and blockfrost)
* dbsync (requires vector-relay and postgres)
* blockfrost (requires postgres and consequently dbsync to keep it up to date)
* wallet-api (requires vector-relay)
* icarus (requires wallet-api)

The docker compose file is envisioned as example of available tooling and will start all of them in sequence.
Feel free to exclude/modify listed services as per your requirements, following the dependency comments.


## Prerequisites:

* intel based linux system (this was tested on, will most likely work on other platforms as well)
* docker with compose
* network


## Start procedure

Run:

```
docker compose up -d
```


## Apex node relay

This is a relay node connected to a running `mainnet` network. All `cardano-cli` commands apply as usual.

For more details consult the `README.md` file from the tool version folder that compose was started from.


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

Credentials to access the postgres database are in `secrets/dbsync/` folder. They are example credentials
and are *NOT* to be used unaltered in any settings other than a local ephemeral test.


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


## Remove procedure

To remove containers and volumes, consult the `README.md` file in actual tool folder that compose was started from
