---
title: "Décodage EDIFACT B2B Logic Apps contenant un segment UNH2.5 - Azure Logic Apps | Documents Microsoft"
description: "Décodage EDIFACT B2B contenant un segment UNH2.5 - Azure Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: padmavc
manager: anneta
editor: 
ms.assetid: cf44af18-1fe5-41d5-9e06-cc57a968207c
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 04/27/2017
ms.author: LADocs; padmavc
ms.translationtype: Human Translation
ms.sourcegitcommit: f6006d5e83ad74f386ca23fe52879bfbc9394c0f
ms.openlocfilehash: 90c9b65062acbe78196e220ba167997d44cdce90
ms.contentlocale: fr-fr
ms.lasthandoff: 05/03/2017


---
# <a name="how-to-handle-edifact-documents-having-unh25-segment"></a>Gérer les documents EDIFACT contenant un segment UNH2.5
Lorsque UNH2.5 est présent dans le document EDIFACT, il est utilisé pour la recherche de schéma. 

Exemple : le champ UNH est **EAN008** dans le message EDIFACT  
UNH+SSDD1+ORDERS:D:03B:UN:**EAN008**'  

Étapes à suivre pour gérer le message 
1. Mettre à jour le schéma
2. Vérifier les paramètres de l’accord  

## <a name="update-the-schema"></a>Mettre à jour le schéma
Pour traiter le message, vous devez déployer un schéma avec le nom de nœud racine UNH2.5.  Pour l’exemple donné, le nom racine du schéma est **EFACT_D03B_ORDERS_EAN008**  

Pour chaque D03B_ORDERS avec un autre segment UNH2.5, vous devez déployer un schéma individuel.  

## <a name="add-schema-to-the-edifact-agreement"></a>Ajouter un schéma à l’accord EDIFACT
### <a name="edifact-decode"></a>Décodage EDIFACT
Pour décoder le message entrant, configurez le schéma EDIFACT dans les paramètres de réception de l’accord EDIFACT.
1. Ajouter le schéma au compte d’intégration    
2. Configurez le schéma EDIFACT dans les paramètres de réception de l’accord EDIFACT. 
3. Sélectionnez un accord EDIFACT et cliquez sur **Modifier au format JSON**.  Ajoutez la valeur UNH2.5 dans l’accord de réception **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image1.png).

### <a name="edifact-encode"></a>Encodage EDIFACT
Pour encoder le message entrant, configurez le schéma EDIFACT dans les paramètres d’envoi de l’accord EDIFACT.
1. Ajouter le schéma au compte d’intégration    
2. Configurez le schéma EDIFACT dans les paramètres d’envoi de l’accord EDIFACT. 
3. Sélectionnez un accord EDIFACT et cliquez sur **Modifier au format JSON**.  Ajoutez la valeur UNH2.5 dans l’accord d’envoi **schemaReferences**
![](./media/logic-apps-enterprise-integration-edifact_inputfile_unh2.5/image2.png)

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur les contrats de compte d’intégration](../logic-apps/logic-apps-enterprise-integration-agreements.md "Découvrez les contrats d’intégration d’entreprise")  
