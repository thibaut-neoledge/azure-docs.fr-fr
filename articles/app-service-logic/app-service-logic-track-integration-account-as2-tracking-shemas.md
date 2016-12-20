---
title: "Schéma de suivi AS2 | Microsoft Docs"
description: "En savoir plus sur le schéma de suivi AS2"
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: f169c411-1bd7-4554-80c1-84351247bf94
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: 1fd5bd1038be6c35cf5fc05c9cfa000d818c5e70


---

# <a name="as2-tracking-schemas"></a>Schémas de suivi AS2
Les schémas de suivi AS2 suivant sont pris en charge :

* Schéma de suivi de message AS2
* Schéma de suivi de MDN AS2

## <a name="as2-message-tracking-schema"></a>Schéma de suivi de message AS2
````java

    {
       "agreementProperties": {  
            "senderPartnerName": "",  
            "receiverPartnerName": "",  
            "as2To": "",  
            "as2From": "",  
            "agreementName": ""  
        },  
        "messageProperties": {
            "direction": "",
            "messageId": "",
            "dispositionType": "",
            "fileName": "",
            "isMessageFailed": "",
            "isMessageSigned": "",
            "isMessageEncrypted": "",
            "isMessageCompressed": "",
            "correlationMessageId": "",
            "incomingHeaders": {
            },
            "outgoingHeaders": {
            },
        "isNrrEnabled": "",
        "isMdnExpected": "",
        "mdnType": ""
        }
    }
````

| Propriété | Description |
| --- | --- |
| senderPartnerName |Chaîne facultative.  Cette propriété indique le nom du partenaire AS2 expéditeur du message. |
| receiverPartnerName |Chaîne facultative.  Cette propriété indique le nom du partenaire AS2 destinataire du message. |
| as2To |Chaîne obligatoire.  Cette propriété indique le nom du destinataire du message AS2 inclus dans les en-têtes du message AS2. |
| as2From |Chaîne obligatoire. Cette propriété indique le nom de l’expéditeur du message AS2 inclus dans les en-têtes du message AS2. |
| agreementName |Chaîne facultative.  Nom du contrat AS2 dans lequel les messages sont résolus. |
| direction |Chaîne obligatoire.  Cette propriété indique la direction du flux de messages (réception ou expédition). |
| messageId |Chaîne facultative.  Cette propriété indique l’ID du message AS2 inclus dans les en-têtes du message AS2. |
| dispositionType |Chaîne facultative. Cette propriété indique la valeur du type de disposition du MDN. |
| fileName |Chaîne facultative.  Cette propriété indique le nom de fichier inclus dans l’en-tête du message AS2. |
| isMessageFailed |Valeur booléenne obligatoire.  Cette propriété indique si le message AS2 a échoué ou non. |
| isMessageSigned |Valeur booléenne obligatoire.  Cette propriété indique si le message AS2 est signé ou non. |
| isMessageEncrypted |Valeur booléenne obligatoire. Cette propriété indique si le message AS2 est chiffré ou non. |
| isMessageCompressed |Valeur booléenne obligatoire. Cette propriété indique si le message AS2 est compressé ou non. |
| correlationMessageId |Chaîne facultative. Cette propriété indique l’ID de message AS2 permettant de mettre en corrélation les messages avec les MDN. |
| incomingHeaders |Dictionnaire de JToken facultatif.  Cette propriété indique les détails de l’en-tête de message AS2 entrant. |
| outgoingHeaders |Dictionnaire de JToken facultatif.  Cette propriété indique les détails de l’en-tête de message AS2 sortant. |
| isNrrEnabled |Valeur booléenne obligatoire.  Utilisez la valeur par défaut si cette valeur n’est pas connue. |
| isMdnExpected |Valeur booléenne obligatoire. Utilisez la valeur par défaut si cette valeur n’est pas connue. |
| mdnType |Enum obligatoire. Les valeurs autorisées sont NotConfigured, Sync ou Async. |

## <a name="as2-mdn-tracking-schema"></a>Schéma de suivi de MDN AS2
````java

    {
        "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "as2To": "",
                "as2From": "",
                "agreementName": "g"
            },
            "messageProperties": {
                "direction": "",
                "messageId": "",
                "originalMessageId": "",
                "dispositionType": "",
                "isMessageFailed": "",
                "isMessageSigned": "",
                "isNrrEnabled": "",
                "statusCode": "",
                "micVerificationStatus": "",
                "correlationMessageId": "",
                "incomingHeaders": { 
                },
                "outgoingHeaders": { 
                }
            }
    }
````

| Propriété | Description |
| --- | --- |
| senderPartnerName |Chaîne facultative. Cette propriété indique le nom du partenaire AS2 expéditeur du message. |
| receiverPartnerName |Chaîne facultative. Cette propriété indique le nom du partenaire AS2 destinataire du message. |
| as2To |Chaîne obligatoire. Cette propriété indique le nom du partenaire qui reçoit ce message AS2. |
| as2From |Chaîne obligatoire. Cette propriété indique le nom du partenaire qui envoie ce message AS2. |
| agreementName |Chaîne facultative. Nom du contrat AS2 dans lequel les messages sont résolus. |
| direction |Chaîne obligatoire. Cette propriété indique la direction du flux de messages (réception ou expédition). |
| messageId |Chaîne facultative. Cette propriété indique l’ID du message AS2. |
| originalMessageId |Chaîne facultative. Cette propriété indique l’ID du message AS2 d’origine. |
| dispositionType |Chaîne facultative. Cette propriété indique la valeur du type de disposition du MDN. |
| isMessageFailed |Valeur booléenne obligatoire. Cette propriété indique si le message AS2 a échoué ou non. |
| isMessageSigned |Valeur booléenne obligatoire. Cette propriété indique si le message AS2 est signé ou non. |
| isNrrEnabled |Valeur booléenne obligatoire.  Utilisez la valeur par défaut si cette valeur n’est pas connue. |
| statusCode |Enum obligatoire.  Les valeurs autorisées sont Accepted, Rejected ou AcceptedWithErrors. |
| micVerificationStatus |Enum obligatoire.  Les valeurs autorisées sont NotApplicable, Succeeded ou Failed. |
| correlationMessageId |Chaîne facultative.  Cette propriété indique l’ID de corrélation.  L’ID de corrélation est l’ID du message d’origine (ID de messages du message pour lequel le MDN est configuré). |
| incomingHeaders |Dictionnaire de JToken facultatif.  Cette propriété indique les détails de l’en-tête de message entrant. |
| outgoingHeaders |Dictionnaire de JToken facultatif.  Cette propriété indique les détails de l’en-tête de message sortant. |

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")    
[En savoir plus sur le suivi des messages B2B](app-service-logic-monitor-b2b-message.md "Learn more about tracking B2B messages")   
[Suivi des messages B2B dans le portail OMS](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[En savoir plus sur le schéma de suivi personnalisé B2B](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Schema")   
[En savoir plus sur le schéma de suivi X12](app-service-logic-track-integration-account-x12-tracking-shemas.md "Learn about X12 Tracking Schema")   
[En savoir plus sur Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")  




<!--HONumber=Nov16_HO3-->


