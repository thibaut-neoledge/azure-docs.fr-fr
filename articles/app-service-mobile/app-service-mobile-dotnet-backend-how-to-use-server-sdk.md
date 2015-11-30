<properties
	pageTitle="Utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps | Azure App Service"
	description="Découvrez comment utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure App Service Mobile Apps."
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777" 
	manager="dwrede"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="09/18/2015"
	ms.author="glenga"/>

# Utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps

Cette rubrique montre comment utiliser le Kit de développement logiciel (SDK) de serveur principal .NET dans les scénarios Azure App Service Mobile Apps. Le Kit de développement logiciel (SDK) Azure Mobile Apps vous permet de travailler avec des clients mobiles à partir de votre application ASP.NET.

>[AZURE.TIP]Le [Kit de développement logiciel (SDK) serveur .NET pour Azure Mobile Apps](https://github.com/Azure/azure-mobile-apps-net-server) est open source sur GitHub. Le référentiel contient la suite complète de tests unitaires du Kit de développement logiciel (SDK) serveur, ainsi que quelques exemples de projets.

## Télécharger et initialiser le Kit de développement logiciel (SDK)

Le Kit de développement logiciel (SDK) est disponible sur [NuGet.org]. Ce package inclut les fonctionnalités de base requises pour utiliser le Kit de développement logiciel (SDK). Pour initialiser le Kit de développement logiciel (SDK), vous devez effectuer certaines actions sur l’objet **HttpConfiguration**.

###Installer le Kit de développement logiciel (SDK)

Pour installer le Kit de développement logiciel (SDK), cliquez sur le projet de serveur dans Visual Studio, sélectionnez **Gérer les packages NuGet**, recherchez le package [Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/), puis cliquez sur **Installer**.

###<a name="server-project-setup"></a> Initialiser le projet de serveur

Un projet de serveur principal .NET est initialisé de la même façon que les autres projets ASP.NET, en incluant une classe de démarrage OWIN. Pour ajouter cette classe dans Visual Studio, cliquez avec le bouton droit sur votre projet de serveur et sélectionnez **Ajouter** -> **Nouvel élément**, puis **Web** -> **Général** -> **Classe de démarrage OWIN**.

Cette opération génère une classe avec l’attribut suivant :

    [assembly: OwinStartup(typeof(YourServiceName.YourStartupClassName))]

Dans la méthode `Configuration()` de votre classe de démarrage OWIN, définissez le projet de serveur avec un objet **HttpConfiguration** qui représente les options de configuration du service. L’exemple suivant initialise le projet de serveur, sans ajout de fonctionnalités :

	// in OWIN startup class
	public void Configuration(IAppBuilder app)
	{
	    HttpConfiguration config = new HttpConfiguration();
	   
	    new MobileAppConfiguration()
	        // no added features
	        .ApplyTo(config);  
	    
	    app.UseWebApi(config);
	}

Pour activer des fonctionnalités spécifiques, vous devez appeler les méthodes d’extension sur l’objet **MobileAppConfiguration** avant d’appeler **ApplyTo**. Par exemple, le code suivant ajoute les itinéraires par défaut à tous les contrôleurs d’API lors de l’initialisation :

	new MobileAppConfiguration()
	    .MapApiControllers()
	    .ApplyTo(config);

La plupart des méthodes d’extension de fonctionnalité sont disponibles via des packages NuGet supplémentaires que vous pouvez inclure et qui sont décrits dans la section ci-dessous. Le démarrage rapide du serveur à partir du portail Azure appelle **UseDefaultConfiguration()**. Cette configuration s’apparente à celle-ci :
    
		new MobileAppConfiguration()
			.AddMobileAppHomeController()             // from the Home package
			.MapApiControllers()
			.AddTables(                               // from the Tables package
				new MobileAppTableConfiguration()
					.MapTableControllers()
					.AddEntityFramework()             // from the Entity package
				)
			.AddPushNotifications()                   // from the Notifications package
			.MapLegacyCrossDomainController()         // from the CrossDomain package
			.ApplyTo(config);


### Extensions de Kit de développement logiciel (SDK)

Les packages d’extension NuGet suivants fournissent différentes fonctionnalités mobiles que votre application peut utiliser. Les extensions s’activent à l’aide de l’objet **MobileAppConfiguration** lors de l’initialisation.

- [Microsoft.Azure.Mobile.Server.Quickstart] Prend en charge la configuration de base de Mobile Apps. S’ajoute à la configuration en appelant la méthode d’extension **UseDefaultConfiguration** pendant l’initialisation. Cette extension comprend les extensions suivantes : packages Notifications, Authentication, Entity, Tables, Crossdomain et Home. Cela correspond au projet de serveur de démarrage rapide que vous téléchargez à partir du portail Azure.

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) implémente la page par défaut *cette application mobile est opérationnelle* pour la racine du site web. S’ajoute à la configuration en appelant la méthode d’extension **AddMobileAppHomeController**.

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) Inclut des classes pour l’utilisation des données et configure le pipeline de données. S’ajoute à la configuration en appelant la méthode d’extension **AddTables**.

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) Permet à Entity Framework d’accéder aux données de la base de données SQL. S’ajoute à la configuration en appelant la méthode d’extension **AddTablesWithEntityFramework**.

- [Microsoft.Azure.Mobile.Server.Authentication] Active l’authentification et configure l’intergiciel OWIN utilisé pour valider les jetons. S’ajoute à la configuration en appelant les méthodes d’extension **AddAppServiceAuthentication** et **IAppBuilder**.**UseMobileAppAuthentication**.

- [Microsoft.Azure.Mobile.Server.Notifications] Active les notifications Push et définit un point de terminaison d’inscription Push. S’ajoute à la configuration en appelant la méthode d’extension **AddPushNotifications**.

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) Crée un contrôleur qui fournit des données aux navigateurs web hérités à partir de votre application mobile. S’ajoute à la configuration en appelant la méthode d’extension **MapLegacyCrossDomainController**.

## Définir un contrôleur d’API personnalisé

Le contrôleur d’API personnalisé fournit les fonctionnalités de base au serveur principal de votre application mobile en exposant un point de terminaison. Le contrôleur d’API personnalisé

1. Dans Visual Studio, cliquez avec le bouton droit sur le dossier Contrôleurs, puis cliquez sur **Ajouter** > **Contrôleur**, sélectionnez **Web API 2 Controller&mdash;Empty** et cliquez sur **Ajouter**.

2. Spécifiez un **nom de contrôleur**, tel que `CustomController`, puis cliquez sur **Ajouter**. Cette opération crée une classe **CustomController** qui hérite d’**ApiController**.

3. Dans le nouveau fichier de classe de contrôleur, ajoutez l’instruction using suivante :

		using Microsoft.Azure.Mobile.Server.Config;

4. Appliquez **MobileAppControllerAttribute** à la définition de classe de contrôleur d’API, comme indiqué dans l’exemple suivant :

		[MobileAppController] 
		public class CustomController : ApiController
		{
		      //...
		}

4. Dans le fichier App\_Start/Startup.MobileApp.cs, ajoutez un appel à la méthode d’extension **MapApiControllers**, comme dans l’exemple suivant :

		new MobileAppConfiguration()
		    .MapApiControllers()
		    .ApplyTo(config);
    
	Notez que vous n’avez pas besoin d’appeler **MapApiControllers** si à la place vous appelez **UseDefaultConfiguration**, ce qui initialise toutes les fonctionnalités.

Tous les contrôleurs auxquels **MobileAppControllerAttribute** n’est pas appliqué restent accessibles aux clients, mais ils ne peuvent pas être utilisés correctement par les clients à l’aide d’un Kit de développement logiciel (SDK) client d’application mobile.

## Définir un contrôleur de table

Un contrôleur de table permet d’accéder aux données d’entité dans un magasin de données basé sur des tables, tel que Base de données SQL ou le stockage de table Azure. Les contrôleurs de table héritent de la classe générique **TableController**, le type générique étant une entité dans le modèle qui représente le schéma de table, comme indiqué ci-après :

	public class TodoItemController : TableController<TodoItem>
    {  
		//...
	}

Les contrôleurs de table s’initialisent à l’aide de la méthode d’extension **AddTables**. L’exemple suivant initialise un contrôleur de table qui utilise Entity Framework pour accéder aux données :

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);
 
Pour obtenir un exemple de contrôleur de table utilisant Entity Framework pour accéder aux données à partir d’une base de données SQL Azure, consultez la classe **TodoItemController** dans le projet de serveur de démarrage rapide téléchargé depuis le portail Azure.

## Ajouter l’authentification à un projet de serveur

Vous pouvez ajouter l’authentification à votre projet de serveur en étendant l’objet **MobileAppConfiguration** et en configurant l’intergiciel OWIN. Quand vous installez le package [Microsoft.Azure.Mobile.Server.Quickstart] et appelez la méthode d’extension **UseDefaultConfiguration**, vous pouvez passer directement à l’étape 3.

1. Dans Visual Studio, installez le package [Microsoft.Azure.Mobile.Server.Authentication]. 

2. Dans le fichier de projet Startup.cs, ajoutez la ligne de code suivante au début de la méthode **Configuration** :

		app.UseMobileAppAuthentication(config);

	Cette opération ajoute le composant d’intergiciel OWIN, qui permet à votre application mobile Azure de valider les jetons émis par la passerelle App Service associée.

3. Ajoutez l’attribut `[Authorize]` à tous les contrôleurs ou méthodes nécessitant l’authentification. Les utilisateurs doivent maintenant s’authentifier pour accéder à ce point de terminaison ou à ceux d’API spécifiques.

Pour découvrir comment authentifier les clients auprès de votre serveur principal Mobile Apps, consultez la page [Ajout de l’authentification à votre application](app-service-mobile-ios-get-started-users.md).

## Ajouter des notifications Push à un projet de serveur

Vous pouvez ajouter des notifications Push à votre projet de serveur en étendant l’objet **MobileAppConfiguration** et en créant un client Notification Hubs. Si vous installez le package [Microsoft.Azure.Mobile.Server.Quickstart] et appelez la méthode d’extension **UseDefaultConfiguration**, vous pouvez passer directement à l’étape 3.

1. Dans Visual Studio, cliquez avec le bouton droit sur le projet de serveur, puis cliquez sur **Gérer les packages NuGet**, recherchez Microsoft.Azure.Mobile.Server.Notifications et cliquez sur **Installer**. Cette opération installe le package [Microsoft.Azure.Mobile.Server.Notifications].
 
3. Répétez cette étape pour installer le package `Microsoft.Azure.NotificationHubs`, qui inclut la bibliothèque cliente Notification Hubs.

2. Dans le fichier App\_Start/Startup.MobileApp.cs, ajoutez un appel de la méthode d’extension **AddPushNotifications** pendant l’initialisation, ce qui se présente comme suit :

		new MobileAppConfiguration()
			// other features...
			.AddPushNotifications()
			.ApplyTo(config);

	Cela crée le point de terminaison d’inscription aux notifications Push dans votre projet de serveur. Ce point de terminaison est utilisé par les clients afin de s’inscrire au hub de notification associé. Vous devez maintenant ajouter le client Notification Hubs utilisé pour envoyer les notifications.

3. Dans un contrôleur à partir duquel vous souhaitez envoyer des notifications Push, ajoutez le code suivant à l’aide de l’instruction using suivante :

		using System.Collections.Generic;
		using Microsoft.Azure.NotificationHubs;

4. Ajoutez le code suivant pour créer un client Notification Hubs :

        // Get the settings for the server project.
        HttpConfiguration config = this.Configuration;
        MobileAppSettingsDictionary settings = 
            config.GetMobileAppSettingsProvider().GetMobileAppSettings();
        
        // Get the Notification Hubs credentials for the Mobile App.
        string notificationHubName = settings.NotificationHubName;
        string notificationHubConnection = settings
            .Connections[MobileAppSettingsKeys.NotificationHubConnectionString].ConnectionString;

        // Create a new Notification Hub client.
        NotificationHubClient hub = NotificationHubClient
        .CreateClientFromConnectionString(notificationHubConnection, notificationHubName);

À ce stade, vous pouvez utiliser le client Notification Hubs pour envoyer des notifications Push aux appareils inscrits. Pour plus d’informations, consultez [Ajout de notifications push à votre application](app-service-mobile-ios-get-started-push.md). Pour plus d’informations sur ce que Notification Hubs vous permet de faire, consultez [Vue d’ensemble de Notification Hubs](../notification-hubs/notification-hubs-overview.md).

## Ajouter des balises à l’installation d’un périphérique pour l’envoi de données aux balises

Après avoir suivi la **procédure de définition d’un contrôleur d’APi personnalisé**, vous souhaiterez configurer une API personnalisée sur votre serveur principal pour travailler avec Notification Hubs afin d’ajouter des balises à une installation de périphérique spécifique. Assurez-vous de transmettre l’ID d’installation enregistré sur l’espace de stockage local client et les balises que vous souhaitez ajouter (facultatif, étant donné que vous pouvez également spécifier des balises directement sur votre serveur principal). L’extrait de code suivant doit être ajouté à votre contrôleur pour utiliser Notification Hubs afin d’ajouter une balise à un ID d’installation de périphérique.

Utilisation de [Nuget – Azure Notification Hubs](https://www.nuget.org/packages/Microsoft.Azure.NotificationHubs/)([référence](https://msdn.microsoft.com/library/azure/mt414893.aspx)) :

		var hub = NotificationHubClient.CreateClientFromConnectionString("my-connection-string", "my-hub");

		hub.PatchInstallation("my-installation-id", new[]
		{
		    new PartialUpdateOperation
		    {
		        Operation = UpdateOperationType.Add,
		        Path = "/tags",
		        Value = "{my-tag}"
		    }
		});
	

Pour envoyer des notifications Push à ces balises, utilisez les [API Notification Hubs](https://msdn.microsoft.com/library/azure/dn495101.aspx).

Vous pouvez également utiliser votre API personnalisé pour enregistrer les installations de périphériques avec Notification Hubs directement sur votre serveur principal.

## Publier le projet de serveur

Pour publier votre projet de serveur vers Azure, procédez comme suit :

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-publish-service](../../includes/app-service-mobile-dotnet-backend-publish-service.md)]


[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

<!---HONumber=Nov15_HO4-->