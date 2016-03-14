<properties
	pageTitle="Ajouter l’API Office 365 Users à vos applications logiques | Microsoft Azure"
	description="Vue d'ensemble de l'API Office 365 Users avec les paramètres de l'API REST"
	services=""	
	documentationCenter="" 	
	authors="msftman"	
	manager="erikre"	
	editor="" 
	tags="connectors" />

<tags
ms.service="multiple"
ms.devlang="na"
ms.topic="article"
ms.tgt_pltfrm="na"
ms.workload="integration"
ms.date="02/25/2016"
ms.author="deonhe"/>

# Prendre en main l'API Office 365 Users

Connexion à Office 365 Users pour obtenir des profils, rechercher des utilisateurs, et plus encore. L’API Office 365 Users peut être utilisée à partir de :

- PowerApps 
- Logic Apps 

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques. Pour la version de schéma 2014-12-01-preview, cliquez sur [API Office 365](../app-service-logic/app-service-logic-connector-office365.md).


Avec Office 365 Users, vous pouvez :

- Créer votre flux d’activité en fonction des données que vous obtenez d’Office 365 Users. 
- Utilisez des actions qui permettent d’obtenir les collaborateurs, le profil utilisateur d’un responsable, et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d'autres actions. Vous pouvez, par exemple, obtenir les collaborateurs d’une personne, puis prendre ces informations et mettre à jour une base de données SQL Azure. 
- Ajouter l’API Office 365 Users à PowerApps Enterprise Ensuite, vos utilisateurs peuvent utiliser cette API dans leurs applications. 

Pour plus d’informations sur l’ajout d’une API à PowerApps Enterprise, consultez [Inscrire une API dans PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions

L’API Office 365 Users met à votre disposition les actions ci-après. Il n'y a aucun déclencheur.

| Déclencheurs | Actions|
| --- | --- |
|Aucun | <ul><li>Obtenir un responsable</li><li>Obtenir mon profil</li><li>Obtenir les collaborateurs</li><li>Obtenir un profil utilisateur</li><li>Rechercher des utilisateurs</li></ul>|

Toutes les API prennent en charge les données aux formats JSON et XML.


## Créer une connexion à Office 365 Users

### Ajouter une configuration à PowerApps
Quand vous ajoutez cette API à PowerApps Enterprise, vous entrez les valeurs **ID client** et **clé secrète client** de votre application Office 365 Azure Active Directory (AAD). La valeur **URL de redirection** est également utilisée dans votre application Office 365. Si vous n’avez pas d’application Office 365, vous pouvez utiliser les étapes suivantes pour en créer une :

1. Dans le [portail Azure][5], ouvrez **Active Directory**, puis ouvrez le nom de client de votre organisation.
2. Sélectionnez l’onglet **Applications**, puis sélectionnez **Ajouter** : ![Applications clientes AAD][7]

3. Dans **AJOUTER UNE APPLICATION** :

	1. Entrez un **Nom** pour votre application.  
	2. Laissez le type d’application sur **Web**.  
	3. Sélectionnez **Suivant**.  

	![Ajouter une application AAD – Informations sur l’application][8]

4. Dans **Propriétés de l’application** :

	1. Entrez l’**URL de connexion** de votre application. Étant donné que vous allez vous authentifier dans AAD pour PowerApps, définissez l’URL de connexion sur \__https://login.windows.net_.
2. Entrez un **URI ID d’application** valide pour votre application.  
	3. Sélectionnez **OK**.  

	![Ajouter une application AAD – Propriétés de l’application][9]

5. Quand vous avez terminé, la nouvelle application AAD s’ouvre. Sélectionnez **Configurer** : ![Application AAD Contoso][10]

6. Sous la section **OAuth 2**, définissez l’**URL de réponse** sur la valeur d’URL de redirection indiquée quand vous avez ajouté la nouvelle API Office 365 Users dans le portail Azure. Sélectionnez **Ajouter une application** : ![Configuration de l’application AAD Contoso][11]

7. Dans **Autorisations pour d’autres applications**, sélectionnez **API Office 365 Unifié (version préliminaire) **, puis sélectionnez **OK** :

	Dans la page Configurer, notez que _API Office 365 Unifié (version préliminaire)_ est ajouté à la liste _Autorisations pour d’autres applications_.

8. Pour **Office 365 Exchange Online**, sélectionnez **Autorisations déléguées**, puis sélectionnez l’autorisation **Lire les profils de base de tous les utilisateurs**.

Une nouvelle application Azure Active Directory est créée. Vous pouvez copier/coller les valeurs **ID client** et **clé secrète client** dans votre configuration d’API Office 365 Users dans le portail Azure.

Pour obtenir des informations intéressantes sur les applications AAD, consultez [Comment et pourquoi les applications sont ajoutées à Azure AD](../active-directory/active-directory-how-applications-are-added.md).


### Ajouter une configuration à des applications logiques
Quand vous ajoutez cette API à vos applications logiques, vous devez vous connecter à votre compte Office 365 Users et autoriser les applications logiques à se connecter à votre compte.

1. Connectez-vous à votre compte Office 365 Users.
2. Autorisez vos applications logiques à se connecter à votre compte Office 365 et à l’utiliser. 

Après avoir créé la connexion, vous entrez les propriétés Office 365 Users, notamment l’ID client. La section **Informations de référence sur l’API REST** dans cette rubrique décrit ces propriétés.

>[AZURE.TIP] Vous pouvez utiliser cette même connexion Office 365 Users dans d’autres applications logiques.


## Informations de référence sur l'API REST Office 365 Users
S’applique à la version 1.0.

### Obtenir mon profil 
Extrait le profil de l'utilisateur actuel. ```GET: /users/me```

Il n'existe aucun paramètre pour cet appel.

#### Response

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|202|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


### Obtenir le profil utilisateur 
Extrait un profil d’utilisateur spécifique. ```GET: /users/{userId}```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|userId|string|yes|path|(aucun)|Nom d’utilisateur principal ou ID de courrier électronique|

#### Response

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|202|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|


### Get manager 
Extrait le profil utilisateur pour le responsable de l’utilisateur spécifié. ```GET: /users/{userId}/manager```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|userId|string|yes|path|(aucun)|Nom d’utilisateur principal ou ID de courrier électronique|

#### Response

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|202|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|



### Obtenir les collaborateurs 
Obtenir les collaborateurs. ```GET: /users/{userId}/directReports```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|userId|string|yes|path|(aucun)|Nom d’utilisateur principal ou ID de courrier électronique|

#### Response

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|202|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|



### Rechercher des utilisateurs 
Extrait les résultats de la recherche des profils utilisateur. ```GET: /users```

| Nom| Type de données|Requis|Emplacement|Valeur par défaut|Description|
| ---|---|---|---|---|---|
|searchTerm|string|no|query|(aucun)|Chaîne de recherche (s'applique à : display name, given name, surname, mail, mail nickname et user principal name)|

#### Response

|Nom|Description|
|---|---|
|200|L’opération a réussi|
|202|L’opération a réussi|
|400|BadRequest|
|401|Non autorisé|
|403|Interdit|
|500|Erreur interne du serveur|
|default|L’opération a échoué.|



## Définitions d'objet

#### User : classe de modèle utilisateur

| Nom | Type de données |Requis
|---|---|---|
|DisplayName|string|no|
|GivenName|string|no|
|Surname|string|no|
|Mail|string|no|
|MailNickname|string|no|
|TelephoneNumber|string|no|
|AccountEnabled|booléenne|no|
|ID|string|yes
|UserPrincipalName|string|no|
|Department|string|no|
|JobTitle|string|no|
|mobilePhone|string|no|


## Étapes suivantes
Après avoir ajouté l’API Office 365 à PowerApps Enterprise, [donnez aux utilisateurs des autorisations](../power-apps/powerapps-manage-api-connection-user-access.md) pour qu’ils puissent utiliser l’API dans leurs applications.

[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/create-api-office365-users/contoso-aad-app-configure.PNG

<!---HONumber=AcomDC_0302_2016-->