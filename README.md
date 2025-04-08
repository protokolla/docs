[protokolla.fi](https://protokolla.fi) - [status](https://status.protokolla.fi/)

Authentication should be mostly done via [[authentik]] and every new service & server should be added to [[status]] page.

## Backups


## Containerization

All the applications should be containerized and ran with Docker as it provides an easy way to migrate hosts in the future. Docker apps are installed under the `/opt/services`-folder on each server and their volume data is also mounted there. If the server has multiple disks, creation of `/data`-style folders is suggested (these disks should be mounted with `nofail` if they can disappear at some point). 

Folder structure:

```
/opt/services
/opt/services/app-name
/opt/services/app-name/docker-compose.yml
/opt/services/app-name/config - docker volume
/opt/services/app-name/data - docker volume
```

## Naming scheme

Servers are named followingly: `countryXX`. XX stands for id number (example of XX: 01, 02, 03...). This setup struggles when a server is deleted from in-between. If you have three servers and delete the second one, will the next server be 02 or 04? This problem will be resolved in the future as the problem arises.

The naming scheme is as simple as possible to support ansible and its [incremental inventory naming](https://docs.ansible.com/ansible/latest/inventory_guide/intro_inventory.html).

```ini
[fi]
fi[01:03].protokolla.fi
```

There is one exception. Proxy servers that allow access to the protokolla.fi intranet should have `proxy` in their name. For example `fi-proxy01`.
## OS & Software

All the servers should run Debian 12 or later version. Proxmox is also okay on virtualization hosts. 

Servers should have only the bare minimum amount of installed software to avoid security holes:
- [UnattendedUpgrades](https://wiki.debian.org/UnattendedUpgrades)
- [Docker](https://get.docker.com/) (if there are containers)