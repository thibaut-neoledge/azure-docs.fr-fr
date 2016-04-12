<properties
	pageTitle="Utiliser des clés SSH sous Linux et Mac | Microsoft Azure"
	description="Générez et utilisez des clés SSH sous Linux et Mac pour les modèles de déploiement du gestionnaire de ressources et classiques sur Azure."
	services="virtual-machines-linux"
	documentationCenter=""
	authors="vlivech"
	manager="timlt"
	editor=""
	tags="" />

<tags
	ms.service="virtual-machines-linux"
	ms.workload="infrastructure-services"
	ms.tgt_pltfrm="vm-linux"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="04/04/2016"
	ms.author="v-livech"/>

# Créer des clés SSH sur Linux et Mac pour les machines virtuelles Linux dans Azure

Pour créer des clés privées et publiques SSH protégées par mot de passe, vous avez besoin de [l’interface de ligne de commande Azure](../xplat-cli-install.md) en mode Gestionnaire de ressources (`azure config mode arm`).

## Liste des commandes rapides

Dans les exemples de commandes suivants, remplacez les valeurs entre &lt; et &gt; par les valeurs de votre propre environnement.

```bash
[chrisL@fedora ~]$ ssh-keygen -t rsa -b 2048 -C "<your_user@yourdomain.com>"

#Enter the name of the file that will be saved in the `~/.ssh/` directory.
azure_fedora_id_rsa

#Enter (twice) a [secure](https://www.xkcd.com/936/) password for the SSH key.

#Enter passphrase for github_id_rsa:
correct horse battery staple

#Add the newly created key to `ssh-agent` on Linux and Mac (also added to OSX Keychain).
[chrisL@fedora ~]$ eval "$(ssh-agent -s)"
[chrisL@fedora ~]$ ssh-add ~/.ssh/azure_fedora_id_rsa

#Copy the SSH public key to your Linux Server.
[chrisL@fedora ~]$ ssh-copy-id -i ~/.ssh/azure_fedora_id_rsa.pub <youruser@yourserver.com>

#Test the login using keys instead of a password.
[chrisL@fedora ~]$ ssh -i ~/.ssh/azure_fedora_id_rsa <youruser@yourserver.com>

Last login: Tue Dec 29 07:07:09 2015 from 66.215.21.201
[chrisL@fedora ~]$

```

## Introduction

L’utilisation de clés privées et publiques SSH est le moyen le plus sûr **et** le plus simple de vous connecter à vos serveurs Linux. En outre, le [chiffrement à clé publique](https://en.wikipedia.org/wiki/Public-key_cryptography) est une méthode plus sécurisée que les mots de passe pour vous connecter à vos machines virtuelles Linux ou BSD dans Azure, puisque ceux-ci sont plus facilement sujets à des attaques par force brute. Votre clé publique peut être partagée avec n’importe qui, mais vous seul (ou votre infrastructure de sécurité locale) possédez votre clé privée.

Cet article aborde la création de fichiers de clé au format *ssh-rsa*, car ceux-ci sont recommandés pour le déploiement sur le gestionnaire de ressources et sont requis sur le [portail](https://portal.azure.com) pour le déploiement classique, ainsi que sur le gestionnaire de ressources.


## Créer les clés SSH

Azure requiert des clés publiques et privées d’au moins 2 048 bits au format ssh-rsa. Utilisez `ssh-keygen` pour créer la paire. Cette fonction pose une série de questions, puis écrit une clé privée et une clé publique correspondante. Lorsque vous créez votre machine virtuelle Azure, vous transmettez le contenu de la clé publique, qui est ensuite copié dans la machine virtuelle Linux, puis utilisé avec votre clé privée locale (stockée de manière sécurisée) pour vous authentifier lorsque vous vous connectez.

### Utilisation de `ssh-keygen`

Cette commande crée une paire de clés SSH à l’aide d’un RSA de 2048 bits, qui fera l’objet d’un commentaire pour faciliter son identification.

```
chrisL@fedora$ ssh-keygen -t rsa -b 2048 -C "username@fedoraVMAzure"
```

##### Explication des commandes

`ssh-keygen` = programme utilisé pour créer les clés

`-t rsa` = type de clé à créer [le format RSA] (https://en.wikipedia.org/wiki/RSA_(cryptosystem))

`-b 2048` = bits de la clé

`-C "username@fedoraVMAzure"` = commentaire pour la clé afin de faciliter son identification. Le commentaire est ajouté à la fin du fichier de la clé publique. Un commentaire se fait souvent sous la forme d’adresse e-mail. Cependant, dans le cadre de cet article, l’utilisation de plusieurs clés SSH a été autorisée ; un commentaire générique conviendrait mieux dans ce cas.

#### Procédure pas à pas pour `ssh-keygen`

```bash
chrisL@fedora$ ssh-keygen -t rsa -b 2048 -C "username@fedoraVMAzure"
Generating public/private rsa key pair.
Enter file in which to save the key (/Users/steve/.ssh/id_rsa): azure_fedora_id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in azure_fedora_id_rsa.
Your public key has been saved in azure_fedora_id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:79:ad:25:cc:65:e9:0c:07:e5:d1:a9:08 username@fedoraVMAzure
The key's randomart image is:
+--[ RSA 4096]----+
|        o o. .   |
|      E. = .o    |
|      ..o...     |
|     . o....     |
|      o S =      |
|     . + O       |
|      + = =      |
|       o +       |
|        .        |
+-----------------+

chrisL@fedora$ ls -al ~/.ssh
-rw------- 1 username staff  1675 Aug 25 18:04 azure_fedora_id_rsa
-rw-r--r-- 1 username staff   410 Aug 25 18:04 azure_fedora_id_rsa.pub
```

`Enter file in which to save the key (/Users/steve/.ssh/id_rsa): azure_fedora_id_rsa` Nom de la paire de clés pour cet article. Une paire de clés est nommée **id\_rsa** par défaut ; certains outils s’attendant au nom de fichier de clé privée **id\_rsa**, il convient d’en avoir un à disposition. (`~/.ssh/` est l’emplacement par défaut pour toutes vos paires de clés SSH et le fichier de configuration SSH.)

`Enter passphrase (empty for no passphrase):` Il est fortement recommandé d’ajouter un mot de passe (« phrase secrète » pour `ssh-keygen`) à vos paires de clés. Sans mot de passe protégeant la paire de clés, toute personne disposant d’une copie du fichier de clé privée peut l’utiliser pour se connecter à n’importe quel serveur (le vôtre) disposant de la clé publique correspondante. L’ajout d’un mot de passe offre donc beaucoup plus de protection au cas où un utilisateur malveillant réussit à accéder à votre fichier de clé privée ; vous avez ainsi plus de temps pour modifier les clés utilisées pour vous authentifier.

`chrisL@fedora$ ls -al ~/.ssh` Cela affiche vos nouvelles paires de clés et leurs autorisations. `ssh-keygen` crée le répertoire `~/.ssh` si celui-ci est absent et définit également la propriété et les modes de fichier appropriés.

## Créer et configurer un fichier de configuration SSH

Bien que facultatif pour qu’une machine virtuelle Linux soit opérationnelle, il est recommandé de créer et de configurer un fichier `~/.ssh/config` afin d’éviter une utilisation accidentelle des mots de passe pour vous connecter à vos machines virtuelles, d’automatiser à l’aide de paires de clés distinctes pour différentes machines virtuelles Azure et de configurer d’autres programmes tels que **git** pour cibler plusieurs serveurs.

L’exemple suivant illustre une configuration standard.

### Créer le fichier

```bash
chrisL@fedora$ touch ~/.ssh/config
```

### Modifier le fichier pour ajouter la nouvelle configuration SSH

```bash
chrisL@fedora$ vim ~/.ssh/config

#Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User username
  PubkeyAuthentication yes
  IdentityFile /Users/steve/.ssh/azure_fedora_id_rsa
# ./Azure Keys
# GitHub keys
Host github.com
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /Users/steve/.ssh/github_id_rsa
Host github.private
  Hostname github.com
  User git
  PubKeyAuthentication yes
  IdentityFile /Users/steve/.ssh/private_repo_github_id_rsa
# ./Github Keys
# Default Settings
Host *
  PubkeyAuthentication=no
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath /Users/steve/.ssh/Connections/ssh-%r@%h:%p
  ControlPersist 4h
  StrictHostKeyChecking=no
  IdentityFile /Users/steve/.ssh/id_rsa
  UseRoaming=no
```

Cette configuration SSH propose des sections pour chaque service, permettant à chacun d’avoir sa propre paire de clés dédiée. Les paramètres par défaut sont destinés à tous les hôtes auxquels vous êtes connecté qui ne correspondent à aucun des groupes ci-dessus. La configuration SSH vous permet également de disposer de deux connexions [GitHub](https://github.com) distinctes, l’une pour le travail public et l’autre pour les référentiels privés pouvant être présents sur votre lieu de travail.


##### Explication du fichier de configuration

`Host` = le nom de l’hôte qui est appelé sur le terminal. `ssh fedora22` indique à `SSH` d’utiliser les valeurs dans le bloc de paramètres intitulé `Host fedora22` REMARQUE : il peut s’agir de toute étiquette logique pour votre utilisation et cela ne représente pas le nom d’hôte réel d’un serveur quelconque.

`Hostname 102.160.203.241` = l’adresse IP ou le nom DNS du serveur auquel vous vous connectez. Cet élément est utilisé pour acheminer un appel vers le serveur et peut être une adresse IP externe mappant vers une adresse IP interne.

`User git` = le compte d’utilisateur distant à utiliser lors de la connexion à la machine virtuelle Azure.

`PubKeyAuthentication yes` = indique au SSH que vous souhaitez utiliser une clé SSH pour la connexion.

`IdentityFile /Users/steve/.ssh/azure_fedora_id_rsa` = indique au SSH quelle paire de clés présenter au serveur pour authentifier la connexion.


## Connexion SSH à une machine virtuelle Linux sans mot de passe

Maintenant que vous disposez d’une paire de clés SSH et d’un fichier de configuration SSH configuré, vous pouvez vous connecter à la machine virtuelle Linux rapidement et en toute sécurité. Lors de la première connexion à un serveur à l’aide d’une clé SSH, l’invite de commandes vous demande de saisir la phrase secrète pour le fichier de clé en question.

`chrisL@fedora$ ssh fedora22`

##### Explication des commandes

Lorsque `chrisL@fedora$ ssh fedora22` est exécuté, le SSH commence par localiser et par charger tous les paramètres à partir du bloc `Host fedora22`, puis charge tous les paramètres restants à partir du dernier bloc `Host *`.

## Étapes suivantes

Vous pouvez à présent utiliser vos fichiers de clés pour [créer une machine virtuelle Linux sécurisée dans Azure à l’aide d’un modèle](virtual-machines-linux-create-ssh-secured-vm-from-template.md).

<!---HONumber=AcomDC_0406_2016-->