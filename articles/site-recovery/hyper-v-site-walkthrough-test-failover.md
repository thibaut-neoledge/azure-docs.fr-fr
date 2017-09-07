---
title: "Exécuter un test de basculement pour la réplication Hyper-V (sans System Center VMM) vers Azure | Microsoft Docs"
description: "Résume les étapes requises pour exécuter un test de basculement pour la réplication des machines virtuelles Hyper-V vers Azure à l’aide du service Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: c9a4c3ca-84a0-4668-b700-9b0046202299
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/22/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 0974b9eda2cb7e3ba54a4a0fad0a768db644caf9
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---

# <a name="step-11-run-a-test-failover-for-hyper-v-replication-to-azure"></a>Étape 11 : Exécuter un test de basculement pour la réplication Hyper-V vers Azure

Cet article explique comment exécuter un test de basculement de machines virtuelles Hyper-V locales (non gérées par System Center VMM) à l’aide du service [Azure Site Recovery](site-recovery-overview.md) dans le portail Azure.

Publiez des commentaires et des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="before-you-start"></a>Avant de commencer

Avant d’exécuter un test de basculement, nous vous recommandons de vérifier les propriétés de la machine virtuelle et d’apporter toutes les modifications requises. Vous pouvez accéder aux propriétés des machines virtuelles dans les **éléments répliqués**. Le panneau **Bases** affiche des informations sur les paramètres et l’état des machines.

## <a name="managed-disk-considerations"></a>Remarques relatives au disque géré

Les [disques gérés](../virtual-machines/windows/managed-disks-overview.md) simplifient la gestion des disques des machines virtuelles Azure, en gérant les comptes de stockage associés aux disques de machines virtuelles. 

- Les disques gérés sont créés et attachés à la machine virtuelle uniquement en cas de basculement vers Azure. Lorsque vous activez la protection, les données des machines virtuelles locales sont répliquées sur des comptes de stockage.
- Des disques gérés ne peuvent être créés que pour des machines virtuelles déployées à l’aide du modèle de déploiement Resource Manager.
- La restauration automatique d’Azure vers l’environnement Hyper-V local n’est actuellement pas prise en charge pour les ordinateurs avec disques gérés. Vous ne devez définir **Utiliser des disques gérés** sur **Oui** que si vous effectuez une migration (basculement vers Azure sans restauration automatique).
- Lorsque ce paramètre est activé, seuls des groupes à haute disponibilité dans les groupes de ressources pour lesquels l’option **Utiliser des disques gérés** est activée peuvent être sélectionnés. Les machines virtuelles dotées de disques gérés doivent se trouver dans des groupes à haute disponibilité dont le paramètre **Utiliser des disques gérés** est défini sur **Oui**. Lorsque ce paramètre n’est pas activé pour les machines virtuelles, seuls les groupes à haute disponibilité des groupes de ressources dont le paramètre relatif aux disques gérés n’est pas activé peuvent être sélectionnés. [En savoir plus](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set).
- - Si le compte de stockage que vous utilisez pour la réplication a été chiffré à l’aide du chiffrement du service de stockage, la création de disques gérés lors du basculement est impossible. Dans ce cas, n’activez pas l’utilisation des disques gérés, ni ne désactivez la protection de la machine virtuelle, et réactivez-la pour utiliser un compte de stockage pour lequel le chiffrement n’est pas activé. [En savoir plus](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview#managed-disks-and-encryption).

 
## <a name="network-considerations"></a>Remarques relatives au réseau
    
- Vous pouvez définir l’adresse IP cible à utiliser pour la machine virtuelle Azure après le basculement. Si vous ne fournissez pas d’adresse IP, la machine ayant basculé utilisera le service DHCP. Si vous définissez une adresse qui n’est pas disponible au moment du basculement, ce dernier échoue. Vous pouvez utiliser la même adresse IP cible pour le test de basculement si cette adresse est disponible sur le réseau de test de basculement.
- Le nombre de cartes réseau est déterminé par la taille spécifiée pour la machine virtuelle cible, comme suit :
    - Si le nombre de cartes réseau sur la machine source est inférieur ou égal au nombre de cartes autorisé pour la taille de la machine cible, la cible présente le même nombre de cartes que la source.
    - Si le nombre de cartes de la machine virtuelle source dépasse la valeur de taille cible autorisée, la taille cible maximale est utilisée.
    - Par exemple, si une machine source présente deux cartes réseau et que la taille de la machine cible en accepte quatre, la machine cible présentera deux cartes. Si la machine source inclut deux cartes, mais que la taille cible prise en charge accepte une seule carte, la machine cible présentera une seule carte.     
- Si la machine virtuelle possède plusieurs cartes réseau, elles se connectent toutes au même réseau.
- Si la machine virtuelle possède plusieurs cartes réseau, la première qui s’affiche dans la liste devient la carte réseau *par défaut* dans la machine virtuelle Azure.


## <a name="view-and-manage-vm-settings"></a>Afficher et gérer les paramètres de machine virtuelle

Nous vous recommandons de vérifier les propriétés de la machine source avant d’effectuer un basculement.

1. Dans **Éléments protégés**, cliquez sur **Éléments répliqués**, puis sélectionnez la machine virtuelle.

    ![Activer la réplication](./media/hyper-v-site-walkthrough-test-failover/test-failover1.png)
2. Dans le panneau **Élément répliqué**, vous pouvez voir un résumé des informations de machine virtuelle, l’état d’intégrité et les derniers points de récupération disponibles. Cliquez sur **Propriétés** pour obtenir plus de détails.

    ![Activer la réplication](./media/hyper-v-site-walkthrough-test-failover/test-failover2.png)
3. Dans **Calcul et réseau**, vous pouvez :
    - Modifier le nom de la machine virtuelle Azure. Le nom doit répondre aux [exigences Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
    - Spécifier un [groupe de ressources] post-basculement.
    - Spécifier une taille cible pour la machine virtuelle Azure.
    - Sélectionner un [groupe à haute disponibilité](../virtual-machines/windows/tutorial-availability-sets.md).
    - Indiquer s’il faut utiliser [des disques gérés](#managed-disk-considerations). Sélectionnez **Oui**, si vous souhaitez attacher des disques gérés à votre machine lors de la migration vers Azure.
    - Afficher ou modifier les paramètres réseau, y compris le réseau/sous-réseau dans lequel la machine virtuelle Azure se trouve après le basculement et l’adresse IP qui lui sera affectée.

    ![Activer la réplication](./media/hyper-v-site-walkthrough-test-failover/test-failover4.png)
4. Des informations sur les disques de données et du système d’exploitation de la machine virtuelle s’affichent dans **Disques** .


## <a name="run-a-test-failover"></a>Exécution d’un test de basculement

À présent, exécutez un test de basculement afin de vérifier que tout fonctionne bien.

- Si vous souhaitez vous connecter à des machines virtuelles Azure à l’aide du protocole RDP après le basculement, [préparez la connexion](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
 - Pour effectuer un test complet, vous avez besoin d’une copie d’Active Directory et du DNS dans votre environnement de test. [En savoir plus](site-recovery-active-directory.md#test-failover-considerations).
 - Pour obtenir des informations complètes sur le test de basculement, consultez [cet article](site-recovery-test-failover-to-azure.md) l’article.
 
 Maintenant, exécutez un basculement :

1. Pour effectuer le basculement d’une seule machine, cliquez sur la machine virtuelle dans **Éléments répliqués**, puis sur l’icône **+Test de basculement**.
2. Pour effectuer le basculement d’un plan de récupération, cliquez avec le bouton droit sur le plan dans **Plans de récupération** et sélectionnez **Test de basculement**. Pour créer un plan de récupération, suivez [ces instructions](site-recovery-create-recovery-plans.md).
3. Dans **Test de basculement**, sélectionnez le réseau Azure auquel les machines virtuelles Azure seront connectées après le basculement.
4. Cliquez sur **OK** pour commencer le basculement. Vous pouvez suivre la progression du basculement en cliquant sur la machine virtuelle pour ouvrir ses propriétés, ou en sélectionnant le travail **Test de basculement** dans le nom du coffre > **Travaux** > **Travaux Site Recovery**.
5. Une fois le basculement terminé, vous devez également voir la machine Azure de réplication apparaître dans le Portail Azure > **Machines virtuelles**. Vous devrez peut-être vous assurer que la machine virtuelle présente la taille appropriée, qu’elle est bien connectée au réseau approprié et qu’elle s’exécute.
6. Si vous avez préparé les connexions après le basculement, vous devez être à même de vous connecter à la machine virtuelle Azure.
7. Une fois que vous avez terminé, cliquez sur **Nettoyer le test de basculement** sur le plan de récupération. Cliquez sur **Notes** pour consigner et enregistrer d’éventuelles observations associées au test de basculement. Cette opération supprimera les machines virtuelles qui ont été créés au cours du test de basculement.



## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus](site-recovery-failover.md) sur les différents types de basculement et leur exécution.
- [En savoir plus sur la restauration automatique](site-recovery-failback-from-azure-to-hyper-v.md) pour restaurer et répliquer des machines virtuelles Azure vers le site Hyper-V local principal.


