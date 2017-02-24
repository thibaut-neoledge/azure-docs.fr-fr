---
title: "Lier un réseau virtuel à un circuit ExpressRoute à l’aide du portail Azure | Microsoft Docs"
description: "Ce document fournit une vue d’ensemble de la façon de lier des réseaux virtuels à des circuits ExpressRoute."
services: expressroute
documentationcenter: na
author: cherylmc
manager: timlt
editor: 
tags: azure-resource-manager
ms.assetid: f5cb5441-2fba-46d9-99a5-d1d586e7bda4
ms.service: expressroute
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/08/2017
ms.author: cherylmc
translationtype: Human Translation
ms.sourcegitcommit: b3c0cca9a6d5171b1248b0f463cbbb26641fc5f2
ms.openlocfilehash: a1a689dbfc35107b52f9b84f74ac8bfac0727a0e


---
# <a name="connect-a-virtual-network-to-an-expressroute-circuit"></a>Connecter un réseau virtuel à un circuit ExpressRoute
> [!div class="op_single_selector"]
> * [Resource Manager - Portail Azure](expressroute-howto-linkvnet-portal-resource-manager.md)
> * [Resource Manager - PowerShell](expressroute-howto-linkvnet-arm.md)
> * [Classic - PowerShell](expressroute-howto-linkvnet-classic.md)
> * [Vidéo - portail Azure](http://azure.microsoft.com/documentation/videos/azure-expressroute-how-to-create-a-connection-between-your-vpn-gateway-and-expressroute-circuit)
> 
>  

Cet article vous aide à lier des réseaux virtuels à des circuits Azure ExpressRoute en utilisant le modèle de déploiement Resource Manager et le portail Azure. Les réseaux virtuels peuvent appartenir au même abonnement ou faire partie d’un autre abonnement.

**À propos des modèles de déploiement Azure**

[!INCLUDE [vpn-gateway-clasic-rm](../../includes/vpn-gateway-classic-rm-include.md)]

## <a name="configuration-prerequisites"></a>Conditions préalables à la configuration
* Veillez à consulter les [conditions préalables](expressroute-prerequisites.md), la [configuration requise pour le routage](expressroute-routing.md) et les [flux de travail](expressroute-workflows.md) avant de commencer la configuration.
* Vous devez disposer d’un circuit ExpressRoute actif.
  
  * Suivez les instructions permettant de [créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et faites-le activer par votre fournisseur de service de connectivité.
  * Vérifiez que l’homologation privée Azure est configurée pour votre circuit. Pour obtenir des instructions sur le routage, consultez l’article [Configurer le routage](expressroute-howto-routing-portal-resource-manager.md) .
  * Vérifiez que l’homologation privée Azure est être configurée, et que l’homologation BGP entre votre réseau et Microsoft est être opérationnelle pour pouvoir activer la connectivité de bout en bout.
  * Vérifiez qu’un réseau virtuel et une passerelle de réseau virtuel ont été créés et entièrement approvisionnés. Suivez les instructions pour créer une [passerelle VPN](../vpn-gateway/vpn-gateway-howto-site-to-site-resource-manager-portal.md) (seulement les étapes 1 à 5).

* Vous pouvez lier jusqu’à 10 réseaux virtuels à un circuit ExpressRoute standard. Tous les réseaux virtuels doivent figurer dans la même région géopolitique lors de l’utilisation d’un circuit ExpressRoute standard. 

* Vous pouvez lier un réseau virtuel à l’extérieur de la zone géopolitique du circuit ExpressRoute ou lier un plus grand nombre de réseaux virtuels à votre circuit ExpressRoute si vous avez activé le module complémentaire Premium d’ExpressRoute. Pour plus d’informations sur le module complémentaire Premium, consultez le [FAQ](expressroute-faqs.md) .

## <a name="connect-a-virtual-network-in-the-same-subscription-to-a-circuit"></a>Connecter un réseau virtuel du même abonnement à un circuit

### <a name="to-create-a-connection"></a>Pour créer une connexion

> [!NOTE]
> Les informations de configuration BGP ne s’affichent pas si vos homologations ont été configurées par le fournisseur de la couche 3. Si votre circuit est dans l’état Approvisionné, vous pouvez créer des connexions.
>

1. Assurez-vous que votre circuit ExpressRoute et l'homologation privée Azure ont été correctement configurés. Suivez les instructions dans [Créer un circuit ExpressRoute](expressroute-howto-circuit-arm.md) et [Configurer le routage](expressroute-howto-routing-arm.md). Votre circuit ExpressRoute doit être similaire à l’image suivante :

    ![Capture d’écran Circuit ExpressRoute](./media/expressroute-howto-linkvnet-portal-resource-manager/routing1.png)
   
2. Vous pouvez maintenant commencer à approvisionner une connexion pour lier votre passerelle de réseau virtuel à votre circuit ExpressRoute. Cliquez sur **Connexion** > **Ajouter** pour ouvrir le panneau **Ajouter une connexion**, puis configurez les valeurs.

    ![Capture d’écran Ajouter une connexion](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub1.png)  

3. Une fois votre connexion correctement configurée, votre objet de connexion affiche les informations de la connexion.

     ![Capture d’écran Objet de connexion](./media/expressroute-howto-linkvnet-portal-resource-manager/samesub2.png)

### <a name="to-delete-a-connection"></a>Suppression d'une connexion
Vous pouvez supprimer une connexion en sélectionnant l’icône **Supprimer** dans le panneau de votre connexion.

## <a name="connect-a-virtual-network-in-a-different-subscription-to-a-circuit"></a>Connecter un réseau virtuel d’un autre abonnement à un circuit
Vous pouvez partager un circuit ExpressRoute entre plusieurs abonnements. La figure suivante montre un schéma simple sur le fonctionnement du partage de circuits ExpressRoute entre plusieurs abonnements.

![Connectivité entre abonnements](./media/expressroute-howto-linkvnet-portal-resource-manager/cross-subscription.png)

- Chacun des petits clouds dans le cloud principal est utilisé pour représenter les abonnements appartenant à différents services au sein d’une organisation.
- Chacun des services au sein de l’organisation peut utiliser son propre abonnement pour déployer ses services, mais ils peuvent partager un même circuit ExpressRoute pour se connecter à votre réseau local.
- Un seul service (dans cet exemple, le service informatique) peut posséder le circuit ExpressRoute. D’autres abonnements au sein de l’organisation peuvent utiliser le circuit ExpressRoute.

    > [!NOTE]
    > Les frais de connectivité et de bande passante pour le circuit dédié s’appliquent au propriétaire du circuit ExpressRoute. Tous les réseaux virtuels partagent la même bande passante.
    > 
    >

### <a name="administration"></a>Administration
Le « propriétaire du circuit » est l’utilisateur avec pouvoir autorisé de la ressource de circuit ExpressRoute. Le propriétaire du circuit peut créer des autorisations utilisables par les « utilisateurs du circuit ». Les utilisateurs du circuit sont les propriétaires des passerelles de réseau virtuel (qui ne figurent pas dans le même abonnement que le circuit ExpressRoute). Les utilisateurs du circuit peuvent échanger des autorisations (une seule autorisation par réseau virtuel).

Le propriétaire du circuit a le pouvoir de modifier et de révoquer les autorisations à tout moment. La révocation d’une autorisation entraîne la suppression de toutes les connexions de l’abonnement dont l’accès a été révoqué.

### <a name="circuit-owner-operations"></a>Opérations du propriétaire du circuit

#### <a name="creating-an-authorization"></a>Création d’une autorisation

Le propriétaire du circuit crée une autorisation. Cela entraîne la création d'une clé d'autorisation qui peut être utilisée par un utilisateur du circuit pour se connecter à ses passerelles de réseau virtuel vers un circuit ExpressRoute. Une autorisation n’est valide que pour une seule connexion.

1. Dans le panneau ExpressRoute, cliquez sur **Autorisations**. Tapez un **nom** pour l’autorisation, puis cliquez sur **Enregistrer**.

    ![Autorisations](./media/expressroute-howto-linkvnet-portal-resource-manager/authorization.png)

2. Une fois la configuration enregistrée, copiez **l’ID de ressource** et la **clé d’autorisation**.

    ![Clé d’autorisation](./media/expressroute-howto-linkvnet-portal-resource-manager/authkey.png)

#### <a name="deleting-authorizations"></a>Suppression des autorisations

Vous pouvez supprimer une connexion en sélectionnant l’icône **Supprimer** dans le panneau de votre connexion.

### <a name="circuit-user-operations"></a>Opérations de l’utilisateur du circuit

   > [!NOTE]
   > L’utilisateur du circuit a besoin de l’ID de ressource et d’une clé d’autorisation du propriétaire du circuit. 

#### <a name="redeeming-connection-authorizations"></a>Utilisation des autorisations de connexion


Renseignez les détails, puis cliquez sur **OK** sous l’onglet De base.

1.    Cliquez sur le bouton **+Nouveau**.

    ![Click New](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection1.png)

2.    Recherchez l’option **Connexion** dans la Place de marché. Sélectionnez-la, puis cliquez sur **Créer**.

    ![Rechercher une connexion](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection2.png)

3.    Vérifiez que l’option **Type de connexion** est définie sur « ExpressRoute ».


4.    Renseignez les détails, puis cliquez sur **OK** dans le panneau De base.

    ![Panneau Informations de base](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection3.png)

5.    Dans le panneau **Paramètres**, sélectionnez **Passerelle de réseau virtuel**, puis cochez la case **Redeem authorization** (Réclamer l’autorisation).

6.    Entrez la **clé d’autorisation** et **l’URI du circuit appairé**, puis donnez un nom à la connexion. Cliquez sur **OK**.

    ![Panneau Paramètres](./media/expressroute-howto-linkvnet-portal-resource-manager/Connection4.png)

7. Passez en revue les informations contenues dans le panneau **Résumé**, puis cliquez sur **OK**.

#### <a name="releasing-connection-authorizations"></a>Libération des autorisations de connexion

Vous pouvez libérer une autorisation en supprimant la connexion qui lie le circuit ExpressRoute et le réseau virtuel.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d'informations sur ExpressRoute, consultez le [FAQ sur ExpressRoute](expressroute-faqs.md).



<!--HONumber=Feb17_HO2-->


