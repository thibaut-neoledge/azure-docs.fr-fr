<properties 
    pageTitle="Comment migrer un réseau virtuel RemoteApp vers un réseau virtuel Azure"
    description="Découvrir comment migrer un réseau virtuel RemoteApp vers un réseau virtuel Azure" 
    services="remoteapp" 
	documentationCenter="" 
    authors="lizap" 
    manager="mbaldwin" />

<tags 
    ms.service="remoteapp" 
    ms.workload="compute" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="09/02/2015" 
    ms.author="elizapo" />



# Comment migrer une collection hybride d’un réseau virtuel RemoteApp à un réseau virtuel Azure

Bonne nouvelle ! Vous avez désormais la possibilité de déployer des collections RemoteApp hybrides directement sur vos réseaux virtuels (VNET) Azure existants au lieu de devoir créer des réseaux virtuels RemoteApp spécifiques. Cela vous permet de tirer parti des dernières fonctionnalités de réseau virtuel (comme ExpressRoute) et offre à vos collections hybrides un accès réseau direct à d’autres services Azure et ordinateurs virtuels déployés sur ce réseau virtuel. (Ceci améliore les performances et facilite l’installation par rapport aux configurations de réseau virtuel à réseau virtuel).


Supposons que vous avez déjà créé une collection RemoteApp hybride appelée *OriginalCollection* avec un réseau virtuel RemoteApp appelé *RemoteAppVNET*. Voici les étapes à suivre pour effectuer la migration vers un nouveau réseau virtuel Azure appelé *AzureVNET*.

1.	Dans l’onglet **Réseaux** du [Portail de gestion](http://manage.windowsazure.com/), créez un réseau virtuel appelé *AzureVNET* en utilisant un emplacement, une configuration DNS et un espace d’adressage identiques (pour au moins l’un des sous-réseaux *AzureVNET*) à ceux utilisés pour *RemoteAppVNET*.
2.	Configurez *AzureVNET* de manière à héberger le déploiement d’Active Directory avec lequel la collection *OriginalCollection* est jointe au domaine, ou à établir une connectivité réseau avec ce dernier.
3.	Dans l’onglet **RemoteApps**, créez une collection RemoteApp appelée *Nouvelle Collection*. (Utilisez l’option **Créer avec VNET** plutôt que l’option **Création rapide**.)
3.	Configurez *NewCollection* de manière à être déployée sur un sous-réseau dans *AzureVNET*.
4.	Configurez *NewCollection* de manière à utiliser la même image et les mêmes informations de jonction de domaine que celles utilisées pour *OriginalCollection*.
5.	Après quelques heures, *NewCollection* apparaît dans la liste de collections avec le statut Actif.

Si vous n’avez PAS besoin de migrer des informations utilisateur depuis la collection d’origine vers la nouvelle collection, suivez les étapes ci-dessous :

6.	Supprimez *OriginalCollection*.
7.	Supprimez *RemoteAppVNET*.

Vous avez terminé !

Si vous DEVEZ migrer des informations utilisateur depuis la collection d’origine vers la nouvelle collection, suivez les étapes ci-dessous :

6.	Envoyez un e-mail à [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20user%20information%20migration) avec votre ID d’abonnement Azure, le nom de votre collection d’origine et le nom de votre nouvelle collection, et demandez à migrer vos informations utilisateur.
7.	Dans les deux jours ouvrés, l’équipe RemoteApp déplace la liste d’accès utilisateur, ainsi que tous les documents et paramètres de l’utilisateur de la collection d’origine vers la nouvelle collection.
8.	Supprimez *OriginalCollection*.
9.	Supprimez *RemoteAppVNET*.

Vous avez terminé !

Si vous avez des questions ou que vous avez besoin d’aide, envoyez un e-mail [remoteappforum@microsoft.com](mailto:remoteappforum@microsoft.com?subject=Azure%20RemoteApp%20VNET%20migration%20help).
 

<!---HONumber=Oct15_HO3-->