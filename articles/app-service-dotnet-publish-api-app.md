<properties 
	pageTitle="Déployer une application API dans Azure App Service" 
	description="Découvrez comment déployer un projet d'application API dans votre abonnement Azure." 
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

# Déployer une application API dans Azure App Service

## Vue d'ensemble

Si vous développez activement votre propre application API à l'aide de Visual Studio et que vous devez tester votre API dans le cloud, vous pouvez créer une application API dans votre abonnement Azure et déployer votre code à l'aide des fonctionnalités de déploiement App Service pratiques de Visual Studio.

Ce didacticiel est le deuxième d'une série de trois :

1. Dans [Créer une application API](app-service-dotnet-create-api-app.md), vous avez créé un projet d’application API. 
* Dans ce didacticiel, vous allez déployer l'application API dans votre abonnement Azure.
* Dans [Déboguer une application API](app-service-dotnet-remotely-debug-api-app.md), vous allez utiliser Visual Studio pour déboguer à distance le code pendant son exécution dans Azure.

## Déployer l'application API 

Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet (pas la solution), puis cliquez sur **Publier**.

![Option de menu Publier le projet](./media/app-service-dotnet-deploy-api-app/20-publish-gesture-v3.png)

Cliquez sur l’onglet **Profil** et sur **Microsoft Azure API Apps (version préliminaire)**.

![Boîte de dialogue Publier le site web](./media/app-service-dotnet-deploy-api-app/21-select-api-apps-for-deployment-v2.png)

Cliquez sur **Nouveau** pour configurer une nouvelle application API dans votre abonnement Azure.

![Boîte de dialogue de sélection des services API existants](./media/app-service-dotnet-deploy-api-app/23-publish-to-apiapps-v3.png)

Dans la boîte de dialogue **Créer une application API**, entrez les informations suivantes :

- Sous **Nom de l’application API**, entrez un nom pour l’application. 
- Si vous avez plusieurs abonnements Azure, choisissez celui à utiliser. Sous Plan App Service, sélectionnez un des plans App Service existants ou **Créer un plan App Service** et entrez le nom d’un nouveau plan. 
- Sous **Groupe de ressources**, sélectionnez un des groupes de ressources existants ou **Créer un groupe de ressources** et entrez un nom. Le nom doit être unique ; envisagez d'utiliser le nom de l'application en tant que préfixe et d'ajouter des informations personnelles telles que votre ID Microsoft (sans le signe @).  
- Sous **Niveau d’accès**, sélectionnez **Accessible à tout le monde**. Cette option rendra votre API complètement publique, ce qui convient pour ce didacticiel. Vous pouvez restreindre l'accès ultérieurement via le portail Azure.
- Sélectionnez une région.  

![Boîte de dialogue de configuration de l’application web Microsoft Azure](./media/app-service-dotnet-deploy-api-app/24-new-api-app-dialog-v3.png)

Cliquez sur **OK** pour créer l’application API dans votre abonnement. Comme le processus peut prendre quelques minutes, Visual Studio affiche une boîte de dialogue vous informant que le processus a commencé.

![Message de confirmation de la création du service de l’API](./media/app-service-dotnet-deploy-api-app/25-api-provisioning-started-v3.png)

Le processus de configuration crée le groupe de ressources et l'application API dans votre abonnement Azure. Visual Studio affiche la progression dans la fenêtre **Activité d’Azure App Service**.

![Notification d’état via la fenêtre Activité d’Azure App Service](./media/app-service-dotnet-deploy-api-app/26-provisioning-success-v3.png)

Une fois que l’application API est configurée, cliquez avec le bouton droit sur le projet dans l’Explorateur de solutions et sélectionnez **Publier** pour rouvrir la boîte de dialogue de publication. Le profil de publication créé à l'étape précédente doit être sélectionné au préalable. Cliquez sur **Publier** pour commencer le processus de déploiement.

![Déploiement de l’application API](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v3.png)

La fenêtre **Activité d’Azure App Service** affiche la progression du déploiement.

![Notification d’état de la fenêtre Activité d’Azure App Service](./media/app-service-dotnet-deploy-api-app/26-5-deployment-success-v4.png)

## Afficher l'application dans le portail Azure

Dans cette section, vous allez accéder au portail pour afficher les paramètres de base disponibles pour les applications API et apporter des modifications itératives à votre application API. Avec chaque déploiement, le portail reflète les modifications que vous apportez à votre application API.

Dans votre navigateur, accédez au [portail Azure](https://portal.azure.com).

Cliquez sur le bouton **Parcourir** dans la barre latérale et sélectionnez **API Apps**.

![Parcourir les options sur le portail Azure](./media/app-service-dotnet-deploy-api-app/27-browse-in-portal-v3.png)

Sélectionnez l'API que vous avez créée dans la liste des applications API dans votre abonnement.

![Liste des applications API](./media/app-service-dotnet-deploy-api-app/28-view-api-list-v3.png)

Cliquez sur **Définition d’API**. Le panneau **Définition d’API** de l’application affiche la liste des opérations sur l’API que vous avez définies quand vous avez créé l’application.

![Définition de l’API](./media/app-service-dotnet-deploy-api-app/29-api-definition-v3.png)

Retournez à présent dans le projet dans Visual Studio. Ajoutez le code suivant au fichier **ContactsController.cs**.

	[HttpPost]
	public HttpResponseMessage Post([FromBody] Contact contact)
	{
		// todo: save the contact somewhere
		return Request.CreateResponse(HttpStatusCode.Created);
	}

Ce code ajoute une méthode **Post** qui peut être utilisée pour publier de nouvelles instances `Contact` dans l’API.

![Ajout de la méthode Post au contrôleur](./media/app-service-dotnet-deploy-api-app/30-post-method-added-v3.png)

Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet, puis sélectionnez **Publier**.

![Menu contextuel de publication du projet](./media/app-service-dotnet-deploy-api-app/31-publish-gesture-v3.png)

Sélectionnez la configuration **Déboguer** dans la liste déroulante **Configuration** et cliquez sur **Publier** pour redéployer l’application API.

![Paramètres Publier le site web](./media/app-service-dotnet-deploy-api-app/36.5-select-debug-option-v3.png)

Sous l’onglet **Aperçu** de l’Assistant **Publier le site web**, cliquez sur **Publier**.

![Boîte de dialogue Publier le site web](./media/app-service-dotnet-deploy-api-app/39-re-publish-preview-step-v2.png)

Une fois le processus de publication terminé, revenez au portail, puis fermez et rouvrez le panneau **Définition d’API**. Vous verrez le nouveau point de terminaison d'API que vous venez de créer et de déployer directement dans votre abonnement Azure.

![Définition de l’API](./media/app-service-dotnet-deploy-api-app/38-portal-with-post-method-v4.png)

## Étapes suivantes

Vous avez vu comment les fonctionnalités de déploiement direct dans Visual Studio facilitent l'itération et le déploiement rapide de votre API ainsi que le test permettant de vérifier qu'elle fonctionne correctement. Dans le [didacticiel suivant](app-service-dotnet-remotely-debug-api-app.md), vous verrez comment déboguer votre application API pendant son exécution dans Azure.



<!--HONumber=54-->