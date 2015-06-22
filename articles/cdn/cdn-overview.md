<properties 
	pageTitle="Vue d'ensemble d'Azure CDN" 
	description="Découvrez le réseau de distribution de contenu (CDN) Azure et comment l'utiliser pour diffuser du contenu haut débit en mettant en cache les objets blob et le contenu statique." 
	services="cdn" 
	documentationCenter=".NET" 
	authors="akucer" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="01/06/2015" 
	ms.author="akucer"/>

#Vue d'ensemble du réseau de distribution de contenu (CDN) Azure

Le réseau de distribution de contenu (CDN) Azure met en cache les objets blob Azure et le contenu statique utilisés par les services cloud à des emplacements stratégiques afin de fournir une bande passante maximale pour la distribution de contenu aux utilisateurs. 

Si vous êtes un client CDN existant, vous pouvez désormais gérer vos points de terminaison CDN via le [portail de gestion Microsoft Azure](https://manage.windowsazure.com). 


>[AZURE.NOTE] Azure CDN utilise un [mode de facturation](http://www.microsoft.com/windowsazure/pricing/) différent d'Azure Storage ou d'Azure Cloud Services.
 

Le CDN offre aux développeurs une solution globale pour la distribution de contenu haut débit en mettant en cache le contenu sur des nœuds physiques dans le monde entier. Pour obtenir la liste actuelle des emplacements des nœuds CDN, consultez la page [Emplacements des points de présence du réseau de distribution de contenu (CDN) Azure](http://msdn.microsoft.com/library/azure/gg680302.aspx).

Les avantages de l'utilisation du CDN pour mettre en cache des données Azure sont notamment les suivants :

- De meilleures performances et une expérience utilisateur plus agréable pour les utilisateurs finaux qui se trouvent éloignés d'une source de contenu et qui utilisent des applications ayant recours à de nombreux " trajets Internet " pour charger le contenu
- Une diffusion à grande échelle pour améliorer la gestion instantanée des charges importantes, par exemple au début de l'événement du lancement d'un produit. 


>[AZURE.IMPORTANT] Quand vous créez ou activez un point de terminaison CDN, sa propagation dans le monde entier peut prendre jusqu'à 60 minutes.
 
Quand une demande d'objet est effectuée pour la première fois au CDN, l'objet est récupéré directement du service BLOB ou du service cloud. Quand une demande est effectuée à l'aide de la syntaxe CDN, elle est redirigée vers le point de terminaison CDN le plus proche de l'emplacement à partir duquel elle a été faite pour fournir l'accès à l'objet. Si l'objet est introuvable sur ce point de terminaison, il est récupéré du service et mis en cache au point de terminaison, où un paramètre de durée de vie est géré pour l'objet mis en cache.
 
##Mise en cache de contenu à partir d'un stockage Azure

Une fois que le CDN est activé sur un compte de stockage Azure, tous les objets blob se trouvant dans des conteneurs publics et qui sont disponibles pour l'accès anonyme sont mis en cache via le CDN. Seuls les objets blob disponibles publiquement peuvent être mis en cache avec le CDN Azure. Pour rendre un objet blob publiquement disponible pour l'accès anonyme, vous devez marquer son conteneur comme étant public. Ensuite, tous les objets blob dans ce conteneur seront accessibles en lecture anonymement. Vous avez également la possibilité de rendre publiques les données du conteneur ou de restreindre l'accès uniquement aux objets blob qu'il contient. Pour plus d'informations sur la gestion du contrôle d'accès aux conteneurs et aux objets, consultez [Limiter l'accès aux conteneurs et aux objets blob](http://msdn.microsoft.com/library/azure/dd179354.aspx).

Pour de meilleures performances, utilisez le cache de périmètre CDN pour distribuer des objets blob dont la taille est inférieure à 10 Go.

Quand vous activez l'accès CDN pour un compte de stockage, le portail de gestion vous fournit un nom de domaine CDN au format suivant :: http://<identifier>.vo.msecnd.net/. Ce nom de domaine peut être utilisé pour accéder aux objets blob d’un conteneur public. Par exemple, si un conteneur public appelé musique se trouve dans un compte de stockage appelé moncompte, les utilisateurs peuvent accéder aux objets blob de ce conteneur à l’aide de l’une des deux URL suivantes :

- **URL de service BLOB Azure** : `http://myAccount.blob.core.windows.net/music/` 
- **URL du CDN Azure** : `http://<identifier>.vo.msecnd.net/music/` 

##Mise en cache de contenu à partir de sites web Azure

Vous pouvez activer le CDN à partir de vos site web pour mettre en cache du contenu web, tels que des images, des scripts et des feuilles de style. Voir [Intégration d'un site web Azure au CDN Azure](../cdn-websites-with-cdn.md).

Quand vous activez l'accès CDN pour un site web, le portail de gestion vous fournit un nom de domaine CDN au format suivant : http://<identificateur>.vo.msecnd.net/. Ce nom de domaine peut être utilisé pour récupérer des objets d'un site web. Par exemple, dans le cas d'un conteneur public nommé cdn et d'un fichier image appelé music.png, les utilisateurs peuvent accéder à l'objet en utilisant une des deux URL suivantes :

- **URL de site web Azure** : `http://mySiteName.azurewebsites.net/cdn/music.png` 
- **URL du CDN Azure** : `http://<identifier>.vo.msecnd.net/cdn/music.png` 
##Mise en cache de contenu à partir de services cloud Azure

Vous pouvez mettre en cache sur le CDN des objets fournis par un service cloud Azure. 

Du point de vue des services cloud, la mise en cache présente les contraintes suivantes : 


- Le CDN doit uniquement servir à mettre en cache du contenu statique.

	>[AZURE.WARNING] La mise en cache de contenu extrêmement volatil ou particulièrement dynamique peut affecter vos performances ou provoquer des problèmes de contenu, avec à la clé un surcoût.
- Votre service cloud doit être déployé dans un environnement de production.
- Votre service cloud doit fournir l'objet sur le port 80 à l'aide du protocole HTTP.
- Le service cloud doit placer le contenu à mettre en cache ou à distribuer dans son dossier /cdn.

Quand vous activez l'accès CDN sur un service cloud, le portail de gestion vous fournit un nom de domaine CDN au format suivant : http://<identificateur>.vo.msecnd.net/. Ce nom de domaine peut être utilisé pour récupérer des objets d'un service cloud. Par exemple, dans le cas d'un service cloud nommé myHostedService et d'une page web ASP.NET appelée music.aspx qui distribue du contenu, les utilisateurs peuvent accéder à l'objet en utilisant une des deux URL suivantes :


- **URL de service cloud Azure** : `http://myHostedService.cloudapp.net/cdn/music.aspx` 
- **URL du CDN Azure** : `http://<identifier>.vo.msecnd.net/music.aspx` 


###Mise en cache de contenu spécifique avec des chaînes de requête

Vous pouvez utiliser des chaînes de requête pour différencier les objets récupérés d'un service cloud. Par exemple, si le service cloud affiche un graphique qui peut varier, vous pouvez passer une chaîne de requête pour récupérer le graphique spécifique requis. Par exemple : 

`http://<identifier>.vo.msecnd.net/chart.aspx?item=1`

Les chaînes de requête sont passées en tant que littéraux de chaîne. Si vous avez un service qui prend deux paramètres, tels que `?area=2&item=1` et que vous l'appelez en utilisant `?item=1&area=2`, vous mettez en cache deux copies du même objet.
 

##Accès à du contenu mis en cache via le protocole HTTPS


Azure vous permet de récupérer du contenu du CDN via des appels HTTPS. Vous pouvez ainsi incorporer le contenu mis en cache dans le CDN dans des pages web sécurisées sans recevoir d'avertissement sur des types de contenu de sécurité mixte.

L'accès à du contenu CDN à l'aide du protocole HTTPS présente les contraintes suivantes :


- Vous devez utiliser le certificat fourni par le CDN. Les certificats tiers ne sont pas pris en charge.
- Vous devez utiliser le domaine CDN pour accéder au contenu. La prise en charge du protocole HTTPS n'est pas disponible pour les noms de domaines personnalisés (enregistrements CNAME), car le CDN ne prend pas en charge les certificats personnalisés pour l'instant.



Même si le protocole HTTPS est activé, le contenu du CDN peut être récupéré à l'aide des protocoles HTTP et HTTPS.

Pour plus d'informations sur l'activation du protocole HTTPS pour le contenu CDN, consultez [Comment activer le réseau de distribution de contenu (CDN) pour Azure](http://msdn.microsoft.com/library/azure/gg680301.aspx).


##Accès à du contenu mis en cache avec des domaines personnalisés

Vous pouvez mapper le point de terminaison HTTP CDN à un nom de domaine personnalisé et utiliser ce nom pour demander des objets depuis le CDN.

Pour plus d'informations sur le mappage d'un domaine personnalisé, consultez [Comment mapper le contenu du réseau de distribution de contenu Azure (CDN) à un domaine personnalisé](http://msdn.microsoft.com/library/azure/gg680307.aspx).


<!--HONumber=49--> 