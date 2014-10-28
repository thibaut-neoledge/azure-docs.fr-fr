<properties pageTitle="Add paging to data (JavaScript) - Azure Mobile Services" metaKeywords="" description="Learn how to use paging to manage the amount of data returned to your Windows Store JavaScript app from Mobile Services." metaCanonical="http://www.windowsazure.com/fr-fr/develop/mobile/tutorials/add-paging-to-data-dotnet/" services="" documentationCenter="Mobile" title="Refine Mobile Services queries with paging" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Affinage des requêtes Mobile Services au moyen de la pagination

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-add-paging-data" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-add-paging-data" title="Windows Phone">Windows Phone</a>
<!--<a href="/fr-fr/documentation/articles/mobile-services-ios-add-paging-data" title="iOS">iOS</a><a href="/fr-fr/documentation/articles/mobile-services-android-add-paging-data" title="Android">Android</a>-->
</div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-add-paging-data" title=".NET backend" class="current">.NET backend</a> | <a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-add-paging-data"  title="JavaScript backend">JavaScript backend</a></div>

Cette rubrique montre comment utiliser la pagination pour gérer la quantité de données renvoyées vers votre application Windows Store à partir d'Azure Mobile Services. Dans ce didacticiel, vous allez utiliser les méthodes de requête **Take** et **Skip** sur le client pour demander des « pages » spécifiques de données.

> [WACOM.NOTE]Pour éviter tout dépassement de capacité dans les appareils mobiles clients, Mobile Services implémente une limite automatique du nombre de pages, qui autorise par défaut un maximum de 50 éléments par réponse. En spécifiant la taille de page, vous pouvez demander explicitement jusqu'à 1 000 éléments dans la réponse.

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données][]. Avant de commencer ce didacticiel, vous devez suivre au moins le premier didacticiel consacré à l'utilisation des séries de données, intitulé [Prise en main des données][].

[WACOM.INCLUDE [mobile-services-javascript-paging][]]

## <a name="next-steps"> </a>Étapes suivantes

Vous voici parvenu à la fin de la série de didacticiels présentant les principes de base de l'utilisation des données dans Mobile Services. Pour plus d'informations sur les rubriques Mobile Services suivantes :

-   [Prise en main de l'authentification][]

    En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

-   [Prise en main des notifications Push][]

    En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

  [Windows Store C#]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-add-paging-data "Windows Store C#"
  [Windows Store JavaScript]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-add-paging-data "Windows Store JavaScript"
  [Windows Phone]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-add-paging-data "Windows Phone"
  [.NET backend]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-add-paging-data ".NET backend"
  [JavaScript backend]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-add-paging-data "JavaScript backend"
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/
  [mobile-services-javascript-paging]: ../includes/mobile-services-javascript-paging.md
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-users/
  [Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push/
