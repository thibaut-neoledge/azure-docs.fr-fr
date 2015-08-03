<properties
   pageTitle="Désactiver, activer ou supprimer un profil Traffic Manager"
   description="Cet article vous aide à utiliser vos profils Traffic Manager."
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
   ms.date="07/09/2015"
   ms.author="joaoma" />

# Désactiver, activer ou supprimer un profil


Vous pouvez désactiver un profil Traffic Manager existant afin qu’il ne renvoie pas les demandes utilisateur vers ses points de terminaison configurés. Lorsque vous désactivez un profil Traffic Manager, le profil et les informations qu’il contient demeurent intacts et peuvent être modifiés via l’interface de Traffic Manager. Vous pouvez réactiver le profil facilement via le Portail de gestion. Dans ce cas, les références seront rétablies. Lorsque vous créez un profil Traffic Manager dans le Portail de gestion, il est automatiquement activé.

## Désactivation d’un profil

1. Modifiez l’enregistrement de ressource DNS sur votre serveur DNS Internet afin d’utiliser le type d’enregistrement approprié et un pointeur adéquat vers un autre nom ou vers l’adresse IP d’un emplacement spécifique sur Internet. En d’autres termes, modifiez l’enregistrement de ressource DNS sur votre serveur DNS Internet afin qu’il n’utilise plus un enregistrement de ressource CNAME pointant vers le nom de domaine de votre profil Traffic Manager.
1. Le trafic ne sera plus dirigé vers les points de terminaison via les paramètres du profil Traffic Manager.
1. Sélectionnez le profil que vous souhaitez désactiver. Sélectionnez le profil dans la page Traffic Manager en cliquant sur la colonne en regard du nom de profil. Ne cliquez pas sur le nom du profil ou sur la flèche en regard du nom. Sinon, vous serez dirigé vers la page de paramètres du profil.
1. Après avoir sélectionné le profil, cliquez sur Désactiver en bas de la page.

## Activation d’un profil

1. Sélectionnez le profil que vous souhaitez activer. Sélectionnez le profil dans la page Traffic Manager en cliquant sur la colonne en regard du nom de profil. Ne cliquez pas sur le nom du profil ou sur la flèche en regard du nom. Sinon, vous serez dirigé vers la page de paramètres du profil.
1. Après avoir sélectionné le profil, cliquez sur Activer en bas de la page.
1. Modifiez l’enregistrement de ressource DNS sur votre serveur DNS Internet afin d’utiliser le type d’enregistrement CNAME, qui mappe votre nom de domaine d’entreprise au nom de domaine de votre profil Traffic Manager. Pour plus d’informations, consultez la page [Redirection d’un domaine Internet d’entreprise vers un domaine Traffic Manager](../point-a-company-internet-domain-to-a-traffic-manager-domain.md).
1. Le trafic est de nouveau dirigé vers les points de terminaison.

## Supprimer un profil


1. Assurez-vous que l’enregistrement de ressource DNS sur votre serveur DNS Internet n’utilise plus un enregistrement de ressource CNAME pointant vers le nom de domaine de votre profil Traffic Manager.
1. Sélectionnez le profil que vous souhaitez supprimer. Pour sélectionner le profil, dans la page Traffic Manager, mettez-le en surbrillance en 
1. cliquant sur la colonne en regard de celui-ci. Ne cliquez pas sur le nom du profil ou sur la flèche en regard du nom. Sinon, vous serez dirigé vers la page de paramètres du profil.
1. Après avoir sélectionné le profil, cliquez sur Supprimer en bas de la page.

## Étapes suivantes

[Traffic Manager](../traffic-manager.md)

[Tâches de configuration de Traffic Manager](https://msdn.microsoft.com/library/azure/hh744830.aspx)


 

<!---HONumber=July15_HO4-->