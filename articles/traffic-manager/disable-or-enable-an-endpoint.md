<properties
   pageTitle="Désactiver ou activer un point de terminaison Traffic Manager"
   description="Cet article vous aide à désactiver ou activer vos points de terminaison du profil Traffic Manager."
   services="traffic-manager"
   documentationCenter="na"
   authors="joaoma"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="07/10/2015"
   ms.author="joaoma" />

# Désactiver ou activer un point de terminaison Traffic Manager

Vous pouvez également désactiver des points de terminaison individuels qui font partie d'un profil Traffic Manager. Les points de terminaison incluent des services cloud et des sites web. Quand un point de terminaison est désactivé, il fait toujours partie du profil, mais celui-ci se comporte comme si ce n'était pas le cas. Cela s'avère particulièrement utile pour supprimer temporairement un point de terminaison en mode de maintenance ou redéployé. Une fois le point de terminaison à nouveau en fonction, il peut être activé.

>[AZURE.NOTE]**La désactivation d'un point de terminaison n'a aucun lien avec son état de déploiement dans Azure. Un point de terminaison sain reste en fonction et peut recevoir du trafic même quand il est désactivé dans Traffic Manager. En outre, le fait de désactiver un point de terminaison dans un profil n’affecte pas son statut dans un autre profil.**

## Pour désactiver un point de terminaison

1. Dans le volet Traffic Manager du portail de gestion, recherchez le profil Traffic Manager qui contient les paramètres de point de terminaison que vous souhaitez modifier, puis cliquez sur la flèche à droite du nom de profil. La page Paramètres du profil s'ouvre.
1. En haut de la page, cliquez sur **Points de terminaison** pour afficher les points de terminaison qui sont inclus dans votre configuration. 
1. Cliquez sur le point de terminaison que vous souhaitez désactiver, puis sur **Désactiver** au bas de la page.
1. Le trafic cesse de circuler vers le point de terminaison en fonction de la durée de vie DNS (TTL) configurée pour le nom de domaine Traffic Manager. Vous pouvez modifier la durée de vie dans la page Configuration du profil Traffic Manager.

## Pour activer un point de terminaison


1. Dans le volet Traffic Manager du portail de gestion, recherchez le profil Traffic Manager qui contient les paramètres de point de terminaison que vous souhaitez modifier, puis cliquez sur la flèche à droite du nom de profil. La page Paramètres du profil s'ouvre.
1. En haut de la page, cliquez sur **Points de terminaison** pour afficher les points de terminaison qui sont inclus dans votre configuration.
1. Cliquez sur le point de terminaison que vous souhaitez activer, puis sur **Activer** au bas de la page.
1. Le trafic vers le service reprend alors, comme défini par le profil.

## Étapes suivantes

[Tâches de configuration de Traffic Manager](https://msdn.microsoft.com/library/azure/hh744830.aspx)

[Vue d’ensemble de Traffic Manager](../traffic-manager.md)

[Services Cloud](http://go.microsoft.com/fwlink/?LinkId=314074)

[Sites Web](http://go.microsoft.com/fwlink/p/?LinkId=393327)


[Opérations sur Traffic Manager (Référence sur l’API REST)](http://go.microsoft.com/fwlink/?LinkId=313584)
 

<!---HONumber=July15_HO4-->