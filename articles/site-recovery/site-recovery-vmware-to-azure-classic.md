<properties
	pageTitle="Répliquer des machines virtuelles VMware et des serveurs physiques sur Azure avec Azure Site Recovery | Microsoft Azure" 
	description="Cet article décrit comment déployer Azure Site Recovery pour orchestrer la réplication, le basculement et la récupération de machines virtuelles VMware ou des serveurs physiques Windows/Linux locaux vers Azure." 
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/15/2016"
	ms.author="raynew"/>

# Répliquer des machines virtuelles VMware et des serveurs physiques sur Azure avec Azure Site Recovery

> [AZURE.SELECTOR]
- [Amélioré](site-recovery-vmware-to-azure-classic.md)
- [Hérité](site-recovery-vmware-to-azure-classic-legacy.md)

Le service Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d’urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles et de serveurs physiques. Les machines peuvent être répliquées vers Azure ou vers un centre de données local secondaire. Pour avoir un rapide aperçu, consultez la section [Qu’est-ce qu’Azure Site Recovery ?](site-recovery-overview.md).

## Vue d’ensemble

Cet article explique comment :

- **Répliquer les machines virtuelles VMware dans Azure** : déployez Site Recovery pour coordonner la réplication, le basculement et la récupération des machines virtuelles VMware locales vers le stockage Azure.
- **Répliquer les serveurs physiques dans Azure** : déployez Azure Site Recovery pour coordonner la réplication, le basculement et la récupération des serveurs physiques Windows et Linux locaux vers Azure.

>[AZURE.NOTE] Cet article décrit comment exécuter une réplication sur Azure Si vous souhaitez répliquer des machines virtuelles VMware ou des serveurs physiques Windows/Linux sur un centre de données secondaire, suivez les instructions de [cet article](site-recovery-vmware-to-vmware.md).

Publiez des commentaires ou des questions au bas de cet article, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Déploiement amélioré 

Cet article contient des instructions relatives à un déploiement amélioré dans le portail Azure Classic. Nous vous conseillons d’utiliser cette version pour tous les nouveaux déploiements. Si vous avez déjà procédé au déploiement à l’aide de l’ancienne version, nous vous conseillons de migrer vers la nouvelle version. En savoir [plus](site-recovery-vmware-to-azure-classic-legacy.md##migrate-to-the-enhanced-deployment) sur la migration.

Le déploiement amélioré est une mise à jour majeure. Voici un récapitulatif des améliorations que nous avons apportées :

- **Aucune machine virtuelle d’infrastructure dans Azure** : les données se répliquent directement dans un compte de stockage Azure. En outre, pour la réplication et le basculement, aucune machine virtuelle d’infrastructure n’est configurée (serveur de configuration, serveur cible maître), comme cela était le cas dans le déploiement hérité.  
- **Installation unifiée** : une seule installation offre une configuration simple et l’extensibilité des composants locaux.
- **Déploiement sécurisé** : l’ensemble du trafic est chiffré et les communications de gestion de la réplication sont envoyées par le biais du port HTTPS 443.
- **Points de récupération** : prise en charge des points de récupération après incident cohérents d’application pour les environnements Windows et Linux, et les configurations avec machine virtuelle unique et multiples.
- **Test de basculement** : prise en charge du basculement de test sans interruption de service vers Azure, sans impact sur la production ou suspension de la réplication.
- **Basculement non planifié** : prise en charge du basculement non planifié vers Azure avec une option améliorée permettant d’arrêter automatiquement les machines virtuelles avant le basculement.
- **Restauration automatique** : restauration intégrée qui réplique uniquement les changements delta vers le site local.
- **vSphere 6.0** : prise en charge limitée des déploiements VMware Vsphere 6.0.


## Comment Site Recovery permet-il de protéger des serveurs physiques et virtuels ?


- Les administrateurs VMware peuvent configurer une protection des charges de travail et des applications métier hors site sur les machines virtuelles VMware. Les gestionnaires de serveur peuvent répliquer les serveurs physiques Windows et Linux locaux vers Azure.
- La console Azure Site Recovery fournit un emplacement unique pour une installation et une gestion simples des processus de réplication, de basculement et de récupération.
- Si vous répliquez des machines virtuelles VMware gérées par un serveur vCenter, Site Recovery peut automatiquement détecter ces machines virtuelles. Si les machines se trouvent sur un hôte ESXi Site Recovery, il détecte les machines virtuelles sur l’ordinateur hôte.
- Permet un basculement facile de votre infrastructure locale vers Azure et la restauration d’Azure sur des serveurs de machines virtuelles VMware sur le site local. 
- Configurer les plans de récupération qui regroupent des charges de travail d’application échelonnées sur plusieurs ordinateurs. Vous pouvez basculer sur ces plans, et Site Recovery fournit la cohérence multimachine virtuelle, de sorte que les machines exécutant les mêmes charges de travail peuvent être récupérées ensemble sur un point de données cohérent.

## Architecture du scénario

Composants du scénario :

- **Un serveur d’administration local** : le serveur d’administration exécute les composants Site Recovery :
	- **Serveur de configuration** : coordonne la communication et gère les processus de réplication et de récupération des données.
	- **Serveur de traitement** : fait office de passerelle de réplication. Il reçoit les données d’ordinateurs source protégés, les optimise grâce à la mise en cache, la compression et le chiffrement et envoie les données de réplication vers le stockage Azure. Il gère également l’installation Push du service Mobilité sur des machines protégées et assure la détection automatique des machines virtuelles VMware.
	- **Serveur cible maître** : gère les données de réplication pendant la restauration automatique à partir d’Azure. Vous pouvez également déployer un serveur d’administration se comportant comme un serveur de traitement, afin de mettre à niveau votre déploiement.
- **Service Mobilité** : ce composant est déployé sur chaque ordinateur (machine virtuelle VMware ou serveur physique) que vous souhaitez répliquer vers Azure. Il enregistre les écritures de données sur la machine et les transmet au serveur de traitement.
- **Azure** : vous n’avez pas besoin de créer de machines virtuelles Azure pour gérer la réplication et le basculement. Le service Site Recovery assure la gestion, puis la réplication des données directement sur le stockage Azure. Les machines virtuelles Azure répliquées sont automatiquement tournées uniquement en cas de basculement vers Azure. Toutefois, si vous souhaitez effectuer une restauration à partir d’Azure vers le site local, vous devrez configurer une machine virtuelle Azure pour qu’elle serve de serveur de traitement.


Le diagramme montre comment ces composants interagissent.

![architecture](./media/site-recovery-vmware-to-azure-classic/architecture.png)

## Planification de la capacité

Lorsque vous planifiez vos capacités, vous devez prendre en compte les environnements suivants :

- **Environnement source** : planification de la capacité ou exigences d’infrastructure et des machines VMware sources.
- **Serveur d’administration** : planification des serveurs d’administration locaux qui exécutent des composants Site Recovery.
- **Bande passante réseau entre la source et la cible** : planification de la bande passante réseau requise pour la réplication entre la source et Azure.

### Considérations relatives à l’environnement source

- **Taux de modification par jour maximal** : un ordinateur protégé ne peut utiliser qu’un serveur de traitement, et un serveur de traitement peut gérer un taux de modification par jour de 2 To. Par conséquent, 2 To est le taux de modification quotidien maximal de données pris en charge pour une machine protégée.
- **Débit maximum** : un ordinateur répliqué peut appartenir à un compte de stockage dans Azure. Un compte de stockage standard peut gérer un maximum de 20 000 requêtes par seconde, et nous vous recommandons de conserver le nombre d’IOPS sur un ordinateur source à 20 000. Pour exemple, si vous disposez d’une machine source à 5 disques et si chaque disque génère 120 IOPS (taille de 8 Ko) sur la source, la limite de 500 IOPS sur le disque Azure est respectée. Nombre de comptes de stockage requis = nombre total d’IOPS source/20 000. 
 

### Considérations relatives aux serveurs d’administration

Le serveur d’administration exécute les composants de Site Recovery qui gèrent l’optimisation, la réplication et la gestion des données. Il doit être en mesure de gérer la capacité de taux de modification quotidien pour toutes les charges de travail en cours d’exécution sur des ordinateurs protégés et bénéficier de suffisamment de bande passante pour la réplication continue des données vers le stockage Azure. Plus précisément :

- Le serveur de traitement reçoit les données de réplication d’ordinateurs protégés et les optimise grâce à la mise en cache, la compression et le chiffrement avant de les transmettre à Azure. Le serveur d’administration doit disposer de suffisamment de ressources pour effectuer ces tâches.
- Le serveur de traitement utilise le cache disque. Nous recommandons un disque de cache distinct de 600 Go ou plus pour gérer les modifications apportées aux données stockées en cas de goulot d’étranglement ou de panne. Au cours du déploiement, vous pouvez configurer le cache sur un lecteur disposant d’au moins 5 Go de stockage disponibles, mais 600 Go est la quantité minimale recommandée.
- À titre de meilleure pratique, nous conseillons de placer le serveur d’administration sur le même réseau et le même segment de réseau local que les machines que vous souhaitez protéger. Il peut se trouver sur un réseau différent, mais les ordinateurs à protéger doivent avoir une visibilité de réseau L3. 

Les recommandations en matière de taille du serveur d’administration sont résumées dans la table qui suit.

**Unité centrale de serveur d’administration** | **Mémoire** | **Taille du disque cache** | **Taux de modification des données** | **Machines protégées**
--- | --- | --- | --- | ---
8 processeurs virtuels (2 sockets * 4 cœurs à 2,5 GHz) | 16 Go | 300 Go | 500 Go ou moins | Déployer un serveur d’administration avec ces paramètres pour répliquer moins de 100 ordinateurs.
12 processeurs virtuels (2 sockets * 6 cœurs à 2,5 GHz) | 18 Go | 600 Go | 500 Go à 1 To | Déployez un serveur d’administration avec ces paramètres pour répliquer entre 100 et 150 ordinateurs.
16 processeurs virtuels (2 sockets * 8 cœurs à 2,5 GHz) | 32 Go | 1 To | 1 To à 2 To | Déployez un serveur d’administration avec ces paramètres pour répliquer entre 150 et 200 ordinateurs.
Déployer un autre serveur de traitement | | | > 2 To | Déployez des serveurs de traitement supplémentaires si vous effectuez la réplication de plus de 200 ordinateurs, ou si le taux de changement des données quotidien dépasse 2 To.

Où :

- Chaque ordinateur source est configuré avec 3 disques de 100 Go chacune.
- Nous avons utilisé le stockage de référence de 8 disques SAP de 10 K tr/min avec RAID 10 pour les mesures de disque cache.

### Bande passante du réseau de la source à la cible
Assurez-vous de calculer la bande passante requise pour la réplication initiale et la réplication différentielle en utilisant l’[outil Capacity Planner](site-recovery-capacity-planner.md).

#### Limitation de la bande passante utilisée pour la réplication

Le trafic de VMware répliqué dans Azure est envoyé via un serveur de traitement spécifique. Vous pouvez limiter la bande passante disponible pour la réplication avec Site Recovery sur le serveur comme suit :

1. Ouvrez le composant logiciel enfichable Microsoft Azure Backup sur le serveur d’administration principal ou sur un serveur d’administration exécutant des serveurs de traitement supplémentaire mis en service. Par défaut, un raccourci vers Microsoft Azure Backup est créé sur le bureau. Vous pouvez également le trouver dans : C:\\Program Files\\Microsoft Azure Recovery Services Agent\\bin\\wabadmin.
2. Dans le composant logiciel enfichable, cliquez sur **Modifier les propriétés**.

	![Limite de bande passante](./media/site-recovery-vmware-to-azure-classic/throttle1.png)

3. Sous l’onglet **Limitation**, spécifiez la bande passante utilisable pour la réplication Site Recovery et la planification applicable.

	![Limite de bande passante](./media/site-recovery-vmware-to-azure-classic/throttle2.png)

Vous pouvez éventuellement définir une limitation avec PowerShell. Voici un exemple :

    Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024) 

#### Optimisation de l’utilisation de la bande passante 
Pour modifier la bande passante utilisée pour la réplication par Azure Site Recovery, vous devez modifier une clé de registre.

La clé qui suit contrôle le nombre de threads utilisés pendant a réplication du disque

    HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows Azure Backup\Replication\UploadThreadsPerVM

 Dans un réseau « surutilisé », cette clé de registre doit être modifiée par rapport aux valeurs par défaut. Nous prenons en charge un maximum de 32 threads.


[En savoir plus](site-recovery-capacity-planner.md) sur la planification détaillée de la capacité.

### Serveurs de traitement supplémentaires

Si vous souhaitez protéger plus de 200 ordinateurs ou si le taux de modification quotidien est supérieur à 2 To, vous pouvez ajouter des serveurs supplémentaires pour gérer la charge. Pour assurer la montée en charge, vous pouvez :

- Augmenter le nombre de serveurs d’administration. Par exemple, vous pouvez protéger jusqu’à 400 machines avec deux serveurs d’administration.
- Ajouter des serveurs de traitement supplémentaires et les utiliser pour gérer le trafic à la place (ou en plus) du serveur d’administration.

Ce tableau décrit un scénario dans lequel :

- Vous pouvez configurer le serveur d’administration d’origine pour l’utiliser uniquement en tant que serveur de configuration.
- Vous pouvez configurer un serveur de traitement supplémentaire.
- Vous pouvez configurer des machines virtuelles protégées pour utiliser le serveur de traitement supplémentaire.
- Chaque ordinateur source protégé est configuré avec trois disques de 100 Go chacun.

**Serveur d’administration d’origine**<br/><br/>(serveur de configuration) | **Serveur de traitement supplémentaire**| **Taille du disque cache** | **Taux de modification des données** | **Machines protégées**
--- | --- | --- | --- | --- 
8 processeurs virtuels (2 sockets * 4 cœurs à 2,5 GHz), 16 Go de mémoire | 4 processeurs virtuels (2 sockets * 2 cœurs à 2,5 GHz), 8 Go de mémoire | 300 Go | 250 Go ou moins | Vous pouvez répliquer 85 machines ou moins.
8 processeurs virtuels (2 sockets * 4 cœurs à 2,5 GHz), 16 Go de mémoire | 8 processeurs virtuels (2 sockets * 4 cœurs à 2,5 GHz), 12 Go de mémoire | 600 Go | 250 Go à 1 To | Vous pouvez répliquer entre 85 et 150 machines.
12 processeurs virtuels (2 sockets * 6 cœurs à 2,5 GHz), 18 Go de mémoire | 12 processeurs virtuels (2 sockets * 6 cœurs à 2,5 GHz), 24 Go de mémoire | 1 To | 1 To à 2 To | Vous pouvez répliquer entre 150 et 225 machines.


La façon dont vous allez mettre à niveau vos serveurs dépend de votre préférence pour la montée en puissance ou la montée en charge. Vous pouvez utiliser la montée en puissance par le développement de serveurs de gestion et de traitement de haut de gamme ou la montée en charge par le déploiement d’un nombre de serveurs supérieur avec moins de ressources. Par exemple, si vous avez besoin de protéger les 220 ordinateurs vous pouvez effectuer des opérations suivantes :

- Configurer le serveur d’administration d’origine avec 12 processeurs virtuels, 18 Go de mémoire, un serveur de traitement supplémentaire comptant 12 processeurs virtuels, 24 Go de mémoire et configurer des ordinateurs protégés pour utiliser uniquement le serveur de traitement supplémentaire.
- Vous pouvez également configurer deux serveurs d’administration (2 x 8 processeurs virtuels, 16 Go de RAM) et deux serveurs de traitement supplémentaires (1 x 8 processeurs virtuels et 4 processeurs virtuels x 1 pour gérer les ordinateurs (220) 135 + 85 machines) et configurer des ordinateurs protégés pour utiliser uniquement les serveurs de traitement supplémentaires.
  

[Suivez ces instructions](#deploy-additional-process-servers) pour configurer un serveur de traitement supplémentaire.

## Avant de lancer le déploiement

Les tableaux résument les conditions préalables de déploiement de ce scénario.


### Conditions préalables pour Azure

**Configuration requise** | **Détails**
--- | ---
**Compte Azure**| Vous aurez besoin d’un compte [Microsoft Azure](https://azure.microsoft.com/). Vous pouvez commencer avec une [version d'évaluation gratuite](https://azure.microsoft.com/pricing/free-trial/). [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery/) sur la tarification Site Recovery. 
**Stockage Azure** | Vous aurez besoin d’un compte de stockage Azure pour stocker les données répliquées. Les données répliquées sont stockées dans le stockage Azure et les machines virtuelles Azure sont lancées au moment du basculement. <br/><br/>Vous avez besoin d’un [compte de stockage géo-redondant standard](../storage/storage-redundancy.md#geo-redundant-storage). Le compte doit se trouver dans la même région que le service Site Recovery et être associé au même abonnement. Notez que la réplication vers les comptes Premium Storage n’est pas prise en charge actuellement et ne doit pas être utilisée.<br/><br/>Nous ne prenons pas en charge le déplacement des comptes de stockage créés à l’aide du [nouveau portail Azure](../storage/storage-create-storage-account.md) entre les groupes de ressources.[Informations supplémentaires sur](../storage/storage-introduction.md) le stockage Azure.<br/><br/> 
**Réseau Azure** | Vous aurez besoin d’un réseau virtuel Azure auquel les machines virtuelles Azure se connecteront au moment du basculement. Le réseau virtuel Azure doit se trouver dans la même région que le coffre Site Recovery.<br/><br/>Notez que pour une restauration automatique après le basculement vers Azure, vous devez disposer d’une connexion VPN (ou Azure ExpressRoute) configurée du réseau Azure vers le site local. 


### Conditions préalables locales

**Configuration requise** | **Détails**
--- | ---
**Serveur d’administration** | Vous avez besoin d’un serveur Windows 2012 R2 local s’exécutant sur une machine virtuelle ou un serveur physique. Tous les composants Site Recovery locaux sont installés sur ce serveur d’administration<br/><br/> Nous vous conseillons de déployer le serveur en tant que machine virtuelle VMware hautement disponible. La restauration automatique vers le site local à partir d’Azure doit toujours s’effectuer vers des machines virtuelles VMware, que vous ayez basculé sur des machines virtuelles ou des serveurs physiques. Si vous ne configurez pas le serveur d’administration comme machine virtuelle VMware, vous devez configurer un serveur cible maître distinct en tant que machine virtuelle VMware pour recevoir le trafic de la restauration automatique.<br/><br/>Le serveur doit avoir une adresse IP statique.<br/><br/>Le nom d’hôte du serveur doit comporter 15 caractères ou moins.<br/><br/>Les paramètres régionaux du système d’exploitation doivent être en anglais uniquement.<br/><br/>Le serveur d’administration nécessite un accès à Internet.<br/><br/>Vous devez disposer d’un accès en sortie à partir du serveur comme suit : un accès provisoire sur HTTP 80 pendant l’installation des composants Site Recovery (pour télécharger MySQL) ; accès sortant permanent sur HTTPS 443 pour la gestion de la réplication ; accès sortant permanent sur HTTPS 9443 pour le trafic de réplication (ce port peut être modifié)<br/><br/> Vérifiez que ces URL sont accessibles à partir du serveur d’administration : <br/>- *.hypervrecoverymanager.windowsazure.com<br/>- *.accesscontrol.windows.net<br/>- *.backup.windowsazure.com<br/>- *.blob.core.windows.net<br/>- *.store.core.windows.net<br/>-https://www.msftncsi.com/ncsi.txt<br/>- [ https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi "https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi")<br/><br/>Si vous avez des règles de pare-feu basées sur l’adresse IP sur le serveur, vérifiez qu’elles autorisent la communication vers Azure. Vous devez autoriser les [plages d’adresses IP Azure Datacenter](https://www.microsoft.com/download/details.aspx?id=41653) et le protocole HTTPS (433). Vous devrez également inscrire sur la liste approuvée les plages d’adresses IP pour la région de votre abonnement et pour l’Ouest des États-Unis. L’URL [https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi](https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi "https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi") permet de télécharger MySQL. 
**Hôte VMware ESXi/vCenter** : | Pour gérer vos machines virtuelles VMware, vous devez disposer d’un ou de plusieurs hyperviseurs VMware vSphere ESX/ESXi exécutant ESX/ESXi version 6.0, 5.5 ou 5.1 avec les dernières mises à jour.<br/><br/> Nous vous recommandons de déployer un serveur VMware vCenter pour gérer vos ordinateurs hôtes ESXi. Celui-ci doit exécuter vCenter version 6.0 ou 5.5 avec les dernières mises à jour.<br/><br/>Notez que Site Recovery ne prend pas en charge les nouvelles fonctionnalités de vCenter et vSphere 6.0 telles que Cross vCenter vMotion, les volumes virtuels et Storage DRS. La prise en charge de Site Recovery est limitée aux fonctionnalités qui étaient déjà disponibles dans la version 5.5.
**Machines protégées** : | **AZURE**<br/><br/>Les machines que vous voulez protéger doivent respecter les [conditions préalables Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements) pour la création de machines virtuelles Azure.<br><br/>Si vous voulez vous connecter aux machines virtuelles après le basculement, vous devez activer les connexions Bureau à distance sur le pare-feu local.<br/><br/>La capacité de disque individuel sur les machines protégées ne doit pas être supérieure à 1 023 Go. Une machine virtuelle peut comporter jusqu’à 64 disques (jusqu’à 64 To). Si la taille de vos disques est supérieure à 1 To, envisagez d’utiliser la réplication de base de données telle que SQL Server AlwaysOn ou Oracle Data Guard<br/><br/>Les clusters invités dont le disque est partagé ne sont pas pris en charge. Si vous disposez d’un déploiement en cluster, envisagez d’utiliser la réplication de base de données telle que SQL Server AlwaysOn ou Oracle Data Guard.<br/><br/>L’amorçage UEFI (Unified Extensible Firmware Interface)/EFI(Extensible Firmware Interface) n’est pas pris en charge.<br/><br/>Les noms des ordinateurs doivent contenir entre 1 et 63 caractères (lettres, chiffres et traits d’union). Le nom doit commencer par une lettre ou un chiffre et se terminer par une lettre ou un chiffre. Une fois qu’un ordinateur est protégé, vous pouvez modifier le nom Azure.<br/><br/>**Machines virtuelles VMware**<br/><br>Vous devez installer VMware vSphere PowerCLI 6.0. sur le serveur d’administration (serveur de configuration).<br/><br/>Les outils VMware doivent être installés et en cours d’exécution sur les machines virtuelles VMware à protéger.<br/><br/>Si la machine virtuelle source est équipée d’une association de cartes réseau, elle est convertie en carte réseau simple après le basculement vers Azure.<br/><br/>Si les machines virtuelles protégées disposent d’un disque iSCSI, Site Recovery convertit le disque iSCSI de machine virtuelle en fichier VHD quand la machine virtuelle bascule vers Azure. Si la cible iSCSI peut être atteinte par la machine virtuelle Azure, alors, elle se connecte à la cible iSCSI et peut voir essentiellement deux disques : le disque dur virtuel de la machine virtuelle Azure et le disque source iSCSI. Dans ce cas, vous devez vous déconnecter de la cible iSCSI qui s’affiche sur la machine virtuelle Azure ayant basculé.<br/><br/>[En savoir plus](#vmware-permissions-for-vcenter-access) sur les autorisations utilisateur VMware nécessaires pour Site Recovery<br/><br/> **MACHINES WINDOWS SERVER (sur les machines virtuelles VMware ou un serveur physique)**<br/><br/>Le serveur doit exécuter un système d’exploitation 64 bits pris en charge : Windows Server 2012 R2, Windows Server 2012 ou Windows Server 2008 R2 avec au moins SP1.<br/><br/>Le nom d’hôte, les points de montage, les noms d’appareils et le chemin du système Windows (par exemple C:\\Windows) doivent être en anglais uniquement.<br/><br/>Le système d’exploitation doit être installé sur le lecteur C:\\ et le disque du système d’exploitation doit être un disque de base Windows (le système d’exploitation ne doit pas être installé sur un disque dynamique Windows.)<br/><br/>Vous devez fournir un compte d’administrateur (administrateur local de l’ordinateur Windows) pour l’installation Push du service Mobilité sur les serveurs Windows. Si le compte fourni n'est pas un compte de domaine, vous devez désactiver le contrôle d'accès utilisateur distant sur l'ordinateur local. [En savoir plus](#install-the-mobility-service-with-push-installation).<br/><br/>Site Recovery prend en charge les machines virtuelles avec disque RDM. Lors de la restauration automatique, Site Recovery réutilisera le disque RDM si le disque de machine virtuelle source et le disque RDM d’origine sont disponibles. Si ce n’est pas le cas, pendant la restauration automatique, Site Recovery crée un fichier VMDK pour chaque disque.<br/><br/>**ORDINATEURS LINUX**<br/><br/>Vous avez besoin d’un système d’exploitation 64 bits : Red Hat Enterprise Linux 6.7 ; CentOS 6.5, 6.6, 6.7 ; Oracle Enterprise Linux 6.4, 6.5 exécutant le noyau compatible Red Hat ou Unbreakable Enterprise Kernel Release 3 (UEK3), SUSE Linux Enterprise Server 11 SP3.<br/><br/>Les fichiers /etc/hosts sur les ordinateurs protégés doivent contenir des entrées qui établissent une correspondance entre le nom d’hôte local et les adresses IP associées à toutes les cartes réseau. <br/><br/>Si vous voulez vous connecter à une machine virtuelle Azure exécutant Linux après le basculement à l’aide d’un client Secure Shell (SSH), vérifiez que le service Secure Shell de l’ordinateur protégé est configuré pour démarrer automatiquement au démarrage du système, et que les règles de pare-feu autorisent la connexion SSH à ce dernier.<br/><br/>Le nom d’hôte, les points de montage, les noms d’appareils et les chemins du système (par exemple, /etc/; /usr) doivent être en anglais uniquement.<br/><br/>La protection ne peut être activée que sur les ordinateurs Linux avec le stockage suivant : système de fichiers (EXT3, ETX4, ReiserFS, XFS) ; logiciel Multipath - Mappeur d’appareil (multi chemin) ; gestionnaire de volume : (LVM2). Les serveurs physiques avec stockage de contrôleur HP CCISS ne sont pas pris en charge. Le système de fichiers ReiserFS est pris en charge uniquement sur SUSE Linux Enterprise Server 11 SP3.<br/><br/>Site Recovery prend en charge les machines virtuelles avec disque RDM. Pendant la restauration automatique de Linux, Site Recovery ne réutilise pas le disque RDM. Il crée à la place un nouveau fichier VMDK pour chaque disque RDM correspondant. 


## Étape 1 : Créer un coffre

1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com/).
2. Développez **Services de données** > **Services de récupération**, puis cliquez sur **Coffre Site Recovery**.
3. Cliquez sur **Créer nouveau** > **Création rapide**.
4. Dans **Name**, entrez un nom convivial pour identifier le coffre.
5. Dans **Region**, sélectionnez la région géographique du coffre. Pour découvrir les régions prises en charge, référez-vous à la disponibilité géographique de la page [Détails des prix d'Azure Site Recovery](https://azure.microsoft.com/pricing/details/site-recovery/)
6. Cliquez sur **Créer un archivage**. ![Nouveau coffre](./media/site-recovery-vmware-to-azure-classic/quick-start-create-vault.png)

Vérifiez la barre d'état pour vous assurer que le coffre a été créé correctement. Le coffre apparaît comme **Actif** dans la page **Recovery Services**.

## Étape 2 : Configurer un réseau Azure

Configurer un réseau Azure afin que les machines virtuelles Azure soient connectées à un réseau après le basculement, et que la restauration automatique sur le site local puisse fonctionner comme prévu.

1. Dans le portail Azure > **Créer un réseau virtuel**, spécifiez le nom du réseau. Plage d’adresses IP et nom de sous-réseau.
2. Vous devrez ajouter VPN/ExpressRoute au réseau si vous devez effectuer la restauration automatique. VPN/ExpressRoute peut être ajouté au réseau, même après le basculement. 

[En savoir plus](../virtual-network/virtual-networks-overview.md) sur les réseaux Azure.

## Étape 3 : Installer les composants VMware

Si vous souhaitez répliquer des machines VMware virtuelles, installez les composants VMware suivants sur le serveur d’administration :

1. [Téléchargez](https://developercenter.vmware.com/tool/vsphere_powercli/6.0) et installez VMware vSphere PowerCLI 6.0.
2. Redémarrez le serveur.


## Étape 4 : Générer une clé d’inscription du coffre

1. À partir de l’administration serveur, ouvrez la console de récupération de Site Recovery dans Azure. Sur la page **Recovery Services**, cliquez sur le coffre pour ouvrir la page Démarrage rapide. Vous pouvez aussi ouvrir cette page à tout moment au moyen de l'icône.

	![Icône Quick Start](./media/site-recovery-vmware-to-azure-classic/quick-start-icon.png)

2. Dans la page **Démarrage rapide**, cliquez sur **Préparer les ressources cible** > **Télécharger une clé d’inscription**. Le fichier d’enregistrement est généré automatiquement. Il est valide pendant cinq jours après sa création.


## Étape 5 : Installer le serveur d’administration
> [AZURE.TIP] Assurez-vous que ces URL sont accessibles depuis le serveur d’administration :
>
- **.hypervrecoverymanager.windowsazure.com
- **.accesscontrol.windows.net
- **.backup.windowsazure.com
- **.blob.core.windows.net
- **.store.core.windows.net
- https://dev.mysql.com/get/archives/mysql-5.5/mysql-5.5.37-win32.msi
- https://www.msftncsi.com/ncsi.txt




[AZURE.VIDEO enhanced-vmware-to-azure-setup-registration]

1. Dans la page **Démarrage rapide**, téléchargez le fichier d’installation unifiée sur le serveur.
2. Exécutez le fichier d’installation pour démarrer le programme d’installation de l’Assistant d’installation unifiée de Site Recovery.
3. Dans **Avant de commencer**, sélectionnez **Installer le serveur de configuration et le serveur de traitement**. Selon la taille de votre déploiement, il se peut que vous ayez besoin de serveurs de traitement supplémentaire ultérieurement, mais pas lorsque vous configurez ce déploiement pour la première fois.

	![Avant de commencer](./media/site-recovery-vmware-to-azure-classic/combined-wiz1.png)

4. Dans **Installation de logiciels tiers**, cliquez sur **J'accepte** pour télécharger et installer MySQL.

	![Logiciel tiers](./media/site-recovery-vmware-to-azure-classic/combined-wiz2.png)

5. Dans **Paramètres Internet**, indiquez comment le fournisseur à installer sur le serveur doit se connecter à Azure Site Recovery par le biais d’Internet.

	- Si vous voulez que le fournisseur se connecte directement, sélectionnez **Se connecter directement sans proxy**.
	- Si vous voulez vous connecter au proxy actuellement défini sur le serveur, sélectionnez **Se connecter avec des paramètres de proxy existants**.
	- Si votre proxy existant nécessite une authentification, ou si vous voulez utiliser un proxy personnalisé pour la connexion du fournisseur, sélectionnez **Se connecter avec des paramètres de proxy personnalisés**.
	- Si vous utilisez un proxy personnalisé, vous devez spécifier l’adresse, le port et les données d’identification
	- Si vous utilisez un proxy, les URL suivantes doivent être accessibles par son intermédiaire :

	![Pare-feu](./media/site-recovery-vmware-to-azure-classic/combined-wiz3.png)

7. Dans **Vérification de la configuration requise**, le programme d’installation exécute une vérification de la configuration requise sur le serveur.

	![Configuration requise](./media/site-recovery-vmware-to-azure-classic/combined-wiz4.png)

>[AZURE.WARNING] Si un message d’avertissement lié au contrôle du paramètre de configuration requise **Synchronisation globale de l’heure** s’affiche, vérifiez que l’heure de l’horloge système est la même que celle du fuseau horaire.
 +	
 +	![Problème de synchronisation](./media/site-recovery-vmware-to-azure-classic/time-sync-issue.png)

8. Dans **Configuration MySQL**, créez des informations d’identification pour vous connecter à l’instance de serveur MySQL. Vous pouvez spécifier ces caractères spéciaux : ‘\_’ , ‘!’ , ‘@’ , ‘$’, ‘\\’, ‘%’.

	![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz5.png)

9. Dans **Détails de l’environnement**, indiquez si vous voulez répliquer des machines virtuelles VMware. Si c’est le cas, l’installation vérifie que PowerCLI 6.0 est installé.

	![MySQL](./media/site-recovery-vmware-to-azure-classic/combined-wiz6.png)

10. Dans **Emplacement d’installation**, sélectionnez où vous voulez installer les fichiers binaires et stocker le cache. Nous vous recommandons un lecteur cache avec 600 Go ou plus d’espace libre.

	![Emplacement d’installation](./media/site-recovery-vmware-to-azure-classic/combined-wiz7.png)

11. Dans **Sélection du réseau**, spécifiez l’écouteur (carte réseau et port SSL) sur lequel le serveur envoie et reçoit des données de réplication. Vous pouvez modifier la valeur par défaut du port (9443). En plus de ce port, le port 443 sera ouvert sur le serveur pour envoyer et recevoir des informations relatives à l’orchestration de la réplication. Le port 443 ne doit pas être utilisé pour les données de réplication.


	![Sélection du réseau](./media/site-recovery-vmware-to-azure-classic/combined-wiz8.png)

12. Dans **Inscription**, recherchez et sélectionnez la clé d’inscription que vous avez téléchargée à partir du coffre.

	![Inscription](./media/site-recovery-vmware-to-azure-classic/combined-wiz9.png)

13.  Dans **Résumé**, passez en revue les informations.

	![Résumé](./media/site-recovery-vmware-to-azure-classic/combined-wiz10.png)
>[AZURE.WARNING] Le proxy de Microsoft Azure Recovery Services Agent doit être installé. Une fois l’installation terminée, lancez une application nommée « Microsoft Azure Recovery Services Shell » à partir du menu Démarrer de Windows. Dans la fenêtre de commande qui s’ouvre, exécutez l’ensemble suivant de commandes pour configurer les paramètres du serveur proxy.
>
	$pwd = ConvertTo-SecureString -String ProxyUserPassword Set-OBMachineSetting -ProxyServer http://myproxyserver.domain.com -ProxyPort PortNumb – ProxyUserName domain\\username -ProxyPassword $pwd net stop obengine net start obengine
	 


### Exécution depuis la ligne de commande

Vous pouvez également exécuter l’Assistant unifié comme suit à partir de la ligne de commande :

    UnifiedSetup.exe [/ServerMode <CS/PS>] [/InstallDrive <DriveLetter>] [/MySQLCredsFilePath <MySQL credentials file path>] [/VaultCredsFilePath <Vault credentials file path>] [/EnvType <VMWare/NonVMWare>] [/PSIP <IP address to be used for data transfer] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <Passphrase file path>]

Où :

- /ServerMode : Obligatoire. Spécifie si l’installation doit installer les serveurs de configuration et de traitement, ou uniquement le serveur de traitement (utilisé pour installer des serveurs de traitement supplémentaires). Valeurs d’entrée : CS, PS.
- InstallDrive : Obligatoire. Spécifie le dossier dans lequel les composants sont installés.
- /MySQLCredFilePath. Obligatoire. Spécifie le chemin d’accès à un fichier dans lequel les informations d’identification du serveur MySQL sont stockées. Accéder au modèle pour créer le fichier.
- /VaultCredFilePath. Obligatoire. Emplacement du fichier d’informations d’identification de coffre
- / EnvType. Obligatoire. Type d’installation. Valeurs : VMware, NonVMware
- / PSIP et /CSIP. Obligatoire. Adresse IP du serveur de traitement et du serveur de configuration.
- /PassphraseFilePath. Obligatoire. Emplacement du fichier de phrase secrète.
- /ByPassProxy. facultatif. Spécifie que le serveur d’administration se connecte à Azure sans proxy.
- /ProxySettingsFilePath. facultatif. Spécifie les paramètres pour un proxy personnalisé (proxy par défaut sur le serveur qui requiert l’authentification ou proxy personnalisé) 




## Étape 6 : Configurer des informations d’identification pour le serveur vCenter

> [AZURE.VIDEO enhanced-vmware-to-azure-discovery]

Le serveur de traitement peut détecter automatiquement les machines virtuelles VMWare qui sont gérés par un serveur vCenter. Pour la détection automatique, Site Recovery a besoin d’un compte et d’informations d’identification permettant d’accéder au serveur vCenter. Cela n’est pas pertinent si vous vous contentez de répliquer des serveurs physiques uniquement.

Procédez comme suit :

1. Sur le serveur vCenter, créez un rôle (**Azure\_Site\_Recovery**) au niveau du serveur vCenter avec les [autorisations requises](#vmware-permissions-for-vcenter-access).
2. Attribuez le rôle **Azure\_Site\_Recovery** à un utilisateur de vCenter.

	>[AZURE.NOTE] Un compte d’utilisateur vCenter doté d’un rôle en lecture seule peut exécuter le basculement sans arrêter les ordinateurs sources protégés. Si vous souhaitez arrêter les machines virtuelles, vous aurez besoin du rôle Azure\_Site\_Recovery. Notez que si vous ne faites que migrer des machines virtuelles VMware vers Azure sans avoir à procéder à une restauration automatique, le rôle en lecture seule est suffisant.

3. Pour ajouter le compte, ouvrez **cspsconfigtool**. Il est disponible sous forme de raccourci sur le bureau et situé dans le dossier \\home\\svsystems\\bin [EMPLACEMENT D’INSTALLATION].
2. Sous l’onglet **Gérer les comptes**, cliquez sur **Ajouter un compte**.

	![Ajouter un compte](./media/site-recovery-vmware-to-azure-classic/credentials1.png)

3. Dans **Détails du compte**, ajoutez des informations d’identification pouvant être utilisées pour accéder au serveur vCenter. Notez que l’affichage du nom de compte dans le portail peut prendre plus de 15 minutes. Pour procéder à une mise à jour immédiate, cliquez sur Actualiser sous l’onglet **Serveurs de configuration**.

	![Détails](./media/site-recovery-vmware-to-azure-classic/credentials2.png)

## Étape 7 : Ajouter des serveurs vCenter ou des hôtes ESXi

Si vous répliquez des machines virtuelles VMware, vous devez ajouter un serveur vCenter (ou hôte ESXi).

1. Sous l’onglet **Serveurs** > **Serveurs de configuration**, sélectionnez le serveur de configuration, puis **Ajouter un serveur vCenter**.

	![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter1.png)

2. Ajoutez les détails du serveur vCenter ou de l’hôte ESXi., le nom du compte que vous avez spécifié pour accéder au serveur vCenter à l’étape précédente, et le serveur de traitement qui servira à détecter les machines virtuelles VMware qui sont gérées par le serveur vCenter. Notez que le serveur vCenter ou hôte ESXi doit se trouver dans le même réseau que le serveur sur lequel est installé le serveur de traitement.

	>[AZURE.NOTE] Si vous ajoutez le serveur vCenter ou un hôte ESXi à l’aide d’un compte qui ne dispose pas des privilèges d’administrateur sur le serveur vCenter ou hôte, assurez-vous que les comptes de serveur vCenter ou ESXi bénéficient de ces autorisations : centre de données, magasin de données, dossier, Jost, ressources, machine virtuelle, commutateur distribué vSphere. En outre, le serveur vCenter a besoin du privilège d’affichage de stockage.

	![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter2.png)

3. Une fois la détection terminée, le serveur vCenter apparaît sous l’onglet **Serveurs de configuration**.

	![vCenter](./media/site-recovery-vmware-to-azure-classic/add-vcenter3.png)
		

## Étape 8 : Créer un groupe de protection

> [AZURE.VIDEO enhanced-vmware-to-azure-protection]


Un groupe de protection contient des machines virtuelles ou des serveurs physiques qui partagent les mêmes paramètres de réplication.

1. Ouvrez **Éléments protégés** > **Groupe de protection** et cliquez pour ajouter un groupe de protection.

	![Créer un groupe de protection](./media/site-recovery-vmware-to-azure-classic/protection-groups1.png)

2. Dans la page **Spécifier les paramètres du groupe de protection**, entrez le nom du groupe et, dans **De**, sélectionnez le serveur de configuration sur lequel vous voulez créer le groupe. La valeur du champ **Cible** est Azure.

	![Paramètres du groupe de protection](./media/site-recovery-vmware-to-azure-classic/protection-groups2.png)

3. Sur la page **Spécifier les paramètres de réplication**, configurez les paramètres de réplication qui seront utilisés pour toutes les machines du groupe.

	![Réplication du groupe de protection](./media/site-recovery-vmware-to-azure-classic/protection-groups3.png)

	- **Cohérence multimachine virtuelle** : si vous activez cette option, elle crée des points de récupération cohérents au niveau de l’application, partagés entre les machines du groupe de protection. Ce paramètre est particulièrement important quand tous les ordinateurs du groupe de protection exécutent la même charge de travail. Tous les ordinateurs seront récupérés au même point de données. Il est disponible que vous répliquiez des machines virtuelles VMware ou des serveurs physiques Windows/Linux.
	- **Seuil d’objectif de point de récupération** : définit l’objectif de point de récupération. Des alertes sont générées lorsque la réplication de la protection continue des données dépasse la valeur de seuil d’objectif de point de récupération configurée.
	- **Rétention de point de récupération** : spécifie la fenêtre de rétention. Les ordinateurs protégés peuvent être récupérés à tout moment dans cette fenêtre.
	- **Fréquence des instantanés cohérents au niveau des applications** : spécifie la fréquence de création des points de récupération contenant des instantanés cohérents au niveau des applications.

Lorsque vous cliquez sur la coche, un groupe de protection est créé avec le nom spécifié. En outre, un deuxième groupe de protection est créé avec le nom <protection-group-name-Failback). Ce groupe de protection est utilisé si vous ne procédez pas à la restauration automatique du site local après le basculement vers Azure. Vous pouvez surveiller les groupes de protection à mesure qu’ils sont créés dans la page **Éléments protégés**.

## Étape 9 : Installer le service Mobilité

La première étape de l’activation de la protection des machines virtuelles et des serveurs physiques consiste à installer le service Mobilité. Il existe deux méthodes pour le faire :

- Transmettre et installer automatiquement le service sur chaque ordinateur du serveur de traitement ; Notez que lorsque vous ajoutez des machines à un groupe de protection, et que ces dernières exécutent déjà une version adéquate du service Mobilité, l’installation Push n’a pas lieu.
- Installez automatiquement le service à l’aide de votre méthode Push d’entreprise telle que WSUS ou System Center Configuration Manager. Assurez-vous que vous avez bien configuré le serveur d’administration avant de le faire.
- Procédez à l’installation manuellement sur chaque ordinateur que vous souhaitez protéger. Assurez-vous que vous avez configuré le serveur d’administration avant de le faire.


### Installer le service Mobilité par le biais d’une installation Push

Quand vous ajoutez des ordinateurs à un groupe de protection, le service de mobilité est automatiquement envoyé et installé sur chaque ordinateur par le serveur de traitement.


#### Préparez une opération Push automatique sur les machines Windows 

Voici comment préparer les ordinateurs Windows afin que le service Mobilité puisse être installé automatiquement par le serveur de traitement.

1.  Créez un compte pouvant être utilisé par le serveur de traitement pour accéder à la machine. Le compte doit disposer des privilèges administrateur (local ou de domaine). Notez que ces informations d’identification ne sont utilisées que pour l’installation Push.

	>[AZURE.NOTE] Si vous n’utilisez pas un compte de domaine, vous devez désactiver le contrôle d’accès utilisateur distant sur l’ordinateur local. Pour ce faire, dans le registre situé sous HKEY\_LOCAL\_MACHINE\\SOFTWARE\\Microsoft\\Windows\\CurrentVersion\\Policies\\System, ajoutez l’entrée de registre DWORD LocalAccountTokenFilterPolicy avec une valeur inférieure de 1. Pour ajouter l’entrée de Registre à partir d’une commande ouverte de l’interface de ligne de commande ou avec PowerShell, entrez **`REG ADD HKEY_LOCAL_MACHINE\SOFTWARE\Microsoft\Windows\CurrentVersion\Policies\System /v LocalAccountTokenFilterPolicy /t REG_DWORD /d 1`**.

2.  Dans le Pare-feu Windows de l’ordinateur à protéger, sélectionnez **Autoriser une application ou une fonctionnalité à franchir le pare-feu**, puis activez **Partage de fichiers et d’imprimantes** et **Infrastructure de gestion Windows**. Pour les ordinateurs qui appartiennent à un domaine, vous pouvez configurer la stratégie de pare-feu avec un objet de stratégie de groupe.

	![Paramètres du pare-feu](./media/site-recovery-vmware-to-azure-classic/mobility1.png)

2. Ajoutez le compte que vous venez de créer :

	- Ouvrez **cspsconfigtool**. Il est disponible sous forme de raccourci sur le bureau et situé dans le dossier \\home\\svsystems\\bin [EMPLACEMENT D’INSTALLATION].
	- Sous l’onglet **Gérer les comptes**, cliquez sur **Ajouter un compte**.
	- Ajoutez le compte que vous venez de créer. Après avoir ajouté le compte, vous devez fournir ces informations d’identification au moment où vous ajoutez un ordinateur à un groupe de protection.


#### Préparez une opération Push automatique sur les serveurs Linux

1.	Vérifiez que l’ordinateur Linux à protéger est pris en charge comme décrit dans [Conditions préalables locales](#on-premises-prerequisites). Vérifiez qu’il existe une connectivité réseau entre l’ordinateur à protéger et le serveur d’administration qui exécute le serveur de traitement. 

2.	Créez un compte pouvant être utilisé par le serveur de traitement pour accéder à la machine. Le compte est un utilisateur racine sur le serveur Linux source. Notez que ces informations d’identification ne sont utilisées que pour l’installation Push.

	- Ouvrez **cspsconfigtool**. Il est disponible sous forme de raccourci sur le bureau et situé dans le dossier \\home\\svsystems\\bin [EMPLACEMENT D’INSTALLATION].
	- Sous l’onglet **Gérer les comptes**, cliquez sur **Ajouter un compte**.
	- Ajoutez le compte que vous venez de créer. Après avoir ajouté le compte, vous devez fournir ces informations d’identification au moment où vous ajoutez un ordinateur à un groupe de protection.

3.	Vérifiez que les fichiers /etc/hosts sur le serveur Linux source contiennent des entrées qui mappent le nom d’hôte local sur les adresses IP associées à toutes les cartes réseau.
4.	Installez les packages openssh, openssh-server et openssl les plus récents sur l'ordinateur que vous souhaitez protéger.
5.	Vérifiez que SSH est activé et exécuté sur le port 22. 
6.	Activez le sous-système SFTP et l'authentification par mot de passe dans le fichier sshd\_config comme suit : 

	- Connectez-vous en tant qu’utilisateur racine.
	- Dans le fichier /etc/ssh/sshd\_config, recherchez la ligne commençant par PasswordAuthentication.
	- Supprimez les commentaires de la ligne et remplacez la valeur **non** par **oui**.
	- Recherchez la ligne qui commence par **Subsystem** et supprimez les commentaires de la ligne.
 
		![Linux](./media/site-recovery-vmware-to-azure-classic/mobility2.png)


### Installer le service de mobilité manuellement

Les programmes d’installation sont disponibles dans C:\\Program Files (x86)\\Microsoft Azure Site Recovery\\home\\svsystems\\pushinstallsvc\\repository.

Système d’exploitation source | Fichier d’installation du service Mobilité
--- | ---
Windows Server (64 bits uniquement) | Microsoft-ASR\_UA\_9.*.0.0\_Windows\_* release.exe
CentOS 6.4, 6.5, 6.6 (64 bits uniquement) | Microsoft-ASR\_UA\_9.*.0.0\_RHEL6-64\_*release.tar.gz SUSE Linux Enterprise Server 11 SP3 (64 bits uniquement) | Microsoft-ASR\_UA\_9.*.0.0\_SLES11-SP3-64\_*release.tar.gz
Oracle Enterprise Linux 6.4, 6.5 (64 bits uniquement) | Microsoft-ASR\_UA\_9.*.0.0\_OL6-64\_*release.tar.gz


#### Installer manuellement sur un serveur Windows


1. Téléchargez et exécutez le programme d’installation approprié.
2. Dans **Avant de commencer**, sélectionnez **Service Mobilité**.

	![Service de mobilité](./media/site-recovery-vmware-to-azure-classic/mobility3.png)

3. Dans **Détails du serveur de configuration**, spécifiez l’adresse IP du serveur d’administration et la phrase secrète qui a été générée quand vous avez installé les composants du serveur d’administration. Vous pouvez récupérer la phrase secrète en exécutant : **<SiteRecoveryInstallationFolder>\\home\\sysystems\\bin\\genpassphrase.exe –n** sur le serveur d’administration.

	![Service de mobilité](./media/site-recovery-vmware-to-azure-classic/mobility6.png)

4. Dans **Emplacement de l’installation**, conservez l’emplacement par défaut, puis cliquez sur **Suivant** pour commencer l’installation.
5. Dans **Progression de l’installation**, surveillez l’installation et redémarrez l’ordinateur si vous y êtes invité.

Vous pouvez également procéder à l’installation à partir de la ligne de commande :

UnifiedAgent.exe [/Role <Agent/MasterTarget>] [/InstallLocation <répertoire\_installation>] [/CSIP <IP address of CS to be registered with>] [/PassphraseFilePath <chemin\_phrase\_secrète>] [/LogFilePath <Log File Path>]

Où :

- /Role : Obligatoire Indique si le service Mobilité doit être installé.
- /InstallLocation : Obligatoire. Spécifie l’emplacement d’installation du service.
- / PassphraseFilePath : Obligatoire. Indiquez la phrase secrète de la phrase secrète de configuration.
- /LogFilePath : Obligatoire. Spécifie l’emplacement des fichiers journaux d’installation 

#### Modifier l’adresse IP du serveur d’administration

Après avoir exécuté l’Assistant, vous pouvez modifier l’adresse IP du serveur d’administration comme suit :

1. Ouvrez le fichier hostconfig.exe (situé sur le bureau).
2. Sous l’onglet **Global**, vous pouvez modifier l’adresse IP du serveur d’administration.

	>[AZURE.NOTE] Vous ne devez modifier que l’adresse IP du serveur d’administration. Le numéro de port des communications de serveur d’administration doit être 443 et l’option Utiliser HTTPS devrait être activé. La phrase secrète ne doit pas être modifiée.

	![Adresse IP du serveur d’administration](./media/site-recovery-vmware-to-azure-classic/host-config.png)

#### Installez manuellement sur un serveur Linux :

1. Copiez l’archive tar appropriée en fonction du tableau ci-dessus vers l’ordinateur Linux que vous souhaitez protéger.
2. Ouvrez un interpréteur de commandes et extrayez l’archive tar compressée vers un chemin local en exécutant : `tar -xvzf Microsoft-ASR_UA_8.5.0.0*`
3. Créez un fichier passphrase.txt dans le répertoire local. Vous y extrairez les contenus de l’archive tar. Pour cela, copiez la phrase secrète à partir de C:\\ProgramData\\Microsoft Azure Site Recovery\\private\\connection.passphrase sur le serveur d’administration et enregistrez-la dans passphrase.txt en exécutant *`echo <passphrase> >passphrase.txt`* dans l’interpréteur de commandes.
4. Installez le service de mobilité en entrant *`sudo ./install -t both -a host -R Agent -d /usr/local/ASR -i <IP address> -p <port> -s y -c https -P passphrase.txt`*.
5. Spécifiez l’adresse IP interne du serveur d’administration et assurez-vous que le port 443 est sélectionné.

**Vous pouvez également procéder à l’installation à partir de la ligne de commande** :

1. Copiez la phrase secrète de C:\\Program Files (x86)\\InMage Systems\\private\\connection sur le serveur d’administration et enregistrez-le en tant que « passphrase.txt » sur le serveur d’administration. Exécutez ensuite ces commandes. Dans notre exemple, l’adresse IP du serveur est 104.40.75.37 et le port HTTPS doit être 443 :

Pour installer sur un serveur de production :

    ./install -t both -a host -R Agent -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt
 
Pour procéder à l’installation sur le serveur cible :


    ./install -t both -a host -R MasterTarget -d /usr/local/ASR -i 104.40.75.37 -p 443 -s y -c https -P passphrase.txt


## Étape 10 : activation de la protection d’une machine

Ajoutez des machines virtuelles à un groupe de protection pour activer leur protection. Avant de commencer, si vous protégez des machines virtuelles VMware, notez les points suivants :

- Les machines virtuelles sont détectées toutes les 15 minutes et il peut s’écouler jusqu’à 15 minutes entre leur détection et leur affichage dans le portail Site Recovery.
- La mise à jour avec les modifications de l’environnement sur la machine virtuelle (par exemple, l’installation d’outils VMware) dans Site Recovery peut prendre plus de 15 minutes.
- Vous pouvez vérifier l’heure de la dernière détection de machines virtuelles VMware dans le champ **Dernier contact** correspondant au serveur vCenter ou à l’hôte ESXi sous l’onglet **Serveurs de configuration**.
- Si vous avez déjà créé un groupe de protection et que vous ajoutez ensuite un serveur vCenter ou un hôte ESXi, l’actualisation du portail Azure Site Recovery et l’affichage des machines virtuelles dans la boîte de dialogue **Ajouter des ordinateurs à un groupe de protection** peuvent prendre plus de 15 minutes.
- Si vous souhaitez ajouter immédiatement des ordinateurs au groupe de protection sans attendre la détection planifiée, mettez en surbrillance le serveur de configuration (ne cliquez pas dessus) et cliquez sur le bouton **Actualiser**.

En outre, notez que :

- Nous vous recommandons de structurer vos groupes de protection afin qu’ils reflètent vos charges de travail. Par exemple, ajoutez des machines exécutant une application spécifique dans le même groupe.
- Quand vous ajoutez des ordinateurs à un groupe de protection, le service Mobilité est automatiquement envoyé et installé sur chaque ordinateur par le serveur de traitement (s’il n’est pas encore installé). Notez que le mécanisme Push doit procéder à la préparation décrite à l’étape précédente.


Ajouter des ordinateurs à un groupe de protection :

1. Cliquez sur **Éléments protégés** > **Groupe de protection** > **Machines** > Ajouter des machines. 
2. Dans **Sélectionner les machines virtuelles** si vous protégez des machines virtuelles VMware, sélectionnez un serveur vCenter qui gère vos machines virtuelles (ou l’hôte EXSi sur lequel elles sont exécutées), puis sélectionnez les machines.

	![Activer la protection](./media/site-recovery-vmware-to-azure-classic/enable-protection2.png)

3.  Dans **Sélectionner les machines virtuelles** si vous protégez des serveurs physiques, dans l’assistant **Ajouter des machines physiques**, entrez l’adresse IP et un nom convivial. Sélectionnez ensuite la famille de systèmes d'exploitation.

	![Activer la protection](./media/site-recovery-vmware-to-azure-classic/enable-protection1.png)
		
4. Dans **Spécifier les ressources cible**, sélectionnez le compte de stockage que vous utilisez pour la réplication, puis déterminez si les paramètres doivent être utilisés pour toutes les charges de travail. Notez que les comptes de stockage Premium ne sont pas pris en charge actuellement.

	>[AZURE.NOTE] Nous ne prenons pas en charge le déplacement des comptes Storage créés à l’aide du [nouveau portail Azure](../storage/storage-create-storage-account.md) dans les groupes de ressources.

	![Activer la protection](./media/site-recovery-vmware-to-azure-classic/enable-protection3.png)

5. Dans **Spécifier les comptes**, sélectionnez le compte que vous avez [configuré](#install-the-mobility-service-with-push-installation) pour être utilisé dans le cadre de l’installation automatique du service Mobilité.

	![Activer la protection](./media/site-recovery-vmware-to-azure-classic/enable-protection4.png)

6. Cochez la case pour terminer l'ajout d'ordinateurs au groupe de protection et démarrer la réplication initiale pour chaque ordinateur.

	>[AZURE.NOTE] Si l’installation Push a été préparée, le service Mobilité est automatiquement installé sur les machines qui ne l’ont pas encore au moment de l’ajout au groupe de protection. Une fois l’installation du service terminé, une tâche de protection démarre, puis échoue. Après la panne, vous devez redémarrer manuellement chaque ordinateur sur lequel le service Mobilité est installé. Après le redémarrage, la tâche de protection recommence et la réplication initiale se produit.

Vous pouvez surveiller l’état sur la page **Tâches**.

![Activer la protection](./media/site-recovery-vmware-to-azure-classic/enable-protection5.png)

Vous pouvez aussi contrôler l’état de la protection dans **Éléments protégés** > <protection group name> > **Machines virtuelles**. Une fois la réplication initiale terminée et les données synchronisées, l’état de la machine passe à **Protégé**.

![Activer la protection](./media/site-recovery-vmware-to-azure-classic/enable-protection6.png)


## Étape 11 Définir les propriétés de l’ordinateur protégé

1. Dès qu’un ordinateur est à l’état **Protégé**, vous pouvez configurer ses propriétés de basculement. Dans les détails du groupe de protection, sélectionnez l'ordinateur et ouvrez l'onglet **Configurer**.
2. Site Recovery suggère automatiquement des propriétés de la machine virtuelle Azure et détecte les paramètres réseau locaux. 

	![Définir les propriétés des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/vm-properties1.png)

3. Vous pouvez modifier ces paramètres :

	-  **Nom de machine virtuelle Azure** : il s’agit du nom attribué à la machine dans Azure après le basculement. Le nom doit satisfaire aux exigences Azure.

	-  **Taille de machine virtuelle Azure** : le nombre de cartes réseau est déterminé par la taille spécifiée pour la machine virtuelle cible. [En savoir plus](../virtual-machines/virtual-machines-linux-sizes.md/#size-tables) sur les tailles et les cartes. Notez les points suivants :

		- Quand vous modifiez la taille d’une machine virtuelle et que vous enregistrez les paramètres, le nombre de cartes réseau change à la prochaine ouverture de l’onglet **Configurer**. Le nombre de cartes réseau des machines virtuelles cible est au minimum le nombre de cartes réseau sur la machine virtuelle source et au maximum le nombre de cartes réseau prises en charge par la machine virtuelle choisie en fonction de sa taille. 
			- Si le nombre de cartes réseau sur la machine source est inférieur ou égal au nombre de cartes autorisé pour la taille de la machine cible, la cible présente le même nombre de cartes que la source.
			- Si le nombre de cartes de la machine virtuelle source dépasse la valeur de taille cible autorisée, la taille cible maximale est utilisée.
			- Par exemple, si une machine source présente deux cartes réseau et que la taille de la machine cible en accepte quatre, la machine cible présentera deux cartes. Si la machine source inclut deux cartes, mais que la taille cible prise en charge accepte une seule carte, la machine cible présentera une seule carte.
		- Si la machine virtuelle présente plusieurs cartes réseau, l’ensemble des cartes doit être connecté au même réseau Microsoft Azure. 
	- **Réseau Azure** : vous devez spécifier un réseau Azure auquel les machines virtuelles Azure sont connectées après le basculement. Si vous n’en spécifiez pas, les machines virtuelles Azure ne seront connectées à aucun réseau. En outre, vous devez spécifier un réseau Azure si vous souhaitez effectuer une restauration automatique depuis Azure vers le site local. La restauration automatique exige une connexion VPN entre un réseau Azure et un réseau local.	
	- **Sous-réseau/Adresse IP Azure** : pour chaque carte réseau, sélectionnez le sous-réseau auquel la machine virtuelle Azure doit se connecter. Notez les points suivants :
		- Si la carte réseau de l’ordinateur source est configurée pour utiliser une adresse IP statique, vous pouvez spécifier une adresse IP statique pour la machine virtuelle Azure. Si vous ne fournissez pas d’adresse IP statique, alors n’importe quelle adresse IP disponible sera allouée. Si l’adresse IP cible est spécifiée, mais qu’elle est déjà utilisée par une autre machine virtuelle dans Azure, le basculement échoue. Si la carte réseau de l’ordinateur source est configurée pour utiliser le protocole DHCP, vous aurez une configuration DHCP pour Azure.

## Étape 12 : Créer un plan de récupération et exécuter le basculement.

> [AZURE.VIDEO enhanced-vmware-to-azure-failover]

Vous pouvez exécuter un basculement pour une seule machine, ou basculer plusieurs machines effectuant la même tâche ou exécutant la même charge de travail. Pour basculer plusieurs ordinateurs en même temps, vous devez les ajouter à un plan de récupération.

### Créer un plan de récupération

1. Dans la page **Plans de récupération**, cliquez sur **Ajouter un plan de récupération**, puis ajoutez un plan de récupération. Spécifiez les détails du plan et sélectionnez **Azure** comme cible.

	![Configurer le plan de récupération](./media/site-recovery-vmware-to-azure-classic/recovery-plan1.png)

2. Dans **Sélectionner les machines virtuelles**, sélectionnez un groupe de protection, puis sélectionnez les ordinateurs du groupe à ajouter au plan de récupération.

	![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/recovery-plan2.png)

Vous pouvez personnaliser le plan pour créer des groupes et définir l’ordre dans lequel les ordinateurs du plan de récupération sont basculés. Vous pouvez également ajouter des scripts et des invites pour des actions manuelles. Vous pouvez créer des scripts manuellement ou à l’aide de [Runbooks Azure Automation](site-recovery-runbook-automation.md). [En savoir plus](site-recovery-create-recovery-plans.md) sur la personnalisation des plans de récupération.

## Exécuter un basculement

Avant d’exécuter un basculement, notez que :


- Assurez-vous que le serveur d’administration fonctionne et est disponible, sinon le basculement échouera.
- Si vous exécutez un basculement non planifié, notez que :

	- qu’il est préférable d’arrêter les machines principales avant d’exécuter un basculement non planifié lorsque c’est possible. Vous êtes ainsi sûr que les machines source et les réplicas ne fonctionnent pas en même temps. Si vous répliquez des machines virtuelles VMware, vous exécutez un basculement non planifié et vous pouvez indiquer que Site Recovery doit faire au mieux pour arrêter les ordinateurs source. Selon l’état du site principal, cela peut fonctionner ou peut ne pas fonctionner. Si vous répliquez des serveurs physiques, Site Recovery ne propose pas cette option. 
	- Lorsque vous effectuez un basculement non planifié, la réplication des données depuis les ordinateurs principaux s’arrête et les différences dans les données ne sont pas transférées après qu’un basculement non planifié ait commencé.
	
- Si vous voulez vous connecter à la machine virtuelle du réplica dans Azure après le basculement, activez Connexion Bureau à distance sur la machine virtuelle avant d’exécuter le test de basculement, et autorisez la connexion RDP via le pare-feu. Vous devez également autoriser RDP sur le point de terminaison public de la machine virtuelle Azure après le basculement. Suivez ces [bonnes pratiques](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) pour vous assurer que RDP fonctionne après un basculement.


### Exécution d’un test de basculement

Exécutez un test de basculement afin de simuler vos processus de basculement et de récupération dans un réseau isolé qui n’affecte pas votre environnement de production et la réplication régulière se poursuit normalement. Le test de basculement commence sur la source et vous pouvez l’exécuter de plusieurs façons :

- **Ne pas spécifier de réseau Azure** : si vous exécutez un test de basculement sans réseau, le test vérifie simplement que les machines virtuelles démarrent et s’affichent correctement dans Azure. Les machines virtuelles ne seront connectées à aucun réseau Azure après le basculement.
- **Spécifier un réseau Azure** : ce type de contrôle de basculement vérifie que l’ensemble de l’environnement de réplication s’affiche comme prévu et que les machines virtuelles Azure sont connectées au réseau spécifié. 


1. Dans la page **Plans de récupération**, sélectionnez le plan et cliquez sur **Test de basculement**.

	![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/test-failover1.png)

2. Dans **Confirmer le test de basculement**, sélectionnez **Aucun** pour indiquer que vous ne voulez pas utiliser de réseau Azure pour le test de basculement, ou sélectionnez le réseau auquel les machines virtuelles de test sont connectées après le basculement. Cochez la case pour démarrer le basculement.

	![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/test-failover2.png)

3. Surveillez la progression de l’opération sous l’onglet **Tâches**.

	![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/test-failover3.png)

4. Une fois le basculement terminé, vous devez également voir la machine Azure de réplica apparaître dans le portail Azure > **Machines virtuelles**. Si vous souhaitez initier une connexion RDP à la machine virtuelle Azure, vous devez ouvrir le port 3389 sur le point de terminaison de la machine virtuelle.

5. Une fois cette opération terminée, lorsque le basculement atteint la phase de fin de test, cliquez sur Terminer le test pour achever l’opération. Cliquez sur Notes pour consigner et enregistrer d’éventuelles observations associées au test de basculement.

6. Cliquez sur **Le test de basculement est terminé.** L’environnement de test est alors automatiquement nettoyé. Une fois cette opération terminée, le basculement de test affiche l’état **Terminé**. Toutes les machines virtuelles ou les éléments créés automatiquement lors du basculement de test sont supprimés. Remarque : si un test de basculement s’étend sur plus de deux semaines, le système le force à se terminer.


	


### Exécuter un basculement non planifié

Le basculement non planifié est lancé depuis Azure et peut être effectué même si le site principal n’est pas disponible.


1. Dans la page **Plans de récupération**, sélectionnez le plan et cliquez sur **Basculement** > **Basculement non planifié**.

	![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/unplanned-failover1.png)

2. Si vous répliquez des machines virtuelles VMware, vous pouvez essayer et arrêter les machines virtuelles locales. C’est tout ce qu’il y a à faire et le basculement se poursuit, que l’effort se termine ou non. En cas d’échec, les détails de l’erreur apparaissent sous l’onglet **Tâches** > **Tâches de basculement non planifiées**.

	![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/unplanned-failover2.png)

	>[AZURE.NOTE] Cette option n’est pas disponible si vous répliquez des serveurs physiques. Vous devez les tester et les arrêter manuellement si possible.
	
3. Dans **Confirmer le basculement**, vérifiez le sens du basculement (vers Azure) et sélectionnez le point de récupération à utiliser pour le basculement. Si vous avez activé le multimachine au moment où vous avez configuré les propriétés de réplication, vous pouvez récupérer jusqu’à la dernière application ou jusqu’au dernier point de récupération cohérent en cas d’incident. Vous pouvez également sélectionner un **point de récupération personnalisé** pour effectuer la récupération à un point antérieur dans le temps. Cochez la case pour démarrer le basculement.

	![Ajouter des machines virtuelles](./media/site-recovery-vmware-to-azure-classic/unplanned-failover3.png)

3. Attendez que la tâche de basculement non planifié se termine. Vous pouvez surveiller la progression du basculement sous l’onglet **Tâches**. Remarque : même si des erreurs se produisent lors d’un basculement non planifié, le plan de récupération s’exécute jusqu’à la fin. Vous devriez également voir la machine Azure de réplica apparaître dans Machines virtuelles dans le portail Azure.

### Se connecter à des machines virtuelles Azure répliquées après basculement

Pour vous connecter à des machines virtuelles répliquées dans Azure après le basculement, vous devez disposer des éléments qui suivent :

1. Une connexion Bureau à distance doit être activée sur l’ordinateur principal.
2. Le pare-feu Windows sur l’ordinateur principal doit autoriser RDP.
3. Après le basculement, vous devrez ajouter RDP au point de terminaison public de la machine virtuelle Azure.

[En savoir plus](http://social.technet.microsoft.com/wiki/contents/articles/31666.troubleshooting-remote-desktop-connection-after-failover-using-asr.aspx) sur la configuration de cette fonction.


## Déployer d’autres serveurs de traitement

Si vous devez monter en charge votre déploiement au-delà de 200 machines source ou si votre taux d’évaluation quotidien total dépasse 2 To, vous aurez besoin de serveurs de traitement supplémentaires pour gérer le volume de trafic. Pour configurer un contrôle de serveur de traitement supplémentaire, vérifiez les exigences dans [Serveurs de traitement supplémentaires](#additional-process-servers) et suivez les instructions données pour configurer le serveur de traitement. Après avoir configuré le serveur, vous pouvez configurer des ordinateurs source pour les utiliser.

### Configurer un serveur de traitement supplémentaire

Pour configurer un serveur de traitement supplémentaire, procédez comme suit :

- Exécutez l’Assistant unifié pour configurer un serveur d’administration en tant que processus de serveur uniquement.
- Si vous souhaitez gérer la réplication des données en utilisant le nouveau serveur de traitement, vous devrez migrer vos machines protégées pour exécuter l’opération.

### Installer le serveur de traitement

1. Sur la page de démarrage rapide, téléchargez le fichier d’installation unifiée pour l’installation du composant Site Recovery. Exécutez le programme d’installation.
2. Dans **Avant de commencer**, sélectionnez **Ajouter des serveurs de traitement supplémentaires pour effectuer un déploiement avec montée en puissance**.

	![Ajouter un serveur de traitement](./media/site-recovery-vmware-to-azure-classic/add-ps1.png)

3. Terminez l’assistant comme vous l’avez fait pendant la [configuration](#step-5-install-the-management-server) du premier serveur d’administration.
4. Dans **Détails du serveur de configuration**, spécifiez l’adresse IP du serveur d’administration d’origine sur lequel vous avez installé le serveur de configuration et la phrase secrète. Sur le serveur d’administration d’origine, exécutez **<SiteRecoveryInstallationFolder>\\home\\sysystems\\bin\\genpassphrase.exe –n** pour obtenir la phrase secrète.

	![Ajouter un serveur de traitement](./media/site-recovery-vmware-to-azure-classic/add-ps2.png)

### Migrez les machines pour utiliser le nouveau serveur de traitement

1. Ouvrez **Serveurs de configuration** > **Serveur** > Nom du serveur d’administration d’origine > **Détails du serveur**.

	![Valider un serveur de traitement](./media/site-recovery-vmware-to-azure-classic/update-process-server1.png)

2. Dans la liste **Serveurs de traitement**, cliquez sur **Modifier le serveur de traitement** à côté du serveur que vous souhaitez modifier.

	![Valider un serveur de traitement](./media/site-recovery-vmware-to-azure-classic/update-process-server2.png)

3. Dans **Modifier le serveur de traitement** > **Serveur de traitement cible**, sélectionnez le nouveau serveur d’administration, puis sélectionnez les machines virtuelles que le nouveau serveur de traitement va gérer. Cliquez sur l’icône d’information pour obtenir des informations sur le serveur. L’espace moyen nécessaire à la réplication de chaque machine virtuelle sélectionnée vers le nouveau serveur de traitement s’affiche pour vous aider à prendre des décisions quant à la charge. Cliquez sur la coche pour commencer la réplication vers le nouveau serveur de traitement.

	![Valider un serveur de traitement](./media/site-recovery-vmware-to-azure-classic/update-process-server3.png)

	


## Autorisations VMware pour l’accès au vCenter

Le serveur de traitement peut découvrir automatiquement les machines virtuelles sur un serveur vCenter. Pour exécuter la détection automatique, vous devez définir un rôle (Azure\_Site\_Recovery) au niveau du serveur vCenter pour permettre à Site Recovery récupération de Site accéder au serveur vCenter. Notez que si vous n’avez qu’à migrer des machines VMware vers Azure et que vous n’avez pas besoin de restauration automatique d’Azure, vous devez définir un rôle en lecture seule suffisant. Les autorisations se définissent comme décrit dans [Étape 6 : Configurer les informations d’identification pour le serveur vCenter](#step-6-set-up-credentials-for-the-vcenter-server). Les autorisations de rôle sont résumées dans le tableau suivant.

**Rôle** | **Détails** | **Autorisations**
--- | --- | ---
Rôle Azure\_Site\_Recovery | Détection de machine virtuelle VMware |Attribuez ces privilèges au serveur v-Center :<br/><br/>Magasin de données -> Allouer de l’espace, Parcourir le magasin de données, Opérations de fichier de bas niveau, Supprimer le fichier, Mettre à jour les fichiers de machine virtuelle<br/><br/>Réseau -> Attribution de réseau<br/><br/>Ressources -> Affecter une machine virtuelle à un pool de ressource, Migrer une machine virtuelle désactivée, Migrer une machine virtuelle désactivée<br/><br/>Tâches -> Créer une tâche, mettre à jour une tâche <br/><br/>Machine virtuelle -> Configuration<br/><br/>Machine virtuelle -> Interagir -> Répondre à la question, Connexion d’appareil, Configurer un support de CD, Configurer une disquette, Mettre hors tension, Mettre sous tension, Installation des outils VMware<br/><br/>Machine virtuelle -> Inventorier -> Créer, Inscrire, Désinscrire<br/><br/>Machine virtuelle -> Approvisionnement -> Autoriser le téléchargement de machines virtuelles, Autoriser le chargement de fichiers de machine virtuelle<br/><br/>Machine virtuelle -> Instantanés -> Supprimer les instantanés
Rôle d’utilisateur vCenter | Découverte/Basculement de machine virtuelle VMware sans arrêt de la machine virtuelle source | Attribuez ces privilèges au serveur v-Center :<br/><br/>Objet de centre de données -> Propager vers l’objet enfant, rôle = Lecture seule <br/><br/>L’utilisateur est affecté au niveau du centre de données et a donc accès à tous les objets présents dans le centre de données. Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec **Propager vers l’objet enfant** aux objets enfants (hôtes ESX, magasins de données, machines virtuelles et réseaux). 
Rôle d’utilisateur vCenter | Basculement et restauration automatique | Attribuez ces privilèges au serveur v-Center :<br/><br/>Objet de centre de données -> Propager vers l’objet enfant, rôle = Azure\_Site\_Recovery <br/><br/>L’utilisateur est affecté au niveau du centre de données et a donc accès à tous les objets présents dans le centre de données. Pour restreindre l’accès, attribuez le rôle **Aucun accès** avec **Propager vers l’objet enfant** à l’objet enfant (hôtes ESX, magasins de données, machines virtuelles et réseaux). 



## Informations et remarques relatives aux logiciels tiers

Do Not Translate or Localize

The software and firmware running in the Microsoft product or service is based on or incorporates material from the projects listed below (collectively, “Third Party Code”). Microsoft is the not original author of the Third Party Code. The original copyright notice and license, under which Microsoft received such Third Party Code, are set forth below.

The information in Section A is regarding Third Party Code components from the projects listed below. Such licenses and information are provided for informational purposes only. This Third Party Code is being relicensed to you by Microsoft under Microsoft's software licensing terms for the Microsoft product or service.

The information in Section B is regarding Third Party Code components that are being made available to you by Microsoft under the original licensing terms.

The complete file may be found on the [Microsoft Download Center](http://go.microsoft.com/fwlink/?LinkId=529428). Microsoft reserves all rights not expressly granted herein, whether by implication, estoppel or otherwise.

## Étapes suivantes

[Découvrez la restauration automatique](site-recovery-failback-azure-to-vmware-classic.md) qui permet de récupérer vos machines basculées s’exécutant dans Azure dans votre environnement local.

<!---HONumber=AcomDC_0323_2016-->