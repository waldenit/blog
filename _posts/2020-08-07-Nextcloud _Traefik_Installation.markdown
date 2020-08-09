---
layout: post
title:  "Nextcloud + Traefik Installation"
date:   2020-08-07
categories: jekyll update
---

## 1. Traefik installieren

Installation der Software Komponenten

```bash
sudo apt install curl git python3-pip apache2-utils
```

Docker installieren

```bash
curl -fsSL https://get.docker.com -o get-docker.sh
sh docker.sh
```

Docker-Compose installieren

```bash
pip3 install docker-compose
```

Traefik Verzeichnisse und Dateien anlegen

```bash
mkdir -p /opt/container/traefik
touch /opt/container/traefik/data/acme.json
chmod 600 /opt/containers/traefik/data/acme.json
touch /opt/container/traefik/data/traefik.yml
touch /opt/container/traefik/data/dynamic.yml
```

Dateien anpassen

```bash
nano /opt/container/traefik/data/traefik.yml
```

Folgenden Text einfügen und darauf achten eine Mail Adresse anzugeben

```bash
api:
  dashboard: true

entryPoints:
  http:
    address: ":80"
  https:
    address: ":443"

providers:
  file:
    filename: /opt/container/traefik/data/dynamic.yml
  docker:
    endpoint: "unix:///var/run/docker.sock"
    exposedByDefault: false

certificatesResolvers:
  http:
    acme:
      email: !MAIL EINTRAGEN!
      storage: acme.json
      httpChallenge:
        entryPoint: http
```

```bash
nano /opt/container/traefik/data/dynamic.yml
```

Folgenden Text einfügen

```bash
ttp:
  middlewares:
    secHeaders:
      headers:
        browserXssFilter: true
        contentTypeNosniff: true
        frameDeny: true
        sslRedirect: true
        #HSTS Configuration
        stsIncludeSubdomains: true
        stsPreload: true
        stsSeconds: 31536000
        customFrameOptionsValue: "SAMEORIGIN"

    https-redirect:
      redirectScheme:
        scheme: https
```

Docker-Compose anlegen und anpassen

```bash
nano /opt/container/traefik/docker-compose.yml
```

Folgenden Text einfügen und darauf achten die Host Variablen zu ändern und ein User+Passwort zu vergeben. Den Hash dazu erzeugt ihr mit folgendem Befehl

```bash
echo $(htpasswd -nb EUERUSERNAME EUERPASSWORT) | sed -e s/\\$/\\$\\$/g
```

```bash
ersion: '3'

services:
  traefik:
    image: traefik:latest
    container_name: traefik
    restart: always
    security_opt:
      - no-new-privileges:true
    networks:
      - proxy
    ports:
      - 80:80
      - 443:443
    volumes:
      - /etc/localtime:/etc/localtime:ro
      - /var/run/docker.sock:/var/run/docker.sock:ro
      - ./data/traefik.yml:/traefik.yml:ro
      - ./data/acme.json:/acme.json
    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.traefik.entrypoints=http"
      - "traefik.http.routers.traefik.rule=Host(`HIER.DOMAIN.DE`)"
      - "traefik.http.middlewares.traefik-auth.basicauth.users=USER:PASSWORD"
      - "traefik.http.middlewares.traefik-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.traefik.middlewares=traefik-https-redirect"
      - "traefik.http.routers.traefik-secure.entrypoints=https"
      - "traefik.http.routers.traefik-secure.rule=Host(`HIER.DOMAIN.DE`)"
      - "traefik.http.routers.traefik-secure.middlewares=traefik-auth"
      - "traefik.http.routers.traefik-secure.tls=true"
      - "traefik.http.routers.traefik-secure.tls.certresolver=http"
      - "traefik.http.routers.traefik-secure.service=api@internal"

networks:
  proxy:
    external: true
```

Netzwerk für den Proxy anlegen

```bash
docker network create proxy
```

Traefik starten

```bash
docker-compose -f /opt/container/traefik/docker-compose.yml up -d
```

## 2. Nextcloud installieren

Verzeichnisse anlegen

```bash
mkdir -p /opt/container/nextcloud/app
mkdir /opt/container/nextcloud/database
```

Docker-Compose anlegen

```bash
nano /opt/container/nextcloud/docker-compose.yml
```

Folgenden Text einfügen und MySQL User + PW + Datenbank, sowie die Host Variablen ändern

```bash
version: '3.3'

services:
  nextcloud-db:
    image: mariadb
    container_name: nextcloud-db
    command: --transaction-isolation=READ-COMMITTED --log-bin=ROW
    restart: always
    volumes:
      - /opt/container/nextcloud/database:/var/lib/mysql
      - /etc/localtime:/etc/localtime:ro
      - /etc/timezone:/etc/timezone:ro
    environment:
      - MYSQL_ROOT_PASSWORD=
      - MYSQL_PASSWORD=
      - MYSQL_DATABASE=nextcloud
      - MYSQL_USER=nextcloud
      - MYSQL_INITDB_SKIP_TZINFO=1

  nextcloud-app:
    image: nextcloud
    container_name: nextcloud-app
    restart: always
    depends_on:
      - nextcloud-db
    volumes:
      - /opt/container/nextcloud/app:/var/www/html
    links:
      - nextcloud-db

    labels:
      - "traefik.enable=true"
      - "traefik.http.routers.nextcloud-app.entrypoints=http"
      - "traefik.http.routers.nextcloud-app.rule=Host(`HIER.DOMAIN.DE`)"
      - "traefik.http.middlewares.nextcloud-app-https-redirect.redirectscheme.scheme=https"
      - "traefik.http.routers.nextcloud-app.middlewares=nextcloud-app-https-redirect"
      - "traefik.http.routers.nextcloud-app-secure.entrypoints=https"
      - "traefik.http.routers.nextcloud-app-secure.rule=Host(`HIER.DOMAIN.DE`)"
      - "traefik.http.routers.nextcloud-app-secure.tls=true"
      - "traefik.http.routers.nextcloud-app-secure.tls.certresolver=http"
      - "traefik.http.routers.nextcloud-app-secure.service=nextcloud-app"
      - "traefik.http.services.nextcloud-app.loadbalancer.server.port=80"
      - "traefik.docker.network=proxy"
      - "traefik.http.routers.nextcloud-app-secure.middlewares=nextcloud-dav"
      - "traefik.http.middlewares.nextcloud-dav.replacepathregex.regex=^/.well-known/ca(l|rd)dav"
      - "traefik.http.middlewares.nextcloud-dav.replacepathregex.replacement=/remote.php/dav/"

    networks:
      - proxy
      - default

networks:
  proxy:
    external: true
  default:
    driver: bridge
```

Nextcloud starten

```bash
docker-compose -f /opt/container/nextcloud/docker-compose.yml up -d
```

Berechtigung ändern

```bash
chown -R www-data:www-data /opt/container/nextcloud/app/
```

Netxtcloud Setup durchführen. !WICHTIG! MariaDB anstelle von SQLite auswählen

Datenbank optimieren

```bash
docker exec --user www-data nextcloud-app php occ db:add-missing-indices
docker exec --user www-data nextcloud-app php occ db:convert-filecache-bigint
```

Reverse Proxy konfigurieren. Auslesen der IP Adresse

```bash
docker inspect traefik
```

Eintragen der IP unter 

```bash
nano /opt/container/nextcloud/app/config/config.php
```

```bash
'trusted_proxies' => '172.19.0.4/16'
```

Umstellen auf https. Ändern folgender Zeile

```bash
'overwrite.cli.url' => 'https://HIER.DOMAIN.DE',
```

Hinzufügen folgender Einträge

```bash
'overwriteprotocol' => 'https',
'overwritehost' => 'HIER.DOMAIN.DE',
```

Fertig. Die Container sollten restartet werden.