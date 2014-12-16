<properties pageTitle="Prise en main des notification Push à l'aide d'un service mobile principal .NET" metaKeywords="" description="Learn how to use Azure Mobile Services and Notification Hubs to send push notifications to your universal Windows app." metaCanonical="" services="mobile-services,notification-hubs" documentationCenter="Mobile" title="Get started with push notifications in Mobile Services" authors="glenga" solutions="mobile" manager="dwrede" editor="" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="09/27/2014" ms.author="glenga" />

# Ajout de notifications Push à votre application Mobile Services

[WACOM.INCLUDE [mobile-services-selector-get-started-push](../includes/mobile-services-selector-get-started-push.md)]

Cette rubrique explique comment utiliser Azure Mobile Services avec un service principal .NET pour envoyer des notifications Push à une application Windows universelle. Dans ce didacticiel, vous allez activer des notifications Push à l'aide d'Azure Notification Hubs dans un projet d'application Windows universelle. Lorsque vous aurez terminé, votre service mobile enverra une notification Push depuis le service principal .NET vers toutes les applications Windows Store et Windows Phone Store enregistrées, chaque fois qu'un enregistrement est inséré dans la table TodoList. Le concentrateur de notification que vous créez est fourni gratuitement avec votre service mobile, peut être géré indépendamment du service mobile et peut être utilisé par d'autres applications et services.

>[WACOM.NOTE]Cette rubrique explique comment utiliser les outils dans Visual Studio Professional 2013 Update 3 pour ajouter la prise en charge des notifications Push depuis Mobile Services vers une application Windows universelle. La même procédure peut être utilisée pour ajouter la prise en charge des notifications Push depuis Mobile Services vers une application Windows Store ou Windows Phone Store 8.1. Pour ajouter des notifications Push à une application Windows Phone 8 ou Windows Phone Silverlight 8.1, consultez cette version de [Prise en main des notifications Push dans Mobile Services](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-phone-get-started-push).

> Si vous ne pouvez pas procéder à la mise à niveau vers Visual Studio Professional 2013 Update 3 ou préférez ajouter manuellement votre projet de service mobile à une solution d'application du Windows Store, consultez [cette version](/fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started-push) de la rubrique.

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
<li><p>Accédez au dossier de projet <code>\Services\MobileServices\your_service_name</code>, ouvrez le fichier de code push.register.cs généré et examinez la méthode <strong>UploadChannel</strong> qui enregistre l'URL de canal d'appareil avec le concentrateur de notification.</p></li> 
<li><p>Ouvrez le fichier de code App.xaml.cs partagé et notez qu'un appel à la méthode <strong>UploadChannel</strong> a été ajouté au gestionnaire d'événements <strong>OnLaunched</strong>.</p> <p>Cela garantit qu'une tentative d'inscription de l'appareil est effectuée chaque fois que l'application est lancée.</p></li>
<li><p>Répétez les étapes précédentes pour ajouter des notifications Push à un projet d'application Windows Phone Store. Dans le fichier App.xaml.cs partagé, supprimez ensuite l'appel supplémentaire à <strong>UploadChannel</strong> et l'encapsuleur conditionnel <code>#if...#endif</code> restant.</p> <p>Les deux projets peuvent maintenant partager un même appel à <strong>UploadChannel</strong>.</p>
<div class="dev-callout"><strong>Remarque</strong> <p>Vous pouvez également simplifier le code généré en unifiant les définitions <code>#if...#endif</code> encapsulées dans <a href="http://msdn.microsoft.com/fr-fr/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> en une seule définition encapsulée  utilisée pour les deux versions de l'application.</p></div></li>
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
<li><p>Ouvrez le fichier de projet App.xaml.cs partagé et identifiez toutes les lignes de code qui créent une instance de la classe <a href="http://msdn.microsoft.com/fr-fr/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> pour accéder au service mobile exécuté dans Azure.</p></li>
<li><p>Placez ce code en commentaire et ajoutez du code pour créer une classe <a href="http://msdn.microsoft.com/fr-fr/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> du même nom, mais en utilisant l'URL de l'hôte local dans le constructeur, similaire à ceci :</p>
<pre><code>// This MobileServiceClient has been configured to communicate with your local
// test project for debugging purposes.
public static MobileServiceClient todolistClient = new MobileServiceClient(
	"http://localhost:4584"
);
</code></pre><p>En utilisant cette classe <a href="http://msdn.microsoft.com/fr-fr/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a>, l'application se connecte au service local plutôt qu'à la version hébergée dans Azure. Si vous voulez revenir à une exécution de l'application sur le service mobile hébergé dans Azure, réintégrez les définitions <a href="http://msdn.microsoft.com/fr-fr/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx">MobileServiceClient</a> d'origine.</p></li>
</ol>

##<a id="test"></a> Test des notifications Push dans votre application

[WACOM.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel explique les règles de base de l'activation d'une application Windows Store pour utiliser Mobile Services et Notification Hubs afin d'envoyer des notifications Push. Vous pouvez ensuite envisager de consulter le didacticiel suivant, [Envoi de notifications Push aux utilisateurs authentifiés], qui explique comment utiliser des balises pour envoyer des notifications Push depuis un service mobile vers des utilisateurs authentifiés uniquement.

Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

* [Prise en main des données]
  <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide des services mobiles.

* [Prise en main de l'authentification]
  <br/>Découvrez comment authentifier les utilisateurs de votre application avec différents types de comptes à l'aide des services mobiles.

* [Présentation de Notification Hubs]
  <br/>Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

* [Débogage des applications de Notification Hubs](http://go.microsoft.com/fwlink/p/?linkid=386630)
  </br>Obtention de conseils de dépannage et débogage des solutions Notification Hubs. 

* [Utilisation d'un client .NET pour Azure Mobile Services]
  <br/>Découvrez plus en détail comment utiliser Mobile Services à partir d'applications Windows C#.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Page Soumettre une application]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[Mes Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Kit de développement logiciel (SDK) Live pour Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Prise en main de Mobile Services]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-get-started
[Prise en main des données]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data
[Prise en main de l'authentification]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users

[Envoi de notifications Push aux utilisateurs authentifiés]: /fr-fr/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users/

[Présentation de Notification Hubs]: /fr-fr/documentation/articles/notification-hubs-overview/

[Utilisation d'un client .NET pour Azure Mobile Services]: /fr-fr/documentation/articles/mobile-services-windows-dotnet-how-to-use-client-library/
