---
title: "Répliquer des machines virtuelles VMware sur Azure | Microsoft Docs"
description: "Résume les étapes à suivre pour répliquer des charges de travail exécutées sur des machines virtuelles VMware sur un stockage Azure"
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: dab98aa5-9c41-4475-b7dc-2e07ab1cfd18
ms.service: site-recovery
ms.workload: backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/12/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: 1429bf0d06843da4743bd299e65ed2e818be199d
ms.openlocfilehash: c2e21eefff3ce501ee5fc4003f60df25c4d7665d
ms.lasthandoff: 03/22/2017


---
# <a name="replicate-vmware-virtual-machines-to-azure-with-site-recovery"></a>Répliquer des machines virtuelles VMware sur Azure avec Site Recovery

> [!div class="op_single_selector"]
> * [Portail Azure](site-recovery-vmware-to-azure.md)
> * [Portail Azure Classic](site-recovery-vmware-to-azure-classic.md)


Cet article explique comment répliquer des machines virtuelles VMware locales sur Azure à l’aide du service [Azure Site Recovery](site-recovery-overview.md) dans le portail Azure.

Si vous voulez migrer des machines virtuelles VMware vers Azure (basculement uniquement), consultez [cet article](site-recovery-migrate-to-azure.md) pour plus d’informations.

Publiez des commentaires et des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="deployment-steps"></a>Étapes du déploiement

Voici ce que vous devez faire :

1. Vérifiez les conditions préalables et les limitations.
2. Configurez des comptes de réseau et de stockage Azure.
3. Préparez l’ordinateur local que vous souhaitez déployer en tant que serveur de configuration.
4. Préparez les comptes VMware à utiliser pour la découverte automatique des machines virtuelles, et éventuellement pour l’installation Push du service Mobilité.
4. Créez un coffre Recovery Services. Le coffre comporte les paramètres de configuration et orchestre la réplication.
5. Spécifiez les paramètres de la source, de la cible et de réplication.
6. Déployez le service Mobilité sur les machines virtuelles que vous souhaitez répliquer.
7. Activez la réplication des machines virtuelles.
7. Exécutez un test de basculement afin de vérifier que tout fonctionne bien.

## <a name="prerequisites"></a>Composants requis

**Configuration requise pour la prise en charge** | **Détails**
--- | ---
**Microsoft Azure** | En savoir plus sur la [configuration requise pour Azure](site-recovery-prereq.md#azure-requirements)
**Serveur de configuration local** | Vous devez disposer d’une machine virtuelle VMware Windows Server 2012 R2 ou une version ultérieure. Configurez ce serveur au cours du déploiement de Site Recovery.<br/><br/> Par défaut, le serveur de processus et le serveur cible maître sont également installés sur cette machine virtuelle. Pour monter en puissance, vous aurez peut-être besoin d’un serveur de processus distinct, dont la configuration requise est identique à celle du serveur de configuration.<br/><br/> Vous trouverez de plus amples informations sur ces composants [ici](site-recovery-set-up-vmware-to-azure.md#configuration-server-minimum-requirements)
**Serveurs VMware locaux** | Un ou plusieurs serveurs VMware vSphere exécutant la version 6.0, 5.5, 5.1 avec les dernières mises à jour. Les serveurs doivent se trouver dans le même réseau que le serveur de configuration (ou un serveur de processus distinct).<br/><br/> Nous recommandons un serveur vCenter pour gérer les hôtes vSphere exécutant la version 6.0 ou 5.5 avec les dernières mises à jour. Seules les fonctionnalités disponibles dans la version 5.5 sont prises en charge lorsque du déploiement de la version 6.0.
**Machines virtuelles locales** | Les machines virtuelles que vous souhaitez répliquer doivent exécuter un [système d’exploitation pris en charge](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions) et être conformes aux [conditions préalables pour Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements). La machine virtuelle doit exécuter des outils VMware.
**URLs** | Le serveur de configuration nécessite un accès à ces URL :<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/> Si vous avez des règles de pare-feu fondées sur l’adresse IP, vérifiez qu’elles autorisent la communication vers Azure.<br/></br> Autorisez les [plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le port HTTPS (443).<br/></br> Autorisez les plages d’adresses IP relatives à la région de votre abonnement Azure et à la région des États-Unis de l’Ouest (utilisées pour la gestion du contrôle d’accès et des identités).<br/><br/> Autorisez cette URL pour le téléchargement de MySQL : http://cdn.mysql.com/archives/mysql-5.5/mysql-5.5.37-win32.msi.
**Service de mobilité** | Installé sur chaque machine virtuelle répliquée.




## <a name="limitations"></a>Limitations

**Limite** | **Détails**
--- | ---
**Microsoft Azure** | Les comptes de stockage et de réseau doivent se trouver dans la même région que le coffre.<br/><br/> Si vous utilisez un compte de stockage premium, vous devez également disposer d’un compte de stockage standard pour stocker les journaux de réplication.<br/><br/> Vous ne pouvez pas effectuer de réplications dans des comptes premium dans le centre et le sud de l’Inde.
**Serveur de configuration local** | Le type d’adaptateur de machine virtuelle VMware doit être VMXNET3. Dans le cas contraire, [installez cette mise à jour](https://kb.vmware.com/selfservice/microsites/search.do?cmd=displayKC&docType=kc&externalId=2110245&sliceId=1&docTypeID=DT_KB_1_1&dialogID=26228401&stateId=1).<br/><br/> vSphere PowerCLI 6.0 doit être installé.<br/><br> L’ordinateur ne doit pas être un contrôleur de domaine. L’ordinateur doit avoir une adresse IP statique.<br/><br/> Le nom d’hôte doit être de 15 caractères ou moins, et le système d’exploitation doit être en anglais.
**VMware** | Seules les fonctionnalités de la version 5.5 sont prises en charge dans vCenter 6.0. Site Recovery ne prend pas en charge les nouvelles fonctionnalités de vCenter et vSphere 6.0 telles que Cross vCenter vMotion, les volumes virtuels et le DRS de stockage.
**Machines virtuelles** | Vérifier les [limitations de la machine virtuelle Azure](site-recovery-prereq.md#azure-requirements)<br/><br/> Vous ne pouvez pas répliquer de machines virtuelles comportant des disques chiffrés, ou des machines virtuelles avec le démarrage UEFI/EFI.<br/><br> Les clusters à disque partagé ne sont pas pris en charge. Si la machine virtuelle source propose l’association de cartes réseau, elle est convertie en une carte réseau unique après le basculement.<br/><br/> Si les machines virtuelles disposent d’un disque iSCSI, Site Recovery le convertit en fichier VHD après le basculement. Si la cible iSCSI peut être atteinte par la machine virtuelle Azure, elle s’y connecte et voit à la fois cette dernière et le VHD. Le cas échéant, déconnectez la cible iSCSI.<br/><br/> Si vous souhaitez activer la cohérence multimachine virtuelle, qui permet aux machines virtuelles exécutant la même charge de travail d’être restaurées ensemble à un point de données cohérent, ouvrez le port 20004 sur la machine virtuelle.<br/><br/> Windows doit être installé sur le lecteur C. Le disque du système d’exploitation doit être un disque de base, et non un disque dynamique. Le disque de données peut être dynamique.<br/><br/> Les fichiers Linux /etc/hosts se trouvant sur les machines virtuelles doivent contenir des entrées qui mappent le nom d’hôte local aux adresses IP associées à toutes les cartes réseau. Le nom d'hôte, les points de montage, le nom de périphérique, les chemins d'accès système et les noms de fichiers (par exemple /etc/; /usr) doivent uniquement être en anglais.<br/><br/> Les types spécifiques de [stockage Linux](site-recovery-support-matrix-to-azure.md#support-for-storage) sont pris en charge.<br/><br/>Créez ou définissez **disk.enableUUID=true** dans les paramètres de la machine virtuelle. Cela fournit un UUID cohérent pour le VMDK, afin qu’il effectue correctement le montage et garantisse que seules les modifications delta soient retransférées sur site lors de la restauration automatique, sans réplication complète.

## <a name="set-up-azure"></a>Configurer Azure

1. [Configurez un réseau Azure](../virtual-network/virtual-networks-create-vnet-arm-pportal.md).
    - Les machines virtuelles Azure seront placées dans ce réseau une fois créées après le basculement.
    - Vous pouvez configurer un réseau dans [Resource Manager](../resource-manager-deployment-model.md), ou en mode classique.

2. Configurer un [compte de stockage Azure](../storage/storage-create-storage-account.md#create-a-storage-account) pour les données répliquées.
    - Le compte peut être standard ou [premium](../storage/storage-premium-storage.md).
    - Vous pouvez configurer un compte dans Resource Manager, ou en mode classique.

3. [Préparez un compte](#prepare-for-automatic-discovery-and-push-installation) sur le serveur vCenter ou les hôtes vSphere afin que Site Recovery puisse détecter automatiquement les machines virtuelles VMware.

## <a name="prepare-the-configuration-server"></a>Préparer le serveur de configuration

1. Installez Windows Server 2012 R2 ou version ultérieure sur une machine virtuelle VMware.
2. Assurez-vous que la machine virtuelle a accès aux URL répertoriées dans les [conditions préalables](#prerequisites).
3. Installez [VMware vSphere PowerCLI 6.0](https://developercenter.vmware.com/tool/vsphere_powercli/6.0).


## <a name="prepare-for-automatic-discovery-and-push-installation"></a>Préparation pour l’installation Push et la découverte automatique

- **Préparer un compte pour la découverte automatique** : le serveur de traitement Site Recovery découvre automatiquement les machines virtuelles. Pour cela, Site Recovery a besoin des informations d’identification permettant d’accéder aux serveurs vCenter et aux hôtes vSphere ESXi.

    1. Pour utiliser un compte dédié, créez un rôle au niveau du serveur vCenter, avec ces [autorisations](#vmware-account-permissions). Donnez-lui un nom, tel que **Azure_Site_Recovery**.
    2. Créez ensuite un utilisateur sur l’hôte vSphere/le serveur vCenter et attribuez le rôle à l’utilisateur. Vous spécifiez ce compte d’utilisateur pendant le déploiement de Site Recovery.

- **Préparer un compte pour effectuer une notification Push du service Mobilité** : si vous souhaitez effectuer une notification Push du service Mobilité aux machines virtuelles, vous devez disposer d’un compte pouvant être utilisé par le serveur de processus pour accéder à la machine virtuelle. Le compte est utilisé uniquement pour l’installation Push. Vous pouvez utiliser un compte local ou de domaine :

    - Pour Windows, si vous n’utilisez pas un compte de domaine, vous devez désactiver le contrôle d’accès utilisateur distant sur la machine locale. Pour cela, dans le registre situé sous **HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System**, ajoutez l’entrée DWORD **LocalAccountTokenFilterPolicy** avec une valeur de 1.
    - Si vous souhaitez ajouter l’entrée de registre pour Windows à partir d’une interface CLI, saisissez :  ``REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1.``
    - Pour Linux, le compte doit être un utilisateur racine sur le serveur Linux source.

## <a name="create-a-recovery-services-vault"></a>Créer un coffre Recovery Services

[!INCLUDE [site-recovery-create-vault](../../includes/site-recovery-create-vault.md)]

## <a name="select-the-protection-goal"></a>Sélectionner l’objectif de protection

Sélectionnez les éléments à répliquer et l’emplacement de la réplication.

1. Cliquez sur **Coffres Recovery Services** > coffre.
2. Dans le menu Ressource, cliquez sur **Site Recovery** > **Étape 1 : Préparer l’infrastructure** > **Objectif de protection**.

    ![Sélectionner des objectifs](./media/site-recovery-vmware-to-azure/choose-goals.png)
3. Dans **Objectif de la protection**, sélectionnez **Vers Azure** > **Oui, avec hyperviseur vSphere VMware**.

    ![Sélectionner des objectifs](./media/site-recovery-vmware-to-azure/choose-goals2.png)

## <a name="set-up-the-source-environment"></a>Configurer l’environnement source

Configurez le serveur de configuration, inscrivez-le dans le coffre et découvrez les machines virtuelles.

1. Cliquez sur **Site Recovery** > **Étape 1 : Préparer l’infrastructure** > **Source**.
2. Si vous n’avez pas de serveur de configuration, cliquez sur **+Serveur de configuration**.

    ![Configurer la source](./media/site-recovery-vmware-to-azure/set-source1.png)
3. Dans **Ajouter un serveur**, vérifiez que **Serveur de configuration** s’affiche dans **Type de serveur**.
4. Téléchargez le fichier d’installation unifiée de Site Recovery.
5. Téléchargez la clé d’inscription du coffre. Vous en aurez besoin lors de l’exécution du programme d’installation unifiée. Une fois générée, la clé reste valide pendant 5 jours.

   ![Configurer la source](./media/site-recovery-vmware-to-azure/set-source2.png)


## <a name="run-site-recovery-unified-setup"></a>Exécuter le programme d’installation unifiée Site Recovery

Suivez les étapes suivantes avant de commencer, puis exécutez le programme d’installation unifiée afin d’installer le serveur de configuration, le serveur de processus et le serveur cible maître.
    - Visionnez une courte vidéo de présentation :

        > [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video1-Source-Infrastructure-Setup/player]

    - Sur la machine virtuelle du serveur de configuration, assurez-vous que l’horloge système est synchronisée avec un [Serveur de temps](https://technet.microsoft.com/windows-server-docs/identity/ad-ds/get-started/windows-time-service/windows-time-service). Elles doivent correspondre. S’il y a 15 minutes d’avance ou de retard, le programme d’installation peut échouer.
    - Exécutez le programme d’installation en tant qu’administrateur local sur la machine virtuelle du serveur de configuration.
    - Assurez-vous que TLS 1.0 est activé sur la machine virtuelle.


[!INCLUDE [site-recovery-add-configuration-server](../../includes/site-recovery-add-configuration-server.md)]

> [!NOTE]
> Le serveur de configuration peut également être installé [en ligne de commande](http://aka.ms/installconfigsrv).



### <a name="add-the-account-for-automatic-discovery"></a>Ajouter le compte pour la découverte automatique

[!INCLUDE [site-recovery-add-vcenter-account](../../includes/site-recovery-add-vcenter-account.md)]

### <a name="connect-to-vmware-servers"></a>Se connecter à des serveurs VMware

Connectez-vous à des hôtes vSphere ESXi ou des serveurs vCenter pour découvrir des machines virtuelles VMware.

- Si vous ajoutez le serveur vCenter ou des hôtes vSphere avec un compte ne disposant pas de droits d’administrateur sur le serveur, ces droits doivent être activés pour le compte :
    - Centre de données, magasin de données, dossier, hôte, réseau, ressource, machine virtuelle, vSphere Distributed Switch.
    - Le serveur vCenter a besoin des autorisations d’affichage de stockage.
- Lorsque vous ajoutez des serveurs VMware, il peut s’écouler 15 minutes ou plus avant leur affichage dans le portail.
Pour permettre à Azure Site Recovery de détecter les machines virtuelles en cours d’exécution dans votre environnement local, vous devez connecter votre serveur VMware vCenter ou vos hôtes ESXi vSphere à Site Recovery.

Sélectionnez **+vCenter** pour connecter un serveur VMware vCenter ou un ordinateur hôte VMware vSphere ESXi.

[!INCLUDE [site-recovery-add-vcenter](../../includes/site-recovery-add-vcenter.md)]

Site Recovery se connecte aux serveurs VMware en utilisant les paramètres spécifiés, et découvre les machines virtuelles.

## <a name="set-up-the-target"></a>Configurer la cible

Avant de configurer l’environnement cible, assurez-vous de disposer d’un [compte de stockage Azure et d’un réseau](#set-up-azure).

1. Cliquez sur **Préparer l’infrastructure** > **Cible** et sélectionnez l’abonnement Azure à utiliser.
2. Spécifiez si votre modèle de déploiement cible est basé sur Resource Manager ou est de type classique.
3. Site Recovery vérifie que vous disposez d’un ou de plusieurs réseaux et comptes Azure Storage compatibles.

   ![Cible](./media/site-recovery-vmware-to-azure/gs-target.png)
4. Si vous n’avez pas créé de compte de stockage ou de réseau, cliquez sur **+Compte de stockage** ou **+Réseau** pour créer un compte Resource Manager ou un réseau en ligne.

## <a name="set-up-replication-settings"></a>Configurer les paramètres de réplication

Visionnez une courte vidéo de présentation avant de commencer :
> [!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video2-vCenter-Server-Discovery-and-Replication-Policy/player]

1. Pour créer une nouvelle stratégie de réplication, cliquez sur **Infrastructure de Site Recovery** > **Stratégies de réplication** > **+Stratégie de réplication**.
2. Dans **Créer une stratégie de réplication**, indiquez le nom de la stratégie.
3. Dans le champ **Seuil d’objectif de point de récupération**, spécifiez la limite de l’objectif de point de récupération. Cette valeur spécifie la fréquence à laquelle les points de récupération des données sont créés. Une alerte est générée lorsque la réplication continue dépasse cette limite.
4. Dans **Rétention des points de récupération**, spécifiez la durée de la fenêtre de rétention pour chaque point de récupération (en heures). Les machines virtuelles répliquées peuvent être récupérées à n’importe quel point dans une fenêtre. Les machines virtuelles répliquées vers Premium Storage peuvent prendre en charge jusqu’à 24 heures de rétention et 72 heures en cas de stockage standard.
5. Dans **Fréquence des captures instantanées de cohérence d’application**, spécifiez la fréquence de création des points de récupération contenant des captures instantanées cohérentes avec les applications (en minutes). Cliquez sur **OK** pour créer la stratégie.

    ![Stratégie de réplication](./media/site-recovery-vmware-to-azure/gs-replication2.png)
8. Lorsque vous créez une stratégie, cette dernière est automatiquement associée au serveur de configuration. Par défaut, une stratégie de correspondance est automatiquement créée pour la restauration automatique. Par exemple, si la stratégie de réplication est **rep-policy**, la stratégie de restauration automatique correspond alors à **rep-policy-failback**. Cette stratégie n’est utilisée qu’à partir du moment où vous initiez une restauration automatique à partir d’Azure.  



## <a name="plan-capacity"></a>Planifier la capacité

1. Votre infrastructure de base est désormais configurée. Vous pouvez donc réfléchir à la planification de la capacité et déterminer si des ressources supplémentaires sont nécessaires. [Plus d’informations](site-recovery-plan-capacity-vmware.md)
2. Lorsque vous avez terminé la planification de la capacité, sélectionnez **Oui** dans **Avez-vous effectué une planification de la capacité ?**

   ![Planification de la capacité](./media/site-recovery-vmware-to-azure/gs-capacity-planning.png)


## <a name="prepare-vms-for-replication"></a>Préparer des machines virtuelles pour la réplication

Le service Mobilité est installé sur toutes les machines virtuelles VMware que vous souhaitez répliquer. Vous pouvez installer le service Mobilité de plusieurs façons :

1. Utilisez une installation Push depuis le serveur de processus. Vous devez préparer les machines virtuelles en vue de l’utilisation de cette méthode.
2. Utilisez des outils de déploiement tels que System Center Configuration Manager ou Azure automation DSC.
3.  Procédez à une installation manuelle.

[En savoir plus](site-recovery-vmware-to-azure-install-mob-svc.md)


## <a name="enable-replication"></a>Activer la réplication

Avant de commencer :

- Lorsque vous ajoutez ou modifiez des machines virtuelles, il peut être nécessaire d’attendre 15 minutes ou plus avant que les modifications n’entrent en vigueur et qu’elles apparaissent dans le portail.
- Vous pouvez consulter l’heure de la dernière découverte des machines virtuelles dans **Serveurs de configuration** > **Last Contact At (Dernier contact à)**.
- Pour ajouter des machines virtuelles sans attendre la découverte planifiée, mettez en surbrillance le serveur de configuration (sans cliquer dessus) et cliquez sur **Actualiser**.
* Si une machine virtuelle est prête pour l’installation Push, le serveur de processus installe automatiquement le service Mobilité lorsque vous activez la réplication.


### <a name="exclude-disks-from-replication"></a>Exclure les disques de la réplication

Par défaut, tous les disques d’une machine sont répliqués. Vous pouvez exclure des disques de la réplication. Par exemple, vous pouvez ne pas répliquer les disques comportant des données temporaires, ou des données actualisées à chaque redémarrage d’une machine ou d’une application (par exemple, pagefile.sys ou tempdb dans SQL Server).

### <a name="replicate-vms"></a>Répliquer des machines virtuelles

Avant de commencer, visionnez une courte vidéo de présentation :

>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video3-Protect-VMware-Virtual-Machines/player]

1. Cliquez sur **Étape 2 : Répliquer l’application** > **Source**.
2. Dans **Source**, sélectionnez le serveur de configuration.
3. Dans **Type de machine**, sélectionnez **Machines virtuelles**.
4. Dans la zone **Hyperviseur vCenter/vSphere**, sélectionnez le serveur vCenter qui gère l’hôte vSphere, ou sélectionnez l’hôte.
5. Sélectionnez le serveur de processus. Si vous n’avez pas créé de serveur de processus supplémentaire, il s’agit du serveur de configuration. Cliquez ensuite sur **OK**.

    ![Activer la réplication](./media/site-recovery-vmware-to-azure/enable-replication2.png)

6. Dans **Cible**, sélectionnez l’abonnement et le groupe de ressources dans lesquels vous voulez créer les machines virtuelles basculées. Choisissez le modèle de déploiement que vous souhaitez utiliser dans Azure (classique ou gestion des ressources) pour les machines virtuelles basculées.


7. Sélectionnez le compte de stockage Azure que vous souhaitez utiliser pour les données de réplication. Si vous ne souhaitez pas utiliser un compte que vous avez déjà configuré, vous pouvez en créer un nouveau.

8. Sélectionnez le sous-réseau et le réseau Azure auxquels les machines virtuelles Azure se connectent lorsqu’elles sont créées après le basculement. Sélectionnez **Effectuez maintenant la configuration pour les machines sélectionnées** pour appliquer le paramètre réseau à l’ensemble des machines que vous sélectionnez à des fins de protection. Sélectionnez **Configurer ultérieurement** pour sélectionner le réseau Azure pour chaque machine. Si vous ne souhaitez pas utiliser un réseau existant, vous pouvez en créer un.

    ![Activer la réplication](./media/site-recovery-vmware-to-azure/enable-rep3.png)
9. Dans **Machines virtuelles** > **Sélectionner les machines virtuelles**, cliquez sur chaque machine à répliquer. Vous pouvez uniquement sélectionner les machines pour lesquelles la réplication peut être activée. Cliquez ensuite sur **OK**.

    ![Activer la réplication](./media/site-recovery-vmware-to-azure/enable-replication5.png)
10. Dans **Propriétés** > **Configurer les propriétés**, sélectionnez le compte utilisé par le serveur de processus pour installer automatiquement le service Mobilité sur la machine.
11. Par défaut, tous les disques sont répliqués. Cliquez sur **Tous les disques** et désélectionnez ceux qui ne doivent pas être répliqués. Cliquez ensuite sur **OK**. Vous pouvez configurer des propriétés de machine virtuelle supplémentaires ultérieurement.

    ![Activer la réplication](./media/site-recovery-vmware-to-azure/enable-replication6.png)
11. Dans **Paramètres de réplication** > **Configurer les paramètres de réplication**, vérifiez que la stratégie de réplication correcte est sélectionnée. Si vous modifiez une stratégie, les modifications seront appliquées à la machine en cours de réplication et aux nouvelles machines.
12. Cochez la case **Cohérence multimachine virtuelle** pour regrouper les machines dans un groupe de réplication, et nommez le groupe. Cliquez ensuite sur **OK**. Notez les points suivants :

    * Les machines des groupes de réplication sont répliquées ensemble et ont des points de récupération cohérents après incident et avec les applications lorsqu’elles basculent.
    * Nous vous recommandons de rassembler les machines virtuelles et les serveurs physiques afin qu’ils reflètent vos charges de travail. L’activation de la cohérence multimachine virtuelle peut affecter les performances de la charge de travail. Elle ne doit être utilisée que si les machines exécutent la même charge de travail et si vous avez besoin de cohérence.

    ![Activer la réplication](./media/site-recovery-vmware-to-azure/enable-replication7.png)
13. Cliquez sur **Activer la réplication**. Vous pouvez suivre la progression du travail **Activer la protection** dans **Paramètres** > **Travaux** > **Travaux Site Recovery**. Une fois le travail **Finaliser la protection** exécuté, la machine est prête pour le basculement.

Une fois que vous activez la réplication, le service Mobilité est installé si vous avez configuré l’installation Push. Une fois que le service Mobilité bénéficie d’une installation Push sur une machine virtuelle, une tâche de protection démarre et échoue. Après cet échec, vous devez redémarrer manuellement chaque machine. Ensuite, la tâche de protection recommence et la réplication initiale se produit.



### <a name="view-and-manage-vm-properties"></a>Afficher et gérer les propriétés des machines virtuelles

Nous vous recommandons de vérifier les propriétés de la machine virtuelle et d’apporter toutes les modifications requises.

1. Cliquez sur **Éléments répliqués** et sélectionnez la machine. Le panneau **Bases** affiche des informations sur les paramètres et l’état des machines.
2. Dans **Propriétés**, vous pouvez afficher les informations sur la réplication et le basculement de la machine virtuelle.
3. Dans **Calcul et réseau** > **Propriétés de calcul**, vous pouvez spécifier la taille de la cible et le nom de la machine virtuelle Azure. Au besoin, modifiez ce nom en fonction de la [configuration Azure requise](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) .
4. Modifiez les paramètres du réseau cible, du sous-réseau et de l’adresse IP qui seront affectés à la machine virtuelle Azure :

   - Vous pouvez définir l’adresse IP cible.

    - Si vous ne fournissez pas d’adresse IP, la machine ayant basculé utilisera le service DHCP.
    - Si vous définissez une adresse qui n’est pas disponible au moment du basculement, ce dernier échoue.
    - Vous pouvez utiliser la même adresse IP cible pour le test de basculement si cette adresse est disponible sur le réseau de test de basculement.

   - Le nombre de cartes réseau est déterminé par la taille spécifiée pour la machine virtuelle cible :

     - Si le nombre de cartes réseau sur la machine source est inférieur ou égal au nombre de cartes autorisé pour la taille de la machine cible, la cible présente le même nombre de cartes que la source.
     - Si le nombre de cartes de la machine virtuelle source dépasse la valeur de taille cible autorisée, la taille cible maximale est utilisée.
     - Par exemple, si une machine source présente deux cartes réseau et que la taille de la machine cible en accepte quatre, la machine cible présentera deux cartes. Si la machine source inclut deux cartes, mais que la taille cible prise en charge accepte une seule carte, la machine cible présentera une seule carte.     
   - Si la machine virtuelle possède plusieurs cartes réseau, elles se connectent toutes au même réseau.
   - Si la machine virtuelle possède plusieurs cartes réseau, la première qui s’affiche dans la liste devient la carte réseau *par défaut* dans la machine virtuelle Azure.
5. Dans **Disques**, vous pouvez voir le système d’exploitation de la machine virtuelle et les disques de données qui seront répliqués.

## <a name="run-a-test-failover"></a>Exécution d’un test de basculement


Après avoir terminé la configuration, exécutez un test de basculement afin de vérifier que tout fonctionne bien. Visionnez une courte vidéo de présentation avant de commencer :
>[!VIDEO https://channel9.msdn.com/Series/Azure-Site-Recovery/VMware-to-Azure-with-ASR-Video4-Recovery-Plan-DR-Drill-and-Failover/player]


1. Pour effectuer le basculement d’une seule machine, dans **Paramètres** > **Éléments répliqués**, cliquez sur la machine virtuelle, puis sur l’icône **+Test de basculement**.

    ![Test de basculement](./media/site-recovery-vmware-to-azure/TestFailover.png)

1. Pour effectuer le basculement d’un plan de récupération, dans **Paramètres** > **Plans de récupération**, cliquez avec le bouton droit sur le plan et sélectionnez **Test de basculement**. Pour créer un plan de récupération, suivez [ces instructions](site-recovery-create-recovery-plans.md).  

1. Dans **Test de basculement**, sélectionnez le réseau Azure auquel les machines virtuelles Azure seront connectées après le basculement.

1. Cliquez sur **OK** pour commencer le basculement. Vous pouvez suivre la progression du basculement en cliquant sur la machine virtuelle pour ouvrir ses propriétés, ou en sélectionnant le travail **Test de basculement** dans le nom de l’archivage > **Paramètres** > **Travaux** > **Travaux Site Recovery**.

1. Une fois le basculement terminé, vous devez également voir la machine Azure de réplication apparaître dans le Portail Azure > **Machines virtuelles**. Vous devrez peut-être vous assurer que la machine virtuelle présente la taille appropriée, qu’elle est bien connectée au réseau approprié et qu’elle s’exécute.

1. Si vous avez [préparé les connexions après le basculement](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover), vous devez être à même de vous connecter à la machine virtuelle Azure.

1. Une fois que vous avez terminé, cliquez sur **Nettoyer le test de basculement de nettoyage** sur le plan de récupération. Cliquez sur **Notes** pour consigner et enregistrer d’éventuelles observations sur le test de basculement. Cette opération supprimera les machines virtuelles qui ont été créées au cours du test de basculement.

Pour plus d’informations sur les tests de basculement, cliquez [ici](site-recovery-test-failover-to-azure.md).


## <a name="vmware-account-permissions"></a>Autorisations du compte VMware

Site Recovery a besoin d’accéder à VMware pour que le serveur de processus découvre automatiquement des machines virtuelles ainsi que pour le basculement et la restauration automatique des machines virtuelles.

- **Migrer** : si vous souhaitez uniquement migrer des machines virtuelles VMware vers Azure, sans jamais procéder à leur restauration automatique, vous pouvez utiliser un compte VMware avec un rôle en lecture seule. Ce type de rôle peut exécuter le basculement mais ne peut pas arrêter les machines source protégées. Cela n’est pas nécessaire pour la migration.
- **Réplication/restauration** : si vous souhaitez déployer la réplication complète (réplication, basculement et restauration automatique), le compte doit être en mesure d’exécuter des opérations telles que la création et la suppression de disques, l’alimentation de machines virtuelles, etc.
- **Découverte automatique** : au moins un compte en lecture seule est nécessaire.


**Tâche** | **Nécessite un compte/rôle** | **Autorisations** | **Détails**
--- | --- | --- | ---
**Le serveur de processus découvre automatiquement les machines virtuelles VMware** | Vous devez disposer d’au moins un utilisateur en lecture seule | Objet de centre de données -> Propager vers l’objet enfant, rôle = lecture seule | L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).
**Type de basculement** | Vous devez disposer d’au moins un utilisateur en lecture seule | Objet de centre de données -> Propager vers l’objet enfant, rôle = lecture seule | L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).<br/><br/> Utile à des fins de migration, mais pas pour la réplication complète, le basculement et la restauration automatique.
**Basculement et restauration automatique** | Nous vous suggérons de créer un rôle (Azure_Site_Recovery) avec les autorisations nécessaires, puis d’attribuer le rôle à un utilisateur ou à un groupe d’utilisateurs VMware. | Objet de centre de données -> Propager vers l’objet enfant, rôle = Azure_Site_Recovery<br/><br/> Banque de données -> Allouer de l’espace, parcourir la banque de données, opérations de fichier de bas niveau, supprimer le fichier, mettre à jour les fichiers de machine virtuelle<br/><br/> Réseau -> Attribution de réseau<br/><br/> Ressource -> Affecter les machines virtuelles au pool de ressources, migrer des machines virtuelles hors tension, migrer des machines virtuelles sous tension<br/><br/> Tâches -> Créer une tâche, Mettre à jour une tâche<br/><br/> Machine virtuelle -> Configuration<br/><br/> Machine virtuelle -> Interagir -> répondre à la question, connexion d’appareil, configurer un support de CD, configurer une disquette, mettre hors tension, mettre sous tension, installation des outils VMware<br/><br/> Machine virtuelle -> Stock -> Créer, inscrire, désinscrire<br/><br/> Machine virtuelle -> Approvisionnement -> Autoriser le téléchargement de machines virtuelles, autoriser le chargement de fichiers de machine virtuelle<br/><br/> Machine virtuelle -> Instantanés -> Supprimer les instantanés | L’utilisateur est affecté au niveau du centre de données et a accès à tous les objets dans le centre de données.<br/><br/> Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’objet **Propager vers enfant** aux objets enfants (hôtes vSphere, banques de données, machines virtuelles et réseaux).


## <a name="next-steps"></a>Étapes suivantes

Une fois la réplication effective, lorsqu’une panne se produit, vous basculez vers Azure et les machines virtuelles Azure sont créées à partir des données répliquées. Vous pouvez alors accéder aux charges de travail et aux applications dans Azure, jusqu'à la restauration automatique vers votre emplacement principal.

- [En savoir plus](site-recovery-failover.md) sur les différents types de basculement et leur exécution.
- Si vous migrez des ordinateurs au lieu de procéder à une réplication et une restauration automatique, [lisez cet article pour en savoir plus](site-recovery-migrate-to-azure.md#migrate-on-premises-vms-and-physical-servers).
- [En savoir plus sur la restauration automatique](site-recovery-failback-azure-to-vmware.md) pour restaurer et répliquer des machines virtuelles Azure vers le site local principal.

## <a name="third-party-software-notices-and-information"></a>Informations et remarques relatives aux logiciels tiers
Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

