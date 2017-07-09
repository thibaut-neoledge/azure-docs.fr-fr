---
title: "Répliquer des serveurs physiques et des machines virtuelles VMware sur Azure dans le portail Classic | Microsoft Docs"
description: "Cet article explique comment déployer Azure Site Recovery pour orchestrer la réplication, le basculement et la récupération de machines virtuelles VMware ou de serveurs physiques Windows/Linux locaux vers Azure."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: jwhit
editor: 
ms.assetid: a9022c1f-43c1-4d38-841f-52540025fb46
ms.service: site-recovery
ms.workload: storage-backup-recovery
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/23/2017
ms.author: raynew
ROBOTS: NOINDEX, NOFOLLOW
redirect_url: site-recovery-vmware-to-azure
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 73c3fb5cf4056ddb9554f598ec7f173d81802f17
ms.contentlocale: fr-fr
ms.lasthandoff: 06/28/2017


---

# <a name="replicate-vmware-virtual-machines-and-physical-servers-to-azure-with-azure-site-recovery"></a>Répliquer des machines virtuelles VMware et des serveurs physiques sur Azure avec Azure Site Recovery
> [!div class="op_single_selector"]
> * [Le portail Azure](site-recovery-vmware-to-azure.md)
> * [Le portail Classic](site-recovery-vmware-to-azure-classic.md)
> * [Le portail Classic (version héritée)](site-recovery-vmware-to-azure-classic-legacy.md)
>
>

Le service Azure Site Recovery contribue à votre stratégie de continuité d’activité et de récupération d’urgence en coordonnant la réplication, le basculement et la récupération des machines virtuelles et des serveurs physiques. Les machines peuvent être répliquées vers Azure ou vers un centre de données local secondaire. Pour une présentation rapide de ce service, consultez l’article [Qu’est-ce qu’Azure Site Recovery ?](site-recovery-overview.md).

## <a name="overview"></a>Vue d’ensemble
Cet article explique comment :

* **Répliquer les machines virtuelles VMware dans Azure** : déployez Site Recovery pour coordonner la réplication, le basculement et la récupération des machines virtuelles VMware locales vers Azure Storage.
* **Répliquer les serveurs physiques dans Azure** : déployez Azure Site Recovery pour coordonner la réplication, le basculement et la récupération des serveurs physiques Windows et Linux locaux vers Azure.

> [!NOTE]
> Cet article décrit comment exécuter une réplication sur Azure Si vous souhaitez répliquer des machines virtuelles VMware ou des serveurs physiques Windows/Linux sur un centre de données secondaire, consultez [Site Recovery VMware to VMware](site-recovery-vmware-to-vmware.md) (Site Recovery - VMware vers VMware).
>
>

Publiez vos commentaires ou vos questions au bas de cet article ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## <a name="enhanced-deployment"></a>Déploiement amélioré
Cet article contient des instructions relatives à un déploiement amélioré dans le portail Azure Classic. Nous vous conseillons d’utiliser cette version pour tous les nouveaux déploiements. Si vous avez déjà procédé au déploiement à l’aide de l’ancienne version, nous vous conseillons de migrer vers la nouvelle version. Pour plus d’informations sur la migration, consultez [Site Recovery VMware to Azure classic legacy](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) (Site Recovery - VMware vers Azure Classic (version héritée).

Le déploiement amélioré est une mise à jour majeure. Voici un récapitulatif des améliorations que nous avons apportées :

* **Aucune machine virtuelle d’infrastructure dans Azure**: les données se répliquent directement dans un compte de stockage Azure. En outre, il n’est pas nécessaire de configurer des machines virtuelles d’infrastructure (serveur de configuration ou serveur cible maître, par exemple) pour la réplication et le basculement, comme cela était le cas dans le déploiement hérité.  
* **Installation unifiée**: une seule installation offre une configuration simple et l’extensibilité des composants locaux.
* **Déploiement sécurisé** : l’ensemble du trafic est chiffré et les communications de gestion de la réplication sont envoyées par le biais du port HTTPS 443.
* **Points de récupération** : prise en charge des points de récupération après incident cohérents avec les applications pour les environnements Windows et Linux, et prise en charge des configurations avec machine virtuelle unique et machines virtuelles multiples.
* **Test de basculement** : prise en charge du basculement de test sans interruption de service vers Azure, sans impact sur la production ou suspension de la réplication.
* **Basculement non planifié** : prise en charge du basculement non planifié vers Azure avec option améliorée permettant d’arrêter automatiquement les machines virtuelles avant le basculement.
* **Restauration automatique**: restauration intégrée qui réplique uniquement les changements différentiels vers le site local.
* **vSphere 6.0** : prise en charge limitée des déploiements VMware vSphere 6.0.

## <a name="how-does-site-recovery-help-protect-virtual-machines-and-physical-servers"></a>Comment Site Recovery permet-il de protéger des serveurs physiques et virtuels ?
* Les administrateurs VMware peuvent configurer des dispositifs de protection hors site pour protéger Azure des charges de travail et des applications métier hors site sur les machines virtuelles VMware. Les gestionnaires de serveur peuvent répliquer les serveurs physiques Windows et Linux locaux vers Azure.
* La console Azure Site Recovery fournit un emplacement unique pour une installation et une gestion simples des processus de réplication, de basculement et de récupération.
* Si vous répliquez des machines virtuelles VMware gérées par un serveur vCenter, Site Recovery peut automatiquement détecter ces machines virtuelles. Si les machines se trouvent sur un hôte ESXi, Site Recovery détecte les machines virtuelles sur l’hôte.
* Si vous exécutez un basculement simple de votre infrastructure locale vers Azure, vous pouvez effectuer une restauration automatique d’Azure vers les serveurs de machines virtuelles VMware sur le site local.
* Vous pouvez configurer les plans de récupération qui regroupent des charges de travail d’application hiérarchisées sur plusieurs machines. Si vous basculez sur ces plans, Site Recovery assure une cohérence multimachine virtuelle, de sorte que les machines exécutant les mêmes charges de travail peuvent être récupérées ensemble sur un point de données cohérent.

## <a name="supported-operating-systems"></a>Systèmes d’exploitation pris en charge
### <a name="windows-64-bit-only"></a>Windows (64 bits uniquement)
* Windows Server 2008 R2 SP1+
* Windows Server 2012
* Windows Server 2012 R2

### <a name="linux-64-bit-only"></a>Linux (64 bits uniquement)
* Red Hat Enterprise Linux 6.7, 7.1 et 7.2
* CentOS 6.5, 6.6, 6.7, 7.0, 7.1 et 7.2
* Oracle Enterprise Linux 6.4 et 6.5 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3)
* SUSE Linux Enterprise Server 11 SP3

## <a name="scenario-architecture"></a>Architecture du scénario
Composants du scénario :

* **Un serveur d’administration local**: le serveur d’administration exécute les composants Site Recovery :
  * **Serveur de configuration** : coordonne la communication et gère les processus de réplication et de récupération des données.
  * **Serveur de traitement** : fait office de passerelle de réplication. Il reçoit les données de machines sources protégées, les optimise grâce à une mise en cache, à une compression et à un chiffrement, et envoie les données de réplication vers le stockage Azure. Il gère également l’installation Push du service Mobilité sur des machines protégées et assure la détection automatique des machines virtuelles VMware.
  * **Serveur cible maître**: gère les données de réplication pendant la restauration automatique à partir d’Azure.
    Vous pouvez également déployer un serveur d’administration se comportant simplement comme un serveur de traitement afin de mettre à l’échelle votre déploiement.
* **Service Mobilité**: ce composant est déployé sur chaque ordinateur (machine virtuelle VMware ou serveur physique) que vous souhaitez répliquer vers Azure. Il enregistre les écritures de données sur la machine et les transmet au serveur de traitement.
* **Azure**: vous n’avez pas besoin de créer de machines virtuelles Azure pour gérer la réplication et le basculement. Le service Site Recovery assure la gestion, puis la réplication des données directement sur le stockage Azure. Les machines virtuelles Azure répliquées sont automatiquement tournées uniquement en cas de basculement vers Azure. Toutefois, si vous souhaitez effectuer une restauration à partir d’Azure vers le site local, vous devez configurer une machine virtuelle Azure en tant que serveur de traitement.

Le schéma suivant (créé par Henry Robalino) montre comment ces composants interagissent :

![Architecture des composants](./media/site-recovery-vmware-to-azure/v2a-architecture-henry.png)

## <a name="capacity-planning"></a>Planification de la capacité
Lorsque vous planifiez vos capacités, vous devez prendre en compte les environnements suivants :

* **Environnement source** : planification de la capacité ou exigences de l’infrastructure et des machines VMware source.
* **Serveur d’administration** : planification des serveurs d’administration locaux qui exécutent des composants Site Recovery.
* **Bande passante réseau entre la source et la cible** : planification de la bande passante réseau requise pour la réplication entre la source et Azure.

### <a name="source-environment-considerations"></a>Considérations relatives à l’environnement source
* **Taux de modification quotidien maximum** : une machine protégée ne peut utiliser qu’un serveur de traitement. Un serveur de traitement unique peut prendre en charge jusqu’à 2 To de données modifiées par jour. Par conséquent, le taux de modification de données quotidien maximum pris en charge pour une machine protégée est égal à 2 To.
* **Débit maximum** : une machine répliquée peut appartenir à un compte de stockage dans Azure. Un compte de stockage standard peut gérer un maximum de 20 000 requêtes par seconde, et nous vous recommandons de conserver le nombre d’IOPS sur un ordinateur source à 20 000. Par exemple, si vous disposez d’une machine source à 5 disques et si chaque disque génère 120 IOPS (taille de 8 Ko) sur la source, la limite de 500 IOPS par disque d’Azure est respectée. Nombre de comptes de stockage requis = nombre total d’IOPS source / 20 000.

### <a name="management-server-considerations"></a>Considérations relatives aux serveurs d’administration
Le serveur d’administration exécute les composants de Site Recovery qui gèrent l’optimisation, la réplication et la gestion des données. Il doit être en mesure de gérer la capacité de taux de modification quotidien pour toutes les charges de travail en cours d’exécution sur des machines protégées et bénéficier de suffisamment de bande passante pour la réplication continue des données vers le stockage Azure. Plus précisément :

* Le serveur de traitement reçoit les données de réplication des machines protégées et les optimise grâce à une mise en cache, à une compression et à un chiffrement avant de les transmettre à Azure. Le serveur d’administration doit disposer de suffisamment de ressources pour effectuer ces tâches.
* Le serveur de processus utilise le cache disque. Nous recommandons un disque de cache distinct de 600 Go ou plus pour gérer les modifications apportées aux données stockées en cas de goulot d’étranglement ou de panne. Au cours du déploiement, vous pouvez configurer le cache sur un lecteur disposant d’au moins 5 Go de stockage disponibles, mais 600 Go est la quantité minimale recommandée.
* À titre de meilleure pratique, nous conseillons de placer le serveur d’administration sur le même réseau et le même segment de réseau local que les machines à protéger. Il peut se trouver sur un réseau différent, mais les machines à protéger doivent présenter une visibilité de réseau L3.

Les recommandations en matière de taille du serveur d’administration sont résumées dans le tableau qui suit :

| **Unité centrale de serveur d’administration** | **Mémoire** | **Taille du disque cache** | **Taux de modification des données** | **Machines protégées** |
| --- | --- | --- | --- | --- |
| 8 processeurs virtuels (2 sockets * 4 cœurs à 2,5 GHz) |16 Go |300 Go |500 Go ou moins |Déployer un serveur d’administration avec ces paramètres pour répliquer moins de 100 machines. |
| 12 processeurs virtuels (2 sockets * 6 cœurs à 2,5 GHz) |18 Go |600 Go |500 Go à 1 To |Déployer un serveur d’administration avec ces paramètres pour répliquer 100 à 150 machines. |
| 16 processeurs virtuels (2 sockets * 8 cœurs à 2,5 GHz) |32 Go |1 To |1 To à 2 To |Déployer un serveur d’administration avec ces paramètres pour répliquer 150 à 200 machines. |
| Déployer un autre serveur de traitement | | |> 2 To |Déployez des serveurs de traitement supplémentaires si vous effectuez la réplication de plus de 200 ordinateurs, ou si le taux de changement des données quotidien dépasse 2 To. |

Où :

* Chaque ordinateur source est configuré avec 3 disques de 100 Go chacune.
* Nous avons utilisé le stockage de référence de 8 disques SAS de 10 000 tr/min avec RAID 10 pour les mesures de disque cache.

### <a name="network-bandwidth-from-source-to-target"></a>Bande passante réseau entre la source et la cible
Veillez à calculer la bande passante requise pour la réplication initiale et la réplication différentielle en utilisant l’[outil Capacity Planner](site-recovery-capacity-planner.md).

#### <a name="throttling-bandwidth-used-for-replication"></a>Limitation de la bande passante utilisée pour la réplication
Le trafic de VMware répliqué dans Azure est envoyé via un serveur de traitement spécifique. Vous pouvez limiter la bande passante disponible pour la réplication avec Site Recovery sur le serveur comme suit :

1. Ouvrez le composant logiciel enfichable Microsoft Azure Backup sur le serveur d’administration principal ou sur un serveur d’administration exécutant des serveurs de traitement supplémentaire mis en service. Par défaut, un raccourci pour Sauvegarde Microsoft Azure est créé sur le bureau. Vous le trouverez également sous C:\Program Files\Microsoft Azure Recovery Services Agent\bin\wabadmin.
2. Dans le composant logiciel enfichable, cliquez sur **Modifier les propriétés**.

    ![Modifier les propriétés de limitation de la bande passante](./media/site-recovery-vmware-to-azure-classic/throttle1.png)
3. Sous l’onglet **Limitation**, spécifiez la bande passante utilisable pour la réplication Site Recovery et la planification applicable.

    ![Limiter la bande passante utilisée pour la réplication](./media/site-recovery-vmware-to-azure-classic/throttle2.png)

Vous pouvez également définir une limitation à l’aide de PowerShell. Voici un exemple :

    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

#### <a name="maximizing-bandwidth-usage"></a>Optimisation de l’utilisation de la bande passante
Pour modifier la bande passante utilisée pour la réplication par Azure Site Recovery, vous devez modifier une clé de Registre.

La clé qui suit contrôle le nombre de threads utilisés pendant la réplication du disque :

    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM

 Dans un réseau surapprovisionné, cette clé de Registre doit être modifiée par rapport aux valeurs par défaut. Nous prenons en charge un maximum de 32 threads.  

Pour plus d’informations sur la planification détaillée de la capacité, consultez [Site Recovery Capacity Planner](site-recovery-capacity-planner.md).

### <a name="additional-process-servers"></a>Serveurs de traitement supplémentaires
Si vous souhaitez protéger plus de 200 machines ou si le taux de modification quotidien est supérieur à 2 To, vous pouvez ajouter des serveurs supplémentaires pour gérer la charge. Pour augmenter la taille des instances, vous pouvez :

* Augmenter le nombre de serveurs d’administration. Par exemple, vous pouvez protéger jusqu’à 400 machines avec deux serveurs d’administration.
* Ajouter des serveurs de traitement supplémentaires et les utiliser pour gérer le trafic à la place (ou en plus) du serveur d’administration.

Ce tableau décrit un scénario dans lequel :

* Vous pouvez configurer le serveur d’administration d’origine pour l’utiliser uniquement en tant que serveur de configuration.
* Vous pouvez configurer un serveur de traitement supplémentaire.
* Vous pouvez configurer des machines virtuelles protégées pour utiliser le serveur de traitement supplémentaire.
* Chaque ordinateur source protégé est configuré avec trois disques de 100 Go chacun.

| **Serveur d’administration d’origine**<br/><br/>(serveur de configuration) | **Serveur de traitement supplémentaire** | **Taille du disque cache** | **Taux de modification des données** | **Machines protégées** |
| --- | --- | --- | --- | --- |
| 8 processeurs virtuels (2 sockets * 4 cœurs à 2,5 GHz), 16 Go de RAM |4 processeurs virtuels (2 sockets * 2 cœurs à 2,5 GHz), 8 Go de RAM |300 Go |250 Go ou moins |Vous pouvez répliquer 85 machines ou moins. |
| 8 processeurs virtuels (2 sockets * 4 cœurs à 2,5 GHz), 16 Go de RAM |8 processeurs virtuels (2 sockets * 4 cœurs à 2,5 GHz), 12 Go de RAM |600 Go |250 Go à 1 To |Vous pouvez répliquer de 85 à 150 machines. |
| 12 processeurs virtuels (2 sockets * 6 cœurs à 2,5 GHz), 18 Go de RAM |12 processeurs virtuels (2 sockets * 6 cœurs à 2,5 GHz), 24 Go de RAM |1 To |1 To à 2 To |Vous pouvez répliquer de 150 à 225 machines. |

Le mode de mise à l’échelle de vos serveurs dépend de votre préférence pour un modèle de montée en puissance ou de montée en charge. La montée en puissance consiste à déployer quelques serveurs de gestion et de processus haut de gamme, tandis que la montée en charge implique le déploiement d’un plus grand nombre de serveurs avec moins de ressources. Par exemple, si vous avez besoin de protéger 220 machines, vous pouvez procéder de l’une ou l’autre des façons suivantes :

* Configurer le serveur d’administration d’origine avec 12 processeurs virtuels et 18 Go de RAM. Configurer un serveur de traitement supplémentaire avec 12 processeurs virtuels et 24 Go de RAM. Configurer les machines protégées de sorte qu’elles utilisent uniquement le serveur de traitement supplémentaire.
* Configurer deux serveurs d’administration (2 × 8 processeurs virtuels, 16 Go de RAM) et deux serveurs de traitement supplémentaires (1 × 8 processeurs virtuels et 4 processeurs virtuels × 1 pour gérer les 135 + 85 (220) machines). Configurer les machines protégées de sorte qu’elles utilisent uniquement les serveurs de traitement supplémentaires.

Pour configurer un serveur de traitement supplémentaire, suivez les instructions de la section [Déployer d’autres serveurs de traitement](#deploy-additional-process-servers).

## <a name="before-you-start-deployment"></a>Avant de lancer le déploiement
Les tableaux suivants résument les conditions préalables pour le déploiement de ce scénario.

### <a name="azure-prerequisites"></a>Conditions préalables pour Azure
| **Configuration requise** | **Détails** |
| --- | --- |
| **Compte Azure** |Vous avez besoin d’un compte [Microsoft Azure](https://azure.microsoft.com/) . Vous pouvez commencer par une version d’ [essai gratuit](https://azure.microsoft.com/pricing/free-trial/). Pour plus d’informations sur la tarification de Site Recovery, consultez la page [Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/). |
| **Stockage Azure** |Vous avez besoin d’un compte de stockage Azure pour stocker les données répliquées. Les données répliquées sont stockées dans le stockage Azure et les machines virtuelles Azure sont lancées au moment du basculement. <br/><br/>Vous avez besoin d’un [compte de stockage géoredondant standard](../storage/storage-redundancy.md#geo-redundant-storage). Le compte doit se trouver dans la même région que le service Site Recovery et être associé au même abonnement. La réplication vers des comptes de stockage Premium n’est pas prise en charge pour le moment et ne doit pas être utilisée.<br/><br/>Nous ne prenons pas en charge le déplacement des comptes de stockage créés à l’aide du [portail Azure](../storage/storage-create-storage-account.md) dans les groupes de ressources. Pour plus d’informations, consultez l’article [Introduction à Microsoft Azure Storage](../storage/storage-introduction.md).<br/><br/> |
| **Réseau Azure** |Vous avez besoin d’un réseau virtuel Azure auquel les machines virtuelles Azure se connecteront au moment du basculement. Le réseau virtuel Azure doit se trouver dans la même région que le coffre Site Recovery.<br/><br/>Pour une restauration automatique après le basculement vers Azure, vous devez disposer d’une connexion VPN (ou Azure ExpressRoute) configurée du réseau Azure vers le site local. |

### <a name="on-premises-prerequisites"></a>Conditions préalables locales
| **Configuration requise** | **Détails** |
| --- | --- |
| **Serveur d’administration** |Vous avez besoin d’un serveur Windows 2012 R2 local s’exécutant sur une machine virtuelle ou un serveur physique. Tous les composants Site Recovery locaux sont installés sur ce serveur d’administration.<br/><br/> Nous vous conseillons de déployer le serveur en tant que machine virtuelle VMware hautement disponible. La restauration automatique vers le site local à partir d’Azure doit toujours s’effectuer vers des machines virtuelles VMware, que vous ayez basculé sur des machines virtuelles ou des serveurs physiques. Si vous ne configurez pas le serveur d’administration en tant que machine virtuelle VMware, vous devez configurer un serveur cible maître distinct en tant que machine virtuelle VMware pour recevoir le trafic de la restauration automatique.<br/><br/>Le serveur ne doit pas être un contrôleur de domaine.<br/><br/>Le serveur doit avoir une adresse IP statique.<br/><br/>Le nom d’hôte du serveur doit être de 15 caractères ou moins.<br/><br/>Les paramètres régionaux du système doivent être en anglais uniquement.<br/><br/>Le serveur d’administration requiert un accès à Internet.<br/><br/>Vous devez disposer d’un accès en sortie depuis le serveur comme suit : un accès provisoire sur le port HTTP 80 lors de l’installation des composants de Site Recovery (pour télécharger MySQL) ; un accès sortant permanent sur le port HTTPS 443 pour la gestion de la réplication ; un accès sortant permanent sur le port HTTPS 9443 pour le trafic de réplication (ce port peut être modifié).<br/><br/> Assurez-vous que ces URL sont accessibles depuis le serveur d’administration : <br/>- \*.hypervrecoverymanager.windowsazure.com<br/>- \*.accesscontrol.windows.net<br/>- \*.backup.windowsazure.com<br/>- \*.blob.core.windows.net<br/>- \*.store.core.windows.net<br/>- https://www.msftncsi.com/ncsi.txt<br/>- [ https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi")<br/><br/>Si vous avez des règles de pare-feu fondées sur l’adresse IP sur le serveur, vérifiez que ces règles autorisent la communication vers Azure. Vous devez autoriser [les plages d’adresses IP de centres de données Azure](https://www.microsoft.com/download/details.aspx?id=41653) et le port HTTPS (443). Vous devez également inscrire sur la liste approuvée les plages d’adresses IP pour la région Azure de votre abonnement et pour les États-Unis de l’Ouest. L’URL [https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi " https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi") permet de télécharger MySQL. |
| **Hôte VMware ESXi/vCenter** |Pour gérer vos machines virtuelles VMware, vous devez disposer d’un ou de plusieurs hyperviseurs VMware vSphere ESX/ESXi exécutant ESX/ESXi version 6.0, 5.5 ou 5.1 avec les dernières mises à jour.<br/><br/> Nous vous recommandons de déployer un serveur VMware vCenter pour gérer vos hôtes ESXi. Ce dernier doit exécuter vCenter version 6.0 ou 5.5 avec les dernières mises à jour.<br/><br/>Notez que Site Recovery ne prend pas en charge les nouvelles fonctionnalités de vCenter et vSphere 6.0, telles que Cross vCenter vMotion, les volumes virtuels et Storage DRS. La prise en charge de Site Recovery est limitée aux fonctionnalités qui étaient déjà disponibles dans la version 5.5. |
| **Machines protégées** |**Microsoft Azure**<br/><br/>Les machines à protéger doivent respecter les [conditions préalables pour Azure](site-recovery-prereq.md) pour la création de machines virtuelles Azure.<br><br/>Si vous souhaitez vous connecter aux machines virtuelles Azure après le basculement, vous devez activer les connexions Bureau à distance au niveau du pare-feu local.<br/><br/>Sur les machines protégées, la capacité d’un disque ne doit pas dépasser 1023 Go. Une machine virtuelle peut comporter jusqu’à 64 disques (jusqu’à 64 To). Si la taille de vos disques est supérieure à 1 To, vous pouvez utiliser la réplication de base de données offerte par des fonctionnalités telles que SQL Server AlwaysOn ou Oracle Data Guard.<br/><br/>2 Go minimum d’espace disponible sur le lecteur d’installation pour l’installation du composant.<br/><br/>Les clusters invités dont le disque est partagé ne sont pas pris en charge. Si vous disposez d’un déploiement en cluster, vous pouvez utiliser la réplication de base de données offerte par des fonctionnalités telles que SQL Server AlwaysOn ou Oracle Data Guard.<br/><br/>L’amorçage UEFI (Unified Extensible Firmware Interface) / EFI (Extensible Firmware Interface) n’est pas pris en charge.<br/><br/>Le nom des machines doit contenir entre 1 et 63 caractères (lettres, chiffres et tirets). Le nom doit commencer par une lettre ou un chiffre et se terminer par une lettre ou un chiffre. Une fois qu’une machine est protégée, vous pouvez modifier le nom Azure.<br/><br/>**Machines virtuelles VMware**<br/><br>Vous devez installer VMware vSphere PowerCLI 6.0. sur le serveur d’administration (serveur de configuration).<br/><br/>Les outils VMware doivent être installés et en cours d’exécution sur les machines virtuelles VMware à protéger.<br/><br/>Si la machine virtuelle source propose l’association de cartes réseau, elle est convertie en une carte réseau unique après le basculement vers Azure.<br/><br/>Si les machines virtuelles protégées disposent d’un disque iSCSI, Site Recovery convertit le disque iSCSI de machine virtuelle en fichier de disque dur virtuel (VHD) lors du basculement de la machine virtuelle vers Azure. Si la cible iSCSI peut être atteinte par la machine virtuelle Azure, alors, elle se connecte à la cible iSCSI et peut voir essentiellement deux disques : le VHD de la machine virtuelle Azure et le disque iSCSI source. Dans ce cas, vous devez vous déconnecter de la cible iSCSI qui s’affiche sur la machine virtuelle Azure ayant basculé.<br/><br/>Pour plus d’informations sur les autorisations utilisateur VMware requises par Site Recovery, consultez la section [Autorisations VMware pour l’accès au vCenter](#vmware-permissions-for-vcenter-access).<br/><br/> **Ordinateurs Windows Server (sur des machines virtuelles VMware ou un serveur physique)**<br/><br/>Le serveur doit exécuter un système d’exploitation 64 bits pris en charge : Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2, avec au moins SP1.<br/><br/>Le système d’exploitation doit être installé sur le lecteur C et le disque du système d’exploitation doit être un disque de base Windows (le système d’exploitation ne doit pas être installé sur un disque dynamique Windows).<br/><br/>Pour les ordinateurs Windows Server 2008 R2, .NET Framework 3.5.1 doit être installé.<br/><br/>Vous devez fournir un compte d’administrateur (administrateur local de l’ordinateur Windows) pour l’installation Push du service Mobilité sur les serveurs Windows. Si le compte fourni n’est pas un compte de domaine, vous devez désactiver le contrôle d’accès utilisateur distant sur l’ordinateur local. Pour plus d’informations, consultez la section [Installer le service Mobilité par le biais d’une installation Push](#install-the-mobility-service-with-push-installation).<br/><br/>Site Recovery prend en charge les machines virtuelles équipées d’un disque RDM. Lors de la restauration automatique, Site Recovery réutilise le disque RDM si la machine virtuelle source et le disque RDM d’origine sont disponibles. Si ce n’est pas le cas, Site Recovery crée un fichier VMDK pour chaque disque.<br/><br/>**Ordinateurs Linux**<br/><br/>Vous devez disposer d’un système d’exploitation 64 bits compatible : Red Hat Enterprise Linux 6.7 ; Centos 6.5, 6.6 ou 6.7 ; Oracle Enterprise Linux 6.4 ou 6.5 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3) ; SUSE Linux Enterprise Server 11 SP3.<br/><br/>Les fichiers /etc/hosts se trouvant sur les ordinateurs protégés doivent contenir des entrées qui mappent le nom d’hôte local aux adresses IP associées à toutes les cartes réseau. <br/><br/>Si vous souhaitez vous connecter à une machine virtuelle Azure exécutant Linux après le basculement à l’aide d’un client Secure Shell (SSH), assurez-vous que le service Secure Shell sur la machine protégée est configuré pour démarrer automatiquement au démarrage du système et que les règles de pare-feu autorisent une connexion SSH à cette machine.<br/><br/>La protection ne peut être activée que pour les machines Linux avec le stockage suivant : système de fichiers (EXT3, ETX4, ReiserFS, XFS) ; logiciel Multipath - Mappeur d’appareil (multivoie) ; gestionnaire de volume (LVM2). Les serveurs physiques avec stockage de contrôleur HP CCISS ne sont pas pris en charge. Le système de fichiers ReiserFS n’est pris en charge que sur SUSE Linux Enterprise Server 11 SP3.<br/><br/>Site Recovery prend en charge les machines virtuelles équipées d’un disque RDM. Pendant la restauration automatique de Linux, Site Recovery ne réutilise pas le disque RDM. Il crée à la place un fichier VMDK pour chaque disque RDM correspondant. |

Pour une machine virtuelle Linux uniquement : définissez le paramètre disk.enableUUID=true dans les paramètres de configuration de la machine virtuelle dans VMware. Si cette ligne n’existe pas, ajoutez-la. Cela est nécessaire pour fournir un UUID cohérent au VMDK et lui assurer ainsi un montage correct. Sans ce paramètre, la restauration automatique entraîne un téléchargement complet, même si la machine virtuelle est disponible localement. L’ajout de ce paramètre garantit que seules les modifications d’ordre différentiel sont retransférées pendant la restauration automatique.

## <a name="step-1-create-a-vault"></a>Étape 1 : Créer un coffre
1. Connectez-vous au [portail Azure](https://manage.windowsazure.com/).
2. Développez **Data Services** > **Recovery Services**, puis cliquez sur **Coffre Site Recovery**.
3. Cliquez sur **Créer nouveau** > **Création rapide**.
4. Dans **Name**, entrez un nom convivial pour identifier le coffre.
5. Dans **Region**, sélectionnez la région géographique du coffre. Pour vérifier les régions prises en charge, consultez la page [Tarification d’Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/).
6. Cliquez sur **Create vault**.
    ![Création d’un coffre](./media/site-recovery-vmware-to-azure-classic/quick-start-create-vault.png)

Vérifiez la barre d’état pour vous assurer que le coffre a été créé correctement. Le coffre apparaît comme **Actif** sur la page principale **Recovery Services**.

## <a name="step-2-set-up-an-azure-network"></a>Étape 2 : Configurer un réseau Azure
Configurer un réseau Azure afin que les machines virtuelles Azure soient connectées à un réseau après le basculement, et que la restauration automatique sur le site local puisse fonctionner comme prévu.

1. Dans le portail Azure, sélectionnez **Créer un réseau virtuel** et spécifiez le nom du réseau, la plage d’adresses IP et le nom du sous-réseau.
2. Si vous avez besoin de la fonctionnalité de restauration automatique, ajoutez VPN/ExpressRoute au réseau. VPN/ExpressRoute peut être ajouté au réseau, même après le basculement.

Pour plus d’informations sur les réseaux virtuels, consultez la [vue d’ensemble des réseaux virtuels](../virtual-network/virtual-networks-overview.md).

> [!NOTE]
> La [migration des réseaux](../azure-resource-manager/resource-group-move-resources.md) entre les groupes de ressources d’un même abonnement ou de plusieurs abonnements n’est pas prise en charge pour les réseaux utilisés pour le déploiement de Site Recovery.
>
>

## <a name="step-3-install-the-vmware-components"></a>Étape 3 : Installer les composants VMware
Si vous souhaitez répliquer des machines VMware virtuelles, exécutez les étapes suivantes sur le serveur d’administration :

1. [Téléchargez](https://my.vmware.com/web/vmware/details?productId=491&downloadGroup=PCLI600R1) et installez VMware vSphere PowerCLI 6.0.
2. Redémarrez le serveur.

## <a name="step-4-download-a-vault-registration-key"></a>Étape 4 : Générer une clé d’inscription du coffre
1. À partir du serveur d’administration, ouvrez la console de récupération de Site Recovery dans Azure. Dans la page **Recovery Services**, cliquez sur le coffre pour ouvrir la page **Démarrage rapide**. Vous pouvez également ouvrir la page **Démarrage rapide** à tout moment en cliquant sur l’icône ci-dessous.

    ![Icône Démarrage rapide](./media/site-recovery-vmware-to-azure-classic/quick-start-icon.png)
2. Dans la page **Démarrage rapide**, cliquez sur **Préparer les ressources cibles** > **Télécharger une clé d’inscription**. Le fichier d’enregistrement est généré automatiquement. Il est valide pendant cinq jours après sa création.

## <a name="step-5-install-the-management-server"></a>Étape 5 : Installer le serveur d’administration
> [!TIP]
> Assurez-vous que ces URL sont accessibles depuis le serveur d’administration :
>
> * *.hypervrecoverymanager.windowsazure.com
> * *.accesscontrol.windows.net
> * *.backup.windowsazure.com
> * *.blob.core.windows.net
> * *.store.core.windows.net
> * https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi
> * https://www.msftncsi.com/ncsi.txt
>
>



>[!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Setup-Registration/player]



1. Dans la page **Démarrage rapide**, téléchargez le fichier d’installation unifiée sur le serveur.
2. Exécutez le fichier d’installation pour démarrer le programme d’installation de l’Assistant d’**installation unifiée de Site Recovery**.
3. Dans **Avant de commencer**, sélectionnez **l’installation du serveur de configuration et du serveur de processus**.

   ![Avant de commencer](./media/site-recovery-vmware-to-azure-classic/combined-wiz1.png)
4. Dans **Licence de logiciel tiers**, cliquez sur **J’accepte** pour télécharger et installer MySQL.

    ![Logiciels tiers](./media/site-recovery-vmware-to-azure-classic/combined-wiz105.PNG)
5. Dans **Inscription**, recherchez et sélectionnez la clé d’inscription que vous avez téléchargée à partir du coffre.

    ![Inscription](./media/site-recovery-vmware-to-azure-classic/combined-wiz3.png)
6. Dans **Paramètres Internet**, indiquez comment le fournisseur s’exécutant sur le serveur de configuration doit se connecter à Azure Site Recovery par le biais d’Internet.

   * Si vous voulez vous connecter avec le proxy actuellement configuré sur la machine, sélectionnez **Se connecter avec les paramètres de proxy existants**.
   * Si vous voulez que le fournisseur se connecte directement, sélectionnez **Se connecter directement sans proxy**.
   * Si le proxy existant nécessite une authentification, ou si vous voulez utiliser un proxy personnalisé pour la connexion au fournisseur, sélectionnez **Se connecter avec des paramètres de proxy personnalisés**.
     * Si vous utilisez un proxy personnalisé, vous devez spécifier l’adresse, le port et les informations d’identification.
     * Si vous utilisez un proxy, vous devez avoir déjà autorisé les URL suivantes :
       * *.hypervrecoverymanager.windowsazure.com    
       * *.accesscontrol.windows.net
       * *.backup.windowsazure.com
       * *.blob.core.windows.net
       * *.store.core.windows.net

    ![Pare-feu](./media/site-recovery-vmware-to-azure-classic/combined-wiz4.png)

1. Dans **Vérification de la configuration requise**, le programme d’installation procède à une vérification afin de garantir le bon déroulement de l’installation.

    ![Composants requis](./media/site-recovery-vmware-to-azure-classic/combined-wiz5.png)

     Si un avertissement s’affiche à propos de la **vérification de la synchronisation globale**, vérifiez que l’heure de l’horloge système (paramètres **Date et heure**) est identique à celle du fuseau horaire.

     ![Problème de synchronisation de l’heure](./media/site-recovery-vmware-to-azure-classic/time-sync-issue.png)

1. Dans **Configuration MySQL**, créez des informations d’identification pour la connexion à l’instance de serveur MySQL qui sera installée.

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz6.png)
2. Dans **Détails de l’environnement**, indiquez si vous voulez répliquer des machines virtuelles VMware. Si c’est le cas, le programme d’installation vérifie que PowerCLI 6.0 est installé.

    ![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz7.png)
3. Dans **Emplacement d’installation**, sélectionnez l’emplacement où vous voulez installer les fichiers binaires et stocker le cache. Vous pouvez sélectionner un lecteur qui dispose d’au moins 5 Go d’espace disque disponible. Toutefois, nous vous recommandons d’utiliser un lecteur de cache présentant au moins 600 Go d’espace disque disponible.

   ![Emplacement d’installation](./media/site-recovery-vmware-to-azure-classic/combined-wiz8.png)
4. Dans **Sélection du réseau**, spécifiez l’écouteur (carte réseau et port SSL) sur lequel le serveur de configuration envoie et reçoit les données de réplication. Vous pouvez modifier la valeur par défaut du port (9443). En plus de ce port, le port 443 sera utilisé par un serveur web qui gère les opérations de réplication. N’utilisez pas le port 443 pour la réception du trafic de réplication.

    ![Sélection du réseau](./media/site-recovery-vmware-to-azure-classic/combined-wiz9.png)



1. Dans **Résumé**, passez en revue les informations, puis cliquez sur **Installer**. Une fois l’installation terminée, une phrase secrète est générée. Vous en aurez besoin pour l’activation de la réplication. Par conséquent, copiez-la et conservez-la en lieu sûr.

   ![Résumé](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)


> [!WARNING]
> Le proxy Microsoft Azure Recovery Services Agent doit être installé.
> Une fois l’installation terminée, lancez Microsoft Azure Recovery Services Shell à partir du menu Démarrer de Windows. Dans la fenêtre de commande qui s’ouvre, exécutez l’ensemble suivant de commandes pour configurer les paramètres du serveur proxy.
>
>
    $pwd = ConvertTo-SecureString -String ProxyUserPassword
    Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\username -ProxyPassword $pwd
    net stop obengine
    net start obengine
>


### <a name="run-setup-from-the-command-line"></a>Exécution depuis la ligne de commande
Vous pouvez également exécuter l’Assistant unifié comme suit à partir de la ligne de commande :

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Où :

* /ServerMode : Obligatoire. Spécifie si l’installation doit installer les serveurs de configuration et de traitement ou uniquement le serveur de traitement (utilisé pour installer des serveurs de traitement supplémentaires). Valeurs d’entrée : CS, PS.
* InstallDrive : Obligatoire. Spécifie le dossier dans lequel les composants sont installés.
* /MySQLCredFilePath. Obligatoire. Spécifie le chemin d’accès à un fichier dans lequel les informations d’identification du serveur MySQL sont stockées. Accéder au modèle pour créer le fichier.
* /VaultCredFilePath. Obligatoire. Emplacement du fichier d’informations d’identification du coffre.
* / EnvType. Obligatoire. Type d’installation. Valeurs : VMware, NonVMware.
* / PSIP et /CSIP. Obligatoire. Adresse IP du serveur de traitement et du serveur de configuration.
* /PassphraseFilePath. Obligatoire. Emplacement du fichier de phrase secrète.
* /ByPassProxy. facultatif. Spécifie le serveur d’administration qui se connecte à Azure sans proxy.
* /ProxySettingsFilePath. facultatif. Spécifie les paramètres d’un proxy personnalisé (proxy par défaut sur le serveur qui requiert l’authentification ou proxy personnalisé).

## <a name="step-6-set-up-credentials-for-the-vcenter-server"></a>Étape 6 : Configurer des informations d’identification pour le serveur vCenter
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Discovery/player]
>
>

Le serveur de traitement peut détecter automatiquement les machines virtuelles VMware qui sont gérées par un serveur vCenter. Pour la détection automatique, Site Recovery a besoin d’un compte et d’informations d’identification permettant d’accéder au serveur vCenter. Ce n’est pas le cas si vous répliquez uniquement des serveurs physiques.

Pour définir le compte et les informations d’identification :

1. Sur le serveur vCenter, créez un rôle (**Azure_Site_Recovery**) au niveau du serveur vCenter avec les [autorisations requises](#vmware-permissions-for-vcenter-access).
2. Attribuez le rôle **Azure_Site_Recovery** à un utilisateur vCenter.

   > [!NOTE]
   > Un compte d’utilisateur vCenter doté d’un rôle en lecture seule peut exécuter le basculement sans arrêter les ordinateurs sources protégés. Si vous souhaitez arrêter les machines virtuelles, vous aurez besoin du rôle Azure_Site_Recovery. Si vous ne faites que migrer des machines virtuelles de VMware vers Azure sans restauration automatique, le rôle en lecture seule est suffisant.
   >
   >
3. Pour ajouter le compte, ouvrez **cspsconfigtool**. Il est disponible sous forme de raccourci sur le bureau et situé dans le dossier \home\svsystems\bin [EMPLACEMENT D’INSTALLATION].
4. Dans l’onglet **Gérer les comptes**, cliquez sur **Ajouter un compte**.

    ![Ajouter un compte](./media/site-recovery-vmware-to-azure-classic/credentials1.png)
5. Dans **Détails du compte**, ajoutez des informations d’identification pouvant être utilisées pour accéder au serveur vCenter. L’affichage du nom de compte dans le portail peut prendre plus de 15 minutes. Pour procéder à une mise à jour immédiate, cliquez sur l’option **Actualiser** de l’onglet **Serveurs de configuration**.

    ![Détails](./media/site-recovery-vmware-to-azure-classic/credentials2.png)

## <a name="step-7-add-vcenter-servers-and-esxi-hosts"></a>Étape 7 : Ajouter des serveurs vCenter ou des hôtes ESXi
Si vous répliquez des machines virtuelles VMware, vous devez ajouter un serveur vCenter (ou un hôte ESXi).

1. Dans l’onglet **Serveurs** > **Serveurs de configuration**, sélectionnez **Ajouter un serveur vCenter**.

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter1.png)
2. Ajoutez les détails du serveur vCenter ou de l’hôte ESXi, le nom du compte que vous avez spécifié pour accéder au serveur vCenter à l’étape précédente, et le serveur de traitement qui servira à détecter les machines virtuelles VMware gérées par le serveur vCenter. Le serveur vCenter ou l’hôte ESXi doit se trouver dans le même réseau que le serveur sur lequel est installé le serveur de traitement.

   > [!NOTE]
   > Si vous ajoutez le serveur vCenter ou un hôte ESXi à l’aide d’un compte qui ne dispose pas des privilèges d’administrateur sur le serveur vCenter ou hôte, assurez-vous que les comptes de serveur vCenter ou ESXi bénéficient de ces autorisations : centre de données, magasin de données, dossier, Jost, ressource, machine virtuelle et commutateur distribué vSphere. L’autorisation d’affichage de stockage doit être activée pour le serveur vCenter.
   >
   >

    ![Ajouter un serveur vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter2.png)
3. Une fois la découverte terminée, le serveur vCenter est répertorié dans l’onglet **Serveurs de configuration**.

    ![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter3.png)

## <a name="step-8-create-a-protection-group"></a>Étape 8 : Créer un groupe de protection
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Protection/player]
>
>

Les groupes de protection sont des regroupements logiques incluant les machines virtuelles ou les serveurs physiques que vous voulez protéger au moyen de ces paramètres de protection. Vous appliquez des paramètres de protection à un groupe de protection, et ces paramètres sont appliqués à toutes les machines (virtuelles ou physiques) que vous ajoutez au groupe.

1. Accédez à **Éléments protégés** > **Groupe de protection**, puis cliquez sur l’icône pour ajouter un groupe de protection.

    ![Créer un groupe de protection](./media/site-recovery-vmware-to-azure-classic/protection-groups1.png)
2. Dans la page **Spécifier les paramètres de groupe de protection**, spécifiez un nom pour le groupe. Dans la liste déroulante **De**, sélectionnez le serveur de configuration sur lequel vous souhaitez créer le groupe. La **cible**  est Microsoft Azure.

    ![Paramètres du groupe de protection](./media/site-recovery-vmware-to-azure-classic/protection-groups2.png)
3. Dans la page **Spécifier les paramètres de réplication**, configurez les paramètres de réplication qui seront utilisés pour toutes les machines du groupe.

    ![Réplication du groupe de protection](./media/site-recovery-vmware-to-azure-classic/protection-groups3.png)

   * **Cohérence multimachine virtuelle** : si vous activez cette option, elle crée des points de récupération cohérents au niveau de l’application, partagés entre les machines du groupe de protection. Ce paramètre est particulièrement important quand toutes les machines du groupe de protection exécutent la même charge de travail. Tous les ordinateurs seront récupérés au même point de données. Il est disponible que vous répliquiez des machines virtuelles VMware ou des serveurs physiques (Windows ou Linux).
   * **Seuil d’objectif de point de récupération**: définit l’objectif de point de récupération. Des alertes sont générées lorsque la réplication de la protection continue des données dépasse la valeur de seuil d’objectif de point de récupération configurée.
   * **Rétention de point de récupération**: spécifie la fenêtre de rétention. Les ordinateurs protégés peuvent être récupérés à tout moment dans cette fenêtre.
   * **Fréquence des instantanés cohérents au niveau des applications** : spécifie la fréquence de création des points de récupération contenant des instantanés cohérents au niveau des applications.

Lorsque vous sélectionnez la coche, un groupe de protection est créé avec le nom spécifié. En outre, un deuxième groupe de protection est créé avec le nom *protection-group-name-Failback*. Ce groupe de protection est utilisé si vous ne procédez pas à la restauration automatique du site local après le basculement vers Azure. Vous pouvez surveiller les groupes de protection à mesure qu’ils sont créés sur la page **Éléments protégés** .

## <a name="step-9-install-the-mobility-service"></a>Étape 9 : Installer le service Mobilité
La première étape de l’activation de la protection des machines virtuelles et des serveurs physiques consiste à installer le service Mobilité. Il existe deux méthodes pour le faire :

* Transmettre et installer automatiquement le service sur chaque ordinateur du serveur de traitement ; Lorsque vous ajoutez des machines à un groupe de protection et que ces dernières exécutent déjà une version adéquate du service Mobilité, l’installation Push n’a pas lieu. Vous pouvez également installer automatiquement le service à l’aide de votre méthode Push d’entreprise, telle que WSUS ou System Center Configuration Manager. Assurez-vous que vous avez bien configuré le serveur d’administration avant de le faire.
* Installez le service manuellement sur chaque machine que vous souhaitez protéger. Assurez-vous que vous avez bien configuré le serveur d’administration avant de le faire.

### <a name="install-the-mobility-service-with-push-installation"></a>Installer le service Mobilité par le biais d’une installation Push
Lorsque vous ajoutez des machines à un groupe de protection, le service Mobilité est automatiquement envoyé et installé sur chaque machine par le serveur de traitement.

#### <a name="prepare-for-automatic-push-on-windows-machines"></a>Préparez une opération Push automatique sur les machines Windows
Voici comment préparer les machines Windows afin que le service Mobilité puisse être installé automatiquement par le serveur de traitement :

1. Créez un compte pouvant être utilisé par le serveur de traitement pour accéder à la machine. Le compte doit disposer des privilèges administrateur (local ou de domaine). Ces informations d’identification ne sont utilisées que pour l’installation Push du service Mobilité.

   > [!NOTE]
   > Si vous n’utilisez pas de compte de domaine, vous devez désactiver le contrôle d’accès utilisateur distant sur l’ordinateur local. Pour ce faire, dans le Registre, sous HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System, ajoutez l’entrée de Registre DWORD LocalAccountTokenFilterPolicy avec une valeur de 1. Pour ajouter l’entrée de Registre à partir d’une commande d’ouverture de la CLI ou avec PowerShell, saisissez **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.
   >
   >
2. Dans le Pare-feu Windows de la machine à protéger, sélectionnez **Autoriser une application ou une fonctionnalité à franchir le pare-feu**, puis activez **Partage de fichiers et d’imprimantes** et **Infrastructure de gestion Windows**. Pour les machines qui appartiennent à un domaine, vous pouvez configurer la stratégie de pare-feu avec un objet de stratégie de groupe.

   ![Paramètres du pare-feu](./media/site-recovery-vmware-to-azure-classic/mobility1.png)
3. Ajoutez le compte que vous venez de créer :

   * Ouvrez **cspsconfigtool**. Il est disponible sous forme de raccourci sur le bureau et situé dans le dossier \home\svsystems\bin [EMPLACEMENT D’INSTALLATION].
   * Dans l’onglet **Gérer les comptes**, cliquez sur **Ajouter un compte**.
   * Ajoutez le compte que vous venez de créer. Après avoir ajouté le compte, vous devez fournir ces informations d’identification au moment où vous ajoutez une machine à un groupe de protection.

#### <a name="prepare-for-automatic-push-on-linux-servers"></a>Préparez une opération Push automatique sur les serveurs Linux
1. Vérifiez que la machine Linux à protéger est prise en charge comme décrit dans la section [Conditions préalables locales](#on-premises-prerequisites). Vérifiez qu’il existe une connectivité réseau entre la machine à protéger et le serveur d’administration qui exécute le serveur de traitement.
2. Créez un compte pouvant être utilisé par le serveur de traitement pour accéder à la machine. Le compte est un utilisateur racine sur le serveur Linux source. Ces informations d’identification ne sont utilisées que pour l’installation Push du service Mobilité.

   * Ouvrez **cspsconfigtool**. Il est disponible sous forme de raccourci sur le bureau et situé dans le dossier \home\svsystems\bin [EMPLACEMENT D’INSTALLATION].
   * Dans l’onglet **Gérer les comptes**, cliquez sur **Ajouter un compte**.
   * Ajoutez le compte que vous venez de créer. Après avoir ajouté le compte, vous devez fournir ces informations d’identification au moment où vous ajoutez une machine à un groupe de protection.
3. Vérifiez que les fichiers /etc/hosts sur le serveur Linux source contiennent des entrées qui mappent le nom d’hôte local sur les adresses IP associées à toutes les cartes réseau.
4. Installez les packages openssh, openssh-server et openssl les plus récents sur la machine à protéger.
5. Vérifiez que le protocole SSH est activé et exécuté sur le port 22.
6. Activez le sous-système SFTP et l'authentification par mot de passe dans le fichier sshd_config comme suit :

   * Connectez-vous en tant qu’utilisateur racine.
   * Dans le fichier /etc/ssh/sshd_config, recherchez la ligne commençant par PasswordAuthentication.
   * Supprimez les commentaires de la ligne et remplacez la valeur **no** par **yes**.
   * Recherchez la ligne commençant par **Subsystem** et supprimez la mise en commentaire de la ligne.

     ![Remplacement de l’absence de sous-système par défaut dans Linux](./media/site-recovery-vmware-to-azure-classic/mobility2.png)

### <a name="install-the-mobility-service-manually"></a>Installer le service de mobilité manuellement
Les programmes d’installation sont disponibles dans C:\Program Files (x86)\Microsoft Azure Site Recovery\home\svsystems\pushinstallsvc\repository.

| Système d’exploitation source | Fichier d’installation du service Mobilité |
| --- | --- |
| Windows Server (64 bits uniquement) |Microsoft-ASR_UA_9.*.0.0_Windows_* release.exe |
| CentOS 6.4, 6.5, 6.6 (64 bits uniquement) |Microsoft-ASR_UA_9.*.0.0_RHEL6-64_*release.tar.gz |
| SUSE Linux Enterprise Server 11 SP3 (64 bits uniquement) |Microsoft-ASR_UA_9.*.0.0_SLES11-SP3-64_*release.tar.gz |
| Oracle Enterprise Linux 6.4, 6.5 (64 bits uniquement) |Microsoft-ASR_UA_9.*.0.0_OL6-64_*release.tar.gz |

#### <a name="install-the-mobility-service-manually-on-a-windows-server"></a>Installer le service Mobilité manuellement sur un serveur Windows
1. Téléchargez et exécutez le programme d’installation approprié.
2. Dans **Avant de commencer**, sélectionnez **Service Mobilité**.

    ![Installation du service Mobilité](./media/site-recovery-vmware-to-azure-classic/mobility3.png)
3. Dans **Détails du serveur de configuration**, spécifiez l’adresse IP du serveur d’administration et la phrase secrète qui a été générée lorsque vous avez installé les composants du serveur d’administration. Vous pouvez récupérer la phrase secrète en exécutant : **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –n** sur le serveur d’administration.

    ![Service Mobilité](./media/site-recovery-vmware-to-azure-classic/mobility6.png)
4. Dans **Emplacement de l’installation**, conservez l’emplacement par défaut, puis cliquez sur **Suivant** pour commencer l’installation.
5. Dans **Progression de l’installation**, vérifiez l’installation et redémarrez la machine si vous y êtes invité.

Vous pouvez également procéder à l’installation en entrant le texte suivant dans la ligne de commande :

    UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <Installation Directory>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>] [/LogFilePath <Log File Path>]

Dans la commande précédente :

* /Role : Obligatoire Indique si le service Mobilité doit être installé.
* /InstallLocation : Obligatoire. Spécifie l’emplacement d’installation du service.
* / PassphraseFilePath : Obligatoire. Indiquez la phrase secrète de la phrase secrète de configuration.
* /LogFilePath : Obligatoire. Spécifie l’emplacement du fichier journal d’installation.

#### <a name="uninstall-the-mobility-service-manually"></a>Désinstaller le service de mobilité manuellement
Vous pouvez désinstaller le service Mobilité à l’aide de l’option **Désinstaller ou modifier un programme** du Panneau de configuration ou à l’aide de la ligne de commande.

La commande permettant désinstaller le service Mobilité à l’aide de la ligne de commande est :

    MsiExec.exe /qn /x {275197FC-14FD-4560-A5EB-38217F80CBD1}

#### <a name="change-the-ip-address-of-the-management-server"></a>Modifier l’adresse IP du serveur d’administration
Après avoir exécuté l’Assistant, vous pouvez modifier l’adresse IP du serveur d’administration comme suit :

1. Ouvrez le fichier hostconfig.exe (situé sur le bureau).
2. Dans l’onglet **Global**, modifiez l’adresse IP du serveur d’administration.

   > [!NOTE]
   > Modifiez uniquement l’adresse IP du serveur d’administration. Le numéro de port des communications de serveur d’administration doit être 443 et l’option **Utiliser HTTPS** doit être laissée activée. Ne modifiez pas la phrase secrète.
   >
   >

    ![Adresse IP du serveur d’administration](./media/site-recovery-vmware-to-azure-classic/host-config.png)

#### <a name="install-the-mobility-service-manually-on-a-linux-server"></a>Installer le service Mobilité manuellement sur un serveur Linux
1. Copiez l’archive tar appropriée sur la machine Linux à protéger. Consultez le tableau de la section [Installer le service de mobilité manuellement](#install-the-mobility-service-manually) pour déterminer quel archive tar vous devez utiliser.
2. Ouvrez un interpréteur de commandes et extrayez l’archive tar compressée dans un emplacement local en exécutant la commande suivante : `tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Créez un fichier nommé passphrase.txt dans le répertoire local où vous avez extrait le contenu de l’archive tar. Pour ce faire, copiez la phrase secrète de C:\ProgramData\Microsoft Azure Site Recovery\private\connection.passphrase sur le serveur d’administration et enregistrez-la dans le fichier passphrase.txt en exécutant *`echo <passphrase> >passphrase.txt`* dans l’interpréteur de commandes.
4. Installez le service Mobilité en entrant la commande suivante : *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*
5. Spécifiez l’adresse IP interne du serveur d’administration et assurez-vous que le port 443 est sélectionné.

#### <a name="install-the-mobility-service-from-the-command-line"></a>Installer le service Mobilité à partir de la ligne de commande

Copiez la phrase secrète de C:\Program Files (x86)\InMage Systems\private\connection sur le serveur d’administration et enregistrez-la dans le fichier passphrase.txt sur le serveur d’administration. Exécutez ensuite les commande suivantes. Dans notre exemple, l’adresse IP du serveur est 104.40.75.37 et le port HTTPS est 443 :

Pour installer sur un serveur de production :

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt

Pour procéder à l’installation sur le serveur cible :

    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


## <a name="step-10-enable-protection-for-a-machine"></a>Étape 10 : activation de la protection d’une machine
Pour activer la protection, ajoutez des machines virtuelles et des serveurs physiques à un groupe de protection. Avant de commencer, si vous protégez des machines virtuelles VMware, notez les points suivants :

* Les machines virtuelles VMware sont détectées toutes les 15 minutes et il peut s’écouler plus de 15 minutes entre leur détection et leur affichage dans le portail Site Recovery.
* La mise à jour avec les modifications de l’environnement sur la machine virtuelle (par exemple, l’installation d’outils VMware) dans Site Recovery peut prendre plus de 15 minutes.
* Vous pouvez vérifier l’heure de la dernière détection de machines virtuelles VMware dans le champ **Dernier contact** correspondant au serveur vCenter ou à l’hôte ESXi, sous l’onglet **Serveurs de configuration**.
* Si vous ajoutez un serveur vCenter ou un hôte ESXi après avoir créé un groupe de protection, l’actualisation du portail Azure Site Recovery et l’affichage des machines virtuelles dans la boîte de dialogue **Ajouter des ordinateurs à un groupe de protection** peuvent prendre plus de 15 minutes.
* Si vous souhaitez procéder immédiatement et ajouter des machines à un groupe de protection sans attendre la détection planifiée, mettez en surbrillance le serveur de configuration (ne cliquez pas dessus), puis cliquez sur le bouton **Actualiser**.

Par ailleurs :

* Nous vous recommandons de structurer vos groupes de protection afin qu’ils reflètent vos charges de travail. Par exemple, ajoutez au même groupe des machines qui exécutent une application spécifique.
* Quand vous ajoutez des ordinateurs à un groupe de protection, le service Mobilité est automatiquement envoyé et installé sur chaque ordinateur par le serveur de traitement (s’il n’est pas encore installé). Vous devez préparer le mécanisme Push en suivant la procédure décrite à l’étape précédente.

### <a name="add-machines-to-a-protection-group"></a>ajouter des ordinateurs à un groupe de protection

1. Accédez à **Éléments protégés** > **Groupe de protection** > **Machines** > **Ajouter des machines**.
2. Si vous protégez des machines virtuelles VMware, dans **Sélectionner les machines virtuelles**, sélectionnez un serveur vCenter qui gère vos machines virtuelles (ou l’hôte EXSi sur lequel elles sont exécutées), puis sélectionnez les machines.

    ![activation de la protection pour les machines virtuelles](./media/site-recovery-vmware-to-azure-classic/enable-protection2.png)
3. Si vous protégez des serveurs physiques, dans **Sélectionner les machines virtuelles**, ouvrez l’Assistant **Ajouter des machines physiques**, puis entrez l’adresse IP et un nom convivial. Sélectionnez ensuite la famille de systèmes d'exploitation.

   ![Activer la protection de serveurs physiques](./media/site-recovery-vmware-to-azure-classic/enable-protection1.png)
4. Dans **Spécifier les ressources cibles**, sélectionnez le compte de stockage que vous utilisez pour la réplication, puis choisissez d’utiliser ou non les paramètres pour toutes les charges de travail. Les comptes de stockage Premium ne sont pas pris en charge pour le moment.

   > [!NOTE]
   > Nous ne prenons pas en charge le déplacement des comptes de stockage créés à l’aide du [portail Azure](../storage/storage-create-storage-account.md) dans les groupes de ressources.                           
   > [La migration de comptes de stockage](../azure-resource-manager/resource-group-move-resources.md) entre les groupes de ressources d’un même abonnement ou de plusieurs abonnements n’est pas prise en charge pour les comptes de stockage utilisés pour le déploiement de Site Recovery.
   >
   >

    ![Configurer les paramètres cibles](./media/site-recovery-vmware-to-azure-classic/enable-protection3.png)
5. Dans **Spécifier les comptes**, sélectionnez le compte dont vous avez [configuré](#install-the-mobility-service-with-push-installation) l’utilisation dans le cadre de l’installation automatique du service Mobilité.

    ![Spécifier les comptes](./media/site-recovery-vmware-to-azure-classic/enable-protection4.png)
6. Cochez la case pour terminer l'ajout d'ordinateurs au groupe de protection et démarrer la réplication initiale pour chaque ordinateur.

   > [!NOTE]
   > Si l’installation Push a été préparée, le service Mobilité est automatiquement installé sur les machines qui ne l’ont pas encore au moment de l’ajout au groupe de protection. Une fois l’installation du service terminée, une tâche de protection démarre, puis échoue. Après l’échec de ce travail, vous devez redémarrer manuellement chaque machine sur laquelle le service Mobilité est installé. Après le redémarrage, la tâche de protection recommence et la réplication initiale se produit.
   >
   >

Vous pouvez surveiller l’état sur la page **Tâches** .

![Surveiller l’état dans la page Tâches](./media/site-recovery-vmware-to-azure-classic/enable-protection5.png)

Vous pouvez aussi contrôler l’état de la protection dans **Éléments protégés** > *Nom du groupe de protection* > **Machines virtuelles**. Une fois la réplication initiale terminée et les données synchronisées, l’état de la machine passe à **Protégé**.

![Surveiller l’état dans Éléments protégés](./media/site-recovery-vmware-to-azure-classic/enable-protection6.png)

## <a name="step-11-set-protected-machine-properties"></a>Étape 11 Définir les propriétés de l’ordinateur protégé
1. Dès qu’une machine présente l’état **Protégé**, vous pouvez configurer ses propriétés de basculement. Dans les détails du groupe de protection, sélectionnez l’ordinateur et ouvrez l’onglet **Configurer**.
2. Site Recovery suggère automatiquement des propriétés de la machine virtuelle Azure et détecte les paramètres réseau locaux.

    ![Définir les propriétés des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/vm-properties1.png)
3. Vous pouvez modifier les paramètres suivants :

   * **Nom de machine virtuelle Azure**: il s’agit du nom attribué à la machine dans Azure après le basculement. Le nom doit satisfaire aux exigences Azure.
   * **Taille de machine virtuelle Azure** : le nombre de cartes réseau est déterminé par la taille spécifiée pour la machine virtuelle cible. Pour plus d’informations sur les tailles et les cartes, consultez les [tableaux des tailles](../virtual-machines/linux/sizes.md). Notez les points suivants :

     * Lorsque vous modifiez la taille d’une machine virtuelle et que vous enregistrez les paramètres, le nombre de cartes réseau change à la prochaine ouverture de l’onglet **Configurer**. Le nombre minimal de cartes réseau sur les machines virtuelles cibles est égal au nombre minimal de cartes réseau sur une machine virtuelle source. Le nombre maximal de cartes réseau est déterminé par la taille de la machine virtuelle.
       * Si le nombre de cartes réseau sur la machine source est inférieur ou égal au nombre de cartes autorisé pour la taille de la machine cible, la cible présentera le même nombre de cartes que la source.
       * Si le nombre de cartes de la machine virtuelle source dépasse la valeur de taille cible autorisée, la taille cible maximale sera utilisée.
        Par exemple, si une machine source présente deux cartes réseau et que la taille de la machine cible en accepte quatre, la machine cible présentera deux cartes. Si la machine source présente deux cartes, mais que la taille cible n’en prend en charge qu’une, la machine cible n’aura qu’une seule carte.
     * Si la machine virtuelle présente plusieurs cartes réseau, celles-ci doivent toutes être connectées au même réseau Azure.
   * **Réseau Azure**: vous devez spécifier le réseau Azure auquel les machines virtuelles Azure sont connectées après le basculement. Si vous ne spécifiez pas de réseau, les machines virtuelles Azure ne seront connectées à aucun réseau. En outre, vous devez spécifier un réseau Azure si vous souhaitez effectuer une restauration automatique à partir d’Azure vers le site local. La restauration automatique exige une connexion VPN entre un réseau Azure et un réseau local.
   * **Sous-réseau/Adresse IP Azure** : pour chaque carte réseau, sélectionnez le sous-réseau auquel la machine virtuelle Azure doit se connecter. Notez que si la carte réseau de l’ordinateur source est configurée pour utiliser une adresse IP statique, vous pouvez spécifier une adresse IP statique pour la machine virtuelle Azure. Si vous ne fournissez pas d’adresse IP statique, n’importe quelle adresse IP disponible sera allouée. Si l’adresse IP cible est spécifiée, mais qu’elle est déjà utilisée par une autre machine virtuelle dans Azure, le basculement échouera. Si la carte réseau de l’ordinateur source est configurée pour utiliser le protocole DHCP, vous aurez une configuration DHCP pour Azure.      

## <a name="step-12-create-a-recovery-plan-and-run-a-failover"></a>Étape 12 : Créer un plan de récupération et exécuter le basculement.
> [!VIDEO https://channel9.msdn.com/Blogs/Azure/Enhanced-VMware-to-Azure-Failover/player]
>
>

Vous pouvez exécuter un basculement pour une seule machine, ou basculer plusieurs machines effectuant la même tâche ou exécutant la même charge de travail. Pour basculer plusieurs machines en même temps, vous devez les ajouter à un plan de récupération.

Pour créer un plan de récupération :

1. Dans la page **Plans de récupération**, cliquez sur **Ajouter un plan de récupération**, puis ajoutez un plan de récupération. Spécifiez les détails du plan et sélectionnez **Azure** comme cible.

 ![Configurer le plan de récupération](./media/site-recovery-vmware-to-azure-classic/recovery-plan1.png)
2. Dans **Sélectionner les machines virtuelles**, sélectionnez un groupe de protection, puis sélectionnez les ordinateurs du groupe à ajouter au plan de récupération.

 ![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/recovery-plan2.png)

Vous pouvez personnaliser le plan pour créer des groupes et définir l’ordre dans lequel les ordinateurs du plan de récupération sont basculés. Vous pouvez également ajouter des scripts et des invites pour des actions manuelles. Vous pouvez créer des scripts manuellement ou à l’aide de [runbooks Azure Automation](site-recovery-runbook-automation.md). Pour plus d’informations sur la personnalisation des plans de récupération, consultez [Créer des plans de récupération](site-recovery-create-recovery-plans.md).

## <a name="run-a-failover"></a>Exécuter un basculement
Avant d’exécuter un basculement :

* Assurez-vous que le serveur d’administration fonctionne et est disponible. Si ce n’est pas le cas, le basculement échouera.
* Si vous exécutez un basculement non planifié :

  * Il est préférable d’arrêter les machines principales avant d’exécuter un basculement non planifié lorsque c’est possible. Vous êtes ainsi sûr que les machines source et les réplicas ne fonctionnent pas en même temps. Si vous répliquez des machines virtuelles VMware lors de l’exécution d’un basculement non planifié, vous pouvez indiquer que Site Recovery doit essayer d’arrêter les machines sources. Selon l’état du site principal, cela peut fonctionner ou non. Si vous répliquez des serveurs physiques, Site Recovery ne propose pas cette option.
  * Un basculement non planifié met un terme à la réplication des données à partir des machines principales. Par conséquent, les différences dans les données ne seront pas transférées une fois qu’un basculement non planifié a commencé.
  * Si vous voulez vous connecter à la machine virtuelle du réplica dans Azure après le basculement, activez la connexion Bureau à distance sur la machine virtuelle avant d’exécuter le basculement. Ensuite, autorisez la connexion RDP via le pare-feu. Vous devez également autoriser RDP sur le point de terminaison public de la machine virtuelle Azure après le basculement. Suivez les [meilleures pratiques](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) pour vous assurer que RDP fonctionne après un basculement.

> [!NOTE]
> Pour optimiser les performances lors d’un basculement vers Azure, assurez-vous que vous avez installé l’Agent Azure sur la machine protégée. Cela accélère le démarrage de la machine et facilite le diagnostic des problèmes. L’Agent Azure est disponible pour [Linux](https://github.com/Azure/WALinuxAgent) et [Windows](http://go.microsoft.com/fwlink/?LinkID=394789).
>
>

### <a name="run-a-test-failover"></a>Exécution d’un test de basculement
Exécutez un test de basculement afin de simuler vos processus de basculement et de récupération dans un réseau isolé qui n’affecte pas votre environnement de production et laisse la réplication normale se poursuivre comme à l’accoutumée. Le test de basculement est initialisé sur la source, et vous pouvez l’exécuter de plusieurs façons :

* **Ne pas spécifier de réseau Azure** : si vous exécutez un test de basculement sans réseau, le test vérifie que les machines virtuelles démarrent et s’affichent correctement dans Azure. Les machines virtuelles ne seront connectées à aucun réseau Azure après le basculement.
* **Spécifier un réseau Azure**: ce type de contrôle de basculement vérifie que l’ensemble de l’environnement de réplication s’affiche comme prévu et que les machines virtuelles Azure sont connectées au réseau spécifié.

Pour exécuter un test de basculement :

1. Sur la page **Plans de récupération**, sélectionnez le plan et cliquez sur **Test de basculement**.

 ![Sélectionner le plan](./media/site-recovery-vmware-to-azure-classic/test-failover1.png)
2. Dans **Confirmer le test de basculement**, sélectionnez **Aucun** pour indiquer que vous ne voulez pas utiliser de réseau Azure pour le test de basculement, ou sélectionnez le réseau auquel les machines virtuelles de test se connecteront après le basculement. Cochez la case pour démarrer le basculement.

 ![Effectuer une sélection](./media/site-recovery-vmware-to-azure-classic/test-failover2.png)
3. Surveillez la progression de l’opération sous l’onglet **Travaux** .

 ![Surveiller la progression](./media/site-recovery-vmware-to-azure-classic/test-failover3.png)
4. Une fois le basculement terminé, vous devriez également voir la machine Azure de réplication apparaître dans la section **Machines virtuelles** du portail Azure. Si vous souhaitez initier une connexion RDP à la machine virtuelle Azure, vous devez ouvrir le port 3389 sur le point de terminaison de la machine virtuelle.
5. Une fois que vous avez terminé, lorsque le basculement atteint la phase de **fin** de test, cliquez sur **Terminer le test** pour achever l’opération. Cliquez sur **Notes** pour consigner et enregistrer d’éventuelles observations sur le test de basculement.
6. Cliquez sur **Le test de basculement est terminé.** L’environnement de test est alors automatiquement nettoyé. Une fois cette opération terminée, le test de basculement affiche l’état **Terminé**. Toutes les machines virtuelles ou les éléments créés automatiquement lors du basculement de test sont supprimés. Si un test de basculement s’étend sur plus de deux semaines, le système le force à se terminer.

### <a name="run-an-unplanned-failover"></a>Exécuter un basculement non planifié
Le basculement non planifié est lancé depuis Azure et peut être effectué même si le site principal n’est pas disponible.

1. Dans la page **Plans de récupération**, sélectionnez le plan et cliquez sur **Basculement** > **Basculement non planifié**.

 ![Sélectionner le plan](./media/site-recovery-vmware-to-azure-classic/unplanned-failover1.png)
2. Si vous répliquez des machines virtuelles VMware, vous pouvez essayer d’arrêter les machines virtuelles locales. C’est tout ce qu’il y a à faire et le basculement se poursuit, que cette action aboutisse ou non. En cas d’échec, les détails de l’erreur apparaissent dans l’onglet **Travaux** sous **Travaux de basculement non planifiés**.

 ![Option d’arrêt des machines virtuelles locales](./media/site-recovery-vmware-to-azure-classic/unplanned-failover2.png)

 > [!NOTE]
 > Cette option n’est pas disponible si vous répliquez des serveurs physiques. Vous devez essayez de les arrêter manuellement si possible.
 >
 >

3. Dans **Confirmer le basculement**, vérifiez le sens du basculement (vers Azure) et sélectionnez le point de récupération à utiliser pour le basculement. Si vous avez activé la cohérence multimachine virtuelle lors de la configuration des propriétés de réplication, vous pouvez effectuer la récupération au dernier point de récupération cohérent d’application ou d’incident. Vous pouvez également sélectionner **Point de récupération personnalisé** pour effectuer la récupération à un point antérieur dans le temps. Cochez la case pour démarrer le basculement.

 ![Confirmer le sens du basculement](./media/site-recovery-vmware-to-azure-classic/unplanned-failover3.png)
4. Attendez la fin du travail de basculement non planifié. Vous pouvez surveiller la progression du basculement sous l’onglet **Travaux** . Même si des erreurs se produisent lors d’un basculement non planifié, le plan de récupération s’exécute jusqu’à la fin. Vous devriez également voir la machine Azure de réplication apparaître dans la section **Machines virtuelles** du portail Azure.

### <a name="connect-to-replicated-azure-virtual-machines-after-failover"></a>Se connecter à des machines virtuelles Azure répliquées après basculement
Pour vous connecter à des machines virtuelles répliquées dans Azure après le basculement, vous devez :

- activer une connexion Bureau à distance sur la machine principale ;
- configurer le Pare-feu Windows de la machine principale de manière à autoriser le protocole RDP ;
- ajouter le protocole RDP au point de terminaison public pour la machine virtuelle Azure.

Pour plus d’informations sur la configuration de ces éléments, consultez [Troubleshooting remote desktop connection after failover using ASR](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) (Résolution des problèmes de connexion Bureau à distance après un basculement à l’aide d’ASR).

## <a name="deploy-additional-process-servers"></a>Déployer d’autres serveurs de traitement
Si vous devez monter en charge votre déploiement au-delà de 200 machines sources ou si votre taux de variation quotidien total dépasse 2 To, vous aurez besoin de serveurs de traitement supplémentaires pour gérer le volume de trafic. Pour configurer un serveur de traitement supplémentaire, vérifiez les exigences de la section [Serveurs de traitement supplémentaires](#additional-process-servers), puis suivez les instructions ci-dessous. Après avoir configuré le serveur, vous pouvez configurer des machines sources pour les utiliser.

### <a name="set-up-an-additional-process-server"></a>Configurer un serveur de traitement supplémentaire
Pour configurer un serveur de traitement supplémentaire, procédez comme suit :

* Exécutez l’Assistant unifié pour configurer un serveur d’administration en tant que processus de serveur uniquement.
* Si vous souhaitez gérer la réplication des données en utilisant uniquement le nouveau serveur de traitement, vous devez migrer vos machines protégées.

### <a name="install-the-process-server"></a>Installer le serveur de traitement
1. Dans la page **Démarrage rapide**, téléchargez le fichier d’installation unifiée pour l’installation du composant Site Recovery. Exécutez le programme d’installation.
2. Dans la zone **Avant de commencer**, sélectionnez **l’ajout de serveurs de traitement supplémentaires pour effectuer un déploiement avec montée en puissance**.

 ![Ajouter un serveur de traitement](./media/site-recovery-vmware-to-azure-classic/add-ps1.png)
3. Exécutez l’Assistant comme lors de la [configuration](#step-5-install-the-management-server) du premier serveur d’administration.
4. Dans **Détails du serveur de configuration**, entrez l’adresse IP du serveur d’administration d’origine sur lequel vous avez installé le serveur de configuration, puis entrez la phrase secrète. Si vous ne connaissez pas la phrase secrète, exécutez **<SiteRecoveryInstallationFolder>\home\sysystems\bin\genpassphrase.exe –n** sur le serveur de gestion d’origine pour la récupérer.

 ![Détails du serveur de configuration](./media/site-recovery-vmware-to-azure-classic/add-ps2.png)

### <a name="migrate-machines-to-use-the-new-process-server"></a>Migrez les machines pour utiliser le nouveau serveur de traitement
1. Ouvrez **Serveurs de configuration** > **Serveur** > *Nom du serveur d’administration d’origine* > **Détails du serveur**.

 ![Détails du serveur](./media/site-recovery-vmware-to-azure-classic/update-process-server1.png)
2. Dans la liste **Serveurs de traitement**, sélectionnez **Modifier le serveur de traitement** en regard du serveur à modifier.

 ![Mettre à jour le serveur de traitement](./media/site-recovery-vmware-to-azure-classic/update-process-server2.png)
3. Sélectionnez **Modifier le serveur de traitement**, **Serveur de traitement cible**, puis sélectionnez le nouveau serveur d’administration. Sélectionnez ensuite les machines virtuelles qui seront gérées par le nouveau serveur de traitement. Cliquez sur l’icône d’information pour obtenir des informations sur le serveur. L’espace moyen nécessaire à la réplication de chaque machine virtuelle sélectionnée vers le nouveau serveur de traitement s’affiche pour vous aider à prendre des décisions quant à la charge. Cliquez sur la coche pour commencer la réplication vers le nouveau serveur de traitement.

 ![Modifier le serveur de traitement](./media/site-recovery-vmware-to-azure-classic/update-process-server3.png)

## <a name="vmware-permissions-for-vcenter-access"></a>Autorisations VMware pour l’accès au vCenter
Le serveur de traitement peut découvrir automatiquement les machines virtuelles sur un serveur vCenter. Pour effectuer une découverte automatique, vous devez définir un rôle (Azure_Site_Recovery) au niveau du serveur vCenter pour permettre à Site Recovery d’accéder au serveur vCenter. Si vous souhaitez seulement migrer des machines VMware vers Azure et que vous n’avez pas besoin d’une restauration automatique à partir d’Azure, vous devez définir un rôle en lecture seule suffisant. Définissez les autorisations décrites dans [Étape 6 : Configurer des informations d’identification pour le serveur vCenter](#step-6-set-up-credentials-for-the-vcenter-server). Les autorisations de rôle sont résumées dans le tableau suivant :

| **Rôle** | **Détails** | **Autorisations** |
| --- | --- | --- |
| Rôle Azure_Site_Recovery |Détection de machine virtuelle VMware |Attribuez ces privilèges au serveur v-Center :<br/><br/>Banque de données : Allouer de l’espace, Parcourir la banque de données, Opérations de fichier de bas niveau, Supprimer le fichier, Mettre à jour les fichiers de machine virtuelle<br/><br/>Réseau : Attribution de réseau<br/><br/>Ressources : Affecter une machine virtuelle à une liste de ressources partagées, Migrer une machine virtuelle hors tension, Migrer une machine virtuelle sous tension<br/><br/>Tâches : Créer une tâche, Mettre à jour une tâche<br/><br/>Machine virtuelle > Configuration<br/><br/>Machine virtuelle > Interagir > Répondre à la question, Connexion d’appareil, Configurer un support de CD, Configurer une disquette, Mettre hors tension, Mettre sous tension, Installation des outils VMware<br/><br/>Machine virtuelle > Stock > Créer, Inscrire, Désinscrire<br/><br/>Machine virtuelle > Approvisionnement > Autoriser le téléchargement de machines virtuelles, Autoriser le chargement de fichiers de machine virtuelle<br/><br/>Machine virtuelle > Instantanés > Supprimer les instantanés |
| Rôle d’utilisateur vCenter |Découverte/Basculement de machine virtuelle VMware sans arrêt de la machine virtuelle source |Attribuez ces privilèges au serveur v-Center :<br/><br/>Objet de centre de données > Propager vers l’objet enfant, rôle = lecture seule <br/><br/>L’utilisateur est affecté au niveau du centre de données et a donc accès à tous les objets du centre de données. Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec l’option **Propager vers l’objet enfant** aux objets enfants (hôtes ESX, banques de données, machines virtuelles et réseaux). |
| Rôle d’utilisateur vCenter |Basculement et restauration automatique |Attribuez ces privilèges au serveur v-Center :<br/><br/>Objet de centre de données -> Propager vers l’objet enfant, rôle = Azure_Site_Recovery<br/><br/>L’utilisateur est affecté au niveau du centre de données et a donc accès à tous les objets dans le centre de données.  Si vous voulez restreindre l’accès, affectez le rôle **Aucun accès** avec l’option **Propager vers l’objet enfant** à l’objet enfant (hôtes ESX, banques de données, machines virtuelles et réseaux). |

## <a name="third-party-software-notices-and-information"></a>Informations et remarques relatives aux logiciels tiers
<!--Do Not Translate or Localize-->

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”).  Microsoft is the not original author of the Third Party Code.  The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only.  This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.  

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

## <a name="next-steps"></a>Étapes suivantes
[Découvrez en détail la restauration automatique](site-recovery-failback-azure-to-vmware-classic.md), qui permet de ramener dans votre environnement local vos machines basculées s’exécutant dans Azure.

