<properties pageTitle="Add paging to data (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Windows Phone app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

Affinage des requêtes Mobile Services au moyen de la pagination
===============================================================

> [AZURE.SELECTOR-LIST (Platform | Backend )]
>  - [(Windows Store C\# | .NET)](mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data.md) 
>  - [(Windows Store C\# | JavaScript)](mobile-services-windows-store-dotnet-add-paging-data.md) 
>  - [(Windows Store JavaScript | .NET)](mobile-services-dotnet-backend-windows-store-javascript-add-paging-data.md) 
>  - [(Windows Store JavaScript | JavaScript)](mobile-services-windows-store-javascript-add-paging-data.md) 
>  - [(Windows Phone | .NET)](mobile-services-dotnet-backend-windows-phone-add-paging-data) 
>  - [(Windows Phone | JavaScript)](mobile-services-windows-phone-add-paging-data) 
>  - [(iOS | JavaScript)](mobile-services-ios-add-paging-data) 
>  - [(Android | JavaScript)](mobile-services-android-add-paging-data) 
>  - [(HTML | .NET)](mobile-services-html-add-paging-data) 
>  - [(Xamarin iOS | .NET)](partner-xamarin-mobile-services-ios-add-paging-data) 
>  - [(Xamarin Android | .NET)](partner-xamarin-mobile-services-android-add-paging-data)

Cette rubrique montre comment utiliser la pagination pour gérer la quantité de données renvoyées vers votre application Windows Phone à partir d'Azure Mobile Services. Dans le cadre de ce didacticiel, vous allez utiliser les méthodes de requête **Take** et **Skip** sur le client pour demander des « pages » spécifiques de données.

> [WACOM.NOTE]Pour éviter tout dépassement de capacité dans les appareils mobiles clients, Mobile Services implémente une limite automatique du nombre de pages, qui autorise par défaut un maximum de 50 éléments par réponse. En spécifiant la taille de page, vous pouvez demander explicitement jusqu'à 1 000 éléments dans la réponse.

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données](/en-us/documentation/articles/mobile-services-windows-phone-get-started-data/). Avant de commencer ce didacticiel, vous devez suivre au moins le premier didacticiel consacré à l'utilisation des séries de données, intitulé [Prise en main des données](/en-us/documentation/articles/mobile-services-windows-phone-get-started-data/).

[WACOM.INCLUDE [mobile-services-windows-dotnet-paging](../includes/mobile-services-windows-dotnet-paging.md)]

Étapes suivantes
----------------

Vous voici parvenu à la fin de la série de didacticiels présentant les principes de base de l'utilisation des données dans Mobile Services. Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main de l'authentification](/en-us/documentation/articles/mobile-services-windows-phone-get-started-users/)
    <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

-   [Prise en main des notifications Push](/en-us/documentation/articles/mobile-services-windows-phone-get-started-push/)
    <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.


