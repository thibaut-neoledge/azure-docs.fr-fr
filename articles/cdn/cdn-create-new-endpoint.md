<properties 
	 pageTitle="Comment activer le réseau de distribution de contenu (CDN) pour Azure" 
	 description="Cette rubrique montre comment activer le réseau de distribution de contenu (CDN) pour Azure." 
	 services="cdn" 
	 documentationCenter="" 
	 authors="zhangmanling" 
	 manager="dwrede" 
	 editor=""/>
<tags 
	 ms.service="cdn" 
	 ms.workload="media" 
	 ms.tgt_pltfrm="na" 
	 ms.devlang="na" 
	 ms.topic="article" 
	 ms.date="03/05/2015" 
	 ms.author="mazha"/>



#Comment activer le réseau de distribution de contenu (CDN) pour Azure  

Dès que vous activez un point de terminaison CDN pour votre origine en tant que compte de stockage ou service cloud, tous les objets disponibles publiquement peuvent bénéficier de la mise en cache de proximité CDN.  


##Pour créer un point de terminaison CDN  

1.	Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com/).
2.	Dans le volet de navigation, cliquez sur **CDN**.
3.	Dans le ruban, cliquez sur **Nouveau**. Dans la boîte de dialogue **Nouveau**, sélectionnez **Services d'application**, puis **CDN**, puis **Création rapide**.
4.	Dans la liste déroulante **Domaine d'origine**, sélectionnez la destination souhaitée dans la liste des types d'origine disponibles.
5.	Cliquez sur le bouton **Créer** pour créer le point de terminaison.




> Remarque : La configuration créée pour le point de terminaison ne sera pas disponible immédiatement, car la propagation de l'enregistrement sur le CDN peut prendre jusqu'à 60 minutes. Les utilisateurs qui tentent d'utiliser immédiatement le nom de domaine CDN peuvent recevoir le code d'état 400 (Requête incorrecte) jusqu'à ce que le contenu soit disponible via le CDN.

#Voir aussi
[Comment mapper le contenu du réseau de distribution de contenu Azure (CDN) à un domaine personnalisé](cdn-map-content-to-custom-domain.md)

<!--HONumber=49--> 