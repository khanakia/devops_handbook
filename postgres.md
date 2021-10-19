# Postgres

### Useful Commands

**Print Server Version**

```
sudo -u postgres psql -c "SELECT version();"

## Outputs:  PostgreSQL 13.3 on x86_64-pc-linux-gnu, compiled by gcc (GCC) 4.8.5 20150623 (Red Hat 4.8.5-44), 64-bit
```

### Enable UUID for database

if you get this error `ERROR: could not open extension control file "/usr/pgsql-13/share/extension/uuid-ossp.control": No such file or directory` then install contrib package&#x20;

```
sudo yum install postgresql13-contrib
```

#### Enable UUID Extension

```
\c mydb
CREATE EXTENSION "uuid-ossp";
\dx
```

###
