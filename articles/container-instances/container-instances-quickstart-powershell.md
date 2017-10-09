---
title: "Guide de démarrage rapide : créer votre premier conteneur Azure Container Instances avec PowerShell"
description: "Commencez à utiliser Azure Container Instances en créant une instance de conteneur Windows avec PowerShell."
services: container-instances
documentationcenter: 
author: mmacy
manager: timlt
editor: 
tags: 
keywords: 
ms.assetid: 
ms.service: container-instances
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/25/2017
ms.author: marsma
ms.custom: mvc
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 0cc6612a91532774a2645676e36f617ddc5de12c
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="create-your-first-container-in-azure-container-instances"></a>Créer son premier conteneur dans Azure Container Instances

Azure Container Instances facilite la création et la gestion de conteneurs Docker dans Azure, sans avoir à approvisionner les machines virtuelles ou à adopter un service de niveau supérieur.

Dans ce guide de démarrage rapide, vous créez un conteneur Windows dans Azure et l’exposez sur internet avec une adresse IP publique. Cette opération s’effectue en une seule commande. Après quelques secondes, ceci s’affiche dans votre navigateur :

![L’application déployée à l’aide d’Azure Container Instances est affichée dans le navigateur][qs-powershell-01]

Si vous n’avez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) avant de commencer.

Ce démarrage rapide requiert le module Azure PowerShell version 4.4 ou ultérieure. Exécutez `Get-Module -ListAvailable AzureRM` pour trouver la version. Si vous devez installer ou mettre à niveau, consultez [Installer le module Azure PowerShell](/powershell/azure/install-azurerm-ps).

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous à votre abonnement Azure avec la commande `Login-AzureRmAccount` et suivez les instructions à l’écran.

```powershell
Login-AzureRmAccount
```

## <a name="create-resource-group"></a>Créer un groupe de ressources

Créez un groupe de ressources Azure avec [New-AzureRmResourceGroup](/powershell/module/azurerm.resources/new-azurermresourcegroup). Un groupe de ressources est un conteneur logique dans lequel les ressources Azure sont déployées et gérées.

```powershell
New-AzureRmResourceGroup -Name myResourceGroup -Location EastUS
```

## <a name="create-a-container"></a>Créez un conteneur.

Vous pouvez créer un conteneur en attribuant un nom, une image Docker ainsi qu’un groupe de ressources Azure. Si vous le souhaitez, vous pouvez exposer le conteneur sur internet avec une adresse IP publique. Dans ce cas, nous allons utiliser un conteneur Nano Server Windows exécutant les Services d'informations Internet (IIS).

```powershell
New-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer -Image microsoft/iis:nanoserver -OsType Windows -IpAddressType Public
```

Après quelques secondes, vous obtenez une réponse à votre demande. Au début, le conteneur présente l’état **En cours de création**, puis il démarre après une minute ou deux. Vous pouvez vérifier l’état à l’aide de l’applet de commande `Get-AzureRmContainerGroup`:

```powershell
Get-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

L’état d’approvisionnement et l’adresse IP du conteneur apparaissent dans la sortie de l’applet de commande :

```
ResourceGroupName        : myResourceGroup
Id                       : /subscriptions/12345678-1234-1234-1234-12345678abcd/resourceGroups/myResourceGroup/providers/Microsoft.ContainerInstance/containerGroups/mycontainer
Name                     : mycontainer
Type                     : Microsoft.ContainerInstance/containerGroups
Location                 : eastus
Tags                     :
ProvisioningState        : Creating
Containers               : {mycontainer}
ImageRegistryCredentials :
RestartPolicy            :
IpAddress                : 40.71.248.73
Ports                    : {80}
OsType                   : Windows
Volumes                  :
```

Lorsque la valeur **ProvisioningState** du conteneur passe à `Succeeded`, vous pouvez atteindre le conteneur dans le navigateur en utilisant l’adresse IP fournie.

![IIS déployé à l’aide d’Azure Container Instances affiché dans un navigateur][qs-powershell-01]

## <a name="delete-the-container"></a>Supprimer un conteneur

Lorsque vous avez fini d’utiliser le conteneur, vous pouvez le supprimer à l’aide de l’applet de commande `Remove-AzureRmContainerGroup`:

```powershell
Remove-AzureRmContainerGroup -ResourceGroupName myResourceGroup -Name mycontainer
```

## <a name="next-steps"></a>Étapes suivantes

Dans le cadre de ce démarrage rapide, vous avez démarré un conteneur Windows prêt à l’emploi dans Azure Container Instances. Si vous voulez essayer de créer un conteneur par vous-même et de le déployer dans Azure Container Instances à l’aide d’Azure Container Registry, passez au didacticiel relatif à Azure Container Instances.

> [!div class="nextstepaction"]
> [Didacticiels Azure Container Instances](./container-instances-tutorial-prepare-app.md)

<!-- IMAGES -->
[qs-powershell-01]: ./media/container-instances-quickstart-powershell/qs-powershell-01.png
