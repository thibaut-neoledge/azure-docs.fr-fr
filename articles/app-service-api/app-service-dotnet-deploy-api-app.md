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
	ms.date="07/31/2015" 
	ms.author="bradyg"/>

# Déployer une application API dans Azure App Service 

## Vue d'ensemble

Dans ce didacticiel, vous allez déployer le projet d’API web que vous avez créé dans le [didacticiel précédent](app-service-dotnet-create-api-app.md) vers une nouvelle [application API](app-service-api-apps-why-best-platform.md). Vous allez utiliser Visual Studio pour créer la ressource d’application API dans [Azure App Service](../app-service/app-service-value-prop-what-is.md) et déployer votre code d’API web avec vers l’application API Azure.

### Autres options de déploiement

Il existe de nombreuses autres manières de déployer les applications API. Une application API est une [application web](../app-service-web/app-service-web-overview.md) avec des fonctionnalités supplémentaires pour l’hébergement de services web et toutes les [méthodes de déploiement disponibles pour les applications web](../app-service-web/web-sites-deploy.md) peuvent également être utilisées avec les applications API. L’application web qui héberge une API est appelée hôte d’application API dans le portail Azure en version préliminaire. Vous pouvez configurer le déploiement dans le panneau du portail de l’hôte d’application API. Pour plus d’informations sur le panneau d’hôte de l’application API, consultez la page [Gérer une application API](app-service-api-manage-in-portal.md).

Le fait que les applications API soient basées sur les applications web signifie également que vous pouvez déployer le code écrit pour les plateformes autres qu’ASP.NET pour les applications API. Pour obtenir un exemple qui utilise Git pour déployer le code Node.js vers une application API, consultez la page [Créer une application API Node.js dans Azure App Service](app-service-api-nodejs-api-app.md).
 
## <a id="provision"></a>Créer une application API dans Azure 

Dans cette section, vous utilisez l’Assistant **Publier le site Web** de Visual Studio pour créer une application API dans Azure. Lorsque les instructions vous invitent à entrer un nom pour l’application API, entrez *ContactsList*.

[AZURE.INCLUDE [app-service-api-pub-web-create](../../includes/app-service-api-pub-web-create.md)]

## <a id="deploy"></a>Déploiement de votre code dans la nouvelle application API

Vous utilisez le même Assistant **Publier le site Web** pour déployer votre code vers la nouvelle application API.

[AZURE.INCLUDE [app-service-api-pub-web-deploy](../../includes/app-service-api-pub-web-deploy.md)]

## Afficher l’application dans le portail Azure en version préliminaire

Dans cette section, vous consultez les paramètres de base disponibles pour les applications API dans le portail et apportez des modifications itératives à votre application API. Avec chaque déploiement, le portail reflète les modifications que vous apportez à votre application API.

1. Dans le [portail Azure en version préliminaire](https://portal.azure.com), accédez au panneau **Application API** de l’application API que vous avez déployée.

4. Cliquez sur **Définition d’API**.
 
	Le panneau **Définition d’API** de l’application affiche la liste des opérations sur l’API que vous avez définies quand vous avez créé l’application.

	![Définition de l’API](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

5. Maintenant, revenez au projet dans Visual Studio et ajoutez le code suivant au fichier **ContactsController.cs**.

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	![Ajout de la méthode Post au contrôleur](./media/app-service-dotnet-deploy-api-app/30-post-method-added-v3.png)

	Ce code ajoute une méthode **Post** qui peut être utilisée pour publier de nouvelles instances `Contact` dans l’API.

6. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.

	![Menu contextuel de publication du projet](./media/app-service-dotnet-deploy-api-app/31-publish-gesture-v3.png)

7. Cliquez sur l'onglet **Paramètres**.

8. Dans la liste déroulante **Configuration**, sélectionnez **Déboguer**.

	![Paramètres Publier le site web](./media/app-service-dotnet-deploy-api-app/36.5-select-debug-option-v3.png)

9. Cliquez sur l’onglet **Aperçu**

10. Cliquez sur **Démarrer l’aperçu** pour afficher les modifications qui seront apportées.

	![Boîte de dialogue Publier le site web](./media/app-service-dotnet-deploy-api-app/39-re-publish-preview-step-v2.png)

11. Cliquez sur **Publier**.

12. Une fois le processus de publication terminé, revenez au portail, puis fermez et rouvrez le panneau **Définition d’API**. Vous verrez le nouveau point de terminaison d'API que vous venez de créer et de déployer directement dans votre abonnement Azure.

	![Définition de l’API](./media/app-service-dotnet-deploy-api-app/38-portal-with-post-method-v4.png)

## Étapes suivantes

Vous avez vu comment les fonctionnalités de déploiement direct dans Visual Studio facilitent l'itération et le déploiement rapide de votre API ainsi que le test permettant de vérifier qu'elle fonctionne correctement. Dans le [didacticiel suivant](../app-service-dotnet-remotely-debug-api-app.md), vous verrez comment déboguer votre application API pendant son exécution dans Azure.
 

<!---HONumber=August15_HO6-->