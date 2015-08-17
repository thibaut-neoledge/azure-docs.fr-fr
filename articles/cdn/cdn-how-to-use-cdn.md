<properties 
	pageTitle="Utilisation de CDN - Guide des fonctionnalités Azure" 
	description="Découvrez comment utiliser le réseau de distribution de contenu (CDN) Azure pour diffuser du contenu haut débit en mettant en cache les objets blob et le contenu statique." 
	services="cdn" 
	documentationCenter=".net" 
	authors="zhangmanling" 
	manager="dwrede" 
	editor=""/>


<tags 
	ms.service="cdn" 
	ms.workload="tbd" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="get-started-article" 
	ms.date="05/19/2015" 
	ms.author="mazha"/>



# Utilisation du réseau de distribution de contenu (CDN) Azure

Le réseau de distribution de contenu (CDN) Azure est le bloc de construction de base pour mettre à l’échelle toute application HTTP dans Azure. Il offre aux clients Azure une solution globale qui met en cache le contenu et le place à proximité des utilisateurs. Par conséquent, au lieu de contacter systématiquement le point d’origine, les demandes des utilisateurs sont acheminées intelligemment au point de présence de périphérie du CDN qui présente les meilleures performances. Cela améliore considérablement les performances et l’expérience utilisateur. Pour obtenir la liste actuelle des emplacements de nœuds CDN, consultez la page [Emplacements des nœuds CDN Azure\].

Les avantages de l’utilisation du CDN pour mettre en cache des données Azure sont notamment les suivants :

-   de meilleures performances et une expérience utilisateur plus agréable pour les utilisateurs finaux qui se trouvent éloignés d’une source de contenu et qui utilisent des applications ayant recours à de nombreux « trajets Internet » pour charger le contenu ;
-   une diffusion à grande échelle pour améliorer la gestion instantanée des charges importantes, par exemple au début d’un événement comme le lancement d’un produit.
-   En répartissant les requêtes utilisateur et la diffusion du contenu sur les points de présence de la périphérie globale, le trafic envoyé à l’origine est moins important, réduisant ainsi la charge de travail.

Les clients du CDN existants peuvent désormais utiliser le CDN Azure dans le [portail de gestion Azure\]. Le CDN est une fonctionnalité sous forme de module, ajoutée à votre abonnement et qui présente un [profil de facturation\] distinct.

##Étape 1 : création d’une origine de CDN dans Azure

L’origine du CDN est l’emplacement à partir duquel le CDN charge le contenu et le place en cache au niveau des points de présence de périphérie. L’origine Azure intégrée comprend Azure Apps, Cloud Services, Storage et Media services.

## Étape 2 : création d’un point de terminaison CDN pointant vers votre origine Azure

Une fois votre origine définie, elle est disponible dans la liste d’origines lorsque vous [créez un point de terminaison CDN](cdn-create-new-endpoint.md).

> [AZURE.NOTE]La configuration créée pour le point de terminaison ne sera pas disponible immédiatement, car la propagation de l’enregistrement sur le CDN peut prendre jusqu’à 60 minutes. Les utilisateurs qui tentent d’utiliser immédiatement le nom de domaine CDN peuvent recevoir le code d’état 400 (Requête incorrecte) jusqu’à ce que le contenu soit disponible via le CDN.

## Étape 3 : configuration de votre configuration CDN 

Vous pouvez activer un certain nombre de fonctionnalités pour votre point de terminaison CDN, par exemple la stratégie de mise en cache, la mise en cache de la chaîne de requête, etc.

## Étape 4 : accès au contenu du CDN

Pour accéder au contenu mis en cache sur le CDN, utilisez l’URL CDN fournie dans le portail. Par exemple, l’adresse d’un objet blob mis en cache est au format suivant : http://<*espace\_noms\_CDN*>.vo.msecnd.net/<*monConteneurPublic*>/<*NomBlob*>



## Voir aussi

[Vue d’ensemble du réseau de distribution de contenu (CDN) Azure](cdn-overview.md)
 

<!---HONumber=August15_HO6-->