<properties
	pageTitle="Déplacez des bases de données entre des serveurs, entre des abonnements, au sein et hors d’Azure."
	description="Étapes à suivre pour copier, déplacer et migrer des données et des bases de données dans Azure SQL Database."
	services="sql-database"
	documentationCenter=""
	authors="v-shysun"
	manager="msmets"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/06/2016"
	ms.author="v-shysun"/>

# Déplacer des bases de données entre des serveurs, entre des abonnements, au sein et hors d’Azure
##Pour déplacer une base de données sur un autre serveur dans le même abonnement
- Dans le [portail Azure](https://portal.azure.com), cliquez sur **Bases de données SQL**, sélectionnez une base de données dans la liste, puis cliquez sur **Copier**. Pour plus d’informations, consultez [Copier une base de données SQL Azure](sql-database-copy.md).

## Pour déplacer une base de données entre des abonnements
- Dans le [portail Azure](https://portal.azure.com), cliquez sur **Serveurs SQL**, puis sélectionnez le serveur qui héberge votre base de données dans la liste. Cliquez sur **Déplacer**, puis sélectionnez les ressources à déplacer et l’abonnement cible.

## Pour migrer une base de données SQL dans Azure
- Déterminez la compatibilité de la base de données, puis choisissez la méthode de migration adaptée à vos besoins. Suivez les instructions et les options indiquées dans [Migration d’une base de données SQL Server](sql-database-cloud-migrate.md).

## Pour créer une copie d’une base de données en vue d’une utilisation hors d’Azure
- [Exportez un fichier BACPAC.](sql-database-export.md)

<!---HONumber=AcomDC_0413_2016-->