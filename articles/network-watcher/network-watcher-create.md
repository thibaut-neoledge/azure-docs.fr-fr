---
title: "Créer une instance d’Azure Network Watcher | Microsoft Docs"
description: "Cette page fournit la procédure nécessaire pour créer une instance de Network Watcher à l’aide du portail et de l’API REST Azure"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: b1314119-0b87-4f4d-b44c-2c4d0547fb76
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 5cce99eff6ed75636399153a846654f56fb64a68
ms.openlocfilehash: 7d8eb5972d35eac6cb55fc393090cfcc21ec341c
ms.lasthandoff: 03/31/2017

---

# <a name="create-an-azure-network-watcher-instance"></a>Créer une instance d’Azure Network Watcher

Network Watcher est un service régional qui vous permet de surveiller et de diagnostiquer l’état au niveau d’un scénario réseau dans, vers et depuis Azure. La surveillance au niveau des scénarios vous permet de diagnostiquer les problèmes avec une vue de bout en bout du réseau. Les outils de visualisation et de diagnostic réseau disponibles avec Network Watcher vous aident à comprendre, diagnostiquer et obtenir des informations sur votre réseau dans Azure.

> [!NOTE]
> Comme Network Watcher ne prend actuellement en charge que l’interface CLI 1.0, les instructions fournies pour créer une nouvelle instance de Network Watcher s’appliquent à cette version.

## <a name="create-a-network-watcher-in-the-portal"></a>Créer un Network Watcher dans le portail

Accédez à **Plus de services** > **Mise en réseau** > **Network Watcher**. Vous pouvez sélectionner tous les abonnements pour lesquels vous souhaitez activer Network Watcher. Cette action crée un Network Watcher dans chaque région disponible.

![créer un Network Watcher][1]

## <a name="create-a-network-watcher-with-powershell"></a>Créer un Network Watcher avec PowerShell

Pour créer une instance de Network Watcher, exécutez l’exemple suivant :

```powershell
New-AzureRmNetworkWatcher -Name "NetworkWatcher_westcentralus" -ResourceGroupName "NetworkWatcherRG" -Location "West Central US"
```

## <a name="create-a-network-watcher-with-the-rest-api"></a>Créer un Network Watcher avec l’API REST

ARMclient permet d’appeler l’API REST à l’aide de PowerShell. ARMClient est accessible sur le site chocolatey à partir de la page [ARMClient sur Chocolatey](https://chocolatey.org/packages/ARMClient).

### <a name="log-in-with-armclient"></a>Se connecter à ARMClient

```powerShell
armclient login
```

### <a name="create-the-network-watcher"></a>Créer le Network Watcher

```powershell
$subscriptionId = '<subscription id>'
$networkWatcherName = '<name of network watcher>'
$resourceGroupName = '<resource group name>'
$apiversion = "2016-09-01"
$requestBody = @"
{
'location': 'West Central US'
}
"@

armclient put "https://management.azure.com/subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}?api-version=${api-version}" $requestBody
```

## <a name="next-steps"></a>Étapes suivantes

Maintenant que vous avez une instance de Network Watcher, découvrez les fonctionnalités disponibles :

* [Topologie](network-watcher-topology-overview.md)
* [Capture de paquets](network-watcher-packet-capture-overview.md)
* [Vérification des flux IP](network-watcher-ip-flow-verify-overview.md)
* [Tronçon suivant](network-watcher-next-hop-overview.md)
* [Affichage des groupes de sécurité](network-watcher-security-group-view-overview.md)
* [Journalisation des flux de groupe de sécurité réseau](network-watcher-nsg-flow-logging-overview.md)
* [Résolution des problèmes de passerelle de réseau virtuel](network-watcher-troubleshoot-overview.md)

Après la création d’une instance Network Watcher, lisez l’article [Créer une capture de paquets déclenchée par une alerte](network-watcher-alert-triggered-packet-capture.md) pour configurer la capture de paquets.

[1]: ./media/network-watcher-create/figure1.png












