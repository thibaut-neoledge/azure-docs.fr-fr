<properties writer="ricksal" pageTitle="Call a custom API from an Android client | Mobile Dev Center" metaKeywords="" description="Learn how to define a custom API and then call it from an Android app that uses Azure Mobile Services." metaCanonical="" disqusComments="1" umbracoNaviHide="1" documentationCenter="Mobile" title="Call a custom API from the client" authors="glenga" />

Appel d'une API personnalisée à partir du client
================================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-windows-store-dotnet-call-custom-api "Windows Store C#")[Windows Store JavaScript](/en-us/documentation/articles/mobile-services-windows-store-javascript-call-custom-api "Windows Store JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-call-custom-api "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-ios-call-custom-api "iOS")[Android](/en-us/documentation/articles/mobile-services-android-call-custom-api "Android")
[.NET backend](/en-us/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api ".NET backend") | [JavaScript backend](/en-us/documentation/articles/mobile-services-android-call-custom-api "JavaScript backend")

Cette rubrique explique comment appeler une API personnalisée à partir d'une application Android. Une API personnalisée vous permet de définir des points de terminaison exposant une fonctionnalité de serveur qui ne mappe pas vers une opération d'insertion, de mise à jour, de suppression ou de lecture. En utilisant une API personnalisée, vous pouvez exercer davantage de contrôle sur la messagerie, notamment lire et définir des en-têtes de message HTTP et définir un format de corps de message autre que JSON.

L'API personnalisée créée dans cette rubrique vous permet d'envoyer une seule requête POST qui définit l'indicateur *completed* sur `true` pour tous les éléments todo de la table de votre service mobile. En l'absence de cette API personnalisée, le client devrait envoyer des demandes individuelles de mise à jour de l'indicateur pour chaque élément todo de la table.

Vous allez ajouter cette fonctionnalité à l'application que vous avez créée avec le didacticiel [Prise en main de Mobile Services](/en-us/documentation/articles/mobile-services-android-get-started/) ou [Prise en main des données](/en-us/documentation/articles/mobile-services-android-get-started-data/). À cet effet, vous allez exécuter la procédure suivante :

1.  [Définition de l'API personnalisée](#define-custom-api)
2.  [Mise à jour de l'application pour appeler l'API personnalisée](#update-app)
3.  [Test de l'application](#test-app)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez avoir terminé le didacticiel [Prise en main de Mobile Services](/en-us/documentation/articles/mobile-services-android-get-started/) ou [Prise en main des données](/en-us/documentation/articles/mobile-services-android-get-started-data/).

Définition de l'API personnalisée
---------------------------------

[WACOM.INCLUDE [mobile-services-create-custom-api](../includes/mobile-services-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-android-call-custom-api](../includes/mobile-services-android-call-custom-api.md)]

Étapes suivantes
----------------

Maintenant que vous avez créé une API personnalisée et que vous l'avez appelée à partir de votre application Android, vous pouvez consulter les rubriques Mobile Services suivantes pour obtenir plus d'informations :

-   [Référence de script serveur Mobile Services](http://go.microsoft.com/fwlink/?LinkId=262293)
    <br/>Familiarisez-vous avec la création des API personnalisées.

-   [Stockage des scripts serveur dans le contrôle du code source](/en-us/documentation/articles/mobile-services-store-scripts-source-control)
     <br/>Apprenez à utiliser la fonctionnalité de contrôle du code source pour développer et publier plus facilement et de manière plus sécurisée le code de script de l'API personnalisée.


