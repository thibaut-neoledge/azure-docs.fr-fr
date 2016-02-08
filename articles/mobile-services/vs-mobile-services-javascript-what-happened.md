<properties 
	pageTitle="Que se passe-t-il lorsque vous ajoutez Mobiles Services à une application Javascript à l'aide des services connectés Visual Studio ? | Microsoft Azure" 
	description="Décrit ce qui est arrivé à votre projet Azure Mobile Services dans Visual Studio" 
	services="mobile-services" 
	documentationCenter="" 
	authors="mlhoop" 
	manager="douge" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="NA" 
	ms.devlang="JavaScript" 
	ms.topic="article" 
	ms.date="01/05/2016" 
	ms.author="mlearned"/>

# Qu’arrive-t-il à mon projet Javascript lorsque j’ajoute Azure Mobile Services à l’aide des services Visual Studio connectés ?

##Package NuGet ajouté

Le package NuGet **WindowsAzure.MobileServices.WinJS** a été installé, y compris la bibliothèque Azure Mobile Service dans le fichier `js\MobileServices.js`.
  
##Valeurs de la chaîne de connexion pour Mobile Services 

Dans le dossier `services\mobileServices\settings`, un nouveau fichier JavaScript (.js) avec un objet **MobileServiceClient** a été généré. Il contient l’URL et la clé d’application du service mobile sélectionné.

##Ajout de références à default.html

Des références à `MobileServices.js` et le fichier de paramètres ont été ajoutées à `default.html`.

##Ajout de fichiers de services connectés

Dans le dossier des services, les fichiers de configuration des services connectés ont été ajoutés.



 

<!---HONumber=AcomDC_0128_2016-->