<properties pageTitle="Prise en main des notifications Push à l'aide d'un service mobile principal JavaScript" metaKeywords="" description="Découvrez comment utiliser Azure Mobile Services et Notification Hubs pour envoyer des notifications Push à votre application universelle Windows." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile-services,notification-hubs" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/15/2014" ms.author="glenga" />


# Ajout de notifications Push à votre application Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Cette rubrique explique comment utiliser Azure Mobile Services avec un service principal JavaScript pour envoyer des notifications Push à une application Windows universelle. Dans ce didacticiel, vous allez activer des notifications Push à l'aide d'Azure Notification Hubs dans un projet d'application Windows universelle. Lorsque vous aurez terminé, votre service mobile enverra une notification Push depuis le service principal JavaScript vers toutes les applications Windows Store et Windows Phone Store enregistrées, chaque fois qu'un enregistrement est inséré dans la table TodoList. Le concentrateur de notification que vous créez est fourni gratuitement avec votre service mobile, peut être géré indépendamment du service mobile et peut être utilisé par d'autres applications et services.

>[WACOM.NOTE]Cette rubrique explique comment utiliser les outils dans Visual Studio 2013 Update 3 pour ajouter la prise en charge des notifications Push depuis Mobile Services vers une application Windows universelle. La même procédure peut être utilisée pour ajouter la prise en charge des notifications Push depuis Mobile Services vers une application Windows Store ou Windows Phone Store 8.1. Si vous ne pouvez pas procéder à la mise à niveau vers Visual Studio 2013 Update 3 ou préférez ajouter manuellement votre projet de service mobile à une solution d'application Windows Store, consultez [cette version](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push) de la rubrique.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1. [Inscription de votre application pour les notifications Push](#register)
2. [Mise à jour du service pour l'envoi de notifications Push](#update-service)
3. [Test des notifications Push dans votre application](#test)

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un [compte Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) actif.
* [Visual Studio 2013 Express pour Windows](http://go.microsoft.com/fwlink/?LinkId=257546) Update 3 ou une version ultérieure .

##<a id="register"></a>Inscription de votre application pour les notifications Push

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Accédez au dossier de projet <code>\services\mobileServices\scripts</code>, copiez le fichier de script &lt;<em>your_service_name</em>&gt;.push.register.js dans le dossier <code>\js</code> partagé, puis supprimez ce fichier des deux projets d'application Windows et WindowsPhone individuels.</p></li> 
<li><p>Ouvrez ce fichier de script dans le dossier de projet <code>\js</code> partagé, identifiez le code dans l'écouteur d'événements <em>activé</em> qui enregistre l'URL de canal d'appareil avec le hub de notification, puis supprimez la fonction de promesse <strong>done</strong>.</p>
<p>Ce didacticiel permet d'envoyer des notifications lorsqu'un nouvel élément est inséré, pas lorsqu'une API personnalisée est appelée.</p></li>
<li><p>Dans le projet d'application Windows, ouvrez le fichier default.html et modifiez le chemin d'accès de la référence du fichier de script vers le dossier de projet <code>\js</code>, pour qu'il se présente comme suit :</p><pre><code>&lt;script src="/js/your_service_name.push.register.js"&gt;&lt;/script&gt;</code></pre></li>
<li><p>Répétez cette étape pour le projet d'application Windows Phone.</p>
<p>Les deux projets utilisent maintenant une version partagée du script d'inscription Push.</p></li>
</ol>

Maintenant que les notifications Push sont activées dans l'application, vous devez mettre à jour le service mobile pour les envoyer. 

##<a id="update-service"></a>Mise à jour du service pour l'envoi de notifications Push

La procédure suivante permet de mettre à jour le script d'insertion enregistré dans la table TodoItem. Vous pouvez implémenter un code similaire dans n'importe quel script serveur ou n'importe où dans vos services principaux. 

[WACOM.INCLUDE [mobile-services-javascript-update-script-notification-hubs](../includes/mobile-services-javascript-update-script-notification-hubs.md)]

##<a id="test"></a> Test des notifications Push dans votre application

[WACOM.INCLUDE [mobile-services-javascript-backend-windows-universal-test-push](../includes/mobile-services-javascript-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel explique les règles de base de l'activation des applications Windows Store pour utiliser Mobile Services et Notification Hubs afin d'envoyer des notifications Push. Vous pouvez ensuite envisager de consulter le didacticiel suivant, [Envoi de notifications Push aux utilisateurs authentifiés], qui explique comment utiliser des balises pour envoyer des notifications Push depuis un service mobile vers des utilisateurs authentifiés uniquement.

Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

* [Prise en main des données]
  <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de services mobiles.

* [Prise en main de l'authentification]
  <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents à l'aide de services mobiles.

* [Présentation de Notification Hubs]
  <br/>Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

* [Débogage des applications Notification Hubs](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Instructions de dépannage et de débogage des solutions Notification Hubs. 

* [Utilisation d'un client HTML/JavaScript pour Azure Mobile Services]
  <br/>En savoir plus sur l'utilisation de Mobile Services à partir d'applications HTML et JavaScript.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Page Soumettre une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mes Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Kit de développement logiciel (SDK) Live]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-get-started
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-data
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-universal-javascript-get-started-users

[Envoi de notifications Push aux utilisateurs authentifiés]: /fr-fr/documentation/articles/mobile-services-javascript-backend-windows-store-javascript-push-notifications-app-users/

[Présentation de Notification Hubs]: /fr-fr/documentation/articles/notification-hubs-overview/

[Utilisation d'un client HTML/JavaScript pour Azure Mobile Services]: /fr-fr/documentation/articles/mobile-services-html-how-to-use-client-library
