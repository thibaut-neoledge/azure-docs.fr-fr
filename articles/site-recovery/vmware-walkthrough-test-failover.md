---
title: "Exécuter un test de basculement pour la réplication VMware dans Azure | Microsoft Docs"
description: "Résume les étapes requises pour exécuter un test de basculement pour la réplication des machines virtuelles VMware vers Azure à l’aide du service Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: carmonm
editor: 
ms.assetid: a640e139-3a09-4ad8-8283-8fa92544f4c6
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: raynew
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: f1a6df56a2bb0094d972d2e659057cc124156b88
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---
# <a name="step-12-run-a-test-failover-to-azure-for-vmware-vms"></a>Étape 12 : Exécuter un test de basculement vers Azure pour les machines virtuelles VMware

Cet article explique comment exécuter un test de basculement à partir des machines virtuelles VMware locales sur Azure à l’aide du service [Azure Site Recovery](site-recovery-overview.md) dans le portail Azure.

Publiez des commentaires et des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="before-you-start"></a>Avant de commencer

Avant d’exécuter un test de basculement, nous vous recommandons de vérifier les propriétés de la machine virtuelle et d’apporter toutes les modifications requises. Vous pouvez accéder aux propriétés des machines virtuelles dans les **éléments répliqués**. Le panneau **Bases** affiche des informations sur les paramètres et l’état des machines.

## <a name="managed-disk-considerations"></a>Remarques relatives au disque géré

Les [disques gérés](../virtual-machines/windows/managed-disks-overview.md) simplifient la gestion des disques des machines virtuelles Azure, en gérant les comptes de stockage associés aux disques de machines virtuelles. 

- Lorsque vous activez la protection pour une machine virtuelle, les données des machines virtuelles sont répliquées vers un compte de stockage. Les disques gérés sont créés et attachés à la machine virtuelle uniquement en cas de basculement.
- Des disques gérés ne peuvent être créés que pour des machines virtuelles déployées à l’aide du modèle du gestionnaire de ressources.  
- Lorsque ce paramètre est activé, seuls les groupes à haute disponibilité des groupes de ressources dont le paramètre **Utiliser des disques gérés par** est activé peuvent être sélectionnés. Les machines virtuelles dotées de disques gérés doivent se trouver dans des groupes à haute disponibilité dont le paramètre **Utiliser des disques gérés** est défini sur **Oui**. Lorsque ce paramètre n’est pas activé pour les machines virtuelles, seuls les groupes à haute disponibilité des groupes de ressources dont le paramètre relatif aux disques gérés n’est pas activé peuvent être sélectionnés.
- [En savoir plus](https://docs.microsoft.com/azure/virtual-machines/windows/manage-availability#use-managed-disks-for-vms-in-an-availability-set) sur les disques gérés et les groupes à haute disponibilité.
- Si le compte de stockage que vous utilisez pour la réplication a été chiffré à l’aide du chiffrement du service de stockage, la création de disques gérés lors du basculement est impossible. Dans ce cas, n’activez pas l’utilisation des disques gérés, ni ne désactivez la protection de la machine virtuelle, et réactivez-la pour utiliser un compte de stockage pour lequel le chiffrement n’est pas activé. [En savoir plus](https://docs.microsoft.com/azure/storage/storage-managed-disks-overview#managed-disks-and-encryption).


## <a name="network-considerations"></a>Remarques relatives au réseau

Vous pouvez définir l’adresse IP cible d’une machine virtuelle Azure créée après le basculement.

- Si vous ne fournissez pas d’adresse IP, la machine ayant basculé utilisera le service DHCP.
- Si vous définissez une adresse qui n’est pas disponible au moment du basculement, ce dernier échoue.
- Vous pouvez utiliser la même adresse IP cible pour le test de basculement si cette adresse est disponible sur le réseau de test de basculement.
- Le nombre de cartes réseau est déterminé par la taille spécifiée pour la machine virtuelle cible :

     - Si le nombre de cartes réseau sur la machine source est inférieur ou égal au nombre de cartes autorisé pour la taille de la machine cible, la cible présente le même nombre de cartes que la source.
     - Si le nombre de cartes de la machine virtuelle source dépasse la valeur de taille cible autorisée, la taille cible maximale est utilisée.
     - Par exemple, si une machine source présente deux cartes réseau et que la taille de la machine cible en accepte quatre, la machine cible présentera deux cartes. Si la machine source inclut deux cartes, mais que la taille cible prise en charge accepte une seule carte, la machine cible présentera une seule carte.     
   - Si la machine virtuelle possède plusieurs cartes réseau, elles se connectent toutes au même réseau.
   - Si la machine virtuelle possède plusieurs cartes réseau, la première qui s’affiche dans la liste devient la carte réseau *par défaut* dans la machine virtuelle Azure.
 - [En savoir plus](vmware-walkthrough-network.md) sur l’adressage IP.



## <a name="view-and-modify-vm-settings"></a>Afficher et modifier des paramètres de machine virtuelle

Nous vous recommandons de vérifier les propriétés de la machine source avant d’effectuer un basculement.

1. Dans **Éléments protégés**, cliquez sur **Éléments répliqués** et sélectionnez la machine virtuelle.
2. Dans le panneau **Élément répliqué**, vous pouvez voir un résumé des informations de machine virtuelle, l’état d’intégrité et les derniers points de récupération disponibles. Cliquez sur **Propriétés** pour obtenir plus de détails.
3. Dans **Calcul et réseau**, vous pouvez :
    - Modifier le nom de la machine virtuelle Azure. Le nom doit répondre aux [exigences Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).
    - Spécifier un [groupe de ressources] post-basculement.
    - Spécifier une taille cible pour la machine virtuelle Azure.
    - Sélectionner un [groupe à haute disponibilité](../virtual-machines/windows/tutorial-availability-sets.md).
    - Indiquer s’il faut utiliser [des disques gérés](#managed-disk-considerations). Sélectionnez **Oui**, si vous souhaitez attacher des disques gérés à votre machine lors de la migration vers Azure.
    - Affichez ou modifiez les paramètres réseau, y compris le réseau/sous-réseau dans lequel la machine virtuelle Azure se trouve après le basculement et l’adresse IP qui lui sera affectée.
4. Des informations sur les disques de données et du système d’exploitation de la machine virtuelle s’affichent dans **Disques**.

## <a name="run-a-test-failover"></a>Exécution d’un test de basculement

Après avoir terminé la configuration, exécutez un test de basculement afin de vérifier que tout fonctionne bien.

- Si vous souhaitez vous connecter à des machines virtuelles Azure à l’aide du protocole RDP après le basculement, [préparez la connexion](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover).
 - Pour effectuer un test complet, vous avez besoin d’une copie d’Active Directory et du DNS dans votre environnement de test. [En savoir plus](site-recovery-active-directory.md#test-failover-considerations).
 - Pour obtenir des informations complètes sur le test de basculement, consultez [cet article](site-recovery-test-failover-to-azure.md).
- Visionnez une courte vidéo de présentation avant de commencer :


>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video4-Recovery-Plan-DR-Drill-and-Failover/player]


Maintenant, exécutez un basculement :

1. Pour effectuer le basculement d’une seule machine, dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine virtuelle, puis sur l’icône **+Test de basculement**.

    ![Test de basculement](./media/vmware-walkthrough-test-failover/test-failover.png)

2. Pour effectuer le basculement d’un plan de récupération, dans **Paramètres** > **Plans de récupération**, cliquez avec le bouton droit sur le plan et sélectionnez **Test de basculement**. Pour créer un plan de récupération, suivez [ces instructions](site-recovery-create-recovery-plans.md).  

3. Dans **Test de basculement**, sélectionnez le réseau Azure auquel les machines virtuelles Azure seront connectées après le basculement.

4. Cliquez sur **OK** pour commencer le basculement. Vous pouvez suivre la progression du basculement en cliquant sur la machine virtuelle pour ouvrir ses propriétés, ou en sélectionnant le travail **Test de basculement** dans le nom de l’archivage > **Paramètres** > **Travaux** > **Travaux Site Recovery**.

5. Une fois le basculement terminé, vous devez également voir la machine Azure de réplication apparaître dans le Portail Azure > **Machines virtuelles**. Vous devrez peut-être vous assurer que la machine virtuelle présente la taille appropriée, qu’elle est bien connectée au réseau approprié et qu’elle s’exécute.

6. Si vous avez préparé les connexions après le basculement, vous devez être à même de vous connecter à la machine virtuelle Azure.

7. Une fois que vous avez terminé, cliquez sur **Nettoyer le test de basculement** sur le plan de récupération. Cliquez sur **Notes** pour consigner et enregistrer d’éventuelles observations sur le test de basculement. Cette opération supprimera les machines virtuelles qui ont été créées au cours du test de basculement.



## <a name="next-steps"></a>Étapes suivantes

- [En savoir plus](site-recovery-failover.md) sur les différents types de basculement et leur exécution.
- Si vous migrez des ordinateurs au lieu de procéder à une réplication et une restauration automatique, [lisez cet article pour en savoir plus](site-recovery-migrate-to-azure.md#migrate-on-premises-vms-and-physical-servers).
- [En savoir plus sur la restauration automatique](site-recovery-failback-azure-to-vmware.md) pour restaurer et répliquer des machines virtuelles Azure vers le site local principal.

