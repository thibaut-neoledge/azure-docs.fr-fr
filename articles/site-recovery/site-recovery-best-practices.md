<properties
	pageTitle="Préparation du déploiement d’Azure Site Recovery | Microsoft Azure"
	description="Cet article décrit comment préparer le déploiement de la réplication avec Azure Site Recovery."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="03/29/2016"
	ms.author="raynew"/>

# Préparer le déploiement d’Azure Site Recovery

Lisez cet article pour avoir une vue d’ensemble de haut niveau de la configuration requise pour le déploiement en rapport avec chaque scénario de réplication pris en charge par le service Azure Site Recovery. Après avoir pris connaissance de la configuration générale requise pour chaque scénario, suivez le lien vers les détails de déploiement spécifiques dans la section relative aux conditions préalables à chaque déploiement.

Après lecture de cet article, publiez vos commentaires ou questions au bas de celui-ci, ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Vue d'ensemble

Les organisations ont besoin d’une stratégie de continuité des activités et de récupération d’urgence qui détermine la façon dont les applications, les charges de travail et les données demeurent opérationnelles et disponibles pendant les temps d’arrêt prévus et imprévus et comment rétablir au plus vite des conditions de travail normales. Une telle stratégie repose sur des solutions qui visent à sécuriser et à récupérer les données d’entreprise et à rendre les charges de travail disponibles en continu quand un incident se produit.

Site Recovery est un service Azure qui participe à votre stratégie de continuité des activités et de récupération d’urgence en orchestrant la réplication des machines virtuelles et des serveurs physiques locaux dans le cloud (Azure) ou sur un centre de données secondaire. Quand des pannes se produisent sur votre site principal, vous basculez sur le site secondaire pour préserver la disponibilité des applications et des charges de travail. Vous restaurez votre site principal dès lors qu’il retrouve un fonctionnement normal. Site Recovery peut être utilisé dans plusieurs scénarios et peut protéger plusieurs charges de travail. Pour plus d’informations, voir [Qu’est-ce que le service Azure Site Recovery ?](site-recovery-overview.md)


## Configuration requise pour la réplication de machines virtuelles Hyper-V

**Composant** | **Réplication dans Azure (avec VMM)** | **Réplication dans Azure (sans VMM)** | **Réplication vers un site secondaire (avec VMM)**
---|---|---|---
**VMM** | Un ou plusieurs serveurs VMM s’exécutant sous System Center 2012 R2. Le serveur VMM doit disposer d’au moins un cloud contenant un ou plusieurs groupes hôtes VMM. | Non applicable | Au moins un serveur VMM s’exécutant sous System Center 2012 R2. Nous recommandons un serveur VMM sur chaque site. Le serveur VMM doit disposer d’au moins un cloud contenant un ou plusieurs groupes hôtes VMM. Le profil de capacité Hyper-V doit être défini sur les clouds. 
**Hyper-V** | Un ou plusieurs serveurs hôtes Hyper-V dans le centre de données local, exécutant au moins Windows Server 2012 R2. Le serveur Hyper-V doit se trouver dans un groupe hôte à l’intérieur d’un cloud VMM. | Un ou plusieurs serveurs Hyper-V sur les sites source et cible, exécutant au moins Windows Server 2012 R2. | Un ou plusieurs serveurs Hyper-V sur les sites source et cible, exécutant au moins Windows Server 2012 R2 avec les dernières mises à jour. Le serveur Hyper-V doit se trouver dans un groupe hôte à l’intérieur d’un cloud VMM.
**Machines virtuelles** | Vous avez besoin d’au moins une machine virtuelle sur le serveur Hyper-V source. Les machines virtuelles répliquées sur Azure doivent remplir les [conditions préalables applicables aux machines virtuelles Azure](#azure-virtual-machine-requirements). <br> Installez ou mettez à niveau [Integration Services](https://technet.microsoft.com/library/dn798297.aspx) dans la machine virtuelle à l'aide de la procédure décrite [ici](https://technet.microsoft.com/library/hh846766.aspx#BKMK_step4). | Au moins une machine virtuelle sur le serveur Hyper-V source. Les machines virtuelles répliquées sur Azure doivent remplir les [conditions préalables applicables aux machines virtuelles Azure](#azure-virtual-machine-requirements). <br> Installez ou mettez à niveau [Integration Services](https://technet.microsoft.com/library/dn798297.aspx) dans la machine virtuelle à l'aide de la procédure décrite [ici](https://technet.microsoft.com/library/hh846766.aspx#BKMK_step4). | Au moins une machine virtuelle dans le cloud VMM source. <br> Installez ou mettez à niveau [Integration Services](https://technet.microsoft.com/library/dn798297.aspx) dans la machine virtuelle à l'aide de la procédure décrite [ici](https://technet.microsoft.com/library/hh846766.aspx#BKMK_step4).
**Compte Azure** | Vous avez besoin d’un compte d’un abonnement [Azure](https://azure.microsoft.com/). | Non applicable | Vous avez besoin d’un compte d’un abonnement [Azure](https://azure.microsoft.com/).
**Stockage Azure** | Vous avez besoin d’un [compte Azure Storage](../storage/storage-redundancy.md#geo-redundant-storage) pour stocker les données répliquées. Les données répliquées sont stockées dans le stockage Azure et les machines virtuelles Azure sont lancées au moment du basculement. | Non applicable | Vous avez besoin d’un [compte Azure Storage](../storage/storage-redundancy.md#geo-redundant-storage) pour stocker les données répliquées. Les données répliquées sont stockées dans le stockage Azure et les machines virtuelles Azure sont lancées au moment du basculement.
**Fournisseurs/Agents** | Pendant le déploiement, vous devez installer le fournisseur Azure Site Recovery sur les serveurs VMM et l’agent Azure Recovery Services sur les serveurs hôtes Hyper-V. Le fournisseur communique avec Azure Site Recovery. L'agent gère la réplication des données entre les serveurs Hyper-V source et cible. Rien ne s’installe sur les machines virtuelles. | Pendant le déploiement, vous devez installer le fournisseur Azure Site Recovery et l’agent Azure Recovery Services sur un serveur hôte ou cluster Hyper-V. Rien ne s’installe sur les machines virtuelles. | Pendant le déploiement, vous devez installer le fournisseur Azure Site Recovery sur les serveurs VMM pour communiquer avec Azure Site Recovery. La réplication se produit entre les serveurs Hyper-V source et cible via le réseau LAN/VPN.
**Connectivité du fournisseur/de l’agent** | Si le fournisseur doit se connecter au service Site Recovery via un proxy, vous devez vous assurer que le proxy peut accéder aux URL de Site Recovery. | Si le fournisseur doit se connecter à Site Recovery via un proxy, vous devez vous assurer que le proxy peut accéder aux URL de Site Recovery. | Si le fournisseur doit se connecter à Site Recovery via un proxy, vous devez vous assurer que le proxy peut accéder aux URL de Site Recovery.
**Connectivité Internet** | À partir du serveur VMM et des hôtes Hyper-V. | À partir des hôtes Hyper-V. | Sur le serveur VMM uniquement.
**Mappage réseau** | Configurez le mappage réseau pour que toutes les machines virtuelles dont le basculement s’effectue sur le même réseau Azure puissent se connecter les unes aux autres, indépendamment du plan de récupération auquel elles sont intégrées. Si une passerelle réseau est configurée sur le réseau Azure cible, les machines virtuelles peuvent également se connecter aux machines virtuelles locales. Si vous ne définissez pas le mappage réseau, seules les machines basculant dans le même plan de récupération peuvent se connecter. | Non applicable | Configurez le mappage réseau pour vous assurer que les machines virtuelles sont connectées aux bons réseaux après le basculement, et que les machines virtuelles de réplication sont placées de manière optimale sur les serveurs hôtes Hyper-V cibles. Si vous ne configurez pas le mappage réseau, les machines répliquées ne seront connectées à aucun réseau de machines virtuelles après le basculement.
**Mappage de stockage** | Non applicable | Non applicable | Vous pouvez éventuellement configurer un mappage de stockage pour vous assurer que les machines virtuelles sont connectées de manière optimale au stockage après le basculement (par défaut, la machine virtuelle de réplication est stockée à l’emplacement indiqué sur le serveur Hyper-V cible).
**Réplication SAN** | Non applicable | Non applicable | Si vous souhaitez effectuer une réplication entre deux sites VMM locaux à l’aide de la réplication SAN, vous pouvez utiliser votre environnement SAN existant. Voir [Baies SAN prises en charge](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).
**Plus d’informations** | [Détail des conditions préalables au déploiement](site-recovery-vmm-to-azure.md#before-you-start) | [Détail des conditions préalables au déploiement](site-recovery-hyper-v-site-to-azure.md#before-you-start#before-you-start) | [Détail des conditions préalables au déploiement](site-recovery-vmm-to-vmm.md#before-you-start)




## Configuration requise pour la réplication des machines virtuelles VMware et serveurs physiques

Le tableau récapitule la configuration requise pour la réplication des machines virtuelles VMware et des serveurs physiques Windows/Linux sur Azure et sur un site secondaire.

>[AZURE.NOTE] Vous pouvez répliquer des machines virtuelles VMware et des serveurs physiques sur Azure à l’aide d’un modèle de déploiement [amélioré](site-recovery-vmware-to-azure-classic.md) ou d’un modèle [hérité](site-recovery-vmware-to-azure-classic-legacy.md) utilisé pour des déploiements plus anciens. Le tableau ci-dessous présente la configuration requise pour le déploiement du modèle amélioré.

**Composant** | **Réplication vers Azure (améliorée)** | **Réplication vers un site secondaire**
---|---|---
**Site principal en local** | Vous installez un serveur d’administration qui exécute tous les composants de Site Recovery (configuration, traitement, cible maître). | Vous installez un serveur de traitement pour la mise en cache, la compression et le chiffrement des données de réplication avant leur envoi au site secondaire. Vous pouvez installer des serveurs de traitement supplémentaires pour l’équilibrage de charge ou la tolérance de panne. 
**Site secondaire en local** | Non applicable | Vous installez un serveur de configuration unique utilisé pour la configuration, la gestion et la surveillance du déploiement.<br/><br>Nous vous recommandons d’installer un serveur vContinuum pour faciliter la gestion du serveur de configuration.<br/><br/>Vous devez configurer le serveur cible maître en tant que machine virtuelle s’exécutant sur le serveur vSphere secondaire. 
**VMware vCenter/ESXi** | Si vous répliquez des machines virtuelles VMware (ou souhaitez restaurer des serveurs physiques) dans votre site principal, celui-ci doit disposer d’un serveur vSphere ESX/ESXi. Nous vous recommandons également d’utiliser un serveur vCenter pour gérer vos hôtes ESXi. | Dans vos sites principal et secondaire, vous devez également avoir un ou plusieurs hôtes VMware ESXi (et éventuellement un serveur vCenter). 
**Restauration automatique** | Vous avez besoin d’un environnement VMware pour restaurer à partir d’Azure, même si vous répliquez des serveurs physiques.<br/><br/>Vous devez configurer un serveur de traitement en tant que machine virtuelle Azure <br/><br/>Le serveur de configuration agit comme en tant que serveur maître cible mais, si vous restaurez des volumes de trafic importants, il se peut que vous deviez configurer un serveur cible maître local supplémentaire. [En savoir plus](site-recovery-failback-azure-to-vmware-classic.md)| La restauration automatique à partir du site secondaire vers le site principal s’effectue vers VMware uniquement, même si l’échec s’est produit sur un ordinateur physique. Pour la restauration automatique, vous devez configurer un serveur cible maître en tant que machine virtuelle sur le serveur vSphere principal.
**Compte Azure** | Vous avez besoin d’un compte d’un abonnement [Azure](https://azure.microsoft.com/). | Non applicable
**Stockage Azure** | Vous avez besoin d’un [compte Azure Storage](../storage/storage-redundancy.md#geo-redundant-storage) pour stocker les données répliquées. Les données répliquées sont stockées dans le stockage Azure et les machines virtuelles Azure sont lancées au moment du basculement. | Non applicable
**Réseau virtuel Azure** | Vous aurez besoin d’un réseau virtuel Azure auquel les machines virtuelles Azure se connecteront au moment du basculement. Pour une restauration automatique après basculement, vous avez besoin d’une connexion VPN (ou d’Azure ExpressRoute) configurée à partir du réseau Azure sur le site local. | Non applicable
**Machines protégées** | Au moins une machine virtuelle VMware ou un serveur Windows/Linux physique. Lors du déploiement, le service Mobilité est installé sur chaque machine que vous souhaitez répliquer. | Au moins une machine virtuelle VMware ou un serveur Windows/Linux physique. Pendant le déploiement, l’Agent unifié est installé sur chaque machine que vous voulez répliquer.
**Connectivité** | Si le serveur d’administration doit se connecte à Site Recovery via un proxy, vous devez vous assurer que le serveur proxy peut se connecter à des URL spécifiques. | Le serveur de configuration a besoin d’un accès Internet.
**Plus d’informations** | [Détail des conditions préalables au déploiement](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) | [Téléchargez](http://download.microsoft.com/download/E/0/8/E08B3BCE-3631-4CED-8E65-E3E7D252D06D/InMage_Scout_Standard_User_Guide_8.0.1.pdf) le guide de l’utilisateur InMage Scout.


## Configuration requise des machines virtuelles Azure

Vous pouvez déployer Site Recovery pour répliquer des machines virtuelles et des serveurs physiques exécutant n’importe quel système d’exploitation pris en charge par Azure. La plupart des versions de Windows et Linux sont concernées. Vous devrez vérifier que les machines virtuelles locales à protéger répondent à la configuration requise d’Azure.


**Fonctionnalité** | **Configuration requise** | **Détails**
---|---|---
Hôte Hyper-V | Doit exécuter Windows Server 2012 R2 | La vérification des conditions préalables échoue si le système d’exploitation n’est pas pris en charge
Hyperviseur VMware | Système d’exploitation pris en charge | [Vérifier la configuration requise](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment)
Système d’exploitation invité | Pour une réplication de Hyper-V sur Azure, Site Recovery prend en charge tous les systèmes d’exploitation [pris en charge par Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Pour une réplication de VMware et de serveur physique, vérifiez les [conditions préalables](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) pour Windows et Linux. | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Architecture du système d’exploitation invité | 64 bits | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Taille du disque du système d’exploitation | Jusqu’à 1 023 Go | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Nombre de disques du système d’exploitation | 1 | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Nombre de disques de données | 16 ou moins (la valeur maximale est fonction de la taille de la machine virtuelle créée. 16 = XL). | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Taille du disque dur virtuel de données | Jusqu’à 1 023 Go | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Adaptateurs réseau | Prise en charge de plusieurs adaptateurs réseau. |
Adresse IP statique | Pris en charge | Si la machine virtuelle principale utilise une adresse IP statique, vous pouvez spécifier l’adresse associée à la machine qui sera créée dans Azure. Notez qu’une adresse IP statique pour une machine virtuelle Linux s’exécutant sur Hyper-V n’est pas prise en charge. 
Disque iSCSI | Non pris en charge | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Disque dur virtuel partagé | Non pris en charge | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Disque FC | Non pris en charge | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Format de disque dur| Disque dur virtuel (VHD) <br/><br/> VHDX | Bien que VDHX ne soit pas actuellement pris en charge dans Azure, Site Recovery convertit automatiquement VHDX en VHD quand vous effectuez un basculement vers Azure. Lorsque vous procédez à la restauration automatique en local, les machines continue à utiliser le format VHDX.
Nom de la machine virtuelle| Entre 1 et 63 caractères. Uniquement des lettres, des chiffres et des traits d’union. Il doit commencer et se terminer par une lettre ou un chiffre. | Mettez à jour la valeur dans les propriétés de machine virtuelle de Site Recovery
Type de machine virtuelle | <p>Génération 1</p> <p>Génération 2 - Windows</p> | Une machine virtuelle de génération 2 dont le type de disque de système d’exploitation est disque de base et qui inclut 1 ou 2 volumes de données avec un format de disque VHDX inférieur à 300 Go est prise en charge. Les machines virtuelles Linux de génération 2 ne sont pas prises en charge. [En savoir plus](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## Optimisation de votre déploiement

Pour optimiser et mettre à l’échelle votre déploiement, tenez compte des conseils suivants.

- **Taille du volume du système d’exploitation** : quand vous répliquez une machine virtuelle dans Azure, la taille du volume du système d’exploitation doit être inférieure à 1 To. Si vous disposez de volumes supérieurs, vous pouvez les déplacer manuellement vers un disque différent avant de démarrer le déploiement.
- **Taille du disque de données** : si vous répliquez vers Microsoft Azure, vous pouvez posséder jusqu’à 32 disques de données sur une machine virtuelle, chacun présentant une taille maximale d’1 To. Vous pouvez répliquer et basculer efficacement une machine virtuelle présentant une taille d’environ 32 To.
- **Limites de plan de récupération** : Scan Recovery peut prendre en charge des milliers de machines virtuelles. Les plans de récupération sont conçus comme un modèle dédié aux applications devant basculer simultanément, de manière à ce que le nombre de machines du plan soit limité à 50.
- **Limites des services Microsoft Azure** : chaque abonnement Microsoft Azure comporte un ensemble de défauts liés aux services principaux, cloud, etc. Pour valider la disponibilité des ressources de votre abonnement, nous vous recommandons d’exécuter un basculement test. Vous pouvez modifier ces limites via le support Microsoft Azure.
- **Planification de la capacité** : en savoir plus sur la [planification de la capacité](site-recovery-capacity-planner.md) pour Site Recovery.
- **Bande passante de réplication** : si vous disposez d’une bande passante de réplication insuffisante, notez les points suivants :
	- **ExpressRoute** : Site Recovery s’associe à des optimiseurs Microsoft Azure ExpressRoute et WAN, tels que Riverbed. [En savoir plus](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sur ExpressRoute.
	- **Trafic de réplication** : Site Recovery exécute une réplication initiale intelligente uniquement à l’aide de blocs de données, pas de l’intégralité du disque dur virtuel. Seules les modifications sont répliquées au cours de la réplication en continu.
	- **Trafic réseau** : pour contrôler le trafic réseau utilisé pour la réplication, configurez le réseau de [Qualité de service (QoS) Windows](https://technet.microsoft.com/library/hh967468.aspx) à l’aide d’une stratégie basée sur l’adresse IP et le port de destination. En plus si vous répliquez vers Microsoft Azure Site Recovery à l’aide de l’agent de sauvegarde Microsoft Azure. Vous pouvez configurer la limitation de cet agent. [En savoir plus](https://support.microsoft.com/kb/3056159).
- **RTO** :si vous souhaitez mesurer l’objectif de délai de récupération procuré par Site Recovery, nous vous recommandons d’exécuter un basculement test et d’examiner les tâches de récupération afin d’analyser le délai d’exécution des opérations. Si vous effectuez un basculement vers Microsoft Azure, vous avez tout intérêt à automatiser l’ensemble des actions manuelles en intégrant avec Microsoft Azure Automation et les plans de récupération.
- **RPO** : Site Recovery prend en charge un objectif de point de récupération quasi synchrone lorsque vous répliquez vers Microsoft Azure. Ceci est valable uniquement si vous disposez d’une bande passante suffisante entre votre centre de données et Microsoft Azure.





## Étapes suivantes

Après avoir appris et comparé les conditions requises générales du déploiement, vous pouvez lire le détail des conditions requises et démarrer le déploiement de chaque scénario.

- [Répliquez des machines virtuelles VMware sur Microsoft Azure.](site-recovery-vmware-to-azure-classic.md)
- [Répliquer des serveurs physiques dans Azure](site-recovery-vmware-to-azure-classic.md)
- [Répliquer un serveur Hyper-V dans des clouds VMM vers Azure](site-recovery-vmm-to-azure.md)
- [Réplication de machines virtuelles Hyper-V (sans VMM) vers Azure](site-recovery-hyper-v-site-to-azure.md)
- [Réplication de machines virtuelles Hyper-V vers un site secondaire](site-recovery-vmm-to-vmm.md)
- [Réplication de machines virtuelles Hyper-V vers un site secondaire avec SAN](site-recovery-vmm-san.md)
- [Réplication de machines virtuelles Hyper-V avec un seul serveur VMM](site-recovery-single-vmm.md)

<!---HONumber=AcomDC_0413_2016-->