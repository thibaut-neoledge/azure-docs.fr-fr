---
title: "Se connecter à Azure SQL Database avec PHP | Microsoft Docs"
description: "Cet article présente un exemple de code PHP que vous pouvez utiliser pour vous connecter à la base de données Azure SQL Database et pour interroger cette dernière."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 4e71db4a-a22f-4f1c-83e5-4a34a036ecf3
ms.service: sql-database
ms.custom: quick start connect
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: php
ms.topic: article
ms.date: 04/17/2017
ms.author: meetb;carlrab;sstein
translationtype: Human Translation
ms.sourcegitcommit: db7cb109a0131beee9beae4958232e1ec5a1d730
ms.openlocfilehash: e39d108e9d6962647cbf76e50299b73939fe5977
ms.lasthandoff: 04/18/2017


---
# <a name="azure-sql-database-use-php-to-connect-and-query-data"></a>Azure SQL Database : utilisez PHP pour vous connecter et interroger des données

Ce guide de démarrage rapide indique comment utiliser [PHP](http://php.net/manual/en/intro-whatis.php) pour se connecter à une base de données Azure SQL, puis utiliser les instructions Transact-SQL pour interroger, insérer, mettre à jour et supprimer des données dans la base de données à partir des plateformes Mac OS, Ubuntu Linux et Windows.

Ce guide de démarrage rapide utilise comme point de départ les ressources créées dans l’une de ces instructions de démarrage rapide :

- [Créer une base de données - Portail](sql-database-get-started-portal.md)
- [Créer une base de données - CLI](sql-database-get-started-cli.md)

## <a name="install-php-and-database-communications-software"></a>Installer le logiciel de communication de la base de données et PHP
### <a name="mac-os"></a>**Mac OS**
Ouvrez votre terminal et saisissez les commandes suivantes pour installer **brew**, le **pilote ODBC Microsoft pour Mac** et les **pilotes PHP Microsoft pour SQL Server**. 

```bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql 
pecl install sqlsrv-4.1.7preview
pecl install pdo_sqlsrv-4.1.7preview
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Saisissez les commandes suivantes pour installer le **pilote ODBC Microsoft pour Linux** et les **pilotes PHP Microsoft pour SQL Server**.

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev gcc g++ php-dev
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

## <a name="get-connection-information"></a>Obtenir des informations de connexion

Récupérez la chaîne de connexion dans le portail Azure. Cette chaîne de connexion vous permet de vous connecter à la base de données SQL Azure.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Bases de données SQL** dans le menu de gauche, puis cliquez sur votre base de données dans la page **Bases de données SQL**. 
3. Sur la page **Vue d’ensemble** de votre base de données, vérifiez le nom complet du serveur, comme indiqué dans l’image ci-dessous. Vous pouvez pointer sur le nom du serveur pour afficher l’option **Cliquez pour copier**.  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Si vous avez oublié vos informations de connexion à votre serveur Azure SQL Database, accédez à la page du serveur SQL Database pour afficher le nom de l’administrateur du serveur et, si nécessaire, réinitialiser le mot de passe.     
    
## <a name="select-data"></a>Sélectionner des données
Utilisez le code suivant pour interroger votre base de données SQL Azure à l’aide de la fonction [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) avec une instruction Transact-SQL [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql). La fonction sqlsrv_query peut être utilisée pour récupérer un jeu de résultats d’une requête à partir d’une base de données SQL. Cette fonction accepte une requête et renvoie un jeu de résultats qui peut être itéré à l’aide de [sqlsrv_fetch_array()](http://php.net/manual/en/function.sqlsrv-fetch-array.php). Remplacez les paramètres du serveur, de la base de données, du nom d’utilisateur et du mot de passe par les valeurs spécifiées lors de la création de la base de données avec les exemples de données AdventureWorksLT. 

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName
     FROM [SalesLT].[ProductCategory] pc
     JOIN [SalesLT].[Product] p
     ON pc.productcategoryid = p.productcategoryid";
$getResults= sqlsrv_query($conn, $tsql);
echo ("Reading data from table" . PHP_EOL);
if ($getResults == FALSE)
    echo (sqlsrv_errors());
while ($row = sqlsrv_fetch_array($getResults, SQLSRV_FETCH_ASSOC)) {
    echo ($row['CategoryName'] . " " . $row['ProductName'] . PHP_EOL);
}
sqlsrv_free_stmt($getResults);
?>
```


## <a name="insert-data"></a>Insertion des données
Utilisez le code suivant pour insérer un nouveau produit dans la table SalesLT.Product dans la base de données spécifiée à l’aide de la fonction [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) et de l’instruction Transact-SQL [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql). Remplacez les paramètres du serveur, de la base de données, du nom d’utilisateur et du mot de passe par les valeurs spécifiées lors de la création de la base de données avec les exemples de données AdventureWorksLT. 

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";
$params = array("BrandNewProduct", "200989", "Blue", 75, 80, "7/1/2016");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) inserted" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
?>
```

## <a name="update-data"></a>Mettre à jour des données
Utilisez le code suivant pour mettre à jour les données de votre base de données SQL Azure à l’aide de la fonction [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) et de l’instruction Transact-SQL [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql). Remplacez les paramètres du serveur, de la base de données, du nom d’utilisateur et du mot de passe par les valeurs spécifiées lors de la création de la base de données avec les exemples de données AdventureWorksLT.

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "UPDATE SalesLT.Product SET ListPrice =? WHERE Name = ?";
$params = array(50,"BrandNewProduct");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) updated" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
?>
```

## <a name="delete-data"></a>Suppression de données
Utilisez le code suivant pour supprimer les données de votre base de données SQL Azure à l’aide de la fonction [sqlsrv_query()](https://docs.microsoft.com/sql/connect/php/sqlsrv-query) et de l’instruction Transact-SQL [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql). Remplacez les paramètres du serveur, de la base de données, du nom d’utilisateur et du mot de passe par les valeurs spécifiées lors de la création de la base de données avec les exemples de données AdventureWorksLT.

```PHP
<?php
$serverName = "your_server.database.windows.net";
$connectionOptions = array(
    "Database" => "your_database",
    "Uid" => "your_username",
    "PWD" => "your_password"
);
//Establishes the connection
$conn = sqlsrv_connect($serverName, $connectionOptions);
$tsql= "DELETE FROM SalesLT.Product WHERE Name = ?";
$params = array("BrandNewProduct");
$getResults= sqlsrv_query($conn, $tsql, $params);
if ($getResults == FALSE)
    echo print_r(sqlsrv_errors(), true);
else{
    $rowsAffected = sqlsrv_rows_affected($getResults);
    echo ($rowsAffected. " row(s) deleted" . PHP_EOL);
    sqlsrv_free_stmt($getResults);
}
```

## <a name="next-steps"></a>Étapes suivantes

- En savoir plus sur le [pilote PHP Microsoft pour SQL Server](https://github.com/Microsoft/msphpsql/).
- [Problèmes avec les fichiers/posez des questions](https://github.com/Microsoft/msphpsql/issues).
- Pour vous connecter et envoyer des requêtes à l’aide de SQL Server Management Studio, consultez [Se connecter à la base de données SQL avec SQL Server Management Studio et exécuter un exemple de requête T-SQL](sql-database-connect-query-ssms.md).
- Pour vous connecter et exécuter des requêtes à l’aide de Visual Studio Code, consultez l’article décrivant comment [se connecter et effectuer des requêtes avec Visual Studio Code](sql-database-connect-query-vscode.md).
- Pour vous connecter et exécuter des requêtes avec .NET, consultez la page [Se connecter et effectuer des requêtes avec .NET](sql-database-connect-query-dotnet.md).
- Pour vous connecter et exécuter des requêtes avec Node.js, consultez la page [se connecter et effectuer des requêtes avec Node.js](sql-database-connect-query-nodejs.md).
- Pour vous connecter et exécuter des requêtes avec Java, consultez la page [se connecter et effectuer des requêtes avec Java](sql-database-connect-query-java.md).
- Pour vous connecter et exécuter des requêtes avec Python, consultez la page [se connecter et effectuer des requêtes avec Python](sql-database-connect-query-python.md).
- Pour vous connecter et exécuter des requêtes avec Ruby, consultez la page [se connecter et effectuer des requêtes avec Ruby](sql-database-connect-query-ruby.md).

