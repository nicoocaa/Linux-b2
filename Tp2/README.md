# TP2 : Utilisation courante de Docker

## Sommaire

- [TP2 : Utilisation courante de Docker](#tp2--utilisation-courante-de-docker)
  - [Sommaire](#sommaire)
- [I. Commun à tous : App Web](#i-commun-à-tous--app-web)
- [II Dév. Python](#ii-dév-python)
- [II Admin. Maîtrise de la stack Web](#ii-admin-maîtrise-de-la-stack-web)
- [II Secu. Big brain](#ii-secu-big-brain)

# I. Commun à tous : App Web

🌞 **`docker-compose.yml`**

[voir le docker compose](/Tp2/docker-tp2/)

# II Dév. Python



# II Admin. Maîtrise de la stack Web

# TP2 admins : Web stack

## Sommaire

- [TP2 admins : Web stack](#tp2-admins--web-stack)
  - [Sommaire](#sommaire)
- [I. Good practices](#i-good-practices)
- [II. Reverse proxy buddy](#ii-reverse-proxy-buddy)
  - [A. Simple HTTP setup](#a-simple-http-setup)
  - [B. HTTPS auto-signé](#b-https-auto-signé)
  - [C. HTTPS avec une CA maison](#c-https-avec-une-ca-maison)

# I. Good practices


🌞 **Limiter l'accès aux ressources**

- limiter la RAM que peut utiliser chaque conteneur à 1G

```bash
nico@debian:~$ docker run -dit --memory="1000m" nginx
beb1cea5202895a46d69e3a0534336155c9e487a4aadd17fe31f49ca1f59de3c
nico@debian:~$
```

- limiter à 1CPU chaque conteneur

```bash
nico@debian:~$ docker run -dit --cpus="1.0" nginx
71de71cbd8a7035552e49f4967193a4238c5169f3b0b61c0b9709c45ec6bd0f8
nico@debian:~$ 
```

🌞 **No `root`**

```bash
nico@debian:~/docker-tp2/suite$ sudo vi Dockerfile
nico@debian:~/docker-tp2/suite$ docker build -t my-secure-nginx .
[+] Building 0.5s (6/6) FINISHED                                                                                                                                             docker:default
 => [internal] load build definition from Dockerfile                                                                                                                                   0.0s
 => => transferring dockerfile: 141B                                                                                                                                                   0.0s
 => [internal] load metadata for docker.io/library/nginx:latest                                                                                                                        0.0s
 => [internal] load .dockerignore                                                                                                                                                      0.0s
 => => transferring context: 2B                                                                                                                                                        0.0s
 => [1/2] FROM docker.io/library/nginx:latest                                                                                                                                          0.1s
 => [2/2] RUN addgroup --system totogroupe && adduser --system --ingroup totogroupe toto                                                                                               0.3s
 => exporting to image                                                                                                                                                                 0.0s
 => => exporting layers                                                                                                                                                                0.0s
 => => writing image sha256:0173cafdf0f1aade3a4354756edc57ab2a04219f8c3ef602b033f319f65224a1                                                                                           0.0s
 => => naming to docker.io/library/my-secure-nginx                                                                                                                                     0.0s
nico@debian:~/docker-tp2/suite$ cat Dockerfile 
FROM nginx

RUN addgroup --system totogroupe && adduser --system --ingroup totogroupe toto


USER toto

nico@debian:~/docker-tp2/suite$ 

```

🌞 **Gestion des droits du volume qui contient le code**

```bash
sudo chmod -R 755 /app
```

🌞 **Gestion des capabilities sur le conteneur NGINX**

```bash
    cap_drop:
      - ALL        
    cap_add:
      - NET_BIND_SERVICE 
```

🌞 **Mode read-only**

````bash
    read_only: true      
```