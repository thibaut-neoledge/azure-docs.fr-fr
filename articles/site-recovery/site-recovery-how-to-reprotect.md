---
title: "Reprotection d’Azure vers votre site local | Microsoft Docs"
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
ms.workload: 
ms.date: 02/13/2017
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: f2da9c4b02f0bb8a93347c05be358516f39e2df0
ms.lasthandoff: 03/09/2017


---
# <a name="reprotect-from-azure-to-on-premises"></a>Reprotection d’Azure vers votre site local

## <a name="overview"></a>Vue d'ensemble
Cet article explique comment reprotéger des machines virtuelles Azure d’Azure vers le site local. Suivez les instructions qu’il contient lorsque vous êtes prêt à restaurer automatiquement vos machines virtuelles VMware ou vos serveurs physiques Windows/Linux après leur basculement du site local vers Azure en suivant ce [didacticiel](site-recovery-failover.md).

Une fois la reprotection terminée et les machines virtuelles répliquées, vous pouvez lancer une restauration automatique sur la machine virtuelle afin d’installer les machines virtuelles sur le site local.

Publiez des commentaires ou des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

Pour une présentation vidéo rapide, vous pouvez également visionner la vidéo ci-dessous.
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video5-Failback-from-Azure-to-On-premises/player]

Le processus complet de restauration automatique est présenté ici.

## <a name="pre-requisites"></a>Conditions préalables
Voici les quelques étapes préalables à prendre en compte lors de la préparation de la reprotection.

* Si les machines virtuelles vers lesquelles vous voulez effectuer une restauration automatique sont gérées par un serveur vCenter, vous devez vous assurer de disposer des autorisations requises pour la détection des machines virtuelles sur les serveurs vCenter. [En savoir plus](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
* Si des captures instantanées sont présentes sur la machine virtuelle locale, la reprotection échoue. Vous pouvez supprimer les captures instantanées avant de passer à la reprotection.
* Avant de procéder à une restauration automatique, vous devez créer deux autres composants :
  * **Créez un serveur de processus**. Le serveur de processus sert à recevoir les données de la machine virtuelle protégée dans Azure et à envoyer les données locales. Il doit être installé sur un réseau à faible latence entre le serveur de processus et la machine virtuelle protégée. Par conséquent, le serveur de processus peut être local (si vous utilisez une connexion ExpressRoute) ou sur Azure si vous utilisez un VPN.
  * **Créez un serveur cible maître**. Le serveur cible maître reçoit des données de restauration automatique. Un serveur cible maître est installé par défaut sur le serveur d’administration que vous avez créé sur site. Toutefois, en fonction du volume de trafic restauré automatiquement, vous devrez peut-être créer un serveur cible maître distinct pour procéder à la restauration automatique.
        * [Une machine virtuelle Linux nécessite un serveur cible maître Linux](site-recovery-how-to-install-linux-master-target.md).
        * Une machine virtuelle Windows nécessite une cible maître Windows. Vous pouvez réutiliser la machine de serveur de traitement + cible maître locale.
* Le serveur de configuration est requis en local lorsque vous effectuez une restauration automatique. Lors de la restauration automatique, la machine virtuelle doit exister dans la base de données du serveur de configuration. Sinon, la restauration automatique échouera. Vous devez donc veiller à planifier une sauvegarde régulière de votre serveur. En cas d'incident, vous devrez le restaurer avec la même adresse IP pour que la restauration automatique réussisse.
* Vérifiez que vous avez défini le paramètre disk.enableUUID=true dans les paramètres de configuration de la machine virtuelle cible maître dans VMware. Si cette ligne n’existe pas, ajoutez-la. Cela est nécessaire pour fournir un UUID cohérent au VMDK et lui assurer ainsi un montage correct.
* **Le serveur cible maître ne peut pas être Storage vMotioned**. Cela peut provoquer l’échec de la restauration. La machine virtuelle ne sera pas affichée car les disques ne seront pas disponibles.
* Vous avez besoin d’un nouveau lecteur dans le serveur maître cible. Ce lecteur est appelé lecteur de rétention. Ajoutez un disque et formatez le lecteur.
* D’autres conditions préalables s’appliquent au serveur cible maître. Elles sont répertoriées dans la section [Points à vérifier après l’installation du serveur cible maître](site-recovery-how-to-reprotect.md#common-things-to-check-after-completing-installation-of-master-target).


### <a name="why-do-i-need-a-s2s-vpn-or-an-expressroute-to-replicate-data-back-to-on-premises"></a>Pourquoi ai-je besoin d’un VPN S2S ou d’un réseau ExpressRoute pour répliquer les données sur le site local ?
Alors que la réplication à partir d’un site local vers Azure peut s’effectuer via Internet ou un réseau ExpressRoute avec une homologation publique, la reprotection et la restauration automatique nécessitent un VPN S2S configuré pour répliquer les données. **Le réseau doit être configuré de sorte que les machines virtuelles basculées dans Azure puissent atteindre (avec une requête ping) le serveur de configuration local**. Vous pouvez également déployer un serveur de processus dans le réseau Azure de la machine virtuelle basculée : ce serveur de processus doit également pouvoir communiquer avec le serveur de configuration local.

### <a name="when-should-i-install-a-process-server-in-azure"></a>Quand dois-je installer un serveur de traitement dans Azure ?


Les machines virtuelles Azure que vous souhaitez reprotéger envoient les données de réplication à un serveur de traitement. Votre réseau doit être configuré de sorte que le serveur de processus soit accessible à partir de la machine virtuelle Azure.

Vous pouvez déployer un serveur de processus dans Azure ou utiliser le serveur de processus existant que vous avez utilisé au cours du basculement. Il est important de prendre en compte la latence lorsque vous envoyez les données de machines virtuelles Azure vers le serveur de processus.

* Si vous avez configuré un réseau ExpressRoute, vous pouvez utiliser un serveur de processus local pour envoyer les données. En effet, la latence entre ce serveur de processus et la machine virtuelle est faible.

    ![Diagramme d’architecture pour ExpressRoute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)



* Toutefois, si vous disposez d’un VPN S2S, nous vous recommandons de déployer le serveur de processus dans Azure.

    ![Diagramme d’architecture pour VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)


N’oubliez pas que la réplication s’effectue uniquement sur les VPN S2S ou via l’homologation privée de votre réseau ExpressRoute. Assurez-vous de disposer de suffisamment de bande passante sur ce réseau.

Pour en savoir plus sur l’installation d’un serveur de traitement Azure, consultez [cet article](site-recovery-vmware-setup-azure-ps-resource-manager.md).

### <a name="what-are-the-different-ports-to-be-open-on-different-components-so-that-reprotect-can-work"></a>Quels ports doivent être ouverts sur les différents composants pour que la reprotection fonctionne ?

![Basculement-restauration automatique sur tous les ports](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

### <a name="which-master-target-server-to-use-for-reprotect"></a>Quel type de serveur cible maître faut-il utiliser pour la reprotection ?
Un serveur cible maître est requis localement pour recevoir les données du serveur de processus, puis pour écrire ces données dans le VMDK de la machine virtuelle locale. Si vous protégez des machines virtuelles Windows, vous avez besoin d’un serveur cible maître Windows. Ici, vous pouvez réutiliser les serveurs de processus et serveur cible maître locaux <!-- !todo component -->. Pour les machines virtuelles Linux, vous devez configurer un serveur cible maître Linux local supplémentaire.


Pour connaître la procédure d’installation de ces deux types de serveurs cibles maîtres, cliquez sur les liens ci-dessous.

* [Déploiement d’un serveur cible maître Windows](site-recovery-vmware-to-azure.md#run-site-recovery-unified-setup)
* [Installation d’un serveur cible maître Linux](site-recovery-how-to-install-linux-master-target.md)


#### <a name="common-things-to-check-after-completing-installation-of-master-target"></a>Points à vérifier après l’installation du serveur cible maître

* Si la machine virtuelle est présente en local sur le serveur vCenter, le serveur cible maître a besoin d’accéder au VMDK de la machine virtuelle locale. Cette opération consiste à écrire les données répliquées sur les disques de la machine virtuelle. Pour cela, vous devez vous assurer que **le magasin de données de la machine virtuelle locale est monté sur l’hôte du serveur cible maître avec accès en lecture écriture**.

* Si la machine virtuelle n’est pas présente en local sur le serveur vCenter, une nouvelle machine virtuelle devra être créée lors de la reprotection. Cette machine virtuelle sera créée sur l’hôte ESX sur lequel vous créez le serveur cible maître. Par conséquent, sélectionnez bien l’hôte ESX afin que la machine virtuelle de la restauration automatique soit créée sur l’hôte de votre choix.

* **Le serveur cible maître ne peut pas être Storage vMotioned**. Cela peut provoquer l’échec de la restauration. La machine virtuelle ne sera pas affichée car les disques ne seront pas disponibles.

* Vous avez besoin d’un nouveau lecteur dans votre serveur cible maître Windows existant. Ce lecteur est appelé lecteur de rétention. Ajoutez un disque et formatez le lecteur. Le lecteur de rétention est utilisé pour arrêter les points dans le temps lorsque la machine virtuelle est répliquée vers le site local. Pour être répertorié pour le serveur cible maître, un lecteur de rétention doit respecter les critères suivants.

   * Le volume ne doit pas être utilisé à d’autres fins (cible de réplication, etc.)

   * Le volume ne doit pas être en mode de verrouillage.

   * Le volume ne doit pas être un volume de cache. (Aucune installation de serveur maître cible ne doit exister sur ce volume. Un volume d’installation personnalisée serveur de processus+serveur maître cible n’est pas éligible comme volume de rétention. Ici, le volume serveur de processus+serveur maître cible est le volume de cache du serveur maître cible.)

   * Le type de système de fichiers du volume ne doit pas être FAT ou FAT32.

   * La capacité du volume ne doit pas être nulle.

   * Le volume de rétention par défaut pour Windows est le volume R.

   * Le volume de rétention par défaut pour Linux est /mnt/retention.

* Une machine virtuelle Linux basculée nécessite un serveur cible maître Linux. Une machine virtuelle Windows basculée nécessite un serveur cible maître Windows.

* Installez les outils VMware sur le serveur cible maître. Sans les outils VMware, les magasins de données sur l’hôte ESXi du serveur cible maître ne peuvent pas être détectés.

* Activez le paramètre disk.EnableUUID = True sur la machine virtuelle du serveur cible maître via les propriétés de vCenter. <!-- !todo Needs link. -->

* Au moins un magasin de données VMFS doit être attaché au serveur cible maître. S’il n’y en a aucun, l’entrée du magasin de données sur la page de reprotection sera vide et vous ne pourrez pas continuer.

* Le serveur cible maître ne peut pas avoir d’instantanés sur les disques. S’il existe des instantanés, l’opération de reprotection/restauration automatique échouera.

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

Avant de procéder à la reprotection, assurez-vous que vous avez installé le [serveur de traitement](site-recovery-vmware-setup-azure-ps-resource-manager.md) dans Azure et le [serveur cible maître Linux](site-recovery-how-to-install-linux-master-target.md) ou Windows local.

> [!NOTE]
> Après le démarrage d’une machine virtuelle dans Azure, il faut un certain temps à l’agent pour s’inscrire à nouveau auprès du serveur de configuration (jusqu’à 15 minutes). Dans cet intervalle, la reprotection échouera et vous recevrez un message d’erreur indiquant que l’agent n’est pas installé. Patientez quelques minutes et réessayez de procéder à la reprotection.



1. Dans le coffre, sous Éléments répliqués, sélectionnez la machine virtuelle ayant basculé et cliquez avec le bouton droit sur **Reprotéger**. Vous pouvez également cliquer sur la machine et sélectionner la reprotection à partir des boutons de commande.
2. Dans le panneau, vous pouvez voir que la direction de protection « d’Azure à local » est déjà sélectionnée.
3. Dans les champs **Serveur cible maître** et **Serveur de traitement**, sélectionnez le serveur cible maître local et le serveur de traitement.
4. Sélectionnez le **Magasin de données** dans lequel vous souhaitez récupérer les disques locaux. Cette option est utilisée lorsque la machine virtuelle locale est supprimée et que des disques doivent être créés. Cette option est ignorée si les disques existent déjà, mais vous devez toujours spécifier une valeur.
5. Choisissez le lecteur de rétention.
6. La stratégie de restauration automatique sera automatiquement sélectionnée.
7. Une fois que vous avez cliqué sur **OK** pour démarrer la reprotection, un travail est lancé pour répliquer la machine virtuelle à partir d’Azure vers le site local. Vous pouvez en suivre la progression sous l’onglet **Travaux** .

Si vous souhaitez procéder à la récupération vers un autre emplacement (si la machine virtuelle locale est supprimée), sélectionnez le lecteur de rétention et le magasin de données configurés pour le serveur cible maître. Si vous effectuez la restauration automatique vers le site local, les machines virtuelles VMware du plan de protection de la restauration automatique utilisent le même magasin de données que le serveur cible maître, et une nouvelle machine virtuelle sera créée dans vCenter.
Si vous souhaitez récupérer la machine virtuelle Azure sur une machine virtuelle locale existante, les magasins de données de la machine virtuelle locale doivent être montés avec accès en lecture/écriture sur l’hôte ESXi du serveur cible maître.
    ![](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

Vous pouvez également effectuer la reprotection au niveau du plan de récupération. Si vous avez un groupe de réplication, il peut être reprotégé à l’aide d’un plan de récupération uniquement. Dans le cadre d’une reprotection avec un plan de récupération, vous devrez définir les valeurs ci-dessus pour chaque machine protégée.

> [!NOTE]
> Un groupe de réplication doit être protégé à l’aide du même serveur cible maître. S’il est protégé à l’aide d’un serveur cible maître différent, aucun point dans le temps ne peut être déterminé pour lui.


Une fois la reprotection réussie, la machine virtuelle passe à l’état protégé.

## <a name="next-steps"></a>Étapes suivantes

Une que la machine virtuelle à l’état protégé, vous pouvez commencer une restauration automatique. La restauration automatique arrêtera la machine virtuelle dans Azure et démarrera la machine virtuelle locale. L’application sera donc indisponible quelques instants. Par conséquent, effectuez la restauration automatique lorsque votre application peut être arrêtée.

[Étapes d’initialisation de la restauration automatique de la machine virtuelle](site-recovery-how-to-failback-azure-to-vmware.md#steps-to-failback)

## <a name="common-issues"></a>Problèmes courants

* Si vos machines virtuelles ont été créées à l’aide d’un modèle, vous devez vous assurer que chaque machine virtuelle présente un UUID unique pour les disques avant de procéder à la reprotection. Si l’UUID de la machine virtuelle locale est en conflit avec celui du serveur cible maître (car ceux-ci ont tous deux été créés à partir du même modèle), la reprotection échouera. Vous devrez déployer un autre serveur cible maître qui n’a pas été créé à partir du même modèle.
* Si vous effectuez une découverte vCenter en mode lecture seule utilisateur et protégez des machines virtuelles, l’opération réussit et le basculement fonctionne. Au cours de la reprotection, le basculement échoue car les banques de données ne peuvent pas être détectées. Par conséquent, les banques de données ne seront pas répertoriées lors de la reprotection. Pour résoudre ce problème, mettez à jour les informations d’identification vCenter à l’aide du compte approprié disposant des autorisations requises, puis renouvelez l’opération. Pour en savoir plus, consultez l’article [Répliquer des machines virtuelles VMware et des serveurs physiques sur Azure avec Azure Site Recovery](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
* Lorsque vous restaurez automatiquement une machine virtuelle Linux et l’exécutez localement, vous constaterez que le package du Gestionnaire de réseau est désinstallé de l’ordinateur. En effet, celui-ci est supprimé une fois la machine virtuelle récupérée dans Azure.
* Lorsqu’une machine virtuelle Linux est configurée avec une adresse IP statique puis restaurée automatiquement vers Azure, l’adresse IP est obtenue via DHCP. Lorsque vous basculez à nouveau en local, la machine virtuelle continue d’utiliser DHCP pour obtenir l’adresse IP. Connectez-vous manuellement à l’ordinateur et redéfinissez l’adresse IP sur une adresse statique le cas échéant. Une machine virtuelle Windows peut récupérer son adresse IP statique.
* Si vous utilisez la version gratuite d’ESXi 5.5 ou de vSphere 6 Hypervisor, le basculement aboutira, mais pas la restauration automatique. Vous devrez effectuer une mise à niveau vers une licence d’évaluation d’un des programmes pour activer la restauration automatique.
* Si vous ne pouvez pas communiquer avec le serveur de configuration à partir du serveur de processus, vérifiez la connectivité au serveur de configuration via Telnet vers l’ordinateur du serveur de configuration sur le port 443. Vous pouvez également essayer d’exécuter un test ping sur le serveur de configuration à partir de l’ordinateur du serveur de processus. Un serveur de processus doit également avoir une pulsation lorsqu’il est connecté au serveur de configuration.
* Si vous tentez d’effectuer une restauration automatique vers un autre serveur vCenter, vérifiez que le nouveau serveur vCenter et le serveur cible maître sont détectés. Un symptôme courant est que les banques de données ne sont pas accessibles/visibles dans la boîte de dialogue **Reprotéger**.
* Une machine WS2008R2SP1 protégée comme machine physique locale ne peut pas être restaurée localement à partir d’Azure.

