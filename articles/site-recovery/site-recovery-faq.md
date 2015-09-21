<properties 
	pageTitle="Microsoft Azure Site Recovery : Forum Aux Questions" 
	description="Cet article traite des questions fréquemment posées sur l’utilisation de Microsoft Azure Site Recovery." 
	services="site-recovery" 
	documentationCenter=""
	authors="csilauraa"
	manager="jwhit"
	editor="tysonn"/>

<tags 
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="na" 
	ms.workload="storage-backup-recovery"
	ms.date="08/26/2015" 
	ms.author="lauraa"/>


# Microsoft Azure Site Recovery : Forum Aux Questions
## À propos de cet article

Cet article contient les questions fréquemment posées sur Microsoft Azure Site Recovery. Pour accéder à une présentation de Microsoft Azure Site Recovery et des scénarios de déploiement associés, voir [Présentation d’Azure Site Recovery](site-recovery-overview.md).

Si votre question ne trouve pas de réponse dans cet article, posez-la sur le [forum Microsoft Azure](https://social.msdn.microsoft.com/Forums/azure/home?forum=hypervrecovmgr).


## Généralités
### ASR est-il sécurisé ? Quel type de données envoyez-vous à Microsoft Azure ?

Oui, ASR est sécurisé. Il n'intercepte pas vos données d'application, ni aucune information sur les composants exécutés au sein de vos machines virtuelles. Aucune connexion à Internet n’est nécessaire, que ce soit à partir d’un hôte Hyper-V ou d’une machine virtuelle.

Comme la réplication est effectuée entre votre entreprise et les sites du fournisseur de services, aucune donnée d’application n’est envoyée à Microsoft Azure. Seules les métadonnées requises pour orchestrer le basculement (comme les noms de cloud ou de VM) sont envoyées à Microsoft Azure. ASR n’a pas la possibilité d’intercepter vos données d’application, qui restent toujours sur site.

ASR est certifié compatible avec la norme ISO 27001:2005. Il devrait prochainement être compatible avec les normes HIPAA et DPA et avec les exigences du JAB FedRAMP.

### Les exigences en matière de conformité nécessitent que les données de nos environnements locaux restent dans la même région géographique. Cela inclut les métadonnées. ASR permet-il de répondre à cette exigence ?

Oui. Quand vous créez un coffre Site Recovery dans la région de votre choix, nous vérifions que toutes les métadonnées dont nous avons besoin pour activer et orchestrer la récupération d'urgence restent au sein de cette région.

### Existe-t-il un kit de développement logiciel (SDK) que je peux utiliser pour automatiser le flux de travail du logiciel ASR ?

Oui. Les flux de travail ASR peuvent être automatisés via le kit de développement logiciel (SDK) Microsoft Azure, PowerShell et l’API REST. Pour en savoir plus à ce sujet, consultez le billet de blog suivant : [Introducing PowerShell support for Azure Site Recovery](http://azure.microsoft.com/blog/2014/11/05/introducing-powershell-support-for-azure-site-recovery/).

### ASR chiffre-t-il la réplication ? 
Entre un serveur local vers Azure et entre des sites locaux, la réplication prend en charge le chiffrement dans le transit pour les *scénarios de protection de Hyper-V et VMM*. La *protection Hyper-V et VMM* sur Azure prend également en charge le chiffrement au repos. Pour plus d’informations, consultez [cet article](https://azure.microsoft.com/blog/2014/09/02/azure-site-recovery-privacy-security-part1/).

### Puis-je augmenter la fréquence de réplication/copie à plus de 15 minutes ?
* **Scénarios Hyper-V et VMM** : non, la réplication des machines virtuelles Hyper-V utilisant la réplication basée sur un hôte peut être configurée seulement à 30 secondes, 5 minutes et 15 minutes
* **Scénario VMware/physique** : ceci n’est pas applicable pour la réplication basée sur l’invité, car la technologie utilise la protection continue des données.

### Puis-je exclure des disques spécifiques de la réplication à l’aide d’ASR ?
Ceci n’est pas pris en charge. Envoyez-nous vos commentaires via le [Forum de commentaires d’Azure Site Recovery – Exclure un disque de la réplication](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6418801-exclude-disks-from-replication).

### Puis-je répliquer des machines virtuelles basées sur des disques dynamiques ?
Les *scénarios Hyper-V et VMM* prennent en charge les disques dynamiques. Les *scénarios de machines virtuelles VMware ou de machines physiques* ne prennent pas en charge les disques dynamiques. Envoyez-nous vos commentaires via le [Forum de commentaires d’Azure Site Recovery](http://feedback.azure.com/forums/256299-site-recovery).

### Quels sont les types de comptes de stockage pris en charge ?
Le [stockage géo-redondant standard](../storage/storage-redundancy.md#geo-redundant-storage) est pris en charge. Le [compte de stockage Premium]((../storage/storage-premium-storage-preview-portal/) est pris en charge seulement pour les scénarios de [machine virtuelle VMware ou de machine physique](site-recovery-vmware-to-azure.md). La prise en charge du stockage localement redondant est dans les backlogs. Envoyez-nous vos commentaires via [Prise en charge du stockage localement redondant](http://feedback.azure.com/forums/256299-site-recovery/suggestions/7204469-local-redundant-type-azure-storage-support).

### Puis-je étendre la réplication depuis un site de récupération existant à un troisième site ?
Ceci n’est pas pris en charge. Envoyez-nous vos commentaires via le [Forum de commentaires d’Azure Site Recovery – Prise en charge de l’extension de la réplication](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6097959-support-for-exisiting-extended-replication).

### Puis-je alimenter les disques initiaux sur Azure à l’aide d’un mécanisme hors connexion ?
Ceci n’est pas pris en charge. Envoyez-nous vos commentaires via le [Forum de commentaires d’Azure Site Recovery – Prise en charge de la réplication hors connexion](http://feedback.azure.com/forums/256299-site-recovery/suggestions/6227386-support-for-offline-replication-data-transfer-from).

### Puis-je limiter la bande passante allouée pour le trafic de réplication quand Hyper-V est utilisé en tant que source ?
- Si vous effectuez une réplication entre deux sites locaux, vous pouvez utiliser les composants de qualité de service (QoS) Windows à cette fin. Voici un exemple de script : 

    	New-NetQosPolicy -Name ASRReplication -IPDstPortMatchCondition 8084 -ThrottleRate (2048*1024)
    	gpupdate.exe /force

- Si vous effectuez une réplication vers Azure, vous pouvez la configurer en vous inspirant de l’exemple d’applet de commande PowerShell suivant :

    	Set-OBMachineSetting -WorkDay $mon, $tue -StartWorkHour "9:00:00" -EndWorkHour "18:00:00" -WorkHourBandwidth (512*1024) -NonWorkHourBandwidth (2048*1024)


## Prise en charge des versions

### Quelles sont les versions des hôtes et clusters Windows Server prises en charge ?
Vous pouvez utiliser les logiciels Windows Server 2012 et Windows Server 2012 R2 lorsque vous choisissez le Réplica Hyper-V pour activer la réplication et la protection entre des sites Hyper-V.


### Quelles sont les versions des systèmes d'exploitation invités Hyper-V prises en charge ?
La liste la plus à jour des systèmes d’exploitation invités prise en charge est disponible dans la rubrique [À propos des ordinateurs virtuels et des systèmes d’exploitation invités](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

### Puis-je configurer la protection des machines virtuelles quand Hyper-V s'exécute sur un système d'exploitation client ?

Vous ne pouvez pas configurer Hyper-V s'exécutant sur un système d'exploitation client pour répliquer des machines virtuelles dans Azure. Le réplica Hyper-V est uniquement pris en charge sur les systèmes d'exploitation de serveur. Vous pouvez répliquer vos ordinateurs clients physiques dans Azure à l'aide de [cet article](site-recovery-vmware-to-azure.md).

### ASR prend-il en charge les machines de génération 2 ?

Oui, ASR prend en charge la réplication des machines virtuelles de génération 2 sur Hyper-V dans Azure. ASR les convertit de la génération 2 à la génération 1 pendant le basculement. Au moment de la restauration automatique, la machine est reconvertie en génération 2. Pour plus d’informations, cliquez [ici](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/).


## Déploiement entre des sites de fournisseurs de services 

### Cette solution fonctionne-t-elle pour les modèles d’infrastructure dédiée ou partagée ?
Oui, ASR prend en charge ces deux modèles d’infrastructure.

### L’identité de mon locataire est-elle communiquée à Microsoft Azure ?
Non. En fait, vos locataires n’ont pas besoin d’accéder au portail ASR. Seul l’administrateur du fournisseur de services effectue des actions sur le portail ASR dans Microsoft Azure.

### Mes locataires recevront-ils des factures de Microsoft Azure ?
Non. La relation de facturation de Microsoft Azure concerne directement le fournisseur de services. Les fournisseurs de services sont responsables de la création de factures spécifiques pour leurs locataires.

### Les données d’application de mes locataires sont-elles diffusées sur Microsoft Azure ?
Lorsque vous utilisez un site appartenant à un fournisseur de services à des fins de récupération d’urgence, les données d’application n’accèdent jamais à Microsoft Azure. Les données sont chiffrées et répliquées directement entre les sites du fournisseur de services. Lorsque vous utilisez Microsoft Azure en tant que site de récupération d’urgence, les données d’application lui sont envoyées, mais les données au repos et en transit restent chiffrées. Vous pouvez également utiliser un réseau VPN, la solution ExpressRoute ou un réseau Internet public pour établir la connectivité à Microsoft Azure.

### Lorsque nous utilisons Microsoft Azure en tant que site de récupération d’urgence, devons-nous exécuter à tout moment des machines virtuelles dans ce dernier ?
Non, le logiciel ASR est conçu comme une solution DRaaS de cloud public de premier choix. Les données sont répliquées vers un compte Microsoft Azure Storage géo-redondant inclus dans votre abonnement. Lorsque vous effectuez un exercice de récupération d’urgence ou un basculement réel, le logiciel ASR crée automatiquement les machines virtuelles dans le cadre de votre abonnement.

### Assurez-vous l’isolation côté locataire lorsque j’utilise Microsoft Azure en tant que site de récupération d’urgence ?
Oui.

### Quelles plates-formes prenez-vous en charge, actuellement ?
Nous prenons en charge Microsoft Azure Pack et Microsoft Cloud Platform System, ainsi que les déploiements Hyper-V 2012 basés sur Microsoft System Center et les versions supérieures. Pour en savoir plus sur l’intégration de Microsoft Azure Pack et ASR, voir [Configurer la protection des machines virtuelles](https://technet.microsoft.com/library/dn850370.aspx).

### Prenez-vous également en charge les déploiements uniques de serveurs VMM et Microsoft Azure Pack ?
Oui, nous gérons les déploiements SCVMM uniques pour l’un ou l’autre de ces scénarios : la récupération d’urgence entre des sites du fournisseur de services, d’une part, et la récupération d’urgence entre un site de ce dernier et Microsoft Azure.

Le déploiement de Microsoft Azure Pack est également pris en charge lorsque vous activez la récupération d’urgence entre des sites du fournisseur de services. Toutefois, l’intégration du Runbook Microsoft ASR n’est pas disponible pour cette topologie.

## Déploiement entre des sites Hyper-V et Azure (sans VMM)

### Pour utiliser le logiciel ASR, faut-il que j’active le réseau VPN de site à site ?
Non, ce n’est pas obligatoire ; ASR fonctionne très bien sur un réseau Internet public. Cependant, si vous avez configuré un réseau VPN de site à site, vous avez la possibilité d’accéder aux machines virtuelles ayant effectué le basculement et ce, comme auparavant. Pour en savoir plus sur les éléments de mise en réseau à prendre en compte lors de l’activation de la récupération d’urgence sur Microsoft Azure, consultez le billet de blog suivant : [Networking Infrastructure Setup for Microsoft Azure as a Disaster Recovery Site](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/).

### Dois-je payer des frais supplémentaires en plus de la protection ASR ?
À l’état stationnaire, nous répliquons les modifications dans le stockage Microsoft Azure Storage géo-redondant. Ainsi, vous n’avez aucun frais de gestion des machines virtuelles IaaS Microsoft Azure à régler (ce qui est un atout majeur). Lorsque vous effectuez un basculement vers Microsoft Azure, ASR crée automatiquement les machines virtuelles IaaS Microsoft Azure. Après cela, vous devez régler les ressources de traitement que vous utilisez dans Microsoft Azure.

### Une de mes succursales n'a pas de serveur VMM installé. Puis-je activer la protection des charges de travail de cette succursale dans Microsoft Azure ? Quels sont les principaux avantages de l’utilisation du logiciel ASR à cette fin ?
Oui, vous pouvez utiliser ASR pour protéger les machines virtuelles exécutées sur des hôtes Hyper-V qui ne sont pas gérés par SCVMM.

Lorsque vous utilisez ASR pour gérer les besoins de vos succursales en termes de récupération d’urgence, vous obtenez une vue unifiée des charges de travail de toutes vos succursales, affichée à un seul emplacement. Vous pouvez également effectuer un basculement en un clic, ainsi que l’administration de la récupération d’urgence de l’ensemble des succursales depuis le bureau central, sans avoir à vous déplacer. Actuellement, le chiffrement des données au repos et le mappage réseau ne sont pas pris en charge en cas de récupération d’urgence des données des succursales.

### Pour les succursales, le nombre de machines virtuelles que je peux protéger est-il limité ?
Non. La prise en charge reste la même, comme dans notre scénario d’entreprise.

### Dois-je installer un agent sur les machines virtuelles que je veux protéger ?

Vous n'avez pas besoin d'agent sur les machines virtuelles. Vous devez installer le fournisseur Site Recovery et l'agent Recovery Services sur chaque serveur Hyper-V exécutant les machines virtuelles que vous souhaitez protéger. Ces deux composants sont installés avec un seul fichier de fournisseur qui peut être téléchargé au cours du déploiement de Site Recovery.

### Le fournisseur s'exécutant sur le serveur Hyper-V requiert-il des paramètres de pare-feu spécifiques ?

Aucun paramètre spécifique n'est requis. Les composants du fournisseur sur le serveur communiquent via une connexion HTTPS avec Azure et utilisent le paramètre Internet par défaut sur le serveur hôte Hyper-V, sauf indication contraire.

### Le serveur Hyper-V doit-il être membre d'un domaine ?

Non, le serveur n'a pas besoin de se trouver dans un domaine.

### Quelles sont les versions des hôtes et clusters Windows Server prises en charge ?
Vous pouvez utiliser Windows Server 2012 et Windows Server 2012 R2 quand vous choisissez ASR et la réplication Hyper-V entre des sites Hyper-V et Azure.

### Quelles sont les versions des systèmes d’exploitation prises en charge ?
La liste la plus à jour des systèmes d’exploitation invités pris en charge est disponible dans la rubrique [À propos des ordinateurs virtuels et des systèmes d’exploitation invités](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx).

## Déploiement entre deux centres de données VMM

### Puis-je répliquer une machine virtuelle qui a été répliquée dans un site secondaire dans Azure ? 

Non, ce type de réplication chaînée n'est pas pris en charge.

### Ai-je besoin de certificats pour configurer la protection entre deux centres de données VMM ?

Non. Lors de la configuration de la protection entre des clouds VMM dans ASR, spécifiez le type d’authentification. Sélectionnez HTTPS, sauf si vous avez configuré un environnement Kerberos opérationnel. Azure Site Recovery configurera automatiquement des certificats pour l'authentification HTTPS. Aucune configuration manuelle n'est nécessaire. Si vous sélectionnez Kerberos, un ticket Kerberos est utilisé pour l'authentification mutuelle des serveurs hôtes. Par défaut, les ports 8083 (pour Kerberos) et 8084 (pour les certificats) seront ouverts dans le Pare-feu Windows sur les serveurs hôtes Hyper-V. Notez que ce paramètre n'est utile que pour les serveurs hôtes Hyper-V s'exécutant sur Windows Server 2012 R2.



## Déploiement entre deux centres de données VMM avec SAN

### Que se passe-t-il si j’ai déjà configuré la réplication basée sur une baie SAN et que je ne souhaite pas modifier ma configuration ?
Aucun problème. ASR peut gérer les scénarios dans lesquels la réplication est déjà configurée. Dans ce cas, vous pouvez utiliser ce logiciel pour assurer l’orchestration de la récupération d’urgence axée sur les machines virtuelles, la configuration de la mise en réseau à des fins de récupération d’urgence, la mise en place de plans de récupération tenant compte des applications, l’exécution d’exercices de récupération d’urgence et la gestion des exigences en matière de conformité et de création de rapports d’audit.


### Dois-je utiliser VMM ? Dois-je utiliser VMM des deux côtés ?
Oui. Nous avons besoin d'assurer la gestion de la baie SAN via VMM au moyen du fournisseur SMI-S propre à la baie.

Nous prenons en charge les déploiements uniques à haut niveau de disponibilité de VMM basés sur le type de baie, même si la configuration recommandée stipule d'utiliser des serveurs VMM distincts pour gérer les sites.


### Quelles sont les baies de stockage prises en charge ?

NetApp, EMC et HP prennent en charge la réplication SAN d’Azure Site Recovery avec des mises à jour de leurs fournisseurs SMI-S. Pour plus d’informations, consultez les liens suivants.

- [Clustered Data ONTAP 8.2 de NetApp](http://community.netapp.com/t5/Technology/NetApp-Unveils-Support-for-Microsoft-Azure-SAN-Replication-with-SMI-S-and/ba-p/94483)
- [Série EMC VMAX](https://thecoreblog.emc.com/high-end-storage/microsoft-azure-site-recovery-now-generally-available-vmax-srdf-integration-pack-ready-for-public-review/)    
- [3PAR HP](http://h20195.www2.hp.com/V2/GetDocument.aspx?docname=4AA5-7068ENW&cc=us&lc=en)


### Que faire si je ne suis pas sûr des modifications de l’administrateur du stockage ?
Nous utilisons la réplication existante telle que l’a configurée l’administrateur du stockage. Cela signifie que ce dernier n’a pas besoin d’apporter des modifications aux baies. Toutefois, les entreprises qui souhaitent automatiser leur gestion du stockage via SCVMM peuvent également configurer le stockage à l'aide d'ASR et VMM.

### Le logiciel me permet-il de prendre en charge la réplication synchrone ? Les clusters invités ? Ou encore le stockage partagé ?
Oui, oui et encore oui !

### Net-net : Que dois-je installer en local pour que ce logiciel fonctionne correctement ?
Lorsque vous utilisez la réplication basée sur la baie (SAN) pour activer la réplication et la protection entre des sites Hyper-V, il vous suffit d’installer le fournisseur de récupération d’urgence du logiciel ASR sur le ou les serveurs SCVMM. N’oubliez pas qu’il s’agit également du *seul* hôte qui requiert une connexion à Internet.


Il est également nécessaire que les baies soient découvertes par SCVMM au moyen d’un fournisseur SMI-S mis à jour, proposé par les fournisseurs de stockage respectifs.

## Déploiement entre des serveurs physiques et Azure

### Puis-je protéger mon serveur physique local dans Azure ?

Oui, vous pouvez répliquer un serveur physique à l'aide d'ASR et l'exécuter en tant que machine virtuelle dans Azure au cas où votre serveur local tombe en panne. La restauration automatique sur un serveur physique local n'est actuellement pas prise en charge. Vous pouvez effectuer la restauration automatique sur une machine virtuelle s'exécutant sur Hyper-V ou VMware.


## Basculement

### Que se passe-t-il si Microsoft Azure ne fonctionne plus ? Puis-je déclencher un basculement à partir de VMM ?

Le logiciel Microsoft Azure est conçu pour assurer la résilience des services, mais nous préférons prévoir les pires situations. Le logiciel ASR est déjà prévu pour assurer le basculement vers un centre de données Microsoft Azure secondaire, dans le respect du contrat SLA Microsoft Azure. Nous vérifions également que, même dans ce cas, les métadonnées et le coffre ASR restent dans la même région géographique que celle de votre coffre.


### Que se passe-t-il si le fournisseur de services Internet associé au centre de données principal rencontre également une panne pendant un incident ? Que se passe-t-il si le fournisseur de services Internet associé au centre de données secondaire rencontre également une panne ?

Vous pouvez utiliser le portail ASR pour effectuer un basculement non planifié, en un seul clic. Pour ce basculement, aucune connexion n’est requise à partir du centre de données principal.

Cependant, il est probable que les applications qui doivent s’exécuter dans votre centre de données secondaire après un basculement auront besoin d’une connexion à Internet, sous une forme ou une autre. ASR suppose que, même si le site principal et le fournisseur de services Internet risquent d’être affectés pendant un incident, le serveur SCVMM du site secondaire est toujours connecté à ASR.

### ASR prend-il en charge la restauration automatique ?
Oui. Le processus de restauration depuis Microsoft Azure vers votre site local, très simple, peut être effectué via un seul clic sur le portail ASR.

### Le basculement automatique est-il possible ?

Il n'existe aucune option de basculement automatique. Vous pouvez appuyer sur un bouton dans le portail pour déclencher un basculement ou utiliser [Site Recovery PowerShell](https://msdn.microsoft.com/library/dn850420.aspx). Vous utilisez Orchestrator ou Operations Manager localement pour témoigner d'une défaillance de machine virtuelle, puis décidez de déclencher le basculement à l'aide du Kit de développement logiciel (SDK). Cela revient à un processus automatique.




## Surveillance

### Je souhaite surveiller la configuration et la réplication en cours. Quelles solutions me proposez-vous ?
Vous pouvez vous abonner de manière à recevoir des alertes par e-mail depuis le service ASR, directement, afin d’être informé de tout problème nécessitant votre attention. Vous pouvez également surveiller l’intégrité de la réplication en continu, via SCOM.

## Étapes suivantes

Pour commencer à déployer ASR, effectuez les opérations suivantes :

- [Configuration de la protection entre un site local VMM et Azure](site-recovery-vmm-to-azure.md) 
- [Configuration de la protection entre un site local Hyper-V et Azure](site-recovery-hyper-v-site-to-azure.md) 
- [Configuration de la protection entre des sites VMM locaux](site-recovery-vmm-to-vmm.md) 
- [Configuration de la protection entre des sites locaux VMM avec SAN](site-recovery-vmm-san.md) 
- [Configuration de la protection avec un seul serveur VMM](site-recovery-single-vmm.md) 

 

<!---HONumber=Sept15_HO2-->