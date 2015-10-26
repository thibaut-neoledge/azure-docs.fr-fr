
<properties
	pageTitle="Configurer la protection avec un seul serveur VMM"
	description="Azure Site Recovery coordonne la réplication, le basculement et la récupération des ordinateurs virtuels Hyper-V situés dans des clouds VMM locaux sur Azure ou un cloud VMM secondaire."
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
	ms.date="10/07/2015"
	ms.author="raynew"/>

#  Configurer la protection avec un seul serveur VMM

## Vue d'ensemble

Azure Site Recovery contribue à mettre en œuvre la stratégie de continuité des activités et de récupération d'urgence de votre entreprise en coordonnant la réplication, le basculement et la récupération de machines virtuelles dans divers scénarios de déploiement. Pour obtenir la liste complète des scénarios de déploiement, consultez [Vue d'ensemble d'Azure Site Recovery](site-recovery-overview.md).

Si votre infrastructure ne contient qu’un serveur VMM, vous pouvez soit déployer Azure Site Recovery pour répliquer des ordinateurs virtuels dans des clouds VMM, soit effectuer les réplications entre les clouds d’un même serveur VMM. Nous vous recommandons de ne procéder ainsi que si vous ne pouvez pas déployer deux serveurs VMM (un sur chaque site) depuis le basculement, car la récupération n’est pas transparente dans ce déploiement. Pour la récupération, vous devez basculer manuellement sur le serveur VMM sans être connecté à la console Azure Site Recovery (en utilisant Réplica Hyper-V dans la console Gestionnaire Hyper-V).

Vous pouvez configurer la réplication à l'aide d'un seul serveur VMM de deux façons :

### Déploiement autonome

Déployez un serveur VMM autonome comme un ordinateur virtuel sur un site primaire et répliquez cet ordinateur virtuel sur un site secondaire avec Azure Site Recovery et Réplica Hyper-V. Pour réduire les interruptions de service, vous pouvez installer SQL Server sur l’ordinateur virtuel VMM. Si VMM utilise un serveur SQL distant, vous devez le récupérer avant le serveur VMM.

![Serveur VMM virtuel autonome](./media/site-recovery-single-vmm/SingleVMMStandalone.png)

### Déploiement de cluster

Pour rendre VMM hautement disponible, déployez-le comme un ordinateur virtuel dans un cluster de basculement Windows. Cette opération est utile si les charges de travail critiques sont gérées par VMM, car elle garantit la disponibilité de ces charges et protège contre le basculement matériel de l'hôte sur lequel VMM s’exécute. Pour déployer un seul serveur VMM avec Site Recovery, l’ordinateur virtuel VMM doit être déployé sur un cluster étendu couvrant des sites géographiquement dispersés. La base de données SQL Server utilisée par VMM doit être protégée avec des groupes de disponibilité SQL Server AlwaysOn avec un réplica sur le site secondaire. Si un problème survient, le serveur VMM et la base de données SQL Server correspondante sont basculés et accessibles à partir du site secondaire.

![Serveur VMM virtuel en cluster](./media/site-recovery-single-vmm/SingleVMMCluster.png)


## Avant de commencer

- Cette procédure pas à pas explique comment déployer Azure Site Recovery avec un seul serveur VMM autonome.
- Vérifiez que les [conditions préalables](site-recovery-vmm-to-vmm.md/#before-you-start) sont remplies avant de commencer le déploiement.
- Le serveur VMM doit avoir au moins deux clouds configurés. L’un sera le cloud protégé et l'autre assurera la protection.
- Les clouds à protéger doivent contenir les éléments suivants :
	- un ou plusieurs groupes hôtes VMM ;
	- un ou plusieurs serveurs hôtes Hyper-V dans chaque groupe hôte ;
	- un ou plusieurs ordinateurs virtuels Hyper-V sur le serveur hôte.

Si vous rencontrez des problèmes pour mettre en œuvre ce scénario, posez vos questions sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).



## Configurer le déploiement d’un serveur

1. Si VMM n'est pas déployé, configurez-le sur un ordinateur virtuel avec une base de données SQL Server installée. Lisez la [configuration système requise](https://technet.microsoft.com/library/dn771747.aspx). 
2. Configurez au moins deux clouds sur le serveur VMM. Pour en savoir plus :

	- [Nouveautés sur le cloud privé dans System Center 2012 R2 VMM](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=) et [VMM 2012 et les clouds](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html). 
	- [Configuration de la structure de cloud VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
	- [Création d'un cloud privé dans VMM](https://technet.microsoft.com/library/jj860425.aspx) et [Procédure pas à pas : création de clouds privés avec System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).
3. Ajoutez le serveur hôte Hyper-V source hébergeant la machine virtuelle à protéger, au cloud que vous allez protéger (le cloud source). Ajoutez le serveur hôte Hyper-V cible au cloud du serveur VMM qui fournira la protection.
4. [Créez](site-recovery-vmm-to-vmm.md/#step-1-create-a-site-recovery-vault) un coffre Azure Site Recovery et générez une clé d'inscription du coffre.
4. [Installez](site-recovery-vmm-to-vmm.md/#step-3-install-the-azure-site-recovery-provider) le fournisseur d’Azure Site Recovery sur le serveur VMM et enregistrez ce dernier dans le coffre. 
5. Vérifiez que les clouds s’affichent dans le portail Site Recovery et [configurez les paramètres de protection du cloud](site-recovery-vmm-to-vmm.md/#step-4-configure-cloud-protection-settings).
	- Dans **Emplacement Source** et **Emplacement cible**, spécifiez le nom du serveur VMM.
	- Dans **Méthode de réplication**, sélectionnez **Sur le réseau** pour la réplication initiale, car les clouds sont situés sur le même serveur.

6. Éventuellement, [configurez le mappage réseau](site-recovery-vmm-to-vmm.md/#step-5-configure-network-mapping) :

	- Dans **Source** et **Cible**, spécifiez le nom du serveur VMM.
	- Dans **Réseau sur la source**, sélectionnez le réseau d’ordinateurs virtuels configuré pour le cloud que vous protégez.
	- Dans **Réseau sur la cible**, sélectionnez le réseau d’ordinateurs virtuels configuré pour le cloud à utiliser pour la protection.
	- Le mappage réseau peut être configuré entre deux réseaux d’ordinateurs virtuels sur le même serveur VMM. Si le même réseau VMM existe sur deux sites, vous pouvez créer un mappage entre les deux réseaux.
7. [Activez la protection](site-recovery-vmm-to-vmm.md/#step-7-enable-virtual-machine-protection) des ordinateurs virtuels dans le cloud VMM à protéger. 
7. Dans la console Gestionnaire Hyper-V, configurez la réplication de l’ordinateur virtuel VMM avec Réplica Hyper-V. L’ordinateur virtuel VMM ne doit être ajouté à aucun cloud VMM.


## Basculer et récupérer

### Créer des plans de récupération

Les plans de récupération regroupent des ordinateurs virtuels qui doivent être basculés et récupérés ensemble.

1. Lorsque vous créez un plan de récupération, indiquez le nom du serveur VMM dans **Source** et **Cible**. Dans **Sélectionner les ordinateurs virtuels**, les ordinateurs virtuels associés au cloud primaire s'affichent.
2. Puis [créez et personnalisez les plans de récupération](https://msdn.microsoft.com/library/azure/dn337331.aspx).


### Récupérer

En cas de sinistre, vous pouvez récupérer les charges de travail en procédant comme suit :

1. Basculez l’ordinateur virtuel VMM manuellement vers le site de récupération à partir de la console Gestionnaire Hyper-V.
2. Une fois l'ordinateur virtuel VMM récupéré, connectez-vous à la console Hyper-V Recovery Manager à partir du portail et effectuez un basculement non planifié des ordinateurs virtuels du site primaire vers le site de récupération.
3.  Une fois le basculement non planifié terminé, les utilisateurs ont accès à toutes les ressources sur le site primaire.


 

<!---HONumber=Oct15_HO3-->