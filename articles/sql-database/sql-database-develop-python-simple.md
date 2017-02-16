---
title: "Se connecter à SQL Database à l’aide de Python | Microsoft Docs"
description: "Cette rubrique présente un exemple de code Python que vous pouvez utiliser pour vous connecter à une base de données SQL Azure."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: development
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 01/03/2016
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 631baac839f4045c4b0fcf23810d9459c45a4998
ms.openlocfilehash: 558d6660235a76bc7f5d23e7b28025496c2d8271


---
# <a name="connect-to-sql-database-by-using-python"></a>Connexion à SQL Database à l’aide de Python
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Cette rubrique montre comment se connecter à une base de données Azure SQL Database et l’interroger à l’aide de Python. Vous pouvez exécuter cet exemple à partir des plateformes Windows, Ubuntu Linux ou Mac.

## <a name="step-1-create-a-sql-database"></a>Étape 1 : créer une base de données SQL
Consultez la [page de prise en main](sql-database-get-started.md) pour apprendre à créer un exemple de base de données.  Il est important que vous suiviez le guide pour créer un **modèle de base de données AdventureWorks**. Les exemples ci-dessous fonctionnent uniquement avec le **schéma AdventureWorks**. Une fois que vous avez créé votre base de données, assurez-vous d’activer l’accès à votre adresse IP en activant les règles de pare-feu, comme décrit dans la [page de prise en main](sql-database-get-started.md).

## <a name="step-2-configure-development-environment"></a>Étape 2 : configurer l’environnement de développement
### <a name="mac-os"></a>**Mac OS**
Ouvrez votre terminal et accédez au répertoire dans lequel vous envisagez de créer votre script python. Entrez les commandes suivantes pour installer **brew**, **FreeTDS** et **pyodbc**. pyodbc utilise FreeTDS sur Mac OS pour se connecter aux bases de données SQL.

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    brew uninstall FreeTDS #if you have an existing installed FreeTDS
    brew update
    brew doctor
    brew install freetds --with-unixodbc
    sudo pip install pyodbc==3.1.1

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Ouvrez votre terminal et accédez au répertoire dans lequel vous envisagez de créer votre script python. Entrez les commandes suivantes pour installer le **pilote Microsoft ODBC pour Linux** et **pyodbc**. pyodbc utilise le pilote Microsoft ODBC sur Linux pour se connecter aux bases de données SQL.

    sudo su
    curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
    curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
    exit
    sudo apt-get update
    sudo apt-get install msodbcsql mssql-tools unixodbc-dev-utf16
    sudo pip install pyodbc==3.1.1

### <a name="windows"></a>**Windows**
Installez le [pilote Microsoft ODBC 13.1](https://www.microsoft.com/en-us/download/details.aspx?id=53339). pyodbc utilise le pilote Microsoft ODBC sur Linux pour se connecter aux bases de données SQL. 

Puis installez pyodbc avec pip.

    pip install pyodbc==3.1.1

Vous trouverez des instructions pour activer l’utilisation de pip [ici](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows).

## <a name="step-3-run-sample-code"></a>Étape 3 : Exécuter l’exemple de code
Créez un fichier appelé **sql_sample.py** et collez le code suivant à l’intérieur. Vous pouvez effectuer cette opération à partir de la ligne de commande à l’aide de :

    python sql_sample.py

### <a name="connect-to-your-sql-database"></a>Se connecter à la base de données SQL
La fonction [pyodbc.connect](https://mkleehammer.github.io/pyodbc/api-connection.html) est utilisée pour la connexion à la base de données SQL.

    import pyodbc
    server = 'yourserver.database.windows.net'
    database = 'yourdatabase'
    username = 'yourusername'
    password = 'yourpassword'
    #for mac
    #driver = '{/usr/local/lib/libtdsodbc.so}'
    #for linux of windows
    driver= '{ODBC Driver 13 for SQL Server}'
    cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
    cursor = cnxn.cursor()
    cursor.execute("select @@VERSION")
    row = cursor.fetchone()
    if row:
        print row

### <a name="execute-an-sql-select-statement"></a>Exécuter une instruction SQL SELECT
La fonction [cursor.execute](https://mkleehammer.github.io/pyodbc/api-cursor.html) peut être utilisée pour récupérer un jeu de résultats d'une requête à partir d'une base de données SQL. Cette fonction accepte n’importe quelle requête et renvoie un jeu de résultats qui peut être itéré à l’aide de [cursor.fetchone()](https://mkleehammer.github.io/pyodbc/api-cursor.html).

    import pyodbc
    server = 'yourserver.database.windows.net'
    database = 'yourdatabase'
    username = 'yourusername'
    password = 'yourpassword'
    #for mac
    driver = '{/usr/local/lib/libtdsodbc.so}'
    #for linux or windows
    driver= '{ODBC Driver 13 for SQL Server}'
    cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
    cursor = cnxn.cursor()
    cursor.execute("select @@VERSION")
    row = cursor.fetchone()
    while row:
        print str(row[0]) + " " + str(row[1]) + " " + str(row[2])     
        row = cursor.fetchone()


### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>Insérer une ligne, transmettre des paramètres et récupérer la clé primaire générée
Dans la base de données SQL, la propriété [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) et l’objet [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) peuvent être utilisés pour générer automatiquement des valeurs de [clé primaire](https://msdn.microsoft.com/library/ms179610.aspx). 

    import pyodbc
    server = 'yourserver.database.windows.net'
    database = 'yourdatabase'
    username = 'yourusername'
    password = 'yourpassword'
    #for mac
    #driver = '{/usr/local/lib/libtdsodbc.so}'
    #for linux or windows
    driver= '{ODBC Driver 13 for SQL Server}'
    cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
    cursor = cnxn.cursor()
    cursor.execute("select @@VERSION")
    row = cursor.fetchone()
    while row:
        print "Inserted Product ID : " +str(row[0])
        row = cursor.fetchone()


### <a name="transactions"></a>Transactions
Cet exemple de code illustre l'utilisation de transactions dans lesquelles vous :

* Commencez une transaction
* Insérez une ligne de données
* Restaurez votre transaction pour annuler l’insertion 

Collez le code suivant dans sql_sample.py.

    import pyodbc
    server = 'yourserver.database.windows.net'
    database = 'yourdatabase'
    username = 'yourusername'
    password = 'yourpassword'
    #for mac
    #driver = '{/usr/local/lib/libtdsodbc.so}'
    #for linux or windows
    driver= '{ODBC Driver 13 for SQL Server}'
    cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
    cursor = cnxn.cursor()
    cursor.execute("BEGIN TRANSACTION")
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, CURRENT_TIMESTAMP)")
    cnxn.rollback()

## <a name="next-steps"></a>Étapes suivantes
* Consultez la [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md)
* Plus d’informations sur le [pilote Python Microsoft pour SQL Server](https://msdn.microsoft.com/library/mt652092.aspx)
* Visitez le [Centre de développement Python](/develop/python/).

## <a name="additional-resources"></a>Ressources supplémentaires
* [Modèles de conception pour les applications SaaS mutualisées avec Base de données SQL Azure](sql-database-design-patterns-multi-tenancy-saas-applications.md)
* Explorez toutes les [fonctionnalités de la base de données SQL](https://azure.microsoft.com/services/sql-database/)



<!--HONumber=Jan17_HO1-->


