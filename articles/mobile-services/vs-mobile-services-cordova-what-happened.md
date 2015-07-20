<properties 
	pageTitle="" 
	description="Décrit ce qui est arrivé à votre projet Azure Mobile Services dans Cordova" 
	services="mobile-services" 
	documentationCenter="" 
	authors="patshea123" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="multiple" 
	ms.topic="article" 
	ms.date="05/06/2015" 
	ms.author="patshea123"/>

# Qu'est-il arrivé à mon projet ?

> [AZURE.SELECTOR]
> - [Getting Started](vs-mobile-services-cordova-getting-started.md)
> - [What Happened](vs-mobile-services-cordova-what-happened.md)

###<span id="whathappened">Qu’est-il arrivé à mon projet ?</span>

#####Références ajoutées

Le plug-in du client Azure Mobile Services (fourni avec toutes les applications hybrides multi-appareils) a été activé.
  
#####Valeurs de la chaîne de connexion pour Mobile Services

Sous `services\mobileServices\settings`, un nouveau fichier JavaScript (.js) avec un objet **MobileServiceClient** a été généré. Il contient l’URL et la clé d’application du service mobile sélectionné. Ce fichier permet l'initialisation d'un objet client du service mobile, de la même manière que le code ci-dessous.

	var mobileServiceClient;
	document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
	        "<your mobile service name>.azure-mobile.net",
	        "<insert your key>"
	    );

[En savoir plus sur Mobile Services](http://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=July15_HO2-->