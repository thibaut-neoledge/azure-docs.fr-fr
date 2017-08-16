---
title: "Mettre à niveau un coffre Site Recovery vers Recovery Services"
description: "Découvrez comment mettre à niveau un coffre Azure Site Recovery vers Recovery Services"
ddocumentationcenter: 
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
ms.translationtype: HT
ms.sourcegitcommit: 14915593f7bfce70d7bf692a15d11f02d107706b
ms.openlocfilehash: 523cab85b195d85007bd85c45dbe3645f7a00ab1
ms.contentlocale: fr-fr
ms.lasthandoff: 08/10/2017

---
# <a name="upgrade-site-recovery-vaults-to-azure-resource-manager-based-recovery-services-vaults"></a>Mettre à niveau des coffres Site Recovery vers Azure Resource Manager Recovery Services

Cet article explique comment mettre à niveau des « Coffres Site Recovery » vers des « Coffres Recovery Service » basés sur Azure Resource Manager sans aucun impact sur la réplication en cours. Pour en savoir plus sur les fonctionnalités et avantages d’Azure Resource Manager, consultez [cet article](../azure-resource-manager/resource-group-overview.md).

## <a name="introduction"></a>Introduction
Coffre Recovery Services est une ressource Azure Resource Manager qui vous permet de gérer vos besoins en sauvegarde et en récupération d’urgence en mode natif dans le cloud. Il s’agit d’un coffre unifié qui peut être utilisé dans le nouveau portail Azure. Il remplace les coffres Sauvegarde et Site Recovery classiques.

Les coffres Recovery Services donnent accès à un ensemble de fonctionnalités, notamment :

-   Prise en charge d’Azure Resource Manager : vous pouvez protéger et basculer vos machines virtuelles et ordinateurs physiques dans la pile Azure Resource Manager.

-   Exclusion de disque : si vous avez des fichiers temporaires ou des données avec un taux de variation élevé pour lesquels vous ne souhaitez pas utiliser la bande passante, vous pouvez exclure des volumes de la réplication. Cette fonctionnalité est activée actuellement pour « VMware vers Azure » et « Hyper-V vers Azure », et sera bientôt étendue à d’autres scénarios.

- Prise en charge du Stockage localement redondant et Premium : vous pouvez désormais protéger des serveurs dans des comptes de stockage Premium qui permettent aux clients de protéger les applications avec des E/S plus élevées. Cette fonctionnalité est activée actuellement dans « VMware vers Azure ».

-   Expérience de démarrage simplifiée : l’expérience avancée de démarrage a été adaptée afin que la configuration de la récupération d’urgence soit le plus simple possible.

- Gérer Sauvegarde et Site Recovery à partir du même coffre : vous pouvez désormais protéger les serveurs pour la récupération d’urgence ou effectuer une sauvegarde à partir du même coffre, ce qui réduit considérablement la surcharge de gestion.

Pour plus d’informations sur les fonctionnalités et sur l’expérience de mise à niveau, consultez ce [blog](https://azure.microsoft.com/blog/azure-site-recovery-now-available-in-a-new-experience-with-support-for-arm-and-csp/).

## <a name="salient-features"></a>Principales fonctionnalités

- **Aucun impact sur la réplication en cours**: les réplications en cours se poursuivent sans interruption pendant et après la mise à niveau.

- **Aucun coût supplémentaire** : bénéficiez de tout un ensemble de nouvelles fonctionnalités sans coût supplémentaire.

- **Aucune perte de données** : s’agissant d’une mise à niveau et non d’une migration, les informations de réplication existantes (points de récupération, paramètres de réplication etc.) restent intactes pendant et après la mise à niveau.


## <a name="what-happens-during-the-upgrade"></a>Que se passe-t-il pendant la mise à niveau ?

Les opérations, telles que l’inscription d’un nouveau serveur ou l’activation de la réplication d’une machine virtuelle, ne sont pas autorisées pendant la mise à niveau. Toute opération qui implique simplement la lecture ou l’écriture de données dans le coffre, comme la réplication en cours d’éléments protégés dans le coffre, se poursuit sans interruption.

## <a name="changes-to-your-automation-and-tooling-after-vault-upgrade"></a>Changements de vos services d’automatisation et de vos outils après la mise à niveau du coffre
Dans le cadre de la mise à niveau du coffre du modèle de déploiement classique vers le modèle de déploiement Resource Manager, vous devez mettre à jour votre automatisation ou vos outils pour vous assurer qu’ils continueront à fonctionner après la mise à niveau.

## <a name="preparing-your-environment-for-vault-upgrade"></a>Préparation de votre environnement pour la mise à niveau du coffre

1.  Installez/mettez à niveau PowerShell avec la version 5 ou ultérieure via ce [lien](https://www.microsoft.com/download/details.aspx?id=50395).

2.  Installez la dernière version d’Azure PowerShell MSI à partir de ce [lien](https://github.com/Azure/azure-powershell/releases).

3.  [Téléchargez](https://aka.ms/vaultupgradescript) le script de mise à niveau du coffre.

## <a name="prerequisites-for-upgrade"></a>Prérequis pour la mise à niveau
Vous devez respecter les prérequis suivants pour pouvoir mettre à niveau vos coffres Site Recovery vers Azure Resource Manager Recovery Services.

- Version minimale de l’agent : la mise à niveau exige que la version du fournisseur Azure Site Recovery installée sur votre serveur soit au moins 5.1.1700.0.

- Configuration prise en charge : votre coffre ne doit pas être configuré avec SAN ou des groupes de disponibilité SQL AlwaysOn. Toutes les autres configurations sont prises en charge.

>[!NOTE]
> Le mappage de stockage ne peut être géré par le biais de PowerShell qu’après la mise à niveau.

- Scénario de déploiement pris en charge : votre coffre ne doit pas être sur le modèle de déploiement hérité « VMware vers Azure ».  Avant de continuer, vous devez basculer vers le modèle de déploiement amélioré.

- Aucun travail actif initié par l’utilisateur qui implique des opérations de plan de gestion : l’accès au plan de gestion étant limité pendant la mise à niveau, vous devez terminer toutes vos actions de plan de gestion, puis déclencher la mise à niveau. Cela n’inclut pas la réplication en cours.

## <a name="frequently-asked-questions"></a>Questions fréquentes (FAQ)

- Cette mise à niveau affecte-t-elle la réplication en cours ?

  Non. La réplication en cours se poursuit sans interruption pendant et après la mise à niveau.

- Que passe-t-il avec les paramètres réseau (VPN de site à site, paramètres IP, et ainsi de suite) ?

  La mise à niveau n’affecte pas les paramètres réseau. Toutes les connexions entre Azure et l’infrastructure locale restent inchangées.
- Qu’advient-il de mes coffres si je n’envisage pas d’effectuer la mise à niveau dans un avenir proche ?

  La prise en charge du coffre Site Recovery dans l’ancien portail Azure sera dépréciée à partir du mois de septembre. Par conséquent, nous recommandons vivement aux clients d’utiliser la fonctionnalité de mise à niveau pour passer au nouveau portail.

- Quelles sont les implications de ce plan de migration pour mes outils existants ?  

  La mise à jour de vos outils avec le modèle de déploiement Resource Manager sur lequel les coffres Recovery Services sont basés est l’une des principales modifications que vous devez prendre en compte dans vos plans de mise à niveau.

- Quelle sera la durée de l’arrêt du plan de gestion ?

  La mise à niveau prend environ 15 à 30 minutes. Elle peut durer jusqu’à une heure.

- Puis-je restaurer après la mise à niveau ?

  Non. La restauration n’est pas prise en charge une fois la mise à niveau des ressources réussie.

- Puis-je vérifier si mon abonnement ou mes ressources peuvent faire l’objet d’une mise à niveau ?

  Oui. Dans l’option de mise à niveau prise en charge par la plateforme, la première étape de la mise à niveau consiste à vérifier que les ressources peuvent faire l’objet d’une mise à niveau. En cas d’échec de la validation des prérequis, vous recevez un message d’erreur ou d’avertissement approprié.

- Comment signaler un problème avec la mise à niveau ?

  Si vous êtes confronté à des défaillances durant la mise à niveau, notez la valeur OperationId mentionnée dans le message d’erreur. Le Support Microsoft travaillera de façon proactive à la résolution du problème. Vous pouvez aussi contacter l’équipe de support technique et lui communiquer votre ID d’abonnement, le nom du coffre et l’ID de l’opération. Nous nous efforcerons de résoudre le problème au plus tôt. Ne retentez l’opération que sauf si vous y êtes explicitement invité par Microsoft.

## <a name="how-to-run-the-script"></a>Comment exécuter le script ?

Exécutez la commande suivante à partir d’une invite de commandes PowerShell :

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionID <subscriptionID>  -VaultName <vaultname> -Location <location> -ResourceType HyperVRecoveryManagerVault -TargetResourceGroupName <rgname>

- SubscriptionID : ID d’abonnement associé au coffre en cours de mise à niveau.
- VaultName : nom du coffre en cours de mise à niveau.
- Location : emplacement du coffre en cours de mise à niveau.
- ResourceType : HyperVRecoveryManagerVault pour les coffres Site Recovery.
- TargetResourceGroupName : groupe de ressources dans lequel vous souhaitez placer le coffre mise à niveau. TargetResourceGroupName peut être un groupe de ressources existant dans Azure Resource Manager ou un nouveau groupe. Si le TargetResourceGroupName fourni n’existe pas, il est créé dans le cadre de la mise à niveau au même emplacement que le coffre. Pour en savoir plus sur les groupes de ressources, cliquez [ici](../azure-resource-manager/resource-group-overview.md#resource-groups) :

>[!NOTE]
>Les noms de groupes de ressources sont soumis à certaines contraintes. Ne pas respecter ces contraintes pourrait entraîner l’échec de la mise à niveau du coffre.

Exemple :

    .\RecoveryServicesVaultUpgrade-1.0.0.ps1 -SubscriptionId 1234-54123-354354-56416-8645 -VaultName gen2dr -Location "north europe" -ResourceType hypervrecoverymanagervault -TargetResourceGroupName abc


Autrement, vous pouvez exécuter le script ci-dessous qui vous invite à fournir des entrées pour tous les paramètres obligatoires.

    PS > .\RecoveryServicesVaultUpgrade-1.0.0.ps1
    cmdlet RecoveryServicesVaultUpgrade-1.0.0.ps1 at command pipeline position 1

    Supply values for the following parameters:
    SubscriptionId:
    VaultName:
    Location:
    ResourceType:
    TargetResourceGroupName:

1.  Le script PowerShell vous invite à entrer vos informations d’identification. Vous devez entrer vos informations d’identification à deux reprises : une fois pour le compte ASM et une autre pour le compte Azure Resource Manager.

2.  Une fois vos informations d’identification entrées, le script exécute une vérification des prérequis pour déterminer que votre infrastructure respecte les prérequis mentionnés plus haut dans le document.

3.  Une fois les prérequis vérifiés, vous êtes invité à confirmer la poursuite de la mise à niveau du coffre. Après cela, le processus démarre la mise à niveau de votre coffre. La mise à niveau entière peut prendre entre 15 et 30 minutes.

4.  Une fois la mise à niveau terminée, vous pouvez accéder au coffre mis à niveau dans le nouveau portail Azure.

## <a name="management-experience-post-upgrade"></a>Expérience de gestion après la mise à niveau

### <a name="how-to-replicate-using-azure-site-recovery-in-the-recovery-services-vault"></a>Réplication à l’aide d’Azure Site Recovery dans le coffre Recovery Services

- Vous pouvez maintenant protéger vos machines virtuelles Azure d’une région à une autre. Pour en savoir plus, consultez [cette documentation](site-recovery-azure-to-azure.md).

- Pour en savoir plus sur la réplication des machines virtuelles VMware vers Azure, consultez [cette documentation](vmware-walkthrough-overview.md).

- Pour en savoir plus sur la réplication des machines virtuelles Hyper-V (sans VMM) vers Azure, consultez [cette documentation](hyper-v-site-walkthrough-overview.md).

- Pour en savoir plus sur la réplication des machines virtuelles Hyper-V (avec VMM) vers Azure, consultez [cette documentation](vmm-to-azure-walkthrough-overview.md).

- Pour en savoir plus sur la réplication des machines virtuelles Hyper-V (avec VMM) vers un site secondaire, consultez [cette documentation](site-recovery-vmm-to-vmm.md).

- Pour en savoir plus sur la réplication des machines virtuelles VMware vers un site secondaire, consultez [cette documentation](site-recovery-vmware-to-vmware.md).

### <a name="how-to-view-your-replicated-items"></a>Affichage de vos éléments répliqués

Voici la page du tableau de bord de coffre Recovery Services qui montre les entités de clés pour le coffre. Cliquez sur **Site Recovery** -> **Éléments répliqués** pour afficher la liste des entités protégées dans le coffre.


![Éléments répliqués](./media/upgrade-site-recovery-vaults/replicateditems.png)

L’écran ci-dessous montre le flux de travail pour l’affichage de vos éléments répliqués, et comment lancer un basculement.

![Éléments répliqués](./media/upgrade-site-recovery-vaults/failover.png)

### <a name="how-to-view-your-replication-settings"></a>Affichage de vos paramètres de réplication

Dans le coffre Site Recovery, chaque groupe de protection est configuré avec des paramètres de réplication (fréquence de copie, rétention des points de récupération, fréquence des instantanés de cohérence de l’application, et ainsi de suite). Dans le coffre Recovery Services, ces paramètres sont configurés en tant que stratégie de réplication. Le nom de la stratégie est celui du groupe de protection ou « primarycloud_Policy ».

Pour en savoir plus sur la stratégie de réplication, consultez [cet article](site-recovery-setup-replication-settings-vmware.md).

