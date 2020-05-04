---
title: Raspberry-Sauvegarde SD card
date: 2020-04-19 21:45:03
tags:
---

J'ai décidé d'effectuer une sauvegarde de la carte SD du raspberry sur le NAS Synology.  
En effet, les cartes SD ne sont pas le support de stockage le plus fiable qui existe.  

# 1 - Création d'un partage NFS sur le NAS : 
Activer le partage NFS :

{% asset_img RB-activerNFS.png %}

Créer un répertoire partagé sur le NAS et activer le partage NFS.
Ici, j'autorise toutes les adresses IP de mon sous-réseau 192.168.1.10/24
{% asset_img RB-configNFS.png %}

# 2 - Ajouter le partage NFS sur le raspberry

- Créer un répertoire sous /mnt : `mkdir /mnt/back_nas`
- Modifier le fichier fstab : `sudo nano /etc/fstab`
    - Ajouter la ligne : `IP_NAS:/volume1/REP_PARTAGE_NAS /mnt/back_nas nfs     rw,user,exec,addr=IP_NAS,nolock 0       0`
- Monter le partage : `sudo mount -a`
- Veuillez à donner les bons droits au répertoire `/mnt/back_nas` pour pouvoir y accéder et écrire

# 3 - Création du script de sauvegarde

J'ai créé le script dans le répertoire de partage du NAS :
``` sh
#!/bin/bash
LOGFILE=/mnt/back_nas/backup.log
REP_NAS=/mnt/back_nas
DATE_ET_HEURE=$(date)
DATE=$(date +"%Y-%m-%d")
BoxToClone=$(uname -n)
echo $DATE_ET_HEURE Debut de la sauvegarde de $BoxToClone >> $LOGFILE
FileName=SD-Backup_$BoxToClone_$DATE.img
File=$REP_NAS/$FileName
sudo dd if=/dev/mmcblk0 bs=4M | sudo gzip -1 -| sudo dd of=$File

#Suppression  image de plus de 7 jours
find $REP_NAS/ -name "*.img" -type f -mtime +7 -exec rm -f {} 

echo $DATE_ET_HEURE Fin sauvegarde >> $LOGFILE
```

Ce script permet de créer la sauvegarde dans un fichier qui se nommera par exemple `SD-Backup_2020-04-19.img` et supprime les sauvegardes de plus de 7 jours.


# 4 - Exécution automatique du script

- Modifier crontab : `sudo crontab -e -u root`
- Ajouter cette ligne pour l'éxécuter à 3 heures tout les lundi `00 03 * * 1 sh /mnt/back_nas/script.sh`

Inspiration : 
https://ouiaremakers.com/posts/tutoriel-diy-sauver-les-framboises
https://domopi.eu/sauvegarde-de-la-carte-sd-du-raspberry-pi-sur-un-serveur-externe/