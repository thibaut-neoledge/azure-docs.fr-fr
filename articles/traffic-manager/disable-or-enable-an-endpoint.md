<properties
   pageTitle="Désactiver ou activer un point de terminaison Traffic Manager | Microsoft Azure"
   description="Cet article vous aide à désactiver ou activer vos points de terminaison du profil Traffic Manager."
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="carmonm"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="12/01/2015"
   ms.author="joaoma" />

# Désactiver ou activer un point de terminaison Traffic Manager

Vous pouvez également désactiver des points de terminaison individuels qui font partie d'un profil Traffic Manager. Les points de terminaison incluent des services cloud et des sites web. Quand un point de terminaison est désactivé, il fait toujours partie du profil, mais celui-ci se comporte comme si ce n'était pas le cas. Cela s'avère particulièrement utile pour supprimer temporairement un point de terminaison en mode de maintenance ou redéployé. Une fois le point de terminaison à nouveau en fonction, il peut être activé.

>[AZURE.NOTE]**La désactivation d'un point de terminaison n'a aucun lien avec son état de déploiement dans Azure. Un point de terminaison sain reste en fonction et peut recevoir du trafic même quand il est désactivé dans Traffic Manager. En outre, le fait de désactiver un point de terminaison dans un profil n’affecte pas son statut dans un autre profil.**

## Pour désactiver un point de terminaison

1. Dans le volet Traffic Manager du portail Azure, recherchez le profil Traffic Manager qui contient les paramètres de point de terminaison que vous souhaitez modifier, puis cliquez sur la flèche à droite du nom de profil. La page Paramètres du profil s'ouvre.
1. En haut de la page, cliquez sur **Points de terminaison** pour afficher les points de terminaison qui sont inclus dans votre configuration. 
1. Cliquez sur le point de terminaison que vous souhaitez désactiver, puis sur **Désactiver** au bas de la page.
1. Le trafic cesse de circuler vers le point de terminaison en fonction de la durée de vie DNS (TTL) configurée pour le nom de domaine Traffic Manager. Vous pouvez modifier la durée de vie dans la page Configuration du profil Traffic Manager.

## Pour activer un point de terminaison


1. Dans le volet Traffic Manager du portail Azure, recherchez le profil Traffic Manager qui contient les paramètres de point de terminaison que vous souhaitez modifier, puis cliquez sur la flèche à droite du nom de profil. La page Paramètres du profil s'ouvre.
1. En haut de la page, cliquez sur **Points de terminaison** pour afficher les points de terminaison qui sont inclus dans votre configuration.
1. Cliquez sur le point de terminaison que vous souhaitez activer, puis sur **Activer** au bas de la page.
1. Le trafic vers le service reprend alors, comme défini par le profil.

## Étapes suivantes

[Traffic Manager - Désactiver, activer ou supprimer un profil](disable-enable-or-delete-a-profile.md)

[Résolution des problèmes liés à l’état Détérioré de Traffic Manager](traffic-manager-troubleshooting-degraded.md)

[Considérations sur les performances de Traffic Manager](traffic-manager-performance-considerations.md)

<!---HONumber=AcomDC_1203_2015-->