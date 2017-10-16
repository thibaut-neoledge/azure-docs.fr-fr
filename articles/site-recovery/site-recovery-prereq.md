---
title: "Conditions préalables pour la réplication vers Azure avec Azure Site Recovery | Microsoft Docs"
description: "Découvrez les conditions préalables pour la réplication des machines virtuelles et des machines physiques vers Azure avec le service Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rajani-janaki-ram
manager: jwhit
editor: tysonn
ms.assetid: e24eea6c-50a7-4cd5-aab4-2c5c4d72ee2d
ms.service: site-recovery
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 06/23/2017
ms.author: rajanaki
ms.openlocfilehash: 490833c14b6856cdaf6f6bfd2f67ce54fb0414a2
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
#  <a name="prerequisites-for-replication-from-on-premises-to-azure-by-using-site-recovery"></a>Conditions préalables pour la réplication depuis un site local vers Azure avec Site Recovery

> [!div class="op_single_selector"]
> * [Répliquer d’Azure vers Azure](site-recovery-azure-to-azure-prereq.md)
> * [Répliquer d’un emplacement local vers Azure](site-recovery-prereq.md)

Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité d’activité et de récupération d’urgence de votre entreprise en coordonnant la réplication d’une machine virtuelle Azure vers une autre région Azure. Il orchestre réplique aussi les machines virtuelles et les serveurs physiques locaux vers le cloud (Azure) ou un centre de données secondaire. En cas de panne sur votre site principal, vous pouvez effectuer un basculement sur un site secondaire pour préserver la disponibilité des applications et des charges de travail. Vous pouvez restaurer votre site principal dès lors qu’il retrouve un fonctionnement normal. Pour plus d’informations sur Site Recovery, consultez la page [Qu’est-ce que Site Recovery ?](site-recovery-overview.md)

Dans cet article, nous résumons les conditions préalables pour le lancement de la réplication Site Recovery à partir d’un ordinateur local vers Azure.

Vous pouvez laisser des commentaires au bas de cet article. Vous pouvez également poser des questions techniques sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="azure-requirements"></a>Conditions requises pour Azure

**Prérequis** | **Détails**
--- | ---
**Compte Azure** | Un [compte Microsoft Azure](http://azure.microsoft.com/). Vous pouvez commencer par une version d’ [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
**Service Site Recovery** | Pour plus d’informations sur la tarification du service Azure Site Recovery, consultez [cette page](https://azure.microsoft.com/pricing/details/site-recovery/). |
**Azure Storage** | Vous avez besoin d’un compte de stockage Azure pour stocker les données répliquées. Le compte de stockage doit se trouver dans la même région que le coffre Azure Recovery Services. Des machines virtuelles Azure sont créées en cas de basculement.<br/><br/> En fonction du modèle de ressource que vous souhaitez utiliser pour le basculement des machines virtuelles Azure, vous pouvez configurer un compte à l’aide du [modèle de déploiement Azure Resource Manager](../storage/common/storage-create-storage-account.md) ou du [modèle de déploiement classique](../storage/common/storage-create-storage-account.md).<br/><br/>Vous pouvez utiliser le [stockage géoredondant](../storage/common/storage-redundancy.md#geo-redundant-storage) ou le stockage localement redondant. Nous vous recommandons d’utiliser le stockage géo-redondant. Avec le stockage géo-redondant, les données sont résilientes si une panne se produit au niveau régional ou si la région principale ne peut pas être récupérée.<br/><br/> Vous pouvez utiliser un compte de stockage Azure standard ou Azure Premium. Le [stockage Premium](https://docs.microsoft.com/azure/storage/storage-premium-storage) est généralement utilisé pour les machines virtuelles nécessitant des performances d’E/S élevées et une faible latence. Avec le stockage Premium, une machine virtuelle peut héberger des charges de travail nécessitant beaucoup d’E/S. Si vous utilisez un stockage Premium pour les données répliquées, il vous faut également un compte de stockage standard. Un compte de stockage standard permet de stocker les journaux de réplication qui capturent les modifications apportées en continu aux données locales.<br/><br/>
**Limites de stockage** | Vous ne pouvez pas déplacer les comptes de stockage que vous utilisez dans Site Recovery vers un autre groupe de ressources ni changer d’abonnement.<br/><br/> La réplication vers des comptes de stockage Premium dans le centre et le sud de l’Inde n’est actuellement pas disponible.
**Réseau Azure** | Vous devez avoir un réseau Azure auquel les machines virtuelles Azure se connectent après basculement. Le réseau Azure doit se trouver dans la même région que le coffre Recovery Services.<br/><br/> Dans le portail Azure, vous pouvez créer un réseau Azure à l’aide du [modèle de déploiement Resource Manager](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou du [modèle de déploiement classique](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).<br/><br/> Si vous effectuez une réplication à partir de System Center Virtual Machine Manager (VMM) vers Azure, vous devez configurer le mappage réseau entre les réseaux VMM VM et Azure. Cela garantit que les machines virtuelles Azure se connectent aux réseaux appropriés après le basculement.
**Limitations du réseau** | Vous ne pouvez pas déplacer les comptes réseau que vous utilisez dans Site Recovery vers un autre groupe de ressources ni changer d’abonnement.
**Mappage réseau** | Si vous répliquez des machines virtuelles Microsoft Hyper-V dans des clouds VMM, vous devez configurer le mappage réseau. Cela garantit que les machines virtuelles Azure se connectent aux réseaux appropriés dès leur création après le basculement.

> [!NOTE]
> Les sections suivantes décrivent les conditions préalables pour les différents composants de l’environnement client. Pour plus d’informations sur la prise en charge des configurations spécifiques, consultez le [tableau de prise en charge](site-recovery-support-matrix.md).
>

## <a name="disaster-recovery-of-vmware-vms-or-physical-windows-or-linux-servers-to-azure"></a>Récupération d’urgence dans Azure de machines virtuelles VMware ou de serveurs Windows/Linux physiques
Les composants suivants sont requis pour la récupération d’urgence des machines virtuelles VMware ou des serveurs physiques Windows/Linux. Ils s’ajoutent à ceux décrits dans les [conditions requises pour Azure](#azure-requirements).


### <a name="configuration-server-or-additional-process-server"></a>Serveur de configuration ou serveur de traitement supplémentaire

Configurez un ordinateur local en tant que le serveur de configuration pour orchestrer la communication entre le site local et Azure. Le tableau ci-dessous répertorie la configuration système et logicielle exigée pour une machine virtuelle utilisée comme serveur de configuration.

> [!IMPORTANT]
> Quand vous déployez un serveur de configuration pour protéger des machines virtuelles VMware, nous vous recommandons de le déployer en tant que machine virtuelle **hautement disponible**.

[!INCLUDE [site-recovery-configuration-server-requirements](../../includes/site-recovery-configuration-and-scaleout-process-server-requirements.md)]

### <a name="vmware-vcenter-or-vsphere-host-prerequisites"></a>Prérequis pour l’hôte VMware vCenter ou vSphere

| **Composant** | **Configuration requise** |
| --- | --- |
| **vSphere** | Vous avez besoin d’un ou de plusieurs hyperviseurs VMware vSphere.<br/><br/>Les hyperviseurs doivent exécuter vSphere version 6.0, 5.5 ou 5.1 avec les dernières mises à jour.<br/><br/>Nous vous recommandons de placer les hôtes vSphere et les serveurs vCenter dans le même réseau que le serveur de traitement. Il s’agit du réseau où se trouve le serveur de configuration, sauf si vous avez configuré un serveur de traitement dédié. |
| **vCenter** | Nous vous recommandons de déployer un serveur VMware vCenter pour gérer vos hôtes vSphere. Il doit exécuter vCenter version 6.0 ou 5.5 avec les dernières mises à jour.<br/><br/>**Limitation** : Site Recovery ne prend pas en charge la réplication entre des instances de vCenter vMotion. Storage DRS et Storage vMotion ne sont pas non plus pris en charge sur une machine virtuelle cible maître après une opération de reprotection.|

### <a name="replicated-machine-prerequisites"></a>Configuration requise pour les machines répliquées

| **Composant** | **Configuration requise** |
| --- | --- |
| **Machines locales** (machines virtuelles VMware) | Les outils VMware doivent être installés et en cours d’exécution sur les machines virtuelles répliquées.<br/><br/> Les machines virtuelles doivent répondre aux [conditions préalables Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) pour la création d’une machine virtuelle Azure.<br/><br/>La capacité du disque sur chaque ordinateur protégé ne peut pas être supérieure à 1 023 Go. <br/><br/>Un minimum de 2 Go d’espace disponible sur le lecteur d’installation est requis pour l’installation du composant.<br/><br/>Si vous souhaitez activer la cohérence de plusieurs machines virtuelles, le port 20004 doit être ouvert sur le pare-feu local de machine virtuelle.<br/><br/>Le nom des machines doit contenir entre 1 et 63 caractères (lettres, chiffres et tirets). Le nom doit commencer et se terminer par une lettre ou un chiffre. <br/><br/>Après avoir activé la réplication d’une machine, vous pouvez modifier le nom Azure.<br/><br/> |
| **Ordinateurs Windows** (serveur physique ou VMware) | L’ordinateur doit exécuter l’un des systèmes d’exploitation 64 bits pris en charge suivants : <br/>- Windows Server 2012 R2<br/>- Windows Server 2012<br/>-Windows Server 2008 R2 avec SP1 ou version ultérieure<br/><br/> Le système d’exploitation doit être installé sur le lecteur C. Le disque du système d’exploitation doit être un disque de base Windows, non un disque dynamique. Le disque de données peut être dynamique.<br/><br/>|
| **Ordinateurs Linux** (serveur physique ou VMware) | L’ordinateur doit exécuter l’un des systèmes d’exploitation 64 bits pris en charge suivants : <br/>- Red Hat Enterprise Linux 5.2 à 5.11, 6.1 à 6.9, 7.0 à 7.3<br/>- CentOS 5.2 à 5.11, 6.1 à 6.9, 7.0 à 7.3<br/>- Serveur Ubuntu 14.04 LTS (pour obtenir la liste des versions de noyau prises en charge pour Ubuntu, voir les [systèmes d’exploitation pris en charge](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions))<br/>- Serveur Ubuntu 16.04 LTS (pour obtenir la liste des versions de noyau prises en charge pour Ubuntu, voir les [systèmes d’exploitation pris en charge](site-recovery-support-matrix-to-azure.md#support-for-replicated-machine-os-versions))<br/>- Debian 7 ou Debian 8<br/>- Oracle Enterprise Linux 6.5 ou 6.4 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3)<br/>-SUSE Linux Enterprise Server 11 SP4 ou SUSE Linux Enterprise Server 11 SP3<br/><br/>Vos fichiers /etc/hosts se trouvant sur les ordinateurs protégés doivent contenir des entrées qui mappent le nom d’hôte local sur les adresses IP associées à toutes les cartes réseau.<br/><br/>Après un basculement, si vous souhaitez vous connecter à une machine virtuelle Azure exécutant Linux à l’aide d’un client Secure Shell (SSH), vérifiez que le service SSH sur l’ordinateur protégé se lance automatiquement au démarrage du système. Assurez-vous également que les règles de pare-feu autorisent une connexion SSH à l’ordinateur protégé.<br/><br/>Le nom d’hôte, les points de montage, les noms d’appareils, ainsi que les chemins d’accès système et les noms de fichiers Linux (par exemple, /etc/ et /usr) doivent uniquement être en anglais.<br/><br/>Les répertoires suivants (s’ils sont configurés en tant que partitions ou systèmes de fichiers séparés) doivent tous se trouver sur le même disque (le disque du système d’exploitation) sur le serveur source :<br/>- / (racine)<br/>- /boot<br/>- /usr<br/>- /usr/local<br/>- /var<br/>- /etc<br/><br/>Actuellement, les fonctionnalités XFS v5, telles que les sommes de contrôle de métadonnées, ne sont pas prises en charge par Site Recovery sur les systèmes de fichiers XFS. Assurez-vous que vos systèmes de fichiers XFS n’utilisent pas de fonctionnalités v5. Vous pouvez utiliser l’utilitaire xfs_info pour vérifier le superbloc XFS pour la partition. Si **ftype** est défini sur **1**, les fonctionnalités v5 sont utilisées.<br/><br/>Sur les serveurs Red Hat Enterprise Linux 7 et CentOS 7, l’utilitaire lsof doit être installé et disponible.<br/><br/>


## <a name="disaster-recovery-of-hyper-v-vms-to-azure-no-vmm"></a>Récupération d’urgence dans Azure de machines virtuelles (pas de VMM)

Les composants suivants sont requis pour la récupération d’urgence de machines virtuelles Hyper-V dans les clouds VMM. Ils s’ajoutent à ceux décrits dans les [conditions requises pour Azure](#azure-requirements).

| **Configuration requise** | **Détails** |
| --- | --- |
| **Hôte Hyper-V** |Un ou plusieurs serveurs locaux doivent exécuter Windows Server 2012 R2 avec les dernières mises à jour et le rôle Hyper-V activé ou Microsoft Hyper-V Server 2012 R2.<br/><br/>Les serveurs Hyper-V doivent avoir une ou plusieurs machines virtuelles.<br/><br/>Les serveurs Hyper-V doivent être connectés à Internet, directement ou via un proxy.<br/><br/>Les correctifs décrits dans l’article de la base de connaissances [2961977](https://support.microsoft.com/kb/2961977) doivent être installés sur les serveurs Hyper-V.
|**Fournisseur et agent**| Pendant un déploiement Site Recovery, vous installez le fournisseur Azure Site Recovery. Dans le cadre de l’installation du fournisseur, l’agent Azure Recovery Services est installé sur chaque serveur Hyper-V exécutant les machines virtuelles que vous souhaitez protéger. <br/><br/>Tous les serveurs Hyper-V d’un coffre Site Recovery doivent disposer des mêmes versions du fournisseur et de l’agent.<br/><br/>Le fournisseur doit se connecter à Site Recovery via Internet. Le trafic peut être envoyé directement ou via un proxy. Le protocole HTTPS basé sur proxy n’est pas pris en charge. Le serveur proxy doit autoriser l’accès aux URL suivantes :<br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/>Si vous avez des règles de pare-feu fondées sur l’adresse IP sur le serveur, vérifiez que ces règles autorisent la communication vers Azure.<br/><br/> Autorisez les [plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le protocole HTTPS (port 443).<br/><br/> Autorisez les plages d’adresses IP relatives à la région Azure de votre abonnement et à la région de l’Ouest des États-Unis (utilisées pour la gestion du contrôle d’accès et des identités).


## <a name="disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-azure"></a>Récupération d’urgence dans Azure de machines virtuelles Hyper-V hébergées dans des clouds VMM

Les composants suivants sont requis pour la récupération d’urgence de machines virtuelles Hyper-V dans les clouds VMM. Ils s’ajoutent à ceux décrits dans les [conditions requises pour Azure](#azure-requirements).

| **Configuration requise** | **Détails** |
| --- | --- |
| **Virtual Machine Manager** |Vous devez avoir un ou plusieurs serveurs VMM s’exécutant sous System Center 2012 R2 ou une version ultérieure. Chaque serveur VMM doit être associé à un ou plusieurs clouds configurés. <br/><br/>Un cloud doit avoir :<br/>- Un ou plusieurs groupes hôtes VMM.<br/>- Un ou plusieurs serveurs hôtes Hyper-V ou clusters dans chaque groupe hôte.<br/><br/>Pour plus d’informations sur la configuration de clouds VMM, voir [How to create a cloud in VMM 2012](http://social.technet.microsoft.com/wiki/contents/articles/2729.how-to-create-a-cloud-in-vmm-2012.aspx) (Création d’un cloud dans VMM 2012). |
| **Hyper-V** |Les serveurs hôtes Hyper-V doivent exécuter au moins Windows Server 2012 R2 avec le rôle Hyper-V activé ou Microsoft Hyper-V Server 2012 R2. Les dernières mises à jour doivent être installées.<br/><br/> Un serveur Hyper-V doivent avoir une ou plusieurs machines virtuelles.<br/><br/> Un serveur hôte ou un cluster Hyper-V comportant des machines virtuelles que vous souhaitez répliquer doit être géré dans un cloud VMM.<br/><br/>Les serveurs Hyper-V doivent être connectés à Internet, directement ou via un proxy.<br/><br/>Les correctifs décrits dans l’article de la base de connaissances [2961977](https://support.microsoft.com/kb/2961977) doivent être installés sur les serveurs Hyper-V.<br/><br/>Les serveurs hôte Hyper-V doivent pouvoir accéder à Internet pour gérer la réplication de données vers Azure. |
| **Fournisseur et agent** |Pendant un déploiement Azure Site Recovery, installez le fournisseur Azure Site Recovery sur le serveur VMM. Installez l’agent Recovery Services sur des hôtes Hyper-V. Le fournisseur et l’agent doivent se connecter à Azure directement via Internet ou via un proxy. Les proxies basés sur HTTPS ne sont pas pris en charge. Le serveur proxy sur le serveur VMM et les hôtes Hyper-V doit autoriser l’accès à : <br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>Si vous avez des règles de pare-feu fondées sur l’adresse IP sur le serveur VMM, vérifiez que ces règles autorisent la communication vers Azure.<br/><br/> Autorisez les [plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le protocole HTTPS (port 443).<br/><br/>Autorisez les plages d’adresses IP relatives à la région Azure de votre abonnement et à la région de l’Ouest des États-Unis (utilisées pour la gestion du contrôle d’accès et des identités).<br/><br/> |

### <a name="replicated-machine-prerequisites"></a>Configuration requise pour les machines répliquées

| **Composant** | **Détails** |
| --- | --- |
| **Machines virtuelles protégées** | Site Recovery fonctionne sur tous les systèmes d’exploitation pris en charge par [Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).<br/><br/>Les machines virtuelles doivent remplir les [conditions préalables Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) pour la création d’une machine virtuelle Azure. Le nom des machines doit contenir entre 1 et 63 caractères (lettres, chiffres et tirets). Le nom doit commencer et se terminer par une lettre ou un chiffre. <br/><br/>Le cas échéant, vous pouvez modifier le nom de la machine virtuelle une fois la réplication activée pour la machine virtuelle. <br/><br/> La capacité du disque pour chaque ordinateur protégé ne peut pas être supérieure à 1 023 Go. Une machine virtuelle peut comporter jusqu’à 16 disques (jusqu’à 16 To).<br/><br/>


## <a name="disaster-recovery-of-hyper-v-vms-in-vmm-clouds-to-a-customer-owned-site"></a>Récupération d’urgence vers le site d’un client de machines virtuelles Hyper-V hébergées dans des clouds VMM

Les composants suivants sont requis pour la récupération d’urgence sur le site d’un client de machines virtuelles Hyper-V dans les clouds VMM. Ils s’ajoutent à ceux décrits dans les [conditions requises pour Azure](#azure-requirements).

| **Composant** | **Détails** |
| --- | --- |
| **Virtual Machine Manager** |  Nous vous recommandons de déployer un serveur VMM sur le site principal et sur le site secondaire.<br/><br/> Vous pouvez [effectuer la réplication entre des clouds sur un seul serveur VMM](site-recovery-vmm-to-vmm.md#prepare-for-single-server-deployment). Pour effectuer une réplication entre clouds sur un seul serveur VMM, vous aurez besoin d’au moins deux clouds configurés sur le serveur VMM.<br/><br/> Les serveurs VMM doivent exécuter au moins System Center 2012 SP1 avec les dernières mises à jour.<br/><br/> Chaque serveur VMM doit disposer d’un ou de plusieurs clouds. Le profil Capacité Hyper-V doit être défini pour tous les clouds. <br/><br/>Les clouds doivent contenir un ou plusieurs groupes hôtes VMM. Pour plus d’informations sur la configuration des clouds VMM, voir [Préparer le déploiement d’Azure Site Recovery](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric). |
| **Hyper-V** | Les serveurs Hyper-V doivent exécuter au moins Windows Server 2012 avec le rôle Hyper-V activé, et les dernières mises à jour doivent être installées.<br/><br/> Un serveur Hyper-V doivent avoir une ou plusieurs machines virtuelles.<br/><br/>  Les serveurs hôtes Hyper-V doivent être situés dans des groupes hôtes dans les clouds VMM principaux et secondaires.<br/><br/> Si vous exécutez Hyper-V dans un cluster sous Windows Server 2012 R2, nous vous recommandons d’installer la mise à jour décrite dans l’article de la base de connaissances [2961977](https://support.microsoft.com/kb/2961977).<br/><br/> Si vous exécutez Hyper-V dans un cluster sous Windows Server 2012, notez que le répartiteur de clusters n’est pas créé automatiquement si vous avez un cluster basé sur des adresses IP statiques. Vous devez configurer manuellement le répartiteur de clusters. Pour plus d’informations sur le répartiteur de clusters, voir [Configure the replica broker role for cluster-to-cluster replication](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx) (Configurer le rôle de répartiteur de réplica pour la réplication de cluster à cluster). |
| **Fournisseur** | Pendant un déploiement Site Recovery, installez le fournisseur Azure Site Recovery sur les serveurs VMM. Le fournisseur communique avec Site Recovery sur HTTPS (port 443) pour orchestrer la réplication. La réplication des données a lieu entre les serveurs Hyper-V principaux et secondaires via le réseau local ou une connexion VPN.<br/><br/> Le fournisseur s’exécutant sur le serveur VMM a besoin d’accéder aux URL suivantes :<br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>Le fournisseur Site Recovery doit autoriser la communication à travers le pare-feu entre les serveurs VMM et les [plages d’adresses IP du centre de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et autoriser le protocole HTTPS (port 443). |


## <a name="url-access"></a>Accès URL
Ces URL doivent être disponibles depuis VMware, VMM et les serveurs hôtes Hyper-V :

|**URL** | **VMM vers VMM** | **VMM vers Azure** | **Hyper-V vers Azure** | **VMware vers Azure** |
|--- | --- | --- | --- | --- |
|``*.accesscontrol.windows.net`` | AUTORISER | Autoriser | Autoriser | Autoriser |
|``*.backup.windowsazure.com`` | Non requis | Autoriser | Autoriser | Autoriser |
|``*.hypervrecoverymanager.windowsazure.com`` | Autoriser | Autoriser | Autoriser | Autoriser |
|``*.store.core.windows.net`` | Autoriser | Autoriser | Autoriser | Autoriser |
|``*.blob.core.windows.net`` | Non requis | Autoriser | Autoriser | Autoriser |
|``https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi`` | Non requis | Non requis | Non requis | Autoriser le téléchargement SQL |
|``time.windows.com`` | AUTORISER | Autoriser | Autoriser | Autoriser|
|``time.nist.gov`` | Autoriser | Autoriser | Autoriser | AUTORISER |
