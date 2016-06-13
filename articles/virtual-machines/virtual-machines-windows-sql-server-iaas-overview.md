<properties
	pageTitle="Présentation de SQL Server sur des machines virtuelles | Microsoft Azure"
	description="Commencez par exécuter des bases de données SQL Server dans le cloud sur des machines virtuelles Azure. Ce modèle IaaS (infrastructure as a service) permet d’exécuter des charges de travail SQL Server dans Azure."
	services="virtual-machines-windows"
	documentationCenter=""
	authors="rothja"
	manager="jhubbard"
	editor=""
	tags="azure-service-management"/>

<tags
	ms.service="virtual-machines-windows"
	ms.devlang="na"
	ms.topic="article"
	ms.tgt_pltfrm="vm-windows-sql-server"
	ms.workload="infrastructure-services"
	ms.date="05/25/2016"
	ms.author="jroth"/>

# Vue d’ensemble de SQL Server dans Azure Virtual Machines

[SQL Server sur les machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) permet d’héberger vos bases de données SQL Server dans le cloud. Par exemple, vous pouvez migrer une base de données locale vers une machine virtuelle Azure préconfigurée avec Windows Server 2012 R2 et SQL Server 2014 Enterprise. Mais il existe de nombreux autres scénarios possibles, comme des configurations de plusieurs ordinateurs qui prennent en charge la haute disponibilité ou des architectures hybrides avec connexion à des réseaux locaux.

Pour une présentation, regardez la vidéo suivante :

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

## Offres SQL

L’exécution de SQL Server sur une machine virtuelle Azure est une option permettant de stocker des données relationnelles dans Azure. Le tableau suivant récapitule les différentes offres.

|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Offre SQL | Description |
|---:|---|---|
|![SQL Server sur les machines virtuelles Azure](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-virtual-machine.png)|[SQL Server sur les machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/)|Exécutez SQL Server sur les machines virtuelles Azure. Gérez directement la machine virtuelle et exécutez votre base de données sur les versions commerciales de SQL Server. |
|![Base de données SQL](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-database.png)|[Base de données SQL](https://azure.microsoft.com/services/sql-database/)|Utilisez le service Base de données SQL pour accéder à votre base de données et la faire évoluer sans avoir à gérer l’infrastructure sous-jacente.|
|![SQL Data Warehouse](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-data-warehouse.png)|[SQL Data Warehouse](https://azure.microsoft.com/services/sql-data-warehouse/)|Utilisez Azure SQL Data Warehouse pour traiter de grandes quantités de données relationnelles et non relationnelles. Offre des fonctionnalités évolutives d’entreposage des données en tant que service.|
|![SQL Server Stretch Database](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-stretch-database.png)|[SQL Server Stretch Database](https://azure.microsoft.com/services/sql-server-stretch-database/)|Étendez dynamiquement les données transactionnelles en local de Microsoft SQL Server 2016 vers Azure.|

>[AZURE.NOTE] Pour obtenir une comparaison détaillée entre les machines virtuelles SQL et Base de données SQL, consultez [Choisir une option de SQL Server cloud : Base de données SQL Azure (PaaS) ou SQL Server sur des machines virtuelles Azure (IaaS)](../sql-database/data-management-azure-sql-database-and-sql-server-iaas.md).

## Déployer une machine virtuelle SQL Server

Pour créer une machine virtuelle SQL Server dans Azure, vous devez d’abord obtenir un abonnement à la plateforme Azure. Vous pouvez acheter un abonnement Azure sur la page [Modes d’achat d’Azure](https://azure.microsoft.com/pricing/purchase-options/). Pour un essai gratuit, consultez la page [Évaluation gratuite d’Azure](https://azure.microsoft.com/pricing/free-trial/).

Après avoir souscrit à un abonnement, le moyen le plus simple de déployer une machine virtuelle SQL Server dans Azure consiste à [approvisionner une image de la galerie de machines SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md). Ces images comprennent les frais de licence de SQL Server dans la tarification relative à la machine virtuelle.

Il est important de noter qu’il existe deux modèles pour créer et gérer les machines virtuelles Azure : le modèle classique et Resource Manager. Pour la plupart des nouveaux déploiements, Microsoft recommande d’utiliser le modèle Resource Manager. Pour plus d’informations, consultez [Présentation du déploiement de Resource Manager et du déploiement classique](../resource-manager-deployment-model.md). Chaque rubrique doit indiquer clairement le modèle ciblé, sauf si elle s’applique à la fois à la version classique et à Resource Manager, comme cet article.

## Choisir une image de machine virtuelle SQL
Le tableau suivant récapitule les images liées à SQL Server actuellement disponibles dans la galerie de machines virtuelles Azure. Cliquez sur l’un des liens présents dans le tableau en fonction de la version, de l’édition et du système d’exploitation. Ensuite, cliquez sur le bouton **Créer une machine virtuelle** sur la page Marketplace.

|Version de SQL Server|Système d’exploitation|Édition SQL Server|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016rtmenterprisewindowsserver2012r2/), [Standard](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016rtmstandardwindowsserver2012r2/), [Web](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016rtmwebwindowsserver2012r2/), [Express](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2016rtmexpresswindowsserver2012r2/)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014sp1enterprisewindowsserver2012r2/), [Standard](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014sp1standardwindowsserver2012r2/), [Web](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014sp1webwindowsserver2012r2/), [Express](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014sp1expresswindowsserver2012r2/)|
|**SQL Server 2014**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014enterprisewindowsserver2012r2/), [Standard](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014standardwindowsserver2012r2/), [Web](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2014webwindowsserver2012r2/)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012r2/), [Standard](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012r2/), [Web](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012r2/)|
|**SQL Server 2012 SP2**|Windows Server 2012|[Enterprise](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2enterprisewindowsserver2012/), [Standard](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2standardwindowsserver2012/), [Web](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2012sp2webwindowsserver2012/), [Express](https://azure.microsoft.com//marketplace/partners/microsoft/sqlserver2012sp2expresswindowsserver2012/)|
|**SQL Server 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2008r2sp3enterprisewindowsserver2008r2/), [Standard](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2008r2sp3standardwindowsserver2008r2/), [Web](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2008r2sp3webwindowsserver2008r2/)|
|**SQL Server 2008 R2 SP3**|Windows Server 2012|[Express](https://azure.microsoft.com/marketplace/partners/microsoft/sqlserver2008r2sp3expresswindowsserver2012/)|

>[AZURE.NOTE] Le Programme d’amélioration du produit est activé par défaut. Si nécessaire, vous pouvez personnaliser ou désactiver le Programme d’amélioration du produit après la configuration de la machine virtuelle. Connectez-vous à la machine virtuelle avec le Bureau à distance et exécutez l’utilitaire **Rapports d’erreurs et d’utilisation SQL Server**.

Vous avez besoin d’aide pour créer une machine virtuelle SQL ? Consultez la procédure pas à pas détaillée dans le [didacticiel d’approvisionnement](virtual-machines-windows-portal-sql-server-provision.md).

En plus de ces images pré-configurées, vous pouvez également [créer une machine virtuelle Azure](virtual-machines-windows-hero-tutorial.md) sans que SQL Server soit pré-installé. Vous pouvez installer n’importe quelle instance de SQL Server pour laquelle vous disposez d’une licence. Vous migrez votre licence vers Azure pour exécuter SQL Server dans une machine virtuelle Azure en utilisant [License Mobility via Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/). Dans ce scénario, vous ne payez que pour les [coûts](https://azure.microsoft.com/pricing/details/virtual-machines/) de stockage et de calcul Azure associés à la machine virtuelle.

Pour déterminer les meilleurs paramètres de configuration de machine virtuelle pour votre image SQL Server, consultez les [Meilleures pratiques relatives aux performances de SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-performance.md). Pour les charges de travail de production, **DS3** est la taille minimale recommandée pour les machines virtuelles pour l’édition Enterprise de SQL Server. **DS2** est la taille minimale recommandée pour les machines virtuelles pour les charges de travail de production avec l’édition Standard.

## Migration de vos données

Une fois que votre machine virtuelle SQL Server est en cours d'exécution, vous pouvez souhaiter migrer des bases de données existantes vers la machine. Pour obtenir la liste des options de migration ainsi que des conseils, consultez [Migrer une base de données SQL Server vers SQL Server dans une machine virtuelle Azure](virtual-machines-windows-migrate-sql.md).

## Haute disponibilité

Si vous avez besoin d’une haute disponibilité, pensez à configurer les groupes de disponibilité SQL Server. Cela implique la présence de plusieurs machines virtuelles Azure dans un réseau virtuel. Le portail Azure dispose d'un modèle qui définit cette configuration pour vous. Pour plus d’informations, consultez [Configurer un groupe de disponibilité AlwaysOn dans des machines virtuelles Azure Resource Manager (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md).

Si vous souhaitez configurer manuellement votre groupe de disponibilité et l’écouteur associé, consultez [Configurer des groupes de disponibilité AlwaysOn dans Azure VM](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Pour plus d’informations sur la haute disponibilité, consultez [Haute disponibilité et récupération d’urgence pour SQL Server sur des machines virtuelles Azure](virtual-machines-windows-sql-high-availability-dr.md).

## Sauvegarde et restauration
Pour les bases de données sur site, Azure peut agir comme un centre de données secondaire pour stocker les fichiers de sauvegarde SQL Server. Pour une vue d'ensemble des options de sauvegarde et de restauration, voir [Sauvegarde et restauration de SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-backup-recovery.md).

La [sauvegarde SQL Server dans l’URL](https://msdn.microsoft.com/library/dn435916.aspx) stocke les fichiers de sauvegarde Azure dans le stockage d’objets blob Azure. La [sauvegarde gérée par SQL Server](https://msdn.microsoft.com/library/dn449496.aspx) vous permet de planifier la sauvegarde et la conservation dans Azure. Ces services peuvent être utilisés avec des instances de SQL Server sur site ou avec SQL Server s’exécutant sur des machines virtuelles Azure. Les machines virtuelles Azure peuvent également tirer parti de la [sauvegarde automatisée](virtual-machines-windows-classic-sql-automated-backup.md) et de la [mise à jour corrective automatisée](virtual-machines-windows-classic-sql-automated-patching.md) pour SQL Server.

## Étapes suivantes

Tout d’abord, [créez votre propre machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md).

Examinez ensuite les [bonnes pratiques de performance](virtual-machines-windows-sql-performance.md) et les [techniques de migration](virtual-machines-windows-migrate-sql.md) lorsque vous envisagez de déplacer vos charges de travail SQL Server vers des machines virtuelles Azure.

Vous avez d’autres questions sur SQL Server sur les machines virtuelles Azure ? Tout d’abord, consultez le [Forum Aux Questions (FAQ) concernant SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-faq.md). Vous devez également ajouter vos questions ou commentaires au bas de l’une des rubriques relatives aux machines virtuelles SQL afin d’interagir avec Microsoft et la communauté.

<!---HONumber=AcomDC_0601_2016-->