<properties writer="ricksal" pageTitle="Call a custom API from an Android client | Mobile Dev Center" metaKeywords="" description="Learn how to define a custom API and then call it from an Android app that uses Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" services="mobile-services" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga"  />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="Mobile-Android" ms.devlang="Java" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Appel d'une API personnalisée à partir du client

<div class="dev-center-tutorial-selector sublanding"><a href="/fr-fr/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api" title="Windows Store C#">Windows Store C#</a><a href="/fr-fr/documentation/articles/mobile-services-windows-store-javascript-call-custom-api" title="Windows Store JavaScript">Windows Store JavaScript</a><a href="/fr-fr/documentation/articles/mobile-services-windows-phone-call-custom-api" title="Windows Phone">Windows Phone</a><a href="/fr-fr/documentation/articles/mobile-services-ios-call-custom-api" title="iOS">iOS</a><a href="/fr-fr/documentation/articles/mobile-services-android-call-custom-api" title="Android" class="current">Android</a><a href="/fr-fr/documentation/articles/mobile-services-html-call-custom-api" title="HTML">HTML</a></div>

<div class="dev-center-tutorial-subselector"><a href="/fr-fr/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api" title=".NET backend">.NET backend</a> | <a href="/fr-fr/documentation/articles/mobile-services-android-call-custom-api"  title="JavaScript backend" class="current">JavaScript backend</a></div>

Cette rubrique montre comment appeler une API personnalisée à partir d'une application Android. Une API personnalisée vous permet de définir des points de terminaison exposant une fonctionnalité de serveur qui ne mappe pas vers une opération d'insertion, de mise à jour, de suppression ou de lecture. En utilisant une API personnalisée, vous pouvez exercer davantage de contrôle sur la messagerie, notamment lire et définir des en-têtes de message HTTP et définir un autre format de corps de message que JSON.

L'API personnalisée créée dans cette rubrique vous permet d'envoyer une seule requête POST qui définit l'indicateur *completed* sur `true` pour toutes les tâches (éléments todo) de la table de votre service mobile. En l'absence de cette API personnalisée, le client doit envoyer des demandes individuelles de mise à jour de l'indicateur pour chaque élément todo de la table.

Vous allez ajouter cette fonctionnalité à l'application que vous avez créée avec le didacticiel [Prise en main de Mobile Services][] ou [Prise en main des données][]. À cet effet, vous allez exécuter la procédure suivante :

1.  [Définition de l'API personnalisée][]
2.  [Mise à jour de l'application pour appeler l'API personnalisée][]
3.  [Test de l'application][]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez avoir terminé le didacticiel [Prise en main de Mobile Services][] ou [Prise en main des données][].

## <a name="define-custom-api"></a>Définition de l'API personnalisée

[WACOM.INCLUDE [mobile-services-create-custom-api][]]

[WACOM.INCLUDE [mobile-services-android-call-custom-api][]]

## Étapes suivantes

Maintenant que vous avez créé une API personnalisée et que vous l'avez appelée à partir de votre application Android, vous pouvez consulter les rubriques Mobile Services suivantes pour obtenir plus d'informations :

-   [Référence de script serveur Mobile Services][]
  
    Familiarisez-vous avec la création des API personnalisées.

-   [Stockage des scripts serveur dans le contrôle du code source][]
 
    Apprenez à utiliser la fonctionnalité de contrôle du code source pour développer et publier plus facilement et de manière plus sécurisée le code de script de l'API personnalisée.


  [Windows Store C#]: /fr-fr/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api "Windows Store C#"
  [Windows Store JavaScript]: /fr-fr/documentation/articles/mobile-services-windows-store-javascript-call-custom-api "Windows Store JavaScript"
  [Windows Phone]: /fr-fr/documentation/articles/mobile-services-windows-phone-call-custom-api "Windows Phone"
  [iOS]: /fr-fr/documentation/articles/mobile-services-ios-call-custom-api "iOS"
  [Android]: /fr-fr/documentation/articles/mobile-services-android-call-custom-api "Android"
  [HTML]: /fr-fr/documentation/articles/mobile-services-html-call-custom-api "HTML"
  [.NET backend]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api ".NET backend"
  [JavaScript backend]: /fr-fr/documentation/articles/mobile-services-android-call-custom-api "JavaScript backend"
  [Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-android-get-started/
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-android-get-started-data/
  [Définition de l'API personnalisée]: #define-custom-api
  [Mise à jour de l'application pour appeler l'API personnalisée]: #update-app
  [Test de l'application]: #test-app
  [mobile-services-create-custom-api]: ../includes/mobile-services-create-custom-api.md
  [mobile-services-android-call-custom-api]: ../includes/mobile-services-android-call-custom-api.md
  [Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/?LinkId=262293
  [Stockage des scripts serveur dans le contrôle du code source]: /fr-fr/documentation/articles/mobile-services-store-scripts-source-control
