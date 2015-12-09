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
	ms.date="11/30/2015"
	ms.author="tdykstra"/>

# Authentification utilisateur pour API Apps dans Azure App Service

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Vue d’ensemble

Ce didacticiel montre comment utiliser les fonctionnalités d’authentification et d’autorisation d’Azure App Service pour protéger une application API et comment consommer l’application API au nom des utilisateurs finaux. Le fournisseur d’authentification illustré dans ce didacticiel est Azure Active Directory, et l’exemple de client est une application AngularJS à page unique exécutée dans un navigateur.

![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)
 
Il s’agit du troisième d’une série de didacticiels qui montrent comment utiliser des applications API dans Azure App Service. Pour plus d’informations sur la série, consultez [Prise en main d’API Apps et d’ASP.NET dans Azure App Service](app-service-api-dotnet-get-started.md).

## Authentification et autorisation dans Azure App Service

Azure App Service offre des services d’authentification et d’autorisation intégrés. Vous pouvez gérer l’authentification dans votre propre code, mais App Service offre une solution clés en main si vous souhaitez réduire la quantité de code à écrire et à tenir à jour.

Vous avez le choix entre cinq fournisseurs d’authentification pris en charge par App Service : Azure Active Directory, Facebook, Google, Twitter et Compte Microsoft. Vous pouvez protéger une API écrite dans n’importe quel langage pris en charge par App Service : sans écrire de code dans votre API, vous pouvez exiger la connexion de l’utilisateur ou un jeton d’accès.

Azure App Service gère l’authentification et laisse à votre code la gestion de l’autorisation. Vous pouvez configurer App Service pour autoriser uniquement les utilisateurs authentifiés à appeler votre API, ou vous pouvez autoriser tous les appelants. Dans les deux cas, App Service transmet les informations d’authentification à votre application dans les en-têtes HTTP, et votre code peut utiliser ces informations pour effectuer des choix d’autorisation.

* Dans une API .NET, vous pouvez utiliser l’attribut `Authorize` et, pour une autorisation affinée, vous pouvez facilement écrire du code basé sur des revendications. Les informations de revendications sont renseignées pour vous dans des classes .NET.

* Pour les API écrites dans d’autres langages, App Service transmet le jeton JWT dans l’en-tête d’autorisation d’une requête HTTP. En outre, Azure définit certains en-têtes spéciaux (par exemple `x-ms-client-principal-id`) pour faciliter l’accès aux revendications les plus importantes.

Pour plus d’informations sur les services d’authentification et d’autorisation dans Azure App Service, consultez [Extension de l’authentification/autorisation App Service](/blog/announcing-app-service-authentication-authorization/) et [App Service API Apps : les nouveautés](app-service-api-whats-changed.md).

## Exemple de projet ContactsList.Angular.AAD

Dans ce didacticiel, vous allez utiliser les exemples de projets que vous avez téléchargés et les ressources Azure (application API et application web) que vous avez créées dans le [premier didacticiel de cette série](app-service-api-dotnet-get-started.md).

Le projet ContactsList.Angular.AAD est un client AngularJS qui comprend du code pour utiliser Azure Active Directory. Ce code est basé sur un exemple AAD qui se trouve dans le dépôt [Azure-Samples/active-directory-angularjs-singlepageapp](https://github.com/Azure-Samples/active-directory-angularjs-singlepageapp).

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

Ici, la méthode `Get` se nomme `getItems` et, dans le contrôleur (*app/scripts/contactsCtrl.js*), `getItems` est raccordée à `$scope.populate`.

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

## Configurer l’authentification et l’autorisation dans Azure

1. Dans le [portail Azure](https://portal.azure.com/), accédez au panneau **Application API** de l’application API que vous avez créée dans le premier didacticiel, puis cliquez sur **Paramètres**.

2. Recherchez la section **Fonctionnalités**, puis cliquez sur **Authentification / Autorisation**.

	![](./media/app-service-api-dotnet-user-principal-auth/features.png)

3. Dans le panneau **Authentification / Autorisation**, cliquez sur **Activé**.

4. Dans la liste déroulante **Action à effectuer lorsque la demande n’est pas authentifiée**, sélectionnez **Se connecter avec Azure Active Directory**.

5. Sous **Fournisseurs d’authentification**, cliquez sur **Azure Active Directory**.

	![](./media/app-service-api-dotnet-user-principal-auth/authblade.png)

6. Dans le panneau **Paramètres Azure Active Directory**, cliquez sur **Express**.

	![](./media/app-service-api-dotnet-user-principal-auth/aadsettings.png)

	Azure crée automatiquement une application AAD dans votre client AAD. Prenez note du nom de la nouvelle application AAD, car vous le sélectionnerez quand vous accéderez au portail Azure classique pour obtenir son ID de client.

7. Cliquez sur **OK**.

10. Dans le panneau **Authentification / Autorisation**, cliquez sur **Enregistrer**.

8. Pour vérifier que l’application API est désormais protégée, accédez à l’URL de l’application API + `/swagger` comme vous l’avez fait dans le premier didacticiel pour utiliser l’interface utilisateur Swagger.

	Cette fois-ci, vous êtes redirigé vers une page d’ouverture de session.

	![](./media/app-service-api-dotnet-user-principal-auth/loginpage.png)

11. Dans le [portail Azure Classic](https://manage.windowsazure.com/), accédez à **Azure Active Directory**.

12. Sous l’onglet Annuaire, cliquez sur votre client AAD.

	![](./media/app-service-api-dotnet-user-principal-auth/selecttenant.png)

14. Cliquez sur **Applications > Applications que ma société possède**, puis cochez la case.

	Vous devrez peut-être aussi actualiser la page pour afficher la nouvelle application.

15. Dans la liste des applications, cliquez sur le nom de celle qu’Azure a créée pour vous quand vous avez activé l’authentification pour votre application API.

	![](./media/app-service-api-dotnet-user-principal-auth/appstab.png)

16. Cliquez sur **Configurer**.

	![](./media/app-service-api-dotnet-user-principal-auth/configure.png)

15. En bas de la page, cliquez sur **Gérer le manifeste > Télécharger le manifeste**, puis enregistrez le fichier à un emplacement où vous pouvez le modifier.

16. Dans le fichier manifeste téléchargé, recherchez la propriété `oauth2AllowImplicitFlow`. Modifiez la valeur de cette propriété en remplaçant `false` par `true`, puis enregistrez le fichier.

16. Cliquez sur **Gérer le manifeste > Télécharger le manifeste sur le serveur**, puis chargez le fichier mis à jour à l’étape précédente.

17. Laissez cette page ouverte pour pouvoir y copier, coller et mettre à jour des valeurs lors des étapes suivantes du didacticiel.

## Configurer les projets Visual Studio pour le protocole HTTPS

1. Dans l’**Explorateur de solutions**, cliquez sur le projet ContactList.API puis, dans la fenêtre **Propriétés**, affectez la valeur **True** à **SSL activé**.

2. Copiez l’URL SSL.

	![](./media/app-service-api-dotnet-user-principal-auth/enablessl.png)

3. Cliquez avec le bouton droit sur le projet ContactsList.API, puis cliquez sur **Propriétés**.

5. Cliquez sur l’onglet **Web**, collez l’URL SSL dans le champ **URL du projet**, puis enregistrez vos modifications.

	![](./media/app-service-api-dotnet-user-principal-auth/setprojecturl.png)

1. Suivez la même procédure pour activer SSL pour le projet ContactsList.Angular.AAD.

2. Définissez les projets ContactsList.API et ContactsList.Angular.AAD comme projets de démarrage, et définissez le projet ContactsList.API pour qu’il démarre en premier.

## Mettre à jour les paramètres AAD et l’URL de point de terminaison dans le projet ContactsList.Angular.AAD

7. Dans le projet ContactsList.Angular.AAD, ouvrez le fichier *app/scripts/app.js*.

8. Dans le code qui définit la variable `endpoints`, assurez-vous que l’URL SSL correcte soit définie pour le projet ContactsList.API et remplacez les deux instances de « yourclientid » par la valeur réelle de l’ID client de l’application AAD mentionnée sous l’onglet **Configurer** du portail Classic pour l’application AAD. Assurez-vous que l’URL de point de terminaison se termine par une barre oblique.

	Le code ressemblera à l’exemple suivant :

		var endpoints = {
		    //"https://{your api app name}.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    "https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

9. Également dans *app.js*, dans le code `adalProvider.init`, remplacez « {your tenant url} » et « {your client id} » par les valeurs réelles.

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

10. Dans *app/scripts/contactsSvc.js*, assurez-vous que l’URL SSL correcte soit définie pour apiEndpoint pour le projet ContactsList.API.

		//var apiEndpoint = "https://{your api app name}.azurewebsites.net";
		var apiEndpoint = "https://localhost:44300";

## Mettre à jour les paramètres AAD dans le projet ContactsList.API

1. Dans le projet ContactsList.API, ouvrez le fichier *Web.config* de l’application.

2. Dans l’élément appSettings, affectez à ida:Authority la valeur « https://login.windows.net/{URL\_de\_votre\_client} » et affectez à ida:ClientId l’ID client de votre application AAD, comme illustré dans l’exemple suivant.

		<appSettings>
		  <add key="ida:Authority" value="https://login.windows.net/contoso.onmicrosoft.com" />
		  <add key="ida:ClientId" value="1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3" />
		</appSettings>

## Configurer l’application AAD pour localhost 

1. Sous l’onglet **Configurer** de l’application AAD dans le portail Classic, dans le champ **URL de connexion**, collez l’URL SSL du projet ContactsList.Angular.AAD et supprimez la barre oblique de fin.

	![](./media/app-service-api-dotnet-user-principal-auth/signonurl.png)

3. Vers le bas de l’onglet **Configurer**, dans le champ **URL de réponse**, collez l’URL SSL du projet ContactsList.Angular.AAD, en remplaçant la valeur existante et en conservant la barre oblique.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurl.png)

4. Cliquez sur **Enregistrer**.

## Exécuter les projets d’API et clients localement

Lors de l’exécution locale de HTTPS, vous recevrez des messages d’avertissement concernant le certificat SSL IIS Express pour localhost. Pour exécuter localement, vous pouvez ignorer ces messages. Si vous préférez, vous pouvez ignorer cette section et passer directement à la suivante, où vous préparez l’exécution de l’application et de l’API dans Azure.

Si vous avez des difficultés à vous connecter, essayez d’utiliser un autre navigateur ou d’ouvrir une fenêtre Incognito ou InPrivate à l’URL du projet AngularJS, par exemple `https://localhost:44301`.

5. Dans Visual Studio, appuyez sur F5 pour exécuter les projets d’API et AngularJS localement.

	L’onglet d’accueil de l’application AngularJS s’affiche.

10. Cliquez sur l’onglet **Connexion**.

	Vous êtes invité à vous connecter.

7. Connectez-vous avec les informations d’identification d’un utilisateur dans votre client AAD.

10. Cliquez sur l’onglet **Contacts**.

	La page **Contacts** s’affiche.

	![](./media/app-service-api-dotnet-user-principal-auth/contactspagelocal.png)

11. Fermez les fenêtres du navigateur.

Vous devez être connecté pour que le code du projet ContactsList.Angular.AAD appelle l’API et affiche les contacts. Toutefois, rien n’empêche les appelants non authentifiés d’appeler l’API. Vous pouvez vérifier cela en exécutant l’interface utilisateur Swagger dans la fenêtre de navigateur qui affiche l’URL SSL du projet ContactsList.API. L’API est protégée des appelants non authentifiés uniquement lorsqu’elle s’exécute dans Azure App Service.

Dans les sections suivantes, vous allez configurer les projets et AAD pour exécuter le client et l’API dans Azure App Service, déployer les projets vers Azure et les tester dans Azure.

## Configurer le projet ContactsList.Angular.AAD pour appeler l’application API Azure

1. Dans le projet ContactsList.Angular.AAD, ouvrez le fichier *app/scripts/app.js*.

2. Commentez le point de terminaison localhost, supprimez les marques de commentaire du point de terminaison Azure et remplacez « {your api app name} » par le nom de votre application API.

	Le code ressemble maintenant à l’exemple suivant.

		var endpoints = {
		    "https://contactslistapi.azurewebsites.net/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		    //"https://localhost:44300/": "1cf55bc9-9ed8-4df31cf55bc9-9ed8-4df3"
		};

1. Effectuez la même modification d’URL de point de terminaison dans le fichier *app/scripts/contactsSvc.js*.

	Le code ressemble maintenant à l’exemple suivant.

		var apiEndpoint = "https://contactslistapi.azurewebsites.net";
		//var apiEndpoint = "https://localhost:44300";


## Configurer l’application AAD pour l’application web Azure

1. Sous l’onglet **Configurer** de l’application AAD dans le portail Classic, dans le champ **URL de connexion**, supprimez l’URL SSL du projet ContactsList.Angular.AAD et remplacez-la par l’URL de base de l’application web sans la barre oblique de fin. (Notez qu’il s’agit de l’URL de l’application web, et non de celle de l’application API.)

	![](./media/app-service-api-dotnet-user-principal-auth/signonurlazure.png)

3. Dans le champ **URL de réponse**, remplacez l’URL SSL du projet ContactsList.Angular.AAD par l’URL de base de l’application web, en conservant la barre oblique de fin.

	![](./media/app-service-api-dotnet-user-principal-auth/replyurlazure.png)

4. Cliquez sur **Enregistrer**.

## Déployer le projet ContactsList.API dans Azure

8. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet ContactsList.API, puis cliquez sur **Publier**.

	L’Assistant **Publier le site web** s’ouvre et affiche le dernier profil utilisé pour ce projet, pour le déploiement vers l’application API créé précédemment.

7. Cliquez sur **Publier**.

8. Fermez la fenêtre de navigateur qui s’ouvre automatiquement.

## Déployer le projet ContactsList.Angular.AAD dans Azure

8. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet ContactsList.Angular.API, puis cliquez sur **Publier**.

9. Cliquez sur **Microsoft Azure App Service**.

10. Dans la boîte de dialogue **App Service**, dans la liste déroulante **Abonnement**, sélectionnez votre abonnement.

11. Développez le groupe de ressources que vous avez créé pour ce didacticiel et sélectionnez l’application web que vous avez créée lors du deuxième didacticiel.

	![](./media/app-service-api-dotnet-user-principal-auth/deploytowebapp.png)

12. Cliquez sur **OK**.

12. Dans l’Assistant **Publier le site web**, cliquez sur l’onglet **Connexion** puis, dans la zone **URL de Destination**, remplacez `http://` par `https://`.

	![](./media/app-service-api-dotnet-user-principal-auth/httpsinconntab.png)

12. Dans l’Assistant **Publier le site web**, cliquez sur l’onglet **Paramètres**, développez **Options de publication du fichier**, puis cochez la case **Supprimer les fichiers supplémentaires de la destination**.

7. Cliquez sur **Publier**.

	Visual Studio déploie le projet et ouvre un navigateur à la page d’accueil de l’application.

## Tester l’application web AngularJS dans Azure

8. Cliquez sur l’onglet **Contacts**.

	Vous êtes invité à vous connecter.

9. Connectez-vous avec les informations d’identification d’un utilisateur dans votre client AAD.

10. La page **Contacts** s’affiche.

	![](./media/app-service-api-dotnet-user-principal-auth/contactspageazure.png)

11. Pour vérifier que l’application API est protégée, accédez à l’URL de l’interface utilisateur Swagger dans une fenêtre de navigateur InPrivate ou Incognito.

	Vous êtes redirigé vers une page d’ouverture de session.

	Le programme frontal peut désormais appeler l’API pour le compte d’un utilisateur authentifié, mais les utilisateurs non authentifiés ne peuvent pas appeler l’API.

## Étapes suivantes

Dans ce didacticiel, vous avez utilisé l’authentification/autorisation App Service pour restreindre l’accès à une application API pour que seuls les utilisateurs authentifiés puissent l’appeler. Dans le didacticiel suivant de la série, vous allez découvrir comment [restreindre l’accès à votre application API pour les scénarios de service à service](app-service-api-dotnet-service-principal-auth.md).

<!---HONumber=AcomDC_1203_2015-->