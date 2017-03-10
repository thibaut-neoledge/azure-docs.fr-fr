---
title: Suivi de messages B2B dans le portail Operations Management Suite - Azure | Microsoft Docs
description: Guide pratique pour le suivi de messages B2B dans le portail Operations Management Suite
author: padmavc
manager: anneta
editor: 
services: logic-apps
documentationcenter: 
ms.assetid: bb7d9432-b697-44db-aa88-bd16ddfad23f
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 11/13/2016
ms.author: padmavc
translationtype: Human Translation
ms.sourcegitcommit: 2aa32d692da81dc34fafbc9cb6ea72a806f31e5c
ms.openlocfilehash: a4068c70a01f80086ff3fb55f8e101c7dce95dea
ms.lasthandoff: 02/27/2017


---
# <a name="track-b2b-messages-in-the-operations-management-suite-portal"></a>Suivi de messages B2B dans le portail Operations Management Suite
La communication B2B implique des échanges de messages entre deux processus ou applications métier en cours d’exécution. Utilisez les fonctionnalités de suivi web suivantes dans le portail Operations Management Suite pour vérifier si les messages sont traités correctement :

* Nombre et état des messages
* État des accusés de réception
* Corrélation entre les messages et les accusés de réception
* Descriptions détaillées des erreurs en cas d’échec
* Fonctionnalités de recherche

## <a name="prerequisites"></a>Composants requis
* Un compte Azure. Vous pouvez créer un [compte gratuit](https://azure.microsoft.com/free).
* Un compte d’intégration. Vous pouvez créer un [compte d’intégration](../logic-apps/logic-apps-enterprise-integration-create-integration-account.md) et configurer la journalisation. Pour configurer la journalisation, consultez [Surveiller des messages B2B](logic-apps-monitor-b2b-message.md).
* Une application logique. Vous pouvez créer une [application logique](../logic-apps/logic-apps-create-a-logic-app.md) et configurer la journalisation. Pour configurer la journalisation, consultez [Diagnostics et alertes Azure](../logic-apps/logic-apps-monitor-your-logic-apps.md#azure-diagnostics-and-alerts).

## <a name="add-logic-apps-b2b-solution-to-the-operations-management-suite-portal"></a>Ajouter une solution B2B d’applications logiques au portail Operations Management Suite

1. Dans le portail Azure, sélectionnez **Plus de services**, recherchez et sélectionnez **Log Analytics**.   
![Recherche de Log Analytics](media/logic-apps-track-b2b-messages-omsportal/browseloganalytics.png)  

2. Sélectionnez **Log Analytics**.  
![Sélection de Log Analytics](media/logic-apps-track-b2b-messages-omsportal/selectla.png)

3. Sélectionnez **Portail OMS**. La page d’accueil du portail Operations Management Suite s’affiche.   
![Parcourir le portail Operations Management Suite](media/logic-apps-track-b2b-messages-omsportal/omsportalpage.png)

4. Sélectionnez **Galerie de solutions**.    
![Sélectionnez Galerie de solutions](media/logic-apps-track-b2b-messages-omsportal/omshomepage1.png)

5. Sélectionnez **Logic Apps B2B**.     
![Sélection de Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage2.png)

6. Sélectionnez **Ajouter** pour ajouter des messages **Logic Apps B2B** à la page d’accueil.  
![Sélection de l’option Ajouter](media/logic-apps-track-b2b-messages-omsportal/omshomepage3.png)

7. La fonctionnalité **Messages Logic Apps B2B** s’affiche sur la page d’accueil.   
![Sélectionner la page d’accueil](media/logic-apps-track-b2b-messages-omsportal/omshomepage4.png)

## <a name="track-data-in-the-operations-management-suite-portal"></a>Suivi des données dans le portail Operations Management Suite

1. Une fois le traitement des messages terminé, le nombre de messages mis à jour s’affiche.   
![Messages mis à jour](media/logic-apps-track-b2b-messages-omsportal/omshomepage6.png)

2. Sélectionnez **Messages Logic Apps B2B** dans la page d’accueil pour afficher les états de message AS2 et X12.  Les données sont basées sur un seul jour.
![Sélection des messages Logic Apps B2B](media/logic-apps-track-b2b-messages-omsportal/omshomepage5.png)

3. Sélectionnez un message AS2 ou X12 par état pour accéder à la liste des messages. La capture d’écran suivante affiche le statut du message AS2. Les descriptions des propriétés des messages AS2 et X12 s’affichent plus loin sous « Descriptions des propriétés de la liste des messages ».  
![Sélectionner l’état de message AS2](media/logic-apps-track-b2b-messages-omsportal/as2messagelist.png)
4. Sélectionnez une ligne dans la liste des messages AS2 ou X12 pour accéder à la recherche dans les journaux.  La recherche dans les journaux répertorie toutes les actions avec le même ID d’exécution.
![Sélectionner l’état du message](media/logic-apps-track-b2b-messages-omsportal/logsearch.png)

## <a name="message-list-property-descriptions"></a>Description des propriétés de la liste des messages

#### <a name="as2-message-list-property-descriptions"></a>Description des propriétés de la liste des messages AS2

| Propriété | Description |
| --- | --- |
| Sender | Partenaire invité configuré dans les paramètres de réception ou partenaire hôte configuré dans les paramètres d’envoi pour un contrat AS2. |
| Receiver | Partenaire hôte configuré dans les paramètres de réception ou partenaire invité configuré dans les paramètres d’envoi pour un contrat AS2. |
| Application logique | Application logique dans laquelle les actions AS2 sont configurées. |
| État | État du message AS2 <br>Success = a reçu ou envoyé un message AS2 correct, aucun MDN n’est configuré <br>Success = a reçu ou envoyé un message AS2 correct, le MDN est configuré et reçu ou un MDN est envoyé <br>Failed = a reçu un message AS2 incorrect, aucun MDN n’est configuré <br>Pending = a reçu ou envoyé un message AS2 correct, le MDN est configuré et un accusé de réception fonctionnel est attendu |
| Ack | État du message de notification d’état du message <br>Accepted = a reçu ou envoyé un MDN positif <br>Pending = en attente de réception ou d’envoi d’un MDN <br>Rejected = a reçu ou envoyé un MDN négatif <br>Not Required = le MDN n’est pas configuré dans l’accord |
| Direction | Direction du message AS2. |
| ID de corrélation : | ID pour mettre en corrélation l’ensemble des déclencheurs et des actions au sein d’une application logique. |
| ID de message |  ID du message AS2 dans les en-têtes du message AS2. |
| Timestamp | Heure à laquelle l’action AS2 traite le message. |

#### <a name="x12-message-list-property-descriptions"></a>Description des propriétés de la liste des messages X12

| Propriété | Description |
| --- | --- |
| Sender | Partenaire invité configuré dans les paramètres de réception ou partenaire hôte configuré dans les paramètres d’envoi pour un contrat AS2. |
| Receiver | Partenaire hôte configuré dans les paramètres de réception ou partenaire invité configuré dans les paramètres d’envoi pour un contrat AS2. |
| Application logique | Application logique dans laquelle les actions AS2 sont configurées. |
| État | État du message X12 <br>Success = a reçu ou envoyé un message X12 correct, aucun accusé de réception fonctionnel n’est configuré <br>Success = a reçu ou envoyé un message X12 correct, un accusé de réception fonctionnel est configuré et reçu ou un accusé de réception fonctionnel est envoyé <br>Failed = a reçu ou envoyé un message X12 incorrect <br>Pending = a reçu ou envoyé un message X12 correct, un accusé de réception fonctionnel est configuré et un accusé de réception fonctionnel est attendu. |
| Ack | État de l’accusé de réception fonctionnel (997) <br>Accepted = a reçu ou envoyé un accusé de réception positif <br>Rejected = a reçu ou envoyé un accusé de réception négatif <br>Pending = attendait un accusé de réception fonctionnel mais ne l’a pas reçu <br>Pending = a généré un accusé de réception fonctionnel mais ne l’a pas envoyé au partenaire <br>Not Required = l’accusé de réception fonctionnel n’est pas configuré |
| Direction | Direction du message X12. |
| ID de corrélation : | ID pour mettre en corrélation l’ensemble des déclencheurs et des actions au sein d’une application logique. |
| Type de message |  Type de message X12 EDI. |
| ICN | Numéro de contrôle de l’échange du message X12. |
| TSCN | Numéro de contrôle de document automatisé du message X12. |
| Timestamp | Heure à laquelle l’action X12 traite le message. |

## <a name="queries-in-the-operations-management-suite-portal"></a>Requêtes dans le portail Operations Management Suite

Sur la page de recherche, vous pouvez créer une requête. Lorsque vous effectuez une recherche, vous pouvez filtrer les résultats à l’aide des contrôles de facette.

### <a name="create-a-query"></a>Créer une requête

1. Dans la recherche de journaux, écrivez une requête et sélectionnez **Enregistrer**. La fenêtre **Enregistrer la recherche** s’affiche. Pour écrire une requête, consultez [Suivi des messages B2B dans le portail Operations Management Suite à l’aide d’une requête](logic-apps-track-b2b-messages-omsportal-query-filter-control-number.md).
![Sélectionner la page d’accueil](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery.png)

2. Dans **Enregistrer la recherche**, ajoutez un **nom** et une **catégorie**, puis sélectionnez **Enregistrer**.   
![Sélectionner la page d’accueil](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery1.png)

3. Pour afficher la requête, sélectionnez **Favoris**.    
![Sélectionner la page d’accueil](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery3.png)

    ![Sélectionner la page d’accueil](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery4.png)

### <a name="use-a-saved-query"></a>Utiliser une requête enregistrée

* Dans la recherche de journaux, sélectionnez **favoris** pour afficher les requêtes enregistrées.  Pour afficher les résultats de la requête, sélectionnez une requête.
![Sélectionner la page d’accueil](media/logic-apps-track-b2b-messages-omsportal/logsearchaddquery5.png)


## <a name="next-steps"></a>Étapes suivantes
[Schéma de suivi personnalisé](logic-apps-track-integration-account-custom-tracking-schema.md "Learn about Custom Tracking Schema")   
[Schéma de suivi AS2](logic-apps-track-integration-account-as2-tracking-schemas.md "Learn about AS2 Tracking Schema")    
[Schéma de suivi X12](logic-apps-track-integration-account-x12-tracking-schema.md "Learn about X12 Tracking Schema")  
[En savoir plus sur Enterprise Integration Pack](../logic-apps/logic-apps-enterprise-integration-overview.md "Learn about Enterprise Integration Pack")

