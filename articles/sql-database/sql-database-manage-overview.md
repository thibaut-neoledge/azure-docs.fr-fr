<properties
	pageTitle="Vue d’ensemble des outils de gestion de la base de données SQL"
	description="Compare les outils et les options de gestion de bases de données SQL Microsoft Azure."
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor="jeffreyg"/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="01/22/2016"
	ms.author="jeffreyg"/>

# Vue d’ensemble des outils de gestion de la base de données SQL

Cette rubrique explore et compare les différents outils et options de gestion des bases de données Azure SQL, afin de vous permettre de choisir l’outil approprié pour le travail, pour votre entreprise et pour vous. Or, la sélection de l’outil adapté dépend du nombre de bases de données que vous gérez, de la tâche effectuée et de la fréquence de son exécution.

## Portail Azure

Le [portail Azure](https://portal.azure.com) est une application basée sur le web dans laquelle vous pouvez créer, mettre à jour et supprimer des serveurs logiques et des bases de données, et surveiller l’activité des bases de données. Cet outil est très utile si vous n’êtes pas familiarisé avec Microsoft Azure, si vous gérez un petit nombre de bases de données, ou si vous devez effectuer une action rapidement.

Pour en savoir plus sur l’utilisation du portail, voir [Gérer des bases de données SQL à l’aide du portail Azure Classic](sql-database-manage-portal.md).

## SQL Server Data Tools et Management Studio dans Visual Studio

Les logiciels SQL Server Management Studio (SSMS) et SQL Server Data Tools (SSDT) de Visual Studio sont des outils clients qui s’exécutent sur votre ordinateur et vous permettent de vous connecter, de gérer et de développer votre base de données dans le cloud. Si vous êtes un développeur d'applications familiarisé avec Visual Studio ou d'autres environnements de développement intégré (IDE), [essayez la fonction SSDT de Visual Studio](https://msdn.microsoft.com/library/mt204009.aspx). De nombreux administrateurs de base de données sont familiarisés avec SSMS, qui peut être utilisé avec les bases de données SQL Azure. [Téléchargez la dernière version de SSMS](https://msdn.microsoft.com/library/mt238290) et utilisez toujours la dernière version quand vous recourez à Azure SQL Database. Pour en savoir plus sur la gestion de vos bases de données SQL Azure avec SSMS, consultez [Gestion de la base de données SQL Azure au moyen de SQL Server Management Studio](sql-database-manage-azure-ssms.md).

## Outils en ligne de commande

Vous pouvez utiliser des outils en ligne de commande tels que PowerShell pour gérer des bases de données et les pools de bases de données élastiques, et automatiser les déploiements de ressources Azure. Microsoft recommande cet outil pour la gestion d'un grand nombre de bases de données et l'automatisation du déploiement et des modifications apportées aux ressources dans un environnement de production.

Pour en savoir plus sur la gestion de vos bases de données SQL Azure avec les outils en ligne de commande, consultez [Gestion d'une base de données SQL avec PowerShell](sql-database-command-line-tools.md).

<!---HONumber=AcomDC_0128_2016-->