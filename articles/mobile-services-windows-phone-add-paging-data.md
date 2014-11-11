<properties pageTitle="Add paging to data (Windows Phone) | Mobile Dev Center" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Windows Phone app from Mobile Services." metaCanonical="" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-phone" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Affinage des requêtes Mobile Services au moyen de la pagination

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-add-paging-data" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-add-paging-data" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/fr-fr/documentation/articles/mobile-services-windows-phone-add-paging-data" title="Windows Phone" class="current">Windows Phone</a><a href="/fr-fr/documentation/articles/mobile-services-ios-add-paging-data" title="iOS" class="current">iOS</a><a href="/fr-fr/documentation/articles/mobile-services-android-add-paging-data" title="Android" class="current">Android</a><a href="/fr-fr/documentation/articles/mobile-services-html-add-paging-data" title="HTML" class="current">HTML</a><a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-ios-add-paging-data" title="Xamarin.iOS">Xamarin.iOS</a><a href="/fr-fr/documentation/articles/partner-xamarin-mobile-services-android-add-paging-data" title="Xamarin.Android" class="current">Xamarin.Android</a></div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-add-paging-data" title=".NET backend">.NET backend</a> | <a href="/fr-fr/documentation/articles/mobile-services-windows-phone-add-paging-data"  title="JavaScript backend" class="current">JavaScript backend</a></div>

Cette rubrique montre comment utiliser la pagination pour gérer la quantité de données renvoyées vers votre application Windows Phone à partir d'Azure Mobile Services. Dans ce didacticiel, vous allez utiliser les méthodes de requête **Take** et **Skip** sur le client pour demander des « pages » spécifiques de données.

> [WACOM.NOTE]Pour éviter tout dépassement de capacité dans les appareils mobiles clients, Mobile Services implémente une limite automatique du nombre de pages, qui autorise par défaut un maximum de 50 éléments par réponse. En spécifiant la taille de page, vous pouvez demander explicitement jusqu'à 1 000 éléments dans la réponse.

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données][Prise en main des données]. Avant de commencer ce didacticiel, vous devez suivre au moins le premier didacticiel consacré à l'utilisation des séries de données, intitulé [Prise en main des données][Prise en main des données].

[WACOM.INCLUDE [mobile-services-windows-dotnet-paging](../includes/mobile-services-windows-dotnet-paging.md)]

## <a name="next-steps"> </a>Étapes suivantes

Vous voici parvenu à la fin de la série de didacticiels présentant les principes de base de l'utilisation des données dans Mobile Services. Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main de l'authentification][Prise en main de l'authentification]

    En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

-   [Prise en main des notifications Push][Prise en main des notifications Push]

    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-data/
  [mobile-services-windows-dotnet-paging]: ../includes/mobile-services-windows-dotnet-paging.md
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-users/
  [Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-windows-phone-get-started-push/
