<properties
    pageTitle="Comment Site Recovery fonctionne-t-il ? | Microsoft Azure"
    description="Cet article propose une vue d’ensemble de l’architecture de Site Recovery."
    services="site-recovery"
    documentationCenter=""
    authors="rayne-wiselman"
    manager="cfreeman"
    editor=""/>

<tags
    ms.service="site-recovery"
    ms.workload="backup-recovery"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.date="10/13/2016"
    ms.author="raynew"/>


# <a name="how-does-azure-site-recovery-work?"></a>Comment Azure Site Recovery fonctionne-t-il ?

Lisez cet article pour comprendre l’architecture du service Azure Site Recovery et les composants qui le font fonctionner.

Publiez des commentaires ou des questions au bas de cet article ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## <a name="overview"></a>Vue d'ensemble

Les organisations ont besoin d’une stratégie de continuité des activités et de récupération d’urgence pour que les charges de travail et les données demeurent opérationnelles et disponibles pendant les temps d’arrêt prévus et imprévus et pour rétablir au plus vite des conditions de travail normales. 

Site Recovery est un service Azure qui participe à votre stratégie de continuité des activités et de récupération d’urgence. Il orchestre et automatise la réplication des machines virtuelles et des serveurs physiques locaux vers le cloud (Azure) ou un centre de données secondaire. Lorsque des pannes se produisent sur votre site principal, vous effectuez un basculement sur le site secondaire pour préserver la disponibilité des applications et des charges de travail. Vous restaurez votre site principal dès lors qu’il retrouve un fonctionnement normal. Pour lire une présentation, consultez [Qu’est-ce que Site Recovery ?](site-recovery-overview.md)

## <a name="site-recovery-in-the-azure-portal"></a>Site Recovery dans le portail Azure

Azure dispose de deux [modèles de déploiement](../resource-manager-deployment-model.md) différents pour créer et utiliser des ressources : le modèle Azure Resource Manager et le modèle classique de gestion des services. De plus, Azure propose deux portails : le [portail Azure Classic](https://manage.windowsazure.com/), qui prend en charge le modèle de déploiement classique, et le [portail Azure](https://portal.azure.com), qui gère les deux modèles de déploiement.

- Site Recovery est disponible dans le portail Azure Classic et dans le portail Azure.
- Dans le Portail Azure Classic, vous pouvez prendre en charge Site Recovery avec le modèle classique de gestion des services.
- Dans le portail Azure, vous pouvez prendre en charge les déploiements classiques et Ressource Manager. 

Les informations contenues dans cet article s’appliquent aux déploiements du portail Azure Classic et du portail Azure. Le tableau récapitule les différences.

**Réplication** | **Portail Azure** | **Portail classique**
--- | --- | ---
**Réplication de machines virtuelles VMware sur Azure** | Processus de déploiement simplifié.<br/><br/> Basculement de machines virtuelles vers un stockage classique ou basé sur Resource Manager.<br/><br/> Réplication vers un stockage classique ou basé sur Resource Manager.<br/><br/> Utilisation des réseaux classiques ou Resource Manager pour se connecter aux machines virtuelles Azure après le basculement.<br/><br/> Utilisation du stockage LRS ou GRS. | Basculement vers le stockage classique uniquement.<br/><br/> Utilisation de réseaux classiques uniquement pour connecter des machines virtuelles après le basculement.<br/><br/> Utilisation du stockage GRS.
**Réplication de machines virtuelles Hyper-V (sans VMM) sur Azure** | Processus de déploiement simplifié.<br/><br/> Basculement de machines virtuelles vers un stockage classique ou basé sur Resource Manager.<br/><br/> Réplication vers un stockage classique ou basé sur Resource Manager.<br/><br/> Utilisation des réseaux classiques ou Resource Manager pour se connecter aux machines virtuelles Azure après le basculement.
**Réplication de machines virtuelles Hyper-V (avec VMM) sur Azure** | Processus de déploiement simplifié.<br/><br/> Basculement de machines virtuelles vers un stockage classique ou basé sur Resource Manager.<br/><br/> Réplication vers un stockage classique ou basé sur Resource Manager.<br/><br/> Utilisation des réseaux classiques ou Resource Manager pour se connecter aux machines virtuelles Azure après le basculement.<br/><br/> Doit configurer le mappage réseau | Basculement vers le stockage classique uniquement.<br/><br/> Utilisation de réseaux classiques uniquement pour connecter des machines virtuelles après le basculement.
**Réplication de machines virtuelles Hyper-V (avec VMM) sur un site secondaire** | Processus de déploiement simplifié.<br/><br/> Basculement de machines virtuelles vers un stockage classique ou basé sur Resource Manager.<br/><br/> Réplication vers un stockage classique ou basé sur Resource Manager.<br/><br/> Utilisation des réseaux classiques ou Resource Manager pour se connecter aux machines virtuelles Azure après le basculement.<br/><br/> Doit configurer le mappage réseau | Basculement vers le stockage classique uniquement.<br/><br/> Utilisation de réseaux classiques uniquement pour connecter des machines virtuelles après le basculement.<br/><br/> Vous pouvez configurer le mappage de stockage. <br/><br/> La réplication SAN n’est pas pris en charge.



## <a name="deployment-scenarios"></a>Scénarios de déploiement

Site Recovery peut être déployé pour orchestrer la réplication dans un certain nombre de scénarios :

- **Réplication de machines virtuelles VMware** : vous pouvez répliquer des machines virtuelles VMware locales dans un stockage Azure ou un centre de données secondaire.
- **Réplication d’ordinateurs physiques** : vous pouvez répliquer des ordinateurs physiques exécutant Windows ou Linux dans un stockage Azure ou dans un centre de données secondaire. Le processus de réplication des ordinateurs physiques est presque identique au processus de réplication des machines virtuelles VMware.
- **Réplication des machines virtuelles Hyper-V** : si vos hôtes Hyper-V sont gérés dans des clouds VMM du System Center, vous pouvez répliquer des machines virtuelles dans Azure ou un centre de données secondaire. Vous pouvez répliquer dans Azure uniquement si les hôtes ne sont pas gérés par VMM. Vous pouvez répliquer des machines virtuelles Hyper-V qui ne sont pas gérées par VMM dans un stockage Azure.
- **Migration de machines virtuelles** : vous pouvez utiliser Site Recovery pour [migrer des machines virtuelles Azure IaaS](site-recovery-migrate-azure-to-azure.md) entre différentes régions ou pour [migrer des instances AWS Windows](site-recovery-migrate-aws-to-azure.md) vers des machines virtuelles Azure IaaS. La réplication complète n’est actuellement pas prise en charge. Vous pouvez effectuer une réplication pour la migration (basculement), mais pas de restauration automatique. 

Azure Site Recovery peut répliquer la plupart des applications exécutées sur ces machines virtuelles et ces serveurs physiques. Vous pouvez consulter la page [Quelles charges de travail pouvez-vous protéger avec Azure Site Recovery ?](site-recovery-workload.md)


## <a name="replicate-vmware-virtual-machines-to-azure"></a>Répliquez des machines virtuelles VMware sur Microsoft Azure.

![Amélioré](./media/site-recovery-components/arch-enhanced.png)

**Composant** | **Détails**
--- | ---
**Microsoft Azure** | **Compte** : vous avez besoin d’un compte Azure.<br/><br/> **Stockage** : vous aurez besoin d’un compte de stockage Azure pour stocker les données répliquées. Vous pouvez utiliser un compte classique ou un compte de stockage Resource Manager. Le compte peut être de type LRS ou GRS lorsque vous déployez dans le portail Azure. Les données répliquées sont stockées dans le stockage Azure et les machines virtuelles Azure sont lancées au moment du basculement.<br/><br/> **Réseau** : vous avez besoin d’un réseau virtuel Azure auquel les machines virtuelles Azure se connecteront lorsqu’elles seront créées au moment du basculement. Dans le portail Azure, des réseaux peuvent être créés dans le modèle classique Service Manager ou dans le modèle Resource Manager.
**Serveur de configuration local** |  vous avez besoin d’une machine Windows Server 2012 R2 local qui exécute le serveur de configuration et d’autres composants de Site Recovery.<br/><br/> Il doit s’agir d’une machine virtuelle VMware à haute disponibilité.<br/><br/> Les composants sur le serveur sont les suivants :<br/><br/> **Serveur de configuration**: coordonne la communication entre votre environnement local et Azure et gère la réplication et la récupération des données.<br/><br/> **Serveur de processus**: fait office de passerelle de réplication. Il reçoit les données de réplication d’ordinateurs source protégés, les optimise grâce à la mise en cache, la compression et le chiffrement et envoie les données vers le stockage Azure. Il gère également l’installation Push du service Mobilité sur des machines protégées et assure la détection automatique des machines virtuelles VMware. À mesure que s’étend votre déploiement, vous pouvez ajouter des serveurs de traitement dédiés supplémentaires afin de gérer de plus grands volumes de trafic de réplication.<br/><br/> **Serveur cible maître**: gère les données de réplication pendant la restauration automatique à partir d’Azure.
**Serveurs de virtualisation locaux** | Un ou plusieurs hôtes vSphere. Nous recommandons également d’utiliser un serveur vCenter pour gérer les hôtes.
**Machines virtuelles à répliquer** | Le composant du service Mobilité doit être installé sur chaque machine virtuelle VMware que vous souhaitez répliquer dans Azure. Ce service enregistre les écritures de données sur la machine et les transmet au serveur de traitement. Ce composant peut être installé manuellement ou transféré et installé automatiquement par le serveur de traitement lorsque vous activez la réplication d’une machine.

- [En savoir plus](site-recovery-vmware-to-azure.md#azure-prerequisites) sur les exigences relatives au déploiement du portail Azure.
- [En savoir plus](site-recovery-failback-azure-to-vmware.md) sur la restauration automatique dans le portail Azure.


## <a name="replicate-physical-servers-to-azure"></a>Répliquer des serveurs physiques dans Azure

![Amélioré](./media/site-recovery-components/arch-enhanced.png)

**Composant** | **Détails**
--- | ---
**Microsoft Azure** | **Compte** : vous avez besoin d’un compte Azure.<br/><br/> **Stockage** : vous aurez besoin d’un compte de stockage Azure pour stocker les données répliquées. Vous pouvez utiliser un compte classique ou un compte de stockage Resource Manager. Le compte peut être de type LRS ou GRS lorsque vous déployez dans le portail Azure. Les données répliquées sont stockées dans le stockage Azure et les machines virtuelles Azure sont lancées au moment du basculement.<br/><br/> **Réseau** : vous avez besoin d’un réseau virtuel Azure auquel les machines virtuelles Azure se connecteront lorsqu’elles seront créées au moment du basculement. Dans le portail Azure, des réseaux peuvent être créés dans le modèle classique Service Manager ou dans le modèle Resource Manager.
**Serveur de configuration local** |  vous avez besoin d’une machine Windows Server 2012 R2 local qui exécute le serveur de configuration et d’autres composants de Site Recovery.<br/><br/> Le serveur peut être virtuel ou physique.<br/><br/> Les composants sur le serveur sont les suivants :<br/><br/> **Serveur de configuration**: coordonne la communication entre votre environnement local et Azure et gère la réplication et la récupération des données.<br/><br/> **Serveur de processus**: fait office de passerelle de réplication. Il reçoit les données de réplication d’ordinateurs source protégés, les optimise grâce à la mise en cache, la compression et le chiffrement et envoie les données vers le stockage Azure. Il gère également l’installation Push du service Mobilité sur des machines protégées et assure la détection automatique des machines virtuelles VMware. À mesure que s’étend votre déploiement, vous pouvez ajouter des serveurs de traitement dédiés supplémentaires afin de gérer de plus grands volumes de trafic de réplication.<br/><br/> **Serveur cible maître**: gère les données de réplication pendant la restauration automatique à partir d’Azure.
**Machines à répliquer** | Le composant du service Mobilité doit être installé sur chaque serveur Windows/Linux physique que vous souhaitez répliquer dans Azure. Ce service enregistre les écritures de données sur la machine et les transmet au serveur de traitement. Ce composant peut être installé manuellement ou transféré et installé automatiquement par le serveur de traitement lorsque vous activez la réplication d’une machine.
**Restauration automatique** | La restauration automatique physique à physique n’est pas prise en charge. Cela signifie que si vous basculez les serveurs physiques vers Azure, puis souhaitez les restaurer automatiquement, vous devez le faire vers une machine virtuelle VMware. Il est impossible d’effectuer cette procédure vers un serveur physique. Vous aurez besoin d’une machine virtuelle Azure vers laquelle effectuer la restauration et, si vous n’avez pas déployé le serveur de configuration en tant que machine virtuelle VMware, vous devrez configurer un serveur cible maître distinct en tant que machine virtuelle VMware. C’est nécessaire, car le serveur cible maître interagit sur et s’attache au stockage VMware pour restaurer les disques vers une machine virtuelle VMware.


- [En savoir plus](site-recovery-vmware-to-azure.md#azure-prerequisites) sur les exigences relatives au déploiement du portail Azure.
- [En savoir plus](site-recovery-failback-azure-to-vmware.md) sur la restauration automatique dans le portail Azure.


## <a name="replicate-hyper-v-vms-not-managed-by-vmm-to-azure"></a>Réplication de machines virtuelles Hyper-V non gérées par VMM dans Azure

![Entre un site Hyper-V et Microsoft Azure](./media/site-recovery-components/arch-onprem-azure-hypervsite.png)

**Composant** | **Détails**
--- | ---
**Microsoft Azure** | **Compte** : vous avez besoin d’un compte Azure.<br/><br/> **Stockage** : vous aurez besoin d’un compte de stockage Azure pour stocker les données répliquées. Vous pouvez utiliser un compte classique ou un compte de stockage Resource Manager. Le compte doit être GRS. Les données répliquées sont stockées dans le stockage Azure et les machines virtuelles Azure sont lancées au moment du basculement.<br/><br/> **Réseau** : vous avez besoin d’un réseau virtuel Azure auquel les machines virtuelles Azure se connecteront lorsqu’elles seront créées au moment du basculement.
**Hôtes/Machines virtuelles Hyper-V** | Un ou plusieurs hôtes Hyper-V exécutant une ou plusieurs machines virtuelles.<br/><br/> Le fournisseur et l’agent Site Recovery sont installés sur chaque hôte lors du déploiement.

- [En savoir plus](site-recovery-hyper-v-site-to-azure.md#azure-prerequisites) sur les exigences relatives au déploiement du portail Azure.
- [En savoir plus](site-recovery-hyper-v-site-to-azure-classic.md#azure-prerequisites) sur les exigences relatives au déploiement du portail Classic.



## <a name="replicate-hyper-v-vms-managed-by-vmm-to-azure"></a>Réplication de machines virtuelles Hyper-V gérées par VMM dans Azure


![VMM vers Azure](./media/site-recovery-components/arch-onprem-onprem-azure-vmm.png)

**Composant** | **Détails**
--- | ---
**Microsoft Azure** | **Compte** : vous avez besoin d’un compte Azure.<br/><br/> **Stockage** : vous aurez besoin d’un compte de stockage Azure pour stocker les données répliquées. Vous pouvez utiliser un compte classique ou un compte de stockage Resource Manager. Le compte doit être GRS. Les données répliquées sont stockées dans le stockage Azure et les machines virtuelles Azure sont lancées au moment du basculement.<br/><br/> **Réseau** : vous avez besoin d’un réseau virtuel Azure auquel les machines virtuelles Azure se connecteront lorsqu’elles seront créées au moment du basculement.
**Serveur VMM** | Vous avez besoin d’un ou plusieurs serveurs VMM locaux, avec un ou plusieurs clouds privés. Le fournisseur Site Recovery est installé sur chaque serveur lors du déploiement.
**Hôtes/Machines virtuelles Hyper-V** | Un ou plusieurs hôtes Hyper-V exécutant une ou plusieurs machines virtuelles.<br/><br/> L’agent Recovery Services est installé sur chaque hôte lors du déploiement.


- [En savoir plus](site-recovery-vmm-to-azure.md#azure-requirements) sur les exigences relatives au déploiement du portail Azure.
- [En savoir plus](site-recovery-vmm-to-azure-classic.md#before-you-start) sur les exigences relatives au déploiement du portail Classic.




## <a name="replicate-vmware-virtual-machines-or-physical-server-to-a-secondary-site"></a>Réplication de machines virtuelles VMware ou d’un serveur physique dans un site secondaire

![VMware vers VMware](./media/site-recovery-components/vmware-to-vmware.png)


**Composant** | **Détails**
--- | ---
**Microsoft Azure** | **Compte** : vous déployez ce scénario à l’aide d’InMage Scout. Pour l’obtenir, vous avez besoin d’un compte Azure.<br/><br/> Après avoir créé un coffre Site Recovery, téléchargez InMage Scout et installez les dernières mises à jour pour configurer le déploiement.
**Site principal** | **Serveur de traitement** : installez le composant serveur de traitement sur votre site principal pour gérer la mise en cache, la compression et l’optimisation des données. Il gère aussi l’installation Push de l’Agent unifié sur les machines à protéger.<br/><br/> **VMware ESX/ESXi et vCenter** : vous avez besoin d’un hyperviseur EXS/ESXi VMware et éventuellement d’un serveur vCenter VMware.<br/><br/> **Machines virtuelles** | Pendant le déploiement, l’agent unifié est installé sur les machines à protéger. Il joue le rôle de fournisseur de communication entre tous les composants.
**Site secondaire** | **Serveur de configuration** : le serveur de configuration est le premier composant installé. Vous l’installez sur le site secondaire pour gérer, configurer et surveiller votre déploiement via le site web de gestion ou la console vContinuum. Chaque déploiement ne comporte qu’un seul serveur de configuration, qui doit être installé sur un ordinateur exécutant Windows Server 2012 R2.<br/><br/> **Serveur vContinuum (site secondaire)** : installé au même emplacement que le serveur de configuration. Il intègre une console qui vous permet de gérer et surveiller votre environnement protégé. Dans une installation par défaut, le serveur vContinuum est le premier serveur cible maître et l’agent unifié y est installé.<br/><br/> **Serveur cible maître** : le serveur cible maître stocke les données répliquées. Il reçoit les données du serveur de traitement, crée une machine de réplication sur le site secondaire et stocke les points de rétention des données. Le nombre de serveurs cibles maîtres dont vous avez besoin dépend du nombre de machines que vous protégez. Si vous voulez effectuer une restauration sur le site principal, vous aurez là aussi besoin d’un serveur cible maître.


Pour répliquer des machines virtuelles VMware ou des serveurs physiques dans un site secondaire, téléchargez InMage Scout inclus dans l’abonnement Azure Site Recovery. Il peut être téléchargé à partir du portail Azure ou du portail Azure Classic.

Vous configurez les serveurs de composants dans chaque site (configuration, processus, cible maître) et installez l’Agent unifié sur les ordinateurs que vous souhaitez répliquer. Après la réplication initiale, l’agent de chaque machine envoie les modifications de réplication différentielle au serveur de traitement. Le serveur de traitement optimise ces données et les transfère vers le serveur cible maître du site secondaire. Le serveur de configuration gère le processus de réplication.


## <a name="replicate-hyper-v-vms-managed-by-vmm-to-a-secondary-site"></a>Réplication de machines virtuelles Hyper-V gérées par VMM dans un site secondaire

![Local vers local](./media/site-recovery-components/arch-onprem-onprem.png)

**Composant** | **Détails**
--- | ---
**Microsoft Azure** | **Compte** : vous avez besoin d’un compte Azure.
**Serveur VMM** | Nous recommandons l’utilisation d’un serveur VMM dans le site principal et un autre dans le site secondaire. Chaque serveur requiert un ou plusieurs clouds privés.<br/><br/> Lors du déploiement, vous installez le fournisseur Azure Site Recovery sur le serveur VMM.
**Hôtes/Machines virtuelles Hyper-V** | Un ou plusieurs hôtes Hyper-V en cours d’exécution dans les clouds VMM sur le site principal et secondaire<br/><br/> Chaque hôte doit avoir une ou plusieurs machines virtuelles à répliquer.<br/><br/>. L’agent Recovery Services est installé sur chaque hôte lors du déploiement.

- [En savoir plus](site-recovery-vmm-to-vmm.md#azure-prerequisites) sur les exigences relatives au déploiement dans le portail Azure.
- [En savoir plus](site-recovery-vmm-to-vmm-classic.md#before-you-start) sur les exigences relatives au déploiement dans le portail Azure Classic.


## <a name="replicate-hyper-v-vms-managed-by-vmm-to-a-secondary-site-using-san-replication"></a>Réplication de machines virtuelles Hyper-V gérées par VMM dans un site secondaire avec réplication SAN

![Réplication SAN](./media/site-recovery-components/arch-onprem-onprem-san.png)

Vous pouvez répliquer des machines virtuelles Hyper-V gérées dans des clouds VMM dans un site secondaire à l’aide de la réplication SAN dans le portail classique. Actuellement, ce scénario n’est pas pris en charge dans le portail Azure.

**Composant** | **Détails**
--- | ---
**Microsoft Azure** | **Compte** : vous avez besoin d’un compte Azure.
**Serveur VMM** | Nous recommandons l’utilisation d’un serveur VMM dans le site principal et un autre dans le site secondaire. Chaque serveur requiert un ou plusieurs clouds privés.<br/><br/> Lors du déploiement, vous installez le fournisseur Azure Site Recovery sur le serveur VMM.
**SAN** | Un groupe SAN pris en charge géré par le serveur VMM principal.<br/><br/> Le SAN doit partager une infrastructure réseau avec un autre groupe SAN dans le site secondaire.
**Hôtes/Machines virtuelles Hyper-V** | Un ou plusieurs hôtes Hyper-V en cours d’exécution dans les clouds VMM sur le site principal et secondaire<br/><br/> Chaque hôte doit avoir une ou plusieurs machines virtuelles à répliquer.<br/><br/>. L’agent Recovery Services est installé sur chaque hôte lors du déploiement.

Pour déployer ce scénario au cours d’un déploiement de Site Recovery, le fournisseur Azure Site Recovery est installé sur les serveurs VMM. Le fournisseur coordonne et orchestre la réplication avec le service Site Recovery via Internet. Les données sont répliquées entre les groupes de stockage principal et secondaire en utilisant la réplication SAN synchrone.

[En savoir plus](site-recovery-vmm-san.md#before-you-start) sur les exigences relatives au déploiement.

## <a name="hyper-v-protection-lifecycle"></a>Cycle de vie de la protection Hyper-V

Ce flux de travail présente le processus de protection, de réplication et de basculement des machines virtuelles Hyper-V.

1. **Activer la protection**: installez le coffre Site Recovery, configurez les paramètres de réplication d’un cloud VMM ou d’un site Hyper-V, puis activez la protection des machines virtuelles. Un travail appelé **Activer la protection** est initié et peut être surveillé sous l’onglet **Travaux**. Le travail vérifie que la machine est conforme à la configuration requise et appelle ensuite la méthode [CreateReplicationRelationship](https://msdn.microsoft.com/library/hh850036.aspx) , laquelle configure la réplication vers Azure avec les paramètres que vous avez configurés. Le travail **Activer la protection** appelle aussi la méthode [StartReplication](https://msdn.microsoft.com/library/hh850303.aspx) pour initialiser une réplication complète des machines virtuelles.
2. **Réplication initiale** : un instantané de machine virtuelle est créé et les disques durs virtuels sont répliqués un par un jusqu’à ce qu’ils soient tous copiés dans Azure ou sur le centre de données secondaire. La durée de cette opération dépend de la taille de la machine virtuelle, de la bande passante réseau et de la méthode de réplication initiale que vous avez choisie. Si des modifications interviennent sur les disques pendant la réplication initiale, le dispositif de suivi de réplication des réplicas Hyper-V assure le suivi de ces modifications dans des journaux de réplication Hyper-V (.hrl), qui se trouvent dans le même dossier que les disques. À chaque disque correspond un fichier .hrl, qui est envoyé au stockage secondaire. Notez que l’instantané et les fichiers journaux consomment des ressources disque pendant la réplication initiale. À la fin de la réplication initiale, l’instantané de machine virtuelle est supprimé et les modifications de disque delta figurant dans le journal sont synchronisées et fusionnées.
3. **Finaliser la protection** : une fois la réplication initiale terminée, le travail **Finaliser la protection** configure les paramètres réseau et d’autres paramètres de post-réplication de sorte que la machine virtuelle soit protégée. Si vous répliquez dans Azure, vous devrez peut-être modifier les paramètres de la machine virtuelle pour la préparer au basculement. À ce stade, vous pouvez exécuter un basculement de test pour vérifier que tout fonctionne comme prévu.
4. **Réplication**: la synchronisation delta commence à l’issue de la réplication initiale, selon les paramètres de réplication.
    - **Échec de la réplication**: si la réplication différentielle échoue et si une réplication complète serait coûteuse en bande passante et en temps, une resynchronisation se produit. Par exemple, si les fichiers .hrl atteignent 50 % de la taille du disque, la machine virtuelle est marquée pour resynchronisation. La resynchronisation réduit la quantité de données envoyées par le calcul des sommes de contrôle des machines virtuelles source et cible et l’envoi du seul delta. Une fois la resynchronisation terminée, la réplication différentielle doit reprendre. Par défaut, la resynchronisation est planifiée pour s’exécuter automatiquement en dehors des heures de bureau, mais vous pouvez resynchroniser une machine virtuelle manuellement.
    - **Erreur de réplication**: si une erreur de réplication se produit, une nouvelle tentative intégrée se produit. S’il s’agit d’une erreur non récupérable, telle qu’une erreur d’authentification ou d’autorisation ou qu’une machine de réplication est dans un état non valide, aucune nouvelle tentative n’intervient. S’il s’agit d’une erreur récupérable, par exemple une erreur réseau ou un manque d’espace disque ou de mémoire, une nouvelle tentative se produit à intervalles croissants (après 1, 2, 4, 8 et 10 minutes, puis toutes les 30 minutes).
4. **Basculements planifiés/non planifiés**: exécutez des basculements planifiés/non planifiés quand cela est nécessaire. Si vous exécutez un basculement planifié, les machines virtuelles sources sont arrêtées pour éviter toute perte de données. Une fois créées, les machines virtuelles de réplication sont en attente de validation. Vous devez les valider pour terminer le basculement, sauf si vous effectuez une réplication avec SAN, auquel cas, la validation est automatique. Dès lors que le site principal est opérationnel, la restauration peut intervenir. Si vous répliquez dans Azure, la réplication inverse est automatique. Dans le cas contraire, vous devez lancer manuellement une réplication inverse.


![flux de travail](./media/site-recovery-components/arch-hyperv-azure-workflow.png)

## <a name="next-steps"></a>Étapes suivantes

[Préparer le déploiement](site-recovery-best-practices.md)



<!--HONumber=Oct16_HO2-->


