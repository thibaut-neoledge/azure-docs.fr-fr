<properties
	pageTitle="Haute disponibilité et récupération d’urgence pour SQL Server | Microsoft Azure"
	description="Une analyse des différents types de stratégies HADR pour SQL Server s’exécutant dans des machines virtuelles Azure."
	services="virtual-machines-windows"
	documentationCenter="na"
	authors="MikeRayMSFT"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>
<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="04/05/2016"
	ms.author="mikeray" />

# Haute disponibilité et récupération d’urgence pour SQL Server dans Azure Virtual Machines

## Vue d’ensemble

Les machines virtuelles Microsoft Azure avec SQL Server permettent de réduire le coût d’une solution de base de données haute disponibilité et récupération d’urgence (HADR). La plupart des solutions HADR SQL Server sont prises en charge dans les machines virtuelles Azure, en tant que solutions Azure uniquement et solutions hybrides. Dans une solution Azure uniquement, le système HADR s’exécute dans Azure. Dans une configuration hybride, une partie de la solution est exécutée dans Azure, tandis que l’autre est exécutée localement dans votre organisation. La flexibilité de l’environnement Azure vous permet de migrer partiellement ou totalement vers Azure afin de répondre aux exigences HADR et en termes de budget de vos systèmes de base de données SQL Server.

[AZURE.INCLUDE [learn-about-deployment-models](../../includes/learn-about-deployment-models-both-include.md)]


## Compréhension du besoin d’une solution HADR

Il vous incombe de garantir que votre système de base de données possède les fonctions HADR requises par le contrat de niveau de service. Le fait que Azure fournisse des mécanismes haute disponibilité, comme le service de réparation pour les services cloud et la détection de la récupération après défaillance pour les machines virtuelles, n’est pas une garantie du respect du contrat de niveau de service souhaité. Ces mécanismes protègent la haute disponibilité des machines virtuelles, mais pas de SQL Server exécuté sur les machines virtuelles. Il est possible que l’instance SQL Server échoue pendant que la machine virtuelle est en ligne et saine. De plus, même les mécanismes haute disponibilité fournis par Azure tiennent compte des temps morts des machines virtuelles en raison d’événements tels que la récupération après une défaillance matérielle ou logicielle et des mises à niveau du système d’exploitation.

Par ailleurs, le stockage géo-redondant dans Azure (implémenté via la fonctionnalité de géo-réplication) peut ne pas être une solution de récupération d’urgence adaptée pour vos bases de données. Comme la géo-réplication envoie les données de manière asynchrone, il est possible que les mises à jour récentes soient perdues en cas de sinistre. La section [Géo-réplication des données et des fichiers journaux non prise en charge sur des disques distincts](#geo-replication-support) contient des informations supplémentaires concernant les limitations de géo-réplication.

## Architectures de déploiement HADR

Les technologies HADR SQL Server prises en charge dans Azure incluent :

- [Groupes de disponibilité AlwaysOn](https://technet.microsoft.com/library/hh510230.aspx)
- [Mise en miroir de bases de données](https://technet.microsoft.com/library/ms189852.aspx)
- [Copie des journaux de transaction](https://technet.microsoft.com/library/ms187103.aspx)
- [Sauvegarde et restauration avec le service de stockage d’objets blob Azure](https://msdn.microsoft.com/library/jj919148.aspx)
- [Instances de cluster de basculement AlwaysOn](https://technet.microsoft.com/library/ms189134.aspx)

Il est possible de combiner les technologies pour implémenter une solution SQL Server qui a des fonctions de haute disponibilité et de récupération d’urgence. Selon la technologie que vous utilisez, un déploiement hybride peut nécessiter un tunnel VPN avec le réseau virtuel Azure. Les sections ci-dessous illustrent certains exemples d’architectures de déploiement.

## Azure uniquement : solutions de haute disponibilité

Disposez d’une solution haute disponibilité pour vos bases de données SQL Server dans Azure à l’aide des groupes de disponibilité AlwaysOn ou de la mise en miroir de bases de données.

|Technology|Exemples d’architecture|
|---|---|
|**Groupes de disponibilité AlwaysOn**|Tous les réplicas de disponibilité exécutés dans les machines virtuelles Azure pour la haute disponibilité dans la même région. Vous devez configurer une machine virtuelle de contrôleur de domaine, car le clustering de basculement Windows Server (WSFC) nécessite un domaine Active Directory.<br/> ![Groupes de disponibilité AlwaysOn](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_always_on.gif)<br/>Pour plus d’informations, consultez [Configuration de groupes de disponibilité AlwaysOn dans Azure (GUI)](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md).|**Mise en miroir de bases de données**|Serveurs principal, miroir et témoin s’exécutant tous dans le même centre de données Azure pour la haute disponibilité. Déployez avec un contrôleur de domaine.<br/>![Mise en miroir de bases de données](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_dbmirroring1.gif)<br/>Vous pouvez également déployer la même configuration de mise en miroir de bases de données sans contrôleur de domaine en utilisant des certificats de serveur à la place.<br/>![Mise en miroir de bases de données](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_ha_dbmirroring2.gif)|
|**Instances de cluster de basculement AlwaysOn**|Les instances de cluster de basculement (FCI) qui nécessitent un stockage partagé, peuvent être créées de 2 manières.<br/><br/>1. Une instance de cluster de basculement sur un WSFC à deux nœuds exécutée dans des machines virtuelles Azure, avec le stockage pris en charge par une solution de clustering tierce. Pour un exemple spécifique utilisant SIOS DataKeeper, consultez [Haute disponibilité pour un partage de fichiers à l’aide de WSFC et du logiciel tiers SIOS Datakeeper](https://azure.microsoft.com/blog/high-availability-for-a-file-share-using-wsfc-ilb-and-3rd-party-software-sios-datakeeper/).<br/><br/>2. Une instance de cluster de basculement sur un WSFC à deux nœuds exécutée dans des machines virtuelles Azure, avec le stockage de bloc partagé cible iSCSI distant via ExpressRoute. Par exemple, NPS (NetApp Private Storage) expose une cible iSCSI via ExpressRoute avec Equinix dans les machines virtuelles Azure.<br/><br/>Pour les solutions tierces de stockage partagé et de réplication de données, vous devez contacter le fournisseur pour tous les problèmes liés à l’accès aux données lors du basculement.<br/><br/>Notez que l’utilisation des instances de cluster de basculement dans [Azure File Storage](https://azure.microsoft.com/services/storage/files/) n’est pas encore prise en charge, car cette solution n’utilise pas Premium Storage. Nous travaillons actuellement à mettre en place cette prise en charge.|

## Azure uniquement : solutions de récupération d’urgence

Disposez d’une solution de récupération d’urgence pour vos bases de données SQL Server dans Azure à l’aide de groupes de disponibilité AlwaysOn, de la mise en miroir de bases de données ou de sauvegarde et restauration avec des objets blob de stockage.

|Technology|Exemples d’architecture|
|---|---|
|**Groupes de disponibilité AlwaysOn**|Réplicas de disponibilité exécutés dans plusieurs centres de données sur les machines virtuelles Azure pour la récupération d’urgence. Cette solution inter-régions empêche l’indisponibilité totale du site. <br/> ![Groupes de disponibilité AlwaysOn](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_alwayson.png)<br/>Au sein d’une région, tous les réplicas doivent se trouver dans le même service cloud et le même réseau virtuel. Comme chaque région aura un réseau virtuel distinct, ces solutions requièrent la connectivité de réseau virtuel à réseau virtuel. Pour plus d’informations, voir [Configurer un VPN de site à site dans le portail Azure Classic](../vpn-gateway/vpn-gateway-site-to-site-create.md).|
|**Mise en miroir de bases de données**|Serveurs principal et miroir s’exécutant dans des centres de données différents pour la récupération d’urgence. Vous devez déployer à l’aide de certificats de serveur, car un domaine Active Directory ne peut pas couvrir plusieurs centres de données.<br/>![Mise en miroir de bases de données](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_dbmirroring.gif)|
|**Sauvegarde et restauration avec le service de stockage d’objets blob Azure**|Bases de données de production sauvegardées directement dans le stockage d’objets blob dans un autre centre de données pour la récupération d’urgence.<br/>![Sauvegarde et restauration](./media/virtual-machines-windows-sql-high-availability-dr/azure_only_dr_backup_restore.gif)<br/>Pour plus d’informations, voir [Sauvegarde et restauration de SQL Server dans les machines virtuelles Azure](virtual-machines-windows-sql-backup-recovery.md).|

## Informatique hybride : solutions de récupération d’urgence

Disposez d’une solution de récupération d’urgence pour vos bases de données SQL Server dans un environnement informatique hybride utilisant des groupes de disponibilité AlwaysOn, la mise en miroir de bases de données, la copie des journaux de transaction et la sauvegarde et la restauration avec le stockage d’objets blob Azure.

|Technology|Exemples d’architecture|
|---|---|
|**Groupes de disponibilité AlwaysOn**|Certains réplicas de disponibilité s’exécutant dans les machines virtuelles Azure et d’autres réplicas s’exécutant sur site pour la récupération d’urgence entre sites. Le site de production peut être local ou situé dans un centre de données Azure.<br/>![Groupes de disponibilité AlwaysOn](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_alwayson.gif)<br/>Étant donné que tous les réplicas de disponibilité doivent être dans le même cluster WSFC, ce dernier doit couvrir les deux réseaux (un cluster WSFC de plusieurs sous-réseaux). Cette configuration nécessite une connexion VPN entre Azure et le réseau local.<br/><br/>Pour une récupération d’urgence réussie de vos bases de données, vous devez également installer un contrôleur de domaine de réplica sur le site de récupération d’urgence.<br/><br/>Il est possible d’utiliser l’Assistant Ajouter un réplica dans SSMS pour ajouter un réplica Azure à un groupe de disponibilité AlwaysOn existant. Pour plus d’informations, consultez la page Didacticiel : Extension de vos groupes de disponibilité AlwaysOn à Azure.|
|**Mise en miroir de bases de données**|Un serveur partenaire exécuté sur une machine virtuelle Azure et l’autre exécuté sur site pour la récupération d’urgence entre sites utilisant des certificats de serveur. Les serveurs partenaires n’ont pas besoin d’être dans le même domaine Active Directory, et aucune connexion VPN n’est requise.<br/>![Mise en miroir de bases de données](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_dbmirroring.gif)<br/>Un autre scénario de mise en miroir de bases de données implique un serveur partenaire exécuté sur une machine virtuelle Azure et l’autre exécuté localement dans le même domaine Active Directory pour la récupération d’urgence entre sites. Une [connexion VPN entre le réseau virtuel Azure et le réseau local](../vpn-gateway/vpn-gateway-site-to-site-create.md) est requise.<br/><br/>Pour une récupération d’urgence réussie de vos bases de données, vous devez également installer un contrôleur de domaine de réplica sur le site de récupération d’urgence.|
|**Copie des journaux de transaction**|Un serveur exécuté sur une machine virtuelle Azure et l’autre exécuté localement pour la récupération d’urgence entre sites. La copie des journaux de transaction dépend du partage de fichiers Windows. Une connexion VPN entre le réseau virtuel Azure et le réseau local est donc requise.<br/>![Copie des journaux de transaction](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_log_shipping.gif)<br/>Pour une récupération d’urgence réussie de vos bases de données, vous devez également installer un contrôleur de domaine de réplica sur le site de récupération d’urgence.|
|**Sauvegarde et restauration avec le service de stockage d’objets blob Azure**|Bases de données de production locales sauvegardées directement dans le stockage d’objets blob Azure pour la récupération d’urgence.<br/>![Sauvegarde et restauration](./media/virtual-machines-windows-sql-high-availability-dr/hybrid_dr_backup_restore.gif)<br/>Pour plus d’informations, voir [Sauvegarde et restauration de SQL Server dans les machines virtuelles Azure](virtual-machines-windows-sql-backup-recovery.md).|

## Considérations importantes pour HADR SQL Server dans Azure

Les machines virtuelles Azure, le stockage et le réseau ont des caractéristiques opérationnelles différentes par rapport à celles d’une infrastructure informatique non virtualisée sur site. Pour une implémentation réussie d’une solution HADR SQL Server dans Azure, vous devez comprendre ces différences et concevoir votre solution de façon à les gérer.

### Nœuds haute disponibilité d’un groupe à haute disponibilité

Les groupes à haute disponibilité dans Azure vous permettent de placer les nœuds haute disponibilité dans des domaines d’erreur et des domaines de mise à niveau distincts. Pour les machines virtuelles Azure que vous devez placer dans le même groupe à haute disponibilité, vous devez les déployer dans le même service cloud. Seuls les nœuds du même service cloud peuvent faire partie du même groupe à haute disponibilité. Pour plus d’informations, voir [Gestion de la disponibilité des machines virtuelles](virtual-machines-windows-manage-availability.md).

### Comportement d’un cluster WSFC sur le réseau Azure

Le service DHCP non compatible RFC dans Azure peut entraîner l’échec de la création de certaines configurations de cluster WSFC, en raison du nom du réseau de clusters auquel est affecté une adresse IP en double (même adresse IP qu’un des nœuds de cluster). Cela constitue un problème lorsque vous implémentez des groupes de disponibilité AlwaysOn, qui dépendent de la fonctionnalité WSFC.

Examinez le scénario où un cluster à deux nœuds est créé et mis en ligne :

1. Le cluster est en ligne, puis NODE1 demande une adresse IP assignée dynamiquement pour le nom du réseau de clusters.

1. Aucune adresse IP autre que celle de NODE1 n’est fournie par le service DHCP, car celui-ci reconnaît que la demande provient de NODE1.

1. Windows détecte qu’une adresse en double est affectée à NODE1 et au nom du réseau de clusters, et le groupe de clusters par défaut n’est pas mis en ligne.

1. Le groupe de clusters par défaut est déplacé vers NODE2, qui traite l’adresse IP de NODE1 comme adresse IP du cluster et met le groupe de clusters par défaut en ligne.

1. Quand NODE2 tente d’établir la connexion avec NODE1, les paquets dirigés vers NODE1 ne quittent jamais NODE2, car il résout l’adresse IP de NODE1 en lui-même. NODE2 ne peut pas établir la connexion avec NODE1, puis perd le quorum et arrête le cluster.

1. Dans le même temps, NODE1 peut envoyer des paquets à NODE2, mais NODE2 ne peut pas répondre. NODE1 perd le quorum et arrête le cluster.

Ce scénario peut être évité en affectant une adresse IP statique inutilisée, telle qu’une adresse IP de lien local comme 169.254.1.1, au nom du réseau de clusters afin de mettre le nom du réseau de clusters en ligne. Pour simplifier ce processus, voir [Configuration d’un cluster de basculement Windows dans Azure pour les groupes de disponibilité AlwaysOn](http://social.technet.microsoft.com/wiki/contents/articles/14776.configuring-windows-failover-cluster-in-windows-azure-for-alwayson-availability-groups.aspx).

Pour plus d’informations, voir [Configuration de groupes de disponibilité AlwaysOn dans Azure (GUI)](virtual-machines-windows-classic-portal-sql-alwayson-availability-groups.md).

### Prise en charge de l’écouteur du groupe de disponibilité

Les écouteurs de groupe de disponibilité sont pris en charge sur les machines virtuelles Azure exécutant Windows Server 2008 R2, Windows Server 2012 et Windows Server 2012 R2. Cette prise en charge est rendue possible par l’utilisation de points de terminaison à charge équilibrée activés sur les machines virtuelles Azure qui sont des nœuds de groupe de disponibilité. Vous devez suivre des étapes de configuration spéciales de façon à ce que les écouteurs fonctionnent avec les applications clientes exécutées dans Azure et avec celles qui s’exécutent sur site.

Il existe deux options principales de configuration de votre écouteur : externe (public) ou interne. Le port d'écoute externe (public) est associé à une adresse IP virtuelle publique (VIP) accessible via Internet. Avec un écouteur externe, vous devez activer le retour direct du serveur, ce qui signifie que vous devez vous connecter à l'écouteur à partir d'un ordinateur qui n'est pas dans le même service cloud que les nœuds du groupe de disponibilité AlwaysOn. Vous pouvez également utiliser un écouteur interne avec équilibrage de charge interne (ILB). Un écouteur interne prend uniquement en charge les clients qui se trouvent dans le même réseau virtuel.

Si le groupe de disponibilité s’étend sur plusieurs sous-réseaux Azure (comme un déploiement qui traverse les régions Azure), la chaîne de connexion du client doit inclure « **MultisubnetFailover=True** ». Ainsi des tentatives de connexion parallèle aux réplicas sont générées dans les différents sous-réseaux. Pour obtenir des instructions sur la configuration d'un port d'écoute, consultez

- [Configuration d'un écouteur à équilibrage de charge interne pour des groupes de disponibilité AlwaysOn dans Azure](virtual-machines-windows-classic-ps-sql-int-listener.md)
- [Configuration d'un écouteur externe pour des groupes de disponibilité AlwaysOn dans Azure](virtual-machines-windows-classic-ps-sql-ext-listener.md)

Vous pouvez encore vous connecter à chaque réplica de disponibilité séparément en vous connectant directement à l’instance de service. En outre, puisque les groupes de disponibilité AlwaysOn sont à compatibilité descendante avec les clients de mise en miroir de bases de données, vous pouvez vous connecter aux réplicas de disponibilité comme les serveurs partenaires de mise en miroir de bases de données tant que les réplicas sont configurés de façon similaire à la mise en miroir de bases de données :

- Un réplica principal et un réplica secondaire

- Le réplica secondaire est configuré comme non lisible (option **Secondaire accessible en lecture** définie sur **Non**)

Voici un exemple de chaîne de connexion cliente, qui correspond à cette configuration apparentée à une mise en miroir de bases de données, à l’aide d’ADO.NET ou de SQL Server Native Client :

	Data Source=ReplicaServer1;Failover Partner=ReplicaServer2;Initial Catalog=AvailabilityDatabase;

Pour plus d’informations sur la connectivité client, consultez :

- [Utilisation de mots clés de chaîne de connexion avec SQL Server Native Client](https://msdn.microsoft.com/library/ms130822.aspx)
- [Connecter des clients à une session de mise en miroir de bases de données (SQL Server)](https://technet.microsoft.com/library/ms175484.aspx)
- [Connexion à l’écouteur du groupe de disponibilité dans un environnement hybride](http://blogs.msdn.com/b/sqlalwayson/archive/2013/02/14/connecting-to-availability-group-listener-in-hybrid-it.aspx)
- [Écouteurs de groupe de disponibilité, connectivité client et basculement d’application (SQL Server)](https://technet.microsoft.com/library/hh213417.aspx)
- [Utilisation de chaînes de connexion de mise en miroir de bases de données avec des groupes de disponibilité](https://technet.microsoft.com/library/hh213417.aspx)

### Latence du réseau dans un environnement hybride

Vous devez déployer votre solution HADR en partant du principe qu’il peut y avoir des périodes de latence réseau élevée entre votre réseau local et Azure. Lorsque vous déployez des réplicas sur Azure, vous devez utiliser la validation asynchrone au lieu de la validation synchrone pour le mode de synchronisation. Lorsque vous déployez des serveurs de mise en miroir de bases de données sur site et dans Azure, utilisez le mode haute performance plutôt que le mode haute sécurité.

### Prise en charge de la géo-réplication

La géo-réplication dans les disques Azure ne prend pas en charge le fichier de données et le fichier journal de la même base de données à stocker sur des disques distincts. GRS réplique les modifications sur chaque disque indépendamment et de manière asynchrone. Ce mécanisme garantit l’ordre d’écriture dans un seul disque sur la copie géo-répliquée, mais pas entre les copies géo-répliquées de plusieurs disques. Si vous configurez une base de données pour stocker le fichier de données et le fichier journal sur les disques distincts, les disques récupérés après sinistre peuvent contenir une copie plus à jour du fichier de données que le fichier journal, ce qui interrompt l’écriture préalable du journal dans SQL Server et des propriétés ACID des transactions. Si vous n’avez pas l’option de désactiver la géo-réplication sur le compte de stockage, vous devez conserver tous les fichiers de données et fichiers journaux pour une base de données spécifique sur le même disque. Si vous devez utiliser plusieurs disques en raison de la taille de la base de données, vous devez déployer une des solutions de récupération d’urgence répertoriées ci-dessus pour assurer la redondance des données.

## Étapes suivantes

Si vous devez créer une machine virtuelle Azure avec SQL Server, voir [Approvisionnement d’une machine virtuelle SQL Server dans Azure](virtual-machines-windows-portal-sql-server-provision.md).

Pour obtenir des performances optimales de SQL Server dans Azure, voir les indications de [Meilleures pratiques relatives aux performances de SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-performance.md).

Pour d’autres rubriques relatives à l’utilisation de SQL Server sur des machines virtuelles Azure, voir [SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-overview.md).

### Autres ressources

- [Installation d’une nouvelle forêt Active Directory dans Azure](../active-directory/active-directory-new-forest-virtual-machine.md)
- [Création du cluster WSFC pour les groupes de disponibilité AlwaysOn dans une machine virtuelle Azure ](http://gallery.technet.microsoft.com/scriptcenter/Create-WSFC-Cluster-for-7c207d3a)

<!---HONumber=AcomDC_0413_2016-->