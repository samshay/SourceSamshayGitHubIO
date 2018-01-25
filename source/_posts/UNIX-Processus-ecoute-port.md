---
title: UNIX - Lister les ports utilisés et par quel processus
date: 2018-01-25 10:57:28
tags:
---

La commande `netstat` permet de lister les ports utilisés et par quel processus.
Pratique quand on veut savoir si un port est utilisé et surtout par qui.

```sh
netstat -ltunp
```

Le résultat :
```
Proto Recv-Q Send-Q Adresse locale          Adresse distante        Etat       PID/Program name
tcp        0      0 0.0.0.0:57619           0.0.0.0:*               LISTEN      -               
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      -               
tcp        0      0 127.0.0.1:631           0.0.0.0:*               LISTEN      -               
tcp        0      0 127.0.0.1:9060          0.0.0.0:*               LISTEN      30136/node      
tcp        0      0 0.0.0.0:111             0.0.0.0:*               LISTEN      -               
tcp6       0      0 :::8080                 :::*                    LISTEN      11258/java      

tcp6       0      0 :::9000                 :::*                    LISTEN      30136/node      
tcp6       0      0 :::111                  :::*                    LISTEN      -               
udp        0      0 0.0.0.0:5353            0.0.0.0:*                           7694/index.ts   

```

> Attention : Dans certain cas il faut lancer la commande en `root` si vous voulez avoir tous les ports.