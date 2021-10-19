# Golang

### Install Golang in Centos 7

```
wget https://golang.org/dl/go1.16.5.linux-amd64.tar.gz
rm -rf /usr/local/go && tar -C /usr/local -xzf go1.16.5.linux-amd64.tar.gz
vi /etc/profile

// Add this line to the end and go will be availble for all the users
export PATH=$PATH:/usr/local/go/bin
```

### go run . error if you run as user other than root

`bash-4.2$ go run . fork/exec /tmp/go-build2346563638/b001/exe/jgo: permission denied`

```
vi ~/.bash_profile
alias go='TMPDIR=~/tmp go'
:wq
source ~/.bash_profile
```

### Point domain app.luci.com to localhost:7011 with WHM/Cpanel

```
mkdir -p /etc/apache2/conf.d/userdata/ssl/2_4/luci/app.luci.com 
cd /etc/apache2/conf.d/userdata/ssl/2_4/luci/app.luci.com 
vi custom.conf
```

#### Add below code to the custom.conf

```
ProxyPassMatch    "/api/(.*)" "http://127.0.0.1:7011/$1"
ProxyPassReverse  "/api/(.*)" "http://127.0.0.1:7011/$1"
ProxyPreserveHost on

<Proxy *>
  Order deny,allow
  Allow from all
</Proxy>
```

#### Reload the host file and restart the http server after you made changes to the custom.conf

```
/scripts/ensure_vhost_includes --all-users
service httpd configtest
service httpd restart
```

### Run go as servcie

`vi /etc/systemd/system/analyzifygo.service`

```
[Unit]
Description=Analyzify Go Service
ConditionPathExists=/home/luci/luci_go
After=network.target

[Service]
Type=simple
User=luci
Group=luci


WorkingDirectory=/home/luci/luci_go
ExecStart=/usr/local/go/bin/go run . server

Restart=on-failure
RestartSec=10

# make sure log directory exists and owned by syslog
# PermissionsStartOnly=true
#ExecStartPre=/bin/mkdir -p /home/luci/logs/lucigoservice
#ExecStartPre=/bin/touch /home/luci/logs/lucigoservice/output.log
#StandardOutput=file:/home/luci/logs/lucigoservice/output.log
#StandardError=file:/home/luci/logs/lucigoservice/error.log

StandardOutput=syslog
StandardError=syslog
SyslogIdentifier=lucigoservice

[Install]
WantedBy=multi-user.target
```

```
systemctl daemon-reload 
systemctl start lucigo.service 
systemctl enable lucigo.service
systemctl daemon-reload 
systemctl restart lucigo.service 
systemctl status lucigo.service
```

### Config rsyslog (System Logging Service)

#### Create directory for log file

`mkdir /var/log/lucigoservice`

Note: in Centos we do not need to set the user owner to 'syslog' as centos does not have any syslog user. So it operates as `root` so the log file will be created even the owner of the log directory is root

#### Then add config file /etc/rsyslog.d/analyzifygo.conf

```
if $programname == 'lucigoservice' then /var/log/lucigoservice/output.log
& stop
```

```
\\ Restart rsyslog
systemctl restart rsyslog.service
```

