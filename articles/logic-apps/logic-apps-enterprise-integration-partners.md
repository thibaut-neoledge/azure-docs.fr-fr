---
title: "Création de partenaires pour les messages d’entreprise à entreprise (B2B) - Azure Logic Apps | Microsoft Docs"
description: "Découvrez comment ajouter des partenaires à votre compte d’intégration avec Enterprise Integration Pack et Logic Apps"
services: logic-apps
documentationcenter: .net,nodejs,java
author: MandiOhlinger
manager: anneta
editor: 
ms.assetid: b179325c-a511-4c1b-9796-f7484b4f6873
ms.service: logic-apps
ms.workload: integration
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2016
ms.author: estfan
ms.custom: H1Hack27Feb2017
translationtype: Human Translation
ms.sourcegitcommit: 5913c81088724ef946ae147f4f3154fa6aefd22e
ms.openlocfilehash: 2a886d1eaf1253b55d8c95786139a2bbdc6f145e
ms.lasthandoff: 03/01/2017


---
# <a name="add-or-update-partners-in-business-to-business-agreements-in-your-workflow"></a>Ajout ou mise à jour des partenaires dans les contrats d’entreprise à entreprise de votre flux de travail

Les partenaires sont des entités qui participent aux transactions d’entreprise à entreprise (B2B) et qui échangent des messages entre eux. Avant de pouvoir créer des partenaires qui vous représentent et qui représentent une autre organisation dans le cadre de ces transactions, vous devez partager des informations qui identifient et valident les messages envoyés par chacun. Après avoir discuté de ces détails et quand vous serez prêt à initier cette relation commerciale, vous pouvez créer des partenaires dans votre compte d’intégration pour vous représenter, ainsi que l’organisation.

## <a name="what-roles-do-partners-have-in-your-integration-account"></a>Quels rôles jouent les partenaires dans votre compte d’intégration ?

Pour définir les détails relatifs aux messages échangés entre les partenaires, vous pouvez créer des contrats entre ces partenaires. Toutefois, avant de pouvoir créer un contrat, vous devez avoir ajouté au moins deux partenaires à votre compte d’intégration. Votre organisation doit faire partie du contrat, en tant que **partenaire hôte**. L’autre partenaire, ou **partenaire invité**, représente l’organisation qui échange des messages avec votre organisation. Le partenaire invité peut être une autre entreprise ou même un service au sein de votre organisation.

Après avoir ajouté ces partenaires, vous pouvez créer un contrat.

Les paramètres de réception et d’envoi sont basés du point de vue du partenaire hébergé. Par exemple, les paramètres de réception dans un contrat déterminent la façon dont le partenaire hébergé reçoit les messages envoyés à partir d’un partenaire invité. De même, les paramètres d’envoi du contrat indiquent la façon dont le partenaire hébergé envoie des messages au partenaire invité.

## <a name="how-to-create-a-partner"></a>Création d’un partenaire

1. Dans le portail Azure, sélectionnez **Parcourir**.

    ![](./media/logic-apps-enterprise-integration-overview/overview-1.png)

2. Entrez **intégration** dans la zone de recherche de filtre et sélectionnez **Comptes d’intégration** dans la liste des résultats.

    ![](./media/logic-apps-enterprise-integration-overview/overview-2.png)

3. Sélectionnez le compte d’intégration dans lequel vous souhaitez ajouter vos partenaires.

    ![](./media/logic-apps-enterprise-integration-overview/overview-3.png)

4. Sélectionnez la mosaïque **Partenaires**.

    ![](./media/logic-apps-enterprise-integration-partners/partner-1.png)

5. Dans le panneau Partenaires, choisissez **Ajouter**.

    ![](./media/logic-apps-enterprise-integration-partners/partner-2.png)

6. Entrez un nom pour votre partenaire, puis sélectionnez un **qualificateur**. Enfin, entrez une **valeur** pour identifier les documents qui entrent dans vos applications.

    ![](./media/logic-apps-enterprise-integration-partners/partner-3.png)

7. Sélectionnez l’icône de notification en forme de *cloche* pour afficher la progression du processus de création du partenaire.

    ![](./media/logic-apps-enterprise-integration-partners/partner-4.png)

8. Pour vérifier que vos nouveaux partenaires ont été correctement ajoutés, sélectionnez la mosaïque **Partenaires**.

    ![](./media/logic-apps-enterprise-integration-partners/partner-5.png)

    Une fois la mosaïque Partenaires sélectionnée, le nouveau partenaire doit également se trouver dans le panneau Partenaires.

    ![](./media/logic-apps-enterprise-integration-partners/partner-6.png)

## <a name="how-to-edit-existing-partners-in-your-integration-account"></a>Comment modifier des partenaires existants dans votre compte d’intégration

1. Sélectionnez la mosaïque **Partenaires**.
2. Une fois le panneau Partenaires ouvert, sélectionnez le partenaire à modifier.
3. Sur la mosaïque **Mettre à jour le partenaire**, apportez les modifications nécessaires.
4. Une fois que vous avez terminé, sélectionnez **Enregistrer**. Si vous souhaitez annuler vos modifications, cliquez sur **Abandonner**.

    ![](./media/logic-apps-enterprise-integration-partners/edit-1.png)

## <a name="how-to-delete-a-partner"></a>Suppression d’un partenaire

1. Sélectionnez la mosaïque **Partenaires**.
2. Une fois le panneau Partenaires ouvert, sélectionnez le partenaire à supprimer.
3. Choisissez **Supprimer**.

    ![](./media/logic-apps-enterprise-integration-partners/delete-1.png)

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur les contrats](../logic-apps/logic-apps-enterprise-integration-agreements.md "Découvrez les contrats d’intégration d’entreprise")  


