---
title: "Reprotection d’Azure vers un site local | Microsoft Docs"
description: "Après avoir automatiquement restauré des machines virtuelles vers Azure, vous pouvez restaurer ces machines virtuelles vers votre site local. Découvrez comment effectuer une reprotection avant une restauration automatique."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: gauravd
editor: 
ms.assetid: 44813a48-c680-4581-a92e-cecc57cc3b1e
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: ruturajd
ms.translationtype: Human Translation
ms.sourcegitcommit: ef1e603ea7759af76db595d95171cdbe1c995598
ms.openlocfilehash: d77f9c4e6365c95b0ea1bf4d00b9f2e9c35eefde
ms.contentlocale: fr-fr
ms.lasthandoff: 06/16/2017


---
# <a name="reprotect-from-azure-to-an-on-premises-site"></a>Reprotection d’Azure vers un site local



## <a name="overview"></a>Vue d’ensemble
Cet article explique comment reprotéger des machines virtuelles Azure d’Azure vers le site local. Suivez les instructions de cet article lorsque vous êtes prêt à restaurer automatiquement vos machines virtuelles VMware ou vos serveurs physiques Windows/Linux après leur basculement du site local vers Azure, en utilisant la procédure [Répliquer des machines virtuelles VMware et des serveurs physiques sur Azure avec Azure Site Recovery](site-recovery-failover.md).

> [!WARNING]
> Si vous avez [procédé à la migration](site-recovery-migrate-to-azure.md#what-do-we-mean-by-migration), déplacé la machine virtuelle vers un autre groupe de ressources ou supprimé la machine virtuelle Azure, vous ne pouvez pas effectuer de restauration automatique après cela.


Une fois la reprotection terminée et les machines virtuelles protégées en cours de réplication, vous pouvez lancer une restauration automatique sur les machines virtuelles afin de les installer sur le site local.

Publiez vos commentaires ou vos questions en bas de cet article ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Pour une vue d’ensemble, regardez la vidéo suivante sur la procédure de basculement d’Azure vers un site local.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]


## <a name="prerequisites"></a>Composants requis
Voici les quelques étapes préalables à prendre en compte lors de la préparation de la reprotection.

* Si les machines virtuelles vers lesquelles vous voulez effectuer une restauration automatique sont gérées par un serveur vCenter, vous devez vous assurer de disposer des autorisations requises pour la détection des machines virtuelles sur les serveurs vCenter. [En savoir plus](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).

> [!WARNING]
> Si des captures instantanées sont présentes sur la cible maître local ou la machine virtuelle, la reprotection échoue. Vous pouvez supprimer les captures instantanées sur la cible maître avant de procéder à la reprotection. Les captures instantanées sur la machine virtuelle vont être fusionnées automatiquement lors de la tâche de reprotection.

* Avant de procéder à une restauration automatique, vous allez devoir créer deux autres composants :
  * **Créez un serveur de processus**. Le serveur de processus reçoit des données à partir de la machine virtuelle protégée dans Azure et envoie des données vers le site local. Un réseau à faible latence est requis entre le serveur de processus et la machine virtuelle protégée. Par conséquent, vous pouvez avoir un serveur de processus local si vous utilisez une connexion Azure ExpressRoute ou un serveur de processus Azure si vous utilisez un VPN.
  * **Créez un serveur cible maître**. Le serveur cible maître reçoit des données de restauration automatique. Un serveur cible maître est installé par défaut sur le serveur d’administration sur site que vous avez créé. Toutefois, en fonction du volume de trafic restauré automatiquement, vous devrez peut-être créer un serveur cible maître distinct pour procéder à la restauration automatique.
        * [Si vous avez une machine virtuelle Linux, vous avez besoin d’un serveur cible maître Linux](site-recovery-how-to-install-linux-master-target.md).
        * Si vous avez une machine virtuelle Windows, vous avez besoin d’un serveur cible maître Windows. Vous pouvez réutiliser le serveur de processus local et les machines cibles maîtres.
* Un serveur de configuration est requis localement, lorsque vous effectuez une restauration automatique. Lors de la restauration automatique, la machine virtuelle doit exister dans la base de données du serveur de configuration. Sinon, la restauration automatique échouera. Veillez à effectuer des sauvegardes régulières de votre serveur. En cas d'incident, vous devez restaurer le serveur avec la même adresse IP pour que la restauration automatique réussisse.
* Vérifiez que vous avez défini le paramètre disk.EnableUUID=true dans les paramètres de configuration de la machine virtuelle cible maître dans VMware. Si cette ligne n’existe pas, ajoutez-la. Ce paramètre est nécessaire pour fournir un UUID cohérent au disque de machine virtuelle (VMDK) et lui assurer ainsi un montage correct.
* *Vous ne pouvez pas utiliser Storage vMotion sur le serveur cible maître*. Cela peut provoquer l’échec de la restauration. La machine virtuelle ne démarrera pas, car elle n’aura pas accès aux disques. Pour éviter ce problème, excluez les serveurs cibles maîtres de votre liste vMotion.
* Vous devez ajouter un nouveau disque sur le serveur maître cible. Ce lecteur est appelé lecteur de rétention. Ajoutez un disque et formatez le lecteur.
* D’autres conditions préalables s’appliquent au serveur cible maître. Elles sont répertoriées dans la section [Points à vérifier après l’installation du serveur cible maître](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-the-master-target-server).


### <a name="why-do-i-need-a-s2s-vpn-or-an-expressroute-connection-to-replicate-data-back-to-the-on-premises-site"></a>Pourquoi ai-je besoin d’un VPN S2S ou d’une connexion ExpressRoute pour répliquer les données sur le site local ?
Alors que la réplication à partir d’un site local vers Azure peut s’effectuer via Internet ou une connexion ExpressRoute avec une homologation publique, la reprotection et la restauration automatique nécessitent un VPN site-à-site (S2S) configuré pour répliquer les données. Le réseau doit être configuré de sorte que les machines virtuelles basculées dans Azure puissent atteindre (avec une requête ping) le serveur de configuration local. Vous pouvez également déployer un serveur de processus dans le réseau Azure de la machine virtuelle basculée. Ce serveur de processus doit également être en mesure de communiquer avec le serveur de configuration local.

### <a name="when-should-i-install-a-process-server-in-azure"></a>Quand convient-il d’installer un serveur de processus dans Azure ?


Les machines virtuelles dans Azure que vous souhaitez reprotéger envoient des données de réplication à un serveur de processus. Votre réseau doit être configuré pour que les machines virtuelles dans Azure puissent atteindre le serveur de processus.

Vous pouvez déployer un serveur de processus dans Azure ou utiliser le serveur de processus existant que vous avez utilisé au cours du basculement. Il est important de prendre en compte la latence lorsque vous envoyez les données des machines virtuelles dans Azure vers le serveur de processus.

Si vous disposez d’une connexion ExpressRoute, un serveur de processus local peut être utilisé pour envoyer des données, car la latence entre la machine virtuelle et le serveur de processus est faible.

 ![Diagramme d’architecture pour ExpressRoute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



Toutefois, si vous disposez uniquement d’un VPN S2S, nous vous recommandons de déployer le serveur de processus dans Azure.

 ![Diagramme d’architecture pour VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


N’oubliez pas que la réplication s’effectue uniquement sur le VPN S2S ou via l’homologation privée de votre réseau ExpressRoute. Assurez-vous de disposer de suffisamment de bande passante sur ce réseau.

Pour en savoir plus sur l’installation d’un serveur de processus Azure, consultez [cet article](site-recovery-vmware-setup-azure-ps-resource-manager.md).

> [!TIP]
> Nous recommandons toujours d’utiliser un serveur de processus Azure lors de la restauration automatique. Les performances de réplication sont plus élevées si le serveur de processus est plus proche de la machine virtuelle de réplication (machine basculée dans Azure). Toutefois, dans vos preuves de concepts ou lors des démonstrations, vous pouvez utiliser le serveur de processus local avec ExpressRoute avec l’homologation privée pour accélérer ces démonstrations.



### <a name="what-are-the-ports-to-be-open-on-different-components-so-that-reprotect-can-work"></a>Quels ports doivent être ouverts sur les différents composants pour que la reprotection fonctionne ?

![Basculement-restauration automatique sur tous les ports](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

### <a name="which-master-target-server-should-be-used-for-reprotect"></a>Quel serveur cible maître faut-il utiliser pour la reprotection ?
Un serveur cible maître local est requis pour recevoir les données du serveur de processus, puis pour écrire ces données dans le VMDK de la machine virtuelle locale. Si vous protégez des machines virtuelles Windows, vous avez besoin d’un serveur cible maître Windows. Vous pouvez réutiliser le serveur de processus local et le serveur cible maître <!-- !todo component -->. Pour les machines virtuelles Linux, vous devez configurer un serveur cible maître Linux local supplémentaire.


Cliquez sur les liens suivants pour en savoir plus sur l’installation d’un serveur cible maître :

* [Installation d’un serveur cible maître Windows](site-recovery-vmware-to-azure.md#run-site-recovery-unified-setup)
* [Installation d’un serveur cible maître Linux](site-recovery-how-to-install-linux-master-target.md)


### <a name="what-datastore-types-are-supported-on-the-on-premises-esxi-host-during-failback"></a>Quels types de banques de données sont pris en charge sur l’hôte ESXi local lors d’une restauration automatique ?

Actuellement, ASR prend en charge uniquement la restauration automatique vers une banque de données VMFS. Les banques de données vSAN ou NFS ne sont pas prises en charge. Notez que vous pouvez protéger des machines virtuelles s’exécutant sur un banque de données vSAN ou NFS. En raison de cette limitation, l’entrée de sélection de banque de données dans l’écran de reprotection est vide s’il s’agit d’une banque de données NFS, ou affiche la banque de données vSAN mais échoue lors de l’exécution du travail. Si vous voulez une restauration automatique, vous pouvez créer une banque de données VMFS locale, puis opérer la restauration automatique sur celle-ci. Cette restauration automatique provoque un téléchargement complet du VMDK. Dans les versions à venir, nous ajouterons la prise en charge des banques de données NFS et vSAN.

#### <a name="common-things-to-check-after-completing-installation-of-the-master-target-server"></a>Points à vérifier après l’installation du serveur cible maître

* Si la machine virtuelle est présente en local sur le serveur vCenter, le serveur cible maître a besoin d’accéder au VMDK de la machine virtuelle locale. Cet accès est nécessaire pour écrire les données répliquées sur des disques de la machine virtuelle. Assurez-vous que le magasin de données de la machine virtuelle locale est monté sur l’hôte du serveur cible maître avec accès en lecture/écriture.

* Si la machine virtuelle n’est pas présente en local sur le serveur vCenter, le service Site Recovery doit créer une machine virtuelle durant la reprotection. Cette machine virtuelle sera créée sur l’hôte ESX sur lequel vous créez le serveur cible maître. Sélectionnez bien l’hôte ESX afin que la machine virtuelle de la restauration automatique soit créée sur l’hôte de votre choix.

* *Vous ne pouvez pas utiliser Storage vMotion pour le serveur cible maître*. Cela peut provoquer l’échec de la restauration. La machine virtuelle ne démarrera pas, car elle n’aura pas accès aux disques.

> [!WARNING]
> Si un serveur cible maître subit un Storage vMotion après la reprotection, les disques des machines virtuelles protégés qui sont attachés au serveur cible maître sont migrés vers la cible de vMotion. Si vous essayez d’effectuer une restauration automatique après cela, le détachement des disques échoue et indique que les disques sont introuvables. Après quoi, il devient très difficile de trouver les disques dans vos comptes de stockage. Vous devez les rechercher manuellement et les attacher à la machine virtuelle. Après quoi, vous pouvez démarrer la machine virtuelle locale.

* Vous devez ajouter un nouveau lecteur dans votre serveur cible maître Windows existant. Ce lecteur est appelé lecteur de rétention. Ajoutez un disque et formatez le lecteur. Le lecteur de rétention est utilisé pour arrêter les points dans le temps où la machine virtuelle est répliquée vers le site local. Pour être répertorié pour le serveur cible maître, un lecteur de rétention doit respecter les critères suivants.

   * Le volume ne doit pas être utilisé à d’autres fins, notamment cible de réplication, etc.

   * Le volume ne doit pas être en mode de verrouillage.

   * Le volume ne doit pas être un volume de cache. Aucune installation de serveur maître cible ne doit exister sur ce volume. Un volume d’installation personnalisée serveur de processus+serveur cible maître n’est pas éligible comme volume de rétention. Lorsque le serveur de processus et le serveur cible maître sont installés sur un volume, ce volume est un volume de cache du serveur cible maître.

   * Le type de système de fichiers du volume ne doit pas être FAT ou FAT32.

   * La capacité du volume ne doit pas être nulle.

   * Le volume de rétention par défaut pour Windows est le volume R.

   * Le volume de rétention par défaut pour Linux est /mnt/retention.

   > [!IMPORTANT]
   > Vous devez ajouter un nouveau lecteur si vous utilisez une machine CS+PS existante, une mise à l’échelle ou une machine PS+MT. Le nouveau lecteur doit respecter les conditions ci-dessus. Si le lecteur de rétention n’est pas présent, aucun n’apparaît dans la liste de sélection déroulante sur le portail. Une fois que vous avez ajouté un lecteur au serveur cible maître local, quinze minutes sont nécessaires pour que ce lecteur apparaisse dans la sélection sur le portail. Vous pouvez également actualiser le serveur de configuration si le lecteur n’apparaît pas au bout de 15 minutes.



* Si vous avez une machine virtuelle Linux basculée, vous avez besoin d’un serveur cible maître Linux. Si vous avez une machine virtuelle Windows basculée, vous avez besoin d’un serveur cible maître Windows.

* Installez les outils VMware sur le serveur cible maître. Sans les outils VMware, les magasins de données sur l’hôte ESXi du serveur cible maître ne peuvent pas être détectés.

* Activez le paramètre disk.EnableUUID=true sur la machine virtuelle du serveur cible maître via les propriétés de vCenter. <!-- !todo Needs link. -->

* Le serveur cible maître doit avoir au moins un magasin de données de système de fichiers de machines virtuelles (VMFS) attaché. S’il n’y en a aucun, l’entrée **Magasin de données** sur la page de reprotection sera vide et vous ne pourrez pas continuer.

* Le serveur cible maître ne peut pas avoir de captures instantanées sur les disques. S’il existe des captures instantanées, l’opération de reprotection/restauration automatique échouera.

* Le serveur cible maître ne peut pas présenter de contrôleur SCSI Paravirtual. Il peut uniquement s’agir d’un contrôleur LSI Logic. Sans contrôleur LSI Logic, la reprotection échouera.

<!--
### Failback policy
To replicate back to on-premises, you will need a failback policy. This policy get automatically created when you create a forward direction policy. Note that

1. This policy gets auto associated with the configuration server during creation.
2. This policy is not editable.
3. The set values of the policy are (RPO Threshold = 15 Mins, Recovery Point Retention = 24 Hours, App Consistency Snapshot Frequency = 60 Mins)
   ![](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

-->

## <a name="steps-to-reprotect"></a>Procédure de reprotection

Avant de procéder à la reprotection, assurez-vous que vous avez installé le [serveur de processus](site-recovery-vmware-setup-azure-ps-resource-manager.md) dans Azure et le [serveur cible maître Linux](site-recovery-how-to-install-linux-master-target.md) ou Windows local.

> [!NOTE]
> Après le démarrage d’une machine virtuelle dans Azure, il faut un certain temps à l’agent pour se réinscrire auprès du serveur de configuration (jusqu’à 15 minutes). Pendant ce temps, la reprotection échoue et renvoie un message d’erreur indiquant que l’agent n’est pas installé. Patientez quelques minutes et essayez de relancer la reprotection.



1. Dans **Coffre** > **Éléments répliqués**, cliquez avec le bouton droit de la souris sur la machine virtuelle ayant basculé et sélectionnez **Reprotéger**. Vous pouvez également cliquer sur la machine et sélectionner **Reprotéger** à partir des boutons de commande.
2. Dans le panneau, notez que la direction de protection **Azure à local** est déjà sélectionnée.
3. Dans les champs **Serveur cible maître** et **Serveur de processus**, sélectionnez le serveur cible maître local et le serveur de processus.
4. Sélectionnez le **Magasin de données** dans lequel vous souhaitez récupérer les disques locaux. Cette option est utilisée lorsque la machine virtuelle locale est supprimée et que vous devez créer de nouveaux disques. Cette option est ignorée si les disques existent déjà, mais vous devez toujours spécifier une valeur.
5. Choisissez le lecteur de rétention.
6. La stratégie de restauration automatique sera automatiquement sélectionnée.
7. Une fois que vous avez cliqué sur **OK** pour démarrer la reprotection, une tâche est lancée pour répliquer la machine virtuelle à partir d’Azure vers le site local. Vous pouvez en suivre la progression sous l’onglet **Travaux** .

Si vous souhaitez procéder à la récupération vers un autre emplacement (si la machine virtuelle locale est supprimée), sélectionnez le lecteur de rétention et le magasin de données configurés pour le serveur cible maître. Si vous effectuez la restauration automatique vers le site local, les machines virtuelles VMware du plan de protection de la restauration automatique utilisent la même banque de données que le serveur cible maître. Une nouvelle machine virtuelle sera créée dans vCenter.
Si vous souhaitez récupérer la machine virtuelle Azure sur une machine virtuelle locale existante, les magasins de données de la machine virtuelle locale doivent être montés avec accès en lecture/écriture sur l’hôte ESXi du serveur cible maître.
    ![Boîte de dialogue Reprotéger](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

Vous pouvez également effectuer la reprotection au niveau du plan de récupération. Un groupe de réplication peut uniquement être reprotégé à l’aide d’un plan de récupération. Lorsque vous effectuez la reprotection à l’aide d’un plan de récupération, vous devrez fournir les valeurs de chaque machine protégée.

> [!NOTE]
> Un groupe de réplication doit être reprotégé à l’aide du même serveur cible maître. S’il est reprotégé à l’aide d’un serveur cible maître différent, le serveur ne peut fournir aucun point dans le temps commun.

> [!NOTE]
> La machine virtuelle locale va être désactivée au cours de la reprotection. Cela permet de garantir la cohérence des données pendant la réplication. N’activez pas la machine virtuelle une fois la reprotection terminée.

Une fois la reprotection réussie, la machine virtuelle passe à l’état protégé.

## <a name="next-steps"></a>Étapes suivantes

Une fois que la machine virtuelle est passée à l’état protégé, vous pouvez commencer une restauration automatique. La restauration automatique arrêtera la machine virtuelle dans Azure et démarrera la machine virtuelle en local. L’application sera donc indisponible quelques instants. Par conséquent, effectuez la restauration automatique lorsque votre application peut tolérer un temps d’arrêt.

[Étapes d’initialisation de la restauration automatique de la machine virtuelle](site-recovery-how-to-failback-azure-to-vmware.md#steps-to-fail-back)

## <a name="common-issues"></a>Problèmes courants

* Si vous avez utilisé un modèle pour créer vos machines virtuelles, assurez-vous que chaque machine virtuelle possède un UUID pour les disques. Si l’UUID de la machine virtuelle locale est en conflit avec celui du serveur cible maître (car ceux-ci ont tous deux été créés à partir du même modèle), la reprotection échouera. Vous devez déployer un autre serveur cible maître qui n’a pas été créé à partir du même modèle.
* Si vous effectuez une découverte utilisateur vCenter en mode lecture seule et protégez des machines virtuelles, la protection réussit et le basculement fonctionne. Au cours de la reprotection, le basculement échoue car les banques de données ne peuvent pas être détectées. Par conséquent, les banques de données ne seront pas répertoriées lors de la reprotection. Pour résoudre ce problème, mettez à jour les informations d’identification vCenter à l’aide du compte approprié disposant des autorisations requises, puis renouvelez l’opération. Pour en savoir plus, consultez l’article [Répliquer des machines virtuelles VMware et des serveurs physiques sur Azure avec Azure Site Recovery](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
* Lorsque vous restaurez automatiquement une machine virtuelle Linux et l’exécutez localement, vous constaterez que le package du Gestionnaire de réseau est désinstallé de l’ordinateur. En effet, celui-ci est supprimé une fois la machine virtuelle récupérée dans Azure.
* Lorsqu’une machine virtuelle Linux est configurée avec une adresse IP statique puis restaurée automatiquement vers Azure, l’adresse IP est obtenue à partir de DHCP. Lorsque vous basculez en local, la machine virtuelle continue d’utiliser DHCP pour obtenir l’adresse IP. Connectez-vous manuellement à l’ordinateur et redéfinissez l’adresse IP sur une adresse statique le cas échéant. Une machine virtuelle Windows peut récupérer son adresse IP fixe.
* Si vous utilisez la version gratuite d’ESXi 5.5 ou de vSphere 6 Hypervisor, le basculement aboutit, mais la restauration automatique ne fonctionnera pas. Vous devrez effectuer une mise à niveau vers une licence d’évaluation d’un des programmes pour activer la restauration automatique.
* Si vous ne pouvez pas communiquer avec le serveur de configuration à partir du serveur de processus, utilisez Telnet pour vérifier la connectivité au serveur de configuration sur le port 443. Vous pouvez également essayer d’exécuter un test ping sur le serveur de configuration à partir du serveur de processus. Un serveur de processus doit également avoir une pulsation lorsqu’il est connecté au serveur de configuration.
* Si vous tentez d’effectuer une restauration automatique vers un autre serveur vCenter, vérifiez que le nouveau serveur vCenter et le serveur cible maître sont détectés. Un symptôme courant est que les banques de données ne sont pas accessibles/visibles dans la boîte de dialogue **Reprotéger**.
* Un serveur Windows Server 2008 R2 SP1 protégé comme serveur physique sur site ne peut pas être restauré localement à partir d’Azure.

