---
title: "Contrôler le routage et les appliances virtuelles avec Azure CLI 2.0 | Microsoft Docs"
description: "Apprenez à contrôler le routage et les appliances virtuelles avec Azure CLI 2.0."
services: virtual-network
documentationcenter: na
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.assetid: 5452a0b8-21a6-4699-8d6a-e2d8faf32c25
ms.service: virtual-network
ms.devlang: azurecli
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/12/2017
ms.author: jdial
ms.translationtype: HT
ms.sourcegitcommit: f9003c65d1818952c6a019f81080d595791f63bf
ms.openlocfilehash: e5d9519998346619093f443b740c8904283f76e8
ms.contentlocale: fr-fr
ms.lasthandoff: 08/09/2017

---
# <a name="create-user-defined-routes-udr-using-the-azure-cli-20"></a>Créer des routages définis par l’utilisateur avec Azure CLI 2.0

> [!div class="op_single_selector"]
> * [PowerShell](virtual-network-create-udr-arm-ps.md)
> * [Interface de ligne de commande Azure](virtual-network-create-udr-arm-cli.md)
> * [Modèle](virtual-network-create-udr-arm-template.md)
> * [PowerShell (déploiement Classic)](virtual-network-create-udr-classic-ps.md)
> * [CLI (déploiement Classic)](virtual-network-create-udr-classic-cli.md)

## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche 

Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) : 

- [Azure CLI 1.0](virtual-network-create-udr-arm-cli-nodejs.md) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager 
- [Azure CLI 2.0 ](#Create-the-UDR-for-the-front-end-subnet) : notre interface Azure CLI nouvelle génération pour le modèle de déploiement Resource Manager (cet article)

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

[!INCLUDE [virtual-network-create-udr-intro-include.md](../../includes/virtual-network-create-udr-intro-include.md)]

[!INCLUDE [virtual-network-create-udr-scenario-include.md](../../includes/virtual-network-create-udr-scenario-include.md)]

Les exemples de commandes d’interface de ligne de commande PowerShell ci-dessous supposent qu’un environnement simple a déjà été créé conformément au scénario décrit ci-dessus. Si vous souhaitez exécuter les commandes telles qu’elles sont présentées dans ce document, commencez par créer l’environnement de test en déployant [ce modèle](http://github.com/telmosampaio/azure-templates/tree/master/IaaS-NSG-UDR-Before), cliquez sur **Déployer dans Azure**, remplacez les valeurs des paramètres par défaut si nécessaire, puis suivez les instructions dans le portail.


## <a name="create-the-udr-for-the-front-end-subnet"></a>Création de routages définis par l’utilisateur pour le sous-réseau frontal
Pour créer la table de routage et l'itinéraire nécessaires pour le sous-réseau frontal selon le scénario ci-dessus, suivez les étapes ci-dessous.

1. Créez une table de routage pour le sous-réseau frontal avec la commande [az network route-table create](/cli/azure/network/route-table#create) :

    ```azurecli
    az network route-table create \
    --resource-group testrg \
    --location centralus \
    --name UDR-FrontEnd
    ```

    Output:

    ```json
    {
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd",
    "location": "centralus",
    "name": "UDR-FrontEnd",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "routes": [],
    "subnets": null,
    "tags": null,
    "type": "Microsoft.Network/routeTables"
    }
    ```

2. Créez un itinéraire qui envoie tout le trafic destiné au sous-réseau frontal (192.168.2.0/24) à la machine virtuelle **FW1** (192.168.0.4) avec la commande [az network route-table route create](/cli/azure/network/route-table/route#create) :

    ```azurecli 
    az network route-table route create \
    --resource-group testrg \
    --name RouteToBackEnd \
    --route-table-name UDR-FrontEnd \
    --address-prefix 192.168.2.0/24 \
    --next-hop-type VirtualAppliance \
    --next-hop-ip-address 192.168.0.4
    ```

    Output:

    ```json
    {
    "addressPrefix": "192.168.2.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd/routes/RouteToBackEnd",
    "name": "RouteToBackEnd",
    "nextHopIpAddress": "192.168.0.4",
    "nextHopType": "VirtualAppliance",
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg"
    }
    ```
    Paramètres :

    * **--route-table-name**. Nom de la table de routage où l'itinéraire sera ajouté. Pour notre scénario, *UDR-FrontEnd*.
    * **--address-prefix**. Préfixe d'adresse pour le sous-réseau auquel les paquets sont destinés. Pour notre scénario, *192.168.2.0/24*.
    * **--next-hop-type**. Type d'objet vers lequel le trafic sera envoyé. Les valeurs possibles sont *VirtualAppliance*, *VirtualNetworkGateway*, *VNETLocal*, *Internet* ou *None*.
    * **--next-hop-ip-address**. Adresse IP de saut suivant. Pour notre scénario, *192.168.0.4*.

3. Exécutez la commande [az network vnet subnet update](/cli/azure/network/vnet/subnet#update) pour associer la table de routage créée ci-dessus au sous-réseau **frontal** :

    ```azurecli
    az network vnet subnet update \
    --resource-group testrg \
    --vnet-name testvnet \
    --name FrontEnd \
    --route-table UDR-FrontEnd
    ```

    Output:

    ```json
    {
    "addressPrefix": "192.168.1.0/24",
    "etag": "W/\"<guid>\"",
    "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/virtualNetworks/testvnet/subnets/FrontEnd",
    "ipConfigurations": null,
    "name": "FrontEnd",
    "networkSecurityGroup": null,
    "provisioningState": "Succeeded",
    "resourceGroup": "testrg",
    "resourceNavigationLinks": null,
    "routeTable": {
        "etag": null,
        "id": "/subscriptions/<guid>/resourceGroups/testrg/providers/Microsoft.Network/routeTables/UDR-FrontEnd",
        "location": null,
        "name": null,
        "provisioningState": null,
        "resourceGroup": "testrg",
        "routes": null,
        "subnets": null,
        "tags": null,
        "type": null
        }
    }
    ```

    Paramètres :
    
    * **--vnet-name**. Nom du réseau virtuel où le sous-réseau est situé. Pour notre scénario, *TestVNet*.

## <a name="create-the-udr-for-the-back-end-subnet"></a>Créer l’itinéraire défini par l’utilisateur pour le sous-réseau principal

Pour créer la table de routage et l’itinéraire nécessaires pour le sous-réseau Backend selon le scénario ci-dessus, suivez les étapes ci-dessous :

1. Exécutez la commande suivante pour créer une table d’itinéraires pour le sous-réseau principal :

    ```azurecli
    az network route-table create \
    --resource-group testrg \
    --name UDR-BackEnd \
    --location centralus
    ```

2. Exécutez la commande suivante pour créer un itinéraire dans la table d’itinéraires pour envoyer tout le trafic destiné au sous-réseau frontal (192.168.1.0/24) à la machine virtuelle **FW1** (192.168.0.4) :

    ```azurecli
    az network route-table route create \
    --resource-group testrg \
    --name RouteToFrontEnd \
    --route-table-name UDR-BackEnd \
    --address-prefix 192.168.1.0/24 \
    --next-hop-type VirtualAppliance \
    --next-hop-ip-address 192.168.0.4
    ```

3. Exécutez la commande suivante pour associer la table de routage créée ci-dessus au sous-réseau **Backend** :

    ```azurecli
    az network vnet subnet update \
    --resource-group testrg \
    --vnet-name testvnet \
    --name BackEnd \
    --route-table UDR-BackEnd
    ```

## <a name="enable-ip-forwarding-on-fw1"></a>Activer le transfert IP sur FW1

Pour activer le transfert IP sur la carte réseau utilisée par **FW1**, procédez comme suit :

1. Exécutez la commande [az network nic show](/cli/azure/network/nic#show) avec un filtre JMESPATH pour afficher la valeur **enable-ip-forwarding** actuelle de **Activer le transfert IP**. Cette option doit avoir la valeur *false*.

    ```azurecli
    az network nic show \
    --resource-group testrg \
    --nname nicfw1 \
    --query 'enableIpForwarding' -o tsv
    ```

    Output:

        false

2. Exécutez la commande suivante pour activer le transfert IP :

    ```azurecli
    az network nic update \
    --resource-group testrg \
    --name nicfw1 \
    --ip-forwarding true
    ```

    Vous pouvez examiner la sortie transmise à la console, ou simplement tester spécifiquement la valeur **enableIpForwarding** à nouveau :

    ```azurecli
    az network nic show -g testrg -n nicfw1 --query 'enableIpForwarding' -o tsv
    ```

    Output:

        true

    Paramètres :

    **--ip-forwarding** : *true* ou *false*.


