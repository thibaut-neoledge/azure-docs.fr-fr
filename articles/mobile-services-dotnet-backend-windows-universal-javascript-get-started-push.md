<properties pageTitle="Prise en main des notification Push à l'aide d'un service mobile principal .NET" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="javascript" ms.topic="article" ms.date="09/27/2014" ms.author="glenga" />


# Ajout de notifications Push à votre application Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Cette rubrique explique comment utiliser Azure Mobile Services avec un service principal .NET pour envoyer des notifications Push à une application Windows universelle. Dans ce didacticiel, vous allez activer des notifications Push à l'aide d'Azure Notification Hubs dans un projet d'application Windows universelle. Lorsque vous aurez terminé, votre service mobile enverra une notification Push depuis le service principal .NET vers toutes les applications Windows Store et Windows Phone Store enregistrées, chaque fois qu'un enregistrement est inséré dans la table TodoList. Le concentrateur de notification que vous créez est fourni gratuitement avec votre service mobile, peut être géré indépendamment du service mobile et peut être utilisé par d'autres applications et services.

>[WACOM.NOTE]Cette rubrique explique comment utiliser les outils dans Visual Studio Professional 2013 Update 3 pour ajouter la prise en charge des notifications Push depuis Mobile Services vers une application Windows universelle. La même procédure peut être utilisée pour ajouter la prise en charge des notifications Push depuis Mobile Services vers une application Windows Store ou Windows Phone Store 8.1. Si vous ne pouvez pas procéder à la mise à niveau vers Visual Studio Professional 2013 Update 3 ou préférez ajouter manuellement votre projet de service mobile à une solution d'application du Windows Store, consultez [cette version](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started-push) de la rubrique.

Ce didacticiel vous familiarise avec les étapes de base permettant d'activer les notifications Push :

1. [Inscription de votre application pour les notifications Push](#register)
2. [Mise à jour du service pour l'envoi de notifications Push](#update-service)
3. [Activation des notifications Push pour les tests en local](#local-testing)
4. [Test des notifications Push dans votre application](#test)

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un compte Microsoft Store [actif](http://go.microsoft.com/fwlink/p/?LinkId=280045).
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Professional 2013</a> (Update 3 ou une version ultérieure). <br/>Une version d'évaluation gratuite est disponible. 

##<a id="register"></a>Inscription de votre application pour les notifications Push

[WACOM.INCLUDE [mobile-services-create-new-push-vs2013](../includes/mobile-services-create-new-push-vs2013.md)]

<ol start="6">
<li><p>Accédez au dossier de projet <code>\services\mobileServices\scripts</code>, copiez le fichier de script <<em>nom_de_votre_service</em>>.push.register.js dans le dossier <code>\js</code> partagé, puis supprimez ce fichier des deux projets d'application Windows et Windows Phone individuels.</p></li> 
<li><p>Ouvrez ce fichier de script dans le dossier de projet <code>\js</code> partagé, identifiez le code dans l'écouteur d'événements <em>activé</em> qui enregistre l'URL de canal d'appareil avec le hub de notification, puis supprimez la fonction de promesse <strong>done</strong>.</p>
<p>Ce didacticiel permet d'envoyer des notifications lorsqu'un nouvel élément est inséré, pas lorsqu'une API personnalisée est appelée.</p></li>
<li><p>Dans le projet d'application Windows, ouvrez le fichier default.html et modifiez le chemin d'accès de la référence du fichier de script vers le dossier de projet <code>\js</code> partagé, pour qu'il se présente comme suit :</p><pre><code><script src="/js/your_service_name.push.register.js"></script></code></pre></li>
<li><p>Répétez cette étape pour le projet d'application Windows Phone.</p>
<p>Les deux projets utilisent maintenant une version partagée du script d'inscription Push.</p></li>
</ol>

Maintenant que les notifications Push sont activées dans l'application, vous devez mettre à jour le service mobile pour les envoyer. 

##<a id="update-service"></a>Mise à jour du service pour l'envoi de notifications Push

La procédure suivante permet de mettre à jour la classe TodoItemController existante pour envoyer des notifications Push à tous les appareils enregistrés lorsqu'un nouvel élément est inséré. Vous pouvez implémenter un code similaire dans n'importe quel [ApiController], [TableController], ou n'importe où dans vos services principaux. 

[WACOM.INCLUDE [mobile-services-dotnet-backend-update-server-push](../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Activation des notifications Push pour les tests en local

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

Les étapes restantes de cette section sont facultatives. Elles vous permettent de tester votre application sur un service mobile qui s'exécute sur un ordinateur local. Si vous souhaitez tester les notifications Push sur le service mobile dans Azure, vous pouvez passer à la dernière section. L'Assistant Ajout d'une notification Push a en effet déjà configuré votre application pour se connecter à votre service quand il s'exécute dans Azure.  

>[WACOM.NOTE]N'utilisez jamais de service mobile de production pour les tests et le développement. Pour les tests, publiez toujours votre projet de service mobile sur un service intermédiaire.

<ol start="5">
<li><p>Accédez au dossier de projet <code>\services\mobileServices\settings</code>, copiez le fichier de script <<em>nom_de_votre_service</em>>.js script dans le dossier <code>\js</code> partagé, puis supprimez ce fichier des deux projets d'application Windows et Windows Phone individuels. Supprimez également ce fichier du dossier <code>\services\mobileServices\scripts</code> dans chaque projet d'application, s'il s'y trouve également.</p></li> 
<li><p>Ouvrez ce fichier de script dans le dossier de projet <code>\js</code> partagé et placez en commentaire le code existant qui définit l'<a href="http://msdn.microsoft.com/fr-fr/library/azure/jj554219.aspx">objet MobileServiceClient</a> utilisé pour accéder au service mobile exécuté dans Azure.</p></li>
<li><p>Ajoutez une nouvelle définition d'objet <strong>MobileServiceClient</strong> avec le même nom, mais en utilisant l'URL de l'hôte local dans le constructeur, comme suit :</p>
<pre><code>// This MobileServiceClient has been configured to communicate with your local
// test project for debugging purposes.
var todolistClient = new WindowsAzure.MobileServiceClient(
	"http://localhost:4584");
</code></pre><p>En utilisant cette classe <strong>MobileServiceClient</strong>, l'application se connecte au service local plutôt qu'à la version hébergée dans Azure. Si vous voulez revenir à une exécution de l'application sur le service mobile hébergé dans Azure, réintégrez les définitions d'objet <strong>MobileServiceClient</strong> d'origine.</p></li>
</ol>

##<a id="test"></a> Test des notifications Push dans votre application

[WACOM.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel explique les règles de base de l'activation des applications du Windows Store pour utiliser Mobile Services et Notification Hubs afin d'envoyer des notifications Push. Vous pouvez ensuite envisager de consulter le didacticiel suivant, [Envoi de notifications Push aux utilisateurs authentifiés], qui explique comment utiliser des balises pour envoyer des notifications Push depuis un service mobile vers des utilisateurs authentifiés uniquement.

Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

* [Prise en main des données]
  <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide des services mobiles.

* [Prise en main de l'authentification]
  <br/>Découvrez comment authentifier les utilisateurs de votre application avec différents types de comptes à l'aide des services mobiles.

* [Présentation de Notification Hubs]
  <br/>Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

* [Débogage des applications de Notification Hubs](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Obtention de conseils de dépannage et débogage des solutions Notification Hubs. 

* [Utilisation d'un client HTML/JavaScript pour Azure Mobile Services]
  <br/>Obtenez plus d'informations sur l'utilisation de Mobile Services depuis des applications HTML et JavaScript.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Page Soumettre une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mes Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Kit de développement logiciel (SDK) Live pour Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-get-started
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-data
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-universal-javascript-get-started-users

[Envoi de notifications Push aux utilisateurs authentifiés]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-javascript-push-notifications-app-users/

[Présentation de Notification Hubs]: /fr-fr/documentation/articles/notification-hubs-overview/

[Utilisation d'un client HTML/JavaScript pour Azure Mobile Services]: /fr-fr/documentation/articles/mobile-services-html-how-to-use-client-library
