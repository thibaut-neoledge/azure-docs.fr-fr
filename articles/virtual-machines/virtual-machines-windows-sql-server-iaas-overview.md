<properties
    pageTitle="Présentation de SQL Server sur les machines virtuelles Azure | Microsoft Azure"
    description="Découvrez comment exécuter les éditions complètes de SQL Server sur les machines virtuelles Azure. Obtenez des liens directs vers toutes les images de machine virtuelle SQL Server et le contenu associé."
    services="virtual-machines-windows"
    documentationCenter=""
    authors="rothja"
    manager="jhubbard"
    editor=""
    tags="azure-service-management"/>

<tags
    ms.service="virtual-machines-windows"
    ms.devlang="na"
    ms.topic="get-started-article"
    ms.tgt_pltfrm="vm-windows-sql-server"
    ms.workload="infrastructure-services"
    ms.date="10/11/2016"
    ms.author="jroth"/>


# <a name="overview-of-sql-server-on-azure-virtual-machines"></a>Présentation de SQL Server sur les machines virtuelles Azure

Cette rubrique décrit les options d’exécution de SQL Server sur des machines virtuelles Azure et fournit à la fois [des liens vers des images de portail](#option-1-create-a-sql-vm-with-per-minute-licensing) et une vue d’ensemble des [tâches courantes](#manage-your-sql-vm).

>[AZURE.NOTE] Si vous connaissez déjà SQL Server et que vous souhaitez simplement savoir comment déployer une machine virtuelle SQL Server, consultez [Approvisionnement d’une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md).

## <a name="overview"></a>Vue d'ensemble
Si vous êtes administrateur de base de données ou développeur, les machines virtuelles Azure vous offrent un moyen de déplacer vers le cloud vos applications et charges de travail SQL Server locales. La vidéo suivante fournit une présentation technique des machines virtuelles SQL Server Azure.

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

La vidéo décrit les points suivants :

|Time|Domaine|
|---|---|
| 00:21 | Que sont les machines virtuelles Azure ? |
| 01:45 | Sécurité |
| 02:50 | Connectivité |
| 03:30 | Performances et fiabilité de stockage |
| 05:20 | Tailles de machine virtuelle |
| 05:54 | Haute disponibilité et SLA |
| 07:30 | Prise en charge de la configuration |
| 08:00 | Surveillance |
| 08:32 | Démo : Création d’une machine virtuelle SQL Server 2016 |

>[AZURE.NOTE] Cette vidéo est consacrée à SQL Server 2016, mais Azure fournit des images de machine virtuelle pour plusieurs versions de SQL Server, notamment 2008, 2012, 2014 et 2016. 

## <a name="understand-your-options"></a>Comprendre vos options
Vous pouvez choisir d’héberger vos données dans Azure pour de nombreuses raisons. Si votre application est déplacée dans Azure, cela améliore également les performances de migration des données. Mais il existe d’autres avantages. Vous avez automatiquement accès à plusieurs centres de données pour assurer les récupérations d’urgence et votre présence mondiale. Les données sont également parfaitement sécurisés et fiables.

L’exécution de SQL Server sur des machines virtuelles Azure vous permet de stocker des données relationnelles dans Azure. Le tableau suivant décrit de façon succincte les offres SQL sur Azure.

|&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;&nbsp;| Offre SQL | Description |
|---:|---|---|
|![SQL Server sur les machines virtuelles Azure](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-virtual-machine.png)|[SQL Server sur les machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/)|Exécutez SQL Server sur des machines virtuelles Azure (l’objet de la présente rubrique). Gérez directement la machine virtuelle et exécutez votre base de données sur les versions commerciales de SQL Server. |
|![Base de données SQL](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-database.png)|[Base de données SQL](https://azure.microsoft.com/services/sql-database/)|Utilisez le service Base de données SQL pour accéder à votre base de données et la faire évoluer sans avoir à gérer l’infrastructure sous-jacente.|
|![SQL Data Warehouse](./media/virtual-machines-windows-sql-server-iaas-overview/azure-sql-data-warehouse.png)|[SQL Data Warehouse](https://azure.microsoft.com/en-us/services/sql-data-warehouse/)|Utilisez Azure SQL Data Warehouse pour traiter de grandes quantités de données relationnelles et non relationnelles. Offre des fonctionnalités évolutives d’entreposage des données en tant que service.|
|![SQL Server Stretch Database](./media/virtual-machines-windows-sql-server-iaas-overview/sql-server-stretch-database.png)|[SQL Server Stretch Database](https://azure.microsoft.com/en-us/services/sql-server-stretch-database/)|Étendez dynamiquement les données transactionnelles en local de Microsoft SQL Server 2016 vers Azure.|

Avec ces différentes options, l’utilisation de SQL Server sur des machines virtuelles Azure est une solution idéale pour plusieurs scénarios. Par exemple, vous pouvez avoir intérêt à affecter à la machine virtuelle Azure une configuration aussi proche que possible de celle d’un ordinateur local SQL Server. Vous pouvez aussi souhaiter exécuter des applications et des services supplémentaires sur le même serveur de base de données. Ces deux ressources peuvent vous aider à bien peser d’autres facteurs de décision :

 - [SQL Server sur les machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/) fournit une vue d’ensemble des meilleurs scénarios pour l’utilisation de SQL Server sur des machines virtuelles Azure. 
 - [Choisir une option de SQL Server cloud : Base de données SQL Azure (PaaS) ou SQL Server sur des machines virtuelles Azure (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md) compare de manière détaillée l’exécution de SQL Database et de SQL Server sur une machine virtuelle.

## <a name="create-a-new-sql-vm"></a>Créer une nouvelle machine virtuelle SQL
Les sections suivantes fournissent des liens directs vers le portail Azure pour les images de la galerie de machines virtuelles SQL Server. Selon l’image que vous sélectionnez, vous pouvez soit payer les coûts de licence SQL Server à la minute, soit apporter votre propre licence (modèle BYOL).

Vous trouverez des instructions pas à pas pour ce processus dans le didacticiel [Approvisionnement d’une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md). Vous pouvez en outre consulter les [meilleures pratiques relatives aux performances de SQL Server dans les machines virtuelles Azure](virtual-machines-windows-sql-performance.md), qui expliquent comment sélectionner la taille de machine appropriée et les autres fonctionnalités disponibles lors de l’approvisionnement.

## <a name="option-1:-create-a-sql-vm-with-per-minute-licensing"></a>Option 1 : Créer une machine virtuelle SQL avec licence à la minute
Le tableau suivant récapitule les images liées à SQL Server actuellement disponibles dans la galerie de machines virtuelles Azure. Cliquez sur un lien pour commencer à créer une machine virtuelle SQL avec la version, l’édition et le système d’exploitation de votre choix.

|Version|Système d’exploitation|Édition|
|---|---|---|
|**SQL 2016**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [Developer](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2)|
|**SQL 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2)|
|**SQL 2014**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2)|
|**SQL 2012 SP3**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012)|
|**SQL 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)|
|**SQL 2008 R2 SP3**|Windows Server 2012|[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012)|

## <a name="option-2:-create-a-sql-vm-with-an-existing-license"></a>Option 2 : Créer une machine virtuelle SQL avec une licence existante
Vous pouvez également apporter votre propre licence (modèle BYOL). Dans ce scénario, vous payez uniquement pour la machine virtuelle sans frais supplémentaires pour la gestion de licences SQL Server. Pour utiliser votre propre licence, utilisez la matrice des versions, éditions et systèmes d’exploitation SQL Server ci-dessous. Dans le portail, le nom de ces images comporte le préfixe **{BYOL}**.

|Version|Système d’exploitation|Édition|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[BYOL Enterprise](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [BYOL Standard](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[BYOL Enterprise](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [BYOL Standard](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[BYOL Enterprise](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [BYOL Standard](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

> [AZURE.IMPORTANT] Pour utiliser des images de machines virtuelles BYOL, vous devez disposer d’un Contrat Entreprise avec [License Mobility via Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/). Vous devez également disposer d’une licence valide pour la version/l’édition de SQL Server que vous voulez utiliser. Vous devez [fournir les informations BYOL nécessaires à Microsoft](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) dans un délai de **10** jours à compter de l’approvisionnement de votre machine virtuelle.

## <a name="manage-your-sql-vm"></a>Gérer votre machine virtuelle SQL
Après avoir approvisionné votre machine virtuelle SQL Server, vous pouvez effectuer plusieurs tâches de gestion facultatives. Par bien des aspects, vous configurez et gérez SQL Server exactement comme vous le feriez avec une instance SQL Server locale. Toutefois, certaines tâches sont propres à Azure. Les sections suivantes illustrent certains de ces domaines avec des liens vers des informations supplémentaires.

### <a name="connect-to-the-vm"></a>Connexion à la machine virtuelle
Une des opérations de gestion les plus simples consiste à se connecter à votre machine virtuelle SQL Server à l’aide d’outils tels que SQL Server Management Studio (SSMS). Pour plus d’informations sur la façon de se connecter à votre nouvelle machine virtuelle SQL Server, consultez la page [Connexion à une machine virtuelle SQL Server sur Azure](virtual-machines-windows-sql-connect.md).

### <a name="migrate-your-data"></a>Migration de vos données
Si vous disposez d’une base de données existante, vous voudrez la déplacer vers la machine virtuelle SQL récemment approvisionnée. Pour obtenir la liste des options de migration ainsi que de l’aide, voir [Migration d’une base de données vers SQL Server sur une machine virtuelle Azure](virtual-machines-windows-migrate-sql.md).

### <a name="configure-high-availability"></a>Configurer la haute disponibilité
Si vous avez besoin d’une haute disponibilité, pensez à configurer les groupes de disponibilité SQL Server. Cela implique la présence de plusieurs machines virtuelles Azure dans un réseau virtuel. Le portail Azure dispose d'un modèle qui définit cette configuration pour vous. Pour plus d’informations, voir [Configurer un groupe de disponibilité AlwaysOn dans des machines virtuelles Azure Resource Manager](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). Si vous souhaitez configurer manuellement votre groupe de disponibilité et l’écouteur associé, voir [Configurer des groupes de disponibilité AlwaysOn sur une machine virtuelle Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Pour plus d’informations sur la haute disponibilité, consultez [Haute disponibilité et récupération d’urgence pour SQL Server sur des machines virtuelles Azure](virtual-machines-windows-sql-high-availability-dr.md).

### <a name="back-up-your-data"></a>Sauvegarde de vos données
Les machines virtuelles Azure peuvent tirer parti de la fonctionnalité [Sauvegarde automatisée](virtual-machines-windows-sql-automated-backup.md), qui crée à intervalles réguliers des sauvegardes de votre base de données dans Blob Storage. Vous pouvez utiliser cette technique manuellement. Pour plus d’informations, voir [Utilisation du stockage Azure pour la sauvegarde et la restauration de SQL Server](virtual-machines-windows-use-storage-sql-server-backup-restore.md). Pour une vue d’ensemble des options de sauvegarde et de restauration, voir [Sauvegarde et restauration de SQL Server dans les machines virtuelles Azure](virtual-machines-windows-sql-backup-recovery.md).

### <a name="automate-updates"></a>Automatiser les mises à jour
Les machines virtuelles Azure peuvent utiliser la fonctionnalité [Mise à jour corrective automatisée](virtual-machines-windows-sql-automated-patching.md) afin de planifier une fenêtre de maintenance pour l’installation automatique des mises à jour importantes de Windows et de SQL Server.

### <a name="customer-experience-improvement-program-(ceip)"></a>Programme d’amélioration du produit (CEIP)
Le Programme d’amélioration du produit est activé par défaut. Il transmet régulièrement des rapports à Microsoft afin de contribuer à améliorer SQL Server. Aucune tâche de gestion n’est requise dans le cadre de ce programme, sauf si vous souhaitez le désactiver après sa mise en service. Vous pouvez personnaliser ou désactiver le CEIP en vous connectant à la machine virtuelle avec le Bureau à distance. Exécutez ensuite l’utilitaire **Rapports d’erreurs et d’utilisation SQL Server** . Suivez les instructions pour désactiver la création de rapports. 

Pour plus d’informations, consultez la section CEIP de la rubrique [Accepter les termes du contrat de licence](https://msdn.microsoft.com/library/ms143343.aspx). 

## <a name="next-steps"></a>Étapes suivantes
[Découvrez le parcours d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) pour SQL Server sur les machines virtuelles Azure.

D’autres questions ? Tout d’abord, consultez le [Forum Aux Questions (FAQ) concernant SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-faq.md). Vous devez également ajouter vos questions ou commentaires au bas de l’une des rubriques relatives aux machines virtuelles SQL afin d’interagir avec Microsoft et la communauté.



<!--HONumber=Oct16_HO2-->


