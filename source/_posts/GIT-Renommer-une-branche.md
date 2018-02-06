---
title: GIT-Renommer une branche
date: 2018-02-06 18:04:14
tags:
---

Ci-dessous les commandes pour renommer une branche avec git :

```sh
#Renommage de la branche
git branch -m maBranche maNouvelleBranche

#Suppression de l ancienne branche sur le serveur
git push origin :maBranche

#Push de la branche renommée sur le serveur
git push origin maNouvelleBranche
```
