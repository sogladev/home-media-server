# Home Media Server 
`docker-compose.yml` for a home media server stack: transmission (+ openvpn), jackett, radarr, sonarr, lidarr, calibre, calibre-web, plex, filebrowser (local only), soulseekqt 

Configurations:
1. without VPN: `*-novpn.yml` is being used ✅
2. with VPN `*-vpn.yml` is stale and may not work properly

##  Folder structure
```
 /
 ├── appdata 
 │    ├── calibre
      ├── calibre-web
      ├── filebrowser
      ├── jackett
      ├── lidarr
      ├── plex
      ├── radarr
      ├── sonarr
      ├── soulseek
      └── transmission
 │
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
filebrowser              | local filesharing

alternatives for books/music

```
(*)  Genesis library
(**) SoulseekQt
```

# Launch

## Using portainer
copypaste `docker-compose*.yml` to portainer 

config and add `home-media-server*.env` to portainer

launch stack

## Docker desktop 
follow all the stepts incl. apt, group,..

https://docs.docker.com/desktop/install/linux-install/

setup settings -> resources -> file sharing

setup a folder so that:
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
8090       | filebrowser

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
setup share folder(s)

vpn is not tested with soulseek.
potential issues: 

1. stuck on `chown` looping through shared media when PUID,PGID is set. Portainer uses its own *portainer.env without setting PUID,PGID

2. portainer uses 6080:6080. "oVNC might not connect through service:transmission-openvpn


# Credits
inspirted by Youtube tutorial of a similar setup [Easy Automated Home Media Server: VPN, Radarr, Sonarr, Lidarr, Librarian in 10 Minutes.](https://www.youtube.com/watch?v=5rtGBwBuzQE)
