---
title: "Activer la réplication des machines virtuelles Azure vers une autre région Azure avec Azure Site Recovery | Microsoft Docs"
description: "Résume les étapes requises pour activer la réplication des machines virtuelles Azure vers une autre région Azure à l’aide du service Azure Site Recovery"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a309644f-d36b-4188-bba7-ad45a2d9bede
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 8/01/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: c30998a77071242d985737e55a7dc2c0bf70b947
ms.openlocfilehash: f426ba4341e61d3c7da820d7d5097b217e94ca0e
ms.contentlocale: fr-fr
ms.lasthandoff: 08/02/2017

---


# <a name="step-5-enable-replication-for-azure-vms"></a>Étape 5 : Activer la réplication des machines virtuelles Azure


Après avoir configuré un [coffre Recovery Services](azure-to-azure-walkthrough-vault.md), utilisez cet article pour activer la réplication des machines virtuelles vers une autre région Azure avec [Azure Site Recovery](site-recovery-overview.md). Pour activer la réplication, vous devez configurer les paramètres sources et cibles, vérifier la stratégie de réplication, puis sélectionner les machines virtuelles que vous souhaitez répliquer.

- Lorsque vous aurez terminé les étapes de cet l’article, vos machines virtuelles Azure devraient être répliquées vers la région Azure secondaire.
- Vous pouvez publier des commentaires au bas de cet article ou poser des questions sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

>[!NOTE]
>
> La réplication de machines virtuelles Azure est actuellement disponible en préversion.


## <a name="select-the-source"></a>Sélectionner la source

1. Dans Coffres Recovery Services, cliquez sur le nom du coffre > **+Répliquer**.
2. Dans **Source**, sélectionnez **Azure - PREVIEW**.
2. Dans **Emplacement source**, sélectionnez la région Azure source où vos machines virtuelles s’exécutent actuellement.
3. Sélectionnez le **Modèle de déploiement de machines virtuelles Azure** de vos machines : **Resource Manager** ou **Classique**.
4. Sélectionnez le **Groupe de ressources source** pour les machines virtuelles Resource Manager, ou **service cloud** pour les machines virtuelles classiques.
5. Cliquez sur **OK** pour enregistrer les paramètres.

    ![Configurer la source](./media/azure-to-azure-walkthrough-enable-replication/source.png)

## <a name="select-the-vms"></a>Sélectionner les machines virtuelles

Site Recovery récupère une liste des machines virtuelles associées à l’abonnement et au groupe de ressources/service cloud.

1. Dans **Machines virtuelles**, sélectionnez les machines virtuelles que vous souhaitez répliquer.
2. Cliquez sur **OK**.

    ![Sélectionner les machines virtuelles](./media/azure-to-azure-walkthrough-enable-replication/vms.png)


## <a name="configure-settings"></a>Configurer les paramètres

Site Recovery approvisionne les paramètres par défaut de la région cible (en fonction des paramètres de la région source) et la stratégie de réplication :

   - **Emplacement cible** : la région cible que vous souhaitez utiliser pour la récupération d’urgence. Il est recommandé que l’emplacement cible corresponde à l’emplacement du coffre Site Recovery.
   - **Groupe de ressources cible** : groupe de ressources auquel les machines virtuelles de la région cible appartiendront après le basculement. Par défaut, Site Recovery crée un groupe de ressources dans la région cible avec un suffixe « asr ».
   - **Réseau virtuel cible** : le réseau dans lequel les machines virtuelles Azure de la région cible se trouveront après le basculement. Par défaut, Site Recovery crée un réseau virtuel (et des sous-réseaux) dans la région cible avec un suffixe « asr ». Ce réseau est mappé à votre réseau source. Notez que vous pouvez affecter une adresse IP spécifique après le basculement d’une machine virtuelle, si vous avez besoin de conserver la même adresse IP dans les emplacements source et cible.
   - **Comptes de stockage de cache** : Site Recovery utilise un compte de stockage dans la région source. Les modifications apportées aux machines virtuelles sources sont envoyées sur ce compte avant la réplication vers l’emplacement cible.
   - **Comptes de stockage cibles** : par défaut, Site Recovery crée un compte de stockage dans la région cible qui reflète le compte de stockage de la machine virtuelle source.
   -  **Groupes à haute disponibilité cibles** : par défaut, Site Recovery crée un groupe à haute disponibilité dans la région cible avec le suffixe « asr ».
   - **Nom de la stratégie de réplication** : nom de la stratégie.
   - **Rétention des points de récupération** : par défaut, Site Recovery conserve les points de récupération pendant 24 heures. Vous pouvez configurer une valeur comprise entre 1 et 72 heures.
   - **Fréquence des instantanés de cohérence des applications** : par défaut, Site Recovery prend un instantané de cohérence des applications toutes les 4 heures. Vous pouvez configurer une valeur comprise entre 1 et 12 heures. Les données sont répliquées en continu :
    - Des points de récupération cohérents d’incident conservent un ordre d’écriture cohérent des données lors de leur création. Ce type de point de récupération est généralement suffisant si votre application est conçue pour récupérer d’un incident sans incohérence de données
    - Des points de récupération cohérents d’incident sont générés toutes les quelques minutes. L’utilisation de ces points de récupération pour le basculement et la récupération de vos machines virtuelles permet d’atteindre un objectif de point de récupération (RPO) de l’ordre de quelques minutes.
    - Les points de récupération de cohérence des applications (en plus de la cohérence de l’ordre d’écriture) garantissent que les applications en cours d’exécution terminent toutes les opérations et vident les mémoires tampons sur le disque (suspension de l’application). Nous vous recommandons d’utiliser ces points de récupération pour les applications de base de données comme SQL Server, Oracle et Exchange.

    ![Configurer les paramètres](./media/azure-to-azure-walkthrough-enable-replication/settings.png)


### <a name="modify-settings"></a>Modifier les paramètres

Si vous souhaitez modifier les paramètres de la cible ou de la stratégie de réplication, procédez comme suit :

1. Pour afficher ou modifier les paramètres de la cible, cliquez sur **Paramètres**.
2. Pour remplacer les paramètres de la cible par défaut, cliquez sur **Personnaliser**. Vous pouvez spécifier un groupe de ressources cible, un réseau virtuel, un groupe à haute disponibilité et un compte de stockage cible. Vous pouvez uniquement ajouter des groupes à haute disponibilité si les machines virtuelles font partie d’un ensemble de la région source.

    ![Configurer les paramètres](./media/azure-to-azure-walkthrough-enable-replication/customize-target.png)

3. Pour remplacer les paramètres de réplication pour les points de récupération et les instantanés de cohérence des applications, cliquez sur **Personnaliser** en regard de **Stratégie de réplication**.

    ![Configurer les paramètres](./media/azure-to-azure-walkthrough-enable-replication/customize-policy.png)

4. Pour commencer l’approvisionnement des ressources de cibles, cliquez sur **Créer des ressources cibles**. L’approvisionnement prend environ une minute. Ne fermez pas le panneau pendant l’approvisionnement, sinon vous devrez recommencer.




## <a name="enable-replication"></a>Activer la réplication

1. Dans **Paramètres**, cliquez sur **Activer la réplication**. Cela active la réplication initiale des machines virtuelles sélectionnées. L’état de réplication initiale peut prendre un certain temps à s’actualiser. Cliquez sur **Actualiser** pour obtenir l’état le plus récent.

2. Vous pouvez suivre la progression du travail **Activer la protection** dans **Paramètres** > **Travaux** > **Travaux Site Recovery**.

3. Dans **Paramètres** > **Éléments répliqués**, vous pouvez afficher l’état des machines virtuelles et la progression de la réplication initiale. Cliquez sur la machine virtuelle pour explorer ses paramètres.



## <a name="next-steps"></a>Étapes suivantes

Aller à [Étape 6 : exécuter un test de basculement](azure-to-azure-walkthrough-test-failover.md)

