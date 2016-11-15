---
title: "Bien démarrer avec la création d’un équilibreur de charge accessible sur Internet dans le mode classique à l’aide de PowerShell | Microsoft Docs"
description: "Découvrez comment créer un équilibreur de charge accessible sur Internet dans le mode classique à l’aide de PowerShell"
services: load-balancer
documentationcenter: na
author: sdwheeler
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: 73e8bfa4-8086-4ef0-9e35-9e00b24be319
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 04/05/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 7344f2c3eeb7d52f8bc60e564d66b2cc51f10f75

---

# <a name="get-started-creating-an-internet-facing-load-balancer-classic-in-powershell"></a>Création d'un équilibreur de charge accessible sur Internet (classique) dans PowerShell

[!INCLUDE [load-balancer-get-started-internet-classic-selectors-include.md](../../includes/load-balancer-get-started-internet-classic-selectors-include.md)]

[!INCLUDE [load-balancer-get-started-internet-intro-include.md](../../includes/load-balancer-get-started-internet-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Cet article traite du modèle de déploiement classique. Vous pouvez également [découvrir comment créer un équilibreur de charge accessible sur Internet à l’aide d’Azure Resource Manager](load-balancer-get-started-internet-arm-ps.md).

[!INCLUDE [load-balancer-get-started-internet-scenario-include.md](../../includes/load-balancer-get-started-internet-scenario-include.md)]

## <a name="set-up-load-balancer-using-powershell"></a>Configurer de l'équilibrage de charge à l'aide de PowerShell

Pour configurer un équilibreur de charge à l’aide de PowerShell, procédez comme suit :

1. Si vous n’avez jamais utilisé Azure PowerShell, consultez [Installation et configuration d’Azure PowerShell](../powershell-install-configure.md) et suivez les instructions jusqu’à la fin pour vous connecter à Azure et sélectionner votre abonnement.
2. Après avoir créé une machine virtuelle, vous pouvez utiliser les applets de commande PowerShell pour ajouter un équilibrage de charge à une machine virtuelle dans le même service cloud.

Dans l'exemple suivant, vous allez ajouter un jeu d'équilibrage de charge appelé « webfarm » au service cloud « mytestcloud » (ou myctestcloud.cloudapp.net), puis ajouter les points de terminaison de l'équilibreur de charge aux machines virtuelles nommées « web1 » et « web2 ». L’équilibreur de charge reçoit le trafic réseau sur le port 80 et effectue l’équilibrage de charge entre les machines virtuelles définies par le point de terminaison local (dans ce cas, le port 80) avec TCP.

### <a name="step-1"></a>Étape 1

Créez un point de terminaison d’équilibrage de charge pour la première machine virtuelle « web1 »

```powershell
    Get-AzureVM -ServiceName "mytestcloud" -Name "web1" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

### <a name="step-2"></a>Étape 2

Créer un autre point de terminaison pour la deuxième machine virtuelle « web2 » en utilisant le même nom de jeu d’équilibrage de charge

```powershell
    Get-AzureVM -ServiceName "mytestcloud" -Name "web2" | Add-AzureEndpoint -Name "HttpIn" -Protocol "tcp" -PublicPort 80 -LocalPort 80 -LBSetName "WebFarm" -ProbePort 80 -ProbeProtocol "http" -ProbePath '/' | Update-AzureVM
```

## <a name="remove-a-virtual-machine-from-a-load-balancer"></a>Supprimez une machine virtuelle de l’équilibreur de charge

Vous pouvez utiliser Remove-AzureEndpoint pour supprimer un point de terminaison de machine virtuelle de l'équilibreur de charge

```powershell
    Get-azureVM -ServiceName mytestcloud  -Name web1 |Remove-AzureEndpoint -Name httpin| Update-AzureVM
```

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez également [commencer par créer un équilibrage de charge interne](load-balancer-get-started-ilb-classic-ps.md) et configurer le type de [mode de distribution](load-balancer-distribution-mode.md) pour un comportement de trafic réseau d’équilibrage de charge spécifique.

Si votre application doit maintenir des connexions actives pour les serveurs situés derrière un équilibreur de charge, vous pouvez obtenir plus d’informations sur les [paramètres de délai d'expiration TCP pour un équilibrage de charge](load-balancer-tcp-idle-timeout.md). Ainsi, vous en saurez plus sur le comportement d’une connexion inactive lorsque vous utilisez l'équilibreur de charge Azure.



<!--HONumber=Nov16_HO2-->


