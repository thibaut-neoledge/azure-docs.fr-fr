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
	ms.date="12/04/2015"
	ms.author="tdykstra"/>

# Consommer une application API à partir de JavaScript à l’aide de CORS

[AZURE.INCLUDE [app-service-api-get-started-selector](../../includes/app-service-api-get-started-selector.md)]

## Vue d’ensemble

Ce didacticiel montre comment consommer une application API à partir de code JavaScript dans un site web fourni à partir d’un domaine autre que celui de l’application API. L’exemple de client utilise AngularJS.

![](./media/app-service-api-cors-consume-javascript/homepageazure.png)
 
Il s’agit du deuxième volet d’une série de didacticiels qui expliquent comment utiliser des applications API dans Azure App Service. Pour accéder au premier didacticiel de la série, choisissez la première rubrique de la liste déroulante **Rubrique** en haut de la page.

## Prise en charge de CORS dans Azure App Service

Pour des raisons de sécurité, le comportement par défaut des navigateurs empêche JavaScript d’effectuer des appels d’API vers un domaine autre que celui d’où provient le code JavaScript. Par exemple, vous pouvez effectuer un appel à partir d’une page web de contoso.com vers un point de terminaison d’API contoso.com, mais pas vers un point de terminaison fabrikam.com. CORS (Cross Origin Resource Sharing) est un protocole Internet qui autorise les scénarios où vous devez effectuer ces appels d’API entre domaines. Dans Azure App Service, cette situation se présente par exemple quand votre client JavaScript s’exécute dans une application web tandis que votre API s’exécute dans une application API.

Azure App Service offre un moyen simple de configurer les domaines autorisés à appeler une application API et la fonctionnalité CORS fonctionne de la même façon pour tous les langages pris en charge par le service API Apps, par exemple, Java et Node.js.

## Procédure pour suivre ce didacticiel

Ce didacticiel fonctionne avec un exemple d’application pour lequel vous téléchargez et créez une application API lors du [premier didacticiel de Prise en main de la version ASP.NET de la série](app-service-api-dotnet-get-started.md). Si vous souhaitez travailler avec Java ou Node.js, consultez la [section de configuration CORS](#corsconfig) ci-dessous pour obtenir des instructions générales qui s’appliquent à toutes les applications API.

## Exemple de projet ContactsList.Angular

Dans le projet [d’exemple d’application ContactsList ](https://github.com/Azure-Samples/app-service-api-dotnet-contact-list), le projet ContactsList.Angular est un client AngularJS simple pour le projet d’API Web ContactsList.API.

Le code JavaScript AngularJS qui appelle l’API se trouve dans le fichier *index.html* du projet ContactsList.Angular. Le code définit les fonctions et les ajoute à l’objet `$scope`, comme illustré ici, où la méthode Get de l’API est définie comme `$scope.refresh()`.

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

2. Placez sur la ligne un commentaire définissant l’URL localhost comme `baseUrl`, supprimez les commentaires de la ligne qui définit une URL azurewebsites.net comme `baseUrl`, puis remplacez l’espace réservé par le nom réel de l’application API déjà créée. Si vous avez nommé l’application API ContactsListAPI, le code ressemble à l’exemple suivant.

		$scope.baseUrl = 'https://ContactsListAPI.azurewebsites.net';
		//$scope.baseUrl = 'http://localhost:51864';

### Déployer le projet ContactsList.Angular dans l’application web

Vous pourriez créer une application web pour déployer le projet AngularJS, mais dans ce didacticiel, vous allez effectuer le déploiement dans l’application web créée dans le didacticiel précédent. Le nom de l’application web indique peut-être que vous y avez initialement déployé un projet ASP.NET MVC, mais après ce déploiement, elle exécutera le code AngularJS.

8. Dans l’**Explorateur de solutions**, cliquez avec le bouton droit sur le projet ContactsList.Angular, puis cliquez sur **Publier**.

9. Cliquez sur l’onglet **Profil**.

3.  À l’étape **Profil** de l’Assistant **Publier le site web**, cliquez sur **Microsoft Azure App Service**.

4. Dans la boîte de dialogue **App Service**, sélectionnez votre abonnement.

5. Avec **Affichage** défini sur la valeur par défaut **Groupe de ressources**, développez le groupe de ressources que vous avez créé pour cette série de didacticiels.

7. Sélectionnez l’application web que vous avez créée dans le premier didacticiel (veillez à ne pas sélectionner l’application API), puis cliquez sur **OK**.

8. Cliquez sur l'onglet **Paramètres**.

9. Développez **Options de publication du fichier**, puis sélectionnez **Supprimer les fichiers supplémentaires de la destination**.

	![](./media/app-service-api-cors-consume-javascript/removeadditionalfiles.png)

	Normalement, lorsque vous déployez un projet web dans une application web App Service existante, vous devez désactiver l’option « Supprimer les fichiers supplémentaires », car toutes les modifications sont généralement des mises à jour ou de nouveaux fichiers. Ici, vous déployez un projet différent vers la même application web. De nombreux fichiers du déploiement précédent ne seront sans doute pas nécessaires dans le nouveau déploiement.

10. Cliquez sur **Publier**.

	Visual Studio déploie le projet ContactsList.Angular dans l’application web et ouvre un navigateur à l’URL de l’application web. Le navigateur affiche la même interface utilisateur AngularJS que celle que vous avez vue exécutée localement, mais maintenant une erreur se produit car votre programme frontal s’exécute dans un domaine différent (l’URL de l’application web) de celui du programme principal (l’URL de l’application API).

	![](./media/app-service-api-cors-consume-javascript/corserror.png)

## <a id="corsconfig"></a> Configurer CORS pour l’application API cible dans Azure

8. Dans une autre fenêtre de navigateur, accédez au [Portail Azure](https://portal.azure.com/).

9. Cliquez sur **Parcourir > API Apps**, puis sélectionnez l’application API cible. Pour ce didacticiel, il s’agit de l’application API que vous avez créée dans le premier didacticiel relatif au projet ContactsList.API.

10. Dans le panneau **Application API**, cliquez sur **Paramètres**.

11. Recherchez la section **API**, puis cliquez sur **CORS**.

12. Dans la zone de texte, saisissez l’URL depuis laquelle vous souhaitez autoriser les appels. Par exemple, si vous avez déployé votre application JavaScript sur une application web nommée ContactsListMVC, saisissez « http://contactslistmvc.azurewebsites.net ».

	Notez que vous pouvez entrer un astérisque (*) à la place d’une URL pour indiquer que tous les domaines d’origine sont acceptés.

13. Cliquez sur **Enregistrer**.

	![](./media/app-service-api-cors-consume-javascript/corsinportal.png)

14. Accédez à la fenêtre de navigateur qui affiche le client AngularJS, puis actualisez la page ou cliquez sur le bouton **Actualiser**.

	La page affiche maintenant les contacts qui sont stockés dans le système de fichiers de l’application API Azure.

	![](./media/app-service-api-cors-consume-javascript/homepageazure.png)

### CORS dans les outils Azure Resource Manager

Vous pouvez également configurer CORS pour une application API à l’aide d’outils tels que Azure PowerShell, CLI ou [Explorateur de ressources](https://resources.azure.com/).

Définissez la propriété `cors` du type de ressource Microsoft.Web/sites/config pour votre ressource <site name>/web. Par exemple, dans **Explorateur de ressources**, accédez à **abonnements > {votre abonnement} > resourceGroups > {votre groupe de ressources} > fournisseurs > Microsoft.Web > sites > {votre site} > config > web**, et vous verrez la propriété cors :

		"cors": {
		    "allowedOrigins": [
		        "contactslistmvc.azurewebsites.net"
		    ]
		}

### Service d’application CORS et API web CORS

Pour les projets d’API Web ASP.NET, il est également facile de configurer CORS dans le code, comme vous le verrez dans la section suivante. Toutefois, si vous utilisez CORS d’App Service et CORS Web API ensemble, c’est CORS App Service qui est prioritaire et CORS Web API n’aura aucun effet. Par exemple, si vous activez un domaine d’origine dans App Service et activez tous les domaines d’origine dans votre code API Web, votre application API Azure accepte uniquement les appels en provenance du domaine spécifié dans Azure.


## Comment configurer le service CORS dans le Code de l’API web

Dans un projet d’API web, vous pouvez installer des packages NuGet [Microsoft.AspNet.WebApi.Cors](https://www.nuget.org/packages/Microsoft.AspNet.WebApi.Cors/) qui permettent de spécifier dans le code les domaines à partir desquels votre API accepte des appels JavaScript. Ce processus est documenté en détail dans l’article [Activation des demandes multi-origines dans API Web ASP.NET 2](http://www.asp.net/web-api/overview/security/enabling-cross-origin-requests-in-web-api). Pour les applications API créées à l’aide d’API Web ASP.NET, le processus est exactement le même, mais il est résumé ici.

1. Dans un projet d’API web, comprennent une ligne de code `config.EnableCors()` dans la méthode d’**inscription** de **WebApiConfig**, comme dans l’exemple suivant. 

		public static class WebApiConfig
	    {
	        public static void Register(HttpConfiguration config)
	        {
	            // Web API configuration and services
	            
		        // The following line enables you to control CORS by using Web API code
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

1. Dans votre contrôleur d’API web, ajoutez l’attribut `EnableCors` à la classe `ContactsController` ou aux méthodes d’action individuelles. Dans l’exemple qui suit, la prise en charge de CORS s’applique à l’ensemble du contrôleur.

		namespace ContactList.Controllers
		{
		    [HttpOperationExceptionFilterAttribute]
		    [EnableCors(origins:"*", headers:"*", methods: "*")]
		    public class ContactsController : ApiController
 
	> **Remarque** : l’utilisation des caractères génériques pour tous les paramètres avec l’attribut `EnableCors` est illustrée uniquement à des fins de démonstration. Elle ouvre votre API à toutes les origines et à toutes les requêtes HTTP. Utilisez cet attribut avec précaution.

## Étapes suivantes 

Dans ce didacticiel, vous avez vu deux façons d’activer la prise en charge de CORS afin que le code JavaScript client puisse appeler une API dans un autre domaine. Dans l’article suivant de la série de prise en main d’API Apps, vous allez découvrir l’[authentification d’App Service API Service](app-service-api-authentication.md).

<!---HONumber=AcomDC_1210_2015-->