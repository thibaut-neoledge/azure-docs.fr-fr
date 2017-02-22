---
title: "Schémas de suivi personnalisé pour la surveillance B2B - Azure Logic Apps | Microsoft Docs"
description: "Créez des schémas de suivi personnalisé pour surveiller les messages B2B des transactions de votre compte d’intégration Azure."
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/27/2017
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: d090ce5a912a2079d2e47d13caf60ca701f0e548
ms.openlocfilehash: a3bb9851d0f7e6f40f456f7989ae949fb38549e0


---
# <a name="custom-tracking-schemas"></a>Schéma de suivi personnalisé
Vous pouvez utiliser un schéma de suivi personnalisé dans votre compte de l’intégration d’Azure pour vous aider à surveiller les transactions B2B.

## <a name="custom-tracking-schema"></a>Schéma de suivi personnalisé
````java

        {
            "sourceType": "",
            "source": {

            "workflow": {
                "systemId": ""
            },
            "runInstance": {
                "runId": ""
            },
            "operation": {
                "operationName": "",
                "repeatItemScopeName": "",
                "repeatItemIndex": "",
                "trackingId": "",
                "correlationId": "",
                "clientRequestId": ""
                }
            },
            "events": [
            {
                "eventLevel": "",
                "eventTime": "",
                "recordType": "",
                "record": {                
                }
            }
         ]
      }

````

| Propriété | Type | Description |
| --- | --- | --- |
| sourceType |   | Type de source d’exécution. Les valeurs autorisées sont **Microsoft.Logic/workflows** et **custom**. (obligatoire) |
| Source |   | Si le type de source est **Microsoft.Logic/workflows**, les informations source doivent suivre ce schéma. Si le type de source est **custom**, le schéma est un JToken. (obligatoire) |
| systemId | String | ID système d’application logique. (obligatoire) |
| runId | String | ID d’exécution d’application logique. (obligatoire) |
| operationName | String | Nom de l’opération (par exemple action ou déclencheur). (obligatoire) |
| repeatItemScopeName | String | Répéter le nom de l’élément si l’action se trouve au sein d’une boucle `foreach`/`until`. (obligatoire) |
| repeatItemIndex | Entier  | Indique si l’action se trouve au sein d’une boucle `foreach`/`until`. Indique l’index de l’élément répété. (obligatoire) |
| trackingId | String | ID de suivi permettant de corréler les messages. (facultatif) |
| correlationId | String | ID de corrélation permettant de corréler les messages. (facultatif) |
| clientRequestId | String | Le client peut remplir ce champ pour corréler les messages. (facultatif) |
| eventLevel |   | Niveau de l’événement. (obligatoire) |
| eventTime |   | Heure de l’événement au format UTC AAAA-MM-JJTHH:MM:SS.00000Z. (obligatoire) |
| recordType |   | Type de l’enregistrement de suivi. La valeur autorisée est **Custom**. (obligatoire) |
| record |   | Type d'enregistrement personnalisé. Le format autorisé est JToken. (obligatoire) |

## <a name="b2b-protocol-tracking-schemas"></a>Schémas de suivi de protocole B2B
Pour plus d’informations sur les schémas de suivi de protocole B2B, consultez :
* [Schémas de suivi AS2](../logic-apps/logic-apps-track-integration-account-as2-tracking-schemas.md)   
* [Schémas de suivi X12](logic-apps-track-integration-account-x12-tracking-schema.md)

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur le [suivi des messages B2B](logic-apps-monitor-b2b-message.md).   
* En savoir plus sur le [suivi de messages B2B dans le portail Operations Management Suite](../logic-apps/logic-apps-track-b2b-messages-omsportal.md).
* En savoir plus sur [Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md).



<!--HONumber=Jan17_HO4-->


