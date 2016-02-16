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
	ms.date="02/05/2016"
	ms.author="tdykstra"/>

# Prise en main d’API Apps et d’ASP.NET dans Azure App Service

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Vue d'ensemble

Ce premier didacticiel de la série explique comment utiliser les fonctionnalités d’Azure App Service pour le développement et l’hébergement d’API :

* la prise en charge intégrée des métadonnées de l’API
* la prise en charge de CORS
* la prise en charge de l’authentification et de l’autorisation

Vous allez déployer un exemple d’application sur deux [applications API](app-service-api-apps-why-best-platform.md) et une application web dans Azure App Service. Comme illustré dans le schéma, l’exemple d’application se présente sous la forme d’une liste de tâches contenant un composant frontal d’application monopage (SPA) AngularJS, une couche intermédiaire d’API web ASP.NET et une couche de données d’API web ASP.NET.

![](./media/app-service-api-dotnet-get-started/noauthdiagram.png)

Voici une capture d’écran du composant frontal SPA.

![](./media/app-service-api-dotnet-get-started/todospa.png)

À la fin de ce didacticiel, vous obtiendrez deux API web capables de fonctionner dans les applications API App Service. Lorsque vous aurez terminé le didacticiel suivant, l’application s’exécutera entièrement dans le cloud, avec votre application SPA intégrée dans une application web App Service. Les autres didacticiels vous permettront d’ajouter des composants d’authentification et d’autorisation.

## Ce que vous allez apprendre

Ce didacticiel vous apprendra à effectuer les opérations suivantes :

* Préparer votre ordinateur au développement Azure en installant le Kit de développement logiciel (SDK) Azure pour .NET
* Utiliser des applications API et des applications web dans Azure App Service à l’aide des outils intégrés à Visual Studio 2015
* Automatiser la découverte d’API en utilisant le package Swashbuckle NuGet pour générer de manière dynamique le fichier JSON de définition d’API Swagger
* Utiliser du code client généré automatiquement pour consommer une application API à partir d’un client .NET
* Utiliser le portail Azure pour configurer le point de terminaison pour les métadonnées d’application API

## Composants requis

[AZURE.INCLUDE [configuration requise](../../includes/app-service-api-dotnet-get-started-prereqs.md)]

[AZURE.INCLUDE [set-up-dev-environment](../../includes/install-sdk-2015-2013.md)]

Ce didacticiel requiert la version 2.8.2 ou ultérieure du Kit de développement logiciel (SDK) Azure pour .NET.

## Téléchargement de l'exemple d'application 

1. Téléchargez le référentiel [Azure-Samples/app-service-api-dotnet-contact-list](https://github.com/Azure-Samples/app-service-api-dotnet-todo-list).

	Vous pouvez cliquer sur le bouton **Télécharger le fichier .zip** ou cloner le référentiel sur votre ordinateur local.

2. Ouvrez la solution ToDoList dans Visual Studio 2015 ou 2013.

	La solution Visual Studio est un exemple d’application qui fonctionne avec des éléments de tâche simples comprenant une description et un propriétaire.

		public class ToDoItem 
		{ 
		    public int ID { get; set; } 
		    public string Description { get; set; } 
		    public string Owner { get; set; } 
		} 
 
	La solution inclut trois projets :

	![](./media/app-service-api-dotnet-get-started/projectsinse.png)

	* **ToDoListAngular** (composant frontal) : une application monopage AngularJS qui appelle la couche intermédiaire. 

	* **ToDoListAPI** (couche intermédiaire) : un projet API web ASP.NET qui appelle la couche de données pour effectuer des opérations CRUD sur les éléments de tâche.

	* **ToDoListAPI** (couche de données) : un projet API web ASP.NET qui exécute les opérations CRUD sur les éléments de tâche. Les éléments de tâche sont stockés en mémoire, ce qui signifie que toutes les modifications sont perdues à chaque redémarrage de l’application.

	La couche intermédiaire renseigne l’ID d’utilisateur dans le champ `Owner` au moment de l’appel de la couche de données. Dans le code que vous téléchargez, l’ID d’utilisateur est toujours « * ». Lorsque vous ajouterez l’authentification dans les autres didacticiels, la couche intermédiaire fournira l’ID d’utilisateur à la couche de données.

2. Générez la solution pour restaurer les packages NuGet.

### Facultatif : exécuter l’application localement

Dans cette section, vous allez vérifier que vous pouvez exécuter le client localement et que vous pouvez appeler l’API pendant qu’elle s’exécute localement.

**Remarque :** ces instructions s’appliquent aux navigateurs Internet Explorer et Edge car ils prennent en charge les appels JavaScript cross-origin depuis et vers les URL `http://localhost`. Si vous utilisez Chrome, démarrez le navigateur avec le bouton `--disable-web-security`. Si vous utilisez Firefox, ignorez cette section.

1. Définissez les trois projets comme projets de démarrage, en commençant par ToDoListDataAPI, suivi de ToDoListAPI et de ToDoListAngular. (Cliquez sur la solution avec le bouton droit de la souris, puis cliquez sur **Propriétés**, sélectionnez **Plusieurs projets de démarrage**, placez les projets dans l’ordre approprié et, pour chaque projet, définissez **Action** sur **Démarrer**.)  

2. Appuyez sur F5 pour démarrer les projets.

	Deux fenêtres du navigateur s’ouvrent sur des pages d’erreur HTTP 403 ; il s’agit d’un comportement normal pour les projets d’API web. La troisième fenêtre du navigateur affiche l’interface utilisateur AngularJS.

3. Dans la fenêtre qui affiche l’interface utilisateur AngularJS, cliquez sur l’onglet **Liste des tâches**.

	L’interface utilisateur affiche par défaut deux éléments d’action.

	![](./media/app-service-api-dotnet-get-started/todospa.png)

4. Ajoutez, modifiez et supprimez des éléments d’action pour voir comment fonctionne l’application.

	Toutes les modifications que vous apportez sont stockées en mémoire et sont perdues lors du redémarrage de l’application.

3. Fermez les fenêtres du navigateur.

## Utiliser l’interface utilisateur et les métadonnées Swagger

La prise en charge des métadonnées d’API [Swagger](http://swagger.io/) 2.0 est intégrée aux applications API App Service. Chaque application API spécifie un point de terminaison d’URL qui renvoie à l’API des métadonnées au format JSON Swagger. Les métadonnées retournées à partir de ce point de terminaison peuvent être utilisées pour générer le code client.

Le package NuGet [Swashbuckle](https://www.nuget.org/packages/Swashbuckle) fournit des métadonnées Swagger 2.0 pour un projet d’API web ASP.NET. Swashbuckle utilise la réflexion pour générer des métadonnées de manière dynamique.

Le package NuGet Swashbuckle est déjà installé dans les projets ToDoListDataAPI et ToDoListAPI que vous avez téléchargés. C’est le cas également lorsque vous créez un projet à l’aide du modèle de projet **Application API Azure**. (Dans Visual Studio : **Fichier > Nouveau > projet > Application ASP.NET Web > Application API Azure**.)

Dans cette section du didacticiel, nous allons examiner les métadonnées Swagger 2.0 générées, puis essayer une interface utilisateur test basée sur les métadonnées Swagger.

2. Définissez le projet ToDoListDataAPI comme projet de démarrage. 
 
4. Appuyez sur F5 pour exécuter le projet en mode débogage.

	Le navigateur s’ouvre et affiche la page d’erreur HTTP 403.

12. Dans la barre d’adresses de votre navigateur, ajoutez `swagger/docs/v1` à la fin de la ligne, puis appuyez sur Retour. (L’URL est `http://localhost:45914/swagger/docs/v1`.)

	Il s’agit de l’URL par défaut utilisée par Swashbuckle pour retourner les métadonnées JSON Swagger 2.0 pour l’API. Si vous utilisez Internet Explorer, le navigateur vous invite à télécharger un fichier *v1.json*.

	![](./media/app-service-api-dotnet-get-started/iev1json.png)

	Si vous utilisez Chrome, Firefox ou Edge, le navigateur affiche le fichier JSON dans la fenêtre du navigateur.

	![](./media/app-service-api-dotnet-get-started/chromev1json.png)

	L’exemple suivant montre la première section de métadonnées Swagger de l’API, avec la définition de la méthode Get. Ce sont ces métadonnées qui pilotent l’interface utilisateur Swagger que vous utiliserez lors des étapes suivantes, et vous les utiliserez dans une section ultérieure de ce didacticiel pour générer automatiquement le code client.

		{
		  "swagger": "2.0",
		  "info": {
		    "version": "v1",
		    "title": "ToDoListDataAPI"
		  },
		  "host": "localhost:45914",
		  "schemes": [ "http" ],
		  "paths": {
		    "/api/ToDoList": {
		      "get": {
		        "tags": [ "ToDoList" ],
		        "operationId": "ToDoList_GetByOwner",
		        "consumes": [ ],
		        "produces": [ "application/json", "text/json", "application/xml", "text/xml" ],
		        "parameters": [
		          {
		            "name": "owner",
		            "in": "query",
		            "required": true,
		            "type": "string"
		          }
		        ],
		        "responses": {
		          "200": {
		            "description": "OK",
		            "schema": {
		              "type": "array",
		              "items": { "$ref": "#/definitions/ToDoItem" }
		            }
		          }
		        },
		        "deprecated": false
		      },

1. Fermez le navigateur.

3. Dans le projet ToDoListDataAPI, dans l’**Explorateur de solutions**, ouvrez le fichier *App\_Start\\SwaggerConfig.cs*, puis faites défiler la page jusqu’au code suivant et supprimez les marques de commentaire.

		/*
		    })
		.EnableSwaggerUi(c =>
		    {
		*/

	Le fichier *SwaggerConfig.cs* est créé quand vous installez le package Swashbuckle dans un projet. Ce fichier permet de configurer Swashbuckle de différentes manières.

	Le code dont vous avez supprimé les marques de commentaire active l’interface utilisateur Swagger que vous utiliserez lors des étapes suivantes. Lorsque vous créez un projet d’API Web à l’aide du modèle de projet Application API, ce code est commenté par défaut par mesure de sécurité.

5. Réexécutez le projet.

3. Dans la barre d’adresses de votre navigateur, ajoutez `swagger` à la fin de la ligne, puis appuyez sur Retour. (L’URL est `http://localhost:45914/swagger`.)

4. Quand l’interface utilisateur Swagger apparaît, cliquez sur **ToDoList** pour voir les méthodes disponibles.

	![](./media/app-service-api-dotnet-get-started/methods.png)

5. Cliquez sur **Obtenir**.

6. Entrez un astérisque comme valeur du paramètre `owner`, puis cliquez sur **Essayer**.

	![](./media/app-service-api-dotnet-get-started/gettryitout1.png)

	L’interface utilisateur Swagger appelle la méthode Get de TodoList et affiche les résultats JSON.

	![](./media/app-service-api-dotnet-get-started/gettryitout.png)

6. Cliquez sur **Post**, puis cochez la case sous **Model Schema**.

	Quand vous cliquez sur le schéma de modèle, la zone de texte est préremplie automatiquement. Vous pouvez y spécifier la valeur du paramètre de la méthode Post. (Si cela ne fonctionne pas dans Internet Explorer, utilisez un autre navigateur, ou entrez la valeur du paramètre manuellement à l’étape suivante.)

	![](./media/app-service-api-dotnet-get-started/post.png)

7. Modifiez le fichier JSON dans la zone d’entrée de paramètre `contact` pour obtenir un résultat similaire à l’exemple suivant, ou insérez votre propre texte descriptif :

		{
		  "ID": 2,
		  "Description": "buy the dog a toy",
		  "Owner": "*"
		}

10. Cliquez sur **Try it out**.

	L’API ToDoList retourne un code de réponse HTTP 204 indiquant que le processus a réussi.

11. Cliquez sur **Get > Try it out**.

	La réponse de la méthode Get inclut désormais le nouvel élément de tâche.

12. Essayez également les méthodes Put, Delete et Get by ID, puis fermez le navigateur.

Swashbuckle fonctionne avec n’importe quel projet d’API Web ASP.NET. Si vous souhaitez ajouter la génération de métadonnées Swagger à un projet existant, il suffit d’installer le package Swashbuckle. Pour créer un projet, utilisez le modèle de projet **Application API Azure** ASP.NET, comme le montre l’illustration qui suit. Ce modèle crée un projet d’API Web avec Swashbuckle installé.

![](./media/app-service-api-dotnet-get-started/apiapptemplate.png)

**Remarque :** par défaut, Swashbuckle peut générer des ID d’opération Swagger en double pour vos méthodes de contrôleur. Cela se produit lorsque les méthodes HTTP du contrôleur sont surchargées, par exemple : `Get()` et `Get(id)`. Pour plus d’informations sur la gestion des surcharges, consultez [Personnaliser les définitions d’API générées par Swashbuckle](app-service-api-dotnet-swashbuckle-customize.md). Si vous créez un projet d’API web dans Visual Studio en utilisant le modèle d’application API Azure, le code qui génère les ID d’opération uniques est automatiquement ajouté au fichier *SwaggerConfig.cs*.

## Créer une application API dans Azure et y déployer le projet ToDoListAPI

Dans cette section, vous allez utiliser les outils Azure intégrés à l’Assistant **Publier le site web** de Visual Studio pour créer une application API dans Azure. Ensuite, vous déploierez le projet ToDoListDataAPI vers la nouvelle application API et vous appellerez l’API en réexécutant l’interface utilisateur Swagger, cette fois-ci pendant son exécution dans le cloud.

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet ToDoListDataAPI, puis cliquez sur **Publier**.

3.  À l’étape **Profil** de l’Assistant **Publier le site web**, cliquez sur **Microsoft Azure App Service**.

	![](./media/app-service-api-dotnet-get-started/selectappservice.png)

4. Connectez-vous à votre compte Azure si ce n’est déjà fait, ou actualisez vos informations d’identification si elles ont expiré.

4. Dans la boîte de dialogue App Service, choisissez l’**Abonnement** Azure que vous souhaitez utiliser, puis cliquez sur **Nouveau**.

	![](./media/app-service-api-dotnet-get-started/clicknew.png)

3. Sous l’onglet **Hébergement** de la boîte de dialogue **Créer App Service**, cliquez sur **Modifier le type**, puis sur **Application API**.

	![](./media/app-service-api-dotnet-get-started/apptype.png)

	Le fait de définir le type en tant qu’**Application API** ne permet pas de déterminer les fonctionnalités qui seront disponibles pour la nouvelle application. L’URL de définition d’API (que vous verrez plus tard dans ce didacticiel), la prise en charge de CORS (qui sera abordée dans le didacticiel suivant) et l’authentification (couverte dans les 3 derniers didacticiels de cette série) sont disponibles pour les applications web, les applications mobiles ainsi que les applications API. La création d’une application en tant qu’application API a uniquement les effets suivants :

	a. Dans le portail Azure, l’icône ou le texte de type d’application s’affiche dans les titres de panneau et dans les listes d’applications. Dans le panneau **Paramètres**, la section API apparaît plus haut dans la liste dans le cas d’une application API par rapport aux autres types d’application.

	b. Dans Visual Studio avec le SDK Azure pour .NET 2.8.1, Visual Studio configure l’URL de définition d’API uniquement lors de la création d’une application API ASP.NET (les autres types d’application ne sont pas concernés).

4. Entrez un **Nom d’application API** unique dans le domaine *azurewebsites.net* ; par exemple, ajoutez un nombre à ToDoListDataAPI pour le rendre unique.

	Visual Studio propose un nom unique en ajoutant une chaîne date-heure au nom du projet. Vous pouvez accepter ce nom si vous le souhaitez.

	Si vous entrez un nom qu’une autre personne a déjà utilisé, un point d’exclamation rouge s’affiche à droite au lieu d’une coche verte, et vous devez entrer un autre nom.

	Azure utilise ce nom comme préfixe de l’URL de votre application. L’URL complète se compose de ce nom plus *. azurewebsites.net*. Par exemple, si le nom du site est `ToDoListDataAPI`, l’URL sera `todolistdataapi.azurewebsites.net`.

6. Dans la liste déroulante **Groupe de ressources**, entrez « ToDoListGroup » ou un autre nom si vous préférez.

	Cette zone vous permet de sélectionner un [groupe de ressources](../azure-preview-portal-using-resource-groups.md) existant ou d’en créer un en tapant un nom différent des groupes de ressources existants de votre abonnement.

	Pour ce didacticiel, il est préférable de créer un groupe car cela facilitera la suppression en une étape de toutes les ressources Azure que vous créez pour le didacticiel.

4. Cliquez sur le bouton **Nouveau** situé en regard de la liste déroulante **Plan App Service**.

	![](./media/app-service-api-dotnet-get-started/createas.png)

	Pour plus d’informations sur les plans App Service, consultez [Présentation des plans App Service](../app-service/azure-web-sites-web-hosting-plans-in-depth-overview.md).

5. Dans la boîte de dialogue **Configurer le plan App Service**, entrez « ToDoListPlan » ou un autre nom si vous préférez.

5. Dans la liste déroulante **Emplacement**, sélectionnez le lieu le plus proche de vous.

	Ce paramètre indique le centre de données Azure dans lequel votre application sera exécutée. Pour les besoins de ce didacticiel, vous pouvez sélectionner n’importe quelle région : la différence ne sera pas sensible. Toutefois, pour une application de production, votre serveur doit être le plus proche possible des clients qui y accèdent, afin de minimiser la [latence](http://www.bing.com/search?q=web%20latency%20introduction&qs=n&form=QBRE&pq=web%20latency%20introduction&sc=1-24&sp=-1&sk=&cvid=eefff99dfc864d25a75a83740f1e0090).

5. Dans la liste déroulante **Taille**, cliquez sur **Gratuit**.

	Pour ce didacticiel, le niveau tarifaire Gratuit fournit des performances suffisantes.

6. Dans la boîte de dialogue **Configurer le plan App Service**, cliquez sur **OK**.

	![](./media/app-service-api-dotnet-get-started/configasp.png)

7. Dans la boîte de dialogue **Créer App Service**, cliquez sur **Créer**.

	Visual Studio crée l’application API et crée un profil de publication qui comporte tous les paramètres nécessaires pour la nouvelle application API. Lors des étapes suivantes, vous utiliserez le nouveau profil de publication pour déployer le projet.
 
	**Remarque :** Il existe d’autres façons de créer des applications API dans Azure App Service. Dans Visual Studio, les mêmes boîtes de dialogue sont disponibles pendant que vous créez un projet. Vous pouvez aussi créer des applications API à l’aide du portail Azure, des [applets de commande Azure pour Windows PowerShell](../powershell-install-configure.md) ou de l’[interface de ligne de commande multiplateforme](../xplat-cli.md).

8. Sous l’onglet **Connexion** de l’Assistant **Publier le site web**, cliquez sur **Publier**.

	![](./media/app-service-api-dotnet-get-started/clickpublish.png)

	Visual Studio déploie le projet ToDoListDataAPI vers la nouvelle application API et ouvre un navigateur à l’URL de l’application API. Une page signalant que la création a réussi s’affiche dans le navigateur.

	![](./media/app-service-api-dotnet-get-started/appcreated.png)

11. Ajoutez « swagger » à l’URL dans la barre d’adresses du navigateur, puis appuyez sur Entrée. (L’URL est `http://{apiappname}.azurewebsites.net/swagger`.)

	Le navigateur affiche la même interface utilisateur Swagger que précédemment, mais elle s’exécute désormais dans le cloud. En essayant la méthode Get, vous voyez que vous revenez aux 2 éléments de tâche par défaut, étant donné que les modifications apportées précédemment ont été enregistrées dans la mémoire de l’ordinateur local.

12. Ouvrez le [portail Azure](https://portal.azure.com/).
 
14. Cliquez sur **Parcourir > API Apps > {votre\_nouvelle\_application\_API}**.

	![](./media/app-service-api-dotnet-get-started/choosenewapiappinportal.png)

16. Cliquez sur **Paramètres** puis, dans le panneau **Paramètres**, recherchez la section API et cliquez sur **Définition de l’API**.

	![](./media/app-service-api-dotnet-get-started/apidefinsettings.png)

	Le panneau Définition de l’API vous permet de spécifier l’URL qui retourne les métadonnées Swagger 2.0 au format JSON. Quand Visual Studio crée l’application API, il affecte la valeur par défaut que vous avez vue précédemment comme URL de définition d’API, à savoir l’URL de base de l’application API plus `/swagger/docs/v1`.

	![](./media/app-service-api-dotnet-get-started/apidefurl.png)

	Quand vous sélectionnez une application API pour laquelle générer le code client, Visual Studio extrait les métadonnées à partir de cette URL.

### URL de définition d’API dans les outils Azure Resource Manager

Vous pouvez également configurer l’URL de définition d’API pour une application API à l’aide d’outils tels qu’Azure PowerShell, l’interface de ligne de commande ou l’[Explorateur de ressources](https://resources.azure.com/).

Pour cela, définissez la propriété `apiDefinition` sur le type de ressource `Microsoft.Web/sites/config` pour votre ressource `<site name>/web`. Par exemple, dans l’**Explorateur de ressources**, accédez à **abonnements > {votre abonnement} > resourceGroups > {votre groupe de ressources} > fournisseurs > Microsoft.Web > sites > {votre site} > config > web**. Vous verrez la propriété `apiDefinition` :

		"apiDefinition": {
		  "url": "https://todolistdataapi.azurewebsites.net/swagger/docs/v1"
		}

Pour voir un exemple de modèle Azure Resource Manager qui inclut JSON pour la configuration de la propriété de définition d’API, ouvrez le [fichier azuredeploy.json dans le référentiel d’exemples d’application](https://github.com/azure-samples/app-service-api-dotnet-todo-list/blob/master/azuredeploy.json).

## <a id="codegen"></a> Consommer à partir d’un client .NET à l’aide d’un code client généré

L’un des avantages de l’intégration de Swagger dans les applications API Azure est la génération de code automatique. Les classes de client générées facilitent l’écriture du code qui appelle une application API.

Dans cette section, vous allez découvrir comment consommer une application API à partir d’un code d’API web ASP.NET.

### Générer du code client

Vous pouvez générer du code client pour une application API à l’aide de Visual Studio ou à partir de la ligne de commande. Dans ce didacticiel, vous allez utiliser Visual Studio. Pour plus d’informations sur la façon de procéder à partir de la ligne de commande, consultez le fichier Lisez-moi du référentiel [Azure/autorest](https://github.com/azure/autorest) sur GitHub.com.

Le projet ToDoListAPI contient déjà le code client généré, mais vous allez le supprimer et le régénérer, ce qui aura pour effet de réinitialiser l’URL cible par défaut de votre propre application API.

1. Dans l’**Explorateur de solutions** de Visual Studio, dans le projet ToDoListAPI, supprimez le dossier *ToDoListDataAPI*.

	Ce dossier a été créé à l’aide du processus de génération de code que vous allez aborder.

	![](./media/app-service-api-dotnet-get-started/deletecodegen.png)

2. Cliquez avec le bouton droit sur le projet ToDoListAPI, puis cliquez sur **Ajouter > Client API REST**.

	![](./media/app-service-api-dotnet-get-started/codegenmenu.png)

3. Dans la boîte de dialogue **Ajouter un client d’API REST**, cliquez sur **URL Swagger**, puis sur **Sélectionner une ressource Azure**.

	![](./media/app-service-api-dotnet-get-started/codegenbrowse.png)

8. Dans la boîte de dialogue **App Service**, développez le groupe de ressources que vous utilisez pour ce didacticiel, sélectionnez votre application API, puis cliquez sur **OK**.

	Cette boîte de dialogue offre plusieurs manières d’organiser les applications API dans la liste, au cas où il y en aurait trop à faire défiler. Elle vous permet également d’entrer une chaîne de recherche pour filtrer les applications API par nom.

	![](./media/app-service-api-dotnet-get-started/codegenselect.png)

	Si l’application API n’apparaît pas dans la liste, vous avez probablement omis accidentellement l’étape qui consiste à faire passer une application du type web au type API au moment de la création de l’application API. Dans ce cas, vous pouvez créer une nouvelle application API en répétant les opérations que vous avez réalisées précédemment. Vous devez choisir un autre nom pour l’application API.

	Notez que lorsque vous revenez à la boîte de dialogue **Client API REST**, la zone de texte a été remplie avec la valeur de l’URL de définition d’API que vous avez vue précédemment dans le portail.

	![](./media/app-service-api-dotnet-get-started/codegenurlplugged.png)

	Pour obtenir les métadonnées nécessaires à la génération du code, vous pouvez également saisir l’URL directement au lieu de passer par la boîte de dialogue. Par exemple, si vous avez déployé votre API vers une application web et qu’elle ne figure pas dans la boîte de dialogue, vous pouvez entrer manuellement l’URL qui retourne les métadonnées Swagger ici.

	Une autre solution permettant d’obtenir les métadonnées consiste à utiliser l’option **Sélectionner un fichier de métadonnées Swagger existant**. Par exemple, si vous souhaitez générer le code client avant le déploiement vers Azure, vous pouvez exécuter localement, télécharger le fichier JSON Swagger et le sélectionner ici.

9. Dans la boîte de dialogue **Ajouter un client API REST**, cliquez sur **OK**.

	Visual Studio crée un dossier nommé d’après l’application API et il génère des classes de client.

	![](./media/app-service-api-dotnet-get-started/codegenfiles.png)

5. Dans le projet ToDoListAPI, ouvrez *Controllers\\ToDoListController.cs* pour afficher le code qui appelle l’API à l’aide du client généré.

	L’extrait de code suivant montre comment instancier l’objet client et appeler la méthode Get.

		private ToDoListDataAPI db = new ToDoListDataAPI(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

	Le paramètre de constructeur obtient l’URL de point de terminaison à partir du paramètre d’application `toDoListDataAPIURL`. Dans le fichier Web.config, cette valeur est définie sur l’URL IIS Express locale du projet d’API dans le paramètre `toDoListDataAPIURL` pour vous permettre d’exécuter l’application localement. Si vous omettez le paramètre de constructeur, le point de terminaison par défaut est l’URL à partir de laquelle vous avez généré le code.

6. Votre classe de client est générée avec un nom différent selon le nom de votre application API. Modifiez ce code pour que le nom de type corresponde à ce qui a été généré dans votre projet. Par exemple, si vous avez nommé votre application API ToDoListDataAPI0121, le code se présente comme suit :

		private ToDoListDataAPI0121 db = new ToDoListDataAPI0121(new Uri(ConfigurationManager.AppSettings["toDoListDataAPIURL"]));
		
		public ActionResult Index()
		{
		    return View(db.Contacts.Get());
		}

#### Créer une application API pour héberger la couche intermédiaire

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet ToDoListAPI, puis cliquez sur **Publier**.

3.  Sous l’onglet **Profil** de l’Assistant **Publier le site web**, cliquez sur **Microsoft Azure App Service**.

5. Dans la boîte de dialogue **App Service**, cliquez sur **Nouveau**.

3. Sous l’onglet **Hébergement** de la boîte de dialogue **Créer App Service**, cliquez sur **Modifier le type**, puis remplacez le type par **Application web**.

4. Entrez un **Nom de l’application API** unique dans le domaine *azurewebsites.net*.

5. Choisissez l’**Abonnement** Azure souhaité.

6. Dans la liste déroulante **Groupe de ressources**, choisissez le groupe de ressources créé précédemment.

4. Dans la liste déroulante **Plan App Service**, choisissez le plan créé précédemment.

7. Cliquez sur **Create**.

	Visual Studio crée l’application API, crée un profil de publication pour celle-ci, puis affiche l’étape **Connexion** de l’Assistant **Publier le site web**.

### Définir l’URL de la couche Données dans les paramètres de l’application de niveau intermédiaire

1. Dans le [portail Azure](https://portal.azure.com/), accédez au panneau **application API** de l’application API que vous avez créée pour héberger le projet TodoListAPI (de niveau intermédiaire).

2. Cliquez sur **Paramètres > Paramètres de l'application**.

3. Dans la section **Paramètres de l’application**, ajoutez la clé et la valeur suivantes :

	|Clé|Valeur|Exemple
	|---|---|---|
	|toDoListDataAPIURL|https://{nom de votre application API de la couche Données}.azurewebsites.net|https://todolistdataapi0121.azurewebsites.net|

4. Cliquez sur **Enregistrer**.

	Lorsque le code sera exécuté dans Azure, cette valeur remplacera dès lors l’URL de l’hôte local qui se trouve dans le fichier Web.config.

### Déployer le projet ToDoListAPI vers la nouvelle application API

3.  À l’étape **Connexion** de l’Assistant **Publier le site web**, cliquez sur **Publier**.

	Visual Studio déploie le projet ToDoListAPI vers la nouvelle application API et ouvre un navigateur à l’URL de l’application API. Une page s’affiche pour confirmer la création.

### Test permettant de vérifier que ToDoListAPI appelle ToDoListDataAPI

11. Ajoutez « swagger » à l’URL dans la barre d’adresses du navigateur, puis appuyez sur Entrée. (L’URL est `http://{apiappname}.azurewebsites.net/swagger`.)

	Le navigateur affiche la même interface utilisateur Swagger que vous avez vue précédemment pour ToDoListDataAPI, mais `owner` n’est plus un champ obligatoire, car l’application API de couche intermédiaire envoie automatiquement cette valeur à l’application API de la couche Données.

12. Essayez la méthode Get ainsi que d’autres méthodes pour confirmer que l’application API de couche intermédiaire appelle correctement l’application API de la couche de données.

	![](./media/app-service-api-dotnet-get-started/midtierget.png)

## Étapes suivantes

Dans ce didacticiel, vous avez vu comment créer des applications API, y déployer du code, y générer du code client et les consommer à partir de clients .NET. Le didacticiel suivant de la série de prise en main d’API Apps montre comment [consommer des applications API à partir de clients JavaScript à l’aide de CORS](app-service-api-cors-consume-javascript.md).

<!---HONumber=AcomDC_0211_2016-->