<properties
	pageTitle="Prise en main d’API Apps et d’ASP.NET dans Azure App Service | Microsoft Azure"
	description="Découvrez comment créer, déployer et consommer une application API ASP.NET dans Azure App Service avec Visual Studio 2015."
	services="app-service\api"
	documentationCenter=".net"
	authors="tdykstra"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="app-service-api"
	ms.workload="na"
	ms.tgt_pltfrm="dotnet"
	ms.devlang="na"
	ms.topic="hero-article"
	ms.date="11/25/2015"
	ms.author="tdykstra"/>

# Prise en main d’API Apps et d’ASP.NET dans Azure App Service

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Vue d’ensemble

Dans ce didacticiel, vous allez créer une [application API App Service](app-service-api-apps-why-best-platform.md), déployer une API Web ASP.NET dans l’application API et consommer l’application API à partir d’un client MVC ASP.NET. Ce didacticiel part du principe que vous connaissez ASP.NET mais que vous n’avez aucune expérience préalable avec Microsoft Azure. À la fin de ce didacticiel, vous disposerez d’une API Web et d’une application cliente qui fonctionneront dans le cloud.

L’exemple d’application est une liste de contacts simple. L’illustration suivante montre comment l’application MVC affiche les données reçues à partir de l’API.

![](./media/app-service-api-dotnet-get-started/mvccontacts.png)

## Ce que vous allez apprendre

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

* Préparer votre ordinateur au développement Azure en installant le Kit de développement logiciel (SDK) Azure pour .NET
* Utiliser des applications API et des applications web dans Azure App Service à l’aide des outils intégrés à Visual Studio 2015
* Automatiser la découverte d’API en utilisant le package Swashbuckle NuGet pour générer de manière dynamique le fichier JSON de définition d’API Swagger
* Utiliser du code client généré automatiquement pour consommer une application API à partir d’un client .NET
* Utiliser le portail Azure pour configurer le point de terminaison pour les métadonnées d’application API

Il s’agit du premier d’une série de didacticiels. Les didacticiels suivants reposent sur ce que vous créez dans celui-ci. Vous y découvrirez comment effectuer les tâches suivantes :
 
* Utiliser CORS pour appeler une application API à partir d’un client JavaScript quand celui-ci appartient à un domaine différent de celui de l’API
* Utiliser Azure Active Directory pour protéger une API contre tout accès non authentifié
* Consommer une API protégée pour les utilisateurs connectés à Azure Active Directory
* Consommer une API protégée à l’aide d’un principal du service

## Conditions préalables

Ce didacticiel part du principe que vous connaissez API Web ASP.NET. Si vous avez besoin d’une introduction, consultez [Prise en main d’API Web ASP.NET 2](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/tutorial-your-first-web-api).

Les instructions et les captures d’écran partent du principe que vous utilisez Visual Studio 2015, mais elles devraient aussi fonctionner pour Visual Studio 2013.

Pour suivre ce didacticiel, vous avez besoin d’un compte Azure. Vous pouvez :

* [Ouvrir un compte Azure gratuitement](/pricing/free-trial/?WT.mc_id=A261C142F). Vous obtenez des crédits que vous pouvez utiliser pour essayer des services Azure payants. Même si vous n’avez plus de crédits, vous pouvez conserver le compte et utiliser les services et fonctionnalités Azure gratuits, comme la fonction Web Apps dans Azure App Service.
* [Activer les avantages abonnés Visual Studio](/pricing/member-offers/msdn-benefits-details/?WT.mc_id=A261C142F). Votre abonnement MSDN vous donne droit chaque mois à des crédits dont vous pouvez vous servir pour les services Azure payants.

Si vous souhaitez commencer à utiliser Azure App Service avant d’ouvrir un compte Azure, accédez à [Essayer App Service](http://go.microsoft.com/fwlink/?LinkId=523751). De là, vous pouvez créer tout de suite une première application temporaire dans App Service. Aucune carte de crédit ni aucun engagement ne sont nécessaires.

[AZURE.INCLUDE [install-sdk-2015-2013](../../includes/install-sdk-2015-2013.md)]

Ce didacticiel nécessite la version 2.8.1 ou ultérieure du Kit de développement logiciel (SDK) Azure pour .NET.

## Vue d’ensemble de l’exemple d’application

Le code que vous allez déployer dans une application API et une application web pour ce didacticiel se trouve dans le dépôt GitHub [Azure-Samples/app-service-api-dotnet-contact-list](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list). La solution ContactsList Visual Studio comprend les projets suivants :

* **ContactsList.API** : projet d’API Web ASP.NET qui retourne une liste de noms et d’adresses de messagerie. L’appel initial à la méthode Get retourne trois contacts codés en dur, puis les appels suivants aux méthodes Put, Post et Delete enregistrent les modifications dans un fichier JSON local.
* **ContactsList.MVC** : client MVC ASP.NET pour l’API ContactsList.
* **ContactsList.Angular** : client d’interface utilisateur AngularJS simple pour l’API ContactsList. Montre comment appeler une application API non protégée (aucune authentification).
* **ContactsList.Angular.AAD** : client AngularJS qui montre comment utiliser Azure Active Directory pour authentifier les utilisateurs.
* **CompanyContacts.API** : projet d’API Web ASP.NET qui retourne une liste codée en dur de contacts en réponse à une demande Get. Appelé par la méthode Get de **ContactsList.API** pour illustrer comment appeler une API à l’aide de l’authentification de service à service (principal du service).
 
## Téléchargement de l’exemple d’application 

1. Téléchargez le dépôt [Azure-Samples/app-service-api-dotnet-contact-list](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list).

	Vous pouvez [télécharger un fichier .zip](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list/archive/master.zip) ou cloner le dépôt sur votre ordinateur local.

2. Ouvrez la solution ContactsList dans Visual Studio 2015 ou 2013.

2. Générez la solution pour restaurer les packages NuGet.

## Utiliser Swashbuckle et Swagger lors de l’exécution locale

[Swagger](http://swagger.io/) est un format JSON pour décrire les méthodes et les réponses d’une API. La prise en charge de Swagger 2.0 est intégrée à Azure App Service. Comme vous le verrez plus loin dans ce didacticiel, si vous fournissez des métadonnées Swagger pour une API, Visual Studio peut générer du code client qui facilite la consommation de l’API.

Pour fournir des métadonnées Swagger 2.0 pour un projet d’API Web ASP.NET, vous pouvez installer le package NuGet [Swashbuckle](https://www.nuget.org/packages/Swashbuckle). Swashbuckle utilise la réflexion pour générer des métadonnées de manière dynamique. Le package NuGet est déjà installé dans le projet ContactsList.API que vous avez téléchargé. Il est aussi déjà installé quand vous créez un projet à l’aide du modèle de projet **Application API Azure**.

Dans cette section du didacticiel, nous allons examiner les métadonnées Swagger 2.0 générées, puis essayer une interface utilisateur test basée sur les métadonnées Swagger.

2. Définissez le projet ContactsList.API comme projet de démarrage. (Cliquez avec le bouton droit sur le projet dans l’**Explorateur de solutions**, puis cliquez sur **Définir comme projet de démarrage** dans le menu contextuel.)
 
4. Appuyez sur F5 pour exécuter le projet en mode débogage.

	Le navigateur s’ouvre et affiche la page 403 Interdit.

	![](./media/app-service-api-dotnet-get-started/403.png)

12. Dans la barre d’adresses de votre navigateur, ajoutez `swagger/docs/v1` à la fin de la ligne, puis appuyez sur Retour. (L’URL est `http://localhost:51864/swagger/docs/v1`.)

	Il s’agit de l’URL par défaut utilisée par Swashbuckle pour retourner les métadonnées JSON Swagger 2.0 pour l’API. Si vous utilisez Internet Explorer, le navigateur vous invite à télécharger un fichier v1.json.

	![](./media/app-service-api-dotnet-get-started/iev1json.png)

	Si vous utilisez Chrome, le navigateur affiche le fichier JSON dans la fenêtre du navigateur.

	![](./media/app-service-api-dotnet-get-started/chromev1json.png)

	L’exemple suivant montre la première section de métadonnées Swagger de l’API, avec la définition de la méthode Get. Ce sont ces métadonnées qui pilotent l’interface utilisateur Swagger que vous utiliserez lors des étapes suivantes, et vous les utiliserez dans une section ultérieure de ce didacticiel pour générer automatiquement le code client.

		{
		  "swagger": "2.0",
		  "info": {
		    "version": "v1",
		    "title": "ContactsList.API"
		  },
		  "host": "localhost:51864",
		  "schemes": [ "http" ],
		  "paths": {
		    "/api/Contacts": {
		      "get": {
		        "tags": [ "Contacts" ],
		        "operationId": "Contacts_Get",
		        "consumes": [ ],
		        "produces": [ "application/json", "text/json", "application/xml", "text/xml" ],
		        "responses": {
		          "200": {
		            "description": "OK",
		            "schema": {
		              "type": "array",
		              "items": { "$ref": "#/definitions/Contact" }
		            }
		          }
		        },
		        "deprecated": false
		      },

1. Fermez le navigateur.

3. Dans le projet ContactsList.API, dans l’**Explorateur de solutions**, ouvrez le fichier *app\_start\\swaggerconfig.cs*, puis faites défiler la page jusqu’au code suivant et supprimez les marques de commentaire.

		/*
		    })
		.EnableSwaggerUi(c =>
		    {
		*/

	Le fichier SwaggerConfig.cs est créé quand vous installez le package Swashbuckle dans un projet. Ce fichier permet de configurer Swashbuckle de différentes manières.

	Le code dont vous avez supprimé les marques de commentaire active l’interface utilisateur Swagger que vous utiliserez lors des étapes suivantes. Quand vous créez un projet d’API Web à l’aide du modèle de projet Application API, ce code est commenté par défaut par mesure de sécurité.

5. Réexécutez le projet.

3. Dans la barre d’adresses de votre navigateur, ajoutez `swagger` à la fin de la ligne, puis appuyez sur Retour. (L’URL est `http://localhost:51864/swagger`.)

4. Quand l’interface utilisateur Swagger apparaît, cliquez sur **Contacts** pour voir les méthodes disponibles.

	![](./media/app-service-api-dotnet-get-started/contactsmethods.png)

5. Cliquez sur **Get > Try it out**.

	L’interface utilisateur Swagger appelle la méthode Get de ContactsList et affiche les résultats JSON.

	![](./media/app-service-api-dotnet-get-started/gettryitout.png)

6. Cliquez sur **Post**, puis cochez la case sous **Model Schema**.

	Quand vous cliquez sur le schéma de modèle, la zone de texte est préremplie automatiquement. Vous pouvez y spécifier la valeur du paramètre de la méthode Post.

	![](./media/app-service-api-dotnet-get-started/post.png)

7. Modifiez le fichier JSON dans la zone d’entrée de paramètre `contact` pour qu’il ressemble à l’exemple suivant, ou insérez vos propres nom et adresse électronique :

		{
		  "CreatedBy": "",
		  "EmailAddress": "carson@contoso.com",
		  "Id": 4,
		  "Name": "Alexander Carson"
		} 

10. Cliquez sur **Try it out**.

	L’API ContactsList retourne HTTP 200 et un corps de réponse qui confirme ce qui a été ajouté.

11. Cliquez sur **Get > Try it out**.

	La réponse de la méthode Get inclut désormais le nouveau contact.

12. Essayez également les méthodes Put, Delete et Get by ID, puis fermez le navigateur.

Swashbuckle fonctionne avec n’importe quel projet d’API Web ASP.NET. Si vous souhaitez ajouter la génération de métadonnées Swagger à un projet existant, il suffit d’installer le package Swashbuckle. Si vous souhaitez créer un projet à déployer comme application API App Service, utilisez le modèle de projet **Application API Azure** ASP.NET. Ce modèle crée un projet d’API Web avec Swashbuckle installé.

![](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

## Créer une application API dans Azure et y déployer le projet ContactsList.API

Dans cette section, vous allez utiliser les outils Azure intégrés à l’Assistant **Publier le site web** de Visual Studio pour créer une application API dans Azure. Ensuite, vous déploierez le projet ContactsList.API vers la nouvelle application API et vous appellerez l’API en réexécutant l’interface utilisateur Swagger, cette fois-ci pendant son exécution dans le cloud.

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet ContactsList.API, puis cliquez sur **Publier**.

3.  À l’étape **Profil** de l’Assistant **Publier le site web**, cliquez sur **Microsoft Azure App Service**.

	![](./media/app-service-api-dotnet-get-started/selectappservice.png)

4. Connectez-vous à votre compte Azure si ce n’est déjà fait, ou actualisez vos informations d’identification si elles ont expiré.

4. Dans la boîte de dialogue App Service, choisissez l’**Abonnement** Azure que vous souhaitez utiliser, puis cliquez sur **Nouveau**.

	![](./media/app-service-api-dotnet-get-started/clicknew.png)

3. Sous l’onglet **Hébergement** de la boîte de dialogue **Créer App Service**, cliquez sur **Modifier le type**, puis sur **Application API**.

4. Entrez un **Nom de l’application API** unique dans le domaine *azurewebsites.net*.

	Visual Studio propose un nom unique en ajoutant une chaîne de date-heure au nom du projet. Vous pouvez accepter ce nom si vous le souhaitez.

	Si vous entrez un nom qu’une autre personne a déjà utilisé, un point d’exclamation rouge s’affiche à droite au lieu d’une coche verte, et vous devez entrer un autre nom.

	Azure utilise ce nom comme préfixe de l’URL de votre application. L’URL complète se compose de ce nom plus *. azurewebsites.net*. Par exemple, si le nom du site est `ContactsListAPI`, l’URL sera `contactslistapi.azurewebsites.net`.

6. Dans la liste déroulante **Groupe de ressources**, entrez « ContactsListGroup » ou un autre nom si vous préférez.

	Cette zone vous permet de sélectionner un [groupe de ressources](../azure-preview-portal-using-resource-groups.md) existant ou d’en créer un en tapant un nom différent des groupes de ressources existants de votre abonnement.

	Pour ce didacticiel, il est préférable de créer un groupe car cela facilitera la suppression en une étape de toutes les ressources Azure que vous créez pour le didacticiel.

4. Cliquez sur le bouton **Nouveau** situé en regard de la liste déroulante **Plan App Service**.

	![](./media/app-service-api-dotnet-get-started/createas.png)

	Pour plus d’informations sur les plans App Service, consultez [Présentation des plans App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. Dans la boîte de dialogue **Configurer le plan App Service**, entrez « ContactsListPlan » ou un autre nom si vous préférez.

5. Dans la liste déroulante **Emplacement**, sélectionnez le lieu le plus proche de vous.

	Ce paramètre indique le centre de données Azure dans lequel votre application sera exécutée. Pour les besoins de ce didacticiel, vous pouvez sélectionner n’importe quelle région : la différence ne sera pas sensible. Toutefois, pour une application de production, votre serveur doit être le plus proche possible des clients qui y accèdent, afin de minimiser la [latence](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Dans la liste déroulante **Taille**, cliquez sur **Gratuit**.

	Pour ce didacticiel, le niveau tarifaire Gratuit fournit des performances suffisantes.

6. Dans la boîte de dialogue **Configurer le plan App Service**, cliquez sur **OK**.

	![](./media/app-service-api-dotnet-get-started/configasp.png)

7. Dans la boîte de dialogue **Créer App Service**, cliquez sur **Créer**.

	Visual Studio crée l’application API et crée un profil de publication qui comporte tous les paramètres nécessaires pour la nouvelle application API. Lors des étapes suivantes, vous utiliserez le nouveau profil de publication pour déployer le projet.
 
	Remarque : Il existe d’autres façons de créer des applications API dans Azure App Service. Dans Visual Studio, les mêmes boîtes de dialogue sont disponibles pendant que vous créez un projet. Vous pouvez aussi créer des applications API à l’aide du portail Azure, des [applets de commande Azure pour Windows PowerShell](../powershell-install-configure.md) ou de l’[interface de ligne de commande multiplateforme](../xplat-cli.md).

8. Sous l’onglet **Connexion** de l’Assistant **Publier le site web**, cliquez sur **Publier**.

	![](./media/app-service-api-dotnet-get-started/clickpublish.png)

	Visual Studio déploie le projet ContactsList.API vers la nouvelle application API et ouvre un navigateur à l’URL de l’application API. Une page signalant que la création a réussi s’affiche dans le navigateur.

	![](./media/app-service-api-dotnet-get-started/appcreated.png)

11. Ajoutez « swagger » à l’URL dans la barre d’adresses du navigateur, puis appuyez sur Entrée. (L’URL est `http://{apiappname}.azurewebsites.net/swagger`.)

	Le navigateur affiche la même interface utilisateur Swagger que précédemment, mais elle s’exécute désormais dans le cloud. Essayez la méthode Get. Vous constatez que les trois contacts par défaut sont affichés, car les modifications apportées précédemment ont été enregistrées dans un fichier local. Les modifications apportées seront désormais enregistrées dans le système de fichiers de l’application API Azure.

12. Ouvrez le [portail Azure](https://portal.azure.com/).
 
14. Cliquez sur **Parcourir > API Apps > {votre\_nouvelle\_application\_API}**.

	![](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

16. Cliquez sur **Paramètres** puis, dans le panneau **Paramètres**, recherchez la section API et cliquez sur **Définition de l’API**.

	![](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

	Le panneau Définition de l’API vous permet de spécifier l’URL qui retourne les métadonnées Swagger 2.0 au format JSON. Quand Visual Studio crée l’application API, il affecte la valeur par défaut que vous avez vue précédemment comme URL de définition d’API, à savoir l’URL de base de l’application API plus `/swagger/docs/v1`.

	![](./media/app-service-api-dotnet-get-started/apidefurl.png)

	Quand vous sélectionnez une application API pour laquelle générer du code, Visual Studio extrait les métadonnées à partir de cette URL.

## <a id="codegen"></a> Consommer à partir de .NET à l’aide de code client généré 

L’un des avantages de l’intégration de Swagger dans les applications API Azure est la génération de code automatique. Les classes de client générées facilitent l’écriture du code qui appelle une application API.

Dans cette section, vous allez découvrir comment consommer une application API à partir d’une application web MVC ASP.NET. Vous exécuterez d’abord le client MVC et API Web localement, puis vous déploierez le client vers une nouvelle application web dans Azure App Service et vous l’exécuterez dans le cloud.

### Générer du code client

Vous pouvez générer du code client pour une application API à l’aide de Visual Studio ou à partir de la ligne de commande. Dans ce didacticiel, vous allez utiliser Visual Studio. Pour plus d’informations sur la façon de procéder à partir de la ligne de commande, consultez le fichier Lisez-moi du dépôt [Azure/autorest](https://github.com/azure/autorest) sur GitHub.com.

Le projet ContactsList.MVC contient déjà le code client généré, mais vous allez le supprimer et le régénérer pour observer comment cela fonctionne et pour faire de l’URL de votre propre application API l’URL cible par défaut.

1. Dans l’**Explorateur de solutions** de Visual Studio, dans le projet ContactsList.MVC, supprimez le dossier *ContactsList.API*.

	![](./media/app-service-api-dotnet-get-started/deletecodegen.png)

2. Cliquez avec le bouton droit sur le projet ContactsList.MVC, puis cliquez sur **Ajouter > Client API REST**.

	![](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. Dans la boîte de dialogue **Ajouter un client API REST**, cliquez sur **Télécharger depuis l’application API Microsoft Azure**, puis sur **Parcourir**.

	![](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

8. Dans la boîte de dialogue **App Service**, développez le groupe de ressources **ContactsListGroup** et sélectionnez votre application API, puis cliquez sur **OK**.

	Cette boîte de dialogue offre plusieurs manières d’organiser les applications API dans la liste, au cas où il y en aurait trop à faire défiler. Elle vous permet également d’entrer une chaîne de recherche pour filtrer les applications API par nom.

	![](./media/app-service-api-dotnet-get-started/codegenselect.png)

	Notez que lorsque vous revenez à la boîte de dialogue **Client API REST**, la zone de texte a été remplie avec la valeur de l’URL de définition d’API que vous avez vue précédemment dans le portail.

	![](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

	En guise d’alternative, vous pouvez entrer l’URL directement au lieu de passer par la boîte de dialogue. Par exemple, si vous avez déployé votre API vers une application web et qu’elle ne figure pas dans la boîte de dialogue, vous pouvez entrer manuellement l’URL qui retourne les métadonnées Swagger ici.

	Notez également la présence de l’option **Sélectionner un fichier de métadonnées Swagger existant**. Si vous souhaitez générer le code avant le déploiement vers Azure, vous pouvez exécuter localement, télécharger le fichier JSON Swagger et le sélectionner ici.

9. Dans la boîte de dialogue **Ajouter un client API REST**, cliquez sur **OK**.

	Visual Studio crée un dossier nommé d’après l’application API et il génère des classes de client.

	![](./media/app-service-api-dotnet-get-started/codegenfiles.png)

5. Ouvrez *Controllers\\ContactsController.cs* pour afficher le code qui appelle l’API à l’aide du client généré.

	L’extrait de code suivant montre comment instancier l’objet client et appeler la méthode Get.

		private ContactsListAPI db = new ContactsListAPI(new Uri("http://localhost:51864"));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

	Ce code passe l’URL IIS Express locale du projet d’API au constructeur de classe de client pour que vous puissiez exécuter le projet web MVC et le projet d’API localement. Si vous omettez le paramètre de constructeur, le point de terminaison par défaut est l’URL à partir de laquelle vous avez généré le code.

6. Votre classe de client est générée avec un nom différent selon le nom de votre application API. Modifiez ce code pour que le nom de type corresponde à ce qui a été généré dans votre projet.

7. Générez la solution.

Le contrôleur et les vues du projet MVC ont une apparence semblable au contrôleur et aux vues qui ont été générés automatiquement pour Entity Framework, car c’est comme cela qu’ils ont été créés : en générant automatiquement un modèle de données Entity Framework, puis en apportant des modifications mineures pour utiliser l’API REST plutôt qu’un contexte de base de données Entity Framework.

### Exécution locale

1. Définissez les projets ContactsList.API et ContactsList.MVC comme projets de démarrage, ContactsList.API démarrant avant ContactsList.MVC. (Cliquez avec le bouton droit sur la solution, cliquez sur **Propriétés**, sur **Plusieurs projets de démarrage**, puis sélectionnez **Démarrer** pour chaque projet. Utilisez les icônes de flèches Haut/Bas pour vous assurer que ContactsList.API figure tout en haut de la liste.) 

2. Appuyez sur F5 pour démarrer les projets.

	Un navigateur affiche la page 403 pour l’API et un autre affiche la page d’accueil de l’application MVC.

3. Cliquez sur **Contacts** dans la barre de menus du navigateur qui affiche la page d’accueil de l’application MVC.

	L’interface utilisateur MVC affiche les contacts stockés localement, et vous pouvez l’utiliser pour ajouter et supprimer des contacts.

	![](./media/app-service-api-dotnet-get-started/mvccontacts.png)

### Créer une application web dans Azure et y déployer le projet ContactsList.MVC

Dans cette section, vous allez créer une application web et déployer un projet web vers l’application web Azure en appliquant la même méthode que lorsque vous avez créé une application API.

#### Modifier le projet MVC pour qu’il pointe vers l’application API Azure 

Avant de déployer vers Azure, modifiez le point de terminaison d’API dans le projet MVC pour que, lorsque le code est déployé, il appelle l’application API Azure que vous avez créée précédemment plutôt que localhost.

1. Dans le projet ContactsList.MVC, ouvrez *Controllers\\ContactsController.cs*.

2. Commentez la ligne qui définit l’URL localhost comme URL de base de l’API et supprimez les marques de commentaire de la ligne qui n’a aucun constructeur de paramètre. Le code ressemble maintenant à l’exemple suivant, sauf que les deux lignes doivent mentionner un nom de classe qui reflète le nom de votre application API à partir de laquelle vous avez généré le code.

		private ContactsListAPI db = new ContactsListAPI();
		//private ContactsListAPI db = new ContactsListAPI(new Uri("http://localhost:51864"));

	L’URL cible par défaut est votre application API Azure, car vous avez généré le code à partir de cet emplacement. Si vous avez appliqué une autre méthode pour générer le code, vous devrez peut-être spécifier l’URL de l’application API Azure de la même façon que l’URL locale.

#### Créer une application web pour héberger le site MVC

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet ContactsList.MVC, puis cliquez sur **Publier**.

2. Dans l’Assistant **Publier le site web**, cliquez sur l’onglet **Profil**.

3.  Sous l’onglet **Profil** de l’Assistant **Publier le site web**, cliquez sur **Microsoft Azure App Service**.

5. Dans la boîte de dialogue **App Service**, cliquez sur **Nouveau**.

3. Sous l’onglet **Hébergement** de la boîte de dialogue **Créer App Service**, cliquez sur **Modifier le type**, puis assurez-vous que le type soit **Application web**.

4. Entrez un **Nom de l’application web** unique dans le domaine *azurewebsites.net*.

5. Choisissez l’**Abonnement** Azure souhaité.

6. Dans la liste déroulante **Groupe de ressources**, choisissez le groupe de ressources créé précédemment.

4. Dans la liste déroulante **Plan App Service**, choisissez le plan créé précédemment.

7. Cliquez sur **Créer**.

	Visual Studio crée l’application web, crée un profil de publication pour elle et affiche l’étape **Connexion** de l’Assistant **Publier le site web**.

### Déployer le projet ContactsList.Web vers la nouvelle application web

3.  À l’étape **Connexion** de l’Assistant **Publier le site web**, cliquez sur **Publier**.

	Visual Studio déploie le projet ContactsList.MVC vers la nouvelle application web et ouvre un navigateur à l’URL de l’application web. L’interface utilisateur MVC qui s’exécutait localement apparaît, mais elle affiche maintenant les contacts qui sont stockés dans le système de fichiers de l’application API Azure.

	![](./media/app-service-api-dotnet-get-started/codegencontacts.png)

## Étapes suivantes

Dans ce didacticiel, vous avez vu comment créer des applications API, y déployer du code et les consommer à partir de clients .NET. Le didacticiel suivant de cette série de prise en main montre comment [consommer des applications API à partir de clients JavaScript à l’aide de CORS](app-service-api-cors-consume-javascript.md).

<!---HONumber=AcomDC_1203_2015-->