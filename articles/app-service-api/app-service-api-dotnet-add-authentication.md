<properties 
	pageTitle="Protéger une application API Azure" 
	description="Découvrez comment protéger une application API Azure à l’aide de Visual Studio." 
	services="app-service\api" 
	documentationCenter=".net" 
	authors="tdykstra" 
	manager="wpickett" 
	editor="jimbe"/>

<tags 
	ms.service="app-service-api" 
	ms.workload="web" 
	ms.tgt_pltfrm="dotnet" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="10/27/2015" 
	ms.author="tdykstra"/>

# Protéger une application API : ajouter une authentification de fournisseur de réseau social ou Azure Active Directory

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

## Vue d'ensemble

Ce didacticiel explique comment protéger une application API afin que seuls les utilisateurs authentifiés puissent y accéder. Ce didacticiel affiche le code que vous pouvez utiliser dans une application API ASP.NET pour récupérer des informations sur l’utilisateur connecté.

Vous allez effectuer les étapes suivantes :

- appeler l’application API pour vérifier qu’elle fonctionne ;
- appliquer les règles d’authentification à l’application API ;
- appeler une nouvelle fois l’application API pour vérifier qu’elle rejette les demandes non authentifiées ;
- établir une connexion au fournisseur configuré ;
- appeler une nouvelle fois l’application API pour vérifier que l’accès authentifié fonctionne ;
- écrire et tester le code qui extrait les revendications de l’utilisateur connecté.

Pour plus d’informations sur l’authentification dans Azure App Service, consultez la page [Authentification pour les applications d’API et les applications mobiles](../app-service/app-service-authentication-overview.md).

## Composants requis

Ce didacticiel fonctionne avec l’application API que vous avez créée dans [Créer une application API](app-service-dotnet-create-api-app.md) et déployée dans [Déployer une application API](app-service-dotnet-deploy-api-app.md).

## Utiliser le navigateur pour appeler l’application API 

Le moyen le plus simple de vérifier que votre application API est publiquement accessible consiste à l’appeler à partir d’un navigateur.

1. Dans votre navigateur, accédez au [portail Azure en version préliminaire].

3. Dans la page d’accueil, cliquez sur **Parcourir > Applications API**, puis sur le nom de l’application API à protéger.

	![Parcourir](./media/app-service-api-dotnet-add-authentication/browse.png)

	![Sélectionner une application API](./media/app-service-api-dotnet-add-authentication/select.png)

3. Dans le panneau **Application API**, cliquez sur l’**URL** pour ouvrir une fenêtre de navigateur qui appelle votre application API.

	![Panneau Application API](./media/app-service-api-dotnet-add-authentication/chooseapiappurl.png)

2. Ajoutez `/api/contacts/get/` à l’URL dans la barre d’adresse du navigateur.

	Par exemple, si l’URL de l’application API est :

    	https://microsoft-apiappeeb5bdsasd744e188be7fa26f239bd4b.azurewebsites.net/

	L’URL complète serait :

    	https://microsoft-apiappeeb5bdsasd744e188be7fa26f239bd4b.azurewebsites.net/api/contacts/get/

	Des navigateurs différents gèrent les appels d’API différemment. L’image montre un appel réussi à partir d’un navigateur Chrome.

	![Réponse Get de chrome](./media/app-service-api-dotnet-add-authentication/chromeget.png)

2. Enregistrez l’URL que vous avez utilisée ; vous l’utiliserez de nouveau plus tard dans le didacticiel.

## Protéger l’application API

Quand vous avez déployé votre application API, vous l’avez déployée dans un groupe de ressources. Vous pouvez ajouter des applications web et d’autres applications API au même groupe de ressources, et chaque application API du groupe de ressources peut avoir l’un des trois paramètres d’accessibilité suivants : <!--todo: diagram showing different accessibility settings-->

- **Public (anonyme)** : tout le monde peut appeler l’application API en dehors du groupe de ressources sans être connecté.
- **Public (authentifié)** : seuls les utilisateurs authentifiés sont autorisés à appeler l’application API en dehors du groupe de ressources.
- **Interne** : seules d’autres applications API ou web du même groupe de ressources sont autorisées à appeler l’application API. (Les appels à partir d’applications web sont considérés comme des appels externes, même si les applications web sont dans le même groupe de ressources.)

Quand Visual Studio a créé le groupe de ressources pour vous, il a également créé une *passerelle*. Une passerelle est une application web spéciale qui gère toutes les demandes destinées aux applications API dans le groupe de ressources.

Quand vous accédez au panneau du groupe de ressources dans le [portail Azure en version préliminaire], vous pouvez voir l’application API et la passerelle dans le diagramme.

![Diagramme du groupe de ressources](./media/app-service-api-dotnet-add-authentication/rgdiagram.png)

### <a id="apiapp"></a>Configurer l’application API afin d’exiger une authentification

Pour configurer votre application API pour accepter uniquement les demandes authentifiées, vous devez définir le paramètre d’accessibilité **Public (authentifié)** et configurer la passerelle pour exiger l’authentification auprès d’un fournisseur comme Azure Active Directory, Google ou Facebook.

[AZURE.INCLUDE [app-service-api-config-auth](../../includes/app-service-api-config-auth.md)]

Vous avez maintenant protégé l’application API contre tout accès non authentifié. Vous devez ensuite configurer la passerelle pour spécifier le fournisseur d’authentification à utiliser.

### <a id="gateway"></a>Configurer la passerelle pour utiliser un fournisseur d’authentification

[AZURE.INCLUDE [app-service-api-gateway-config-auth](../../includes/app-service-api-gateway-config-auth.md)]

## Vérifier que l’authentification fonctionne

**Remarque :** si vous avez un problème de connexion lorsque vous effectuez les étapes suivantes, essayez d’ouvrir une fenêtre privée ou incognito.
 
1. Ouvrez une fenêtre de navigateur et, dans la barre d’adresse, entrez l’URL qui appelle la méthode `Get` de votre application API, comme vous l’avez fait précédemment.

	Cette fois, la tentative d’accès à l’application API génère un message d’erreur.

	![Échec de la réponse Get de chrome](./media/app-service-api-dotnet-add-authentication/chromegetfail.png)

2. Dans le navigateur, accédez à l’URL de connexion. L’URL suit ce modèle :

    	http://[gatewayurl]/login/[providername]

	Vous pouvez obtenir l’URL de la passerelle dans le panneau **Passerelle** du [portail Azure en version préliminaire]. (Pour accéder au panneau **Passerelle**, cliquez sur la passerelle dans le diagramme illustré dans le panneau **Groupe de ressources**.)

	![URL de la passerelle](./media/app-service-api-dotnet-add-authentication/gatewayurl.png)

	La valeur du paramètre [Providername] doit être l’une des suivantes :
	
	* "microsoftaccount"
	* "facebook"
	* "twitter"
	* "google"
	* "aad"

	Voici un exemple d’URL de connexion pour Azure Active Directory :

		https://dropboxrgaeb4ae60b7cb4f3d966dfa43.azurewebsites.net/login/aad/

	Notez que, contrairement à l’URL précédente, celle-ci n’inclut pas le nom de votre application API : la passerelle sert à vous authentifier et non à authentifier l’application API. La passerelle gère l’authentification de toutes les applications API dans le groupe de ressources.

3. Entrez vos informations d’identification quand le navigateur affiche une page de connexion.
 
	Si vous avez configuré la connexion Azure Active Directory, employez l’un des utilisateurs répertoriés sous l’onglet **Utilisateurs** pour l’application que vous avez créée sous l’onglet Azure Active Directory du [portail Azure], comme admin@contoso.onmicrosoft.com.

	![Utilisateurs Azure Active Directory](./media/app-service-api-dotnet-add-authentication/aadusers.png)

	![Page de connexion](./media/app-service-api-dotnet-add-authentication/ffsignin.png)

4. Quand le message « Connexion terminée » s’affiche, entrez à nouveau l’URL à la méthode Get de votre application API.

	Cette fois, l’appel réussit, car vous êtes authentifié. La passerelle reconnaît que vous êtes un utilisateur authentifié et transmet votre demande à votre application API.

	![Connexion terminée](./media/app-service-api-dotnet-add-authentication/logincomplete.png)

	![Réponse Get de chrome](./media/app-service-api-dotnet-add-authentication/chromeget.png)

	Si vous avez activé l’interface utilisateur de Swagger, vous pouvez également accéder à la page de l’interface utilisateur de Swagger dès maintenant. Toutefois, une icône en forme de croix rouge **ERREUR** est affichée dans la partie inférieure droite de la page. Si vous cliquez sur l’icône, un message indique que le fichier JSON Swagger n’est pas accessible. Ceci est dû au fait que Swagger effectue un appel AJAX sans inclure le jeton Zumo pour tenter de récupérer le fichier JSON. Cela n’empêche pas le bon fonctionnement de la page de l’interface utilisateur de Swagger.

## Utiliser Postman pour envoyer une demande Post

Quand vous vous connectez à la passerelle, la passerelle renvoie un jeton d’authentification. Ce jeton doit être inclus avec toutes les demandes provenant de sources externes qui passent par la passerelle. Quand vous accédez à une API avec un navigateur, le navigateur stocke généralement le jeton dans un cookie et l’envoie avec tous les appels ultérieurs à l’API.

Afin de voir ce qui se passe en arrière-plan, vous utilisez dans cette section du didacticiel un outil de navigateur pour créer et soumettre une demande Post, vous obtenez le jeton d’autorisation du cookie et l’incluez dans un en-tête HTTP. Cette section est facultative : dans la section précédente, vous avez déjà vérifié que l’application API accepte uniquement les accès authentifiés.

Ces instructions indiquent comment utiliser l’outil Postman dans le navigateur Chrome, mais vous pouvez effectuer la même opération avec n’importe quel outil client REST et outil de développement de navigateur.

1. Dans une fenêtre de navigateur Chrome, effectuez les étapes indiquées dans la section précédente pour vous authentifier et ouvrez les Outils de développement (F12).

	![Accédez à l’onglet Ressources.](./media/app-service-api-dotnet-add-authentication/resources.png)

3. Sous l’onglet **Ressources** des Outils de développement Chrome, recherchez les cookies pour votre passerelle, puis cliquez trois fois sur la valeur du cookie **x-zumo-auth** pour le sélectionner en entier.

	**Remarque** : assurez-vous que vous copiez bien l’intégralité de la valeur du cookie. Si vous double-cliquez, vous risquez de ne récupérer que la première partie.

5. Cliquez avec le bouton droit sur la **Valeur** du cookie **x-zumo-auth**, puis cliquez sur **Copier**.

	![Copiez le jeton d’autorisation](./media/app-service-api-dotnet-add-authentication/copyzumotoken.png)

4. Installez l’extension Postman dans votre navigateur Chrome si ce n’est pas déjà fait.

6. Ouvrez l’extension Postman.

7. Dans le champ URL de la demande, entrez l’URL à la méthode Get de votre application API que vous avez utilisée précédemment, mais sans `get/` à la fin.
 
		http://[apiappurl]/api/contacts
    
8. Cliquez sur **En-têtes**, puis ajoutez un en-tête *x-zumo-auth*. Collez la valeur du jeton du Presse-papiers dans le champ **Valeur**.

9. Ajoutez un en-tête *Content-Type* avec la valeur *application/json*.

10. Cliquez sur **form-data**, puis ajoutez une clé *contact* avec la valeur suivante :

		{   "Id": 0,   "Name": "Li Yan",   "EmailAddress": "yan@contoso.com" }

11. Cliquez sur Envoyer.

	L’application API retourne une réponse *201 Créé*.

	![Ajoutez des en-têtes et un corps](./media/app-service-api-dotnet-add-authentication/addcontact.png)

12. Pour vérifier que cette demande ne fonctionnerait pas sans le jeton d’authentification, supprimez l’en-tête d’authentification et cliquez à nouveau sur Envoyer.

	Vous obtenez une réponse *403 Interdit*.

	![Réponse 403 Interdit](./media/app-service-api-dotnet-add-authentication/403forbidden.png)

## Obtenir des informations sur l’utilisateur connecté

Dans cette section, vous allez modifier le code de l’application API ContactsList afin qu’il récupère et renvoie l’adresse e-mail et le nom de l’utilisateur connecté.

1. Dans Visual Studio, ouvrez le projet d’application API que vous avez déployé dans [Déployer une application API](app-service-dotnet-deploy-api-app.md) et que vous avez appelé pour les besoins de ce didacticiel.

3. Ouvrez le fichier apiapp.json et ajoutez une ligne indiquant que l’application API utilise l’authentification Microsoft Azure Active Directory.

		"authentication": [{"type": "aad"}]

	Le fichier apiapp.json final ressemblera à ce qui suit :

		{
		    "$schema": "http://json-schema.org/schemas/2014-11-01/apiapp.json#",
		    "id": "ContactsList",
		    "namespace": "microsoft.com",
		    "gateway": "2015-01-14",
		    "version": "1.0.0",
		    "title": "ContactsList",
		    "summary": "",
		    "author": "",
		    "endpoints": {
		        "apiDefinition": "/swagger/docs/v1",
		        "status": null
		    },
		    "authentication": [{"type": "aad"}]
		}

	Ce didacticiel utilise Azure Active Directory à titre d’exemple. Si vous utilisez un autre fournisseur, indiquez l’identificateur approprié. Voici les valeurs valides :

	* "aad"
	* "microsoftaccount"
	* "google"
	* "twitter"
	* "facebook" 

3. Dans le fichier *ContactsController.cs*, ajoutez une instruction `using` au début du fichier.

		using Microsoft.Azure.AppService.ApiApps.Service;

2. Remplacez le code dans la méthode `Get` par le code suivant.

		var runtime = Runtime.FromAppSettings(Request);
		var user = runtime.CurrentUser;
		TokenResult token = await user.GetRawTokenAsync("aad");
		var name = (string)token.Claims["name"];
		var email = (string)token.Claims["http://schemas.xmlsoap.org/ws/2005/05/identity/claims/upn"];
		return new Contact[]
		{
		    new Contact { Id = 1, EmailAddress = email, Name = name }
		};

	Au lieu des trois exemples de contact, le code renvoie les informations de contact associées à l’utilisateur connecté.

	Cet exemple de code utilise Azure Active Directory. Si vous utilisez un autre fournisseur, indiquez le nom de jeton et l’identificateur de revendication appropriés, comme indiqué à l’étape précédente.

	Pour en savoir plus sur les revendications Microsoft Azure Active Directory disponibles, voir [Types de jetons et de revendications pris en charge](https://msdn.microsoft.com/library/dn195587.aspx).

3. Ajoutez une instruction pour l’élément `Microsoft.Azure.AppService.ApiApps.Service`.

		using Microsoft.Azure.AppService.ApiApps.Service;

3. Redéployez le projet.

	Visual Studio enregistre les paramètres appliqués lorsque vous avez déployé le projet en suivant le didacticiel [Déployer](app-service-dotnet-deploy-api-app.md). Cliquez avec le bouton droit de la souris sur le projet, puis sélectionnez **Publier**. Ensuite, cliquez sur **Publier** dans la boîte de dialogue **Publier sur le site web**.

6. Suivez la procédure précédemment effectuée pour envoyer une demande Get à l’application API protégée.

	Le message de réponse indique le nom et l’ID de l’identité que vous avez utilisée pour vous connecter.

	![Message de réponse avec l’utilisateur connecté](./media/app-service-api-dotnet-add-authentication/chromegetuserinfo.png)

## Étapes suivantes

Vous avez vu comment protéger une application API Azure en exigeant une authentification de fournisseur de réseau social ou Azure Active Directory. Pour plus d’informations, consultez la page [Authentification pour les applications d’API et les applications mobiles](../app-service/app-service-authentication-overview.md).

[portail Azure]: https://manage.windowsazure.com/
[portail Azure en version préliminaire]: https://portal.azure.com/

<!---HONumber=AcomDC_1203_2015-->