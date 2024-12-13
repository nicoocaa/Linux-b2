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

[Document dédié au lancement d'un environnement de dév Python avec Docker.](./dev.md)

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

- on parle du conteneur Python qui contient l'app web
- **là y'a un dilemne vraiment très récurrent qui apparaît**
  - on a un user qui lance les programmes dans le conteneur
  - mais il n'existe pas sur la machine hôte
  - donc, s'il y a un volume partagé, à qui doivent appartenir les fichiers ?
- je vous laisse méditer sur ça, et me donner la commande `chmod`/`chown` adaptée (à faire sur la machine hôte, pour que ce soit propre, et utilisable par le conteneur)

🌞 **Gestion des capabilities sur le conteneur NGINX**

- les capabilities sont des droits que possèdent un programme pendant son exécution
- [il en existe une vingtaine](https://man7.org/linux/man-pages/man7/capabilities.7.html) : ça correspond aux droit de `root`, mais divisés en plusieurs droits indépendants
- autrement dit, on peut ne pas être `root` mais avoir des droits identiques, si on peut lancer des programmes qui ont toutes les capabilities
- quand on lance un conteneur avec `docker run`, on peut limiter les capabilities qu'il aura le droit d'utiliser
- **adaptez le `docker-compose.yml` pour qu'il enlève le plus de capabilities possible au conteneur NGINX**

> En vrai, en ayant déjà changé l'utilisateur avec lequel s'exécute NGINX, et en modifiant un peu la conf, en vrai, y'a moyen de tout lui enlever ;D

🌞 **Mode read-only**

- certains conteneurs ne vont (quasiment) jamais avoir besoin d'écrire sur le disque dur (comme le conteneur NGINX)
- passer le conteneur NGINX en mode read-only
- c'est documenté sur la page de NGINX sur le Docker Hub

> **Empêcher l'écriture est une mesure de sécurité très forte.** Rappelez-vous qu'un système, un OS, ce n'est qu'un gros tas de fichiers. Toucher aux fichiers, c'est toucher à l'OS, au kernel, aux programmes, aux données persos, à la conf, etc. Un vilain hacker va toujours chercher à déposer ses programmes, à exécuter du code pour pivoter sur d'autres machines. Très peu de choses sont possibles si la machine est en read-only !

⭐ **BONUS** :

- appliquer ces mesures sur tous les conteneurs du `docker-compose.yml`, principalement :
  - no `root`
  - gestion de capabilities
  - mode read-only quand c'est possible

# II. Reverse proxy buddy

On continue sur le sujet app Web !

On va ajouter un reverse proxy dans le mix !

## A. Simple HTTP setup

🌞 **Adaptez le `docker-compose.yml`** de [la partie précédente](./web.md)

- il doit inclure un quatrième conteneur : un reverse proxy NGINX
  - image officielle !
  - un volume pour ajouter un fichier de conf
- je vous file une conf minimale juste en dessous
- c'est le seul conteneur exposé (partage de ports)
  - il permet d'accéder soit à PHPMyAdmin
  - soit à votre site
- vous ajouterez au fichier `hosts` de **votre PC** (le client)
  - `www.supersite.com` qui pointe vers l'IP de la machine qui héberge les conteneurs
  - `pma.supersite.com` qui pointe vers la même IP (`pma` pour PHPMyAdmin)
  - en effet, c'est grâce au nom que vous saisissez que NGINX saura vers quel conteneur vous renvoyer !

> *Tu peux choisir un nom de domaine qui te plaît + on s'en fout, mais pense à bien adapter tous mes exemples par la suite si tu en choisis un autre.*

```nginx
server {
    listen       80;
    server_name  www.supersite.com;

    location / {
        proxy_pass   http://nom_du_conteneur_PHP;
    }
}

server {
    listen       80;
    server_name  pma.supersite.com;

    location / {
        proxy_pass   http://nom_du_conteneur_PMA;
    }
}
```

## B. HTTPS auto-signé

🌞 **HTTPS** auto-signé

- générez un certificat et une clé auto-signés
- adaptez la conf de NGINX pour tout servir en HTTPS
- la clé et le certificat doivent être montés avec des volumes (`-v`)
- la commande pour générer une clé et un cert auto-signés :

```bash
openssl req -new -newkey rsa:4096 -days 365 -nodes -x509 -keyout www.supersite.com.key -out www.supersite.com.crt
```

> Vous pouvez générer deux certificats (un pour chaque sous-domaine) ou un certificat *wildcard* qui est valide pour `*.supersite.com` (genre tous les sous-domaines de `supersite.com`).

## C. HTTPS avec une CA maison

> **Vous pouvez jeter la clé et le certificat de la partie précédente :D**

On va commencer par générer la clé et le certificat de notre Autorité de Certification (CA). Une fois fait, on pourra s'en servir pour signer d'autres certificats, comme celui de notre serveur web.

Pour que la connexion soit trusted, il suffira alors d'ajouter le certificat de notre CA au magasin de certificats de votre navigateur sur votre PC.

Il vous faudra un shell bash et des commandes usuelles sous la main pour réaliser les opérations. Lancez une VM, ou ptet Git Bash, ou ptet un conteneur debian oneshot ?

🌞 **Générer une clé et un certificat de CA**

```bash
# mettez des infos dans le prompt, peu importe si c'est fake
# on va vous demander un mot de passe pour chiffrer la clé aussi
$ openssl genrsa -des3 -out CA.key 4096
$ openssl req -x509 -new -nodes -key CA.key -sha256 -days 1024  -out CA.pem
$ ls
# le pem c'est le certificat (clé publique)
# le key c'est la clé privée
```

Il est temps de générer une clé et un certificat que notre serveur web pourra utiliser afin de proposer une connexion HTTPS.

🌞 **Générer une clé et une demande de signature de certificat pour notre serveur web**

```bash
$ openssl req -new -nodes -out www.supersite.com.csr -newkey rsa:4096 -keyout www.supersite.com.key
$ ls
# www.supersite.com.csr c'est la demande de signature
# www.supersite.com.key c'est la clé qu'utilisera le serveur web
```

🌞 **Faire signer notre certificat par la clé de la CA**

- préparez un fichier `v3.ext` qui contient :

```ext
authorityKeyIdentifier=keyid,issuer
basicConstraints=CA:FALSE
keyUsage = digitalSignature, nonRepudiation, keyEncipherment, dataEncipherment
subjectAltName = @alt_names

[alt_names]
DNS.1 = www.supersite.com
DNS.2 = www.tp2.admins
```

- effectuer la demande de signature pour récup un certificat signé par votre CA :

```bash
$ openssl x509 -req -in www.supersite.com.csr -CA CA.pem -CAkey CA.key -CAcreateserial -out www.supersite.com.crt -days 500 -sha256 -extfile v3.ext
$ ls
# www.supersite.com.crt c'est le certificat qu'utilisera le serveur web
```

🌞 **Ajustez la configuration NGINX**

- le site web doit être disponible en HTTPS en utilisant votre clé et votre certificat
- une conf minimale ressemble à ça :

```nginx
server {
    [...]
    # faut changer le listen
    listen <IP>:443 ssl;

    # et ajouter ces deux lignes
    ssl_certificate /chemin/vers/le/cert/www.supersite.com.crt;
    ssl_certificate_key /chemin/vers/la/clé/www.supersite.com.key;
    [...]
}
```

🌞 **Prouvez avec un `curl` que vous accédez au site web**

- depuis votre PC
- avec un `curl -k` car il ne reconnaît pas le certificat là

🌞 **Ajouter le certificat de la CA dans votre navigateur**

- vous pourrez ensuite visitez `https://www.supersite.com` sans alerte de sécurité, et avec un cadenas vert
- il est nécessaire de joindre le site avec son nom pour que HTTPS fonctionne (fichier `hosts`)

> *En entreprise, c'est comme ça qu'on fait pour qu'un certificat de CA non-public soit trusted par tout le monde : on dépose le certificat de CA dans le navigateur (et l'OS) de tous les PCs. Evidemment, on utilise une technique de déploiement automatisé aussi dans la vraie vie, on l'ajoute pas à la main partout hehe.*

# II Secu. Big brain

Pour les sécus, vous faites tout : la partie "II Dév" et la partie et "II Admin". Hf.