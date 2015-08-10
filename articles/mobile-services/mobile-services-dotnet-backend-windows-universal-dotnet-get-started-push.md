<properties 
	pageTitle="Ajout de notifications Push à votre application Windows 8.1 universelle | Azure Mobile Services" 
	description="Découvrez comment utiliser Azure Notification Hubs pour envoyer des notifications Push à votre application Windows 8.1 universelle à partir de votre service mobile principal .NET." 
	services="mobile-services,notification-hubs" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="07/21/2015" 
	ms.author="glenga"/>

# Ajout de notifications push à votre application Mobile Services

[AZURE.INCLUDE [mobile-services-selector-get-started-push](../../includes/mobile-services-selector-get-started-push.md)]

##Vue d'ensemble
Cette rubrique explique comment utiliser Azure Mobile Services avec un backend .NET pour envoyer des notifications Push à une application Windows universelle. Dans ce didacticiel, vous allez activer des notifications Push à l'aide d'Azure Notification Hubs dans un projet d'application Windows universelle. Lorsque vous aurez terminé, votre service mobile enverra une notification Push depuis le backend .NET vers toutes les applications Windows Store et Windows Phone Store enregistrées, chaque fois qu'un enregistrement est inséré dans la table TodoList. Le hub de notification que vous créez est fourni gratuitement avec votre service mobile, peut être géré indépendamment du service mobile et peut être utilisé par d'autres applications et services.

>[AZURE.NOTE]Cette rubrique explique comment utiliser les outils dans Visual Studio Professional 2013 Update 3 pour ajouter la prise en charge des notifications Push depuis Mobile Services vers une application Windows universelle. La même procédure peut être utilisée pour ajouter la prise en charge des notifications Push depuis Mobile Services vers une application Windows Store ou Windows Phone Store 8.1. Pour ajouter des notifications Push à une application Windows Phone 8 ou Windows Phone Silverlight 8.1, consultez cette version de [Prise en main des notifications Push dans Mobile Services](mobile-services-dotnet-backend-windows-phone-get-started-push.md).

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

* Un [compte Microsoft Store](http://go.microsoft.com/fwlink/p/?LinkId=280045) actif.
* <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio Community 2013</a>. 

##<a id="register"></a>Inscription de votre application pour les notifications Push

[AZURE.INCLUDE [mobile-services-create-new-push-vs2013](../../includes/mobile-services-create-new-push-vs2013.md)]

&nbsp;&nbsp;6. Accédez au dossier de projet `\Services\MobileServices\your_service_name`, ouvrez le fichier de code push.register.cs généré et examinez la méthode **UploadChannel** qui enregistre l'URL de canal d'appareil avec le concentrateur de notification.
 
&nbsp;&nbsp;7. Ouvrez le fichier de code App.xaml.cs partagé et notez qu'un appel vers la méthode **UploadChannel** a été ajouté au gestionnaire d'événements **OnLaunched**. Cela garantit qu'une tentative d'inscription de l'appareil est effectuée chaque fois que l'application est lancée.

&nbsp;&nbsp;8. Répétez les étapes précédentes pour ajouter des notifications Push à un projet d'application Windows Phone Store. Ensuite, dans le fichier App.xaml.cs partagé, supprimez l'appel supplémentaire vers **UploadChannel** et l'encapsuleur conditionnel `#if...#endif` restant. Les deux projets peuvent maintenant partager un même appel vers **UploadChannel**.

> [AZURE.NOTE]Vous pouvez également simplifier le code généré en unifiant les définitions [MobileServiceClient](http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx) encapsulées dans `#if...#endif` en une seule définition non encapsulée, utilisée pour les deux versions de l'application.

Maintenant que les notifications Push sont activées dans l'application, vous devez mettre à jour le service mobile pour les envoyer.

##<a id="update-service"></a>Mise à jour du service pour l'envoi de notifications Push

La procédure suivante permet de mettre à jour la classe TodoItemController existante pour envoyer des notifications Push à tous les appareils enregistrés lorsqu'un nouvel élément est inséré. Vous pouvez implémenter un code similaire dans n'importe quel [ApiController](https://msdn.microsoft.com/library/system.web.http.apicontroller.aspx), [TableController](https://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobile.service.tables.tablecontroller.aspx), ou n'importe où dans vos services de backend.

[AZURE.INCLUDE [mobile-services-dotnet-backend-update-server-push](../../includes/mobile-services-dotnet-backend-update-server-push.md)]

##<a id="local-testing"></a> Activation des notifications Push pour les tests en local

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-local-push-vs2013](../../includes/mobile-services-dotnet-backend-configure-local-push-vs2013.md)]

Les étapes restantes de cette section sont facultatives. Elles vous permettent de tester votre application sur un service mobile qui s'exécute sur un ordinateur local. Si vous souhaitez tester les notifications Push sur le service mobile dans Azure, vous pouvez passer à la dernière section. L'assistant Ajouter une notification Push a en effet déjà configuré votre application pour se connecter à votre service lorsqu'il s'exécute dans Azure.

>[AZURE.NOTE]N'utilisez jamais de service mobile de production pour les tests et le développement. Pour les tests, publiez toujours votre projet de service mobile vers un service intermédiaire.

&nbsp;&nbsp;5. Ouvrez le fichier de projet App.xaml.cs partagé et identifiez toutes les lignes de code qui créent une instance de la classe [MobileServiceClient] pour accéder au service mobile exécuté dans Azure.

&nbsp;&nbsp;6. Placez ce code en commentaire et ajoutez du code pour créer une classe [MobileServiceClient] du même nom, mais en utilisant l'URL de l'hôte local dans le constructeur, similaire à ceci :

	// This MobileServiceClient has been configured to communicate with your local
	// test project for debugging purposes.
	public static MobileServiceClient todolistClient = new MobileServiceClient(
		"http://localhost:4584"
	);

&nbsp;&nbsp;En utilisant cette classe [MobileServiceClient], l'application se connecte au service local au lieu de la version hébergée dans Azure. Si vous souhaitez revenir à une exécution de l'application sur le service mobile hébergé dans Azure, réintégrez les définitions [MobileServiceClient] d'origine.

##<a id="test"></a> Test des notifications Push dans votre application

[AZURE.INCLUDE [mobile-services-dotnet-backend-windows-universal-test-push](../../includes/mobile-services-dotnet-backend-windows-universal-test-push.md)]

## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel explique les règles de base de l'activation d'une application Windows Store pour utiliser Mobile Services et Notification Hubs afin d'envoyer des notifications Push. Vous pouvez ensuite envisager de consulter le didacticiel suivant, [Envoi de notifications Push aux utilisateurs authentifiés], qui explique comment utiliser des balises pour envoyer des notifications Push depuis un service mobile vers des utilisateurs authentifiés uniquement.

Découvrez Mobile Services et Notification Hubs dans les rubriques suivantes :

* [Ajout de Mobile Services à une application existante][Get started with data] <br/>En savoir plus sur le stockage et l'interrogation des données à l'aide de services mobiles.

* [Ajout de l'authentification à votre application][Get started with authentication] <br/>En savoir plus sur l'authentification des utilisateurs de votre application avec des types de comptes différents utilisant des services mobiles.

* [Présentation de Notification Hubs] <br/>Découvrez comment Notification Hubs opère pour envoyer des notifications à vos applications sur toutes les principales plateformes clientes.

* [Débogage des applications Notification Hubs](http://go.microsoft.com/fwlink/p/?linkid=386630) </br>Instructions de dépannage et de débogage des solutions Notification Hubs.

* [Utilisation d'un client .NET pour Azure Mobile Services] <br/>En savoir plus sur l'utilisation de Mobile Services à partir d'applications C# Windows.

<!-- Anchors. -->

<!-- Images. -->

<!-- URLs. -->
[Submit an app page]: http://go.microsoft.com/fwlink/p/?LinkID=266582
[My Applications]: http://go.microsoft.com/fwlink/p/?LinkId=262039
[Live SDK for Windows]: http://go.microsoft.com/fwlink/p/?LinkId=262253
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Get started with data]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-data.md
[Get started with authentication]: mobile-services-dotnet-backend-windows-universal-dotnet-get-started-users.md

[Envoi de notifications Push aux utilisateurs authentifiés]: mobile-services-dotnet-backend-windows-store-dotnet-push-notifications-app-users.md

[Présentation de Notification Hubs]: ../notification-hubs-overview.md

[Utilisation d'un client .NET pour Azure Mobile Services]: mobile-services-windows-dotnet-how-to-use-client-library.md
[MobileServiceClient]: http://msdn.microsoft.com/library/azure/microsoft.windowsazure.mobileservices.mobileserviceclient.aspx

<!---HONumber=July15_HO5-->