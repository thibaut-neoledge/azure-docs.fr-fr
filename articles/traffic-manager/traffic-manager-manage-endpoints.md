---
title: "Gérer des points de terminaison dans Azure Traffic Manager | Microsoft Docs"
description: "Cet article vous aide à ajouter, supprimer, activer et désactiver des points de terminaison d’Azure Traffic Manager."
services: traffic-manager
documentationcenter: 
author: sdwheeler
manager: carmonm
editor: 
ms.assetid: ade2bbc2-35a7-43c5-8001-4698f7254526
ms.service: traffic-manager
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 10/11/2016
ms.author: sewhee
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: e6af6652d39fad5812c15e19fa29c757595a78b6

---

# <a name="add-disable-enable-or-delete-endpoints"></a>Ajouter, désactiver, activer ou supprimer des points de terminaison

La fonction Web Apps d’Azure App Service fournit déjà des fonctionnalités de routage du trafic par basculement et tourniquet pour les sites web d’un centre de données, indépendamment du mode de site Web. Azure Traffic Manager vous permet de spécifier le routage du trafic par basculement et tourniquet pour des sites web et des services cloud de différents centres de données. La première étape nécessaire pour fournir cette fonctionnalité consiste à ajouter le point de terminaison de service cloud ou de site web à Traffic Manager.

> [!NOTE]
> Cet article explique comment utiliser le Portail Classic. Le Portail Azure Classic prend uniquement en charge la création et l’affectation de services cloud et d’applications web en tant que points de terminaison. Le nouveau [Portail Azure](https://portal.azure.com) est l’interface privilégiée.

Vous pouvez également désactiver des points de terminaison individuels qui font partie d'un profil Traffic Manager. Quand un point de terminaison est désactivé, il fait toujours partie du profil, mais celui-ci se comporte comme si ce n'était pas le cas. Cette action s’avère utile pour supprimer temporairement un point de terminaison en mode de maintenance ou redéployé. Une fois le point de terminaison à nouveau en fonction, il peut être activé.

> [!NOTE]
> La désactivation d'un point de terminaison n'a aucun lien avec son état de déploiement dans Azure. Un point de terminaison sain reste en fonction et peut recevoir du trafic même quand il est désactivé dans Traffic Manager. En outre, le fait de désactiver un point de terminaison dans un profil n'affecte pas son statut dans un autre profil.

## <a name="to-add-a-cloud-service-or-website-endpoint"></a>Pour ajouter un point de terminaison de service cloud ou de site web

1. Dans le volet Traffic Manager du Portail Azure Classic, recherchez le profil Traffic Manager qui contient les paramètres de point de terminaison que vous souhaitez modifier. Pour ouvrir la page Paramètres, cliquez sur la flèche à droite du nom de profil.
2. En haut de la page, cliquez sur **Points de terminaison** pour afficher les points de terminaison qui font déjà partie de votre configuration.
3. En bas de la page, cliquez sur **Ajouter** pour accéder à la page **Ajouter des points de terminaison de service**. Par défaut, la page répertorie les services cloud sous **Points de terminaison de service**.
4. Pour les services cloud, procédez comme suit : dans la liste, sélectionnez ceux qui vous intéressent pour les ajouter en tant que points de terminaison de ce profil. L'effacement du nom de service cloud le supprime de la liste des points de terminaison.
5. Pour les sites web, cliquez sur la liste déroulante **Type de service**, puis sélectionnez **Application web**.
6. Dans la liste, sélectionnez les sites web pour les ajouter en tant que points de terminaison pour ce profil. L'effacement du nom de site web le supprime de la liste des points de terminaison. Vous ne pouvez sélectionner qu’un seul site web par centre de données Azure (également appelé région). Une fois que vous avez sélectionné le premier site Web, vous ne pouvez plus sélectionner les autres sites Web du même centre de données. Notez également que seuls les sites web standard sont répertoriés.
7. Après avoir sélectionné les points de terminaison pour ce profil, cliquez sur la coche dans le coin inférieur droit pour enregistrer vos modifications.

> [!NOTE]
> Après ajout ou suppression d’un point de terminaison dans un profil selon la méthode de routage du trafic par *Basculement*, la liste de priorité des basculements peut ne pas être ordonnée comme vous le souhaitez. Vous pouvez ajuster l’ordre de la liste de priorité des basculements sur la page Configuration. Pour plus d’informations, consultez la rubrique [Configurer le routage du trafic par basculement](traffic-manager-configure-failover-routing-method.md).

## <a name="to-disable-an-endpoint"></a>Pour désactiver un point de terminaison

1. Dans le volet Traffic Manager du Portail Azure Classic, recherchez le profil Traffic Manager qui contient les paramètres de point de terminaison que vous souhaitez modifier. Pour ouvrir la page Paramètres, cliquez sur la flèche à droite du nom de profil.
2. En haut de la page, cliquez sur **Points de terminaison** pour afficher les points de terminaison qui sont inclus dans votre configuration.
3. Cliquez sur le point de terminaison que vous souhaitez désactiver, puis sur **Désactiver** en bas de la page.
4. Les clients continuent à envoyer le trafic vers le point de terminaison pendant la durée de vie (TTL). Vous pouvez modifier la durée de vie sur la page Configuration du profil Traffic Manager.

## <a name="to-enable-an-endpoint"></a>Pour activer un point de terminaison

1. Dans le volet Traffic Manager du Portail Azure Classic, recherchez le profil Traffic Manager qui contient les paramètres de point de terminaison que vous souhaitez modifier. Pour ouvrir la page Paramètres, cliquez sur la flèche à droite du nom de profil.
2. En haut de la page, cliquez sur **Points de terminaison** pour afficher les points de terminaison qui sont inclus dans votre configuration.
3. Cliquez sur le point de terminaison que vous souhaitez activer, puis sur **Activer** au bas de la page.
4. Les clients sont dirigés vers le point de terminaison activé, comme l’impose le profil.

## <a name="to-delete-a-cloud-service-or-website-endpoint"></a>Pour supprimer un point de terminaison de service cloud ou de site web

1. Dans le volet Traffic Manager du Portail Azure Classic, recherchez le profil Traffic Manager qui contient les paramètres de point de terminaison que vous souhaitez modifier. Pour ouvrir la page Paramètres, cliquez sur la flèche à droite du nom de profil.
2. En haut de la page, cliquez sur **Points de terminaison** pour afficher les points de terminaison qui font déjà partie de votre configuration.
3. Dans la page Points de terminaison, cliquez sur le nom du point de terminaison que vous souhaitez supprimer du profil.
4. En bas de la page, cliquez sur **Supprimer**.

## <a name="next-steps"></a>Étapes suivantes

* [Gérer des profils Traffic Manager](traffic-manager-manage-profiles.md)
* [Configurer des méthodes de routage](traffic-manager-configure-routing-method.md)
* [Résolution des problèmes liés à l’état Détérioré de Traffic Manager](traffic-manager-troubleshooting-degraded.md)
* [Considérations sur les performances de Traffic Manager](traffic-manager-performance-considerations.md)
* [Opérations sur Traffic Manager (Référence sur l’API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)




<!--HONumber=Nov16_HO2-->


