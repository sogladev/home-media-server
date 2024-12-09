# Home Media Server
`docker-compose.yml` setup for a comprehensive home media server stack. Seamlessly manage and access your content, including movies, TV shows, music, and eBooks + Kobo eReader sync. Featuring apps like Plex, Transmission, Prowlarr, Radarr, Sonarr, Lidarr, Calibre, and Filebrowser, this server empowers you to enjoy and organize your media collection with ease.

You have two configuration options:
1. Without VPN (`*-novpn.yml`): For straightforward local use.
1. With VPN (`*-vpn.yml`): Enhances privacy and secures your P2P traffic via OpenVPN

Optional Stacks:
* Traefik: Simplifies service management by providing a reverse proxy with easy-to-configure, accessible URLs for all services.
* Organizr: Acts as a unified front-end dashboard to access and organize all your services in one convenient place.

## VPN or no VPN
Choosing whether to use a VPN is entirely up to you. Here’s the key difference:
- **Without VPN**: Your P2P and Soulseek traffic will route directly through your internet connection, which is simple to set up but offers no additional privacy.
- **With VPN**: Routes all P2P and Soulseek traffic through OpenVPN, providing enhanced privacy, encryption, and security. This is entirely **separate** from your normal internet traffic. This setup is highly recommended if you value anonymity

Choosing the VPN setup requires a few additional steps. Here’s what you need:

1. **A VPN with P2P Support**: Critical for torrenting and file-sharing services.
2. **OpenVPN Configuration Files**: Obtain these from your VPN provider.
3. **Credentials**: Login details. These depend on your VPN provider

If you already have a VPN that supports P2P, you can skip this paragraph. If not, consider a reliable provider. I recommend **ProtonVPN** (tested and pre-configured with this setup) or alternatives like **Surfshark** and **Windscribe**.


## Services and their use
| Images                   | Use                                 |
| ------------------------ | ----------------------------------- |
| transmission (+ openvpn) | downloads                           |
| prowlarr                 | indexer manager                     |
| calibre                  | books library manager               |
| calibre-web              | books library to enable Kobo Sync   |
| openbooks (*)            | books finder                        |
| radarr                   | movies                              |
| lidarr                   | music                               |
| sonarr                   | tv                                  |
| plex                     | movies music tv                     |
| soulseek                 | music downloads                     |
| filebrowser              | local filesharing                   |
| organizr                 | all your services from a single tab |

```
(*) Alternative:  Library Genesis (Libgen)
```
##  Folder structure
```
 /
 ├── appdata
      ├── calibre
      ├── calibre-web
      ├── filebrowser
      ├── lidarr
      ├── openbooks
      ├── organizr
      ├── plex
      ├── prowlarr
      ├── radarr
      ├── sonarr
      ├── soulseek
      └── transmission
 ├── downloads
      ├── complete
           ├── books
           ├── movies
           ├── music
           └── tv
      ├── soulseekqt
      └── incomplete
└── media
     ├── books
     ├── movies
     ├── music
     └── tv
```

# Deploying the stack

## Using portainer
copypaste `docker-compose*.yml` to portainer

config and add `home-media-server*.env` to portainer

launch stack

## Docker desktop
Make sure file sharing is enabled for the directories you are mapping.

```
setup settings -> resources -> file sharing
```

Then you can setup a folder so that:
```
.
├── docker-compose.yml
└── stack.env
```
then run
```
docker compose up
```

# Config
After launching for the first time, some configuration is required. Always refer to the official docs. Some pointers are listed per application

Config through web by going to `http://0.0.0.0:{port}/`

Port       | Application     | Domain
 ----------|-----------------|--------------------------------
8989       | sonarr          | sonarr.domain.duckdns.org
7880       | radarr          | radarr.domain.duckdns.org
9696       | prowlarr        | prowlarr.domain.duckdns.org
8686       | lidarr          | lidarr.domain.duckdns.org
8083       | calibre-web     | calibre-web.domain.duckdns.org
8092       | calibre http    |
8093       | calibre https   | calibre.domain.duckdns.org
32400      | plex            | plex.domain.duckdns.org
6080       | soulseek        | soulseek.domain.duckdns.org
8090       | filebrowser     | filebrowser.domain.duckdns.org
8099       | openbooks       | openbooks.domain.duckdns.org
9983       | organizr        | organizr.domain.duckdns.org

## Transmission
Use environment variables to configure

refer to: https://github.com/transmission/transmission/blob/main/docs/Editing-Configuration-Files.md

for example some bandwidth envs are setup by default:
```
Bandwidth

    speed-limit-down: Number (KB/s, default = 100)
    speed-limit-down-enabled: Boolean (default = false)
    speed-limit-up: Number (KB/s, default = 100)
    speed-limit-up-enabled: Boolean (default = false)
    upload-slots-per-torrent: Number (default = 14)
```

### VPN Configuration
If using ProtonVPN, you can skip the OpenVPN configuration files and you only need to obtain your credentials. These are obtained through the web interface under sidebar option `Account` -> `OpenVPN / IKEv2 username and password`. These will be your
`OPENVPN_USERNAME` and `OPENVPN_PASSWORD` respectively. Direct link to ProtonVPN docs http://haugene.github.io/docker-transmission-openvpn/provider-specific/#protonvpn

For other providers, the docs can be found at http://haugene.github.io/docker-transmission-openvpn/

## Prowlarr
https://docs.prowler.com/projects/prowler-open-source/en/latest/

## Sonarr, radarr, lidarr
These services require you to setup an indexer (prowlarr) and a download client (transmission)

1. Add indexers to apps
copy the API key from the app then add the app in the Prowlarr web interface

2. Add download client
```
Transmission client
    Add category {tv|music|movies|books}
```
## Calibre, calibre-web
default login calibre: `abc abc`
default login calibre-web: `admin admin123`

Use calibre to add books, calibre-web to setup a store endpoint to enable Kobo Sync

### Setup eReader with Kobo sync and calibre-web
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

## Filebrowser

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

## Soulseek
setup Share folder(s) by clicking "Share Folder" at the very top of interface

## Openbooks
documentation: https://evan-buss.github.io/openbooks/setup/docker/

Books are downloaded through the browser. They need to be manually added to calibre's library:
- Move downloaded file to `/books_add_me` folder. This is mapped in the compose file (`.yml`) file
- open calibre and click the `Add books` button. This copies the file to the library.
- The downloaded file can be removed

The library will automaticly synchronize with `calibre web` and can then be synced to your ereader. Periodic syncing should be enabled by default on your ereader.

## Organizr
documentation: https://docs.organizr.app/
https://github.com/causefx/Organizr

# Credits
Inspired by Youtube tutorial of a similar setup [Easy Automated Home Media Server: VPN, Radarr, Sonarr, Lidarr, Librarian in 10 Minutes.](https://www.youtube.com/watch?v=5rtGBwBuzQE)

The traefik reverse proxy setup is adapted from "Traefik 3 Docker Certificates Guide" by [TechnoTim](https://github.com/timothystewart6), originally published at https://technotim.live/posts/traefik-3-docker-certificates/, and licensed under [CC BY 4.0](https://creativecommons.org/licenses/by/4.0/). Changes were made to customize the configuration for use with DuckDNS instead of Cloudflare and routing services.

