# TP4 : Hardening Script

## Sommaire

- [TP4 : Hardening Script](#tp4--hardening-script)
  - [Sommaire](#sommaire)
- [0. Setup](#0-setup)
- [I. Setup initial](#i-setup-initial)
- [II. Hardening script](#ii-hardening-script)

# 0. Setup

➜ **Machines Rocky Linux**

- on aura un serveur web et un reverse proxy (deux machines donc)

# I. Setup initial

| Machine      | IP          | Rôle                       |
| ------------ | ----------- | -------------------------- |
| `rp.tp5.b2`  | `10.5.1.11` | reverse proxy (NGINX)      |
| `web.tp5.b2` | `10.5.1.12` | serveur Web (NGINX oci) |

🌞 **Setup `web.tp5.b2`**

```bash
[nico@localhost app_nulle]$ sudo systemctl restart nginx
[nico@localhost app_nulle]$ sudo systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; enabled; preset: disabled)
     Active: active (running) since Fri 2025-01-17 10:37:12 CET; 18s ago
    Process: 2060 ExecStartPre=/usr/bin/rm -f /run/nginx.pid (code=exited, status=0/SUCCESS)
    Process: 2062 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SUCCESS)
    Process: 2063 ExecStart=/usr/sbin/nginx (code=exited, status=0/SUCCESS)
   Main PID: 2064 (nginx)
      Tasks: 2 (limit: 11024)
     Memory: 2.0M
        CPU: 43ms
     CGroup: /system.slice/nginx.service
             ├─2064 "nginx: master process /usr/sbin/nginx"
             └─2065 "nginx: worker process"

Jan 17 10:37:12 localhost.localdomain systemd[1]: Starting The nginx HTTP and reverse proxy server...
Jan 17 10:37:12 localhost.localdomain nginx[2062]: nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
Jan 17 10:37:12 localhost.localdomain nginx[2062]: nginx: configuration file /etc/nginx/nginx.conf test is successful
Jan 17 10:37:12 localhost.localdomain systemd[1]: Started The nginx HTTP and reverse proxy server.
[nico@localhost app_nulle]$ curl http://10.5.1.12/
<!DOCTYPE html>
<html>
<head>
    <title>Bienvenue</title>
</head>
<body>
    <h1>Bienvenue sur App Nulle !</h1>
</body>
</html>

[nico@localhost app_nulle]$
```

🌞 **Setup `rp.tp5.b2`**

```bash
[nico@localhost etc]$ sudo systemctl restart nginx
[nico@localhost etc]$ sudo systemctl status nginx
● nginx.service - The nginx HTTP and reverse proxy server
     Loaded: loaded (/usr/lib/systemd/system/nginx.service; disabled; preset: disabled)
     Active: active (running) since Fri 2025-01-17 11:06:44 CET; 8s ago
    Process: 1565 ExecStartPre=/usr/bin/rm -f /run/nginx.pid (code=exited, status=0/SUCCESS)
    Process: 1566 ExecStartPre=/usr/sbin/nginx -t (code=exited, status=0/SUCCESS)
    Process: 1567 ExecStart=/usr/sbin/nginx (code=exited, status=0/SUCCESS)
   Main PID: 1568 (nginx)
      Tasks: 2 (limit: 11024)
     Memory: 2.0M
        CPU: 18ms
     CGroup: /system.slice/nginx.service
             ├─1568 "nginx: master process /usr/sbin/nginx"
             └─1569 "nginx: worker process"

Jan 17 11:06:44 localhost.localdomain systemd[1]: Starting The nginx HTTP and reverse proxy server...
Jan 17 11:06:44 localhost.localdomain nginx[1566]: nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
Jan 17 11:06:44 localhost.localdomain nginx[1566]: nginx: configuration file /etc/nginx/nginx.conf test is successful
Jan 17 11:06:44 localhost.localdomain systemd[1]: Started The nginx HTTP and reverse proxy server.
[nico@localhost etc]$ curl http://app.tp5.b2/
curl: (6) Could not resolve host: app.tp5.b2
[nico@localhost etc]$

```


```powershell
PS C:\Windows\System32\drivers\etc> curl http://app.tp5.b2/


StatusCode        : 200
StatusDescription : OK
Content           : <!DOCTYPE html>
                    <html>
                    <head>
                        <title>Bienvenue</title>
                    </head>
                    <body>
                        <h1>Bienvenue sur App Nulle !</h1>
                    </body>
                    </html>


RawContent        : HTTP/1.1 200 OK
                    Connection: keep-alive
                    Accept-Ranges: bytes
                    Content-Length: 130
                    Content-Type: text/html
                    Date: Fri, 17 Jan 2025 10:07:48 GMT
                    ETag: "678a290c-82"
                    Last-Modified: Fri, 17 Jan 2025 0...
Forms             : {}
Headers           : {[Connection, keep-alive], [Accept-Ranges, bytes], [Content-Length, 130], [Content-Type, text/html]...}
Images            : {}
InputFields       : {}
Links             : {}
ParsedHtml        : mshtml.HTMLDocumentClass
RawContentLength  : 130



PS C:\Windows\System32\drivers\etc>
```

🌞 **HTTPS `rp.tp5.b2`**

```bash
[nico@localhost etc]$ sudo openssl req -new -newkey rsa:4080 -days 365 -nodes -x509 -keyout /etc/pki/tls/private/server.key -out /etc/pki/tls/certs/server.c
rt
.....+......+......+.......+..+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*...+....+......+......+.....+....+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*...+.+........+.........+...............+.+..+....+.......................+......+.+......+...............+........+.+.........+.....+....+....................+...+.............+.....+.+.....+.+..............+.+.....+....+......+..+......+.+...........+..........+..+...+............+...+....+......+.....+...+.......+...+.....+.......+...+...........+...+..........+...........+.......+.................+.........................+..+.........+.+........+.......+...+.....+......+.......+...+.........+.....+...+....+.....+......+....+........+..........+...+........+.+.....+.........+.....................+.+...+.........+........+....+...+......+.....+..............................+.........+.+..........................+.............+..+..........+.....+...+.+..+...+.+...........+.......+..+...+.+...............+...........+.+........+............+...........................+....................................+.......+...+.....+....+............+.........+.........+..+.+..+.+.........+..+......+.......+........+...+.+...+.....+......+.......+..............+......+.........+.+...+...........+...................+......+.........+.....................+.....+.............+.....+.......+......+........+.......+.....+............+...+....+........+.+.........+......+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
...+.....+.+............+..+...+....+.....+...+......+.........+.+..+...+......+...+.......+......+............+...+..+.+...........+...+......+....+...+...+.....+.+.........+......+........+...+...+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*..+......+....+...+..+...+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++*......+......+.......+........+.......+......+.........+..+..........+........+..........+..+.........+....+........+..........+........................+......+.........+......+..+............+......+.......+...............+..+..................+............................+..+............+.........+......+...+...............+.............+...+...............+++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++++
-----
You are about to be asked to enter information that will be incorporated
into your certificate request.
What you are about to enter is what is called a Distinguished Name or a DN.
There are quite a few fields but you can leave some blank
For some fields there will be a default value,
If you enter '.', the field will be left blank.
-----
Country Name (2 letter code) [XX]:
State or Province Name (full name) []:
Locality Name (eg, city) [Default City]:
Organization Name (eg, company) [Default Company Ltd]:
Organizational Unit Name (eg, section) []:
Common Name (eg, your name or your server's hostname) []:
Email Address []:
[nico@localhost etc]$ ls /etc/pki/tls/private/server.key
/etc/pki/tls/private/server.key
[nico@localhost etc]$ ls /etc/pki/tls/certs/server.crt
/etc/pki/tls/certs/server.crt
[nico@localhost etc]$ sudo vi /etc/nginx/conf.d/app.tp5.b2.conf
[nico@localhost etc]$ sudo nginx -t
nginx: the configuration file /etc/nginx/nginx.conf syntax is ok
nginx: configuration file /etc/nginx/nginx.conf test is successful
[nico@localhost etc]$ sudo systemctl restart nginx
[nico@localhost etc]$ sudo firewall-cmd --permanent --add-service=https
success
[nico@localhost etc]$ sudo firewall-cmd --reload
success
[nico@localhost etc]$ sudo firewall-cmd --list-all
public (active)
  target: default
  icmp-block-inversion: no
  interfaces: enp0s3 enp0s8
  sources:
  services: cockpit dhcpv6-client http https ssh
  ports:
  protocols:
  forward: yes
  masquerade: no
  forward-ports:
  source-ports:
  icmp-blocks:
  rich rules:
[nico@localhost etc]$
```

# II. Hardening script

Dans cette section, le coeur du sujet, vous allez développer un script `bash` qui permet de renforcer la sécurité de ces deux machines.

➜ **Votre script doit permettre de :**

- **configurer l'OS**
  - tout ce qui va être relatif au kernel
  - et tous les services basiques du système, comme la gestion de l'heure
  - éventuellement de la conf systemd, sudo, etc.
- **configurer l'accès à distance**
  - on pose une conf SSH robuste
- **gérer la conf NGINX**
  - votre script doit aussi proposer un fichier de conf NGINX maîtrisé et robuste
- **ajouter et configurer des services de sécurité**
  - on pense à fail2ban, AIDE, ou autres

> Réutilisez votre travail sur le sujet hardening du TP précédent évidemment. Réutilisez aussi ce que vous saviez déjà faire (bah si, non ?) comme fail2ban, ou l'application du principe du moindre privilège, la gestion de `sudo`. Enfin, soyez créatifs, c'est un exo libre.

➜ **N'hésitez pas à :**

- éclater le code dans plusieurs fichiers
- écrire des fonctions plutôt que tout à la suite

> Le but c'est de bosser sur le coeur du sujet : harden une machine Linux. En plus, être capable de l'automatiser comme ça on peut le lancer sur n'importe quelle nouvelle machine. Et aussi, vous faire prendre du skill sur `bash`.

![Feels good](./img/feels_good.png)