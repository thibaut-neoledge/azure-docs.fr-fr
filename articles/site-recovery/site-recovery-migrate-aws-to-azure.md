<properties
	pageTitle="Migrer des machines virtuelles Windows dans Amazon Web Services (AWS) vers Microsoft Azure"
	description="Utiliser Azure Site Recovery pour migrer des machines virtuelles Windows en cours d’exécution dans Amazon Web Services (AWA) vers Azure."
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
	ms.date="08/26/2015"
	ms.author="raynew"/>

#  Migrer des machines virtuelles dans Amazon Web Services (AWS) vers Azure


## Vue d’ensemble

Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d’urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles dans divers déploiements. Pour obtenir la liste complète des scénarios de déploiement, consultez la [Vue d’ensemble d’Azure Site Recovery](site-recovery-overview.md).

Cet article explique comment utiliser Site Recovery pour migrer ou basculer des instances de Windows s’exécutant dans AWS sur Azure. Cet article fait appel à la plupart des étapes décrites dans la rubrique [Configurer la protection entre des machines virtuelles ou des serveurs physiques VMWare locaux et Azure](site-recovery-vmware-to-azure.md). Nous vous suggérons de lire cet article pour obtenir des instructions détaillées sur chaque étape du déploiement.

## Prise en main

Voici ce dont vous avez besoin pour commencer :

- **Serveur de configuration** : machine virtuelle Azure agissant en tant que serveur de configuration. Le serveur de configuration coordonne la communication entre les ordinateurs locaux et serveurs Azure.
- **Serveur principal cible** : machine virtuelle Azure qui se comporte comme un serveur principal cible. Le serveur reçoit et conserve des données répliquées de machines virtuelles.
- **Serveur de processus** : création d’une machine virtuelle exécutant Windows Server 2012 R2 Les machines virtuelles protégées envoient des données de réplication à ce serveur.
- **Instances de machine virtuelle EC2** : instances que vous souhaitez migrer, puis protéger.

- En savoir plus sur ces composants dans la rubrique [De quoi ai-je besoin ?](site-recovery-vmware-to-azure.md#what-do-i-need)
- Vous devez également lire les instructions de [planification de capacité](site-recovery-vmware-to-azure.md#capacity-planning) et vous assurer que vous disposez de toutes les [conditions préalables au déploiement](site-recovery-vmware-to-azure.md#before-you-start) en place avant de commencer.

## Étapes du déploiement

1. [création d'un coffre](site-recovery-vmware-to-azure.md#step-1-create-a-vault)
2. [Déploiement du serveur de configuration](site-recovery-vmware-to-azure.md#step-2-deploy-a-configuration-server) en tant que machine virtuelle Azure.
3. [déploiement du serveur cible principal](site-recovery-vmware-to-azure.md#step-2-deploy-a-configuration-server) en tant que machine virtuelle Azure.
4. [Déploiement d’un serveur de processus](site-recovery-vmware-to-azure.md#step-4-deploy-the-on-premises-process-server). Notez les points suivants :

	- Vous devez déployer le serveur de processus sur le même sous-réseau/Cloud privé virtuel de Amazon en tant qu’instances EC2. ![Instances EC2](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration1.png)

	- Après avoir déployé le serveur de processus, vérifiez qu’il peut communiquer avec les instances EC2 que vous souhaitez migrer.
	- Chaque machine virtuelle que vous souhaitez protéger exige l’installation du service installé le service Mobilité. Ce service envoie des données au serveur de processus. Le service de mobilité peut être installé manuellement ou transféré et installé automatiquement par le serveur de processus lorsque la protection de machine virtuelle est activée. Les règles de pare-feu sur les instances EC2 que vous souhaitez migrer doivent être configurées pour autoriser l’installation Push de ce service. Le groupe de sécurité pour les instances EC2 doit avoir les règles suivantes :

		![règles de pare-feu](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration2.png)

	- Une fois que le serveur de processus est déployé et enregistré avec le serveur de configuration dans le coffre Site Recovery, il doit apparaître sous l’onglet **Serveurs de configuration** dans la console de récupération de site. Cette opération peut prendre jusqu’à 15 minutes.
	
		![serveur de processus](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration3.png)

5. [Installer les dernières mises à jour](site-recovery-vmware-to-azure.md#step-5-install-latest-updates). Assurez-vous que tous les serveurs de composant que vous avez installés sont à jour.
6. [Créer un groupe de protection](site-recovery-vmware-to-azure.md#step-7-create-a-protection-group). Pour commencer à protéger les instances migrées à l’aide de Site Recovery, vous devez configurer un groupe de protection. Vous spécifiez les paramètres de réplication pour un groupe et elles seront appliquées à toutes les instances que vous ajoutez à ce groupe. 
7. [Configurer des machines virtuelles](site-recovery-vmware-to-azure.md#step-8-set-up-machines-you-want-to-protect). Vous devrez accéder au service de mobilité installé sur chaque instance (automatiquement ou manuellement).
8. [Activer la protection des machines virtuelles](site-recovery-vmware-to-azure.md#step-9-enable-protection). Activez la protection des instances en les ajoutant à un groupe de protection. Notez les points suivants :

	- Vous pouvez découvrir les instances EC2 que vous souhaitez migrer vers Azure à l’aide de l’adresse IP privée de l’instance que vous pouvez obtenir à partir de la console EC2.
	-  Sous l’onglet correspondant au groupe de protection que vous avez créé, cliquez sur Ajouter des machines > Machines virtuelles ![Découverte EC2](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration4.png)
	- Spécifiez l’adresse IP privée de l’instance.
		- ![Découverte EC2](./media/site-recovery-migrate-aws-to-azure/ASR_AWSMigration5.png)
	- La protection est activée et la réplication initiale s’exécute selon les paramètres de réplication initiale pour le groupe de protection
9. [Exécuter un basculement non planifié](site-recovery-failover.md#run-an-unplanned-failover). Une fois la réplication initiale terminée, vous pouvez exécuter un basculement non planifié d’AWS vers Azure. Si vous le souhaitez, vous pouvez créer un plan de récupération et exécuter un basculement non planifié pour migrer des machines virtuelles d’AWS vers Azure. [Découvrez d’autres informations](site-recovery-create-recovery-plans.md) sur les plans de récupération.
		
## Étapes suivantes

Publier des commentaires ou des questions dans le [forum Site Recovery](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr)

<!---HONumber=Oct15_HO3-->