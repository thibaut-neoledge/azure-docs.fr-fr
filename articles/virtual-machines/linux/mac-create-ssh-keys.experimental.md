---
title: "Créer une paire de clés SSH pour les machines virtuelles Linux dans Azure | Microsoft Docs"
description: "Créez une paire de clés publique et privée SSH en toute sécurité pour les machines virtuelles Azure Linux."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: 
ms.assetid: 34ae9482-da3e-4b2d-9d0d-9d672aa42498
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: get-started-article
ms.date: 03/08/2017
ms.author: rasquill
experiment_id: rasquill-ssh-20170308
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 19acd4efca7ef043f31b436b96f9129caee9591b
ms.lasthandoff: 04/03/2017



---

# <a name="create-an-ssh-public-and-private-key-pair-for-linux-vms"></a>Créer une paire de clés publique et privée SSH pour les machines virtuelles Linux

Cet article vous indique comment générer une paire de clés publique et privée SSH RSA de version de protocole 2 à utiliser avec des machines virtuelles Linux.  Avec une paire de clés SSH, vous pouvez créer des machines virtuelles sur Azure qui utilisent par défaut des clés SSH pour l’authentification, éliminant ainsi la nécessité de recourir aux mots de passe pour la connexion.  Les mots de passe sont vulnérables et peuvent exposer vos machines virtuelles à de constantes attaques par force brute visant à découvrir ces mots de passe. Les machines virtuelles créées avec des modèles Azure ou `azure-cli` peuvent inclure votre clé publique SSH dans le cadre du déploiement, éliminant ainsi l’étape de configuration post-déploiement qui consiste à désactiver les connexions par mot de passe pour SSH.

## <a name="quick-commands"></a>Commandes rapides

Exécutez les commandes ci-après à partir d’un shell Bash en remplaçant les exemples par vos propres choix.

Votre fichier de clé publique SSH est créé par défaut dans `~/.ssh/id_rsa.pub`. Lorsque vous y êtes invité, à l’aide de la commande suivante, créez une « phrase secrète » pour sécuriser votre clé privée. (La phrase secrète est un mot de passe utilisé pour chiffrer votre clé privée.)

```bash
ssh-keygen -t rsa -b 2048 
```

Ajoutez la clé nouvellement créée à `ssh-agent` :

```bash
ssh-add ~/.ssh/id_rsa
```

> [!IMPORTANT] 
> Les commandes ci-dessus fonctionnent sur les systèmes d’exploitation Linux de presque toutes les distributions, mais ne fonctionnent pas nécessairement dans des conteneurs, car l’environnement peut être radicalement limité. 

## <a name="detailed-walkthrough"></a>Procédure pas à pas

L’utilisation de clés publiques et privées SSH constitue le moyen le plus simple pour vous connecter à vos serveurs Linux. Le [chiffrement par clé publique](https://en.wikipedia.org/wiki/Public-key_cryptography) est un moyen bien plus sécurisé que les mots de passe pour vous connecter à votre machine virtuelle Linux ou BSD dans Azure. Les mots de passe sont beaucoup plus exposés aux attaques par force brute.

> [!IMPORTANT]
> Votre clé publique peut être partagée avec n’importe qui, mais vous seul (ou votre infrastructure de sécurité locale) possédez votre clé privée.  La clé privée SSH doit être protégée par un [mot de passe très sécurisé](https://www.xkcd.com/936/) (source : [xkcd.com](https://xkcd.com)).  Ce mot de passe permet simplement d’accéder à la clé SSH privée et **n’est pas** le mot de passe du compte utilisateur.  Lorsque vous ajoutez un mot de passe à votre clé SSH, il chiffre celle-ci à l’aide d’une clé AES 128 bits, la rendant inutilisable sans le mot de passe qui permet de la déchiffrer.  Si un attaquant parvient à voler votre clé privée, et que celle-ci n’a pas de mot de passe, il peut l’utiliser pour se connecter aux serveurs contenant la clé publique correspondante.  En revanche, si la clé privée est protégée par un mot de passe, cette dernière ne peut pas être utilisée par l’attaquant. Le mot de passe fournit ainsi une couche supplémentaire de sécurité pour votre infrastructure sur Azure.

Cet article crée des fichiers de clés publique et privée SSH RSA de version de protocole 2, qui sont recommandés pour les déploiements sur Resource Manager.  Les clés *ssh-rsa* sont nécessaires sur le [portail](https://portal.azure.com) pour les déploiements Classic et Resource Manager.

## <a name="disable-ssh-passwords-by-using-ssh-keys"></a>Désactiver les mots de passe SSH à l’aide des clés SSH

Azure requiert des clés publiques et privées d’au moins 2 048 bits au format ssh-rsa. Utilisez `ssh-keygen` pour créer les clés. Cette fonction pose une série de questions, puis génère une clé privée et une clé publique correspondante. Lorsqu’une machine virtuelle Azure est créée, la clé publique est copiée dans `~/.ssh/authorized_keys`.  Les clés SSH dans `~/.ssh/authorized_keys` servent à demander au client la clé privée correspondante sur une connexion SSH.  Lorsqu’une machine virtuelle Linux Azure est créée à l’aide de clés SSH pour l’authentification, Azure configure le serveur SSHD de façon à ne pas autoriser les connexions par mot de passe, mais uniquement les clés SSH.  Par conséquent, en créant des machines virtuelles Linux Azure avec des clés SSH, vous pouvez contribuer à sécuriser le déploiement de machines virtuelles et éviter l’étape de configuration classique après le déploiement qui consiste à désactiver les mots de passe dans le fichier de configuration sshd_config.

## <a name="using-ssh-keygen"></a>Utilisation de ssh-keygen

Cette commande crée une paire de clés SSH (chiffrées) protégées par un mot de passe à l’aide du protocole RSA 2 048 bits. Elle fait l’objet d’un commentaire pour faciliter son identification.  

Par défaut, les clés SSH sont conservées dans le `~/.ssh`répertoire.  Si vous n’avez pas de répertoire `~/.ssh`, la commande `ssh-keygen` en crée un pour vous avec les autorisations appropriées.

```bash
ssh-keygen \
-t rsa \
-b 2048 
```

*Explication des commandes*

`ssh-keygen` = programme utilisé pour créer les clés

`-t rsa` = type de clé à créer qui est au format RSA [wikipédia] (https://fr.wikipedia.org/wiki/Chiffrement_RSA)

`-b 2048` = bits de la clé


## <a name="classic-portal-and-x509-certs"></a>Portail Classic et certificats X.509

Si vous utilisez le [portail Azure Classic](https://manage.windowsazure.com/), des certificats X.509 sont requis pour les clés SSH.  Aucun autre type de clé publique SSH n’est autorisé, les certificats X.509 sont *obligatoires*.

Pour créer un certificat X.509 à partir de votre clé privée SSH-RSA existante :

```bash
openssl req -x509 \
-key ~/.ssh/id_rsa \
-nodes \
-days 365 \
-newkey rsa:2048 \
-out ~/.ssh/id_rsa.pem
```

## <a name="classic-deploy-using-asm"></a>Déploiement Classic à l’aide de `asm`

Si vous utilisez le modèle de déploiement Classic (CLI de gestion des services Azure `asm`), vous pouvez utiliser une clé publique SSH-RSA ou une clé au format RFC4716 dans un conteneur **.pem**.  La clé publique SSH-RSA est l’élément créé précédemment dans cet article à l’aide de `ssh-keygen`.

Pour créer une clé au format RFC4716 à partir d’une clé publique SSH existante, procédez comme suit :

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>Exemple de ssh-keygen

```bash
ssh-keygen -t rsa -b 2048 -C "ahmet@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): 
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/ahmet/.ssh/id_rsa.
Your public key has been saved in /home/ahmet/.ssh/id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 ahmet@myserver
The keys randomart image is:
+--[ RSA 2048]----+
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
```

Fichiers de clés enregistrés :

`Enter file in which to save the key (/home/ahmet/.ssh/id_rsa): ~/.ssh/id_rsa`

Nom de la paire de clés dans cet article.  Le nom par défaut de la paire de clés est **id_rsa**. Certains outils pouvant demander le nom du fichier de clé privée **id_rsa**, il est utile d’en avoir un. Le répertoire `~/.ssh/` est l’emplacement par défaut des paires de clés SSH et du fichier de configuration SSH.  Si un chemin d’accès complet n’est pas spécifié, `ssh-keygen` va créer les clés dans le répertoire de travail actuel, et non dans le répertoire par défaut `~/.ssh`.

Le contenu du répertoire `~/.ssh` s’affiche.

```bash
ls -al ~/.ssh
-rw------- 1 ahmet staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 ahmet staff   410 Aug 25 18:04 rsa.pub
```

Mot de passe de clé :

`Enter passphrase (empty for no passphrase):`

`ssh-keygen` fait référence à un mot de passe utilisé pour chiffrer la clé privée en tant que « phrase secrète ».  Il est *vivement* recommandé d’ajouter une phrase secrète à vos paires de clés. Sans phrase secrète pour protéger la clé privée, toute personne disposant d’une copie du fichier de clé peut l’utiliser pour se connecter à un serveur disposant de la clé publique correspondante. L’ajout d’une phrase secrète offre donc une protection renforcée si un utilisateur malveillant parvient à accéder à votre fichier de clé privée. Vous avez ainsi plus de temps pour modifier les clés utilisées pour vous authentifier.

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>Utilisation de ssh-agent pour stocker votre mot de passe de clé privée

Pour éviter de saisir la phrase secrète de votre fichier de clé privée à chaque connexion SSH, vous pouvez utiliser `ssh-agent` pour le mettre en cache. Si vous utilisez un Mac, le trousseau OSX stocke et sécurise les mots de passe de clés privées lorsque vous appelez `ssh-agent`.

Vérifiez et utilisez `ssh-agent` et `ssh-add` pour indiquer au système SSH les fichiers de clés, afin qu’il ne soit pas nécessaire d’utiliser la phrase secrète de manière interactive.

```bash
eval "$(ssh-agent -s)"
```

Ensuite, ajoutez la clé privée à `ssh-agent` à l’aide de la commande `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Le mot de passe de la clé privée est maintenant stocké dans `ssh-agent`.

## <a name="using-ssh-copy-id-to-install-the-new-key"></a>Utilisation de `ssh-copy-id` pour installer la nouvelle clé
Si vous avez déjà créé une machine virtuelle, vous pouvez installer la nouvelle clé publique SSH sur votre machine virtuelle Linux avec la commande suivante, en remplaçant le nom d’utilisateur de machine virtuelle et l’adresse du serveur par vos propres valeurs :

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub ahmet@myserver
```

## <a name="create-and-configure-an-ssh-config-file"></a>Créer et configurer un fichier de configuration SSH

Il est recommandé de créer et de configurer un fichier `~/.ssh/config` pour accélérer les connexions et optimiser le comportement de votre client SSH.

L’exemple suivant illustre une configuration standard.

### <a name="create-the-file"></a>Créer le fichier

```bash
touch ~/.ssh/config
```

### <a name="edit-the-file-to-add-the-new-ssh-configuration"></a>Modifiez le fichier pour ajouter la nouvelle configuration SSH :

```bash
vim ~/.ssh/config
```

### <a name="example-sshconfig-file"></a>Exemple de fichier `~/.ssh/config` :

```bash
# Azure Keys
Host fedora22
  Hostname 102.160.203.241
  User ahmet
# ./Azure Keys
# Default Settings
Host *
  PubkeyAuthentication=yes
  IdentitiesOnly=yes
  ServerAliveInterval=60
  ServerAliveCountMax=30
  ControlMaster auto
  ControlPath ~/.ssh/SSHConnections/ssh-%r@%h:%p
  ControlPersist 4h
  IdentityFile ~/.ssh/id_rsa
```

Cette configuration SSH propose des sections pour chaque serveur, permettant à chacun d’eux d’avoir sa propre paire de clés dédiée. Les paramètres par défaut (`Host *`) sont destinés à tous les hôtes qui ne correspondent pas à ceux répertoriés plus haut dans le fichier de configuration.

### <a name="config-file-explained"></a>Explication du fichier de configuration

`Host` = nom de l’hôte appelé sur le terminal.  `ssh fedora22` indique à `SSH` d’utiliser les valeurs dans le bloc de paramètres intitulé `Host fedora22`. REMARQUE : la valeur Host peut correspondre à une quelconque étiquette logique du point de vue de votre utilisation et ne représente pas le nom d’hôte réel d’un serveur.

`Hostname 102.160.203.241` = adresse IP ou nom DNS du serveur en cours d’accès.

`User ahmet` = compte d’utilisateur distant à utiliser lors de la connexion au serveur.

`PubKeyAuthentication yes` = indique à SSH que vous souhaitez utiliser une clé SSH pour la connexion.

`IdentityFile /home/ahmet/.ssh/id_id_rsa` = clé privée SSH et clé publique à utiliser pour l’authentification.

## <a name="ssh-into-linux-without-a-password"></a>Connexion SSH sous Linux sans mot de passe

Maintenant que vous avez une paire de clés SSH et un fichier de configuration SSH configuré, vous pouvez vous connecter à votre machine virtuelle Linux rapidement et en toute sécurité. Lors de votre première connexion à un serveur à l’aide d’une clé SSH, l’invite de commandes vous demande de saisir la phrase secrète du fichier de clé en question.

```bash
ssh fedora22
```

### <a name="command-explained"></a>Explication des commandes

Lorsque `ssh fedora22` est exécuté, SSH commence par localiser et charger tous les paramètres du bloc `Host fedora22`, puis charge tous les paramètres restants du dernier bloc `Host *`.

## <a name="next-steps"></a>Étapes suivantes

L’étape suivante consiste à créer des machines virtuelles Linux de Azure à l’aide de la nouvelle clé publique SSH.  Les machines virtuelles Azure créées avec une clé publique SSH comme identifiant de connexion sont mieux sécurisées que celles créées avec la méthode de connexion par défaut (les mots de passe).  Les machines virtuelles Azure créées à l’aide de clés SSH sont par défaut configurées avec les mots de passe désactivés, bloquant ainsi les tentatives de déchiffrement par force brute. Si vous avez besoin d’assistance lors de la création de votre paire de clés SSH ou si vous avez besoin de certificats supplémentaires, par exemple pour l’utilisation avec le portail Classic, consultez [les étapes détaillées pour créer des paires de clés SSH et des certificats](create-ssh-keys-detailed.md).

* [Créer une machine virtuelle Linux sécurisée à l’aide d’un modèle Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Créer une machine virtuelle Linux à l’aide du portail Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Créer une machine virtuelle Linux sécurisée à l’aide de l'interface de ligne de commande Azure (CLI)](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

