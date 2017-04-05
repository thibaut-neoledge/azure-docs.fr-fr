---
title: "Configuration de l’acheminement (homologation) d’un circuit ExpressRoute - Resource Manager et Azure | Microsoft Docs"
description: "Cet article vous guide tout au long des étapes de création et d’approvisionnement de l’homologation privée, publique et Microsoft d’un circuit ExpressRoute. Cet article vous montre également comment vérifier l&quot;état, mettre à jour ou supprimer des homologations pour votre circuit."
documentationcenter: na
services: expressroute
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: 8c2a7ed2-ae5c-4e49-81f6-77cf9f2b2ac9
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/21/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: 0bec803e4b49f3ae53f2cc3be6b9cb2d256fe5ea
ms.openlocfilehash: 5e33ffc313e7edfcf7243f1113e07b215dc5b10b
ms.lasthandoff: 03/24/2017


---
# <a name="create-and-modify-peering-for-an-expressroute-circuit"></a>Créer et modifier l’homologation pour un circuit ExpressRoute
> [!div class="op_single_selector"]
> * [Gestionnaire des ressources - Portail Azure](expressroute-howto-routing-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-routing-arm.md)
> * [Classic - PowerShell](expressroute-howto-routing-classic.md)
> * [Vidéo - Homologation privée](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-private-peering-for-your-expressroute-circuit)
> * [Vidéo - Homologation publique](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-azure-public-peering-for-your-expressroute-circuit)
> * [Vidéo - Homologation Microsoft](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-set-up-microsoft-peering-for-your-expressroute-circuit)
> 
> 

Cet article vous guide tout au long des étapes de création et de gestion d'une configuration de routage d'un circuit ExpressRoute à l'aide du portail Azure et du modèle de déploiement Resource Manager.

**À propos des modèles de déploiement Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Conditions préalables à la configuration
* Veillez à consulter les pages relatives aux [conditions préalables](expressroute-prerequisites.md), à la [configuration requise pour le routage](expressroute-routing.md) et aux [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
* Vous devez disposer d’un circuit ExpressRoute actif. Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-portal-resource-manager.md) et faites-le activer par votre fournisseur de connectivité avant de poursuivre. Le circuit ExpressRoute doit être dans un état approvisionné et activé pour être en mesure d'exécuter les applets de commande décrites ci-dessous.
* Si vous prévoyez d’utiliser une clé partagée/le hachage MD5, veillez à l’utiliser aux deux extrémités du tunnel et à limiter le nombre de caractères à 25 maximum.

Ces instructions s'appliquent uniquement aux circuits créés avec des fournisseurs de services proposant des services de connectivité de couche 2. Si vous utilisez un fournisseur de services proposant des services gérés de couche 3 (généralement IPVPN, à l’image de MPLS), votre fournisseur de connectivité configure et gère le routage pour vous. 

> [!IMPORTANT]
> Nous n'annonçons pas d’homologations configurées par les fournisseurs de services via le portail de gestion des services. Nous travaillons sur la prochaine activation de cette fonctionnalité. Contactez votre fournisseur de services avant de configurer des homologations BGP.
> 
> 

Vous pouvez configurer une, deux ou les trois homologations (privée Azure, publique Azure et Microsoft) pour un circuit ExpressRoute. Vous pouvez configurer les homologations dans l’ordre de votre choix. Toutefois, vous devez veiller à finaliser une par une la configuration de chaque homologation. 

## <a name="azure-private-peering"></a>Homologation privée Azure
Cette section fournit des instructions sur la façon de créer, obtenir, mettre à jour et supprimer la configuration d'homologation privée Azure pour un circuit ExpressRoute. 

### <a name="to-create-azure-private-peering"></a>Pour créer une homologation privée Azure
1. Configurer le circuit ExpressRoute. Assurez-vous que le circuit est entièrement approvisionné par le fournisseur de connectivité avant de continuer.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Configurez l'homologation privée Azure pour le circuit. Assurez-vous de disposer des éléments suivants avant de procéder aux étapes suivantes :
   
   * Un sous-réseau /30 pour le lien principal. Ce sous-réseau ne doit faire partie d’aucun espace d'adressage réservé aux réseaux virtuels.
   * Un sous-réseau /30 pour le lien secondaire. Ce sous-réseau ne doit faire partie d’aucun espace d'adressage réservé aux réseaux virtuels.
   * Un ID VLAN valide pour établir cette homologation. Assurez-vous qu'aucune autre homologation sur le circuit n'utilise le même ID VLAN.
   * Un numéro AS pour l'homologation. Vous pouvez utiliser des numéros à 2 et 4 octets. Vous pouvez utiliser un numéro AS privé pour cette homologation. Veillez à ne pas utiliser le numéro 65515.
   * Un hachage MD5 si vous choisissez d’en utiliser un. **Cette étape est facultative**.
3. Sélectionnez la ligne d'homologation privée Azure, comme indiqué ci-dessous.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate1.png)
4. Configurer l’homologation privée Azure. L'image ci-dessous montre un exemple de configuration.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)
5. Enregistrez la configuration après avoir spécifié tous les paramètres. Une fois la configuration acceptée, vous verrez quelque chose de similaire à l'exemple ci-dessous.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="to-view-azure-private-peering-details"></a>Pour afficher les détails d’une homologation privée Azure
Vous pouvez afficher les propriétés d'homologation privée Azure en sélectionnant l'homologation.

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate3.png)

### <a name="to-update-azure-private-peering-configuration"></a>Pour mettre à jour la configuration d'homologation privée Azure
Vous pouvez sélectionner la ligne pour l'homologation et modifier les propriétés d'homologation. 

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate2.png)

### <a name="to-delete-azure-private-peering"></a>Pour supprimer une homologation privée Azure
Vous pouvez supprimer votre configuration d’homologation en sélectionnant l'icône Supprimer comme indiqué ci-dessous.

![](./media/expressroute-howto-routing-portal-resource-manager/rprivate4.png)

## <a name="azure-public-peering"></a>Homologation publique Azure
Cette section fournit des instructions sur la façon de créer, d’obtenir, de mettre à jour et de supprimer la configuration d'homologation publique Azure pour un circuit ExpressRoute. 

### <a name="to-create-azure-public-peering"></a>Pour créer une homologation publique Azure
1. Configurer le circuit ExpressRoute. Assurez-vous que le circuit est entièrement approvisionné par le fournisseur de connectivité avant de continuer.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Configurez l’homologation publique Azure pour le circuit. Assurez-vous de disposer des éléments suivants avant de procéder aux étapes suivantes :
   
   * Un sous-réseau /30 pour le lien principal. 
   * Un sous-réseau /30 pour le lien secondaire. 
   * Toutes les adresses IP utilisées pour configurer cette homologation doivent être des adresses IPv4 publiques valides.
   * Un ID VLAN valide pour établir cette homologation. Assurez-vous qu'aucune autre homologation sur le circuit n'utilise le même ID VLAN.
   * Un numéro AS pour l'homologation. Vous pouvez utiliser des numéros à 2 et 4 octets.
   * Un hachage MD5 si vous choisissez d’en utiliser un. **Cette étape est facultative**.
3. Sélectionnez la ligne d'homologation publique Azure, comme indiqué ci-dessous.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic1.png)
4. Configurez l’homologation publique. L'image ci-dessous montre un exemple de configuration.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)
5. Enregistrez la configuration après avoir spécifié tous les paramètres. Une fois la configuration acceptée, vous verrez quelque chose de similaire à l'exemple ci-dessous.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="to-view-azure-public-peering-details"></a>Pour afficher les détails d’une homologation publique Azure
Vous pouvez afficher les propriétés d'homologation publique Azure en sélectionnant l'homologation.

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic3.png)

### <a name="to-update-azure-public-peering-configuration"></a>Pour mettre à jour la configuration d'homologation publique Azure
Vous pouvez sélectionner la ligne pour l'homologation et modifier les propriétés d'homologation. 

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic2.png)

### <a name="to-delete-azure-public-peering"></a>Pour supprimer une homologation publique Azure
Vous pouvez supprimer votre configuration d’homologation en sélectionnant l'icône Supprimer comme indiqué ci-dessous.

![](./media/expressroute-howto-routing-portal-resource-manager/rpublic4.png)

## <a name="microsoft-peering"></a>Homologation Microsoft
Cette section fournit des instructions sur la façon de créer, d’obtenir, de mettre à jour et de supprimer la configuration d'homologation Microsoft pour un circuit ExpressRoute. 

### <a name="to-create-microsoft-peering"></a>Pour créer une homologation Microsoft
1. Configurer le circuit ExpressRoute. Assurez-vous que le circuit est entièrement approvisionné par le fournisseur de connectivité avant de continuer.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/listprovisioned.png)
2. Configurez l’homologation Microsoft pour le circuit. Assurez-vous de disposer des informations suivantes avant de poursuivre.
   
   * Un sous-réseau /30 pour le lien principal. Il doit s’agir d’un préfixe IPv4 public valide vous appartenant et enregistré dans un registre RIR / IRR.
   * Un sous-réseau /30 pour le lien secondaire. Il doit s’agir d’un préfixe IPv4 public valide vous appartenant et enregistré dans un registre RIR / IRR.
   * Un ID VLAN valide pour établir cette homologation. Assurez-vous qu'aucune autre homologation sur le circuit n'utilise le même ID VLAN.
   * Un numéro AS pour l'homologation. Vous pouvez utiliser des numéros à 2 et 4 octets.
   * **Préfixes publiés :** vous devez fournir une liste de tous les préfixes que vous prévoyez de publier sur la session BGP. Seuls les préfixes d'adresses IP publiques sont acceptés. Vous pouvez envoyer une liste séparée par des virgules si vous prévoyez d'envoyer un jeu de préfixes. Ces préfixes doivent être enregistrés en votre nom dans un registre RIR / IRR.
   * **ASN client :** si vous publiez des préfixes non enregistrés dans le numéro AS d’homologation, vous pouvez spécifier le numéro AS avec lequel ils sont enregistrés. **Cette étape est facultative**.
   * **Nom du registre de routage :** vous pouvez spécifier les registres RIR/IRR par rapport auquel le numéro AS et les préfixes sont enregistrés. **Cette étape est facultative.**
   * Un hachage MD5 si vous choisissez d’en utiliser un. **Cette étape est facultative.**
3. Vous pouvez sélectionner l'homologation que vous souhaitez configurer comme indiqué ci-dessous. Sélectionnez la ligne d'homologation Microsoft.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft1.png)
4. Configurez l’homologation Microsoft. L'image ci-dessous montre un exemple de configuration.
   
   ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft2.png)
5. Enregistrez la configuration après avoir spécifié tous les paramètres. 
   
    Si votre circuit obtient un état nécessaire à la validation (comme indiqué ci-dessous), vous devez ouvrir un ticket de support pour apporter la preuve de possession des préfixes à notre équipe de support.    
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft5.png)

    Vous pouvez ouvrir un ticket de support directement depuis le portail, comme indiqué ci-dessous     

    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft6.png)


1. Une fois la configuration acceptée, vous verrez quelque chose de similaire à l'exemple ci-dessous.
   
    ![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="to-view-microsoft-peering-details"></a>Pour afficher les détails de l’homologation Microsoft
Vous pouvez afficher les propriétés d'homologation publique Azure en sélectionnant l'homologation.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft3.png)

### <a name="to-update-microsoft-peering-configuration"></a>Pour mettre à jour la configuration d’homologation Microsoft
Vous pouvez sélectionner la ligne pour l'homologation et modifier les propriétés d'homologation. 

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft7.png)

### <a name="to-delete-microsoft-peering"></a>Pour supprimer une homologation Microsoft
Vous pouvez supprimer votre configuration d’homologation en sélectionnant l'icône Supprimer comme indiqué ci-dessous.

![](./media/expressroute-howto-routing-portal-resource-manager/rmicrosoft4.png)

## <a name="next-steps"></a>Étapes suivantes
Ensuite, [liez un réseau virtuel à un circuit ExpressRoute](expressroute-howto-linkvnet-portal-resource-manager.md).
* Pour plus d'informations sur les workflows ExpressRoute, consultez [Workflows ExpressRoute](expressroute-workflows.md).
* Pour plus d’informations sur l’homologation du circuit, consultez [Circuits ExpressRoute et domaines de routage](expressroute-circuit-peerings.md).
* Pour plus d’informations sur l’utilisation des réseaux virtuels, consultez la page [Présentation du réseau virtuel](../virtual-network/virtual-networks-overview.md).


