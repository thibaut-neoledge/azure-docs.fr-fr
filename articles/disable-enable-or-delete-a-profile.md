<properties
   pageTitle="Désactiver, activer ou supprimer un profil Traffic Manager"
   description="Cet article vous aide à utiliser vos profils Traffic Manager."
   services="traffic-manager"
   documentationCenter="na"
   authors="cherylmc"
   manager="adinah"
   editor="tysonn" />
<tags 
   ms.service="traffic-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="02/23/2015"
   ms.author="cherylmc" />

# Désactiver, activer ou supprimer un profil

## Désactiver ou activer un profil

Vous pouvez désactiver un profil Traffic Manager existant pour qu'il ne renvoie pas les demandes utilisateur vers ses points de terminaison configurés. Quand vous désactivez un profil Traffic Manager, le profil lui-même et les informations qu'il contient restent intacts et peuvent être modifiés via l'interface de Traffic Manager. Pour réactiver le profil, vous pouvez le faire facilement dans le portail de gestion ; les références reprendront alors. Quand vous créez un profil Traffic Manager dans le portail de gestion, il est automatiquement activé.

### Pour désactiver un profil

1. Modifiez l'enregistrement de ressource DNS sur votre serveur DNS Internet pour utiliser le type d'enregistrement approprié et un pointeur vers un autre nom ou l'adresse IP d'un emplacement spécifique sur Internet. En d'autres termes, modifiez l'enregistrement de ressource DNS sur votre serveur DNS Internet pour qu'il n'utilise plus un enregistrement de ressource CNAME qui pointe vers le nom de domaine de votre profil Traffic Manager.
1. Le trafic ne sera plus acheminé vers les points de terminaison via les paramètres du profil Traffic Manager.
1. Sélectionnez le profil que vous souhaitez désactiver. Pour sélectionner le profil, dans la page Traffic Manager, mettez-le en surbrillance en cliquant sur la colonne en regard de son nom. Ne cliquez pas sur le nom du profil ou sur la flèche en regard du nom, car cela vous dirigerait vers la page des paramètres du profil.
1. Après avoir sélectionné le profil, cliquez sur Désactiver en bas de la page.

### Pour activer un profil

1. Sélectionnez le profil que vous souhaitez activer. Pour sélectionner le profil, dans la page Traffic Manager, mettez-le en surbrillance en cliquant sur la colonne en regard de son nom. Ne cliquez pas sur le nom du profil ou sur la flèche en regard du nom, car cela vous dirigerait vers la page des paramètres du profil.
1. Après avoir sélectionné le profil, cliquez sur Activer en bas de la page.
1. Modifiez l'enregistrement de ressource DNS sur votre serveur DNS Internet pour utiliser le type d'enregistrement CNAME, qui mappe le nom de domaine de votre société au nom de domaine de votre profil Traffic Manager. Pour plus d'informations, consultez [Redirection d'un domaine Internet d'entreprise vers un domaine Traffic Manager](point-a-company-internet-domain-to-a-traffic-manager-domain.md).
1. Le trafic sera redirigé vers les points de terminaison.

## Supprimer un profil


### Pour supprimer un profil

1. Assurez-vous que l'enregistrement de ressource DNS sur votre serveur DNS Internet n'utilise plus un enregistrement de ressource CNAME qui pointe vers le nom de domaine de votre profil Traffic Manager.
1. Sélectionnez le profil que vous souhaitez supprimer. Pour sélectionner le profil, dans la page Traffic Manager, mettez-le en surbrillance en 
1. cliquant sur la colonne en regard de celui-ci. Ne cliquez pas sur le nom du profil ou sur la flèche en regard du nom, car cela vous dirigerait vers la page des paramètres du profil.
1. Après avoir sélectionné le profil, cliquez sur Supprimer en bas de la page.

## Voir aussi

[Traffic Manager](traffic-manager.md)

[Tâches de Configuration de Traffic Manager](https://msdn.microsoft.com/library/azure/hh744830.aspx)



<!--HONumber=49-->