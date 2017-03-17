---
title: "Vérifier le trafic avec la vérification des flux IP d’Azure Network Watcher - REST | Microsoft Docs"
description: "Cet article explique comment savoir si le trafic en direction ou en provenance d’une machine virtuelle est autorisé ou refusé"
services: network-watcher
documentationcenter: na
author: georgewallace
manager: timlt
editor: 
ms.assetid: 3307a79f-03be-46a0-aaaf-b2079cb5f3b2
ms.service: network-watcher
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/22/2017
ms.author: gwallace
translationtype: Human Translation
ms.sourcegitcommit: 1d0136b044f6049e59fa09d824cf244cac703c45
ms.openlocfilehash: 5c2054800d2234653c60c35f9c009499e231fd31
ms.lasthandoff: 02/23/2017


---
# <a name="check-if-traffic-is-allowed-or-denied-with-ip-flow-verify-a-component-of-azure-network-watcher"></a>Vérifier si le trafic est autorisé ou refusé en direction ou en provenance d’une machine virtuelle avec le composant de vérification des flux IP d’Azure Network Watcher

> [!div class="op_single_selector"]
> - [portail Azure](network-watcher-check-ip-flow-verify-portal.md)
> - [PowerShell](network-watcher-check-ip-flow-verify-powershell.md)
> - [INTERFACE DE LIGNE DE COMMANDE](network-watcher-check-ip-flow-verify-cli.md)
> - [API REST Azure](network-watcher-check-ip-flow-verify-rest.md)

La vérification des flux IP est une fonctionnalité de Network Watcher qui vous permet de vérifier si le trafic en direction ou en provenance d’une machine virtuelle est autorisé. La validation peut être exécutée pour le trafic entrant ou sortant. Cette fonctionnalité est très utile pour définir si une machine virtuelle peut actuellement communiquer avec une ressource externe ou un serveur back-end. La vérification des flux IP peut être utilisée pour vérifier si les règles de votre groupe de sécurité réseau sont correctement configurées et pour résoudre les problèmes de flux bloqués par les règles de groupe de sécurité réseau. La vérification des flux IP permet aussi de s’assurer que le trafic que vous souhaitez bloquer est correctement bloqué par le groupe de sécurité réseau.

## <a name="before-you-begin"></a>Avant de commencer

ARMclient permet d’appeler l’API REST à l’aide de PowerShell. ARMClient est accessible sur le site chocolatey à partir de la page [ARMClient sur Chocolatey](https://chocolatey.org/packages/ARMClient).

Ce scénario suppose que vous ayez déjà suivi la procédure décrite dans [Créer une instance d’Azure Network Watcher](network-watcher-create.md) pour créer un Network Watcher.

## <a name="scenario"></a>Scénario

Ce scénario utilise la vérification des flux IP pour vérifier si une machine virtuelle peut communiquer avec une autre machine via le port 443. Si le trafic est refusé, la règle de sécurité refusant ce trafic est renvoyée. Pour plus d’informations sur la vérification des flux IP, consultez la page [Présentation de la vérification des flux IP](network-watcher-ip-flow-verify-overview.md)

Dans ce scénario, vous allez :

* Récupérer une machine virtuelle
* Appeler la vérification des flux IP
* Vérifier les résultats

## <a name="log-in-with-armclient"></a>Se connecter à ARMClient

```PowerShell
armclient login
```

## <a name="retrieve-a-virtual-machine"></a>Récupérer une machine virtuelle

Exécutez le script suivant pour renvoyer une machine virtuelle. Le code suivant a besoin de valeurs pour les variables :

* **subscriptionId** - L’ID d’abonnement à utiliser.
* **resourceGroupName** - Le nom d’un groupe de ressources qui contient les machines virtuelles.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"

armclient get https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Compute/virtualMachines?api-version=2015-05-01-preview
```

Les informations nécessaires sont l’ID sous le type de `Microsoft.Compute/virtualMachines`. Les résultats doivent ressembler à l’exemple de code suivant :

```json
...,
        "networkProfile": {
          "networkInterfaces": [
            {
              "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft
.Network/networkInterfaces/contosovm842"
            }
          ]
        },
        "provisioningState": "Succeeded"
      },
      "resources": [
        {
          "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Com
pute/virtualMachines/ContosoVM/extensions/CustomScriptExtension"
        }
      ],
      "type": "Microsoft.Compute/virtualMachines",
      "location": "westcentralus",
      "id": "/subscriptions/{00000000-0000-0000-0000-000000000000}/resourceGroups/ContosoExampleRG/providers/Microsoft.Compute
/virtualMachines/ContosoVM",
      "name": "ContosoVM"
    }
  ]
}
```

## <a name="call-ip-flow-verify"></a>Appeler la vérification des flux IP

L’exemple suivant crée une demande pour vérifier le trafic d’une machine virtuelle spécifiée. La réponse renvoyée indique si le trafic est autorisé ou si le trafic est refusé. Si le trafic est refusé, elle indique également quelle règle bloque le trafic.

> [!NOTE]
> La vérification des flux IP nécessite l’allocation de la ressource de machine virtuelle.

Le script requiert l’ID de la ressource d’une machine virtuelle et d’une carte d’interface réseau sur la machine virtuelle. Ces valeurs sont fournies par le résultat précédent.

> [!Important]
> Pour tous les appels REST de Network Watcher, le nom du groupe de ressources dans l’URI de requête correspond à celui qui contient l’instance de Network Watcher, et non les ressources sur lesquelles vous exécutez les actions de diagnostic.

```powershell
$subscriptionId = "<subscription id>"
$resourceGroupName = "<resource group name>"
$networkWatcherName = "<network watcher name>"
$vmName = "<vm name>"
$vmNICName = "<vm NIC name>"
$direction = "<direction of traffic>" # Examples are: Inbound or Outbound
$localIP = "<source IP>"
$localPort = "<source Port>"
$remoteIP = "<destination IP>"
$remotePort = "<destination Port>" # Examples are: 80, or 80-120
$protocol = "<UDP, TCP or *>"
$targetUri = "<uri of target resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.compute/virtualMachine/${vmName}
$targetNic = "<uri of target nic resource>" # Example: /subscriptions/${subscriptionId}/resourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkInterfaces/${vmNICName}

$requestBody = @"
{
    'targetResourceId':  '$targetUri',
    'direction':  '$direction',
    'protocol':  '$protocol',
    'localPort':  '$localPort',
    'remotePort':  '$remotePort',
    'localIPAddress':  '$localIP',
    'remoteIPAddress':  '$remoteIP',
    'targetNICResourceId':  '$targetNic'
}
"@
        
armclient post "https://management.azure.com/subscriptions/${subscriptionId}/ResourceGroups/${resourceGroupName}/providers/Microsoft.Network/networkWatchers/${networkWatcherName}/ipFlowVerify?api-version=2016-12-01" $requestBody -verbose
```

## <a name="understanding-the-results"></a>Compréhension des résultats

La réponse que vous obtenez en retour vous indique si le trafic est autorisé ou refusé. La réponse ressemble à l’un des exemples suivants :

**Autorisé**

```json
{
  "access": "Allow",
  "ruleName": "defaultSecurityRules/AllowInternetOutBound"
}
```

**Refusé**

```json
{
  "access": "Deny",
  "ruleName": "defaultSecurityRules/DefaultInboundDenyAll"
}
```

## <a name="next-steps"></a>Étapes suivantes

Si le trafic est bloqué alors qu’il ne devrait pas l’être, consultez [Gérer les groupes de sécurité réseau à partir du portail](../virtual-network/virtual-network-manage-nsg-arm-portal.md) pour en savoir plus sur les groupes de sécurité réseau.













