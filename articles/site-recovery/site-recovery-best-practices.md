<properties
	pageTitle="Meilleures pratiques du déploiement de Site Recovery"
	description="Microsoft Azure Site Recovery coordonne la réplication, le basculement et la récupération des machines virtuelles hébergées, sur des serveurs locaux, vers Microsoft Azure ou un centre de données secondaire."
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
	ms.date="05/08/2015"
	ms.author="raynew"/>


# Meilleures pratiques du déploiement de Site Recovery


<a id="overview" name="overview" href="#overview"></a>

## Dans cet article

L’article vous communique les meilleures pratiques à consulter et à mettre en œuvre avant de déployer Microsoft Azure Site Recovery. Pour consulter une introduction à Site Recovery et aux scénarios de déploiement associés, consultez la [Vue d’ensemble de Site Recovery](site-recovery-overview.md).

Après avoir lu cet article, peut-être aurez-vous des questions. Le cas échéant, posez-les sur le [Forum Azure Recovery Services](https://social.msdn.microsoft.com/forums/azure/home?forum=hypervrecovmgr).


## Préparation à Microsoft Azure

- **Compte Azure** : vous devez avoir un compte [Microsoft Azure](http://azure.microsoft.com/). Si vous n'en possédez pas, commencez avec une [version d'évaluation gratuite](pricing/free-trial/).
- En savoir plus sur la [tarification](pricing/details/site-recovery/) de Site Recovery 
- **Microsoft Azure Storage** : si vous déployez Site Recovery avec la réplication sur Microsoft Azure, vous devez posséder un compte Microsoft Azure Storage. Vous pouvez en créer un pendant le déploiement, ou en préparer un avant de démarrer. La géo-réplication doit être activée pour ce compte. Il doit se trouver dans la même région que le coffre Azure Site Recovery et être associé au même abonnement. Consultez l’[Introduction à Microsoft Azure Storage](../storage/storage-introduction.md). 

## Machines virtuelles

Si vous souhaitez répliquer sur Microsoft Azure Storage, notez les points suivants :

- **Système d’exploitation pris en charge** : vous pouvez déployer Site Recovery afin d’orchestrer la protection de machines virtuelles exécutant tout système d’exploitation pris en charge par Microsoft Azure. La plupart des versions de Windows et Linux sont concernées.
- **Prise en charge VHDX** : VDHX n’est actuellement pas pris en charge par Microsoft Azure, mais Site Recovery convertit automatiquement VHDX en VHD lorsque vous procédez au basculement vers Microsoft Azure. Lorsque vous procédez à la restauration automatique en local, les machines continue à utiliser le format VHDX.
- **Configuration requise Microsoft Azure** : assurez-vous que l’ensemble des machines virtuelles que vous souhaitez protéger sont conformes à la configuration requise Microsoft Azure.

**Fonction de machine virtuelle** | **Support** | **Détails**
---|---|---
Système d’exploitation hôte | Windows Server 2012 R2 | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Système d’exploitation invité | <p>Windows Server 2008 R2 ou version ultérieure</p><p>Linux : Centos, openSUSE, SUSE, Ubuntu</p> | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge. Mettez à jour la valeur dans la console VMM.
Architecture du système d’exploitation invité | 64 bits | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Taille du disque du système d’exploitation | Jusqu'à 1 023 Go | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Nombre de disques du système d’exploitation | 1 | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge. Mettez à jour la valeur dans la console VMM.
Nombre de disques de données | 16 ou moins (la valeur maximale est fonction de la taille de la machine virtuelle créée. 16 = XL). | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Taille du disque dur virtuel de données | Jusqu'à 1 023 Go | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Adaptateurs réseau | Prise en charge de plusieurs adaptateurs réseau. |
Adresse IP statique | Pris en charge | Si la machine virtuelle principale utilise une adresse IP statique, vous pouvez spécifier l’adresse associée à la machine qui sera créée dans Microsoft Azure.
Disque iSCSI | Non pris en charge | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Disque dur virtuel partagé | Non pris en charge | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Disque FC | Non pris en charge | La vérification de la configuration requise est mise en échec en cas de défaut de prise en charge.
Format de disque dur| <p>Disque dur virtuel</p><p>VHDX</p> |
Nom de la machine virtuelle| Entre 1 et 63 caractères. Uniquement des lettres, des chiffres et des traits d'union. Il doit commencer et se terminer par une lettre ou un chiffre. | Mettez à jour la valeur dans les propriétés de machine virtuelle de Site Recovery
Type de machine virtuelle | <p>Génération 1</p> <p>Génération 2 - Windows</p> | Une machine virtuelle de génération 2 dont le type de disque de système d'exploitation est disque de base et qui inclut 1 ou 2 volumes de données avec un format de disque VHDX inférieur à 300 Go est prise en charge. Les machines virtuelles Linux de génération 2 ne sont pas prises en charge. [En savoir plus](http://azure.microsoft.com/blog/2015/04/28/disaster-recovery-to-azure-enhanced-and-were-listening/) 


## Serveurs VMM

Site Recovery peut orchestrer la réplication des machines virtuelles hébergées sur des serveurs hôtes Hyper-V dans des clouds System Center Virtual Machine Manager (VMM) comme suit : - Répliquer d’un serveur VMM local sur Microsoft Azure (à l’aide de Réplica Hyper-V) - Répliquer sur un site secondaire local (à l’aide de Réplica Hyper-V). Nous vous recommandons de déployer avec un serveur VMM dans le site principal et un autre dans le site secondaire. Toutefois, vous pouvez également, au besoin, [déployer un serveur VMM unique](site-recovery-single-vmm.md) pour les deux sites. - Répliquer sur un site secondaire local (à l’aide de SAN). Vous devez disposer d’un centre de données principal et d’un centre de données secondaire avec un serveur VMM sur chacun des sites. Si vous souhaitez déployer VMM avec Site Recovery, vous devrez configurer votre infrastructure VMM. Si vous ne possédez pas de serveur VMM, cliquez [ici](site-recovery-hyper-v-site-to-azure.md).


### Vérifier la version VMM

**Version VMM prise en charge** | **Scénario Site Recovery pris en charge** | **Hôte Hyper-V pris en charge (source/cible)** | **Fournisseur/agent pris en charge**
---|---|---|---
VMM sur System Center 2012 R2 avec au minimum Update Rollup 5 (virtuel au sein du cluster uniquement) | Protection locale en local avec réplication SAN | Au minimum Windows Server 2012 avec les dernières mises à jour | Version la plus récente du fournisseur Microsoft Azure Site Recovery (installation sur les serveurs VMM) |
VMM sur System Center 2012 R2 (recommandé) (cluster ou autonome) | <p>Protection locale en local avec réplication Hyper-V</p> <p>Protection locale Microsoft Azure avec réplication Hyper-V</p> | <p>Au minimum Windows Server 2012 avec les dernières mises à jour</p><p>Windows Server 2012 R2 (source, non applicable pour la cible)</p> | <p>Version la plus récente du fournisseur Microsoft Azure Site Recovery (installation sur les serveurs VMM)</p> <p>Version la plus récente de l’agent Microsoft Azure Recovery Services (installation sur les serveurs hôtes Hyper-V pour la réplication vers Microsoft Azure uniquement)</p>
VMM sur System Center 2012 SP1 avec la dernière mise à jour cumulative (cluster ou autonome) | Protection locale en local | Windows Server 2012 avec les dernières mises à jour | Version la plus récente du fournisseur Microsoft Azure Site Recovery (installation sur les serveurs VMM) |

### Configurer l’infrastructure cloud VMM

Quel que soit le scénario de déploiement envisagé avec Site Recovery, vous devez créer au moins deux clouds privés (un sur le serveur VMM source et l’autre sur la cible) dans VMM afin de bénéficier des avantages du modèle de cloud privé lors de la définition, de la gestion et de l’accès au cloud et à ses ressources sous-jacentes à l’aide de la console VMM. Un cloud VMM recueille et présente un ensemble agrégé de ressources, dont l’utilisation est limitée par les paramètres de capacité du cloud et par les quotas des rôles utilisateur. Les utilisateurs du libre-service peuvent gérer et utiliser les ressources cloud sans maîtriser intégralement l’infrastructure sous-jacente. Ajoutez ou supprimez facilement des ressources pour réduire ou accroître l’élasticité du cloud. Si vous devez configurer des clouds, utilisez les ressources suivantes :


- [Nouveautés du cloud privé avec System Center 2012 R2 VMM](http://channel9.msdn.com/Events/TechEd/NorthAmerica/2013/MDC-B357#fbid=)
- [VMM 2012 et les clouds](http://www.server-log.com/blog/2011/8/26/vmm-2012-and-the-clouds.html).
- [Planifier l’infrastructure VMM](https://msdn.microsoft.com/library/azure/dn469075.aspx#BKMK_Fabric)
- [Création d’un cloud privé dans VMM](https://technet.microsoft.com/fr-fr/library/jj860425.aspx)
- [Procédure pas à pas : Création de clouds privés avec System Center 2012 SP1 VMM](http://blogs.technet.com/b/keithmayer/archive/2013/04/18/walkthrough-creating-private-clouds-with-system-center-2012-sp1-virtual-machine-manager-build-your-private-cloud-in-a-month.aspx).




## Fournisseurs et agents

Les fournisseurs et les agents sont installés sur les serveurs locaux, et peuvent ainsi se connecter à Site Recovery dans Microsoft Azure. Ils se connectent via Internet à l’aide d’une connexion HTTPS chiffrée.

- **Configuration requise des fournisseurs** : durant le déploiement, vous installerez plusieurs composants sur vos serveurs locaux :
	- **Sur les serveurs VMM** : vous installerez le fournisseur Microsoft Azure Site Recovery sur les serveurs VMM que vous souhaitez inscrire dans le coffre.
	- **Sur les serveurs hôtes Hyper-V hébergés dans un cloud VMM** : vous installerez l’agent Microsoft Azure Recovery Services.
	- **Sur les serveurs hôtes Hyper-V sans serveur VMM** : vous installerez le fournisseur Microsoft Azure Site Recovery et l’agent Microsoft Azure Recovery Services sur chacun des serveurs hôtes Hyper-V ou nœud de cluster.
-  **Installation des fournisseurs** : notez les points suivants relatifs à l’installation des fournisseurs et des agents :
	-  Vous devez installer la même version de fournisseur sur l’ensemble des fournisseurs d’un coffre Site Recovery. Il est impossible d’utiliser plusieurs versions au sein d’un même coffre.
	-  Si vous souhaitez répliquer vers Azure à partir de serveurs Hyper-V situés dans un nuage VMM, le serveur VMM doit exécuter System Center 2012 R2. Si vous souhaitez répliquer vers un centre de données secondaire, VMM doit exécuter System Center 2012 avec SP1 ou R2.
- **Proxy** : il n’est pas nécessaire d’ajouter d’exceptions de pare-feu ni de créer de proxy spécifique. Si vous ne souhaitez pas utiliser de proxy personnalisé pour la connexion du fournisseur, procédez comme suit avant de démarrer le déploiement :

	- Configurez le serveur de proxy personnalisé avant d’installer le fournisseur.
	- Autorisez ces URL sur le pare-feu :
		- **.hypervrecoverymanager.windowsazure.com
- **.accesscontrol.windows.net
- **.backup.windowsazure.com
- **.blob.core.windows.net
- **.store.core.windows.net

	- Si vous déployez Site Recovery avec VMM et utilisez un proxy personnalisé, un compte RunAs VMM (DRAProxyAccount) sera créé automatiquement à l’aide des informations d’identification du proxy spécifiées dans les paramètres personnalisés du proxy dans le portail Site Recovery. Pour permettre l’authentification du compte, vous devez configurer le serveur proxy.



## Connectivité locale

- **Connectivité du fournisseur** : des fournisseurs et des agents sont installés sur les serveurs locaux afin d’établir la connexion à Site Recovery. Ils se connectent à Site Recovery via Internet à l’aide d’une connexion HTTPS chiffrée. Il n’est pas nécessaire d’ajouter d’exceptions de pare-feu ni de créer de proxy spécifique.
- **Connectivité Internet** : vous devrez disposer d’une connectivité Internet suivante pour le serveur :
	- Si vous protégez des machines virtuelles sur des serveurs hôtes Hyper-V au sein d’un cloud VMM, seul le serveur VMM doit bénéficier d’une connexion à Internet.
	- Si vous protégez des machines virtuelles sur des serveurs hôtes Hyper-V sans serveur VMM, seuls les serveurs hôtes Hyper-V doivent bénéficier d’une connexion à Internet.
	- Il n’est pas nécessaire d’instaurer une connectivité Internet sur les machines virtuelles que vous souhaitez protéger.
- **VPN site à site** : il n’est pas nécessaire d’établir une connexion VPN site à site pour vous connecter à Site Recovery. Toutefois, si vous disposez d’une connexion site à site, vous serez en mesure d’accéder aux machines virtuelles basculées comme vous le faisiez avant le basculement. Pour répliquer vers Microsoft Azure, vous devez configurer un réseau VPN site à site entre votre site local et un réseau Microsoft Azure spécifique. Il n’est pas utilisé pour le trafic chiffré de réplication. La connexion est établie sur Internet vers le compte Microsoft Azure de votre abonnement.
- **Connectivity après le basculement** : pour vous assurer que les utilisateurs peuvent se connecter aux machines virtuelles après le basculement vers Microsoft Azure, procédez comme suit :
	- Avant le basculement, activez RDP pour les machines virtuelles.
	- Après le basculement, utilisez une connexion site à site afin de pouvoir vous y connecter comme vous le faisiez auparavant ou activez le point de terminaison RDP pour la machine.

## Stockage

- **Compte Microsoft Azure Storage** : si vous répliquez vers Microsoft Azure, vous devez disposer d’un compte Microsoft Azure Storage. La géo-réplication doit être activée pour ce compte. Il doit se trouver dans la même région que le coffre Azure Site Recovery et être associé au même abonnement. Pour en savoir plus, consultez l’[Introduction à Microsoft Azure Storage](../storage/storage-introduction.md).
- **Mappage du stockage** : si vous répliquez les machines virtuelles sur les serveurs VMM locaux, vous pouvez configurer le mappage de stockage afin de garantir la connexion optimale des machines au stockage après le basculement. Lorsque vous répliquez entre deux sites VMM locaux, la machine virtuelle du réplica est stockée par défaut sur l’emplacement indiqué sur le serveur hôte cible Hyper-V. Vous pouvez configurer le mappage entre les classes de stockage VMM sur les serveurs VMM sources et cibles. Si vous souhaitez utiliser cette fonctionnalité, assurez-vous que vous avez classifications de stockage avant de commencer le déploiement. [En savoir plus](site-recovery-storage-mapping.md) sur le mappage de stockage.
- **SAN** : si vous souhaitez répliquer entre deux sites locaux avec la réplication SAN, notez les points suivants :
	- Vous pouvez répliquer des machines virtuelles Hyper-V uniquement vers un centre de données secondaire avec la réplication SAN. Vous ne pouvez pas répliquer vers Microsoft Azure.
	- Vous pouvez utiliser votre environnement SAN existant. Il n’est pas nécessaire d’apporter des modifications sur les baies SAN.
	- Vous devez vérifier que votre baie SAN est [prise en charge](http://social.technet.microsoft.com/wiki/contents/articles/28317.deploying-azure-site-recovery-with-vmm-and-san-supported-storage-arrays.aspx).
	- Pour le déploiement SAN, vous devez disposer de deux serveurs VMM dans les sites sources et cibles.
	- Si vous souhaitez déployer la réplication SAN, assurez-vous que vos clusters hôtes exécutent Windows Server 2012 ou 2012 R2. [En savoir plus](https://technet.microsoft.com/library/cc794868%28v=ws.10%29.aspx) sur les systèmes d’exploitation invités pris en charge par différentes versions de Hyper-V.
	- Il vous faudra découvrir et classifier le stockage SAN dans VMM.
	- Si vous ne procédez pas encore à la réplication, il vous faudra, après la découverte, créer des numéros d’unité logique et allouer le stockage dans la console VMM. Si vous avez démarré la réplication, passez cette étape.
	- Pour consulter des instructions complètes, lisez cet [article](site-recovery-vmm-san.md).


## Réseaux

- **En savoir plus sur les considérations de mise en réseau** : [En savoir plus](http://azure.microsoft.com/blog/2014/09/04/networking-infrastructure-setup-for-microsoft-azure-as-a-disaster-recovery-site/) sur les considérations et les meilleures pratiques de mise en réseau.

- **Configurez le mappage réseau** : le mappage réseau est un élément important à prendre en compte lors du déploiement de VMM et de Site Recovery. Il permet de placer de manière optimale les machines virtuelles sur les serveurs hôtes Hyper-V cibles et garantit que vos machines virtuelles répliquées sont connectées aux réseaux appropriés après le basculement. Vous pouvez configurer le mappage réseau lorsque vous répliquez vers Microsoft Azure ou vers un centre de données secondaire :
	- **Mappage réseau vers Microsoft Azure** : si vous répliquez vers Microsoft Azure, le mappage réseau vous garantit que l’ensemble des machines basculées sur le même réseau peuvent se connecter entre elles, indépendamment du plan de récupération sollicité. Par ailleurs, si une passerelle réseau est configurée sur le réseau cible Microsoft Azure, les machines virtuelles peuvent se connecter aux autres machines virtuelles locales. Si vous ne définissez pas le mappage réseau, seules les machines basculant dans le même plan de récupération peuvent se connecter.
	- **Mappage réseau sur le site secondaire** : si vous répliquez vers un site secondaire VMM, le mappage réseau vous garantit que les machines virtuelles sont connectées aux réseaux appropriés après le basculement et que les machines virtuelles réplicas sont placées de manière optimale sur les serveurs hôtes Hyper-V. Si vous ne configurez pas le mappage réseau, les machines répliquées ne seront connectées à aucun réseau de machines virtuelles.
	- [En savoir plus](site-recovery-network-mapping) sur le mappage réseau.
- **Configurez les réseaux VMM** :
	- Configurez les réseaux logiques et de machines virtuelles de manière appropriée dans VMM. En savoir plus sur les [réseaux logiques](http://blogs.technet.com/b/scvmm/archive/2013/02/14/networking-in-vmm-2012-sp1-logical-networks-part-i.aspx) et les [réseaux de machines virtuelles](https://technet.microsoft.com/library/jj721575.aspx).
	- Assurez-vous que l’ensemble des machines virtuelles du serveur source VMM sont connectées au réseau de machines virtuelles.
	- Vérifiez que les réseaux de machines virtuelles sont liés au réseau logique associé au cloud.
	- Si vous répliquez vers Microsoft Azure, créez des réseaux virtuels dans Microsoft Azure. Notez que plusieurs réseaux de machines virtuelles peuvent être mappés à un seul réseau Azure. Lisez les [tâches de configuration du réseau virtuel](../vpn-gateway/vpn-gateway-site-to-site-create.md).

## Optimisation des performances

- **Taille du volume du système d'exploitation** : lorsque vous répliquez une machine virtuelle vers Microsoft Azure, la taille du volume du système d’exploitation doit être inférieure à 1 To. Si vous disposez de volumes supérieurs, vous pouvez les déplacer manuellement vers un disque différent avant de démarrer le déploiement.
- **Taille du disque de données** : si vous répliquez vers Microsoft Azure, vous pouvez posséder jusqu’à 32 disques de données sur une machine virtuelle, chacun présentant une taille maximale d’1 To. Vous pouvez répliquer et basculer efficacement une machine virtuelle présentant une taille d’environ 32 To.
- **Limites de plan de récupération** : Scan Recovery peut prendre en charge des milliers de machines virtuelles. Les plans de récupération sont conçus comme un modèle dédié aux applications devant basculer simultanément, de manière à ce que le nombre de machines du plan soit limité à 50.
- **Limites des services Microsoft Azure** : chaque abonnement Microsoft Azure comporte un ensemble de défauts liés aux services principaux, cloud, etc. Pour valider la disponibilité des ressources de votre abonnement, nous vous recommandons d’exécuter un basculement test. Vous pouvez modifier ces limites via le support Microsoft Azure.
- **Planification de la capacité** : pour bénéficier d’instructions supplémentaires, utilisez le [planificateur de capacité pour Réplica Hyper-V](http://www.microsoft.com/en-in/download/details.aspx?id=39057).
- **Bande passante de réplication** : si vous disposez d’une bande passante de réplication insuffisante, notez les points suivants :
	- **ExpressRoute** : Site Recovery s’associe à des optimiseurs Microsoft Azure ExpressRoute et WAN, tels que Riverbed. [En savoir plus](http://blogs.technet.com/b/virtualization/archive/2014/07/20/expressroute-and-azure-site-recovery.aspx) sur ExpressRoute.
	- **Trafic de réplication** : Site Recovery exécute une réplication initiale intelligente uniquement à l’aide de blocs de données, pas de l’intégralité du disque dur virtuel. Seules les modifications sont répliquées au cours de la réplication en continu.
	- **Trafic réseau** : pour contrôler le trafic réseau utilisé pour la réplication, configurez le réseau de [Qualité de service (QoS) Windows](https://technet.microsoft.com/library/hh967468.aspx) à l’aide d’une stratégie basée sur l’adresse IP et le port de destination. En plus si vous répliquez vers Microsoft Azure Site Recovery à l’aide de l’agent de sauvegarde Microsoft Azure. Vous pouvez configurer la limitation de cet agent. [En savoir plus](https://support.microsoft.com/kb/3056159).
- **RTO** :si vous souhaitez mesurer l’objectif de délai de récupération procuré par Site Recovery, nous vous recommandons d’exécuter un basculement test et d’examiner les tâches de récupération afin d’analyser le délai d’exécution des opérations. Si vous effectuez un basculement vers Microsoft Azure, vous avez tout intérêt à automatiser l’ensemble des actions manuelles en intégrant avec Microsoft Azure Automation et les plans de récupération.
- **RPO** : Site Recovery prend en charge un objectif de point de récupération quasi synchrone lorsque vous répliquez vers Microsoft Azure. Ceci est valable uniquement si vous disposez d’une bande passante suffisante entre votre centre de données et Microsoft Azure.

## Basculement
- **Interruption sur le site principal** : si vous répliquez à partir d’un centre de données local vers un autre et que les deux sont interrompus sur votre site principal, exécutez un basculement non planifié à partir du portail Site Recovery. Il n’est pas nécessaire de disposer d’une connectivité avec le centre de données principal pour exécuter le basculement.
- **Conserver l’adresse IP après le basculement vers le site secondaire** : si vous souhaitez conserver l’adresse IP d’une machine virtuelle source après son basculement vers un centre de données secondaire, suivez la procédure [suivante](http://blogs.technet.com/b/scvmm/archive/2014/04/04/retaining-ip-address-after-failover-using-hyper-v-recovery-manager.aspx).
- **Conserver l’adresse IP après le basculement vers Microsoft Azure** : pour spécifier l’adresse IP à attribuer à la machine virtuelle basculée, accédez à l’onglet de configuration de la machine virtuelle. En savoir plus sur la [configuration des propriétés réseau de la machine virtuelle](site-recovery-vmm-to-azure.md#step-8-enable-protection-for-virtual-machines)
- **Conserver l’adresse IP publique** : si vous souhaitez conserver une adresse IP publique après le basculement vers un site secondaire, Site Recovery vous le permet, à condition que votre ISP assure la prise en charge. Vous ne pouvez pas conserver une adresse IP publique après un basculement vers Microsoft Azure.
- **Conserver les adresses internes non RFC dans Microsoft Azure** : il est possible de conserver les espaces d’adresses 1918 non RFC après le basculement vers Microsoft Azure.
- **Basculement partiel vers le centre de données secondaire** : si vous basculez un site partiel vers votre centre de données secondaire et souhaitez vous connecter de nouveau à votre site principal, utilisez le modèle VPN site à site pour connecter une application basculée du site secondaire sur les composants d’infrastructure exécutés sur le site principal. Notez que si l’ensemble du sous-réseau est basculé, vous pouvez conserver l’adresse IP de la machine virtuelle. Si vous basculez un sous-réseau partiel, vous ne pouvez pas conserver l’adresse IP de la machine virtuelle, car les sous-réseaux ne peuvent pas être divisés entre plusieurs sites.
- **Basculement partiel vers Microsoft Azure** : si vous basculez un site partiel sur Microsoft Azure et souhaitez vous connecter de nouveau au site principal, utilisez le modèle VPN afin de connecter une application basculée de Microsoft Azure aux composants d’infrastructure exécutés sur le site principal. Notez que si l’ensemble du sous-réseau est basculé, vous pouvez conserver l’adresse IP de la machine virtuelle. Si vous basculez un sous-réseau partiel, vous ne pouvez pas conserver l’adresse IP de la machine virtuelle, car les sous-réseaux ne peuvent pas être divisés entre plusieurs sites.
- **Conserver la lettre de lecteur** : si vous souhaitez conserver la lettre de lecteur sur les machines virtuelles après le basculement, vous pouvez définir la stratégie SAN de la machine virtuelle sur **Activé**. [En savoir plus](https://support.microsoft.com/kb/3031135).
- **Acheminer les demandes des clients après le basculement vers Microsoft Azure** : Microsoft Azure Site Recovery fonctionne en parallèle avec Microsoft Azure Traffic Manager pour acheminer les demandes des clients vers votre application après le basculement. Vous pouvez utiliser des scripts dans les plans de récupération (avec Microsoft Azure Automation) afin d’effectuer les mises à jour DNS.

## Intégration

- **Intégration avec d’autres technologies de continuité d’activité et de récupération d’urgence** : Site Recovery s’intègre avec d’autres technologies de continuité d’activité et de récupération d’urgence. Pour la réplication basée sur les applications, nous vous recommandons d’utiliser SQL Server AlwaysOn pour répliquer les machines virtuelles exécutant la base de données, et Réplica Hyper-V pour les machines virtuelles frontales. AlwaysOn nécessite l’achat de licences SQL Server Enterprise pour votre site principal et votre site secondaire et l’exécution d’une machine virtuelle Microsoft Azure dans votre abonnement. La réplication intégrée de Site Recovery s’avère précieuse dans les scénarios où les applications sont soumises à des temps d’arrêt. Le cas échéant, nous vous recommandons d’utiliser Site Recovery pour orchestrer la réplication des bases de données, des applications et des machines virtuelles frontales. Cette approche vous permet de réaliser des économies sur la version requise de SQL Server, de réduire votre nombre de licences et de réduire le coût lié à la conservation des machines virtuelles exécutées en permanence dans Microsoft Azure.

## Étapes suivantes

Après avoir consulté ces meilleures pratiques, vous pouvez amorcer le déploiement de Site Recovery :

- [Configuration de la protection entre un site local VMM et Azure](site-recovery-vmm-to-azure.md)
- [Configuration de la protection entre un site local Hyper-V et Azure](site-recovery-hyper-v-site-to-azure.md)
- [Configuration de la protection entre des sites VMM locaux](site-recovery-vmm-to-vmm.md)
- [Configuration de la protection entre des sites locaux VMM avec SAN](site-recovery-vmm-san.md)
- [Configuration de la protection avec un seul serveur VMM](site-recovery-single-vmm.md)
 

<!---HONumber=August15_HO6-->