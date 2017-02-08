---
title: "Création de solutions B2B avec Enterprise Integration Pack | Microsoft Docs"
description: "En savoir plus sur la réception de données à l’aide des fonctionnalités B2B d&quot;Enterprise Integration Pack"
services: logic-apps
documentationcenter: .net,nodejs,java
author: msftman
manager: anneta
editor: cgronlun
ms.assetid: 20fc3722-6f8b-402f-b391-b84e9df6fcff
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: deonhe
translationtype: Human Translation
ms.sourcegitcommit: dc8c9eac941f133bcb3a9807334075bfba15de46
ms.openlocfilehash: cb8cac0c63930139760617d721faffeef70cfbec


---
# <a name="learn-about-receiving-data-using-the-b2b-features-of-the-enterprise-integration-pack"></a>En savoir plus sur la réception de données à l’aide des fonctionnalités B2B d'Enterprise Integration Pack
## <a name="overview"></a>Vue d'ensemble
Ce document fait partie du Logic Apps Enterprise Integration Pack. Consultez la vue d’ensemble pour en savoir plus sur les [fonctionnalités d’Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Composants requis
Pour utiliser les actions AS2 et X12, vous aurez besoin d'un compte Enterprise Integration

[Comment créer un compte Enterprise Integration](../logic-apps/logic-apps-enterprise-integration-accounts.md)

## <a name="how-to-use-the-logic-apps-b2b-connectors"></a>Comment utiliser les connecteurs B2B Logic Apps
Une fois que vous avez créé un compte d’intégration et que vous y avez ajouté des partenaires et des contrats, vous êtes prêt à créer une application logique qui implémente un flux de travail Business to Business (B2B).

Dans cette procédure, vous verrez comment utiliser les actions AS2 et X12 pour créer une application logique B2B qui reçoit les données d’un partenaire commercial.

1. Créez une nouvelle application logique et [liez-la à votre compte d’intégration](../logic-apps/logic-apps-enterprise-integration-accounts.md).  
2. Ajoutez un déclencheur **Requête - Lors de la réception d’une demande HTTP** à votre application logique.  
   ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)  
3. Ajouter l’action **Decode AS2** en sélectionnant d’abord **Ajouter une action**  
   ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)  
4. Entrez le mot **as2** dans la zone de recherche afin de filtrer toutes les actions et d’obtenir celle que vous souhaitez utiliser  
   ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)  
5. Sélectionnez l’action **AS2 - Decode AS2 message**  
   ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)  
6. Comme indiqué, ajoutez l'élément **Body** (Corps) comme entrée. Dans cet exemple, sélectionnez le corps de la demande HTTP qui a déclenché l’application logique. Vous pouvez également entrer une expression comme entrée dans les en-têtes du champ**HEADERS** :
   
    @triggerOutputs()['headers']
7. Ajoutez les **en-têtes** requis pour AS2. Ils apparaitront dans les en-têtes de requête HTTP. Dans cet exemple, sélectionnez les en-têtes de la demande HTTP qui a déclenché l’application logique.
8. Ajoutez maintenant à nouveau l’action Decode X12 message en sélectionnant **Ajouter une action**  
   ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)   
9. Entrez le mot **x12** dans la zone de recherche afin de filtrer toutes les actions et d’obtenir celle que vous souhaitez utiliser  
   ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)  
10. Sélectionnez l’action **X12 - Decode X12 message** pour l’ajouter à l’application logique  
    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)  
11. Vous devez maintenant spécifier l’entrée de cette action qui correspondra à la sortie de l’action AS2 ci-dessus. Le contenu réel du message figure dans un objet JSON, au format base64 encodé. Vous devez donc spécifier une expression comme entrée : entrez l’expression suivante dans le champ d'entrée **X12 FLAT FILE MESSAGE TO DECODE**  
    
    @base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])  
12. Cette étape décodera les données X12 provenant d’un partenaire commercial et générera plusieurs éléments dans un objet JSON. Pour informer le partenaire de la réception des données, vous pouvez renvoyer une réponse contenant l'élément AS2 Message Disposition Notification (MDN) dans une action Response HTTP  
13. Ajoutez l’action **Response** en sélectionnant **Ajouter une action**   
    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)  
14. Entrez le mot **response** dans la zone de recherche afin de filtrer toutes les actions et d’obtenir celle que vous souhaitez utiliser  
    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)  
15. Sélectionnez l’action **Response** à ajouter  
    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)  
16. Définissez le champ de réponse **BODY** (corps) à l’aide de l’expression suivante pour accéder à l’élément MDN à partir de la sortie de l’action **Decode X12 message**  
    
    @base64ToString(body('Decode_AS2_message')?['OutgoingMdn']?['Content'])  

![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

1. Enregistrez votre travail   
   ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

À ce stade, vous avez terminé de configurer votre application logique B2B. Dans une application réelle, vous souhaiterez peut-être stocker les données X12 décodées dans une application métier ou un magasin de données. Vous pouvez facilement ajouter des actions supplémentaires pour effectuer cette opération, ou écrire des API personnalisées pour vous connecter à vos propres applications métier puis utiliser ces API dans votre application logique.

## <a name="features-and-use-cases"></a>Fonctionnalités et cas d’usage
* Les actions de décodage et d'encodage AS2 et X12 vous permettent de recevoir des données et d’en envoyer à des partenaires commerciaux à l'aide de protocoles standard en utilisant des applications logiques  
* Vous pouvez utiliser AS2 et X12 ensemble ou de façon isolée pour échanger des données avec des partenaires commerciaux en fonction des besoins
* Les actions B2B facilitent la création de partenaires et de contrats dans le compte d’intégration et leur utilisation dans une application logique  
* En étendant votre application logique avec d’autres actions, vous pouvez envoyer et recevoir des données vers et depuis d’autres applications et services tels que SalesForce  

## <a name="learn-more"></a>En savoir plus
[En savoir plus sur Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)  




<!--HONumber=Jan17_HO3-->


