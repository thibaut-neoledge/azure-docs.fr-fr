---
title: " Utiliser le connecteur Slack dans vos applications logiques | Microsoft Docs"
description: Utiliser le connecteur Slack dans vos applications logiques Microsoft Azure App Service
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 234cad64-b13d-4494-ae78-18b17119ba24
ms.service: multiple
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: cacaa4a4a0089e1c3df54ae466af251026e1a55b


---
# <a name="get-started-with-the-slack-connector"></a>Prise en main du connecteur Slack
Slack est un outil de communication collaboratif qui centralise toutes les communications de votre équipe dans un seul emplacement que vous pouvez consulter instantanément, où que vous vous trouviez.

> [!NOTE]
> Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.
> 
> 

Avec le connecteur Slack, vous pouvez :

* créer des applications logiques.

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md).

## <a name="lets-talk-about-triggers-and-actions"></a>À propos des déclencheurs et des actions
Le connecteur Slack peut être utilisé en tant qu’action ; il n’y a aucun déclencheur. Tous les connecteurs prennent en charge les données aux formats JSON et XML. 

 Le connecteur Slack met à votre disposition les actions et/ou les déclencheurs ci-après.

### <a name="slack-actions"></a>Actions Slack
Vous pouvez effectuer les actions suivantes :

| Action | Description |
| --- | --- |
| PostMessage |Publier un message dans un canal spécifié. |

## <a name="create-a-connection-to-slack"></a>Créer une connexion à Slack
Pour utiliser le connecteur Slack, vous devez créer une **connexion** , puis fournir les détails de ces propriétés : 

| Propriété | Requis | Description |
| --- | --- | --- |
| Jeton |Oui |Fournir les informations d’identification de Slack |

Suivez ces étapes pour vous connecter à Slack et terminer la configuration de la **connexion** à Slack dans votre application logique :

1. Sélectionnez **Périodicité**
2. Sélectionnez une **Fréquence** et entrez un **Intervalle**
3. Sélectionnez **Ajouter une action**  
   ![Configurer Slack][1]  
4. Entrez Slack dans la zone de recherche et attendez que la recherche renvoie toutes les entrées contenant Slack dans leur nom
5. Sélectionnez **Slack - Publier un message**
6. Sélectionnez **Se connecter à Slack** :  
   ![Configurer Slack][2]
7. Entrer vos informations d’identification Slack pour vous connecter et autoriser l’application    
   ![Configurer Slack][3]  
8. Vous êtes redirigé vers la page de connexion de votre organisation. **Autorisez** Slack à interagir avec votre application logique :      
   ![Configurer Slack][5] 
9. Une fois l’autorisation terminée, vous serez redirigé vers votre application logique pour la terminer en configurant la section **Slack - Obtenir tous les messages** . Ajouter les autres déclencheurs et actions dont vous avez besoin.  
   ![Configurer Slack][6]
10. Enregistrez votre travail en sélectionnant **Enregistrer** sur la barre de menu supérieure.

> [!TIP]
> Vous pouvez utiliser cette connexion dans d'autres applications logiques.
> 
> 

## <a name="slack-rest-api-reference"></a>Informations de référence sur l’API REST Slack
#### <a name="this-documentation-is-for-version-10"></a>Cette documentation concerne la version 1.0.
### <a name="post-a-message-to-a-specified-channel"></a>Publier un message dans un canal spécifié.
**```POST: /chat.postMessage```** 

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| channel |string |yes |query |(aucun) |Canal, groupe privé ou canal de messagerie instantanée auquel envoyer le message. Peut être un nom (ex : #general) ou un identifiant encodé. |
| texte |string |yes |query |(aucun) |Texte du message à envoyer. Pour connaître les options de mise en forme, consultez https://api.slack.com/docs/formatting. |
| username |string |no |query |(aucun) |Nom du robot |
| as_user |booléenne |no |query |(aucun) |Transmettre true pour publier le message en tant qu’utilisateur authentifié, plutôt que robot |
| parse |string |no |query |(aucun) |Modifier la façon dont les messages sont traités. Pour plus d’informations, consultez https://api.slack.com/docs/formatting. |
| link_names |integer |no |query |(aucun) |Rechercher et lier les noms de canaux et les noms d’utilisateur. |
| unfurl_links |booléenne |no |query |(aucun) |Transmettre true pour activer le déploiement d’un contenu principalement textuel. |
| unfurl_media |booléenne |no |query |(aucun) |Transmettre false pour désactiver le déploiement d’un contenu multimédia. |
| icon_url |string |no |query |(aucun) |URL d’une image à utiliser comme icône pour ce message |
| icon_emoji |string |no |query |(aucun) |Emoji à utiliser comme icône pour ce message |

### <a name="here-are-the-possible-responses"></a>Voici les réponses possibles :
| Nom | Description |
| --- | --- |
| 200 |OK |
| 400 |Demande incorrecte |
| 408 |Délai d’expiration de la demande |
| 429 |Trop de demandes |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| 503 |Service Slack indisponible |
| 504 |Dépassement du délai de la passerelle |
| default |L’opération a échoué. |

- - -
## <a name="object-definitions"></a>Définition(s) d’objet :
 **Message**: message Yammer

Propriétés requises pour Message :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| id |integer |
| content_excerpt |string |
| sender_id |integer |
| replied_to_id |integer |
| created_at |string |
| network_id |integer |
| message_type |string |
| sender_type |string |
| url |string |
| web_url |string |
| group_id |integer |
| body |non défini |
| thread_id |integer |
| direct_message |booléenne |
| client_type |string |
| client_url |string |
| language |string |
| notified_user_ids |array |
| privacy |string |
| liked_by |non défini |
| system_message |booléenne |

 **PostOperationRequest**: représente une demande de publication sur Yammer pour le connecteur Yammer

Propriétés requises pour PostOperationRequest :

body

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| body |string |
| group_id |integer |
| replied_to_id |integer |
| direct_to_id |integer |
| broadcast |booléenne |
| topic1 |string |
| topic2 |string |
| topic3 |string |
| topic4 |string |
| topic5 |string |
| topic6 |string |
| topic7 |string |
| topic8 |string |
| topic9 |string |
| topic10 |string |
| topic11 |string |
| topic12 |string |
| topic13 |string |
| topic14 |string |
| topic15 |string |
| topic16 |string |
| topic17 |string |
| topic18 |string |
| topic19 |string |
| topic20 |string |

 **MessageList**: liste des messages

Propriétés requises pour MessageList :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| messages |array |

 **MessageBody**: corps du message

Propriétés requises pour MessageBody :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| parsed |string |
| plain |string |
| rich |string |

 **LikedBy**: aimé par

Propriétés requises pour LikedBy :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| count |integer |
| names |array |

 **YammmerEntity**: aimé par

Propriétés requises pour YammmerEntity :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| type |string |
| id |integer |
| full_name |string |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)

## <a name="object-definitions"></a>Définition(s) d’objet :
 **WebResultModel**: résultats de la recherche sur le web par Bing

Propriétés requises pour WebResultModel :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| Intitulé |string |
| Description |string |
| DisplayUrl |string |
| id |string |
| FullUrl |string |

 **VideoResultModel**: résultats de la recherche de vidéos par Bing

Propriétés requises pour VideoResultModel :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| Intitulé |string |
| DisplayUrl |string |
| id |string |
| MediaUrl |string |
| Runtime |integer |
| Miniature |non défini |

 **ThumbnailModel**: propriétés de miniature de l’élément multimédia

Propriétés requises pour ThumbnailModel :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| MediaUrl |string |
| ContentType |string |
| Largeur |integer |
| Hauteur |integer |
| FileSize |integer |

 **ImageResultModel**: résultats de la recherche d’images par Bing

Propriétés requises pour ImageResultModel :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| Intitulé |string |
| DisplayUrl |string |
| id |string |
| MediaUrl |string |
| SourceUrl |string |
| Miniature |non défini |

 **NewsResultModel**: résultats de la recherche d’actualités par Bing

Propriétés requises pour NewsResultModel :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| Intitulé |string |
| Description |string |
| DisplayUrl |string |
| id |string |
| Source |string |
| Date |string |

 **SpellResultModel**: résultats des suggestions orthographiques de Bing

Propriétés requises pour SpellResultModel :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| id |string |
| Valeur |string |

 **RelatedSearchResultModel**: résultats de la recherche liée par Bing

Propriétés requises pour RelatedSearchResultModel :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| Intitulé |string |
| id |string |
| BingUrl |string |

 **CompositeSearchResultModel**: résultats de la recherche composite par Bing

Propriétés requises pour CompositeSearchResultModel :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| WebResultsTotal |integer |
| ImageResultsTotal |integer |
| VideoResultsTotal |integer |
| NewsResultsTotal |integer |
| SpellSuggestionsTotal |integer |
| WebResults |array |
| ImageResults |array |
| VideoResults |array |
| NewsResults |array |
| SpellSuggestionResults |array |
| RelatedSearchResults |array |

## <a name="object-definitions"></a>Définition(s) d’objet :
 **PostOperationResponse**: représente une réponse de l’opération de publication du connecteur Slack pour publier sur Slack

Propriétés requises pour PostOperationResponse :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| OK |booléenne |
| channel |string |
| ts |string |
| Message |non défini |
| error |string |

 **MessageItem**: message de canal.

Propriétés requises pour MessageItem :

Aucune des propriétés n’est obligatoire. 

**Toutes les propriétés**: 

| Nom | Type de données |
| --- | --- |
| texte |string |
| id |string |
| user |string |
| created |integer |
| is_user-deleted |booléenne |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../app-service-logic/app-service-logic-create-a-logic-app.md)

[1]: ./media/connectors-create-api-slack/connectionconfig1.png
[2]: ./media/connectors-create-api-slack/connectionconfig2.png 
[3]: ./media/connectors-create-api-slack/connectionconfig3.png
[4]: ./media/connectors-create-api-slack/connectionconfig4.png
[5]: ./media/connectors-create-api-slack/connectionconfig5.png
[6]: ./media/connectors-create-api-slack/connectionconfig6.png



<!--HONumber=Nov16_HO3-->


