---
title: "Procédure détaillée de création d’une paire de clés SSH pour les machines virtuelles Linux dans Azure | Microsoft Docs"
description: "Découvrez les étapes supplémentaires permettant de créer une paire de clés publique et privée SSH pour les machines virtuelles Linux dans Azure, ainsi que des certificats spécifiques pour différents scénarios."
services: virtual-machines-linux
documentationcenter: 
author: dlepow
manager: timlt
editor: 
tags: 
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 6/28/2017
ms.author: danlep
ms.openlocfilehash: d4548c6f21d04effd57ea36e4fc0d15f77568903
ms.sourcegitcommit: 50e23e8d3b1148ae2d36dad3167936b4e52c8a23
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/18/2017
---
# <a name="detailed-walk-through-to-create-an-ssh-key-pair-and-additional-certificates-for-a-linux-vm-in-azure"></a>Procédure détaillée de création d’une paire de clés SSH et de certificats supplémentaires pour une machine virtuelle Linux dans Azure
Avec une paire de clés SSH, vous pouvez créer des machines virtuelles sur Azure qui utilisent par défaut des clés SSH pour l’authentification, éliminant ainsi la nécessité de recourir aux mots de passe pour la connexion. Les mots de passe sont vulnérables et peuvent exposer vos machines virtuelles à de constantes attaques par force brute visant à découvrir ces mots de passe. Les machines virtuelles créées avec l’interface de ligne de commande Azure ou avec des modèles Resource Manager peuvent inclure votre clé publique SSH dans le cadre du déploiement, ce qui évite l’étape de configuration post-déploiement consistant à désactiver les connexions par mot de passe pour SSH. Cet article fournit des étapes détaillées et des exemples supplémentaires de génération de certificats destinés, par exemple, à être utilisés avec les machines virtuelles Linux. Si vous souhaitez rapidement créer et utiliser une paire de clés SSH, consultez [Créer une paire de clés publique et privée SSH pour les machines virtuelles Linux](mac-create-ssh-keys.md).

## <a name="understanding-ssh-keys"></a>Présentation des clés SSH

L’utilisation de clés publiques et privées SSH constitue le moyen le plus simple pour vous connecter à vos serveurs Linux. Le [chiffrement par clé publique](https://en.wikipedia.org/wiki/Public-key_cryptography) est un moyen bien plus sécurisé que les mots de passe pour vous connecter à votre machine virtuelle Linux ou BSD dans Azure. Les mots de passe sont beaucoup plus exposés aux attaques par force brute.

Votre clé publique peut être partagée avec n’importe qui, mais vous seul (ou votre infrastructure de sécurité locale) possédez votre clé privée.  La clé privée SSH doit être protégée par un [mot de passe très sécurisé](https://www.xkcd.com/936/) (source : [xkcd.com](https://xkcd.com)).  Ce mot de passe permet simplement d’accéder au fichier de clé privée SSH et **n’est pas** le mot de passe du compte utilisateur.  Lorsque vous ajoutez un mot de passe à votre clé SSH, il chiffre celle-ci à l’aide d’une clé AES 128 bits, la rendant inutilisable sans le mot de passe qui permet de la déchiffrer.  Si un attaquant parvient à voler votre clé privée, et que celle-ci n’a pas de mot de passe, il peut l’utiliser pour se connecter aux serveurs contenant la clé publique correspondante.  En revanche, si la clé privée est protégée par un mot de passe, cette dernière ne peut pas être utilisée par l’attaquant. Le mot de passe fournit ainsi une couche supplémentaire de sécurité pour votre infrastructure sur Azure.

Cet article crée une paire de fichiers de clés publique et privée SSH RSA de version de protocole 2 (également appelées clés « ssh-rsa »), qui sont recommandés pour les déploiements sur Azure Resource Manager. Les clés *ssh-rsa* sont nécessaires sur le [portail](https://portal.azure.com) pour les déploiements Classic et Resource Manager.

## <a name="ssh-keys-use-and-benefits"></a>Utilisation et avantages des clés SSH

Azure requiert au minimum des clés publique et privée SSH au format RSA de version de protocole 2 de 2 048 bits ; le fichier de clé publique possède le format de conteneur `.pub`. Utilisez `ssh-keygen` pour créer les clés. Cette fonction pose une série de questions, puis génère une clé privée et une clé publique correspondante. Lors de la création d’une machine virtuelle Azure, Azure copie la clé publique dans le dossier `~/.ssh/authorized_keys` sur la machine virtuelle. Les clés SSH dans `~/.ssh/authorized_keys` servent à demander au client la clé privée correspondante sur une connexion SSH.  Lorsqu’une machine virtuelle Linux Azure est créée à l’aide de clés SSH pour l’authentification, Azure configure le serveur SSHD de façon à ne pas autoriser les connexions par mot de passe, mais uniquement les clés SSH.  Par conséquent, en créant des machines virtuelles Linux Azure avec des clés SSH, vous pouvez contribuer à sécuriser le déploiement de machines virtuelles et éviter l’étape de configuration classique après le déploiement qui consiste à désactiver les mots de passe dans le fichier **sshd_config**.

## <a name="using-ssh-keygen"></a>Utilisation de ssh-keygen

Cette commande crée une paire de clés SSH (chiffrées) protégées par un mot de passe à l’aide du protocole RSA 2 048 bits. Elle fait l’objet d’un commentaire pour faciliter son identification.  

Par défaut, les clés SSH sont conservées dans le `~/.ssh`répertoire.  Si vous n’avez pas de répertoire `~/.ssh`, la commande `ssh-keygen` en crée un pour vous avec les autorisations appropriées.

```bash
ssh-keygen \
    -t rsa \
    -b 2048 \
    -C "azureuser@myserver" \
    -f ~/.ssh/id_rsa \
    -N mypassword
```

*Explication des commandes*

`ssh-keygen` = programme utilisé pour créer les clés

`-t rsa` = type de clé à créer correspondant au format RSA [wikipedia][parenthèses à la fin](`https://en.wikipedia.org/wiki/RSA_(cryptosystem) `)
`-b 2048` = bits de la clé

`-C "azureuser@myserver"` = commentaire ajouté à la fin du fichier de la clé publique pour l’identifier facilement.  Un courrier électronique est généralement utilisé comme commentaire, mais vous pouvez utiliser le contenu le mieux adapté à votre infrastructure.

## <a name="classic-deploy-using-asm"></a>Déploiement Classic à l’aide de `asm`

Si vous utilisez le modèle de déploiement Classic (mode `asm` dans l’interface de ligne de commande), vous pouvez utiliser une clé publique SSH-RSA ou une clé au format RFC4716 dans un conteneur .pem.  La clé publique SSH-RSA est l’élément créé précédemment dans cet article à l’aide de `ssh-keygen`.

Pour créer une clé au format RFC4716 à partir d’une clé publique SSH existante, procédez comme suit :

```bash
ssh-keygen \
-f ~/.ssh/id_rsa.pub \
-e \
-m RFC4716 > ~/.ssh/id_ssh2.pem
```

## <a name="example-of-ssh-keygen"></a>Exemple de ssh-keygen

```bash
ssh-keygen -t rsa -b 2048 -C "azureuser@myserver"
Generating public/private rsa key pair.
Enter file in which to save the key (/home/azureuser/.ssh/id_rsa):
Enter passphrase (empty for no passphrase):
Enter same passphrase again:
Your identification has been saved in /home/azureuser/.ssh/id_rsa.
Your public key has been saved in /home/azureuser/.ssh/id_rsa.pub.
The key fingerprint is:
14:a3:cb:3e:78:ad:25:cc:55:e9:0c:08:e5:d1:a9:08 azureuser@myserver
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

`Enter file in which to save the key (/home/azureuser/.ssh/id_rsa): ~/.ssh/id_rsa`

Nom de la paire de clés dans cet article.  Le nom par défaut de la paire de clés est **id_rsa**. Certains outils pouvant demander le nom du fichier de clé privée **id_rsa**, il est utile d’en avoir un. Le répertoire `~/.ssh/` est l’emplacement par défaut des paires de clés SSH et du fichier de configuration SSH.  Si aucun chemin d’accès complet n’est spécifié, `ssh-keygen` crée les clés dans le répertoire de travail actuel, et non dans le répertoire par défaut `~/.ssh`.

Le contenu du répertoire `~/.ssh` s’affiche.

```bash
ls -al ~/.ssh
-rw------- 1 azureuser staff  1675 Aug 25 18:04 id_rsa
-rw-r--r-- 1 azureuser staff   410 Aug 25 18:04 id_rsa.pub
```

Mot de passe de clé :

`Enter passphrase (empty for no passphrase):`

`ssh-keygen` fait référence à un mot de passe utilisé pour la clé privée en tant que « phrase secrète ».  Il est *vivement* recommandé d’ajouter un mot de passe à votre clé privée. Sans mot de passe pour protéger le fichier de clés, toute personne disposant du fichier peut l’utiliser pour se connecter à un serveur disposant de la clé publique correspondante. L’ajout d’un mot de passe (phrase secrète) offre donc une meilleure protection, si un utilisateur malveillant a accès à votre fichier de clé privée. Vous avez ainsi plus de temps pour modifier les clés utilisées pour vous authentifier.

## <a name="using-ssh-agent-to-store-your-private-key-password"></a>Utilisation de ssh-agent pour stocker votre mot de passe de clé privée

Pour éviter de saisir le mot de passe de votre fichier de clé privée à chaque connexion SSH, vous pouvez utiliser `ssh-agent` pour le mettre en cache. Si vous utilisez un Mac, le trousseau OSX stocke et sécurise les mots de passe de clés privées lorsque vous appelez `ssh-agent`.

Vérifiez et utilisez ssh-agent et ssh-add pour indiquer au système SSH les fichiers de clés, afin qu’il ne soit pas nécessaire d’utiliser la phrase secrète de manière interactive.

```bash
eval "$(ssh-agent -s)"
```

Ensuite, ajoutez la clé privée à `ssh-agent` à l’aide de la commande `ssh-add`.

```bash
ssh-add ~/.ssh/id_rsa
```

Le mot de passe de la clé privée est maintenant stocké dans `ssh-agent`.

## <a name="using-ssh-copy-id-to-copy-the-key-to-an-existing-vm"></a>Copier la clé dans une machine virtuelle existante à l’aide de `ssh-copy-id`
Si vous avez déjà créé une machine virtuelle, vous pouvez installer la nouvelle clé publique SSH sur votre machine virtuelle Linux avec :

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

L’étape suivante consiste à créer des machines virtuelles Linux de Azure à l’aide de la nouvelle clé publique SSH.  Les machines virtuelles Azure créées avec une clé publique SSH comme identifiant de connexion sont mieux sécurisées que celles créées avec la méthode de connexion par défaut (les mots de passe).  Les machines virtuelles Azure créées à l’aide de clés SSH sont par défaut configurées avec les mots de passe désactivés, bloquant ainsi les tentatives de déchiffrement par force brute.

* [Créer une machine virtuelle Linux sécurisée à l’aide d’un modèle Azure](create-ssh-secured-vm-from-template.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Créer une machine virtuelle Linux à l’aide du portail Azure](quick-create-portal.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
* [Créer une machine virtuelle Linux sécurisée à l’aide de l'interface de ligne de commande Azure (CLI)](quick-create-cli.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)
