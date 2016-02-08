<properties 
	pageTitle="Déployer une application API dans Azure App Service" 
	description="Découvrez comment déployer un projet d’application API dans votre abonnement Azure." 
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
	ms.date="01/08/2016" 
	ms.author="tdykstra"/>

# Déployer une application API dans Azure App Service 

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## Vue d'ensemble

Dans ce didacticiel, vous déployez le projet d’API web que vous avez créé dans le [didacticiel précédent](app-service-dotnet-create-api-app.md) vers une nouvelle [application API](app-service-api-apps-why-best-platform.md). Vous utilisez Visual Studio pour créer la ressource d’application API dans [Azure App Service](../app-service/app-service-value-prop-what-is.md), puis déployez votre code d’API web vers l’application API Azure.

### Autres options de déploiement

Il existe de nombreuses autres manières de déployer les applications API. Une application API est une [application web](../app-service-web/app-service-web-overview.md) avec des fonctionnalités supplémentaires pour l’hébergement de services web et toutes les [méthodes de déploiement disponibles pour les applications web](../app-service-web/web-sites-deploy.md) peuvent également être utilisées avec les applications API. L’application web qui héberge une API est appelée hôte d’application API dans le portail Azure en version préliminaire. Vous pouvez configurer le déploiement dans le panneau du portail de l’hôte d’application API. Pour plus d’informations sur le panneau d’hôte de l’application API, consultez la page [Gérer une application API](app-service-api-manage-in-portal.md).

Le fait que les applications API soient basées sur les applications web signifie également que vous pouvez déployer le code écrit pour les plateformes autres qu’ASP.NET pour les applications API. Pour obtenir un exemple qui utilise Git pour déployer le code Node.js vers une application API, consultez la page [Créer une application API Node.js dans Azure App Service](app-service-api-nodejs-api-app.md).
 
## <a id="provision"></a>Créer une application API dans Azure 

Dans cette section, vous utilisez l’Assistant **Publier le site Web** de Visual Studio pour créer une application API dans Azure. Lorsque les instructions vous invitent à entrer un nom pour l’application API, entrez *ContactsList*.

[AZURE.INCLUDE [app-service-api-pub-web-create](../../includes/app-service-api-pub-web-create.md)]

## <a id="deploy"></a>Déploiement de votre code dans la nouvelle application API Azure

Vous utilisez le même Assistant **Publier le site Web** pour déployer votre code vers la nouvelle application API.

[AZURE.INCLUDE [app-service-api-pub-web-deploy](../../includes/app-service-api-pub-web-deploy.md)]

## Appeler l'application API Azure 

Étant donné que vous avez activé l'interface utilisateur Swagger dans le didacticiel précédent, vous pouvez utiliser ceci pour vérifier que l'application API s'exécute dans Azure.

1. Dans le [portail Azure en version préliminaire](https://portal.azure.com/), accédez au panneau **Application API** de l’application API que vous avez déployée.

2. Cliquez sur l’URL de l’application API.

	![Cliquez sur l'URL](./media/app-service-dotnet-deploy-api-app/clickurl.png)

	Une page « Application API créée avec succès » s'affiche.

3. Ajoutez « /swagger » à la fin de l’URL dans la barre d’adresse du navigateur.

4. Sur la page Swagger qui s’affiche, cliquez sur **Contacts > Obtenir > Faire un essai**.

	![Faites un essai](./media/app-service-dotnet-deploy-api-app/swaggerui.png)

## Afficher la définition d’API dans le portail

1. Dans le [portail Azure en version préliminaire](https://portal.azure.com/), revenez au panneau **Application API** de l’application API que vous avez déployée.

4. Cliquez sur **Définition d’API**.
 
	Le panneau **Définition d’API** de l’application affiche la liste des opérations sur l’API que vous avez définies quand vous avez créé l’application.

	![Définition de l’API](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

Ensuite, vous apporterez une modification à la définition de l'API et vous visualiserez les modifications répercutées dans le portail.

5. Revenez au projet dans Visual Studio et ajoutez le code suivant au fichier **ContactsController.cs**.   

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	Ce code ajoute une méthode **Post** qui peut être utilisée pour publier de nouvelles instances `Contact` dans l’API.

	Le code de la classe de Contacts ressemble maintenant à l'exemple suivant.

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
		
		    [HttpPost]
		    public HttpResponseMessage Post([FromBody] Contact contact)
		    {
		        // todo: save the contact somewhere
		        return Request.CreateResponse(HttpStatusCode.Created);
		    }
		}

7. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.

9. Cliquez sur l’onglet **Aperçu**

10. Cliquez sur **Démarrer l’aperçu** pour voir quels fichiers seront copiés vers Azure.

	![Boîte de dialogue Publier le site web](./media/app-service-dotnet-deploy-api-app/39-re-publish-preview-step-v2.png)

11. Cliquez sur **Publier**.

6. Redémarrez la passerelle comme la première fois que vous avez effectué une publication.

12. Une fois le processus de publication terminé, revenez au portail, puis fermez et rouvrez le panneau **Définition d’API**. Vous verrez le nouveau point de terminaison d'API que vous venez de créer et de déployer directement dans votre abonnement Azure.

	![Définition de l’API](./media/app-service-dotnet-deploy-api-app/38-portal-with-post-method-v4.png)

## Étapes suivantes

Vous avez vu comment les fonctionnalités de déploiement direct dans Visual Studio facilitent le test permettant de vérifier que l’API fonctionne correctement. Dans le [didacticiel suivant](../app-service-dotnet-remotely-debug-api-app.md), vous verrez comment déboguer votre application API pendant son exécution dans Azure.

Les application API sont des applications web avec des fonctionnalités supplémentaires pour l’hébergement d’API, ce qui signifie que vous pouvez utiliser n’importe quelle méthode qui fonctionne avec des applications web. Pour plus d’informations sur les options de déploiement des applications web, consultez [Déployer une applications web dans Azure App Service](../app-service-web/web-sites-deploy.md).

Pour en savoir plus sur les fonctionnalités API Apps, consultez [Qu’est-ce qu’API Apps ?](app-service-api-apps-why-best-platform.md).

<!---HONumber=AcomDC_0128_2016-->