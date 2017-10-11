---
title: "Répliquer des machines virtuelles Azure entre des régions Azure à des fins de récupération d’urgence : Azure vers Azure | Microsoft Docs"
description: "Résume les étapes nécessaires pour répliquer des machines virtuelles Azure entre des régions Azure (Azure vers Azure) avec le service Azure Site Recovery à des fins de récupération d’urgence."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmon
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/10/2017
ms.author: raynew
ms.openlocfilehash: 9ca33057f6030fdcc233f6053fdc392d62f8f9f4
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="replicate-azure-vms-between-regions-with-azure-site-recovery"></a>Répliquer des machines virtuelles Azure entre des régions avec Azure Site Recovery

>[!NOTE]
>
> La réplication Azure Site Recovery pour les machines virtuelles Azure est actuellement en préversion.

Cet article explique comment répliquer des machines virtuelles Azure entre des régions Azure à l’aide du service [Site Recovery](site-recovery-overview.md) dans le portail Azure.

Publiez des commentaires et des questions au bas de cet article ou sur le [forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="disaster-recovery-in-azure"></a>Récupération d’urgence dans Azure

Les fonctionnalités d’infrastructure Azure intégrées permettent de mettre en place une stratégie de disponibilité solide et fiable pour les charges de travail qui s’exécutent sur des machines virtuelles Azure. Toutefois, il existe de nombreuses raisons pour lesquelles vous devez planifier vous-même la récupération d’urgence entre des régions Azure :

- Vous devez respecter les critères de conformité des applications et des charges de travail spécifiques qui nécessitent une stratégie de continuité des activités et de récupération d’urgence.
- Vous souhaitez pouvoir protéger et récupérer des machines virtuelles Azure en fonction de vos décisions professionnelles, et pas uniquement en fonction des fonctionnalités Azure intégrées.
- Vous devez tester le basculement et la récupération conformément à vos besoins professionnels et de conformité, sans aucun impact sur la production.
- Vous devez basculer vers la région de récupération en cas de sinistre, puis rebasculer vers la région source d’origine sans interruption.

Utilisez Site Recovery pour la réplication des machines virtuelles Azure vers Azure pour vous aider à effectuer toutes ces tâches.


## <a name="why-use-site-recovery"></a>Pourquoi utiliser Azure Site Recovery ?      

Site Recovery fournit un moyen simple de répliquer des machines virtuelles Azure d’une région à une autre :

- **Déploiement automatique**. Contrairement à un modèle de réplication actif-actif, aucune infrastructure coûteuse et complexe n’est nécessaire dans la région secondaire. Quand vous activez la réplication, Site Recovery crée automatiquement les ressources nécessaires dans la région cible en fonction des paramètres de la région source.
- **Contrôle des régions**. Avec Site Recovery, vous pouvez répliquer de n’importe quelle région vers n’importe quelle région d’un même continent. Comparez ceci au stockage géoredondant avec accès en lecture, qui réplique uniquement de façon asynchrone entre des [régions jumelées](https://docs.microsoft.com/azure/best-practices-availability-paired-regions) standard. Le stockage géoredondant avec accès en lecture fournit un accès en lecture seule aux données de la région cible.
- **Réplication automatisée**. Site Recovery fournit une réplication continue automatisée. Le basculement et la restauration automatique peuvent être déclenchés en un seul clic.
- **RTO et RPO**. Site Recovery tire parti de l’infrastructure réseau Azure qui connecte les régions pour maintenir les valeurs RTO (Objectif de délai de récupération) et RPO (Objectif de point de récupération) à un niveau très faible.
- **Test**. Vous pouvez exécuter des exercices de récupération d’urgence avec des tests de basculement à la demande en fonction de vos besoins, sans affecter vos charges de travail de production ou la réplication continue.
- **Plans de récupération**. Vous pouvez utiliser des plans de récupération pour orchestrer le basculement et la restauration automatique de l’application en cours d’exécution sur plusieurs machines virtuelles. La fonctionnalité de plan de récupération offre une parfaite intégration avec les runbooks d’automation Azure.


## <a name="deployment-summary"></a>Résumé du déploiement

Voici un résumé de ce que vous devez faire pour configurer la réplication des machines virtuelles entre des régions Azure :

1. Créez un coffre Recovery Services. Le coffre contient les paramètres de configuration et orchestre la réplication.
2. Activez la réplication des machines virtuelles Azure.
3. Exécutez un test de basculement pour vérifier que tout fonctionne comme prévu.

>[!IMPORTANT]
>
> Vous pouvez vérifier la [matrice de prise en charge pour la réplication de machines virtuelles Azure](./site-recovery-support-matrix-azure-to-azure.md).

>[!IMPORTANT]
>
> Pour plus d’informations sur la façon de configurer la connectivité réseau sortante nécessaire pour les machines virtuelles Azure pour la réplication Site Recovery, consultez le [document d’aide à la mise en réseau](./site-recovery-azure-to-azure-networking-guidance.md).

### <a name="before-you-start"></a>Avant de commencer

* Pour pouvoir activer la réplication d’une machine virtuelle Azure, votre compte d’utilisateur Azure doit disposer de certaines [autorisations](site-recovery-role-based-linked-access-control.md#permissions-required-to-enable-replication-for-new-virtual-machines).
* Votre abonnement Azure doit être activé pour créer des machines virtuelles à l’emplacement cible que vous souhaitez utiliser en tant que région de récupération d’urgence. Contactez le support pour activer le quota requis.

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

>[!NOTE]
>
> Nous vous recommandons de créer le coffre Recovery Services à l’emplacement où vous souhaitez répliquer vos machines virtuelles. Par exemple, si votre emplacement cible est la région États-Unis du Centre, créez le coffre dans **États-Unis du Centre**.

## <a name="enable-replication"></a>Activer la réplication

Dans **Coffres Recovery Services**, cliquez sur le nom du coffre. Dans le coffre, cliquez sur le bouton **+Répliquer**.

### <a name="step-1-configure-the-source"></a>Étape 1. Configurer la source
1. Dans **Source**, sélectionnez **Azure - PREVIEW**.
2. Dans **Emplacement source**, sélectionnez la région Azure source où vos machines virtuelles s’exécutent actuellement.
3. Sélectionnez le modèle de déploiement de vos machines virtuelles : **Resource Manager** ou **Classique**.
4. Sélectionnez le **Groupe de ressources source** pour les machines virtuelles Resource Manager, ou **service cloud** pour les machines virtuelles classiques.

    ![Configurer la source](./media/site-recovery-azure-to-azure/source.png)

### <a name="step-2-select-virtual-machines"></a>Étape 2. Sélectionner les machines virtuelles

* Sélectionnez les machines virtuelles à répliquer, puis cliquez sur **OK**.

    ![Sélectionner les machines virtuelles](./media/site-recovery-azure-to-azure/vms.png)

### <a name="step-3-configure-settings"></a>Étape 3. Configurer les paramètres

1. Pour remplacer les paramètres cibles par défaut et spécifier les paramètres de votre choix, cliquez sur **Personnaliser**. Pour plus d’informations, consultez [Personnaliser les ressources cibles](site-recovery-replicate-azure-to-azure.md##customize-target-resources).

    ![Configurer les paramètres](./media/site-recovery-azure-to-azure/settings.png)


2. Par défaut, Site Recovery crée une stratégie de réplication qui effectue des captures instantanées de cohérence des applications toutes les quatre heures et conserve les points de récupération pendant 24 heures. Pour créer une stratégie avec des paramètres différents, cliquez sur **Personnaliser** en regard de **Stratégie de réplication**.

    ![Personnaliser la stratégie](./media/site-recovery-azure-to-azure/customize-policy.png)

3. Pour commencer l’approvisionnement des ressources de cibles, cliquez sur **Créer des ressources cibles**. L’approvisionnement prend environ une minute. Ne fermez pas le panneau pendant l’approvisionnement, sinon vous devrez recommencer.

4. Pour déclencher la réplication de la machine virtuelle sélectionnée, cliquez sur **Activer la réplication**.

5. Vous pouvez suivre la progression du travail **Activer la protection** dans **Paramètres** > **Travaux** > **Travaux Site Recovery**.

6. Dans **Paramètres** > **Éléments répliqués**, vous pouvez afficher l’état des machines virtuelles et la progression de la réplication initiale. Cliquez sur la machine virtuelle pour explorer ses paramètres.

## <a name="run-a-test-failover"></a>Exécution d’un test de basculement

Après avoir terminé la configuration, exécutez un test de basculement pour vérifier que tout fonctionne comme prévu :

1. Pour effectuer le basculement d’une seule machine, dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine virtuelle, puis sur l’icône **+Tester le basculement**.

2. Pour effectuer le basculement d’un plan de récupération, dans **Paramètres** > **Plans de récupération**, cliquez avec le bouton droit sur le plan et sélectionnez **Tester le basculement**. Pour créer un plan de récupération, suivez [ces instructions](site-recovery-create-recovery-plans.md). 

3. Dans **Tester le basculement**, sélectionnez le réseau virtuel Azure cible auquel les machines virtuelles Azure sont connectées après le basculement.

4. Pour démarrer le basculement, cliquez sur **OK**. Pour suivre la progression, cliquez sur la machine virtuelle pour ouvrir ses propriétés. Vous pouvez également cliquer sur le travail **Test de basculement** dans le nom du coffre > **Paramètres** > **Travaux** > **Travaux Site Recovery**.

5. Une fois le basculement terminé, le réplica de machine Azure apparaît dans le portail Azure > **Machines virtuelles**. Assurez-vous que la machine virtuelle présente la taille appropriée, qu’elle est bien connectée au réseau approprié et qu’elle s’exécute.

6. Pour supprimer les machines virtuelles qui ont été créées pendant le test de basculement, cliquez sur **Nettoyer le test de basculement** sur l’élément répliqué ou le plan de récupération. Cliquez sur **Notes** pour consigner et enregistrer d’éventuelles observations sur le test de basculement. 

Pour plus d’informations sur les tests de basculement, cliquez [ici](site-recovery-test-failover-to-azure.md).


## <a name="next-steps"></a>Étapes suivantes

Après avoir testé le déploiement :

- [Apprenez-en davantage](site-recovery-failover.md) sur les différents types de basculement et leur mode exécution.
- En savoir plus sur l’[utilisation des plans de récupération](site-recovery-create-recovery-plans.md) afin de réduire l’objectif de délai de récupération (RTO).
