---
title: "Schémas de suivi X12 pour la surveillance B2B - Azure Logic Apps | Microsoft Docs"
description: "Utilisez des schémas de suivi X12 pour surveiller les messages B2B des transactions de votre compte d’intégration Azure."
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: LADocs; padmavc
ms.custom: H1Hack27Feb2017
ms.translationtype: Human Translation
ms.sourcegitcommit: 5913c81088724ef946ae147f4f3154fa6aefd22e
ms.openlocfilehash: c8c7642a28f1dc386a73d014abe13ec36c6cbe69
ms.contentlocale: fr-fr
ms.lasthandoff: 03/01/2017


---
# <a name="start-or-enable-tracking-of-x12-messages-to-monitor-success-errors-and-message-properties"></a>Démarrage ou activation du suivi des messages X12 pour surveiller la réussite, les erreurs et les propriétés de message
Vous pouvez utiliser ces schémas de suivi X12 dans votre compte d’intégration Azure pour surveiller les transactions B2B :

* Schéma de suivi de document informatisé X12
* Schéma de suivi d’accusé de réception de document informatisé X12
* Schéma de suivi d’échange X12
* Schéma de suivi d’accusé de réception d’échange X12
* Schéma de suivi de groupe fonctionnel X12
* Schéma de suivi d’accusé de réception de groupe fonctionnel X12

## <a name="x12-transaction-set-tracking-schema"></a>Schéma de suivi de document informatisé X12
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "transactionSetControlNumber": "",
                "CorrelationMessageId": "",
                "messageType": "",
                "isMessageFailed": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "needAk2LoopForValidMessages":  "",
                "segmentsCount": ""
            }
    }
````

| Propriété | Type | Description |
| --- | --- | --- |
| senderPartnerName | String | Nom de partenaire de l’expéditeur du message X12. (facultatif) |
| receiverPartnerName | String | Nom de partenaire du destinataire du message X12. (facultatif) |
| senderQualifier | String | Qualificateur du partenaire d’envoi. (obligatoire) |
| senderIdentifier | String | Identificateur du partenaire d’envoi. (obligatoire) |
| receiverQualifier | String | Qualificateur du partenaire de réception. (obligatoire) |
| receiverIdentifier | String | Identificateur du partenaire de réception. (obligatoire) |
| agreementName | String | Nom du contrat X12 dans lequel les messages sont résolus. (facultatif) |
| direction | Enum | Direction du flux de messages (envoi ou réception). (obligatoire) |
| interchangeControlNumber | String | Numéro de contrôle de l’échange. (facultatif) |
| functionalGroupControlNumber | String | Numéro de contrôle fonctionnel. (facultatif) |
| transactionSetControlNumber | String | Numéro de contrôle de document informatisé. (facultatif) |
| CorrelationMessageId | String | ID de message de corrélation. Correspond à {AgreementName}*{GroupControlNumber}*{TransactionSetControlNumber}. (facultatif) |
| messageType | String | Type de document ou de document informatisé. (facultatif) |
| isMessageFailed | Boolean | Indique si le message X12 a échoué. (obligatoire) |
| isTechnicalAcknowledgmentExpected | Boolean | Indique si l’accusé de réception technique est configuré dans le contrat X12. (obligatoire) |
| isFunctionalAcknowledgmentExpected | Boolean | Indique si l’accusé de réception fonctionnel est configuré dans le contrat X12. (obligatoire) |
| needAk2LoopForValidMessages | Boolean | Indique si la boucle AK2 est nécessaire pour un message valide. (obligatoire) |
| segmentsCount | Entier  | Nombre de segments dans le document informatisé X12. (facultatif) |

## <a name="x12-transaction-set-acknowledgement-tracking-schema"></a>Schéma de suivi d’accusé de réception de document informatisé X12
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "respondingtransactionSetControlNumber": "",
                "respondingTransactionSetId": "",
                "statusCode": "",
                "processingStatus": "",
                "CorrelationMessageId": ""
                "isMessageFailed": "",
                "ak2Segment": "",
                "ak3Segment": "",
                "ak5Segment": ""
            }
    }
````

| Propriété | Type | Description |
| --- | --- | --- |
| senderPartnerName | String | Nom de partenaire de l’expéditeur du message X12. (facultatif) |
| receiverPartnerName | String | Nom de partenaire du destinataire du message X12. (facultatif) |
| senderQualifier | String | Qualificateur du partenaire d’envoi. (obligatoire) |
| senderIdentifier | String | Identificateur du partenaire d’envoi. (obligatoire) |
| receiverQualifier | String | Qualificateur du partenaire de réception. (obligatoire) |
| receiverIdentifier | String | Identificateur du partenaire de réception. (obligatoire) |
| agreementName | String | Nom du contrat X12 dans lequel les messages sont résolus. (facultatif) |
| direction | Enum | Direction du flux de messages (envoi ou réception). (obligatoire) |
| interchangeControlNumber | String | Numéro de contrôle de l’échange de l’accusé de réception fonctionnel. Valeur renseignée uniquement pour l’envoi si un accusé de réception fonctionnel a été reçu pour les messages envoyés au partenaire. (facultatif) |
| functionalGroupControlNumber | String | Numéro de contrôle de groupe fonctionnel de l’accusé de réception fonctionnel. Valeur renseignée uniquement pour l’envoi si un accusé de réception fonctionnel a été reçu pour les messages envoyés au partenaire. (facultatif) |
| isaSegment | String | Segment ISA du message. Valeur renseignée uniquement pour l’envoi si un accusé de réception fonctionnel a été reçu pour les messages envoyés au partenaire. (facultatif) |
| gsSegment | String | Segment GS du message. Valeur renseignée uniquement pour l’envoi si un accusé de réception fonctionnel a été reçu pour les messages envoyés au partenaire. (facultatif) |
| respondingfunctionalGroupControlNumber | String | Numéro de contrôle de l’échange de réponse. (facultatif) |
| respondingFunctionalGroupId | String | ID du groupe fonctionnel de réponse qui est mappé à AK101 dans l’accusé de réception. (facultatif) |
| respondingtransactionSetControlNumber | String | Numéro de contrôle de document informatisé de réponse. (facultatif) |
| respondingTransactionSetId | String | ID du document informatisé de réponse, qui est mappé à AK201 dans l’accusé de réception. (facultatif) |
| statusCode | Boolean | Code d’état de l’accusé de réception du document informatisé. (obligatoire) |
| segmentsCount | Enum | Code d’état de l’accusé de réception. Les valeurs autorisées sont **Accepted**, **Rejected** et **AcceptedWithErrors**. (obligatoire) |
| processingStatus | Enum | État de traitement de l’accusé de réception. Les valeurs autorisées sont **Received**, **Generated** ou **Sent**. (obligatoire) |
| CorrelationMessageId | String | ID de message de corrélation. Correspond à {AgreementName}*{GroupControlNumber}*{TransactionSetControlNumber}. (facultatif) |
| isMessageFailed | Boolean | Indique si le message X12 a échoué. (obligatoire) |
| ak2Segment | String | Accusé de réception pour un document informatisé dans le groupe fonctionnel reçu. (facultatif) |
| ak3Segment | String | Signale les erreurs dans un segment de données. (facultatif) |
| ak5Segment | String | Signale si le document informatisé identifié dans le segment AK2 est accepté ou rejeté et pourquoi. (facultatif) |

## <a name="x12-interchange-tracking-schema"></a>Schéma de suivi d’échange X12
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "isa09": "",
                "isa10": "",
                "isa11": "",
                "isa12": "",
                "isa14": "",
                "isa15": "",
                "isa16": ""
            }
    }
````

| Propriété | Type | Description |
| --- | --- | --- |
| senderPartnerName | String | Nom de partenaire de l’expéditeur du message X12. (facultatif) |
| receiverPartnerName | String | Nom de partenaire du destinataire du message X12. (facultatif) |
| senderQualifier | String | Qualificateur du partenaire d’envoi. (obligatoire) |
| senderIdentifier | String | Identificateur du partenaire d’envoi. (obligatoire) |
| receiverQualifier | String | Qualificateur du partenaire de réception. (obligatoire) |
| receiverIdentifier | String | Identificateur du partenaire de réception. (obligatoire) |
| agreementName | String | Nom du contrat X12 dans lequel les messages sont résolus. (facultatif) |
| direction | Enum | Direction du flux de messages (envoi ou réception). (obligatoire) |
| interchangeControlNumber | String | Numéro de contrôle de l’échange. (facultatif) |
| isaSegment | String | Segment ISA du message. (facultatif) |
| isTechnicalAcknowledgmentExpected | Boolean | Indique si l’accusé de réception technique est configuré dans le contrat X12. (obligatoire) |
| isMessageFailed | Boolean | Indique si le message X12 a échoué. (obligatoire) |
| isa09 | String | Date d’échange du document X12. (facultatif) |
| isa10 | String | Heure d’échange du document X12. (facultatif) |
| isa11 | String | Identificateur des normes de contrôle d’échange X12. (facultatif) |
| isa12 | String | Numéro de version du contrôle d’échange X12. (facultatif) |
| isa14 | String | Un accusé de réception X12 est exigé. (facultatif) |
| isa15 | String | Indicateur de test ou de production. (facultatif) |
| isa16 | String | Séparateur d'éléments. (facultatif) |

## <a name="x12-interchange-acknowledgement-tracking-schema"></a>Schéma de suivi d’accusé de réception d’échange X12
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "isaSegment": "",
                "respondingInterchangeControlNumber": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ta102": "",
                "ta103": "",
                "ta105": ""
            }
    }
````

| Propriété | Type | Description |
| --- | --- | --- |
| senderPartnerName | String | Nom de partenaire de l’expéditeur du message X12. (facultatif) |
| receiverPartnerName | String | Nom de partenaire du destinataire du message X12. (facultatif) |
| senderQualifier | String | Qualificateur du partenaire d’envoi. (obligatoire) |
| senderIdentifier | String | Identificateur du partenaire d’envoi. (obligatoire) |
| receiverQualifier | String | Qualificateur du partenaire de réception. (obligatoire) |
| receiverIdentifier | String | Identificateur du partenaire de réception. (obligatoire) |
| agreementName | String | Nom du contrat X12 dans lequel les messages sont résolus. (facultatif) |
| direction | Enum | Direction du flux de messages (envoi ou réception). (obligatoire) |
| interchangeControlNumber | String | Numéro de contrôle de l’échange de l’accusé de réception technique reçu de partenaires. (facultatif) |
| isaSegment | String | Segment ISA de l’accusé de réception technique reçu de partenaires. (facultatif) |
| respondingInterchangeControlNumber |String | Numéro de contrôle de l’échange pour l’accusé de réception technique reçu de partenaires. (facultatif) |
| isMessageFailed | Boolean | Indique si le message X12 a échoué. (obligatoire) |
| statusCode | Enum | Code d’état de l’accusé de réception de l’échange. Les valeurs autorisées sont **Accepted**, **Rejected** et **AcceptedWithErrors**. (obligatoire) |
| processingStatus | Enum | État de l’accusé de réception. Les valeurs autorisées sont **Received**, **Generated** ou **Sent**. (obligatoire) |
| ta102 | String | Date de l’échange. (facultatif) |
| ta103 | String | Heure de l’échange. (facultatif) |
| ta105 | String | Code de note de l’échange. (facultatif) |

## <a name="x12-functional-group-tracking-schema"></a>Schéma de suivi de groupe fonctionnel X12
````java

    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "gsSegment": "",
                "isTechnicalAcknowledgmentExpected": "",
                "isFunctionalAcknowledgmentExpected": "",
                "isMessageFailed": "",
                "gs01": "",
                "gs02": "",
                "gs03": "",
                "gs04": "",
                "gs05": "",
                "gs07": "",
                "gs08": ""
            }
    }
````

| Propriété | Type | Description |
| --- | --- | --- |
| senderPartnerName | String | Nom de partenaire de l’expéditeur du message X12. (facultatif) |
| receiverPartnerName | String | Nom de partenaire du destinataire du message X12. (facultatif) |
| senderQualifier | String | Qualificateur du partenaire d’envoi. (obligatoire) |
| senderIdentifier | String | Identificateur du partenaire d’envoi. (obligatoire) |
| receiverQualifier | String | Qualificateur du partenaire de réception. (obligatoire) |
| receiverIdentifier | String | Identificateur du partenaire de réception. (obligatoire) |
| agreementName | String | Nom du contrat X12 dans lequel les messages sont résolus. (facultatif) |
| direction | Enum | Direction du flux de messages (envoi ou réception). (obligatoire) |
| interchangeControlNumber | String | Numéro de contrôle de l’échange. (facultatif) |
| functionalGroupControlNumber | String | Numéro de contrôle fonctionnel. (facultatif) |
| gsSegment | String | Segment GS de message. (facultatif) |
| isTechnicalAcknowledgmentExpected | Boolean | Indique si l’accusé de réception technique est configuré dans le contrat X12. (obligatoire) |
| isFunctionalAcknowledgmentExpected | Boolean | Indique si l’accusé de réception fonctionnel est configuré dans le contrat X12. (obligatoire) |
| isMessageFailed | Boolean | Indique si le message X12 a échoué. (obligatoire)|
| gs01 | String | Code d’identificateur fonctionnel. (facultatif) |
| gs02 | String | Code de l’expéditeur de l’application. (facultatif) |
| gs03 | String | Code du destinataire de l’application. (facultatif) |
| gs04 | String | Date du groupe fonctionnel. (facultatif) |
| gs05 | String | Heure du groupe fonctionnel. (facultatif) |
| gs07 | String | Code de l’agence responsable. (facultatif) |
| gs08 | String | Code identificateur de version/du secteur. (facultatif) |

## <a name="x12-functional-group-acknowledgement-tracking-schema"></a>Schéma de suivi d’accusé de réception de groupe fonctionnel X12
````java
    {
            "agreementProperties": {
                "senderPartnerName": "",
                "receiverPartnerName": "",
                "senderQualifier": "",
                "senderIdentifier": "",
                "receiverQualifier": "",
                "receiverIdentifier": "",
                "agreementName": ""
            },
            "messageProperties": {
                "direction": "",
                "interchangeControlNumber": "",
                "functionalGroupControlNumber": "",
                "isaSegment": "",
                "gsSegment": "",
                "respondingfunctionalGroupControlNumber": "",
                "respondingFunctionalGroupId": "",
                "isMessageFailed": "",
                "statusCode": "",
                "processingStatus": "",
                "ak903": "",
                "ak904": "",
                "ak9Segment": ""
            }
    }
````

| Propriété | Type | Description |
| --- | --- | --- |
| senderPartnerName | String | Nom de partenaire de l’expéditeur du message X12. (facultatif) |
| receiverPartnerName | String | Nom de partenaire du destinataire du message X12. (facultatif) |
| senderQualifier | String | Qualificateur du partenaire d’envoi. (obligatoire) |
| senderIdentifier | String | Identificateur du partenaire d’envoi. (obligatoire) |
| receiverQualifier | String | Qualificateur du partenaire de réception. (obligatoire) |
| receiverIdentifier | String | Identificateur du partenaire de réception. (obligatoire) |
| agreementName | String | Nom du contrat X12 dans lequel les messages sont résolus. (facultatif) |
| direction | Enum | Direction du flux de messages (envoi ou réception). (obligatoire) |
| interchangeControlNumber | String | Numéro de contrôle de l’échange, qui renseigne le côté envoi lors de la réception d’un accusé de réception technique de partenaires. (facultatif) |
| functionalGroupControlNumber | String | Numéro de contrôle de groupe fonctionnel de l’accusé de réception technique, qui renseigne le côté envoi lors de la réception d’un accusé de réception technique de partenaires. (facultatif) |
| isaSegment | String | Identique au numéro de contrôle de l’échange, mais renseigné uniquement dans des cas spécifiques. (facultatif) |
| gsSegment | String | Identique au numéro de contrôle de groupe fonctionnel, mais renseigné uniquement dans des cas spécifiques. (facultatif) |
| respondingfunctionalGroupControlNumber | String | Numéro de contrôle du groupe fonctionnel d’origine. (facultatif) |
| respondingFunctionalGroupId | String | Mappé à AK101 dans l’ID de groupe fonctionnel de l’accusé de réception. (facultatif) |
| isMessageFailed | Boolean | Indique si le message X12 a échoué. (obligatoire) |
| statusCode | Enum | Code d’état de l’accusé de réception. Les valeurs autorisées sont **Accepted**, **Rejected** et **AcceptedWithErrors**. (obligatoire) |
| processingStatus | Enum | État de traitement de l’accusé de réception. Les valeurs autorisées sont **Received**, **Generated** ou **Sent**. (obligatoire) |
| ak903 | String | Nombre maximal de documents informatisés reçus. (facultatif) |
| ak904 | String | Nombre de documents informatisés acceptés dans le groupe fonctionnel identifié. (facultatif) |
| ak9Segment | String | Indique si le groupe fonctionnel identifié dans le segment AK1 est accepté ou rejeté et pourquoi. (facultatif) |

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur le [suivi des messages B2B](logic-apps-monitor-b2b-message.md).
* En savoir plus sur les [schémas de suivi AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md).
* En savoir plus sur les [schémas de suivi personnalisé B2B](../logic-apps/logic-apps-track-integration-account-custom-tracking-schema.md).
* En savoir plus sur le [suivi de messages B2B dans le portail Operations Management Suite](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
* En savoir plus sur [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).  

