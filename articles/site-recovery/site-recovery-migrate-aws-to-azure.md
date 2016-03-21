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
	ms.date="02/22/2016"
	ms.author="raynew"/>

#  Migrer des machines virtuelles Windows dans Amazon Web Services (AWS) vers Azure avec Azure Site Recovery

Le service Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d’urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles et de serveurs physiques. Les machines peuvent être répliquées vers Azure ou vers un centre de données local secondaire. Pour avoir un rapide aperçu, consultez la section [Qu’est-ce qu’Azure Site Recovery ?](site-recovery-overview.md)


## Vue d’ensemble

Cet article explique comment utiliser Site Recovery pour migrer ou basculer des instances de Windows s’exécutant dans AWS sur Azure. Il résume les étapes décrites en détail dans [Répliquer des machines virtuelles VMware ou des serveurs physiques sur Azure](site-recovery-vmware-to-azure-classic.md). L’article lié est la dernière version améliorée du scénario qui réplique des machines virtuelles VMware ou des serveurs physiques Windows/Linux sur Azure. Nous vous suggérons de consulter cet article pour obtenir des instructions détaillées sur chaque étape du déploiement.

>[AZURE.NOTE] Pour la migration de différentes machines virtuelles Windows dans AWS, vous ne devez **plus suivre** les instructions de cet [article hérité](site-recovery-vmware-to-azure-classic-legacy.md).

## Prise en main

Voici ce dont vous avez besoin pour commencer :

- **Serveur d’administration** : une machine virtuelle locale exécutant Windows Server 2012 R2 qui agit en tant que serveur d’administration. Vous installez les composants Site Recovery (y compris le serveur de configuration et le serveur de processus) sur ce serveur. Pour en savoir plus, consultez [Considérations relatives aux serveurs d’administration](site-recovery-vmware-to-azure-classic.md#management-server-considerations) et [Conditions préalables locales](site-recovery-vmware-to-azure-classic.md#on-premises-prerequisites).
- **Instances de machine virtuelle EC2** : instances que vous souhaitez migrer, puis protéger.

## Étapes du déploiement

1. [création d'un coffre](site-recovery-vmware-to-azure-classic.md#step-1-create-a-vault)
2. [Déployer un serveur d’administration](site-recovery-vmware-to-azure-classic.md#Step-5-install-the-management-server). 
3. Après avoir déployé le serveur d’administration, vérifiez qu’il peut communiquer avec les instances EC2 que vous souhaitez migrer.
4. [Créer un groupe de protection](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). Un groupe de protection contient des machines protégées qui partagent les mêmes paramètres de réplication. Spécifiez des paramètres de réplication pour un groupe afin que ceux-ci s’appliquent à toutes les machines que vous ajoutez à ce groupe. 
5. [Installer le service Mobilité](site-recovery-vmware-to-azure-classic.md#step-9-install-the-mobility-service). Chaque machine virtuelle que vous souhaitez protéger exige l’installation du service installé le service Mobilité. Ce service envoie des données au serveur de processus. Le service de mobilité peut être installé manuellement ou transféré et installé automatiquement par le serveur de processus lorsque la protection de machine virtuelle est activée. Les règles de pare-feu sur les instances EC2 que vous souhaitez migrer doivent être configurées pour autoriser l’installation Push de ce service. Le groupe de sécurité pour les instances EC2 doit avoir les règles suivantes :

	![règles de pare-feu](./media/site-recovery-migrate-aws-to-azure/migrate-firewall.png)

6. [Activer la protection des machines](site-recovery-vmware-to-azure-classic.md#step-10-enable-protection-for-a-machine). Ajoutez les machines que vous souhaitez protéger au groupe de réplication.

	![activer la protection](./media/site-recovery-migrate-aws-to-azure/migrate-add-machines.png)

7. Vous pouvez découvrir les instances EC2 que vous souhaitez migrer vers Azure à l’aide de l’adresse IP privée de l’instance que vous pouvez obtenir à partir de la console EC2. Dans le groupe de protection, vous pouvez ajouter l’adresse IP privée de chaque instance.

	![activer la protection](./media/site-recovery-migrate-aws-to-azure/migrate-machine-ip.png)

	Après avoir ajouté une machine, la protection est activée et la réplication initiale s’exécute selon les paramètres du groupe de protection.

8. [Exécuter un basculement non planifié](site-recovery-failover.md#run-an-unplanned-failover). Une fois la réplication initiale terminée, vous pouvez exécuter un basculement non planifié d’AWS vers Azure pour chaque machine virtuelle. Si vous le souhaitez, vous pouvez créer un plan de récupération et exécuter un basculement non planifié pour migrer des machines virtuelles d’AWS vers Azure. [Découvrez d’autres informations](site-recovery-create-recovery-plans.md) sur les plans de récupération.
		
## Étapes suivantes

Publier des commentaires ou des questions dans le [forum Site Recovery](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

<!------HONumber=AcomDC_0309_2016-->