---
title: Travis - Deploiement SSH
date: 2018-02-15 09:36:45
tags:
---

Pour mon projet de domotique qui est exécuté sur mon RaspberryPi, j'ai eu envie de pousser le déploiement encore plus loin et d'utiliser [TRAVIS](https://travis-ci.org/).

Le but étant de :
- Faire un build du projet à partir des sources hébergés sous GitHub.
- Déployer automatiquement le war sur le raspberry via ssh.


# Création clé SSH

> Attention : ne pas mettre de mot de passe.

```sh
ssh-keygen -t rsa -b 4096 -C 'build@travis-ci.org' -f ./deploy_rsa
```

# Chiffrement de la clé publique
Au préalable il faut installer le client console de Travis : [Installation](https://github.com/travis-ci/travis.rb#installation)

Pour cette étape, j'ai copié le fichier `deploy_rsa` au même niveau que mon fichier `.travis.yml`
Ensuite j'ai exécuté la commande pour initialiser la connexion avec Travis
```sh
travis login
```

Ensuite la commande suivante va nous permettre de chiffrer la clé publique `deploy_rsa` - fichier `deploy_rsa.enc` et de la stocker sous Git.
Attention à supprimer le fichier `deploy_rsa` et de ne pas commiter.
```sh
travis encrypt-file deploy_rsa --add
```
Votre fichier travis a aussi été modifié, la ligne suivante a été ajoutée :
```yml
before_install: 
- openssl aes-256-cbc -K $encrypted_xxxxxx_key -iv $encrypted_xxxxxxx_iv -in deploy_rsa.enc -out /tmp/deploy_rsa -d
```

Vous pouvez dès maintenant déplacer la ligne avec `openssl` et la copier comme cela :
```yml
before_deploy: 
- openssl aes-256-cbc -K $encrypted_xxxxxx_key -iv $encrypted_xxxxxxx_iv -in deploy_rsa.enc -out /tmp/deploy_rsa -d
- eval "$(ssh-agent -s)" 
- chmod 600 /tmp/deploy_rsa 
- ssh-add /tmp/deploy_rsa 
```
Cette configuration permet de decrypter au sein de Travis le fichier `deploy_rsa` et d'ajouter cette clé publique ssh à Travis pour ensuite se connecter en ssh.

# Configuration du déploiement ssh
Vu qu'on est en train de modifier le fichier `.travis.yml`, on va également ajouter cela :
```yml
deploy:
  provider: script
  skip_cleanup: true
  script: rsync -r --delete-after --quiet $TRAVIS_BUILD_DIR/target/*.war $ssh_user@$ssh_deploy_host:$ssh_dir
  on:
    branch: master
```
Ici, on vient copier le fichier avec `rsync` directement sur le serveur distant.
Pour info, j'ai ajouté des variable d'environnement au job :
{% asset_img Travis-EnvironmentVariables.png %}

Vous pouvez dès maintenant pusher le fichier `deploy_rsa.enc` et `.travis.yml` sous GIT. Vous ne devez pusher rien d'autre.


# Ajout de la clé privée sur le serveur

Pour ma part, j'ai directement copié cette clé (`fichier deploy_rsa.pub` ) sur mon serveur et exécuté la commande suivante :

```ssh
ssh-copy-id -i deploy_rsa.pub <ssh-user>@<deploy-host>
#Suppression fichier deploy_rsa.pub
rm -rf deploy_rsa.pub
```

# Fin

Voila c'est tout, une fois votre job TRAVIS exécuté, votre fichier war sera directement installé sur votre serveur.

[Projet GIT de domotique](https://github.com/samshay/DomotiquePleumeleuc)
