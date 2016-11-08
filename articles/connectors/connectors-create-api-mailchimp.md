---
title: MailChimp | Microsoft Docs
description: Créez des applications logiques avec Azure App Service. MailChimp est un service SaaS qui permet aux entreprises de gérer et d’automatiser les activités de marketing par courrier électronique, y compris l’envoi de courriers électroniques de marketing, de messages automatisés et de campagnes ciblées.
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: ''
tags: connectors

ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe

---
# Prise en main du connecteur MailChimp
MailChimp est un service SaaS qui permet aux entreprises de gérer et d’automatiser les activités de marketing par courrier électronique, y compris l’envoi de courriers électroniques de marketing, de messages automatisés et de campagnes ciblées.

> [!NOTE]
> Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.
> 
> 

Vous pouvez commencer par créer une application logique. Pour cela, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## Déclencheurs et actions
Le connecteur MailChimp peut être utilisé en tant qu’action ; il possède un ou plusieurs déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML.

 Le connecteur MailChimp met à votre disposition les actions et/ou les déclencheurs ci-après.

### Actions de MailChimp
Vous pouvez effectuer les actions suivantes :

| Action | Description |
| --- | --- |
| [newcampaign](connectors-create-api-mailchimp.md#newcampaign) |Créer une nouvelle campagne basée sur un type de campagne, la liste des destinataires et les paramètres de la campagne (ligne d’objet, titre, from\_name et reply\_to) |
| [newlist](connectors-create-api-mailchimp.md#newlist) |Créer une nouvelle liste dans votre compte MailChimp |
| [addmember](connectors-create-api-mailchimp.md#addmember) |Ajouter ou mettre à jour un membre de la liste |
| [removemember](connectors-create-api-mailchimp.md#removemember) |Supprimer un membre d’une liste. |
| [updatemember](connectors-create-api-mailchimp.md#updatemember) |Mettre à jour les informations d’un membre spécifique de la liste |

### Déclencheurs de MailChimp
Vous pouvez écouter les événements suivants :

| Déclencheur | Description |
| --- | --- |
| Lorsqu’un membre a été ajouté à une liste |Déclenche un flux de travail lorsqu’un nouveau membre a été ajouté à une liste |
| Lorsqu’une nouvelle liste est créée |Déclenche un flux de travail lors de la création d’une nouvelle liste |

## Créer une connexion à MailChimp
Pour créer des applications logiques avec MailChimp, vous devez d’abord créer une **connexion**, puis fournir les détails pour les propriétés suivantes :

| Propriété | Requis | Description |
| --- | --- | --- |
| Jeton |Oui |Fournir des informations d’identification MailChimp |

> [!INCLUDE [Procédure de création d’une connexion à MailChimp](../../includes/connectors-create-api-mailchimp.md)]
> 
> [!TIP]
> Vous pouvez utiliser cette connexion dans d’autres applications logiques.
> 
> 

## Référence pour MailChimp
S’applique à la version 1.0.

## newcampaign
Nouvelle campagne : créer une nouvelle campagne basée sur un type de campagne, la liste des destinataires et les paramètres de la campagne (ligne d’objet, titre, from\_name et reply\_to)

```POST: /campaigns```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| newCampaignRequest | |yes |body |(aucun) |Objet JSON à envoyer dans le corps avec les nouveaux paramètres de demande de nouvelle campagne |

#### Réponse
| Name | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## newlist
Nouvelle liste : créer une nouvelle liste dans votre compte MailChimp

```POST: /lists```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| newListRequest | |yes |body |(aucun) |Objet JSON à envoyer dans le corps avec les nouveaux paramètres de demande de nouvelle campagne |

#### Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## addmember
Ajouter un membre à la liste : ajouter ou mettre à jour un membre de la liste

```POST: /lists/{list_id}/members```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| list\_id |string |yes |path |(aucun) |L’ID unique de la liste |
| newMemberInList | |yes |body |(aucun) |Objet JSON à envoyer dans le corps avec les informations du nouveau membre |

#### Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## removemember
Supprimer le membre de la liste : supprimer un membre d’une liste.

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| list\_id |string |yes |path |(aucun) |L’ID unique de la liste |
| member\_email |string |yes |path |(aucun) |L’adresse de messagerie du membre à supprimer |

#### Réponse
| Name | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## updatemember
Mettre à jour les informations d’un membre : mettre à jour les informations d’un membre spécifique de la liste

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| list\_id |string |yes |path |(aucun) |L’ID unique de la liste |
| member\_email |string |yes |path |(aucun) |L’adresse de messagerie unique du membre à mettre à jour |
| updateMemberInListRequest | |yes |body |(aucun) |Objet JSON à envoyer dans le corps avec les informations mises à jour du membre |

#### Réponse
| Name | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## OnMemberSubscribed
Lorsqu’un membre a été ajouté à une liste : déclenche un flux de travail lorsqu’un nouveau membre a été ajouté à une liste

```GET: /trigger/lists/{list_id}/members```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| list\_id |string |yes |path |(aucun) |L’ID unique de la liste |

#### Réponse
| Name | Description |
| --- | --- |
| 200 |OK |
| 202 |Acceptée |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## OnCreateList
Lorsqu’une nouvelle liste est créée : déclenche un flux de travail lors de la création d’une nouvelle liste

```GET: /trigger/lists```

Il n’existe aucun paramètre pour cet appel

#### Réponse
| Nom | Description |
| --- | --- |
| 200 |OK |
| 202 |Acceptée |
| 400 |Demande incorrecte |
| 401 |Non autorisé |
| 403 |Interdit |
| 404 |Introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

## Définitions d’objet
### NewCampaignRequest
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| type |string |Oui |
| recipients |non défini |Oui |
| paramètres |non défini |Oui |
| variate\_settings |non défini |Non |
| tracking |non défini |Non |
| rss\_opts |non défini |Non |
| social\_card |non défini |Non |

### Recipient
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| list\_id |string |Oui |
| segment\_opts |non défini |Non |

### Paramètres
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| subject\_line |string |Oui |
| title |string |Non |
| from\_name |string |Oui |
| reply\_to |string |Oui |
| use\_conversation |booléenne |Non |
| to\_name |string |Non |
| folder\_id |integer |Non |
| authentifier |booléenne |Non |
| auto\_footer |booléenne |Non |
| inline\_css |booléenne |Non |
| auto\_tweet |booléenne |Non |
| auto\_fb\_post |array |Non |
| fb\_comments |booléenne |Non |

### Variate\_Settings
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| winner\_criteria |string |Non |
| wait\_time |integer |Non |
| test\_size |integer |Non |
| subject\_lines |array |Non |
| send\_times |array |Non |
| from\_names |array |Non |
| reply\_to\_addresses |array |Non |

### Suivi
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| opens |booléenne |Non |
| html\_clicks |booléenne |Non |
| text\_clicks |booléenne |Non |
| goal\_tracking |booléenne |Non |
| ecomm360 |booléenne |Non |
| google\_analytics |string |Non |
| clicktale |string |Non |
| salesforce |non défini |Non |
| highrise |non défini |Non |
| capsule |non défini |Non |

### RSS\_Opts
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| feed\_url |string |Non |
| frequency |string |Non |
| constrain\_rss\_img |string |Non |
| schedule |non défini |Non |

### Social\_Card
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| image\_url |string |Non |
| description |string |Non |
| title |string |Non |

### Segment\_Opts
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| saved\_segment\_id |integer |Non |
| match |string |Non |

### Salesforce
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| campaign |booléenne |Non |
| HDInsight |booléenne |Non |

### Highrise
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| campaign |booléenne |Non |
| HDInsight |booléenne |Non |

### Capsule
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| HDInsight |booléenne |Non |

### Planification
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| hour |integer |Non |
| daily\_send |non défini |Non |
| weekly\_send\_day |string |Non |
| monthly\_send\_date |number |Non |

### Daily\_Send
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| sunday |booléenne |Non |
| monday |booléenne |Non |
| tuesday |booléenne |Non |
| wednesday |booléenne |Non |
| thursday |booléenne |Non |
| friday |booléenne |Non |
| saturday |booléenne |Non |

### CampaignResponseModel
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |Non |
| type |string |Non |
| create\_time |string |Non |
| archive\_url |string |Non |
| status |string |Non |
| emails\_sent |integer |Non |
| send\_time |string |Non |
| content\_type |string |Non |
| recipient |array |Non |
| paramètres |non défini |Non |
| variate\_settings |non défini |Non |
| tracking |non défini |Non |
| rss\_opts |non défini |Non |
| ab\_split\_opts |non défini |Non |
| social\_card |non défini |Non |
| report\_summary |non défini |Non |
| delivery\_status |non défini |Non |
| \_links |array |Non |

### AB\_Split\_Opts
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| split\_test |string |Non |
| pick\_winner |string |Non |
| wait\_units |string |Non |
| wait\_time |integer |Non |
| split\_size |integer |Non |
| from\_name\_a |string |Non |
| from\_name\_b |string |Non |
| reply\_email\_a |string |Non |
| reply\_email\_b |string |Non |
| subject\_a |string |Non |
| subject\_b |string |Non |
| send\_time\_a |string |Non |
| send\_time\_b |string |Non |
| send\_time\_winner |string |Non |

### Report\_Summary
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| opens |integer |Non |
| unique\_opens |integer |Non |
| open\_rate |number |Non |
| clicks |integer |Non |
| subscriber\_clicks |number |Non |
| click\_rate |number |Non |

### Delivery\_Status
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| activé |booléenne |Non |
| can\_cancel |booléenne |Non |
| status |string |Non |
| emails\_sent |integer |Non |
| emails\_canceled |integer |Non |

### Lien
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| rel |string |Non |
| href |string |Non |
| statique |string |Non |
| targetSchema |string |Non |
| schema |string |Non |

### NewListRequest
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| name |string |Oui |
| contact |non défini |Oui |
| permission\_reminder |string |Oui |
| use\_archive\_bar |booléenne |Non |
| campaign\_defaults |non défini |Oui |
| notify\_on\_subscribe |string |Non |
| notify\_on\_unsubscribe |string |Non |
| email\_type\_option |booléenne |Oui |
| visibility |string |Non |

### Contact
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| société |string |Oui |
| address1 |string |Oui |
| address2 |string |Non |
| city |string |Oui |
| state |string |Oui |
| zip |string |Oui |
| country |string |Oui |
| phone |string |Oui |

### Campaign\_Defaults
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| from\_name |string |Oui |
| from\_email |string |Oui |
| subject |string |Non |
| language |string |Oui |

### CreateNewListResponseModel
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |Oui |
| name |string |Oui |
| contact |non défini |Oui |
| permission\_reminder |string |Oui |
| use\_archive\_bar |booléenne |Non |
| campaign\_defaults |non défini |Oui |
| notify\_on\_subscribe |string |Non |
| notify\_on\_unsubscribe |string |Non |
| date\_created |string |Non |
| list\_rating |integer |Non |
| email\_type\_option |booléenne |Oui |
| subscribe\_url\_short |string |Non |
| subscribe\_url\_long |string |Non |
| beamer\_address |string |Non |
| visibility |string |Non |
| modules |array |Non |
| stats |non défini |Non |
| \_links |array |Non |

### Statistiques
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| member\_count |integer |Non |
| unsubscribe\_count |integer |Non |
| cleaned\_count |integer |Non |
| member\_count\_since\_send |integer |Non |
| unsubscribe\_count\_since\_send |integer |Non |
| cleaned\_count\_since\_send |integer |Non |
| campaign\_count |integer |Non |
| campaign\_last\_sent |integer |Non |
| merge\_field\_count |integer |Non |
| avg\_sub\_rate |number |Non |
| avg\_unsub\_rate |number |Non |
| target\_sub\_rate |number |Non |
| open\_rate |number |Non |
| click\_rate |number |Non |
| last\_sub\_date |string |Non |
| last\_unsub\_date |string |Non |

### GetListsResponseModel
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| lists |array |Non |
| total\_items |integer |Non |

### NewMemberInListRequest
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| email\_type |string |Non |
| status |string |Oui |
| merge\_fields |non défini |Non |
| interests |string |Non |
| language |string |Non |
| vip |booléenne |Non |
| location |non défini |Non |
| email\_address |string |Oui |

### FirstAndLastName
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| FNAME |string |Non |
| LNAME |string |Non |

### Lieu
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| latitude |number |Non |
| longitude |number |Non |

### MemberResponseModel
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |Non |
| email\_address |string |Non |
| unique\_email\_id |string |Non |
| email\_type |string |Non |
| status |string |Non |
| merge\_fields |non défini |Non |
| interests |string |Non |
| stats |non défini |Non |
| ip\_signup |string |Non |
| timestamp\_signup |string |Non |
| ip\_opt |string |Non |
| timestamp\_opt |string |Non |
| member\_rating |integer |Non |
| last\_changed |string |Non |
| language |string |Non |
| vip |booléenne |Non |
| email\_client |string |Non |
| location |non défini |Non |
| last\_note |non défini |Non |
| list\_id |string |Non |
| \_links |array |Non |

### Last\_Note
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| note\_id |integer |Non |
| created\_at |string |Non |
| created\_by |string |Non |
| note |string |Non |

### GetAllMembersResponseModel
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| membres |array |Non |
| list\_id |string |Non |
| total\_items |integer |Non |

### Object
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
|  | | |

### UpdateMemberInListRequest
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| email\_address |string |Non |
| email\_type |string |Non |
| status |string |Oui |
| merge\_fields |non défini |Non |
| interests |string |Non |
| language |string |Non |
| vip |booléenne |Non |
| location |non défini |Non |

### GetMembersResponseModel
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| membres |array |Non |
| list\_id |string |Non |
| total\_items |integer |Non |

### AddUserResponseModel
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |Oui |
| email\_address |string |Oui |
| unique\_email\_id |string |Non |
| email\_type |string |Non |
| status |string |Non |
| merge\_fields |non défini |Oui |
| interests |string |Non |
| stats |non défini |Non |
| ip\_signup |string |Non |
| timestamp\_signup |string |Non |
| ip\_opt |string |Non |
| timestamp\_opt |string |Non |
| member\_rating |integer |Non |
| last\_changed |string |Non |
| language |string |Non |
| vip |booléenne |Non |
| email\_client |string |Non |
| location |non défini |Non |
| last\_note |non défini |Non |
| list\_id |string |Non |
| \_links |array |Non |

## Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)

<!---HONumber=AcomDC_0824_2016-->