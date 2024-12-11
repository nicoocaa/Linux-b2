# I. Init

- [I. Init](#i-init)k
  - [3. sudo c pa bo](#3-sudo-c-pa-bo)
  - [4. Un premier conteneur en vif](#4-un-premier-conteneur-en-vif)
  - [5. Un deuxième conteneur en vif](#5-un-deuxième-conteneur-en-vif)
- [II. Images](#ii-images)
  - [1. Images publiques](#1-images-publiques)
  - [2. Construire une image](#2-construire-une-image)
- [III. Docker compose](#iii-docker-compose)


## 3. sudo c pa bo

On va faire en sorte que vous puissiez taper des commandes `docker` sans avoir besoin des droits `root`, et donc de `sudo`.

Pour ça il suffit d'ajouter votre utilisateur au groupe `docker`.

> ***Pour que le changement de groupe prenne effet, il faut vous déconnecter/reconnecter de la session SSH** (pas besoin de reboot la machine, pitié).*

🌞 **Ajouter votre utilisateur au groupe `docker`**

```bash
nico@debian:~$ docker ps
permission denied while trying to connect to the Docker daemon socket at unix:///var/run/docker.sock: Get "http://%2Fvar%2Frun%2Fdocker.sock/v1.47/containers/json": dial unix /var/run/docker.sock: connect: permission denied
nico@debian:~$ sudo docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
nico@debian:~$ sudo usermod -aG docker ${USER}
nico@debian:~$ su - ${USER}
Password: 
nico@debian:~$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
nico@debian:~$ 
```

## 4. Un premier conteneur en vif


🌞 **Lancer un conteneur NGINX**

- avec la commande suivante :

```bash
nico@debian:~$ docker run -d -p 9999:80 nginx
Unable to find image 'nginx:latest' locally
latest: Pulling from library/nginx
bc0965b23a04: Pull complete 
650ee30bbe5e: Pull complete 
8cc1569e58f5: Pull complete 
362f35df001b: Pull complete 
13e320bf29cd: Pull complete 
7b50399908e1: Pull complete 
57b64962dd94: Pull complete 
Digest: sha256:fb197595ebe76b9c0c14ab68159fd3c08bd067ec62300583543f0ebda353b5be
Status: Downloaded newer image for nginx:latest
784a0fe79bb454cbde9733a79e2b51bbb3ca6c12558fc2226ff825436661fc13
nico@debian:~$ 


```

🌞 **Visitons**

```bash
nico@debian:~$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                                     NAMES
784a0fe79bb4   nginx     "/docker-entrypoint.…"   3 minutes ago   Up 3 minutes   0.0.0.0:9999->80/tcp, [::]:9999->80/tcp   adoring_chaum
nico@debian:~$ docker logs 784
/docker-entrypoint.sh: /docker-entrypoint.d/ is not empty, will attempt to perform configuration
/docker-entrypoint.sh: Looking for shell scripts in /docker-entrypoint.d/
/docker-entrypoint.sh: Launching /docker-entrypoint.d/10-listen-on-ipv6-by-default.sh
10-listen-on-ipv6-by-default.sh: info: Getting the checksum of /etc/nginx/conf.d/default.conf
10-listen-on-ipv6-by-default.sh: info: Enabled listen on IPv6 in /etc/nginx/conf.d/default.conf
/docker-entrypoint.sh: Sourcing /docker-entrypoint.d/15-local-resolvers.envsh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/20-envsubst-on-templates.sh
/docker-entrypoint.sh: Launching /docker-entrypoint.d/30-tune-worker-processes.sh
/docker-entrypoint.sh: Configuration complete; ready for start up
2024/12/11 10:07:49 [notice] 1#1: using the "epoll" event method
2024/12/11 10:07:49 [notice] 1#1: nginx/1.27.3
2024/12/11 10:07:49 [notice] 1#1: built by gcc 12.2.0 (Debian 12.2.0-14) 
2024/12/11 10:07:49 [notice] 1#1: OS: Linux 6.1.0-27-amd64
2024/12/11 10:07:49 [notice] 1#1: getrlimit(RLIMIT_NOFILE): 1048576:1048576
2024/12/11 10:07:49 [notice] 1#1: start worker processes
2024/12/11 10:07:49 [notice] 1#1: start worker process 28
2024/12/11 10:07:49 [notice] 1#1: start worker process 29
2024/12/11 10:07:49 [notice] 1#1: start worker process 30
2024/12/11 10:07:49 [notice] 1#1: start worker process 31
2024/12/11 10:07:49 [notice] 1#1: start worker process 32
2024/12/11 10:07:49 [notice] 1#1: start worker process 33
2024/12/11 10:07:49 [notice] 1#1: start worker process 34
2024/12/11 10:07:49 [notice] 1#1: start worker process 35
2024/12/11 10:07:49 [notice] 1#1: start worker process 36
2024/12/11 10:07:49 [notice] 1#1: start worker process 37
2024/12/11 10:07:49 [notice] 1#1: start worker process 38
2024/12/11 10:07:49 [notice] 1#1: start worker process 39
2024/12/11 10:07:49 [notice] 1#1: start worker process 40
2024/12/11 10:07:49 [notice] 1#1: start worker process 41
2024/12/11 10:07:49 [notice] 1#1: start worker process 42
2024/12/11 10:07:49 [notice] 1#1: start worker process 43
172.17.0.1 - - [11/Dec/2024:10:09:44 +0000] "GET / HTTP/1.1" 200 615 "-" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/130.0.0.0 Safari/537.36" "-"
2024/12/11 10:09:45 [error] 28#28: *1 open() "/usr/share/nginx/html/favicon.ico" failed (2: No such file or directory), client: 172.17.0.1, server: localhost, request: "GET /favicon.ico HTTP/1.1", host: "localhost:9999", referrer: "http://localhost:9999/"
172.17.0.1 - - [11/Dec/2024:10:09:45 +0000] "GET /favicon.ico HTTP/1.1" 404 555 "http://localhost:9999/" "Mozilla/5.0 (X11; Linux x86_64) AppleWebKit/537.36 (KHTML, like Gecko) Chrome/130.0.0.0 Safari/537.36" "-"
nico@debian:~$ docker inspect 78
[
    {
        "Id": "784a0fe79bb454cbde9733a79e2b51bbb3ca6c12558fc2226ff825436661fc13",
        "Created": "2024-12-11T10:07:49.62313075Z",
        "Path": "/docker-entrypoint.sh",
        "Args": [
            "nginx",
            "-g",
            "daemon off;"
        ],
        "State": {
            "Status": "running",
            "Running": true,
            "Paused": false,
            "Restarting": false,
            "OOMKilled": false,
            "Dead": false,
            "Pid": 11185,
            "ExitCode": 0,
            "Error": "",
            "StartedAt": "2024-12-11T10:07:49.696686282Z",
            "FinishedAt": "0001-01-01T00:00:00Z"
        },
        "Image": "sha256:66f8bdd3810c96dc5c28aec39583af731b34a2cd99471530f53c8794ed5b423e",
        "ResolvConfPath": "/var/lib/docker/containers/784a0fe79bb454cbde9733a79e2b51bbb3ca6c12558fc2226ff825436661fc13/resolv.conf",
        "HostnamePath": "/var/lib/docker/containers/784a0fe79bb454cbde9733a79e2b51bbb3ca6c12558fc2226ff825436661fc13/hostname",
        "HostsPath": "/var/lib/docker/containers/784a0fe79bb454cbde9733a79e2b51bbb3ca6c12558fc2226ff825436661fc13/hosts",
        "LogPath": "/var/lib/docker/containers/784a0fe79bb454cbde9733a79e2b51bbb3ca6c12558fc2226ff825436661fc13/784a0fe79bb454cbde9733a79e2b51bbb3ca6c12558fc2226ff825436661fc13-json.log",
        "Name": "/adoring_chaum",
        "RestartCount": 0,
        "Driver": "overlay2",
        "Platform": "linux",
        "MountLabel": "",
        "ProcessLabel": "",
        "AppArmorProfile": "docker-default",
        "ExecIDs": null,
        "HostConfig": {
            "Binds": null,
            "ContainerIDFile": "",
            "LogConfig": {
                "Type": "json-file",
                "Config": {}
            },
            "NetworkMode": "bridge",
            "PortBindings": {
                "80/tcp": [
                    {
                        "HostIp": "",
                        "HostPort": "9999"
                    }
                ]
            },
            "RestartPolicy": {
                "Name": "no",
                "MaximumRetryCount": 0
            },
            "AutoRemove": false,
            "VolumeDriver": "",
            "VolumesFrom": null,
            "ConsoleSize": [
                41,
                188
            ],
            "CapAdd": null,
            "CapDrop": null,
            "CgroupnsMode": "private",
            "Dns": [],
            "DnsOptions": [],
            "DnsSearch": [],
            "ExtraHosts": null,
            "GroupAdd": null,
            "IpcMode": "private",
            "Cgroup": "",
            "Links": null,
            "OomScoreAdj": 0,
            "PidMode": "",
            "Privileged": false,
            "PublishAllPorts": false,
            "ReadonlyRootfs": false,
            "SecurityOpt": null,
            "UTSMode": "",
            "UsernsMode": "",
            "ShmSize": 67108864,
            "Runtime": "runc",
            "Isolation": "",
            "CpuShares": 0,
            "Memory": 0,
            "NanoCpus": 0,
            "CgroupParent": "",
            "BlkioWeight": 0,
            "BlkioWeightDevice": [],
            "BlkioDeviceReadBps": [],
            "BlkioDeviceWriteBps": [],
            "BlkioDeviceReadIOps": [],
            "BlkioDeviceWriteIOps": [],
            "CpuPeriod": 0,
            "CpuQuota": 0,
            "CpuRealtimePeriod": 0,
            "CpuRealtimeRuntime": 0,
            "CpusetCpus": "",
            "CpusetMems": "",
            "Devices": [],
            "DeviceCgroupRules": null,
            "DeviceRequests": null,
            "MemoryReservation": 0,
            "MemorySwap": 0,
            "MemorySwappiness": null,
            "OomKillDisable": null,
            "PidsLimit": null,
            "Ulimits": [],
            "CpuCount": 0,
            "CpuPercent": 0,
            "IOMaximumIOps": 0,
            "IOMaximumBandwidth": 0,
            "MaskedPaths": [
                "/proc/asound",
                "/proc/acpi",
                "/proc/kcore",
                "/proc/keys",
                "/proc/latency_stats",
                "/proc/timer_list",
                "/proc/timer_stats",
                "/proc/sched_debug",
                "/proc/scsi",
                "/sys/firmware",
                "/sys/devices/virtual/powercap"
            ],
            "ReadonlyPaths": [
                "/proc/bus",
                "/proc/fs",
                "/proc/irq",
                "/proc/sys",
                "/proc/sysrq-trigger"
            ]
        },
        "GraphDriver": {
            "Data": {
                "LowerDir": "/var/lib/docker/overlay2/ec6682a6c3e0ab39dbd48c398c535c13ecfdfeaf5b80ea060ed6cf12696058a1-init/diff:/var/lib/docker/overlay2/06eaa65b0e3c06ee83c673a52b521e97b7ee1273b93c165c6ab10a1c0dd80032/diff:/var/lib/docker/overlay2/bbacce69b2876d827135f519df13de8a1291291d4f0fd62cadf2c9cb6f646854/diff:/var/lib/docker/overlay2/0e2ff34f98d30b7eddd6452cec5f02a2729e404d2e57f12767548f931fcc6466/diff:/var/lib/docker/overlay2/c626f63520c69c239edf1a7d9cc93728c9cbf06fdf477eca5aacff37a5c60b00/diff:/var/lib/docker/overlay2/032bfdf26696058745704bd9c64ff18ee5c0443289b7d30301c9ccaf586cfaba/diff:/var/lib/docker/overlay2/62ecce86d2fd9ac003b9e238cc7a217f05806e7053a2f8baff1a2880557310a2/diff:/var/lib/docker/overlay2/4eb9851132d0e833354695f03ac49922dd2223d6d681a88191243a1811048364/diff",
                "MergedDir": "/var/lib/docker/overlay2/ec6682a6c3e0ab39dbd48c398c535c13ecfdfeaf5b80ea060ed6cf12696058a1/merged",
                "UpperDir": "/var/lib/docker/overlay2/ec6682a6c3e0ab39dbd48c398c535c13ecfdfeaf5b80ea060ed6cf12696058a1/diff",
                "WorkDir": "/var/lib/docker/overlay2/ec6682a6c3e0ab39dbd48c398c535c13ecfdfeaf5b80ea060ed6cf12696058a1/work"
            },
            "Name": "overlay2"
        },
        "Mounts": [],
        "Config": {
            "Hostname": "784a0fe79bb4",
            "Domainname": "",
            "User": "",
            "AttachStdin": false,
            "AttachStdout": false,
            "AttachStderr": false,
            "ExposedPorts": {
                "80/tcp": {}
            },
            "Tty": false,
            "OpenStdin": false,
            "StdinOnce": false,
            "Env": [
                "PATH=/usr/local/sbin:/usr/local/bin:/usr/sbin:/usr/bin:/sbin:/bin",
                "NGINX_VERSION=1.27.3",
                "NJS_VERSION=0.8.7",
                "NJS_RELEASE=1~bookworm",
                "PKG_RELEASE=1~bookworm",
                "DYNPKG_RELEASE=1~bookworm"
            ],
            "Cmd": [
                "nginx",
                "-g",
                "daemon off;"
            ],
            "Image": "nginx",
            "Volumes": null,
            "WorkingDir": "",
            "Entrypoint": [
                "/docker-entrypoint.sh"
            ],
            "OnBuild": null,
            "Labels": {
                "maintainer": "NGINX Docker Maintainers <docker-maint@nginx.com>"
            },
            "StopSignal": "SIGQUIT"
        },
        "NetworkSettings": {
            "Bridge": "",
            "SandboxID": "e5572c21ad719bfc65b039f9705b506ad76904c15a9643ea46db5bbb7c26b55e",
            "SandboxKey": "/var/run/docker/netns/e5572c21ad71",
            "Ports": {
                "80/tcp": [
                    {
                        "HostIp": "0.0.0.0",
                        "HostPort": "9999"
                    },
                    {
                        "HostIp": "::",
                        "HostPort": "9999"
                    }
                ]
            },
            "HairpinMode": false,
            "LinkLocalIPv6Address": "",
            "LinkLocalIPv6PrefixLen": 0,
            "SecondaryIPAddresses": null,
            "SecondaryIPv6Addresses": null,
            "EndpointID": "c43e1a574109415748934e8cf87de8d05b05430b6caea5837da87d60aa2ea8b6",
            "Gateway": "172.17.0.1",
            "GlobalIPv6Address": "",
            "GlobalIPv6PrefixLen": 0,
            "IPAddress": "172.17.0.2",
            "IPPrefixLen": 16,
            "IPv6Gateway": "",
            "MacAddress": "02:42:ac:11:00:02",
            "Networks": {
                "bridge": {
                    "IPAMConfig": null,
                    "Links": null,
                    "Aliases": null,
                    "MacAddress": "02:42:ac:11:00:02",
                    "DriverOpts": null,
                    "NetworkID": "a8449011400cb67ed414c3416452cc06edc22933f169414184c7f4e4b05ea20b",
                    "EndpointID": "c43e1a574109415748934e8cf87de8d05b05430b6caea5837da87d60aa2ea8b6",
                    "Gateway": "172.17.0.1",
                    "IPAddress": "172.17.0.2",
                    "IPPrefixLen": 16,
                    "IPv6Gateway": "",
                    "GlobalIPv6Address": "",
                    "GlobalIPv6PrefixLen": 0,
                    "DNSNames": null
                }
            }
        }
    }
]
nico@debian:~$ sudo ss -lnpt
[sudo] Mot de passe de nico : 
State                           Recv-Q                          Send-Q                                                   Local Address:Port                                                     Peer Address:Port                          Process                                                           
LISTEN                          0                               128                                                          127.0.0.1:631                                                           0.0.0.0:*                              users:(("cupsd",pid=973,fd=7))                                   
LISTEN                          0                               32                                                       192.168.122.1:53                                                            0.0.0.0:*                              users:(("dnsmasq",pid=1384,fd=6))                                
LISTEN                          0                               4096                                                           0.0.0.0:9999                                                          0.0.0.0:*                              users:(("docker-proxy",pid=11112,fd=4))                          
LISTEN                          0                               128                                                              [::1]:631                                                              [::]:*                              users:(("cupsd",pid=973,fd=6))                                   
LISTEN                          0                               50                                                                   *:1716                                                                *:*                              users:(("kdeconnectd",pid=1917,fd=20))                           
LISTEN                          0                               4096                                                              [::]:9999                                                             [::]:*                              users:(("docker-proxy",pid=11136,fd=4))                          
nico@debian:~$ 

```

🌞 **On va ajouter un site Web au conteneur NGINX**


```bash
nico@debian:~$ mkdir nginx
nico@debian:~$ cd nginx/
nico@debian:~/nginx$ sudo vi index.html
nico@debian:~/nginx$ docker run -d -p 9999:8080 -v /home/nico/nginx/index.html:/var/www/html/index.html -v /home/nico/nginx/site_nul.conf:/etc/nginx/conf.d/site_nul.conf nginx
30adabf810c624a4e7c8202ac0b6ee0ed69ec4a4bbad4b8dc77c6ee51d7b48fd
docker: Error response from daemon: driver failed programming external connectivity on endpoint eloquent_poincare (ccdd9321e1a2799a61b0500620488a053de091fac4e87298750584983927d208): Bind for 0.0.0.0:9999 failed: port is already allocated.
nico@debian:~/nginx$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED          STATUS          PORTS                                     NAMES
784a0fe79bb4   nginx     "/docker-entrypoint.…"   27 minutes ago   Up 27 minutes   0.0.0.0:9999->80/tcp, [::]:9999->80/tcp   adoring_chaum
nico@debian:~/nginx$ docker rm -f 78
78
nico@debian:~/nginx$ docker ps
CONTAINER ID   IMAGE     COMMAND   CREATED   STATUS    PORTS     NAMES
nico@debian:~/nginx$ docker run -d -p 9999:8080 -v /home/nico/nginx/index.html:/var/www/html/index.html -v /home/nico/nginx/site_nul.conf:/etc/nginx/conf.d/site_nul.conf nginx
96272dcd0203b4fb6ba0c584f2fa0643041f6c92e8d3e015b6bb0c864e20cfdc
nico@debian:~/nginx$ 

```

🌞 **Visitons**

```bash
nico@debian:~/nginx$ docker ps
CONTAINER ID   IMAGE     COMMAND                  CREATED         STATUS         PORTS                                                 NAMES
96272dcd0203   nginx     "/docker-entrypoint.…"   2 minutes ago   Up 2 minutes   80/tcp, 0.0.0.0:9999->8080/tcp, [::]:9999->8080/tcp   vigorous_cori
nico@debian:~/nginx$ curl http://localhost:9999/
<h1>MEOOOW</h1>
nico@debian:~/nginx$ 

```

## 5. Un deuxième conteneur en vif

Cette fois on va lancer un conteneur Python, comme si on voulait tester une nouvelle lib Python par exemple. Mais sans installer ni Python ni la lib sur notre machine.

On va donc le lancer de façon interactive : on lance le conteneur, et on pop tout de suite un shell dedans pour faire joujou.

🌞 **Lance un conteneur Python, avec un shell**

```bash
nico@debian:~$ docker run -it python bash
root@b4fc2658c2dc:/# 
```

🌞 **Installe des libs Python**

```bash
root@b4fc2658c2dc:/# pip install aiohttp
Collecting aiohttp
  Downloading aiohttp-3.11.10-cp313-cp313-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (7.7 kB)
Collecting aiohappyeyeballs>=2.3.0 (from aiohttp)
  Downloading aiohappyeyeballs-2.4.4-py3-none-any.whl.metadata (6.1 kB)
Collecting aiosignal>=1.1.2 (from aiohttp)
  Downloading aiosignal-1.3.1-py3-none-any.whl.metadata (4.0 kB)
Collecting attrs>=17.3.0 (from aiohttp)
  Downloading attrs-24.2.0-py3-none-any.whl.metadata (11 kB)
Collecting frozenlist>=1.1.1 (from aiohttp)
  Downloading frozenlist-1.5.0-cp313-cp313-manylinux_2_5_x86_64.manylinux1_x86_64.manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (13 kB)
Collecting multidict<7.0,>=4.5 (from aiohttp)
  Downloading multidict-6.1.0-cp313-cp313-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (5.0 kB)
Collecting propcache>=0.2.0 (from aiohttp)
  Downloading propcache-0.2.1-cp313-cp313-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (9.2 kB)
Collecting yarl<2.0,>=1.17.0 (from aiohttp)
  Downloading yarl-1.18.3-cp313-cp313-manylinux_2_17_x86_64.manylinux2014_x86_64.whl.metadata (69 kB)
Collecting idna>=2.0 (from yarl<2.0,>=1.17.0->aiohttp)
  Downloading idna-3.10-py3-none-any.whl.metadata (10 kB)
Downloading aiohttp-3.11.10-cp313-cp313-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (1.7 MB)
   ━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━ 1.7/1.7 MB 37.1 MB/s eta 0:00:00
Downloading aiohappyeyeballs-2.4.4-py3-none-any.whl (14 kB)
Downloading aiosignal-1.3.1-py3-none-any.whl (7.6 kB)
Downloading attrs-24.2.0-py3-none-any.whl (63 kB)
Downloading frozenlist-1.5.0-cp313-cp313-manylinux_2_5_x86_64.manylinux1_x86_64.manylinux_2_17_x86_64.manylinux2014_x86_64.whl (267 kB)
Downloading multidict-6.1.0-cp313-cp313-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (131 kB)
Downloading propcache-0.2.1-cp313-cp313-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (227 kB)
Downloading yarl-1.18.3-cp313-cp313-manylinux_2_17_x86_64.manylinux2014_x86_64.whl (339 kB)
Downloading idna-3.10-py3-none-any.whl (70 kB)
Installing collected packages: propcache, multidict, idna, frozenlist, attrs, aiohappyeyeballs, yarl, aiosignal, aiohttp
Successfully installed aiohappyeyeballs-2.4.4 aiohttp-3.11.10 aiosignal-1.3.1 attrs-24.2.0 frozenlist-1.5.0 idna-3.10 multidict-6.1.0 propcache-0.2.1 yarl-1.18.3
WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager, possibly rendering your system unusable.It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv. Use the --root-user-action option if you know what you are doing and want to suppress this warning.
root@b4fc2658c2dc:/# pip install aioconsole
Collecting aioconsole
  Downloading aioconsole-0.8.1-py3-none-any.whl.metadata (46 kB)
Downloading aioconsole-0.8.1-py3-none-any.whl (43 kB)
Installing collected packages: aioconsole
Successfully installed aioconsole-0.8.1
WARNING: Running pip as the 'root' user can result in broken permissions and conflicting behaviour with the system package manager, possibly rendering your system unusable.It is recommended to use a virtual environment instead: https://pip.pypa.io/warnings/venv. Use the --root-user-action option if you know what you are doing and want to suppress this warning.
root@b4fc2658c2dc:/# python
Python 3.13.1 (main, Dec  4 2024, 20:40:27) [GCC 12.2.0] on linux
Type "help", "copyright", "credits" or "license" for more information.
>>> import aiohttp
>>> 
KeyboardInterrupt
>>> 
>>> 

```


# II. Images

## 1. Images publiques

🌞 **Récupérez des images**

```bash
nico@debian:~$ docker pull python:3.11
3.11: Pulling from library/python
fdf894e782a2: Already exists 
5bd71677db44: Already exists 
551df7f94f9c: Already exists 
ce82e98d553d: Already exists 
2371bf9a39a3: Pull complete 
0b3239f18dfa: Pull complete 
de07a735a679: Pull complete 
Digest: sha256:2c80c66d876952e04fa74113864903198b7cfb36b839acb7a8fef82e94ed067c
Status: Downloaded newer image for python:3.11
docker.io/library/python:3.11
nico@debian:~$ docker pull mysql:5.7
5.7: Pulling from library/mysql
20e4dcae4c69: Pull complete 
1c56c3d4ce74: Pull complete 
e9f03a1c24ce: Pull complete 
68c3898c2015: Pull complete 
6b95a940e7b6: Pull complete 
90986bb8de6e: Pull complete 
ae71319cb779: Pull complete 
ffc89e9dfd88: Pull complete 
43d05e938198: Pull complete 
064b2d298fba: Pull complete 
df9a4d85569b: Pull complete 
Digest: sha256:4bc6bc963e6d8443453676cae56536f4b8156d78bae03c0145cbe47c2aad73bb
Status: Downloaded newer image for mysql:5.7
docker.io/library/mysql:5.7
nico@debian:~$ docker pull wordpress
Using default tag: latest
latest: Pulling from library/wordpress
bc0965b23a04: Already exists 
e0aa8e8bfd10: Pull complete 
f45eeb7b7c66: Pull complete 
2802fa207e46: Pull complete 
2bc706e6dbbe: Pull complete 
8f2b85a95cfd: Pull complete 
2586d713206f: Pull complete 
086063f0275c: Pull complete 
61a388cf2f83: Pull complete 
73fd6b827991: Pull complete 
c2dd75e58cab: Pull complete 
3b01564181f9: Pull complete 
16d45113a90d: Pull complete 
4f4fb700ef54: Pull complete 
c4f8720ddb1e: Pull complete 
d374174149dd: Pull complete 
f09c82e22e1b: Pull complete 
dd7711b88413: Pull complete 
a89cceed0693: Pull complete 
dab7a4cf5d37: Pull complete 
e6f609a11365: Pull complete 
1bbc7feeba6d: Pull complete 
Digest: sha256:2f3572d5cd722489fe47d59ed45d947dc9507f5a019eb0567ddf24aedf9257ed
Status: Downloaded newer image for wordpress:latest
docker.io/library/wordpress:latest
nico@debian:~$ git pull linuxserver/wikijs
fatal: not a git repository (or any of the parent directories): .git
nico@debian:~$ docker pull linuxserver/wikijs
Using default tag: latest
latest: Pulling from linuxserver/wikijs
72387e7898ce: Pull complete 
ec7680b185bf: Pull complete 
b80232684b80: Pull complete 
52e5d6eefe42: Pull complete 
f224b10b4dc1: Pull complete 
d179dce5799c: Pull complete 
ff211b9bebf2: Pull complete 
979004086415: Pull complete 
Digest: sha256:45ecf23a3bf849a0bf0c9e2d832aede159e98efd29fef70bbc7ff4dd87522eba
Status: Downloaded newer image for linuxserver/wikijs:latest
docker.io/linuxserver/wikijs:latest
nico@debian:~$ docker images
REPOSITORY           TAG       IMAGE ID       CREATED         SIZE
linuxserver/wikijs   latest    863e49d2e56c   4 days ago      465MB
python               latest    3ca4060004b1   7 days ago      1.02GB
python               3.11      342f2c43d207   7 days ago      1.01GB
nginx                latest    66f8bdd3810c   2 weeks ago     192MB
wordpress            latest    c89b40a25cd1   2 weeks ago     700MB
mysql                5.7       5107333e08a8   12 months ago   501MB
hello-world          latest    d2c94e258dcb   19 months ago   13.3kB
nico@debian:~$ 

```

🌞 **Lancez un conteneur à partir de l'image Python**

```bash
nico@debian:~$ docker run -it python:3.11 bash
root@9692b80dac81:/# python -v
import _frozen_importlib # frozen
import _imp # builtin
import '_thread' # <class '_frozen_importlib.BuiltinImporter'>
import '_warnings' # <class '_frozen_importlib.BuiltinImporter'>
import '_weakref' # <class '_frozen_importlib.BuiltinImporter'>
import '_io' # <class '_frozen_importlib.BuiltinImporter'>
import 'marshal' # <class '_frozen_importlib.BuiltinImporter'>
import 'posix' # <class '_frozen_importlib.BuiltinImporter'>
import '_frozen_importlib_external' # <class '_frozen_importlib.FrozenImporter'>
# installing zipimport hook
import 'time' # <class '_frozen_importlib.BuiltinImporter'>
import 'zipimport' # <class '_frozen_importlib.FrozenImporter'>
# installed zipimport hook
# code object from /usr/local/lib/python3.11/encodings/__init__.py
# created '/usr/local/lib/python3.11/encodings/__pycache__/__init__.cpython-311.pyc'
import '_codecs' # <class '_frozen_importlib.BuiltinImporter'>
import 'codecs' # <class '_frozen_importlib.FrozenImporter'>
# code object from /usr/local/lib/python3.11/encodings/aliases.py
# created '/usr/local/lib/python3.11/encodings/__pycache__/aliases.cpython-311.pyc'
import 'encodings.aliases' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465ca8a10>
import 'encodings' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465ed57d0>
# code object from /usr/local/lib/python3.11/encodings/utf_8.py
# created '/usr/local/lib/python3.11/encodings/__pycache__/utf_8.cpython-311.pyc'
import 'encodings.utf_8' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465ef8dd0>
import '_signal' # <class '_frozen_importlib.BuiltinImporter'>
import '_abc' # <class '_frozen_importlib.BuiltinImporter'>
import 'abc' # <class '_frozen_importlib.FrozenImporter'>
import 'io' # <class '_frozen_importlib.FrozenImporter'>
import '_stat' # <class '_frozen_importlib.BuiltinImporter'>
import 'stat' # <class '_frozen_importlib.FrozenImporter'>
import '_collections_abc' # <class '_frozen_importlib.FrozenImporter'>
import 'genericpath' # <class '_frozen_importlib.FrozenImporter'>
import 'posixpath' # <class '_frozen_importlib.FrozenImporter'>
import 'os' # <class '_frozen_importlib.FrozenImporter'>
import '_sitebuiltins' # <class '_frozen_importlib.FrozenImporter'>
Processing user site-packages
Processing global site-packages
Adding directory: '/usr/local/lib/python3.11/site-packages'
Processing .pth file: '/usr/local/lib/python3.11/site-packages/distutils-precedence.pth'
# code object from /usr/local/lib/python3.11/site-packages/_distutils_hack/__init__.py
# created '/usr/local/lib/python3.11/site-packages/_distutils_hack/__pycache__/__init__.cpython-311.pyc'
import '_distutils_hack' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465ccec90>
import 'site' # <class '_frozen_importlib.FrozenImporter'>
# extension module 'readline' loaded from '/usr/local/lib/python3.11/lib-dynload/readline.cpython-311-x86_64-linux-gnu.so'
# extension module 'readline' executed from '/usr/local/lib/python3.11/lib-dynload/readline.cpython-311-x86_64-linux-gnu.so'
import 'readline' # <_frozen_importlib_external.ExtensionFileLoader object at 0x7f3465cce7d0>
# code object from /usr/local/lib/python3.11/rlcompleter.py
# created '/usr/local/lib/python3.11/__pycache__/rlcompleter.cpython-311.pyc'
import 'atexit' # <class '_frozen_importlib.BuiltinImporter'>
# code object from /usr/local/lib/python3.11/inspect.py
# created '/usr/local/lib/python3.11/__pycache__/inspect.cpython-311.pyc'
# code object from /usr/local/lib/python3.11/ast.py
# created '/usr/local/lib/python3.11/__pycache__/ast.cpython-311.pyc'
import '_ast' # <class '_frozen_importlib.BuiltinImporter'>
# code object from /usr/local/lib/python3.11/contextlib.py
# created '/usr/local/lib/python3.11/__pycache__/contextlib.cpython-311.pyc'
# code object from /usr/local/lib/python3.11/collections/__init__.py
# created '/usr/local/lib/python3.11/collections/__pycache__/__init__.cpython-311.pyc'
import 'itertools' # <class '_frozen_importlib.BuiltinImporter'>
# code object from /usr/local/lib/python3.11/keyword.py
# created '/usr/local/lib/python3.11/__pycache__/keyword.cpython-311.pyc'
import 'keyword' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465d99a50>
# code object from /usr/local/lib/python3.11/operator.py
# created '/usr/local/lib/python3.11/__pycache__/operator.cpython-311.pyc'
import '_operator' # <class '_frozen_importlib.BuiltinImporter'>
import 'operator' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465d99a10>
# code object from /usr/local/lib/python3.11/reprlib.py
# created '/usr/local/lib/python3.11/__pycache__/reprlib.cpython-311.pyc'
import 'reprlib' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465d986d0>
import '_collections' # <class '_frozen_importlib.BuiltinImporter'>
import 'collections' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465d76ad0>
# code object from /usr/local/lib/python3.11/functools.py
# created '/usr/local/lib/python3.11/__pycache__/functools.cpython-311.pyc'
# code object from /usr/local/lib/python3.11/types.py
# created '/usr/local/lib/python3.11/__pycache__/types.cpython-311.pyc'
import 'types' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465d41350>
import '_functools' # <class '_frozen_importlib.BuiltinImporter'>
import 'functools' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465d76f90>
import 'contextlib' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465d74f50>
# code object from /usr/local/lib/python3.11/enum.py
# created '/usr/local/lib/python3.11/__pycache__/enum.cpython-311.pyc'
import 'enum' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465d75810>
import 'ast' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465cf1b90>
# code object from /usr/local/lib/python3.11/dis.py
# created '/usr/local/lib/python3.11/__pycache__/dis.cpython-311.pyc'
# code object from /usr/local/lib/python3.11/opcode.py
# created '/usr/local/lib/python3.11/__pycache__/opcode.cpython-311.pyc'
# extension module '_opcode' loaded from '/usr/local/lib/python3.11/lib-dynload/_opcode.cpython-311-x86_64-linux-gnu.so'
# extension module '_opcode' executed from '/usr/local/lib/python3.11/lib-dynload/_opcode.cpython-311-x86_64-linux-gnu.so'
import '_opcode' # <_frozen_importlib_external.ExtensionFileLoader object at 0x7f3465d8dc50>
import 'opcode' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465cf28d0>
import 'dis' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465cf1e90>
# code object from /usr/local/lib/python3.11/collections/abc.py
# created '/usr/local/lib/python3.11/collections/__pycache__/abc.cpython-311.pyc'
import 'collections.abc' # <_frozen_importlib_external.SourceFileLoader object at 0x7f346598f490>
# code object from /usr/local/lib/python3.11/importlib/__init__.py
# created '/usr/local/lib/python3.11/importlib/__pycache__/__init__.cpython-311.pyc'
# code object from /usr/local/lib/python3.11/warnings.py
# created '/usr/local/lib/python3.11/__pycache__/warnings.cpython-311.pyc'
import 'warnings' # <_frozen_importlib_external.SourceFileLoader object at 0x7f346598e9d0>
import 'importlib' # <_frozen_importlib_external.SourceFileLoader object at 0x7f346598f9d0>
import 'importlib.machinery' # <class '_frozen_importlib.FrozenImporter'>
# code object from /usr/local/lib/python3.11/linecache.py
# created '/usr/local/lib/python3.11/__pycache__/linecache.cpython-311.pyc'
# code object from /usr/local/lib/python3.11/tokenize.py
# created '/usr/local/lib/python3.11/__pycache__/tokenize.cpython-311.pyc'
# code object from /usr/local/lib/python3.11/re/__init__.py
# created '/usr/local/lib/python3.11/re/__pycache__/__init__.cpython-311.pyc'
# code object from /usr/local/lib/python3.11/re/_compiler.py
# created '/usr/local/lib/python3.11/re/__pycache__/_compiler.cpython-311.pyc'
import '_sre' # <class '_frozen_importlib.BuiltinImporter'>
# code object from /usr/local/lib/python3.11/re/_parser.py
# created '/usr/local/lib/python3.11/re/__pycache__/_parser.cpython-311.pyc'
# code object from /usr/local/lib/python3.11/re/_constants.py
# created '/usr/local/lib/python3.11/re/__pycache__/_constants.cpython-311.pyc'
import 're._constants' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465bb9090>
import 're._parser' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465aef590>
# code object from /usr/local/lib/python3.11/re/_casefix.py
# created '/usr/local/lib/python3.11/re/__pycache__/_casefix.cpython-311.pyc'
import 're._casefix' # <_frozen_importlib_external.SourceFileLoader object at 0x7f34659840d0>
import 're._compiler' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465b68f10>
# code object from /usr/local/lib/python3.11/copyreg.py
# created '/usr/local/lib/python3.11/__pycache__/copyreg.cpython-311.pyc'
import 'copyreg' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465986850>
import 're' # <_frozen_importlib_external.SourceFileLoader object at 0x7f346598e4d0>
# code object from /usr/local/lib/python3.11/token.py
# created '/usr/local/lib/python3.11/__pycache__/token.cpython-311.pyc'
import 'token' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465ac5bd0>
import 'tokenize' # <_frozen_importlib_external.SourceFileLoader object at 0x7f346598fcd0>
import 'linecache' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465acc450>
import 'inspect' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465cf1350>
import 'rlcompleter' # <_frozen_importlib_external.SourceFileLoader object at 0x7f3465ccf850>
🌞Python 3.11.11 (main, Dec  4 2024, 20:38:25) [GCC 12.2.0] on linux🌞
Type "help", "copyright", "credits" or "license" for more information.
>>> 
KeyboardInterrupt
>>> 

```
## 2. Construire une image

Pour construire une image il faut :

- créer un fichier `Dockerfile`
- exécuter une commande `docker build` pour produire une image à partir du `Dockerfile`

🌞 **Ecrire un Dockerfile pour une image qui héberge une application Python**

```bash
nico@debian:~/python_app_build$ cat Dockerfile 
FROM debian
        RUN apt update
        RUN apt install -y python3 python3-pip

        RUN pip install emoji

        COPY app.py

        ENTRYPOINT ["python3", "app.py"]
nico@debian:~/python_app_build$ cat app.py 
import emoji

print(emoji.emojize("Cet exemple d'application est vraiment naze :thumbs_down:"))

nico@debian:~/python_app_build$ 

```
🌞 **Build l'image**

- déplace-toi dans ton répertoire de build `cd python_app_build`
- `docker build . -t python_app:version_de_ouf`
  - le `.` indique le chemin vers le répertoire de build (`.` c'est le dossier actuel)
  - `-t python_app:version_de_ouf` permet de préciser un nom d'image (ou *tag*)
- une fois le build terminé, constater que l'image est dispo avec une commande `docker`

🌞 **Lancer l'image**

- lance l'image avec `docker run` :

```bash
docker run python_app:version_de_ouf
```


# III. Docker compose

Pour la fin de ce TP on va manipuler un peu `docker compose`.

🌞 **Créez un fichier `docker-compose.yml`**

```bash
nico@debian:~$ mkdir compose_test
nico@debian:~$ cd compose_test/
nico@debian:~/compose_test$ sudo vi docker-compose.yml
nico@debian:~/compose_test$ cat docker-compose.yml 
version: "3"

services:
  conteneur_nul:
    image: debian
    entrypoint: sleep 9999
  conteneur_flopesque:
    image: debian
    entrypoint: sleep 9999

nico@debian:~/compose_test$ 
```

🌞 **Lancez les deux conteneurs** avec `docker compose`


```bash
nico@debian:~/compose_test$ docker compose up -d
WARN[0000] /home/nico/compose_test/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion 
[+] Running 3/3
 ✔ Network compose_test_default                  Created                                                                                                                               0.2s 
 ✔ Container compose_test-conteneur_flopesque-1  Started                                                                                                                               0.4s 
 ✔ Container compose_test-conteneur_nul-1        Started                                                                                                                               0.4s 
nico@debian:~/compose_test$ 

```

🌞 **Vérifier que les deux conteneurs tournent**

```bash
nico@debian:~/compose_test$ docker compose ps
WARN[0000] /home/nico/compose_test/docker-compose.yml: the attribute `version` is obsolete, it will be ignored, please remove it to avoid potential confusion 
NAME                                 IMAGE     COMMAND        SERVICE               CREATED              STATUS              PORTS
compose_test-conteneur_flopesque-1   debian    "sleep 9999"   conteneur_flopesque   About a minute ago   Up About a minute   
compose_test-conteneur_nul-1         debian    "sleep 9999"   conteneur_nul         About a minute ago   Up About a minute   
nico@debian:~/compose_test$ 
```

🌞 **Pop un shell dans le conteneur `conteneur_nul`**

```bash
nico@debian:~/compose_test$ docker exec -it compose_test-conteneur_nul-1 bash
root@239e78addb08:/# apt-get update
Get:1 http://deb.debian.org/debian bookworm InRelease [151 kB]
Get:2 http://deb.debian.org/debian bookworm-updates InRelease [55.4 kB]
Get:3 http://deb.debian.org/debian-security bookworm-security InRelease [48.0 kB]
Get:4 http://deb.debian.org/debian bookworm/main amd64 Packages [8789 kB]
Get:5 http://deb.debian.org/debian bookworm-updates/main amd64 Packages [8856 B]
Get:6 http://deb.debian.org/debian-security bookworm-security/main amd64 Packages [216 kB]
Fetched 9268 kB in 4s (2291 kB/s)                    
Reading package lists... Done
root@239e78addb08:/# apt-get install -y iputils-ping
Reading package lists... Done
Building dependency tree... Done
Reading state information... Done
The following additional packages will be installed:
  libcap2-bin libpam-cap
The following NEW packages will be installed:
  iputils-ping libcap2-bin libpam-cap
0 upgraded, 3 newly installed, 0 to remove and 0 not upgraded.
Need to get 96.3 kB of archives.
After this operation, 312 kB of additional disk space will be used.
Get:1 http://deb.debian.org/debian bookworm/main amd64 libcap2-bin amd64 1:2.66-4 [34.7 kB]
Get:2 http://deb.debian.org/debian bookworm/main amd64 iputils-ping amd64 3:20221126-1+deb12u1 [47.2 kB]
Get:3 http://deb.debian.org/debian bookworm/main amd64 libpam-cap amd64 1:2.66-4 [14.5 kB]
Fetched 96.3 kB in 0s (596 kB/s)
debconf: delaying package configuration, since apt-utils is not installed
Selecting previously unselected package libcap2-bin.
(Reading database ... 6089 files and directories currently installed.)
Preparing to unpack .../libcap2-bin_1%3a2.66-4_amd64.deb ...
Unpacking libcap2-bin (1:2.66-4) ...
Selecting previously unselected package iputils-ping.
Preparing to unpack .../iputils-ping_3%3a20221126-1+deb12u1_amd64.deb ...
Unpacking iputils-ping (3:20221126-1+deb12u1) ...
Selecting previously unselected package libpam-cap:amd64.
Preparing to unpack .../libpam-cap_1%3a2.66-4_amd64.deb ...
Unpacking libpam-cap:amd64 (1:2.66-4) ...
Setting up libcap2-bin (1:2.66-4) ...
Setting up libpam-cap:amd64 (1:2.66-4) ...
debconf: unable to initialize frontend: Dialog
debconf: (No usable dialog-like program is installed, so the dialog based frontend cannot be used. at /usr/share/perl5/Debconf/FrontEnd/Dialog.pm line 78.)
debconf: falling back to frontend: Readline
debconf: unable to initialize frontend: Readline
debconf: (Can't locate Term/ReadLine.pm in @INC (you may need to install the Term::ReadLine module) (@INC contains: /etc/perl /usr/local/lib/x86_64-linux-gnu/perl/5.36.0 /usr/local/share/perl/5.36.0 /usr/lib/x86_64-linux-gnu/perl5/5.36 /usr/share/perl5 /usr/lib/x86_64-linux-gnu/perl-base /usr/lib/x86_64-linux-gnu/perl/5.36 /usr/share/perl/5.36 /usr/local/lib/site_perl) at /usr/share/perl5/Debconf/FrontEnd/Readline.pm line 7.)
debconf: falling back to frontend: Teletype
Setting up iputils-ping (3:20221126-1+deb12u1) ...
root@239e78addb08:/# ping conteneur_flopesque
PING conteneur_flopesque (172.18.0.2) 56(84) bytes of data.
64 bytes from compose_test-conteneur_flopesque-1.compose_test_default (172.18.0.2): icmp_seq=1 ttl=64 time=0.118 ms
64 bytes from compose_test-conteneur_flopesque-1.compose_test_default (172.18.0.2): icmp_seq=2 ttl=64 time=0.124 ms
64 bytes from compose_test-conteneur_flopesque-1.compose_test_default (172.18.0.2): icmp_seq=3 ttl=64 time=0.099 ms
64 bytes from compose_test-conteneur_flopesque-1.compose_test_default (172.18.0.2): icmp_seq=4 ttl=64 time=0.118 ms
^C
--- conteneur_flopesque ping statistics ---
4 packets transmitted, 4 received, 0% packet loss, time 3004ms
rtt min/avg/max/mdev = 0.099/0.114/0.124/0.009 ms
root@239e78addb08:/# 

```