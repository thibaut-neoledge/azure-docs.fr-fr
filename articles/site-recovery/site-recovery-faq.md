<properties 
	pageTitle="Azure Site Recovery : Forum Aux Questions | Microsoft Azure" 
	description="Cet article traite des questions fréquemment posées sur Azure Site Recovery." 
	services="site-recovery" 
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor=""/>

<tags 
	ms.service="get-started-article"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na" 
	ms.workload="storage-backup-recovery"
	ms.date="07/12/2016" 
	ms.author="raynew"/>


# Azure Site Recovery : Forum Aux Questions (FAQ)
## À propos de cet article

Cet article contient les questions fréquemment posées sur Microsoft Azure Site Recovery. Si, après avoir lu cet article, vous avez des questions, posez-les sur le [forum Azure Recovery Services](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## Généralités

###À quoi sert Site Recovery ?

Site Recovery contribue à mettre en œuvre la stratégie de continuité d’activité et de récupération d’urgence (BCDR) de votre entreprise en coordonnant et en automatisant la réplication à partir de machines virtuelles et de serveurs physiques locaux sur Azure ou sur un centre de données secondaire. [En savoir plus](site-recovery-overview.md).


### Que peut protéger Site Recovery ?

- **Machines virtuelles Hyper-V** : Site Recovery peut protéger toute charge de travail en cours d’exécution sur une machine virtuelle Hyper-V.
- **Serveurs physiques** : Site Recovery peut protéger les serveurs physiques exécutant Windows ou Linux.
- **Machines virtuelles VMware** : Site Recovery peut protéger toute charge de travail en cours d’exécution dans une machine virtuelle VMware.

### Site Recovery prend-il en charge le modèle Azure Resource Manager ? 

Site Recovery est disponible non seulement dans le portail Azure Classic, mais également dans le portail Azure, où il prend en charge le modèle Resource Manager. Pour la plupart des scénarios de déploiement, l’utilisation de Site Recovery dans le portail Azure rationalise l’expérience de déploiement et vous permet de répliquer des machines virtuelles et des serveurs physiques aussi bien dans un stockage classique que dans un stockage Resource Manager. Voici les déploiements pris en charge :

- [Réplication de machines virtuelles VMware ou de serveurs physiques sur Azure dans le portail Azure](site-recovery-vmware-to-azure.md)
- [Réplication de machines virtuelles Hyper-V gérées dans des clouds VMM sur Azure dans le portail Azure](site-recovery-vmm-to-azure.md)
- [Réplication de machines virtuelles Hyper-V (sans VMM) sur Azure dans le portail Azure](site-recovery-hyper-v-site-to-azure.md)
- [Réplication de machines virtuelles Hyper-V gérées dans des clouds VMM sur un site secondaire dans le portail Azure](site-recovery-vmm-to-vmm.md)


### Quelle est la configuration requise dans Hyper-V pour orchestrer la réplication avec Site Recovery ? 

Ce dont vous avez besoin pour le serveur hôte Hyper-V dépend du scénario de déploiement. Découvrez les prérequis dans :

- [Réplication de machines virtuelles Hyper-V (sans VMM) sur Azure](site-recovery-hyper-v-site-to-azure.md#before-you-start)
- [Réplication de machines virtuelles Hyper-V (avec VMM) sur Azure](site-recovery-vmm-to-azure.md#before-you-start)
- [Réplication de machines virtuelles Hyper-V sur un centre de données secondaire](site-recovery-vmm-to-vmm.md#before-you-start)

- Si vous répliquez sur un centre de données secondaire, consultez l’article [Systèmes d’exploitation invités pris en charge pour les ordinateurs virtuels Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
- Si vous effectuez une réplication vers Azure, Site Recovery prend en charge tous les systèmes d’exploitation invités [pris en charge par Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### Puis-je protéger des machines virtuelles lorsque Hyper-V est en cours d’exécution sur un système d’exploitation client ?

Non, les machines virtuelles doivent se trouver sur un serveur hôte Hyper-V s’exécutant sur une machine serveur Windows prise en charge. Si vous devez protéger un ordinateur client, vous pouvez le répliquer en tant que machine physique [vers Azure](site-recovery-vmware-to-azure.md) ou vers un [centre de données secondaire](site-recovery-vmware-to-vmware.md).


### Quelles charges de travail puis-je protéger avec Site Recovery ?

Vous pouvez utiliser Site Recovery pour protéger la plupart des charges de travail en cours d’exécution sur une machine virtuelle ou un serveur physique pris(e) en charge. Site Recovery assure la prise en charge de la réplication compatible avec les applications afin qu’elles puissent être récupérées dans un état intelligent. Site Recovery s’intègre aux applications Microsoft, notamment à SharePoint, Exchange, Dynamics, SQL Server et Active Directory, et fonctionne en étroite collaboration avec les principaux fournisseurs, notamment Oracle, SAP, IBM et Red Hat. [En savoir plus](site-recovery-workload.md) sur la protection des charges de travail.


### Les hôtes Hyper-V doivent-ils résider dans des clouds VMM ? 

Si vous souhaitez effectuer une réplication vers un centre de données secondaire, les machines virtuelles Hyper-V doivent résider sur des serveurs hôtes Hyper-V situés dans un cloud VMM. Si vous souhaitez procéder à une réplication vers Azure, vous pouvez répliquer des machines virtuelles sur des serveurs hôtes Hyper-V avec ou sans clouds VMM. [En savoir plus](site-recovery-hyper-v-site-to-azure.md)

### Puis-je déployer Site Recovery avec VMM si je ne dispose que d’un seul serveur VMM ? 

Oui. Vous pouvez répliquer des machines virtuelles Hyper-V dans le cloud VMM vers Azure, ou répliquer entre des clouds VMM sur le même serveur. Pour la réplication entre sites locaux, nous recommandons de disposer d’un serveur VMM dans les sites principaux et secondaires. [En savoir plus](site-recovery-single-vmm.md)

### Quels serveurs physiques puis-je protéger ?

Vous pouvez répliquer des serveurs physiques exécutant Windows et Linux sur Azure ou sur un site secondaire. [Découvrez](site-recovery-vmware-to-azure.md#protected-machine-prerequisites) la configuration requise du système d’exploitation. Que vous répliquiez des serveurs physiques sur Azure ou sur un site secondaire, les mêmes exigences s’appliquent.

Notez que les serveurs physiques seront exécutés en tant que machines virtuelles dans Azure si votre serveur local tombe en panne. La restauration automatique sur un serveur physique local n’est actuellement pas prise en charge, mais vous pouvez l’effectuer sur une machine virtuelle s’exécutant sur Hyper-V ou VMware.


### Quelles machines virtuelles VMware puis-je protéger ?

Pour protéger les machines virtuelles VMware, vous avez besoin d’un hyperviseur vSphere et de machines virtuelles exécutant les outils VMware. Nous vous recommandons également de disposer d’un serveur VMware vCenter pour gérer les hyperviseurs. [En savoir plus](site-recovery-vmware-to-azure.md#protected-machine-prerequisites) sur la configuration requise exacte pour la réplication des serveurs VMware et des machines virtuelles vers Azure ou sur un site secondaire.

### Puis-je gérer la récupération d’urgence pour mes succursales avec Site Recovery ?

Oui. Lorsque vous utilisez Site Recovery pour coordonner la réplication et le basculement dans vos succursales, vous obtenez une orchestration unifiée et l’affichage de toutes les charges de travail de vos succursales dans un emplacement central. Vous pouvez facilement exécuter les basculements et gérer la récupération d’urgence de toutes les succursales à partir de votre siège social, sans vous rendre dans ces succursales.

## Sécurité

### Les données de réplication sont-elles envoyées vers le service Site Recovery ?

Non, Site Recovery n’intercepte pas les données répliquées et n’a pas d’informations sur les éléments exécutés sur vos machines virtuelles ou serveurs physiques. Les données de réplication sont échangées entre des hôtes Hyper-V, des hyperviseurs VMware ou des serveurs physiques locaux et le stockage Azure ou votre site secondaire. Site Recovery n’a aucun moyen d’intercepter ces données. Seules les métadonnées nécessaires pour coordonner la réplication et le basculement sont envoyées au service Site Recovery.

Le logiciel Site Recovery est certifié conforme aux normes ISO 27001:2013, 27018, HIPAA et DPA. Il fait actuellement l’objet d’une évaluation de conformité aux exigences SOC2 et JAB FedRAMP.


### Pour la conformité, les données de nos environnements locaux doivent rester dans la même région géographique. Cela inclut les métadonnées. Site Recovery peut-il nous aider ?

Oui. Quand vous créez un coffre Site Recovery dans une région, nous vérifions que toutes les métadonnées dont nous avons besoin pour activer et coordonner la réplication et le basculement restent au sein de cette région.

### Site Recovery chiffre-t-il la réplication ?

Pour la réplication de machines virtuelles et de serveurs physiques entre des sites locaux, le chiffrement en transit est pris en charge. Pour la réplication de machines virtuelles et de serveurs physiques vers Azure, le chiffrement en transit et le chiffrement au repos (dans Azure) sont tous deux pris en charge.


## Réplication


### Existe-t-il des conditions requises pour la réplication des machines virtuelles vers Azure ?

Les machines virtuelles que vous souhaitez répliquer vers Azure doivent se conformer aux [exigences d’Azure](site-recovery-best-practices.md#virtual-machines).

### Puis-je répliquer des machines virtuelles de génération 2 Hyper-V vers Azure ?

Oui. Site Recovery les convertit de la génération 2 à la génération 1 pendant le basculement. Au moment de la restauration automatique, la machine est reconvertie en génération 2. [En savoir plus](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### Si je réplique vers Azure comment vais-je payer les machines virtuelles Azure ? 

Lors d’une réplication normale, les données sont répliquées dans le stockage Azure géo-redondant. Ainsi, vous n’avez aucuns frais de gestion des machines virtuelles IaaS Azure à régler, ce qui est un atout majeur. Lorsque vous exécutez un basculement vers Azure, Site Recovery crée automatiquement les machines virtuelles IaaS Azure. Après cela, vous êtes facturé pour les ressources de calcul que vous utilisez dans Azure.


### Existe-t-il un kit de développement logiciel (SDK) que je peux utiliser pour automatiser le flux de travail du logiciel ASR ?

Oui. Vous pouvez automatiser les flux de travail Site Recovery à l’aide de l’API Rest, de PowerShell ou du Kit de développement logiciel (SDK) Azure. Scénarios actuellement pris en charge pour le déploiement de Site Recovery à l’aide de PowerShell :

- [Réplication vers Azure de machines virtuelles Hyper-V hébergées dans des clouds VMM à l’aide de PowerShell et du déploiement classique](site-recovery-deploy-with-powershell.md)
- [Réplication vers Azure de machines virtuelles Hyper-V hébergées dans des clouds VMM à l’aide de PowerShell et d’Azure Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
- [Réplication vers Azure de machines virtuelles Hyper-V (sans VMM) à l’aide de PowerShell et du déploiement classique](site-recovery-hyper-v-site-to-azure-classic.md)
- [Réplication vers Azure de machines virtuelles Hyper-V (sans VMM) à l’aide de PowerShell et d’Azure Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)


### Si je réplique vers Azure, de quel type de compte de stockage ai-je besoin ?

- **Portail Azure Classic** : si vous déployez Site Recovery dans le portail Azure Classic, vous avez besoin d’un [compte de stockage géo-redondant standard](../storage/storage-redundancy.md#geo-redundant-storage). Premium Storage n’est pas actuellement pris en charge. Ce compte doit se trouver dans la même région que le coffre Site Recovery.

- **Portail Azure** : si vous déployez Site Recovery dans le portail Azure, vous avez besoin d’un compte de stockage LRS ou GRS. Nous vous recommandons d’utiliser un compte GRS, afin que les données soient résilientes si une panne se produit au niveau régional, ou si la région principale ne peut pas être récupérée. Ce compte doit se trouver dans la même région que le coffre Recovery Services. Premium Storage est actuellement pris en charge uniquement pour la réplication de machines virtuelles VMware ou de serveurs physiques.

### À quelle fréquence puis-je répliquer les données ?

- **Hyper-V :** les machines virtuelles Hyper-V peuvent être répliquées toutes les 30 secondes, 5 minutes ou 15 minutes. Si vous avez configuré une réplication SAN, la réplication est alors synchrone.
- **Serveurs VMware et physiques :** une fréquence de réplication n’est pas pertinente ici. La réplication est continue.

### Puis-je étendre la réplication depuis un site de récupération existant à un site tiers ?

La réplication étendue ou chaînée n’est pas prise en charge. Demandez cette fonctionnalité dans le [forum de commentaires](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).


### Puis-je effectuer une réplication hors connexion la première fois que je réplique vers Azure ? 

Ceci n’est pas pris en charge. Demandez cette fonctionnalité dans le [forum de commentaires](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).


### Puis-je exclure des disques spécifiques de la réplication ?

Cette fonctionnalité est prise en charge dans le cadre d’une [réplication de machines virtuelles VMware et de serveurs physiques](site-recovery-vmware-to-azure.md#exclude-disks-from-replication) vers Azure à l’aide du portail Azure.


### Puis-je répliquer des machines virtuelles avec des disques dynamiques ?

Les disques dynamiques sont pris en charge lors de la réplication de machines virtuelles Hyper-V. Ils sont également pris en charge lors de la réplication de machines virtuelles VMware et de serveurs physiques vers Azure si vous utilisez le [portail Azure](site-recovery-vmware-to-azure.md) ou le [portail Azure Classic avec déploiement amélioré](site-recovery-vmware-to-azure-classic.md). Notez que le disque du système d’exploitation doit être un disque de base.

### Puis-je limiter la bande passante allouée pour le trafic de réplication Hyper-V ?

Oui. Pour plus d’informations sur la limitation de bande passante, consultez les articles de déploiement suivants :

- [Planification de la capacité pour la réplication de machines virtuelles VMware et de serveurs physiques](site-recovery-vmware-to-azure.md#step-5-capacity-planning)
- [Planification de la capacité pour la réplication de machines virtuelles Hyper-V dans des clouds VMM](site-recovery-vmm-to-azure.md#step-5-capacity-planning)
- [Planification de la capacité pour la réplication de machines virtuelles Hyper-V sans VMM](site-recovery-hyper-v-site-to-azure.md#step-5-capacity-planning)

## Basculement


### Si j’effectue le basculement vers Azure, comment accéder aux machines virtuelles Azure après le basculement ? 

Vous pouvez accéder aux machines virtuelles Azure via une connexion Internet sécurisée, via un réseau privé virtuel de site à site ou via Azure ExpressRoute. Vous devez préparer un certain nombre de choses afin de vous connecter. En savoir plus :

- [Connexion à des machines virtuelles Azure après un basculement de machines virtuelles VMware ou de serveurs physiques](hsite-recovery-vmware-to-azure.md#step-7-test-the-deployment)
- [Connexion à des machines virtuelles Azure après un basculement de machines virtuelles Hyper-V dans des clouds VMM](site-recovery-vmm-to-azure.md#step-7-test-your-deployment)
- [Connexion à des machines virtuelles Azure après un basculement de machines virtuelles Hyper-V sans VMM](site-recovery-hyper-v-site-to-azure.md#step-7-test-the-deployment)


### Si j’effectue le basculement vers Azure, comment Azure s’assure-t-il de la résilience de mes données ?

Azure est conçu pour la résilience. Site Recovery est déjà prévu pour assurer le basculement vers un centre de données Azure secondaire, dans le respect du contrat SLA Azure le cas échéant. Dans ce cas, nous nous assurons que vos métadonnées et vos coffres restent dans la même région géographique que vous avez choisie pour votre coffre.

### Si j’effectue une réplication entre deux centres de données, que se passe-t-il si mon centre de données principal connaît une panne inattendue ?

Vous pouvez déclencher un basculement non planifié à partir du site secondaire. Site Recovery n’a pas besoin d’être connecté au site principal pour effectuer le basculement.

### Le basculement est-il automatique ?

Le basculement n’est pas automatique. Vous lancez les basculements d’un seul clic dans le portail, ou vous pouvez utiliser [Site Recovery PowerShell](https://msdn.microsoft.com/library/dn850420.aspx) pour déclencher un basculement. La restauration automatique est également une action simple dans le portail Site Recovery.

Pour automatiser les processus, vous pouvez utiliser Orchestrator ou Operations Manager localement pour détecter une défaillance de machine virtuelle, puis déclencher le basculement à l’aide du Kit de développement logiciel (SDK).

- [Découvrez plus d’informations](site-recovery-create-recovery-plans.md) sur les plans de récupération.
- [En savoir plus](site-recovery-failover.md) sur le basculement.
- [En savoir plus](site-recovery-failback-azure-to-vmware.md) sur la restauration automatique de serveurs physiques et de machines virtuelles VMware


## Fournisseurs de services


### Je suis un fournisseur de services. Site Recovery fonctionne-t-il pour les modèles d’infrastructure dédiée ou partagée ?
Oui, Site Recovery prend en charge les modèles d’infrastructure dédiée et partagée.

### Pour un fournisseur de services, l’identité de mon locataire est-elle communiquée au service Site Recovery ?
Non. L’identité du locataire reste anonyme. Vos locataires n’ont pas besoin d’accéder au portail Site Recovery. Seul l’administrateur du fournisseur de services interagit avec le portail.


### Les données d’application de mes clients sont-elles diffusées sur Azure ?
Lors de la réplication entre des sites appartenant à un fournisseur de services, les données d’application n’accèdent jamais à Azure. Les données sont chiffrées en transit et répliquées directement entre les sites du fournisseur de services.

Si vous répliquez vers Azure, les données d’application sont envoyées vers le stockage Azure, mais pas vers le service Site Recovery. Les données sont chiffrées en transit et restent chiffrées dans Azure.


### Mes clients recevront-ils une facture pour les services Azure ?

Non. La relation de facturation d’Azure concerne directement le fournisseur de services. Les fournisseurs de services sont responsables de la création de factures spécifiques pour leurs locataires.

### Si je réplique vers Azure, faut-il exécuter à tout moment des machines virtuelles dans ce dernier ?

Non, les données sont répliquées vers un compte de stockage Azure de votre abonnement. Lorsque vous effectuez un basculement de test (test de récupération d’urgence) ou un basculement réel, Site Recovery crée automatiquement les machines virtuelles dans votre abonnement.

### Assurez-vous l’isolation au niveau des clients lors de la réplication vers Azure ?

Oui.

### Quelles plates-formes prenez-vous en charge, actuellement ?

Nous prenons en charge Azure Pack et le système Cloud Platform, ainsi que les déploiements basés sur System Center (2012 et versions supérieures). [En savoir plus](https://technet.microsoft.com/library/dn850370.aspx) sur l’intégration d’Azure Pack et de Site Recovery.

### Prenez-vous en charge les déploiements uniques de serveurs VMM et Azure Pack ?

Oui, vous pouvez répliquer des machines virtuelles Hyper-V vers Azure, ou répliquer entre des sites du fournisseur de services. Notez que si vous répliquez entre des sites du fournisseur de services, l’intégration de runbooks Azure n’est pas disponible.


## Étapes suivantes

- Lisez la [Vue d’ensemble de Microsoft Azure Site Recovery](site-recovery-overview.md)
- En savoir plus sur l’[architecture de Site Recovery](site-recovery-components.md)

 

<!---HONumber=AcomDC_0720_2016-->