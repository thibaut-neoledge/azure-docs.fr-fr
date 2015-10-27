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
   ms.date="10/21/2015"
   ms.author="lodipalm"/>

#Création de SQL Data Warehouse à l’aide TQSL 

> [AZURE.SELECTOR]
- [Azure Preview Portal](sql-data-warehouse-get-started-provision.md)
- [TSQL](sql-data-warehouse-get-started-create-TSQL.md)
- [PowerShell](sql-data-warehouse-get-started-create-powershell.md)

Cet article vous explique comment créer un entrepôt SQL Data Warehouse à l’aide de Transact SQL. Pour effectuer les étapes de cet article, vous avez besoin des éléments suivants :

- Un abonnement Azure. Si vous avez besoin d'un abonnement Azure, cliquez simplement sur **VERSION D'ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
- Visual Studio. Pour obtenir une copie gratuite de Visual Studio, consultez la page [Téléchargements Visual Studio](https://www.visualstudio.com/downloads/download-visual-studio-vs).
- Un serveur SQL Server A V12. Vous aurez besoin d’un serveur SQL Server V12 pour créer l’entrepôt SQL Data Warehouse. Si vous ne disposez pas d’un serveur V12 SQL Server, nous vous conseillons de procéder à la création dans la version préliminaire du portail afin de créer votre entrepôt SQL Data Warehouse sur un nouveau serveur.

Cet article n’abordera pas la façon de configurer correctement Visual Studio et de se connecter. Pour obtenir une description complète de la procédure à suivre, consultez la documentation relative aux [connexions et requêtes][]. Pour commencer, vous devez ouvrir l’Explorateur d’objets SQL Server dans Visual Studio et vous connecter au serveur que vous allez utiliser pour créer votre entrepôt SQL Data Warehouse. Une fois que vous avez terminé, vous êtes en mesure de créer un entrepôt SQL Data Warehouse en exécutant la commande suivante sur la base de données MASTER :

        CREATE DATABASE <Name> (EDITION='datawarehouse', SERVICE_OBJECTIVE = '<Compute Size - DW####>', MAXSIZE= <Storage Size - #### GB>);

Vous pouvez également créer un entrepôt de données SQL en ouvrant la ligne de commande et en exécutant la commande suivante :

        sqlcmd -S <Server Name>.database.windows.net -I -U <User> -P <Password> -Q "CREATE DATABASE <Name> (EDITION='datawarehouse', SERVICE_OBJECTIVE = '<Compute Size - DW####>', MAXSIZE= <Storage Size - #### GB>)"

Lors de l’exécution des instructions TSQL, notez les paramètres MAXSIZE et SERVICE\_OBJECTIVE. Ils serviront à définir le volume de stockage et les ressources de calcul alloués à l’instance d’entrepôt de données. MAXSIZE accepte les volumes suivants et nous vous suggérons de choisir un volume important pour laisser de la place pour une éventuelle évolution :

+ 250 Go
+ 500 Go
+ 750 Go
+ 1024 Go
+ 5120 Go
+ 10240 Go
+ 20480 Go
+ 30720 Go
+ 40960 Go
+ 51200 Go

SERVICE\_OBJECTIVE indique le nombre de DWU avec laquelle votre instance démarre avec et acceptera les valeurs suivantes :

+ DW100
+ DW200
+ DW300
+ DW400
+ DW500
+ DW600
+ DW1000
+ DW1200
+ DW1500
+ DW2000

Pour plus d’informations sur l’impact de ces paramètres sur la facturation, consultez notre [page de tarification][].

## Étapes suivantes
Une fois votre entrepôt SQL Data Warehouse approvisionné, vous pouvez [charger les données d’exemple][] ou découvrir comment [développer][], [charger][] ou [migrer][] les données.

[connexions et requêtes]: ./sql-data-warehouse-get-started-connect.md
[migrer]: ./sql-data-warehouse-overview-migrate.md
[développer]: ./sql-data-warehouse-overview-develop.md
[charger]: ./sql-data-warehouse-overview-load.md
[charger les données d’exemple]: ./sql-data-warehouse-get-started-manually-load-samples.md
[page de tarification]: https://azure.microsoft.com/pricing/details/sql-data-warehouse/

<!---HONumber=Oct15_HO4-->