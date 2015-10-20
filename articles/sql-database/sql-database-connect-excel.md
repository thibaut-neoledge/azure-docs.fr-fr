<properties
	pageTitle="Se connecter à une base de données SQL Azure avec Excel"
	description="Feuille de calcul Excel pour Base de données SQL Azure pour la création de rapports et l’exploration de données."
	services="sql-database"
	documentationCenter=""
	authors="joseidz"
	manager="jeffreyg"
	editor="jeffreyg"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="get-started-article"
	ms.date="10/09/2015"
	ms.author="joseidz"/>


# Se connecter à une base de données SQL Azure avec Excel

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Cet article vous montre comment connecter Excel à une base de données SQL Azure et créer un rapport sur les données dans la base de données. Vous avez d’abord besoin d’une base de données SQL. Si vous n’en avez pas, consultez [Créer votre première base de données SQL](sql-database-get-started.md) pour obtenir une base de données avec des données exemple opérationnelle en quelques minutes. Cet article est basé sur les exemples de données à partir de cet article, mais vous pouvez suivre des étapes similaires sur vos propres données.

Vous aurez besoin d’une copie d’Excel. Cet article utilise [Microsoft Excel 2016](https://products.office.com/fr-FR/).

## Se connecter et créer un rapport

1.	Ouvrez Excel et créez un nouveau classeur ou ouvrez le classeur auquel vous souhaitez vous connecter.

2.	Dans la barre de menus en haut de la page, cliquez sur **Données**, cliquez sur **À partir d’autres sources**, puis cliquez sur **À partir de SQL Server**.
	
	![Sélectionnez la source de données](./media/sql-database-connect-excel/excel_data_source.png)

	L’Assistant Connexion de données s’ouvre.

3.	Dans la boîte de dialogue **Connexion à un serveur de base de données**, saisissez le **Nom du serveur** qui héberge votre serveur logique selon le format **<*servername*>.database.windows.net**. Par exemple, **adventureserver.database.windows.net**.

4.	Dans la section **Références de connexion**, cliquez sur **Utiliser le nom d’utilisateur et le mot de passe suivants :**, saisissez le **Nom d’utilisateur** et le **Mot de passe** que vous avez configuré pour le serveur de base de données SQL lorsque vous l’avez créé, puis cliquez sur **Suivant**.

	> [AZURE.TIP]Les deux compléments pour Excel, [PowerPivot](https://www.microsoft.com/download/details.aspx?id=102) et [Power Query](https://www.microsoft.com/download/details.aspx?id=39379), ont des expériences similaires.

5. Dans la boîte de dialogue **Sélection d’une base de données et d’une table**, sélectionnez la base de données **AdventureWorks** dans le menu déroulant, puis **vGetAllCategories** dans la liste des tables et des vues, et cliquez sur **Suivant**.

	![Sélectionner une base de données et une table][5]

6. Dans la boîte de dialogue **Enregistrement du fichier de connexion de données et fin**, cliquez sur**Terminer**.

7. Dans la boîte de dialogue **Importer des données**, sélectionnez **PivotChart**, puis cliquez sur **OK**.

	![Sélectionner Importer des données][2]

8. Dans la boîte de dialogue **Champs de tableau croisé dynamique**, sélectionnez la configuration suivante pour créer un rapport correspondant au nombre de produits par catégorie.

	![Configuration][3]

	Vous devez obtenir le résultat suivant :

	![réussi][4]

## Étapes suivantes

Si vous êtes développeur de SaaS (Software as a Service), découvrez les [pools de bases de données élastiques](sql-database-elastic-pool.md). Vous pouvez gérer facilement de grandes collections de bases de données à l’aide des [tâches de bases de données élastiques](sql-database-elastic-jobs-overview.md).

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=Oct15_HO3-->