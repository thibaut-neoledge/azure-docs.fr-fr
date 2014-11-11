<properties urlDisplayName="Call a custom API from the client" pageTitle="Call a custom API from a Windows Store client - Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from a Windows Store app that use Azure Mobile Services." metaCanonical="" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga" solutions="" manager="" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga" />

# Appel d'une API personnalisée à partir du client

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api" title="Windows Store C#" class="current">Windows Store C#</a><a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-call-custom-api" title="Windows Store JavaScript" class="current">Windows Store JavaScript</a><a href="/fr-fr/documentation/articles/mobile-services-windows-phone-call-custom-api" title="Windows Phone" class="current">Windows Phone</a><a href="/fr-fr/documentation/articles/mobile-services-ios-call-custom-api" title="iOS">iOS</a><a href="/fr-fr/documentation/articles/mobile-services-android-call-custom-api" title="Android" class="current">Android</a><a href="/fr-fr/documentation/articles/mobile-services-html-call-custom-api" title="HTML">HTML</a></div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api" title=".NET backend">.NET backend</a> | <a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api"  title="JavaScript backend" class="current">JavaScript backend</a></div>

Cette rubrique explique comment appeler une API personnalisée à partir d'une application Windows Store. Une API personnalisée vous permet de définir des points de terminaison exposant une fonctionnalité de serveur qui ne mappe pas vers une opération d'insertion, de mise à jour, de suppression ou de lecture. En utilisant une API personnalisée, vous pouvez exercer davantage de contrôle sur la messagerie, notamment lire et définir des en-têtes de message HTTP et définir un autre format de corps de message que JSON.

L'API personnalisée créée dans cette rubrique permet d'envoyer une seule requête POST qui définit l'indicateur complété sur `true` pour toutes les tâches (éléments todo) de la table. En l'absence de cette API personnalisée, le client doit envoyer des demandes individuelles de mise à jour de l'indicateur pour chaque élément todo de la table.

Vous allez ajouter cette fonctionnalité à l'application que vous avez créée avec le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services] ou [Prise en main des données][Prise en main des données]. À cet effet, vous allez exécuter la procédure suivante :

1.  [Définition de l'API personnalisée][Définition de l'API personnalisée]
2.  [Mise à jour de l'application pour appeler l'API personnalisée][Mise à jour de l'application pour appeler l'API personnalisée]
3.  [Test de l'application][Test de l'application]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez avoir terminé le didacticiel [Prise en main de Mobile Services][Prise en main de Mobile Services] ou [Prise en main des données][Prise en main des données]. Ce didacticiel utilise Visual Studio 2012 Express pour Windows 8.

## <a name="define-custom-api"></a>Définition de l'API personnalisée

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-call-custom-api](../includes/mobile-services-windows-store-dotnet-call-custom-api.md)]

## Étapes suivantes

Maintenant que vous avez créé une API personnalisée et que vous l'avez appelée à partir de votre application Windows Store, vous pouvez consulter les rubriques Mobile Services suivantes pour obtenir plus d'informations :

-   [Définition d'une API personnalisée qui prend en charge les notifications périodiques][Définition d'une API personnalisée qui prend en charge les notifications périodiques]

    Apprenez à utiliser une API personnalisée pour prendre en charge les notifications périodiques dans une application Windows Store. Si les notifications périodiques sont activées, Windows accédera régulièrement au point de terminaison de votre API personnalisée et utilisera le XML renvoyé, dans un format mosaïque, pour mettre à jour la vignette de l'application dans le menu Démarrer.

-   [Référence de script serveur Mobile Services][Référence de script serveur Mobile Services]

    Familiarisez-vous avec la création des API personnalisées.

-   [Stockage des scripts serveur dans le contrôle du code source][Stockage des scripts serveur dans le contrôle du code source]

     Apprenez à utiliser la fonctionnalité de contrôle du code source pour développer et publier plus facilement et de manière plus sécurisée le code de script de l'API personnalisée.

  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-store-get-started/
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-get-started-data/
  [Définition de l'API personnalisée]: #define-custom-api
  [Mise à jour de l'application pour appeler l'API personnalisée]: #update-app
  [Test de l'application]: #test-app
  [mobile-services-create-custom-api]: ../includes/mobile-services-create-custom-api.md
  [mobile-services-windows-store-dotnet-call-custom-api]: ../includes/mobile-services-windows-store-dotnet-call-custom-api.md
  [Définition d'une API personnalisée qui prend en charge les notifications périodiques]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-create-pull-notifications
  [Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Stockage des scripts serveur dans le contrôle du code source]: /fr-fr/documentation/articles/mobile-services-store-scripts-source-control
