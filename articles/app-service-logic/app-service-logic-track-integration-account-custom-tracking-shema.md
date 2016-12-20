---
title: "Schéma de suivi personnalisé | Microsoft Docs"
description: "En savoir plus sur le schéma de suivi personnalisé"
author: padmavc
manager: erikre
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: 433ae852-a833-44d3-a3c3-14cca33403a2
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/31/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 63ba6e8fec8df1cdbc9928b011130f47c5facf89
ms.openlocfilehash: f846f23bef61e8e772920196b9c81d059546d8a6


---
# <a name="custom-tracking-schema"></a>Schéma de suivi personnalisé
## <a name="supported-custom-tracking-schema"></a>Schéma de suivi personnalisé pris en charge
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

| Propriété | Description |
| --- | --- |
| sourceType |Obligatoire.  Cette propriété indique le type de source d’exécution. Les valeurs autorisées sont Microsoft.Logic/workflows ou custom. |
| Source |Obligatoire. Si le type de source est Microsoft.Logic/workflows, les informations source doivent suivre ce schéma. Si le type de source est custom, le schéma est un JToken.  |
| systemId |Chaîne obligatoire.  Elle indique l’ID système de l’application logique. |
| runId |Chaîne obligatoire.  Elle indique l’ID d’exécution de l’application logique. |
| operationName |Chaîne obligatoire.  Elle indique le nom de l’opération (par exemple action ou déclencheur). |
| repeatItemScopeName |Chaîne obligatoire. Elle indique le nom de l’élément de répétition si l’action fait partie d’une boucle foreach ou until. |
| repeatItemIndex |Entier obligatoire.  Si l’action fait partie d’une boucle foreach ou until, il indique l’index de l’élément répété.  |
| trackingId |Chaîne facultative. Elle indique l’ID de suivi permettant de corréler les messages. |
| correlationId |Chaîne facultative. Elle indique l’ID de corrélation permettant de corréler les messages. |
| clientRequestId |Chaîne facultative.  Le client peut remplir ce champ pour corréler les messages. |
| eventLevel |Obligatoire. Cette propriété indique le niveau de l’événement. |
| eventTime |Obligatoire. Cette propriété indique l’heure de l’événement au format UTC AAAA-MM-DDTHH:MM:SS.00000Z |
| recordType |Obligatoire. Cette propriété indique le type de l’enregistrement de suivi. La valeur autorisée est Custom. |
| record |Obligatoire.  Cette propriété indique le type d’enregistrement personnalisé et le format autorisé est JToken. |
|  | |

## <a name="supported-b2b-protocol-tracking-schemas"></a>Protocole B2B pris en charge pour les schémas de suivi
* [Schéma de suivi AS2](app-service-logic-track-integration-account-as2-tracking-shemas.md)   
* [Schéma de suivi X12](app-service-logic-track-integration-account-x12-tracking-shemas.md) 

## <a name="next-steps"></a>Étapes suivantes
[En savoir plus sur le suivi des messages B2B](app-service-logic-monitor-b2b-message.md "Learn more about tracking B2B messages")   
[Suivi des messages B2B dans le portail OMS](app-service-logic-track-b2b-messages-omsportal.md "Tracking B2B messages")   
[En savoir plus sur Enterprise Integration Pack](app-service-logic-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")   




<!--HONumber=Nov16_HO3-->


