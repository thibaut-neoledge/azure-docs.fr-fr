---
title: "Ajouter un utilisateur sur une machine virtuelle Linux dans Azure | Microsoft Docs"
description: Ajouter un utilisateur sur une machine virtuelle Linux dans Azure.
services: virtual-machines-linux
documentationcenter: 
author: vlivech
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f8aa633b-8b75-45d7-b61d-11ac112cedd5
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 08/18/2016
ms.author: v-livech
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: a95157f57c0cbd1f2a9ed68a0fe83140d7c9ec40
ms.lasthandoff: 04/03/2017


---
# <a name="add-a-user-to-an-azure-vm"></a>Ajouter un utilisateur à une machine virtuelle Azure
Une des premières tâches à effectuer sur toute nouvelle machine virtuelle Linux consiste à créer un utilisateur.  Cet article décrit la création d’un compte d’utilisateur sudo, la définition du mot de passe, l’ajout des clés publiques SSH et enfin l’utilisation de `visudo` pour autoriser sudo sans mot de passe.

Les conditions préalables sont : [un compte Azure](https://azure.microsoft.com/pricing/free-trial/), [les clés publiques et privées SSH](mac-create-ssh-keys.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json), un groupe de ressources Azure et l’interface CLI Azure installée et basculée sur le mode Azure Resource Manager avec `azure config mode arm`.

## <a name="quick-commands"></a>Commandes rapides
```bash
# Add a new user on RedHat family distros
sudo useradd -G wheel exampleUser

# Add a new user on Debian family distros
sudo useradd -G sudo exampleUser

# Set a password
sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully

# Copy the SSH Public Key to the new user
ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver

# Change sudoers to allow no password
# Execute visudo as root to edit the /etc/sudoers file
visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL

# Verify everything
# Verify the SSH keys & User account
bill@slackware$ ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
sudo top
```

## <a name="detailed-walkthrough"></a>Procédure pas à pas
### <a name="introduction"></a>Introduction
Une des premières tâches courantes à effectuer avec un nouveau serveur consiste à ajouter un compte utilisateur.  Les connexions racine doivent être désactivées et le compte racine lui-même ne doit pas être utilisé avec votre serveur Linux, mais uniquement avec sudo.  L’affectation à un utilisateur de privilèges d’escalade racine à l’aide de sudo est la méthode appropriée pour l’administration et l’utilisation de Linux.

À l’aide de la commande `useradd` , nous ajoutons des comptes utilisateurs à la machine virtuelle Linux.  L’exécution de `useradd` modifie `/etc/passwd`, `/etc/shadow`, `/etc/group` et `/etc/gshadow`.  Nous ajoutons un indicateur de ligne de commande à la commande `useradd` pour ajouter également le nouvel utilisateur au groupe sudo approprié sur Linux.  Bien que `useradd` crée une entrée dans `/etc/passwd`, cela n’attribue pas de mot de passe au nouveau compte utilisateur.  Nous créons un mot de passe initial pour le nouvel utilisateur à l’aide de la commande `passwd` .  La dernière étape consiste à modifier les règles sudo pour permettre à cet utilisateur d’exécuter des commandes avec des privilèges sudo sans devoir entrer un mot de passe pour chaque commande.  En nous connectant à l’aide de la clé publique, nous supposons que ce compte utilisateur est à l’abri des attaques, et nous allons autoriser sudo à accéder sans mot de passe.  

### <a name="adding-a-single-sudo-user-to-an-azure-vm"></a>Ajout d’un utilisateur sudo unique à une machine virtuelle Azure
Connectez-vous à la machine virtuelle Azure à l’aide des clés SSH.  Si vous n’avez pas configuré un accès par clé publique SSH, commencez par lire entièrement l’article [Utilisation d’une authentification par clé publique avec Azure](http://link.to/article).  

La commande `useradd` effectue les tâches suivantes :

* créer un compte d’utilisateur
* créer un groupe d’utilisateurs avec le même nom
* ajouter une entrée vierge à `/etc/passwd`
* ajouter une entrée vierge à `/etc/gpasswd`

L’indicateur de ligne de commande `-G` ajoute le nouveau compte utilisateur au groupe Linux approprié, en attribuant au nouveau compte des privilèges d’escalade racine.

#### <a name="add-the-user"></a>Ajouter l’utilisateur
```bash
# On RedHat family distros
sudo useradd -G wheel exampleUser

# On Debian family distros
sudo useradd -G sudo exampleUser
```

#### <a name="set-a-password"></a>Définir le mot de passe
La commande `useradd` crée l’utilisateur et ajoute une entrée à `/etc/passwd` et à `/etc/gpasswd`, mais ne définit ne pas réellement de mot de passe.  Le mot de passe est ajouté à l’entrée à l’aide de la commande `passwd` .

```bash
sudo passwd exampleUser
Enter new UNIX password:
Retype new UNIX password:
passwd: password updated successfully
```

Nous disposons désormais d’un utilisateur avec privilèges sudo sur le serveur.

### <a name="add-your-ssh-public-key-to-the-new-user-account"></a>Ajouter votre clé publique SSH au nouveau compte utilisateur
À partir de votre ordinateur, utilisez la commande `ssh-copy-id` avec le nouveau mot de passe.

```bash
ssh-copy-id -i ~/.ssh/id_rsa exampleuser@exampleserver
```

### <a name="using-visudo-to-allow-sudo-usage-without-a-password"></a>Utilisation de visudo pour autoriser l’utilisation de sudo sans mot de passe
L’utilisation de `visudo` pour modifier le fichier `/etc/sudoers` ajoute quelques couches de protection contre toute modification incorrecte de ce fichier important.  Lors de l’exécution de `visudo`, le fichier `/etc/sudoers` est verrouillé pour s’assurer qu’aucun autre utilisateur ne puisse apporter des modifications pendant que le fichier est en cours de modification.  Le fichier `/etc/sudoers` est également analysé par `visudo` pour rechercher d’éventuelles erreurs lorsque vous tenter de l’enregistrer ou de le quitter, de sorte que vous ne pouvez pas enregistrer un fichier sudoers mal formé.

Nous disposons déjà des utilisateurs dans le bon groupe par défaut pour l’accès sudo.  Nous allons maintenant activer ces groupes pour qu’ils utilisent sudo sans mot de passe.

```bash
# Execute visudo as root to edit the /etc/sudoers file
visudo

# On RedHat family distros uncomment this line:
## Same thing without a password
# %wheel        ALL=(ALL)       NOPASSWD: ALL

# to this
## Same thing without a password
%wheel        ALL=(ALL)       NOPASSWD: ALL

# On Debian family distros change this line:
# Allow members of group sudo to execute any command
%sudo   ALL=(ALL:ALL) ALL

# to this
%sudo   ALL=(ALL) NOPASSWD:ALL
```

### <a name="verify-the-user-ssh-keys-and-sudo"></a>Vérifier l’utilisateur, les clés ssh et sudo
```bash
# Verify the SSH keys & User account
ssh -i ~/.ssh/id_rsa exampleuser@exampleserver

# Verify sudo access
sudo top
```

