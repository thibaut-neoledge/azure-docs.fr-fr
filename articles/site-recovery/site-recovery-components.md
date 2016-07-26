<properties
	pageTitle="Comment Site Recovery fonctionne-t-il ? | Microsoft Azure"
	description="Cet article propose une vue d’ensemble de l’architecture de Site Recovery."
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
	ms.topic="get-started-article"
	ms.date="07/12/2016"
	ms.author="raynew"/>

# Comment Azure Site Recovery fonctionne-t-il ?

Lisez cet article pour comprendre l’architecture sous-jacente du service Azure Site Recovery et les composants qui le font fonctionner.

Publiez des commentaires ou des questions au bas de cet article ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Vue d'ensemble

Les organisations ont besoin d’une stratégie BCDR qui détermine la façon dont les applications, les charges de travail et les données demeurent opérationnelles et disponibles pendant les temps d’arrêt prévus et imprévus, et qui précise comment rétablir au plus vite des conditions de travail normales. Votre stratégie BCDR doit assurer la sécurisation et la récupération des données d’entreprise, ainsi que la disponibilité continue des charges de travail suite à un sinistre.

Site Recovery est un service Azure qui participe à votre stratégie de continuité des activités et de récupération d’urgence en orchestrant la réplication des machines virtuelles et des serveurs physiques locaux dans le cloud (Azure) ou sur un centre de données secondaire. Lorsque des pannes se produisent sur votre site principal, vous effectuez un basculement sur le site secondaire pour préserver la disponibilité des applications et des charges de travail. Vous restaurez votre site principal dès lors qu’il retrouve un fonctionnement normal. Pour en savoir plus, consultez [Qu’est-ce que Site Recovery ?](site-recovery-overview.md)

## Site Recovery dans le portail Azure

Azure dispose de deux [modèles de déploiement](../resource-manager-deployment-model.md) différents pour créer et utiliser des ressources : le modèle Azure Resource Manager et le modèle classique de gestion des services. De plus, Azure propose deux portails : le [portail Azure Classic](https://manage.windowsazure.com/), qui prend en charge le modèle de déploiement classique, et le [portail Azure](https://portal.azure.com), qui gère les deux modèles de déploiement.

Site Recovery est disponible dans le portail Azure Classic et dans le portail Azure. Dans le portail Azure Classic, vous pouvez prendre en charge Site Recovery avec le modèle classique de gestion des services. Dans le portail Azure, vous pouvez prendre en charge les déploiements classiques et Ressource Manager. [En savoir plus](site-recovery-overview.md#site-recovery-in-the-azure-portal) sur le déploiement à l’aide du portail Azure.

Les informations contenues dans cet article s’appliquent aux déploiements du portail Azure Classic et du portail Azure. Les différences sont indiquées, le cas échéant.

## Scénarios de déploiement

Site Recovery peut être déployé pour orchestrer la réplication dans un certain nombre de scénarios :

- **Réplication des machines virtuelles VMware **: vous pouvez répliquer des machines virtuelles VMware locales dans Azure ou dans un centre de données secondaire.
- - **Réplication des ordinateurs physiques** : vous pouvez répliquer des ordinateurs physiques exécutant Windows ou Linux dans Azure ou dans un centre de données secondaire. Le processus de réplication des ordinateurs physiques est presque identique au processus de réplication des machines virtuelles VMware
- **Réplication des machines virtuelles Hyper-V (sans VMM)** : vous pouvez répliquer dans Azure des machines virtuelles Hyper-V qui ne sont pas gérées par VMM.
- **Réplication des machines virtuelles Hyper-V gérées dans des clouds System Center VMM **: vous pouvez répliquer des machines virtuelles Hyper-V locales exécutées sur des serveurs hôtes dans des clouds VMM dans Azure ou dans un centre de données secondaire. Vous pouvez répliquer à l’aide de Réplica Hyper-V standard ou de la réplication SAN.
- **Migration des machines virtuelles** : vous pouvez utiliser Site Recovery pour [migrer des machines virtuelles Azure IaaS](site-recovery-migrate-azure-to-azure.md) entre différentes régions ou pour [migrer des instances AWS Windows](site-recovery-migrate-aws-to-azure.md) vers des machines virtuelles Azure IaaS. Actuellement, seule la migration est prise en charge, ce qui signifie que vous pouvez basculer vers ces machines virtuelles, mais que vous ne pouvez pas les restaurer.

Azure Site Recovery peut répliquer la plupart des applications exécutées sur ces machines virtuelles et ces serveurs physiques. Vous pouvez consulter la page [Quelles charges de travail pouvez-vous protéger avec Azure Site Recovery ?](site-recovery-workload.md) pour obtenir un récapitulatif complet des applications prises en charge.


## Réplication dans Azure : machines virtuelles VMware ou serveurs Windows/Linux physiques

Il existe différentes manières de répliquer des machines virtuelles VMware avec Site Recovery.

- **À l’aide du portail Azure** : lorsque vous déployez Site Recovery dans le portail Azure, vous pouvez basculer des machines virtuelles vers le stockage classique Service Manager ou vers Resource Manager. La réplication de machines virtuelles VMware dans le portail Azure offre de nombreux avantages, notamment la possibilité de répliquer dans le stockage classique ou dans Resource Manager dans Azure. [En savoir plus](site-recovery-vmware-to-azure.md).
- **À l’aide du portail Classic** : vous pouvez déployer Site Recovery dans le portail Classic à l’aide d’une expérience améliorée. Cette option doit être utilisée pour tous les nouveaux déploiements dans le portail Classic. Dans ce déploiement, vous pouvez uniquement basculer des machines virtuelles vers le stockage classique dans Azure, et non vers le stockage Resource Manager. [En savoir plus](site-recovery-vmware-to-azure-classic.md). Il existe également une [expérience héritée](site-recovery-vmware-to-azure-classic-legacy.md) pour configurer la réplication de machines virtuelles VMware dans le portail Classic. Cette option ne doit pas être utilisée pour les nouveaux déploiements. Si vous avez déjà déployé à l’aide de l’expérience héritée, [en savoir plus sur la migration](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) vers le déploiement amélioré.



Les spécifications architecturales pour le déploiement de Site Recovery pour répliquer les machines virtuelles VMware/serveurs physiques dans le portail Azure ou le portail Azure Classic (amélioré) sont similaires, avec quelques différences :

- Si vous déployez dans le portail Azure, vous pouvez répliquer dans le stockage basé sur Resource Manager et utiliser les réseaux Resource Manager pour la connexion aux machines virtuelles Azure après le basculement.
- Lorsque vous déployez dans le portail Azure, les stockages LRS et GRS sont tous deux pris en charge. Dans le portail Classic, GRS est requis.
- Le processus de déploiement est simplifié et plus convivial dans le portail Azure.


Voici ce dont vous aurez besoin :

- **Compte Azure** : vous devez disposer d’un compte Microsoft Azure.
- **Stockage Azure** : vous aurez besoin d’un compte de stockage Azure pour stocker les données répliquées. Vous pouvez utiliser un compte classique ou un compte de stockage Resource Manager. Le compte peut être de type LRS ou GRS lorsque vous déployez dans le portail Azure. Les données répliquées sont stockées dans le stockage Azure et les machines virtuelles Azure sont lancées au moment du basculement.
- **Réseau Azure** : vous aurez besoin d’un réseau virtuel Azure auquel les machines virtuelles Azure se connecteront lorsqu’elles sont créées au moment du basculement. Le portail Azure peut contenir des réseaux créés dans le modèle classique Service Manager ou dans le modèle Resource Manager.
- **Serveur de configuration local** : vous aurez besoin d’un Windows Server 2012 R2 local qui exécute le serveur de configuration et d’autres composants de Site Recovery. Si vous répliquez des machines virtuelles VMware, elles doivent être hautement disponibles. Si vous souhaitez répliquer des serveurs physiques, la machine peut être physique. Les composants suivants de Site Recovery seront installés sur la machine :
	- **Serveur de configuration** : coordonne la communication entre votre environnement local et Azure et gère la réplication et la récupération des données.
	- **Serveur de processus** : fait office de passerelle de réplication. Il reçoit les données de réplication d’ordinateurs source protégés, les optimise grâce à la mise en cache, la compression et le chiffrement et envoie les données vers le stockage Azure. Il gère également l’installation Push du service Mobilité sur des machines protégées et assure la détection automatique des machines virtuelles VMware. À mesure que s’étend votre déploiement, vous pouvez ajouter des serveurs de traitement dédiés supplémentaires afin de gérer de plus grands volumes de trafic de réplication.
	- **Serveur cible maître** : gère les données de réplication pendant la restauration automatique à partir d’Azure.
- **Machines virtuelles ou serveurs physiques à répliquer** : le composant du service Mobilité doit être installé sur chaque machine que vous souhaitez répliquer dans Azure. Ce service enregistre les écritures de données sur la machine et les transmet au serveur de traitement. Ce composant peut être installé manuellement ou transféré et installé automatiquement par le serveur de traitement lorsque vous activez la réplication d’une machine.
- **Hôtes vSphere/serveur vCenter** : vous aurez besoin d’un ou de plusieurs serveurs hôtes vSphere exécutant des machines virtuelles VMware. Nous vous recommandons de déployer un serveur vCenter pour gérer ces hôtes.
- **Restauration automatique** : voici ce dont vous avez besoin :
	- **La restauration automatique physique à physique n’est pas prise en charge** : cela signifie que si vous basculez les serveurs physiques vers Azure, puis souhaitez les restaurer automatiquement, vous devez le faire vers une machine virtuelle VMware. Il est impossible d’effectuer cette procédure vers un serveur physique. Vous aurez besoin d’une machine virtuelle Azure vers laquelle effectuer la restauration et, si vous n’avez pas déployé le serveur de configuration en tant que machine virtuelle VMware, vous devrez configurer un serveur cible maître distinct en tant que machine virtuelle VMware. C’est nécessaire, car le serveur cible maître interagit sur et s’attache au stockage VMware pour restaurer les disques vers une machine virtuelle VMware.
	- - **Serveur de traitement temporaire dans Azure** : si vous souhaitez effectuer une restauration automatique à partir d’Azure après le basculement, vous devez définir une machine virtuelle Azure configurée comme serveur de traitement pour gérer la réplication à partir d’Azure. Vous pourrez supprimer cette machine virtuelle une fois la restauration terminée.
	- **Connexion VPN** : pour une restauration automatique, vous devrez disposer d’une connexion VPN (ou d’Azure ExpressRoute) configurée à partir du réseau Azure sur le site local.
	- **Serveur cible maître local distinct** : le serveur cible maître local gère la restauration automatique. Le serveur cible maître est installé par défaut sur le serveur d’administration. Toutefois, si vous restaurez automatiquement de plus grands volumes de trafic vous devez définir un serveur cible maître local à cet effet.

**Architecture générale**

![Amélioré](./media/site-recovery-components/arch-enhanced.png)

**Composants de déploiement**

![Amélioré](./media/site-recovery-components/arch-enhanced2.png)

**Restauration automatique**

![Restauration automatique améliorée](./media/site-recovery-components/enhanced-failback.png)


- [En savoir plus](site-recovery-vmware-to-azure.md#azure-prerequisites) sur les exigences relatives au déploiement du portail Azure.
- [En savoir plus](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) sur les exigences relatives au déploiement amélioré dans le portail Azure Classic.
- [En savoir plus](site-recovery-failback-azure-to-vmware.md) sur la restauration automatique dans le portail Azure.
- [En savoir plus](site-recovery-failback-azure-to-vmware-clas- [Learn more](site-recovery-failback-azure-to-vmware-classic.md) about failback in the Auzre portal.sic.md) sur la restauration automatique dans le portail Classic.

## Réplication dans Azure : machines virtuelles Hyper-V non gérées par VMM

Vous pouvez répliquer des machines virtuelles Hyper-V qui ne sont pas gérées par System Center VMM dans Azure avec Site Recovery comme suit :

- **À l’aide du portail Azure** : lorsque vous déployez Site Recovery dans le portail Azure, vous pouvez basculer des machines virtuelles vers le stockage classique ou vers Resource Manager. [En savoir plus](site-recovery-hyper-v-site-to-azure.md).
- **À l’aide du portail Classic** : vous pouvez déployer Site Recovery dans le portail Classic. Dans ce déploiement, vous pouvez uniquement basculer des machines virtuelles vers le stockage classique dans Azure, et non vers le stockage Resource Manager. [En savoir plus](site-recovery-hyper-v-site-to-azure-classic.md).

L’architecture pour les deux déploiements est similaire, à ceci près que :

- Si vous déployez dans le portail Azure, vous pouvez répliquer dans le stockage Resource Manager et utiliser les réseaux Resource Manager pour la connexion aux machines virtuelles Azure après le basculement.
- Le processus de déploiement est simplifié et plus convivial dans le portail Azure.

Voici ce dont vous aurez besoin :

- **Compte Azure** : vous devez disposer d’un compte Microsoft Azure.
- **Stockage Azure** : vous aurez besoin d’un compte de stockage Azure pour stocker les données répliquées. Dans le portail Azure, vous pouvez utiliser un compte Classic ou un compte de stockage Resource Manager. Dans le portail Classic, vous pouvez uniquement utiliser un compte Classic. Les données répliquées sont stockées dans Azure Storage et les machines virtuelles Azure sont créées au moment du basculement.
- **Réseau Azure** : vous aurez besoin d’un réseau Azure auquel les machines virtuelles Azure se connecteront lorsqu’elles sont créées après le basculement.
- **Hôte Hyper-V** : vous aurez besoin d’un ou de plusieurs serveurs hôtes Windows Server 2012 R2 Hyper-V. Lors du déploiement de Site Recovery, vous allez installer
- **Les machines virtuelles Hyper-V** : vous aurez besoin d’une ou de plusieurs machines virtuelles sur le serveur hôte Hyper-V. Le fournisseur Azure Site Recovery et l’agent Azure Recovery Services sur l’hôte Hyper-V lors du déploiement Site Recovery. Le fournisseur coordonne et orchestre la réplication avec le service Site Recovery via Internet. L’agent gère les données de réplication de données via HTTPS 443. Les communications en provenance du fournisseur et de l’agent sont sécurisées et chiffrées. Les données répliquées dans le stockage Azure sont également chiffrées.

**Architecture générale**

![Entre un site Hyper-V et Microsoft Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)


- [En savoir plus](site-recovery-hyper-v-site-to-azure.md#azure-prerequisites) sur les exigences relatives au déploiement du portail Azure.
- [En savoir plus](site-recovery-hyper-v-site-to-azure-classic.md#azure-prerequisites) sur les exigences relatives au déploiement du portail Classic.



## Réplication dans Azure : machines virtuelles Hyper-V gérées par VMM

Vous pouvez répliquer des machines virtuelles Hyper-V dans des clouds VMM dans Azure avec Site Recovery comme suit :

- **À l’aide du portail Azure** : lorsque vous déployez Site Recovery dans le portail Azure, vous pouvez basculer des machines virtuelles vers le stockage classique ou vers Resource Manager. [En savoir plus](site-recovery-vmm-to-azure.md).
- **À l’aide du portail Classic** : vous pouvez déployer Site Recovery dans le portail Classic. Dans ce déploiement, vous pouvez uniquement basculer des machines virtuelles vers le stockage classique dans Azure, et non vers le stockage Resource Manager. [En savoir plus](site-recovery-vmm-to-azure-classic.md).

L’architecture pour les deux déploiements est similaire, à ceci près que :

- Si vous déployez dans le portail Azure, vous pouvez répliquer dans le stockage basé sur Resource Manager et utiliser les réseaux Resource Manager pour la connexion aux machines virtuelles Azure après le basculement.
- Le processus de déploiement est simplifié et plus convivial dans le portail Azure.


Voici ce dont vous aurez besoin :

- **Compte Azure** : vous devez disposer d’un compte Microsoft Azure.
- **Stockage Azure** : vous aurez besoin d’un compte de stockage Azure pour stocker les données répliquées. Dans le portail Azure, vous pouvez utiliser un compte Classic ou un compte de stockage Resource Manager. Dans le portail Classic, vous pouvez uniquement utiliser un compte Classic. Les données répliquées sont stockées dans Azure Storage et les machines virtuelles Azure sont créées au moment du basculement.
- **Réseau Azure**: vous devez configurer le mappage réseau afin que les machines virtuelles Azure soient connectées aux réseaux appropriés lorsqu’elles sont créées après le basculement.
- **Serveur VMM**: vous avez besoin d’un ou de plusieurs serveurs VMM locaux s’exécutant sur System Center 2012 R2 et configurés avec un ou plusieurs clouds privés. Si vous déployez dans le portail Azure, vous aurez besoin de réseaux logiques et de machines virtuelles configurés afin de pouvoir configurer le mappage réseau. Dans le portail Classic, c’est facultatif. Un réseau de machines virtuelles doit être lié à un réseau logique lui-même associé au cloud.
- **Hôte Hyper-V** : vous aurez besoin d’un ou de plusieurs serveurs hôtes Windows Server 2012 R2 Hyper-V dans le cloud VMM.
- **Machines virtuelles Hyper-V** : vous aurez besoin d’une ou de plusieurs machines virtuelles sur le serveur hôte Hyper-V.

**Architecture générale**

![VMM vers Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

- [En savoir plus](site-recovery-vmm-to-azure.md#azure-requirements) sur les exigences relatives au déploiement du portail Azure.
- [En savoir plus](site-recovery-vmm-to-azure-classic.md#before-you-start) sur les exigences relatives au déploiement du portail Classic.




## Répliquer dans un site secondaire : machines virtuelles VMware ou serveurs physiques 

Pour répliquer des machines virtuelles VMware ou des serveurs physiques dans un site secondaire, téléchargez InMage Scout inclus dans l’abonnement Azure Site Recovery. Il peut être téléchargé à partir du portail Azure ou du portail Azure Classic.

Vous configurez les serveurs de composants dans chaque site (configuration, processus, cible maître) et installez l’Agent unifié sur les ordinateurs que vous souhaitez répliquer. Après la réplication initiale, l’agent de chaque ordinateur envoie les modifications de réplication différentielle au serveur de traitement. Le serveur de traitement optimise ces données et les transfère vers le serveur cible maître du site secondaire. Le serveur de configuration gère le processus de réplication.

Voici ce dont vous avez besoin :

**Compte Azure** : vous déployez ce scénario à l’aide d’InMage Scout. Pour l’obtenir, vous avez besoin d’un abonnement Azure. Après avoir créé un coffre Site Recovery, téléchargez InMage Scout et installez les dernières mises à jour pour configurer le déploiement. **Serveur de traitement (site principal)** : installez le composant serveur de traitement sur votre site principal pour gérer la mise en cache, la compression et l’optimisation des données. Il gère aussi l’installation Push de l’Agent unifié sur les machines à protéger. **VMware ESX/ESXi et serveur vCenter (site principal)** : si vous protégez des machines virtuelles VMware, vous aurez besoin d’un hyperviseur VMware ESX/ESXi et éventuellement d’un serveur VMware vCenter capable de gérer des hyperviseurs.
- **Machines virtuelles/serveurs physiques (site principal)** : l’Agent unifié doit être installé sur les machines virtuelles VMware ou les serveurs physiques Windows/Linux que vous souhaitez protéger. Cet agent est également installé sur les ordinateurs servant de serveur cible maître. Il joue le rôle de fournisseur de communication entre tous les composants.
- - **Serveur de configuration (site secondaire)** : le serveur de configuration est le premier composant installé. Vous l’installez sur le site secondaire pour gérer, configurer et surveiller votre déploiement via le site web de gestion ou la console vContinuum. Chaque déploiement ne comporte qu’un seul serveur de configuration, qui doit être installé sur un ordinateur exécutant Windows Server 2012 R2.
- **Serveur vContinuum (site secondaire)** : installé au même emplacement (site secondaire) que le serveur de configuration. Il intègre une console qui vous permet de gérer et surveiller votre environnement protégé. Dans une installation par défaut, le serveur vContinuum est le premier serveur cible maître et l’Agent unifié y est installé.
- **Serveur cible maître (site secondaire)** : le serveur cible maître stocke les données répliquées. Il reçoit les données du serveur de traitement, crée une machine de réplication sur le site secondaire et stocke les points de rétention des données. Le nombre de serveurs cibles maîtres dont vous avez besoin dépend du nombre de machines que vous protégez. Si vous voulez effectuer une restauration sur le site principal, vous aurez là aussi besoin d’un serveur cible maître.

**Architecture générale**

![VMware vers VMware](./media/site-recovery-components/vmware-to-vmware.png)


## Réplication dans un site secondaire : machines virtuelles Hyper-V gérées par VMM


Vous pouvez répliquer des machines virtuelles Hyper-V qui sont gérées par System Center VMM dans un centre de données secondaires avec Site Recovery comme suit :

- **À l’aide du portail Azure** : lorsque vous déployez Site Recovery dans le portail Azure. [En savoir plus](site-recovery-hyper-v-site-to-azure.md).
- **À l’aide du portail Classic** : vous pouvez déployer Site Recovery dans le portail Classic. [En savoir plus](site-recovery-hyper-v-site-to-azure-classic.md).

L’architecture pour les deux déploiements est similaire, à ceci près que :

- Si vous déployez dans le portail Azure, vous devez configurer le mappage réseau. C’est facultatif dans le portail Classic.
- Le processus de déploiement est simplifié et plus convivial dans le portail Azure.
- - Si vous déployez dans le portail Azure Classic, le [mappage de stockage](site-recovery-storage-mapping.md) est disponible.

Voici ce dont vous aurez besoin :

- **Compte Azure** : vous devez disposer d’un compte Microsoft Azure.
- **Serveur VMM** : nous recommandons un serveur VMM sur le site principal et un sur le site secondaire, chacun contenant au moins un cloud privé VMM. Le serveur doit exécuter au moins System Center 2012 SP1 avec les dernières mises à jour et être connecté à Internet. Le profil de capacité Hyper-V doit être défini sur les clouds. Vous allez installer le fournisseur Azure Site Recovery sur le serveur VMM. Le fournisseur coordonne et orchestre la réplication avec le service Site Recovery via Internet. Les communications entre le fournisseur et Azure sont sécurisées et chiffrées.
- **Serveur Hyper-V** : les serveurs hôtes Hyper-V doivent être situés dans les clouds VMM principaux et secondaires. Les serveurs hôtes doivent exécuter au moins Windows Server 2012 avec les dernières mises à jour installées, et être connectés à Internet. Les données sont répliquées entre les serveurs hôtes Hyper-V principal et secondaire via une liaison LAN ou VPN en utilisant Kerberos ou une authentification par certificat.
- **Machines protégées** : le serveur hôte Hyper-V source doit avoir au moins une machine virtuelle à protéger.

**Architecture générale**

![Local vers local](./media/site-recovery-components/arch-onprem-onprem.png)


- [En savoir plus](site-recovery-vmm-to-vmm.md#azure-prerequisites) sur les exigences relatives au déploiement dans le portail Azure.
- - [En savoir plus](site-recovery-vmm-to-vmm-classic.md#before-you-start) sur les exigences relatives au déploiement dans le portail Azure Classic.




## Réplication dans un site secondaire avec réplication SAN : machines virtuelles Hyper-V gérées par VMM

Vous pouvez répliquer des machines virtuelles Hyper-V gérées dans des clouds VMM dans un site secondaire à l’aide de la réplication SAN dans le portail Azure Classic. Actuellement, ce scénario n’est pas pris en charge dans le nouveau portail Azure.

Pour déployer ce scénario au cours d’un déploiement de Site Recovery, le fournisseur Azure Site Recovery est installé sur les serveurs VMM. Le fournisseur coordonne et orchestre la réplication avec le service Site Recovery via Internet. Les données sont répliquées entre les groupes de stockage principal et secondaire en utilisant la réplication SAN synchrone.

Voici ce dont vous aurez besoin :

**Compte Azure** : vous avez besoin d’un abonnement Azure
- **Groupe SAN** : un [groupe SAN pris en charge](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) géré par le serveur VMM principal. Le SAN partage une infrastructure réseau avec une autre baie de stockage SAN dans le site secondaire.
- **Serveur VMM** : nous recommandons un serveur VMM sur le site principal et un sur le site secondaire, chacun contenant au moins un cloud privé VMM. Le serveur doit exécuter au moins System Center 2012 SP1 avec les dernières mises à jour et être connecté à Internet. Le profil de capacité Hyper-V doit être défini sur les clouds.
- **Serveur Hyper-V** : serveurs hôtes Hyper-V situés dans les clouds VMM principaux et secondaires. Les serveurs hôtes doivent exécuter au moins Windows Server 2012 avec les dernières mises à jour installées, et être connectés à Internet.
- **Machines protégées** : le serveur hôte Hyper-V source doit avoir au moins une machine virtuelle à protéger.

**Architecture de réplication SAN**

![Réplication SAN](./media/site-recovery-components/arch-onprem-onprem-san.png)

[En savoir plus](site-recovery-vmm-san.md#before-you-start) sur les exigences relatives au déploiement.
### Au niveau local



## Cycle de vie de la protection Hyper-V

Ce flux de travail présente le processus de protection, de réplication et de basculement des machines virtuelles Hyper-V.

1. **Activer la protection** : installez le coffre Site Recovery, configurez les paramètres de réplication d’un cloud VMM ou d’un site Hyper-V, puis activez la protection des machines virtuelles. Un travail appelé **Activer la protection** est initiée et peut être surveillée sous l’onglet **Travaux**. Le travail vérifie que la machine est conforme à la configuration requise et appelle ensuite la méthode [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), laquelle configure la réplication vers Azure avec les paramètres que vous avez configurés. Le travail **Activer la protection** appelle aussi la méthode [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) pour initialiser une réplication complète des machines virtuelles.
2. **Réplication initiale** : un instantané de machine virtuelle est créé et les disques durs virtuels sont répliqués un par un jusqu’à ce qu’ils soient tous copiés dans Azure ou sur le centre de données secondaire. La durée de cette opération dépend de la taille de la machine virtuelle, de la bande passante réseau et de la méthode de réplication initiale que vous avez choisie. Si des modifications interviennent sur les disques pendant la réplication initiale, le dispositif de suivi de réplication des réplicas Hyper-V assure le suivi de ces modifications dans des journaux de réplication Hyper-V (.hrl), qui se trouvent dans le même dossier que les disques. À chaque disque correspond un fichier .hrl, qui est envoyé au stockage secondaire. Notez que l’instantané et les fichiers journaux consomment des ressources disque pendant la réplication initiale. À la fin de la réplication initiale, l’instantané de machine virtuelle est supprimé et les modifications de disque delta figurant dans le journal sont synchronisées et fusionnées.
3. **Finaliser la protection** : une fois la réplication initiale terminée, le travail **Finaliser la protection** configure les paramètres réseau et d’autres paramètres de post-réplication de sorte que la machine virtuelle soit protégée. Si vous répliquez dans Azure, vous devrez peut-être modifier les paramètres de la machine virtuelle pour la préparer au basculement. À ce stade, vous pouvez exécuter un basculement de test pour vérifier que tout fonctionne comme prévu.
4. **Réplication** : la synchronisation delta commence à l’issue de la réplication initiale, selon les paramètres de réplication.
	- **Échec de la réplication** : si la réplication différentielle échoue et si une réplication complète serait coûteuse en bande passante et en temps, une resynchronisation se produit. Par exemple, si les fichiers .hrl atteignent 50 % de la taille du disque, la machine virtuelle est marquée pour resynchronisation. La resynchronisation réduit la quantité de données envoyées par le calcul des sommes de contrôle des machines virtuelles source et cible et l’envoi du seul delta. Une fois la resynchronisation terminée, la réplication différentielle doit reprendre. Par défaut, la resynchronisation est planifiée pour s’exécuter automatiquement en dehors des heures de bureau, mais vous pouvez resynchroniser une machine virtuelle manuellement.
	- **Erreur de réplication** : si une erreur de réplication se produit, une nouvelle tentative intégrée se produit. S’il s’agit d’une erreur non récupérable, telle qu’une erreur d’authentification ou d’autorisation ou qu’une machine de réplication est dans un état non valide, aucune nouvelle tentative n’intervient. S’il s’agit d’une erreur récupérable, par exemple une erreur réseau ou un manque d’espace disque ou de mémoire, une nouvelle tentative se produit à intervalles croissants (après 1, 2, 4, 8 et 10 minutes, puis toutes les 30 minutes).
4. **Basculements planifiés/non planifiés** : exécutez des basculements planifiés/non planifiés quand cela est nécessaire. Si vous exécutez un basculement planifié, les machines virtuelles sources sont arrêtées pour éviter toute perte de données. Une fois créées, les machines virtuelles de réplication sont en attente de validation. Vous devez les valider pour terminer le basculement, sauf si vous effectuez une réplication avec SAN, auquel cas, la validation est automatique. Dès lors que le site principal est opérationnel, la restauration peut intervenir. Si vous répliquez dans Azure, la réplication inverse est automatique. Dans le cas contraire, vous devez lancer manuellement une réplication inverse.
 

![flux de travail](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## Étapes suivantes

[Préparer le déploiement](site-recovery-best-practices.md)

<!---HONumber=AcomDC_0720_2016-->