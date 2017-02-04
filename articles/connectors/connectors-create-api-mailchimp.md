---
title: "MailChimp | Microsoft Docs"
description: "Créez des applications logiques avec Azure App Service. MailChimp est un service SaaS qui permet aux entreprises de gérer et d’automatiser les activités de marketing par courrier électronique, y compris l’envoi de courriers électroniques de marketing, de messages automatisés et de campagnes ciblées."
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 36559de2-94f0-4355-b492-2926dfc56486
ms.service: logic-apps
ms.devlang: multiple
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 08/18/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: cee2c702c705e1451c0a6d01b140e6291b1e5ce1


---
# <a name="get-started-with-the-mailchimp-connector"></a>Prise en main du connecteur MailChimp
MailChimp est un service SaaS qui permet aux entreprises de gérer et d’automatiser les activités de marketing par courrier électronique, y compris l’envoi de courriers électroniques de marketing, de messages automatisés et de campagnes ciblées.

> [!NOTE]
> Cette version de l’article s’applique à la version de schéma 2015-08-01-preview des applications logiques.
> 
> 

Vous pouvez commencer par créer une application logique. Pour cela, consultez [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Le connecteur MailChimp peut être utilisé en tant qu’action ; il possède un ou plusieurs déclencheurs. Tous les connecteurs prennent en charge les données aux formats JSON et XML.

 Le connecteur MailChimp met à votre disposition les actions et/ou les déclencheurs ci-après.

### <a name="mailchimp-actions"></a>Actions de MailChimp
Vous pouvez effectuer les actions suivantes :

| Action | Description |
| --- | --- |
| [newcampaign](connectors-create-api-mailchimp.md#newcampaign) |Créer une nouvelle campagne basée sur un type de campagne, la liste des destinataires et les paramètres de la campagne (ligne d’objet, titre, from_name et reply_to) |
| [newlist](connectors-create-api-mailchimp.md#newlist) |Créer une nouvelle liste dans votre compte MailChimp |
| [addmember](connectors-create-api-mailchimp.md#addmember) |Ajouter ou mettre à jour un membre de la liste |
| [removemember](connectors-create-api-mailchimp.md#removemember) |Supprimer un membre d’une liste. |
| [updatemember](connectors-create-api-mailchimp.md#updatemember) |Mettre à jour les informations d’un membre spécifique de la liste |

### <a name="mailchimp-triggers"></a>Déclencheurs de MailChimp
Vous pouvez écouter les événements suivants :

| Déclencheur | Description |
| --- | --- |
| Lorsqu’un membre a été ajouté à une liste |Déclenche un flux de travail lorsqu’un nouveau membre a été ajouté à une liste |
| Lorsqu’une nouvelle liste est créée |Déclenche un flux de travail lors de la création d’une nouvelle liste |

## <a name="create-a-connection-to-mailchimp"></a>Créer une connexion à MailChimp
Pour créer des applications logiques avec MailChimp, vous devez d’abord créer une **connexion**, puis fournir les détails pour les propriétés suivantes :

| Propriété | Requis | Description |
| --- | --- | --- |
| Jeton |Oui |Fournir des informations d’identification MailChimp |

> [!INCLUDE [Steps to create a connection to MailChimp](../../includes/connectors-create-api-mailchimp.md)]
> 
> [!TIP]
> Vous pouvez utiliser cette connexion dans d'autres applications logiques.
> 
> 

## <a name="reference-for-mailchimp"></a>Référence pour MailChimp
S’applique à la version 1.0.

## <a name="newcampaign"></a>newcampaign
Nouvelle campagne : créer une nouvelle campagne basée sur un type de campagne, la liste des destinataires et les paramètres de la campagne (ligne d’objet, titre, from_name et reply_to)

```POST: /campaigns```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| newCampaignRequest | |yes |body |(aucun) |Objet JSON à envoyer dans le corps avec les nouveaux paramètres de demande de nouvelle campagne |

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

## <a name="newlist"></a>newlist
Nouvelle liste : créer une nouvelle liste dans votre compte MailChimp

```POST: /lists```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| newListRequest | |yes |body |(aucun) |Objet JSON à envoyer dans le corps avec les nouveaux paramètres de demande de nouvelle campagne |

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

## <a name="addmember"></a>addmember
Ajouter un membre à la liste : ajouter ou mettre à jour un membre de la liste

```POST: /lists/{list_id}/members```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |string |Oui |path |(aucun) |L’ID unique de la liste |
| newMemberInList | |yes |body |(aucun) |Objet JSON à envoyer dans le corps avec les informations du nouveau membre |

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

## <a name="removemember"></a>removemember
Supprimer le membre de la liste : supprimer un membre d’une liste.

```DELETE: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |string |Oui |path |(aucun) |L’ID unique de la liste |
| member_email |string |Oui |path |(aucun) |L’adresse de messagerie du membre à supprimer |

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

## <a name="updatemember"></a>updatemember
Mettre à jour les informations d’un membre : mettre à jour les informations d’un membre spécifique de la liste

```PATCH: /lists/replacemailwithhash/{list_id}/members/{member_email}```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |string |Oui |path |(aucun) |L’ID unique de la liste |
| member_email |string |Oui |path |(aucun) |L’adresse de messagerie unique du membre à mettre à jour |
| updateMemberInListRequest | |yes |body |(aucun) |Objet JSON à envoyer dans le corps avec les informations mises à jour du membre |

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

## <a name="onmembersubscribed"></a>OnMemberSubscribed
Lorsqu’un membre a été ajouté à une liste : déclenche un flux de travail lorsqu’un nouveau membre a été ajouté à une liste

```GET: /trigger/lists/{list_id}/members```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| list_id |string |Oui |path |(aucun) |L’ID unique de la liste |

#### <a name="response"></a>Réponse
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

## <a name="oncreatelist"></a>OnCreateList
Lorsqu’une nouvelle liste est créée : déclenche un flux de travail lors de la création d’une nouvelle liste

```GET: /trigger/lists```

Il n’existe aucun paramètre pour cet appel

#### <a name="response"></a>Réponse
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

## <a name="object-definitions"></a>Définitions d’objet
### <a name="newcampaignrequest"></a>NewCampaignRequest
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| type |string |Oui |
| recipients |non défini |Oui |
| paramètres |non défini |Oui |
| variate_settings |non défini |Non |
| tracking |non défini |Non |
| rss_opts |non défini |Non |
| social_card |non défini |Non |

### <a name="recipient"></a>Recipient
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| list_id |string |Oui |
| segment_opts |non défini |Non |

### <a name="settings"></a>Paramètres
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| subject_line |string |Oui |
| title |string |Non |
| from_name |string |Oui |
| reply_to |string |Oui |
| use_conversation |booléenne |Non |
| to_name |string |Non |
| folder_id |integer |Non |
| authentifier |booléenne |Non |
| auto_footer |booléenne |Non |
| inline_css |booléenne |Non |
| auto_tweet |booléenne |Non |
| auto_fb_post |array |Non |
| fb_comments |booléenne |Non |

### <a name="variatesettings"></a>Variate_Settings
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| winner_criteria |string |Non |
| wait_time |integer |Non |
| test_size |integer |Non |
| subject_lines |array |Non |
| send_times |array |Non |
| from_names |array |Non |
| reply_to_addresses |array |Non |

### <a name="tracking"></a>Suivi
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| opens |booléenne |Non |
| html_clicks |booléenne |Non |
| text_clicks |booléenne |Non |
| goal_tracking |booléenne |Non |
| ecomm360 |booléenne |Non |
| google_analytics |string |Non |
| clicktale |string |Non |
| salesforce |non défini |Non |
| highrise |non défini |Non |
| capsule |non défini |Non |

### <a name="rssopts"></a>RSS_Opts
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| feed_url |string |Non |
| frequency |string |Non |
| constrain_rss_img |string |Non |
| schedule |non défini |Non |

### <a name="socialcard"></a>Social_Card
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| image_url |string |Non |
| description |string |Non |
| title |string |Non |

### <a name="segmentopts"></a>Segment_Opts
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| saved_segment_id |integer |Non |
| match |string |Non |

### <a name="salesforce"></a>Salesforce
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| campaign |booléenne |Non |
| HDInsight |booléenne |Non |

### <a name="highrise"></a>Highrise
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| campaign |booléenne |Non |
| HDInsight |booléenne |Non |

### <a name="capsule"></a>Capsule
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| HDInsight |booléenne |Non |

### <a name="schedule"></a>Planification
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| hour |integer |Non |
| daily_send |non défini |Non |
| weekly_send_day |string |Non |
| monthly_send_date |number |Non |

### <a name="dailysend"></a>Daily_Send
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| sunday |booléenne |Non |
| monday |booléenne |Non |
| tuesday |booléenne |Non |
| wednesday |booléenne |Non |
| thursday |booléenne |Non |
| friday |booléenne |Non |
| saturday |booléenne |Non |

### <a name="campaignresponsemodel"></a>CampaignResponseModel
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |Non |
| type |string |Non |
| create_time |string |Non |
| archive_url |string |Non |
| status |string |Non |
| emails_sent |integer |Non |
| send_time |string |Non |
| content_type |string |Non |
| recipient |array |Non |
| paramètres |non défini |Non |
| variate_settings |non défini |Non |
| tracking |non défini |Non |
| rss_opts |non défini |Non |
| ab_split_opts |non défini |Non |
| social_card |non défini |Non |
| report_summary |non défini |Non |
| delivery_status |non défini |Non |
| _links |array |Non |

### <a name="absplitopts"></a>AB_Split_Opts
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| split_test |string |Non |
| pick_winner |string |Non |
| wait_units |string |Non |
| wait_time |integer |Non |
| split_size |integer |Non |
| from_name_a |string |Non |
| from_name_b |string |Non |
| reply_email_a |string |Non |
| reply_email_b |string |Non |
| subject_a |string |Non |
| subject_b |string |Non |
| send_time_a |string |Non |
| send_time_b |string |Non |
| send_time_winner |string |Non |

### <a name="reportsummary"></a>Report_Summary
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| opens |integer |Non |
| unique_opens |integer |Non |
| open_rate |number |Non |
| clicks |integer |Non |
| subscriber_clicks |number |Non |
| click_rate |number |Non |

### <a name="deliverystatus"></a>Delivery_Status
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| activé |booléenne |Non |
| can_cancel |booléenne |Non |
| status |string |Non |
| emails_sent |integer |Non |
| emails_canceled |integer |Non |

### <a name="link"></a>Lien
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| rel |string |Non |
| href |string |Non |
| statique |string |Non |
| targetSchema |string |Non |
| schema |string |Non |

### <a name="newlistrequest"></a>NewListRequest
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| Nom |string |Oui |
| contact |non défini |Oui |
| permission_reminder |string |Oui |
| use_archive_bar |booléenne |Non |
| campaign_defaults |non défini |Oui |
| notify_on_subscribe |string |Non |
| notify_on_unsubscribe |string |Non |
| email_type_option |booléenne |Oui |
| visibility |string |Non |

### <a name="contact"></a>Contact
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

### <a name="campaigndefaults"></a>Campaign_Defaults
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| from_name |string |Oui |
| from_email |string |Oui |
| subject |string |Non |
| language |string |Oui |

### <a name="createnewlistresponsemodel"></a>CreateNewListResponseModel
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |Oui |
| name |string |Oui |
| contact |non défini |Oui |
| permission_reminder |string |Oui |
| use_archive_bar |booléenne |Non |
| campaign_defaults |non défini |Oui |
| notify_on_subscribe |string |Non |
| notify_on_unsubscribe |string |Non |
| date_created |string |Non |
| list_rating |integer |Non |
| email_type_option |booléenne |Oui |
| subscribe_url_short |string |Non |
| subscribe_url_long |string |Non |
| beamer_address |string |Non |
| visibility |string |Non |
| modules |array |Non |
| stats |non défini |Non |
| _links |array |Non |

### <a name="stats"></a>Statistiques
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| member_count |integer |Non |
| unsubscribe_count |integer |Non |
| cleaned_count |integer |Non |
| member_count_since_send |integer |Non |
| unsubscribe_count_since_send |integer |Non |
| cleaned_count_since_send |integer |Non |
| campaign_count |integer |Non |
| campaign_last_sent |integer |Non |
| merge_field_count |integer |Non |
| avg_sub_rate |number |Non |
| avg_unsub_rate |number |Non |
| target_sub_rate |number |Non |
| open_rate |number |Non |
| click_rate |number |Non |
| last_sub_date |string |Non |
| last_unsub_date |string |Non |

### <a name="getlistsresponsemodel"></a>GetListsResponseModel
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| lists |array |Non |
| total_items |integer |Non |

### <a name="newmemberinlistrequest"></a>NewMemberInListRequest
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| email_type |string |Non |
| status |string |Oui |
| merge_fields |non défini |Non |
| interests |string |Non |
| language |string |Non |
| vip |booléenne |Non |
| location |non défini |Non |
| email_address |string |Oui |

### <a name="firstandlastname"></a>FirstAndLastName
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| FNAME |string |Non |
| LNAME |string |Non |

### <a name="location"></a>Lieu
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| latitude |number |Non |
| longitude |number |Non |

### <a name="memberresponsemodel"></a>MemberResponseModel
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |Non |
| email_address |string |Non |
| unique_email_id |string |Non |
| email_type |string |Non |
| status |string |Non |
| merge_fields |non défini |Non |
| interests |string |Non |
| stats |non défini |Non |
| ip_signup |string |Non |
| timestamp_signup |string |Non |
| ip_opt |string |Non |
| timestamp_opt |string |Non |
| member_rating |integer |Non |
| last_changed |string |Non |
| language |string |Non |
| vip |booléenne |Non |
| email_client |string |Non |
| location |non défini |Non |
| last_note |non défini |Non |
| list_id |string |Non |
| _links |array |Non |

### <a name="lastnote"></a>Last_Note
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| note_id |integer |Non |
| created_at |string |Non |
| created_by |string |Non |
| note |string |Non |

### <a name="getallmembersresponsemodel"></a>GetAllMembersResponseModel
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| membres |array |Non |
| list_id |string |Non |
| total_items |integer |Non |

### <a name="object"></a>Object
### <a name="updatememberinlistrequest"></a>UpdateMemberInListRequest
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| email_address |string |Non |
| email_type |string |Non |
| status |string |Oui |
| merge_fields |non défini |Non |
| interests |string |Non |
| language |string |Non |
| vip |booléenne |Non |
| location |non défini |Non |

### <a name="getmembersresponsemodel"></a>GetMembersResponseModel
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| membres |array |Non |
| list_id |string |Non |
| total_items |integer |Non |

### <a name="adduserresponsemodel"></a>AddUserResponseModel
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| id |string |Oui |
| email_address |string |Oui |
| unique_email_id |string |Non |
| email_type |string |Non |
| status |string |Non |
| merge_fields |non défini |Oui |
| interests |string |Non |
| stats |non défini |Non |
| ip_signup |string |Non |
| timestamp_signup |string |Non |
| ip_opt |string |Non |
| timestamp_opt |string |Non |
| member_rating |integer |Non |
| last_changed |string |Non |
| language |string |Non |
| vip |booléenne |Non |
| email_client |string |Non |
| location |non défini |Non |
| last_note |non défini |Non |
| list_id |string |Non |
| _links |array |Non |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


