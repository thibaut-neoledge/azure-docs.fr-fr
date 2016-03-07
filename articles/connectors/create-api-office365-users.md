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
ms.date="02/22/2016"
ms.author="deonhe"/>

# Prendre en main l'API Office 365 Users

Le fournisseur Office 365 Users Connection vous permet d'accéder aux profils des utilisateurs de votre organisation à l'aide de votre compte Office 365. Vous pouvez effectuer différentes actions comme obtenir votre profil, le profil d’un utilisateur, le responsable ou les collaborateurs directs d’un utilisateur, et également mettre à jour un profil utilisateur.

>[AZURE.NOTE] Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques. Pour la version de schéma 2014-12-01-preview, cliquez sur [API Office 365](../app-service-logic/app-service-logic-connector-office365.md).


Avec Office 365 Users, vous pouvez :

* Créer des applications logiques
* Créer des applications PowerApps

Pour plus d'informations sur l'ajout d'une API à PowerApps Enterprise, consultez [Inscrire une API dans PowerApps](../power-apps/powerapps-register-from-available-apis.md).

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## À propos des déclencheurs et des actions

L’API Office 365 Users peut être utilisée en tant qu'action ; il n'y a aucun déclencheur. Toutes les API prennent en charge les données aux formats JSON et XML.

 L'API Office 365 Users met à votre disposition les actions et/ou les déclencheurs ci-après.

### Actions Office 365 Users
Vous pouvez effectuer les actions suivantes :

|Action|Description|
|--- | ---|
|MyProfile|Extrait le profil de l'utilisateur actuel|
|UserProfile|Extrait un profil d’utilisateur spécifique|
|Manager|Extrait le profil utilisateur pour le responsable de l'utilisateur spécifié|
|DirectReports|Obtenir les collaborateurs|
|SearchUser|Extrait les résultats de la recherche des profils utilisateur|
## Créer une connexion à Office 365 Users
Pour utiliser l'API Office 365 Users, vous devez créer une **connexion**, puis fournir les détails de ces propriétés :

|Propriété| Requis|Description|
| ---|---|---|
|Jeton|Oui|Fournir les informations d'identification Office 365|


>[AZURE.TIP] Vous pouvez utiliser cette connexion dans d'autres applications logiques.

## Informations de référence sur l'API REST Office 365 Users
#### Cette documentation concerne la version 1.0.


### Obtenir mon profil 


 Extrait le profil de l'utilisateur actuel ```GET: /users/me```

Il n'existe aucun paramètre pour cet appel
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
------



### Obtenir le profil utilisateur 


 Extrait un profil d’utilisateur spécifique ```GET: /users/{userId}```



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
------



### Get manager 


 Extrait le profil utilisateur pour le responsable de l'utilisateur spécifié ```GET: /users/{userId}/manager```



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
------



### Obtenir les collaborateurs 


 Obtenir les collaborateurs ```GET: /users/{userId}/directReports```



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
------



### Rechercher des utilisateurs 


 Extrait les résultats de la recherche des profils utilisateur ```GET: /users```



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
------



## Définition(s) d'objet : 

 **User** : classe de modèle utilisateur.

Propriétés requises pour User :

ID

**Toutes les propriétés**:


| Nom | Type de données |
|---|---|
|DisplayName|string|
|GivenName|string|
|Surname|string|
|Mail|string|
|MailNickname|string|
|TelephoneNumber|string|
|AccountEnabled|booléenne|
|ID|string|
|UserPrincipalName|string|
|Department|string|
|JobTitle|string|


## Étapes suivantes
Après avoir ajouté l'API Office 365 à PowerApps Enterprise, [donnez aux utilisateurs des autorisations](../power-apps/powerapps-manage-api-connection-user-access.md) pour qu’ils puissent utiliser l'API dans leurs applications.

[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

<!---HONumber=AcomDC_0224_2016-->