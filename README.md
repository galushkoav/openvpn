# openvpn with docker-compose

# build docker

# Clone repo
```bash
mkdir -p /docker-compose
cd /docker-compose
git clone https://github.com/galushkoav/openvpn.git
cd openvpn
```

# build

```bash
docker-compose build
```

## Init service

```bash
mkdir -p configs/openvpn/conf
docker-compose run --rm openvpn ovpn_genconfig -u udp://openvpn.itc-life.ru
docker-compose run --rm openvpn ovpn_initpki nopass
```

# Start service

```bash
docker-compose up -d openvpn
```

# Configure for static ip address's

### add lines to openvpn.conf

```bash
nano configs/openvpn/conf/openvpn.conf 
```

```bash
# Client config directory
client-config-dir /etc/openvpn/ccd
ifconfig-pool-persist /etc/openvpn/ipp.list
```

# Generate,delete and list users for openvpn

## Gen cert
```bash
expor```basht CLIENTNAME="your_client_name"

docker-compose run --rm openvpn easyrsa build-client-full $CLIENTNAME nopass

docker-compose run --rm openvpn ovpn_getclient $CLIENTNAME > $CLIENTNAME.ovpn
```

## Delete cert

```bash
docker-compose run --rm openvpn ovpn_revokeclient $CLIENTNAME remove
```

## List users

```bash
docker-compose run --rm openvpn ovpn_listclients
```

# Create supervisor for openvpn

## Install supervisor
```bash
apt-get install supervisor
```


```bash
[supervisord]
nodaemon=true
environment=HOME="/usr/bin/"
[program:openvpn]
command=/usr/sbin/openvpn --config  /home/user/user1@itc-life.ru.ovpn
autostart=true
autorestart=true
startretries=3
stderr_logfile=/var/log/openvpn.err
stdout_logfile=/var/log/openvpn.log
[include]
files = /etc/supervisor/conf.d/*.conf
```

## Update supervisor config
```bash
suspervisorctl reread && supervisorctl update
```