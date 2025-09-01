# Home Media Server

`docker-compose.yml` setup for a comprehensive home media server stack. Seamlessly manage and access your content, including movies, TV shows, music, web links, rss feeds and eBooks + Kobo eReader sync. Featuring apps like Plex/Jellyfin, Overseerr/Jellyseerr, Transmission, Prowlarr, Radarr, Sonarr, Lidarr, Freshrss, Linkwarden, Calibre, and Filebrowser, this server empowers you to enjoy and organize your media collection with ease.

You have two configuration options:

1. Without VPN (`*-novpn.yml`): For straightforward local use.
1. (deprecated, see vpn branch) With VPN (`*-vpn.yml`): Enhances privacy and secures your P2P traffic via OpenVPN

Optional Stacks:

- Traefik: Simplifies service management by providing a reverse proxy with easy-to-configure, accessible URLs for all services.

## Services and their use

| Service                                                 | Use                           |
| ------------------------------------------------------- | ----------------------------- |
| [bazarr](https://www.bazarr.media/)                     | Subtitle management           |
| [calibre](https://calibre-ebook.com/)                   | Ebook management              |
| [calibre-web](https://github.com/janeczku/calibre-web)  | Web interface for Calibre     |
| [filebrowser](https://filebrowser.org/)                 | File management               |
| [freshrss](https://freshrss.org/)                       | RSS feed reader               |
| [jellyfin](https://jellyfin.org/)                       | Media server                  |
| [jellyseerr](https://github.com/Fallenbagel/jellyseerr) | Media request management      |
| [lidarr](https://lidarr.audio/)                         | Music collection manager      |
| [linkwarden](https://github.com/linkwarden/linkwarden)  | Bookmark manager              |
| [openbooks](https://github.com/evan-buss/openbooks)     | Book searcher (\*)            |
| [organizr](https://github.com/causefx/Organizr)         | Unified web interface         |
| [overseerr](https://overseerr.dev/)                     | Media request management      |
| [plex](https://www.plex.tv/)                            | Media server                  |
| [prowlarr](https://prowlarr.com/)                       | Indexer manager               |
| [radarr](https://radarr.video/)                         | Movie collection manager      |
| [sonarr](https://sonarr.tv/)                            | TV show collection manager    |
| [soularr](https://soularr.net)                          | connects Lidarr with Soulseek |
| [slsk](https://www.slsknet.org/)                        | Soulseek P2P file sharing     |
| [transmission](https://transmissionbt.com/)             | BitTorrent client             |

```
(*) Alternative:  Library Genesis (Libgen)
```

## Folder structure

```
/
├── appdata
     ├── bazarr
     ├── calibre
     ├── calibre-web
     ├── filebrowser
     ├── freshrss
     ├── jackett
     ├── jellyfin
     ├── jellyseerr
     ├── lidarr
     ├── linkwarden
     ├── openbooks
     ├── organizr
     ├── overseerr
     ├── plex
     ├── prowlarr
     ├── radarr
     ├── slskd
     ├── sonarr
     ├── soularr
     ├── soulseek
     ├── traefik
     └── transmission
└── data
     ├── media
     │   ├── books
     │   ├── books_not_in_library
     │   ├── filebrowser
     │   ├── movies
     │   ├── music
     │   └── tv
     ├── soulseek
     │   ├── downloads
     │   └── incomplete
     └── torrents
          ├── incomplete
          ├── movies
          └── tv
```

```sh
mkdir -p appdata/{bazarr,calibre,calibre-web,filebrowser,freshrss,jackett,jellyfin,jellyseerr,lidarr,linkwarden,openbooks,organizr,overseerr,plex,prowlarr,radarr,slskd,sonarr,soularr,soulseek,traefik,transmission} \
data/media/{books,books_not_in_library,filebrowser,movies,music,tv} \
data/soulseek/{downloads,incomplete} \
data/torrents/{incomplete,movies,tv}
```

## Deploying the stack

This section covers deployment options for your home media server stack. You can choose to deploy using Portainer, Docker Compose with Docker Desktop, or Podman. Each method has its own advantages, but for simplicity and ease of use, I recommend using Portainer.

### Using portainer

copypaste `docker-compose*.yml` to portainer

config and add `home-media-server*.env` to portainer

launch stack

### Command line

setup a folder so that:

```
.
├── docker-compose.yml
└── stack.env
```

```sh
$ docker compose --env-file stack.env up --detach
```

## Config

After launching for the first time, some configuration is required. Always refer to the official docs (see [Services and their use](#services-and-their-use)). Some pointers are listed per application.

Config through web by going to `http://0.0.0.0:{port}/`

| Port  | Application     | Domain                          | Domain Short |
| ----- | --------------- | ------------------------------- | ------------ |
| 6767  | bazarr          | bazarr.domain.duckdns.org       | b            |
| 8093  | calibre https   | calibre.domain.duckdns.org      | c            |
| 8092  | calibre http    |                                 |              |
| 8083  | calibre-web     | calibre-web.domain.duckdns.org  | cw           |
| 8090  | filebrowser     | filebrowser.domain.duckdns.org  | f            |
| 9092  | freshrss        | freshrss.domain.duckdns.org     | rss          |
| 8096  | jellyfin        | jellyfin.domain.duckdns.org     | j            |
| 5056  | jellyseerr      | jellyseerr.domain.duckdns.org   | js           |
| 8686  | lidarr          | lidarr.domain.duckdns.org       | l            |
| 9093  | linkwarden      | linkwarden.domain.duckdns.org   | lw           |
| 8099  | openbooks       | openbooks.domain.duckdns.org    | op           |
| 9983  | organizr        | organizr.domain.duckdns.org     | o            |
| 5055  | overseerr       | overseerr.domain.duckdns.org    | ov           |
| 32400 | plex            | plex.domain.duckdns.org         | p            |
| 9696  | prowlarr        | prowlarr.domain.duckdns.org     | pr           |
| 7880  | radarr          | radarr.domain.duckdns.org       | r            |
| 5030  | slsk (soulseek) | soulseek.domain.duckdns.org     | so           |
| 8989  | sonarr          | sonarr.domain.duckdns.org       | s            |
| 9091  | transmission    | transmission.domain.duckdns.org | t            |

### Transmission

mapping:
downloads: `/data/torrents`
incomplete: `/data/torrents/incomplete`

#### non-default web UI

manually install

https://github.com/johman10/flood-for-transmission

1. cd `appdata/transmission`
2. curl / download
3. extract so that `appdata/transmission/flood-for-transmission`

### Prowlarr

https://prowlarr.com/#downloads-v3-docker

instead of localhost, use their service names:

```
http://prowlarr:9696
http://radarr:7878
```

### Sonarr, radarr, lidarr

These services require you to setup an indexer (prowlarr) and a download client (transmission)

1. Add indexers to apps
   copy the API key from the app then add the app in the Prowlarr web interface

2. Add download client

```
Transmission client
    Add category {tv|movies}
```

### Calibre, calibre-web

default login calibre: `abc abc`
default login calibre-web: `admin admin123`

Use calibre to add books, calibre-web to setup a store endpoint to enable Kobo Sync

#### Setup eReader with Kobo sync and calibre-web

documentation: https://github.com/janeczku/calibre-web/wiki/Kobo-Integration
Note that the following is written and tested for a `Kobo Aura H2O` model, steps may be different or this might not work at all for any other models.

Under abc's Profile (default profile)
"Kobo Sync Token" click "create/view token"

This feature might need to be enabled by default

> Enable Kobo sync checkbox in the Basic configuration >> Feature
> Configuration page.

Next connect eReader with a usb-cable to your computer. A single line in a configuration file needs to change to point the store to your home media server.

> The Kobo eReader.conf file found under the .kobo/Kobo directory on Kobo devices is used to configure which URL the device uses for syncing books.
> By default, the config file contains the following row:
>
> api_endpoint=https://storeapi.kobo.com
>
> Users can generate a URL to sync with Calibre-Web instead by clicking the
> Create/View button under their Calibre-Web profile page.

You need to change the ip to your local ip address, for example:

```
api_endpoint=http://192.168.0.205:8083/kobo/0cf8e8ccb456ad872f781f88b2cfdf83
```

#### Troubleshooting

books aren't updating? Test with

```
http://localhost:8083/calibreweb/kobo/{token}/v1/library/sync
```

Go the api_endpoint in browser. If this is empty, consider pressing "Force full kobo sync" in the user options in calibre-web

### Filebrowser

`- /home/${USER}/Applications/Filebrowser:/srv`

Mounts a single folder called Filebrowser to /srv. This folder must be empty.

Other folders are mounted as such: /srv/Pictures
so they are accessible in the container as /Pictures

This will create Pictures/ under Applications. Pictures/ will be empty

filebrowser.db must exist before mounting, else mounts a directory
`touch filebrowser.db`

same with settings.json

```
❯ cat settings.json
{
  "port": 80,
  "baseURL": "",
  "address": "",
  "log": "stdout",
  "database": "/database/filebrowser.db",
  "root": "/srv"
}
```

admin/admin on initial login :8090

### Openbooks

documentation: https://evan-buss.github.io/openbooks/setup/docker/

Books are downloaded through the browser. They need to be manually added to calibre's library:

- Move downloaded file to `/books_add_me` folder. This is mapped in the compose file (`.yml`) file
- open calibre and click the `Add books` button. This copies the file to the library.
- The downloaded file can be removed

The library will automaticly synchronize with `calibre web` and can then be synced to your ereader. Periodic syncing should be enabled by default on your ereader.

### Organizr

documentation: https://docs.organizr.app/
https://github.com/causefx/Organizr

### Slskd

default username and password:
slskd

example config in `slskd/slskd.yml`

## Credits

Inspired by Youtube tutorial of a similar setup [Easy Automated Home Media Server: VPN, Radarr, Sonarr, Lidarr, Librarian in 10 Minutes.](https://www.youtube.com/watch?v=5rtGBwBuzQE)

The traefik reverse proxy setup is adapted from "Traefik 3 Docker Certificates Guide" by [TechnoTim](https://github.com/timothystewart6), originally published at https://technotim.live/posts/traefik-3-docker-certificates/, and licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Changes were made to customize the configuration for use with DuckDNS instead of Cloudflare and routing services.
