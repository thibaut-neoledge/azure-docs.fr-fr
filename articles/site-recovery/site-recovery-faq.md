---
title: "Azure Site Recovery : Forum Aux Questions | Microsoft Docs"
description: "Cet article traite des questions fréquemment posées sur Azure Site Recovery."
services: site-recovery
documentationcenter: 
author: rayne-wiselman
manager: cfreeman
editor: 
ms.assetid: 5cdc4bcd-b4fe-48c7-8be1-1db39bd9c078
ms.service: get-started-article
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 02/21/2017
ms.author: raynew
translationtype: Human Translation
ms.sourcegitcommit: aebd94edbad210f15bce5b39ebf73269ec67a209
ms.openlocfilehash: 3bcba1123d3ec00c760d022d200609bf28c22362
ms.lasthandoff: 03/01/2017


---
# <a name="azure-site-recovery-frequently-asked-questions-faq"></a>Azure Site Recovery : Forum Aux Questions (FAQ)
Cet article contient les questions fréquemment posées sur Microsoft Azure Site Recovery. Si, après avoir lu cet article, vous avez des questions, posez-les sur le [forum Azure Recovery Services](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).

## <a name="general"></a>Généralités
### <a name="what-does-site-recovery-do"></a>À quoi sert Site Recovery ?
Site Recovery contribue à mettre en œuvre la stratégie de continuité d’activité et de récupération d’urgence (BCDR) de votre entreprise en coordonnant et en automatisant la réplication à partir de machines virtuelles et de serveurs physiques locaux sur Azure ou sur un centre de données secondaire. [Plus d’informations](site-recovery-overview.md)

### <a name="what-can-site-recovery-protect"></a>Que peut protéger Site Recovery ?
* **Machines virtuelles Hyper-V**: Site Recovery peut protéger toute charge de travail en cours d’exécution sur une machine virtuelle Hyper-V.
* **Serveurs physiques**: Site Recovery peut protéger les serveurs physiques exécutant Windows ou Linux.
* **Machines virtuelles VMware**: Site Recovery peut protéger toute charge de travail en cours d’exécution dans une machine virtuelle VMware.

### <a name="does-site-recovery-support-the-azure-resource-manager-model"></a>Site Recovery prend-il en charge le modèle Azure Resource Manager ?
Site Recovery est disponible non seulement dans le portail Azure Classic, mais également dans le portail Azure, où il prend en charge le modèle Resource Manager. Pour la plupart des scénarios de déploiement, l’utilisation de Site Recovery dans le portail Azure rationalise l’expérience de déploiement et vous permet de répliquer des machines virtuelles et des serveurs physiques aussi bien dans un stockage classique que dans un stockage Resource Manager. Voici les déploiements pris en charge :

* [Réplication de machines virtuelles VMware ou de serveurs physiques sur Azure dans le portail Azure](site-recovery-vmware-to-azure.md)
* [Réplication de machines virtuelles Hyper-V gérées dans des clouds VMM sur Azure dans le portail Azure](site-recovery-vmm-to-azure.md)
* [Réplication de machines virtuelles Hyper-V (sans VMM) sur Azure dans le portail Azure](site-recovery-hyper-v-site-to-azure.md)
* [Réplication de machines virtuelles Hyper-V gérées dans des clouds VMM sur un site secondaire dans le portail Azure](site-recovery-vmm-to-vmm.md)

### <a name="what-do-i-need-in-hyper-v-to-orchestrate-replication-with-site-recovery"></a>Quelle est la configuration requise dans Hyper-V pour orchestrer la réplication avec Site Recovery ?
Ce dont vous avez besoin pour le serveur hôte Hyper-V dépend du scénario de déploiement. Découvrez les prérequis dans :

* [Réplication de machines virtuelles Hyper-V (sans VMM) sur Azure](site-recovery-hyper-v-site-to-azure.md)
* [Réplication de machines virtuelles Hyper-V (avec VMM) sur Azure](site-recovery-vmm-to-azure.md)
* [Réplication de machines virtuelles Hyper-V sur un centre de données secondaire](site-recovery-vmm-to-vmm.md)
* Si vous répliquez sur un centre de données secondaire, consultez l’article [Systèmes d’exploitation invités pris en charge pour les ordinateurs virtuels Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
* Si vous effectuez une réplication vers Azure, Site Recovery prend en charge tous les systèmes d’exploitation invités [pris en charge par Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### <a name="can-i-protect-vms-when-hyper-v-is-running-on-a-client-operating-system"></a>Puis-je protéger des machines virtuelles lorsque Hyper-V est en cours d’exécution sur un système d’exploitation client ?
Non, les machines virtuelles doivent se trouver sur un serveur hôte Hyper-V s’exécutant sur une machine serveur Windows prise en charge. Si vous devez protéger un ordinateur client, vous pouvez le répliquer en tant que machine physique [vers Azure](site-recovery-vmware-to-azure.md) ou vers un [centre de données secondaire](site-recovery-vmware-to-vmware.md).

### <a name="what-workloads-can-i-protect-with-site-recovery"></a>Quelles charges de travail puis-je protéger avec Site Recovery ?
Vous pouvez utiliser Site Recovery pour protéger la plupart des charges de travail en cours d’exécution sur une machine virtuelle ou un serveur physique pris(e) en charge. Site Recovery assure la prise en charge de la réplication compatible avec les applications afin qu’elles puissent être récupérées dans un état intelligent. Site Recovery s’intègre aux applications Microsoft, notamment à SharePoint, Exchange, Dynamics, SQL Server et Active Directory, et fonctionne en étroite collaboration avec les principaux fournisseurs, notamment Oracle, SAP, IBM et Red Hat. [En savoir plus](site-recovery-workload.md) sur la protection des charges de travail.

### <a name="do-hyper-v-hosts-need-to-be-in-vmm-clouds"></a>Les hôtes Hyper-V doivent-ils résider dans des clouds VMM ?
Si vous souhaitez effectuer une réplication vers un centre de données secondaire, les machines virtuelles Hyper-V doivent résider sur des serveurs hôtes Hyper-V situés dans un cloud VMM. Si vous souhaitez procéder à une réplication vers Azure, vous pouvez répliquer des machines virtuelles sur des serveurs hôtes Hyper-V avec ou sans clouds VMM. [En savoir plus](site-recovery-hyper-v-site-to-azure.md).

### <a name="can-i-deploy-site-recovery-with-vmm-if-i-only-have-one-vmm-server"></a>Puis-je déployer Site Recovery avec VMM si je ne dispose que d’un seul serveur VMM ?

Oui. Vous pouvez répliquer des machines virtuelles Hyper-V dans le cloud VMM vers Azure, ou répliquer entre des clouds VMM sur le même serveur. Pour la réplication entre sites locaux, nous recommandons de disposer d’un serveur VMM dans les sites principaux et secondaires.  

### <a name="what-physical-servers-can-i-protect"></a>Quels serveurs physiques puis-je protéger ?
Vous pouvez répliquer des serveurs physiques exécutant Windows et Linux sur Azure ou sur un site secondaire. [Découvrez](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) la configuration requise du système d’exploitation.  Que vous répliquiez des serveurs physiques sur Azure ou sur un site secondaire, les mêmes exigences s’appliquent.


Notez que les serveurs physiques seront exécutés en tant que machines virtuelles dans Azure si votre serveur local tombe en panne. La restauration automatique sur un serveur physique local n’est actuellement pas prise en charge, mais vous pouvez l’effectuer sur une machine virtuelle s’exécutant sur Hyper-V ou VMware.

### <a name="what-vmware-vms-can-i-protect"></a>Quelles machines virtuelles VMware puis-je protéger ?

Pour protéger les machines virtuelles VMware, vous avez besoin d’un hyperviseur vSphere et de machines virtuelles exécutant les outils VMware. Nous vous recommandons également de disposer d’un serveur VMware vCenter pour gérer les hyperviseurs. [En savoir plus](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements) sur la configuration requise exacte pour la réplication des serveurs VMware et des machines virtuelles vers Azure ou sur un site secondaire.


### <a name="can-i-manage-disaster-recovery-for-my-branch-offices-with-site-recovery"></a>Puis-je gérer la récupération d’urgence pour mes succursales avec Site Recovery ?
Oui. Lorsque vous utilisez Site Recovery pour coordonner la réplication et le basculement dans vos succursales, vous obtenez une orchestration unifiée et l’affichage de toutes les charges de travail de vos succursales dans un emplacement central. Vous pouvez facilement exécuter les basculements et gérer la récupération d’urgence de toutes les succursales à partir de votre siège social, sans vous rendre dans ces succursales.

## <a name="pricing"></a>Tarification

### <a name="what-charges-do-i-incur-while-using-azure-site-recovery"></a>Quels frais sont facturés lors de l’utilisation d’Azure Site Recovery ?
Lorsque vous utilisez Site Recovery, des frais vous sont facturés pour la licence Site Recovery, le stockage Azure, les transactions de stockage et le transfert de données sortant. [En savoir plus](https://azure.microsoft.com/pricing/details/site-recovery).

La licence Site Recovery est destinée à une instance protégée, l’instance étant une machine virtuelle ou un serveur physique.

- Si un disque de machine virtuelle est répliqué vers un compte de stockage standard, les frais de stockage Azure concernent la consommation de stockage. Par exemple, si le disque source présente une taille de 1 To et 400 Go sont utilisés, Site Recovery crée un disque dur virtuel (VHD) de 1 To dans Azure, mais le stockage est facturé pour 400 Go (plus la quantité d’espace de stockage utilisée pour les journaux de réplication).
- Si un disque de machine virtuelle est répliqué vers un compte de stockage Premium, les frais de stockage Azure concernent la taille de stockage provisionnée, arrondie selon l’option de disque de stockage Premium la plus proche. Par exemple, si le disque source présente une taille de 50 Go, Site Recovery crée un disque de 50 Go dans Azure et Azure mappe celui-ci au disque de stockage Premium le plus proche (P10).  Les coûts sont calculés selon le disque P10 et non selon le disque de 50 Go.  [Plus d’informations](https://aka.ms/premium-storage-pricing)  Si vous utilisez le stockage Premium, un compte de stockage standard est également requis pour la journalisation de réplication, et la quantité d’espace de stockage standard utilisée pour ces journaux est facturée en plus.

Des coûts sont aussi facturés pendant le test de basculement, les coûts de la machine virtuelle, du stockage, de la sortie et des transactions de stockage s’appliquant.



## <a name="security"></a>Sécurité
### <a name="is-replication-data-sent-to-the-site-recovery-service"></a>Les données de réplication sont-elles envoyées vers le service Site Recovery ?
Non, Site Recovery n’intercepte pas les données répliquées et n’a pas d’informations sur les éléments exécutés sur vos machines virtuelles ou serveurs physiques.
Les données de réplication sont échangées entre des hôtes Hyper-V, des hyperviseurs VMware ou des serveurs physiques locaux et le stockage Azure ou votre site secondaire. Site Recovery n’a aucun moyen d’intercepter ces données. Seules les métadonnées nécessaires pour coordonner la réplication et le basculement sont envoyées au service Site Recovery.  

Le logiciel Site Recovery est certifié conforme aux normes ISO 27001:2013, 27018, HIPAA et DPA. Il fait actuellement l’objet d’une évaluation de conformité aux exigences SOC2 et JAB FedRAMP.

### <a name="for-compliance-reasons-even-our-on-premises-metadata-must-remain-within-the-same-geographic-region-can-site-recovery-help-us"></a>Pour des raisons de conformité, même nos métadonnées locales doivent rester dans la même région géographique. Site Recovery peut-il nous aider ?
Oui. Quand vous créez un coffre Site Recovery dans une région, nous vérifions que toutes les métadonnées dont nous avons besoin pour activer et coordonner la réplication et le basculement restent au sein de cette région.

### <a name="does-site-recovery-encrypt-replication"></a>Site Recovery chiffre-t-il la réplication ?
Pour la réplication de machines virtuelles et de serveurs physiques entre des sites locaux, le chiffrement en transit est pris en charge. Pour la réplication de machines virtuelles et de serveurs physiques vers Azure, le chiffrement en transit et le chiffrement au repos (dans Azure) sont tous deux pris en charge.

## <a name="replication"></a>Réplication

### <a name="can-i-replicate-over-a-site-to-site-vpn-to-azure"></a>Puis-je répliquer un VPN de site à site vers Azure ?
Azure Site Recovery réplique des données vers un compte de stockage Azure, via un point de terminaison public. La réplication ne s’effectue pas via un réseau VPN de site à site. Vous pouvez créer un réseau VPN de site à site, avec un réseau virtuel Azure. Cela n’interfère pas avec la réplication Site Recovery.

### <a name="can-i-use-expressroute-to-replicate-virtual-machines-to-azure"></a>Puis-je utiliser ExpressRoute pour répliquer des machines virtuelles vers Azure ?
Oui, vous pouvez utiliser ExpressRoute pour répliquer des machines virtuelles vers Azure. Azure Site Recovery réplique des données vers un compte de stockage Azure via un point de terminaison public. Vous devez configurer l’[homologation publique](../expressroute/expressroute-circuit-peerings.md#public-peering) afin d’utiliser ExpressRoute pour la réplication Site Recovery. Une fois que les machines virtuelles ont été basculées vers un réseau virtuel Azure, vous pouvez y accéder à l’aide de la configuration de [l’homologation privée](../expressroute/expressroute-circuit-peerings.md#private-peering) avec le réseau virtuel Azure.

### <a name="are-there-any-prerequisites-for-replicating-virtual-machines-to-azure"></a>Existe-t-il des conditions requises pour la réplication des machines virtuelles vers Azure ?
Les machines virtuelles que vous souhaitez répliquer vers Azure doivent se conformer aux [exigences d’Azure](site-recovery-support-matrix-to-azure.md#failed-over-azure-vm-requirements).

### <a name="can-i-replicate-hyper-v-generation-2-virtual-machines-to-azure"></a>Puis-je répliquer des machines virtuelles de génération 2 Hyper-V vers Azure ?
Oui. Site Recovery les convertit de la génération 2 à la génération 1 pendant le basculement. Au moment de la restauration automatique, la machine est reconvertie en génération 2. [En savoir plus](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### <a name="if-i-replicate-to-azure-how-do-i-pay-for-azure-vms"></a>Si je réplique vers Azure comment vais-je payer les machines virtuelles Azure ?
Lors de la réplication normale, les données sont répliquées dans le stockage Azure géo-redondant. Ainsi, vous n’avez aucuns frais de gestion des machines virtuelles IaaS Azure à régler, ce qui est un atout majeur. Lorsque vous exécutez un basculement vers Azure, Site Recovery crée automatiquement les machines virtuelles IaaS Azure. Après cela, vous êtes facturé pour les ressources de calcul que vous utilisez dans Azure.

### <a name="can-i-automate-site-recovery-scenarios-with-an-sdk"></a>Puis-je automatiser des scénarios Site Recovery avec un Kit de développement logiciel (SDK) ?
Oui. Vous pouvez automatiser les flux de travail Site Recovery à l’aide de l’API Rest, de PowerShell ou du Kit de développement logiciel (SDK) Azure. Scénarios actuellement pris en charge pour le déploiement de Site Recovery à l’aide de PowerShell :

* [Réplication vers Azure de machines virtuelles Hyper-V hébergées dans des clouds VMM à l’aide de PowerShell et d’Azure Resource Manager](site-recovery-vmm-to-azure-powershell-resource-manager.md)
* [Réplication vers Azure de machines virtuelles Hyper-V (sans VMM) à l’aide de PowerShell et d’Azure Resource Manager](site-recovery-deploy-with-powershell-resource-manager.md)

### <a name="if-i-replicate-to-azure-what-kind-of-storage-account-do-i-need"></a>Si je réplique vers Azure, de quel type de compte de stockage ai-je besoin ?
* **Portail Azure Classic**: si vous déployez Site Recovery dans le portail Azure Classic, vous avez besoin d’un [compte de stockage géoredondant standard](../storage/storage-redundancy.md#geo-redundant-storage). Premium Storage n’est pas actuellement pris en charge. Ce compte doit se trouver dans la même région que le coffre Site Recovery.
* **Portail Azure**: si vous déployez Site Recovery dans le portail Azure, vous avez besoin d’un compte de stockage LRS ou GRS. Nous vous recommandons d’utiliser un compte GRS, afin que les données soient résilientes si une panne se produit au niveau régional, ou si la région principale ne peut pas être récupérée. Ce compte doit se trouver dans la même région que le coffre Recovery Services. Le Stockage Premium est maintenant pris en charge pour les machines virtuelles VMware, les machines virtuelles Hyper-V et la réplication de serveurs physiques lorsque vous déployez Site Recovery dans le portail Azure.

### <a name="how-often-can-i-replicate-data"></a>À quelle fréquence puis-je répliquer les données ?
* **Hyper-V :** les machines virtuelles Hyper-V peuvent être répliquées toutes les 30 secondes (sauf pour le Stockage Premium), 5 minutes ou 15 minutes. Si vous avez configuré une réplication SAN, la réplication est alors synchrone.
* **Serveurs VMware et physiques :** une fréquence de réplication n’est pas pertinente ici. La réplication est continue.

### <a name="can-i-extend-replication-from-existing-recovery-site-to-another-tertiary-site"></a>Puis-je étendre la réplication depuis un site de récupération existant à un site tiers ?
La réplication étendue ou chaînée n’est pas prise en charge. Demandez cette fonctionnalité dans le [forum de commentaires](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

### <a name="can-i-do-an-offline-replication-the-first-time-i-replicate-to-azure"></a>Puis-je effectuer une réplication hors connexion la première fois que je réplique vers Azure ?
Ceci n’est pas pris en charge. Demandez cette fonctionnalité dans le [forum de commentaires](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### <a name="can-i-exclude-specific-disks-from-replication"></a>Puis-je exclure des disques spécifiques de la réplication ?
Cette fonctionnalité est prise en charge dans le cadre d’une [réplication de machines virtuelles VMware et de machines virtuelles Hyper-V](site-recovery-exclude-disk.md) vers Azure à l’aide du portail Azure.

### <a name="can-i-replicate-virtual-machines-with-dynamic-disks"></a>Puis-je répliquer des machines virtuelles avec des disques dynamiques ?
Les disques dynamiques sont pris en charge lors de la réplication de machines virtuelles Hyper-V. Ils sont également pris en charge lors de la réplication des machines virtuelles et physiques VMware. Le disque du système d’exploitation doit être un disque de base.

### <a name="can-i-add-a-new-machine-to-an-existing-replication-group"></a>Puis-je ajouter un nouvel ordinateur à un groupe de réplication existant ?
L’ajout de nouveaux ordinateurs à des groupes de réplication est pris en charge. Pour ce faire, sélectionnez le groupe de réplication (à partir du panneau Éléments répliqués) et cliquez avec le bouton droit/sélectionnez le menu contextuel du groupe de réplication, puis sélectionnez l’option appropriée.

![Ajouter à un groupe de réplication](./media/site-recovery-faq/add-server-replication-group.png)

### <a name="can-i-throttle-bandwidth-allotted-for-hyper-v-replication-traffic"></a>Puis-je limiter la bande passante allouée pour le trafic de réplication Hyper-V ?
Oui. Pour plus d’informations sur la limitation de bande passante, consultez les articles de déploiement suivants :

* [Planification de la capacité pour la réplication de machines virtuelles VMware et de serveurs physiques](site-recovery-plan-capacity-vmware.md)
* [Planification de la capacité pour la réplication de machines virtuelles Hyper-V dans des clouds VMM](site-recovery-vmm-to-azure.md#step-5-capacity-planning)
* [Planification de la capacité pour la réplication de machines virtuelles Hyper-V sans VMM](site-recovery-hyper-v-site-to-azure.md#step-5-capacity-planning)

## <a name="failover"></a>Basculement
### <a name="if-im-failing-over-to-azure-how-do-i-access-the-azure-virtual-machines-after-failover"></a>Si j’effectue le basculement vers Azure, comment accéder aux machines virtuelles Azure après le basculement ?
Vous pouvez accéder aux machines virtuelles Azure via une connexion Internet sécurisée, via un réseau privé virtuel de site à site ou via Azure ExpressRoute. Vous devez préparer un certain nombre de choses afin de vous connecter. En savoir plus :

* [Connexion à des machines virtuelles Azure après un basculement de machines virtuelles VMware ou de serveurs physiques](site-recovery-test-failover-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)
* [Connexion à des machines virtuelles Azure après un basculement de machines virtuelles Hyper-V dans des clouds VMM](site-recovery-vmm-to-azure.md#step-7-test-your-deployment)
* [Connexion à des machines virtuelles Azure après un basculement de machines virtuelles Hyper-V sans VMM](site-recovery-hyper-v-site-to-azure.md#prepare-to-connect-to-azure-vms-after-failover)

### <a name="if-i-fail-over-to-azure-how-does-azure-make-sure-my-data-is-resilient"></a>Si j’effectue le basculement vers Azure, comment Azure s’assure-t-il de la résilience de mes données ?
Azure est conçu pour la résilience. Site Recovery est déjà prévu pour assurer le basculement vers un centre de données Azure secondaire, dans le respect du contrat SLA Azure le cas échéant. Dans ce cas, nous nous assurons que vos métadonnées et vos coffres restent dans la même région géographique que vous avez choisie pour votre coffre.  

### <a name="if-im-replicating-between-two-datacenters-what-happens-if-my-primary-datacenter-experiences-an-unexpected-outage"></a>Si j’effectue une réplication entre deux centres de données, que se passe-t-il si mon centre de données principal connaît une panne inattendue ?
Vous pouvez déclencher un basculement non planifié à partir du site secondaire. Site Recovery n’a pas besoin d’être connecté au site principal pour effectuer le basculement.

### <a name="is-failover-automatic"></a>Le basculement est-il automatique ?
Le basculement n’est pas automatique. Vous lancez les basculements d’un seul clic dans le portail, ou vous pouvez utiliser [Site Recovery PowerShell](https://docs.microsoft.com/en-us/powershell/resourcemanager/azurerm.siterecovery/v3.2.0/azurerm.siterecovery) pour déclencher un basculement. La restauration automatique est une action simple dans le portail Site Recovery.

Pour automatiser les processus, vous pouvez utiliser Orchestrator ou Operations Manager localement pour détecter une défaillance de machine virtuelle, puis déclencher le basculement à l’aide du Kit de développement logiciel (SDK).

* [Découvrez plus d’informations](site-recovery-create-recovery-plans.md) sur les plans de récupération.
* [En savoir plus](site-recovery-failover.md) sur le basculement.
* [En savoir plus](site-recovery-failback-azure-to-vmware.md) sur la restauration automatique de serveurs physiques et de machines virtuelles VMware

## <a name="service-providers"></a>Fournisseurs de services
### <a name="im-a-service-provider-does-site-recovery-work-for-dedicated-and-shared-infrastructure-models"></a>Je suis un fournisseur de services. Site Recovery fonctionne-t-il pour les modèles d’infrastructure dédiée ou partagée ?
Oui, Site Recovery prend en charge les modèles d’infrastructure dédiée et partagée.

### <a name="for-a-service-provider-is-the-identity-of-my-tenant-shared-with-the-site-recovery-service"></a>Pour un fournisseur de services, l’identité de mon locataire est-elle communiquée au service Site Recovery ?
Non. L’identité du locataire reste anonyme. Vos locataires n’ont pas besoin d’accéder au portail Site Recovery. Seul l’administrateur du fournisseur de services interagit avec le portail.

### <a name="will-tenant-application-data-ever-go-to-azure"></a>Les données d’application de mes locataires sont-elles diffusées sur Azure ?
Lors de la réplication entre des sites appartenant à un fournisseur de services, les données d’application n’accèdent jamais à Azure. Les données sont chiffrées en transit et répliquées directement entre les sites du fournisseur de services.

Si vous répliquez vers Azure, les données d’application sont envoyées vers le stockage Azure, mais pas vers le service Site Recovery. Les données sont chiffrées en transit et restent chiffrées dans Azure.

### <a name="will-my-tenants-receive-a-bill-for-any-azure-services"></a>Mes clients recevront-ils une facture pour les services Azure ?
Non. La relation de facturation d’Azure concerne directement le fournisseur de services. Les fournisseurs de services sont responsables de la création de factures spécifiques pour leurs locataires.

### <a name="if-im-replicating-to-azure-do-we-need-to-run-virtual-machines-in-azure-at-all-times"></a>Si je réplique vers Azure, faut-il exécuter à tout moment des machines virtuelles dans ce dernier ?
Non, les données sont répliquées vers un compte de stockage Azure de votre abonnement. Lorsque vous effectuez un basculement de test (test de récupération d’urgence) ou un basculement réel, Site Recovery crée automatiquement les machines virtuelles dans votre abonnement.

### <a name="do-you-ensure-tenant-level-isolation-when-i-replicate-to-azure"></a>Assurez-vous l’isolation au niveau des clients lors de la réplication vers Azure ?
Oui.

### <a name="what-platforms-do-you-currently-support"></a>Quelles plates-formes prenez-vous en charge, actuellement ?
Nous prenons en charge Azure Pack et le système Cloud Platform, ainsi que les déploiements basés sur System Center (2012 et versions supérieures). [En savoir plus](https://technet.microsoft.com/library/dn850370.aspx) sur l’intégration d’Azure Pack et de Site Recovery.

### <a name="do-you-support-single-azure-pack-and-single-vmm-server-deployments"></a>Prenez-vous en charge les déploiements uniques de serveurs VMM et Azure Pack ?
Oui, vous pouvez répliquer des machines virtuelles Hyper-V vers Azure, ou répliquer entre des sites du fournisseur de services.  Notez que si vous répliquez entre des sites du fournisseur de services, l’intégration de runbooks Azure n’est pas disponible.

## <a name="next-steps"></a>Étapes suivantes
* Lisez la [Vue d’ensemble de Microsoft Azure Site Recovery](site-recovery-overview.md)
* En savoir plus sur l’ [architecture de Site Recovery](site-recovery-components.md)  

