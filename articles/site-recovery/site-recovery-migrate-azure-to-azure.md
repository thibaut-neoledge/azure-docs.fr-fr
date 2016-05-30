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
	ms.date="03/16/2016"
	ms.author="raynew"/>

#  Migrer des machines virtuelles IaaS Azure entre différentes régions Azure avec Azure Site Recovery

## Vue d'ensemble

Cet article explique comment utiliser Site Recovery pour migrer des machines virtuelles Azure d’une région Azure vers une autre. Avant de commencer, notez les points suivants :

- Vous ne pouvez que migrer pour l’instant. Cela signifie que vous pouvez basculer les machines virtuelles d’une région Azure à une autre, mais vous ne pourrez pas les restaurer par la suite.
- Cet article résume et utilise la plupart des étapes décrites en détail dans [Répliquer des machines virtuelles VMware ou des serveurs physiques sur Azure](site-recovery-vmware-to-azure-classic.md), qui fournit les dernières instructions améliorées pour la configuration de la réplication. Nous vous suggérons de suivre cet article pour obtenir des instructions détaillées lors de la migration.
- Vous **ne devez plus suivre** les instructions de cet [article hérité](site-recovery-vmware-to-azure-classic-legacy.md).

Publier des commentaires ou des questions au bas de cet article ou sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Composants requis

Voici ce dont vous avez besoin pour ce déploiement :

- **Serveur d’administration** : machine virtuelle exécutant Windows Server 2012 R2 et qui agit en tant que serveur d’administration. Vous installez les composants Site Recovery (y compris le serveur de configuration et le serveur de processus) sur ce serveur. Pour en savoir plus, consultez les [considérations relatives aux serveurs d’administration](site-recovery-vmware-to-azure-classic.md#management-server-considerations) et les [conditions préalables source](site-recovery-vmware-to-azure-classic.md#on-premises-prerequisites).
- **Machines virtuelles IaaS** : machines virtuelles à migrer.

## Étapes du déploiement

1. [Créer un coffre](site-recovery-vmware-to-azure-classic.md#step-1-create-a-vault).
2. [Déployer un serveur d’administration](site-recovery-vmware-to-azure-classic.md#Step-5-install-the-management-server).
3. Après avoir déployé le serveur d’administration, vérifiez qu’il peut communiquer avec les machines virtuelles que vous souhaitez migrer.
4. [Créer un groupe de protection](site-recovery-vmware-to-azure-classic.md#step-8-create-a-protection-group). Un groupe de protection contient des machines protégées qui partagent les mêmes paramètres de réplication. Spécifiez des paramètres de réplication pour un groupe afin que ceux-ci s’appliquent à toutes les machines que vous ajoutez à ce groupe.
5. [Installer le service Mobilité](site-recovery-vmware-to-azure-classic.md#step-9-install-the-mobility-service). Chaque machine virtuelle que vous souhaitez protéger exige l’installation du service installé le service Mobilité. Ce service envoie des données au serveur de processus. Le service de mobilité peut être installé manuellement ou transféré et installé automatiquement par le serveur de processus lorsque la protection de machine virtuelle est activée. Notez que les règles de pare-feu sur les machines virtuelles IaaS que vous souhaitez migrer doivent être configurées pour autoriser l’installation Push de ce service.
6. [Activer la protection des machines](site-recovery-vmware-to-azure-classic.md#step-10-enable-protection-for-a-machine). Ajoutez les machines que vous souhaitez protéger au groupe de réplication. 
7. Vous pouvez découvrir les machines virtuelles IaaS que vous souhaitez migrer vers Azure à l’aide de l’adresse IP privée des machines virtuelles. Trouvez cette adresse sur le tableau de bord de machine virtuelle dans Azure.
8. Sous l’onglet correspondant au groupe de protection que vous avez créé, cliquez sur **Ajouter des machines** > **Machines physiques**.

	![Découverte EC2](./media/site-recovery-migrate-azure-to-azure/migrate-add-machines.png)

9. Spécifiez l’adresse IP privée de la machine virtuelle.

	![Découverte EC2](./media/site-recovery-migrate-azure-to-azure/migrate-machine-ip.png)
	
	Après avoir ajouté une machine, la protection est activée et la réplication initiale s’exécute selon les paramètres du groupe de protection.

10. [Exécuter un basculement non planifié](site-recovery-failover.md#run-an-unplanned-failover). Une fois la réplication initiale terminée, vous pouvez exécuter un basculement non planifié d’une région Azure vers une autre. Vous pouvez également créer un plan de récupération et exécuter un basculement non planifié pour migrer plusieurs machines virtuelles d’une région à l’autre. [Découvrez d’autres informations](site-recovery-create-recovery-plans.md) sur les plans de récupération.
		
## Étapes suivantes

Découvrez les autres scénarios de réplication dans [Qu’est-ce que le service Azure Site Recovery ?](site-recovery-overview.md)

<!---HONumber=AcomDC_0518_2016-->