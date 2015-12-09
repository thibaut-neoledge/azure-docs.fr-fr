<properties
	pageTitle="Préparer le déploiement d’Azure Site Recovery"
	description="Azure Site Recovery coordonne la réplication, le basculement et la récupération des machines virtuelles et des serveurs physiques situés sur des serveurs locaux dans Azure ou sur un centre de données secondaire."
	services="site-recovery"
	documentationCenter=""
	authors="rayne-wiselman"
	manager="jwhit"
	editor="tysonn"/>

<tags
	ms.service="site-recovery"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="na"
	ms.workload="storage-backup-recovery"
	ms.date="12/01/2015"
	ms.author="raynew"/>

# Préparer le déploiement d’Azure Site Recovery

## À propos de cet article

Cet article explique comment préparer le déploiement d’Azure Site Recovery. Si, après avoir lu cet article, vous avez des questions, posez-les sur le [forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).

## Protection des machines virtuelles Hyper-V

Pour protéger les machines virtuelles Hyper-V, vous avez le choix entre deux options de déploiement. Vous pouvez répliquer les machines virtuelles Hyper-V locales dans Azure ou sur un centre de données secondaire. Chaque déploiement a ses exigences propres.

**Prérequis** | **Réplication dans Azure (avec VMM)** | **Réplication de machines virtuelles Hyper-V dans Azure (sans VMM)** | **Réplication de machines virtuelles Hyper-V sur un site secondaire (avec VMM)** | **Détails**
---|---|---|---|---
**VMM** | VMM exécuté sur System Center 2012 R2. <br/><br/>Au moins un cloud VMM contenant un ou plusieurs groupes d’hôtes VMM. | N/D | Serveurs VMM sur les sites principal et secondaire s’exécutant au moins sur System Center 2012 SP1 avec les dernières mises à jour. <br/><br/> Au moins un cloud sur chaque serveur VMM. Le profil de capacité Hyper-V doit être défini sur les clouds.<br/><br/> Le cloud source doit avoir au moins un groupe d’hôtes VMM. | Facultatif. Vous n’avez pas besoin de déployer System Center VMM pour répliquer des machines virtuelles Hyper-V dans Azure. En revanche, si vous le faites, vérifiez que le serveur VMM est correctement configuré. Vérifiez notamment que vous exécutez la bonne version de VMM et que les clouds sont configurés.
**Hyper-V** | Au moins un serveur hôte Hyper-V sur le centre de données local exécutant Windows Server 2012 R2 ou une version ultérieure. | Au moins un serveur Hyper-V sur les sites source et cible exécutant au moins Windows Server 2012 avec les dernières mises à jour installées, et connecté à Internet.<br/><br/> Les serveurs Hyper-V doivent se trouver dans un groupe hôte d’un cloud VMM. | Au moins un serveur Hyper-V sur les sites source et cible exécutant au moins Windows Server 2012 avec les dernières mises à jour installées, et connecté à Internet.<br/><br/> Les serveurs Hyper-V doivent se trouver dans un groupe hôte d’un cloud VMM. | 
**Machines virtuelles** | Au moins une machine virtuelle sur le serveur hôte Hyper-V source. | Au moins une machine virtuelle sur le serveur hôte Hyper-V dans le cloud VMM source. | Au moins une machine virtuelle sur le serveur hôte Hyper-V dans le cloud VMM source. | Les machines virtuelles répliquées sur Azure doivent être conformes à la [configuration requise des machines virtuelles Azure](site-recovery-best-practices/#virtual-machines).
**Compte Azure** | Vous avez besoin d’un compte [Azure](http://azure.microsoft.com/) et d’un abonnement au service Site Recovery. | Vous avez besoin d’un compte [Azure](http://azure.microsoft.com/) et d’un abonnement au service Site Recovery. | N/D | Si vous n’avez pas de compte, commencez avec une [version d’évaluation gratuite](pricing/free-trial/). En savoir plus sur les [tarifs](pricing/details/site-recovery/) du service.
**Stockage Azure** | Vous avez besoin d’un abonnement pour bénéficier d’un compte de stockage Azure prenant en charge la géo-réplication. | Vous avez besoin d’un abonnement pour bénéficier d’un compte de stockage Azure prenant en charge la géo-réplication. | N/D | Le compte doit se trouver dans la même région que le coffre Azure Site Recovery et être associé au même abonnement. [En savoir plus sur le stockage](../storage/storage-introduction.md).
**Mappage de stockage** | N/D | N/D | Vous pouvez éventuellement configurer le mappage de stockage pour vous assurer que les machines virtuelles sont connectées de manière optimale au stockage après le basculement. Lorsque vous répliquez entre deux sites VMM locaux, la machine virtuelle du réplica est stockée par défaut sur l’emplacement indiqué sur le serveur hôte cible Hyper-V. Vous pouvez configurer le mappage entre les classes de stockage VMM sur les serveurs VMM sources et cibles. | Pour utiliser cette fonctionnalité, vous devez configurer des classifications de stockage avant de commencer le déploiement. [En savoir plus](site-recovery-storage-mapping.md).
**Réplication SAN** | N/D | N/D | Si vous souhaitez effectuer une réplication entre deux sites VMM locaux à l’aide de la réplication SAN, vous pouvez utiliser votre environnement SAN existant. | Vous avez besoin d’un [groupe SAN pris en charge](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx). Notez que le stockage SAN doit être découvert et classifié dans VMM.<br/><br/>Si aucune réplication n’est en cours, vous devez créer des numéros d’unité logique (LUN) et allouer du stockage dans la console VMM. Si vous avez démarré la réplication, passez cette étape.
**Mise en réseau** | Configurez le mappage réseau pour que toutes les machines virtuelles dont le basculement s’effectue sur le même réseau Azure puissent se connecter les unes aux autres, indépendamment du plan de récupération auquel elles sont intégrées. Par ailleurs, si une passerelle réseau est configurée sur le réseau cible Azure, les machines virtuelles peuvent se connecter aux autres machines virtuelles locales. Si vous ne définissez pas le mappage réseau, seules les machines basculant dans le même plan de récupération peuvent se connecter. | N/D | <br/><br/>Configurez le mappage réseau pour vous assurer que les machines virtuelles sont connectées aux bons réseaux après le basculement et que les machines virtuelles de réplica sont placées de manière optimale sur les serveurs hôtes Hyper-V. Si vous ne configurez pas le mappage réseau, les machines répliquées ne seront connectées à aucun réseau de machines virtuelles après le basculement. | [En savoir plus](site-recovery-network-mapping) sur le mappage réseau. <br/><br/> Pour configurer le mappage réseau avec VMM, vous devez vérifier que le réseau logique VMM et le réseau de machines virtuelles sont correctement configurés. [En savoir plus](http://blogs.technet.com/b/scvmm/archive/2013/02/14/networking-in-vmm-2012-sp1-logical-networks-part-i.aspx) et [réseaux de machines virtuelles](https://technet.microsoft.com/library/jj721575.aspx)). De plus, prenez connaissance des [considérations relatives aux réseaux pour VMM](site-recovery-network-design/#vmm-design).  
**Fournisseurs et agents** | Pendant le déploiement, vous devez installer le fournisseur Azure Site Recovery sur les serveurs VMM. Sur les serveurs Hyper-V des clouds VMM, vous devez installer l’agent Azure Recovery Services. | Pendant le déploiement, vous devez installer le fournisseur Azure Site Recovery et l’agent Azure Recovery Services sur le serveur hôte ou le cluster Hyper-V.| Pendant le déploiement, vous devez installer le fournisseur Azure Site Recovery sur les serveurs VMM. Sur les serveurs Hyper-V des clouds VMM, vous devez installer l’agent Azure Recovery Services. | Fournisseur et agents se connectent à Site Recovery via Internet en utilisant une connexion HTTPS chiffrée. Vous n’avez pas besoin d’ajouter d’exceptions de pare-feu ni de créer un proxy spécifique pour la connexion du fournisseur, mais si vous ne voulez pas utiliser de proxy personnalisé, vous devez autoriser ces URL sur le pare-feu avant de commencer le déploiement : <br/><br/> *.hypervrecoverymanager.windowsazure.com <br/><br/> *.accesscontrol.windows.net<br/><br/> *.backup.windowsazure.com <br/><br/> *.blob.core.windows.net <br/><br/>*.store.core.windows.net **Connectivité Internet** | Seuls les serveurs VMM ont besoin d’une connexion Internet | Seuls les serveurs hôtes Hyper-V ont besoin d’une connexion Internet | Seuls les serveurs VMM ont besoin d’une connexion Internet | Il n’est pas utile d’installer quoi que ce soit sur les machines virtuelles et elles ne se connectent pas directement à Internet.



## Protéger des machines virtuelles VMware ou des serveurs physiques

Pour protéger des machines virtuelles VMware ou des serveurs physiques Windows/Linux, deux options de déploiement s’offrent à vous. Vous pouvez les répliquer sur Azure ou dans un centre de données secondaire. Chaque déploiement a ses exigences propres.

**Prérequis** | **Répliquer des machines virtuelles VMware/serveurs physiques sur Azure)** | **Répliquer des machines virtuelles VMware/serveurs physiques sur un site secondaire**  
---|---|--- 
**Site principal** | **Serveur de traitement** : serveur Windows dédié (physique ou virtuel). | **Serveur de traitement** : serveur Windows dédié (ordinateur physique ou machine virtuelle VMware).<br/><br/>  
**Site local secondaire** | N/D | **Serveur de configuration** : serveur Windows dédié (physique ou virtuel). <br/><br/> **Serveur cible maître** : serveur dédié (physique ou virtuel). Configurez avec Windows pour protéger les ordinateurs Windows ou avec Linux pour protéger Linux.
**Microsoft Azure** | **Abonnement** : vous avez besoin d’un abonnement pour bénéficier du service Site Recovery. En savoir plus sur les [tarifs](pricing/details/site-recovery/). <br/><br/> **Compte de stockage** : vous avez besoin d’un compte de stockage prenant en charge la géo-réplication. Le compte doit se trouver dans la même région que le coffre Site Recovery et être associé au même abonnement. [En savoir plus](../storage/storage-introduction.md).<br/><br/> **Serveur de configuration** : vous devez configurer le serveur de configuration en tant que machine virtuelle Azure. <br/><br/> **Serveur cible maître** : vous devez configurer le serveur cible maître en tant que machine virtuelle Azure. <br/><br/> Configurez avec Windows pour protéger les ordinateurs Windows ou avec Linux pour protéger Linux.<br/><br/> **Réseau virtuel Azure** : vous avez besoin d’un réseau virtuel Azure sur lequel le serveur de configuration et le serveur cible maître seront déployés. Il doit faire partie du même abonnement et de la même région que le coffre Azure Site Recovery. | N/D  
**Machines virtuelles/serveurs physiques** | Au moins une machine virtuelle VMware ou un serveur Windows/Linux physique.<br/><br/>Pendant le déploiement, le service Mobilité est installé sur chaque machine.| Au moins une machine virtuelle VMware ou un serveur physique Linux/Windows.<br/><br/> Pendant le déploiement, l’Agent unifié est installé sur chaque machine.




## Configuration requise des machines virtuelles Azure

Vous pouvez déployer Site Recovery pour répliquer des machines virtuelles et des serveurs physiques exécutant n’importe quel système d’exploitation pris en charge par Azure. La plupart des versions de Windows et Linux sont concernées. Vous devez vérifier que les machines virtuelles locales à protéger répondent à la configuration requise d’Azure.


**Fonctionnalité** | **Support** | **Détails**
---|---|---
Système d’exploitation hôte Hyper-V | Windows Server 2012 R2 | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Système d’exploitation hyperviseur VMware | Exécution d’un système d’exploitation pris en charge | [Détails](site-recovery-vmware-to-azure/#before-you-start) 
Système d’exploitation invité | Pour une réplication de Hyper-V vers Azure, Site Recovery prend en charge tous les systèmes d’exploitation [pris en charge par Azure](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx). <br/><br/> Pour une réplication de serveur physique et VMware, vérifiez la [configuration requise](site-recovery-vmware-to-azure/#before-you-start) pour Windows et Linux. | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge. 
Architecture du système d’exploitation invité | 64 bits | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Taille du disque du système d’exploitation | Jusqu’à 1 023 Go | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Nombre de disques du système d’exploitation | 1 | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge. 
Nombre de disques de données | 16 ou moins (la valeur maximale est fonction de la taille de la machine virtuelle créée. 16 = XL). | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Taille du disque dur virtuel de données | Jusqu’à 1 023 Go | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Adaptateurs réseau | Prise en charge de plusieurs adaptateurs réseau. |
Adresse IP statique | Pris en charge | Si la machine virtuelle principale utilise une adresse IP statique, vous pouvez spécifier l’adresse associée à la machine qui sera créée dans Azure.
Disque iSCSI | Non pris en charge | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Disque dur virtuel partagé | Non pris en charge | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Disque FC | Non pris en charge | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Format de disque dur| Disque dur virtuel (VHD) <br/><br/> VHDX | Bien que VDHX ne soit pas actuellement pris en charge dans Azure, Site Recovery convertit automatiquement VHDX en VHD quand vous effectuez un basculement vers Azure. Lorsque vous procédez à la restauration automatique en local, les machines continue à utiliser le format VHDX.
Nom de la machine virtuelle| Entre 1 et 63 caractères. Uniquement des lettres, des chiffres et des traits d’union. Il doit commencer et se terminer par une lettre ou un chiffre. | Mettez à jour la valeur dans les propriétés de machine virtuelle de Site Recovery
Type de machine virtuelle | <p>Génération 1</p> <p>Génération 2 - Windows</p> | Une machine virtuelle de génération 2 dont le type de disque de système d’exploitation est disque de base et qui inclut 1 ou 2 volumes de données avec un format de disque VHDX inférieur à 300 Go est prise en charge. Les machines virtuelles Linux de génération 2 ne sont pas prises en charge. [En savoir plus](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/)



## Optimisation de votre déploiement

Pour optimiser et mettre à l’échelle votre déploiement, tenez compte des conseils suivants.

- **Taille du volume du système d’exploitation** : quand vous répliquez une machine virtuelle dans Azure, la taille du volume du système d’exploitation doit être inférieure à 1 To. Si vous disposez de volumes supérieurs, vous pouvez les déplacer manuellement vers un disque différent avant de démarrer le déploiement.
- **Taille du disque de données** : si vous répliquez vers Microsoft Azure, vous pouvez posséder jusqu’à 32 disques de données sur une machine virtuelle, chacun présentant une taille maximale d’1 To. Vous pouvez répliquer et basculer efficacement une machine virtuelle présentant une taille d’environ 32 To.
- **Limites de plan de récupération** : Scan Recovery peut prendre en charge des milliers de machines virtuelles. Les plans de récupération sont conçus comme un modèle dédié aux applications devant basculer simultanément, de manière à ce que le nombre de machines du plan soit limité à 50.
- **Limites des services Microsoft Azure** : chaque abonnement Microsoft Azure comporte un ensemble de défauts liés aux services principaux, cloud, etc. Pour valider la disponibilité des ressources de votre abonnement, nous vous recommandons d’exécuter un basculement test. Vous pouvez modifier ces limites via le support Microsoft Azure.
- **Planification de la capacité** : consultez [Capacity Planner](http://www.microsoft.com/en-in/download/details.aspx?id=39057) si vous prévoyez de répliquer des machines virtuelles Hyper-V.
- **Bande passante de réplication** : si vous disposez d’une bande passante de réplication insuffisante, notez les points suivants :
	- **ExpressRoute** : Site Recovery s’associe à des optimiseurs Microsoft Azure ExpressRoute et WAN, tels que Riverbed. [En savoir plus](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sur ExpressRoute.
	- **Trafic de réplication** : Site Recovery exécute une réplication initiale intelligente uniquement à l’aide de blocs de données, pas de l’intégralité du disque dur virtuel. Seules les modifications sont répliquées au cours de la réplication en continu.
	- **Trafic réseau** : pour contrôler le trafic réseau utilisé pour la réplication, configurez le réseau de [Qualité de service (QoS) Windows](https://technet.microsoft.com/library/hh967468.aspx) à l’aide d’une stratégie basée sur l’adresse IP et le port de destination. En plus si vous répliquez vers Microsoft Azure Site Recovery à l’aide de l’agent de sauvegarde Microsoft Azure. Vous pouvez configurer la limitation de cet agent. [En savoir plus](https://support.microsoft.com/kb/3056159).
- **RTO** :si vous souhaitez mesurer l’objectif de délai de récupération procuré par Site Recovery, nous vous recommandons d’exécuter un basculement test et d’examiner les tâches de récupération afin d’analyser le délai d’exécution des opérations. Si vous effectuez un basculement vers Microsoft Azure, vous avez tout intérêt à automatiser l’ensemble des actions manuelles en intégrant avec Microsoft Azure Automation et les plans de récupération.
- **RPO** : Site Recovery prend en charge un objectif de point de récupération quasi synchrone lorsque vous répliquez vers Microsoft Azure. Ceci est valable uniquement si vous disposez d’une bande passante suffisante entre votre centre de données et Microsoft Azure.





## Étapes suivantes

Après avoir consulté ces meilleures pratiques, vous pouvez amorcer le déploiement de Site Recovery :

- [Configuration de la protection entre un site local VMM et Azure](site-recovery-vmm-to-azure.md)
- [Configuration de la protection entre un site local Hyper-V et Azure](site-recovery-hyper-v-site-to-azure.md)
- [Configuration de la protection entre des sites VMM locaux](site-recovery-vmm-to-vmm.md)
- [Configuration de la protection entre des sites locaux VMM avec SAN](site-recovery-vmm-san.md)
- [Configuration de la protection avec un seul serveur VMM](site-recovery-single-vmm.md)
 

<!---HONumber=Nov15_HO4-->