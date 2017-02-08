---
title: SendGrid | Microsoft Docs
description: "Créez des applications logiques avec Azure App Service. Le fournisseur de connexion SendGrid vous permet d’envoyer un message électronique et de gérer les listes de destinataires."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: bc4f1fc2-824c-4ed7-8de8-e82baff3b746
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: e30231bd576436ae69f4fa42d0e2ab312c3938d6


---
# <a name="get-started-with-the-sendgrid-connector"></a>Prise en main du connecteur SendGrid
Le fournisseur de connexion SendGrid vous permet d’envoyer un message électronique et de gérer les listes de destinataires.

> [!NOTE]
> Cette version de l’article s’applique à la version de schéma 2015-08-01-preview des applications logiques. 
> 
> 

Vous pouvez commencer par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Le connecteur SendGrid peut être utilisé en tant qu’action ; il possède un ou plusieurs déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML. 

 Le connecteur SendGrid met à votre disposition les actions ci-après. Il n'y a aucun déclencheur.

### <a name="sendgrid-actions"></a>Actions de SendGrid
Vous pouvez effectuer les actions suivantes :

| Action | Description |
| --- | --- |
| [SendEmail](connectors-create-api-sendgrid.md#sendemail) |Envoie un message électronique à l’aide de l’API SendGrid (limité à 10 000 destinataires) |
| [AddRecipientToList](connectors-create-api-sendgrid.md#addrecipienttolist) |Ajouter un destinataire individuel à une liste de destinataires |

## <a name="create-a-connection-to-sendgrid"></a>Créer une connexion à SendGrid
Pour créer des applications logiques avec SendGrid, vous devez d’abord créer une **connexion**, puis fournir les détails pour les propriétés suivantes : 

| Propriété | Requis | Description |
| --- | --- | --- |
| ApiKey |Oui |Fournir votre clé d’API SendGrid |

> [!INCLUDE [Steps to create a connection to SendGrid](../../includes/connectors-create-api-sendgrid.md)]
> 
> [!TIP]
> Vous pouvez utiliser cette connexion dans d'autres applications logiques.
> 
> 

Après avoir créé la connexion, vous pouvez l’utiliser pour exécuter les actions et écouter les déclencheurs décrits dans cet article.

## <a name="reference-for-sendgrid"></a>Référence pour SendGrid
S’applique à la version 1.0.

## <a name="sendemail"></a>SendEmail
Envoyer un message électronique : envoie un message électronique à l’aide de l’API SendGrid (limité à 10 000 destinataires) 

```POST: /api/mail.send.json``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| request | |yes |body |(aucun) |Message électronique à envoyer |

#### <a name="response"></a>Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 429 |Trop de demandes |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## <a name="addrecipienttolist"></a>AddRecipientToList
Ajouter un destinataire à une liste : ajouter un destinataire individuel à une liste de destinataires 

```POST: /v3/contactdb/lists/{listId}/recipients/{recipientId}``` 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| listId |string |Oui |path |(aucun) |ID unique de la liste des destinataires |
| recipientId |string |Oui |path |(aucun) |ID unique du destinataire |

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
### <a name="emailrequest"></a>EmailRequest
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| À partir |string |Oui |
| fromname |string |Non |
| to |string |Oui |
| toname |string |Non |
| subject |string |Oui |
| body |string |Oui |
| ishtml |booléenne |Non |
| cc |string |Non |
| ccname |string |Non |
| bcc |string |Non |
| bccname |string |Non |
| replyto |string |Non |
| date |string |Non |
| headers |string |Non |
| fichiers d'entrée |array |Non |
| filenames |array |Non |

### <a name="emailresponse"></a>EmailResponse
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| message |string |Non |

### <a name="recipientlists"></a>RecipientLists
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| lists |array |Non |

### <a name="recipientlist"></a>RecipientList
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |integer |Non |
| name |string |Non |
| recipient_count |integer |Non |

### <a name="recipients"></a>Recipients
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| recipients |array |Non |

### <a name="recipient"></a>Recipient
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| email |string |Non |
| last_name |string |Non |
| first_name |string |Non |
| id |string |Non |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Feb17_HO1-->


