<properties 
	pageTitle="Microsoft Azure Site Recovery : Forum Aux Questions" 
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
	ms.date="12/01/2015" 
	ms.author="raynew"/>


# Azure Site Recovery : Forum Aux Questions (FAQ)
## À propos de cet article

Cet article contient le Forum Aux Questions relatif à Site Recovery. Après avoir lu le FAQ, peut-être aurez-vous des questions. Le cas échéant, posez-les sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## Support

###À quoi sert Site Recovery ?

Site Recovery contribue à mettre en œuvre la stratégie de continuité d’activité et de récupération d’urgence (BCDR) de votre entreprise en coordonnant et en automatisant la réplication à partir de machines virtuelles et de serveurs physiques locaux sur Azure ou sur un centre de données secondaire. [En savoir plus](site-recovery-overview.md).


### Que peut protéger Site Recovery ?

- **Machines virtuelles Hyper-V** : Site Recovery peut protéger toute charge de travail en cours d’exécution sur une machine virtuelle Hyper-V. 
- **Serveurs physiques** : Site Recovery peut protéger les serveurs physiques exécutant Windows ou Linux.
- **Machines virtuelles VMware** : Site Recovery peut protéger toute charge de travail en cours d’exécution dans une machine virtuelle VMware.


###Quelles machines virtuelles Hyper-V puis-je protéger ?

Cela dépend du scénario de déploiement.

Vérifiez la configuration requise du serveur hôte Hyper-V dans les pages suivantes :

- [Réplication de machines virtuelles Hyper-V (sans VMM) sur Azure](site-recovery-hyper-v-site-to-azure/#before-you-start)
- [Réplication de machines virtuelles Hyper-V (avec VMM) sur Azure](site-recovery-vmm-to-azure/#before-you-start)
- [Réplication de machines virtuelles Hyper-V sur un centre de données secondaire](site-recovery-vmm-to-vmm/#before-you-start)

En ce qui concerne les systèmes d’exploitation invités :

- Si vous effectuez une réplication vers un centre de données secondaire, consultez la liste des systèmes d’exploitation invités pris en charge pour les machines virtuelles s’exécutant sur des serveurs hôtes Hyper-V dans [Systèmes d’exploitation invités pris en charge pour les machines virtuelles Hyper-V](https://technet.microsoft.com/library/mt126277.aspx).
- Si vous effectuez une réplication vers Azure, Site Recovery prend en charge tous les systèmes d’exploitation invités [pris en charge par Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

Site Recovery peut protéger toute charge de travail en cours d’exécution sur une machine virtuelle prise en charge.

### Puis-je protéger des machines virtuelles lorsque Hyper-V est en cours d’exécution sur un système d’exploitation client ?

Non, cette option n’est pas prise en charge. À la place, vous devez [répliquer l’ordinateur client physique](site-recovery-vmware-to-azure.md) sur Azure ou sur un centre de données secondaire.


### Quelles charges de travail puis-je protéger avec Site Recovery ?

Vous pouvez utiliser Site Recovery pour protéger la plupart des charges de travail en cours d’exécution sur une machine virtuelle ou un serveur physique. Site Recovery peut vous aider à déployer la récupération d’urgence tenant compte des applications. Site Recovery s’intègre avec les applications Microsoft, y compris SharePoint, Exchange, Dynamics, SQL Server et Active Directory, et fonctionne en étroite collaboration avec les principaux fournisseurs, notamment Oracle, SAP, IBM et Red Hat. Vous pouvez personnaliser votre solution de récupération d’urgence pour chaque application. [En savoir plus](site-recovery-workload.md) sur la protection des charges de travail.


### Ai-je toujours besoin d’un serveur VMM System Center pour protéger les machines virtuelles Hyper-V ? 

Non. Si vous effectuez la réplication vers Azure, vous pouvez répliquer des machines virtuelles Hyper-V se trouvant sur des serveurs hôtes Hyper-V situés ou non dans des clouds VMM. Si vous effectuez la réplication vers un centre de données secondaire, les serveurs hôtes Hyper-V doivent être gérés dans des clouds VMM. [En savoir plus](site-recovery-hyper-v-site-to-azure.md)

### Puis-je déployer Site Recovery avec VMM si je ne dispose que d’un seul serveur VMM ? 

Oui. Vous pouvez répliquer des machines virtuelles Hyper-V dans le cloud sur le serveur VMM vers Azure, ou répliquer entre des clouds VMM sur le même serveur. Pour la réplication d’un site local vers un autre, notez que nous recommandons de disposer d’un serveur VMM dans les sites principaux et secondaires. [En savoir plus](site-recovery-single-vmm.md)

### Quels serveurs physiques puis-je protéger ?

Vous pouvez protéger des serveurs physiques exécutant Windows et Linux, sur Azure ou sur un site secondaire. Pour connaître la configuration requise du système d’exploitation, consultez la page [De quoi ai-je besoin ?](site-recovery-vmware-to-azure/#what-do-i-need). Que vous répliquiez des serveurs physiques sur Azure ou sur un site secondaire, les mêmes restrictions s’appliquent.

Notez que les serveurs physiques seront exécutés en tant que machines virtuelles dans Azure si votre serveur local tombe en panne. La restauration automatique sur un serveur physique local n’est actuellement pas prise en charge, mais vous pouvez l’effectuer sur une machine virtuelle s’exécutant sur Hyper-V ou VMware.

### Quelles machines virtuelles VMware puis-je protéger ?

Pour ce scénario, vous avez besoin d’un serveur VMware vCenter, d’un hyperviseur vSphere et de machines virtuelles exécutant les outils VMware. Pour connaître la configuration requise exacte, consultez la page [De quoi ai-je besoin ?](site-recovery-vmware-to-azure/#what-do-i-need). Que vous répliquiez des serveurs physiques sur Azure ou sur un site secondaire, les mêmes restrictions s’appliquent.

### Existe-t-il des conditions requises pour la réplication des machines virtuelles vers Azure ?

Les machines virtuelles que vous souhaitez répliquer vers Azure doivent se conformer aux [exigences d’Azure](site-recovery-best-practices/#virtual-machines).

### Puis-je répliquer des machines virtuelles de génération 2 Hyper-V vers Azure ?

Oui. Site Recovery les convertit de la génération 2 à la génération 1 pendant le basculement. Au moment de la restauration automatique, la machine est reconvertie en génération 2. [En savoir plus](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).

### Si je réplique vers Azure comment vais-je payer les machines virtuelles Azure ? 

Lors de la réplication normale, les données sont répliquées dans le stockage Azure géo-redondant. Ainsi, vous n’avez aucuns frais de gestion des machines virtuelles IaaS Azure à régler, ce qui est un atout majeur. Lorsque vous exécutez un basculement vers Azure, Site Recovery crée automatiquement les machines virtuelles IaaS Azure. Après cela, vous êtes facturé pour les ressources de calcul que vous utilisez dans Azure.

### Puis-je gérer la récupération d’urgence pour mes succursales avec Site Recovery ?

Oui. Lorsque vous utilisez Site Recovery pour coordonner la réplication et le basculement dans vos succursales, vous obtenez une orchestration unifiée et l’affichage de toutes les charges de travail de vos succursales dans un emplacement central. Vous pouvez facilement exécuter les basculements et gérer la récupération d’urgence de toutes les succursales à partir de votre siège social, sans vous rendre dans ces succursales.

### Existe-t-il un kit de développement logiciel (SDK) que je peux utiliser pour automatiser le flux de travail du logiciel ASR ?

Oui. Vous pouvez automatiser les flux de travail Site Recovery à l’aide de l’API Rest, de PowerShell ou du Kit de développement logiciel (SDK) Azure. En savoir plus dans [Déploiement d’Azure Site Recovery avec PowerShell](site-recovery-deploy-with-powershell.md).


## Sécurité et conformité

### Mes données sont-elles envoyées au service Site Recovery ?

Non, Site Recovery n’intercepte pas vos données d’application et n’a pas d’informations sur les éléments qui s’exécutent sur vos machines virtuelles ou serveurs physiques.

Les données de réplication sont échangées entre des hôtes Hyper-V, des hyperviseurs VMware ou des serveurs physiques dans vos centres de données principaux et secondaires, ou entre votre centre de données et le stockage Azure. Site Recovery n’a aucun moyen d’intercepter ces données. Seules les métadonnées nécessaires pour coordonner la réplication et le basculement sont envoyées au service Site Recovery.

Le logiciel Site Recovery est certifié conforme à la norme ISO 27001:2005. Il devrait prochainement être compatible avec les normes HIPAA et DPA et avec les exigences du JAB FedRAMP.

### Pour la conformité, les données de nos environnements locaux doivent rester dans la même région géographique. Cela inclut les métadonnées. Site Recovery peut-il nous aider ?

Oui. Quand vous créez un coffre Site Recovery dans une région, nous vérifions que toutes les métadonnées dont nous avons besoin pour activer et coordonner la réplication et le basculement restent au sein de cette région.

### Site Recovery chiffre-t-il la réplication ?
Lors de la réplication de machines virtuelles et de serveurs physiques entre des sites locaux, le chiffrement en transit est pris en charge. Lors de la réplication de machines virtuelles et de serveurs physiques entre des sites locaux et Azure, le chiffrement en transit et le chiffrement dans Azure sont tous deux pris en charge.




## Réplication et basculement

### Si je réplique vers Azure, de quel type de compte de stockage ai-je besoin ?

Vous avez besoin d’un compte de stockage avec [stockage géo-redondant standard](../storage/storage-redundancy/#geo-redundant-storage). Un [compte de stockage premium](../storage/storage-premium-storage-preview-portal/) est pris en charge uniquement si vous répliquez des machines virtuelles VMware, ou des serveurs physiques Windows/Linux sur Azure.

La prise en charge du stockage localement redondant standard est en souffrance. Envoyez des commentaires sur cette fonctionnalité dans le [forum de commentaires](http://feedback.azure.com/forums/256299-site-recovery/suggestions/7204469-local-redundant-type-azure-storage-support).

### À quelle fréquence puis-je répliquer les données ?
- **Hyper-V :** les machines virtuelles Hyper-V peuvent être répliquées toutes les 30 secondes, 5 minutes ou 15 minutes. Si vous avez configuré la réplication SAN, alors la réplication sera synchrone.
- **Serveurs VMware et physiques :** une fréquence de réplication n’est pas pertinente ici. La réplication sera continue. 

### Puis-je étendre la réplication depuis un site de récupération existant à un site tiers ?
La réplication étendue ou chaînée n’est pas prise en charge. Envoyez des commentaires sur cette fonctionnalité dans le [forum de commentaires](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).


### Puis-je effectuer une réplication hors connexion la première fois que je réplique vers Azure ? 

Ceci n’est pas pris en charge. Envoyez-nous des commentaires sur cette fonctionnalité dans le [forum de commentaires](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).


### Puis-je exclure des disques spécifiques de la réplication ?

Ceci n’est pas pris en charge. Envoyez-nous des commentaires sur cette fonctionnalité dans le [forum de commentaires](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication).

### Puis-je répliquer des machines virtuelles avec des disques dynamiques ?

Les disques dynamiques sont pris en charge lors de la réplication de machines virtuelles Hyper-V. Ils ne sont pas pris en charge lors de la réplication de machines virtuelles ou de serveurs physiques VMware. Envoyez-nous des commentaires sur cette fonctionnalité dans le [forum de commentaires](http://feedback.azure.com/forums/256299-site-recovery).

### Si j’effectue le basculement vers Azure, comment accéder aux machines virtuelles Azure après le basculement ? 

Vous pouvez accéder aux machines virtuelles Azure via une connexion Internet sécurisée ou via un VPN de site à site (ou Azure ExpressRoute) si vous en possédez un. Les communications via une connexion VPN s’effectuent vers des ports internes sur le réseau Azure sur lequel se trouve la machine virtuelle. Les communications via Internet sont mappées aux points de terminaison publics sur le service cloud Azure pour les machines virtuelles. [En savoir plus](site-recovery-network-design/#connectivity-after-failover)

### Si j’effectue le basculement vers Azure, comment Azure s’assure-t-il de la résilience de mes données ?

Azure est conçu pour la résilience du service. Site Recovery est déjà prévu pour assurer le basculement vers un centre de données Azure secondaire, dans le respect du contrat SLA Azure le cas échéant. Dans ce cas, nous nous assurons que vos métadonnées et vos coffres restent dans la même région géographique que vous avez choisie pour votre coffre.

### Si j’effectue une réplication entre deux centres de données, que se passe-t-il si mon centre de données principal connaît une panne inattendue ?

Vous pouvez déclencher un basculement non planifié à partir du site secondaire. Site Recovery n’a pas besoin d’être connecté au site principal pour effectuer le basculement.


### Le basculement est-il automatique ?

Le basculement n’est pas automatique. Vous lancez les basculements d’un seul clic dans le portail, ou vous pouvez utiliser [Site Recovery PowerShell](https://msdn.microsoft.com/library/dn850420.aspx) pour déclencher un basculement. La restauration automatique est également une action simple dans le portail Site Recovery. Pour automatiser les processus, vous pouvez utiliser Orchestrator ou Operations Manager localement pour détecter une défaillance de machine virtuelle, puis déclencher le basculement à l’aide du Kit de développement logiciel (SDK).

### Si je réplique des machines virtuelles Hyper-V, puis-je limiter la bande passante allouée pour le trafic de réplication Hyper-V ?
- Si vous répliquez entre des machines virtuelles Hyper-V deux sites locaux, vous pouvez utiliser les composants de qualité de service (QoS) Windows. Voici un exemple de script : 

    	New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
    	gpupdate.exe /force

- Si vous répliquez des machines virtuelles Hyper-V vers Azure, vous pouvez configurer la limitation à l’aide de l’exemple d’applet de commande PowerShell suivant :

    	Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)



## Déploiement du fournisseur de services

### Site Recovery fonctionne-t-il pour les modèles d’infrastructure dédiée ou partagée ?
Oui, Site Recovery prend en charge les modèles d’infrastructure dédiée et partagée.

### L’identité de mon client est-elle communiquée au service Site Recovery ?
Non. En fait, vos clients n’ont pas besoin d’accéder au portail Site Recovery. Seul l’administrateur du fournisseur de services effectue des actions sur le portail.


### Les données d’application de mes clients sont-elles diffusées sur Azure ?
Lors de la réplication entre des sites appartenant à un fournisseur de services, les données d’application n’accèdent jamais à Azure. Les données sont chiffrées en transit et répliquées directement entre les sites du fournisseur de services.

Si vous répliquez vers Azure, les données d’application sont envoyées vers le stockage Azure, mais pas vers le service Site Recovery. Les données sont chiffrées en transit et restent chiffrées dans Azure.

### Mes clients recevront-ils une facture pour les services Azure ?

Non. La relation de facturation d’Azure concerne directement le fournisseur de services. Les fournisseurs de services sont responsables de la création de factures spécifiques pour leurs locataires.

### Si je réplique vers Azure, faut-il exécuter à tout moment des machines virtuelles dans ce dernier ?

Non, les données sont répliquées vers un compte de stockage Azure géo-redondant dans votre abonnement. Lorsque vous effectuez un basculement de test (test de récupération d’urgence) ou un basculement réel, Site Recovery crée automatiquement les machines virtuelles dans votre abonnement.

### Assurez-vous l’isolation au niveau des clients lors de la réplication vers Azure ?

Oui.

### Quelles plates-formes prenez-vous en charge, actuellement ?

Nous prenons en charge Azure Pack et le système Cloud Platform, ainsi que les déploiements basés sur System Center (2012 et versions supérieures). Pour en savoir plus sur l’intégration d’Azure Pack et de Site Recovery, consultez [Configurer la protection des machines virtuelles](https://technet.microsoft.com/library/dn850370.aspx).

### Prenez-vous en charge les déploiements uniques de serveurs VMM et Azure Pack ?

Oui, vous pouvez répliquer les machines virtuelles Hyper-V et Azure, ou entre des sites du fournisseur de services. Notez que si vous répliquez entre des sites du fournisseur de services, l’intégration de runbooks Azure n’est pas disponible.


## Étapes suivantes

- Lisez la [Vue d’ensemble de Microsoft Azure Site Recovery](site-recovery-overview.md)
- En savoir plus sur l’[architecture de Site Recovery](site-recovery-components.md)  

 

<!---HONumber=AcomDC_1203_2015-->