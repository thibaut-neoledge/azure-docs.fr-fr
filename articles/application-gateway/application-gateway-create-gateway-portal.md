---
title: "Créer une passerelle Application Gateway - Portail Azure | Microsoft Docs"
description: "Découvrez comment créer une passerelle Application Gateway à l’aide du portail"
services: application-gateway
documentationcenter: na
author: davidmu1
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 54dffe95-d802-4f86-9e2e-293f49bd1e06
ms.service: application-gateway
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 07/31/2017
ms.author: davidmu
ms.openlocfilehash: 17d09ce98c40717d1db0927f791a7c97ea7835e0
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="create-an-application-gateway-with-the-portal"></a>Créer une passerelle Application Gateway avec le portail

[Application Gateway](application-gateway-introduction.md) est une appliance virtuelle dédiée qui intègre Application Delivery Controller (ADC) en tant que service, offrant diverses fonctionnalités d’équilibrage de charge de couche 7 pour votre application. Cet article vous guide tout au long de la création d’une passerelle Application Gateway avec le portail Azure et l’ajout d’un serveur existant en tant que membre principal.

## <a name="log-in-to-azure"></a>Connexion à Azure

Connectez-vous au portail Azure à l’adresse [http://portal.azure.com](http://portal.azure.com)

## <a name="create-application-gateway"></a>Créer une passerelle Application Gateway

Pour créer une passerelle Application Gateway, procédez comme suit : La passerelle Application Gateway Azure requiert son propre sous-réseau. Lorsque vous créez un réseau virtuel, assurez-vous que vous laissez suffisamment d’espace d’adresse pour disposer de plusieurs sous-réseaux. Une fois la passerelle Application Gateway déployée sur un sous-réseau, seules d’autres passerelles Application Gateway peuvent être ajoutées.

1. Dans le volet Favoris, cliquez sur **Nouveau**.
1. Dans le panneau **Nouveau**, cliquez sur **Mise en réseau**. Dans le panneau **Mise en réseau**, cliquez sur **Application Gateway**, comme indiqué dans l’image suivante :

    ![Création d’une passerelle Application Gateway][1]

1. Dans le panneau **De base** qui s’affiche, entrez les valeurs suivantes et cliquez sur **OK** :

   | **Paramètre** | **Valeur** | **Détails**|
   |---|---|---|
   |**Nom**|AdatumAppGateway|Nom de la passerelle Application Gateway|
   |**Niveau**|Standard|Les valeurs disponibles sont Standard et WAF. Consultez la page [Pare-feu d’applications web (WAF)](application-gateway-web-application-firewall-overview.md) pour en savoir plus sur WAF.|
   |**Taille de la référence (SKU)**|Moyenne|Si vous sélectionnez le niveau Standard, vous avez le choix entre Petite, Moyenne et Grande. Si vous choisissez le niveau WAF, les options sont limitées à Moyenne et Grande.|
   |**Nombre d’instances**|2|Nombre d’instances de la passerelle Application Gateway pour la haute disponibilité. Un nombre d’instances de 1 doit être utilisé uniquement à des fins de test.|
   |**Abonnement**|[Votre abonnement]|Sélectionnez l’abonnement dans lequel créer la passerelle de l’application.|
   |**Groupe de ressources**|**Créer un nouveau :** AdatumAppGatewayRG|Créez un groupe de ressources. Le nom du groupe de ressources doit être unique au sein de l’abonnement sélectionné. Pour plus d’informations sur les groupes de ressources, consultez l’article [Présentation de Resource Manager](../azure-resource-manager/resource-group-overview.md?toc=%2fazure%2fapplication-gateway%2ftoc.json#resource-groups).|
   |**Emplacement**|Ouest des États-Unis||

1. Dans le panneau **Paramètres** qui s’affiche sous **Réseau virtuel**, cliquez sur **Choisir un réseau virtuel**. Le panneau **Choisir un réseau virtuel** s’affiche.  Cliquez sur **Créer un nouveau** pour ouvrir le panneau **Créer un réseau virtuel**.

   ![choisir un réseau virtuel][2]

1. Dans le panneau **Créer un réseau virtuel**, entrez les valeurs suivantes, puis cliquez sur **OK**. Les panneaux **Créer un réseau virtuel** et **Choisir un réseau virtuel** se ferment. En outre, le champ **Sous-réseau** du panneau **Paramètres** est automatiquement renseigné avec le nom du sous-réseau sélectionné.

   | **Paramètre** | **Valeur** | **Détails**|
   |---|---|---|
   |**Nom**|AdatumAppGatewayVNET|Nom de la passerelle Application Gateway|
   |**Espace d’adressage**|10.0.0.0/16|Espace d’adressage du réseau virtuel|
   |**Nom du sous-réseau**|AppGatewaySubnet|Nom du sous-réseau de la passerelle Application Gateway|
   |**Plage d’adresses de sous-réseau**|10.0.0.0/28|Ce sous-réseau autorise plusieurs sous-réseaux supplémentaires dans le réseau virtuel pour les membres du pool principal|

1. Dans le panneau **Paramètres**, sous **Configuration d’adresse IP frontale**, choisissez **Public** comme **Type d’adresse IP**

1. Dans le panneau **Paramètres**, sous **Adresse IP publique**, cliquez sur **Choisir une adresse IP publique** pour accéder au panneau **Choisir une adresse IP publique**, puis cliquez sur **Créer**.

   ![choisir une adresse ip publique][3]

1. Dans le panneau **Créer une adresse IP publique**, acceptez la valeur par défaut et cliquez sur **OK**. Le panneau se ferme et le champ **Adresse IP publique** est renseigné avec l’adresse IP publique choisie.

1. Dans le panneau **Paramètres**, sous **Configuration de l’écouteur**, cliquez sur **HTTP** sous **Protocole**. Entrez le port à utiliser dans le champ **Port**.

2. Cliquez sur **OK** dans le panneau **Paramètres** pour continuer.

1. Passez en revue les paramètres du panneau **Résumé** et cliquez sur **OK** pour démarrer la création de la passerelle Application Gateway. La création d’une passerelle Application Gateway est une tâche fastidieuse.

## <a name="add-servers-to-backend-pools"></a>Ajouter des serveurs aux pools principaux

Une fois la passerelle Application Gateway créée, il reste à y ajouter les systèmes qui hébergent l’application dont la charge doit être équilibrée. Les adresses IP, les noms de domaine complets ou les cartes réseaux de ces serveurs sont ajoutés aux pools d’adresses principaux.

### <a name="ip-address-or-fqdn"></a>Adresse IP ou nom de domaine complet

1. Une fois la passerelle Application Gateway créée, allez dans le panneau **Favoris** du portail Azure, puis cliquez sur **Toutes les ressources**. Cliquez sur la passerelle **AdatumAppGateway** dans le panneau Toutes les ressources. Si l’abonnement que vous avez déjà sélectionné comporte plusieurs ressources, vous pouvez saisir **AdatumAppGateway** dans la case **Filtrer par nom…** pour accéder facilement à la passerelle d’application.

1. La passerelle Application Gateway que vous avez créée s’affiche. Cliquez sur **Pools principaux**, puis sélectionnez le pool principal actuel **appGatewayBackendPool** pour accéder au panneau **appGatewayBackendPool**.

   ![Pools principaux Application Gateway][4]

1. Cliquez sur **Ajouter une cible** pour ajouter des adresses IP ou des valeurs de nom de domaine complet. Choisissez **Adresse IP ou nom de domaine complet** comme **Type** et entrez votre adresse IP ou le nom de domaine complet dans le champ. Répétez cette étape pour les autres membres du pool principal. Une fois que vous avez terminé, cliquez sur **Enregistrer**.

### <a name="virtual-machine-and-nic"></a>Machine virtuelle et carte d’interface réseau

Vous pouvez également ajouter des cartes d’interface réseau de machine virtuelle en tant que membres de pool principal. Seules les machines virtuelles s’exécutant dans le même réseau virtuel que la passerelle Application Gateway peuvent être sélectionnées.

1. Une fois la passerelle Application Gateway créée, allez dans le panneau **Favoris** du portail Azure, puis cliquez sur **Toutes les ressources**. Cliquez sur la passerelle **AdatumAppGateway** dans le panneau Toutes les ressources. Si l’abonnement que vous avez déjà sélectionné comporte plusieurs ressources, vous pouvez saisir **AdatumAppGateway** dans la case **Filtrer par nom…** pour accéder facilement à la passerelle d’application.

1. La passerelle Application Gateway que vous avez créée s’affiche. Cliquez sur **Pools principaux**, puis sélectionnez le pool principal actuel **appGatewayBackendPool** pour accéder au panneau **appGatewayBackendPool**.

   ![Pools principaux Application Gateway][5]

1. Cliquez sur **Ajouter une cible** pour ajouter des adresses IP ou des valeurs de nom de domaine complet. Choisissez **Machine virtuelle** comme **Type** et sélectionnez la machine virtuelle et la carte réseau à utiliser. Une fois que vous avez terminé, cliquez sur **Enregistrer**.

   > [!NOTE]
   > Seules les machines virtuelles s’exécutant dans le même réseau virtuel que la passerelle Application Gateway peuvent être sélectionnées dans la liste déroulante.

## <a name="clean-up-resources"></a>Supprimer des ressources

Lorsque vous n’en avez plus besoin, supprimez le groupe de ressources, la passerelle Application Gateway et toutes les ressources associées. Pour ce faire, sélectionnez le groupe de ressources à partir du panneau de la passerelle Application Gateway, puis cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

Dans ce scénario, vous avez déployé une passerelle Application Gateway et ajouté un serveur au serveur principal. Les étapes suivantes consistent à configurer la passerelle Application Gateway en modifiant les paramètres et en ajustant les règles de la passerelle. Vous trouverez ces étapes dans les articles suivants :

Apprenez à créer des sondes d’intégrité personnalisées en vous rendant sur [Créer une sonde d’intégrité personnalisée](application-gateway-create-probe-portal.md)

Découvrez comment configurer le déchargement SSL et éviter à vos serveurs web le déchiffrement SSL coûteux en vous rendant sur [Configurer le déchargement SSL](application-gateway-ssl-portal.md)

Découvrez comment protéger vos applications grâce au [Pare-feu d’applications Web](application-gateway-webapplicationfirewall-overview.md), une fonctionnalité de passerelle Application Gateway.

<!--Image references-->
[1]: ./media/application-gateway-create-gateway-portal/figure1.png
[2]: ./media/application-gateway-create-gateway-portal/figure2.png
[3]: ./media/application-gateway-create-gateway-portal/figure3.png
[4]: ./media/application-gateway-create-gateway-portal/figure4.png
[5]: ./media/application-gateway-create-gateway-portal/figure5.png
[scenario]: ./media/application-gateway-create-gateway-portal/scenario.png
