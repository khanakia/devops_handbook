# Allow Remote Connection

**Note:** You need to first add a password before enable remote connection.

### Change Password

```
sudo -u postgres psql
ALTER USER postgres PASSWORD 'qj4bOd15sEQ9PSCEcBmc';
```

### Test Connection using Password

```
PGPASSWORD=qj4bOd15sEQ9PSCEcBmc psql -U postgres -p 5432
```

### Allow Remote Connection

* Edit this file `/etc/postgresql/14/main/postgresql.conf`
* Add this code starting from Line 60: `listen_addresses='*'`
*   Edit this file `/etc/postgresql/14/main/pg_hba.conf` and replace the code with this.

    ```
    # Database administrative login by Unix domain socket
    local   all             postgres                                md5

    # TYPE  DATABASE        USER            ADDRESS                 METHOD

    # "local" is for Unix domain socket connections only
    local   all             all                                     md5
    # IPv4 local connections:
    host    all             all             0.0.0.0/0             md5
    # IPv6 local connections:
    host    all             all             ::0/0                 md5
    # Allow replication connections from localhost, by a user with the
    # replication privilege.
    local   replication     all                                     md5
    host    replication     all             127.0.0.1/32            md5
    host    replication     all             ::1/128                 md5
    ```


* Restart the PostgresSQL Server `sudo systemctl restart postgresql`

####

