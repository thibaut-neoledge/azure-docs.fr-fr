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
	 ms.date="06/03/2015" 
	 ms.author="mazha"/>



#Comment activer le réseau de distribution de contenu (CDN) pour Azure  

CDN peut être activé pour votre origine via le portail de gestion Azure. Le type d'origine actuellement disponible inclut : Web Apps, le stockage, et Cloud Services. Vous pouvez également activer CDN pour votre point de terminaison de diffusion en continu d’Azure Media Services. Dès que vous activez un point de terminaison CDN pour votre origine, tous les objets disponibles publiquement peuvent bénéficier de la mise en cache de proximité CDN.

Notez que vous pouvez également créer une origine personnalisée, qui ne doit pas forcément être Azure.

##Pour créer un point de terminaison CDN  

1.	Connectez-vous au [portail de gestion Azure](http://manage.windowsazure.com/).
2.	Dans le volet de navigation, cliquez sur **CDN**.
3.	Dans le ruban, cliquez sur **New**. Dans la boîte de dialogue **Nouveau**, sélectionnez **APP SERVICES**, **CDN**, puis **CRÉATION RAPIDE**.
4.	Dans la liste déroulante **TYPE D’ORIGINE**, sélectionnez un type d'origine depuis la liste des types d’origine disponibles.
	
	La liste des URL d'origine disponibles apparaîtra dans la liste déroulante **URL D’ORIGINE**.
	

	![createnew][createnew]

	Si vous sélectionnez **Origine personnalisée**, vous pouvez entrer une URL d'origine personnalisée. Qui ne doit pas être forcément une origine Azure.

	![customorigin][customorigin]

	>[AZURE.NOTE]À l’heure actuelle, seul HTTP est pris en charge pour l’origine. Vous devez utiliser l'extension Media Services pour activer Azure CDN pour un point de terminaison de diffusion en continu d’Azure Media Services.
	
5.	Cliquez sur le bouton **Create** pour créer le point de terminaison.


>[AZURE.NOTE]La configuration créée pour le point de terminaison ne sera pas disponible immédiatement, car la propagation de l’enregistrement sur le CDN peut prendre jusqu’à 60 minutes. Les utilisateurs qui tentent d’utiliser immédiatement le nom de domaine CDN peuvent recevoir le code d’état 400 (Requête incorrecte) jusqu’à ce que le contenu soit disponible via le CDN.

##Voir aussi
[Comment mapper le contenu du réseau de distribution de contenu (CDN) à un domaine personnalisé](cdn-map-content-to-custom-domain.md)

[createnew]: ./media/cdn-create-new-endpoint/cdn-create-new-account.png

[customorigin]: ./media/cdn-create-new-endpoint/cdn-custom-origin.png
 

<!---HONumber=July15_HO4-->