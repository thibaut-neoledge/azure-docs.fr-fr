---
title: "Ajouter le connecteur Facebook à vos applications logiques | Microsoft Docs"
description: "Vue d’ensemble du connecteur Facebook avec les paramètres d’API REST"
services: 
documentationcenter: 
author: MandiOhlinger
manager: anneta
editor: 
tags: connectors
ms.assetid: f4d6f0ed-c09b-488c-be1c-8cf2b5b1d4b8
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/07/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: b8a66308c4f4f1df610cdacd092ef133bd605665


---
# <a name="get-started-with-the-facebook-connector"></a>Prise en main du connecteur Facebook
Connectez-vous à Facebook et publiez dans un journal, obtenez un flux de page et bien plus encore. 

> [!NOTE]
> Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.
> 
> 

Avec Facebook, vous pouvez effectuer les opérations suivantes :

* Créer votre flux d’activité en fonction des données que vous obtenez de Facebook. 
* Utiliser un déclencheur quand une publication est reçue.
* Utiliser des actions pour publier dans votre journal, obtenir un flux de page et bien plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, quand il y a une nouvelle publication dans votre journal, vous pouvez la transférer vers votre flux Twitter. 

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Le connecteur Facebook inclut le déclencheur et les actions suivants. 

| Déclencheurs | Actions |
| --- | --- |
| <ul><li>Quand il y a une nouvelle publication dans mon journal</li></ul> |<ul><li>Obtenir un flux à partir de mon journal</li><li>Publier dans mon journal</li><li>Quand il y a une nouvelle publication dans mon journal</li><li>Obtenir un flux de page</li><li>Obtenir le journal d’un utilisateur</li><li>Publier dans une page</li></ul> |

Tous les connecteurs prennent en charge les données aux formats JSON et XML.

## <a name="create-a-connection-to-facebook"></a>Créer une connexion à Facebook
Quand vous ajoutez ce connecteur à vos applications logiques, vous devez autoriser celles-ci à se connecter à votre compte Facebook.

1. Connectez-vous à votre compte Facebook.
2. Sélectionnez **Autoriser**et permettez à vos applications logiques de se connecter à votre compte Facebook et de l’utiliser. 

> [!INCLUDE [Steps to create a connection to Facebook](../../includes/connectors-create-api-facebook.md)]
> 
> [!TIP]
> Vous pouvez utiliser cette même connexion Facebook dans d’autres applications logiques.
> 
> 

## <a name="swagger-rest-api-reference"></a>Informations de référence sur l’API REST Swagger
S’applique à la version 1.0.

### <a name="get-feed-from-my-timeline"></a>Obtenir un flux à partir de mon journal
Obtient les flux à partir du journal de l’utilisateur connecté.  
```GET: /me/feed```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| fields |string |no |query |(aucun) |Spécifie les champs à retourner. Exemple : id, name, image. |
| limit |integer |no |query |(aucun) |Nombre maximal de publications à récupérer |
| par |string |no |query |(aucun) |Limiter la liste des publications à celles auxquelles est associé un emplacement. |
| filter |string |no |query |(aucun) |Récupérer uniquement les publications qui correspondent à un filtre de flux particulier. |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

### <a name="post-to-my-timeline"></a>Publier dans mon journal
Publier un message d’état dans le journal d’un utilisateur connecté.  
```POST: /me/feed```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| post |string |Oui |body |(aucun) |Nouveau message à publier |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

### <a name="when-there-is-a-new-post-on-my-timeline"></a>Quand il y a une nouvelle publication dans mon journal
Déclenche un nouveau flux quand il y a une nouvelle publication dans le journal de l’utilisateur connecté.  
```GET: /trigger/me/feed```

Il n’existe aucun paramètre. 

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

### <a name="get-page-feed"></a>Obtenir un flux de page
Obtenir des publications à partir du flux d’une page spécifique.  
```GET: /{pageId}/feed```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| pageId |string |Oui |path |(aucun) |ID de la page à partir de laquelle les publications doivent être récupérées. |
| limit |integer |no |query |(aucun) |Nombre maximal de publications à récupérer |
| include_hidden |booléenne |no |query |(aucun) |Indique s’il faut inclure des publications masquées par la page |
| fields |string |no |query |(aucun) |Spécifie les champs à retourner. Exemple : id, name, image. |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

### <a name="get-user-timeline"></a>Obtenir le journal d’un utilisateur
Obtenir des publications à partir du journal d’un utilisateur.  
```GET: /{userId}/feed```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| userId |string |Oui |path |(aucun) |ID de l’utilisateur dont le journal doit être récupéré. |
| limit |integer |no |query |(aucun) |Nombre maximal de publications à récupérer |
| par |string |no |query |(aucun) |Limiter la liste des publications à celles auxquelles est associé un emplacement. |
| filter |string |no |query |(aucun) |Récupérer uniquement les publications qui correspondent à un filtre de flux particulier. |
| fields |string |no |query |(aucun) |Spécifie les champs à retourner. Exemple : id, name, image. |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

### <a name="post-to-page"></a>Publier dans une page
Publier un message dans une page Facebook en tant qu’utilisateur connecté.  
```POST: /{pageId}/feed```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| pageId |string |Oui |path |(aucun) |ID de la page à publier. |
| post |divers |Oui |body |(aucun) |Nouveau message à publier |

#### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 500 |Erreur interne du serveur |
| default |L’opération a échoué. |

## <a name="object-definitions"></a>Définitions d’objet
#### <a name="getfeedresponse"></a>GetFeedResponse
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| données |array |no |

#### <a name="triggerfeedresponse"></a>TriggerFeedResponse
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| données |array |no |

#### <a name="postitem-a-single-entry-in-a-profiles-feed"></a>PostItem : entrée unique dans le flux d’un profil
Le profil peut être un utilisateur, une page, une application ou un groupe. 

| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |no |
| admin_creator |array |no |
| caption |string |no |
| created_time |string |no |
| Description |string |no |
| feed_targeting |non défini |no |
| from |non défini |no |
| icon |string |no |
| is_hidden |booléenne |no |
| is_published |booléenne |no |
| link |string |no |
| message |string |no |
| Nom |string |no |
| object_id |string |no |
| picture |string |no |
| place |non défini |no |
| privacy |non défini |no |
| properties |array |no |
| source |string |no |
| status_type |string |no |
| story |string |no |
| targeting |non défini |no |
| to |array |no |
| type |string |no |
| updated_time |string |no |
| with_tags |non défini |no |

#### <a name="triggeritem-a-single-entry-in-a-profiles-feed"></a>TriggerItem : entrée unique dans le flux d’un profil
Le profil peut être un utilisateur, une page, une application ou un groupe.

| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |no |
| created_time |string |no |
| from |non défini |no |
| message |string |no |
| type |string |no |

#### <a name="adminitem"></a>AdminItem
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |no |
| link |string |no |

#### <a name="propertyitem"></a>PropertyItem
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Nom |string |no |
| texte |string |no |
| href |string |no |

#### <a name="userpostfeedrequest"></a>UserPostFeedRequest
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| message |string |yes |
| link |string |no |
| picture |string |no |
| Nom |string |no |
| caption |string |no |
| Description |string |no |
| place |string |no |
| tags |string |no |
| privacy |non défini |no |
| object_attachment |string |no |

#### <a name="pagepostfeedrequest"></a>PagePostFeedRequest
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| message |string |yes |
| link |string |no |
| picture |string |no |
| Nom |string |no |
| caption |string |no |
| Description |string |no |
| Actions |array |no |
| place |string |no |
| tags |string |no |
| object_attachment |string |no |
| targeting |non défini |no |
| feed_targeting |non défini |no |
| published |booléenne |no |
| scheduled_publish_time |string |no |
| backdated_time |string |no |
| backdated_time_granularity |string |no |
| child_attachments |array |no |
| multi_share_end_card |booléenne |no |

#### <a name="postfeedresponse"></a>PostFeedResponse
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |no |

#### <a name="profilecollection"></a>ProfileCollection
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| données |array |no |

#### <a name="useritem"></a>UserItem
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |no |
| first_name |string |no |
| last_name |string |no |
| Nom |string |no |
| gender |string |no |
| about |string |no |

#### <a name="actionitem"></a>ActionItem
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Nom |string |no |
| link |string |no |

#### <a name="targetitem"></a>TargetItem
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| countries |array |no |
| locales |array |no |
| regions |array |no |
| cities |array |no |

#### <a name="feedtargetitem-object-that-controls-news-feed-targeting-for-this-post"></a>FeedTargetItem : objet qui contrôle le ciblage du flux d’informations pour cette publication
Tout membre de ces groupes est susceptible de voir cette publication, à la différence des autres personnes. S’applique uniquement aux pages.

| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| countries |array |no |
| regions |array |no |
| cities |array |no |
| age_min |integer |no |
| age_max |integer |no |
| genders |array |no |
| relationship_statuses |array |no |
| interested_in |array |no |
| college_years |array |no |
| interests |array |no |
| relevant_until |integer |no |
| education_statuses |array |no |
| locales |array |no |

#### <a name="placeitem"></a>PlaceItem
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |no |
| Nom |string |no |
| overall_rating |number |no |
| location |non défini |no |

#### <a name="locationitem"></a>LocationItem
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| city |string |no |
| country |string |no |
| latitude |number |no |
| located_in |string |no |
| longitude |number |no |
| Nom |string |no |
| region |string |no |
| state |string |no |
| street |string |no |
| zip |string |no |

#### <a name="privacyitem"></a>PrivacyItem
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Description |string |no |
| value |string |yes |
| allow |string |no |
| deny |string |no |
| friends |string |no |

#### <a name="childattachmentsitem"></a>ChildAttachmentsItem
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| link |string |no |
| picture |string |no |
| image_hash |string |no |
| Nom |string |no |
| Description |string |no |

#### <a name="postphotorequest"></a>PostPhotoRequest
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| url |string |yes |
| caption |string |no |

#### <a name="postphotoresponse"></a>PostPhotoResponse
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |yes |
| post_id |string |yes |

#### <a name="postvideorequest"></a>PostVideoRequest
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| videoData |string |yes |
| Description |string |yes |
| title |string |yes |
| uploadedVideoName |string |no |

#### <a name="getphotoresponse"></a>GetPhotoResponse
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| données |non défini |yes |

#### <a name="getphotoresponseitem"></a>GetPhotoResponseItem
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| url |string |yes |
| is_silhouette |booléenne |yes |
| height |string |no |
| width |string |no |

#### <a name="geteventresponse"></a>GetEventResponse
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| données |array |yes |

#### <a name="geteventresponseitem"></a>GetEventResponseItem
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |yes |
| Nom |string |yes |
| start_time |string |no |
| end_time |string |no |
| timezone |string |no |
| location |string |no |
| Description |string |no |
| ticket_uri |string |no |
| rsvp_status |string |Oui |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).




<!--HONumber=Feb17_HO3-->


