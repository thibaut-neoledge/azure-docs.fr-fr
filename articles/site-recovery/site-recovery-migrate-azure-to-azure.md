<properties
	pageTitle="Migrer des machines virtuelles IaaS depuis entre différentes régions Azure avec Site Recovery | Microsoft Azure"
	description="Utilisez Site Recovery pour migrer des machines virtuelles IaaS Azure d’une région Azure vers une autre."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.workload="backup-recovery"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="02/22/2016"
	ms.author="raynew"/>

#  Migrer des machines virtuelles IaaS Azure entre différentes régions Azure avec Site Recovery

Le service Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d’urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles et de serveurs physiques. Les machines peuvent être répliquées vers Azure ou vers un centre de données local secondaire. Pour obtenir un rapide aperçu, consultez [Qu’est-ce qu’Azure Site Recovery ?](site-recovery-overview.md)

## Vue d’ensemble

Cet article explique comment utiliser Site Recovery pour migrer ou basculer des instances de Windows s’exécutant dans AWS sur Azure. Il résume les étapes décrites en détail dans [Répliquer des machines virtuelles VMware ou des serveurs physiques sur Azure](site-recovery-vmware-to-azure-classic.md). L'article lié est la dernière version améliorée du scénario qui réplique des machines virtuelles VMware ou des serveurs physiques Windows/Linux sur Azure. Nous vous suggérons de consulter cet article pour obtenir des instructions détaillées sur chaque étape du déploiement.

>[AZURE.NOTE] Pour la migration entre différentes régions, vous **ne devez plus suivre** les instructions de cet [article hérité](site-recovery-vmware-to-azure-classic-legacy.md).

## Prise en main

Voici ce dont vous avez besoin pour commencer :

- **Serveur d’administration** : une machine virtuelle locale exécutant Windows Server 2012 R2 qui agit en tant que serveur d'administration. Installez les composants Site Recovery (y compris le serveur de configuration et le serveur de traitement) sur ce serveur. Pour en savoir plus, consultez [Considérations relatives aux serveurs d’administration](site-recovery-vmware-to-azure-classic.md#management-server-considerations) et [Conditions préalables locales](site-recovery-vmware-to-azure-classic.md#on-premises-prerequisites).
- **Machines virtuelles IaaS** : machines virtuelles à migrer.

## Étapes du déploiement

1. [Créer un coffre](site-recovery-vmware-to-azure-classic.md#step-1-create-a-vault).
2. [Déployer un serveur d'administration](site-recovery-vmware-to-azure-classic.md#Step-5-install-the-management-server).
3. Après avoir déployé le serveur d’administration, vérifiez qu’il peut communiquer avec les machines virtuelles que vous souhaitez migrer.
4. [Créer un groupe de protection](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). Un groupe de protection contient des machines protégées qui partagent les mêmes paramètres de réplication. Spécifiez des paramètres de réplication pour un groupe pour que ceux-ci s’appliquent à toutes les machines que vous ajoutez à ce groupe.
5. [Installer le service Mobilité](site-recovery-vmware-to-azure-classic.md#step-9-install-the-mobility-service). Chaque machine virtuelle que vous souhaitez protéger exige l’installation du service installé le service Mobilité. Ce service envoie des données au serveur de processus. Le service de mobilité peut être installé manuellement ou transféré et installé automatiquement par le serveur de processus lorsque la protection de machine virtuelle est activée. Notez que les règles de pare-feu sur les machines virtuelles IaaS que vous souhaitez migrer doivent être configurées pour autoriser l’installation Push de ce service.
6. [Activer la protection les machines](site-recovery-vmware-to-azure-classic.md#step-10-enable-protection-for-a-machine). Ajoutez les machines que vous souhaitez protéger au groupe de réplication. 
7. Vous pouvez découvrir les machines virtuelles IaaS que vous souhaitez migrer vers Azure à l’aide de l’adresse IP privée des machines virtuelles. Trouvez cette adresse sur le tableau de bord de machine virtuelle dans Azure.
8. Sous l’onglet correspondant au groupe de protection que vous avez créé, cliquez sur **Ajouter des machines** > **Machines physiques**.

	![Découverte EC2](./media/site-recovery-migrate-azure-to-azure/migrate-add-machines.png)

9. Spécifiez l’adresse IP privée de la machine virtuelle.

	![Découverte EC2](./media/site-recovery-migrate-azure-to-azure/migrate-machine-ip.png)
	
Après avoir ajouté une machine, la protection est activée et la réplication initiale s’exécute selon les paramètres du groupe de protection.

10. [Exécuter un basculement non planifié](site-recovery-failover.md#run-an-unplanned-failover). Une fois la réplication initiale terminée, vous pouvez exécuter un basculement non planifié d’une région Azure vers une autre. Vous pouvez également créer un plan de récupération et exécuter un basculement non planifié pour migrer plusieurs machines virtuelles d’une région à l’autre. [Découvrez d’autres informations](site-recovery-create-recovery-plans.md) sur les plans de récupération.
		
## Étapes suivantes

Publier des commentaires ou des questions dans le [forum Site Recovery](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

<!---HONumber=AcomDC_0309_2016-->