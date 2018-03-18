# media-docker
a short and sweet way to get a full-blown media stack running on a server in minutes. four files and sudo access are all you need to get started.

## what's included
with this package, you'll get a media server environment capable of finding, grabbing, downloading, and presenting: movies, tv, books, music, and comics. it does this (relatively) securely, prioritizing usenet and torrenting-over-VPN.

### services and where to access them
| service | purpose | url / ports |
| ------- | ------- | :---------: |
| plex | movie / tv / music interface | https://plex.${DOMAIN} <br> :32400 |
| ubooquity | book / comic interface | https://ubooquity.${DOMAIN}/ubooquity <br> :8160 <br> :8161 |
| traefik | reverse proxy | https://traefik.${DOMAIN} <br> :80 <br> :443|
| heimdall | dashboard | https://heimdall.${DOMAIN} |
| sabnzbd | nzb download | https://sab.${DOMAIN} |
| transmission | torrent download | https://transmission.${DOMAIN} |
| hydra | nzb searcher | https://hydra.${DOMAIN} |
| jackett | torznab searcher | https://jackett.${DOMAIN} |
| sonarr | tv management | https://sonarr.${DOMAIN} |
| radarr | movie management | https://radarr.${DOMAIN} |
| headphones | music management | https://headphones.${DOMAIN} |
| lazylibrarian | book management | https://lazylibrarian.${DOMAIN} |
| mylar | comic management | https://mylar.${DOMAIN} |
| ombi | plex requests | https://ombi.${DOMAIN} |
| tautulli | plex statistics | https://tautulli.${DOMAIN} |

## installation
installation is omega-easy!

1. have an Ubuntu machine available
2. be root
3. run `sudo git clone https://github.com/joshuhn/media-docker/ /media-docker/ && cd /media-docker/`
4. make sure that the script is executable (`chmod +x .\deploy.sh`) and run `.\deploy.sh`
5. wait a few minutes, watch the color-coded status messages scroll by
6. use your newly configured media stack! hooray!

## what are those?
### deploy.sh
a straightforward shell script that ensures your environment is configured as needed to ensure a solid media server. the process it takes is as follows:

1. perform local server configuration (sudo user setup, timezone)
2. cleanup legacy Docker packages
3. install and enable apt over HTTPS
4. grab and install Docker's official GPG key, add their managed repository to apt
5. install Docker CE & Docker Compose
6. install Cockpit for remote management
7. pre-configure Traefik (set your email & put files in place)
8. build the Docker container environment via docker-compose.yml
9. everything's functional!

all of the customizable bits (users, directories, email) are pulled from the dotenv file. if anything fails, the shell script will tell you in big red text.

if you're feeling saucy and confident that your system is properly configured, you can bypass `.\deploy.sh` and run `docker-compose up --force-recreate -d` on your own.

### docker-compose.yml
this file contains all of the instructions Docker Compose needs to pull, build, and configure the entire media environment. as with `.\deploy.sh`, all user-configurable items are exposed through the dotenv file.

all services are accessed via a Traefik reverse proxy for security. unfortunately, due to the complexity or poor design (or both) of Plex and Ubooquity, they're also able to be reached directly. there's light at the end of that tunnel, though, as the Traefik team are currently working on a method of applying multiple routing labels to a single container. once that's implemented, we'll apply it here to make up for the needlessly many ports those services use.

### .env
a simple dotenv file containing the variables necessary to configure and install all necessary components for the project.

```
# SYSTEM CONFIGURATION
USER_NAME=#username for local sudo-er
PASSWORD=#password for local sudo-er
BASE_DIR=#base path for container configuration storage
MEDIA_DIR=#base path for media library storage
TIMEZONE=#local timezon, i.e. America/Montevideo

# DOCKER CONFIGURATION
DOMAIN=#domain name for the media stack
COMPOSE_VERSION=#version of Docker Compose to install, i.e. 1.20.0-rc2

# VPN CREDENTIALS
VPN_PROVIDER=
VPN_USER=
VPN_PASS=

# TRAEFIK CONFIGURATION
EMAIL_ADDRESS=#email address for let's encrypt
```

### traefik.toml
you don't need to worry about this one, the only thing to change is your email address and `.\deploy.sh` takes care of this for you.

## thanks!
this very much stands on the shoulders of those who came before, all this has done is made the deployment process simple.

this project makes use of the following Docker containers:
- linuxserver\plex
- linuxserver\ubooquity
- linuxserver\sabnzbd
- linuxserver\hydra
- linuxserver\jackett
- linuxserver\sonarr
- linuxserver\radarr
- linuxserver\headphones
- linuxserver\lazylibrarian
- linuxserver\mylar
- linuxserver\heimdall
- linuxserver\ombi
- tautulli\tautulli
- haugene\transmission-openvpn
- haugene\transmission-openbpn-proxy
- traefik
