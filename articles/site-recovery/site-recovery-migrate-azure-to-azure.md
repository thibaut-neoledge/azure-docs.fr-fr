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
	ms.date="12/14/2015"
	ms.author="raynew"/>

#  Migrer des machines virtuelles IaaS Azure entre différentes régions Azure avec Site Recovery


## Vue d’ensemble

Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d'urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles dans divers scénarios de déploiement. Pour obtenir la liste complète des scénarios de déploiement, consultez la [Vue d’ensemble d’Azure Site Recovery](site-recovery-overview.md).

Cet article explique comment utiliser Site Recovery pour migrer des machines virtuelles IaaS Azure d’une région Azure vers une autre. Cet article fait appel à la plupart des étapes décrites dans la rubrique [Configurer la protection entre des machines virtuelles ou des serveurs physiques VMWare locaux et Azure](site-recovery-vmware-to-azure.md). Nous vous suggérons de lire cet article pour obtenir des instructions détaillées sur chaque étape du déploiement.

## Prise en main

Voici ce dont vous avez besoin pour commencer :

- **Serveur de configuration** : machine virtuelle Azure agissant en tant que serveur de configuration. Le serveur de configuration coordonne la communication entre les ordinateurs locaux et serveurs Azure.
- **Serveur principal cible** : machine virtuelle Azure qui se comporte comme un serveur principal cible. Le serveur reçoit et conserve des données répliquées de machines virtuelles.
- **Serveur de processus** : création d’une machine virtuelle exécutant Windows Server 2012 R2 Les machines virtuelles protégées envoient des données de réplication à ce serveur.
- **Machines virtuelles IaaS** : machines virtuelles à migrer.

- Pour en savoir plus sur ces composants, consultez la rubrique [De quoi ai-je besoin ?](site-recovery-vmware-to-azure.md#what-do-i-need)
- Vous devez également lire les instructions de [planification de capacité](site-recovery-vmware-to-azure.md#capacity-planning) et vous assurer que vous disposez de toutes les [conditions préalables au déploiement](site-recovery-vmware-to-azure.md#before-you-start) en place avant de commencer.

## Étapes du déploiement

1. [création d'un coffre](site-recovery-vmware-to-azure.md/#step-1-create-a-vault)
2. [Déploiement du serveur de configuration](site-recovery-vmware-to-azure.md#step-2-deploy-a-configuration-server) en tant que machine virtuelle Azure.
3. [déploiement du serveur cible principal](site-recovery-vmware-to-azure.md#step-2-deploy-a-configuration-server) en tant que machine virtuelle Azure.
4. [Déploiement d’un serveur de processus](site-recovery-vmware-to-azure.md#step-4-deploy-the-on-premises-process-server). Notez les points suivants :

	- Vous devez déployer le serveur de processus sur le même réseau virtuel/sous-réseau que les machines virtuelles Iaas que vous souhaitez migrer. ![Machines virtuelles IaaS](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure1.png)

	- Après avoir déployé le serveur de processus, vérifiez qu’il peut communiquer avec machines virtuelles que vous souhaitez migrer.
	- Chaque machine virtuelle que vous souhaitez protéger exige l’installation du service installé le service Mobilité. Ce service envoie des données au serveur de processus. Le service de mobilité peut être installé manuellement ou transféré et installé automatiquement par le serveur de processus lorsque la protection de machine virtuelle est activée. Les règles de pare-feu sur les machines virtuelles IaaS que vous souhaitez migrer doivent être configurées pour autoriser l’installation Push de ce service. 

	- Une fois que le serveur de processus est déployé et enregistré avec le serveur de configuration dans le coffre Site Recovery, il doit apparaître sous l’onglet **Serveurs de configuration** dans la console de récupération de site. Notez que cela peut prendre jusqu’à 15 minutes.
	
		![serveur de processus](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure2.png)

5. [Installer les dernières mises à jour](site-recovery-vmware-to-azure.md#step-5-install-latest-updates). Assurez-vous que tous les serveurs de composant que vous avez installés sont à jour.
6. [Créer un groupe de protection](site-recovery-vmware-to-azure.md#step-7-create-a-protection-group). Pour commencer à protéger les machines virtuelles migrées à l’aide de Site Recovery, vous devez configurer un groupe de protection. Vous spécifiez des paramètres de réplication pour un groupe et elles vous être appliquées à toutes les machines que vous ajoutez à ce groupe. 
7. [Configurer des machines virtuelles](site-recovery-vmware-to-azure.md#step-8-set-up-machines-you-want-to-protect). Vous devrez accéder au service de mobilité installé sur chaque machine virtuelle (automatiquement ou manuellement).
8. [Étape 8 : Activation de la protection des machines virtuelles](site-recovery-vmware-to-azure.md#step-9-enable-protection) Activez la protection des machines virtuelles en les ajoutant à un groupe de protection. Notez les points suivants :

	- Vous pouvez découvrir les machines virtuelles IaaS que vous souhaitez migrer vers Azure à l’aide de l’adresse IP privée des machines virtuelles. Trouvez cette adresse sur le tableau de bord de machine virtuelle dans Azure.
	-  Sous l’onglet correspondant au groupe de protection que vous avez créé, cliquez sur Ajouter des machines > Machines virtuelles ![Découverte EC2](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure3.png)
	- Spécifiez l’adresse IP privée de la machine virtuelle.
		- ![Découverte EC2](./media/site-recovery-migrate-azure-to-azure/ASR_MigrateAzure4.png)
	- La protection est activée et la réplication initiale s’exécute selon les paramètres de réplication initiale du groupe de protection.
9. [Étape 9 : Exécuter un basculement non planifié](site-recovery-failover.md#run-an-unplanned-failover). Une fois la réplication initiale terminée, vous pouvez exécuter un basculement non planifié d’une région Azure vers une autre. Vous pouvez également créer un plan de récupération et exécuter un basculement non planifié pour migrer plusieurs machines virtuelles d’une région à l’autre. [Découvrez d’autres informations](site-recovery-create-recovery-plans.md) sur les plans de récupération.
		
## Étapes suivantes

Publier des commentaires ou des questions dans le [forum Site Recovery](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

<!---HONumber=AcomDC_1217_2015-->