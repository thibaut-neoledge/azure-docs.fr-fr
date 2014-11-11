<properties pageTitle="Get started with push notification using a JavaScript backend mobile service" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/11/2014" ms.author="glenga" />

# Prise en main des notifications Push dans Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Cette rubrique explique comment utiliser Azure Mobile Services avec un service principal JavaScript pour envoyer des notifications Push à une application Windows universelle. Dans ce didacticiel, vous allez activer des notifications Push à l'aide d'Azure Notification Hubs dans un projet d'application Windows universelle. Lorsque vous aurez terminé, votre service mobile enverra une notification Push depuis le service principal JavaScript vers toutes les applications Windows Store et Windows Phone Store enregistrées, chaque fois qu'un enregistrement est inséré dans la table TodoList. Le concentrateur de notification que vous créez est fourni gratuitement avec votre service mobile, peut être géré indépendamment du service mobile et peut être utilisé par d'autres applications et services.

> [WACOM.NOTE]Cette rubrique explique comment utiliser les outils dans Visual Studio 2013 Update 3 pour ajouter la prise en charge des notifications Push depuis Mobile Services vers une application Windows universelle. La même procédure peut être utilisée pour ajouter la prise en charge des notifications Push depuis Mobile Services vers une application Windows Store ou Windows Phone Store 8.1. Pour ajouter des notifications Push à une application Windows Phone 8 ou Windows Phone Silverlight 8.1, consultez cette version de [Prise en main des notifications Push dans Mobile Services][Prise en main des notifications Push dans Mobile Services].

> Si vous ne pouvez pas procéder à la mise à niveau vers Visual Studio 2013 Update 3 ou préférez ajouter manuellement votre projet de service mobile à une solution d'application Windows Store, consultez [cette version][cette version] de la rubrique.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1.  [Inscription de votre application pour les notifications Push][Inscription de votre application pour les notifications Push]
2.  [Mise à jour du service pour l'envoi de notifications Push][Mise à jour du service pour l'envoi de notifications Push]
3.  [Test des notifications Push dans votre application][Test des notifications Push dans votre application]

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

-   Un [compte Microsoft Store][compte Microsoft Store] actif.
-   [Visual Studio 2013 Express pour Windows][Visual Studio 2013 Express pour Windows] Update 3 ou une version ultérieure

## <span id="register"></span></a>Inscription de votre application pour les notifications Push

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Accédez au dossier de projet <code>\Services\MobileServices\your_service_name</code>, ouvrez le fichier de code push.register.cs généré et examinez la méthode <b>UploadChannel</b> qui enregistre l'URL de canal d'appareil avec le concentrateur de notification.</p></li>

<li><p>Ouvrez le fichier de code App.xaml.cs partagé et notez qu'un appel vers la méthode <b>UploadChannel</b> a été ajouté au gestionnaire d'événements <b>OnLaunched</b>.</p>

    <p>Cela garantit qu'une tentative d'inscription de l'appareil est effectuée chaque fois que l'application est lancée.</p></li>

<li><p>Répétez les étapes précédentes pour ajouter des notifications Push à un projet d'application Windows Phone Store ; ensuite, dans le fichier App.xaml.cs partagé, supprimez l'appel supplémentaire vers <b>UploadChannel</b> et l'encapsuleur conditionnel <code>#if...#endif</code> restant.</p>

    <p>Les deux projets peuvent maintenant partager un même appel vers <b>UploadChannel</b>.</p>

    <div class="dev-callout"><strong>Remarque</strong> <p>Vous pouvez &eacute;galement simplifier le code g&eacute;n&eacute;r&eacute; en unifiant les d&eacute;finitions <a href="http://msdn.microsoft.com/fr-fr/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> encapsul&eacute;es dans <code data-inline="1">#if...#endif</code> en une seule d&eacute;finition encapsul&eacute;e, utilis&eacute;e pour les deux versions de l'application.</p></div></li>
</ol>

Maintenant que les notifications Push sont activées dans l'application, vous devez mettre à jour le service mobile pour les envoyer.

## <span id="update-service"></span></a>Mise à jour du service pour l'envoi de notifications Push

La procédure suivante permet de mettre à jour le script d'insertion enregistré dans la table TodoItem. Vous pouvez implémenter un code similaire dans n'importe quel script serveur ou n'importe où dans vos services principaux.

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

## <span id="test"></span></a> Test des notifications Push dans votre application

[WACOM.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel explique les règles de base de l'activation d'une application Windows Store pour utiliser Mobile Services et Notification Hubs afin d'envoyer des notifications Push. Vous pouvez ensuite envisager de consulter le didacticiel suivant, [Envoi de notifications Push aux utilisateurs authentifiés][Envoi de notifications Push aux utilisateurs authentifiés], qui explique comment utiliser des balises pour envoyer des notifications Push depuis un service mobile vers des utilisateurs authentifiés uniquement.

Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

-   [Prise en main des données][Prise en main des données]
    En savoir plus sur le stockage et l'interrogation des données à l'aide des services mobiles.

-   [Prise en main de l'authentification][Prise en main de l'authentification]
    En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents utilisant des services mobiles.

-   [Présentation de Notification Hubs][Présentation de Notification Hubs]
    Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

-   [Utilisation d'un client .NET pour Azure Mobile Services][Utilisation d'un client .NET pour Azure Mobile Services]
    En savoir plus sur l'utilisation de Mobile Services à partir d'applications C# Windows.


  [mobile-services-selector-get-started-push]: ../includes/mobile-services-selector-get-started-push.md
  [Prise en main des notifications Push dans Mobile Services]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-phone-get-started-push
  [cette version]: /fr-fr/documentation/articles/mobile-services-javscript-backend-windows-store-dotnet-get-started-push
  [Inscription de votre application pour les notifications Push]: #register
  [Mise à jour du service pour l'envoi de notifications Push]: #update-service
  [Test des notifications Push dans votre application]: #test
  [compte Microsoft Store]: http://go.microsoft.com/fwlink/p/?LinkId=280045
  [Visual Studio 2013 Express pour Windows]: http://go.microsoft.com/fwlink/?LinkId=257546
  [mobile-services-create-new-push-vs2013]: ../includes/mobile-services-create-new-push-vs2013.md
  [MobileServiceClient]: http://msdn.microsoft.com/fr-fr/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx
  [mobile-services-javascript-update-script-notification-hubs]: ../includes/mobile-services-javascript-update-script-notification-hubs.md
  [mobile-services-javascript-backend-windows-universal-test-push]: ../includes/mobile-services-javascript-backend-windows-universal-test-push.md
  [Envoi de notifications Push aux utilisateurs authentifiés]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-dotnet-push-notifications-app-users/
  [Prise en main des données]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-data
  [Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-universal-dotnet-get-started-users
  [Présentation de Notification Hubs]: /fr-fr/documentation/articles/notification-hubs-overview/
  [Utilisation d'un client .NET pour Azure Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
