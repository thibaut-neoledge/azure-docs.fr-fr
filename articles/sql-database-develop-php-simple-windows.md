<properties
	pageTitle="Connexion à la base de données SQL à l'aide de PHP sous Windows"
	description="Présente un exemple de programme PHP qui se connecte à la base de données SQL Azure à partir d'un client Windows et fournit des liens vers les composants logiciels nécessaires requis par le client."
	services="sql-database"
	documentationCenter=""
	authors="MightyPen"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="data-management"
	ms.tgt_pltfrm="na"
	ms.devlang="php"
	ms.topic="article"
	ms.date="04/27/2015"
	ms.author="genemi"/>


# Connexion à la base de données SQL à l'aide de PHP sous Windows


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Cette rubrique illustre comment vous pouvez vous connecter à la base de données SQL Azure à partir d'une application cliente écrite en PHP qui s'exécute sur Windows.


## Conditions préalables


Pour exécuter l'exemple de code PHP fourni dans cette rubrique, les éléments logiciels suivants doivent être installés sur votre ordinateur :


- [Pilotes Microsoft pour PHP pour Microsoft SQL Server](http://www.microsoft.com/download/details.aspx?id=20098) (SQLSRV32.EXE contient les derniers bits)
- [Microsoft SQL Server Native Client 11.0](http://www.microsoft.com/download/details.aspx?id=36434)
- Les installations spécifiques sont à exécuter à l'aide de [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx). Pour plus d'informations, consultez la section suivante.


### Installations avec Web Platform Installer


1. Téléchargez et exécutez [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx) et sélectionnez les modules requis.
2. Utilisez [Web Platform Installer](http://www.microsoft.com/web/downloads/platform.aspx) pour installer les composants suivants :
 - IIS ou IIS Express.<br/>Par exemple, le [téléchargement pour IIS Express 8.0](http://www.microsoft.com/download/details.aspx?id=34679) est accessible en exécutant une recherche sur le Web : téléchargement IIS Express.
 - PHP pour Windows, version 5.5 ou ultérieure, version 32 bits.
3. Modifiez le fichier PHP.INI en ajoutant une entrée dans la section Dynamic Extensions, comme ci-dessous :<br/>**extension=php_sqlsrv_55_nts.dll**


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


## Insérer des valeurs dans la table


	function InsertData()
	{
		try
		{
			$conn = OpenConnection();

			$tsql = "INSERT INTO [SalesLT].[Customer]
					   ([NameStyle],[FirstName],[MiddleName],[LastName],
					   [PasswordHash],[PasswordSalt],[rowguid],[ModifiedDate])
					   VALUES (?,?,?,?,?,?,?,?)";

			$params = array("0", "Luiz", "F", "Santos",
				"L/Rlwxzp4w7RWmEgXX+/A7cXaePEPcp+KwQhl2fJL7w=",
				"1KjXYs4=", "88949BFE-0BB4-4148-AFC2-DD8C8DAE4CD6",
				date("Y-m-d"));

			$insertReview = sqlsrv_prepare($conn, $tsql, $params);
			if($insertReview == FALSE)
				die(FormatErrors( sqlsrv_errors()));

			if(sqlsrv_execute($insertReview) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			sqlsrv_free_stmt($insertReview);

			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## Supprimer des valeurs de la table


	function DeleteData()
	{
		try
		{
			$conn = OpenConnection();

			$tsql = "DELETE FROM [SalesLT].[Customer] WHERE FirstName=? AND LastName=?";
			$params = array($_REQUEST['FirstName'], $_REQUEST['LastName']);

			$deleteReview = sqlsrv_prepare($conn, $tsql, $params);
			if($deleteReview == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			if(sqlsrv_execute($deleteReview) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			sqlsrv_free_stmt($deleteReview);

			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## Sélectionner des valeurs dans la table


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


## Transactions


	function Transactions()
	{
		try
		{
			$conn = OpenConnection();

			if (sqlsrv_begin_transaction($conn) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			/* Initialize parameter values. */
			$orderId = 43659;
			$qty = 5;
			$productId = 709;
			$offerId = 1;
			$price = 5.70;

			/* Set up and execute the first query. */
			$tsql1 = "INSERT INTO SalesLT.SalesOrderDetail
			   (SalesOrderID,OrderQty,ProductID,SpecialOfferID,UnitPrice)
			   VALUES (?, ?, ?, ?, ?)";
			$params1 = array($orderId, $qty, $productId, $offerId, $price);
			$stmt1 = sqlsrv_query($conn, $tsql1, $params1);

			/* Set up and execute the second query. */
			$tsql2 = "UPDATE Production.ProductInventory SET Quantity = (Quantity - ?)
					  WHERE ProductID = ?";
			$params2 = array($qty, $productId);
			$stmt2 = sqlsrv_query( $conn, $tsql2, $params2 );

			/* If both queries were successful, commit the transaction. */
			/* Otherwise, rollback the transaction. */
			if($stmt1 && $stmt2)
			{
					sqlsrv_commit($conn);
					echo "Transaction was committed.\n";
			}
			else
			{
					sqlsrv_rollback($conn);
					echo "Transaction was rolled back.\n";
			}

			/* Free statement and connection resources. */
			sqlsrv_free_stmt( $stmt1);
			sqlsrv_free_stmt( $stmt2);

			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## Procédures stockées


	function ExecuteSProc()
	{
		try
		{
			$conn = OpenConnection();

			$tsql = "EXEC [dbo].[uspLogError]";

			$execReview = sqlsrv_prepare($conn, $tsql);
			if($execReview == FALSE)
				die(FormatErrors( sqlsrv_errors()));

			if(sqlsrv_execute($execReview) == FALSE)
				die(FormatErrors(sqlsrv_errors()));

			sqlsrv_free_stmt($execReview);

			sqlsrv_close($conn);
		}
		catch(Exception $e)
		{
			echo("Error!");
		}
	}


## Pour aller plus loin


Pour plus d'informations sur l'installation et l'utilisation de PHP, consultez [Accès aux bases de données du serveur SQL avec PHP](http://technet.microsoft.com/library/cc793139.aspx).

<!---HONumber=58-->