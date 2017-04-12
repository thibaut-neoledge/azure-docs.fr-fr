---
title: "Utilisation des privilèges racines sur les machines virtuelles Linux | Microsoft Docs"
description: "Apprenez à utiliser les privilèges root sur une machine virtuelle Linux dans Azure."
services: virtual-machines-linux
documentationcenter: 
author: szarkos
manager: timlt
editor: 
tags: azure-service-management,azure-resource-manager
ms.assetid: a2c106a2-dceb-43a3-9dd1-50ed77685952
ms.service: virtual-machines-linux
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 02/02/2017
ms.author: szark
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: dc39db1f5fecffb60499a5420bfe72850e2fffd9
ms.lasthandoff: 04/03/2017


---
# <a name="using-root-privileges-on-linux-virtual-machines-in-azure"></a>Utilisation des privilèges root sur les machines virtuelles Linux dans Azure
[!INCLUDE [learn-about-deployment-models](../../../includes/learn-about-deployment-models-both-include.md)]

Par défaut, l’utilisateur `root` est désactivé sur les machines virtuelles Linux dans Microsoft Azure. Les utilisateurs peuvent exécuter des commandes avec des privilèges élevés à l’aide de la commande `sudo` . Toutefois, l'expérience peut varier en fonction du mode de déploiement du système.

1. **Clé SSH et mot de passe OU mot de passe uniquement :** la machine virtuelle a été déployée soit avec un certificat (fichier `.CER`) ou une clé SSH et un mot de passe, soit avec seulement un nom d’utilisateur et un mot de passe. Dans ce cas, `sudo` demande le mot de passe de l’utilisateur avant d’exécuter la commande.
2. **Clé SSH uniquement :** la machine virtuelle a été configurée avec un certificat (fichier `.cer`, `.pem` ou `.pub`) ou une clé SSH, mais sans mot de passe.  Dans ce cas, `sudo` **ne demande pas** le mot de passe de l’utilisateur avant d’exécuter la commande.

## <a name="ssh-key-and-password-or-password-only"></a>Clé SSH et mot de passe ou mot de passe uniquement
Connectez-vous à la machine virtuelle Linux à l’aide de l’authentification par clé SSH ou par mot de passe, puis exécutez les commandes à l’aide de `sudo`, par exemple :

    # sudo <command>
    [sudo] password for azureuser:

Dans ce cas, l'utilisateur est invité à fournir un mot de passe. Une fois le mot de passe entré, `sudo` exécute la commande avec les privilèges `root`.

Vous pouvez également activer la méthode sudo sans mot de passe en modifiant le fichier `/etc/sudoers.d/waagent` , par exemple :

    #/etc/sudoers.d/waagent
    azureuser ALL = (ALL) NOPASSWD: ALL

Cette modification permet à l’utilisateur « azureuser » de poursuivre sans entrer de mot de passe.

## <a name="ssh-key-only"></a>Clé SSH uniquement
Connectez-vous à la machine virtuelle Linux à l’aide de l’authentification par clé SSH, puis exécutez les commandes à l’aide de `sudo`, par exemple :

    # sudo <command>

Dans ce cas, l'utilisateur **n'est pas** invité à fournir un mot de passe. Une fois la touche `<enter>` activée, `sudo` exécute la commande avec les privilèges `root`.


