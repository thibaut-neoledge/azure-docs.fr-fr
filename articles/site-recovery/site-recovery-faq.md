<properties 
	pageTitle="Site Recovery : Forum Aux Questions | Microsoft Azure" 
	description="Cet article répond aux questions courantes sur Azure Site Recovery."
	services="site-recovery" 
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags 
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na" 
	ms.workload="storage-backup-recovery"
	ms.date="02/14/2016"
	ms.author="raynew"/>


# Azure Site Recovery : Forum Aux Questions (FAQ)

Cet article contient le Forum Aux Questions relatif à Site Recovery.

Si vous avez toujours des questions après avoir lu cet article, vous pouvez les poser en bas de cet article ou sur le [forum Azure Recovery Services](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## Vue d’ensemble

Site Recovery contribue à votre stratégie de continuité des activités et de récupération d’urgence en s’assurant que les charges de travail et les applications restent disponibles en cas de pannes. Site Recovery orchestre et automatise la réplication des machines virtuelles et des serveurs physiques locaux vers Azure ou vers un centre de données secondaire. [En savoir plus](site-recovery-overview.md).


### Que peut protéger Site Recovery ?

- **Machines virtuelles Hyper-V** : Site Recovery peut protéger toute charge de travail en cours d’exécution sur une machine virtuelle Hyper-V. 
- **Serveurs physiques** : Site Recovery peut protéger les charges de travail s’exécutant sur des serveurs Windows/Linux.
- **Machines virtuelles VMware** : Site Recovery peut protéger toute charge de travail s’exécutant sur une machine virtuelle VMware.


### Quelle est la configuration requise pour Hyper-V ?

La configuration requise pour le serveur hôte Hyper-V dépend du scénario de déploiement. Vérifiez les composants requis dans :

- [Réplication de machines virtuelles Hyper-V (sans VMM) sur Azure](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Réplication de machines virtuelles Hyper-V (avec VMM) sur Azure](site-recovery-vmm-to-azure.md#before-you-start)
- [Réplication de machines virtuelles Hyper-V sur un centre de données secondaire](site-recovery-vmm-to-vmm.md#before-you-start)

Configuration requise pour les invités s’exécutant sur le serveur hôte Hyper-V :

- Si vous effectuez une réplication vers un site secondaire, consultez la liste des systèmes d’exploitation invités pris en charge pour les machines virtuelles s’exécutant sur des serveurs hôtes Hyper-V dans [Systèmes d’exploitation invités pris en charge pour les machines virtuelles Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
- Si vous effectuez une réplication vers Azure, Site Recovery prend en charge tous les systèmes d’exploitation invités [pris en charge par Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).


### Puis-je protéger des machines virtuelles lorsque Hyper-V est en cours d’exécution sur un système d’exploitation client ?

Non, les machines virtuelles doivent se trouver sur un serveur hôte Hyper-V exécutant une machine serveur Windows prise en charge. Si vous devez protéger un ordinateur client, vous pouvez le répliquer en tant que machine physique [vers Azure](site-recovery-vmware-to-azure-classic.md) ou vers un [centre de données secondaire](site-recovery-vmware-to-vmware.md).


### Quelles charges de travail puis-je protéger avec Site Recovery ?

Site Recovery peut répliquer la plupart des charges de travail s’exécutant sur un serveur physique ou une machine virtuelle pris en charge. Site Recovery assure également la prise en charge de la réplication compatible avec les applications afin qu’elles puissent être récupérées dans un état intelligent. Site Recovery s’intègre aux applications Microsoft, notamment à SharePoint, Exchange, Dynamics, SQL Server et Active Directory, et fonctionne en étroite collaboration avec les principaux fournisseurs que sont Oracle, SAP, IBM et Red Hat. [En savoir plus](site-recovery-workload.md) sur la protection des charges de travail.


### Ai-je toujours besoin d’un serveur VMM System Center pour protéger les machines virtuelles Hyper-V ? 

Si vous souhaitez effectuer une réplication vers un centre de données secondaire, les machines virtuelles Hyper-V doivent être situées sur les serveurs hôtes Hyper-V d’un cloud VMM. Néanmoins si vous souhaitez procéder à une réplication vers Azure, vous pouvez répliquer des machines virtuelles sur des serveurs hôtes Hyper-V avec ou sans clouds VMM. [En savoir plus](site-recovery-hyper-v-site-to-azure.md).

### Puis-je déployer Site Recovery avec VMM si je ne dispose que d’un seul serveur VMM ? 

Oui. Vous pouvez répliquer des machines virtuelles Hyper-V dans le cloud sur le serveur VMM vers Azure, ou répliquer entre des clouds VMM sur le même serveur. Pour la réplication entre sites locaux, nous recommandons de disposer d’un serveur VMM dans les sites principaux et secondaires. [En savoir plus](site-recovery-single-vmm.md)

### Quels serveurs physiques puis-je protéger ?

Vous pouvez protéger des serveurs physiques exécutant Windows et Linux, sur Azure ou sur un site secondaire. [Découvrez](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) la configuration requise du système d’exploitation. Que vous répliquiez des serveurs physiques sur Azure ou sur un site secondaire, les mêmes restrictions s’appliquent.

Notez que les serveurs physiques seront exécutés en tant que machines virtuelles dans Azure si votre serveur local tombe en panne. La restauration automatique sur un serveur physique local n'est actuellement pas prise en charge. Vous pouvez effectuer la restauration automatique uniquement sur une machine virtuelle exécutée sous VMware.

### Quelles machines virtuelles VMware puis-je protéger ?

Pour protéger les machines virtuelles VMware, vous avez besoin d’un hyperviseur vSphere et de machines virtuelles exécutant les outils VMware. Nous vous recommandons également de disposer d’un serveur VMware vCenter pour gérer les hyperviseurs. [Découvrez](site-recovery-vmware-to-azure-classic.md#before-you-start-deployment) la configuration requise pour les serveurs et les machines virtuelles VMware. Que vous répliquiez des machines virtuelles VMware vers Azure ou vers un site secondaire, les mêmes restrictions s’appliquent.

### Existe-t-il des conditions requises pour la réplication des machines virtuelles vers Azure ?

Les machines virtuelles que vous souhaitez répliquer vers Azure doivent se conformer aux [exigences d’Azure](site-recovery-best-practices.md#azure-virtual-machine-requirements).

### Puis-je répliquer des machines virtuelles de génération 2 Hyper-V vers Azure ?

Oui. Site Recovery convertit les machines virtuelles de la génération 2 à la génération 1 pendant le basculement. Au moment de la restauration automatique, la machine virtuelle est reconvertie en génération 2. [En savoir plus](https://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### Si je réplique vers Azure comment vais-je payer les machines virtuelles Azure ? 

Lors de la réplication normale, les données sont répliquées dans le stockage Azure géo-redondant. Ainsi, vous n’avez aucuns frais de gestion des machines virtuelles IaaS Azure à régler, ce qui est un atout majeur. Lorsque vous exécutez un basculement vers Azure, Site Recovery crée automatiquement les machines virtuelles IaaS Azure. Après cela, vous êtes facturé pour les ressources de calcul que vous utilisez dans Azure.

### Puis-je gérer la récupération d’urgence pour mes succursales avec Site Recovery ?

Oui. Lorsque vous utilisez Site Recovery pour coordonner la réplication et le basculement dans vos succursales, vous obtenez une orchestration unifiée et l’affichage de toutes les charges de travail de vos succursales dans un emplacement central. Vous pouvez facilement exécuter les basculements et gérer la récupération d’urgence de toutes les succursales à partir de votre siège social, sans vous rendre dans ces succursales.

### Existe-t-il un kit de développement logiciel (SDK) que je peux utiliser pour automatiser le flux de travail de Site Recovery ?

Oui. Vous pouvez automatiser les flux de travail Site Recovery à l’aide de l’API Rest, de PowerShell ou du Kit de développement logiciel (SDK) Azure. En savoir plus sur le [Déploiement de Site Recovery à l’aide de PowerShell et d’Azure Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md).


## Sécurité et conformité

### Les données de réplication sont-elles envoyées vers le service Site Recovery ?

Non, Site Recovery n’intercepte pas les données répliquées et n’a pas d’informations sur les éléments exécutés sur vos machines virtuelles ou serveurs physiques.

Les données de réplication sont échangées entre des hôtes Hyper-V, des hyperviseurs VMware ou des serveurs physiques locaux et le stockage Azure ou votre site secondaire. Site Recovery n’a aucun moyen d’intercepter ces données. Seules les métadonnées nécessaires pour coordonner la réplication et le basculement sont envoyées au service Site Recovery.

Le logiciel Site Recovery est certifié conforme à la norme ISO 27001:2005. Il devrait prochainement être compatible avec les normes HIPAA et DPA et avec les exigences du JAB FedRAMP.

### Pour la conformité, même les métadonnées de nos environnements locaux doivent rester dans la même région géographique. Site Recovery peut-il procéder ainsi ?

Oui. Quand vous créez un coffre Site Recovery dans une région, nous vérifions que toutes les métadonnées dont nous avons besoin pour activer et coordonner la réplication et le basculement restent au sein de cette région.

### Site Recovery chiffre-t-il la réplication ?
Pour la réplication de machines virtuelles et de serveurs physiques entre des sites locaux, le chiffrement en transit est pris en charge. Pour la réplication des machines virtuelles et des serveurs physiques vers Azure, le chiffrement en transit et le chiffrement au repos (dans Azure) sont tous deux pris en charge.




## Réplication et basculement

### Si je réplique vers Azure, de quel type de compte de stockage ai-je besoin ?

Vous avez besoin d’un compte de stockage avec [stockage géo-redondant standard](../storage/storage-introduction.md#replication-for-durability-and-high-availability). Premium Storage n’est pas actuellement pris en charge.

### À quelle fréquence puis-je répliquer les données ?
- **Hyper-V :** les machines virtuelles Hyper-V s’exécutant sur Windows Server 2012 R2 peuvent être répliquées toutes les 30 secondes, 5 minutes ou 15 minutes. Si vous avez configuré une réplication SAN, alors la réplication sera synchrone.
- **Serveurs VMware et physiques :** une fréquence de réplication n’est pas pertinente ici. La réplication sera continue. 

### Puis-je étendre la réplication d’un site de récupération existant vers un autre site de récupération ?
La réplication étendue ou chaînée n’est pas prise en charge. [Envoyez-nous des commentaires](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication/) sur cette fonctionnalité.


### Puis-je effectuer une réplication hors connexion la première fois que je réplique vers Azure ? 

Ceci n’est pas pris en charge. [Envoyez-nous des commentaires](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from/) sur cette fonctionnalité.


### Puis-je exclure des disques spécifiques de la réplication ?

Ceci n’est pas pris en charge. [Envoyez-nous des commentaires](https://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication/) sur cette fonctionnalité.

### Puis-je répliquer des machines virtuelles avec des disques dynamiques ?

Les disques dynamiques sont pris en charge lors de la réplication de machines virtuelles Hyper-V. Ils sont également pris en charge lors de la réplication des machines virtuelles et physiques VMware si vous utilisez le [déploiement amélioré](site-recovery-vmware-to-azure-classic.md). Notez que le disque du système d’exploitation doit être un disque de base.


### Comment accéder aux machines virtuelles Azure après le basculement vers Azure ? 

Vous pouvez accéder aux machines virtuelles Azure via une connexion Internet sécurisée, via un réseau privé virtuel de site à site ou via Azure ExpressRoute. Les communications via une connexion VPN s’effectuent vers des ports internes sur le réseau Azure sur lequel se trouve la machine virtuelle. Les communications via Internet sont mappées aux points de terminaison publics sur le service cloud Azure pour les machines virtuelles.

### Dans le cas de basculement vers Azure, comment Azure s’assure-t-il de la résilience de mes données ?

Azure est conçu pour la résilience. Site Recovery est déjà prévu pour assurer le basculement vers un centre de données Azure secondaire, dans le respect du contrat SLA Azure le cas échéant. Dans ce cas, nous nous assurons que vos métadonnées et vos coffres restent dans la même région géographique que vous avez choisie pour votre coffre.

### Si j’effectue une réplication entre deux sites, que se passe-t-il si mon site principal connaît une panne inattendue ?

Vous pouvez déclencher un basculement non planifié à partir du site secondaire. Site Recovery n’a pas besoin d’être connecté au site principal pour effectuer le basculement.


### Le basculement est-il automatique ?

Le basculement n’est pas automatique. Vous pouvez lancer les basculements en un seul clic dans le portail ou bien utiliser les [applets de commande Site Recovery PowerShell](https://msdn.microsoft.com/library/dn850420.aspx) pour déclencher un basculement. La restauration automatique est une série d’actions incluses dans le portail Site Recovery.

Pour automatiser le basculement, vous pouvez utiliser Orchestrator ou Operations Manager localement pour détecter une défaillance de machine virtuelle, puis déclencher le basculement à l’aide du Kit de développement logiciel (SDK).

### Puis-je limiter la bande passante allouée pour le trafic de réplication Hyper-V ?
- Si vous répliquez entre des machines virtuelles Hyper-V deux sites locaux, vous pouvez utiliser les composants de qualité de service (QoS) Windows. Voici un exemple de script : 

    	New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
    	gpupdate.exe /force

- Si vous répliquez des machines virtuelles Hyper-V vers Azure, vous pouvez configurer la limitation à l’aide de l’exemple d’applet de commande PowerShell suivant :

    	Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)

- [En savoir plus](https://support.microsoft.com/fr-FR/kb/3056159) sur la limitation du trafic Hyper-V.
- [En savoir plus](site-recovery-vmware-to-azure-classic.md#capacity-planning) sur la limitation de la réplication VMware vers Azure.



## Déploiement du fournisseur de services

### Site Recovery fonctionne-t-il pour les modèles d’infrastructure dédiée ou partagée ?
Oui, Site Recovery prend en charge les modèles d’infrastructure dédiée et partagée.

### L’identité de mon client est-elle communiquée au service Site Recovery ?
Non. En fait, vos clients n’ont pas besoin d’accéder au portail Site Recovery. Seul l’administrateur du fournisseur de services interagit avec le portail.


### Les données d’application de mes clients sont-elles diffusées sur Azure ?
Lors de la réplication entre des sites appartenant à un fournisseur de services, les données d’application n’accèdent jamais à Azure. Les données sont chiffrées en transit et répliquées directement entre les sites du fournisseur de services.

Si vous répliquez vers Azure, les données d’application sont envoyées vers le stockage Azure, mais pas vers le service Site Recovery. Les données sont chiffrées en transit et restent chiffrées dans Azure.

### Mes clients recevront-ils une facture pour les services Azure ?

Non. La relation de facturation d’Azure concerne directement le fournisseur de services. Les fournisseurs de services sont responsables de la création de factures spécifiques pour leurs locataires.

### Si je réplique vers Azure, faut-il exécuter à tout moment des machines virtuelles dans ce dernier ?

Non, les données sont répliquées vers un compte de stockage Azure géo-redondant dans votre abonnement. Lorsque vous effectuez un basculement de test (test de récupération d’urgence) ou un basculement réel, Site Recovery crée automatiquement les machines virtuelles dans votre abonnement.

### Assurez-vous l’isolation au niveau des clients lors de la réplication vers Azure ?

Oui.

### Quelles plates-formes prenez-vous en charge, actuellement ?

Nous prenons en charge Azure Pack, le système Cloud Platform, ainsi que les déploiements de System Center (versions 2012 et supérieures). Pour en savoir plus sur l’intégration d’Azure Pack, consultez [Configurer la protection des machines virtuelles](https://technet.microsoft.com/library/dn850370.aspx).

### Prenez-vous en charge les déploiements uniques de serveurs VMM et Azure Pack ?

Oui, vous pouvez répliquer les machines virtuelles Hyper-V et Azure, ou entre des sites du fournisseur de services. Notez que si vous répliquez entre des sites du fournisseur de services, l’intégration de runbooks Azure n’est pas disponible.


## Étapes suivantes

- Lisez la [Vue d’ensemble de Microsoft Azure Site Recovery](site-recovery-overview.md)
- En savoir plus sur l’[architecture de Site Recovery](site-recovery-components.md)  

<!---HONumber=AcomDC_0316_2016-->