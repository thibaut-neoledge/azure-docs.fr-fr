---
title: "Ajouter le connecteur Twilio à vos applications logiques | Microsoft Docs"
description: "Vue d’ensemble du connecteur Twilio avec les paramètres d’API REST"
services: 
documentationcenter: 
author: msftman
manager: erikre
editor: 
tags: connectors
ms.assetid: 43116187-4a2f-42e5-9852-a0d62f08c5fc
ms.service: logic-apps
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: integration
ms.date: 09/19/2016
ms.author: mandia
translationtype: Human Translation
ms.sourcegitcommit: b92f954680603891ced503a1134791312b5214f0
ms.openlocfilehash: 6770aa8387fe3d381fd9c566ca0c38c88a4b1e55


---
# <a name="get-started-with-the-twilio-connector"></a>Prise en main du connecteur Twilio
Connectez-vous à Twilio pour envoyer et recevoir des SMS, des MMS, et des adresses IP.

> [!NOTE]
> Cette version de l'article s'applique à la version de schéma 2015-08-01-preview des applications logiques.
> 
> 

Avec Twilio, vous pouvez :

* Créer votre flux d'activité en fonction des données que vous obtenez de Twilio. 
* Utiliser des actions pour obtenir un message, répertorier les messages, et plus encore. Ces actions obtiennent une réponse, puis mettent la sortie à la disposition d’autres actions. Par exemple, quand vous recevez un nouveau message Twilio, vous pouvez prendre ce message et l’utiliser comme flux de travail Service Bus. 

Pour ajouter une opération aux applications logiques, consultez [Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md).

## <a name="triggers-and-actions"></a>Déclencheurs et actions
Le connecteur Twilio inclut les actions suivantes. Il n'y a aucun déclencheur. 

| Déclencheurs | Actions |
| --- | --- |
| Aucun |<ul><li>Obtenir le message.</li><li>Répertorier les messages</li><li>Envoyer un message</li></ul> |

Tous les connecteurs prennent en charge les données aux formats JSON et XML. 

## <a name="create-a-connection-to-twilio"></a>Créer une connexion à Twilio
Quand vous ajoutez ce connecteur à vos applications logiques, entrez les valeurs Twilio suivantes :

| Propriété | Requis | Description |
| --- | --- | --- |
| ID de compte |Oui |Entrez votre ID de compte Twilio |
| Jeton d'accès |Oui |Entrez votre jeton d’accès Twilio |

> [!INCLUDE [Steps to create a connection to Twilio](../../includes/connectors-create-api-twilio.md)]
> 
> 

Si vous n’avez pas de jeton d’accès, consultez [Twilio](https://www.twilio.com/docs/api/ip-messaging/guides/identity) pour savoir comment en créer un.

> [!TIP]
> Vous pouvez utiliser cette même connexion Twilio dans d’autres applications logiques.
> 
> 

## <a name="swagger-rest-api-reference"></a>Informations de référence sur l'API REST Swagger
#### <a name="this-documentation-is-for-version-10"></a>Cette documentation concerne la version 1.0.
### <a name="get-message"></a>Obtenir le message.
Renvoie un message unique spécifié par l'ID du message fourni.  
```GET: /Messages/{MessageId}.json```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| MessageId |string |Oui |path |Aucun |ID de message |

### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 400 |Demande incorrecte |
| 404 |Message introuvable |
| 500 |Erreur interne du serveur. Une erreur inconnue s'est produite |
| default |L’opération a échoué. |

### <a name="list-messages"></a>Répertorier les messages
Renvoie une liste de messages associés à votre compte.  
```GET: /Messages.json```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| À |string |no |query |Aucun |Vers le numéro de téléphone |
| À partir |string |no |query |Aucun |À partir du numéro de téléphone |
| DateSent |string |no |query |Aucun |Afficher uniquement les messages envoyés à cette date (au format GMT), sous la forme AAAA-MM-JJ. Exemple : DateSent=2009-07-06. Vous pouvez également spécifier une inégalité, par exemple DateSent<=AAAA-MM-JJ pour les messages envoyés avant minuit à une certaine date, puis DateSent>=AAAA-MM-JJ pour les messages envoyés minuit à une certaine date. |
| PageSize |integer |no |query |50 |Nombre de ressources à retourner dans chaque page de la liste. 50 par défaut. |
| Page |integer |no |query |0 |Nombre de page. La valeur par défaut est 0. |

### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 400 |Demande incorrecte |
| 500 |Erreur interne du serveur. Une erreur inconnue s’est produite. |
| default |L’opération a échoué. |

### <a name="send-message"></a>Envoyer un message
Envoyez un message à un numéro de téléphone portable.  
```POST: /Messages.json```

| Nom | Type de données | Requis | Emplacement | Valeur par défaut | Description |
| --- | --- | --- | --- | --- | --- |
| sendMessageRequest | |Oui |body |Aucun |Message à envoyer |

### <a name="response"></a>Response
| Nom | Description |
| --- | --- |
| 200 |L’opération a réussi |
| 400 |Demande incorrecte |
| 500 |Erreur interne du serveur. Une erreur inconnue s'est produite |
| default |L’opération a échoué. |

## <a name="object-definitions"></a>Définitions d'objet
#### <a name="sendmessagerequest-request-model-for-send-message-operation"></a>SendMessageRequest : modèle de demande pour l'opération d'envoi de message
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| À partir |string |Oui |
| À |string |Oui |
| body |string |yes |
| media_url |array |no |
| status_callback |string |no |
| messaging_service_sid |string |no |
| application_sid |string |no |
| max_price |string |no |

#### <a name="message-model-for-message"></a>Message : modèle de message
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| body |string |no |
| À partir |string |no |
| À |string |no |
| status |string |no |
| sid |string |no |
| account_sid |string |no |
| api_version |string |no |
| num_segments |string |no |
| num_media |string |no |
| date_created |string |no |
| date_sent |string |no |
| date_updated |string |no |
| direction |string |no |
| error_code |string |no |
| error_message |string |no |
| price |string |no |
| price_unit |string |no |
| URI |string |no |
| subresource_uris |array |no |
| messaging_service_sid |string |no |

#### <a name="messagelist-response-model-for-list-messages-operation"></a>MessageList : modèle de réponse pour l'opération Répertorier les messages
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| messages |array |no |
| Page |integer |no |
| page_size |integer |no |
| num_pages |integer |no |
| URI |string |no |
| first_page_uri |string |no |
| next_page_uri |string |no |
| total |integer |no |
| previous_page_uri |string |no |

#### <a name="incomingphonenumberlist-response-model-for-list-messages-operation"></a>IncomingPhoneNumberList : modèle de réponse pour l'opération Répertorier les messages
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| incoming_phone_numbers |array |no |
| Page |integer |no |
| page_size |integer |no |
| num_pages |integer |no |
| URI |string |no |
| first_page_uri |string |no |
| next_page_uri |string |no |

#### <a name="addincomingphonenumberrequest-request-model-for-add-incoming-number-operation"></a>AddIncomingPhoneNumberRequest : modèle de demande pour l'opération Ajouter un numéro entrant
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| PhoneNumber |string |Oui |
| AreaCode |string |no |
| FriendlyName |string |no |

#### <a name="incomingphonenumber-incoming-phone-number"></a>IncomingPhoneNumber : numéro de téléphone entrant
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| phone_number |string |no |
| friendly_name |string |no |
| sid |string |no |
| account_sid |string |no |
| date_created |string |no |
| date_updated |string |no |
| capabilities |non défini |no |
| status_callback |string |no |
| status_callback_method |string |no |
| api_version |string |no |

#### <a name="capabilities-phone-number-capabilities"></a>Capabilities : fonctionnalités du numéro de téléphone
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| mms |booléenne |no |
| sms |booléenne |no |
| voice |booléenne |no |

#### <a name="availablephonenumbers-available-phone-numbers"></a>AvailablePhoneNumbers : numéros de téléphone disponibles
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| phone_number |string |no |
| friendly_name |string |no |
| lata |string |no |
| latitude |string |no |
| longitude |string |no |
| postal_code |string |no |
| rate_center |string |no |
| region |string |no |
| mms |booléenne |no |
| sms |booléenne |no |
| voice |booléenne |no |

#### <a name="usagerecords-usage-records-class"></a>UsageRecords : classe d'enregistrements d'utilisation
| Nom de la propriété | Type de données | Requis |
| --- | --- | --- |
| category |string |no |
| usage |string |no |
| usage_unit |string |no |
| Description |string |no |
| price |number |no |
| price_unit |string |no |
| count |string |no |
| count_unit |string |no |
| start_date |string |no |
| end_date |string |no |

## <a name="next-steps"></a>Étapes suivantes
[Créer une application logique](../logic-apps/logic-apps-create-a-logic-app.md)




<!--HONumber=Jan17_HO3-->


