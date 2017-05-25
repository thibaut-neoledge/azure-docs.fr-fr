---
title: "Démarrage rapide avec Azure Cloud Shell (version préliminaire) | Microsoft Docs"
description: "Démarrage rapide pour Azure Cloud Shell"
services: 
documentationcenter: 
author: jluk
manager: timlt
tags: azure-resource-manager
ms.assetid: 
ms.service: 
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-linux
ms.devlang: na
ms.topic: article
ms.date: 05/10/2017
ms.author: juluk
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: b087ce2b7ee71b867e2123a70a2bfbf0fbea1e3b
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017

---

# <a name="quickstart"></a>Démarrage rapide
Ce document explique comment utiliser l’interface Azure Cloud Shell dans le [portail Azure](https://ms.portal.azure.com/).

## <a name="start-cloud-shell"></a>Démarrer Cloud Shell
1. Lancez **Cloud Shell** dans le volet de navigation supérieure du portail Azure <br>
![](media/shell-icon.png)
2. Sélectionnez un abonnement pour créer un compte de stockage et un partage de fichiers Azure avec <br>
![](media/storage-prompt.png)
3. Sélectionnez Créer le stockage

> [!TIP]
> Vous êtes authentifié automatiquement pour Azure CLI 2.0 dans chaque session.

### <a name="set-your-subscription"></a>Définissez votre abonnement
1. Répertoriez les événements auxquels vous avez accès : <br>
`az account list`
2. Définissez votre abonnement préféré : <br>
`az account set --subscription my-subscription-name`

> [!TIP]
> Votre abonnement sera mémorisé pour les sessions ultérieures à l’aide de `azureProfile.json`, conservé dans votre répertoire $Home.

### <a name="create-a-resource-group"></a>Créer un groupe de ressources
Créez un nouveau groupe de ressources dans WestUS nommé « MyRG » : <br>
`az group create -l westus -n MyRG` <br>

### <a name="create-a-linux-vm"></a>Créer une machine virtuelle Linux
Créez une machine virtuelle Ubuntu dans votre nouveau groupe de ressources. Azure CLI 2.0 crée les clés ssh et configure la machine virtuelle avec elles. <br>
`az vm create -n my_vm_name -g MyRG --image UbuntuLTS --generate-ssh-keys`

> [!NOTE]
> Les clés publiques et privées utilisées pour authentifier votre machine virtuelle sont placées dans `/User/.ssh/id_rsa` et `/User/.ssh/id_rsa.pub` par Azure CLI 2.0, par défaut. Votre dossier .ssh est conservé dans l’image de 5 Go de votre partage de fichiers Azure attaché.

Votre nom d’utilisateur sur cette machine virtuelle sera votre nom d’utilisateur utilisé dans Cloud Shell ($User@Azure:).

### <a name="ssh-into-your-linux-vm"></a>Se connecter avec SSH à votre machine virtuelle Linux
1. Recherchez le nom de votre machine virtuelle dans la barre de recherche du portail Azure
2. Cliquez sur « Connexion », puis exécutez : `ssh username@ipaddress`

![](media/sshcmd-copy.png)

Lors de l’établissement de la connexion SSH, vous devriez voir l’invite de bienvenue sur Ubuntu.
![](media/ubuntu-welcome.png)

## <a name="cleaning-up"></a>Nettoyage 
Supprimez votre groupe de ressources et toutes les ressources qu’il contient : <br>
Exécutez `az group delete -n MyRG`.

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur le stockage persistant dans Cloud Shell](persisting-shell-storage.md) [En savoir plus sur Azure CLI 2.0] (https://docs.microsoft.com/cli/azure/) [En savoir plus sur le stockage de fichiers Azure] (https://docs.microsoft.com/azure/storage/storage-introduction#file-storage) 

