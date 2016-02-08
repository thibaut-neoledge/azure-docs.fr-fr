<properties
	pageTitle="Version préliminaire d’Azure AD B2C : utilisation de l’API Graph | Microsoft Azure"
	description="Comment appeler l’API Graph pour un client B2C à l’aide d’une identité d’application pour automatiser le processus."
	services="active-directory-b2c"
	documentationCenter=".net"
	authors="dstrockis"
	manager="msmbaldwin"
	editor=""/>

<tags
	ms.service="active-directory-b2c"
	ms.workload="identity"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="01/21/2016"
	ms.author="dastrock"/>

# Version préliminaire d'Azure AD B2C : utilisation de l'API Graph

<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-graph-switcher](../../includes/active-directory-b2c-devquickstarts-graph-switcher.md)]-->

Les clients Azure AD B2C sont souvent très volumineux, ce qui signifie que plusieurs tâches courantes de gestion de client doivent être effectuées par programmation. La gestion des utilisateurs en est un parfait exemple : vous devez peut-être migrer un magasin d’utilisateurs existant vers un client B2C ou vous souhaitez peut-être héberger l’inscription des utilisateurs dans votre page, en créant des comptes d’utilisateur dans Azure AD en arrière-plan. Ces types de tâches requièrent la capacité de créer, lire, mettre à jour et supprimer des comptes d'utilisateur. Vous pouvez faire tout cela à l'aide de l'API Azure AD Graph.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Pour les clients B2C, il existe essentiellement deux modes de communication avec l’API Graph.

- Pour les tâches interactives, à exécution unique, vous souhaiterez probablement effectuer les tâches de gestion en agissant comme un compte d’administrateur dans le client B2C. Ce mode requiert qu'un administrateur se connecte avec ses informations d'identification avant d'effectuer les appels à l'API Graph.
- Pour les tâches automatisées, en continu, vous souhaiterez sans doute effectuer les tâches de gestion à l'aide d'un type de compte de service auquel vous accordez les privilèges nécessaires. Dans Azure AD, vous pouvez faire cela en inscrivant une application et en l'authentification auprès d'Azure AD à l'aide d'une « identité d'application », par le biais de l'[octroi d'informations d'identification client OAuth 2.0](active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). Dans ce cas, l'application appelle l'API Graph agissant comme elle-même, et non comme un utilisateur spécifique.  

Dans cet article, nous allons démontrer comment exécuter le cas d'utilisation automatisée. Pour cette démonstration, nous allons créer un « B2CGraphClient » .NET 4.5, qui effectue des opérations CRUD. Dans le cadre de ce test, le client aura une interface de ligne de commande Windows permettant d'appeler des méthodes différentes. Toutefois, le code est écrit pour se comporter de façon non interactive et automatisée. Allons-y.

## Obtention d’un client Azure AD B2C

Avant de pouvoir créer des applications, des utilisateurs ou d’interagir avec Azure AD, vous avez besoin d’un client Azure AD B2C comportant un compte d’administrateur général. Si vous n'en avez pas encore un, suivez le guide de [prise en main d'Azure AD B2C](active-directory-b2c-get-started.md).

## Enregistrement d’une application de service dans votre client

Un client B2C étant disponible, vous devez créer votre application de service à l’aide des applets de commande PowerShell Azure AD. Premièrement, téléchargez et installez l'[Assistant de connexion Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152). Ensuite, vous pouvez télécharger et installer le [Module Azure Active Directory 64 bits pour Windows Powershell](http://go.microsoft.com/fwlink/p/?linkid=236297).

> [AZURE.NOTE]
Pour utiliser l’API Graph avec votre client B2C, vous devrez enregistrer une application dédiée à l’aide de PowerShell en suivant ces instructions. Vous ne pouvez pas réutiliser vos applications B2C déjà existantes que vous avez enregistrées dans le portail Azure. Il s’agit d’une limitation d’Azure AD B2C en version préliminaire qui sera supprimée dans un avenir proche, ce qui fera l’objet d’une nouvelle mise à jour de cet article.

Une fois que vous avez installé le module PowerShell, ouvrez PowerShell et connectez-vous à votre client B2C. Après avoir exécuté `Get-Credential`, vous êtes invité à indiquer un nom d’utilisateur et un mot de passe. Entrez ceux relatifs à votre compte d’administrateur du client B2C.

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
```

Avant de créer votre application, vous devez générer une nouvelle « clé secrète client ». Votre application utilisera la clé secrète client pour s'authentifier auprès d'Azure AD et acquérir des jetons d'accès. Vous pouvez générer une clé secrète valide dans Powershell :

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
```

La commande finale ci-dessus doit imprimer votre nouvelle clé secrète client. Copiez-la et conservez-la en lieu sûr ; vous en aurez besoin rapidement. Vous pouvez désormais créer votre application, en fournissant la nouvelle clé secrète client comme informations d'identification pour l'application :

```
> New-MsolServicePrincipal -DisplayName "My New B2C Graph API App" -Type password -Value $newClientSecret

DisplayName           : My New B2C Graph API App
ServicePrincipalNames : {dd02c40f-1325-46c2-a118-4659db8a55d5}
ObjectId              : e2bde258-6691-410b-879c-b1f88d9ef664
AppPrincipalId        : dd02c40f-1325-46c2-a118-4659db8a55d5
TrustedForDelegation  : False
AccountEnabled        : True
Addresses             : {}
KeyType               : Password
KeyId                 : a261e39d-953e-4d6a-8d70-1f915e054ef9
StartDate             : 9/2/2015 1:33:09 AM
EndDate               : 9/2/2016 1:33:09 AM
Usage                 : Verify
```

Si la création de l'application réussit, elle doit imprimer certaines des propriétés de l'application, telles que celles ci-dessus. Vous aurez besoin de `ObjectId` et de `AppPrincipalId`, donc prenez-en note également.

Une application dans votre client B2C étant créée, vous devez lui affecter les autorisations nécessaires pour effectuer des opérations CRUD utilisateur. Vous devrez affecter trois rôles différents à l'application : lecteurs de répertoire (pour la lecture des utilisateurs), enregistreurs de répertoire (pour la création et la mise à jour des utilisateurs) et administrateur de compte utilisateur (pour la suppression d'utilisateurs). Ces rôles ont des identificateurs connus. Vous pouvez donc exécuter les commandes ci-dessous, en remplaçant le paramètre `-RoleMemberObjectId` par le paramètre `ObjectId` ci-dessus. Pour afficher la liste de tous les rôles de répertoire, essayez d’exécuter `Get-MsolRole`.

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```  

Vous disposez maintenant d’une application qui a l’autorisation de créer, lire, mettre à jour et supprimer des utilisateurs de votre client B2C. Écrivons du code qui utilise cette application.

## Téléchargement, configuration et création de l'exemple de code

Tout d'abord, téléchargez l'exemple de code et exécutez-le. Nous pourrons ensuite observer ce qui se passe en arrière-plan. Vous pouvez [télécharger l’exemple de code en tant que fichier .zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip) ou le cloner dans un répertoire de votre choix :

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Ouvrez la solution `B2CGraphClient\B2CGraphClient.sln` Visual Studio dans Visual Studio. Dans le projet `B2CGraphClient`, ouvrez le fichier `App.config`. Remplacez les trois paramètres de l'application par vos propres valeurs, comme suit :

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

À présent, cliquez avec le bouton droit sur la solution `B2CGraphClient` et regénérez l’exemple. En cas de réussite, vous devez maintenant avoir un fichier exécutable `B2C.exe` situé dans `B2CGraphClient\bin\Debug`.

## Utilisateur CRUD avec l'API Graph

Pour utiliser le B2CGraphClient, ouvrez une invite de commandes Windows cmd et cd pour le répertoire `Debug`. Exécutez ensuite la commande `B2C Help`.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

Ceci affiche une brève description de chaque commande. Lorsque vous appelez une de ces commandes, le B2CGraphClient fait une requête auprès de l'API Azure AD Graph.

### Obtention d’un jeton d’accès

Toute requête à l'API Graph requiert un jeton d'accès pour l'authentification. Le B2CGraphClient utilise la bibliothèque ADAL open source pour vous aider à acquérir des jetons d'accès. Il n'est pas obligatoire d'utiliser la bibliothèque ADAL pour obtenir des jetons. Vous pouvez obtenir des jetons en créant des requêtes HTTP vous-même. La bibliothèque ADAL facilite l'acquisition des jetons en fournissant une API simple et en prenant soin de certains détails importants, tels que la mise en cache des jetons d'accès.

> [AZURE.NOTE]
	Cet exemple de code utilise délibérément ADAL v2, la version disponible de la bibliothèque ADAL. Il n'utilise PAS la bibliothèque ADAL v4, qui est une version préliminaire conçue pour travailler avec Azure AD B2C. Pour la version préliminaire d'Azure AD B2C, vous devez utiliser la bibliothèque ADAL v2 pour communiquer avec l'API Graph. Au fil du temps, nous activerons l'accès à l'API Graph avec la bibliothèque ADAL v4, afin que vous n'ayez pas à utiliser deux versions différentes de la bibliothèque ADAL dans votre solution Azure AD B2C complète.

Lorsque le B2CGraphClient s’exécute, il crée une instance de la classe `B2CGraphClient`. Le constructeur de cette classe définit la structure de l'authentification de la bibliothèque ADAL :

```C#
public B2CGraphClient(string clientId, string clientSecret, string tenant)
{
	// The client_id, client_secret, and tenant are provided in Program.cs, which pulls the values from App.config
	this.clientId = clientId;
	this.clientSecret = clientSecret;
	this.tenant = tenant;

	// The AuthenticationContext is ADAL's primary class, in which you indicate the tenant to use.
	this.authContext = new AuthenticationContext("https://login.microsoftonline.com/" + tenant);

	// The ClientCredential is where you pass in your client_id and client_secret, which are
	// provided to Azure AD in order to receive an access_token using the app's identity.
	this.credential = new ClientCredential(clientId, clientSecret);
}
```

Utilisons la commande `B2C Get-User` à titre d’exemple. Lorsque `Get-User` est appelé sans entrées supplémentaires, l’interface de ligne de commande appelle la méthode `B2CGraphClient.GetAllUsers(...)`. Cette méthode appelle `B2CGraphClient.SendGraphGetRequest(...)`, qui envoie une requête HTTP GET à l’API Graph. Avant d'envoyer la requête GET, elle obtient d'abord un jeton d'accès à l'aide de la bibliothèque ADAL :

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	// First, use ADAL to acquire a token using the app's identity (the credential)
	// The first parameter is the resource we want an access_token for; in this case, the Graph API.
	AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

	...

```

Comme vous pouvez le voir, vous pouvez obtenir un jeton d’accès pour l’API Graph en appelant la méthode `AuthenticationContext.AcquireToken(...)` de la bibliothèque ADAL. La bibliothèque ADAL renvoie un jeton\_d'accès représentant l'identité de l'application.

### Lecture des utilisateurs

Lorsque vous souhaitez obtenir la liste des utilisateurs à partir de l’API Graph ou obtenir un utilisateur particulier, vous pouvez envoyer une requête HTTP GET au point de terminaison `/users`. Une requête pour tous les utilisateurs dans un client ressemblerait à ce qui suit :

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```  

Pour afficher cette requête en action, essayez d'exécuter :

 ```
 > B2C Get-User
 ```

Il existe deux points importants à noter ici :

- Le jeton d’accès acquis via la bibliothèque ADAL a été ajouté à l’en-tête `Authorization` à l’aide du schéma `Bearer`.
- Pour les clients B2C, vous devez utiliser le paramètre de requête `api-version=beta`.


> [AZURE.NOTE]
	La version bêta de l'API Graph d'Azure AD fournit une fonctionnalité d'aperçu. Consultez ce [billet de blog de l’équipe API Graph](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx) pour plus d’informations sur la version bêta.

Ces détails sont gérés dans la méthode `B2CGraphClient.SendGraphGetRequest(...)` :

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	...

	// For B2C user managment, be sure to use the beta Graph API version.
	HttpClient http = new HttpClient();
	string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=beta";
	if (!string.IsNullOrEmpty(query))
	{
		url += "&" + query;
	}

	// Append the access token for the Graph API to the Authorization header of the request, using the Bearer scheme.
	HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
	request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
	HttpResponseMessage response = await http.SendAsync(request);

	...
```

### Création de comptes d'utilisateurs clients

Quand vous créez des comptes d’utilisateur dans votre client B2C, vous pouvez envoyer une requête HTTP POST au point de terminaison `/users` :

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
	// These properties are all required for creating consumer users.

	"accountEnabled": true,
	"alternativeSignInNamesInfo": [             // controls what identifier the user uses to sign into their account
		{
			"type": "emailAddress",             // can be 'emailAddress' or 'userName'
			"value": "joeconsumer@gmail.com"
		}
	],
	"creationType": "NameCoexistence",          // always set to 'NameCoexistence'
	"displayName": "Joe Consumer",				// a value that can be used for diplaying to the end-user
	"mailNickname": "joec",						// a mail alias for the user
	"passwordProfile": {
		"password": "P@ssword!",
		"forceChangePasswordNextLogin": false   // always set to false
	},
	"passwordPolicies": "DisablePasswordExpiration"
}
```

Chacune des propriétés incluses dans la requête ci-dessus est requise pour la création d'utilisateurs clients. Les commentaires `//` ont été inclus à titre d’illustration. Ne les incluez pas dans une requête réelle.

Pour voir cette requête en action, essayez d'exécuter une des commandes suivantes :

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

La commande `Create-User` prend un fichier `.json` comme paramètre d’entrée, qui contient la représentation JSON d’un objet utilisateur. L’exemple de code contient deux exemples de fichiers `.json` : `usertemplate-email.json` et `usertemplate-username.json` que vous pouvez modifier pour répondre à vos besoins. Outre les champs obligatoires ci-dessus, il existe plusieurs champs facultatifs inclus dans ces fichiers que vous pouvez utiliser. Vous trouverez plus d’informations sur ces champs dans la [référence d’entité API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#UserEntity).

Vous pouvez voir comment cette requête POST est conçue dans `B2CGraphClient.SendGraphPostRequest(...)`, qui :

- Attache un jeton d’accès à l’en-tête `Authorization` de la requête.
- Définit `api-version=beta`.
- Inclut l'objet utilisateur JSON dans le corps de la requête.

### Mise à jour de comptes d'utilisateurs clients

La mise à jour des objets utilisateurs est similaire à la création d'objets utilisateurs, mais elle utilise le verbe HTTP PATCH :

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
	"displayName": "Joe Consumer",				// this request only updates the user's displayName
}
```

Vous pouvez essayer de mettre à jour un utilisateur en mettant à jour vos fichiers JSON avec de nouvelles données, et en utilisant le B2CGraphClient pour exécuter l'une des commandes suivantes :

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Inspectez la méthode `B2CGraphClient.SendGraphPatchRequest(...)` pour plus d’informations sur l’envoi de cette requête.

### Suppression d'utilisateurs

La suppression d'un utilisateur est simple : il vous suffit d'utiliser le verbe HTTP DELETE et de construire l'URL avec l'ID d'objet correct :

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Pour obtenir un exemple, essayez d'exécuter la commande ci-dessous et consultez la requête DELETE obtenue imprimée dans la console :

```
> B2C Delete-User <object-id-of-user>
```

Inspectez la méthode `B2CGraphClient.SendGraphDeleteRequest(...)` pour plus d’informations sur l’envoi de cette requête.

Il existe beaucoup d'autres actions que vous pouvez effectuer avec l'API Azure AD Graph en plus de la gestion des utilisateurs. La [référence de l’API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) fournit les détails de chaque action, ainsi que des exemples de requête.


## Utilisation d'attributs personnalisés

Presque toutes les applications grand public doivent stocker un type d'informations de profil utilisateur personnalisé. Pour ce faire, une méthode consiste à définir un attribut personnalisé dans votre client B2C, qui vous permet de traiter cet attribut comme toute autre propriété sur un objet utilisateur. Vous pouvez mettre à jour l'attribut, supprimer l'attribut, interroger l'attribut, envoyer l'attribut comme revendication dans les jetons de connexion, et ainsi de suite.

Pour définir un attribut personnalisé dans votre client B2C, consultez la [référence d’attribut personnalisé dans la version préliminaire de B2C](active-directory-b2c-reference-custom-attr.md).

Vous pouvez afficher les attributs personnalisés définis dans votre client B2C à l’aide de B2CGraphClient :

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

La sortie de ces fonctions révèle les détails de chaque attribut personnalisé, tel que :

```JSON
{
      "odata.type": "Microsoft.DirectoryServices.ExtensionProperty",
      "objectType": "ExtensionProperty",
      "objectId": "cec6391b-204d-42fe-8f7c-89c2b1964fca",
      "deletionTimestamp": null,
      "appDisplayName": "",
      "name": "extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number",
      "dataType": "Integer",
      "isSyncedFromOnPremises": false,
      "targetObjects": [
        "User"
      ]
}
```

Vous pouvez utiliser le nom complet, tel que `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number` en tant que propriété sur vos objets utilisateur. Il vous suffit de mettre à jour votre fichier `.json` avec la nouvelle propriété, une valeur pour la propriété, et d’exécuter :

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

Et c'est terminé ! Avec B2CGraphClient, vous disposez maintenant d’une application de service capable de gérer vos utilisateurs de client B2C par programmation. Elle utilise sa propre identité d'application pour s'authentifier auprès de l'API Azure AD Graph et acquiert des jetons à l'aide d'une clé secrète client. Lorsque vous intégrez cette fonctionnalité dans votre propre application, n'oubliez pas les quelques points clés suivants pour les applications B2C :

- Vous devez accorder les autorisations appropriées dans le client à l’application.
- Pour le moment, vous devez utiliser la bibliothèque ADAL v2 pour obtenir des jetons d'accès (ou vous pouvez envoyer des messages de protocole directement, sans bibliothèque).
- Lors de l’appel de l’API Graph, utilisez [`api-version=beta`](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx).
- Lors de la création et la mise à jour des utilisateurs clients, il existe des propriétés requises, décrites ci-dessus.

> [AZURE.IMPORTANT]
Vous devez prendre en compte les caractéristiques de la réplication du service d’annuaire sous-jacent à Azure AD B2C quand vous utilisez l’API Azure AD Graph dans votre application B2C (pour plus d’informations, consultez [cet article](http://blogs.technet.com/b/ad/archive/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-geo-distributed-cloud-directory.aspx)). Une fois qu’un consommateur s’est inscrit à votre application B2C en utilisant une stratégie d’**inscription**, si vous tentez de lire immédiatement l’objet utilisateur à l’aide de l’API Azure AD Graph dans votre application, vous risquez de ne pas l’obtenir. Vous devez attendre quelques secondes, le temps que le processus de réplication aille à son terme. Nous publierons des instructions plus concrètes sur la « garantie de cohérence des opérations de lecture-écriture » fournie par l’API Azure AD Graph et le service d’annuaire au moment de la mise à disposition générale.

Si vous avez des questions ou des demandes d’action que vous souhaitez exécuter avec l’API Graph dans votre client B2C, nous sommes à votre écoute ! Entrez un commentaire sur l’article ou enregistrez un problème dans le référentiel GitHub d’exemple de code.

<!---HONumber=AcomDC_0128_2016-->