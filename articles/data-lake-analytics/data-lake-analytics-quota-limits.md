---
title: Limites de quota pour Azure Data Lake Analytics | Microsoft Docs
description: "Découvrez comment ajuster et augmenter les limites de quota dans un compte Azure Data Lake Analytics (ADLA)."
services: data-lake-analytics
keywords: Service Analytique Azure Data Lake
documentationcenter: 
author: omidm1
editor: omidm1
ms.assetid: 49416f38-fcc7-476f-a55e-d67f3f9c1d34
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 06/18/2017
ms.author: omidm
ms.openlocfilehash: 957f306ea0e80b5830ad64e5ef06c6d122d9eccc
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2017
---
# <a name="azure-data-lake-analytics-quota-limits"></a>Limites de quota pour Azure Data Lake Analytics

Découvrez comment ajuster et augmenter les limites de quota dans un compte Azure Data Lake Analytics (ADLA). Il vous sera plus simple d’appréhender le comportement de vos tâches U-SQL si vous connaissez ces limites. Toutes ces limites sont souples. Pour les augmenter, il vous suffit de nous contacter.

## <a name="azure-subscriptions-limits"></a>Limites des abonnements Azure

**Nombre maximal de comptes ADLA par abonnement :** 5.

 Il s’agit du nombre maximal de comptes ADLA que vous pouvez créer par abonnement. Lorsque vous essayez de créer le sixième compte ADLA, le message d’erreur suivant s’affiche : « Vous avez atteint le nombre maximal de comptes Data Lake Analytics autorisés (5) dans (zone) sous l’abonnement (nom). » Dans ce cas, supprimez les comptes ADLA non utilisés ou contactez-nous en [ouvrant un ticket de support](#increase-maximum-quota-limits).

## <a name="adla-account-limits"></a>Limites des comptes ADLA

**Nombre maximal d’unités Analytics par compte :**  250

Il s’agit du nombre maximal d’unités Analytics pouvant s’exécuter simultanément dans votre compte. Si le nombre total d’unités Analytics exécutées dans l’ensemble des tâches dépasse cette limite, les tâches les plus récentes sont automatiquement placées dans la file d’attente. Par exemple :

* Si une seule tâche est exécutée avec 250 unités Analytics, lorsque vous envoyez une deuxième tâche, celle-ci est placée dans la file d’attente jusqu’à ce que la première tâche soit terminée.
* Si vous avez déjà cinq tâches en cours d’exécution et que chacune utilise 50 unités Analytics, lorsque vous envoyez une sixième tâche nécessitant 20 unités Analytics, celle-ci est placée dans la file d’attente jusqu’à ce que les 20 unités Analytics soient disponibles.

**Nombre maximal de tâches U-SQL simultanées par compte :** 20

Il s’agit du nombre maximal de tâches pouvant s’exécuter simultanément dans votre compte. Au-dessus de cette valeur, les tâches les plus récentes sont automatiquement placées dans la file d’attente.

## <a name="adjust-adla-quota-limits-per-account"></a>Ajuster les limites de quota ADLA par compte

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Choisissez un compte ADLA existant.
3. Cliquez sur **Propriétés**.
4. Ajustez les valeurs **Parallélisme** et **Tâches simultanées** selon vos besoins.

    ![Volet du portail Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

## <a name="increase-maximum-quota-limits"></a>Augmenter les limites de quota maximales

1. Ouvrez une demande de support dans le Portail Azure.

    ![Volet du portail Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Volet du portail Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Sélectionnez **Quota** comme type de problème.
3. Sélectionnez votre **Abonnement** (vérifiez qu’il ne s’agit pas d’une version d’essai).
4. Sélectionnez le type de quota **Data Lake Analytics**.

    ![Volet du portail Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5. Dans le panneau Problème, expliquez le motif de votre demande d’augmentation de limite. Indiquez pourquoi vous avez besoin de cette capacité supplémentaire dans la zone **Détails**.

    ![Volet du portail Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6. Vérifiez vos informations de contact et créez la demande de support.

Microsoft examine votre demande et essaie de répondre aux besoins de votre activité dans les plus brefs délais.

## <a name="next-steps"></a>Étapes suivantes

* [Vue d'ensemble de Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Gestion d'Azure Data Lake Analytics à l'aide d'Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Surveiller et résoudre les problèmes des tâches Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)
