# Home Media Server 
`docker-compose.yml` for a home media server stack:
transmission (+ openvpn), jackett, radarr, sonarr, lidarr, calibre,
calibre-web, plex
##  Folder structure
```
 /
 ├── appdata 
 │    ├── calibre
      ├── calibre-web
      ├── jackett
      ├── lidarr
      ├── plex
      ├── radarr
      ├── sonarr
      └── transmission
 │
 ├── downloads
      ├── complete
           ├── books
           ├── movies
           ├── music
           └── tv
      └── incomplete
└── media              
     ├── books
     ├── movies
     ├── music
     └── tv
```
## Programs and their use
Images                   | Use
-------------------------|-----------------
transmission (+ openvpn) | downloads
readarr (*)              | books
calibre                  | books
radarr                   | movies         
lidarr (**)              | music
sonarr                   | tv
plex                     | movies music tv
soulseek                 | downloads

To download books/music I prefer the following. I suspect private/usenet
trackers are best for books/music but I stick to public torrent trackers. 
```
(*)  Genesis library
(**) SoulseekQt
```

# Launch
copypaste `docker-compose*.yml` to portainer 

config and add `home-media-server*.env` to portainer

launch stack

# Config
Config through web by going to `http://0.0.0.0:{port}/`
Port                   | Application
-------------------------|-----------------
8989       | sonarr
9117       | jackett
8686       | lidarr
8083       | calibre-web
8080       | calibre
8081       | calibre
7880       | radarr 
32400      | plex
6080       | soulseek

## Jackett
Add some trackers

## Sonarr, radarr, lidarr
Add indexer jackett 
```
API key jackett
torznab stream
    0.0.0.0 -> localhost
    http://localhost:9117/torznab/all/
```
Add download client
```
Transmission client
    Add category {tv|music|movies|books}
```
## Calibre, calibre-web
default login calibre: `abc abc`
default login calibre-web: `admin admin123`

create library through calibre

setup Kobo sync with calibre-web
```
Enable Kobo sync checkbox in the Basic configuration >> Feature
Configuration page. If you are accessing your Calibre-Web instance from
your Kobo via some sort of portforwarding mechanism (e.g. your router maps
port X to machine Y on port Z), please change the External Port setting to
port X in this example (the external port). This necessary due to a bug in
HTTP-requests the Kobo reader creates and can't be avoided.
```

plugin kobo ereader and config
```
The Kobo eReader.conf file found under the .kobo/Kobo directory on Kobo devices is used to configure which URL the device uses for syncing books.
By default, the config file contains the following row:

api_endpoint=https://storeapi.kobo.com

Users can generate a URL to sync with Calibre-Web instead by clicking the
Create/View button under their Calibre-Web profile page.
```

## Soulseek
setup share folder(s)

# Credits
inspirted by Youtube tutorial of a similar setup [Easy Automated Home Media Server: VPN, Radarr, Sonarr, Lidarr, Librarian in 10 Minutes.](https://www.youtube.com/watch?v=5rtGBwBuzQE)