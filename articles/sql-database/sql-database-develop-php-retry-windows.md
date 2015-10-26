<properties
	pageTitle="Connexion PHP sur Windows à SQL DB | Microsoft Azure"
	description="Présente un exemple de programme PHP qui se connecte à Azure SQL Database à partir d’un client Windows avec la gestion des erreurs temporaires et fournit des liens vers les composants logiciels nécessaires requis par le client."
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
	ms.date="07/21/2015"
	ms.author="mebha"/>


# Connexion à SQL Database à l’aide de PHP sur Windows avec la gestion des erreurs temporaires


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Cette rubrique montre comment vous connecter à Azure SQL Database à partir d'une application cliente écrite en PHP qui s'exécute sur Windows.


[AZURE.INCLUDE [sql-database-develop-includes-prerequisites-php-windows](../../includes/sql-database-develop-includes-prerequisites-php-windows.md)]


## Créer une base de données et récupérer la chaîne de connexion


Consultez la [rubrique Prise en main](sql-database-get-started.md) pour découvrir comment créer un exemple de base de données et obtenir votre chaîne de connexion. Il est important que vous suiviez le guide pour créer un **modèle de base de données AdventureWorks**. Les exemples ci-dessous fonctionnent uniquement avec le **schéma AdventureWorks**.


## Se connecter et interroger votre base de données 

Le programme de démonstration est conçu afin qu’une erreur temporaire au cours d’une tentative de connexion aboutisse à une nouvelle tentative. Mais une erreur temporaire au cours de la commande de requête oblige le programme à ignorer la connexion et à en créer une nouvelle, avant de réessayer la commande de requête. Nous ne vous recommandons ni ne vous déconseillons ce choix de conception. Le programme de démonstration illustre quelques aspects de la flexibilité de conception qui vous sont accessibles.

<br>La longueur de cet exemple de code est due essentiellement à la logique de l’exception catch. Une version plus courte du fichier Program.cs est disponible [ici](sql-database-develop-php-simple-windows.md). <br>La méthode Main se trouve dans le fichier Program.cs. La pile des appels s’exécute comme suit : * Main appelle ConnectAndQuery. * ConnectAndQuery appelle EstablishConnection. * EstablishConnection appelle IssueQueryCommand.

La fonction [sqlsrv\_query()](http://php.net/manual/en/function.sqlsrv-query.php) peut être utilisée pour récupérer un jeu de résultats d'une requête effectuée dans la base de données SQL. Cette fonction accepte toutes les requêtes et l'objet de connexion, et retourne un jeu de résultats qui peut faire l'objet d'une itération à l'aide de [sqlsrv\_fetch\_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php).

	<?php
		// Variables to tune the retry logic.  
		$connectionTimeoutSeconds = 30;  // Default of 15 seconds is too short over the Internet, sometimes.
		$maxCountTriesConnectAndQuery = 3;  // You can adjust the various retry count values.
		$secondsBetweenRetries = 4;  // Simple retry strategy.
		$errNo = 0;
		$serverName = "tcp:yourdatabase.database.windows.net,1433";
		$connectionOptions = array("Database"=>"AdventureWorks",
		   "Uid"=>"yourusername", "PWD"=>"yourpassword", "LoginTimeout" => $connectionTimeoutSeconds);
		$conn;
		$errorArr = array();
		for ($cc = 1; $cc <= $maxCountTriesConnectAndQuery; $cc++)
		{
		    $errorArr = array();
		    $ctr = 0;
		    // [A.2] Connect, which proceeds to issue a query command. 
		    $conn = sqlsrv_connect($serverName, $connectionOptions);  
		    if( $conn == true)
		    {
		        echo "Connection was established"; 
		        echo "<br>";
		 
		        $tsql = "SELECT [CompanyName] FROM SalesLT.Customer";
		        $getProducts = sqlsrv_query($conn, $tsql);
		        if ($getProducts == FALSE)
		            die(FormatErrors(sqlsrv_errors()));
		        $productCount = 0;
		        $ctr = 0;
		        while($row = sqlsrv_fetch_array($getProducts, SQLSRV_FETCH_ASSOC))
		        {   
		            $ctr++;
		            echo($row['CompanyName']);
		            echo("<br/>");
		            $productCount++;
		            if($ctr>10)
		                break;
		        }
		        sqlsrv_free_stmt($getProducts);
		        break;
		    }
		    // Adds any the error codes from the SQL Exception to an array.
		    else {  
		        if( ($errors = sqlsrv_errors() ) != null) {
		            foreach( $errors as $error ) {
		                $errorArr[$ctr] = $error['code'];
		                $ctr = $ctr + 1;
		            }
		        }
		        $isTransientError = TRUE;
		        // [A.4] Check whether sqlExc.Number is on the whitelist of transients.
		        $isTransientError = IsTransientStatic($errorArr);
		        if ($isTransientError == TRUE)  // Is a static persistent error...
		        { 
		            echo("Persistent error suffered, SqlException.Number==". $errorArr[0].". Program Will terminate."); 
		            echo "<br>";
		            // [A.5] Either the connection attempt or the query command attempt suffered a persistent SqlException.
		            // Break the loop, let the hopeless program end.
		            exit(0);
		        }
		        // [A.6] The SqlException identified a transient error from an attempt to issue a query command.
		        // So let this method reloop and try again. However, we recommend that the new query
		        // attempt should start at the beginning and establish a new connection.
		        if ($cc >= $maxCountTriesConnectAndQuery)
		        {
		            echo "Transient errors suffered in too many retries - " . $cc ." Program will terminate.";
		            echo "<br>";
		            exit(0);
		        }
		        echo("Transient error encountered.  SqlException.Number==". $errorArr[0]. " . Program might retry by itself.");  
		        echo "<br>";
		        echo $cc . " Attempts so far. Might retry.";
		        echo "<br>";
		        // A very simple retry strategy, a brief pause before looping. This could be changed to exponential if you want.
		        sleep(1*$secondsBetweenRetries);
		    }
		    // [A.3] All has gone well, so let the program end.
		}
		function IsTransientStatic($errorArr) {
		    $arrayOfTransientErrorNumbers = array(4060, 10928, 10929, 40197, 40501, 40613);
		    $result = array_intersect($arrayOfTransientErrorNumber, $errorArr);
		    $count = count($result);
		    if($count >= 0) //change to > 0 later.
		        return TRUE;
		}
	?>
	
## Étapes suivantes

Pour plus d'informations sur l'installation et l'utilisation de PHP, consultez [Accès aux bases de données du serveur SQL avec PHP](http://technet.microsoft.com/library/cc793139.aspx).

 

<!---HONumber=Oct15_HO3-->