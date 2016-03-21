<properties 
	pageTitle="Authentifier avec l’API REST de Mobile Engagement"
	description="Décrit comment authentifier avec l’API REST Azure Mobile Engagement" 
	services="mobile-engagement" 
	documentationCenter="mobile" 
	authors="piyushjo"
	manager="erikre"
	editor=""/>

<tags
	ms.service="mobile-engagement"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="mobile-multiple"
	ms.workload="mobile" 
	ms.date="03/07/2016"
	ms.author="wesmc"/>

# Authentifier avec l’API REST de Mobile Engagement

## Vue d'ensemble

Ce document décrit en détail comment faire fonctionner le mécanisme d’authentification pour les nouvelles API.

Il suppose que vous disposez d’un abonnement Azure valide et que vous avez créé une application Mobile Engagement à l'aide d'un de nos [didacticiels pour les développeurs](mobile-engagement-windows-store-dotnet-get-started.md).

## Authentification

Un jeton OAuth basé sur Microsoft Azure Active Directory doit être utilisé pour l’authentification.

Pour authentifier une requête API, un en-tête d'autorisation doit être ajouté à chaque requête. Il doit être au format suivant :

	Authorization: Bearer eyJ0eXAiOiJKV1QiLCJhbGmJlNmV2ZWJPamg2TTNXR1E...

>[AZURE.NOTE] Les jetons Azure Active Directory expirent dans 1 heure.

Il existe plusieurs façons d’obtenir un jeton. Comme les API sont généralement appelées à partir d'un service cloud, vous utiliserez probablement une clé d’API. Dans la terminologie Azure, une clé d’API est appelée mot de passe principal de du service. La procédure suivante décrit comment la configurer manuellement.

> [AZURE.WARNING] La clé affichée dans Azure Active Directory n'est PAS la clé d'API Mobile Engagement affichée dans le portail. Le concept de clé d'API Mobile Engagement est obsolète et a été remplacé par l'authentification AAD décrite dans ce document.

#### Installation unique (manuelle)

Lors de cette opération, notez les informations suivantes qui vous serviront ultérieurement :
	
1. Création d’une application Azure Active Directory à l’aide de [ce guide](../resource-group-create-service-principal-portal.md).

	- Le nom de l'application que vous avez choisi s’appelle `{AD_APP_NAME}` dans ce document.
	- L'identificateur de client affiché dans le menu de configuration s’appelle `{CLIENT_ID}` dans ce document.
	- La clé affichée une seule fois après l'enregistrement s’appelle `{CLIENT_SECRET}` dans ce document.
	- Cliquez sur le bouton **AFFICHER LES POINTS DE TERMINAISON** dans la barre inférieure, copiez l’**URL DU POINT DE TERMINAISON DU JETON OAUTH 2.0**, également appelée `https://login.microsoftonline.com/{TENANT_ID}/oauth2/token` dans ce document. <br/>                                    
2. Attribuez un rôle au principal du service, par exemple Lecteur ou Propriétaire, à l'aide de l’[interface de ligne de commande Azure](../xplat-cli-install.md).

	Si vous êtes sous Windows, modifiez votre variable d’environnement `PATH` afin d’inclure `C:\Program Files (x86)\Microsoft SDKs\Azure\CLI\bin` et pouvoir utiliser les commandes azure.

	Exécutez les commandes suivantes pour configurer votre compte avec l'interface de ligne de commande (CLI) Azure :

		azure config mode arm 
		azure login

	Cette commande permet ensuite de rechercher l'ID objet de votre application.

		$ azure ad sp show --search {AD_APP_NAME} 
		info: Executing command ad sp show 
		+ Getting active directory service principals
		data: Object Id: 71785194-b7f5-4701-a9d6-fefb6cd32d18 
		data: Display Name: {AD_APP_NAME} 
		data: Service Principal Names:
		data: {AD_APP_URI}
		data: 8cdaf270-763c-4577-b2a2-ce559b47d353 
		data: 
		info: ad sp show command OK

	Notez l’élément `Object Id` dans la sortie.

	Affectez ensuite le rôle `Owner` au principal du service à l'aide de cette commande :
  
		$ azure role assignment create --objectId {OBJECT_ID} -o Owner 
		info: Executing command role assignment create
		+ Finding role with specified name
		-data: RoleAssignmentId :
		/subscriptions/{SUBSCRIPTION_ID}/providers/Microsoft.Authorization/roleAssignm
		ents/009392b1-2b7c-4de8-8f70-1fccb2e0a331 
		data: RoleDefinitionName : Reader
		data: RoleDefinitionId : acdd72a7-3385-48ef-bd42-f606fba81ae7 
		data: Scope : /subscriptions/{SUBSCRIPTION_ID} 
		data: Display Name : {AD_APP_NAME}
		data: SignInName :
		data: ObjectId : {OBJECT_ID} 
		data: ObjectType : ServicePrincipal

#### Installation unique (à l'aide d’un script)

Il s'agit d'une autre façon d’effectuer les étapes susmentionnées à l'aide d'un script PowerShell.

1. Récupérez la dernière version d’Azure PowerShell. Consultez ce [lien](../powershell-install-configure.md) pour obtenir des instructions de téléchargement. 

2. Ouvrez Windows PowerShell en mode administrateur et assurez-vous que vous avez installé les [applets de commande Azure Resource Manager](https://msdn.microsoft.com/library/mt125356.aspx).

		Install-Module AzureRM
		Install-AzureRM

3. Exécutez la commande suivante :

		Import-Module AzureRM.profile

4. Exécutez la commande suivante pour lancer une boîte de dialogue de connexion. Après la connexion, la commande indique que l'abonnement est "actif", ce qui signifie que toutes les commandes que vous exécutez s'appliquent à cet abonnement.

		Add-AzureRmAccount

5. Exécutez la commande suivante pour répertorier tous vos abonnements. Copiez le GUID de l'abonnement que vous souhaitez utiliser.

		Get-AzureRmSubscription

6. Exécutez la commande suivante pour spécifier le GUID qui servira à configurer l'abonnement à utiliser. Ceci est particulièrement utile lorsque vous avez plusieurs abonnements.

		Select-AzureRmSubscription –SubscriptionId <subscriptionId>

7. Copiez le texte du script [New-AzureRmServicePrincipalOwner.ps1](https://raw.githubusercontent.com/matt-gibbs/azbits/master/src/New-AzureRmServicePrincipalOwner.ps1) sur votre ordinateur local puis exécutez-le.

	>[Azure.Note] Votre stratégie de sécurité par défaut peut vous empêche d'exécuter un script PowerShell. Dans ce cas, configurez temporairement votre stratégie d'exécution pour permettre l'exécution du script à l'aide de la commande suivante :

	>	Set-ExecutionPolicy RemoteSigned

	Le script vous invite à attribuer un "nom" à votre principal de service. Vous pouvez attribuer le nom de votre choix.

	Une fois le script terminé, il affiche quatre valeurs qui doivent être authentifiées par programmation avec Active Directory : **TenantId**, **SubscriptionId**, **ApplicationId** et **Secret**.

	Copiez ces valeurs pour référence. Pour obtenir maintenant un jeton d’accès, vous utilisez la valeur TenantId `{TENANT_ID}`, la valeur ApplicationId `{CLIENT_ID}` et la valeur Secret `{CLIENT_SECRET}`.

8. Vérifiez sur le portail de gestion Azure qu’une nouvelle application Active Directory apparaît sous **Afficher les applications que ma société possède**.

#### Procédure d'obtention d'un jeton valide

Pour obtenir un nouveau jeton, appelez cette API :

	https://login.microsoftonline.com/{TENANT_ID}/oauth2/token 

Voici un exemple de requête :

	POST /{TENANT_ID}/oauth2/token HTTP/1.1
	Host: login.microsoftonline.com
	Content-Type: application/x-www-form-urlencoded Content-Length: 195
	grant_type=client_credentials&client_id={CLIENT_ID}&client_secret={CLIENT_SECRET}&reso
	urce=https%3A%2F%2Fmanagement.core.windows.net%2F

Voici un exemple de réponse :

	HTTP/1.1 200 OK
	Content-Type: application/json; charset=utf-8
	Content-Length: 1234
	
	{"token_type":"Bearer","expires_in":"3599","expires_on":"1445395811","not_before":"144
	5391911","resource":"https://management.core.windows.net/","access_token":{ACCESS_T
	OKEN}}

Cet exemple incluait l’encodage de l’URL des paramètres POST, et la valeur `resource` est en fait `https://management.core.windows.net/`. Veillez également à encoder l’URL `{CLIENT_SECRET}` car elle peut contenir des caractères spéciaux.

Dans chaque appel d'API, incluez l'en-tête de requête d'autorisation :

	Authorization: Bearer {ACCESS_TOKEN}

Si vous obtenez un code d'état 401, vérifiez le corps de la réponse car il peut vous indiquer que le jeton a expiré. Dans ce cas, récupérez un nouveau jeton.

##Utilisation des API

Maintenant que vous avez un jeton valide, vous êtes prêt à passer les appels d'API.

1. Dans chaque requête API, vous devez passer un jeton valide, non expiré, que vous avez obtenu dans la section précédente.

2. Vous devez spécifier certains paramètres dans l'URI de la requête qui identifie votre application. L'URI de la requête ressemble à ceci :

		https://management.azure.com/subscriptions/{subscription-id}/resourcegroups/MobileEngagement/
		providers/Microsoft.MobileEngagement/appcollections/{app-collection}/apps/{app-resource-name}/

	Pour obtenir les paramètres, cliquez sur le nom de votre application puis sur Tableau de bord : vous verrez alors une page similaire à celle-ci, avec 3 paramètres.

	- **1** `{subscription-id}`
	- **2** `{app-collection}`
	- **3** `{app-resource-name}`

	![](./media/mobile-engagement-api-authentication/mobile-engagement-api-uri-params.png)

>[AZURE.NOTE] <br/>
>1. Ignorez l’adresse racine de l’API car elle s’appliquait aux API précédentes.<br/> 2. Vous devez utiliser le nom de ressource de l'application qui est différent du nom de l'application elle-même. 

<!---HONumber=AcomDC_0309_2016-->