<properties 
	pageTitle="" 
	description="" 
	services="mobile-services" 
	documentationCenter="" 
	authors="kempb" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/8/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Mise en route](vs-mobile-services-cordova-getting-started.md)
> - [Que s'est-il passé ?](vs-mobile-services-cordova-what-happened.md)

###<span id="whathappened">Qu'est-il arrivé à mon projet ?</span>

#####Références ajoutées

Le plug-in du client Azure Mobile Services (fourni avec toutes les applications hybrides multi-appareils) a été activé.
  
#####Valeurs de la chaîne de connexion pour Mobile Services

Sous `services\mobileServices\settings`, un nouveau fichier JavaScript (.js) avec un objet **MobileServiceClient** a été généré. Il contient l'URL et la clé d'application du service mobile sélectionné. Ce fichier permet l'initialisation d'un objet client du service mobile, de la même manière que le code ci-dessous.

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[En savoir plus sur les services mobiles](http://azure.microsoft.com/documentation/services/mobile-services/)

<!--HONumber=42-->
