<properties
	pageTitle="PHP sur Windows pour la base de données SQL | Microsoft Azure"
	description="Présente un exemple de programme PHP qui se connecte à la base de données SQL Azure à partir d'un client Windows et fournit des liens vers les composants logiciels nécessaires requis par le client."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="php"
	ms.topic="article"
	ms.date="06/10/2015"
	ms.author="mebha"/>


# Connexion à la base de données SQL à l'aide de PHP sous Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Cette rubrique illustre comment vous pouvez vous connecter à la base de données SQL Azure à partir d'une application cliente écrite en PHP qui s'exécute sur Windows.


## Conditions préalables


Pour exécuter l'exemple de code PHP fourni dans cette rubrique, les éléments logiciels suivants doivent être installés sur votre ordinateur :


- [Pilotes Microsoft pour PHP pour Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=20098) (SQLSRV32.EXE contient les derniers bits)
- [Microsoft SQL Server Native Client 11.0](http://www.microsoft.com/download/details.aspx?id=36434)
- [Pilote Microsoft ODBC](https://www.microsoft.com/fr-fr/download/details.aspx?id=36434)
- IIS Express
- [PHP 5.6 for IIS Express](http://www.microsoft.com/web/downloads/platform.aspx) : téléchargez-le à partir du programme d'installation de la plateforme. Assurez-vous d'utiliser Internet Explorer pour télécharger le programme d'installation de la plateforme

Consultez notre [blog de l'équipe](http://blogs.msdn.com/b/sqlphp/archive/2015/05/11/getting-started-with-php-and-microsoft-sql-server.aspx) et notre [vidéo](https://www.youtube.com/watch?v=0oCjiRK_tUk) pour découvrir comment installer et configurer les exigences mentionnées précédemment.


## Créer une base de données et récupérer la chaîne de connexion


Consultez la [rubrique Prise en main](sql-database-get-started.md) pour découvrir comment créer un exemple de base de données et obtenir votre chaîne de connexion. Il est important que vous suiviez le guide pour créer un **modèle de base de données AdventureWorks**. Les exemples ci-dessous fonctionnent uniquement avec le **schéma AdventureWorks**.


## Se connecter à la base de données SQL


La fonction **OpenConnection** est appelée presque en premier dans toutes les fonctions qui suivent.


	function OpenConnection()
	{
		try
		{
			$serverName = "tcp:myserver.database.windows.net,1433";
			$connectionOptions = array("Database"=>"AdventureWorks",
				"Uid"=>"MyUser", "PWD"=>"MyPassword");
			$conn = sqlsrv_connect($serverName, $connectionOptions);
			if($conn == false)
				die(FormatErrors(sqlsrv_errors()));
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## Exécution d'une requête et récupération du jeu de résultats

La fonction [sqlsrv_query()](http://php.net/manual/en/function.sqlsrv-query.php) peut être utilisée pour récupérer un jeu de résultats d'une requête à partir d'une base de données SQL. Cette fonction accepte toutes les requêtes et l'objet de connexion, et retourne un jeu de résultats qui peut faire l'objet d'une itération à l'aide de [sqlsrv_fetch_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php).

	function ReadData()
	{
		try
		{
			$conn = OpenConnection();
			$tsql = "SELECT [CompanyName] FROM SalesLT.Customer";
			$getProducts = sqlsrv_query($conn, $tsql);
			if ($getProducts == FALSE)
				die(FormatErrors(sqlsrv_errors()));
			$productCount = 0;
			while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))
			{
				echo($row['CompanyName']);
				echo("<br/>");
				$productCount++;
			}
			sqlsrv_free_stmt($getProducts);
			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}
	

## Insérer une ligne, transmettre des paramètres et récupérer la clé primaire générée


Dans la base de données SQL, la propriété [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) et l'objet [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) peuvent être utilisés pour générer automatiquement des valeurs de [clé primaire](https://msdn.microsoft.com/library/ms179610.aspx).


	function InsertData()
	{
		try
		{
			$conn = OpenConnection();

			$tsql = "INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT 			INSERTED.ProductID VALUES ('SQL Server 1', 'SQL Server 2', 0, 0, getdate())";
			//Insert query
			$insertReview = sqlsrv_query($conn, $tsql);
			if($insertReview == FALSE)
				die(FormatErrors( sqlsrv_errors()));
			echo "Product Key inserted is :";	
			while($row = sqlsrv_fetch_array($insertReview, SQLSRV_FETCH_ASSOC))
			{   
				echo($row['ProductID']);
			}
			sqlsrv_free_stmt($insertReview);
			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}

## Transactions


Cet exemple de code illustre l'utilisation de transactions dans lesquelles vous :

-Commencez une transaction

-Insérez une ligne de données, mettez à jour une autre ligne de données

-Validez votre transaction si l'insertion et la mise à jour ont réussi et restaurez la transaction si l'une des deux a échoué


	function Transactions()
	{
		try
		{
			$conn = OpenConnection();

			if (sqlsrv_begin_transaction($conn) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			$tsql1 = "INSERT INTO SalesLT.SalesOrderDetail (SalesOrderID,OrderQty,ProductID,UnitPrice) 
			VALUES (71774, 22, 709, 33)";
			$stmt1 = sqlsrv_query($conn, $tsql1);
			
			/* Set up and execute the second query. */
			$tsql2 = "UPDATE SalesLT.SalesOrderDetail SET OrderQty = (OrderQty + 1) WHERE ProductID = 709";
			$stmt2 = sqlsrv_query( $conn, $tsql2);
			
			/* If both queries were successful, commit the transaction. */
			/* Otherwise, rollback the transaction. */
			if($stmt1 && $stmt2)
			{
			       sqlsrv_commit($conn);
			       echo("Transaction was commited");
			}
			else
			{
			    sqlsrv_rollback($conn);
			    echo "Transaction was rolled back.\n";
			}
			/* Free statement and connection resources. */
			sqlsrv_free_stmt( $stmt1);
			sqlsrv_free_stmt( $stmt2);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## Pour aller plus loin


Pour plus d'informations sur l'installation et l'utilisation de PHP, consultez [Accès aux bases de données du serveur SQL avec PHP](http://technet.microsoft.com/library/cc793139.aspx).

 

<!---HONumber=July15_HO2-->