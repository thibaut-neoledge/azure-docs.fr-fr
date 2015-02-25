<properties pageTitle="Affinement des requêtes Mobile Services au moyen de la pagination (Windows Store) | Centre de développement mobile" description="Découvrez comment utiliser la pagination pour gérer la quantité de données renvoyées vers votre application Windows Store à partir de Mobile Services." services="mobile-services" documentationCenter="windows" authors="ggailey777" manager="dwrede" editor=""/>

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/09/2014" ms.author="glenga"/>


# Affinage des requêtes Mobile Services au moyen de la pagination

[AZURE.INCLUDE [mobile-services-selector-add-paging-data](../includes/mobile-services-selector-add-paging-data.md)]

Cette rubrique vous présente l'utilisation de la pagination pour gérer la quantité de données renvoyées vers votre application Windows Store à partir d'Azure Mobile Services. Dans ce didacticiel, vous allez utiliser les méthodes de requête **Take** et **Skip** sur le client pour demander des " pages " spécifiques de données.

>[AZURE.NOTE]Pour éviter tout dépassement de capacité dans les appareils mobiles clients, Mobile Services implémente une limite automatique du nombre de pages, qui autorise par défaut un maximum de 50 éléments par réponse. En spécifiant la taille de page, vous pouvez demander explicitement jusqu'à 1 000 éléments dans la réponse.

Ce didacticiel s'appuie sur la procédure et l'exemple d'application présentés dans le didacticiel précédent intitulé [Prise en main des données]. Avant de commencer ce didacticiel, vous devez suivre au moins le premier didacticiel consacré à l'utilisation des séries de données, intitulé [Prise en main des données]. 

[AZURE.INCLUDE [mobile-services-windows-dotnet-paging](../includes/mobile-services-windows-dotnet-paging.md)]

## <a name="next-steps"> </a>Étapes suivantes

Vous voici parvenu à la fin de la série de didacticiels présentant les principes de base de l'utilisation des données dans Mobile Services. Pour plus d'informations sur les rubriques Mobile Services suivantes :

* [Prise en main de l'authentification]
  <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec un compte Windows.

* [Prise en main des notifications Push] 
  <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.
  
* [Guide de fonctionnement Mobile Services .NET]
  <br/>Découvrez plus en détail comment utiliser Mobile Services avec .NET.
  
<!-- Anchors. -->

[Étapes suivantes]:#next-steps

<!-- Images. -->


<!-- URLs. -->
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-store-get-started/
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-users/
[Prise en main des notifications Push]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-push/

[Portail de gestion]: https://manage.windowsazure.com/
[Guide de fonctionnement Mobile Services .NET]: /fr-fr/develop/mobile/how-to-guides/work-with-net-client-library


<!--HONumber=42-->
