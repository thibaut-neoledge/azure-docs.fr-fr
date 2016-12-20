---
title: "Schéma de suivi x12 | Microsoft Docs"
description: "En savoir plus sur le schéma de suivi X12"
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: a5413f80-eaad-4bcf-b371-2ad0ef629c3d
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: 48dfc8327b7e2dfb16b0392fd6d44e83bdfb1177


---
# <a name="x12-tracking-schemas"></a>Schémas de suivi X12
Les schémas de suivi X12 pris en charge sont

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

| Propriété | Description |
| --- | --- |
| senderPartnerName |Chaîne facultative.  Cette propriété indique le nom du partenaire X12 expéditeur du message. |
| receiverPartnerName |Chaîne facultative.  Cette propriété indique le nom du partenaire X12 destinataire du message. |
| senderQualifier |Chaîne obligatoire.  Cette propriété indique le qualificateur du partenaire expéditeur. |
| senderIdentifier |Chaîne obligatoire.  Cette propriété indique l’identificateur du partenaire expéditeur. |
| receiverQualifier |Chaîne obligatoire.  Cette propriété indique le qualificateur du partenaire destinataire. |
| receiverIdentifier |Chaîne obligatoire.  Cette propriété indique l’identificateur du partenaire destinataire. |
| agreementName |Chaîne facultative.  Nom du contrat X12 dans lequel les messages sont résolus. |
| direction |Enum obligatoire.  Cette propriété indique la direction du flux de messages.  Les valeurs autorisées sont receive ou send. |
| interchangeControlNumber |Chaîne facultative.  Cette propriété indique le numéro de contrôle de l’échange. |
| functionalGroupControlNumber |Chaîne facultative.  Cette propriété indique le numéro de contrôle fonctionnel. |
| transactionSetControlNumber |Chaîne facultative.  Cette propriété indique le numéro de contrôle du document informatisé. |
| CorrelationMessageId |Chaîne facultative.  Cette propriété indique l’ID de message de corrélation.  L’D de corrélation correspond à {AgreementName}*{GroupControlNumber}*{TransactionSetControlNumber}. |
| messageType |Chaîne facultative. Cette propriété indique le type de document ou de document informatisé. |
| isMessageFailed |Valeur booléenne obligatoire.  Cette propriété indique si le message X12 a réussi ou a échoué. |
| isTechnicalAcknowledgmentExpected |Valeur booléenne obligatoire.  Cette propriété indique si l’accusé de réception technique est configuré ou non dans le contrat X12. |
| isFunctionalAcknowledgmentExpected |Valeur booléenne obligatoire.  Cette propriété indique si l’accusé de réception fonctionnel est configuré ou non dans le contrat X12. |
| needAk2LoopForValidMessages |Valeur booléenne obligatoire.  Indique si la boucle AK2 est requise pour les messages valides ou non. Type : booléen. |
| segmentsCount |Entier facultatif.  Cette propriété indique le nombre de segments dans le document informatisé X12. |

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

| Propriété | Description |
| --- | --- |
| senderPartnerName |Chaîne facultative.  Cette propriété indique le nom du partenaire X12 expéditeur du message. |
| receiverPartnerName |Chaîne facultative.  Cette propriété indique le nom du partenaire X12 destinataire du message. |
| senderQualifier |Chaîne obligatoire.  Cette propriété indique le qualificateur du partenaire expéditeur. |
| senderIdentifier |Chaîne obligatoire.  Cette propriété indique l’identificateur du partenaire expéditeur. |
| receiverQualifier |Chaîne obligatoire.  Cette propriété indique le qualificateur du partenaire destinataire. |
| receiverIdentifier |Chaîne obligatoire.  Cette propriété indique l’identificateur du partenaire destinataire. |
| agreementName |Chaîne facultative.  Nom du contrat X12 dans lequel les messages sont résolus. |
| direction |Enum obligatoire.  Cette propriété indique la direction du flux de messages.  Les valeurs autorisées sont receive ou send. |
| interchangeControlNumber |Chaîne facultative.  Cette propriété indique le numéro de contrôle de l’échange de l’accusé de réception fonctionnel. La valeur est uniquement indiquée pour l’envoi si un accusé de réception fonctionnel a été reçu pour les messages envoyés au partenaire. |
| functionalGroupControlNumber |Chaîne facultative.  Cette propriété indique le numéro de contrôle du groupe fonctionnel de l’accusé de réception fonctionnel. La valeur est uniquement indiquée pour l’envoi si un accusé de réception fonctionnel a été reçu pour les messages envoyés au partenaire. |
| isaSegment |Chaîne facultative.  Cette propriété indique le segment ISA du message. La valeur est uniquement indiquée pour l’envoi si un accusé de réception fonctionnel a été reçu pour les messages envoyés au partenaire. |
| gsSegment |Chaîne facultative.  Cette propriété indique le segment GS du message. La valeur est uniquement indiquée pour l’envoi si un accusé de réception fonctionnel a été reçu pour les messages envoyés au partenaire. |
| respondingfunctionalGroupControlNumber |Chaîne facultative.  Cette propriété indique le numéro de contrôle de l’échange de réponse. |
| respondingFunctionalGroupId |Chaîne facultative. Cette propriété indique l’ID du groupe fonctionnel de réponse qui est mappé à AK101 dans l’accusé de réception. |
| respondingtransactionSetControlNumber |Chaîne facultative.  Cette propriété indique le numéro de contrôle du document informatisé de réponse. |
| respondingTransactionSetId |Chaîne facultative.  Cette propriété indique l’ID du document informatisé de réponse, qui est mappé à AK201 dans l’accusé de réception. |
| statusCode |Valeur booléenne obligatoire.  Cette propriété indique le code d’état de l’accusé de réception du document informatisé. |
| segmentsCount |Enum obligatoire.  Cette propriété indique le code d’état de l’accusé de réception.  Les valeurs autorisées sont Accepted, Rejected ou AcceptedWithErrors. |
| processingStatus |Enum obligatoire.  Cette propriété indique l’état de traitement de l’accusé de réception.  Les valeurs autorisées sont Received, Generated ou Sent. |
| CorrelationMessageId |Chaîne facultative.  Cette propriété indique l’ID de message de corrélation.  L’D de corrélation correspond à {AgreementName}*{GroupControlNumber}*{TransactionSetControlNumber}. |
| isMessageFailed |Valeur booléenne obligatoire.  Cette propriété indique si le message X12 a réussi ou a échoué. |
| ak2Segment |Chaîne facultative. Cette propriété indique le segment ak2. Le segment ak2 indique un accusé de réception pour un document informatisé dans le groupe fonctionnel reçu. |
| ak3Segment |Chaîne facultative. Cette propriété indique le segment ak3.  Le segment ak3 signale les erreurs dans un segment de données. |
| ak5Segment |Chaîne facultative. Cette propriété indique le segment ak5.  Le segment ak5 indique si le document informatisé identifié dans le segment AK2 est accepté ou rejeté et pourquoi. |

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

| Propriété | Description |
| --- | --- |
| senderPartnerName |Chaîne facultative.  Cette propriété indique le nom du partenaire X12 expéditeur du message. |
| receiverPartnerName |Chaîne facultative.  Cette propriété indique le nom du partenaire X12 destinataire du message. |
| senderQualifier |Chaîne obligatoire.  Cette propriété indique le qualificateur du partenaire expéditeur. |
| senderIdentifier |Chaîne obligatoire.  Cette propriété indique l’identificateur du partenaire expéditeur. |
| receiverQualifier |Chaîne obligatoire.  Cette propriété indique le qualificateur du partenaire destinataire. |
| receiverIdentifier |Chaîne obligatoire.  Cette propriété indique l’identificateur du partenaire destinataire. |
| agreementName |Chaîne facultative.  Nom du contrat X12 dans lequel les messages sont résolus. |
| direction |Enum obligatoire.  Cette propriété indique la direction du flux de messages.  Les valeurs autorisées sont receive ou send. |
| interchangeControlNumber |Chaîne facultative.  Cette propriété indique le numéro de contrôle de l’échange. |
| isaSegment |Chaîne facultative.  Cette propriété indique le segment ISA du message. |
| isTechnicalAcknowledgmentExpected |Valeur booléenne obligatoire.  Cette propriété indique si l’accusé de réception technique est configuré ou non dans le contrat X12. |
| isMessageFailed |Valeur booléenne obligatoire.  Cette propriété indique si le message X12 a réussi ou a échoué. |
| isa09 |Chaîne facultative.  Cette propriété indique la date d’échange du document X12. |
| isa10 |Chaîne facultative. Cette propriété indique l’heure d’échange du document X12. |
| isa11 |Chaîne facultative. Cette propriété indique l’identificateur des standards de contrôle d’échange X12. |
| isa12 |Chaîne facultative.  Cette propriété indique le numéro de version du contrôle d’échange X12. |
| isa14 |Chaîne facultative.  Cette propriété indique si un accusé de réception X12 est demandé. |
| isa15 |Chaîne facultative.  Cette propriété indique l’indicateur de test ou de production. |
| isa16 |Chaîne facultative. Cette propriété indique le séparateur d’éléments. |

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

| Propriété | Description |
| --- | --- |
| senderPartnerName |Chaîne facultative.  Cette propriété indique le nom du partenaire X12 expéditeur du message. |
| receiverPartnerName |Chaîne facultative.  Cette propriété indique le nom du partenaire X12 destinataire du message. |
| senderQualifier |Chaîne obligatoire.  Cette propriété indique le qualificateur du partenaire expéditeur. |
| senderIdentifier |Chaîne obligatoire.  Cette propriété indique l’identificateur du partenaire expéditeur. |
| receiverQualifier |Chaîne obligatoire.  Cette propriété indique le qualificateur du partenaire destinataire. |
| receiverIdentifier |Chaîne obligatoire.  Cette propriété indique l’identificateur du partenaire destinataire. |
| agreementName |Chaîne facultative.  Nom du contrat X12 dans lequel les messages sont résolus. |
| direction |Enum obligatoire.  Cette propriété indique la direction du flux de messages.  Les valeurs autorisées sont receive ou send. |
| interchangeControlNumber |Chaîne facultative.  Cette propriété indique le numéro de contrôle de l’échange de l’accusé de réception technique. L’accusé de réception technique reçu des partenaires a cette valeur. |
| isaSegment |Chaîne facultative.  Cette propriété indique le segment ISA de l’accusé de réception technique. L’accusé de réception technique reçu des partenaires a cette valeur. |
| respondingInterchangeControlNumber |Chaîne facultative.  Cette propriété indique le numéro de contrôle de l’échange de l’accusé de réception technique. L’accusé de réception technique reçu des partenaires a cette valeur. |
| isMessageFailed |Valeur booléenne obligatoire.  Cette propriété indique si le message X12 a réussi ou a échoué. |
| statusCode |Enum obligatoire.  Cette propriété indique le code d’état de l’accusé de réception de l’échange.  Les valeurs autorisées sont Accepted, Rejected ou AcceptedWithErrors. |
| processingStatus |Enum obligatoire.  Cette propriété indique l’état de l’accusé de réception.  Les valeurs autorisées sont Received, Generated ou Sent. |
| ta102 |Chaîne facultative. Cette propriété indique la date de l’échange. |
| ta103 |Chaîne facultative. Cette propriété indique l’heure de l’échange. |
| ta105 |Chaîne facultative. Cette propriété indique la copie de l’échange. |

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

| Propriété | Description |
| --- | --- |
| senderPartnerName |Chaîne facultative.  Cette propriété indique le nom du partenaire X12 expéditeur du message. |
| receiverPartnerName |Chaîne facultative.  Cette propriété indique le nom du partenaire X12 destinataire du message. |
| senderQualifier |Chaîne obligatoire.  Cette propriété indique le qualificateur du partenaire expéditeur. |
| senderIdentifier |Chaîne obligatoire.  Cette propriété indique l’identificateur du partenaire expéditeur. |
| receiverQualifier |Chaîne obligatoire.  Cette propriété indique le qualificateur du partenaire destinataire. |
| receiverIdentifier |Chaîne obligatoire.  Cette propriété indique l’identificateur du partenaire destinataire. |
| agreementName |Chaîne facultative.  Nom du contrat X12 dans lequel les messages sont résolus. |
| direction |Enum obligatoire.  Cette propriété indique la direction du flux de messages.  Les valeurs autorisées sont receive ou send. |
| interchangeControlNumber |Chaîne facultative. Cette propriété indique le numéro de contrôle de l’échange. |
| functionalGroupControlNumber |Facultatif. Numéro de contrôle fonctionnel. Type : chaîne. |
| gsSegment |Chaîne facultative.  Cette propriété indique le segment GS du message. |
| isTechnicalAcknowledgmentExpected |Valeur booléenne obligatoire.  Cette propriété indique si l’accusé de réception technique est configuré ou non dans le contrat X12. |
| isFunctionalAcknowledgmentExpected |Valeur booléenne obligatoire.  Cette propriété indique si l’accusé de réception fonctionnel est configuré ou non dans le contrat X12. |
| isMessageFailed |Valeur booléenne obligatoire.  Cette propriété indique si le message X12 a réussi ou a échoué. |
| gs01 |Chaîne facultative. Cette propriété indique le code d’identificateur fonctionnel. |
| gs02 |Chaîne facultative. Cette propriété indique le code de l’expéditeur de l’application. |
| gs03 |Chaîne facultative. Cette propriété indique le code du destinataire de l’application. |
| gs04 |Chaîne facultative. Cette propriété indique la date du groupe fonctionnel. |
| gs05 |Chaîne facultative. Cette propriété indique l’heure du groupe fonctionnel. |
| gs07 |Chaîne facultative. Cette propriété indique le code de l’agence responsable. |
| gs08 |Chaîne facultative. Cette propriété indique le code d’identificateur de la version/de l’activité. Type : chaîne. |

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

| Propriété | Description |
| --- | --- |
| senderPartnerName |Chaîne facultative.  Cette propriété indique le nom du partenaire X12 expéditeur du message. |
| receiverPartnerName |Chaîne facultative.  Cette propriété indique le nom du partenaire X12 destinataire du message. |
| senderQualifier |Chaîne obligatoire.  Cette propriété indique le qualificateur du partenaire expéditeur. |
| senderIdentifier |Chaîne obligatoire.  Cette propriété indique l’identificateur du partenaire expéditeur. |
| receiverQualifier |Chaîne obligatoire.  Cette propriété indique le qualificateur du partenaire destinataire. |
| receiverIdentifier |Chaîne obligatoire.  Cette propriété indique l’identificateur du partenaire destinataire. |
| agreementName |Chaîne facultative.  Nom du contrat X12 dans lequel les messages sont résolus. |
| direction |Enum obligatoire.  Cette propriété indique la direction du flux de messages.  Les valeurs autorisées sont receive ou send. |
| interchangeControlNumber |Chaîne facultative. Cette propriété indique le numéro de contrôle de l’échange. La valeur est indiquée pour l’envoi lorsqu’un accusé de réception technique a été reçu des partenaires. |
| functionalGroupControlNumber |Chaîne facultative. Cette propriété indique le numéro de contrôle du groupe fonctionnel de l’accusé de réception technique. La valeur est indiquée pour l’envoi lorsqu’un accusé de réception technique a été reçu des partenaires. |
| isaSegment |Facultatif. Identique à ci-dessus. Le numéro de contrôle de l’échange est indiqué uniquement dans des cas spécifiques. Type : chaîne. |
| gsSegment |Facultatif. Identique à ci-dessus. Le numéro de contrôle du groupe fonctionnel est indiqué uniquement dans des cas spécifiques. Type : chaîne. |
| respondingfunctionalGroupControlNumber |Facultatif. Numéro de contrôle du groupe fonctionnel d’origine. Type : chaîne. |
| respondingFunctionalGroupId |Facultatif. Mappe vers AK101 dans l’accusé de réception. ID du groupe fonctionnel. Type : chaîne. |
| isMessageFailed |Valeur booléenne obligatoire.  Cette propriété indique si le message X12 a réussi ou a échoué. |
| statusCode |Enum obligatoire.  Cette propriété indique le code d’état de l’accusé de réception. Les valeurs autorisées sont Accepted, Rejected ou AcceptedWithErrors. |
| processingStatus |Enum obligatoire.  Cette propriété indique l’état de traitement de l’accusé de réception. Les valeurs autorisées sont Received, Generated ou Sent. |
| ak903 |Chaîne facultative. Cette propriété indique le nombre de documents informatisés reçus. |
| ak904 |Chaîne facultative. Cette propriété indique le nombre de documents informatisés acceptés dans le groupe fonctionnel identifié. |
| ak9Segment |Chaîne facultative.  Le segment Ak9 indique si le groupe fonctionnel identifié dans le segment AK1 est accepté ou rejeté et pourquoi. |

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur le suivi des messages B2B](app-service-logic-monitor-b2b-message.md "Learn more about tracking B2B messages")   
[Suivi des messages B2B dans le portail OMS](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[En savoir plus sur le schéma de suivi personnalisé](app-service-logic-track-integration-account-custom-tracking-shema.md "Learn about Custom Schema")   
[En savoir plus sur le schéma de suivi AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md "Learn about AS2 Schema")   
[En savoir plus sur Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")  




<!--HONumber=Nov16_HO3-->


