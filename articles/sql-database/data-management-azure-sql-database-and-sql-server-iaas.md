<properties
	pageTitle="SQL (PaaS) Database ou SQL Server dans le cloud sur les machines virtuelles (IaaS) | Microsoft Azure"
	description="Découvrez l’option SQL Server cloud adaptée à votre application : Azure SQL (PaaS) Database ou SQL Server sur les machines virtuelles Azure."
	services="sql-database, virtual-machines"
	keywords="Cloud SQL Server, SQL Server dans le cloud, base de données PaaS, cloud SQL Server, DBaaS"
	documentationCenter=""
	authors="carlrabeler"
	manager="jhubbard"
	editor="cjgronlund"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="06/21/2016"
	ms.author="carlrab"/>

# Choisir une option de SQL Server cloud : Base de données SQL Azure (PaaS) ou SQL Server sur des machines virtuelles Azure (IaaS)

Azure propose deux options pour héberger des charges de travail SQL Server dans Microsoft Azure :

* [Azure SQL Database](https://azure.microsoft.com/services/sql-database/): base de données SQL native du cloud, également appelée base de données PaaS (plateforme en tant que service) ou DBaaS (base de données en tant que service) optimisée pour le développement d’applications SaaS (logiciel en tant que service). Elle est compatible avec la plupart des fonctionnalités de SQL Server.
* [SQL Server sur les machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/): SQL Server installé et hébergé dans le cloud sur des machines virtuelles Windows Server s’exécutant sur Azure, également appelé infrastructure en tant que service (IaaS).

Découvrez comment chaque option s’inscrit dans la plate-forme de données de Microsoft et obtenez de l’aide sur l’option qui répond aux besoins de votre entreprise. Que vous privilégiiez les économies ou une administration minimale, cet article peut vous aider à déterminer l’approche adaptée en fonction des exigences clients essentielles.


## Plateforme de données de Microsoft

L'une des premières choses à comprendre dans toute discussion comparant les bases de données Azure et SQL Server locales est que vous pouvez tout utiliser. La plateforme de données de Microsoft utilise la technologie SQL Server et la rend disponible sur les machines physiques locales, les environnements de cloud privé, les environnements de cloud privé hébergé par un tiers et le cloud public. Cela vous permet de répondre à des besoins spécifiques et différents via une combinaison de déploiements locaux et hébergés dans le cloud, tout en utilisant le même ensemble de produits serveur, d'outils de développement et de savoir-faire dans ces environnements.

   ![Options SQL Server Cloud : SQL server sur IaaS ou base de données SQL SaaS dans le cloud.](./media/data-management-azure-sql-database-and-sql-server-iaas/SQLIAAS_SQL_Server_Cloud_Continuum.png)

Comme indiqué sur le diagramme, chaque offre peut être caractérisée par le niveau d'administration dont vous disposez sur l'infrastructure (sur l'axe des X) et par le degré d'efficacité opérationnelle obtenue par consolidation au niveau de la base de données et par automatisation (sur l'axe des Y).

Lorsque vous concevez une application, quatre options de base sont disponibles pour l'hébergement de sa partie SQL Server :

- SQL Server sur des machines physiques non virtualisées
- SQL Server sur des machines virtualisées locales (cloud privé)
- SQL Server sur Azure Virtual Machines (cloud public Microsoft)
- Base de données SQL Azure (cloud public Microsoft)

Dans les sections suivantes, nous allons découvrir SQL Server dans le cloud public Microsoft : Base de données SQL Azure et SQL Server sur Azure Virtual Machines. Nous allons également explorer les motivations commerciales courantes permettant de déterminer l'option qui convient le mieux à votre application.

## Étude détaillée d’Azure SQL Database et de SQL Server sur les machines virtuelles Azure

La **base de données SQL Azure** est une base de données DBaaS relationnelle hébergée dans le cloud Azure, qui appartient aux catégories *Logiciel en tant que service (SaaS)* et *Plateforme en tant que service (PaaS)*. Le service [Base de données SQL](sql-database-technical-overview.md) repose sur du matériel et des logiciels standardisés, qui sont détenus, hébergés et gérés par Microsoft. Avec SQL Database, vous pouvez développer directement sur le service à l'aide de fonctionnalités intégrées. Lorsque vous utilisez SQL Database, vous payez à l’utilisation, avec des possibilités de mise à l’échelle pour obtenir plus de puissance sans interruption du service.

**SQL Server sur les machines virtuelles Azure** est une *infrastructure en tant que service (IaaS)* et vous permet d’exécuter SQL Server dans une machine virtuelle résidant dans le cloud. Comme pour SQL Database, elle repose sur du matériel standardisé détenu, hébergé et géré par Microsoft. Lorsque vous utilisez SQL Server sur une machine virtuelle, vous pouvez appliquer votre propre licence SQL Server ou utiliser une image SQL Server déjà sous licence du portail Azure.

En général, ces deux options SQL sont optimisées à différentes fins :

- L’option **SQL Database** est optimisée pour l’approvisionnement et la gestion de nombreuses bases de données à moindre coût. Elle réduit les coûts d’administration, car vous n’avez plus à gérer les machines virtuelles, le système d’exploitation et le logiciel de base de données. Sont également incluses les mises à niveau, la haute disponibilité et les [sauvegardes](sql-database-automated-backups.md). En règle générale, Azure SQL Database augmente considérablement le nombre de bases de données gérées par un informaticien ou un développeur.
- L’option **SQL Server sur les machines virtuelles Azure** est optimisée pour étendre les applications SQL Server locales vers le cloud dans un scénario hybride ou déployer une application sur Azure dans un scénario de migration ou de développement/test. Un scénario hybride, par exemple, consiste à conserver les réplicas de la base de données dans Azure à l’aide de réseaux [Azure Virtual Network](../virtual-network/virtual-networks-overview.md). Avec SQL Server sur les machines virtuelles Azure, vous disposez de droits d’administration complets sur une instance SQL Server dédiée et sur une machine virtuelle dans le cloud. C'est le choix idéal lorsqu'une organisation possède déjà les ressources informatiques disponibles pour maintenir les machines virtuelles. Avec SQL Server sur les machines virtuelles, vous pouvez créer un système hautement personnalisé qui répond aux exigences de performance de disponibilité de votre application.

Le tableau suivant résume les principales caractéristiques de SQL Database et de SQL Server sur les machines virtuelles Azure :

| | Base de données SQL | SQL Server sur Azure Virtual Machines|
| -------------- | ------------ | ---------------------- |
| **Idéal pour :** | Nouvelles applications conçues pour le cloud qui ont des contraintes de temps de développement et de marketing. | Applications existantes qui requièrent une migration rapide vers le cloud avec un minimum de modifications. Scénarios de développement et de test rapides lorsque vous ne souhaitez pas acheter du matériel SQL Server local non destiné à la production. |
|| Applications qui nécessitent des mécanismes intégrés de haute disponibilité, de récupération d’urgence et de mise à niveau. | Applications qui ne nécessitent pas de mécanismes intégrés de haute disponibilité, de récupération d’urgence et de mise à niveau. |
||Équipes qui ne souhaitent pas gérer le système d’exploitation sous-jacent et les paramètres de configuration.| Si vous avez besoin d'un environnement informatique personnalisé avec des droits d'administration complets.|
||Bases de données de moins de 1 To ou bases de données plus volumineuses pouvant être [partitionnées horizontalement ou verticalement](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling) à l’aide d’un modèle de scale-out.|Bases de données de plus de 1 To qui ne peuvent pas être [partitionnées horizontalement ou verticalement](sql-database-elastic-scale-introduction.md#horizontal-and-vertical-scaling).|
||[Création d’applications SaaS (logiciel en tant que service)](sql-database-design-patterns-multi-tenancy-saas-applications.md).| Création d’applications hybrides.|
|||||
|**Ressources :**|Vous ne souhaitez pas utiliser les ressources informatiques pour le support et la maintenance de l’infrastructure sous-jacente, mais vous voulez vous concentrer sur la couche Application.|Vous disposez des ressources informatiques pour le support et la maintenance.|
|**Coût total de possession :**|Élimine les coûts matériels et réduit les coûts d’administration.|Élimine les coûts matériels.|
|**Continuité des activités :**|Outre les fonctionnalités intégrées d’infrastructure de tolérance de panne, Base de données SQL Azure fournit des fonctionnalités telles que la [limite de restauration dans le temps](sql-database-point-in-time-restore.md), la [géorestauration](sql-database-geo-restore.md) et la [géo-réplication active](sql-database-active-geo-replication.md) afin d’améliorer la continuité des activités. Pour plus d’informations, voir [Vue d’ensemble de la continuité des activités de la base de données SQL Azure](sql-database-business-continuity.md).|L’option SQL Server sur les machines virtuelles Azure vous permet de configurer une solution de haute disponibilité et de récupération d’urgence pour les besoins spécifiques de votre base de données. Vous pouvez donc disposer d'un système hautement optimisé pour votre application. Vous pouvez tester et exécuter des basculements par vous-même si nécessaire. Pour plus d’informations, voir [Haute disponibilité et récupération d’urgence pour SQL Server dans Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).|
|**Cloud hybride :**|Votre application locale peut accéder aux données d’Azure SQL Database.|Avec l’option SQL Server sur les machines virtuelles Azure, vous pouvez avoir des applications qui s’exécutent en partie dans le cloud et en partie localement. Par exemple, vous pouvez étendre votre réseau local et votre domaine Active Directory au cloud par le biais d’[Azure Virtual Network](../virtual-network/virtual-networks-overview.md). En outre, vous pouvez stocker des fichiers de données locaux dans Azure Storage en utilisant les [fichiers de données SQL Server dans Azure](http://msdn.microsoft.com/library/dn385720.aspx). Pour plus d’informations, consultez la rubrique [Introduction au cloud hybride SQL Server 2014](http://msdn.microsoft.com/library/dn606154.aspx).|
||Prend en charge la [réplication transactionnelle de SQL Server](https://msdn.microsoft.com/library/mt589530.aspx) en tant qu’abonné.|Prend en charge la [réplication transactionnelle SQL Server](https://msdn.microsoft.com/library/mt589530.aspx), la récupération d’urgence et les [réplicas AlwaysOn dans Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-high-availability-dr.md).|
|||||
|||||

## Critères pour choisir entre Azure SQL Database et SQL Server sur les machines virtuelles Azure

### Coût

Que vous soyez une start-up à court de liquidités ou une équipe dans une société établie qui subit de fortes contraintes budgétaires, le manque de capitaux est souvent le principal moteur pour décider du mode d'hébergement des bases de données. Dans cette section, nous aborderons d’abord les notions de base de facturation et de licence dans Azure pour ces deux options de base de données relationnelles : Azure SQL Database et SQL Server sur les machines virtuelles Azure. Nous verrons ensuite comment calculer le coût total de l'application.

#### Notions de base sur la facturation et les licences

**Base de données SQL** est vendu aux abonnés en tant que service, et non par le biais d’une licence, alors que la [gestion des licences SQL Server dans Azure Virtual Machines](../virtual-machines/virtual-machines-windows-sql-server-iaas-overview.md) requiert soit une licence SQL Server à la minute, soit votre propre licence par l’intermédiaire de Software Assurance.

Actuellement, **SQL Database** est disponible en plusieurs niveaux de service, tous facturés à un tarif horaire fixe en fonction du niveau de service et du niveau de performances choisis. Par ailleurs, vous êtes facturé pour le trafic internet sortant aux [tarifs de transfert de données](https://azure.microsoft.com/pricing/details/data-transfers/) standard. Les niveaux de service De base, Standard et Premium sont conçus pour fournir des performances prévisibles avec plusieurs niveaux de performances pour répondre aux exigences de pic de votre application. Vous pouvez changer de niveau de service et de niveau de performances pour répondre aux besoins de débit variés de votre application. Si votre base de données présente un volume élevé de transactions et doit prendre en charge de nombreux utilisateurs simultanément, nous vous recommandons d’opter pour le niveau de service Premium. Pour obtenir des informations à jour sur les niveaux de service pris en charge, consultez [Niveaux de service d’Azure SQL Database](sql-database-service-tiers.md). Vous pouvez également créer des [pools de base de données élastiques](sql-database-elastic-pool.md) pour partager des ressources de performances entre des instances de base de données.

Avec **SQL Database**, Microsoft configure, corrige et met à niveau automatiquement le logiciel de base de données, ce qui réduit vos coûts d’administration. En outre, ses fonctionnalités de [sauvegarde intégrée](sql-database-automated-backups.md) vous permettent de réaliser d’importantes économies, notamment si vous avez un grand nombre de bases de données.

Avec **SQL Server sur Azure Virtual Machines**, vous pouvez utiliser soit l’image SQL Server fournie par la plateforme (qui inclut une licence), soit votre licence SQL Server. Lorsque vous utilisez les images fournies par Azure, les coûts opérationnels dépendent de la taille de la machine virtuelle et de l’édition SQL Server choisie. Quelle que soit la taille de la machine virtuelle ou l’édition de SQL Server, vous payez à la minute l’utilisation de la licence de SQL Server et Windows Server, ainsi que l’utilisation du stockage Azure pour les disques de machine virtuelle. L’option de facturation à la minute vous permet d’utiliser SQL Server aussi longtemps que vous en avez besoin, sans acheter de licences SQL Server supplémentaires. Si vous utilisez votre propre licence SQL Server sur Azure, seuls les coûts de stockage et de Windows Server vous sont facturés. Pour plus d’informations sur l’utilisation de votre propre licence, consultez [License Mobility via Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/).

#### Calcul du coût total de l'application

Lorsque vous utilisez une plateforme cloud, le coût d’exécution de votre application comprend principalement les coûts de développement et d’administration, ainsi que les coûts de service nécessaires liés à la plateforme de cloud public.

Voici le calcul de coûts détaillé pour votre application exécutée dans SQL Database et SQL Server sur les machines virtuelles Azure :

**Avec Azure SQL Database :**

*Coût total de l’application = coûts d’administration très réduits + coûts de développement logiciel + coûts du service SQL Database*

**Avec SQL Server sur les machines virtuelles Azure :**

*Coût total de l’application = coûts réduits de modification/développement logiciel + coûts d’administration + coûts des licences SQL Server et Windows Server + coûts de stockage dans Azure*

Pour plus d'informations sur la tarification, consultez les ressources suivantes :

- [Tarification des bases de données SQL](https://azure.microsoft.com/pricing/details/sql-database/)
- [Tarification des machines virtuelles](https://azure.microsoft.com/pricing/details/virtual-machines/) pour [SQL](https://azure.microsoft.com/pricing/details/virtual-machines/#sql) et [Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#windows)
- [Calcul des coûts Azure](https://azure.microsoft.com/pricing/calculator/)

> [AZURE.NOTE] Dans SQL Serveur, quelques fonctionnalités ne sont pas applicables ou disponibles avec SQL Database. Pour plus d’informations, consultez [Consignes et limitations générales de base de données SQL Azure](sql-database-general-limitations.md) et [Informations sur le langage Transact-SQL de la base de données SQL Azure](sql-database-transact-sql-information.md). Si vous déplacez une solution SQL Server vers le nuage, consultez [Migration d’une base de données SQL Server vers une base de données SQL Azure](sql-database-cloud-migrate.md). Lorsque vous migrez une application SQL Server locale vers SQL Database, nous vous recommandons de mettre à jour l’application pour tirer parti des fonctionnalités des services cloud. Par exemple, vous pouvez héberger votre couche applicative dans [Applications Web Azure](https://azure.microsoft.com/services/app-service/web/) ou des [Services cloud Azure](https://azure.microsoft.com/services/cloud-services/) pour accroître votre rentabilité.

### Administration

Pour de nombreuses entreprises, la décision de migrer vers un service cloud vise essentiellement à simplifier la complexité d’administration, qui a un coût. Avec **SQL Database**, Microsoft administre le matériel physique, réplique automatiquement toutes les données pour assurer la haute disponibilité, configure et met à niveau le logiciel de base de données, gère l’équilibrage de charge et procède au basculement transparent en cas de défaillance du serveur. Vous pouvez continuer d’administrer votre base de données, mais vous n’avez plus à gérer le moteur de base de données, ni le système d’exploitation ou le matériel du serveur. Parmi les éléments que vous pouvez continuer d’administrer, il y a les bases de données et les connexions, l’optimisation des index et des requêtes, ainsi que l’audit et la sécurité.

Par ailleurs, vous pouvez avoir les compétences en interne et souhaiter garder la main sur l’emplacement de la base de données ainsi que le disque. Avec **SQL Server sur les machines virtuelles Azure**, vous avez le contrôle sur le système d’exploitation et la configuration de l’instance SQL Server. Avec une machine virtuelle, c’est à vous de décider quand mettre à jour ou à niveau le système d’exploitation et le logiciel de base de données, et quand installer d’autres logiciels tels que les outils de sauvegarde et de protection antivirus. En outre, vous pouvez contrôler la taille de la machine virtuelle, le nombre de disques et leurs configurations de stockage. Par exemple, Azure vous permet de modifier la taille d’une machine virtuelle en fonction des besoins. Pour plus d’informations, consultez [Tailles de machines virtuelles et de services cloud pour Azure](../virtual-machines/virtual-machines-linux-sizes.md).

### Contrat de Niveau de Service (SLA)

Pour bon nombre de services informatiques, répondre aux obligations de temps d’exécution d’un contrat de niveau de service (SLA) est la priorité absolue. Dans cette section, nous allons détailler les implications du contrat SLA pour chaque option d’hébergement de base de données.

Pour **Azure SQL Database**, avec les niveaux de service De base, Standard et Premium, Microsoft fournit un contrat SLA dont la disponibilité est de 99,99 %. Pour obtenir les dernières informations, consultez [Contrats de niveau de service](https://azure.microsoft.com/support/legal/sla/sql-database/). Pour obtenir les dernières informations sur les niveaux de service de SQL Database et les plans de continuité des activités pris en charge, consultez [Niveaux de service](sql-database-service-tiers.md).

Pour **SQL Server sur les machines virtuelles Azure**, Microsoft fournit un SLA avec une disponibilité de 99,95 %, qui ne couvre que la machine virtuelle. Ce contrat SLA ne couvre pas les processus (comme SQL Server) exécutés sur la machine virtuelle et nécessite l’hébergement d’au moins deux instances de machine virtuelle dans un groupe à haute disponibilité. Pour plus d’informations, consultez le [contrat SLA de machine virtuelle](https://azure.microsoft.com/support/legal/sla/virtual-machines/). Pour obtenir une haute disponibilité de la base de données dans les machines virtuelles, vous devez configurer une des options de haute disponibilité prises en charge dans SQL Server, comme les [groupes de disponibilité AlwaysOn](http://blogs.technet.com/b/dataplatforminsider/archive/2014/08/25/sql-server-alwayson-offering-in-microsoft-azure-portal-gallery.aspx).

### <a name="market"></a>Délai de mise sur le marché

**SQL Database** est la solution idéale pour les applications cloud lorsque la productivité des développeurs et la rapidité de mise sur marché sont essentielles. Avec les fonctionnalités de programmation comme le DBA, il est parfait pour les architectes et les développeurs du cloud, car il tempère la nécessité de gérer le système d'exploitation et la base de données sous-jacents. Par exemple, vous pouvez utiliser l’[API REST](http://msdn.microsoft.com/library/azure/dn505719.aspx) et des [applets de commande PowerShell](http://msdn.microsoft.com/library/azure/dn546726.aspx) pour automatiser et gérer les opérations administratives de milliers de bases de données. Les fonctionnalités, telles que les [pools de base de données élastiques](sql-database-elastic-pool.md), vous permettent de vous concentrer sur la couche applicative et de mettre votre solution sur le marché plus rapidement.

L’option **SQL Server sur les machines virtuelles Azure** est idéale si vos applications requièrent un accès à et un contrôle sur toutes les fonctionnalités d’une instance de SQL Server. Elle convient également si vous souhaitez migrer des applications et des bases de données locales en l’état vers Azure. Étant donné que vous n'avez pas besoin de modifier la présentation, l'application et les couches de données, vous économisez en temps et en budget sur le remaniement de votre solution existante. Ainsi, vous pouvez vous concentrer sur la migration de toutes vos solutions vers Azure et sur l’optimisation des performances requises par la plateforme Azure. Pour plus d’informations, consultez [Meilleures pratiques relatives aux performances de SQL Server dans les machines virtuelles Azure](../virtual-machines/virtual-machines-windows-sql-performance.md).

## Résumé

Cet article a décrit les options SQL Database et SQL Server sur les machines virtuelles Azure, ainsi que les critères les plus courants susceptibles d’influencer votre décision. Voici un résumé des suggestions à prendre en compte :

Choisissez **Azure SQL Database** dans les cas suivants :

- Vous créez des applications cloud ou vous souhaitez migrer votre solution SQL Server pour réaliser des économies et profiter des fonctionnalités d’optimisation des performances offertes par les services cloud. Cette approche apporte les avantages d’un service cloud entièrement géré, permet de réduire le délai de mise sur le marché et optimise les à coûts long terme.

- Vous souhaitez que Microsoft effectue des opérations de gestion courantes sur vos bases de données et vous avez besoin d'une plus grande disponibilité offerte par le biais des contrats SLA pour les bases de données.



Choisissez **SQL Server sur les machines virtuelles Azure** dans les cas suivants :

- Vous avez des applications locales et vous ne souhaitez plus gérer votre propre matériel, ou vous envisagez des solutions hybrides. Cette approche vous permet d’étendre plus rapidement les capacités de vos bases de données, tout en facilitant la connexion à vos applications locales via un tunnel sécurisé.

- Vous avez des ressources informatiques, et vous avez besoin de droits d’administration complets sur SQL Server ainsi que d’une compatibilité totale avec la version de SQL Server locale. Cette approche vous permet de réduire les coûts de développement ou de modifications des applications existantes avec la possibilité d'exécuter la plupart des applications. En outre, elle fournit un contrôle intégral sur la machine virtuelle, le système d’exploitation et la configuration de la base de données.


## Étapes suivantes
- Pour plus d’informations sur la prise en main du service Base de données SQL, voir [Didacticiel sur la base de données SQL : Créer une base de données SQL en quelques minutes à l’aide du portail Azure](sql-database-get-started.md).
- Voir [Tarification de Base de données SQL](https://azure.microsoft.com/pricing/details/sql-database/).
- Pour plus d’informations sur la prise en main de SQL Server sur Azure Virtual Machines, voir [Approvisionnement d’une machine virtuelle SQL Server dans Azure](../virtual-machines/virtual-machines-windows-portal-sql-server-provision.md).
- Consultez [Parcours d’apprentissage SQL Server sur une machine virtuelle Azure](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/).

<!---HONumber=AcomDC_0622_2016-->