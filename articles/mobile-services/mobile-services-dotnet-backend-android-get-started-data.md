<properties 
	pageTitle="Prise en main des données (Android) | Centre de développement mobile" 
	description="Découvrez comment utiliser Mobile Services pour tirer profit des données de votre application Android." 
	services="mobile-services" 
	documentationCenter="android" 
	authors="RickSaling" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-android" 
	ms.devlang="java" 
	ms.topic="article" 
	ms.date="05/05/2015" 
	ms.author="ricksal"/>

# Ajout de services mobiles à une application existante

[AZURE.INCLUDE [mobile-services-selector-get-started-data](../../includes/mobile-services-selector-get-started-data.md)]

Cette rubrique montre comment utiliser Azure Mobile Services en tant que source de données principale pour une application Android. Dans ce didacticiel, vous allez créer un service mobile, télécharger un projet Eclipse Android pour une application qui stocke les données en mémoire, intégrer le service mobile à l'application, puis afficher les modifications apportées aux données lors de l'exécution de l'application.

Le service mobile que vous allez créer dans ce didacticiel prendra en charge le runtime .NET dans le service mobile. Vous pourrez ainsi utiliser les langages .NET et Visual Studio pour la logique métier côté serveur dans le service mobile. Pour créer un service mobile vous permettant d'écrire votre logique métier côté serveur en JavaScript, consultez la [version du backend JavaScript](mobile-services-android-get-started-data.md) de cette rubrique.

> [AZURE.NOTE]Si vous souhaitez consulter la version Eclipse de ce didacticiel, allez à : [Prise en main des données (Eclipse)]

Pour réaliser ce didacticiel, vous avez besoin des éléments suivants :

+ <a href="https://go.microsoft.com/fwLink/p/?LinkID=391934" target="_blank">Visual Studio 2013</a> (Update 3 ou une version ultérieure). 

+ Un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](http://azure.microsoft.com/pricing/free-trial/?WT.mc_id=AE564AB28&amp;returnurl=http%3A%2F%2Fazure.microsoft.com%2Ffr-fr%2Fdocumentation%2Farticles%2Fmobile-services-dotnet-backend-android-get-started-data%2F).

##<a name="create-service"></a>Création d'un service mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/mobile-services-dotnet-backend-create-new-service.md)]

##<a name="download-the-service"></a>Téléchargement du service sur votre ordinateur local

[AZURE.INCLUDE [mobile-services-download-service-locally](../../includes/mobile-services-download-service-locally.md)]

##<a name="test-the-service"></a>Test du service mobile

[AZURE.INCLUDE [mobile-services-dotnet-backend-test-local-service](../../includes/mobile-services-dotnet-backend-test-local-service.md)]

##<a name="publish-the-service"></a>Publication du service mobile sur Azure

[AZURE.INCLUDE [mobile-services-dotnet-backend-publish-service](../../includes/mobile-services-dotnet-backend-publish-service.md)]

##<a name="download-app"></a>Téléchargement du projet GetStartedWithData

###Obtention de l'exemple de code

[AZURE.INCLUDE [mobile-services-dotnet-backend-create-new-service](../../includes/download-android-sample-code.md)]

###Vérification de la version du Kit de développement logiciel (SDK) Android

[AZURE.INCLUDE [mobile-services-verify-android-sdk-version](../../includes/mobile-services-verify-android-sdk-version.md)]


###Inspection et exécution de l'exemple de code

[AZURE.INCLUDE [mobile-services-android-run-sample-code](../../includes/mobile-services-android-run-sample-code.md)]

##<a name="update-app"></a>Mise à jour de l'application afin d'utiliser le service mobile pour l'accès aux données

[AZURE.INCLUDE [mobile-services-android-getting-started-with-data](../../includes/mobile-services-android-getting-started-with-data.md)]

##<a name="test-app"></a>Test de l'application avec le service mobile publié

Maintenant que l'application a été mise à jour pour utiliser Mobile Services pour le stockage principal, vous pouvez la tester avec Mobile Services à l'aide de l'émulateur Android ou d'un téléphone Android.

1. Dans le menu **Exécuter**, cliquez sur **Exécuter** pour démarrer le projet.

	Cela permet d'exécuter votre application, créée avec le Kit de développement logiciel (SDK) Android, qui utilise la bibliothèque cliente pour envoyer une requête renvoyant des éléments à partir de votre service mobile.

5. Comme auparavant, tapez un texte explicite, puis cliquez sur **Ajouter**.

   	Cela permet d'envoyer un nouvel élément en tant qu'insertion au service mobile.

    Vous pouvez redémarrer l'application pour vous assurer que les modifications ont été conservées dans la base de données sous Azure. Vous pouvez aussi examiner la base de données à partir du portail de gestion Azure : les deux étapes suivantes permettent de voir les modifications apportées à votre base de données.

4. Dans le portail de gestion Azure, cliquez sur Gérer pour la base de données associée à votre service mobile.

    ![](./media/mobile-services-dotnet-backend-android-get-started-data/manage-sql-azure-database.png)

5. Dans le portail de gestion, exécutez une requête pour afficher les modifications effectuées par l'application Windows Store. Votre requête sera semblable à la requête suivante mais le nom de votre base de données sera utilisé à la place de `todolist`.

        SELECT * FROM [todolist].[todoitems]

    ![](./media/mobile-services-dotnet-backend-android-get-started-data/sql-azure-query.png)

Cela conclut le didacticiel **Prise en main des données** pour Android.


## <a name="next-steps"> </a>Étapes suivantes

Ce didacticiel a présenté les bases de l'activation d'une application Android pour utiliser les données dans Mobile Services.

Ensuite, essayez l'un de ces autres didacticiels :

* [Prise en main de l'authentification] <br/>En savoir plus sur l'authentification des utilisateurs de votre application.

* [Prise en main des notifications Push] <br/>En savoir plus sur l'envoi d'une notification Push très basique sur votre application.

* [Guide de fonctionnement Mobile Services Android](mobile-services-android-how-to-use-client-library.md) <br/>En savoir plus sur l'utilisation de Mobile Services avec Android.
  
<!-- Anchors. -->

[Create a new mobile service]: #create-service
[Download the service locally]: #download-the-service-locally
[Test the mobile service]: #test-the-service
[Download the GetStartedWithData project]: #download-app
[Update the app to use the mobile service for data access]: #update-app
[Test the Android App against the service hosted locally]: #test-locally-hosted
[Publish the mobile service to Azure]: #publish-mobile-service
[Test the Android App against the service hosted in Azure]: #test-azure-hosted
[Test the app against the published mobile service]: #test-app
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Prise en main des données (Eclipse)]: mobile-services-dotnet-backend-android-get-started-data-EC.md
[Get started with Mobile Services]: mobile-services-dotnet-backend-windows-store-dotnet-get-started.md
[Prise en main de l'authentification]: mobile-services-dotnet-backend-android-get-started-users.md
[Prise en main des notifications Push]: mobile-services-dotnet-backend-android-get-started-push.md

[Azure Management Portal]: https://manage.windowsazure.com/
[Management Portal]: https://manage.windowsazure.com/
[Mobile Services SDK]: http://go.microsoft.com/fwlink/p/?LinkId=257545
[Developer Code Samples site]: http://go.microsoft.com/fwlink/p/?LinkId=328660
   

<!---HONumber=July15_HO2-->