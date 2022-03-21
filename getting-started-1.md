# Boost Database

Boost stores metadata about deals in a sqlite database in the root directory of the Boost repo.

To open the database use a sqlite client:

`sqlite3 boost.db`

Boost keeps a separate database just for deal logs, so as to make it easier to manage log data separately from deal metadata. The logs database is named `boost.logs.db`
