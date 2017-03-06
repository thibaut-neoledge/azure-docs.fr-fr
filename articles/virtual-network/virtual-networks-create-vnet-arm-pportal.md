---
title: "Création d’un réseau virtuel - Portail Azure | Microsoft Docs"
description: "Découvrez comment créer un réseau virtuel au moyen du portail Azure."
services: virtual-network
documentationcenter: 
author: jimdial
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 4ad679a4-a959-4e48-a317-d9f5655a442b
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 11/8/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 63f2f6dde56c1b5c4b3ad2591700f43f6542874d
ms.openlocfilehash: 54094c18dcbb751835bfa56d53358ce19e08387d
ms.lasthandoff: 02/28/2017


---
# <a name="create-a-virtual-network-using-the-azure-portal"></a>Créer un réseau virtuel au moyen du portail Azure

[!INCLUDE [virtual-networks-create-vnet-intro](../../includes/virtual-networks-create-vnet-intro-include.md)]

Azure propose deux modèles de déploiement : Azure Resource Manager et classique. Microsoft recommande la création de ressources via le modèle de déploiement Resource Manager. Pour en savoir plus sur les différences entre deux modèles, lisez l’article [Comprendre les modèles de déploiement Azure](../azure-resource-manager/resource-manager-deployment-model.md).
 
Cet article expliquer comment créer un réseau virtuel dans le modèle de déploiement Resource Manager à l’aide du portail Azure. Vous pouvez également créer un réseau virtuel via Resource Manager à l’aide d’autres outils ou créer un réseau virtuel via le modèle de déploiement classique en sélectionnant une option différente dans la liste suivante :

> [!div class="op_single_selector"]
- [Portail](virtual-networks-create-vnet-arm-pportal.md)
- [PowerShell](virtual-networks-create-vnet-arm-ps.md)
- [INTERFACE DE LIGNE DE COMMANDE](virtual-networks-create-vnet-arm-cli.md)
- [Modèle](virtual-networks-create-vnet-arm-template-click.md)
- [Portail (classique)](virtual-networks-create-vnet-classic-pportal.md)
- [PowerShell (classique)](virtual-networks-create-vnet-classic-netcfg-ps.md)
- [Interface de ligne de commande (classique)](virtual-networks-create-vnet-classic-cli.md)


[!INCLUDE [virtual-networks-create-vnet-scenario-include](../../includes/virtual-networks-create-vnet-scenario-include.md)]

## <a name="create-a-virtual-network"></a>Créez un réseau virtuel

Pour créer un réseau virtuel à l’aide du portail Azure, procédez comme suit :

1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **Nouveau** > **Mise en réseau** > **Réseau virtuel**, comme illustré dans l’image suivante :

    ![Nouveau réseau virtuel](./media/virtual-network-create-vnet-arm-pportal/1.png)

3. Dans le panneau **Réseau virtuel** qui apparaît, vérifiez que *Resource Manager* est sélectionné, cliquez sur **Créer**, comme illustré dans l’image suivante :

    ![Réseau virtuel](./media/virtual-network-create-vnet-arm-pportal/2.png)
    
4. Dans le panneau **Créer un réseau virtuel** qui apparaît, entrez *TestVNet* pour **Nom**, *192.168.0.0/16* pour **Espace d’adressage**, *Frontal* pour **Nom du sous-réseau** *192.168.1.0/24* pour **Plage d’adresses de sous-réseau**, *TestRG* pour **Groupe de ressources**, sélectionnez votre **Abonnement**, un **Emplacement** et cliquez sur le bouton **Créer**, comme illustré dans l’image suivante :

    ![Création d’un réseau virtuel](./media/virtual-network-create-vnet-arm-pportal/3.png)

    Sinon, vous pouvez sélectionner un groupe de ressources existant. Pour plus d’informations sur les groupes de ressources, consultez l’article [Présentation de Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups). Vous pouvez également sélectionner un emplacement différent. Pour en savoir plus sur les emplacements et les régions Azure, lisez l’article [Régions Azure](https://azure.microsoft.com/regions).

5. Le portail vous permet seulement de créer un sous-réseau lors de la création d’un réseau virtuel. Pour ce scénario, un deuxième sous-réseau doit être créé une fois le réseau virtuel créé. Pour créer le deuxième sous-réseau, cliquez sur **Toutes les ressources**, puis sur **TestVNet** dans le panneau **Toutes les ressources**, comme illustré dans l’image suivante :

    ![Réseau virtuel créé](./media/virtual-network-create-vnet-arm-pportal/4.png)

6. Dans le panneau **TestVNet** qui apparaît, cliquez sur **Sous-réseau**, puis sur **+Sous-réseau**, entrez *Principal* pour **Nom**, *192.168.2.0/24* pour **Plage d’adresses** dans le panneau **Ajouter un sous-réseau**, puis cliquez sur **OK**, comme illustré dans l’image suivante :

    ![Ajouter un sous-réseau](./media/virtual-network-create-vnet-arm-pportal/5.png)

7. Les deux sous-réseaux sont répertoriés, comme illustré dans l’image suivante :
    
    ![Liste des sous-réseaux dans le réseau virtuel](./media/virtual-network-create-vnet-arm-pportal/6.png)

Cet article vous a expliqué comment créer un réseau virtuel avec deux sous-réseaux à des fins de test. Avant de créer un réseau virtuel pour l’utiliser en production, nous vous recommandons de lire les articles [Présentation du réseau virtuel](virtual-networks-overview.md) et [Planifier et concevoir des réseaux virtuels](virtual-network-vnet-plan-design-arm.md) afin de comprendre parfaitement les réseaux virtuels et tous les paramètres. 

## <a name="next-steps"></a>Étapes suivantes

Apprenez à connecter :

- Une machine virtuelle (VM) à un réseau virtuel en lisant les articles [Créer une machine virtuelle Windows](../virtual-machines/virtual-machines-windows-hero-tutorial.md) ou [Créer une machine virtuelle Linux](../virtual-machines/virtual-machines-linux-quick-create-portal.md). Au lieu de créer un réseau virtuel et un sous-réseau comme indiqué dans les procédures de ces articles, vous pouvez sélectionner un réseau virtuel et un sous-réseau existants pour établir la connexion à une machine virtuelle.
- Le réseau virtuel à d’autres réseaux virtuels en lisant l’article sur la [connexion des réseaux virtuels](../vpn-gateway/vpn-gateway-howto-vnet-vnet-resource-manager-portal.md).
- Le réseau virtuel à un réseau local à l’aide d’un réseau privé virtuel (VPN) site à site ou d’un circuit ExpressRoute. Découvrez comment en lisant les articles [Connecter un réseau virtuel à un réseau local à l’aide d’un VPN de site à site](../vpn-gateway/vpn-gateway-howto-multi-site-to-site-resource-manager-portal.md) et [Lier un réseau virtuel à un circuit ExpressRoute](../expressroute/expressroute-howto-linkvnet-portal-resource-manager.md).
