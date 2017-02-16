---
title: "Contrôler le routage et les appliances virtuelles à l’aide d’Azure CLI | Microsoft Docs"
description: "Apprenez à contrôler le routage et des appliances virtuelles à l’aide d’Azure CLI."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 5452a0b8-21a6-4699-8d6a-e2d8faf32c25
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/15/2016
ms.author: jdial
translationtype: Human Translation
ms.sourcegitcommit: a9b48f149427e5ceb69bcaa97b1bf08519499b6f
ms.openlocfilehash: 6d03903d662ecac24fd4afc47134ce9f39af484f


---
# <a name="create-user-defined-routes-udr-using-the-azure-cli"></a>Créer des routages définis par l’utilisateur à l’aide d’Azure CLI

> [!div class="op_single_selector"]
- [PowerShell](virtual-network-create-udr-arm-ps.md)
- [Interface de ligne de commande Azure](virtual-network-create-udr-arm-cli.md)
- [Modèle](virtual-network-create-udr-arm-template.md)
- [PowerShell (classique)](virtual-network-create-udr-classic-ps.md)
- [Interface de ligne de commande (classique)](virtual-network-create-udr-classic-cli.md)


[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

> [!IMPORTANT]
> Avant d’utiliser des ressources Azure, il est important de comprendre qu’Azure dispose actuellement de deux modèles de déploiement : Azure Resource Manager et classique. Veillez à bien comprendre les [modèles et outils de déploiement](../azure-resource-manager/resource-manager-deployment-model.md) avant d’utiliser une ressource Azure. Pour consulter la documentation relative aux différents outils, cliquez sur les onglets situés en haut de cet article.
>

Cet article traite du modèle de déploiement de Resource Manager. Vous pouvez également [créer des routages définis par l’utilisateur dans le modèle de déploiement Classic](virtual-network-create-udr-classic-cli.md).

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Les exemples de commandes d’interface de ligne de commande PowerShell ci-dessous supposent qu’un environnement simple a déjà été créé conformément au scénario décrit ci-dessus. Si vous souhaitez exécuter les commandes telles qu’elles sont présentées dans ce document, commencez par créer l’environnement de test en déployant [ce modèle](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before), cliquez sur **Déployer dans Azure**, remplacez les valeurs des paramètres par défaut si nécessaire, puis suivez les instructions dans le portail.

[!INCLUDE [azure-cli-prerequisites-include.md](../../includes/azure-cli-prerequisites-include.md)]

## <a name="create-the-udr-for-the-front-end-subnet"></a>Création de routages définis par l’utilisateur pour le sous-réseau frontal
Pour créer la table de routage et l'itinéraire nécessaires pour le sous-réseau frontal selon le scénario ci-dessus, suivez les étapes ci-dessous.

1. Exécutez la commande suivante pour créer une table de routage pour le sous-réseau frontal :

    ```azurecli
    azure network route-table create -g TestRG -n UDR-FrontEnd -l uswest
    ```
   
    Output:
   
        info:    Executing command network route-table create
        info:    Looking up route table "UDR-FrontEnd"
        info:    Creating route table "UDR-FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    Name                            : UDR-FrontEnd
        data:    Type                            : Microsoft.Network/routeTables
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        info:    network route-table create command OK
   
    Paramètres :
   
   * **-g (ou --resource-group)**. Nom du groupe de ressources dans lequel sera créé l’itinéraire défini par l’utilisateur (UDR). Pour notre scénario, *TestRG*.
   * **-l (ou --location)**. Région Azure dans laquelle l’itinéraire défini par l’utilisateur (UDR) sera créé. Pour notre scénario, *westus*.
   * **-n (ou --name)**. Nom du nouvel itinéraire défini par l’utilisateur (UDR). Pour notre scénario, *UDR-FrontEnd*.
2. Exécutez la commande suivante pour créer un routage dans la table de routage pour envoyer tout le trafic destiné au sous-réseau Backend (192.168.2.0/24) à la machine virtuelle **FW1** (192.168.0.4) :

    ```azurecli
    azure network route-table route create -g TestRG -r UDR-FrontEnd -n RouteToBackEnd -a 192.168.2.0/24 -y VirtualAppliance -p 192.168.0.4
    ```
   
    Output:
   
        info:    Executing command network route-table route create
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        info:    Creating route "RouteToBackEnd" in a route table "UDR-FrontEnd"
        info:    Looking up route "RouteToBackEnd" in route table "UDR-FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd/routes/RouteToBackEnd
        data:    Name                            : RouteToBackEnd
        data:    Provisioning state              : Succeeded
        data:    Next hop type                   : VirtualAppliance
        data:    Next hop IP address             : 192.168.0.4
        data:    Address prefix                  : 192.168.2.0/24
        info:    network route-table route create command OK
   
    Paramètres :
   
   * **-r (ou --route-table-name)**. Nom de la table de routage où l'itinéraire sera ajouté. Pour notre scénario, *UDR-FrontEnd*.
   * **-a (ou --address-prefix)**. Préfixe d'adresse pour le sous-réseau auquel les paquets sont destinés. Pour notre scénario, *192.168.2.0/24*.
   * **-y (ou --next-hop-type)**. Type d'objet vers lequel le trafic sera envoyé. Les valeurs possibles sont *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* ou *None*.
   * **-p (ou --next-hop-ip-address**). Adresse IP de saut suivant. Pour notre scénario, *192.168.0.4*.
3. Exécutez la commande suivante pour associer la table de routage créée ci-dessus au sous-réseau **FrontEnd** :

    ```azurecli
    azure network vnet subnet set -g TestRG -e TestVNet -n FrontEnd -r UDR-FrontEnd
    ```
   
    Output:
   
        info:    Executing command network vnet subnet set
        info:    Looking up the subnet "FrontEnd"
        info:    Looking up route table "UDR-FrontEnd"
        info:    Setting subnet "FrontEnd"
        info:    Looking up the subnet "FrontEnd"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/FrontEnd
        data:    Type                            : Microsoft.Network/virtualNetworks/subnets
        data:    ProvisioningState               : Succeeded
        data:    Name                            : FrontEnd
        data:    Address prefix                  : 192.168.1.0/24
        data:    Network security group          : [object Object]
        data:    Route Table                     : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        routeTables/UDR-FrontEnd
        data:    IP configurations:
        data:      /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB1/ipConf
        igurations/ipconfig1
        data:      /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/networkInterfaces/NICWEB2/ipConf
        igurations/ipconfig1
        data:    
        info:    network vnet subnet set command OK
   
    Paramètres :
   
   * **-e (ou --vnet-name)**. Nom du réseau virtuel où le sous-réseau est situé. Pour notre scénario, *TestVNet*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Créer l’itinéraire défini par l’utilisateur pour le sous-réseau principal
Pour créer la table de routage et l’itinéraire nécessaires pour le sous-réseau Backend selon le scénario ci-dessus, suivez les étapes ci-dessous :

1. Exécutez la commande suivante pour créer une table d’itinéraires pour le sous-réseau principal :

    ```azurecli
    azure network route-table create -g TestRG -n UDR-BackEnd -l westus
    ```

2. Exécutez la commande suivante pour créer un itinéraire dans la table d’itinéraires pour envoyer tout le trafic destiné au sous-réseau frontal (192.168.1.0/24) à la machine virtuelle **FW1** (192.168.0.4) :

    ```azurecli
    azure network route-table route create -g TestRG -r UDR-BackEnd -n RouteToFrontEnd -a 192.168.1.0/24 -y VirtualAppliance -p 192.168.0.4
    ```

3. Exécutez la commande suivante pour associer la table de routage créée ci-dessus au sous-réseau **Backend** :

    ```azurecli
    azure network vnet subnet set -g TestRG -e TestVNet -n BackEnd -r UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-fw1"></a>Activer le transfert IP sur FW1
Pour activer le transfert IP sur la carte réseau utilisée par **FW1**, procédez comme suit :

1. Exécutez la commande suivante et notez la valeur attribuée à **Activer le transfert IP**. Cette option doit avoir la valeur *false*.

    ```azurecli
    azure network nic show -g TestRG -n NICFW1
    ```

    Output:
   
        info:    Executing command network nic show
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : false
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic show command OK
2. Exécutez la commande suivante pour activer le transfert IP :

    ```azurecli
    azure network nic set -g TestRG -n NICFW1 -f true
    ```
   
    Output:
   
        info:    Executing command network nic set
        info:    Looking up the network interface "NICFW1"
        info:    Updating network interface "NICFW1"
        info:    Looking up the network interface "NICFW1"
        data:    Id                              : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        networkInterfaces/NICFW1
        data:    Name                            : NICFW1
        data:    Type                            : Microsoft.Network/networkInterfaces
        data:    Location                        : westus
        data:    Provisioning state              : Succeeded
        data:    MAC address                     : 00-0D-3A-30-95-B3
        data:    Enable IP forwarding            : true
        data:    Tags                            : displayName=NetworkInterfaces - DMZ
        data:    Virtual machine                 : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Compute/
        virtualMachines/FW1
        data:    IP configurations:
        data:      Name                          : ipconfig1
        data:      Provisioning state            : Succeeded
        data:      Public IP address             : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        publicIPAddresses/PIPFW1
        data:      Private IP address            : 192.168.0.4
        data:      Private IP Allocation Method  : Static
        data:      Subnet                        : /subscriptions/[Subscription Id]/resourceGroups/TestRG/providers/Microsoft.Network/
        virtualNetworks/TestVNet/subnets/DMZ
        data:    
        info:    network nic set command OK
   
    Paramètres :
   
   * **-f (ou --enable-ip-forwarding)**. *true* ou *false*.




<!--HONumber=Jan17_HO1-->


