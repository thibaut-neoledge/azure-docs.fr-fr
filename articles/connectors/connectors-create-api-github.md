---
title: GitHub | Microsoft Docs
description: "Créez des applications logiques avec Azure App Service. GitHub est un servie d’hébergement de dépôt Git sur le web. Il offre toutes les fonctionnalités distribuées de contrôle de révision et de gestion du code source (SCM) de Git, ainsi que ses propres fonctionnalités."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 8f873e6c-f4c0-4c2e-a5bd-2e953efe5e2b
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 6f336db8c8719b99420b353dca82e36a0d837769


---
# <a name="get-started-with-the-github-connector"></a>Prise en main du connecteur GitHub
GitHub est un servie d’hébergement de dépôt Git sur le web. Il offre toutes les fonctionnalités distribuées de contrôle de révision et de gestion du code source (SCM) de Git, ainsi que ses propres fonctionnalités.

> [!NOTE]
> Cette version de l’article s’applique à la version de schéma 2015-08-01-preview des applications logiques. 
> 
> 

Vous pouvez commencer par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Le connecteur GitHub peut être utilisé en tant qu’action ; il possède un ou plusieurs déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML. 

 Le connecteur GitHub met à votre disposition les actions et/ou les déclencheurs ci-après.

### <a name="github-actions"></a>Actions de GitHub
Vous pouvez effectuer les actions suivantes :

| Action | Description |
| --- | --- |
| [CreateIssue](connectors-create-api-github.md#createissue) |Crée un problème |

### <a name="github-triggers"></a>Déclencheurs de GitHub
Vous pouvez écouter les événements suivants :

| Déclencheur | Description |
| --- | --- |
| Lorsqu’un problème est ouvert |Un problème est ouvert |
| Lorsqu’un problème est fermé |Un problème est fermé |
| Lorsqu’un problème est assigné |Un problème est assigné |

## <a name="create-a-connection-to-github"></a>Créer une connexion à GitHub
Pour créer des applications logiques avec GitHub, vous devez d’abord créer une **connexion**, puis fournir les détails pour les propriétés suivantes : 

| Propriété | Requis | Description |
| --- | --- | --- |
| Jeton |Oui |Fournir des informations d’identification GitHub |

Après avoir créé la connexion, vous pouvez l’utiliser pour exécuter les actions et écouter les déclencheurs décrits dans cet article. 

> [!INCLUDE [Steps to create a connection to GitHub](../../includes/connectors-create-api-github.md)]
> 
> [!TIP]
> Vous pouvez utiliser cette connexion dans d'autres applications logiques.
> 
> 

## <a name="reference-for-github"></a>Référence pour GitHub
S’applique à la version : 1.0

## <a name="createissue"></a>CreateIssue
Créer un problème : crée un problème 

```POST: /repos/{repositoryOwner}/{repositoryName}/issues``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| repositoryOwner |string |Oui |path |(aucun) |Propriétaire du dépôt |
| repositoryName |string |Oui |path |(aucun) |Nom du dépôt |
| issueBasicDetails | |yes |body |(aucun) |Publication des détails |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="issueopened"></a>IssueOpened
Lorsqu’un problème est ouvert : un problème est ouvert 

```GET: /trigger/issueOpened``` 

Il n’existe aucun paramètre pour cet appel

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="issueclosed"></a>IssueClosed
Lorsqu’un problème est fermé : un problème est fermé 

```GET: /trigger/issueClosed``` 

Il n’existe aucun paramètre pour cet appel

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="issueassigned"></a>IssueAssigned
Lorsqu’un problème est assigné : un problème est assigné 

```GET: /trigger/issueAssigned``` 

Il n’existe aucun paramètre pour cet appel

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="object-definitions"></a>Définitions d’objet
### <a name="issuebasicdetailsmodel"></a>IssueBasicDetailsModel
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| title |string |Oui |
| body |string |Oui |
| assignee |string |Oui |

### <a name="issuedetailsmodel"></a>IssueDetailsModel
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| title |string |Oui |
| body |string |Oui |
| assignee |string |Oui |
| number |string |Non |
| state |string |Non |
| created_at |string |Non |
| repository_url |string |Non |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


