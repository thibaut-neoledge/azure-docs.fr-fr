<properties 
	pageTitle="Utilisation de CDN | Microsoft Azure" 
	description="Découvrez comment utiliser le réseau de distribution de contenu (CDN) Azure pour diffuser du contenu haut débit en mettant en cache les objets blob et le contenu statique." 
	services="cdn" 
	documentationCenter=".net" 
	authors="camsoper" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="01/20/2016" 
	ms.author="casoper"/>


# Utilisation du réseau de distribution de contenu (CDN) Azure

Le réseau de distribution de contenu (CDN) Azure est le bloc de construction de base pour mettre à l’échelle toute application HTTP dans Azure. Il offre aux clients Azure une solution globale qui met en cache le contenu et le place à proximité des utilisateurs. Par conséquent, au lieu de contacter systématiquement le point d’origine, les demandes des utilisateurs sont acheminées intelligemment au point de présence de périphérie du CDN qui présente les meilleures performances. Cela améliore considérablement les performances et l’expérience utilisateur. Pour obtenir la liste actuelle des emplacements des nœuds CDN, consultez la page [Emplacements des points de présence (POP) du réseau de distribution de contenu (CDN) Azure](cdn-pop-locations.md).

Les avantages de l’utilisation du CDN pour mettre en cache des données Azure sont notamment les suivants :

- de meilleures performances et une expérience utilisateur enrichie pour les utilisateurs finaux éloignés d’une source de contenu et qui utilisent des applications ayant recours à de nombreuses requêtes HTTP pour charger le contenu ;
- une diffusion à grande échelle pour améliorer la gestion instantanée des charges importantes, par exemple au début d’un événement comme le lancement d’un produit.
- Si l’on répartit les requêtes utilisateur et la diffusion du contenu sur les points de présence de la périphérie globale, le trafic transmis à l’origine est moins important.

>[AZURE.TIP]Azure CDN peut distribuer des contenus issus d’origines diverses. Les origines intégrées dans Azure incluent App Service, Cloud Service, des objets blobs de stockage et le service multimédia. Vous pouvez également définir une origine personnalisée en utilisant n’importe quelle adresse web accessible publiquement.

##Comment activer le réseau de diffusion de contenu (CDN)

1. Créer un profil CDN avec un point de terminaison référençant votre origine

	Un profil CDN est une collection de points de terminaison CDN. Chaque profil contient un ou plusieurs points de terminaison CDN. Une fois que vous avez créé un profil CDN, vous pouvez en créer un nouveau en utilisant l’origine que vous avez choisie.
	
	>[AZURE.NOTE]Par défaut, un abonnement Azure unique est limité à quatre profils CDN. Chaque profil CDN est limité à dix points de terminaison CDN.
	>
	> La tarification CDN est appliquée au niveau du profil CDN. Si vous souhaitez utiliser une combinaison de fonctionnalités CDN Standard et Premium, vous aurez besoin de plusieurs profils CDN.
	
	Pour obtenir un didacticiel détaillé sur la création de points de terminaison et de profils CDN, consultez [Comment activer le réseau de distribution de contenu pour Azure](cdn-create-new-endpoint.md).
	
2. Configuration de votre configuration CDN

	Vous pouvez activer un certain nombre de fonctionnalités à votre point de terminaison CDN, par exemple la [stratégie de mise en cache](cdn-caching-policy.md), la [mise en cache de la chaîne de requête](cdn-query-string.md), [moteur de règles](cdn-rules-engine.md), entre autres. Pour plus d’informations, consultez le menu **Gérer** de gauche.

3. Accès au contenu du CDN

	Pour accéder au contenu mis en cache sur le CDN, utilisez l’URL CDN fournie dans le portail. Par exemple, l’adresse d’un objet blob mis en cache est au format suivant : `http://<identifier>.azureedge.net/<myPublicContainer>/<BlobName>`

## Mise en cache de contenu à partir d'un stockage Azure

Une fois que le CDN est activé sur un compte de stockage Azure, tous les objets blob se trouvant dans des conteneurs publics et qui sont disponibles pour l'accès anonyme sont mis en cache via le CDN. Seuls les objets blob disponibles publiquement peuvent être mis en cache avec le CDN Azure. Pour rendre un objet blob publiquement disponible pour l'accès anonyme, vous devez marquer son conteneur comme étant public. Ensuite, tous les objets blob dans ce conteneur seront accessibles en lecture anonymement. Vous avez également la possibilité de rendre publiques les données du conteneur ou de restreindre l'accès uniquement aux objets blob qu'il contient. Pour plus d’informations sur la gestion du contrôle d’accès aux conteneurs et aux objets blobs, consultez [Gérer l’accès aux ressources de stockage Azure](../storage/storage-manage-access-to-resources.md).

Pour de meilleures performances, utilisez le cache de périmètre CDN pour distribuer des objets blob dont la taille est inférieure à 10 Go.

Quand vous activez l’accès CDN d’un compte de stockage, le portail de gestion vous fournit un nom de domaine CDN au format suivant : `http://<identifier>.azureedge.net/`. Ce nom de domaine peut être utilisé pour accéder aux objets blob dans un conteneur public. Les utilisateurs peuvent, par exemple, accéder aux objets blobs d’un conteneur public nommé « musique » dans un compte de stockage nommé « mon\_compte », via l’une de ces deux URL suivantes :

- **URL du service Azure Blob** : `http://myAccount.blob.core.windows.net/music/` 
- **URL du CDN Azure** : `http://<identifier>.azureedge.net/music/` 

## Mise en cache de contenu à partir de sites web Azure

Vous pouvez activer le CDN à partir de vos site web pour mettre en cache du contenu web, tels que des images, des scripts et des feuilles de style. Consultez la rubrique [Intégration d'un site web Azure à Azure CDN](../app-service-web/cdn-websites-with-cdn.md).

Quand vous activez l’accès CDN pour un site web, le portail de gestion vous fournit un nom de domaine CDN au format suivant : `http://<identifier>.azureedge.net/`. Ce nom de domaine peut être utilisé pour récupérer des objets d'un site web. Par exemple, dans le cas d'un conteneur public nommé cdn et d'un fichier image appelé music.png, les utilisateurs peuvent accéder à l'objet en utilisant une des deux URL suivantes :

- **URL du site Web Azure** : `http://mySiteName.azurewebsites.net/cdn/music.png` 
- **URL du CDN Azure** : `http://<identifier>.azureedge.net/cdn/music.png`
 
## Mise en cache de contenu à partir de services cloud Azure

Vous pouvez mettre en cache sur le CDN des objets fournis par un service cloud Azure.

Du point de vue des services cloud, la mise en cache présente les contraintes suivantes :


- Le CDN doit uniquement servir à mettre en cache du contenu statique.

	>[AZURE.WARNING]La mise en cache de contenu extrêmement volatil ou particulièrement dynamique peut affecter vos performances ou provoquer des problèmes de contenu, avec à la clé un surcoût.
- Votre service cloud doit être déployé dans un environnement de production.
- Votre service cloud doit fournir l'objet sur le port 80 à l'aide du protocole HTTP.
- Le service cloud doit placer le contenu à mettre en cache ou à distribuer dans son dossier /cdn.

Quand vous activez l’accès CDN pour service cloud, le portail de gestion vous fournit un nom de domaine CDN au format suivant : `http://<identifier>.azureedge.net/`. Ce nom de domaine peut être utilisé pour récupérer des objets d'un service cloud. Par exemple, dans le cas d'un service cloud nommé myHostedService et d'une page web ASP.NET appelée music.aspx qui distribue du contenu, les utilisateurs peuvent accéder à l'objet en utilisant une des deux URL suivantes :


- **URL du service cloud Azure** : `http://myHostedService.cloudapp.net/music.aspx` 
- **URL du CDN Azure** : `http://<identifier>.azureedge.net/music.aspx` 

## Mise en cache de contenu avec les origines personnalisées

Vous pouvez mettre en cache sur le CDN des objets fournis par n’importe quelle application web accessible publiquement.

La mise en cache depuis des origines personnalisées présente les contraintes suivantes :

- Le CDN doit uniquement servir à mettre en cache du contenu statique.

	>[AZURE.WARNING]La mise en cache de contenu extrêmement volatil ou particulièrement dynamique peut affecter vos performances ou provoquer des problèmes de contenu, avec à la clé un surcoût.
- Le contenu relatif à l’origine personnalisée doit être hébergé sur un serveur ayant une adresse IP publique. Les nœuds de périmètre CDN sont incapables de récupérer des ressources à partir de serveurs intranet derrière un pare-feu.

Quand vous activez l’accès CDN pour une origine personnalisée, le portail Azure vous fournit un nom de domaine CDN au format suivant : `http://<identifier>.azureedge.net/`. Ce nom de domaine peut être utilisé pour récupérer des objets d’une origine personnalisée. Par exemple, dans le cas d’un site situé sur www.contoso.com et d’une page web ASP.NET appelée music.aspx qui distribue du contenu, les utilisateurs peuvent accéder à l’objet en utilisant l’une des deux URL suivantes :


- **URL d’origine personnalisée** : `http://www.contoso.com/music.aspx` 
- **URL du CDN Azure** : `http://<identifier>.azureedge.net/music.aspx` 

## Mise en cache de contenu spécifique avec des chaînes de requête

Vous pouvez utiliser des chaînes de requête pour différencier les objets récupérés à partir d’une origine. Par exemple, si le service cloud affiche un graphique évolutif, vous pouvez passer une chaîne de requête pour récupérer le graphique spécifique requis. Par exemple :

`http://<identifier>.azureedge.net/chart.aspx?item=1`

Les chaînes de requête sont passées en tant que littéraux de chaîne. Si votre service prend deux paramètres, par exemple `?area=2&item=1` et que appelez par la suite des appels l’origine en utilisant `?item=1&area=2`, vous allez mettre en cache deux copies du même objet.

Pour plus d’informations sur la mise en cache de chaîne de requête, consultez [Contrôle du comportement de mise en cache des requêtes CDN avec des chaînes de requête](cdn-query-string.md).

## Accès à du contenu mis en cache via le protocole HTTPS

Azure vous permet de récupérer du contenu du CDN via des appels HTTPS. Vous pouvez ainsi incorporer le contenu mis en cache dans le CDN dans des pages web sécurisées sans recevoir d'avertissement sur des types de contenu de sécurité mixte.

L'accès à du contenu CDN à l'aide du protocole HTTPS présente les contraintes suivantes :


- Vous devez utiliser le certificat fourni par le CDN. Les certificats tiers ne sont pas pris en charge.
- Vous devez utiliser le domaine CDN pour accéder au contenu. La prise en charge du protocole HTTPS n'est pas disponible pour les noms de domaines personnalisés (enregistrements CNAME), car le CDN ne prend pas en charge les certificats personnalisés pour l'instant.

Pour plus d'informations sur l'activation du protocole HTTPS pour le contenu CDN, consultez la rubrique [Comment activer le réseau de distribution de contenu (CDN) pour Azure](cdn-create-new-endpoint.md).


## Accès à du contenu mis en cache avec des domaines personnalisés

Vous pouvez mapper le point de terminaison HTTP CDN à un nom de domaine personnalisé et utiliser ce nom pour demander des objets depuis le CDN.

Pour plus d'informations sur le mappage d'un domaine personnalisé, consultez la rubrique [Comment mapper le contenu du réseau de distribution de contenu Azure (CDN) à un domaine personnalisé](cdn-map-content-to-custom-domain.md).

## Gestion du CDN par programmation

Microsoft Azure CDN peut être géré par le biais de la programmation à l’aide de l’[API REST de fournisseur de ressources CDN](https://msdn.microsoft.com/library/mt634456.aspx).


## Voir aussi

- [Comment activer le réseau de distribution de contenu pour Azure](cdn-create-new-endpoint.md)
- [Vue d’ensemble du réseau de distribution de contenu (CDN) Azure](cdn-overview.md)
- [Purger un point de terminaison CDN Azure](cdn-purge-endpoint.md)
- [Fournisseur de ressources CDN API REST](https://msdn.microsoft.com/library/mt634456.aspx)

<!---HONumber=AcomDC_0121_2016-->