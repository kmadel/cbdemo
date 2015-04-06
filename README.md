# cbdemo

Uses [Docker Compose](https://docs.docker.com/compose/) to build a Jenkins Operations Center high availability environment using Docker containers.

Currently specific to Mac OS X.

Base includes:
- [dnsdock](https://github.com/tonistiigi/dnsdock) providing DNS for docker containers and exposed to Mac OS X 
- HA Jenkins Operation Center proxied via HAProxy
- HA Client Master proxied via HAProxy
- 2 shared slaves

###Instructions
- install [boot2docker](https://github.com/boot2docker/osx-installer/releases/tag/v1.5.0)
- install [Docker Compose](https://docs.docker.com/compose/install/)
- run boot2docker and add `bip` and `dns` docker daemon default options
  - `boot2docker ssh`
  - `vi /var/lib/boot2docker/profile`
  - add the following line and save: `EXTRA_ARGS="-bip=172.17.42.1/24 -dns 172.17.42.1 -dns 8.8.8.8"`
  - exit ssh and restart boot2docker: `boot2docker restart`
  - update the VirtualBox network adapter (vboxnet<x> - number may vary) *Promiscuous Mode* to *Allow All* 
- Route traffic from Mac OS X to boot2docker VM IP: `sudo route -n add -net 172.17.0.0 <BOOT2DOCKER_IP>`
  - BOOT2DOCKER_IP retrieved via `boot2docker ip`
- Configure OS X to use dnsdock DNS by creating the file `/etc/resolver/docker` with content of `nameserver 172.17.42.1`
- clone this repo somewhere under the `/Users` directory
- Update the `docker-compose.yml` file:
  - Update `/Users/kmadel/dev/CloudBees/docker/cbdemo` under dnsdock -> volumes to point to where you cloned this repo. NOTE: You could have several different directories configured for different demos and just change this to point to the demo you want to run.
  - Update the `jocproxy` and `apiteamproxy` services' volumes to point to the respective haproxy subfolders where you clonde this repo
- from your boot2docker shell, run `docker-compose up -d` and after a few minutes (maybe a bit longer) you should have:
  - HA JOC at http://joc.bee.docker (check http://joc.bee.docker:9000 for HAProxy statistics)
  - HA Jenkins Enterprise at http://apiteam.bee.docker
  
