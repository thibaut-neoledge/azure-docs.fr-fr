<properties 
	pageTitle="Convertir une API existante en application API" 
	description="Découvrez comment configurer un projet d'API web Visual Studio pour le déploiement sous forme d'application API dans Azure App Service." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="03/24/2015" 
	ms.author="tdykstra"/>

# Convertir une API existante en application API

## Vue d'ensemble

Dans ce didacticiel, vous créez un projet d'API web ASP.NET dans Visual Studio, puis ajoutez des packages NuGet et métadonnées de projet qui vous permettent de publier et déployer le projet comme une application API dans Azure App Service. Le didacticiel explique également comment personnaliser les métadonnées d'application API.

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Créer un projet d'API web

1.  Ouvrez la boîte de dialogue **Nouveau projet** dans Visual Studio.

2.  Sélectionnez le nœud **Cloud** dans le volet **Modèles installés**, puis le modèle **Application Web ASP.NET**.

3.  Nommez le projet  *ContactsList*, puis cliquez sur **OK**.

	![New Project dialog](./media/app-service-dotnet-create-api-app-visual-studio/vstemplate.png)

4. Dans la boîte de dialogue **Nouveau projet ASP.NET**, sélectionnez le modèle **Vide**, cochez la case **API Web**, décochez la case **Héberger dans le cloud**, puis cliquez sur **OK**.

	![Azure API App template](./media/app-service-dotnet-create-api-app-visual-studio/vstemplate2.png)

	Visual Studio crée des fichiers projet pour un projet d'API web vide.

	![Web API files in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/sewebapi.png)

3. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le dossier **Modèles**, puis cliquez sur **Ajouter > Classe** dans le menu contextuel. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-class-v2.png) 

4. Nommez le nouveau fichier *Contact.cs*, puis cliquez sur **Ajouter**.

5. Remplacez le contenu du nouveau fichier par le code suivant. 

		namespace ContactsList.Models
		{
			public class Contact
			{
				public int Id { get; set; }
				public string Name { get; set; }
				public string EmailAddress { get; set; }
			}
		}

5. Cliquez avec le bouton droit sur le dossier **Contrôleurs** et cliquez sur **Ajouter > Contrôleur** dans le menu contextuel. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller.png)

6. Dans la boîte de dialogue **Ajouter une génération de modèles automatique**, sélectionnez l'option **Contrôleur d'API web 2 - Vide**, puis cliquez sur **Ajouter**. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller-dialog.png)

7. Nommez le contrôleur **ContactsController**, puis cliquez sur **Ajouter**. 

	![](./media/app-service-dotnet-create-api-app-visual-studio/new-controller-name.png)

8. Remplacez le code dans le nouveau fichier de contrôleur par le code suivant. 

		using ContactsList.Models;
		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Net;
		using System.Net.Http;
		using System.Threading.Tasks;
		using System.Web.Http;
		
		namespace ContactsList.Controllers
		{
		    public class ContactsController : ApiController
		    {
		        [HttpGet]
		        public IEnumerable<Contact> Get()
		        {
		            return new Contact[]{
		                new Contact { Id = 1, EmailAddress = "barney@contoso.com", Name = "Barney Poland"},
		                new Contact { Id = 2, EmailAddress = "lacy@contoso.com", Name = "Lacy Barrera"},
		                new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"},
		            };
		        }
		    }
		}


Vous disposez maintenant d'un projet d'API web qui fonctionne. La façon la plus simple de vérifier qu'il fonctionne consiste à appeler la méthode Get à partir d'un navigateur.

6. Appuyez sur Ctrl+F5 pour exécuter le projet.

	Le navigateur affiche une erreur HTTP 403, car il s'ouvre sans une URL complète qui pointe vers la méthode Get.

7. Dans la barre d'adresse du navigateur, ajoutez " api/contacts/get/ " à l'URL et appuyez sur Entrée.  L'URL finale ressemble à l'exemple suivant :

		http://localhost:25735/api/contacts/get/

	Des navigateurs différents répondent de façon différente à un appel d'API ; si vous utilisez Internet Explorer, vous verrez un message de téléchargement au bas de la fenêtre du navigateur :

	![](./media/app-service-dotnet-create-api-app-visual-studio/get-response.png)

## Configurer l'application pour le déploiement sous forme d'application API

1. Dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le projet (pas la solution), puis cliquez sur **Ajouter > Kit de développement logiciel (SDK) d'application API Azure**.

	![Add API app metadata](./media/app-service-dotnet-create-api-app-visual-studio/addapiappsdk.png)

2. Dans la boîte de dialogue **Choisir la source des métadonnées d'application API**, cliquez sur **Génération automatique de métadonnées**.

	![Choose automatic metadata](./media/app-service-dotnet-create-api-app-visual-studio/chooseswagger.png)

	Ce choix active l'interface utilisateur Swagger dynamique, que vous verrez plus tard dans le didacticiel. Vous pouvez également fournir un fichier de définition d'API Swagger statique. Pour ce faire, sélectionnez **Spécifier un fichier JSON statique contenant des métadonnées Swagger** et Visual Studio vous demande de télécharger un fichier. Le fichier téléchargé est enregistré en tant que *apiDefinition.Swagger.json* dans le dossier *Metadata*.

3. Cliquez sur **OK**.

	Visual Studio ajoute un fichier *apiapp.json* et un dossier *Metadata*.

	![API App files in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/metadatainse.png)

	Visual Studio ajoute également des packages NuGet Swashbuckle et, dans les étapes suivantes, vous allez essayer l'interface utilisateur de définition d'API dynamique fournie par Swagger. 

6. Appuyez sur Ctrl+F5 pour exécuter le projet.

	Comme avant, le navigateur affiche une erreur HTTP 403.

7. Dans la barre d'adresse du navigateur, ajoutez " swagger/ " à l'URL et appuyez sur Entrée.  L'URL finale ressemble à l'exemple suivant :

		http://localhost:25735/swagger/

8. Dans la page Swagger, cliquez sur **Contacts** pour voir les méthodes disponibles.
 
	Seule la méthode `Get` est affichée, car il s'agit de la seule méthode que vous avez créée dans votre contrôleur Contacts. La page affiche un exemple de réponse JSON.

9. Cliquez sur la méthode Get pour afficher un exemple de réponse JSON et un bouton **Essayer**.

 	![Swagger - expand Contacts](./media/app-service-dotnet-create-api-app-visual-studio/swagger1.png)

9. Cliquez sur **Essayer**.

	La réponse que vous avez codée dans ContactsController est retournée.

 	![Swagger - Try it out](./media/app-service-dotnet-create-api-app-visual-studio/swagger2.png)

Votre projet d'API web est maintenant prêt à être déployé comme application API dans Azure App Service. Les sections suivantes de ce didacticiel fournissent des informations sur les métadonnées que vous pouvez modifier pour personnaliser votre application API. 

## Examiner apiapp.json

Les paramètres du fichier  *apiapp.json* déterminent la façon dont l'application API est identifiée et présentée dans Azure Marketplace. Dans cette version préliminaire, comme Visual Studio n'inclut pas la possibilité de publier des applications API dans Marketplace, un grand nombre de ces paramètres n'a aucun effet.

![apiapp.json in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/apiappjsoninse.png)

Le contenu initial du fichier qui est créé quand vous choisissez l'entrée de menu **Kit de développement logiciel (SDK) d'application API Azure** ressemble à l'exemple suivant :

		{
		  "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		  "id": "ContactsList",
		  "namespace": "",
		  "gateway": "2015-01-14",
		  "version": "1.0.0",
		  "title": "ContactsList",
		  "summary": "",
		  "author": "",
		  "endpoints": {
		    "apiDefinition": "/swagger/docs/v1",
		    "status": null
		  }
		}

Le tableau suivant décrit le format et l'utilisation des champs inclus dans le fichier ainsi que d'autres champs facultatifs que vous pouvez lui ajouter. 

**Remarque :** comme mentionné plus haut, un grand nombre de ces champs n'a aucun effet dans cette version préliminaire, car ils déterminent la façon dont l'application API est présentée dans Azure Marketplace, mais Visual Studio n'a pas encore inclus la possibilité de publier des applications API dans Marketplace.

| Nom (en gras = requis) | Type | Commentaires |
|:-----------|:------------|:------------|
|**id**           |string|ID de ce package. Doit être unique dans un espace de noms et ne doit pas contenir de points, ni de caractère / ou @. Quand vous déployez le projet, Visual Studio valide le fait que l'ID est unique et vous donne la possibilité de le modifier. 
|**namespace**    |string|Espace de noms qui, avec la propriété **id**, identifie de façon unique l'application API.  La propriété est requise, mais la valeur peut être une chaîne vide. <br/><br/>Cette propriété vous permet de spécifier un domaine, par exemple contoso.com.  Si l'ID de package que vous souhaitez utiliser l'est déjà, vous pouvez ajouter un domaine afin de pouvoir employer cet ID de package. Par exemple, si ContactsList existe déjà dans la galerie d'applications API sans espace de noms, vous pouvez ajouter un package ContactsList avec l'espace de noms contoso.com. <br/><br/>Vous pouvez également spécifier un domaine pour ajouter un package à la galerie d'applications API accessible uniquement aux membres de votre organisation. <br/><br/>L'espace de noms sera utilisé comme nom d'éditeur dans Marketplace, après la conversion des points en tirets et des tirets en deux tirets (--).<br/><br/>L'espace de noms est " microsoft.com " pour les applications API fournies par Microsoft.  
|**version**      |string|Format [Semver](http://docs.nuget.org/Create/Versioning), par exemple alpha 1.0.1, 1.1.0.
|**gateway**      |string|Version de la passerelle, exprimée sous forme de date, par exemple : 2015-01-14. Une *gateway* est une application web particulière via laquelle toutes les demandes adressées aux applications API dans un groupe de ressources sont routées. Une de ses fonctions principales consiste à gérer l'authentification. Actuellement, la seule version de passerelle est 2015-01-14. À l'avenir, quand de nouvelles versions de passerelles seront disponibles, cette propriété vous donnera la possibilité d'éviter les modifications avec rupture et de continuer à utiliser l'API de passerelle précédente. 
|**title**        |string|Nom complet affiché de l'application API.
|**summary**      |string|Bref résumé de l'application API comprenant 100 caractères au maximum.
|description      |string|Description complète de l'application API. Peut contenir du code HTML, 1 500 caractères au maximum.
|**author**       |string|Auteur(s) de l'application API, 256 caractères au maximum.
|homepage         |URI|Page d'accueil de l'application API.
|endpoints        |objet[]|Tableau d'un élément qui peut contenir un point de terminaison de définition d'API. 
|>>endpoints.apiDefinition|string|URI relatif de l'interface utilisateur de définition d'API Swagger dynamique (par exemple, " /swagger/docs/v1 ") ou d'un fichier de définition d'API Swagger statique. Pour l'API web ASP.NET, l'interface utilisateur Swagger générée dynamiquement est généralement le meilleur choix mais, si cela ne fonctionne pas avec votre API ou si vous n'utilisez pas l'API web ASP.NET, vous pouvez fournir un fichier de définition statique. Pour fournir un fichier de définition statique, vous pouvez spécifier l'URI relatif qui pointe dessus ici, ou vous pouvez laisser cette propriété vide et inclure le fichier de définition d'API statique dans le dossier de métadonnées en tant que [apiDefinition.swagger.json](#apidef). 
|>>endpoints.status|URI|Réservé pour une utilisation ultérieure.
|categories       |string[]|Détermine où le package s'affiche dans Azure Marketplace. Les valeurs valides sont : réseau social, entreprise, intégration, protocole, service de données d'application, autre. Valeur par défaut : autre.
|license          |objet|Licence de l'application API.
|>>**license.type**|string|Identificateur de licence SPDX, par exemple MIT.
|>>license.url    |url|Url absolue pointant vers le texte de la licence dans son intégralité.
|>>license.requireAcceptance|valeur booléenne|Indique si une licence doit être approuvée avant l'installation. Valeur par défaut : false.
|links            |objet[]|Tableau de liens à ajouter à la page Marketplace.
|>>**links.text** |string|Texte du lien.
|>>**links.url**  |url|URL du lien.
|authentication|objet[]|Tableau qui indique le type d'authentification nécessaire à cette application API pour effectuer des appels d'API sortants.  Par exemple, un connecteur DropBox doit s'authentifier auprès de DropBox et un connecteur Salesforce doit s'authentifier auprès de Salesforce.
|>>authentication.type|string|Les valeurs prises en charge sont les suivantes (sans distinction minuscules/majuscules) : Box, DropBox, Facebook, GitHub, Google, Instagram, Marketo, Office 365, OneDrive, Quickbooks, Salesforce, SharePointOnline, SugarCRM, Twitter, Yammer. Selon cette valeur, le portail connaît les valeurs de configuration, telles que l'ID client et la question secrète du client, qui sont requises. 
|>>authentication.scopes|string[]|Tableau des portées qui sont spécifiques au type d'authentification.
|copyright        |string|Mention de copyright de l'application API.
|brandColor       |string|Couleur de marque facultative pour piloter l'expérience de l'interface utilisateur sous tout format compatible avec CSS, par exemple #abc, rouge.
|tags             |string[]|Liste de balises associées au package.

<!--todo ajouter aux dépendances de document une fois prêt
|dependencies    |objet[]|Tableau des dépendances du package.
|>>dependencies.id|string|ID du package de dépendance.
|>>dependencies.domain|string|Domaine du package de dépendance.
|>>dependencies.version|string|Version du package de dépendance.
-->

<!--todo ajouter à l'URI d'état de document une fois prêt
URI vers la méthode Get d'un service web qui retourne une valeur qui indique l'état actuel de l'application API. Si vous fournissez cet URI, le portail affiche l'état opérationnel actuel de l'application API ainsi que d'autres informations sur celle-ci, par exemple :  exécution, quota presque atteint, expiration du certificat SSL, etc. Le format de la réponse JSON que le portail s'attend à recevoir est indiqué ci-dessous, à la suite de la fin du tableau. Si vous ne fournissez pas d'URI endpoints.status, le portail affiche l'état de la plateforme Azure en tant qu'état de l'application API.
Voici un exemple illustrant le format attendu de la réponse JSON à partir de la méthode Get vers laquelle pointe  `endpoints.status` :
		{
		  "status":[{ 
		    "name":"Nearing Quota", 
		    "message":"One or more quotas is nearing their limit", 
		    "level":"Warning" 
		  }, { 
		    "name":"Expiring Certificate", 
		    "message":"The SSL Certificate associated with this website is about to expire", 
		    "level":"Error"
		  }, {
		    "name":"Running", 
		    "message":"The website is handling traffic", 
		    "level":"Info"
		  }]
		}
La propriété `name` est une courte description de l'état, `message` est une description plus longue et  `level` peut indiquer " Erreur ", " Avertissement " ou " Infos " pour un état normal.
-->

## Examiner le dossier de métadonnées

Le dossier de métadonnées peut contenir des icônes et des captures d'écran pour la galerie d'applications API, un fichier Swagger documentant l'API et la configuration de l'interface utilisateur pour le portail Azure. Toutes ces informations sont facultatives.

<!-- todo: add later
an Azure Resource Manager template that specifies resources required, 
-->

![Metadata folder in Solution Explorer](./media/app-service-dotnet-create-api-app-visual-studio/metadatafolderinse.png)

**Remarque :** comme indiqué ci-dessus pour *apiapps.json*, les métadonnées se rapportant à la présentation de l'application API dans Azure Marketplace n'ont aucun effet pour cette version préliminaire, car Visual Studio n'inclut pas la possibilité de publier des applications API dans Marketplace.

### Dossier de métadonnées/d'icônes

Vous pouvez fournir des icônes à afficher dans la galerie. 
Si vous ne fournissez pas des icônes personnalisées, les icônes d'application API génériques seront utilisées. Les fichiers icône doivent être au format PNG et respecter les conventions suivantes pour le nom et la taille :

<!--todo: also used in the workflow designer--> 

|Nom de fichier|Taille
|:-----|:-----:
|small.png|40X40
|medium.png|90X90
|large.png|115X115
|hero.png|815X290
|wide.png|255X115

### Dossier de métadonnées/captures d'écran

Vous pouvez fournir jusqu'à 5 captures d'écran à afficher dans la galerie. Les fichiers image doivent être au format PNG, 533x324.

### <a id="apidef"></a>Fichier de métadonnées/apiDefinition.swagger.json

Vous pouvez utiliser un fichier au format [Swagger 2.0](https://github.com/swagger-api/swagger-spec/blob/master/versions/2.0.md) pour décrire la définition d'API de l'application API. Cela vous permet d'exposer les définitions d'API statiquement dans le package. 

<!--todo Explain why this is useful. Original text:
Static API definitions are required for workflow designers to understand the API App triggers and actions before provisioning.-->

<!-- todo: découvrir s'il existe une possibilité de remplacement -- peut-être le sous-dossier deploymentTemplates.
### Fichier resourceTemplate.delta.json
Vous pouvez spécifier un modèle du gestionnaire de ressources Azure personnalisé qui est exécuté lors du déploiement de l'application API. Les ressources spécifiées dans ce fichier delta sont ajoutées aux ressources qui sont créées par défaut pour une application API. Par exemple, si votre application API nécessite une instance de base de données SQL, vous pouvez utiliser ce fichier pour que la base de données soit configurée automatiquement  et que la chaîne de connexion soit définie dans les paramètres de configuration lors du déploiement de l'application API.
-->  

### Fichier de métadonnées/UIDefinition.json

Vous pouvez fournir des informations d'interface utilisateur, comme des indicateurs et la validation, au [format Azure
Marketplace](https://auxdocs.azurewebsites.net/fr-fr/documentation/articles/gallery-items).

### Dossier de métadonnées/deploymentTemplates

Visual Studio crée ce dossier quand vous choisissez l'entrée de menu **Kit de développement logiciel (SDK) d'application API Azure**, mais il n'est pas utilisé pour cette version préliminaire.

## Étapes suivantes

Votre projet d'API web est maintenant prêt à être déployé comme une application API et vous pouvez pour cela suivre le didacticiel [Déployer une application API](app-service-dotnet-deploy-api-app.md).

Pour plus d'informations, consultez [Que sont les applications API ?](app-service-api-apps-why-best-platform.md)

<!--HONumber=49-->