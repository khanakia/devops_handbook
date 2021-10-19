# Caddy Server

Installation: [https://caddyserver.com/docs/install](https://caddyserver.com/docs/install)

### Ubuntu Installation

```
sudo apt install -y debian-keyring debian-archive-keyring apt-transport-https
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/gpg.key' | sudo tee /etc/apt/trusted.gpg.d/caddy-stable.asc
curl -1sLf 'https://dl.cloudsmith.io/public/caddy/stable/debian.deb.txt' | sudo tee /etc/apt/sources.list.d/caddy-stable.list
sudo apt update
sudo apt install caddy
```

### Caddy Service File Path

In case if you want to customize the service file:

`Created symlink /etc/systemd/system/multi-user.target.wants/caddy.service → /lib/systemd/system/caddy.service.`

### Generate Cloudflare SSL&#x20;

* Go to Cloudflare > SSL/TLS > Origin Server
* Create Certificate and Download the Certificate File and Key File
* Check Host API Section how to use the cert and key file downloaded.

### Host API (Go, Node.js) and Single Page App (react, vue.js)

Config File Path: `/etc/caddy/Caddyfile`

```
:80 {	
    # redirect any request made on 80 to main domain
    # we do not want anybody to access 80 port
    # Note: you can block the 80 port in your firewall settings also
    redir https://luci.com
}

# Go, Node.js API Endpoint
api.luci.com {
    reverse_proxy localhost:2121
    tls /etc/caddy/ssl/cloudflare.cert /etc/caddy/ssl/cloudflare.pem 
}

# React, Vue Application
app.luci.com {
    tls /etc/caddy/ssl/cloudflare.cert /etc/caddy/ssl/cloudflare.pem 

    root * /home/luci/html/react_app/dist
    
    encode gzip

    # redirect all the request to index.html
	try_files {path} index.html

    file_server   
}
```

### Useful Commands

```
sudo systemctl status caddy
sudo systemctl restart caddy
sudo systemctl stop caddy

# print or convert the caddy file to JSON Format config
caddy adapt --config /etc/caddy/Caddyfile
```
