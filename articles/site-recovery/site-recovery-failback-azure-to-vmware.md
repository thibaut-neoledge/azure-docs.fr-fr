---
title: "Restaurer automatiquement en local des machines virtuelles VMware à partir d’Azure | Microsoft Docs"
description: "Découvrez la restauration automatique sur le site local après le basculement des machines virtuelles VMware et des serveurs physiques vers Azure."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: mkjain
editor: 
ms.assetid: 5a47337f-89a9-43e8-8fdc-7da373c0fb0f
ms.service: site-recovery
ms.devlang: na
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: required
ms.date: 10/05/2016
ms.author: ruturajd
translationtype: Human Translation
ms.sourcegitcommit: cfe4957191ad5716f1086a1a332faf6a52406770
ms.openlocfilehash: ba801c69cf2d0d542bebf54e99ef981854284ab0
ms.lasthandoff: 03/09/2017


---
# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site"></a>Restaurer automatiquement des machines virtuelles VMware et des serveurs physiques sur le site local
> [!div class="op_single_selector"]
> * [Machines VMware / machines physiques d’Azure](site-recovery-failback-azure-to-vmware.md)
> * [Machines virtuelles Hyper-V d’Azure](site-recovery-failback-from-azure-to-hyper-v.md)

Cet article explique comment restaurer automatiquement des machines virtuelles Azure à partir d’Azure vers le site local. Suivez ces instructions lorsque vous êtes prêt à restaurer automatiquement vos machines virtuelles VMware ou vos serveurs physiques Windows ou Linux après avoir protégé à nouveau vos machines en suivant cette [référence](site-recovery-how-to-reprotect.md).

>[!NOTE]
>Si vous utilisez le Portail Azure Classic, reportez-vous aux instructions mentionnées [ici](site-recovery-failback-azure-to-vmware-classic.md) pour l’architecture VMware vers Azure améliorée et [ici](site-recovery-failback-azure-to-vmware-classic-legacy.md) pour l’architecture héritée.

## <a name="overview"></a>Vue d'ensemble
Le diagramme de cette section illustre l’architecture de restauration automatique correspondant à ce scénario.

Utilisez cette architecture lorsque le serveur de processus est local et que vous utilisez une connexion Azure ExpressRoute :

![Diagramme d’architecture pour ExpressRoute](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Utilisez cette architecture lorsque le serveur de processus est sur Azure et que vous disposez d’une connexion VPN ou ExpressRoute :

![Diagramme d’architecture pour VPN](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

Pour afficher la liste complète des ports et le diagramme de l’architecture de restauration automatique, reportez-vous à l’image suivante :

![Liste des ports de basculement-restauration automatique](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

Une fois que vous avez procédé au basculement vers Azure, vous pouvez effectuer une restauration automatique sur votre site local en trois étapes :

* **Étape 1** : reprotégez les machines virtuelles Azure afin qu’elles soient répliquées sur les machines virtuelles VMware qui s’exécutent sur votre site local.
* **Étape 2** : une fois que vos machines virtuelles Azure sont répliquées vers votre site local, exécutez un basculement pour procéder à la restauration automatique à partir d’Azure.
* **Étape 3**: une fois vos données restaurées automatiquement, reprotégez les machines virtuelles locales vers lesquelles vous avez procédé à la restauration automatique pour qu’elles soient répliquées vers Azure.

### <a name="fail-back-to-the-original-location-or-an-alternate-location"></a>Récupération automatique vers l’emplacement d’origine ou un autre emplacement
Après avoir effectué le basculement d’une machine virtuelle VMware, vous pouvez procéder à une restauration automatique vers la même machine virtuelle source si celle-ci existe toujours sur le site local. Dans ce scénario, seules les modifications delta sont restaurées automatiquement.

Si vous avez procédé au basculement de serveurs physiques, la restauration automatique est toujours effectuée vers une nouvelle machine virtuelle VMware. Avant d’effectuer la restauration automatique d’un ordinateur physique, notez les points suivants :
* Un ordinateur physique protégé reviendra comme un ordinateur virtuel après un basculement à partir d’Azure vers VMware. Une machine physique Windows Server 2008 R2 SP1 protégée et basculée vers Azure ne peut pas être restaurée. Une machine Windows Server 2008 R2 SP1 qui a démarré comme une machine virtuelle locale pourra être restaurée automatiquement.
* Veillez à découvrir au moins un serveur cible maître ainsi que les hôtes ESX/ESXi nécessaires sur lesquels vous devez effectuer une restauration automatique.

Si vous procédez à une restauration automatique vers la machine virtuelle d’origine, les conditions suivantes doivent être respectées :

* Si la machine virtuelle est gérée par un serveur vCenter, l’ordinateur hôte ESX du serveur cible maître doit avoir accès à la banque de données des machines virtuelles.
* Si la machine virtuelle se trouve sur un ordinateur hôte ESX, mais n’est pas gérée par vCenter, son disque dur doit se situer dans une banque de données accessible par l’ordinateur hôte du serveur cible maître.
* Si votre machine virtuelle se trouve sur un ordinateur hôte ESX et n’utilise pas vCenter, vous devez exécuter la détection de l’ordinateur hôte ESX du serveur cible maître avant d’assurer la reprotection. Cela s’applique également si vous restaurez automatiquement des serveurs physiques.
* Une autre option (si la machine virtuelle locale existe) consiste à la supprimer avant de procéder à une restauration automatique. L’opération de restauration automatique crée ensuite une machine virtuelle sur le même hôte que l’ordinateur hôte ESX cible maître.

Si vous effectuez la restauration automatique vers un autre emplacement, les données sont récupérées dans la même banque de données et sur le même ordinateur hôte ESX que ceux qui sont utilisés par le serveur cible maître local.

## <a name="prerequisites"></a>Composants requis
* Vous avez besoin d’un environnement VMware afin de restaurer automatiquement les machines virtuelles VMware et les serveurs physiques. La restauration automatique vers les serveurs physiques n’est pas prise en charge.
* Pour procéder à une restauration automatique, vous devez avoir créé un réseau Azure lors de la configuration initiale de la protection. La restauration automatique nécessite une connexion VPN ou ExpressRoute à partir du réseau Azure dans lequel les machines virtuelles Azure sont situées sur le site local.
* Si les machines virtuelles vers lesquelles vous voulez effectuer une restauration automatique sont gérées par un serveur vCenter, assurez-vous de disposer des autorisations requises pour la détection des machines virtuelles sur les serveurs vCenter. Pour en savoir plus, consultez l’article [Répliquer des machines virtuelles VMware et des serveurs physiques sur Azure avec Azure Site Recovery](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
* Si des instantanés sont présents sur une machine virtuelle, la reprotection échouera. Vous pouvez supprimer les instantanés ou les disques.
* Avant de procéder à la restauration automatique, créez les composants suivants :
  * **Créez un serveur de processus dans Azure**. Il s’agit d’une machine virtuelle Azure que vous créez et maintenez en cours d’exécution pendant la restauration automatique. Vous pouvez supprimer cette machine virtuelle à l’issue de la restauration automatique.
  * **Créez un serveur cible maître**. Le serveur cible maître envoie et reçoit des données de restauration automatique. Un serveur cible maître est installé par défaut sur le serveur d’administration que vous avez créé sur site. Toutefois, en fonction du volume de trafic restauré automatiquement, vous devrez peut-être créer un serveur cible maître distinct pour procéder à la restauration automatique.
  * Pour créer un autre serveur cible maître s’exécutant sur Linux, configurez la machine virtuelle Linux avant d’installer le serveur cible maître, comme décrit ci-dessous.
* Le serveur de configuration est requis en local lorsque vous effectuez une restauration automatique. Lors de la restauration automatique, la machine virtuelle doit exister dans la base de données du serveur de configuration. Si la base de données du serveur de configuration ne contient aucune machine virtuelle, la restauration automatique échouera. Vous devez donc veiller à planifier une sauvegarde régulière de votre serveur. En cas d’incident, vous devrez le restaurer avec la même adresse IP pour que la restauration automatique réussisse.
* Définissez le paramètre disk.enableUUID=true dans les **paramètres de configuration** de la machine virtuelle cible maître dans VMware. Si cette ligne n’existe pas, ajoutez-la. Ce paramètre est requis pour fournir un identificateur unique universel (UUID) cohérent au fichier de disque de machine virtuelle afin qu’il soit monté correctement.
* Soyez attentif à la condition « Le serveur cible maître ne peut pas être Storage vMotioned », qui peut faire échouer la restauration automatique. La machine virtuelle ne s’affichera pas car les disques ne sont pas mis à sa disposition.
* Ajoutez un lecteur, appelé lecteur de rétention, sur le serveur cible maître. Ajoutez un disque et formatez le lecteur.

## <a name="failback-policy"></a>Stratégie de restauration automatique
Pour effectuer une réplication en retour vers votre site local, vous avez besoin d’une stratégie de restauration automatique. La stratégie est automatiquement créée lorsque vous créez une stratégie dans le sens initial et est automatiquement associée au serveur de configuration. Elle n’est pas modifiable. La stratégie comprend les paramètres de réplication suivants :

* Seuil d’objectif de point de récupération : 15 minutes
* Rétention de point de récupération : 24 heures
* Fréquence des instantanés de cohérence au niveau application : 60 minutes

 ![Paramètres de réplication de la stratégie de restauration automatique](./media/site-recovery-failback-azure-to-vmware-new/failback-policy.png)

## <a name="set-up-a-process-server-in-azure"></a>Configurer un serveur de processus dans Azure
Installez un serveur de processus dans Azure pour que les machines virtuelles Azure puissent renvoyer les données vers un serveur cible maître local.

Si vous avez protégé vos machines en tant que ressources classiques (la machine virtuelle récupérée dans Azure est une machine virtuelle créée à l’aide du modèle de déploiement classique), vous aurez besoin d’un serveur de processus dans Azure. Si vous avez récupéré les machines virtuelles en définissant le type de déploiement sur Azure Resource Manager, le type de déploiement du serveur de processus doit également être défini sur Resource Manager. Le type de déploiement est sélectionné par le réseau virtuel Azure dans lequel vous déployez le serveur de processus.

1. Dans **Coffre** > **Paramètres** > **Infrastructure Site Recovery** (sous **Gérer**) > **Serveurs de configuration** (sous **Pour VMware et les machines physiques**), sélectionnez le serveur de configuration.
2. Cliquez sur **Serveur de processus**.

  ![Bouton Serveur de processus](./media/site-recovery-failback-azure-to-vmware-classic/add-processserver.png)
3. Pour le déploiement du serveur de processus, choisissez **Déployer un serveur de processus de restauration automatique dans Azure**.
4. Sélectionnez l’abonnement dans lequel vous avez récupéré les machines virtuelles.
5. Sélectionnez le réseau Azure dans lequel vous avez récupéré les machines virtuelles. Le serveur de processus doit se trouver dans le même réseau, de sorte que les machines virtuelles récupérées et le serveur de processus puissent communiquer.
6. Si vous avez sélectionné un réseau créé selon un *modèle de déploiement classique*, créez une machine virtuelle via la place de marché Azure, puis installez-y le serveur de processus.

 ![Fenêtre Ajouter un serveur de processus](./media/site-recovery-failback-azure-to-vmware-classic/add-classic.png)

 Lorsque vous créez le serveur de processus, faites attention à ce qui suit :
 * Le nom de l’image est *Microsoft Azure Site Recovery Process Server V2*. Sélectionnez le modèle de déploiement **Classique**.

       ![Select "Classic" as the Process Server deployment model](./media/site-recovery-failback-azure-to-vmware-classic/templatename.png)
 * Installez le serveur de processus en suivant les instructions de la rubrique [Répliquer des machines virtuelles VMware et des serveurs physiques sur Azure avec Azure Site Recovery](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server).
7. Si vous sélectionnez le réseau Azure *Resource Manager*, déployez le serveur de processus en fournissant les informations suivantes :

  * Le nom du groupe de ressources dans lequel vous souhaitez déployer le serveur
  * Le nom du serveur
  * Un nom d’utilisateur et un mot de passe pour la connexion au serveur
  * Le compte de stockage dans lequel vous souhaitez déployer le serveur
  * Le sous-réseau et l’interface réseau pour la connexion au serveur
   >[!NOTE]
   >Vous devez créer votre propre [interface réseau](../virtual-network/virtual-networks-multiple-nics.md) et la sélectionner lors du déploiement du serveur de processus.

    ![Saisir les informations dans la boîte de dialogue « Ajouter un serveur de processus »](./media/site-recovery-failback-azure-to-vmware-classic/psinputsadd.png)

8. Cliquez sur **OK**. Cela déclenche une tâche qui crée une machine virtuelle selon le type de déploiement Resource Manager lors de l’installation du serveur de processus. Pour enregistrer le serveur sur le serveur de configuration, exécutez l’installation au sein de la machine virtuelle en suivant les instructions de la rubrique [Répliquer des machines virtuelles VMware et des serveurs physiques sur Azure avec Azure Site Recovery](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server). Une tâche de déploiement du serveur de processus est également déclenchée.

  Le serveur de processus s’affiche dans l’onglet **Serveurs de configuration** > **Serveurs associés** > **Serveurs de processus**.

    ![](./media/site-recovery-failback-azure-to-vmware-new/pslistingincs.png)

    > [!NOTE]
    > Le serveur de processus ne s’affiche pas sous **Propriétés de la machine virtuelle**. Il s’affiche uniquement dans l’onglet **Serveurs** du serveur d’administration dans lequel il est enregistré. L’affichage du serveur de processus peut prendre environ 10 à 15 minutes.


## <a name="set-up-the-master-target-server-on-premises"></a>Configurer le serveur cible maître local
Le serveur cible maître reçoit les données de restauration automatique. Le serveur est automatiquement installé sur le serveur d’administration local, mais si vous effectuez une restauration automatique d’un trop grand nombre de données, il se peut que vous deviez configurer un serveur cible maître supplémentaire. Pour configurer un serveur cible maître local, procédez comme suit :

> [!NOTE]
> Pour configurer un serveur cible maître sous Linux, passez à la procédure suivante. Utilisez uniquement le système d’exploitation CentOS 6.6 Minimal comme système d’exploitation du serveur cible maître.

1. Si vous configurez le serveur cible maître sous Windows, ouvrez la page de démarrage rapide de la machine virtuelle sur laquelle vous installez le serveur cible maître.
2. Téléchargez le fichier d’installation pour le programme d’installation unifiée d’Azure Site Recovery.
3. Exécutez le programme d’installation puis, dans la zone **Avant de commencer**, sélectionnez **Ajouter des serveurs de processus supplémentaires pour augmenter la taille du déploiement**.
4. Terminez l’Assistant de la même façon que lors de la [configuration du serveur d’administration](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server). Sur la page **Détails du serveur de configuration**, spécifiez l’adresse IP du serveur cible maître ainsi qu’une phrase secrète pour accéder à la machine virtuelle.

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>Configurer une machine virtuelle Linux en tant que serveur cible maître
Pour configurer le serveur d’administration exécutant le serveur cible maître comme une machine virtuelle Linux, installez le système d’exploitation CentOS 6.6 Minimal. Ensuite, récupérez les ID SCSI pour chaque disque dur SCSI, installez des packages supplémentaires et appliquez des modifications personnalisées.

#### <a name="install-centos-66"></a>Installer CentOS 6.6

1. Installez le système d’exploitation minimal CentOS 6.6 sur la machine virtuelle du serveur d’administration. Conservez l’image ISO dans un lecteur de DVD et démarrez le système. Ignorez les tests de média. Sélectionnez la langue **US English** (Anglais US) et **Appareils de stockage de base**, vérifiez que le disque dur ne comporte pas de données importantes et cliquez sur **Oui** pour supprimer toutes les données. Entrez le nom d’hôte du serveur d’administration et sélectionnez la carte réseau du serveur.  Dans la boîte de dialogue **Système de modification**, sélectionnez **Connecter automatiquement**, puis ajoutez une adresse IP statique, un réseau et des paramètres DNS. Spécifiez un fuseau horaire. Pour accéder au serveur d’administration, entrez le mot de passe racine.
2. Quand le système vous demande le type d’installation souhaitée, sélectionnez **Créer une disposition personnalisée** comme partition. Cliquez sur **Next**. Sélectionnez **Free** (Libre), puis cliquez sur **Créer**. Créez les partitions **/**,  **/var/crash** et **/home** avec **FS Type:** **ext4**. Créez la partition d’échange **FS Type: swap**.
3. Si des appareils préexistants sont trouvés, un message d’avertissement s’affiche. Cliquez sur **Format** pour formater le lecteur avec les paramètres de partition. Cliquez sur **Enregistrer les modifications sur le disque** pour appliquer les modifications de partition.
4. Sélectionnez **Installer le chargeur de démarrage** > **Suivant** pour installer le chargeur de démarrage dans la partition racine.
5. Une fois l’installation terminée, cliquez sur **Redémarrer**.

#### <a name="retrieve-the-scsi-ids"></a>Récupérer les ID SCSI

1. À l’issue de l’installation, récupérez les ID SCSI de chaque disque dur SCSI de la machine virtuelle. Pour ce faire, arrêtez la machine virtuelle du serveur d’administration. Dans les propriétés de la machine virtuelle dans VMware, cliquez avec le bouton droit sur l’entrée de la machine virtuelle > **Modifier les paramètres** > **Options**.
2. Sélectionnez **Avancé** > **Général**, puis cliquez sur **Paramètres de configuration**. Cette option n’est pas disponible lorsque la machine est en cours d’exécution. Pour que l’option soit disponible, vous devez arrêter la machine.
3. Effectuez l’une des actions suivantes :
 * Si la ligne **disk.EnableUUID** existe, vérifiez que la valeur est définie sur **True** (Vrai) (la valeur est sensible à la casse). Si c’est déjà le cas, vous pouvez annuler et tester la commande SCSI dans un système d’exploitation invité après son démarrage.
 * Si la ligne **disk.EnableUUID** n’existe pas, cliquez sur **Ajouter une ligne** et ajoutez-la avec la valeur **True** (Vrai). N’utilisez pas de guillemets doubles.

#### <a name="install-additional-packages"></a>Installer des packages supplémentaires
Téléchargez et installez des packages supplémentaires.

1. Vérifiez que le serveur cible maître est connecté à Internet.
2. Pour télécharger et installer 15 packages depuis le référentiel CentOS, exécutez la commande suivante : `# yum install –y xfsprogs perl lsscsi rsync wget kexec-tools`.
3. Si les machines sources que vous protégez exécutent Linux avec le système de fichiers Reiser ou XFS pour l’appareil racine ou de démarrage, vous devez télécharger et installer des packages supplémentaires comme suit :

   * \# cd /usr/local
   * \# wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
   * \# wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
   * \# rpm –ivh kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm
   * \# wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
   * \# rpm –ivh xfsprogs-3.1.1-16.el6.x86_64.rpm
   * \# yum install device-mapper-multipath (requis pour activer les packages à plusieurs chemins sur le serveur cible maître)

#### <a name="apply-custom-changes"></a>Appliquer des modifications personnalisées
Pour appliquer des modifications personnalisées après avoir effectué les étapes de post-installation et installé les packages, procédez comme suit :

1. Copiez le binaire de l’agent unifié RHEL (Red Hat Enterprise Linux) 6-64 sur la machine virtuelle. Pour décompresser le fichier binaire, exécutez la commande suivante : `tar –zxvf <file name>`.
2. Pour accorder des autorisations, exécutez la commande suivante : `# chmod 755 ./ApplyCustomChanges.sh`.
3. Exécutez le script suivant : `# ./ApplyCustomChanges.sh`. Exécutez-le une seule fois. Redémarrez le serveur après l’exécution du script.

## <a name="run-the-failback"></a>Exécuter la restauration automatique
### <a name="reprotect-the-azure-vms"></a>Reprotéger les machines virtuelles Azure
1. Dans **Coffre**, sous **Éléments répliqués**, cliquez avec le bouton droit sur la machine virtuelle ayant basculé et sélectionnez **Reprotéger**.
2. Dans le panneau, vous pouvez voir que la direction de protection **d’Azure à local** est déjà sélectionnée.
3. Dans les champs **Serveur cible maître** et **Serveur de processus**, sélectionnez le serveur cible maître local et le serveur de processus de machine virtuelle Azure.
4. Sélectionnez la banque de données dans laquelle vous souhaitez récupérer les disques locaux. Utilisez cette option lorsque la machine virtuelle locale est supprimée et que vous devez créer de nouveaux disques. Ignorez cette option si les disques existent déjà, mais que vous devez toujours spécifier une valeur.
5. Utilisez un lecteur de rétention pour arrêter les points dans le temps lorsque la machine virtuelle est répliquée vers le site local. Vous trouverez ci-après quelques critères d’un lecteur de rétention. Sans ces critères, le lecteur ne s’affiche pas pour le serveur cible maître.

  * Le volume ne doit pas être utilisé à d’autres fins (cible de réplication, etc.).
  * Le volume ne doit pas être en mode de verrouillage.
  * Le volume ne doit pas être un volume de cache. (Aucune installation de serveur maître cible ne doit exister sur ce volume. Un volume d’installation personnalisée serveur de processus+serveur cible maître n’est pas éligible comme volume de rétention. Ici, le volume serveur de processus+serveur cible maître installé est le volume de cache du serveur cible maître.)
  * Le type de système de fichiers du volume ne doit pas être FAT ou FAT32.
  * La capacité du volume ne doit pas être nulle.
  * Le volume de rétention par défaut pour Windows est le volume R.
  * Le volume de rétention par défaut pour Linux est /mnt/retention.

6. La stratégie de restauration automatique est sélectionnée automatiquement.
7. Une fois que vous avez cliqué sur **OK** pour démarrer la reprotection, une tâche est lancée pour répliquer la machine virtuelle à partir d’Azure vers le site local. Vous pouvez en suivre la progression sous l’onglet **Travaux** .

Si vous souhaitez procéder à la récupération vers un autre emplacement, sélectionnez le lecteur de rétention et la banque de données configurés pour le serveur cible maître. Si vous effectuez la restauration automatique vers le site local, les machines virtuelles VMware du plan de protection de la restauration automatique utilisent la même banque de données que le serveur cible maître. Si vous voulez récupérer la machine virtuelle Azure de réplica vers la même machine virtuelle locale, celle-ci doit déjà figurer dans la même banque de données que le serveur cible maître. En l’absence de machine virtuelle locale, une nouvelle machine virtuelle de ce type est créée pendant la reprotection.

![Sélectionner « Azure vers site local » dans le menu déroulant](./media/site-recovery-failback-azure-to-vmware-new/reprotectinputs.png)

Vous pouvez également effectuer la reprotection au niveau du plan de récupération. Si vous avez un groupe de réplication, il peut être reprotégé uniquement à l’aide d’un plan de récupération. Lorsque vous restaurez la protection à l’aide d’un plan de récupération, utilisez les valeurs précédentes pour chaque machine protégée.

> [!NOTE]
> Les groupes de réplication doivent être reprotégés à l’aide du même serveur cible maître. Si ce n’est pas le cas, aucun point dans le temps commun ne pourra être défini pour ces groupes.

### <a name="run-a-failover-to-the-on-premises-site"></a>Exécuter un basculement vers le site local
Après la reprotection de la machine virtuelle, vous pouvez initier un basculement à partir d’Azure vers le site local.

1. Sur la page Éléments répliqués, cliquez avec le bouton droit sur la machine virtuelle, puis sélectionnez **Basculement non planifié**.
2. Dans **Confirmer le basculement**, vérifiez le sens du basculement (à partir d’Azure) et sélectionnez le point de récupération à utiliser pour le basculement (le dernier point ou le dernier point de cohérence au niveau application). Un point de récupération de cohérence au niveau application se produit avant le dernier point dans le temps et entraîne une perte de données.
3. Site Recovery arrête les machines virtuelles Azure pendant le basculement. Une fois que vous avez vérifié que la restauration automatique s’est terminée comme prévu, vous pouvez vous assurer que les machines virtuelles Azure ont été arrêtées correctement.

### <a name="reprotect-the-on-premises-site"></a>Reprotéger le site local
Une fois la restauration automatique terminée, validez la machine virtuelle pour garantir la suppression des machines virtuelles récupérées dans Azure. Pour ce faire, cliquez avec le bouton droit sur l’élément protégé, puis cliquez sur **Valider**. Ceci déclenche une tâche qui supprimera les anciennes machines virtuelles récupérées dans Azure.

Une fois la validation terminée, vos données devraient se trouver à nouveau sur le site local, sans toutefois être protégées. Pour démarrer une nouvelle réplication vers Azure, procédez comme suit :

1. Dans **Coffre**, sous **Paramètres** > **Éléments répliqués**, sélectionnez les machines virtuelles qui ont été restaurées automatiquement, puis cliquez sur **Reprotéger**.
2. Affectez la valeur du serveur de processus à utiliser pour renvoyer les données vers Azure.
3. Cliquez sur **OK**.

Une fois la reprotection effectuée, la machine virtuelle est répliquée vers Azure et vous pouvez effectuer un basculement.

### <a name="resolve-common-failback-issues"></a>Résoudre les problèmes courants lors de la restauration automatique
* Si vous effectuez une découverte vCenter en mode lecture seule utilisateur et protégez des machines virtuelles, l’opération réussit et le basculement fonctionne. Au cours de la reprotection, le basculement échoue car les banques de données ne peuvent pas être détectées. Par conséquent, les banques de données ne seront pas répertoriées lors de la reprotection. Pour résoudre ce problème, mettez à jour les informations d’identification vCenter à l’aide du compte approprié disposant des autorisations requises, puis renouvelez l’opération. Pour en savoir plus, consultez l’article [Répliquer des machines virtuelles VMware et des serveurs physiques sur Azure avec Azure Site Recovery](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
* Lorsque vous restaurez automatiquement une machine virtuelle Linux et l’exécutez localement, vous constaterez que le package du Gestionnaire de réseau est désinstallé de l’ordinateur. En effet, celui-ci est supprimé une fois la machine virtuelle récupérée dans Azure.
* Lorsqu’une machine virtuelle est configurée avec une adresse IP statique puis restaurée automatiquement vers Azure, l’adresse IP est obtenue via DHCP. Lorsque vous basculez à nouveau en local, la machine virtuelle continue d’utiliser DHCP pour obtenir l’adresse IP. Connectez-vous manuellement à l’ordinateur et redéfinissez l’adresse IP sur une adresse statique le cas échéant.
* Si vous utilisez la version gratuite d’ESXi 5.5 ou de vSphere 6 Hypervisor, le basculement aboutira, mais pas la restauration automatique. Vous devrez effectuer une mise à niveau vers une licence d’évaluation d’un des programmes pour activer la restauration automatique.
* Si vous ne pouvez pas communiquer avec le serveur de configuration à partir du serveur de processus, vérifiez la connectivité au serveur de configuration via Telnet vers l’ordinateur du serveur de configuration sur le port 443. Vous pouvez également essayer d’exécuter un test ping sur le serveur de configuration à partir de l’ordinateur du serveur de processus. Un serveur de processus doit également avoir une pulsation lorsqu’il est connecté au serveur de configuration.
* Si vous tentez d’effectuer une restauration automatique vers un autre serveur vCenter, vérifiez que le nouveau serveur vCenter et le serveur cible maître sont détectés. Un symptôme courant est que les banques de données ne sont pas accessibles/visibles dans la boîte de dialogue **Reprotéger**.
* Une machine WS2008R2SP1 protégée comme machine physique locale ne peut pas être restaurée localement à partir d’Azure.

## <a name="fail-back-with-expressroute"></a>Effectuer une restauration automatique avec ExpressRoute
Vous pouvez effectuer une restauration automatique à l’aide d’une connexion VPN ou ExpressRoute. Si vous souhaitez utiliser une connexion ExpressRoute, notez les points suivants :

* La connexion ExpressRoute doit être configurée sur le réseau virtuel Azure vers lequel les machines sources basculent, et sur lequel les machines virtuelles Azure sont hébergées après le basculement.
* Les données sont répliquées vers un compte de stockage Azure sur un point de terminaison public. Pour utiliser une connexion ExpressRoute, vous devez configurer une homologation publique dans ExpressRoute avec le centre de données cible pour la réplication Site Recovery.

