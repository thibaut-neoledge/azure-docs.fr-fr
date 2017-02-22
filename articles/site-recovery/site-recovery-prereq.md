---
title: "Conditions préalables pour la réplication vers Azure avec Azure Site Recovery | Microsoft Docs"
description: "Cet article résume les conditions préalables pour la réplication des machines virtuelles et des machines physiques vers Azure avec le service Azure Site Recovery."
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
ms.date: 12/11/2016
ms.author: rajanaki
translationtype: Human Translation
ms.sourcegitcommit: 46ea97f5c13b7befc78466fae92a737107be8f83
ms.openlocfilehash: 31cae0cf625552c0e9547d0e514c6017c6a179ef

---

#  <a name="prerequisites-for-replication-to-azure-using-azure-site-recovery"></a>Conditions préalables pour la réplication vers Azure avec Azure Site Recovery

 Site Recovery est un service Azure qui participe à votre stratégie de continuité des activités et de récupération d’urgence en orchestrant la réplication des machines virtuelles et des serveurs physiques locaux dans le cloud (Azure) ou sur un centre de données secondaire. Lorsque des pannes se produisent sur votre site principal, vous pouvez effectuer un basculement sur un site secondaire pour préserver la disponibilité des applications et des charges de travail. Vous pouvez restaurer votre site principal dès lors qu’il retrouve un fonctionnement normal. Pour en savoir plus, consultez [Qu’est-ce que Site Recovery ?](site-recovery-overview.md)

Cet article résume les conditions préalables pour lancer la réplication Site Recovery vers Azure.

Envoyez vos commentaires au bas de cet article ou posez vos questions techniques sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="azure-requirements"></a>Conditions requises pour Azure

**Prérequis** | **Détails**
--- | ---
**Compte Azure** | Un compte [Microsoft Azure](http://azure.microsoft.com/) .<br/><br/> Vous pouvez commencer par une version d’ [essai gratuit](https://azure.microsoft.com/pricing/free-trial/).
**Service Site Recovery** | [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification Site Recovery. |
**Stockage Azure** | Vous avez besoin d’un compte de stockage Azure pour stocker les données répliquées. Il doit se trouver dans la même région que le coffre Recovery Services. Les données répliquées sont stockées dans Azure Storage et les machines virtuelles Azure sont créées au moment du basculement.<br/><br/> En fonction du modèle de ressource que vous souhaitez utiliser pour les machines virtuelles Azure ayant fait l’objet d’un basculement, vous pouvez configurer un compte dans le [modèle Resource Manager](../storage/storage-create-storage-account.md) ou le [modèle Classic](../storage/storage-create-storage-account-classic-portal.md).<br></br>Vous pouvez utiliser le stockage [GRS](../storage/storage-redundancy.md#geo-redundant-storage) ou LRS. Nous vous recommandons d’utiliser un compte GRS, afin que les données soient résilientes si une panne se produit au niveau régional, ou si la région principale ne peut pas être récupérée.<br/><br/> Si vous répliquez des machines virtuelles VMware ou des serveurs physiques dans le portail Azure, le stockage Premium est pris en charge. Le [stockage Premium](https://docs.microsoft.com/en-us/azure/storage/storage-premium-storage) est généralement utilisé pour les machines virtuelles nécessitant des performances d’E/S élevées et une faible latence pour héberger les charges de travail nécessitant beaucoup d’E/S. Si vous utilisez un stockage Premium pour les données répliquées, vous avez également besoin d’un compte de stockage standard pour stocker les journaux de réplication qui capturent les modifications apportées en continu aux données locales.<br/><br/>
**Limites de stockage** | Vous ne pouvez pas déplacer les comptes de stockage utilisés par Site Recovery entre des groupes de ressources du même abonnement ou de différents abonnements.<br/><br/> La réplication des comptes de stockage Premium dans le centre et le sud de l’Inde n’est actuellement pas prise en charge.
**Réseau Azure** | Vous avez besoin d’un réseau Azure auquel les machines virtuelles Azure se connectent après le basculement. Ce réseau doit se trouver dans la même région que le coffre Recovery Services.<br/><br/> Dans le portail Azure, vous pouvez créer des réseaux dans le [modèle Ressource Manager](../virtual-network/virtual-networks-create-vnet-arm-pportal.md) ou dans le [modèle classique](../virtual-network/virtual-networks-create-vnet-classic-pportal.md).<br/><br/> Si vous répliquez à partir de VMM vers Azure, vous allez configurer [le mappage réseau](site-recovery-network-mapping.md) entre les réseaux VMM VM et Azure pour vous assurer que les machines virtuelles Azure se connectent aux réseaux appropriés après le basculement.
**Limitations du réseau** | Vous ne pouvez pas déplacer les comptes réseau utilisés par Site Recovery entre des groupes de ressources du même abonnement ou de différents abonnements.
**Mappage réseau** | Si vous répliquez des machines virtuelles Hyper-V dans des clouds VMM, vous devez configurer le [mappage réseau](site-recovery-network-mapping.md) afin que les machines virtuelles Azure soient connectées aux réseaux appropriés lorsqu’elles sont créées après le basculement.

>[!NOTE]
>Les sections suivantes décrivent les conditions préalables pour les différents composants dans l’environnement du client. Pour plus d’informations sur la prise en charge des configurations spécifiques, consultez le [tableau de prise en charge](site-recovery-support-matrix.md)
>

## <a name="disaster-recovery-of-vmware-virtual-machines-or-physical-windowslinux-servers-to-azure"></a>Récupération d’urgence dans Azure de machines virtuelles VMware ou de serveurs Windows/Linux physiques
Voici les composants nécessaires pour la récupération d’urgence de machines virtuelles VMware ou de serveurs Windows/Linux physiques en plus de ceux mentionnés [ci-dessus](#Azure requirements).


1. **Serveur de configuration ou serveur de traitement supplémentaire** : vous devez configurer un ordinateur local en tant que serveur de configuration pour coordonner les communications entre le site local et Azure, et pour gérer la réplication de données. <br></br>

2. **Hôte VMware vCenter/vSphere**

| **Composant** | **Configuration requise** |
| --- | --- |
| **vSphere** | Un ou de plusieurs hyperviseurs VMware vSphere.<br/><br/>Les hyperviseurs doivent exécuter vSphere version 6.0, 5.5 ou 5.1 avec les dernières mises à jour.<br/><br/>Nous vous recommandons de placer les hôtes vSphere et les serveurs vCenter sur le même réseau que le serveur de processus (le réseau dans lequel se trouve le serveur de configuration, sauf si vous avez configuré un serveur de processus dédié). |
| **vCenter** | Nous vous recommandons de déployer un serveur VMware vCenter pour gérer vos hôtes vSphere. Ce dernier doit exécuter vCenter version 6.0 ou 5.5 avec les dernières mises à jour.<br/><br/>**Limitation** : Site Recovery ne prend pas en charge les nouvelles fonctionnalités de vCenter et vSphere 6.0, telles que Cross vCenter vMotion, les volumes virtuels et Storage DRS. La prise en charge de Site Recovery est limitée aux fonctionnalités qui étaient déjà disponibles dans la version 5.5.||

3. **Configuration requise pour les machines répliquées**


| **Composant** | **Configuration requise** |
| --- | --- |
| **Machines virtuelles VMware locales** | Les outils VMware doivent être installés sur les machines virtuelles répliquées, et elles doivent être en cours d’exécution.<br/><br/> Les machines virtuelles doivent répondre à la [configuration requise Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) pour la création de machines virtuelles Azure.<br/><br/>Sur les machines protégées, la capacité d’un disque ne doit pas dépasser 1023 Go. <br/><br/>2 Go minimum d’espace disponible sur le lecteur d’installation pour l’installation du composant.<br/><br/>Le **port 20004** doit être ouvert sur le pare-feu local de machine virtuelle si vous souhaitez activer la cohérence de plusieurs machines virtuelles.<br/><br/>Le nom des machines doit contenir entre 1 et 63 caractères (lettres, chiffres et tirets). Le nom doit commencer par une lettre ou un chiffre et se terminer par une lettre ou un chiffre. Après avoir activé la réplication d’une machine, vous pouvez modifier le nom Azure.<br/><br/> |
| **Ordinateurs Windows (serveur physique ou VMware)** | L’ordinateur doit exécuter un système d’exploitation 64 bits pris en charge : Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2, avec au moins SP1.<br/><br/> Le système d’exploitation doit être installé sur le lecteur C:\. Le disque du système d’exploitation doit être un disque de base Windows, et non un disque dynamique. Le disque de données peut être dynamique.<br/><br/>|
| **Ordinateurs Linux** (serveur physique ou VMware) | Vous devez disposer d’un système d’exploitation 64 bits compatible : Red Hat Enterprise Linux 6.7,7.1,7.2 ; Centos 6.5, 6.6,6.7,7.0,7.1,7.2 ; Oracle Enterprise Linux 6.4, 6.5 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>Les fichiers /etc/hosts se trouvant sur les ordinateurs protégés doivent contenir des entrées qui mappent le nom d’hôte local aux adresses IP associées à toutes les cartes réseau.<br/><br/>Si vous souhaitez vous connecter à une machine virtuelle Azure exécutant Linux après le basculement à l’aide d’un client Secure Shell (ssh), assurez-vous que le service Secure Shell sur l’ordinateur protégé est configuré pour démarrer automatiquement au démarrage du système et que les règles de pare-feu autorisent une connexion ssh à cet ordinateur.<br/><br/>Le nom d'hôte, les points de montage, les noms de périphériques et les chemins d'accès système et les noms de fichiers Linux (par exemple /etc/; /usr) doivent uniquement être en anglais.<br/><br/>

## <a name="disaster-recovery-of-hyper-v-virtual-machines-to-azure-no-vmm"></a>Récupération d’urgence dans Azure de machines virtuelles (pas de VMM)

Voici les composants nécessaires à récupération d’urgence de machines virtuelles Hyper-V dans des clouds VMM en plus de ceux mentionnés [ci-dessus](#Azure requirements).

| **Configuration requise** | **Détails** |
| --- | --- |
| **Hôte Hyper-V** |Un ou plusieurs serveurs locaux exécutant Windows Server 2012 R2 avec les dernières mises à jour et le rôle Hyper-V activé ou Microsoft Hyper-V Server 2012 R2.<br></br>Le serveur Hyper-V doit contenir une ou plusieurs machines virtuelles.<br></br>Les serveurs Hyper-V doivent être connectés à Internet, directement ou via un proxy.<br></br>Les correctifs mentionnés dans le document [KB2961977](https://support.microsoft.com/en-us/kb/2961977) doivent être installés sur les serveurs Hyper-V.
|**Fournisseur et agent**| Pendant le déploiement d’Azure Site Recovery, vous devez installer le fournisseur Azure Site Recovery. Dans le cadre de l’installation du fournisseur, l’agent Azure Recovery Services est installé sur chaque serveur Hyper-V exécutant les machines virtuelles que vous souhaitez protéger. <br></br>Tous les serveurs Hyper-V d’un coffre Site Recovery doivent disposer des mêmes versions du fournisseur et de l’agent.<br></br>Le fournisseur devra se connecter à Azure Site Recovery via Internet. Le trafic peut être envoyé directement ou via un proxy. Notez que HTTPS basé sur proxy n’est pas pris en charge. Le serveur proxy doit autoriser l’accès aux éléments suivants : <br/><br/> [!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)]<br/><br/>Si vous avez des règles de pare-feu fondées sur l’adresse IP sur le serveur, vérifiez que ces règles autorisent la communication vers Azure.<br></br> Autorisez les [plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le port HTTPS (443).<br></br> Autorisez les plages d’adresses IP relatives à la région de votre abonnement Azure et à la région des États-Unis de l’Ouest (utilisées pour la gestion du contrôle d’accès et des identités).


## <a name="disaster-recovery-of-hyper-v-virtual-machines-in-vmm-clouds-to-azure"></a>Récupération d’urgence dans Azure de machines virtuelles Hyper-V hébergées dans des clouds VMM

Voici les composants nécessaires à récupération d’urgence de machines virtuelles Hyper-V dans des clouds VMM en plus de ceux mentionnés [ci-dessus](#Azure requirements).

| **Configuration requise** | **Détails** |
| --- | --- |
| **VMM** |Un ou plusieurs serveurs VMM s’exécutant sous **System Center 2012 R2 ou version ultérieure**. Chaque serveur VMM doit être associé à un ou plusieurs clouds configurés. <br></br>Un cloud doit contenir :<br>- Un ou plusieurs groupes hôtes VMM.<br/>- Un ou plusieurs serveurs hôtes Hyper-V ou clusters dans chaque groupe hôte.<br/><br/>[En savoir plus](http://social.technet.microsoft.com/wiki/contents/articles/2729.how-to-create-a-cloud-in-vmm-2012.aspx) sur la configuration des clouds VMM. |
| **Hyper-V** |Les serveurs hôtes Hyper-V doivent exécuter au moins **Windows Server 2012 R2** avec le rôle Hyper-V ou **Microsoft Hyper-V Server 2012 R2** et les dernières mises à jour doivent être installées.<br/><br/> Un serveur Hyper-V doit contenir au moins une machine virtuelle.<br/><br/> Un serveur hôte ou un cluster Hyper-V comportant des machines virtuelles que vous souhaitez répliquer doit être géré dans un cloud VMM.<br/><br/>Les serveurs Hyper-V doivent être connectés à Internet, directement ou via un proxy.<br/><br/>Les correctifs mentionnés dans l’article [2961977](https://support.microsoft.com/kb/2961977) doivent être installés sur les serveurs Hyper-V.<br/><br/>Les serveurs hôte Hyper-V doivent pouvoir accéder à Internet pour gérer la réplication de données vers Azure. |
| **Fournisseur et agent** |Lors du déploiement d’Azure Site Recovery, vous installez le fournisseur Azure Site Recovery sur le serveur VMM et l’agent Recovery Services sur les hôtes Hyper-V. Le fournisseur et l’agent doivent se connecter à Azure via Internet, directement ou via un proxy. Les proxies basés sur HTTPS ne sont pas pris en charge. Le serveur proxy sur le serveur VMM et les hôtes Hyper-V doit autoriser l’accès à : <br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>Si le serveur VMM inclut des règles de pare-feu basées sur l’adresse IP, vérifiez qu’elles autorisent la communication vers Azure.<br/><br/> Autorisez [les plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et le port HTTPS (443).<br/><br/> Autorisez les plages d’adresses IP relatives à la région de votre abonnement Azure et à la région des États-Unis de l’Ouest (utilisées pour la gestion du contrôle d’accès et des identités).<br/><br/> |

### <a name="replicated-machine-prerequisites"></a>Configuration requise pour les machines répliquées
| **Composant** | **Détails** |
| --- | --- |
| **Machines virtuelles protégées** | Site Recovery fonctionne sur tous les systèmes d’exploitation pris en charge par [Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).<br></br>Les machines virtuelles doivent répondre à la [configuration requise Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) pour la création de machines virtuelles Azure. Le nom des machines doit contenir entre 1 et 63 caractères (lettres, chiffres et tirets). Le nom doit commencer par une lettre ou un chiffre et se terminer par une lettre ou un chiffre. <br></br>Le cas échéant, vous pouvez modifier le nom une fois la réplication activée pour la machine virtuelle. <br/><br/> Sur les machines protégées, la capacité d’un disque ne doit pas dépasser 1 023 Go. Une machine virtuelle peut comporter jusqu’à 16 disques (jusqu’à 16 To).<br/><br/>


## <a name="disaster-recovery-of-hyper-v-virtual-machines-in-vmm-clouds-to-a-customer-owned-site"></a>Récupération d’urgence vers le site d’un client de machines virtuelles Hyper-V hébergées dans des clouds VMM

Voici les composants nécessaires à la récupération d’urgence vers un site client de machines virtuelles Hyper-V dans des clouds VMM en plus de ceux mentionnés [ci-dessus](#Azure requirements).

| **Composants** | **Détails** |
| --- | --- |
| **VMM** |  Nous vous recommandons de déployer un serveur VMM dans le site principal et un autre dans le site secondaire.<br/><br/> Vous pouvez [effectuer la réplication entre des clouds sur un seul serveur VMM](site-recovery-single-vmm.md). Pour ce faire, vous avez besoin d’au moins deux clouds configurés sur le serveur VMM.<br/><br/> Les serveurs VMM doivent exécuter **au moins System Center 2012 SP1** avec les dernières mises à jour.<br/><br/> Chaque serveur VMM doit disposer d’au moins un cloud. Le profil Capacité Hyper-V doit être défini pour tous les clouds. <br/><br/>Les clouds doivent contenir un ou plusieurs groupes hôtes VMM. [En savoir plus](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric) sur la configuration des clouds VMM.<br/><br/> Les serveurs VMM doivent avoir accès à Internet. |
| **Hyper-V** | Les serveurs Hyper-V doivent exécuter **au moins Windows Server 2012 avec le rôle Hyper-V** et les dernières mises à jour doivent être installées.<br/><br/> Un serveur Hyper-V doit contenir au moins une machine virtuelle.<br/><br/>  Les serveurs hôtes Hyper-V doivent être situés dans des groupes hôtes dans les clouds VMM principaux et secondaires.<br/><br/> Si vous exécutez Hyper-V dans un cluster Windows Server 2012 R2, vous devez installer la [mise à jour 2961977](https://support.microsoft.com/kb/2961977).<br/><br/> Si vous exécutez Hyper-V dans un cluster sous Windows Server 2012, notez que le répartiteur de clusters n’est pas créé automatiquement si vous avez un cluster basé sur des adresses IP statiques. Vous devez configurer manuellement le service Broker de cluster. [En savoir plus](http://social.technet.microsoft.com/wiki/contents/articles/18792.configure-replica-broker-role-cluster-to-cluster-replication.aspx). |
| **Fournisseur** | Pendant un déploiement de Site Recovery, vous installez le fournisseur Azure Site Recovery sur des serveurs VMM. Le fournisseur communique avec Site Recovery par le biais de HTTPS 443 pour orchestrer la réplication. La réplication des données a lieu entre les serveurs Hyper-V principaux et secondaires par le biais du réseau local ou d’une connexion VPN.<br/><br/> Le fournisseur s’exécutant sur le serveur VMM a besoin d’accéder aux URL suivantes :<br/><br/>[!INCLUDE [site-recovery-URLS](../../includes/site-recovery-URLS.md)] <br/><br/>Autorisez la communication de pare-feu des serveurs VMM vers les [plages IP de centre de données Azure](https://www.microsoft.com/download/confirmation.aspx?id=41653) et autorisez le protocole HTTPS (443). |



<!--HONumber=Jan17_HO2-->


