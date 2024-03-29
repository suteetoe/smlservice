traefik.yml
```
## traefik.yml

# Docker configuration backend
providers:
  docker:
    defaultRule: "Host(`{{ trimPrefix `/` .Name }}.docker.localhost`)"

api:
  dashboard: true
  insecure: true
  
```


New Version 2.5

```
version: '3'

services:
  traefik:
    image: traefik:v2.5
    restart: always
    command:
      - "--providers.docker=true"
      - "--entrypoints.web.address=:80"
      - "--api.dashboard=true"
      - "--log.level=DEBUG"
    labels:
      # - "traefik.enable=true"
      # - "traefik.http.routers.api.rule=(Host(`host.com`) && (PathPrefix(`/api`) || PathPrefix(`/dashboard`)))"
      - "traefik.http.routers.api.rule=((PathPrefix(`/api`) || PathPrefix(`/dashboard`)))"
      - "traefik.http.routers.api.service=api@internal"
      - "traefik.http.routers.api.middlewares=auth"
      - "traefik.http.middlewares.auth.basicauth.users=toe:$$apr1$$9dmswe8v$$k1RQ.WAI57gWWHD.l/.dV1,sml:$$apr1$$ia38r5sb$$8uxrh4fGN.OOFjdhB9FNW0"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
    ports:
      - 80:80
    networks:
      - frontend

networks:
  frontend:
    external:
      name: sml_service_network

```


SSL

```
version: '3'

services:
  traefik:
    image: traefik:v2.5
    restart: always
    command:
      - "--providers.docker=true"
      - "--entrypoints.web.address=:80"
      - "--entrypoints.websecure.address=:443"
      - "--certificatesresolvers.letsencrypt.acme.httpchallenge=true"
      - "--certificatesresolvers.letsencrypt.acme.httpchallenge.entrypoint=web"
      - "--certificatesresolvers.letsencrypt.acme.email=toe@smlsoft.com"
      - "--certificatesresolvers.letsencrypt.acme.storage=/letsencrypt/acme.json"
      - "--api.dashboard=true"
      - "--log.level=DEBUG"
    labels:
      # - "traefik.enable=true"
      # - "traefik.http.routers.api.rule=(Host(`host.com`) && (PathPrefix(`/api`) || PathPrefix(`/dashboard`)))"
      - "traefik.http.routers.api.rule=((PathPrefix(`/api`) || PathPrefix(`/dashboard`)))"
      - "traefik.http.routers.api.service=api@internal"
      - "traefik.http.routers.api.middlewares=auth"
      - "traefik.http.middlewares.auth.basicauth.users=toe:$$apr1$$9dmswe8v$$k1RQ.WAI57gWWHD.l/.dV1,sml:$$apr1$$ia38r5sb$$8uxrh4fGN.OOFjdhB9FNW0"
    volumes:
      - /var/run/docker.sock:/var/run/docker.sock
      - ./letsencrypt:/letsencrypt
    ports:
      - 80:80
      - 8080:8080
    networks:
      - frontend

networks:
  frontend:
    external:
      name: sml_service_network
```