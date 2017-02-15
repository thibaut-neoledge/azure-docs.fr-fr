---
title: Limites de quota pour Azure Data Lake Analytics | Microsoft Docs
description: "Découvrez comment ajuster et augmenter les limites de quota dans un compte Azure Data Lake Analytics (ADLA). Il vous sera plus simple d’appréhender le comportement de vos tâches U-SQL si vous connaissez ces limites. Toutes ces limites sont souples, et vous pourrez toujours augmenter les limites maximales en prenant contact avec nous."
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
ms.date: 11/16/2016
ms.author: omidm
translationtype: Human Translation
ms.sourcegitcommit: ce76fb1feaa38ff20ccc873114541a015126a1ad
ms.openlocfilehash: cffafbc20294c235060b03a75f6d1bee712115d1


---
# <a name="azure-data-lake-analytics-quota-limits"></a>Limites de quota pour Azure Data Lake Analytics
Découvrez comment ajuster et augmenter les limites de quota dans un compte Azure Data Lake Analytics (ADLA). Il vous sera plus simple d’appréhender le comportement de vos tâches U-SQL si vous connaissez ces limites. Toutes ces limites sont souples, et vous pourrez toujours augmenter les limites maximales en prenant contact avec nous.

**Configuration requise**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un compte Data Lake Analytics**. Consultez [Prise en main d’Azure Data Lake Analytics à l’aide du Portail Azure](https://azure.microsoft.com/en-us/documentation/articles/data-lake-analytics-get-started-portal/#create-adl-analytics-account).
* **Connaissances de base du processus de travail Analytique Data Lake**. Consultez [Prise en main d’Azure Data Lake Analytics à l’aide du Portail Azure](https://azure.microsoft.com/en-us/documentation/articles/data-lake-analytics-get-started-portal/).

<!-- ################################ -->
<!-- ################################ -->
## <a name="quota-limits"></a>Limites de quota :
La liste ci-dessous décrit les limites de quota actuelles du système :

**Limites par abonnement Azure :** les limites suivantes s’appliquent aux abonnements Azure :
* **Nombre maximal de comptes ADLA par abonnement :** 5. Il s’agit du nombre maximal de comptes ADLA que vous pouvez créer par abonnement. Lorsque vous essayez de créer le sixième compte ADLA, vous recevez une erreur indiquant que vous avez atteint le nombre maximal de comptes Data Lake Analytics autorisés (5) dans la zone sous ce nom d’abonnement. Vous pouvez facilement résoudre ce problème en supprimant les comptes ADLA utilisés sous votre abonnement ou en prenant contact avec nous en ouvrant un ticket de support.

**Limites par compte ADLA :**
* **Nombre maximal d’unités Analytics par compte :** 250. Il s’agit du nombre maximal d’unités Analytics pouvant s’exécuter simultanément dans votre compte. Le nombre total d’unités Analytics en cours d’exécution sur l’ensemble de vos tâches ne peut dépasser cette valeur. En cas de dépassement, les tâches les plus récentes seront placées automatiquement en file d’attente. Par exemple :
    * Une seule de vos tâches peut être exécutée avec 250 unités Analytics. Lors de la soumission de la deuxième tâche, elle est conservée dans la file d’attente des tâches jusqu’à ce que la première s’achève.
    * Si 5 tâches sont déjà en cours d’exécution et que chacune d’elles a été soumise avec 50 unités Analytics, lorsque vous soumettez la sixième avec, par exemple, 20 unités Analytics, elle est conservée dans la file d’attente des tâches et son exécution démarre lorsque 20 unités Analytics sont disponibles.
* **Nombre maximal de tâches U-SQL simultanées par compte :** 20. Il s’agit du nombre maximal de tâches pouvant s’exécuter simultanément dans votre compte. En cas de dépassement, les tâches les plus récentes sont placées automatiquement en file d’attente.

**Pour ajuster les limites de quota ADLA par compte :**
1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Choisissez le compte ADLA que vous avez déjà créé.
3. Cliquez sur **Propriétés**.
4. Ajustez les valeurs **Parallélisme** et **Tâches simultanées** selon vos besoins.

    ![Volet du portail Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-properties.png)

**Pour augmenter les limites de quota maximales :**
1. Ouvrez une demande de support dans le Portail Azure.

    ![Volet du portail Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-help-support.png)

    ![Volet du portail Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request.png)
2. Sélectionnez **Quota** comme type de problème.
3. Sous **Abonnement**, sélectionnez votre abonnement en vous assurant qu’il ne s’agit pas d’un abonnement d’évaluation.
4. Sélectionnez le type de quota **Data Lake Analytics**.

    ![Volet du portail Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-basics.png)

5.  Dans le panneau Problème, expliquez le motif de votre demande d’augmentation de limite. Indiquez pourquoi vous avez besoin de cette capacité supplémentaire dans la zone **Détails**.

    ![Volet du portail Azure Data Lake Analytics](./media/data-lake-analytics-quota-limits/data-lake-analytics-quota-support-request-details.png)

6.  Vérifiez vos informations de contact et créez la demande de support.

Nous examinerons votre demande et tenterons de répondre aux besoins de votre activité dès que possible.

## <a name="see-also"></a>Voir aussi
* [Vue d'ensemble de Microsoft Azure Data Lake Analytics](data-lake-analytics-overview.md)
* [Prise en main de Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-get-started-portal.md)
* [Gestion d'Azure Data Lake Analytics à l'aide d'Azure PowerShell](data-lake-analytics-manage-use-powershell.md)
* [Surveiller et résoudre les problèmes des tâches Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-monitor-and-troubleshoot-jobs-tutorial.md)



<!--HONumber=Nov16_HO3-->


