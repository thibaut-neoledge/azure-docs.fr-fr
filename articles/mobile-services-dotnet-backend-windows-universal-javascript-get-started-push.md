<properties pageTitle="Get started with push notification using a .NET backend mobile service" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/15/2014" ms.author="glenga" />

# Prise en main des notifications Push dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Cette rubrique explique comment utiliser Azure Mobile Services avec un service principal .NET pour envoyer des notifications Push à une application Windows universelle. Dans ce didacticiel, vous allez activer des notifications Push à l'aide d'Azure Notification Hubs dans un projet d'application Windows universelle. Lorsque vous aurez terminé, votre service mobile enverra une notification Push depuis le service principal .NET vers toutes les applications Windows Store et Windows Phone Store enregistrées, chaque fois qu'un enregistrement est inséré dans la table TodoList. Le concentrateur de notification que vous créez est fourni gratuitement avec votre service mobile, peut être géré indépendamment du service mobile et peut être utilisé par d'autres applications et services.

> [WACOM.NOTE]Cette rubrique explique comment utiliser les outils dans Visual Studio Professional 2013 Update 3 pour ajouter la prise en charge des notifications Push depuis Mobile Services vers une application Windows universelle. La même procédure peut être utilisée pour ajouter la prise en charge des notifications Push depuis Mobile Services vers une application Windows Store ou Windows Phone Store 8.1. Si vous ne pouvez pas procéder à la mise à niveau vers Visual Studio Professional 2013 Update 3 ou préférez ajouter manuellement votre projet de service mobile à une solution d'application Windows Store, consultez [cette version][cette version] de la rubrique.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Inscription de votre application pour les notifications Push][Inscription de votre application pour les notifications Push]
2.  [Mise à jour du service pour l'envoi de notifications Push][Mise à jour du service pour l'envoi de notifications Push]
3.  [Activation des notifications Push pour les tests en local][Activation des notifications Push pour les tests en local]
4.  [Test des notifications Push dans votre application][Test des notifications Push dans votre application]

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

-   Un [compte Microsoft Store][compte Microsoft Store] actif.
-   [Visual Studio Professional 2013][Visual Studio Professional 2013] (Update 3 ou une version ultérieure).
    Une version d'évaluation gratuite est disponible.

## <span id="register"></span></a>Inscription de votre application pour les notifications Push

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

1.  Accédez au dossier de projet `\services\mobileServices\scripts`, copiez le fichier de script \<*nom\_de\_votre\_service*\>.push.register.js dans le dossier `\js` partagé, puis supprimez ce fichier des deux projets d'application Windows et WindowsPhone individuels.

2.  Ouvrez ce fichier de script dans le dossier de projet `\js` partagé, identifiez le code dans l'écouteur d'événements *activé* qui enregistre l'URL de canal d'appareil avec le hub de notification, puis supprimez la fonction de promesse **done**.

    Ce didacticiel permet d'envoyer des notifications lorsqu'un nouvel élément est inséré, pas lorsqu'une API personnalisée est appelée.

3.  Dans le projet d'application Windows, ouvrez le fichier default.html et modifiez le chemin d'accès de la référence du fichier de script vers le dossier de projet `\js`, pour qu'il se présente comme suit :

        <script src="/js/your_service_name.push.register.js"></script>

4.  Répétez cette étape pour le projet d'application Windows Phone.

    Les deux projets utilisent maintenant une version partagée du script d'inscription Push.

Maintenant que les notifications Push sont activées dans l'application, vous devez mettre à jour le service mobile pour les envoyer.

## <span id="update-service"></span></a>Mise à jour du service pour l'envoi de notifications Push

La procédure suivante permet de mettre à jour la classe TodoItemController existante pour envoyer des notifications Push à tous les appareils enregistrés lorsqu'un nouvel élément est inséré. Vous pouvez implémenter un code similaire dans n'importe quel [ApiController], [TableController] personnalisé, ou n'importe où dans vos services principaux.

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

## <span id="local-testing"></span></a> Activation des notifications Push pour les tests en local

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

Les étapes restantes de cette section sont facultatives. Elles vous permettent de tester votre application sur un service mobile qui s'exécute sur un ordinateur local. Si vous souhaitez tester les notifications Push sur le service mobile dans Azure, vous pouvez passer à la dernière section. L'assistant Ajouter une notification Push a en effet déjà configuré votre application pour se connecter à votre service lorsqu'il s'exécute dans Azure.

> [WACOM.NOTE]N'utilisez jamais de service mobile de production pour les tests et le développement. Pour les tests, publiez toujours votre projet de service mobile vers un service intermédiaire.

1.  Accédez au dossier de projet `\services\mobileServices\settings`, copiez le fichier de script \<*nom\_de\_votre\_service*\>.js script dans le dossier `\js` partagé, puis supprimez ce fichier des deux projets d'application Windows et WindowsPhone individuels. Supprimez également ce fichier du dossier `\services\mobileServices\scripts` dans chaque projet d'application, s'il s'y trouve également.

2.  Ouvrez ce fichier de script dans le dossier de projet `\js` partagé et placez en commentaire le code existant qui définit l'[objet MobileServiceClient][objet MobileServiceClient] utilisé pour accéder au service mobile exécuté dans Azure.

3.  Ajoutez une nouvelle définition d'objet **MobileServiceClient** avec le même nom, mais en utilisant l'URL de l'hôte local dans le constructeur, comme suit :

        // This MobileServiceClient has been configured to communicate with your local
        // test project for debugging purposes.
        var todolistClient = new WindowsAzure.MobileServiceClient(
            "http://localhost:4584");

    En utilisant cet objet **MobileServiceClient**, l'application se connecte au service local au lieu de la version hébergée dans Azure. Si vous souhaitez revenir à une exécution de l'application sur le service mobile hébergé dans Azure, réintégrez les définitions d'objet **MobileServiceClient** d'origine.

## <span id="test"></span></a> Test des notifications Push dans votre application

[WACOM.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel explique les règles de base de l'activation des applications Windows Store pour utiliser Mobile Services et Notification Hubs afin d'envoyer des notifications Push. Vous pouvez ensuite envisager de consulter le didacticiel suivant, [Envoi de notifications Push aux utilisateurs authentifiés][Envoi de notifications Push aux utilisateurs authentifiés], qui explique comment utiliser des balises pour envoyer des notifications Push depuis un service mobile vers des utilisateurs authentifiés uniquement.

Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

-   [Prise en main des données][Prise en main des données]
    En savoir plus sur le stockage et l'interrogation des données à l'aide des services mobiles.

-   [Prise en main de l'authentification][Prise en main de l'authentification]
    En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents utilisant des services mobiles.

-   [Présentation de Notification Hubs][Présentation de Notification Hubs]
    Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

-   [Utilisation d'un client HTML/JavaScript pour Azure Mobile Services][Utilisation d'un client HTML/JavaScript pour Azure Mobile Services]
    En savoir plus sur l'utilisation de Mobile Services à partir d'applications HTML et JavaScript.


  [mobile-services-selector-get-started-push]: ../includes/mobile-services-selector-get-started-push.md
  [cette version]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push
  [Inscription de votre application pour les notifications Push]: #register
  [Mise à jour du service pour l'envoi de notifications Push]: #update-service
  [Activation des notifications Push pour les tests en local]: #local-testing
  [Test des notifications Push dans votre application]: #test
  [compte Microsoft Store]: http://go.microsoft.com/fwlink/p/?LinkId=280045
  [Visual Studio Professional 2013]: https://go.microsoft.com/fwLink/p/?LinkID=391934
  [mobile-services-create-new-push-vs2013]: ../includes/mobile-services-create-new-push-vs2013.md
  [mobile-services-dotnet-backend-update-server-push]: ../includes/mobile-services-dotnet-backend-update-server-push.md
  [mobile-services-dotnet-backend-configure-local-push-vs2013]: ../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md
  [objet MobileServiceClient]: http://msdn.microsoft.com/fr-fr/library/azure/jj554219.aspx
  [mobile-services-dotnet-backend-windows-universal-test-push]: ../includes/mobile-services-dotnet-backend-windows-universal-test-push.md
  [Envoi de notifications Push aux utilisateurs authentifiés]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-users
  [Présentation de Notification Hubs]: /fr-fr/documentation/articles/notification-hubs-overview/
  [Utilisation d'un client HTML/JavaScript pour Azure Mobile Services]: /fr-fr/documentation/articles/mobile-services-html-how-to-use-client-library
