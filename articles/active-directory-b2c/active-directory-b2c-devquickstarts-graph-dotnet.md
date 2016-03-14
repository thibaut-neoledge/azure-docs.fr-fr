<properties
	pageTitle="Version préliminaire d’Azure Active Directory B2C : utilisation de l’API Graph | Microsoft Azure"
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

# Version préliminaire d’Azure AD B2C : utilisation de l’API Graph


<!-- TODO [AZURE.INCLUDE [active-directory-b2c-devquickstarts-graph-switcher](../../includes/active-directory-b2c-devquickstarts-graph-switcher.md)]-->

Les clients Azure Active Directory (Azure AD) B2C sont souvent très volumineux. Par conséquent, de nombreuses tâches courantes de gestion de client doivent être effectuées par programmation. La gestion des utilisateurs en est un parfait exemple. Il se peut que vous ayez besoin de migrer un magasin d’utilisateurs existant vers un client B2C ou que vous souhaitiez héberger l’inscription des utilisateurs sur votre page et créer des comptes d’utilisateur dans Azure AD en arrière-plan. Pour effectuer ces types de tâches, vous devez être en mesure de créer, de lire, de mettre à jour et de supprimer des comptes d’utilisateur. Vous pouvez faire tout cela à l’aide de l’API Azure AD Graph.

[AZURE.INCLUDE [active-directory-b2c-preview-note](../../includes/active-directory-b2c-preview-note.md)]

Pour les clients B2C, il existe deux modes principaux de communication avec l’API Graph.

- Pour les tâches interactives, à exécution unique, vous devez agir comme un compte d’administrateur dans le client B2C lorsque vous exécutez ces tâches. Avec ce mode, un administrateur doit se connecter à l’aide de ses informations d’identification avant de pouvoir effectuer des appels à l’API Graph.
- Pour les tâches automatisées, en continu, vous devez utiliser un compte de service doté des privilèges nécessaires pour effectuer des tâches de gestion. Dans Azure AD, vous pouvez le faire en inscrivant une application et en l’authentifiant auprès d’Azure AD à l’aide d’un **ID d’application** qui utilise l’[octroi des informations d’identification du client OAuth 2.0](../active-directory/active-directory-authentication-scenarios.md#daemon-or-server-application-to-web-api). Dans ce cas, l’application joue son propre rôle, et non celui d’un utilisateur, pour appeler l’API Graph.

Dans cet article, nous allons expliquer comment exécuter le cas d’utilisation automatisée. Pour cette démonstration, nous allons créer un `B2CGraphClient` .NET 4.5 qui effectuera les opérations de création, de lecture, de mise à jour et de suppression (CRUD) d’utilisateurs. Le client aura une interface de ligne de commande (CLI) Windows permettant d’appeler des méthodes différentes. Toutefois, le code est écrit pour se comporter de façon non interactive et automatisée.

## Obtention d’un client Azure AD B2C

Avant de pouvoir créer des applications ou des utilisateurs, ou interagir avec Azure AD, vous avez besoin d’un client Azure AD B2C comportant un compte d’administrateur général. Si vous n’avez pas encore de client, suivez le guide de [prise en main d’Azure AD B2C](active-directory-b2c-get-started.md).

## Enregistrement d’une application de service dans votre client

Une fois que vous disposez d’un client B2C, vous devez créer votre application de service à l’aide des applets de commande PowerShell Azure AD. Premièrement, téléchargez et installez l’[Assistant de connexion Microsoft Online Services](http://go.microsoft.com/fwlink/?LinkID=286152). Ensuite, téléchargez et installez le [Module Azure Active Directory 64 bits pour Windows PowerShell](http://go.microsoft.com/fwlink/p/?linkid=236297).

> [AZURE.NOTE]
Pour utiliser l’API Graph avec votre client B2C, vous devrez enregistrer une application dédiée à l’aide de PowerShell. Pour ce faire, suivez les instructions données dans cet article. Vous ne pouvez pas réutiliser les applications B2C déjà existantes que vous avez enregistrées dans le portail Azure. Cette limitation propre à Azure AD B2C en version préliminaire devrait être supprimée très prochainement. Nous prévoyons de mettre à jour cet article lorsque ce sera le cas.

Une fois le module PowerShell installé, ouvrez PowerShell et connectez-vous à votre client B2C. Après avoir exécuté `Get-Credential`, vous serez invité à fournir un nom d’utilisateur et un mot de passe. Entrez le nom d’utilisateur et le mot de passe du compte d’administrateur de votre client B2C.

```
> $msolcred = Get-Credential
> Connect-MsolService -credential $msolcred
```

Avant de créer votre application, vous devez générer une nouvelle **clé secrète client**. Votre application utilisera cette clé secrète client pour s’authentifier auprès d’Azure AD et acquérir des jetons d’accès. Vous pouvez générer une clé secrète valide dans PowerShell :

```
> $bytes = New-Object Byte[] 32
> $rand = [System.Security.Cryptography.RandomNumberGenerator]::Create()
> $rand.GetBytes($bytes)
> $rand.Dispose()
> $newClientSecret = [System.Convert]::ToBase64String($bytes)
> $newClientSecret
```

La commande finale doit afficher votre nouvelle clé secrète client. Copiez cette clé et conservez-la en lieu sûr. Vous en aurez besoin ultérieurement. Vous pouvez désormais créer votre application en fournissant la nouvelle clé secrète client comme information d’identification pour l’application :

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

Si la création de l’application réussit, cette dernière doit afficher les propriétés ci-dessus. Copiez les valeurs de `ObjectId` et de `AppPrincipalId`, qui seront également requises ultérieurement.

Après avoir créé une application dans votre client B2C, vous devez lui attribuer les autorisations nécessaires pour effectuer des opérations CRUD d’utilisateurs. Attribuez trois rôles à l’application : lecteurs de répertoire (pour la lecture des utilisateurs), enregistreurs de répertoire (pour la création et la mise à jour des utilisateurs) et administrateur des comptes d’utilisateur (pour la suppression d’utilisateurs). Ces rôles ont des identificateurs connus. Vous pouvez donc remplacer le paramètre `-RoleMemberObjectId` par le paramètre `ObjectId` ci-dessus et exécuter les commandes suivantes. Pour afficher la liste de tous les rôles de répertoire, essayez d’exécuter `Get-MsolRole`.

```
> Add-MsolRoleMember -RoleObjectId 88d8e3e3-8f55-4a1e-953a-9b9898b8876b -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId 9360feb5-f418-4baa-8175-e2a00bac4301 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
> Add-MsolRoleMember -RoleObjectId fe930be7-5e62-47db-91af-98c3a49a38b1 -RoleMemberObjectId <Your-ObjectId> -RoleMemberType servicePrincipal
```

Vous disposez maintenant d’une application autorisée à créer, lire, mettre à jour et supprimer des utilisateurs de votre client B2C.

## Téléchargement, configuration et création de l’exemple de code

Tout d’abord, téléchargez l’exemple de code et exécutez-le. Nous l’examinerons ensuite plus en détail. Vous pouvez [télécharger l’exemple de code en tant que fichier .zip](https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet/archive/master.zip). Vous pouvez également le cloner dans un répertoire de votre choix :

```
git clone https://github.com/AzureADQuickStarts/B2C-GraphAPI-DotNet.git
```

Ouvrez la solution `B2CGraphClient\B2CGraphClient.sln` Visual Studio dans Visual Studio. Dans le projet `B2CGraphClient`, ouvrez le fichier `App.config`. Remplacez les trois paramètres de l’application par vos propres valeurs :

```
<appSettings>
    <add key="b2c:Tenant" value="contosob2c.onmicrosoft.com" />
    <add key="b2c:ClientId" value="{The AppPrincipalId from above}" />
    <add key="b2c:ClientSecret" value="{The client secret you generated above}" />
</appSettings>
```

[AZURE.INCLUDE [active-directory-b2c-devquickstarts-tenant-name](../../includes/active-directory-b2c-devquickstarts-tenant-name.md)]

Ensuite, cliquez avec le bouton droit sur la solution `B2CGraphClient` et générez à nouveau l’exemple. Si l’opération aboutit, vous devez maintenant disposer d’un fichier exécutable `B2C.exe` dans le répertoire `B2CGraphClient\bin\Debug`.

## Création d’opérations CRUD d’utilisateurs à l’aide de l’API Graph

Pour utiliser le B2CGraphClient, ouvrez une invite de commandes Windows `cmd` et remplacez votre répertoire par le répertoire `Debug`. Exécutez ensuite la commande `B2C Help`.

```
> cd B2CGraphClient\bin\Debug
> B2C Help
```

Ceci affiche une brève description de chaque commande. À chaque fois que vous appelez une de ces commandes, `B2CGraphClient` envoie une requête à l’API Azure AD Graph.

### Obtention d’un jeton d’accès

Toute requête envoyée à l’API Graph requiert un jeton d’accès pour l’authentification. `B2CGraphClient` utilise la bibliothèque d’authentification Active Directory (ADAL) open source pour vous aider à acquérir des jetons d’accès. La bibliothèque ADAL facilite l’acquisition des jetons en fournissant une API simple et en prenant soin de certains détails importants, tels que la mise en cache des jetons d’accès. Cependant, vous n’êtes pas obligé d’utiliser la bibliothèque ADAL pour obtenir des jetons. Vous pouvez également en obtenir en créant des requêtes HTTP.

> [AZURE.NOTE]
	Cet exemple de code utilise ADAL v2, la version de la bibliothèque ADAL mise à la disposition générale. Il n’utilise pas la bibliothèque ADAL v4, qui est une version préliminaire conçue pour travailler avec Azure AD B2C. Pour la version préliminaire d'Azure AD B2C, vous devez utiliser la bibliothèque ADAL v2 pour communiquer avec l'API Graph. À terme, nous prévoyons de donner accès à l’API Graph à l’aide de la bibliothèque ADAL v4 afin de vous éviter d’avoir à utiliser deux versions de la bibliothèque ADAL dans votre solution Azure AD B2C complète.

Lorsque `B2CGraphClient` est exécuté, il crée une instance de la classe `B2CGraphClient`. Le constructeur de cette classe définit une structure d’authentification de la bibliothèque ADAL :

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
	// provided to Azure AD in order to receive an access_token by using the app's identity.
	this.credential = new ClientCredential(clientId, clientSecret);
}
```

Nous allons utiliser la commande `B2C Get-User` à titre d’exemple. Lorsque `B2C Get-User` est appelé sans entrées supplémentaires, l’interface de ligne de commande appelle la méthode `B2CGraphClient.GetAllUsers(...)`. Cette méthode appelle `B2CGraphClient.SendGraphGetRequest(...)`, qui envoie une requête HTTP GET à l’API Graph. Avant d’envoyer la requête GET, `B2CGraphClient.SendGraphGetRequest(...)` obtient un jeton d’accès à l’aide de la bibliothèque ADAL :

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	// First, use ADAL to acquire a token by using the app's identity (the credential)
	// The first parameter is the resource we want an access_token for; in this case, the Graph API.
	AuthenticationResult result = authContext.AcquireToken("https://graph.windows.net", credential);

	...

```

Vous pouvez obtenir un jeton d’accès pour l’API Graph en appelant la méthode `AuthenticationContext.AcquireToken(...)` de la bibliothèque ADAL. La bibliothèque ADAL renvoie un `access_token` (jeton d’accès) qui représente l’identité de l’application.

### Lecture des utilisateurs

Pour obtenir la liste des utilisateurs ou un utilisateur particulier à partir de l’API Graph, vous pouvez envoyer une requête HTTP `GET` au point de terminaison `/users`. Une requête pour obtenir tous les utilisateurs d’un client se présente ainsi :

```
GET https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Pour afficher cette requête, exécutez :

 ```
 > B2C Get-User
 ```

Deux points importants sont à prendre en considération :

- Le jeton d’accès acquis via la bibliothèque ADAL est ajouté à l’en-tête `Authorization` à l’aide du schéma `Bearer`.
- Pour les clients B2C, vous devez utiliser le paramètre de requête `api-version=beta`.


> [AZURE.NOTE]
	La version bêta de l'API Graph d'Azure AD fournit une fonctionnalité d'aperçu. Pour plus d’informations sur la version bêta, consultez ce [billet de blog de l’équipe API Graph](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx).

Ces détails sont gérés dans la méthode `B2CGraphClient.SendGraphGetRequest(...)` :

```C#
public async Task<string> SendGraphGetRequest(string api, string query)
{
	...

	// For B2C user management, be sure to use the beta Graph API version.
	HttpClient http = new HttpClient();
	string url = "https://graph.windows.net/" + tenant + api + "?" + "api-version=beta";
	if (!string.IsNullOrEmpty(query))
	{
		url += "&" + query;
	}

	// Append the access token for the Graph API to the Authorization header of the request by using the Bearer scheme.
	HttpRequestMessage request = new HttpRequestMessage(HttpMethod.Get, url);
	request.Headers.Authorization = new AuthenticationHeaderValue("Bearer", result.AccessToken);
	HttpResponseMessage response = await http.SendAsync(request);

	...
```

### Création de comptes d’utilisateurs clients

Quand vous créez des comptes d’utilisateur dans votre client B2C, vous pouvez envoyer une requête HTTP `POST` au point de terminaison `/users` :

```
POST https://graph.windows.net/contosob2c.onmicrosoft.com/users?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 338

{
	// All of these properties are required to create consumer users.

	"accountEnabled": true,
	"alternativeSignInNamesInfo": [             // controls which identifier the user uses to sign in to the account
		{
			"type": "emailAddress",             // can be 'emailAddress' or 'userName'
			"value": "joeconsumer@gmail.com"
		}
	],
	"creationType": "NameCoexistence",          // always set to 'NameCoexistence'
	"displayName": "Joe Consumer",				// a value that can be used for displaying to the end user
	"mailNickname": "joec",						// an email alias for the user
	"passwordProfile": {
		"password": "P@ssword!",
		"forceChangePasswordNextLogin": false   // always set to false
	},
	"passwordPolicies": "DisablePasswordExpiration"
}
```

Toutes les propriétés de cette requête sont requises pour créer des utilisateurs clients. Les commentaires `//` ont été inclus à titre d’illustration. Ne les incluez pas dans une requête réelle.

Pour afficher la requête, exécutez l’une des commandes suivantes :

```
> B2C Create-User ..\..\..\usertemplate-email.json
> B2C Create-User ..\..\..\usertemplate-username.json
```

La commande `Create-User` prend un fichier .json comme paramètre d’entrée. Celui-ci contient une représentation JSON d’un objet utilisateur. L’exemple de code contient deux exemples de fichiers .json : `usertemplate-email.json` et `usertemplate-username.json`. Vous pouvez modifier ces fichiers en fonction de vos besoins. Outre les champs obligatoires ci-dessus, ces fichiers incluent plusieurs champs facultatifs que vous pouvez utiliser. Vous trouverez plus d’informations sur ces champs facultatifs dans la [référence d’entité API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/entity-and-complex-type-reference#UserEntity).

Vous pouvez voir comment la requête POST est construite dans `B2CGraphClient.SendGraphPostRequest(...)`, qui :

- attache un jeton d’accès à l’en-tête `Authorization` de la requête ;
- définit le paramètre `api-version=beta` ;
- inclut l’objet utilisateur JSON dans le corps de la requête.

### Mise à jour de comptes d’utilisateurs clients

Le processus de mise à jour d’objets utilisateur est similaire à celui utilisé pour créer des objets utilisateur. Cependant, ce processus fait appel à la méthode HTTP `PATCH`:

```
PATCH https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
Content-Type: application/json
Content-Length: 37

{
	"displayName": "Joe Consumer",				// this request updates only the user's displayName
}
```

Essayez de mettre à jour un utilisateur en mettant à jour vos fichiers JSON avec de nouvelles données. Vous pouvez ensuite utiliser `B2CGraphClient` pour exécuter l’une des commandes suivantes :

```
> B2C Update-User <user-object-id> ..\..\..\usertemplate-email.json
> B2C Update-User <user-object-id> ..\..\..\usertemplate-username.json
```

Inspectez la méthode `B2CGraphClient.SendGraphPatchRequest(...)` pour plus d’informations sur l’envoi de cette requête.

### Suppression d’utilisateurs

Le processus de suppression d’un utilisateur est simple. Utilisez la méthode HTTP `DELETE` et construisez l’URL avec l’ID d’objet correct :

```
DELETE https://graph.windows.net/contosob2c.onmicrosoft.com/users/<user-object-id>?api-version=beta
Authorization: Bearer eyJhbGciOiJSUzI1NiIsIng1dCI6IjdkRC1nZWNOZ1gxWmY3R0xrT3ZwT0IyZGNWQSIsInR5cCI6IkpXVCJ9.eyJhdWQiOiJod...
```

Pour obtenir un exemple, saisissez la commande suivante et consultez la requête de suppression affichée dans la console :

```
> B2C Delete-User <object-id-of-user>
```

Inspectez la méthode `B2CGraphClient.SendGraphDeleteRequest(...)` pour plus d’informations sur l’envoi de cette requête.

L’API Azure AD Graph permet d’effectuer de nombreuses actions en plus de la gestion des utilisateurs. La [référence de l’API Azure AD Graph](https://msdn.microsoft.com/Library/Azure/Ad/Graph/api/api-catalog) fournit des informations détaillées sur chaque action, ainsi que des exemples de requête.

## Utilisation d’attributs personnalisés

La plupart des applications grand public doivent stocker un certain type d’informations de profil utilisateur personnalisé. Pour ce faire, une solution consiste à définir un attribut personnalisé dans votre client B2C. Vous pouvez ensuite traiter cet attribut de la même façon que toute autre propriété d’un objet utilisateur. Vous pouvez mettre à jour l’attribut, le supprimer, l’interroger, l’envoyer en tant que revendication dans les jetons de connexion, etc.

Pour définir un attribut personnalisé dans votre client B2C, consultez la [référence d’attribut personnalisé dans la version préliminaire de B2C](active-directory-b2c-reference-custom-attr.md).

Vous pouvez afficher les attributs personnalisés définis dans votre client B2C à l’aide de `B2CGraphClient` :

```
> B2C Get-B2C-Application
> B2C Get-Extension-Attribute <object-id-in-the-output-of-the-above-command>
```

Le résultat de ces fonctions révèle les détails de chaque attribut personnalisé, tels que :

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

Vous pouvez utiliser le nom complet, tel que `extension_55dc0861f9a44eb999e0a8a872204adb_Jersey_Number`, en tant que propriété de vos objets utilisateur. Mettez à jour votre fichier .json avec la nouvelle propriété et une valeur pour la propriété, puis exécutez :

```
> B2C Update-User <object-id-of-user> <path-to-json-file>
```

Avec `B2CGraphClient`, vous disposez d’une application de service capable de gérer les utilisateurs de votre client B2C par programmation. `B2CGraphClient` utilise sa propre identité d’application pour s’authentifier auprès de l’API Azure AD Graph et acquiert des jetons à l’aide d’une clé secrète client. Lorsque vous intégrez cette fonctionnalité dans votre application, prenez en considération les quelques points clés suivants pour les applications B2C :

- Vous devez accorder les autorisations appropriées à l’application dans le client.
- Pour le moment, vous devez utiliser la bibliothèque ADAL v2 pour obtenir des jetons d’accès (vous pouvez également envoyer des messages de protocole directement, sans utiliser de bibliothèque).
- Lorsque vous appelez l’API Graph, utilisez [`api-version=beta`](http://blogs.msdn.com/b/aadgraphteam/archive/2015/04/10/graph-api-versioning-and-the-new-beta-version.aspx).
- Lorsque vous créez et mettez à jour des utilisateurs clients, certaines propriétés sont requises, comme décrit plus haut.

> [AZURE.IMPORTANT] Vous devez prendre en compte les caractéristiques de la réplication du service d’annuaire sous-jacent à Azure AD B2C quand vous utilisez l’API Azure AD Graph dans votre application B2C (pour plus d’informations, consultez [cet article](http://blogs.technet.com/b/ad/archive/2014/09/02/azure-ad-under-the-hood-of-our-geo-redundant-highly-available-geo-distributed-cloud-directory.aspx)). Une fois qu’un consommateur s’est inscrit à votre application B2C en utilisant une stratégie d’**inscription**, si vous tentez immédiatement de lire l’objet utilisateur à l’aide de l’API Azure AD Graph dans votre application, vous risquez de ne pas l’obtenir. Vous devez attendre quelques secondes, le temps que le processus de réplication aille à son terme. Nous prévoyons de publier des instructions plus concrètes sur la « garantie de cohérence des opérations de lecture-écriture » fournie par l’API Azure AD Graph et le service d’annuaire au moment de la mise à disposition générale.

Si vous avez des questions ou souhaitez effectuer d’autres actions à l’aide de l’API Graph sur votre client B2C, laissez un commentaire sur cet article ou enregistrez un problème dans le référentiel d’exemples de code GitHub.

<!---HONumber=AcomDC_0302_2016-->