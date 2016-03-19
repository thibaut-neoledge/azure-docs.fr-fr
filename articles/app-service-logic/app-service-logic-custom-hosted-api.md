<properties 
	pageTitle="Appeler une API personnalisée dans Logic Apps" 
	description="Utilisation de votre API personnalisée hébergée sur App Service avec les applications logiques" 
	authors="stepsic-microsoft-com" 
	manager="dwrede" 
	editor="" 
	services="app-service\logic" 
	documentationCenter=""/>

<tags
	ms.service="app-service-logic"
	ms.workload="integration"
	ms.tgt_pltfrm="na"
	ms.devlang="na"	
	ms.topic="article"
	ms.date="02/23/2016"
	ms.author="stepsic"/>
	
# Utilisation de votre API personnalisée hébergée sur App Service avec les applications logiques

Bien que les applications logiques soient équipées d’un ensemble de plus de 40 connecteurs pour un large éventail de services, il se peut que vous deviez appeler votre propre API personnalisée pour exécuter votre propre code. L’un des moyens les plus simples et les plus évolutifs d’héberger vos propres API web personnalisées consiste à utiliser App Service. Cet article explique comment appeler une API web hébergée dans une application API App Service, une application web ou une application mobile.

## Déployer votre application web

Tout d’abord, vous devez déployer votre API sous forme d’application web dans Service App. Les instructions fournies ici traitent du déploiement de base : [Créer une application web ASP.NET](../app-service-web/web-sites-dotnet-get-started.md). Bien qu’ils soit possible d’appeler une API à partir d’une application logique, nous vous recommandons d’ajouter des métadonnées Swagger pour une intégration aisée avec les actions des applications logiques. Vous trouverez plus d’informations sur l’[ajout de swagger](../app-service-api/app-service-api-dotnet-get-started.md/#use-swagger-metadata-and-ui).

### Paramètres de l’API

Pour que le concepteur des applications logiques puisse analyser votre Swagger, il est important que vous activiez CORS et que vous définissiez les propriétés APIDefinition de votre application web. Le portail Azure vous permet de les définir facilement. Il vous suffit d’ouvrir le panneau des paramètres de votre application web et de définir « Définition API » sur l’URL de votre fichier swagger.json dans la section API (en général https://{name}.azurewebsites.net/swagger/docs/v1)) et d’ajouter une stratégie CORS pour '*' pour autoriser les demandes du concepteur d’applications logiques.

## Appel dans l’API

Dans le portail d’applications logiques, si vous avez défini CORS et les propriétés de la définition d’API, vous devriez être en mesure d’ajouter facilement des actions API personnalisées dans votre flux. Dans votre concepteur, vous pouvez choisir de parcourir vos sites web d’abonnement pour répertorier les sites web avec une URL swagger définie. Vous pouvez également utiliser l’action HTTP + Swagger pour pointer vers un swagger et répertorier les entrées et les actions disponibles. Enfin, vous pouvez aussi créer une demande à l’aide de l’action HTTP pour appeler n’importe quelle API, y compris celles qui n’ont pas ou n’exposent pas de document swagger.

Si vous voulez sécuriser votre API, il existe plusieurs façons de procéder :

1. Aucune modification de code nécessaire : Azure Active Directory peut être utilisé pour protéger votre API sans nécessiter de modification du code ou de redéploiement.
1. Appliquer l’authentification de base, l’authentification AAD ou l’authentification de certificat dans le code de votre API.

## Sécurisation des appels à votre API sans modification de code 

Dans cette section, vous allez créer deux applications Azure Active Directory : une pour votre application Logic, et une pour votre application web. Vous devez authentifier des appels à votre application web à l’aide du principal de service (id client et le phrase secrète) associé à l’application AAD pour votre application logique. Enfin, vous devez inclure l’ID d’application dans votre définition d’application logique.

### Partie 1 : Configurer l’identité de l’application pour votre application logique

C’est ce que l’application logique utilise pour s’authentifier sur Active Directory. Il vous *suffit* de le faire une fois pour votre répertoire. Par exemple, vous pouvez choisir d’utiliser la même identité pour toutes vos applications logiques, même si vous pouvez également créer des identités uniques si vous le souhaitez. Vous pouvez effectuer cette opération dans l’interface utilisateur ou utiliser PowerShell.

#### Créez l’identité de l’application en utilisant le portail Azure Classic

1. Accédez à [Active Directory dans le portail Azure Classic](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) et sélectionnez l’annuaire que vous utilisez pour votre application web
2. Cliquez sur l’onglet **Applications**
3. Sur la barre de commandes en bas de la page, cliquez sur **Ajouter**.
4. Fournissez à votre identité un nom à utiliser, puis cliquez sur la flèche suivante.
5. Insérez une chaîne unique formatée comme un domaine dans la zone de texte, puis cliquez sur la coche
6. Cliquez sur l’onglet **Configurer** de l’application.
7. Copie lez l’**ID Client**, il sera utilisé dans votre application Logic
8. Dans la section **Clés**, cliquez sur **Sélectionner une durée**, puis choisissez 1 an ou 2 ans
9. Cliquez sur le bouton **Enregistrer** en bas de l’écran (vous devrez peut-être attendre quelques secondes)
10. Veillez à copier la clé dans la zone. Elle sera également utilisée dans votre application logique

#### Créer l’identité de l’application à l’aide de PowerShell
1. `Switch-AzureMode AzureResourceManager`
2. `Add-AzureAccount`
3. `New-AzureADApplication -DisplayName "MyLogicAppID" -HomePage "http://someranddomain.tld" -IdentifierUris "http://someranddomain.tld" -Password "Pass@word1!"`
4. Veillez à copier l’**ID de locataire**, l’**ID d’Application** et le mot de passe que vous avez utilisé

### Partie 2: Protégez votre application web avec une identité d’application AAD

Si votre application web est déjà déployée, vous pouvez simplement l’activer dans le portail. Autrement, vous pouvez activer le volet autorisation de votre déploiement Azure Resource Manager.

#### Activer l’autorisation dans le portail Azure

1. Accédez à l’application web et cliquez sur **Paramètres** dans la barre de commandes.
2. Cliquez sur **Autorisation/authentification**. 
3. Activez l’**autorisation/authentification**.

À ce stade, une application est automatiquement créée pour vous. Vous aurez besoin de l’ID client de cette application pour la partie 3, et vous devez donc :

1. Accédez à [Active Directory dans le portail Azure Classic](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory) et sélectionnez votre annuaire. 
2. Rechercher l’application dans la zone de recherche
3. Cliquez sur cette dernière dans la liste
4. Cliquez sur l’onglet **Configurer**
5. Vous devez en principe voir l’**ID Client**

#### Déploiement de votre application web à l’aide d’un modèle ARM

Tout d’abord, vous devez créer une application pour votre application web. Elle doit être différente de l’application utilisée pour votre application logique. Commencez en exécutant les étapes figurant dans la partie 1, mais désormais, pour la **page d’accueil** et **IdentifierUris**, utilisez la https://**URL** réelle de votre application web.

>[AZURE.NOTE]Quand vous créez l’application pour votre application web, vous devez utiliser l’[approche du portail Azure Classic](https://manage.windowsazure.com/#Workspaces/ActiveDirectoryExtension/directory), car l’applet de commande PowerShell ne configure pas les autorisations requises pour connecter les utilisateurs à un site web.

Une fois que vous avez l’ID client et l’ID de locataire, définissez ce qui suit en tant que sous-ressource de l’application web dans votre modèle de déploiement :

```
"resources" : [
	{
		"apiVersion" : "2015-08-01",
		"name" : "web",
		"type" : "config",
		"dependsOn" : [
			"[concat('Microsoft.Web/sites/','parameters('webAppName'))]"
		],
		"properties" : {
			"siteAuthEnabled": true,
			"siteAuthSettings": {
			  "clientId": "<<clientID>>",
			  "issuer": "https://sts.windows.net/<<tenantID>>/",
			}
		}
	}
]
```

Pour exécuter un déploiement automatique capable de déployer une application web vide et une application logique utilisant AAD ensemble, cliquez sur le bouton suivant : [![Déploiement sur Azure](http://azuredeploy.net/deploybutton.png)](https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fraw.githubusercontent.com%2FAzure%2Fazure-quickstart-templates%2Fmaster%2F201-logic-app-custom-api%2Fazuredeploy.json)

Pour voir le modèle terminé, consultez [Appels de Logic App dans une API personnalisée hébergée sur App Service et protégée par AAD](https://github.com/Azure/azure-quickstart-templates/blob/master/201-logic-app-custom-api/azuredeploy.json).


### Partie 3 : Remplir la section Autorisation dans l’application logique

Dans la section **Autorisation** de l’opération **HTTP**: `{"tenant":"<<tenantId>>", "audience":"<<clientID from Part 2>>", "clientId":"<<clientID from Part 1>>","secret": "<<Password or Key from Part 1>>","type":"ActiveDirectoryOAuth" }`

| Élément | Description |
|---------|-------------|
| type | Type d'authentification. Pour l’authentification ActiveDirectoryOAuth, la valeur est ActiveDirectoryOAuth. |
| locataire | L'identifiant du locataire est utilisé pour identifier le locataire Active Directory. |
| audience | Obligatoire. Ressource à laquelle vous vous connectez. |
| clientID | L'identifiant client pour l'application Azure AD. |
| secret | Obligatoire. Secret du client qui demande le jeton. | 

Le modèle ci-dessus comporte déjà cette configuration, mais si vous créez l’application logique directement, vous devez inclure la section d’autorisation complète.

## Sécurisation de votre API dans le code

### Certificat d’autorisation

Vous pouvez utiliser des certificats clients pour valider les demandes entrantes sur votre application web. Voir [Configuration de l’authentification mutuelle TLS pour une application web](../app-service-web/app-service-web-configure-tls-mutual-auth.md) pour savoir comment configurer votre code.

Dans la section *Autorisation*, vous devez fournir : `{"type": "clientcertificate","password": "test","pfx": "long-pfx-key"}`.

| Élément | Description |
|---------|-------------|
| type | Obligatoire. Type d’authentification. Pour les certificats client SSL, la valeur doit être ClientCertificate. |
| pfx | Obligatoire. Contenu codé en base64 du fichier PFX. |
| password | Obligatoire. Mot de passe pour accéder au fichier PFX. |

### Authentification de base

Vous pouvez utiliser l’authentification de base (nom d’utilisateur et mot de passe) pour valider les demandes entrantes. L’authentification de base est un modèle courant et vous pouvez le faire dans n’importe quel langage servant à créer votre application.

Dans la section *Autorisation*, vous devez fournir : `{"type": "basic","username": "test","password": "test"}`.

| Élément | Description |
|---------|-------------|
| type | Obligatoire. Type d'authentification. Pour l’authentification de base, la valeur doit être définie sur De base. |
| username | Obligatoire. Nom d'utilisateur à authentifier. |
| password | Obligatoire. Mot de passe à authentifier. |
 
### Gérer l’authentification AAD dans le code

Par défaut, l’authentification Azure Active Directory que vous activez dans le portail n’affine pas les autorisations. Par exemple, elle ne verrouille pas votre API sur un utilisateur ou une application spécifique, mais uniquement sur un locataire particulier.

Si vous souhaitez restreindre l’API à la seule application logique, par exemple, du code, vous pouvez extraire l’en-tête qui contient le jeton JWT, et contrôler l’identité de l’appelant en rejetant toutes les demandes ne correspondant pas.

Pour aller plus loin, si vous souhaitez le mettre en œuvre au sein de votre code en totalité, et ne pas exploiter la fonction de portail, vous pouvez lire l’article : [Utilisation d’Active Directory pour l’authentification dans Azure App Service](../app-service-web/web-sites-authentication-authorization.md).

Vous devez toujours exécuter les opérations ci-dessus pour créer une identité d’application pour votre application logique, puis l’utiliser pour l’API.

<!---HONumber=AcomDC_0224_2016-->