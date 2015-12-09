<properties
	pageTitle="Consommer une application API à partir de JavaScript à l’aide de CORS | Microsoft Azure"
	description="Découvrez comment consommer une application API dans Azure App Service, à partir d’un client JavaScript et à l’aide de CORS."
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
	ms.topic="get-started-article"
	ms.date="11/27/2015"
	ms.author="tdykstra"/>

# Consommer une application API à partir de JavaScript à l’aide de CORS

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Vue d’ensemble

Ce didacticiel montre comment consommer une application API à partir de code JavaScript dans un site web fourni à partir d’un domaine autre que celui de l’application API. L’exemple de client utilise AngularJS.

![](./media/app-service-api-cors-consume-javascript/homepageazure.png)
 
Il s’agit du deuxième d’une série de didacticiels qui montrent comment utiliser des applications API dans Azure App Service. Pour plus d’informations sur la série, consultez [Prise en main d’API Apps et d’ASP.NET dans Azure App Service](app-service-api-dotnet-get-started.md).

## Présentation de CORS

Pour des raisons de sécurité, le comportement par défaut des navigateurs empêche JavaScript d’effectuer des appels d’API vers un domaine autre que celui d’où provient le code JavaScript. Par exemple, vous pouvez effectuer un appel à partir d’une page web de contoso.com vers un point de terminaison d’API contoso.com, mais pas vers un point de terminaison fabrikam.com. CORS (Cross Origin Resource Sharing) est un protocole Internet qui autorise les scénarios où vous devez effectuer ces appels d’API entre domaines. Dans Azure App Service, cette situation se présente par exemple quand votre client JavaScript s’exécute dans une application web tandis que votre API s’exécute dans une application API.

Dans un projet d’API web, vous pouvez installer des packages NuGet CORS qui permettent de spécifier dans le code les domaines à partir desquels votre API accepte des appels JavaScript. Vous pouvez aussi utiliser la fonctionnalité CORS intégrée à Azure App Service pour spécifier les domaines à partir desquels votre application API accepte des appels. La première partie de ce didacticiel montre comment utiliser la fonctionnalité Azure, qui peut être utilisée pour tous les langages pris en charge par le service API Apps. La deuxième partie est facultative et illustre la méthode de package NuGet qui fonctionne avec l’API web ASP.NET.

## Exemple de projet ContactsList.Angular

Dans ce didacticiel, vous allez utiliser les exemples de projets que vous avez téléchargés et les ressources Azure (application API et application web) que vous avez créées dans le premier didacticiel de cette série.

Le projet ContactsList.Angular est un client AngularJS simple pour le projet d’API web ContactsList.API. Le code JavaScript AngularJS qui appelle l’API se trouve dans le fichier *index.html* du projet ContactsList.Angular. Le code définit les fonctions et les ajoute à l’objet `$scope`, comme illustré ici, où la méthode Get de l’API est définie comme `$scope.refresh()`.

		angular.module('myApp', []).controller('contactListCtrl', function ($scope, $http) {
		    $scope.baseurl = 'http://localhost:51864';
		
		    $scope.refresh = function () {
		        $scope.status = "Refreshing Contacts...";
		        $http({
		            method: 'GET',
		            url: $scope.baseUrl + '/api/contacts',
		            headers: {
		                'Content-Type': 'application/json'
		            }
		        }).then(function (results) {
		            $scope.contacts = results.data;
		            $scope.status = "Contacts loaded";
		        }, function (err) {
		            $scope.status = "Error loading contacts";
		        });
		    };
		
		    // POST and DELETE not shown
		
		    $scope.refresh();
		});

Le code appelle la méthode $scope.refresh() lorsque la page se charge (à la fin de l’extrait de code ci-dessus) et est associé au bouton **Actualiser** dans l’interface utilisateur.

		<th><button class="btn btn-sm btn-info" ng-click="refresh()">Refresh</button></th>

## Exécution locale du projet AngularJS

Dans cette section, vous allez vérifier que vous pouvez exécuter le client localement et que vous pouvez appeler l’API pendant qu’elle s’exécute localement.

1. Définissez les projets ContactsList.API et ContactsList.Angular comme projets de démarrage, ContactsList.API démarrant avant ContactsList.Angular. 

2. Appuyez sur F5 pour démarrer les projets.

	L’interface utilisateur AngularJS affiche les contacts stockés localement. Vous pouvez l’utiliser pour ajouter et supprimer des contacts.

	![](./media/app-service-api-cors-consume-javascript/homepagelocal.png)

3. Fermez les fenêtres du navigateur.

## Modifier le projet AngularJS pour qu’il pointe vers l’application API Azure 

Maintenant, vous allez exécuter le programme frontal AngularJS dans le cloud et appeler l’API principale qui s’exécute dans le cloud. Avant de déployer le programme frontal dans Azure, vous devez modifier le point de terminaison d’API dans le projet AngularJS pour que le code appelle l’application API Azure que vous avez créée.

1. Dans le projet ContactsList.Angular, ouvrez *index.html*.

2. Placez un commentaire sur la ligne qui définit l’URL localhost comme `baseUrl`, supprimez les commentaires de la ligne qui définit une URL azurewebsites.net comme `baseUrl`, puis remplacez l’espace réservé par le nom réel de l’application API déjà créée. Si vous avez nommé l’application API ContactsListAPI, le code ressemble à l’exemple suivant.

		$scope.baseUrl = 'https://ContactsListAPI.azurewebsites.net';
		//$scope.baseUrl = 'http://localhost:51864';

### Déployer le projet ContactsList.Angular dans l’application web

Vous pourriez créer une application web pour déployer le projet AngularJS, mais dans ce didacticiel, vous allez effectuer le déploiement dans l’application web déjà créée. Le nom de l’application web indique peut-être que vous y avez initialement déployé un projet ASP.NET MVC, mais après ce déploiement, elle exécutera le code AngularJS.

8. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet ContactsList.Angular, puis cliquez sur **Publier**.

9. Cliquez sur l’onglet **Profil**.

3.  À l’étape **Profil** de l’Assistant **Publier le site web**, cliquez sur **Microsoft Azure App Service**.

4. Dans la boîte de dialogue **App Service**, sélectionnez votre abonnement.

5. Avec **Affichage** défini sur la valeur par défaut **Groupe de ressources**, développez le groupe de ressources que vous avez créé pour cette série de didacticiels.

7. Sélectionnez l’application web que vous avez créée dans le premier didacticiel (veillez à ne pas sélectionner l’application API), puis cliquez sur **OK**.

8. Cliquez sur l’onglet **Paramètres**.

9. Développez **Options de publication du fichier**, puis sélectionnez **Supprimer les fichiers supplémentaires de la destination**.

	![](./media/app-service-api-cors-consume-javascript/removeadditionalfiles.png)

	Normalement, lorsque vous déployez un projet web dans une application web App Service existante, vous devez désactiver l’option « Supprimer les fichiers supplémentaires », car toutes les modifications sont généralement des mises à jour ou de nouveaux fichiers. Ici, vous déployez un projet différent vers la même application web. De nombreux fichiers du déploiement précédent ne seront sans doute pas nécessaires dans le nouveau déploiement.

10. Cliquez sur **Publier**.

	Visual Studio déploie le projet ContactsList.Angular dans l’application web et ouvre un navigateur à l’URL de l’application web. Le navigateur affiche la même interface utilisateur AngularJS que celle que vous avez vue exécutée localement, mais maintenant une erreur se produit car votre programme frontal s’exécute dans un domaine différent (l’URL de l’application web) de celui du programme principal (l’URL de l’application API).

	![](./media/app-service-api-cors-consume-javascript/corserror.png)

## Configurer CORS pour l’application API cible dans Azure

8. Dans une autre fenêtre de navigateur, accédez au [Portail Azure](https://portal.azure.com/).

9. Accédez au panneau Application API de l’application API créée dans le premier didacticiel.

10. Cliquez sur **Paramètres**.

11. Recherchez la section **API**, puis cliquez sur **CORS**.

12. Dans la zone de texte, entrez l’URL de l’application web créée dans le premier didacticiel pour le programme frontal ASP.NET MVC. Par exemple, si vous avez nommé l’application web ContactsListMVC, entrez « http://contactslistmvc.azurewebsites.net ».

	Notez que vous pouvez entrer un astérisque (*) à la place d’une URL pour indiquer que tous les domaines d’origine sont acceptés.

13. Cliquez sur **Enregistrer**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

14. Revenez à la fenêtre de navigateur qui affiche le client AngularJS en cours d’exécution dans l’application web Azure, puis actualisez la page ou cliquez sur le bouton **Actualiser**.

	La page affiche maintenant les contacts qui sont stockés dans le système de fichiers de l’application API Azure.

	![](./media/app-service-api-cors-consume-javascript/homepageazure.png)

## Configurer CORS pour l’application API cible dans le code API Web

Si vous ne souhaitez pas utiliser la prise en charge CORS d’Azure App Service, vous avez la possibilité d’activer CORS dans votre code API Web ASP.NET. Ce processus est documenté en détail dans l’article [Activation des demandes multi-origines dans API Web ASP.NET 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api). Pour les applications API créées à l’aide d’API Web ASP.NET, le processus est exactement le même, mais il est résumé ici.

Dans cette section, vous allez désactiver la prise en charge de CORS dans Azure App Service, puis activer la prise en charge de CORS dans API Web. Notez que la désactivation de la prise en charge de CORS dans Azure n’est pas une étape facultative avant l’activation de la prise en charge dans API Web. Si vous appliquez les deux méthodes, CORS dans Azure est prioritaire et CORS dans API Web n’a aucun effet. Par exemple, si vous activez un domaine d’origine dans Azure et que vous activez tous les domaines d’origine dans votre code API Web, votre application API Azure accepte uniquement les appels en provenance du domaine que vous avez spécifié dans Azure.

### Désactiver la prise en charge de CORS dans Azure

1. Pour désactiver la prise en charge de CORS dans Azure, revenez au portail Azure et effacez l’URL que vous avez entrée dans le panneau CORS, puis cliquez sur **Enregistrer**.
 
3.  Revenez à l’URL de l’application web dans laquelle vous avez déployé l’application AngularJS, puis actualisez la page ou cliquez sur le bouton **Actualiser**.

	Le message d’erreur de chargement des contacts s’affiche à nouveau.

	![](./media/app-service-api-cors-consume-javascript/corserror.png)

### Activer la prise en charge de CORS dans API Web

La fonctionnalité CORS pour API Web est fournie par le package NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/). Ce package est déjà installé dans l’exemple d’application téléchargé. Pour activer CORS, il suffit de supprimer certains commentaires du code.

1. Dans le projet ContactsList.API, ouvrez le fichier *app\_start/webapiconfig.cs*. Supprimez les commentaires de la ligne de code config.EnableCors dans la méthode **Register** de **WebApiConfig**. 

	Une fois le fichier mis à jour, le code doit ressembler à l’exemple suivant :

		public static class WebApiConfig
	    {
	        public static void Register(HttpConfiguration config)
	        {
	            // Web API configuration and services
	            
		        // Uncomment the following line to control CORS by using Web API code
				config.EnableCors();
	
	            // Web API routes
	            config.MapHttpAttributeRoutes();
	
	            config.Routes.MapHttpRoute(
	                name: "DefaultApi",
	                routeTemplate: "api/{controller}/{id}",
	                defaults: new { id = RouteParameter.Optional }
	            );
	        }
	    }

1. Ouvrez le fichier *Controllers/ContactsController.cs* et supprimez les commentaires de la ligne qui ajoute l’attribut `EnableCors` à la classe `ContactsController`.

		namespace ContactList.Controllers
		{
		    [HttpOperationExceptionFilterAttribute]
		    [EnableCors(origins:"*", headers:"*", methods: "*")]
		    public class ContactsController : ApiController
 
	Si vous préférez, vous pouvez appliquer l’attribut à des méthodes d’action individuelles plutôt qu’à l’ensemble du contrôleur.

	> **Remarque** : l’utilisation des caractères génériques pour tous les paramètres avec l’attribut `EnableCors` est illustrée uniquement à des fins de démonstration. Elle ouvre votre API à toutes les origines et à toutes les requêtes HTTP. Utilisez cet attribut avec précaution.

### Déployer l’API dans Azure et retester le programme frontal

8. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet ContactsList.API, puis cliquez sur **Publier**.

	L’Assistant **Publier le site web** s’ouvre automatiquement à l’étape **Aperçu** du profil de publication de l’application API que vous avez créée, puisqu’il s’agit de la dernière cible vers laquelle vous avez déployé ce projet.

3.  Cliquez sur **Publier**.

	![](./media/app-service-api-cors-consume-javascript/pubapi.png)

	Une fois le déploiement terminé, le navigateur ouvre une fenêtre à l’URL de l’application API, qui affiche ensuite une page signalant que la création de l’application web a réussi.

3.  Revenez à l’URL de l’application web dans laquelle vous avez déployé l’application AngularJS, puis actualisez la page ou cliquez sur le bouton **Actualiser**.

	La page se charge à nouveau correctement.

	![](./media/app-service-api-cors-consume-javascript/homepageazure.png)

## Réactiver la prise en charge de CORS dans Azure

Pour utiliser les services d’authentification Azure intégrés dans les didacticiels suivants, vous devez utiliser CORS dans Azure plutôt que CORS dans API Web.
 
1. Pour activer la prise en charge de CORS dans Azure, revenez au panneau CORS du portail Azure de votre application API et entrez de nouveau l’URL de l’application web.

13. Cliquez sur **Enregistrer**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

## Étapes suivantes 

Dans ce didacticiel, vous avez vu deux façons d’activer la prise en charge de CORS pour que le code JavaScript client puisse appeler une API dans un autre domaine. Dans le didacticiel suivant, vous allez voir comment [restreindre l’accès à votre application API pour que seuls les utilisateurs authentifiés puissent y accéder](app-service-api-dotnet-user-principal-auth.md).

<!---HONumber=Nov15_HO4-->