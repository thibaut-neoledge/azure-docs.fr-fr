<properties
	pageTitle="Authentification utilisateur pour API Apps dans Azure App Service | Microsoft Azure"
	description="Découvrez comment protéger une application API dans Azure App Service en accordant l’accès uniquement aux utilisateurs authentifiés."
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
	ms.date="01/26/2016"
	ms.author="tdykstra"/>

# Authentification utilisateur pour API Apps dans Azure App Service

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Vue d’ensemble

Cet article portera sur les points suivants :

* La protection d’une application API App Service, afin que seuls les utilisateurs authentifiés puissent l’appeler.
* La configuration d’un fournisseur d’authentification, avec des détails destinés à Azure Active Directory (Azure AD).
* L’utilisation d’une application API protégée à l’aide d’[ADAL (Active Directory Authentication Library) pour JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js).

Cet article contient deux sections :

* La section [Configuration de l’authentification utilisateur dans Azure App Service](#authconfig) explique comment configurer l’authentification utilisateur de n’importe quelle application API. Elle s’applique également à toutes les infrastructures prises en charge par App Service, notamment .NET, Node.js et Java.

* Le [reste de l’article](#tutorialstart) vous guide dans la configuration d’un exemple d’application .NET fonctionnant dans App Service afin d’utiliser Azure Active Directory comme authentification utilisateur.

## <a id="authconfig"></a> Configuration de l’authentification utilisateur dans Azure App Service

Cette section fournit des instructions générales s’appliquant à n’importe quelle application API. Pour connaître les étapes spécifiques à l’exemple d’application .NET Ma liste des tâches, passez à la section [Suite des didacticiels de mise en route de .NET](#tutorialstart).

1. Dans le [portail Azure](https://portal.azure.com/), accédez au panneau **Application API** de l’application API que vous souhaitez protéger, puis cliquez sur **Paramètres**

2. Dans le panneau **Paramètres**, recherchez la section **Fonctionnalités**, puis cliquez sur **Authentification/Autorisation**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Dans le panneau **Authentification/Autorisation**, cliquez sur **Activé**.

4. Sélectionnez une des options de la liste déroulante **Action à exécuter quand une demande n’est pas authentifiée**.

	* Si vous souhaitez que seuls les appels authentifiés accèdent votre application API, choisissez parmi les options **Se connecter avec...**. Cette option vous permet de protéger l’application API sans écrire de code s’exécutant dans cette dernière.

	* Si vous souhaitez que tous les appels atteignent votre application API, choisissez **Autoriser les requêtes (aucune action)**. Vous pouvez utiliser cette option pour diriger les appelants non authentifiés vers une sélection de fournisseurs d’authentification. Avec cette option, vous devez écrire du code pour gérer l’autorisation.

	Pour plus d’informations, consultez la page [Authentification et autorisation pour les applications API dans Azure App Service](app-service-api-authentication.md#multiple-protection-options).

5. Sélectionnez un ou plusieurs **fournisseurs d’authentification**.

	L’image montre les options qui exigent que tous les appelants soient authentifiés par Azure AD.
 
	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

	Lorsque vous choisissez un fournisseur d’authentification, le portail affiche un panneau de configuration correspondant à ce fournisseur.

	Pour obtenir des instructions détaillées sur la procédure de configuration des panneaux de configuration du fournisseur d’authentification, consultez [Configurer votre application App Service pour utiliser la connexion Azure Active Directory](../app-service-mobile/app-service-mobile-how-to-configure-active-directory-authentication.md). (Le lien mène à un article sur Azure AD, mais l’article lui-même contient des onglets associés à des articles destinés aux autres fournisseurs d’authentification).

7. Une fois que vous en avez terminé avec le panneau de configuration du fournisseur d’authentification, cliquez sur **OK**.

7. Dans le panneau **Authentification/Autorisation**, cliquez sur **Enregistrer**.

App Service authentifie alors tous les appels API avant qu’ils atteignent l’application API. Ces services d’authentification fonctionnent de la même manière pour tous les langages pris en charge par App Service, notamment .NET, Node.js et Java.

Pour exécuter des appels d’API authentifiés, l’appelant inclut le jeton de porteur OAuth 2.0 du fournisseur d’authentification dans l’en-tête d’autorisation des demandes HTTP. Le jeton peut être acquis à l’aide du kit de développement logiciel du fournisseur d’authentification.

## <a id="tutorialstart"></a> Suite des didacticiels dédiés aux didacticiels de mise en route .NET

Si vous suivez la série dédiée à la mise en route de Node.js ou Java pour les applications API, passez à l’article suivant, intitulé [Authentification du principal du service pour les applications API](app-service-api-dotnet-service-principal-auth.md).

Si vous suivez la série Mise en route de .NET pour les applications API et si vous avez déjà déployé l’exemple d’application comme indiqué dans les [premier](app-service-api-dotnet-get-started.md) et [deuxième](app-service-api-cors-consume-javascript.md) didacticiels, passez à la section [Configurer l’authentification](#azureauth).

Si vous n’avez pas suivi les premier et deuxième didacticiels, mais que vous voulez suivre celui-ci, commencez par vous assurer que vous respectez bien les [conditions préalables requises](app-service-api-dotnet-get-started.md#prerequisites) de la série. Procédez ensuite comme suit pour télécharger et déployer l’exemple d’application. Ces étapes reprennent ce que vous aviez dans les deux premiers didacticiels, mais ici, les instructions sont résumées.

1. Téléchargez l’exemple d’application.

	a. Effectuez le téléchargement à partir du référentiel [Samples/app-service-api-dotnet-todo-list](https://github.com/Azure-Samples/app-service-api-dotnet-to-do-list).

	a. Ouvrez la solution ToDoList dans Visual Studio 2015 et générez la solution pour restaurer les packages NuGet.

2. Déployez le projet ToDoListDataAPI sur une nouvelle application API

	a. Dans le projet ToDoListDataAPI, ouvrez le fichier *App\_Start/Swaggerconfig.cs* et supprimez les commentaires du code **EnableSwaggerUi**.

	b. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet ToDoListDataAPI, puis cliquez sur **Publier**.

	c. À l’étape **Profil** de l’Assistant **Publier le site web**, cliquez sur **Microsoft Azure App Service**.

	d. Dans la boîte de dialogue **App Service** App Service, choisissez l’**Abonnement** Azure que vous souhaitez utiliser, puis cliquez sur **Nouveau**.

	e. Sous l’onglet **Hébergement** de la boîte de dialogue **Créer App Service**, cliquez sur **Modifier le type**, puis sur **Application API**.

	f. Saisissez un **Nom de l’application API** tel que ToDoListDataAPI, plus un nombre pour le rendre unique dans le domaine *azurewebsites.net*, par exemple : par exemple, ajoutez un nombre à ToDoListDataAPI1230.

	g. Dans le menu déroulant **Groupe de ressources**, entrez un nom tel que TodoListGroup pour créer un nouveau groupe de ressources.

	h. Dans le menu déroulant **Plan de service d’applications**, cliquez sur **Nouveau** et entrez les informations requises dans la boîte de dialogue **Configurer le plan App Service**.

	i. Cliquez sur **Create**.

	j. Cliquez sur **Publier**.

3. Déployez le projet ToDoListAPI sur la nouvelle application API.

	a. Dans le projet ToDoListAPI, ouvrez *Controllers\\ToDoListController.cs* et changez `http://localhost:45914` en `https://{your ToDoListDataAPI app name}.azurewebsites.net`.

	b. Pour le déploiement, suivez la procédure que vous avez utilisée pour le projet ToDoListDataAPI. N’oubliez pas de modifier le type en **Application API**.

4. Déployez le projet ToDoListAngular dans une nouvelle application web

	a. Dans le projet ToDoListAngular, ouvrez le fichier *app/scripts/todoListSvc.js*.

	b. Placez sur la ligne un commentaire définissant l’URL localhost sur `apiEndpoint`, supprimez les commentaires de la ligne qui définit une URL azurewebsites.net comme `apiEndPoint`, puis remplacez l’espace réservé par le nom réel de l’application API app déjà créée pour le projet ToDoListAPI.

	c. Pour le déploiement, suivez la procédure que vous avez utilisée pour le projet ToDoListDataAPI **sans toutefois changer le type de Web App en API App**.

5. Configurez CORS pour l’application API cible dans Azure.

	a. Rendez-vous sur le [portail Azure](https://portal.azure.com/), puis accédez à l’application API que vous avez créée pour le projet ToDoListAPI.

	b. Dans le panneau **Application API**, cliquez sur **Paramètres**.

	c. Recherchez la section **API**, puis cliquez sur **CORS**.

	d. Dans la zone de texte, entrez l’URL d’où doivent provenir les appels. Dans le présent didacticiel, il s’agit de l’URL de l’application web que vous avez créée pour le projet ToDoListAngular. Saisissez par exemple « https://todolistangular.azurewebsites.net ».

	e. Cliquez sur **Enregistrer**.

6. Ouvrez un navigateur vers l’URL HTTPS de l’application web et vérifiez que vous pouvez afficher, ajouter, modifier et supprimer des éléments d’action.

## <a id="azureauth"></a> Configurer l’authentification dans Azure

À présent, l’application s’exécute dans Azure App Service sans exiger que l’utilisateur soit authentifié. Dans cette section, vous allez ajouter l’authentification en effectuant les tâches suivantes :

* Configurez App Service pour qu’elle exige une authentification Azure Active Directory (Azure AD) pour appeler l’application API de niveau intermédiaire.
* Créez une application Azure AD.
* Configurez l’application Azure AD pour envoyer le jeton de porteur après la connexion au frontal AngularJS. 

### Configurer l’authentification dans App Service

1. Dans le [portail Azure](https://portal.azure.com/), accédez au panneau de l’**API App** que vous avez créée pour le projet ToDoListAPI.

2. Cliquez sur **Settings**.

2. Dans le panneau **Paramètres**, recherchez la section **Fonctionnalités**, puis cliquez sur **Authentification/Autorisation**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Dans le panneau **Authentification/Autorisation**, cliquez sur **Activé**.

4. Dans la liste déroulante **Action à effectuer lorsque la demande n’est pas authentifiée**, sélectionnez **Se connecter avec Azure Active Directory**.

	Cette option permet de s’assurer qu’aucune demande non authentifiée n’atteindra l’application API.

5. Sous **Fournisseurs d’authentification**, cliquez sur **Azure Active Directory**.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Dans le panneau **Paramètres Azure Active Directory**, cliquez sur **Express**

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	Avec l’option **Express**, App Service permet à Azure de créer automatiquement une application Azure AD dans le [client](https://msdn.microsoft.com/fr-FR/library/azure/jj573650.aspx#BKMK_WhatIsAnAzureADTenant).

	Vous n’avez pas à créer un client, car chaque compte Azure en possède un.

7. Sous **Mode d’administration**, cliquez sur **Créer une application AD**.

	La zone de saisie **Créer une application** du portail comporte automatiquement une valeur par défaut.
	
	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings2.png)

8. Notez la valeur contenue dans la zone de saisie **Créer une application** ; vous rechercherez plus tard cette application AAD dans le portail Azure Classic.

	Azure crée automatiquement une application Azure AD dans votre client Azure AD. Par défaut, l’application Azure AD porte le même nom que celui de l’application API. Vous pouvez, si vous le souhaitez, choisir un autre nom.
 
7. Cliquez sur **OK**.

7. Dans le panneau **Authentification/Autorisation**, cliquez sur **Enregistrer**.

Maintenant, seuls les utilisateurs de votre client Azure AD peuvent appeler l’application API.

### Facultatif : tester l’application API

1. Dans un navigateur, accédez à l’URL de l’application API : dans le panneau **Application API** dans le portail Azure, cliquez sur le lien sous **URL**.  

	Vous êtes redirigé vers un écran de connexion, car les demandes non authentifiées ne sont pas autorisées à atteindre l’application API.

	Si votre navigateur accède à la page « créé avec succès », le navigateur peut déjà être connecté. Dans ce cas, ouvrez une fenêtre InPrivate ou Incognito et accédez à l’URL de l’application API.

2. Connectez-vous avec les informations d’identification d’un utilisateur de votre client Azure AD.

	Une fois que vous êtes connecté, la page signalant que la création a réussi s’affiche dans le navigateur.

9. Fermez le navigateur.

### Configuration de l’application Azure Active Directory

Lorsque vous avez configuré l’authentification Azure AD, App Service a créé pour vous une application Azure AD. Par défaut, la nouvelle application Azure AD a été configurée pour fournir le jeton de porteur à l’URL de l’application API. Dans cette section, vous allez configurer l’application Azure AD pour fournir le jeton de porteur au frontal AngularJS plutôt que directement à l’application de niveau intermédiaire de l’application API. Le frontal AngularJS envoie le jeton à l’application API lorsqu’il appelle l’application API.

11. Dans le [portail Azure Classic](https://manage.windowsazure.com/), accédez à **Azure Active Directory**.

	Vous devez utiliser le portail classique, car certains paramètres Azure Active Directory auxquels vous devez accéder ne sont pas encore disponibles dans le portail Azure actuel.

12. Sous l’onglet **Annuaire**, cliquez sur votre client AAD.

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. Cliquez sur **Applications > Applications que ma société possède**, puis cliquez sur la coche.

	Vous devrez peut-être aussi actualiser la page pour afficher la nouvelle application.

15. Dans la liste des applications, cliquez sur le nom de celle qu’Azure a créée pour vous quand vous avez activé l’authentification pour votre application API.

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. Cliquez sur **Configurer**.

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

17. Définissez **URL de connexion** sur l’URL de votre application web AngularJS, sans barre oblique à la fin.

	Par exemple : https://todolistangular.azurewebsites.net

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

17. Définissez **URL de réponse** sur l’URL de votre application web, sans barre oblique à la fin.

	Par exemple : https://todolistsangular.azurewebsites.net

16. Cliquez sur **Save**.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

15. En bas de la page, cliquez sur **Gérer le manifeste > Télécharger le manifeste**.

	![](./media/app-service-api-dotnet-user-principal-auth/downloadmanifest.png)

17. Téléchargez le fichier à un emplacement où vous pouvez le modifier.

16. Dans le fichier manifeste téléchargé, recherchez la propriété `oauth2AllowImplicitFlow`. Modifiez la valeur de cette propriété en remplaçant `false` par `true`, puis enregistrez le fichier.

	Ce paramètre est obligatoire pour l’accès depuis une application de page JavaScript unique. Il permet au jeton de porteur Oauth 2.0 d’être renvoyé dans le fragment d’URL.

16. Cliquez sur **Gérer le manifeste > Télécharger le manifeste sur le serveur**, puis chargez le fichier mis à jour à l’étape précédente.

17. Copiez la valeur **ID Client** et enregistrez-la à un endroit où vous pourrez la récupérer par la suite.

## Configurer le projet ToDoListAngular pour utiliser l’authentification

Dans cette section, vous allez modifier le serveur frontal AngularJS afin qu’il utilise ADAL (Active Directory Authentication Library) pour que JS acquière un jeton de porteur pour l’utilisateur connecté à partir d’Azure AD. Le code inclut le jeton dans les requêtes HTTP envoyées au niveau intermédiaire, comme l’indique le diagramme suivant.

![](./media/app-service-api-dotnet-user-principal-auth/appdiagram.png)

Apportez les modifications suivantes au projet ToDoListAngular.

1. Ouvrez le fichier *index.html*.

2. Supprimez les commentaires des lignes faisant référence à la bibliothèque d’authentification Active Directory (ADAL) pour les scripts JS.

		<script src="app/scripts/adal.js"></script>
		<script src="app/scripts/adal-angular.js"></script>

1. Ouvrez le fichier *app/scripts/app.js*.

2. Supprimez le commentaire du bloc de code marqué pour « sans authentification » et supprimez le bloc de code marqué « avec authentification ».

	Cette modification fait référence au fournisseur d’authentification ADAL JS et lui fournit des valeurs de configuration. Lors des étapes suivantes, vous allez définir les valeurs de configuration pour votre application API et l’application Azure AD.

8. Dans le code qui définit la variable `endpoints`, définissez l’URL de l’API sur l’URL de l’application API créée pour le projet ToDoListAPI et définissez l’ID d’application Azure AD sur l’ID de client que vous avez copié depuis le portail Azure Classic.

	Le code ressemble maintenant à l’exemple suivant.

		var endpoints = {
		    "https://todolistapi0121.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. Dans l’appel de `adalProvider.init`, définissez `tenant` sur votre nom client et `clientId` sur la valeur que vous avez déjà utilisée à l’étape précédente.

	Le code ressemble maintenant à l’exemple suivant.

		adalProvider.init(
		    {
		        instance: 'https://login.microsoftonline.com/', 
		        tenant: 'contoso.onmicrosoft.com',
		        clientId: '1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3',
		        extraQueryParameter: 'nux=1',
		        endpoints: endpoints
		    },
		    $httpProvider
		    );

	Ces modifications de `app.js` indiquent que le code appelant et l’API appelée se trouvent dans la même application Azure AD.

1. Ouvrez le fichier *app/scripts/homeCtrl.js*.

2. Supprimez le commentaire du bloc de code marqué pour « sans authentification » et supprimez le bloc de code marqué « avec authentification ».

1. Ouvrez le fichier *app/scripts//indexCtrl.js*.

2. Supprimez le commentaire du bloc de code marqué pour « sans authentification » et supprimez le bloc de code marqué « avec authentification ».

### Déployer le projet ToDoListAngular dans Azure

8. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet ToDoListAngular, puis cliquez sur **Publier**.

9. Cliquez sur **Publier**.

	Visual Studio déploie le projet et ouvre un navigateur vers l’URL de base d’application Web.

	Vous devez toujours apporter une modification à l’application API de niveau intermédiaire avant de pouvoir tester l’application.

10. Fermez le navigateur.

## Configurer le projet ToDoListAPI pour utiliser l’authentification

Actuellement le projet ToDoListAPI envoie « * » comme valeur `owner` pour ToDoListDataAPI. Dans cette section, vous allez modifier le code pour envoyer l’ID de l’utilisateur connecté.

Apportez les modifications suivantes au projet ToDoListAPI.

1. Ouvrez le fichier *Controllers/ToDoListController.cs* et supprimez les commentaires correspondant à la ligne de chaque méthode d’action définissant `owner` sur la valeur de revendication d’Azure AD `NameIdentifier`. Par exemple :

		owner = ((ClaimsIdentity)User.Identity).FindFirst(ClaimTypes.NameIdentifier).Value;

### Déployez le projet ToDoListAPI vers Azure

8. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet ToDoListAPI, puis cliquez sur **Publier**.

9. Cliquez sur **Publier**.

	Visual Studio déploie le projet et ouvre un navigateur vers l’URL de base d’application API.

10. Fermez le navigateur.

### Test de l'application

9. Accédez à l’URL de l’application web **Utilisation de HTTPS et non de HTTP**.

8. Cliquez sur l’onglet **Liste des tâches**.

	Vous êtes invité à vous connecter.

9. Connectez-vous avec les informations d’identification d’un utilisateur dans votre client AAD.

10. La page **Liste des tâches** s’affiche.

	![](./media/app-service-api-dotnet-user-principal-auth/webappindex.png)

	Aucune tâche ne s’affiche, car jusqu’ici, elles étaient toutes pour le propriétaire « * ». Maintenant le niveau intermédiaire demande des éléments pour l’utilisateur connecté, et aucun n’a encore été créé.

11. Ajoutez de nouveaux éléments de tâche pour vérifier que l’application fonctionne.

12. Dans une autre fenêtre de navigateur, accédez à l’URL de l’interface utilisateur Swagger pour l’application API de ToDoListDataAPI, puis cliquez sur **ToDoList > Obtenir**. Entrez un astérisque en tant que paramètre `owner`, puis cliquez sur **Essayer**.

	La réponse indique que la propriété Propriétaire des nouveaux éléments de tâche contient l’ID d’utilisateur Active Directory Azure.

	![](./media/app-service-api-dotnet-user-principal-auth/todolistapiauth.png)


## Génération de projets à partir de 0

Les deux projets d’API Web ont été créés à l’aide du modèle de projet **application API Azure** et par remplacement du contrôleur de valeurs par défaut par un contrôleur ToDoList.

Pour plus d’informations sur la création d’une application à page unique AngularJS avec un Back end Web API 2, consultez [Exercice pratique : créer une application à page unique (SPA) avec l’API Web ASP.NET et Angular.js](http://www.asp.net/web-api/overview/getting-started-with-aspnet-web-api/build-a-single-page-application-spa-with-aspnet-web-api-and-angularjs). Pour plus d’informations sur l’ajout du code d’authentification Azure AD, consultez les ressources suivantes :

* [Sécurisation d’une application à page unique AngularJS avec Azure AD](../active-directory/active-directory-devquickstarts-angular.md).
* [Présentation de JS ADAL v1](http://www.cloudidentity.com/blog/2015/02/19/introducing-adal-js-v1/)

## Résolution de problèmes

Si vous exécutez correctement l’application sans authentification, puis qu’elle ne fonctionne pas lorsque vous implémentez l’authentification, la plupart du temps, le problème est dû à des paramètres de configuration erronés et incohérents. Commencez par vérifier soigneusement tous les paramètres Azure App Service et Azure Active Directory. Voici quelques suggestions spécifiques :

* Dans l’onglet Azure AD **Configurer**, vérifiez **URL de réponse**.
* Dans Azure AD, téléchargez le manifeste et assurez-vous que `oauth2AllowImplicitFlow` a été remplacé par `true`. 
* Dans le code source AngularJS, vérifiez l’URL d’application API de niveau intermédiaire et l’ID client Azure AD.
* Après avoir configuré le code dans un projet, assurez-vous que vous avez redéployé ce projet et pas un des autres.
* Assurez-vous que vous allez accéder aux URL HTTPS et non à vos URL HTTP dans votre navigateur.
* Assurez-vous que CORS (Partage des ressources cross-origin) est toujours activé sur l’application API de niveau intermédiaire, ce qui permet des appels à l’URL HTTPS frontale depuis le niveau intermédiaire. En cas de doute sur la nature du problème(s’il est lié ou non à CORS), essayez « * » en tant qu’URL d’origine autorisée.
* Assurez-vous d’obtenir le plus d’informations possible dans les messages d’erreur en définissant le [mode customErrors sur OFF](../app-service-web/web-sites-dotnet-troubleshoot-visual-studio.md#remoteview).
* L’onglet de console des outils de développement de votre navigateur comporte souvent davantage d’informations sur l’erreur, et vous pouvez examiner les requêtes HTTP sur l’onglet Réseau.

## Étapes suivantes

Ce didacticiel vous a montré comment utiliser l’authentification du Service d’application pour une application API et comment appeler l’application API à l’aide de la bibliothèque ADAL JS. Dans le didacticiel suivant, vous allez découvrir comment [sécuriser l’accès à votre application API pour les scénarios de service à service](app-service-api-dotnet-service-principal-auth.md).

<!---HONumber=AcomDC_0128_2016-->