---
title: "Configurer SSHD sur des machines virtuelles Linux Azure | Microsoft Docs"
description: "Configurez SSHD pour les meilleures pratiques de sécurité et pour verrouiller SSH sur des machines virtuelles Linux Azure."
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 11/21/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: 08499c4242fdc59ef932d6b8f2e8442e5cdc55b2
ms.openlocfilehash: a606f06de55b1db4392d066a3e1d026eb3b54d74


---

# <a name="configure-sshd-on-azure-linux-vms"></a>Configurer SSHD sur des machines virtuelles Linux Azure

Cet article explique comment verrouiller le serveur SSH sur Linux, mettre en œuvre les meilleures pratiques de sécurité et accélérer le processus de connexion SSH en utilisant des clés SSH plutôt que des mots de passe.  Pour renforcer le verrouillage de SSHD, nous allons désactiver la connexion de l’utilisateur racine, limiter les utilisateurs autorisés à se connecter via une liste de groupes approuvés, désactiver la version 1 du protocole SSH, définir un minimum de bits pour les clés et configurer la déconnexion automatique des utilisateurs inactifs.  Les éléments requis pour cet article sont les suivants : un compte Azure ([obtenir un essai gratuit](https://azure.microsoft.com/pricing/free-trial/)) et [des fichiers de clés SSH publiques et privées](virtual-machines-linux-mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

## <a name="quick-commands"></a>Commandes rapides

Configurez `/etc/ssh/sshd_config` en utilisant les paramètres suivants :

### <a name="disable-password-logins"></a>Désactiver les connexions par mot de passe

```bash
PasswordAuthentication no
```

### <a name="disable-login-by-the-root-user"></a>Désactiver la connexion de l’utilisateur racine

```bash
PermitRootLogin no
```

### <a name="allowed-groups-list"></a>Liste de groupes autorisés

```bash
AllowGroups wheel
```

### <a name="allowed-users-list"></a>Liste d’utilisateurs autorisés

```bash
AllowUsers ahmet ralph
```

### <a name="disable-ssh-protocol-version-1"></a>Désactiver la version 1 du protocole SSH

```bash
Protocol 2
```

### <a name="minimum-key-bits"></a>Minimum de bits pour les clés

```bash
ServerKeyBits 2048
```

### <a name="disconnect-idle-users"></a>Déconnecter les utilisateurs inactifs

```bash
ClientAliveInterval 300
ClientAliveCountMax 0
```

## <a name="detailed-walkthrough"></a>Procédure pas à pas

SSHD est le serveur SSH qui s’exécute sur la machine virtuelle Linux.  SSH est un client qui s’exécute à partir d’un interpréteur de commandes sur votre MacBook ou votre poste de travail Linux, ou à partir d’un script Bash sur Windows.  SSH est également le protocole utilisé pour sécuriser et chiffrer les communications entre votre poste de travail et la machine virtuelle Linux, ce qui fait également de SSH un VPN (réseau privé virtuel).

Dans le cadre de cet article, il est très important que la connexion à votre machine virtuelle Linux soit ouverte durant toutes les étapes.  Une fois une connexion SSH établie, la session reste ouverte tant que la fenêtre n’est pas fermée.  Le fait d’avoir un terminal connecté permet d’apporter des modifications au service SSHD en empêchant que l’accès soit bloqué si une modification entraîne une rupture.  Si vous voyez toutefois votre accès à votre machine virtuelle Linux bloqué à cause d’une configuration SSHD rompue, Azure offre la possibilité de réinitialiser une configuration SSHD rompue à l’aide de [l’extension d’accès aux machines virtuelles Azure](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json).

Pour cette raison, nous ouvrons deux terminaux et établissons une connexion SSH avec la machine virtuelle Linux à partir de chacun d’eux.  Nous utilisons le premier terminal pour modifier le fichier de configuration SSHD et redémarrer le service SSHD.  Nous utilisons le deuxième terminal pour tester ces modifications une fois le service redémarré.  Étant donné que nous désactivons les mots de passe SSH et que nous nous appuyons uniquement sur les clés SSH, si ces dernières sont incorrectes et que vous fermez la connexion à la machine virtuelle, celle-ci sera définitivement verrouillée et personne ne pourra se connecter à moins de la supprimer et de la recréer.

## <a name="disable-password-logins"></a>Désactiver les connexions par mot de passe

Le moyen le plus rapide pour sécuriser votre machine virtuelle Linux consiste à désactiver les connexions par mot de passe.  Si les connexions par mot de passe sont activées, les robots qui sillonnent le web essaieront immédiatement d’utiliser une attaque par force brute pour deviner le mot de passe de votre machine virtuelle Linux à l’aide de SSH.  Lorsque les connexions par mot de passe sont désactivées complètement, le serveur SSH ignorer toutes les tentatives de connexion par mot de passe.

```bash
PasswordAuthentication no
```

## <a name="disable-login-by-the-root-user"></a>Désactiver la connexion de l’utilisateur racine

Conformément aux meilleures pratiques applicables à Linux, l’utilisateur `root` ne doit jamais être connecté via SSH ou à l’aide de `sudo su`.  Toutes les commandes nécessitant des autorisations de niveau racine doivent toujours être exécutées par le biais de la commande `sudo`, qui enregistre toutes les actions à des fins d’audit ultérieur.  La désactivation de la connexion de l’utilisateur `root` via SSH est une étape des meilleures pratiques de sécurité permettant de s’assurer que seuls les utilisateurs autorisés sont en mesure d’établir une connexion SSH.

```bash
PermitRootLogin no
```

## <a name="allowed-groups-list"></a>Liste de groupes autorisés

Le protocole SSH offre une méthode de restriction des utilisateurs et des groupes autorisés à se connecter via SSH.  Cette fonctionnalité s’appuie sur des listes pour autoriser ou interdire l’accès à des utilisateurs et des groupes spécifiques.  La définition du groupe wheel sur la liste `AllowGroups` permet de restreindre les connexions approuvées via SSH aux comptes d’utilisateurs appartenant au groupe wheel.

```bash
AllowGroups wheel
```

## <a name="allowed-users-list"></a>Liste d’utilisateurs autorisés

La restriction des connexions SSH à certains utilisateurs offre un moyen plus spécifique d’accomplir la même tâche que `AllowGroups`.  

```bash
AllowUsers ahmet ralph
```

## <a name="disable-ssh-protocol-version-1"></a>Désactiver la version 1 du protocole SSH

La version 1 du protocole SSH n’est pas sécurisée et doit être désactivée.  La version 2 du protocole SSH est la version actuelle. Elle offre un moyen sécurisé d’établir une connexion SSH avec votre serveur.  La désactivation de la version 1 de SSH permet de refuser tout accès aux clients SSH qui tentent d’établir une connexion avec le serveur SSH à l’aide de cette version du protocole.  Seules les connexions faisant appel à la version 2 de SSH sont autorisées à négocier une connexion avec le serveur SSH.

```bash
Protocol 2
```

## <a name="minimum-key-bits"></a>Minimum de bits pour les clés

Conformément aux meilleures pratiques de sécurité, les connexions SSH par mot de passe sont désactivées et seules les clés SSH peuvent être utilisées pour l’authentification auprès du serveur SSH.  Ces clés SSH peuvent être créées à l’aide de clés de longueur différente, mesurées en bits.  Selon les meilleures pratiques, une longueur de 2 048 bits constitue le niveau de sécurité minimal acceptable pour les clés.  Les clés de moins de 2 048 bits risquent en effet d’être déchiffrées.  La définition de `ServerKeyBits` sur `2048` permet d’autoriser les connexions faisant appel à des clés d’au moins 2 048 bits et d’interdire les connexions qui utilisent des clés moins longues.

```bash
ServerKeyBits 2048
```

## <a name="disconnect-idle-users"></a>Déconnecter les utilisateurs inactifs

Le protocole SSH offre la possibilité de déconnecter les utilisateurs ayant une session ouverte qui sont restés inactifs pendant plus d’un certain nombre de secondes.  Le fait de conserver les sessions ouvertes uniquement pour les utilisateurs actifs permet de limiter l’exposition de la machine virtuelle Linux.

```bash
ClientAliveInterval 300
ClientAliveCountMax 0
```

## <a name="restart-sshd"></a>Redémarrer SSHD

Pour activer les paramètres de `/etc/ssh/sshd_config`, vous devez redémarrer le processus SSHD, ce qui entraîne le redémarrage du serveur SSH.  La fenêtre de terminal utilisée pour redémarrer le serveur SSH reste ouverte, empêchant la perte de la session SSH ouverte.  Pour tester les nouveaux paramètres du serveur SSH, utilisez une deuxième fenêtre de terminal ou un deuxième onglet.  L’utilisation d’un terminal distinct pour tester la connexion SSH vous permet de revenir en arrière et d’apporter des modifications supplémentaires à `/etc/ssh/sshd_config` dans le premier terminal sans voir votre accès bloqué si une modification de la configuration SSHD entraîne une rupture.  

### <a name="on-redhat-centos-and-fedora"></a>Sur Redhat, Centos et Fedora

```bash
service sshd restart
```

### <a name="on-debian--ubuntu"></a>Sur Debian et Ubuntu

```bash
service ssh restart
```

## <a name="reset-sshd-using-azure-reset-access"></a>Réinitialiser SSHD à l’aide de la commande reset-access Azure

Si votre accès est bloqué à cause d’une modification ayant entraîné une rupture de la configuration SSHD, vous pouvez utiliser l’extension d’accès aux machines virtuelles Azure pour rétablir la configuration SSHD d’origine.

Remplacez les exemples de noms par vos propres noms.

```azurecli
azure vm reset-access \
--resource-group myResourceGroup \
--name myVM \
--reset-ssh
```

## <a name="install-fail2ban"></a>Installer Fail2ban

Il est vivement recommandé d’installer et de configurer l’application open source Fail2ban, qui bloque les tentatives répétées de connexion à votre machine virtuelle Linux via SSH à l’aide d’une attaque par force brute.  Fail2ban enregistre les tentatives répétées de connexion via SSH ayant échoué, puis crée des règles de pare-feu pour bloquer l’adresse IP dont proviennent ces tentatives.

* [Page d’accueil de Fail2ban](http://www.fail2ban.org/wiki/index.php/Main_Page)

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez configuré et verrouillé le serveur SSH sur votre machine virtuelle Linux, vous pouvez suivre d’autres meilleures pratiques de sécurité.  

* [Gérer les utilisateurs, SSH et vérifier ou réparer les disques de machines virtuelles Azure Linux à l'aide de l’extension VMAccess](virtual-machines-linux-using-vmaccess-extension.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

* [Chiffrer des disques sur une machine virtuelle Linux à l’aide de l’interface de ligne de commande Azure (CLI)](virtual-machines-linux-encrypt-disks.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

* [Accès et sécurité dans les modèles Azure Resource Manager](virtual-machines-linux-dotnet-core-3-access-security.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)



<!--HONumber=Dec16_HO2-->


