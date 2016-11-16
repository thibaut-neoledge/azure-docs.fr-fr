---
title: "Utilisation de clés SSH sous Linux et Mac | Microsoft Docs"
description: "Générez et utilisez des clés SSH sous Linux et Mac pour les modèles de déploiement du gestionnaire de ressources et classiques sur Azure."
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
ms.date: 10/25/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 689445bc656b5cdebc7689f7fec6e2ea2683576e


---
# <a name="create-ssh-keys-on-linux-and-mac-for-linux-vms-in-azure"></a>Créer des clés SSH sur Linux et Mac pour les machines virtuelles Linux dans Azure
Avec une paire de clés SSH, vous pouvez créer des machines virtuelles sur Azure qui utilisent par défaut des clés SSH pour l’authentification, éliminant tout recours aux mots de passe pour la connexion.  Les mots de passe sont vulnérables et peuvent exposer vos machines virtuelles à des attaques par force brute visant à découvrir votre mot de passe. Les machines virtuelles créées avec des modèles Azure ou `azure-cli` peuvent inclure votre clé publique SSH dans le cadre du déploiement, évitant toute opération de configuration post-déploiement.  Si vous vous connectez à une machine virtuelle Linux à partir de Windows, consultez [ce document.](virtual-machines-linux-ssh-from-windows.md)

L’article requiert :

* un compte Azure ([obtenir un essai gratuit](https://azure.microsoft.com/pricing/free-trial/)).
* [l’interface de ligne de commande Azure (CLI)](../xplat-cli-install.md) connectée à `azure login`
* l’interface de ligne de commande (CLI) Azure *doit être en *mode Azure Resource Manager`azure config mode arm`

## <a name="quick-commands"></a>Commandes rapides
Dans les commandes suivantes, remplacez les exemples par vos propres choix.

Par défaut, les clés SSH sont conservées dans le `.ssh`répertoire.  

```bash
cd ~/.ssh/
```

Si vous n’avez pas de répertoire `~/.ssh`, la commande `ssh-keygen` en créera un pour vous avec les autorisations appropriées.

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

Entrez le nom du fichier enregistré dans le répertoire `~/.ssh/` :

```bash
id_rsa
```

Saisissez la phrase secrète pour id_rsa :

```bash
correct horse battery staple
```

Il existe désormais une paire de clés SSH `id_rsa` et `id_rsa.pub` dans le répertoire `~/.ssh`.

```bash
ls -al ~/.ssh
```

Ajoutez la clé créée dans `ssh-agent` sur Linux et Mac (et dans le trousseau OSX) :

```bash
eval "$(ssh-agent -s)"
ssh-add ~/.ssh/id_rsa
```

Copiez la clé publique SSH dans votre serveur Linux :

```bash
ssh-copy-id -i ~/.ssh/id_rsa.pub myusername@myserver
```

Testez la connexion à l’aide de clés au lieu d’un mot de passe :

```bash
ssh -o PreferredAuthentications=publickey -o PubkeyAuthentication=yes -i ~/.ssh/id_rsa myusername@myserver
Last login: Tue April 12 07:07:09 2016 from 66.215.22.201
$
```

## <a name="detailed-walkthrough"></a>Procédure pas à pas
L’utilisation de clés publiques et privées SSH constitue le moyen le plus simple pour vous connecter à vos serveurs Linux. Le [chiffrement par clé publique](https://en.wikipedia.org/wiki/Public-key_cryptography) est un moyen bien plus sécurisé que les mots de passe pour vous connecter à votre machine virtuelle Linux ou BSD dans Azure. Les mots de passe sont beaucoup plus exposés aux attaques par force brute. Votre clé publique peut être partagée avec n’importe qui, mais vous seul (ou votre infrastructure de sécurité locale) possédez votre clé privée.  La clé privée SSH doit être protégée par un [mot de passe très sécurisé ](https://www.xkcd.com/936/) (source :[xkcd.com](https://xkcd.com)).  Ce mot de passe permet simplement d’accéder à la clé SSH privée et **n’est pas** le mot de passe du compte utilisateur.  Lorsque vous ajoutez un mot de passe à votre clé SSH, il chiffre celle-ci, la rendant inutilisable sans le mot de passe qui permet de la déverrouiller.  Si un attaquant parvient à voler votre clé privée, et que celle-ci n’a pas de mot de passe, il peut l’utiliser pour se connecter aux serveurs contenant la clé publique correspondante.  En revanche, si la clé privée est protégée par un mot de passe, cette dernière ne peut pas être utilisée par l’attaquant. Le mot de passe fournit ainsi une couche supplémentaire de sécurité pour votre infrastructure sur Azure.

Cet article crée des fichiers de clés formatés *ssh rsa*, qui sont recommandés pour les déploiements sur Resource Manager.  Les clés *ssh-rsa* sont nécessaires sur le [portail](https://portal.azure.com) pour les déploiements classiques et Resource Manager.

## <a name="create-the-ssh-keys"></a>Créer les clés SSH
Azure requiert des clés publiques et privées d’au moins 2 048 bits au format ssh-rsa. Utilisez `ssh-keygen` pour créer les clés. Cette fonction pose une série de questions, puis génère une clé privée et une clé publique correspondante. Lorsqu’une machine virtuelle Azure est créée, la clé publique est copiée dans `~/.ssh/authorized_keys`.  Les clés SSH dans `~/.ssh/authorized_keys` servent à demander au client la clé privée correspondante sur une connexion SSH.

## <a name="using-sshkeygen"></a>Utilisation de ssh-keygen
Cette commande crée une paire de clés SSH (chiffrées) protégées par un mot de passe, à l’aide du protocole RSA 2048 bits. Elle fait l’objet d’un commentaire pour faciliter son identification.  

Commencez par modifier des répertoires, afin que toutes vos clés ssh soient créées dans ce répertoire.

```bash
cd ~/.ssh
```

Si vous n’avez pas de répertoire `~/.ssh`, la commande `ssh-keygen` en créera un pour vous avec les autorisations appropriées.

```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
```

*Explication des commandes*

`ssh-keygen` = programme utilisé pour créer les clés

`-t rsa` = type de clé à créer ce qui est au [format RSA](https://en.wikipedia.org/wiki/RSA_(cryptosystem)

`-b 2048` = bits de la clé

`-C "myusername@myserver"` = commentaire ajouté à la fin du fichier de la clé publique pour l’identifier facilement.  Un courrier électronique est généralement utilisé comme commentaire, mais vous pouvez utiliser le contenu le mieux adapté à votre infrastructure.

### <a name="using-pem-keys"></a>Utilisation de clés PEM
Si vous utilisez le modèle de déploiement Classic (portail Azure Classic ou l’interface CLI de gestion des services Azure `asm`), vous devrez peut-être utiliser des clés SSH au format PEM pour accéder à vos machines virtuelles Linux.  Voici comment créer une clé PEM à partir d’une clé SSH publique et d’un certificat x509.

Pour créer une clé au format PEM à partir d’une clé publique SSH, procédez comme suit :

```bash
ssh-keygen -f ~/.ssh/id_rsa.pub -e > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-sshkeygen"></a>Exemple de ssh-keygen
```bash
ssh-keygen -t rsa -b 2048 -C "myusername@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/myusername/.ssh/id_rsa): id_rsa
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in id_rsa.
Your public key has been saved in id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 myusername@myserver
The key's randomart image is:
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

`Enter file in which to save the key (/home/myusername/.ssh/id_rsa): id_rsa`

Nom de la paire de clés dans cet article.  Le nom par défaut de la paire de clés est **id_rsa**. Certains outils pouvant demander le nom du fichier de clé privée **id_rsa**, il est utile d’en avoir un. Le répertoire `~/.ssh/` est l’emplacement par défaut des paires de clés SSH et du fichier de configuration SSH.

```bash
ls -al ~/.ssh
-rw------- 1 myusername staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 myusername staff   410 Aug 25 18:04 rsa.pub
```
Le contenu du répertoire `~/.ssh` s’affiche. `ssh-keygen` crée le répertoire `~/.ssh`, si nécessaire, et définit la propriété et les modes de fichier appropriés.

Mot de passe de clé :

`Enter passphrase (empty for no passphrase):`

`ssh-keygen` fait référence à un mot de passe utilisé comme phrase secrète.  Il est *vivement* recommandé d’ajouter un mot de passe à vos paires de clés. Sans mot de passe pour protéger la paire de clés, toute personne disposant d’une copie du fichier de clé privée peut l’utiliser pour se connecter à un serveur disposant de la clé publique correspondante. L’ajout d’un mot de passe offre donc une meilleure protection, si un utilisateur malveillant a accès à votre fichier de clé privée. Vous avez ainsi plus de temps pour modifier les clés utilisées pour vous authentifier.

## <a name="using-sshagent-to-store-your-private-key-password"></a>Utilisation de ssh-agent pour stocker votre mot de passe de clé privée
Pour éviter de saisir le mot de passe de votre fichier de clé privée à chaque connexion SSH, vous pouvez utiliser `ssh-agent` pour le mettre en cache. Si vous utilisez un Mac, le trousseau OSX stocke et sécurise les mots de passe de clés privées lorsque vous appelez `ssh-agent`.

Vérifiez d’abord que `ssh-agent` est en cours d’exécution.

```bash
eval "$(ssh-agent -s)"
```

Ensuite, ajoutez la clé privée à `ssh-agent` à l’aide de la commande `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Le mot de passe de la clé privée est maintenant stocké dans `ssh-agent`.

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
  User myusername
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
`Host` = nom de l’hôte appelé sur le terminal.  `ssh fedora22` indique à `SSH` d’utiliser les valeurs dans le bloc de paramètres intitulé `Host fedora22` REMARQUE : il peut s’agir d’une étiquette logique pour votre utilisation et cette valeur ne représente pas le nom d’hôte réel d’un serveur.

`Hostname 102.160.203.241` = adresse IP ou nom DNS du serveur en cours d’accès.

`User myusername` = le compte d’utilisateur distant à utiliser lors de la connexion au serveur.

`PubKeyAuthentication yes` = indique à SSH que vous souhaitez utiliser une clé SSH pour la connexion.

`IdentityFile /home/myusername/.ssh/id_id_rsa` = clé privée SSH et clé publique à utiliser pour l’authentification.

## <a name="ssh-into-linux-without-a-password"></a>Connexion SSH sous Linux sans mot de passe
Maintenant que vous avez une paire de clés SSH et un fichier de configuration SSH configuré, vous pouvez vous connecter à votre machine virtuelle Linux rapidement et en toute sécurité. Lors de votre première connexion à un serveur à l’aide d’une clé SSH, l’invite de commandes vous demande de saisir la phrase secrète du fichier de clé en question.

```bash
ssh fedora22
```

### <a name="command-explained"></a>Explication des commandes
Lorsque `ssh fedora22` est exécuté, SSH commence par localiser et charger tous les paramètres du bloc `Host fedora22`, puis charge tous les paramètres restants du dernier bloc `Host *`.

## <a name="next-steps"></a>Étapes suivantes
L’étape suivante consiste à créer des machines virtuelles Linux de Azure à l’aide de la nouvelle clé publique SSH.  Les machines virtuelles Azure créées avec une clé publique SSH comme identifiant de connexion sont mieux sécurisées que celles créées avec la méthode de connexion par défaut (les mots de passe).  Les machines virtuelles Azure créées à l’aide de clés SSH sont par défaut configurées avec les mots de passe désactivés, bloquant ainsi les tentatives de déchiffrement par force brute.

* [Créer une machine virtuelle Linux sécurisée à l’aide d’un modèle Azure](virtual-machines-linux-create-ssh-secured-vm-from-template.md)
* [Créer une machine virtuelle Linux à l’aide du portail Azure](virtual-machines-linux-quick-create-portal.md)
* [Créer une machine virtuelle Linux sécurisée à l’aide de l'interface de ligne de commande Azure (CLI)](virtual-machines-linux-quick-create-cli.md)




<!--HONumber=Nov16_HO2-->


