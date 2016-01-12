<properties
   pageTitle="Créer un entrepôt SQL Data Warehouse avec TSQL | Microsoft Azure"
   description="Découvrez comment créer un entrepôt Azure SQL Data Warehouse avec TSQL"
   services="sql-data-warehouse"
   documentationCenter="NA"
   authors="lodipalm"
   manager="barbkess"
   editor=""
   tags="azure-sql-data-warehouse"/>

<tags
   ms.service="sql-data-warehouse"
   ms.devlang="NA"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-services"
   ms.date="01/04/2016"
   ms.author="lodipalm"/>

# Créer une base de données SQL Data Warehouse à l’aide de Transact-SQL (TSQL).

> [AZURE.SELECTOR]
- [Azure Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-database-tsql.md)
- [PowerShell](sql-data-warehouse-get-started-provision-powershell.md)

Cet article vous explique comment créer une base de données SQL Data Warehouse à l’aide de Transact SQL (TSQL).

## Avant de commencer

Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

- Un abonnement Azure. Si vous avez besoin d'un abonnement Azure, cliquez simplement sur **VERSION D'ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
- Visual Studio. Pour obtenir une copie gratuite de Visual Studio, consultez la page [Téléchargements Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).
- Un serveur logique SQL V12. Vous aurez besoin d’un serveur SQL V12 pour créer l’entrepôt SQL Data Warehouse. Si vous ne disposez pas d’un serveur SQL logique V12, le [didacticiel du portail Azure][] vous montre comment en créer un.

## Créer une base de données avec Visual Studio

Cet article n’abordera pas la façon de configurer correctement Visual Studio et de se connecter. Pour obtenir une description complète de la procédure à suivre, consultez la documentation relative aux [connexions et requêtes][]. Pour commencer, ouvrez l’Explorateur d’objets SQL Server dans Visual Studio et connectez-vous au serveur que vous allez utiliser pour créer votre base de données SQL Data Warehouse. Une fois que vous avez terminé, vous êtes en mesure de créer un entrepôt SQL Data Warehouse en exécutant la commande suivante sur la base de données MASTER :

        CREATE DATABASE <Name> (EDITION='datawarehouse', SERVICE_OBJECTIVE = '<Compute Size - DW####>', MAXSIZE= <Storage Size - #### GB>);

## Créez une base de données avec sqlcmd.

Vous pouvez également créer un entrepôt de données SQL en ouvrant la ligne de commande et en exécutant la commande suivante :

        sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE <Name> (EDITION='datawarehouse', SERVICE_OBJECTIVE = '<Compute Size - DW####>', MAXSIZE= <Storage Size - #### GB>)"

Lors de l’exécution des instructions TSQL, notez les paramètres MAXSIZE et SERVICE\_OBJECTIVE. Ils serviront à définir le volume de stockage et les ressources de calcul alloués à l’instance d’entrepôt de données. MAXSIZE acceptera les tailles suivantes et nous vous suggérons de choisir une grande taille pour laisser de la place en vue d’un éventuel développement : 250 Go, 500 Go, 750 Go, 1 024 Go, 5120 Go, 10240 Go, 20480 Go, 30720 Go, 40960 Go, 51200 Go.

SERVICE\_OBJECTIVE indique le nombre de DWU avec lequel votre instance démarre et accepte les valeurs suivantes : DW100, DW200, DW300, DW400, DW500, DW600, DW1000, DW1200, DW1500, DW2000. Pour plus d’informations sur l’impact de ces paramètres sur la facturation, consultez notre [page de tarification][].

## Étapes suivantes
Une fois votre entrepôt SQL Data Warehouse approvisionné, vous pouvez [charger les données d’exemple][] ou découvrir comment [développer][], [charger][] ou [migrer][] les données.

[didacticiel du portail Azure]: ./sql-data-warehouse-get-started-provision.md
[connexions et requêtes]: ./sql-data-warehouse-get-started-connect.md
[migrer]: ./sql-data-warehouse-overview-migrate.md
[développer]: ./sql-data-warehouse-overview-develop.md
[charger]: ./sql-data-warehouse-overview-load.md
[charger les données d’exemple]: ./sql-data-warehouse-get-started-manually-load-samples.md
[page de tarification]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/

<!---HONumber=AcomDC_0107_2016-->