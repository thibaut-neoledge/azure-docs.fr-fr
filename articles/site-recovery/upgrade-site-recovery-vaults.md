---
title: "Mettre à niveau un coffre Site Recovery vers un coffre Recovery Services"
description: "Découvrez comment mettre à niveau un coffre Azure Site Recovery vers un coffre Recovery Services"
documentationcenter: 
author: rajani-janaki-ram
manager: rochakm
editor: 
ms.assetid: 
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 07/31/2017
ms.author: rajani-janaki-ram
ms.openlocfilehash: fdb33ea0d08353b491f2934fcf885fcb6910b9a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="upgrade-a-site-recovery-vault-to-an-azure-resource-manager-based-recovery-services-vault"></a>Mettre à niveau un coffre Site Recovery vers un coffre Recovery Services basé sur Azure Resource Manager

Cet article explique comment mettre à niveau des coffres Azure Site Recovery vers des coffres Recovery Services s’appuyant sur Azure Resource Manager sans impact sur la réplication en cours. Pour plus d’informations sur les fonctionnalités et les avantages d’Azure Resource Manager, consultez [Vue d’ensemble d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md).

## <a name="introduction"></a>Introduction
Un coffre Recovery Services est une ressource Azure Resource Manager permettant de gérer la sauvegarde et la récupération d’urgence en mode natif dans le cloud. Il s’agit d’un coffre unifié que vous pouvez utiliser dans le nouveau portail Azure et qui remplace les coffres Site Recovery et de sauvegarde classique.

Les coffres Recovery Services offrent tout un ensemble de fonctionnalités, notamment :

* Prise en charge d’Azure Resource Manager : vous pouvez protéger et basculer vos machines virtuelles et ordinateurs physiques dans une pile Azure Resource Manager.

* Exclusion de disque : si vous avez des fichiers temporaires ou des données avec un taux de variation élevé pour lesquels vous ne souhaitez pas utiliser toute la bande passante, vous pouvez exclure des volumes de la réplication. Cette fonctionnalité est actuellement activée dans *VMware vers Azure* et *Hyper-V vers Azure*, et elle est également étendue à d’autres scénarios.

* Prise en charge du stockage localement redondant et Premium : vous pouvez désormais protéger des serveurs dans des comptes de stockage Premium qui permettent aux clients de protéger les applications avec des opérations d’entrée/sortie par seconde (IOPS) plus élevées. Cette fonctionnalité est activée actuellement dans *VMware vers Azure*.

* Expérience de démarrage simplifiée : l’expérience avancée de démarrage a été conçue pour faciliter au maximum la configuration de la récupération d’urgence.

* Gestion de la sauvegarde et de Site Recovery à partir du même coffre : vous pouvez désormais protéger les serveurs avec la récupération d’urgence ou effectuer une sauvegarde à partir du même coffre, ce qui peut réduire considérablement la surcharge de gestion.

Pour plus d’informations sur l’expérience et les fonctionnalités mises à niveau, consultez le [blog sur le stockage, la sauvegarde et la récupération](https://azure.microsoft.com/blog/azure-site-recovery-now-available-in-a-new-experience-with-support-for-arm-and-csp/).

## <a name="salient-features"></a>Principales fonctionnalités

* Aucun impact sur la réplication en cours : les réplications en cours se poursuivent sans interruption pendant et après la mise à niveau.

* Aucun coût supplémentaire : à disposition, un ensemble complet de fonctionnalités mises à jour sans coût supplémentaire.

* Aucune perte de données : s’agissant d’un processus de mise à niveau et non d’une migration, les paramètres ainsi que les points de récupération et de réplication existants restent intactes pendant et après la mise à niveau.


## <a name="what-happens-during-the-vault-upgrade"></a>Que se passe-t-il pendant la mise à niveau ?

Lors de la mise à niveau, vous ne pouvez pas effectuer d’opérations, telles que l’inscription d’un nouveau serveur ou l’activation de la réplication pour une machine virtuelle. Toute opération qui implique la lecture ou l’écriture de données dans le coffre, comme la réplication en cours d’éléments protégés dans le coffre, se poursuit sans interruption.

### <a name="changes-to-automation-and-tooling-after-the-upgrade"></a>Changements dans les services d’automatisation et d’outils après la mise à niveau
Lorsque vous mettez à niveau le type du coffre, en passant du modèle de déploiement classique au modèle de déploiement Resource Manager, mettez à jour l’automatisation ou les outils existants pour être sûr qu’ils continuent de fonctionner après la mise à niveau.

### <a name="prepare-your-environment-for-the-upgrade"></a>Préparation de votre environnement en vue de la mise à niveau

* [Installer PowerShell ou le mettre à niveau vers la version 5 ou une version ultérieure](https://www.microsoft.com/download/details.aspx?id=50395)
* [Installer la dernière version d’Azure PowerShell MSI](https://github.com/Azure/azure-powershell/releases)
* [Télécharger le script de mise à niveau du coffre Recovery Services](https://aka.ms/vaultupgradescript)

### <a name="prerequisites"></a>Composants requis
Afin de mettre à niveau les coffres Site Recovery vers des coffres Recovery Services s’appuyant sur Azure Resource Manager, vous devez respecter les conditions suivantes :

* Version minimale de l’agent : la version du fournisseur Azure Site Recovery installée sur votre serveur doit être 5.1.1700.0 ou une version ultérieure.

* Configuration prise en charge : vous ne pouvez pas configurer votre coffre avec le réseau de zone de stockage (SAN) ou des groupes de disponibilité AlwaysOn SQL Server. Toutes les autres configurations sont prises en charge.

    >[!NOTE]
    >Après la mise à niveau, vous pouvez gérer le mappage de stockage uniquement via PowerShell.

* Scénario de déploiement pris en charge : votre coffre ne doit pas être le modèle de déploiement hérité *VMware vers Azure*. Avant de continuer, commencez par passer au modèle de déploiement amélioré.

* Aucun travail actif initié par l’utilisateur qui implique des opérations de plan de gestion : l’accès au plan de gestion étant limité pendant la mise à niveau, terminez toutes vos actions de plan de gestion avant de déclencher la mise à niveau. Ce processus n’inclut pas la réplication en cours.

## <a name="frequently-asked-questions"></a>Forum Aux Questions

**Cette mise à niveau affecte-t-elle la réplication en cours ?**

Non. La réplication en cours se poursuit sans interruption pendant et après la mise à niveau.

**Que passe-t-il avec les paramètres réseau, tels que les paramètres IP et VPN de site à site ?**

La mise à niveau n’affecte pas les paramètres réseau. Toutes les connexions depuis Azure vers un emplacement local demeurent intactes.

**Qu’advient-il de mes coffres si je n’envisage pas d’effectuer la mise à niveau dans un avenir proche ?**

Il est prévu de déprécier la prise en charge du coffre Site Recovery dans l’ancien portail Azure à compter du mois de septembre 2017. Nous préconisons l’utilisation de la fonctionnalité de mise à niveau pour passer au nouveau portail.

**Quelles sont les implications de ce plan de migration pour mes outils existants ?**  

La mise à jour de vos outils vers le modèle de déploiement Resource Manager constitue l’un des plus importants changements à prendre en compte dans vos plans de mise à niveau. Les coffres Recovery Services s’appuient sur le modèle de déploiement Resource Manager. 

**Quelle est la durée de l’arrêt du plan de gestion ?**

La mise à niveau prend généralement entre 15 et 30 minutes environ, et elle peut aller jusqu’à une heure.

**Puis-je restaurer après la mise à niveau ?**

Non. La restauration n’est pas prise en charge une fois la mise à niveau des ressources opérée avec succès.

**Puis-je vérifier si mon abonnement ou mes ressources peuvent faire l’objet d’une mise à niveau ?**

Oui. Dans l’option de mise à niveau prise en charge par la plateforme, la première étape de la mise à niveau consiste à vérifier que les ressources peuvent être mises à niveau. Si la validation échoue, vous recevez des messages d’erreur ou des avertissements en conséquence.

**Comment signaler un problème avec la mise à niveau ?**

Si vous rencontrez des échecs lors de la mise à niveau, notez l’ID d’opération qui est répertorié dans l’erreur. Le Support Microsoft travaille activement en amont à la résolution du problème. Vous pouvez aussi contacter l’équipe de support technique et lui communiquer votre ID d’abonnement, le nom du coffre et l’ID de l’opération. Le support technique s’efforce de résoudre le problème le plus rapidement possible. Ne retentez l’opération que si vous y êtes explicitement invité par Microsoft.

## <a name="run-the-script"></a>Exécutez le script

Dans PowerShell, exécutez la commande suivante :

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionID <subscriptionID>  -VaultName <vaultname> -Location <location> -ResourceType HyperVRecoveryManagerVault -TargetResourceGroupName <rgname>

* SubscriptionID : ID d’abonnement associé au coffre qui est en cours de mise à niveau.

* VaultName : le nom du coffre que vous mettez à niveau.

* Location : l’emplacement du coffre que vous mettez à niveau.

* ResourceType : HyperVRecoveryManagerVault pour les coffres Site Recovery.

* TargetResourceGroupName : le groupe de ressources dans lequel vous souhaitez placer le coffre mis à niveau. TargetResourceGroupName peut être un groupe de ressources existant dans Azure Resource Manager ou un nouveau groupe. Si le TargetResourceGroupName qui est fourni n’existe pas, il est créé dans le cadre de la mise à niveau au même emplacement que le coffre. Pour plus d’informations, consultez la section « Groupes de ressources » de [Présentation d’Azure Resource Manager](../azure-resource-manager/resource-group-overview.md#resource-groups).

    >[!NOTE]
    >L’attribution de noms de groupe de ressources est soumise à certaines contraintes. Pour éviter un échec de la mise à niveau du coffre, veillez à respecter attentivement la convention d’affectation de noms.
    >
    >Par exemple :
    >
    >.\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionId 1234-54123-354354-56416-8645 -VaultName gen2dr -Location "north europe" -ResourceType hypervrecoverymanagervault -TargetResourceGroupName abc

Vous pouvez également exécuter le script suivant. Entrez les valeurs pour les paramètres obligatoires.

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1
    cmdlet RecoveryServicesVaultUpgrade-1.0.0.ps1 at command pipeline position 1

    Supply values for the following parameters:
    SubscriptionId:
    VaultName:
    Location:
    ResourceType:
    TargetResourceGroupName:

1. Le script PowerShell vous invite à entrer vos informations d’identification. Entrez-les à deux reprises, une fois pour le compte du modèle de déploiement classique, et une autre pour le compte Azure Resource Manager.

2. Lorsque vous avez entré vos informations d’identification, le script exécute une vérification pour déterminer si la configuration de votre infrastructure répond aux exigences mentionnées précédemment.

3. Une fois les prérequis vérifiés et confirmés, vous êtes invité à poursuivre la mise à niveau du coffre. Le processus démarre la mise à niveau de votre coffre. La mise à niveau entière peut prendre entre 15 et 30 minutes.

4. Dès que la mise à niveau est terminée, vous pouvez accéder au coffre mis à niveau dans le nouveau portail Azure.

## <a name="post-upgrade-vault-management"></a>Gestion du coffre après la mise à niveau

### <a name="replicate-by-using-azure-site-recovery-in-the-recovery-services-vault"></a>Répliquer à l’aide d’Azure Site Recovery dans le coffre Recovery Services

* Vous pouvez maintenant protéger vos machines virtuelles Azure d’une région à une autre. Pour plus d’informations, consultez [Répliquer des machines virtuelles Azure entre des régions avec Azure Site Recovery](site-recovery-azure-to-azure.md).

* Pour plus d’informations sur la réplication des machines virtuelles VMware vers Azure, consultez [Répliquer des machines virtuelles VMware vers Azure Site Recovery](vmware-walkthrough-overview.md).

* Pour plus d’informations sur la réplication des machines virtuelles Hyper-V (sans VMM) vers Azure, consultez [Répliquer des machines virtuelles Hyper-V (sans VMM) vers Azure](hyper-v-site-walkthrough-overview.md).

* Pour plus d’informations sur la réplication des machines virtuelles Hyper-V (avec VMM) vers Azure, consultez [Répliquer vers Azure des machines virtuelles Hyper-V hébergées dans des clouds VMM à l’aide de Site Recovery sur le portail Azure](vmm-to-azure-walkthrough-overview.md).

* Pour plus d’informations sur la réplication des machines virtuelles Hyper-V (avec VMM) vers un site secondaire, consultez [Répliquer des machines virtuelles Hyper-V dans des clouds VMM sur un site VMM secondaire avec le portail Azure](site-recovery-vmm-to-vmm.md).

* Pour plus d’informations sur la réplication des machines virtuelles VMware vers un site secondaire, consultez [Répliquer des machines virtuelles ou serveurs physiques VMware locaux sur un site secondaire dans le portail Azure Classic](site-recovery-vmware-to-vmware.md).

### <a name="view-your-replicated-items"></a>Afficher vos éléments répliqués

L’illustration suivante montre la page du tableau de bord de coffre Recovery Services qui affiche les entités de clés pour le coffre. Pour afficher la liste des entités protégées dans le coffre, sélectionnez **Site Recovery** > **Éléments répliqués**.


![Éléments répliqués](./media/upgrade-site-recovery-vaults/replicateditems.png)

L’illustration suivante montre le flux de travail pour l’affichage de vos éléments répliqués, et la commande **Basculement** pour l’initialisation d’un basculement.

![Éléments répliqués](./media/upgrade-site-recovery-vaults/failover.png)

### <a name="view-your-replication-settings"></a>Afficher vos paramètres de réplication

Dans le coffre Site Recovery, chaque groupe de protection est configuré à l’aide de la fréquence de copie, la rétention des points de récupération, la fréquence des instantanés de cohérence de l’application ainsi que d’autres paramètres de réplication. Dans le coffre Recovery Services, ces paramètres sont configurés en tant que stratégie de réplication. Le nom de la stratégie est celui du groupe de protection ou de *primarycloud_Policy*.

Pour plus d’informations sur la stratégie de réplication, consultez [Gérer la stratégie de réplication pour VMware vers Azure](site-recovery-setup-replication-settings-vmware.md).
