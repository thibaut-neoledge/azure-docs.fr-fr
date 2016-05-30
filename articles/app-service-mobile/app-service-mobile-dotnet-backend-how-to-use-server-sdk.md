<properties
	pageTitle="Utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps | Azure App Service"
	description="Découvrez comment utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure App Service Mobile Apps."
	keywords="app service, azure app service, application mobile, service mobile, mise à l’échelle, évolutif, déploiement d’application, déploiement d’application azure"
	services="app-service\mobile"
	documentationCenter=""
	authors="ggailey777"
	manager="erikre"
	editor=""/>

<tags
	ms.service="app-service-mobile"
	ms.workload="mobile"
	ms.tgt_pltfrm="mobile-multiple"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="03/06/2016"
	ms.author="glenga"/>

# Utiliser le Kit de développement logiciel (SDK) de serveur principal .NET pour Azure Mobile Apps

[AZURE.INCLUDE [app-service-mobile-selector-server-sdk](../../includes/app-service-mobile-selector-server-sdk.md)]

Cette rubrique montre comment utiliser le Kit de développement logiciel (SDK) de serveur principal .NET dans les scénarios Azure App Service Mobile Apps. Le Kit de développement logiciel (SDK) Azure Mobile Apps vous permet de travailler avec des clients mobiles à partir de votre application ASP.NET.

>[AZURE.TIP] Le [Kit de développement logiciel (SDK) serveur .NET pour Azure Mobile Apps](https://github.com/Azure/azure-mobile-apps-net-server) est libre de droits sur GitHub. Le référentiel contient la suite complète de tests unitaires du Kit de développement logiciel (SDK) serveur, ainsi que quelques exemples de projets.

## Documentation de référence

La documentation de référence du Kit de développement logiciel (SDK) serveur se trouve ici : [Référence .NET Azure Mobile Apps](https://msdn.microsoft.com/library/azure/dn961176.aspx).

## <a name="create-app"></a>Créer un serveur principal .NET pour votre application mobile

Si vous démarrez un nouveau projet, vous pouvez créer une application App Service à l’aide du [portail Azure] ou de Visual Studio. Cette section vous aide à utiliser l’un de ces deux composants pour créer un nouveau serveur principal d’application mobile hébergeant une API simple de liste de tâches. Vous pouvez l’exécuter en local ou publier le projet sur votre application mobile App Service hébergée sur le cloud.

Si vous ajoutez des fonctionnalités mobiles à un projet existant, consultez la section [Télécharger et initialiser le Kit de développement logiciel](#install-sdk) ci-dessous.

### Création d’un serveur principal .NET à l’aide du portail Azure

Vous pouvez créer une nouvelle application mobile dans le [portail Azure]. Vous pouvez suivre la procédure ci-dessous, ou créer simultanément un client et un serveur en suivant le didacticiel [Créer une application mobile](app-service-mobile-ios-get-started.md).

[AZURE.INCLUDE [app-service-mobile-dotnet-backend-create-new-service](../../includes/app-service-mobile-dotnet-backend-create-new-service.md)]

&nbsp;&nbsp;9. Dans le panneau _Prise en main_, sous **Créer une table API**, sélectionnez **C#** en tant que **langue du serveur principal**.

&nbsp;&nbsp;10. Cliquez sur Télécharger, extrayez les fichiers de projets compressés sur votre ordinateur local, puis ouvrez la solution dans Visual Studio.

### Créer un serveur principal .NET à l’aide de Visual Studio 2013 et de Visual Studio 2015

Pour créer un projet Mobile Apps dans Visual Studio, il vous faudra installer le [Kit de développement logiciel (SDK) Azure pour .NET](https://azure.microsoft.com/downloads/), version 2.8.1 ou ultérieure. Une fois que vous avez installé le Kit, créez une application ASP.NET :

1. Ouvrez la boîte de dialogue **Nouveau projet** (dans le menu *Fichier* > **Nouveau** > **Projet...**).

2. Développez **Modèles** > **Visual C#**, puis sélectionnez **Web**.

3. Sélectionnez **Application web ASP.NET**.

4. Renseignez le nom du projet. Cliquez ensuite sur **OK**.

5. Sous _Modèles ASP.NET 4.5.2_, sélectionnez **Application mobile Azure**. Cochez la case **Hôte dans le cloud** afin de créer une nouvelle application mobile dans le cloud, dans laquelle vous pourrez publier ce projet.

6. Cliquez sur **OK**. Après sa création, votre application apparaît dans l’Explorateur de solutions.

## <a name="install-sdk"></a>Télécharger et initialiser le Kit de développement logiciel (SDK)

Le Kit de développement logiciel (SDK) est disponible sur [NuGet.org]. Ce package inclut les fonctionnalités de base requises pour utiliser le Kit de développement logiciel (SDK). Pour initialiser le Kit de développement logiciel (SDK), vous devez effectuer certaines actions sur l’objet **HttpConfiguration**.

###Installer le Kit de développement logiciel (SDK)

Pour installer le Kit de développement logiciel (SDK), cliquez sur le projet de serveur dans Visual Studio, sélectionnez **Gérer les packages NuGet**, recherchez le package [Microsoft.Azure.Mobile.Server](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server/), puis cliquez sur **Installer**.

###<a name="server-project-setup"></a> Initialiser le projet de serveur

Un projet de serveur principal .NET est initialisé de la même façon que les autres projets ASP.NET, en incluant une classe de démarrage OWIN. Assurez-vous que vous avez référencé le package NuGet `Microsoft.Owin.Host.SystemWeb`. Pour ajouter cette classe dans Visual Studio, cliquez avec le bouton droit sur votre projet de serveur et sélectionnez **Ajouter** > **Nouvel élément**, puis **web** > **Général** > **Classe de démarrage OWIN**.

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

Pour activer des fonctionnalités spécifiques, vous devez appeler les méthodes d’extension sur l’objet **MobileAppConfiguration** avant d’appeler **ApplyTo**. Par exemple, le code suivant ajoute les itinéraires par défaut à tous les contrôleurs d’API avec l’attribut `[MobileAppController]` lors de l’initialisation :

	new MobileAppConfiguration()
	    .MapApiControllers()
	    .ApplyTo(config);

Notez que `MapApiControllers` mappe uniquement les contrôleurs avec l’attribut `[MobileAppController]`.

La plupart des méthodes d’extension de fonctionnalité sont disponibles via des packages NuGet supplémentaires que vous pouvez inclure et qui sont décrits dans la section ci-dessous.

Le démarrage rapide du serveur à partir du portail Azure appelle **UseDefaultConfiguration()**. Cette configuration s’apparente à celle-ci :

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

- [Microsoft.Azure.Mobile.Server.Quickstart] prend en charge la configuration de base de Mobile Apps. S’ajoute à la configuration en appelant la méthode d’extension **UseDefaultConfiguration** pendant l’initialisation. Cette extension comprend les extensions suivantes : packages Notifications, Authentication, Entity, Tables, Crossdomain et Home. Cela correspond au projet de serveur de démarrage rapide que vous téléchargez à partir du portail Azure.

- [Microsoft.Azure.Mobile.Server.Home](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Home/) implémente la page par défaut *cette application mobile est opérationnelle* pour la racine du site web. S’ajoute à la configuration en appelant la méthode d’extension **AddMobileAppHomeController**.

- [Microsoft.Azure.Mobile.Server.Tables](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Tables/) Inclut des classes pour l’utilisation des données et configure le pipeline de données. S’ajoute à la configuration en appelant la méthode d’extension **AddTables**.

- [Microsoft.Azure.Mobile.Server.Entity](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Entity/) permet à Entity Framework d’accéder aux données de la base de données SQL. S’ajoute à la configuration en appelant la méthode d’extension **AddTablesWithEntityFramework**.

- [Microsoft.Azure.Mobile.Server.Authentication] Active l’authentification et configure l’intergiciel OWIN utilisé pour valider les jetons. S’ajoute à la configuration en appelant les méthodes d’extension **AddAppServiceAuthentication** et **IAppBuilder**.**UseAppServiceAuthentication**.

- [Microsoft.Azure.Mobile.Server.Notifications] active les notifications Push et définit un point de terminaison d’inscription Push. S’ajoute à la configuration en appelant la méthode d’extension **AddPushNotifications**.

- [Microsoft.Azure.Mobile.Server.CrossDomain](http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.CrossDomain/) crée un contrôleur qui fournit des données aux navigateurs web hérités à partir de votre application mobile. S’ajoute à la configuration en appelant la méthode d’extension **MapLegacyCrossDomainController**.

- [Microsoft.Azure.Mobile.Server.Login] assure une prise en charge préliminaire pour l’authentification personnalisée via la méthode MobileAppLoginHandler.CreateToken(). Il s’agit d’une méthode statique, qu’il n’est pas nécessaire d’activer dans la configuration.

## <a name="publish-server-project"></a>Publier le projet de serveur

Cette section vous explique comment publier votre projet de serveur principal .NET à partir de Visual Studio. Vous pouvez également déployer votre projet de serveur principal en utilisant l’une des autres méthodes décrites dans la [Documentation sur le déploiement d’Azure App Service](../app-service-web/web-sites-deploy.md).

1. Avec Visual Studio, développez le projet pour restaurer des packages NuGet.

2. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le projet, puis cliquez sur **Publier**. La première fois que vous publiez, vous devez définir un profil de publication. Si vous disposez déjà d’un profil défini, vous pouvez simplement le sélectionner et cliquer sur **Publier**.

2. Si vous êtes invité à sélectionner une cible de publication, cliquez sur **Microsoft Azure App Service** > **Suivant**, puis (si nécessaire) connectez-vous avec vos informations d’identification Azure. Visual Studio récupère vos paramètres de publication depuis Azure et les stocke en sécurité.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-1.png)

3. Choisissez votre **Abonnement**, sélectionnez **Type de ressource** à partir de **Affichage**, développez **Application mobile** et cliquez sur votre serveur principal Application mobile, puis cliquez sur **OK**.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-2.png)

4. Vérifiez les informations de profil de publication, puis cliquez sur **Publier**.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-wizard-3.png)

	Une fois le serveur principal d’application mobile publié, une page vous indique que l’opération a réussi.

	![](./media/app-service-mobile-dotnet-backend-how-to-use-server-sdk/publish-success.png)

##<a name="define-table-controller"></a> Définir un contrôleur de table

Un contrôleur de table permet d’accéder aux données d’entité dans un magasin de données basé sur des tables, tel que Base de données SQL ou le stockage de table Azure. Les contrôleurs de table héritent de la classe générique **TableController**, le type générique étant une entité dans le modèle qui représente le schéma de table, comme indiqué ci-après :

	public class TodoItemController : TableController<TodoItem>
    {
		//...
	}

Les contrôleurs de table s’initialisent à l’aide de la méthode d’extension **AddTables**. Cela ajoute les itinéraires sous `/tables/` pour toutes les sous-classes de `TableController`.

L’exemple suivant initialise un contrôleur de table qui utilise Entity Framework pour accéder aux données :

    new MobileAppConfiguration().AddTables(
        new MobileAppTableConfiguration()
        .MapTableControllers()
        .AddEntityFramework()).ApplyTo(config);

Pour obtenir un exemple de contrôleur de table utilisant Entity Framework pour accéder aux données à partir d’une base de données SQL Azure, consultez la classe **TodoItemController** dans le projet de serveur de démarrage rapide téléchargé depuis le portail Azure.

## Définir un contrôleur d’API personnalisé

Le contrôleur d’API personnalisé fournit les fonctionnalités de base au serveur principal de votre application mobile en exposant un point de terminaison. Vous pouvez enregistrer un contrôleur d’API mobile spécifique à l’aide de l’attribut [MobileAppController]. Cet attribut enregistre l'itinéraire et définit également le sérialiseur JSON Mobile Apps.

1. Dans Visual Studio, cliquez avec le bouton droit sur le dossier Contrôleurs, puis cliquez sur **Ajouter** > **Contrôleur**, sélectionnez **web API 2 Controller&mdash;Empty** et cliquez sur **Ajouter**.

2. Spécifiez un **nom de contrôleur**, tel que `CustomController`, puis cliquez sur **Ajouter**. Cette opération crée une classe **CustomController** qui hérite d’**ApiController**.

3. Dans le nouveau fichier de classe de contrôleur, ajoutez l’instruction using suivante :

		using Microsoft.Azure.Mobile.Server.Config;

4. Appliquez l’attribut **[MobileAppController]** à la définition de classe du contrôleur d’API, comme indiqué dans l’exemple suivant :

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

Tous les contrôleurs auxquels **MobileAppControllerAttribute** n’est pas appliqué restent accessibles aux clients, mais ils peuvent ne pas être utilisés correctement par les clients à l’aide d’un Kit de développement logiciel (SDK) client d’application mobile.

## Utiliser l’authentification

Mobile Apps utilise les fonctions d’authentification d’App Service et d’ASP.NET pour simplifier le processus d’activation de l’authentification de vos applications. Cette section vous explique comment effectuer les tâches suivantes liées à l’authentification dans votre projet de serveur principal .NET :

+ [Ajouter l’authentification à un projet de serveur](#add-auth)
+ [Utiliser l’authentification personnalisée pour votre application](#custom-auth)
+ [Récupérer des informations utilisateur authentifiées](#user-info)
+ [Limiter l’accès aux données pour les utilisateurs autorisés](#authorize)

### <a name="add-auth"></a>Ajouter l’authentification à un projet de serveur

Vous pouvez ajouter l’authentification à votre projet de serveur en étendant l’objet **MobileAppConfiguration** et en configurant l’intergiciel OWIN. Lorsque vous installez le package [Microsoft.Azure.Mobile.Server.Quickstart] et appelez la méthode d’extension **UseDefaultConfiguration**, vous pouvez passer directement à l’étape 3.

1. Dans Visual Studio, installez le package [Microsoft.Azure.Mobile.Server.Authentication].

2. Dans le fichier de projet Startup.cs, ajoutez la ligne de code suivante au début de la méthode **Configuration** :

		app.UseAppServiceAuthentication(config);

	Cette opération ajoute le composant d’intergiciel OWIN, qui permet à votre application mobile Azure de valider les jetons émis par la passerelle App Service associée.

3. Ajoutez l’attribut `[Authorize]` à tous les contrôleurs ou méthodes nécessitant une authentification. Les utilisateurs doivent maintenant s’authentifier pour accéder à ce point de terminaison ou à des API spécifiques.

Pour découvrir comment authentifier les clients auprès de votre serveur principal Mobile Apps, consultez la page [Ajouter une authentification à votre application](app-service-mobile-ios-get-started-users.md).

### <a name="custom-auth"></a>Utiliser l’authentification personnalisée pour votre application

Vous pouvez décider de fournir votre propre système de connexion si vous ne souhaitez pas utiliser l’un des fournisseurs d’authentification/d’autorisation App Service. Pour ce faire, installez le package [Microsoft.Azure.Mobile.Server.Login].

Il vous faudra fournir votre propre logique pour déterminer la nécessité de connexion d’un utilisateur. Par exemple, vous pouvez définir des mots de passe salés et hachés dans une base de données. Dans l’exemple ci-dessous, la méthode `isValidAssertion()` est responsable de ces vérifications ; elle est définie à un autre endroit.

L’authentification personnalisée est exposée via la création d’un élément ApiController et l’exposition des actions d’inscription et de connexion (voir ci-dessous). Le client peut essayer de se connecter en collectant les informations appropriées auprès de l’utilisateur et en soumettant une requête HTTPS POST à l’API, en plaçant les informations de l’utilisateur dans le corps. Une fois que le serveur a validé l’assertion, un jeton peut être émis via la méthode `AppServiceLoginHandler.CreateToken()`.

Une action de connexion peut se présenter ainsi :

		public IHttpActionResult Post([FromBody] JObject assertion)
		{
			if (isValidAssertion(assertion)) // user-defined function, checks against a database
			{
				JwtSecurityToken token = AppServiceLoginHandler.CreateToken(new Claim[] { new Claim(JwtRegisteredClaimNames.Sub, assertion["username"]) },
					mySigningKey,
					myAppURL,
					myAppURL,
					TimeSpan.FromHours(24) );
				return Ok(new LoginResult()
				{
					AuthenticationToken = token.RawData,
					User = new LoginResultUser() { UserId = userName.ToString() }
				});
			}
			else // user assertion was not valid
			{
				return this.Request.CreateUnauthorizedResponse();
			}
		}

Dans l’exemple ci-dessous, LoginResult et LoginResultUser sont de simples objets exposant les propriétés affichées. Le client attend que les réponses de connexion soient renvoyées en tant qu’objets JSON de la forme suivante :

		{
			"authenticationToken": "<token>",
			"user": {
				"userId": "<userId>"
			}
		}

La méthode `AppServiceLoginHandler.CreateToken()` inclut un paramètre _audience_ et un paramètre _émetteur_. Ces deux éléments sont généralement définis sur l’URL de la racine de votre application, à l’aide du schéma HTTPS. De la même manière, vous devez définir _secretKey_ en tant que clé de signature de votre application. Il s’agit d’une valeur sensible, qui ne doit jamais être partagée ou incluse dans un client. Hébergé dans App Service, vous pouvez obtenir cette valeur en faisant référence à la variable d’environnement _WEBSITE\_AUTH\_SIGNING\_KEY_. Si vous en avez besoin dans un contexte de débogage local, suivez les instructions de la section [Débogage local avec authentification](#local-debug) afin de récupérer la clé et de la stocker en tant que paramètre d’application.

Vous devez également fournir une durée de vie associée au jeton émis, ainsi que les revendications que vous souhaitez inclure. Il est nécessaire que vous fournissiez une revendication d’objet, tel qu’illustré dans l’exemple de code.

Vous pouvez aussi simplifier le code client de façon à utiliser la méthode `loginAsync()` (le nom peut varier d’une plateforme à une autre) à la place d’une demande HTTP POST manuelle. Vous utiliserez la surcharge qui prend un paramètre de jeton supplémentaire et qui se met en corrélation avec l’objet d’assertion faisant l’objet de la demande POST. Dans ce cas, le fournisseur doit avoir un nom personnalisé que vous lui aurez attribué. Ensuite, sur le serveur, votre action de connexion doit figurer sur le chemin _/.auth/login/{customProviderName}_ qui contient ce nom personnalisé. Pour placer votre contrôleur sur ce chemin, ajoutez un itinéraire à votre HttpConfiguration avant d’appliquer votre MobileAppConfiguration.

		config.Routes.MapHttpRoute("CustomAuth", ".auth/login/CustomAuth", new { controller = "CustomAuth" });

Remplacez la chaîne « CustomAuth » ci-dessus par le nom du contrôleur hébergeant votre action de connexion.

>[AZURE.TIP] L’utilisation de l’approche loginAsync() est l’assurance que le jeton d’authentification est joint à chaque appel supplémentaire au service.

###<a name="user-info"></a>Récupérer des informations utilisateur authentifiées

Lorsqu’un utilisateur est authentifié par App Service, vous pouvez accéder à l’ID utilisateur affecté et à d’autres informations dans votre code de serveur principal .NET. Cela est utile pour prendre des décisions d’autorisation pour un utilisateur donné dans le serveur principal, par exemple si un utilisateur spécifique peut accéder à une ligne de table ou à une autre ressource. Le code suivant indique comment obtenir l’ID utilisateur pour un utilisateur connecté :

    // Get the SID of the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;

Le SID est dérivé de l’ID utilisateur spécifique au fournisseur et est statique pour un utilisateur donné et un fournisseur de connexion.

App Service vous permet également de demander des revendications spécifiques à votre fournisseur de connexion. Cela vous permet de demander d’autres informations à partir du fournisseur, par exemple à l’aide des API Graph Facebook. Vous pouvez spécifier des revendications au niveau du panneau de fournisseur dans le portail. Certaines demandes nécessitent une configuration supplémentaire avec le fournisseur.

Le code suivant appelle la méthode d’extension **GetAppServiceIdentityAsync** pour obtenir les informations d'identification de connexion, qui incluent l'accès au jeton nécessaire pour effectuer des requêtes par rapport à l’API Graph Facebook :

    // Get the credentials for the logged-in user.
    var credentials =
        await this.User
        .GetAppServiceIdentityAsync<FacebookCredentials>(this.Request);

    if (credentials.Provider == "Facebook")
    {
        // Create a query string with the Facebook access token.
        var fbRequestUrl = "https://graph.facebook.com/me/feed?access_token="
            + credentials.AccessToken;

        // Create an HttpClient request.
        var client = new System.Net.Http.HttpClient();

        // Request the current user info from Facebook.
        var resp = await client.GetAsync(fbRequestUrl);
        resp.EnsureSuccessStatusCode();

        // Do something here with the Facebook user information.
        var fbInfo = await resp.Content.ReadAsStringAsync();
    }

Notez que vous devez ajouter une instruction using pour `System.Security.Principal` afin de faire fonctionner la méthode d’extension **GetAppServiceIdentityAsync**.

###<a name="authorize"></a>Limiter l’accès aux données pour les utilisateurs autorisés

Il est souvent nécessaire de limiter les données qui sont retournées à un utilisateur authentifié. Ce type de partitionnement des données s’effectue en ajoutant une colonne UserId à la table et en stockant le SID de l’utilisateur au moment de l’insertion des données.

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

À ce stade, vous pouvez utiliser le client Notification Hubs pour envoyer des notifications Push aux appareils inscrits. Pour plus d’informations, consultez [Ajout de notifications Push à votre application](app-service-mobile-ios-get-started-push.md). Pour plus d'informations sur ce que Notification Hubs vous permet de faire, consultez [Vue d'ensemble de Notification Hubs](../notification-hubs/notification-hubs-overview.md).

##<a name="tags"></a>Ajouter des balises à l’installation d’un appareil pour activer un push ciblé

Notification Hubs vous permet d’envoyer des notifications ciblées vers des enregistrements spécifiques à l’aide de balises. Une balise qui est créée automatiquement est l’ID d’installation, ce qui est spécifique à une instance de l’application sur un appareil donné. Une inscription avec un ID d’installation est également appelée *installation*. Vous pouvez utiliser l’ID d’installation pour gérer l’installation, par exemple pour ajouter des balises. L’ID d’installation est accessible à partir de la propriété **installationId** sur **MobileServiceClient**.

L’exemple suivant montre comment utiliser un ID d’installation pour ajouter une balise à une installation spécifique dans Notification Hubs :

	hub.PatchInstallation("my-installation-id", new[]
	{
	    new PartialUpdateOperation
	    {
	        Operation = UpdateOperationType.Add,
	        Path = "/tags",
	        Value = "{my-tag}"
	    }
	});

Notez que toutes les balises fournies par le client pendant l’inscription aux notifications Push sont ignorées par le backend pendant la création de l’installation. Pour permettre à un client d’ajouter des balises à l’installation, vous devez créer une nouvelle API personnalisée qui ajoute des balises à l’aide du modèle ci-dessus. Pour obtenir un exemple de contrôleur d’API personnalisé qui permet aux clients d’ajouter des balises à une installation, consultez [Balises de notification Push ajoutées au client](https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#client-added-push-notification-tags) dans l’exemple de démarrage rapide final d’App Service Mobile Apps pour le serveur principal .NET.

##<a name="push-user"></a>Envoyer des notifications Push à un utilisateur authentifié

Quand un utilisateur authentifié s’inscrit aux notifications Push, une balise avec l’ID d’utilisateur est automatiquement ajoutée à l’inscription. Grâce à cette balise, vous pouvez envoyer des notifications Push à tous les appareils inscrits par un utilisateur spécifique. Le code suivant permet d’obtenir le SID de l’utilisateur qui émet la demande et d’envoyer un modèle de notification Push à chaque inscription d’appareil pour cet utilisateur :

    // Get the current user SID and create a tag for the current user.
    var claimsPrincipal = this.User as ClaimsPrincipal;
    string sid = claimsPrincipal.FindFirst(ClaimTypes.NameIdentifier).Value;
    string userTag = "_UserId:" + sid;

    // Build a dictionary for the template with the item message text.
    var notification = new Dictionary<string, string> { { "message", item.Text } };

    // Send a template notification to the user ID.
    await hub.SendTemplateNotificationAsync(notification, userTag);

Quand vous vous inscrivez à des notifications Push à partir d’un client authentifié, assurez-vous au préalable que l’authentification est bien terminée. Pour plus d’informations, consultez [Envoi de notifications Push aux utilisateurs](https://github.com/Azure-Samples/app-service-mobile-dotnet-backend-quickstart/blob/master/README.md#push-to-users) dans l’exemple de démarrage rapide final d’App Service Mobile Apps pour le serveur principal .NET.

## Déboguer et dépanner le Kit de développement logiciel (SDK) serveur .NET

Azure App Service fournit plusieurs techniques de débogage et de résolution des problèmes pour les applications ASP.NET.

- [Surveiller les applications web dans Microsoft Azure App Service](../app-service-web/web-sites-monitor.md)
- [Activer la journalisation des diagnostics pour les applications web dans Azure App Service](../app-service-web/web-sites-enable-diagnostic-log.md)
- [Dépanner une application web dans le Service d’application Microsoft Azure à l’aide de Visual Studio](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md)

### Journalisation

Vous pouvez écrire dans les journaux de diagnostics App Service à l’aide du traçage ASP.NET standard. Avant de pouvoir écrire dans les journaux, vous devez activer les diagnostics de votre serveur principal d’application mobile.

Pour activer les diagnostics et écrire dans les journaux :

1. Suivez les étapes indiquées dans [Activer des diagnostics](../app-service-web/web-sites-enable-diagnostic-log.md#enablediag).

2. Ajoutez l’instruction using suivante dans votre fichier de code :

		using System.Web.Http.Tracing;

3. Créez un writer de suivi pour écrire à partir du serveur principal .NET dans les journaux de diagnostic, comme ceci :

		ITraceWriter traceWriter = this.Configuration.Services.GetTraceWriter();
		traceWriter.Info("Hello, World");

4. Publiez à nouveau votre projet de serveur et accédez au serveur principal d’application mobile pour exécuter le chemin d’accès du code avec la journalisation.

5. Téléchargez et évaluez les fichiers journaux, comme décrit dans [Télécharger des journaux](../app-service-web/web-sites-enable-diagnostic-log.md#download).

### <a name="local-debug"></a>Débogage local avec authentification

Vous pouvez exécuter localement votre application afin de tester les modifications avant de les publier dans le cloud. Pour de nombreuses applications, il vous suffit d’appuyer sur *F5* lorsque vous êtes dans Visual Studio. Toutefois, certains points spécifiques sont à prendre en compte en lien avec l’authentification.

Vous devez disposer d’une application mobile basée sur le cloud avec l’authentification/l’autorisation App Service configurées, et votre client doit posséder le point de terminaison du cloud spécifié en tant qu’hôte secondaire de connexion. Consultez la documentation associée à la plateforme cliente sélectionnée ([iOS](app-service-mobile-ios-how-to-use-client-library.md), [Windows/Xamarin](app-service-mobile-dotnet-how-to-use-client-library.md)) pour connaître la procédure appropriée.

Assurez-vous que [Microsoft.Azure.Mobile.Server.Authentication] est installé sur votre application. Ensuite, dans la classe de démarrage OWIN de votre application, ajoutez les éléments suivants, après que `MobileAppConfiguration` a été appliquée à votre `HttpConfiguration` :

		app.UseAppServiceAuthentication(new AppServiceAuthenticationOptions()
		{
			SigningKey = ConfigurationManager.AppSettings["authSigningKey"],
			ValidAudiences = new[] { ConfigurationManager.AppSettings["authAudience"] },
			ValidIssuers = new[] { ConfigurationManager.AppSettings["authIssuer"] },
			TokenHandler = config.GetMobileAppTokenHandler()
		});

Dans l’exemple ci-dessus, vous devez configurer les paramètres d’application _authAudience_ et _authIssuer_ de votre fichier web.config sur l’URL de la racine de votre application, à l’aide du schéma HTTPS. De la même manière, vous devez définir _authSigningKey_ en tant que valeur de clé de signature de votre application. Il s’agit d’une valeur sensible, qui ne doit jamais être partagée ou incluse dans un client. Pour l’obtenir, accédez à votre application dans le [portail Azure], puis cliquez sur **Outils**. Puis sélectionnez **Kudu** et cliquez sur **Accéder**. Vous atteindrez alors le point de terminaison de gestion Kudu associé à votre site. Cliquez sur **Environnement**, puis cherchez la valeur sous _WEBSITE\_AUTH\_SIGNING\_KEY_. Il s’agit de la valeur à utiliser pour _authSigningKey_ dans la configuration de votre application locale.

Votre serveur exécuté localement est désormais équipé de manière appropriée pour valider les jetons obtenus par le client à partie du point de terminaison basé sur le cloud.


[portail Azure]: https://portal.azure.com
[NuGet.org]: http://www.nuget.org/
[Microsoft.Azure.Mobile.Server.Quickstart]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Quickstart/
[Microsoft.Azure.Mobile.Server.Authentication]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Authentication/
[Microsoft.Azure.Mobile.Server.Login]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Login/
[Microsoft.Azure.Mobile.Server.Notifications]: http://www.nuget.org/packages/Microsoft.Azure.Mobile.Server.Notifications/

<!---HONumber=AcomDC_0518_2016-->