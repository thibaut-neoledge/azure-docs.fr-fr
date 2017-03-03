---
title: "Connexion à une base de données SQL avec PHP sous Windows | Microsoft Docs"
description: "Présente un exemple de programme PHP qui se connecte à la base de données SQL Azure à partir d&quot;un client Windows et fournit des liens vers les composants logiciels nécessaires requis par le client."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 02/13/2017
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 94fa09526683582bc017213d0ad9455f31cb22ae
ms.openlocfilehash: fba66e9d41daa2df34fbb3ffd8c92e664eaa560e
ms.lasthandoff: 02/21/2017


---

# <a name="connect-to-sql-database-by-using-php"></a>Connexion à SQL Database à l’aide de PHP sur Windows
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Cette rubrique montre comment se connecter à une base de données Azure SQL Database et l’interroger à l’aide de PHP. Vous pouvez exécuter cet exemple à partir des plateformes Windows ou Linux. 


## <a name="step-1-create-a-sql-database"></a>Étape 1 : créer une base de données SQL
Consultez la [page de prise en main](sql-database-get-started.md) pour apprendre à créer un exemple de base de données.  Il est important que vous suiviez le guide pour créer un **modèle de base de données AdventureWorks**. Les exemples ci-dessous fonctionnent uniquement avec le **schéma AdventureWorks**. Une fois que vous avez créé votre base de données, assurez-vous d’activer l’accès à votre adresse IP en activant les règles de pare-feu, comme décrit dans la [page de prise en main](sql-database-get-started.md).

## <a name="step-2-configure-development-environment"></a>Étape 2 : configurer l’environnement de développement

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Ouvrez votre terminal et accédez au répertoire dans lequel vous envisagez de créer votre script python. Entrez les commandes suivantes pour installer le **pilote Microsoft ODBC pour Linux**, **pdo_sqlsrv** et **sqlsrv**. Pilote PHP Microsoft pour SQL Server utilise le pilote Microsoft ODBC sur Linux pour se connecter aux bases de données SQL.

```
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql-release.list
exit
sudo apt-get update
sudo ACCEPT_EULA=Y apt-get install msodbcsql unixodbc-dev gcc g++ build-essential
sudo pecl install sqlsrv pdo_sqlsrv
sudo echo "extension= pdo_sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
sudo echo "extension= sqlsrv.so" >> `php --ini | grep "Loaded Configuration" | sed -e "s|.*:\s*||"`
```

### <a name="windows"></a>**Windows**
- Installez PHP 7.1.1 (x64) [à partir de WebPlatform Installer](https://www.microsoft.com/web/downloads/platform.aspx?lang=) 
- Installez le [pilote Microsoft ODBC 13.1](https://www.microsoft.com/download/details.aspx?id=53339). 
- Téléchargez les DLL non-thread safe pour le [Pilote PHP Microsoft pour SQL Server](https://pecl.php.net/package/sqlsrv/4.1.6.1/windows) et placez les fichiers binaires dans le dossier PHP\v7.x\ext.
- Ensuite, modifiez votre fichier php.ini (C:\Program Files\PHP\v7.1\php.ini) en ajoutant la référence à la DLL. Par exemple :
      
      extension=php_sqlsrv.dll
      extension=php_pdo_sqlsrv.dll

À ce stade, vous devriez avoir les DLL enregistrées avec PHP.

## <a name="step-3-run-sample-code"></a>Étape 3 : Exécuter l’exemple de code
Créez un fichier appelé **sql_sample.php** et collez le code suivant à l’intérieur. Vous pouvez effectuer cette opération à partir de la ligne de commande à l’aide de :

```
php sql_sample.php
```

### <a name="connect-to-your-sql-database"></a>Se connecter à la base de données SQL
La fonction [sqlsrv connect](http://php.net/manual/en/function.sqlsrv-connect.php) est utilisée pour la connexion à la base de données SQL.

```
<?php
$serverName = "yourserver.database.windows.net";
$connectionOptions = array(
    "Database" => "yourdatabase",
    "Uid" => "yourusername",
    "PWD" => "yourpassword"
    );
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if($conn)
    echo "Connected!"
?>
```

### <a name="execute-an-sql-select-statement"></a>Exécuter une instruction SQL SELECT
La fonction [sqlsrv_query()](http://php.net/manual/en/function.sqlsrv-query.php) peut être utilisée pour récupérer un jeu de résultats d'une requête à partir d'une base de données SQL. 

```
<?php
$serverName = "yourserver.database.windows.net";
$connectionOptions = array(
    "Database" => "yourdatabase",
    "Uid" => "yourusername",
    "PWD" => "yourpassword"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if($conn)
    echo "Connected!"
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
function FormatErrors( $errors )
{
    /* Display errors. */
    echo "Error information: ";
    
    foreach ( $errors as $error )
    {
        echo "SQLSTATE: ".$error['SQLSTATE']."";
        echo "Code: ".$error['code']."";
        echo "Message: ".$error['message']."";
    }
}
?>
```

### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>Insérer une ligne, transmettre des paramètres et récupérer la clé primaire générée
Dans la base de données SQL, la propriété [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) et l’objet [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) peuvent être utilisés pour générer automatiquement des valeurs de [clé primaire](https://msdn.microsoft.com/library/ms179610.aspx). 


```
<?php
$serverName = "yourserver.database.windows.net";
$connectionOptions = array(
    "Database" => "yourdatabase",
    "Uid" => "yourusername",
    "PWD" => "yourpassword"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
if($conn)
    echo "Connected!"
$tsql = "INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server 1', 'SQL Server 2', 0, 0, getdate())";  
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
function FormatErrors( $errors )
{
    /* Display errors. */
    echo "Error information: ";
    foreach ( $errors as $error )
    {
        echo "SQLSTATE: ".$error['SQLSTATE']."";
        echo "Code: ".$error['code']."";
        echo "Message: ".$error['message']."";
    }
}
?>
```


## <a name="next-steps"></a>Étapes suivantes
* Consultez la [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md)
* Plus d’informations sur le [pilote PHP Microsoft pour SQL Server](https://github.com/Microsoft/msphpsql/)
* [Problèmes avec les fichiers/posez des questions](https://github.com/Microsoft/msphpsql/issues).

## <a name="additional-resources"></a>Ressources supplémentaires
* [Modèles de conception pour les applications SaaS mutualisées avec Base de données SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explorez toutes les [fonctionnalités de la base de données SQL](https://azure.microsoft.com/services/sql-database/)

