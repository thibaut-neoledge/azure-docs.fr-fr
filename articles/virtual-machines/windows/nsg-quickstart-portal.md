---
title: "Ouvrir des ports sur une machine virtuelle à l’aide du portail Azure | Microsoft Docs"
description: "Découvrez comment ouvrir un port / créer un point de terminaison sur votre machine virtuelle Windows à l’aide du modèle de déploiement Resource Manager dans le Portail Azure"
services: virtual-machines-windows
documentationcenter: 
author: iainfoulds
manager: timlt
editor: 
ms.assetid: f7cf0319-5ee7-435e-8f94-c484bf5ee6f1
ms.service: virtual-machines-windows
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: vm-windows
ms.workload: infrastructure-services
ms.date: 05/11/2017
ms.author: iainfou
ms.translationtype: Human Translation
ms.sourcegitcommit: 97fa1d1d4dd81b055d5d3a10b6d812eaa9b86214
ms.openlocfilehash: 20aa8247a16195310a8cb03c13c34186456fd7af
ms.contentlocale: fr-fr
ms.lasthandoff: 05/11/2017


---
# <a name="how-to-open-ports-to-a-virtual-machine-with-the-azure-portal"></a>Guide d’ouverture de ports vers une machine virtuelle avec le portail Azure
[!INCLUDE [virtual-machines-common-nsg-quickstart](../../../includes/virtual-machines-common-nsg-quickstart.md)]

## <a name="quick-commands"></a>Commandes rapides
Vous pouvez également [effectuer ces étapes à l’aide d’Azure PowerShell](nsg-quickstart-powershell.md).

Créez d’abord votre Groupe de sécurité réseau. Sélectionnez un groupe de ressources dans le portail, cliquez sur **Ajouter**, puis recherchez et sélectionnez ’Groupe de sécurité de réseau’ :

![Ajouter un groupe de sécurité réseau](./media/nsg-quickstart-portal/add-nsg.png)

Entrez un nom pour votre groupe de sécurité réseau, sélectionnez ou créez un groupe de ressources, et sélectionnez un emplacement. Lorsque vous avez terminé, cliquez sur **Créer** :

![Création d'un groupe de sécurité réseau](./media/nsg-quickstart-portal/create-nsg.png)

Sélectionnez un nouveau groupe de sécurité réseau. Sélectionnez « Règles de sécurité entrantes », puis cliquez sur le bouton **Ajouter** pour créer une règle :

![Ajouter une règle de trafic entrant](./media/nsg-quickstart-portal/add-inbound-rule.png)

Entrez un nom pour votre nouvelle règle. Le port 80 est déjà entré par défaut. Sur ce panneau, vous pouvez modifier la source, le protocole et la destination lors de l’ajout de règles supplémentaires à votre groupe de sécurité réseau. Cliquez sur **OK** pour créer la règle :

![Créer une règle de trafic entrant](./media/nsg-quickstart-portal/create-inbound-rule.png)

L’étape finale consiste à associer un sous-réseau ou une interface réseau spécifique à votre groupe de sécurité réseau. Associons le groupe de sécurité réseau à un sous-réseau. Sélectionnez ’Sous-réseaux’, puis cliquez sur **Associer** :

![Associer un groupe de sécurité réseau à un sous-réseau](./media/nsg-quickstart-portal/associate-subnet.png)

Sélectionnez votre réseau virtuel, puis sélectionnez le sous-réseau approprié :

![Association d’un groupe de sécurité réseau à un réseau virtuel](./media/nsg-quickstart-portal/select-vnet-subnet.png)

Vous avez maintenant créé un groupe de sécurité réseau, créé une règle de trafic entrant qui autorise le trafic sur le port 80 et l’avez associé à un sous-réseau. Toute machine virtuelle que vous connectez à ce sous-réseau est accessible sur le port 80.

## <a name="more-information-on-network-security-groups"></a>En savoir plus sur les groupes de sécurité réseau
Les commandes rapides vous permettent d’être opérationnel avec le trafic entrant vers votre machine virtuelle. Les groupes de sécurité réseau fournissent un grand nombre de fonctionnalités intéressantes et une granularité pour contrôler l’accès à vos ressources. Découvrez plus d’informations sur la [création d’un groupe de sécurité réseau et de règles de liste de contrôle d’accès ici](../../virtual-network/virtual-networks-create-nsg-arm-ps.md).

Vous pouvez définir des groupes de sécurité réseau et des règles de liste de contrôle d’accès dans le cadre de modèles Azure Resource Manager. En savoir plus sur la [création de groupes de sécurité réseau avec des modèles](../../virtual-network/virtual-networks-create-nsg-arm-template.md).

Si vous devez utiliser le réacheminement de port pour mapper un seul port externe sur un port interne de votre machine virtuelle, utilisez un équilibreur de charge et des règles de traduction d’adresses réseau (NAT). Par exemple, vous souhaitez peut-être exposer le port TCP 8080 en externe et diriger le trafic vers le port TCP 80 sur une machine virtuelle. En savoir plus sur [la création d'un équilibreur de charge accessible sur Internet](../../load-balancer/load-balancer-get-started-internet-arm-ps.md)

## <a name="next-steps"></a>Étapes suivantes
Dans cet exemple, vous avez créé une règle simple pour autoriser le trafic HTTP. Vous trouverez plus d’informations sur la création d’environnements plus détaillés dans les articles suivants :

* [Présentation d’Azure Resource Manager](../../azure-resource-manager/resource-group-overview.md)
* [Présentation du groupe de sécurité réseau](../../virtual-network/virtual-networks-nsg.md)
* [Présentation d’Azure Resource Manager](../../load-balancer/load-balancer-arm.md)


