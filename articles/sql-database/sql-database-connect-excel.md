<properties
	pageTitle="Connectez Excel à la base de données SQL | Microsoft Azure"
	description="Découvrez comment connecter Microsoft Excel à la base de données SQL Azure dans le cloud. Importez des données dans Excel pour les rapports et l’exploration des données."
	services="sql-database"
	keywords="connecter excel à sql, importer des données vers excel"
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
	ms.date="02/04/2016"
	ms.author="joseidz"/>


# Connectez Excel à une base de données SQL Azure et créez un rapport 

> [AZURE.SELECTOR]
- [C#](sql-database-connect-query.md)
- [SSMS](sql-database-connect-query-ssms.md)
- [Excel](sql-database-connect-excel.md)

Apprenez comment connecter Excel à une base de données SQL afin d’importer des données dans Excel. Ensuite, créez un rapport sur les données.

Vous avez d’abord besoin d’une base de données SQL. Si vous n’en avez pas, consultez [Créer votre première base de données SQL](sql-database-get-started.md) pour obtenir une base de données avec des données exemple opérationnelle en quelques minutes. Dans cet article, vous allez importer des données d’exemple dans Excel à partir de cet article, mais vous pouvez suivre des étapes similaires sur vos propres données.

Vous aurez besoin d’une copie d’Excel. Cet article utilise [Microsoft Excel 2016](https://products.office.com/fr-FR/).

## Connectez Excel à une base de données SQL et créez un rapport

1.	Pour la connexion d’Excel à la base de données SQL, ouvrez Excel, puis créez un nouveau classeur. Ou bien, ouvrez un classeur Excel existant que vous souhaitez vous connecter à la base de données SQL.

2.	Dans la barre de menus en haut de la page, cliquez sur **Données**, cliquez sur **À partir d’autres sources**, puis cliquez sur **À partir de SQL Server**.

	![Sélectionner les données source : Connecter Excel à la base de données SQL.](./media/sql-database-connect-excel/excel_data_source.png)

	L’Assistant Connexion de données s’ouvre.

3.	Dans la boîte de dialogue **Connexion à un serveur de base de données**, saisissez le **Nom du serveur** qui héberge votre serveur logique selon le format **<*servername*>.database.windows.net**. Par exemple, **adventureserver.database.windows.net**.

4.	Dans la section **Références de connexion**, cliquez sur **Utiliser le nom d’utilisateur et le mot de passe suivants :**, saisissez le **Nom d’utilisateur** et le **Mot de passe** que vous avez configuré pour le serveur de base de données SQL lorsque vous l’avez créé, puis cliquez sur **Suivant**.

	> [AZURE.TIP] Les deux compléments pour Excel, [PowerPivot](https://www.microsoft.com/download/details.aspx?id=102) et [Power Query](https://www.microsoft.com/download/details.aspx?id=39379), ont des expériences similaires.

5. Dans la boîte de dialogue **Sélection d’une base de données et d’une table**, sélectionnez la base de données **AdventureWorks** dans le menu déroulant, puis **vGetAllCategories** dans la liste des tables et des vues, et cliquez sur **Suivant**.

	![Sélectionner une base de données et une table][5]

6. Dans la boîte de dialogue **Enregistrement du fichier de connexion de données et fin**, cliquez sur**Terminer**.

7. Dans la boîte de dialogue **Importer des données**, sélectionnez **PivotChart**, puis cliquez sur **OK**.

	![Importer des données dans Excel : dans la boîte de dialogue Importer des données, sélectionnez Graphique croisé dynamique.][2]

8. Dans la boîte de dialogue **Champs de tableau croisé dynamique**, sélectionnez la configuration suivante pour créer un rapport correspondant au nombre de produits par catégorie.

	![Configurez le rapport de base de données.][3]

	Vous devez obtenir le résultat suivant :

	![Réussite : Excel connecté à la base de données SQL.][4]

## Étapes suivantes

Si vous êtes développeur de SaaS (Software as a Service), découvrez les [Pools de bases de données élastiques](sql-database-elastic-pool.md). Vous pouvez gérer facilement de grandes collections de bases de données à l’aide des [Tâches de bases de données élastiques](sql-database-elastic-jobs-overview.md).

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=AcomDC_0204_2016-->