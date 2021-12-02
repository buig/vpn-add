# Configuration with docker-compose
Copied from https://github.com/kylemanna/docker-openvpn/blob/master/docs/docker-compose.md

# Quick Start with docker-compose

* Add a new service in docker-compose.yml

```yaml
version: '2'
services:
  openvpn:
    cap_add:
     - NET_ADMIN
    image: kylemanna/openvpn
    container_name: openvpn
    ports:
     - "1194:1194/udp"
    restart: always
    volumes:
     - ./openvpn-data/conf:/etc/openvpn
```


* Initialize the configuration files and certificates

```bash
docker-compose run --rm openvpn ovpn_genconfig -u udp://vpn.add.local
docker-compose run --rm openvpn ovpn_initpki
```

* Fix ownership (depending on how to handle your backups, this may not be needed)

```bash
sudo chown -R $(whoami): ./openvpn-data
```

* Start OpenVPN server process

```bash
docker-compose up -d openvpn
```

* You can access the container logs with

```bash
docker-compose logs -f
```

* Generate a client certificate

```bash
# with a passphrase (recommended)
docker-compose run --rm openvpn easyrsa build-client-full your_client_name
# without a passphrase (not recommended)
docker-compose run --rm openvpn easyrsa build-client-full your_client_name nopass
```

* Retrieve the client configuration with embedded certificates

```bash
docker-compose run --rm openvpn ovpn_getclient your_client_name > your_client_name.ovpn
```

* Revoke a client certificate

```bash
# Keep the corresponding crt, key and req files.
docker-compose run --rm openvpn ovpn_revokeclient your_client_name
# Remove the corresponding crt, key and req files.
docker-compose run --rm openvpn ovpn_revokeclient your_client_name remove
```

## Debugging Tips

* Create an environment variable with the name DEBUG and value of 1 to enable debug output (using "docker -e").

```bash
docker-compose run -e DEBUG=1 -p 1194:1194/udp openvpn
```
