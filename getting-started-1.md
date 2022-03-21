# Boost Database

Boost stores metadata about deals in a sqlite database in the root directory of the Boost repo.

To open the database use a sqlite client:

`sqlite3 boost.db`

The database tables are

* `Deals` metadata about Boost storage deals (eg deal proposal) and their current state (eg checkpoint)
* `FundsLogs` log of each change in funds reserved for a deal
* `FundsTagged` how much FIL is tagged for deal collateral and publish message for a deal
* `StorageLogs` log of each change in storage reserved for a deal
* `StorageTagged` how much storage is tagged for a deal

Boost keeps a separate database just for deal logs, so as to make it easier to manage log data separately from deal metadata. The logs database is named `boost.logs.db` and it has a single table `DealLogs` that stores logs for each deal, indexed by uuid.
