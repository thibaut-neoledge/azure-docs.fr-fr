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
	ms.date="01/08/2016"
	ms.author="tdykstra"/>

# Authentification utilisateur pour API Apps dans Azure App Service

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Vue d’ensemble

Ce didacticiel montre comment utiliser les fonctionnalités d’authentification et d’autorisation d’Azure App Service pour protéger une application API et comment consommer l’application API au nom des utilisateurs finaux. Le fournisseur d’authentification mentionné dans ce didacticiel est Azure Active Directory, et l’API est l’API Web ASP.NET. Le client est une application AngularJS à page unique exécutée dans un navigateur.

![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)
 
## Authentification et autorisation dans App Service

Pour obtenir une présentation des fonctions utilisées dans ce didacticiel, consultez le didacticiel précédent de cette série, [Authentification et autorisation pour API Apps dans Azure App Service](app-service-api-authentication.md).

## Procédure pour suivre ce didacticiel

Ce didacticiel s’appuie sur un exemple d’application que vous téléchargez et une application API créée pendant le [premier didacticiel de la série Prise en main d’API Asp et ASP.NET](app-service-api-dotnet-get-started.md).

## Exemple de projet ContactsList.Angular.AAD

Dans l’[exemple d’application ContactsList](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list), le projet ContactList.Angular .AAD est un client AngularJS qui contient le code permettant de travailler avec Azure Active Directory. Ce code est basé sur un exemple AAD qui se trouve dans le référentiel [Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp-dotnet-webapi).

Le code du projet ContactsList.Angular.AAD est structuré différemment du projet ContactsLists.Angular plus simple. Le code qui appelle l’API se trouve dans le fichier *app/scripts/contactsSvc.js* dans le projet ContactsList.Angular.AAD.

		angular.module('contactsListApp')
		.factory('contactsSvc', ['$http', function ($http) {
		    //var apiEndpoint = "https://{your api app name}.azurewebsites.net";
		    var apiEndpoint = "https://localhost:44300";
		
		    $http.defaults.useXDomain = true;
		    delete $http.defaults.headers.common['X-Requested-With']; 
		
		    return {
		        getItems: function () {
		            return $http.get(apiEndpoint + '/api/contacts');
		        },
		        getItem : function(id){
		            return $http.get(apiEndpoint + '/api/contacts/' + id);
		        },
		        postItem : function(item){
		            return $http.post(apiEndpoint + '/api/contacts', item);
		        },
		        putItem : function(item){
		            return $http.put(apiEndpoint + '/api/contacts/', item);
		        },
		        deleteItem : function(id){
		            return $http({
		                method: 'DELETE',
		                url: apiEndpoint + '/api/contacts/' + id
		            });
		        }
		    };
		}]);

Ici, la méthode `Get` se nomme `getItems`. Dans le contrôleur (*app/scripts/contactsCtrl.js*), `getItems` est raccordé à `$scope.populate`.

		$scope.populate = function () {
		    contactsSvc.getItems().then(function (results) {
		        $scope.contactsList = results.data;
		        $scope.loadingMessage = "";
		    }, function (err) {
		        $scope.loadingMessage = "";
		        $scope.error = "Error: " + err;
		    });
		};

Dans la vue (*app/views/Contacts.html*), $scope.populate est appelée lors de l’initialisation.

		<div ng-init="populate()">

Le code supplémentaire destiné à la connexion et incluant un jeton d’autorisation avec les requêtes d’API est fourni par [Azure Active Directory Authentication Library pour JavaScript](https://github.com/AzureAD/azure-activedirectory-library-for-js), dans les fichiers *adal.js* et *adal-Angular.js*.

Dans le fichier *app.js*, le code transmet les informations de configuration et le fournisseur `$http` à la fonction `adalProvider.init`. Les informations de configuration incluent l’ID client de l’application AAD qui appartient à chaque point de terminaison d’API et l’ID client appartenant à l’application AngularJS. La fonction `init` ajoute des intercepteurs au fournisseur `$http`, qui à leur tour ajoutent le jeton d’autorisation aux demandes.

		var endpoints = { 
		    //"https://{your api app name}.azurewebsites.net/": "{your client id}"
		    "https://localhost:44300/": "{your client id}"
		};

		adalProvider.init(
		    {
		        instance: 'https://login.microsoftonline.com/', 
		        tenant: '{your tenant url}',
		        clientId: '{your client id}',
		        extraQueryParameter: 'nux=1',
		        endpoints: endpoints
		        //cacheLocation: 'localStorage', // enable this for 
		    },
		    $httpProvider
		    );

## Configurer l’authentification et l’autorisation dans Azure

1. Dans le [portail Azure](https://portal.azure.com/), accédez au panneau **API App** de l’application API que vous souhaitez protéger afin que seuls les utilisateurs authentifiés puissent l’appeler. (Pour ce didacticiel, choisissez l’application API sur laquelle vous avez déployé le projet ContactsList.API).

2. Cliquez sur **Paramètres**

2. Recherchez la section **Fonctionnalités**, puis cliquez sur **Authentification/Autorisation**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Dans le panneau **Authentification/Autorisation**, cliquez sur **Activé**.

4. Dans la liste déroulante **Action à effectuer lorsque la demande n’est pas authentifiée**, sélectionnez **Se connecter avec Azure Active Directory**.

5. Sous **Fournisseurs d’authentification**, cliquez sur **Azure Active Directory**.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Dans le panneau **Paramètres Azure Active Directory**, cliquez sur **Express**

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	Ici, le terme « Express » signifie qu’Azure crée automatiquement une application AAD dans votre client AAD. Prenez note du nom de la nouvelle application AAD, car vous le sélectionnerez quand vous accéderez au portail Azure classique pour obtenir son ID de client.

7. Cliquez sur **OK**.

10. Dans le panneau **Authentification/Autorisation**, cliquez sur **Enregistrer**.

8. Pour vérifier que l’application API est désormais protégée, accédez à l’URL de l’application API + `/swagger` comme vous l’avez fait dans le premier didacticiel pour utiliser l’interface utilisateur Swagger.

	Cette fois-ci, vous êtes redirigé vers une page d’ouverture de session.

	![](./media/app-service-api-dotnet-user-principal-auth/loginpage.png)

11. Dans le [portail Azure Classic](https://manage.windowsazure.com/), accédez à **Azure Active Directory**.

	Vous devez accéder au portail classique, car certains paramètres Azure Active Directory auxquels vous devez accéder ne sont pas encore disponibles dans le portail Azure actuel.

12. Sous l’onglet **Annuaire**, cliquez sur votre client AAD.

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. Cliquez sur **Applications > Applications que ma société possède**, puis cliquez sur la coche.

	Vous devrez peut-être aussi actualiser la page pour afficher la nouvelle application.

15. Dans la liste des applications, cliquez sur le nom de celle qu’Azure a créée pour vous quand vous avez activé l’authentification pour votre application API.

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. Cliquez sur **Configurer**.

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

15. En bas de la page, cliquez sur **Gérer le manifeste > Télécharger le manifeste**, puis enregistrez le fichier à un emplacement où vous pouvez le modifier.

16. Dans le fichier manifeste téléchargé, recherchez la propriété `oauth2AllowImplicitFlow`. Modifiez la valeur de cette propriété en remplaçant `false` par `true`, puis enregistrez le fichier.

	Ce paramètre est obligatoire pour l’accès depuis une application de page JavaScript unique. Il permet au jeton de porteur Oauth 2.0 d’être renvoyé dans le fragment d’URL.

16. Cliquez sur **Gérer le manifeste > Télécharger le manifeste sur le serveur**, puis chargez le fichier mis à jour à l’étape précédente.

17. Laissez cette page ouverte pour pouvoir y copier, coller et mettre à jour des valeurs lors des étapes suivantes du didacticiel.

## Configurer le projet ContactsList.Angular.AAD pour appeler l’application API Azure

Les instructions suivantes expliquent comment déployer l’application dans Azure et l’exécuter à cet endroit, mais avec de légères modifications, vous pouvez l’exécuter localement. L’exemple de code contient des points de terminaison URL localhost. Si vous souhaitez le faire fonctionner en local, configurez les projets pour SSL, utilisez les URL SSL localhost dans le code de projet et utilisez les URL SSL localhost dans la configuration de l’application AAD. Lorsqu’il est exécuté en local, le code AngularJS autorise uniquement les utilisateurs connectés à appeler l’API, mais les appelants non authentifiés issus d’autres clients peuvent également appeler l’API.

1. Dans le projet ContactsList.Angular.AAD, ouvrez le fichier *app/scripts/app.js*.

8. Dans le code qui définit la variable `endpoints`, commentez le point de terminaison localhost et supprimez le commentaire du point de terminaison Azure.

10. Remplacez l’ID de client « yourclientid » avec la valeur réelle de l’application AAD à partir de l’onglet **Configurer** du portail Classic de l’application AAD.

2. Remplacez « {nom de votre application api} » par le nom de l’application API sur laquelle vous avez déployé le projet ContactsList.API.

	Le code ressemble maintenant à l’exemple suivant.

		var endpoints = {
		    "https://contactslistapi.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    //"https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. Dans *app.js*, dans l’appel de `adalProvider.init`, remplacez « {votre URL cliente} » et « {votre ID client} » par les valeurs réelles.

	Le code ressemblera à l’exemple suivant :

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

1. Dans le fichier *app/scripts/contactsSvc.js*, effectuez le même changement d’URL de point de terminaison à partir de localhost vers l’URL API app que celui que vous avez fait dans *app.js*.

	Le code ressemble maintenant à l’exemple suivant.

		var apiEndpoint = "https://contactslistapi.azurewebsites.net";
		//var apiEndpoint = "https://localhost:44300";

## Configurer l’application AAD pour l’application web Azure

1. Sous l’onglet **Configurer** de l’application AAD dans le portail Classic, dans le champ **URL de connexion**, supprimez l’URL déjà en place et remplacez-la par l’URL de base de l’application web sans la barre oblique de fin. (Notez qu’il s’agit de l’URL de l’application web qui exécutera le code AngularJS, et non de l’URL de l’application API.)

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

3. Dans le champ **URL de réponse**, remplacez l’URL qui existe déjà par l’URL de base de l’application web.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

4. Cliquez sur **Enregistrer**.

## Déployer le projet ContactsList.Angular.AAD dans Azure

8. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet ContactsList.Angular.AAD, puis cliquez sur **Publier**.

9. Cliquez sur **Microsoft Azure App Service**.

10. Dans la boîte de dialogue **App Service**, dans la liste déroulante **Abonnement**, sélectionnez votre abonnement.

11. Développez le groupe de ressources que vous avez créé pour ce didacticiel et sélectionnez l’application web que vous avez créée lors du deuxième didacticiel.

	![](./media/app-service-api-dotnet-user-principal-auth/deploytowebapp.png)

12. Cliquez sur **OK**.

12. Dans l’Assistant **Publier le site web**, cliquez sur l’onglet **Connexion** puis, dans la zone **URL de Destination**, remplacez `http://` par `https://`.

	![](./media/app-service-api-dotnet-user-principal-auth/httpsinconntab.png)

	Ce paramètre détermine sur quelle page l’URL ouvrira l’explorateur par défaut après un déploiement réussi.

12. Dans l’Assistant **Publier le site web**, cliquez sur l’onglet **Paramètres**, développez **Options de publication du fichier**, puis cochez la case **Supprimer les fichiers supplémentaires de la destination**.

7. Cliquez sur **Publier**.

	Visual Studio déploie le projet et ouvre un navigateur à la page d’accueil de l’application.

## Tester l’application web AngularJS dans Azure

8. Cliquez sur l’onglet **Contacts**.

	Vous êtes invité à vous connecter.

9. Connectez-vous avec les informations d’identification d’un utilisateur dans votre client AAD.

10. La page **Contacts** s’affiche.

	![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)

Le programme frontal peut désormais appeler l’API pour le compte d’un utilisateur authentifié, mais les utilisateurs non authentifiés ne peuvent pas appeler l’API.

## Étapes suivantes

Dans ce didacticiel, vous avez utilisé l’authentification/autorisation App Service pour restreindre l’accès à une application API pour que seuls les utilisateurs authentifiés puissent l’appeler. Dans le didacticiel suivant de la série, vous allez découvrir comment [restreindre l’accès à votre application API pour les scénarios de service à service](app-service-api-dotnet-service-principal-auth.md).

<!---HONumber=AcomDC_0114_2016-->