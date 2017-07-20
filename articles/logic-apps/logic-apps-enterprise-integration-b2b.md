---
title: "Créer des solutions B2B - Azure Logic Apps | Microsoft Docs"
description: "Recevoir des données dans des applications logiques à l’aide des fonctionnalités B2B dans Enterprise Integration Pack"
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
ms.author: LADocs; padmavc
ms.translationtype: Human Translation
ms.sourcegitcommit: a30a90682948b657fb31dd14101172282988cbf0
ms.openlocfilehash: 0625787ddcbc0091e70b111f687e25929720ad15
ms.contentlocale: fr-fr
ms.lasthandoff: 05/25/2017


---
# <a name="receive-data-in-logic-apps-with-the-b2b-features-in-the-enterprise-integration-pack"></a>Recevoir des données dans des applications logiques à l’aide des fonctionnalités B2B dans Enterprise Integration Pack

Après avoir créé un compte d’intégration comportant des partenaires et des contrats, vous êtes prêt à créer un workflow interentreprises (B2B) pour votre application logique avec [Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md).

## <a name="prerequisites"></a>Composants requis

Pour utiliser les actions AS2 et X12, vous devez disposer d’un compte Enterprise Integration. Découvrez [comment créer un compte Enterprise Integration](../logic-apps/logic-apps-enterprise-integration-accounts.md).

## <a name="create-a-logic-app-with-b2b-connectors"></a>Créer une application logique avec des connecteurs B2B

Suivez ces étapes pour créer une application logique B2B qui utilise les actions AS2 et X12 pour recevoir des données d’un partenaire commercial :

1. Créez une application logique, puis [liez-la à votre compte d’intégration](../logic-apps/logic-apps-enterprise-integration-accounts.md).

2. Ajoutez un déclencheur **Requête - Lors de la réception d’une requête HTTP** à votre application logique.

    ![](./media/logic-apps-enterprise-integration-b2b/flatfile-1.png)

3. Pour ajouter l’action **Decode AS2**, sélectionnez **Ajouter une action**.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-2.png)

4. Pour filtrer toutes les actions et obtenir celle que vous souhaitez utiliser, entrez le mot **as2** dans la zone de recherche.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-5.png)

5. Sélectionnez l’action **AS2 - Decode AS2 message**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-6.png)

6. Ajoutez le **corps** que vous souhaitez utiliser comme entrée. Dans cet exemple, sélectionnez le corps de la demande HTTP qui a déclenché l’application logique. Ou entrez une expression qui saisit les en-têtes dans le champ **EN-TÊTES** :

    @triggerOutputs()['headers']

7. Ajoutez les **en-têtes** requis pour AS2, que vous pouvez trouver dans les en-têtes de requête HTTP. Dans cet exemple, sélectionnez les en-têtes de la requête HTTP qui déclenche l’application logique.

8. Ajoutez maintenant l’action du message Decode X12. Sélectionnez **Ajouter une action**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-9.png)

9. Pour filtrer toutes les actions et obtenir celle que vous souhaitez utiliser, entrez le mot **x12** dans la zone de recherche.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-10.png)

10. Sélectionnez l’action **X12 - Decode X12 message**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-as2message.png)

11. Vous devez maintenant spécifier l’entrée de cette action. Cette entrée est le résultat de l’action AS2 précédente.

    Le contenu réel du message est dans un objet JSON encodé au format Base64. Vous devez donc spécifier une expression comme entrée. 
    Entrez l’expression suivante dans le champ de saisie **X12 FLAT FILE MESSAGE TO DECODE** (MESSAGE DE FICHIER PLAT X12 À DÉCODER) :
    
    @base64ToString(body('Decode_AS2_message')?['AS2Message']?['Content'])

    Ajoutez des étapes permettant de décoder les données X12 provenant d’un partenaire commercial et de générer des éléments dans un objet JSON. 
    Pour informer le partenaire de la réception des données, vous pouvez renvoyer une réponse contenant l’élément AS2 Message Disposition Notification (MDN) dans une action Response HTTP.

12.    Pour ajouter l’action **Response**, sélectionnez **Ajouter une action**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-14.png)

13. Pour filtrer toutes les actions et obtenir celle que vous souhaitez utiliser, entrez le mot **response** dans la zone de recherche.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-15.png)

14. Sélectionner l’action **Response**.

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-16.png)

15. Pour accéder à l’élément MDN à partir de la sortie de l’action **Decode X12 message**, définissez le champ de réponse **CORPS** à l’aide de l’expression suivante :

    @base64ToString(body('Decode_AS2_message')?['OutgoingMdn']?['Content'])

    ![](./media/logic-apps-enterprise-integration-b2b/b2b-17.png)  

16. Enregistrez votre travail.

    ![](./media/logic-apps-enterprise-integration-b2b/transform-5.png)  

Vous avez maintenant terminé la configuration de votre application logique B2B. Dans une application réelle, vous souhaiterez peut-être stocker les données X12 décodées dans une application métier ou un magasin de données. Pour vous connecter à vos propres applications métier puis utiliser ces API dans votre application logique, vous pouvez ajouter des actions supplémentaires ou écrire des API personnalisées.

## <a name="features-and-use-cases"></a>Fonctionnalités et cas d’usage

* Les actions de codage et de décodage AS2 et X12 permettent d’échanger des données entre les partenaires commerciaux à l’aide de protocoles standard dans les applications logiques.
* Pour échanger des données avec des partenaires commerciaux, vous pouvez utiliser AS2 et X12 ensemble ou de façon isolée.
* Les actions B2B facilitent la création de partenaires et de contrats dans votre compte d’intégration et leur utilisation dans une application logique.
* En étendant votre application logique avec d’autres actions, vous pouvez envoyer et recevoir des données vers et depuis d’autres applications et services tels que SalesForce.

## <a name="learn-more"></a>En savoir plus
[En savoir plus sur Enterprise Integration Pack](logic-apps-enterprise-integration-overview.md)

