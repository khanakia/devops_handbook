# Postgres 12 to 13 Migration

If we need to upgrade a DB in use to a major Postgres version the general approach is to:

* Install the desired PG version&#x20;
  * If we need to upgrade the TSDB (Timescale) version as well, we generally need to update Postgres first, then TSDB. Check their docs for reference.
* Configure Postgres of new version
* Migrate data over by running `pg_dumpall` of existing version and `pg_restore` to new version
  * Check storage. You’ll temporarily 2x the storage in use, so it’s possible you need to increase storage. If Ubuntu you’ll need to also expand the disk volume and extend its file system. Note its specific filesystem type as instructions vary depending on it.
* Validate its success
* Turn off old Postgres
* Update Postgres of new version to use port 5432 and accept traffic
* Delete old Postgres and validate that backups continue functioning as expected.

**This approach has minimal downtime (\~2m for 5GB, around 50M records).**

#### 1. **INSTALL**

```
## Installation
https://www.postgresql.org/download/linux/ubuntu/

## Verify Install
pg_lsclusters
```

#### 2. Configs

```
## Copy settings from postgresql.conf  from existing server to new. 
sudo vim /etc/postgresql/12/main/postgresql.conf
sudo vim /etc/postgresql/13/main/postgresql.conf
sudo service postgresql restart
```

```
## Change method in local connections to “trust” in both versions (Temporary) 
sudo vim /etc/postgresql/12/main/pg_hba.conf
sudo vim /etc/postgresql/13/main/pg_hba.conf
sudo service postgresql restart
```

**MIGRATE DATA**

```
sudo su - postgres	
pg_dumpall -p 5432 | psql -d postgres -p 5433
You can ignore the warnings and errors about empty hypertables
```

**VALIDATE**

```
psql -U postgres -p 5433
\c demo
\d
Query for data, check systems doing any write/read operations against this
```

#### **Final Configs**

```
sudo service postgresql stop old postgres
sudo vim /etc/postgresql/13/main/postgresql.conf
set the port from 5433 -> 5432
sudo service postgresql@13-main restart
```

#### **Validate Again**

```
psql -U postgres : is it your new PG version?
\c demo
\d 
Query for data, check systems doing any write/read operations against this
```

Delete old postgres and its data

**Notes: **

If restart/stop/start postgres services and you experience this: “psql: FATAL: Peer authentication failed for user “postgres” (or any user)”?** **[**https://gist.github.com/AtulKsol/4470d377b448e56468baef85af7fd614**](https://gist.github.com/AtulKsol/4470d377b448e56468baef85af7fd614)

References&#x20;

https://pgdash.io/blog/postgres-13-getting-started.html https://www.kostolansky.sk/posts/upgrading-to-postgresql-12/ https://www.postgresql.org/docs/13/upgrading.html https://stackoverflow.com/questions/65092546/postgresql-invalid-data-directory-cant-open-pid-file-var-run-postgresql-10 https://stackoverflow.com/questions/31645550/postgresql-why-psql-cant-connect-to-server https://docs.timescale.com/timescaledb/latest/how-to-guides/update-timescaledb/#update-timescaledb https://aws.amazon.com/premiumsupport/knowledge-center/expand-root-ebs-linux/ Postgresql: Upgrading postgres with pg\_upgrade

