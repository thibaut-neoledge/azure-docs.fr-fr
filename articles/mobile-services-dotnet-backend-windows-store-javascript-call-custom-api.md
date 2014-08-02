<properties pageTitle="Call a custom API from a Windows Store client - Mobile Services" metaKeywords="" description="Learn how to define a custom API and then call it from a Windows Store app that use Windows Azure Mobile Services." metaCanonical="" services="" documentationCenter="" title="Call a custom API from the client" authors="" solutions="" writer="glenga" manager="" editor="" />

Appel d'une API personnalisée à partir du client
================================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-call-custom-api "Windows Store C#")[Windows Store JavaScript](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-call-custom-api "Windows Store JavaScript")[Windows Phone](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-call-custom-api "Windows Phone")[iOS](/en-us/documentation/articles/mobile-services-dotnet-backend-ios-call-custom-api "iOS")[Android](/en-us/documentation/articles/mobile-services-dotnet-backend-android-call-custom-api "Android")

[.NET principal](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-call-custom-api ".NET principal") | [JavaScript principal](/en-us/documentation/articles/mobile-services-windows-store-javascript-call-custom-api "JavaScript principal")

Cette rubrique explique comment appeler une API personnalisée à partir d'une application Windows Store. Une API personnalisée vous permet de définir des points de terminaison exposant une fonctionnalité de serveur qui ne mappe pas vers une opération d'insertion, de mise à jour, de suppression ou de lecture. En utilisant une API personnalisée, vous pouvez exercer davantage de contrôle sur la messagerie, notamment lire et définir des en-têtes de message HTTP et définir un autre format de corps de message que JSON.

L'API personnalisée créée dans cette rubrique permet d'envoyer une seule requête POST qui définit l'indicateur complété sur `true` pour toutes les tâches (éléments todo) de la table. En l'absence de cette API personnalisée, le client doit envoyer des demandes individuelles de mise à jour de l'indicateur pour chaque élément todo de la table.

Vous allez ajouter cette fonctionnalité à l'application que vous avez créée avec le didacticiel [Prise en main de Mobile Services](/en-us/documentation/articles/mobile-services-windows-store-get-started/) ou [Prise en main des données](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/). Pour cela, vous allez exécuter la procédure suivante :

1.  [Définition de l'API personnalisée](#define-custom-api)
2.  [Mise à jour de l'application pour appeler l'API personnalisée](#update-app)
3.  [Test de l'application](#test-app)

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez avoir terminé le didacticiel [Prise en main de Mobile Services](/en-us/documentation/articles/mobile-services-windows-store-get-started/) ou [Prise en main des données](/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-data/). Ce didacticiel utilise Visual Studio 2012 Express pour Windows 8.

Définition de l'API personnalisée
---------------------------------

[WACOM.INCLUDE [mobile-services-dotnet-backend-create-custom-api](../includes/mobile-services-dotnet-backend-create-custom-api.md)]

[WACOM.INCLUDE [mobile-services-windows-store-javascript-call-custom-api](../includes/mobile-services-windows-store-javascript-call-custom-api.md)]

Étapes suivantes
----------------

Maintenant que vous avez créé une API personnalisée et que vous l'avez appelée à partir de votre application Windows Store, vous pouvez consulter les rubriques Mobile Services suivantes pour obtenir plus d'informations :

-   [Définition d'une API personnalisée qui prend en charge les notifications périodiques](/en-us/documentation/articles/mobile-services-windows-store-javascript-create-pull-notifications)
    Apprenez à utiliser une API personnalisée pour prendre en charge les notifications périodiques dans une application Windows Store. Si les notifications périodiques sont activées, Windows accédera régulièrement au point de terminaison de votre API personnalisée et utilisera le XML renvoyé, dans un format mosaïque, pour mettre à jour la vignette de l'application dans le menu Démarrer.

-   [Référence des scripts serveur Mobile Services](http://go.microsoft.com/fwlink/?LinkId=262293)
    Familiarisez-vous avec la création des API personnalisées.

-   [Stockage des scripts serveur dans le contrôle du code source](/en-us/documentation/articles/mobile-services-store-scripts-source-control)
     Apprenez à utiliser la fonctionnalité de contrôle du code source pour développer et publier plus facilement et de manière plus sécurisée le code de script de l'API personnalisée.


