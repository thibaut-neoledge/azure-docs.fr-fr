<properties 
	pageTitle="Créer une application API dans Azure App Service" 
	description="Cet article explique comment utiliser Visual Studio 2013 pour créer une application API dans Azure App Service" 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="bradygaster" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/19/2015" 
	ms.author="bradyg;tarcher"/>

# Créer une application API dans Azure App Service

## Vue d'ensemble

Il s'agit du premier didacticiel d'une série de trois, qui vous permet de prendre en main les applications API dans Azure App Service.

1. Dans ce didacticiel, vous créez une application API et préparez son déploiement dans votre abonnement Azure. 
* Dans [Déployer une application API](app-service-dotnet-create-api-app.md), vous déployez l'application API dans votre abonnement Azure.
* Dans [Déboguer une application API](app-service-dotnet-remotely-debug-api-app.md), vous utilisez Visual Studio pour déboguer à distance le code pendant son exécution dans Azure.

[AZURE.INCLUDE [install-sdk-2013-only](../includes/install-sdk-2013-only.md)]

## Créer votre première application API ##

Ouvrez Visual Studio 2013 et sélectionnez **Fichier > Nouveau projet**. Sélectionnez le modèle **Application Web ASP.NET**.  Nommez le projet  *ContactsList*, puis cliquez sur **OK**.

![](./media/app-service-dotnet-create-api-app/01-filenew-v3.png)

Sélectionnez le modèle de projet **Application API Azure** et cliquez sur **OK**.

![](./media/app-service-dotnet-create-api-app/02-api-app-template-v3.png)

Cliquez avec le bouton droit sur le dossier **Modèles** dans le projet d'API web et sélectionnez **Ajouter > Classe** dans le menu contextuel. 

![](./media/app-service-dotnet-create-api-app/03-add-new-class-v3.png) 

Nommez le nouveau fichier  *Contact.cs*, puis cliquez sur **Ajouter**. 

![](./media/app-service-dotnet-create-api-app/0301-add-new-class-dialog-v3.png) 

Remplacez tout le contenu du fichier par le code suivant. 

	namespace ContactsList.Models
	{
		public class Contact
		{
			public int Id { get; set; }
			public string Name { get; set; }
			public string EmailAddress { get; set; }
		}
	}

Cliquez avec le bouton droit sur le dossier **Contrôleurs** et sélectionnez **Ajouter > Contrôleur** dans le menu contextuel. 

![](./media/app-service-dotnet-create-api-app/05-new-controller-v3.png)

Dans la boîte de dialogue **Ajouter une génération de modèles automatique**, sélectionnez l'option **Contrôleur d'API web 2 - Vide** et cliquez sur **Ajouter**. 

![](./media/app-service-dotnet-create-api-app/06-new-controller-dialog-v3.png)

Nommez le contrôleur **ContactsController** et cliquez sur **Ajouter**. 

![](./media/app-service-dotnet-create-api-app/07-new-controller-name-v2.png)

Remplacez le code dans ce fichier par le code ci-dessous. 

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
                	new Contact { Id = 3, EmailAddress = "lora@microsoft.com", Name = "Lora Riggs"}
	            };
	        }
	    }
	}

Les projets d'application API sont activés avec la génération de métadonnées [Swagger](http://swagger.io/ "Official Swagger information") automatique et une page de test d'API. Par défaut, la page de test d'API est désactivée. Pour activer la page de test d'API, ouvrez le fichier  *App_Start/SwaggerConfig.cs*. Recherchez **EnableSwaggerUI** :

![](./media/app-service-dotnet-create-api-app/12-enable-swagger-ui-with-box.png)

Annulez les marques de commentaire sur les lignes de code suivantes :

        })
    .EnableSwaggerUi(c =>
        {

Une fois terminé, le fichier doit avoir l'aspect suivant dans Visual Studio 2013.

![](./media/app-service-dotnet-create-api-app/13-enable-swagger-ui-with-box.png)

Pour afficher la page de test d'API, exécutez l'application localement et accédez à `/swagger`. 

![](./media/app-service-dotnet-create-api-app/14-swagger-ui.png)

Cliquez sur le bouton **Essayer**. Vous constatez que l'API fonctionne et retourne le résultat attendu. 

![](./media/app-service-dotnet-create-api-app/15-swagger-ui-post-test.png)

## Étapes suivantes

Votre application API est maintenant prête à être déployée et vous pouvez pour cela suivre le didacticiel [Déployer une application API](app-service-dotnet-deploy-api-app.md).

Pour plus d'informations, consultez [Que sont les applications API ?](app-service-api-apps-why-best-platform.md)

<!--HONumber=49-->