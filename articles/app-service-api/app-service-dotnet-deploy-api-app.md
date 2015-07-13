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
	ms.date="05/04/2015" 
	ms.author="bradyg;tarcher"/>

# Déployer une application API dans Azure App Service 

## Vue d'ensemble

Dans ce didacticiel, vous allez déployer le projet d’API web que vous avez créé dans le [didacticiel précédent](app-service-dotnet-create-api-app.md) vers une nouvelle [application API](app-service-api-apps-why-best-platform.md). Vous allez utiliser Visual Studio pour créer la ressource d’application API dans [Azure App Service](../app-service/app-service-value-prop-what-is.md) et déployer votre code d’API web avec vers l’application API Azure.

### Autres options de déploiement

Il existe de nombreuses autres manières de déployer les applications API. Une application API est une [application web](../app-service-web/app-service-web-overview.md) avec des fonctionnalités supplémentaires pour l’hébergement de services web et toutes les [méthodes de déploiement disponibles pour les applications web](../app-service-web/web-sites-deploy.md) peuvent également être utilisées avec les applications API. L’application web qui héberge une API est appelée hôte d’application API dans le portail Azure en version préliminaire. Vous pouvez configurer le déploiement dans le panneau du portail de l’hôte d’application API. Pour plus d’informations sur le panneau d’hôte de l’application API, consultez la page [Gérer une application API](app-service-api-manage-in-portal.md).

Le fait que les applications API soient basées sur les applications web signifie également que vous pouvez déployer le code écrit pour les plateformes autres qu’ASP.NET pour les applications API. Pour obtenir un exemple qui utilise Git pour déployer le code Node.js vers une application API, consultez la page [Créer une application API Node.js dans Azure App Service](app-service-api-nodejs-api-app.md).
 
## <a id="provision"></a>Créer une application API dans Azure 

Dans cette section, vous utilisez l'Assistant **Publier le site Web** de Visual Studio pour créer une application API dans Azure.

1. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet (pas la solution), puis cliquez sur **Publier**. 

	![Option de menu Publier le projet](./media/app-service-dotnet-deploy-api-app/20-publish-gesture-v3.png)

2. Cliquez sur l’onglet **Profil** et sur **Microsoft Azure API Apps (version préliminaire)**.

	![Boîte de dialogue Publier le site web](./media/app-service-dotnet-deploy-api-app/21-select-api-apps-for-deployment-v2.png)

3. Cliquez sur **Nouveau** pour configurer une nouvelle application API dans votre abonnement Azure.

	![Boîte de dialogue de sélection des services API existants](./media/app-service-dotnet-deploy-api-app/23-publish-to-apiapps-v3.png)

4. Dans la boîte de dialogue **Créer une application API**, entrez les informations suivantes :

	- Entrez ContactsList comme **nom de l’application API**. 
	- Si vous avez plusieurs abonnements Azure, sélectionnez celui que vous souhaitez utiliser.
	- Sous **Plan App Service**, sélectionnez un des plans App Service existants ou **Créer un plan App Service** et entrez le nom d’un nouveau plan. 
	- Sous **Groupe de ressources**, sélectionnez un des groupes de ressources existants ou **Créer un groupe de ressources** et entrez un nom. Le nom doit être unique ; envisagez d'utiliser le nom de l'application en tant que préfixe et d'ajouter des informations personnelles telles que votre ID Microsoft (sans le signe @).  
	- Sous **Niveau d’accès**, sélectionnez **Accessible à tout le monde**. Cette option rendra votre API complètement publique, ce qui convient pour ce didacticiel. Vous pouvez restreindre l’accès ultérieurement via le portail Azure en version préliminaire.
	- Dans **Région**, sélectionnez une région proche de vous.  

	![Boîte de dialogue de configuration de l’application web Microsoft Azure](./media/app-service-dotnet-deploy-api-app/24-new-api-app-dialog-v3.png)

5. Cliquez sur **OK** pour créer l’application API dans votre abonnement. Comme ce processus peut prendre quelques minutes, Visual Studio affiche une boîte de dialogue de confirmation.

	![Message de confirmation de la création du service de l’API](./media/app-service-dotnet-deploy-api-app/25-api-provisioning-started-v3.png)

6. Cliquez sur **OK** dans la boîte de dialogue de confirmation. Le processus de configuration crée le groupe de ressources et l'application API dans votre abonnement Azure. Visual Studio affiche la progression dans la fenêtre **Activité d’Azure App Service**.

	![Notification d’état via la fenêtre Activité d’Azure App Service](./media/app-service-dotnet-deploy-api-app/26-provisioning-success-v3.png)

## <a id="deploy"></a>Déploiement de votre code dans la nouvelle application API

Dans cette section, vous utilisez le même Assistant **Publier le site Web** pour déployer votre code.

7. Cliquez avec le bouton droit sur le projet dans l’**Explorateur de solutions** et sélectionnez **Publier** pour rouvrir la boîte de dialogue de publication. Le profil de publication créé à l'étape précédente doit être sélectionné au préalable. Cliquez sur **Publier** pour commencer le processus de déploiement. 

	![Déploiement de l’application API](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v3.png)

	La fenêtre **Activité d’Azure App Service** affiche la progression du déploiement.

	![Notification d’état de la fenêtre Activité d’Azure App Service](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v4.png)

	Pendant ce processus de déploiement, Visual Studio redémarre automatiquement la *passerelle*. La passerelle est une application web qui gère les fonctions d'administration pour toutes les applications d'API d'un groupe de ressources, et elle doit être redémarrée pour reconnaître les modifications API effectuées dans une application API. Si vous utilisez une autre méthode pour déployer une application API, et si votre déploiement change l'API, vous devrez peut-être redémarrer manuellement la passerelle. Pour plus d'informations sur le redémarrage de la passerelle dans le portail, consultez la rubrique [Gérer une application API](app-service-api-manage-in-portal.md).

## Afficher l’application dans le portail Azure en version préliminaire

Dans cette section, vous allez accéder au portail pour afficher les paramètres de base disponibles pour les applications API et apporter des modifications itératives à votre application API. Avec chaque déploiement, le portail reflète les modifications que vous apportez à votre application API.

1. Dans votre navigateur, accédez au [portail Azure en version préliminaire](https://portal.azure.com). 

2. Cliquez sur le bouton **Parcourir** dans la barre latérale et sélectionnez **API Apps**.

	![Parcourir les options sur le portail Azure](./media/app-service-dotnet-deploy-api-app/27-browse-in-portal-v3.png)

3. Sélectionnez l’API que vous avez créée dans la liste des applications API de votre abonnement.

	![Liste des applications API](./media/app-service-dotnet-deploy-api-app/28-view-api-list-v3.png)

4. Cliquez sur **Définition d’API**. Le panneau **Définition d’API** de l’application affiche la liste des opérations sur l’API que vous avez définies quand vous avez créé l’application.

	![Définition de l’API](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

5. Maintenant, revenez au projet dans Visual Studio et ajoutez le code suivant au fichier **ContactsController.cs**. Ce code ajoute une méthode **Post** qui peut être utilisée pour publier de nouvelles instances `Contact` dans l’API.

		[HttpPost]
		public HttpResponseMessage Post([FromBody] Contact contact)
		{
			// todo: save the contact somewhere
			return Request.CreateResponse(HttpStatusCode.Created);
		}

	![Ajout de la méthode Post au contrôleur](./media/app-service-dotnet-deploy-api-app/30-post-method-added-v3.png)

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
 

<!---HONumber=62-->