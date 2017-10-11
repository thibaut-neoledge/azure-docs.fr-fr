---
title: "Gérer les groupes de sécurité réseau - Portail Azure | Microsoft Docs"
description: "Découvrez comment gérer les groupes de sécurité réseau à l’aide du portail Azure."
services: virtual-network
documentationcenter: na
author: jimdial
manager: timlt
editor: tysonn
tags: azure-resource-manager
ms.assetid: faee5ac8-f4c4-4f97-ade5-197a37aad496
ms.service: virtual-network
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/04/2016
ms.author: jdial
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: ecb4fb4608628f5a1bd54fac6af19fecfa4508f2
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="manage-network-security-groups-using-the-azure-portal"></a>Gérer les groupes de sécurité réseau à l’aide du portail Azure

[!INCLUDE [virtual-networks-create-nsg-selectors-arm-include](../../includes/virtual-networks-create-nsg-selectors-arm-include.md)]

[!INCLUDE [virtual-networks-create-nsg-intro-include](../../includes/virtual-networks-create-nsg-intro-include.md)]

[!INCLUDE [azure-arm-classic-important-include](../../includes/azure-arm-classic-important-include.md)]

Cet article traite du modèle de déploiement de Resource Manager. Vous pouvez également [créer des groupes de sécurité réseau dans le modèle de déploiement classique](virtual-networks-create-nsg-classic-ps.md).

[!INCLUDE [virtual-networks-create-nsg-scenario-include](../../includes/virtual-networks-create-nsg-scenario-include.md)]

Les exemples de commandes PowerShell ci-dessous supposent qu’un environnement simple a déjà été créé conformément au scénario décrit ci-dessous. Si vous souhaitez exécuter les commandes telles qu’elles sont présentées dans ce document, commencez par créer l’environnement de test en déployant [ce modèle](http://github.com/telmosampaio/azure-templates/tree/master/201-IaaS-WebFrontEnd-SQLBackEnd), cliquez sur **Déployer dans Azure**, remplacez les valeurs des paramètres par défaut si nécessaire, puis suivez les instructions dans le portail. Les étapes ci-dessous utilisent **RG-NSG** comme nom du groupe de ressources vers lequel le modèle a été déployé.

## <a name="create-the-nsg-frontend-nsg"></a>Créer le groupe de sécurité réseau NSG-FrontEnd
Pour créer le groupe de sécurité réseau **NSG-FrontEnd** selon le scénario ci-dessus, suivez les étapes ci-dessous.

1. Dans un navigateur, accédez à http://portal.azure.com et, si nécessaire, connectez-vous avec votre compte Azure.
2. Cliquez sur **Parcourir >** > **Groupes de sécurité réseau**.
   
    ![Portail Azure - Groupes de sécurité réseau](./media/virtual-networks-create-nsg-arm-pportal/figure11.png)
3. Dans le panneau **Groupes de sécurité réseau**, cliquez sur **Ajouter**.
   
    ![Portail Azure - Groupes de sécurité réseau](./media/virtual-networks-create-nsg-arm-pportal/figure12.png)
4. Dans le panneau **Créer un groupe de sécurité réseau**, créez un groupe de sécurité réseau nommé *NSG-FrontEnd* dans le groupe de ressources *RG-NSG*, puis cliquez sur **Créer**.
   
    ![Portail Azure - Groupes de sécurité réseau](./media/virtual-networks-create-nsg-arm-pportal/figure13.png)

## <a name="create-rules-in-an-existing-nsg"></a>Création de règles dans un NSG existant
Pour créer des règles dans un groupe de sécurité réseau existant à partir du portail Azure, suivez les étapes ci-dessous.

1. Cliquez sur **Parcourir >** > **Groupes de sécurité réseau**.
2. Dans la liste des NSGs, cliquez sur **NSG-FrontEnd** > **Règles de sécurité de trafic entrant**
   
    ![Portail Azure - NSG-FrontEnd](./media/virtual-networks-create-nsg-arm-pportal/figure2.png)
3. Dans la liste **Règles de sécurité de trafic entrant**, cliquez sur **Ajouter**.
   
    ![Portail Azure - Ajouter une règle](./media/virtual-networks-create-nsg-arm-pportal/figure3.png)
4. Dans le panneau **Ajouter une règle de sécurité de trafic entrant**, créez une règle nommée *web-rule* avec une priorité de *200* autorisant l’accès via *TCP* sur le port *80* à une machine virtuelle de n’importe quelle source, puis cliquez sur **OK**. Notez que la plupart de ces paramètres sont déjà des valeurs par défaut.
   
    ![Portail Azure - Paramètres des règles](./media/virtual-networks-create-nsg-arm-pportal/figure4.png)
5. Après quelques secondes, vous voyez la nouvelle règle dans le NSG.
   
    ![Portail Azure - Nouvelle règle](./media/virtual-networks-create-nsg-arm-pportal/figure5.png)
6. Répétez les étapes 1 à 6 pour créer une règle de trafic entrant nommée *rdp-rule* avec la priorité *250* autorisant l’accès via *TCP* sur le port *3389* à une machine virtuelle de n’importe quelle source.

## <a name="associate-the-nsg-to-the-frontend-subnet"></a>Associer le groupe de sécurité réseau au sous-réseau FrontEnd
1. Cliquez sur **Parcourir >** > **Groupes de ressources** > **RG-NSG**.
2. Dans le panneau **RG-NSG**, cliquez sur **...** > **TestVNet**.
   
    ![Portail Azure - TestVNet](./media/virtual-networks-create-nsg-arm-pportal/figure14.png)
3. Dans le panneau **Paramètres**, cliquez sur **Sous-réseaux** > **FrontEnd** > **Groupe de sécurité réseau** > **NSG-FrontEnd**.
   
    ![Portail Azure - Paramètres de sous-réseau](./media/virtual-networks-create-nsg-arm-pportal/figure15.png)
4. Dans le panneau **FrontEnd**, cliquez sur **Enregistrer**.
   
    ![Portail Azure - Paramètres de sous-réseau](./media/virtual-networks-create-nsg-arm-pportal/figure16.png)

## <a name="create-the-nsg-backend-nsg"></a>Créer le groupe de sécurité réseau NSG-BackEnd
Pour créer le groupe de sécurité réseau **NSG-BackEnd** et l’associer au sous-réseau **BackEnd**, suivez les étapes ci-dessous.

1. Répétez les étapes de la section [Créer le groupe de sécurité réseau NSG-FrontEnd](#Create-the-NSG-FrontEnd-NSG) pour créer un groupe de sécurité réseau nommé *NSG-BackEnd*
2. Répétez les étapes de la section [Créer des règles dans un groupe de sécurité réseau existant](#Create-rules-in-an-existing-NSG) pour créer les règles **entrantes** dans le tableau ci-dessous.
   
   | Règle de trafic entrant | Règle de trafic sortant |
   | --- | --- |
   | ![Portail Azure - Règle entrante](./media/virtual-networks-create-nsg-arm-pportal/figure17.png) |![Portail Azure - Règle entrante](./media/virtual-networks-create-nsg-arm-pportal/figure18.png) |
3. Répétez les étapes de la section [Associer le groupe de sécurité réseau au sous-réseau FrontEnd](#Associate-the-NSG-to-the-FrontEnd-subnet) pour associer le groupe de sécurité réseau **NSG-Backend** au sous-réseau **BackEnd**.

## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment [gérer des groupes de sécurité réseau existants](virtual-network-manage-nsg-arm-portal.md)
* [Activez la journalisation](virtual-network-nsg-manage-log.md) des groupes de sécurité réseau.

