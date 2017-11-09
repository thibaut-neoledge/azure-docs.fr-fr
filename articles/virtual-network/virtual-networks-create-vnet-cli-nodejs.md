---
title: "Création d’un réseau virtuel à l’aide d’Azure CLI 1.0 | Microsoft Docs"
description: "Découvrez comment créer un réseau virtuel à l’aide d’Azure CLI 1.0 | Resource Manager."
services: virtual-network
documentationcenter: 
author: jimdial
manager: carmonm
editor: 
tags: azure-resource-manager
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/16/2017
ms.author: jdial
ms.openlocfilehash: f0649c5c8c04dda72d2f147601efb37217f9bade
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="create-a-virtual-network-using-the-azure-cli"></a>Créer un réseau virtuel à l’aide d’Azure CLI

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure propose deux modèles de déploiement : Azure Resource Manager et classique. Microsoft recommande la création de ressources via le modèle de déploiement Resource Manager. Pour en savoir plus sur les différences entre deux modèles, lisez l’article [Comprendre les modèles de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md).

## <a name="cli-versions-to-complete-the-task"></a>Versions de l’interface de ligne de commande permettant d’effectuer la tâche
Vous pouvez exécuter la tâche en utilisant l’une des versions suivantes de l’interface de ligne de commande (CLI) :

- [Azure CLI 2.0](virtual-networks-create-vnet-arm-cli.md) : notre interface Azure CLI nouvelle génération pour le modèle de déploiement Resource Manager
- [Azure CLI 1.0](#create-a-virtual-network) : notre interface de ligne de commande pour les modèles de déploiement Classique et Resource Manager (cet article)

 
[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Pour créer un réseau virtuel à l’aide d’Azure CLI, procédez comme suit :

1. Installez et configurez Azure CLI en suivant les étapes décrites dans l’article [Installer et configurer l’interface de ligne de commande Azure](../cli-install-nodejs.md).

2. Créez un réseau virtuel et un sous-réseau :

    ```azurecli
    azure network vnet create --vnet TestVNet -e 192.168.0.0 -i 16 -n FrontEnd -p 192.168.1.0 -r 24 -l "Central US"
    ```

    Sortie attendue :
   
            info:    Executing command network vnet create
            + Looking up network configuration
            + Looking up locations
            + Setting network configuration
            info:    network vnet create command OK

    Paramètres utilisés :

   * **--vnet**. Nom du réseau virtuel à créer. Pour notre scénario, *TestVNet*
   * **-e (ou --address-space)**. Espace d'adressage du réseau virtuel. Pour notre scénario, *192.168.0.0*
   * **-i (ou -cidr)**. Masque de réseau au format CIDR. Pour notre scénario, *16*.
   * **-n (ou --subnet-name**). Nom du premier sous-réseau. Pour notre scénario, *FrontEnd*.
   * **-p (ou --subnet-start-ip)**. Adresse IP de début pour le sous-réseau, ou espace d'adressage du sous-réseau. Pour notre scénario, *192.168.1.0*.
   * **-r (ou --subnet-cidr)**. Masque de réseau au format CIDR pour le sous-réseau. Pour notre scénario, *24*.
   * **-l (ou --location)**. Région Azure où le réseau virtuel est créé. Pour notre scénario, *Centre des États-Unis*.

3. Créez un sous-réseau :

    ```azurecli
    azure network vnet subnet create -t TestVNet -n BackEnd -a 192.168.2.0/24
    ```
   
    Sortie attendue :

            info:    Executing command network vnet subnet create
            + Looking up network configuration
            + Creating subnet "BackEnd"
            + Setting network configuration
            + Looking up the subnet "BackEnd"
            + Looking up network configuration
            data:    Name                            : BackEnd
            data:    Address prefix                  : 192.168.2.0/24
            info:    network vnet subnet create command OK

    Paramètres utilisés :

   * **-t (ou --vnet-name**. Nom du réseau virtuel où sera créé le sous-réseau. Pour notre scénario, *TestVNet*.
   * **-n (ou --name)**. Nom du nouveau sous-réseau. Pour notre scénario, *BackEnd*.
   * **-a (ou --address-prefix)**. Bloc CIDR de sous-réseau. Pour notre scénario, *192.168.2.0/24*.
   
4. Pour afficher les propriétés du nouveau réseau virtuel :

    ```azurecli
    azure network vnet show
    ```
   
    Sortie attendue :
   
            info:    Executing command network vnet show
            Virtual network name: TestVNet
            + Looking up the virtual network sites
            data:    Name                            : TestVNet
            data:    Location                        : Central US
            data:    State                           : Created
            data:    Address space                   : 192.168.0.0/16
            data:    Subnets:
            data:      Name                          : FrontEnd
            data:      Address prefix                : 192.168.1.0/24
            data:
            data:      Name                          : BackEnd
            data:      Address prefix                : 192.168.2.0/24
            data:
            info:    network vnet show command OK

## <a name="next-steps"></a>Étapes suivantes

Apprenez à connecter :

- Une machine virtuelle à un réseau virtuel en lisant l’article [Création d’une machine virtuelle Linux](../virtual-machines/linux/quick-create-cli.md). Au lieu de créer un réseau virtuel et un sous-réseau comme indiqué dans les procédures de ces articles, vous pouvez sélectionner un réseau virtuel et un sous-réseau existants pour établir la connexion à une machine virtuelle.
- Le réseau virtuel à d’autres réseaux virtuels en lisant l’article sur la [connexion des réseaux virtuels](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
- Le réseau virtuel à un réseau local à l’aide d’un réseau privé virtuel (VPN) site à site ou d’un circuit ExpressRoute. Découvrez comment en lisant [Connecter un réseau virtuel à un réseau local à l’aide d’un VPN de site à site](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) et [Lier un réseau virtuel à un circuit ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).