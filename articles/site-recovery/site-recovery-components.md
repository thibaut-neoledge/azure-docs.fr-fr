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
	ms.date="02/16/2016"
	ms.author="raynew"/>

# Comment Azure Site Recovery fonctionne-t-il ?

Cet article décrit l’architecture sous-jacente de Site Recovery et des composants qui le font fonctionner. Après avoir lu cet article, vous pourrez poser des questions sur le [forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Vue d'ensemble

Les organisations ont besoin d’une stratégie de continuité des activités et de récupération d’urgence qui détermine la façon dont les applications, les charges de travail et les données demeurent opérationnelles et disponibles pendant les temps d’arrêt prévus et imprévus et comment rétablir au plus vite des conditions de travail normales. Une telle stratégie repose sur des solutions qui visent à sécuriser et à récupérer les données d’entreprise et à rendre les charges de travail disponibles en continu quand un incident se produit.

Site Recovery est un service Azure qui participe à votre stratégie de continuité des activités et de récupération d’urgence en orchestrant la réplication des machines virtuelles et des serveurs physiques locaux dans le cloud (Azure) ou sur un centre de données secondaire. Quand des pannes se produisent sur votre site principal, vous basculez sur le site secondaire pour préserver la disponibilité des applications et des charges de travail. Vous restaurez votre site principal dès lors qu’il retrouve un fonctionnement normal.

Site Recovery peut être utilisé dans plusieurs scénarios et peut protéger plusieurs charges de travail.

- **Protection des machines virtuelles VMware **: vous pouvez protéger des machines virtuelles VMware locales en les répliquant dans [Azure](site-recovery-vmware-to-azure-classic.md) ou dans un [centre de données secondaire](site-recovery-vmware-to-vmware.md).
- **Protection des machines virtuelles Hyper-V **: vous pouvez protéger des machines virtuelles Hyper-V locales dans des clouds VVM en les répliquant dans [Azure](site-recovery-vmm-to-azure.md) ou dans un [centre de données secondaire](site-recovery-vmm-to-vmm.md). Vous pouvez répliquer dans [Azure](site-recovery-hyper-v-site-to-azure.md) des machines virtuelles Hyper-V qui ne sont pas gérées par VMM.
- **Protection des serveurs physiques dans Azure** : vous pouvez protéger des ordinateurs physiques exécutant Windows ou Linux en les répliquant dans [Azure](site-recovery-vmware-to-azure-classic.md) ou dans un [centre de données secondaire](site-recovery-vmware-to-vmware.md).
- **Migration des machines virtuelles** : vous pouvez utiliser Site Recovery pour [migrer des machines virtuelles Azure IaaS](site-recovery-migrate-azure-to-azure.md) entre différentes régions ou pour [migrer des instances AWS Windows](site-recovery-migrate-aws-to-azure.md) vers des machines virtuelles Azure IaaS.

Azure Site Recovery peut répliquer la plupart des applications exécutées sur ces machines virtuelles et ces serveurs physiques. Vous pouvez consulter la page [Quelles charges de travail pouvez-vous protéger avec Azure Site Recovery ?](site-recovery-workload.md) pour obtenir un récapitulatif complet des applications prises en charge.


## Répliquer des machines virtuelles VMware locales ou des serveurs physiques dans Azure

Si vous voulez protéger des machines virtuelles VMware ou des ordinateurs physiques Windows/Linux en les répliquant sur Azure, voici ce dont vous avez besoin.

Deux architectures différentes sont actuellement disponibles pour ce scénario de réplication :

- **Architecture héritée** : cette architecture ne doit pas être utilisée pour les nouveaux déploiements. 
- **Architecture améliorée **: cette toute dernière solution doit être utilisée pour tous les nouveaux déploiements. Vous pouvez également [migrer votre architecture héritée](site-recovery-vmware-to-azure-classic-legacy.md#migrate-to-the-enhanced-deployment) vers cette nouvelle solution.

Voici l’architecture de déploiement amélioré

![Amélioré](./media/site-recovery-components/arch-enhanced.png)

- **En local** : lorsque vous déployez l’architecture améliorée, il n’est pas nécessaire de déployer des machines virtuelles d’infrastructure dans Azure. En outre, l’ensemble du trafic est chiffré et les communications de gestion de la réplication sont envoyées via HTTPS 443. Voici les composants dont vous avez besoin dans votre infrastructure locale :

	- **Serveur de gestion **: un seul serveur de gestion qui exécute tous les composants de Site Recovery, notamment :

		- **Un serveur de configuration** : pour coordonner la communication entre votre environnement local et Azure et pour gérer les processus de réplication et de récupération des données.
		- **Un serveur de traitement** : fait office de passerelle de réplication. Il reçoit les données d’ordinateurs source protégés, les optimise grâce à la mise en cache, la compression et le chiffrement et envoie les données de réplication vers le stockage Azure. Il gère également l’installation Push du service Mobilité sur des machines protégées et assure la détection automatique des machines virtuelles VMware. À mesure que s’étend votre déploiement, vous pouvez ajouter des serveurs dédiés supplémentaires qui s’exécutent en tant que serveurs de traitement uniquement afin de gérer de plus grands volumes de trafic de réplication.
		- **Serveur cible maître** : gère les données de réplication pendant la restauration automatique à partir d’Azure. 

	- **Hôte VMware ESX/ESXi et serveur vCenter** : un ou plusieurs serveurs hôtes ESX/ESXi sur lesquels sont hébergées les machines virtuelles VMware. Nous vous recommandons d’utiliser un serveur vCenter pour gérer ces hôtes. Notez que même si vous protégez des serveurs physiques, vous aurez besoin d’un environnement VMware afin de basculer vos données d’Azure vers votre site local.
	
	- **Machines protégées** : le composant du service de mobilité doit être installé sur chaque ordinateur que vous souhaitez répliquer dans Azure. Il enregistre les écritures de données sur la machine et les transmet au serveur de traitement. Ce composant peut être installé manuellement ou transféré et installé automatiquement par le serveur de traitement lorsque vous activez la protection d’une machine.

- **Azure** : voici les composants dont vous avez besoin dans votre infrastructure Azure :
	- **Compte Azure** : vous devez disposer d’un compte Microsoft Azure.
	- **Stockage Azure** : vous aurez besoin d’un compte de stockage Azure pour stocker les données répliquées. Les données répliquées sont stockées dans le stockage Azure et les machines virtuelles Azure sont lancées au moment du basculement. 
	- **Réseau Azure** : vous aurez besoin d’un réseau virtuel Azure auquel les machines virtuelles Azure se connecteront au moment du basculement. Vous aurez également besoin d’une connexion VPN (ou Azure ExpressRoute) configurée à partir du réseau Azure sur le site local.

	![Amélioré](./media/site-recovery-components/arch-enhanced2.png)

En savoir plus sur les [exigences relatives au déploiement](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment).

### Architecture de restauration automatique

- La restauration automatique doit s’effectuer entre Azure et des machines virtuelles VMware. Il est impossible actuellement d’effectuer cette procédure vers un serveur physique.
- Pour procéder à une restauration automatique, vous avez besoin d’une connexion VPN (ou Azure ExpressRoute) entre votre réseau Azure et votre réseau local.
- Vous avez également besoin d’un serveur de traitement dans Azure pour la restauration. Vous pourrez le supprimer une fois la restauration terminée.
- Vous avez besoin d’un serveur cible maître en local. Un serveur cible maître est installé par défaut sur le serveur de gestion lorsque vous l’avez configuré en local. Mais pour de plus grands volumes de trafic, nous vous recommandons de configurer un serveur cible maître distinct en local pour la restauration.

![Restauration automatique améliorée](./media/site-recovery-components/enhanced-failback.png)

En savoir plus sur la [restauration automatique](site-recovery-failback-azure-to-vmware-classic.md).

### Architecture héritée

L’architecture héritée requiert l’utilisation d’un serveur de configuration et d’un serveur de traitement en local ainsi que d’hôtes VMware ESX/ESXi et d’un serveur vCenter. Le service de mobilité doit également être installé sur les ordinateurs que vous souhaitez protéger. Dans Azure, vous allez définir des machines virtuelles Azure pour le serveur de configuration et le serveur cible maître. Vous avez également besoin d’un abonnement Azure, d’un compte de stockage et d’un réseau virtuel.



## Répliquer des machines virtuelles Hyper-V dans des clouds VMM vers Azure.

Si vos machines virtuelles se trouvent sur un hôte Hyper-V géré dans un cloud System Center VMM, voici ce dont vous avez besoin pour les répliquer dans Azure.

- En local : 
	- **Serveur VMM** : au moins un serveur VMM configuré avec au moins un cloud privé VMM. Le serveur doit être exécuté sous System Center 2012 R2. Le serveur VMM doit disposer d’une connexion à Internet. Si vous souhaitez vous assurer que les machines virtuelles Azure sont bien connectées à un réseau après le basculement, vous devez configurer le mappage réseau. Pour ce faire, vous devez connecter les machines virtuelles sources à un réseau de machines virtuelles VMM. Ce réseau doit être lié à un réseau logique lui-même associé au cloud.
	- **Serveur Hyper-V** : au moins un serveur hôte Hyper-V hébergé dans le cloud VMM. Les hôtes Hyper-V doivent exécuter Windows Server 2012 R2.
	- **Machines protégées** : le serveur hôte Hyper-V source doit avoir au moins une machine virtuelle à protéger.
	
- Azure :
	- **Compte Azure** : vous devez disposer d’un compte Microsoft Azure.
	- **Stockage Azure** : vous aurez besoin d’un compte de stockage Azure pour stocker les données répliquées. Les données répliquées sont stockées dans le stockage Azure et les machines virtuelles Azure sont lancées au moment du basculement.
	- **Réseau Azure** : si vous souhaitez vous assurer que les machines virtuelles Azure sont bien connectées à des réseaux après le basculement, vous devez configurer le mappage réseau. Pour ce faire, vous devez configurer un réseau Azure.

	![VMM vers Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

Dans ce scénario, le fournisseur Azure Site Recovery est installé sur le serveur VMM lors du déploiement de Site Recovery. Celui-ci coordonne et orchestre la réplication avec le service Site Recovery via Internet. L’agent Azure Recovery Services est installé au cours du déploiement de Site Recovery sur le serveur hôte Hyper-V et les données sont répliquées entre celui-ci et le stockage Azure via HTTPS 443. Les communications en provenance du fournisseur et de l’agent sont sécurisées et chiffrées. Les données répliquées dans le stockage Azure sont également chiffrées.

En savoir plus sur les [exigences relatives au déploiement](site-recovery-vmm-to-azure.md#before-you-start).

## Répliquer des machines virtuelles Hyper-V dans Azure (sans VMM)

Si vos machines virtuelles ne sont pas gérées par un serveur System Center VMM, voici ce que vous devez faire pour les répliquer dans Azure.

- En local : 
	- **Serveur Hyper-V** : au moins un serveur hôte Hyper-V. Les hôtes Hyper-V doivent exécuter Windows Server 2012 R2.
	- **Machines protégées** : le serveur hôte Hyper-V source doit avoir au moins une machine virtuelle à protéger.
	
- Azure :
	- **Compte Azure** : vous devez disposer d’un compte Microsoft Azure.
	- **Stockage Azure** : vous aurez besoin d’un compte de stockage Azure pour stocker les données répliquées. Les données répliquées sont stockées dans le stockage Azure et les machines virtuelles Azure sont lancées au moment du basculement.

	![Entre un site Hyper-V et Microsoft Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

Dans ce scénario, le fournisseur Azure Site Recovery et l’agent Azure Recovery Services sont installés sur le serveur VMM lors du déploiement de Site Recovery. Le fournisseur coordonne et orchestre la réplication avec le service Site Recovery via Internet. L’agent gère les données de réplication de données via HTTPS 443. Les communications en provenance du fournisseur et de l’agent sont sécurisées et chiffrées. Les données répliquées dans le stockage Azure sont également chiffrées.

En savoir plus sur les [exigences relatives au déploiement](site-recovery-hyper-v-site-to-azure.md#before-you-start)

## Répliquer des machines virtuelles Hyper-V sur un centre de données secondaire

Si vous souhaitez protéger vos machines virtuelles Hyper-V en les répliquant sur un centre de données secondaire, voici les composants dont vous avez besoin. À noter que ces instructions ne valent que si votre serveur hôte Hyper-V est géré dans un cloud System Center VMM.

- **En local** : 
	- **Serveur VMM** : nous vous recommandons d’installer un serveur VMM sur le site principal et un autre sur le site secondaire, chacun contenant au moins un cloud privé VMM. Le serveur doit s’exécuter au minimum sous System Center 2012 SP1 avec les dernières mises à jour et être connecté à Internet. Le profil de capacité Hyper-V doit être défini sur les clouds.
	- **Serveur Hyper-V** : serveurs hôtes Hyper-V situés dans les clouds VMM principaux et secondaires. Les serveurs hôtes doivent exécuter au moins Windows Server 2012 avec les dernières mises à jour installées, et être connectés à Internet.
	- **Machines protégées** : le serveur hôte Hyper-V source doit avoir au moins une machine virtuelle à protéger.
	
- **Azure** : vous avez besoin d’un abonnement Azure.

	![Local vers local](./media/site-recovery-components/arch-onprem-onprem.png)

Dans ce scénario, le fournisseur Azure Site Recovery est installé lors du déploiement de Site Recovery sur le serveur VMM. Celui-ci coordonne et orchestre la réplication avec le service Site Recovery via Internet. Les données sont répliquées entre les serveurs hôtes Hyper-V principal et secondaire via une liaison LAN ou VPN en utilisant Kerberos ou une authentification par certificat. Les communications en provenance du fournisseur et celles échangées entre les serveurs hôtes Hyper-V sont sécurisées et chiffrées.

En savoir plus sur les [exigences relatives au déploiement](site-recovery-vmm-to-vmm.md#before-you-start)



## Répliquer des machines virtuelles Hyper-V sur un centre de données secondaire avec la réplication SAN

Si vos machines virtuelles se trouvent sur un hôte Hyper-V géré dans un cloud System Center VMM et que vous utilisez un stockage SAN, voici ce dont vous avez besoin pour effectuer une réplication entre deux centres de données.

- **En local** : 
	- **Groupe SAN** : un [groupe SAN pris en charge](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx) géré par le serveur VMM principal. Le SAN partage une infrastructure réseau avec une autre baie de stockage SAN dans le site secondaire.
	- **Serveur VMM** : nous vous recommandons d’installer un serveur VMM sur le site principal et un autre sur le site secondaire, chacun contenant au moins un cloud privé VMM. Le serveur doit s’exécuter au minimum sous System Center 2012 SP1 avec les dernières mises à jour et être connecté à Internet. Le profil de capacité Hyper-V doit être défini sur les clouds.
	- **Serveur Hyper-V** : serveurs hôtes Hyper-V situés dans les clouds VMM principaux et secondaires. Les serveurs hôtes doivent exécuter au moins Windows Server 2012 avec les dernières mises à jour installées, et être connectés à Internet.
	- **Machines protégées** : le serveur hôte Hyper-V source doit avoir au moins une machine virtuelle à protéger.
	
- **Azure** : vous avez besoin d’un abonnement Azure.

	![Réplication SAN](./media/site-recovery-components/arch-onprem-onprem-san.png)

Dans ce scénario, le fournisseur Azure Site Recovery est installé lors du déploiement de Site Recovery sur le serveur VMM. Celui-ci coordonne et orchestre la réplication avec le service Site Recovery via Internet. Les données sont répliquées entre les groupes de stockage principal et secondaire en utilisant la réplication SAN synchrone.

En savoir plus sur les [exigences relatives au déploiement](site-recovery-vmm-san.md#before-you-start)


## Répliquer des machines virtuelles VMware ou des serveurs physiques dans un site secondaire

Si vous voulez protéger des machines virtuelles VMware ou des ordinateurs physiques Windows/Linux en les répliquant entre deux centres de données locaux, voici ce dont vous avez besoin.

- **En local, site principal** : 
	- **Serveur de traitement** : installez le composant serveur de traitement sur votre site principal pour gérer la mise en cache, la compression et l’optimisation des données. Il gère aussi l’installation Push de l’Agent unifié sur les machines à protéger.
	- **Hyperviseur VMware ESX/ESXi et serveur vCenter** : si vous protégez des machines virtuelles VMware, vous aurez besoin d’un hyperviseur VMware ESX/ESXi ou d’un serveur VMware vCenter capable de gérer plusieurs hyperviseurs.
	- Machines protégées : l’Agent unifié doit être installé sur les machines virtuelles VMware ou les serveurs physiques Windows/Linux que vous souhaitez protéger. Cet agent est également installé sur les ordinateurs servant de serveur cible maître. Il joue le rôle de fournisseur de communication entre tous les composants InMage.
	
- **En local, site secondaire** :
	- **Serveur de configuration** : le serveur de configuration est le premier composant installé. Vous l’installez sur le site secondaire pour gérer, configurer et surveiller votre déploiement via le site web de gestion ou la console vContinuum. Le serveur de configuration inclut aussi le mécanisme Push qui permet un déploiement à distance de l’Agent unifié. Chaque déploiement ne comporte qu’un seul serveur de configuration, qui doit être installé sur un ordinateur exécutant Windows Server 2012 R2.
	- **Serveur vContinuum** : installation au même emplacement (site secondaire) que le serveur de configuration. Il intègre une console qui vous permet de gérer et surveiller votre environnement protégé. Dans une installation par défaut, le serveur vContinuum est le premier serveur cible maître et l’Agent unifiée y est installé.
	- **Serveur cible maître** : le serveur cible maître stocke les données répliquées. Il reçoit les données du serveur de traitement, crée une machine de réplication sur le site secondaire et stocke les points de rétention des données. Le nombre de serveurs cibles maîtres dont vous avez besoin dépend du nombre de machines que vous protégez. Si vous voulez effectuer une restauration sur le site principal, vous aurez là aussi besoin d’un serveur cible maître. 

- **Azure** : vous avez besoin d’un abonnement Azure. Vous devez télécharger InMage Scout pour configurer le déploiement après avoir créé un coffre Site Recovery. Vous devez aussi installer la dernière mise à jour pour tous les serveurs de composant InMage.


	![VMware vers VMware](./media/site-recovery-components/vmware-to-vmware.png)

Dans ce scénario, les modifications de la réplication différentielle sont envoyées de l’Agent unifié exécuté sur la machine protégée vers le serveur de traitement. Le serveur de traitement optimise ces données et les transfère vers le serveur cible maître du site secondaire. Le serveur de configuration gère le processus de réplication.


## Cycle de vie de la protection Hyper-V

Ce flux de travail présente le processus de protection, de réplication et de basculement des machines virtuelles Hyper-V.

1. **Activer la protection** : installez le coffre Site Recovery, configurez les paramètres de réplication d’un cloud VMM ou d’un site Hyper-V, puis activez la protection des machines virtuelles. Un travail appelé **Activer la protection** est initiée et peut être surveillée sous l’onglet **Travaux**. Le travail vérifie que la machine est conforme à la configuration requise et appelle ensuite la méthode [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx), laquelle configure la réplication vers Azure avec les paramètres que vous avez configurés. Le travail **Activer la protection** appelle aussi la méthode [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) pour initialiser une réplication complète des machines virtuelles.
2. **Réplication initiale** : un instantané de machine virtuelle est créé et les disques durs virtuels sont répliqués un par un jusqu’à ce qu’ils soient tous copiés dans Azure ou sur le centre de données secondaire. La durée de cette opération dépend de la taille, de la bande passante réseau et de la méthode de réplication initiale que vous avez choisie. Si des modifications interviennent sur les disques pendant la réplication initiale, le dispositif de suivi de réplication des réplicas Hyper-V assure le suivi de ces modifications dans des journaux de réplication Hyper-V (.hrl), qui se trouvent dans le même dossier que les disques. À chaque disque correspond un fichier .hrl, qui est envoyé au stockage secondaire. Notez que l’instantané et les fichiers journaux consomment des ressources disque pendant la réplication initiale. À la fin de la réplication initiale, l’instantané de machine virtuelle est supprimé et les modifications de disque delta figurant dans le journal sont synchronisées et fusionnées.
3. **Finaliser la protection** : une fois la réplication initiale terminée, le travail **Finaliser la protection** configure les paramètres réseau et d’autres paramètres de post-réplication et la machine virtuelle est protégée. Si vous répliquez dans Azure, vous devrez peut-être modifier les paramètres de la machine virtuelle pour la préparer au basculement. À ce stade, vous pouvez exécuter un basculement de test pour vérifier que tout fonctionne comme prévu.
4. **Réplication** : à l’issue de la réplication différentielle, la synchronisation se produit, selon les paramètres de réplication et la méthode. 
	- **Échec de la réplication** : si la réplication différentielle échoue et qu’une réplication complète serait coûteuse en bande passante et en temps, une resynchronisation se produit. Par exemple, si les fichiers .hrl atteignent 50 % de la taille du disque, la machine virtuelle est marquée pour resynchronisation. La resynchronisation réduit la quantité de données envoyées par le calcul des sommes de contrôle des machines virtuelles source et cible et l’envoi du seul delta. Une fois la resynchronisation terminée, la réplication différentielle doit reprendre. Par défaut, la resynchronisation est planifiée pour s’exécuter automatiquement en dehors des heures de bureau, mais vous pouvez resynchroniser une machine virtuelle manuellement.
	- **Erreur de réplication** : si une erreur de réplication se produit, une nouvelle tentative intégrée se produit. S’il s’agit d’une erreur non récupérable, telle qu’une erreur d’authentification ou d’autorisation ou qu’une machine de réplication est dans un état non valide, aucune nouvelle tentative n’intervient. S’il s’agit d’une erreur récupérable, par exemple une erreur réseau ou un manque d’espace disque ou de mémoire, une nouvelle tentative se produit à intervalles croissants (après 1, 2, 4, 8 et 10 minutes, puis toutes les 30 minutes).
4. **Basculements planifiés/non planifiés** : exécutez des basculements planifiés/non planifiés quand cela est nécessaire. Si vous exécutez un basculement planifié, les machines virtuelles sources sont arrêtées pour éviter toute perte de données. Une fois créées, les machines virtuelles de réplication sont en attente de validation. Vous devez les valider pour terminer le basculement, sauf si vous effectuez une réplication avec SAN, auquel cas, la validation est automatique. Dès lors que le site principal est opérationnel, la restauration peut intervenir. Si vous répliqué dans Azure, la réplication inverse est automatique. Dans le cas contraire, vous devez lancer une réplication inverse.
 

![flux de travail](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## Étapes suivantes

[Préparer le déploiement](site-recovery-best-practices.md).

<!---HONumber=AcomDC_0218_2016-->