<properties
	pageTitle="Migrer des machines virtuelles Windows dans Amazon Web Services vers Azure avec Site Recovery | Microsoft Azure"
	description="Cet article décrit comment migrer des machines virtuelles Windows s’exécutant dans Amazon Web Services (AWA) vers Azure à l’aide d’Azure Site Recovery."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na"
	ms.workload="backup-recovery"
	ms.date="08/22/2016"
	ms.author="raynew"/>

#  Migrer des machines virtuelles Windows dans Amazon Web Services (AWS) vers Azure avec Azure Site Recovery

## Vue d'ensemble

Bienvenue dans Azure Site Recovery. Utilisez cet article pour migrer des instances Windows exécutées dans AWS vers Azure en utilisant Site Recovery. Avant de commencer, notez les points suivants :

- Azure dispose de deux modèles de déploiement différents pour créer et utiliser des ressources : Azure Resource Manager et classique. De plus, Azure propose deux portails : le portail Azure Classic, qui prend en charge le modèle de déploiement classique, et le portail Azure, qui gère les deux modèles de déploiement. Les étapes de base pour la migration sont les mêmes que vous configuriez Site Recovery dans le gestionnaire de ressources ou dans le portail classique. Toutefois, les instructions et captures d’écran de l’interface utilisateur de cet article sont pertinentes pour le portail Azure.
- **Actuellement vous pouvez uniquement migrer AWS vers Azure. Vous pouvez basculer les machines virtuelles d’AWS vers Azure, mais vous ne pouvez pas les restaurer à nouveau. Il n’y a pas de réplication en continu.**
- Les instructions de migration de cet article sont basées sur les instructions pour la réplication d’une machine physique vers Azure. Il contient des liens vers les étapes de [Réplication de machines virtuelles VMware ou de serveurs physiques sur Azure](site-recovery-vmware-to-azure.md), qui décrit comment répliquer un serveur physique dans le portail Azure.
- Si vous configurez Site Recovery dans le portail classique, suivez les instructions détaillées de [cet article](site-recovery-vmware-to-azure-classic.md). Vous **ne devez plus suivre** les instructions de cet [article hérité](site-recovery-vmware-to-azure-classic-legacy.md).

Publiez des commentaires ou des questions au bas de cet article ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)


## Composants requis

Voici ce dont vous avez besoin pour ce déploiement

- **Serveur de configuration** : une machine virtuelle locale exécutant Windows Server 2012 R2 qui agit en tant que serveur de configuration. Vous installez aussi les autres composants Site Recovery (y compris le serveur de traitement et le serveur maître) sur cette machine virtuelle. Vous pouvez en lire davantage dans [Architecture du scénario](site-recovery-vmware-to-azure.md#scenario-architecture) et [Configuration requise du serveur](site-recovery-vmware-to-azure.md#configuration-server-prerequisites).
- **Instances de machine virtuelle EC2** : instances tournant sous Windows que vous souhaitez migrer.

## Étapes du déploiement

Cette section décrit les étapes de déploiement dans le nouveau portail Azure. Si vous avez besoin de ces étapes de déploiement pour Site Recovery dans le portail classique, consultez [cet article](site-recovery-vmware-to-azure-classic.md).

1. [Créer un coffre](site-recovery-vmware-to-azure.md#create-a-recovery-services-vault).
2. [Déployer un serveur de configuration](site-recovery-vmware-to-azure.md#step-2-set-up-the-source-environment).
3. Après avoir déployé le serveur de configuration, vérifiez qu’il peut communiquer avec les machines virtuelles que vous souhaitez migrer.
4. [Configurer les paramètres de réplication](site-recovery-vmware-to-azure.md#step-4-set-up-replication-settings). Créez une stratégie de réplication et affectez-la au serveur de configuration.
5. [Installer le service Mobilité](site-recovery-vmware-to-azure.md#step-6-replication-application). Chaque machine virtuelle que vous souhaitez protéger exige l’installation du service installé le service Mobilité. Ce service envoie des données au serveur de processus. Le service de mobilité peut être installé manuellement ou transféré et installé automatiquement par le serveur de processus lorsque la protection de machine virtuelle est activée. Les règles de pare-feu sur les instances EC2 que vous souhaitez migrer doivent être configurées pour autoriser l’installation Push de ce service. Le groupe de sécurité pour les instances EC2 doit avoir les règles suivantes :

	![règles de pare-feu](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)

6. [Activer la réplication](site-recovery-vmware-to-azure.md#enable-replication). Activez la réplication pour les machines virtuelles que vous souhaitez migrer. Vous pouvez découvrir les instances EC2 à l’aide de l’adresse IP privée, que vous pouvez obtenir à partir de la console EC2.
7. [Exécuter un basculement non planifié](site-recovery-failover.md#run-an-unplanned-failover). Une fois la réplication initiale terminée, vous pouvez exécuter un basculement non planifié d’AWS vers Azure pour chaque machine virtuelle. Si vous le souhaitez, vous pouvez créer un plan de récupération et exécuter un basculement non planifié pour migrer des machines virtuelles d’AWS vers Azure. [Découvrez d’autres informations](site-recovery-create-recovery-plans.md) sur les plans de récupération.

## Étapes suivantes

Découvrez les autres scénarios de réplication dans [Qu’est-ce que le service Azure Site Recovery ?](site-recovery-overview.md)

<!---HONumber=AcomDC_0824_2016-->