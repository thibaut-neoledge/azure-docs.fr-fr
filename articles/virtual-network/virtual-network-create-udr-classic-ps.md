---
title: "Contrôler le routage et utiliser des appliances virtuelles à l’aide de PowerShell | Microsoft Docs"
description: "Apprenez à contrôler le routage dans les réseaux virtuels à l’aide de PowerShell | Classic"
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-service-management
ms.assetid: d8d07c16-cbe5-4536-acd6-870269346fe3
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/02/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: ba7a67b8ae57da165f45bd3552a3dfac5f4ef64b
ms.openlocfilehash: 0bf0f3b64319a42a3b693f64ae705756b10be64e


---
# <a name="control-routing-and-use-virtual-appliances-classic-using-powershell"></a>Contrôle du routage et utilisation des appliances virtuelles (classiques) à l'aide de PowerShell

> [!div class="op_single_selector"]
- [PowerShell](virtual-network-create-udr-arm-ps.md)
- [Interface de ligne de commande Azure](virtual-network-create-udr-arm-cli.md)
- [Modèle](virtual-network-create-udr-arm-template.md)
- [PowerShell (classique)](virtual-network-create-udr-classic-ps.md)
- [Interface de ligne de commande (classique)](virtual-network-create-udr-classic-cli.md)

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Avant d’utiliser des ressources Azure, il est important de comprendre qu’Azure dispose actuellement de deux modèles de déploiement : Azure Resource Manager et classique. Veillez à bien comprendre les [modèles et outils de déploiement](../azure-resource-manager/resource-manager-deployment-model.md) avant d’utiliser une ressource Azure. Pour consulter la documentation des différents outils, sélectionnez une option en haut de cet article. Cet article traite du modèle de déploiement classique.
> 

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Les exemples de commandes Azure PowerShell ci-dessous supposent qu’un environnement simple a déjà été créé conformément au scénario décrit ci-dessus. Si vous souhaitez exécuter les commandes telles qu'elles sont présentées dans ce document, créez l'environnement décrit dans [Créer un VNet (classique) à l'aide de PowerShell](virtual-networks-create-vnet-classic-netcfg-ps.md).

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Création des itinéraires définis par l'utilisateur pour le sous-réseau frontal
Pour créer la table de routage et l'itinéraire nécessaires pour le sous-réseau frontal selon le scénario ci-dessus, suivez les étapes ci-dessous.

1. Exécutez la commande suivante pour créer une table de routage pour le sous-réseau frontal :

    ```powershell
    New-AzureRouteTable -Name UDR-FrontEnd -Location uswest `
    -Label "Route table for front end subnet"
    ```

    Output:
   
        Name         Location   Label                          
        ----         --------   -----                          
        UDR-FrontEnd West US    Route table for front end subnet
2. Exécutez la commande suivante pour créer un routage dans la table de routage pour envoyer tout le trafic destiné au sous-réseau Backend (192.168.2.0/24) à la machine virtuelle **FW1** (192.168.0.4) :

    ```powershell
    Get-AzureRouteTable UDR-FrontEnd `
    |Set-AzureRoute -RouteName RouteToBackEnd -AddressPrefix 192.168.2.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```
   
    Output:
   
        Name     : UDR-FrontEnd
        Location : West US
        Label    : Route table for frontend subnet
        Routes   : 
                   Name                 Address Prefix    Next hop type        Next hop IP address
                   ----                 --------------    -------------        -------------------
                   RouteToBackEnd       192.168.2.0/24    VirtualAppliance     192.168.0.4  
3. Exécutez la commande suivante pour associer la table de routage au sous-réseau **FrontEnd** :

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName FrontEnd `
    -RouteTableName UDR-FrontEnd
    ```

## <a name="create-the-udr-for-the-back-end-subnet"></a>Créer l’itinéraire défini par l’utilisateur pour le sous-réseau principal
Pour créer la table d’itinéraires et l’itinéraire nécessaires au sous-réseau principal, conformément au scénario, procédez comme suit :

1. Exécutez la commande suivante pour créer une table d’itinéraires pour le sous-réseau principal :

    ```powershell
    New-AzureRouteTable -Name UDR-BackEnd `
    -Location uswest `
    -Label "Route table for back end subnet"
    ```

2. Exécutez la commande suivante pour créer un itinéraire dans la table d’itinéraires pour envoyer tout le trafic destiné au sous-réseau frontal (192.168.1.0/24) à la machine virtuelle **FW1** (192.168.0.4) :

    ```powershell
    Get-AzureRouteTable UDR-BackEnd `
    |Set-AzureRoute -RouteName RouteToFrontEnd -AddressPrefix 192.168.1.0/24 `
    -NextHopType VirtualAppliance `
    -NextHopIpAddress 192.168.0.4
    ```

3. Exécutez la commande suivante pour associer la table de routage au sous-réseau **Backend** :

    ```powershell
    Set-AzureSubnetRouteTable -VirtualNetworkName TestVNet `
    -SubnetName BackEnd `
    -RouteTableName UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-the-fw1-vm"></a>Activer le transfert IP sur la machine virtuelle FW1

Pour activer le transfert IP dans la machine virtuelle FW1, procédez comme suit :

1. Exécutez la commande suivante pour vérifier l’état du transfert IP :

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Get-AzureIPForwarding
    ```

    Output:
   
        Disabled
2. Exécutez la commande suivante pour activer le transfert IP pour la machine virtuelle *FW1* :

    ```powershell
    Get-AzureVM -Name FW1 -ServiceName TestRGFW `
    | Set-AzureIPForwarding -Enable
    ```



<!--HONumber=Jan17_HO1-->


