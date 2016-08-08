<properties
	pageTitle="Qu'est-il arrivé à mon projet .NET après l'ajout de Mobile Services à l'aide des services connectés de Visual Studio ? | Microsoft Azure"
	description="Décrit ce qui est arrivé à votre projet Visual Studio .NET après l’ajout d’Azure Mobile Services à l’aide des services connectés "
	services="mobile-services"
	documentationCenter=""
	authors="mlhoop"
	manager="douge"
	editor=""/>

<tags
	ms.service="mobile-services"
	ms.workload="mobile"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/21/2016"
	ms.author="mlearned"/>

# Qu'est-il arrivé à mon projet Visual Studio .NET après l'ajout d'Azure Mobile Services à l'aide des services connectés ?

[AZURE.INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

## Références ajoutées

Le package NuGet Azure Mobile Services a été ajouté à votre projet. Suite à cette opération, les références .NET suivantes ont été ajoutées à votre projet :

- **Microsoft.WindowsAzure.Mobile**
- **Microsoft.WindowsAzure.Mobile.Ext**
- **Newtonsoft.Json**
- **System.Net.Http.Extensions**
- **System.Net.Http.Primitives**

## Valeurs de la chaîne de connexion pour Mobile Services

Dans votre fichier App.xaml.cs, un objet **MobileServiceClient** a été créé avec l'URL et la clé d'application du service mobile sélectionné.

## Projet Mobile Services ajouté

Si un service mobile .NET est créé dans le fournisseur de services connectés, un projet de services mobiles est créé et ajouté à la solution.


[En savoir plus sur Mobile Services](https://azure.microsoft.com/documentation/services/mobile-services/)

<!---HONumber=AcomDC_0727_2016-->