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
	ms.tgt_pltfrm="vs-what-happened" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/8/2014" 
	ms.author="kempb"/>

> [AZURE.SELECTOR]
> - [Mise en route](vs-mobile-services-dotnet-getting-started.md)
> - [Que s'est-il passé](vs-mobile-services-dotnet-what-happened.md)

###<span id="whathappened">Qu'est-il arrivé à mon projet ?</span>

#####Références ajoutées

Le package NuGet Azure Mobile Services a été ajouté à votre projet. Suite à cette opération, les références .NET suivantes ont été ajoutées à votre projet :

- `Microsoft.WindowsAzure.Mobile`
- `Microsoft.WindowsAzure.Mobile.Ext`
- `Newtonsoft.Json`
- `System.Net.Http.Extensions`
- `System.Net.Http.Primitives` 

#####Valeurs de la chaîne de connexion pour Mobile Services

Dans votre fichier App.xaml.cs, un objet **MobileServiceClient** a été créé avec l'URL et la clé d'application du service mobile sélectionné. 

[En savoir plus sur les services mobiles](http://azure.microsoft.com/documentation/services/mobile-services/)


<!--HONumber=42-->
