<properties
	pageTitle="Ajouter l’API Office 365 Users à PowerApps Enterprise ou à des applications logiques | Microsoft Azure"
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
ms.date="03/03/2016"
ms.author="deonhe"/>

# Prendre en main l'API Office 365 Users

Connexion à Office 365 Users pour obtenir des profils, rechercher des utilisateurs, et plus encore. L’API Office 365 Users peut être utilisée à partir de :

- Logic Apps 
- PowerApps

> [AZURE.SELECTOR]
- [Logic Apps](../articles/connectors/create-api-office365-users.md)
- [PowerApps Enterprise](../articles/power-apps/powerapps-create-api-office365-users.md)

&nbsp;

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

Quand vous ajoutez cette API à vos applications logiques, vous devez vous connecter à votre compte Office 365 Users et autoriser les applications logiques à se connecter à votre compte.

1. Connectez-vous à votre compte Office 365 Users.
2. Autorisez vos applications logiques à se connecter à votre compte Office 365 et à l’utiliser. 

Après avoir créé la connexion, vous entrez les propriétés Office 365 Users, notamment l’ID client. La section **Informations de référence sur l'API REST** dans cette rubrique décrit ces propriétés.

>[AZURE.TIP] Vous pouvez utiliser cette même connexion Office 365 Users dans d’autres applications logiques.


## Informations de référence sur l'API REST Office 365 Users
S’applique à la version 1.0.

### Obtenir mon profil 
Extrait le profil de l’utilisateur actuel. 
```GET: /users/me```

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
Extrait un profil d’utilisateur spécifique. 
```GET: /users/{userId}```

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
Extrait le profil utilisateur pour le responsable de l’utilisateur spécifié. 
```GET: /users/{userId}/manager```

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
Obtenir les collaborateurs. 
```GET: /users/{userId}/directReports```

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
Extrait les résultats de la recherche des profils utilisateur. 
```GET: /users```

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

|Nom de la propriété | Type de données |Requis
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

[Créez une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

Revenez à la [liste des API](apis-list.md).

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/create-api-office365-users/contoso-aad-app-configure.PNG

<!---------HONumber=AcomDC_0309_2016-->