---
title: "Restaurer automatiquement des machines virtuelles VMware à partir d’Azure dans le portail Classic | Microsoft Docs"
description: "Découvrez la restauration automatique sur le site local après le basculement des machines virtuelles VMware et des serveurs physiques vers Azure."
services: site-recovery
documentationcenter: 
author: ruturaj
manager: mkjain
editor: 
ms.assetid: 7ca86e21-7a5d-45ab-8f4b-e42da90f199a
ms.service: site-recovery
ms.devlang: na
ms.tgt_pltfrm: na
ms.topic: article
ms.workload: storage-backup-recovery
ms.date: 06/05/2017
ms.author: ruturajd
ms.translationtype: Human Translation
ms.sourcegitcommit: 75653b84d6ccbefe7d5230449bea81f498e10a98
ms.openlocfilehash: a898cb9c66a22bb8d19170fdd03d0fc3b4d93000
ms.contentlocale: fr-fr
ms.lasthandoff: 01/30/2017


---
# <a name="fail-back-vmware-virtual-machines-and-physical-servers-to-the-on-premises-site-classic-portal"></a>Restaurer automatiquement des serveurs physiques et des machines virtuelles VMware sur le site local (portail Classic)
> [!div class="op_single_selector"]
> * [portail Azure](site-recovery-failback-azure-to-vmware.md)
> * [Portail Azure Classic](site-recovery-failback-azure-to-vmware-classic.md)
> * [Portail Azure Classic (version héritée)](site-recovery-failback-azure-to-vmware-classic-legacy.md)
>
>

Cet article explique comment restaurer automatiquement des machines virtuelles Azure d’Azure vers le site local. Suivez les instructions qu’il contient lorsque vous êtes prêt à restaurer automatiquement vos machines virtuelles VMware ou vos serveurs physiques Windows/Linux après leur basculement du site local vers Azure en suivant ce [didacticiel](site-recovery-vmware-to-azure-classic.md).

## <a name="overview"></a>Vue d'ensemble
Ce diagramme illustre l’architecture de restauration automatique correspondant à ce scénario.

Utilisez cette architecture lorsque le serveur de processus est local et que vous utilisez ExpressRoute.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture.png)

Utilisez cette architecture lorsque le serveur de processus est sur Azure et que vous disposez d’une connexion VPN ou ExpressRoute.

![](./media/site-recovery-failback-azure-to-vmware-classic/architecture2.png)

Pour afficher la liste complète des ports et le diagramme de l’architecture de restauration automatique, reportez-vous à l'image ci-dessous

![](./media/site-recovery-failback-azure-to-vmware-classic/Failover-Failback.png)

Voici comment fonctionne la restauration automatique :

* Une fois que vous avez procédé au basculement vers Azure, vous pouvez effectuer une restauration automatique sur votre site local en quelques étapes :
  * **Étape 1**: reprotégez les machines virtuelles Azure afin qu’elles soient répliquées sur les machines virtuelles VMware qui s’exécutent sur votre site local. L’activation de la reprotection déplace les machines virtuelles dans un groupe de protection de la restauration automatique qui a été créé automatiquement lorsque vous avez créé initialement le groupe de protection du basculement. Si vous avez ajouté votre groupe de protection du basculement à un plan de récupération, le groupe de protection de la restauration automatique est ajouté automatiquement au plan.  Pendant la reprotection, vous devez spécifier la planification de la restauration automatique.
  * **Étape 2**: une fois que vos machines virtuelles Azure sont répliquées vers votre site local, exécutez un basculement pour procéder à la restauration automatique à partir d’Azure.
  * **Étape 3**: une fois vos données restaurées automatiquement, reprotégez les machines virtuelles locales vers lesquelles vous avez procédé à la restauration automatique pour qu’elles soient répliquées vers Azure.


  [!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Failback/player]

### <a name="failback-to-the-original-or-alternate-location"></a>Restaurer automatiquement vers l’emplacement d’origine ou vers un autre emplacement
Si vous avez effectué le basculement d’une machine virtuelle VMware, vous pouvez procéder à une restauration automatique vers la même machine virtuelle source si elle existe toujours sur site. Dans ce scénario, seules les modifications delta sont restaurées automatiquement. Notez les points suivants :

* Si vous avez procédé au basculement de serveurs physiques, la restauration automatique est toujours effectuée vers une nouvelle machine virtuelle VMware.
  * Avant d’effectuer la restauration automatique d’un ordinateur physique, notez les points suivants :
  * L’ordinateur physique protégé reviendra comme un ordinateur virtuel après un basculement à partir d'Azure vers VMware
  * Veillez à découvrir au moins un serveur maître cible ainsi que les hôtes ESX/ESXi nécessaires sur lesquels vous devez effectuer une restauration automatique.
* Si vous procédez à une restauration automatique vers la machine virtuelle d’origine, les conditions suivantes doivent être respectées :

  * Si la machine virtuelle est gérée par un serveur vCenter, l’ordinateur hôte ESX du serveur maître cible doit avoir accès à la banque de données des machines virtuelles.
  * Si la machine virtuelle se trouve sur un ordinateur hôte ESX, mais n’est pas gérée par vCenter, son disque dur doit se situer dans une banque de données accessible par l’ordinateur hôte du serveur maître cible.
  * Si votre machine virtuelle se trouve sur un ordinateur hôte ESX et n’utilise pas vCenter, vous devez exécuter la détection de l’ordinateur hôte ESX du serveur maître cible avant d’assurer la reprotection. Cela s’applique également si vous restaurez automatiquement des serveurs physiques.
  * Une autre option (si la machine virtuelle locale existe) consiste à la supprimer avant de procéder à une restauration automatique. La restauration automatique crée ensuite une machine virtuelle sur le même hôte que l’ordinateur hôte ESX cible maître.
* Si vous effectuez la restauration automatique vers un autre emplacement, les données sont récupérées dans la même banque de données et sur le même ordinateur hôte ESX que ceux qui sont utilisés par le serveur cible maître local.

## <a name="prerequisites"></a>Conditions préalables
* Vous avez besoin d’un environnement VMware afin de restaurer automatiquement les machines virtuelles VMware et les serveurs physiques. La restauration automatique vers les serveurs physiques n’est pas prise en charge.
* Pour procéder à une restauration automatique, vous devez avoir créé un réseau Azure lors de la configuration initiale de la protection. La restauration automatique nécessite une connexion VPN ou ExpressRoute à partir du réseau Azure dans lequel les machines virtuelles Azure sont situées sur le site local.
* Si les machines virtuelles vers lesquelles vous voulez effectuer une restauration automatique sont gérées par un serveur vCenter, vous devez vous assurer de disposer des autorisations requises pour la détection des machines virtuelles sur les serveurs vCenter. [En savoir plus](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access).
* Si des instantanés sont présents sur une machine virtuelle, la reprotection échoue. Vous pouvez supprimer les instantanés ou les disques.
* Avant de procéder à une restauration automatique, vous devez créer un certain nombre de composants :
  * **Créez un serveur de processus dans Azure**. Il s’agit d’une machine virtuelle Azure que vous devez créer et maintenir en cours d’exécution pendant la restauration automatique. Vous pouvez supprimer cette machine à l’issue de la restauration automatique.
  * **Créez un serveur cible maître**. Le serveur cible maître envoie et reçoit des données de restauration automatique. Un serveur cible maître est installé par défaut sur le serveur d’administration que vous avez créé sur site. Toutefois, en fonction du volume de trafic restauré automatiquement, vous devrez peut-être créer un serveur cible maître distinct pour procéder à la restauration automatique.
  * Si vous souhaitez créer un autre serveur cible maître s’exécutant sur Linux, vous devez configurer la machine virtuelle Linux avant d’installer le serveur cible maître, comme décrit ci-dessous.
* Le serveur de configuration est requis en local lorsque vous effectuez une restauration automatique. Lors de la restauration automatique, la machine virtuelle doit exister dans la base de données du serveur de configuration. Sinon, la restauration automatique échouera. Vous devez donc veiller à planifier une sauvegarde régulière de votre serveur. En cas d'incident, vous devrez le restaurer avec la même adresse IP pour que la restauration automatique réussisse.

## <a name="set-up-the-process-server-in-azure"></a>Configurer le serveur de processus dans Azure
Vous devez installer un serveur de processus dans Azure pour que les machines virtuelles Azure puissent renvoyer les données vers un serveur cible maître local.

1. Dans le portail Site Recovery > **Serveurs de configuration**, effectuez votre sélection pour ajouter un nouveau serveur de processus.

   ![](./media/site-recovery-failback-azure-to-vmware-classic/ps1.png)
2. Indiquez un nom de serveur de processus, puis entrez le nom et le mot de passe que vous allez utiliser pour vous connecter à la machine virtuelle Azure en tant qu’administrateur. Dans **Serveur de configuration** , sélectionnez le serveur d’administration local et indiquez le réseau Azure dans lequel le serveur de processus doit être déployé. Il doit s’agir du réseau dans lequel se trouvent les machines virtuelles Azure. Indiquez une adresse IP unique à partir du sous-réseau select et commencez le déploiement.

   ![](./media/site-recovery-failback-azure-to-vmware-classic/ps2.png)

   Une tâche de déploiement du serveur de processus est alors déclenchée.

   ![](./media/site-recovery-failback-azure-to-vmware-classic/ps3.png)

   Une fois le serveur de processus déployé dans Azure, vous pouvez vous y connecter à l’aide des informations d’identification que vous avez spécifiées. Une boîte de dialogue de serveur de processus s’ouvre la première fois que vous vous connectez. Tapez l’adresse IP du serveur d’administration local ainsi que sa phrase secrète. Conservez le paramètre par défaut du port 443. Vous pouvez également laisser le port 9443 par défaut pour la réplication des données, sauf si vous avez modifié spécifiquement ce paramètre lors de la configuration du serveur d’administration local.

   > [!NOTE]
   > Le serveur n’apparaît pas dans **Propriétés de machine virtuelle**. Il n’est visible que dans l’onglet **Serveurs** du serveur d’administration auprès duquel il est inscrit. L’affichage du serveur de processus peut prendre environ 10 à 15 minutes.
   >
   >

## <a name="set-up-the-master-target-server-on-premises"></a>Configurer le serveur cible maître local
Le serveur cible maître reçoit les données de restauration automatique. Un serveur cible maître est automatiquement installé sur le serveur d’administration local, mais si vous effectuez la restauration automatique de très nombreuses données, il se peut que vous deviez configurer un serveur cible maître supplémentaire. Procédez comme suit :

> [!NOTE]
> Si vous voulez installer un serveur cible maître sur Linux, suivez les instructions de la procédure suivante.
>
>

1. Si vous installez le serveur cible maître sur Windows, ouvrez la page Démarrage rapide à partir de la machine virtuelle sur laquelle vous installez le serveur cible maître, puis téléchargez le fichier d’installation de l’Assistant Installation unifiée d’Azure Site Recovery.
2. Exécutez le programme d’installation puis, dans la zone **Avant de commencer**, sélectionnez **Ajouter des serveurs de traitement supplémentaires pour effectuer un déploiement avec montée en puissance**.
3. Terminez l’Assistant de la même façon que lors de la [configuration du serveur d’administration](site-recovery-vmware-to-azure-classic.md#step-5-install-the-management-server). Sur la page **Détails du serveur de configuration** , spécifiez l’adresse IP de ce serveur cible maître ainsi qu’une phrase secrète pour accéder à la machine virtuelle.

### <a name="set-up-a-linux-vm-as-the-master-target-server"></a>Configurer une machine virtuelle Linux en tant que serveur cible maître
Pour configurer le serveur d’administration exécutant le serveur cible maître en tant que machine virtuelle Linux, vous devez installer le système d’exploitation minimal CentOS 6.6, récupérer les ID SCSI de chaque disque dur SCSI, installer des packages supplémentaires et appliquer des modifications personnalisées.

#### <a name="install-centos-66"></a>Installer CentOS 6.6
1. Installez le système d’exploitation minimal CentOS 6.6 sur la machine virtuelle du serveur d’administration. Conservez l’image ISO dans un lecteur de DVD et démarrez le système. Ignorez les tests de média, sélectionnez la langue US English (Anglais US) et **Appareils de stockage de base**, vérifiez que le disque dur ne comporte pas de données importantes et cliquez sur **Oui, supprimer toutes les données**. Entrez le nom d’hôte du serveur d’administration et sélectionnez la carte réseau du serveur.  Dans la boîte de dialogue **Système de modification**, sélectionnez **Connecter automatiquement**, puis ajoutez une adresse IP statique, un réseau et des paramètres DNS. Spécifiez un fuseau horaire et un mot de passe racine pour accéder au serveur d’administration.
2. Quand le système vous demande le type d’installation souhaitée, sélectionnez **Créer une disposition personnalisée** comme partition. Après avoir cliqué sur **Suivant** select **Libre** et cliquez sur Créer. Créez les partitions **/**,  **/var/crash** et **/home** avec **FS Type:** **ext4**. Créez la partition d’échange **FS Type: swap**.
3. Si des appareils préexistants sont trouvés, un message d’avertissement s’affiche. Cliquez sur **Format** pour formater le lecteur avec les paramètres de partition. Cliquez sur **Enregistrer les modifications sur le disque** pour appliquer les modifications de partition.
4. Sélectionnez **Installer le chargeur de démarrage** > **Suivant** pour installer le chargeur de démarrage dans la partition racine.
5. Une fois l’installation terminée, cliquez sur **Redémarrer**.

#### <a name="retrieve-the-scsi-ids"></a>Récupérer les ID SCSI
1. À l’issue de l’installation, récupérez les ID SCSI de chaque disque dur SCSI de la machine virtuelle. Pour ce faire, arrêtez la machine virtuelle du serveur d’administration puis, dans les propriétés de la machine virtuelle dans VMware, cliquez avec le bouton droit sur l’entrée de la machine virtuelle > **Modifier les paramètres** > **Options**.
2. Sélectionnez **Avancé** > **Général**, puis cliquez sur **Paramètres de configuration**. Cette option est désactivée lorsque la machine est en cours d’exécution. Pour l’activer, vous devez arrêter la machine.
3. Si la ligne **disk.EnableUUID** existe, vérifiez que la valeur est définie sur **True** (Vrai) (la valeur est sensible à la casse). Si c’est déjà le cas, vous pouvez annuler et tester la commande SCSI dans un système d’exploitation invité après son démarrage.
4. Si la ligne n’existe pas, cliquez sur **Ajouter une ligne** et ajoutez-la avec la valeur **True** (Vrai). N’utilisez pas de guillemets doubles.

#### <a name="install-additional-packages"></a>Installer des packages supplémentaires
Vous devez télécharger et installer des packages supplémentaires.

1. Vérifiez que le serveur cible maître est connecté à Internet.
2. Exécutez la commande suivante pour télécharger et installer 15 packages à partir du dépôt CentOS : **# yum install –y xfsprogs perl lsscsi rsync wget kexec-tools**.
3. Si les machines sources que vous protégez exécutent Linux avec le système de fichiers Reiser ou XFS pour l’appareil racine ou de démarrage, vous devez télécharger et installer des packages supplémentaires comme suit :

   * # <a name="cd-usrlocal"></a>cd /usr/local
   * # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmskmod-reiserfs-00-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm)
   * # <a name="wget-httpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpmhttpelrepoorglinuxelrepoel6x8664rpmsreiserfs-utils-3621-1el6elrepox8664rpm"></a>wget [http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm](http://elrepo.org/linux/elrepo/el6/x86_64/RPMS/reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm)
   * # <a name="rpm-ivh-kmod-reiserfs-00-1el6elrepox8664rpm-reiserfs-utils-3621-1el6elrepox8664rpm"></a>rpm –ivh kmod-reiserfs-0.0-1.el6.elrepo.x86_64.rpm reiserfs-utils-3.6.21-1.el6.elrepo.x86_64.rpm
   * # <a name="wget-httpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpmhttpmirrorcentosorgcentos66osx8664packagesxfsprogs-311-16el6x8665rpm"></a>wget [http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm](http://mirror.centos.org/centos/6.6/os/x86_64/Packages/xfsprogs-3.1.1-16.el6.x86_65.rpm)
   * # <a name="rpm-ivh-xfsprogs-311-16el6x8664rpm"></a>rpm –ivh xfsprogs-3.1.1-16.el6.x86_64.rpm

#### <a name="apply-custom-changes"></a>Appliquer des modifications personnalisées
Pour appliquer des modifications personnalisées après avoir effectué les étapes de post-installation et installé les packages, procédez comme suit :

1. Copiez le binaire de l’agent unifié RHEL (Red Hat Enterprise Linux) 6-64 sur la machine virtuelle. Exécutez cette commande pour décompresser le fichier binaire : **tar –zxvf <file name>**
2. Exécutez cette commande pour accorder des autorisations : **# chmod 755 ./ApplyCustomChanges.sh**
3. Exécutez le script : **#./ApplyCustomChanges.sh**. Vous ne devez exécuter le script qu’une seule fois. Redémarrez le serveur après l’exécution du script.

## <a name="run-the-failback"></a>Exécuter la restauration automatique
### <a name="reprotect-the-azure-vms"></a>Reprotéger les machines virtuelles Azure
1. Dans le portail Site Recovery > onglet **Machines**, sélectionnez la machine virtuelle qui a été basculée, puis cliquez sur **Reprotéger**.
2. Dans les champs **Serveur cible maître** et **Serveur de traitement**, sélectionnez le serveur cible maître local et le serveur de traitement de machine virtuelle Azure.
3. Sélectionnez le compte que vous avez configuré pour la connexion à la machine virtuelle.
4. Sélectionnez la version de restauration automatique du groupe de protection. Par exemple, si la machine virtuelle est protégée dans le groupe de protection PG1, vous devez sélectionner PG1_Failback.
5. Si vous souhaitez procéder à la récupération vers un autre emplacement, sélectionnez le lecteur de rétention et la banque de données configurés pour le serveur cible maître. Si vous effectuez la restauration automatique vers le site local, les machines virtuelles VMware du plan de protection de la restauration automatique utilisent la même banque de données que le serveur cible maître. Si vous voulez récupérer la machine virtuelle Azure de réplica vers la même machine virtuelle locale, celle-ci doit déjà figurer dans la même banque de données que le serveur cible maître. En l’absence de machine virtuelle locale, une nouvelle machine virtuelle de ce type est créée pendant la reprotection.

   ![](./media/site-recovery-failback-azure-to-vmware-classic/failback1.png)
6. Une fois que vous avez cliqué sur **OK** pour démarrer la reprotection, une tâche est lancée pour répliquer la machine virtuelle à partir d’Azure vers le site local. Vous pouvez en suivre la progression sous l’onglet **Tâches** .

   ![](./media/site-recovery-failback-azure-to-vmware-classic/failback2.png)

### <a name="run-a-failover-to-the-on-premises-site"></a>Exécuter un basculement vers le site local
Après la reprotection, la machine virtuelle est déplacée vers la version de restauration automatique de son groupe de protection. Elle est également automatiquement ajoutée au plan de récupération que vous avez utilisé pour le basculement vers Azure (le cas échéant).

1. Dans la page **Plans de récupération**, sélectionnez le plan de récupération qui contient le groupe de restauration automatique, puis cliquez sur **Basculement** > **Basculement non planifié**.
2. Dans **Confirmer le basculement** , vérifiez le sens du basculement (vers Azure) et sélectionnez le point de récupération à utiliser pour le basculement (dernier). Si vous avez activé **Multimachine virtuelle** lorsque vous avez configuré les propriétés de réplication, vous pouvez récupérer jusqu’à la dernière application ou jusqu’au dernier point de récupération cohérent en cas d’incident. Cochez la case pour démarrer le basculement.
3. Site Recovery arrête les machines virtuelles Azure pendant le basculement. Après que vous avez vérifié que la restauration automatique s’est terminée comme prévu, vous pouvez vous assurer que les machines virtuelles Azure ont été arrêtées correctement.

### <a name="reprotect-the--on-premises-site"></a>Reprotéger le site local
Une fois la restauration automatique terminée, vos données se trouvent de nouveau sur le site local, mais elles ne sont pas protégées. Pour démarrer à nouveau une réplication vers Azure, procédez comme suit :

1. Dans le portail Site Recovery > onglet **Machines**, sélectionnez les machines virtuelles qui ont été restaurées automatiquement, puis cliquez sur **Reprotéger**.
2. Après avoir vérifié que la réplication vers Azure fonctionne comme prévu, dans Azure, vous pouvez supprimer les machines virtuelles Azure (qui ne sont pas actuellement en cours d’exécution) qui ont été restaurées automatiquement.

### <a name="common-issues-in-failback"></a>Problèmes courants lors de la restauration automatique
1. Si vous effectuez une découverte vCenter en mode lecture seule utilisateur et protégez des machines virtuelles, l’opération réussit et le basculement fonctionne. Au moment de la reprotection, l’opération échouera car les magasins de données ne peuvent pas être détectés. Par conséquent, vous ne verrez pas les magasins de données répertoriés lors de la reprotection. Pour résoudre ce problème, mettez à jour les informations d'identification vCenter à l’aide du compte approprié disposant des autorisations, puis renouvelez l'opération. [En savoir plus](site-recovery-vmware-to-azure-classic.md#vmware-permissions-for-vcenter-access)
2. Lorsque vous restaurez automatiquement une machine virtuelle Linux et l’exécutez localement, vous constaterez que le package du Gestionnaire de réseau est désinstallé de l'ordinateur. Une fois la machine virtuelle récupérée dans Azure, le package du Gestionnaire de réseau est en effet supprimé.
3. Lorsqu'une machine virtuelle est configurée avec une adresse IP statique puis restaurée automatiquement vers Azure, l'adresse IP est acquise via DHCP. Lorsque vous basculez à nouveau en local, la machine virtuelle continue d'utiliser DHCP pour acquérir l'adresse IP. Vous devez manuellement vous connecter à l'ordinateur et redéfinir l'adresse IP sur une adresse statique si nécessaire.
4. Si vous utilisez la version gratuite d’ESXi 5.5 ou de vSphere 6 Hypervisor, le basculement devrait aboutir, mais la restauration automatique ne fonctionnera pas. Vous devrez effectuer une mise à niveau vers une licence d'évaluation pour activer la restauration automatique.

## <a name="failing-back-with-expressroute"></a>Restauration automatique avec ExpressRoute
Vous pouvez effectuer une restauration automatique via une connexion VPN ou via Azure ExpressRoute. Si vous souhaitez utiliser ExpressRoute, notez les points suivants :

* La solution ExpressRoute doit être configurée sur le réseau virtuel Azure vers lequel les machines sources basculent, et sur lequel les machines virtuelles Azure sont situées après le basculement.
* Les données sont répliquées vers un compte de stockage Azure sur un point de terminaison public. Vous devez configurer une homologation publique dans ExpressRoute avec le centre de données cible pour que la réplication Site Recovery utilise ExpressRoute.

