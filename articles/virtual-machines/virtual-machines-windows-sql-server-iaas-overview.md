<properties
	pageTitle="Premiers pas avec SQL Server sur Azure Virtual Machines | Microsoft Azure"
	description="Déplacez vos charges de travail de bases de données SQL Server locales vers le cloud avec Azure Virtual Machines. Prenez en main rapidement les images de machines virtuelles SQL préconfigurées."
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
	ms.date="07/22/2016"
	ms.author="jroth"/>

# Premiers pas avec SQL Server sur Azure Virtual Machines

Cette rubrique décrit les options dont vous disposez pour exécuter SQL Server sur Azure Virtual Machines, et fournit des conseils et des ressources pour bien démarrer.

Si vous êtes administrateur de base de données, vous cherchez peut-être à migrer vos charges de travail SQL Server locales vers le cloud. Ou, si vous êtes développeur, vous envisagez peut-être d’utiliser les fonctionnalités de base de données relationnelle de SQL Server pour votre application Microsoft Azure. Quel avantage présente l’exécution de charges de travail SQL Server dans Azure Virtual Machines ? La vidéo de présentation suivante décrit les avantages et fournit une vue d’ensemble technique.

> [AZURE.VIDEO data-driven-sql-server-2016-azure-vm-is-the-best-platform-for-sql-server-2016]

## Évaluation des avantages

Avant de commencer, évaluez d’abord l’intérêt de l’utilisation de SQL Server sur Azure Virtual Machines.

Si vous déplacez d’autres charges de travail vers Azure, par exemple une application d’entreprise, il est judicieux de déplacer également des bases de données SQL Server dépendantes vers Azure pour améliorer les performances. Toutefois, l’hébergement de SQL Server dans Azure Virtual Machines présente d’autres avantages. Par exemple, vous avez automatiquement accès à plusieurs centres de données pour assurer les récupérations d’urgence et votre présence mondiale. Pour une liste complète des scénarios et des avantages, consultez la [page produit SQL Server sur les machines virtuelles Azure](https://azure.microsoft.com/services/virtual-machines/sql-server/).

> [AZURE.NOTE] Lorsque vous évaluez SQL Server sur Azure Virtual Machines, passez également en revue les autres options de stockage et SQL sur Azure, comme [Base de données SQL](../sql-database/sql-database-technical-overview.md), [SQL Data Warehouse](../sql-data-warehouse/sql-data-warehouse-overview-what-is.md) et [SQL Server Stretch Database](../sql -server-stretch-database/sql-server-stretch-database-overview.md). Pour obtenir une comparaison détaillée, consultez la rubrique [Choisir une option de SQL Server cloud : Base de données SQL Azure (PaaS) ou SQL Server sur Azure Virtual Machines (IaaS)](../sql-database/sql-database-paas-vs-sql-server-iaas.md).

Une fois que vous décidez d’exécuter SQL Server sur Azure Virtual Machines, l’une de vos premières décisions sera d’utiliser ou non une image de machine virtuelle qui inclut les coûts de licence de SQL Server. Vous avez également la possibilité d’utiliser votre BYOL (apportez votre propre licence) et de payer seulement pour la machine virtuelle elle-même. Les deux sections suivantes décrivent ces options.

## Option 1 : Déployer une machine virtuelle SQL (gestion de licences par minute)
Le tableau suivant récapitule les images liées à SQL Server actuellement disponibles dans la galerie de machines virtuelles Azure. Cliquez sur un lien pour commencer à créer une machine virtuelle SQL avec la version, l’édition et le système d’exploitation de votre choix. Toutes les images incluent [les coûts des licences SQL Server](https://azure.microsoft.com/pricing/details/virtual-machines/#Sql).

Des instructions pas à pas sont disponibles dans le didacticiel [Approvisionnement d’une machine virtuelle SQL Server dans le portail Azure](virtual-machines-windows-portal-sql-server-provision.md). Vous pouvez en outre consulter les [Meilleures pratiques relatives aux performances de SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-performance.md), qui expliquent comment sélectionner la taille de machine appropriée et les autres fonctionnalités disponibles lors de l’approvisionnement.

|Version|Système d'exploitation|Édition|
|---|---|---|
|**SQL 2016**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMEnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMStandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMWebWindowsServer2012R2), [Developer](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMDeveloperWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2016RTMExpressWindowsServer2012R2)|
|**SQL 2014 SP1**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2014SP1ExpressWindowsServer2012R2)|
|**SQL 2014**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2014EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2014StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2014WebWindowsServer2012R2)|
|**SQL 2012 SP3**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3WebWindowsServer2012R2), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP3ExpressWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012R2)|
|**SQL 2012 SP2**|Windows Server 2012|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2EnterpriseWindowsServer2012), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2StandardWindowsServer2012), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2WebWindowsServer2012), [Express](https://portal.azure.com/#create/Microsoft.SQLServer2012SP2ExpressWindowsServer2012)|
|**SQL 2008 R2 SP3**|Windows Server 2008 R2|[Enterprise](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3EnterpriseWindowsServer2008R2), [Standard](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3StandardWindowsServer2008R2), [Web](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3WebWindowsServer2008R2)|
|**SQL 2008 R2 SP3**|Windows Server 2012|[Express](https://portal.azure.com/#create/Microsoft.SQLServer2008R2SP3ExpressWindowsServer2012)|

## Option 2 : Déployer une machine virtuelle SQL (BYOL)
Vous pouvez également choisir la solution BYOL (apportez votre propre licence). Dans ce scénario, vous payez uniquement pour la machine virtuelle sans frais supplémentaires pour la gestion de licences SQL Server. Pour utiliser votre propre licence, utilisez la matrice des versions, éditions et systèmes d’exploitation SQL Server ci-dessous. Dans le portail, les noms d’image comportent le préfixe **{BYOL}**.

> [AZURE.IMPORTANT] Pour utiliser les images BYOL de machines virtuelles, vous devez disposer d’un Contrat Entreprise avec [License Mobility via Software Assurance sur Azure](https://azure.microsoft.com/pricing/license-mobility/). Vous devez également disposer d’une licence valide pour la version/l’édition de SQL Server que vous voulez utiliser. Vous devez [fournir les informations BYOL nécessaires à Microsoft](http://d36cz9buwru1tt.cloudfront.net/License_Mobility_Customer_Verification_Guide.pdf) dans un délai de **10** jours à compter de l’approvisionnement de votre machine virtuelle.

Les instructions du [didacticiel relatif à l’approvisionnement](virtual-machines-windows-portal-sql-server-provision.md) s’appliquent, mais vous devez utiliser l’une des options d’image **BYOL** suivantes. Vous pouvez en outre consulter les [Meilleures pratiques relatives aux performances de SQL Server dans Azure Virtual Machines](virtual-machines-windows-sql-performance.md), qui expliquent comment sélectionner la taille de machine appropriée et les autres fonctionnalités disponibles lors de l’approvisionnement.

|Version|Système d’exploitation|Édition|
|---|---|---|
|**SQL Server 2016**|Windows Server 2012 R2|[BYOL Enterprise](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2), [BYOL Standard](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2016RTMStandardWindowsServer2012R2)|
|**SQL Server 2014 SP1**|Windows Server 2012 R2|[BYOL Enterprise](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1EnterpriseWindowsServer2012R2), [BYOL Standard](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2014SP1StandardWindowsServer2012R2)|
|**SQL Server 2012 SP2**|Windows Server 2012 R2|[BYOL Enterprise](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3EnterpriseWindowsServer2012R2), [BYOL Standard](https://portal.azure.com/#create/Microsoft.BYOLSQLServer2012SP3StandardWindowsServer2012R2)|

## Gérer votre machine virtuelle SQL
Après avoir approvisionné votre machine virtuelle SQL Server, vous pouvez effectuer plusieurs tâches de gestion facultatives. Par certains aspects, vous configurez et gérez SQL Server exactement comme vous le feriez en local. Toutefois, certaines tâches sont spécifiques à Azure. Les sections suivantes illustrent certains de ces domaines avec des liens vers des informations supplémentaires.

### Migration de vos données

Si vous disposez d’une base de données existante, vous voudrez la déplacer vers la machine virtuelle SQL récemment approvisionnée. Pour obtenir la liste des options de migration ainsi que de l’aide, voir [Migration d’une base de données vers SQL Server sur une machine virtuelle Azure](virtual-machines-windows-migrate-sql.md).

### Configurer la haute disponibilité

Si vous avez besoin d’une haute disponibilité, pensez à configurer les groupes de disponibilité SQL Server. Cela implique la présence de plusieurs machines virtuelles Azure dans un réseau virtuel. Le portail Azure dispose d'un modèle qui définit cette configuration pour vous. Pour plus d’informations, voir [Configurer un groupe de disponibilité AlwaysOn dans des machines virtuelles Azure Resource Manager (GUI)](virtual-machines-windows-portal-sql-alwayson-availability-groups.md). Si vous souhaitez configurer manuellement votre groupe de disponibilité et l’écouteur associé, voir [Configurer des groupes de disponibilité AlwaysOn sur une machine virtuelle Azure](virtual-machines-windows-portal-sql-alwayson-availability-groups-manual.md).

Pour plus d’informations sur la haute disponibilité, consultez [Haute disponibilité et récupération d’urgence pour SQL Server sur des machines virtuelles Azure](virtual-machines-windows-sql-high-availability-dr.md).

### Sauvegarder vos données
Azure Virtual Machines peut tirer parti de la fonctionnalité [Sauvegarde automatisée](virtual-machines-windows-sql-automated-backup.md), qui crée à intervalles réguliers des sauvegardes de votre base de données dans Blob Storage. Vous pouvez utiliser cette technique manuellement. Pour plus d’informations, voir [Utilisation du stockage Azure pour la sauvegarde et la restauration de SQL Server](virtual-machines-windows-use-storage-sql-server-backup-restore.md). Pour une vue d’ensemble des options de sauvegarde et de restauration, voir [Sauvegarde et restauration de SQL Server dans les machines virtuelles Azure](virtual-machines-windows-sql-backup-recovery.md).

### Automatiser les mises à jour
Azure Virtual Machines peut utiliser la fonctionnalité [Mise à jour corrective automatisée](virtual-machines-windows-sql-automated-patching.md) afin de planifier une fenêtre de maintenance pour l’installation automatique des mises à jour importantes de Windows et de SQL Server.

### Programme d’amélioration du produit (CEIP)
Le Programme d’amélioration du produit est activé par défaut. Il ne s’agit pas d’une tâche de gestion, sauf si vous souhaitez désactiver le CEIP après l’approvisionnement. Vous pouvez personnaliser ou désactiver le CEIP en vous connectant à la machine virtuelle avec le Bureau à distance. Puis exécutez l’utilitaire **Rapports d’erreurs et d’utilisation SQL Server**. Suivez les instructions pour désactiver la création de rapports.

## Étapes suivantes
[Découvrez le parcours d’apprentissage](https://azure.microsoft.com/documentation/learning-paths/sql-azure-vm/) pour SQL Server sur les machines virtuelles Azure.

D’autres questions ? Tout d’abord, consultez le [Forum Aux Questions (FAQ) concernant SQL Server sur les machines virtuelles Azure](virtual-machines-windows-sql-server-iaas-faq.md). Vous devez également ajouter vos questions ou commentaires au bas de l’une des rubriques relatives aux machines virtuelles SQL afin d’interagir avec Microsoft et la communauté.

<!---HONumber=AcomDC_0727_2016-->