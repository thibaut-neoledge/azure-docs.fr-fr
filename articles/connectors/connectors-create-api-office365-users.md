---
title: "Ajouter le connecteur Office 365 Users à des applications logiques | Microsoft Docs"
description: "Vue d’ensemble du connecteur Office 365 Users avec les paramètres de l’API REST"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: b2146481-9105-4f56-b4c2-7ae340cb922f
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 2e4af1c9369a97d518d027dc4679b9f01ca53d4c


---
# <a name="get-started-with-the-office-365-users-connector"></a>Prise en main du connecteur Office 365 Users
Connexion à Office 365 Users pour obtenir des profils, rechercher des utilisateurs, et plus encore. 

> [!NOTE]
> Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.
> 
> 

Avec Office 365 Users, vous pouvez :

* Créer votre flux d’activité en fonction des données que vous obtenez d’Office 365 Users. 
* Utilisez des actions qui permettent d’obtenir les collaborateurs, le profil utilisateur d’un responsable, et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d'autres actions. Vous pouvez, par exemple, obtenir les collaborateurs d’une personne, puis prendre ces informations et mettre à jour une base de données SQL Azure. 

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Le connecteur Office 365 Users met à votre disposition les actions ci-après. Il n'y a aucun déclencheur.

| Déclencheurs | Actions |
| --- | --- |
| Aucun |<ul><li>Get manager</li><li>Obtenir mon profil</li><li>Obtenir les collaborateurs</li><li>Obtenir le profil utilisateur</li><li>Rechercher des utilisateurs</li></ul> |

Tous les connecteurs prennent en charge les données aux formats JSON et XML. 

## <a name="create-a-connection-to-office-365-users"></a>Créer une connexion à Office 365 Users
Quand vous ajoutez ce connecteur à vos applications logiques, vous devez vous connecter à votre compte Office 365 Users et autoriser les applications logiques à se connecter à votre compte.

> [!INCLUDE [Steps to create a connection to Office 365 Users](../../includes/connectors-create-api-office365users.md)]
> 
> 

Après avoir créé la connexion, vous entrez les propriétés Office 365 Users, notamment l’ID client. La section **Informations de référence sur l'API REST** dans cette rubrique décrit ces propriétés.

> [!TIP]
> Vous pouvez utiliser cette même connexion Office 365 Users dans d’autres applications logiques.
> 
> 

## <a name="office-365-users-rest-api-reference"></a>Informations de référence sur l'API REST Office 365 Users
S’applique à la version 1.0.

### <a name="get-my-profile"></a>Obtenir mon profil
Extrait le profil de l’utilisateur actuel.  
```GET: /users/me``` 

Il n'existe aucun paramètre pour cet appel.

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 202 |L’opération a réussi |
| 400 |BadRequest |
| 401 |Non autorisé |
| 403 |Interdit |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

### <a name="get-user-profile"></a>Obtenir le profil utilisateur
Extrait un profil d’utilisateur spécifique.  
```GET: /users/{userId}``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| userId |string |Oui |path |Aucun |Nom d’utilisateur principal ou ID de courrier électronique |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 202 |L’opération a réussi |
| 400 |BadRequest |
| 401 |Non autorisé |
| 403 |Interdit |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

### <a name="get-manager"></a>Get manager
Extrait le profil utilisateur pour le responsable de l’utilisateur spécifié.  
```GET: /users/{userId}/manager``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| userId |string |Oui |path |Aucun |Nom d’utilisateur principal ou ID de courrier électronique |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 202 |L’opération a réussi |
| 400 |BadRequest |
| 401 |Non autorisé |
| 403 |Interdit |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

### <a name="get-direct-reports"></a>Obtenir les collaborateurs
Obtenir les collaborateurs.  
```GET: /users/{userId}/directReports``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| userId |string |Oui |path |Aucun |Nom d’utilisateur principal ou ID de courrier électronique |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 202 |L’opération a réussi |
| 400 |BadRequest |
| 401 |Non autorisé |
| 403 |Interdit |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

### <a name="search-for-users"></a>Rechercher des utilisateurs
Extrait les résultats de la recherche des profils utilisateur.  
```GET: /users``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| searchTerm |string |no |query |Aucun |Chaîne de recherche (s'applique à : display name, given name, surname, mail, mail nickname et user principal name) |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 202 |L’opération a réussi |
| 400 |BadRequest |
| 401 |Non autorisé |
| 403 |Interdit |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

## <a name="object-definitions"></a>Définitions d'objet
#### <a name="user-user-model-class"></a>User : classe de modèle utilisateur
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| DisplayName |string |no |
| GivenName |string |no |
| Surname |string |no |
| Mail |string |no |
| MailNickname |string |no |
| TelephoneNumber |string |no |
| AccountEnabled |booléenne |no |
| ID |string |yes |
| UserPrincipalName |string |no |
| Department |string |no |
| JobTitle |string |no |
| mobilePhone |string |no |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

Revenir à la [liste des API](apis-list.md).

<!--References-->
[5]: https://portal.azure.com
[7]: ./media/connectors-create-api-office365-users/aad-tenant-applications.PNG
[8]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-appinfo.PNG
[9]: ./media/connectors-create-api-office365-users/aad-tenant-applications-add-app-properties.PNG
[10]: ./media/connectors-create-api-office365-users/contoso-aad-app.PNG
[11]: ./media/connectors-create-api-office365-users/contoso-aad-app-configure.PNG



<!--HONumber=Feb17_HO2-->


