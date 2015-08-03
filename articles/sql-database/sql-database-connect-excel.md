<properties
	pageTitle="Se connecter à une base de données SQL Azure avec Excel"
	description="Feuille de calcul Excel pour Base de données SQL Azure pour la création de rapports et l’exploration de données."
	services="sql-database"
	documentationCenter=""
	authors="joseidz"
	manager="joseidz"
	editor="joseidz"/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="joseidz"/>


# Se connecter à une base de données SQL Azure avec Excel
Connectez Excel à une base de données SQL Azure et créez un rapport sur les données dans la base de données.

## Composants requis
- Une base de données SQL Azure configurée et en cours d'exécution. Pour créer une base de données SQL, consultez [Prise en main de Base de données SQL Microsoft Azure](sql-database-get-started.md).
- [Microsoft Excel 2013](https://products.office.com/fr-fr/) (ou Microsoft Excel 2010)

## Se connecter à une base de données SQL et créer un rapport
1.	Ouvrez Excel.
2.	Dans la barre de menus en haut de la page, cliquez sur **Données**.
3.	Cliquez sur **À partir d’autres sources**, puis sur **À partir de SQL Server**. L’**Assistant Connexion de données** s’ouvre.

	![Assistant Connexion de données][1]
4.	Dans la zone **Nom du serveur**, tapez le nom du serveur de base de données SQL Azure. Exemple :

	 	adventureserver.database.windows.net
5.	Dans la section **Références de connexion**, sélectionnez **Utiliser le nom d’utilisateur et le mot de passe suivants**, puis tapez les informations d’identification appropriées pour le serveur de base de données SQL. Cliquez ensuite sur **Next**.

	Remarque : les deux compléments pour Excel, [PowerPivot](https://www.microsoft.com/download/details.aspx?id=102) et [Power Query](https://www.microsoft.com/download/details.aspx?id=39379), ont des expériences très similaires.

6. Dans la boîte de dialogue **Sélection d’une base de données et d’une table**, sélectionnez la base de données **AdventureWorks** dans le menu déroulant, puis **vGetAllCategories** dans la liste des tables et des vues, et cliquez sur **Suivant**.

	![Sélectionner une base de données et une table][5]
7. Dans la boîte de dialogue **Enregistrement du fichier de connexion de données et fin**, cliquez sur**Terminer**.
8. Dans la boîte de dialogue **Importer des données**, sélectionnez **PivotChart**, puis cliquez sur **OK**.

	![Sélectionner Importer des données][2]
9. Dans la boîte de dialogue **Champs de tableau croisé dynamique**, sélectionnez la configuration suivante pour créer un rapport correspondant au nombre de produits par catégorie.

	![Configuration][3]
10.	Vous devez obtenir le résultat suivant :

	![réussi][4]

## Étapes suivantes

Si vous êtes développeur de SaaS (Software as a Service), découvrez les [pools de bases de données élastiques](sql-database-elastic-pool.md). Vous pouvez gérer facilement de grandes collections de bases de données à l’aide des [tâches de bases de données élastiques](sql-database-elastic-jobs-overview.md).

<!--Image references-->
[1]: ./media/sql-database-connect-excel/connect-to-database-server.png
[2]: ./media/sql-database-connect-excel/import-data.png
[3]: ./media/sql-database-connect-excel/power-pivot.png
[4]: ./media/sql-database-connect-excel/power-pivot-results.png
[5]: ./media/sql-database-connect-excel/select-database-and-table.png

<!---HONumber=July15_HO4-->