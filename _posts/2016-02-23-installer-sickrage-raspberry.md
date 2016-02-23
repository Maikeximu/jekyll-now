---
layout: post
title: "Installer Sickrage sur un Raspberry Pi"
published: true
categories:
-raspberry-pi
---


# Installer Sickrage sur un Raspberry Pi

Sickrage vous permet d'automatiser le téléchargement de vos séries préférées. Voici comment procéder. Cela vous permettra d'utiliser au mieux votre Raspberry afin qu'il automatise au maximum les tâches répétitives. Ce tutoriel, couplé à un Media Center de type OSMC/OpenElec vous permettra d'intégrer vos séries à votre MediaCenter.

## Prérequis

- une distribution permettant de vous connecter en SSH
- une distribution de type debian (Raspbian). Pour Openelec, le fonctionnement diffère puisque vous ne pouvez pas directement utiliser des commandes debian. 

## Installation de Transmission sur un Raspberry Pi

Note : Sur OSMC, vous pouvez installer Transmission simplement en vous rendant sur OSMC Config dans le menu Program -> AppStore -> Tranmission Client -> Install.

Transmission est un client Torrent  P2P permettant de télécharger des fichiers. De manière classique, il faudra vous connecter à votre Raspberry en SSH puis mettre à jour vos paquets.

`sudo apt-get update`

Nous installons ensuite le client Transmission.

`sudo apt-get install transmission-daemon`

Afin de paramétrer Transmission, il faut stopper son fonctionnement en tant que service.

`sudo /etc/init.d/transmission-daemon stop`

### Configuration de Transmission

Il faut indiquer à Tranmission plusieurs choses comme les dossiers dans lesquels il téléchargera ou encore l'accès à l'interface web (par défaut localhost:9091). Pour cela, il faut modifier le fichier settings.json.

`sudo nano /etc/transmission-daemon/settings.json`

Editez les éléments suivants (généralement /pi sera votre point d'entrée). Changez le en fonction des spécificités de votre installation :

- `“download-dir”: “/pi/downloads”` -> où vos téléchargements complets seront stockés,
- `“incomplete-dir”: “/pi/incomplete”` -> pour les fichiers incomplets,
- `“incomplete-dir-enabled”:  true` -> pour activer le dossier pour les fichiers incomplets,
- `“rpc-password”: “Mot de passe”` -> un mot de passe afin d'accéder à l'administration web de Transmission - ce mot de passe sera stocké en crypté lorsque vous enregistrerez le fichier,
- `“rpc-username”: “Utilisateur”` -> un login pour accéder à Tranmission,

Quittez le fichier avec la commande `CTRL+X`puis `y`lorsqu'on vous demande si vous souhaitez sauvegarder les modifications.

Autorisez ensuite l'utilisateur debian-transmission à écrire/lire/modifier vos fichiers téléchargés afin d'éviter les conflits qui pourraient survenir.

`sudo usermod -a -G osmc debian-transmission`

Redémarrez maintenant le service Transmission grâce à la commande suivante :

`sudo /etc/init.d/transmission-daemon start``

Normalement, vous pouvez accéder, à l'aide des identifiants renseignés ci-dessus  à l'interface web de Transmission (en local pour le moment) à l'adresse suivante : http://IP.DU.RASPBERRY:9091/

## Installation de Sickrage sur un Raspberry

Rendez-vous, toujours en ligne de commande à la racine de l'utilisateur du Raspberry :

`cd /home/pi` (ou `cd /home/osmc` si vous utilisez osmc)

Installez ensuite git qui vous permet de récupérer sans problème des repetoires (repo)  stockés sur github.

`sudo apt-get install git``

Clonez ensuite le repo Sickrage :

`sudo git clone https://github.com/SickRage/SickRage.git sickbeard`

Installez ensuite les principales dépendances qui permettent de lancer Sickrage :

`sudo apt-get install python-cheetah`

Exécutez ensuite le script de lancement de Sickrage.

`sudo python /sickbeard/SickBeard.py -d``

Vérifions maintenant le Sickrage s'est bien exécuté en vous rendant à l'adresse :
http://IP.DU.RASPBERRY:8081/

SI tout fonctionne, nous devons maintenant le lancer à chaque démarrage en tant que service grâce à ces quelques commandes modifiant le fichier `rc.local`.

sudo nano /etc/rc.local

Ajoutez la ligne suivante au dessus de `exit 0`qui interrompt le fichier.

`sudo python /home/pi/sickbeard/SickBeard.py -d`

## Synchronisation de Sickrage avec vos client Bittorrent

Vous devez configurer Sickrage en fonction de votre client de téléchargement. Renseignez bien les données correspondant par exemple à Transmission (adresse IP du Raspberry ou localhost:9091 et les identifiants choisis pour accéder à l'interface de Transmission).
