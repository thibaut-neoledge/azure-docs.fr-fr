<properties
   pageTitle="Gérer des points de terminaison dans Azure Traffic Manager | Microsoft Azure"
   description="Cet article vous aide à ajouter, supprimer, activer et désactiver des points de terminaison d’Azure Traffic Manager."
   services="traffic-manager"
   documentationCenter=""
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="03/17/2016"
   ms.author="joaoma" />

# Ajouter, désactiver, activer ou supprimer des points de terminaison

La fonction Web Apps d’Azure App Service fournit déjà des fonctionnalités de routage du trafic par basculement et tourniquet pour les sites web d’un centre de données, indépendamment du mode de site Web. Azure Traffic Manager vous permet de spécifier le routage du trafic par basculement et tourniquet pour des sites web et des services cloud de différents centres de données. La première étape nécessaire pour fournir cette fonctionnalité consiste à ajouter le point de terminaison de service cloud ou de site web à Traffic Manager.

>[AZURE.NOTE] Vous ne pouvez pas ajouter des emplacements externes ou des profils Traffic Manager en tant que points de terminaison à l’aide du portail Azure Classic. Vous devez utilisez l’API REST [Create Definition](http://go.microsoft.com/fwlink/p/?LinkId=400772) ou l’applet de commande Windows PowerShell [Add-AzureTrafficManagerEndpoint](http://go.microsoft.com/fwlink/p/?LinkId=400774).

Vous pouvez également désactiver des points de terminaison individuels qui font partie d'un profil Traffic Manager. Les points de terminaison incluent des services cloud et des sites web. Quand un point de terminaison est désactivé, il fait toujours partie du profil, mais celui-ci se comporte comme si ce n'était pas le cas. Cela s'avère particulièrement utile pour supprimer temporairement un point de terminaison en mode de maintenance ou redéployé. Une fois le point de terminaison à nouveau en fonction, il peut être activé.

>[AZURE.NOTE] La désactivation d'un point de terminaison n'a aucun lien avec son état de déploiement dans Azure. Un point de terminaison sain reste en fonction et peut recevoir du trafic même quand il est désactivé dans Traffic Manager. En outre, le fait de désactiver un point de terminaison dans un profil n'affecte pas son statut dans un autre profil.

## Pour ajouter un point de terminaison de service cloud ou de site web


1. Dans le volet Traffic Manager du portail Azure Classic, recherchez le profil Traffic Manager qui contient les paramètres de point de terminaison que vous souhaitez modifier, puis cliquez sur la flèche à droite du nom de profil. La page Paramètres du profil s'ouvre.
2. En haut de la page, cliquez sur **Points de terminaison** pour afficher les points de terminaison qui font déjà partie de votre configuration.
3. En bas de la page, cliquez sur **Ajouter** pour accéder à la page **Ajouter des points de terminaison de service**. Par défaut, la page répertorie les services cloud sous **Points de terminaison de service**.
4. For les services cloud, procédez comme suit : dans la liste, sélectionnez ceux qui vous intéressent pour les activer en tant que points de terminaison pour ce profil. L'effacement du nom de service cloud le supprime de la liste des points de terminaison.
5. Pour les sites web, cliquez sur la liste déroulante **Type de service**, puis sélectionnez **Application web**.
6. Dans la liste, sélectionnez les sites web pour les ajouter en tant que points de terminaison pour ce profil. L'effacement du nom de site web le supprime de la liste des points de terminaison. Notez que vous ne pouvez sélectionner qu'un seul site web par centre de données Azure (également appelé région). Dans un centre de données qui héberge plusieurs sites web, si vous sélectionnez un site web, les autres ne peuvent pas être sélectionnés. Notez également que seuls les sites web standard sont répertoriés.
7. Après avoir sélectionné les points de terminaison pour ce profil, cliquez sur la coche dans le coin inférieur droit pour enregistrer vos modifications.

>[AZURE.NOTE] Si vous utilisez la méthode de routage du trafic par *Basculement*, une fois que vous avez ajouté ou supprimé un point de terminaison, veillez à ajuster la liste prioritaire de basculement dans la page Configuration pour refléter l’ordre de basculement de votre configuration. Pour plus d’informations, consultez la rubrique [Configurer le routage du trafic par basculement](traffic-manager-configure-failover-routing-method.md).

## Pour désactiver un point de terminaison

1. Dans le volet Traffic Manager du portail Azure Classic, recherchez le profil Traffic Manager qui contient les paramètres de point de terminaison que vous souhaitez modifier, puis cliquez sur la flèche à droite du nom de profil. La page Paramètres du profil s'ouvre.
2. En haut de la page, cliquez sur **Points de terminaison** pour afficher les points de terminaison qui sont inclus dans votre configuration.
3. Cliquez sur le point de terminaison que vous souhaitez désactiver, puis sur **Désactiver** en bas de la page.
4. Le trafic cesse de circuler vers le point de terminaison en fonction de la durée de vie DNS (TTL) configurée pour le nom de domaine Traffic Manager. Vous pouvez modifier la durée de vie dans la page Configuration du profil Traffic Manager.

## Pour activer un point de terminaison

1. Dans le volet Traffic Manager du portail Azure Classic, recherchez le profil Traffic Manager qui contient les paramètres de point de terminaison que vous souhaitez modifier, puis cliquez sur la flèche à droite du nom de profil. La page Paramètres du profil s'ouvre.
2. En haut de la page, cliquez sur **Points de terminaison** pour afficher les points de terminaison qui sont inclus dans votre configuration.
3. Cliquez sur le point de terminaison que vous souhaitez activer, puis sur **Activer** au bas de la page.
4. Le trafic vers le service reprend alors, comme défini par le profil.

## Pour supprimer un point de terminaison de service cloud ou de site web


1. Dans le volet Traffic Manager du portail Azure Classic, recherchez le profil Traffic Manager qui contient les paramètres de point de terminaison que vous souhaitez modifier, puis cliquez sur la flèche à droite du nom de profil. La page Paramètres du profil s'ouvre.
2. En haut de la page, cliquez sur **Points de terminaison** pour afficher les points de terminaison qui font déjà partie de votre configuration.
3. Dans la page Points de terminaison, cliquez sur le nom du point de terminaison que vous souhaitez supprimer du profil.
4. En bas de la page, cliquez sur **Supprimer**.

>[AZURE.NOTE] Vous ne pouvez pas supprimer des emplacements externes ou des profils Traffic Manager en tant que points de terminaison à l’aide du portail Azure Classic. Vous devez utiliser Windows PowerShell. Pour plus d’informations, consultez [Remove-AzureTrafficManagerEndpoint](https://msdn.microsoft.com/library/dn690251.aspx).

## Étapes suivantes


[Configurer la méthode de routage par basculement](traffic-manager-configure-failover-routing-method.md)

[Configurer la méthode de routage du trafic en tourniquet (round robin)](traffic-manager-configure-round-robin-routing-method.md)

[Configurer la méthode de routage basé sur les performances](traffic-manager-configure-performance-routing-method.md)

[Résolution des problèmes liés à l’état Détérioré de Traffic Manager](traffic-manager-troubleshooting-degraded.md)

[Opérations sur Traffic Manager (Référence sur l’API REST)](http://go.microsoft.com/fwlink/p/?LinkID=313584)

<!---HONumber=AcomDC_0323_2016-->