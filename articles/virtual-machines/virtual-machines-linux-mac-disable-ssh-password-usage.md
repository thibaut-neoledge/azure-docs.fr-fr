---
title: Désactiver les mots de passe SSH sur votre machine virtuelle Linux en configurant SSHD | Microsoft Docs
description: Sécurisez votre machine virtuelle Linux sur Azure en désactivant les connexions par mot de passe pour SSH.
services: virtual-machines-linux
documentationcenter: ''
author: vlivech
manager: timlt
editor: ''
tags: ''

ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/26/2016
ms.author: v-livech

---
# Désactiver les mots de passe SSH sur votre machine virtuelle Linux en configurant SSHD
Cet article traite du verrouillage de la sécurité de connexion de votre machine virtuelle Linux. Dès que le port SSH 22 est ouvert, les robots tentent de se connecter en devinant le mot de passe. Cet article décrit comment désactiver les connexions par mot de passe sur SSH. En supprimant complètement la possibilité d’utiliser des mots de passe, nous protégeons la machine virtuelle Linux contre ce type d’attaque par force brute. L’avantage supplémentaire est que nous configurons le SSHD Linux pour qu’il n’autorise que les connexions via des clés publiques et privées SSH, qui est de loin la méthode la plus sûre pour se connecter à Linux. Le nombre des différentes combinaisons possibles permettant de deviner la clé privée est immense et par conséquent décourage les robots de seulement essayer d’attaquer les clés SSH par force brute.

## Objectifs
* Configurer SSHD pour interdire ce qui suit :
  * Connexions par mot de passe
  * Connexion de l’utilisateur racine
  * Authentification par stimulation/réponse
* Configurer SSHD pour autoriser ce qui suit :
  * Uniquement les connexions par clés SSH
* Redémarrage de SSHD quand la session est encore ouverte
* Test de la nouvelle configuration SSHD

## Introduction
[Définition de SSH](https://en.wikipedia.org/wiki/Secure_Shell)

SSHD est le serveur SSH qui s’exécute sur la machine virtuelle Linux. SSH est un client qui s’exécute à partir d’un interpréteur de commandes sur votre poste de travail MacBook ou Linux. SSH est également le protocole utilisé pour sécuriser et chiffrer les communications entre votre poste de travail et la machine virtuelle Linux.

Dans le cadre de cet article, il est très important que la connexion à votre machine virtuelle Linux soit ouverte durant toutes les étapes. Pour cette raison, nous allons ouvrir deux terminaux et exécuter SSH vers la machine virtuelle Linux à partir de chacun d’eux. Nous utilisons le premier terminal pour modifier le fichier de configuration SSHD et redémarrer le service SSHD. Nous utilisons le deuxième terminal pour tester ces modifications une fois que le service est redémarré. Étant donné que nous désactivons les mots de passe SSH et que nous nous appuyons uniquement sur les clés SSH, si ces dernières sont incorrectes et que vous fermez la connexion à la machine virtuelle, celle-ci sera définitivement verrouillée et personne ne pourra se connecter à moins de la supprimer et de la recréer.

## Composants requis
* [Créer des clés SSH sur Linux et Mac pour les machines virtuelles Linux dans Azure](virtual-machines-linux-mac-create-ssh-keys.md)
* Compte Azure
  * [Version d’évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/)
  * [Portail Azure](http://portal.azure.com)
* Machine virtuelle Linux s’exécutant sur Azure
* Paire de clés publique et privée SSH dans `~/.ssh/`
* Clé publique SSH dans `~/.ssh/authorized_keys` sur la machine virtuelle Linux
* Droits sudo sur la machine virtuelle
* Port 22 ouvert

## Commandes rapides
*Les administrateurs Linux chevronnés souhaitant uniquement la version TLDR commencent ici. Les utilisateurs qui souhaitent une explication détaillée et une procédure pas à pas peuvent ignorer cette section.*

```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes

# Change PermitRootLogin to this:
PermitRootLogin no

# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no

# On the Debian Family
username@macbook$ sudo service ssh restart

# On the RedHat Family
username@macbook$ sudo service sshd restart
```

## Procédure pas à pas détaillée
Connexion à la machine virtuelle Linux sur le terminal 1 (T1). Connexion à la machine virtuelle Linux sur le terminal 2 (T2).

Dans T2, nous modifions le fichier de configuration SSHD.

```
username@macbook$ sudo vim /etc/ssh/sshd_config
```

Ensuite, nous modifions seulement les paramètres pour désactiver les mots de passe et activer les connexions par clé SSH. Vous pouvez rechercher et modifier de nombreux paramètres dans ce fichier pour sécuriser Linux et SSH selon vos besoins.

#### Désactiver les connexions par mot de passe
```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PasswordAuthentication to this:
PasswordAuthentication no
```

#### Activer l’authentification par clé publique
```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PubkeyAuthentication to this:
PubkeyAuthentication yes
```

#### Désactiver la connexion racine
```
username@macbook$ sudo vim /etc/ssh/sshd_config

# Change PermitRootLogin to this:
PermitRootLogin no
```

#### Désactiver l’authentification par stimulation/réponse
```
# Change ChallengeResponseAuthentication to this:
ChallengeResponseAuthentication no
```

### Redémarrer SSHD
Dans l’interpréteur de commandes de T1, vérifiez que vous êtes toujours connecté. Cela est essentiel pour continuer à avoir accès à votre machine virtuelle si vos clés SSH ne sont pas correctes, car les mots de passe sont désormais désactivés. Si des paramètres sont incorrects sur votre machine virtuelle Linux, vous pouvez corriger sshd\_config à partir de T1, car vous êtes toujours connecté et que SSH maintient la connexion active pendant le redémarrage du service SSHD.

À partir de T2, effectuez l’exécution :

##### Pour la famille Debian
```
username@macbook$ sudo service ssh restart
```

##### Pour la famille RedHat
```
username@macbook$ sudo service sshd restart
```

Les mots de passe sont désormais désactivés sur votre machine virtuelle, ce qui la protège contre les tentatives d’attaque par force brute des connexions par mot de passe. Quand seules les clés SSH sont autorisées, vous pouvez vous connecter de façon plus rapide et beaucoup plus sûre.

<!---HONumber=AcomDC_0831_2016-->