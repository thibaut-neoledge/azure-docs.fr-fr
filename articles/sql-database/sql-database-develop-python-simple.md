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
ms.date: 10/05/2016
ms.author: meetb
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: 5f3a4e49646063b41af5a9941f27291762f5336e


---
# <a name="connect-to-sql-database-by-using-python"></a>Connexion à SQL Database à l’aide de Python
[!INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Cette rubrique montre comment se connecter à une base de données Azure SQL Database et l’interroger à l’aide de Python. Vous pouvez exécuter cet exemple à partir des plateformes Windows, Ubuntu Linux ou Mac.

## <a name="step-1-create-a-sql-database"></a>Étape 1 : créer une base de données SQL
Consultez la [page de prise en main](sql-database-get-started.md) pour apprendre à créer un exemple de base de données.  Il est important que vous suiviez le guide pour créer un **modèle de base de données AdventureWorks**. Les exemples ci-dessous fonctionnent uniquement avec le **schéma AdventureWorks**. Une fois que vous avez créé votre base de données, assurez-vous d’activer l’accès à votre adresse IP en activant les règles de pare-feu, comme décrit dans la [page de prise en main](sql-database-get-started.md).

## <a name="step-2-configure-development-environment"></a>Étape 2 : configurer l’environnement de développement
### <a name="mac-os"></a>**Mac OS**
### <a name="install-the-required-modules"></a>Installer les modules requis
Ouvrez votre terminal et procédez aux installations suivantes :

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
    brew install FreeTDS
    sudo -H pip install pymssql==2.1.1

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Ouvrez votre terminal et accédez au répertoire dans lequel vous envisagez de créer votre script python. Entrez les commandes suivantes pour installer **FreeTDS** et **pymssql**. pymssql utilise FreeTDS pour se connecter aux bases de données SQL.

    sudo apt-get --assume-yes update
    sudo apt-get --assume-yes install freetds-dev freetds-bin
    sudo apt-get --assume-yes install python-dev python-pip
    sudo pip install pymssql==2.1.1

### <a name="windows"></a>**Windows**
Installez pymssql à partir [**d’ici**](http://www.lfd.uci.edu/~gohlke/pythonlibs/#pymssql). 

Veillez choisir le fichier whl correct. Par exemple : si vous utilisez Python 2.7 sur un ordinateur 64 bits, choisissez : pymssql‑2.1.1‑cp27‑none‑win_amd64.whl. Une fois le fichier .whl téléchargé, placez-le dans le dossier C:/Python27.

À présent, installez le pilote pymssql à l’aide de pip à partir de la ligne de commande. cd dans C:/Python27 et exécutez ce qui suit :

    pip install pymssql‑2.1.1‑cp27‑none‑win_amd64.whl

Vous trouverez des instructions pour activer l’utilisation de pip [ici](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows).

## <a name="step-3-run-sample-code"></a>Étape 3 : Exécuter l’exemple de code
Créez un fichier appelé **sql_sample.py** et collez le code suivant à l’intérieur. Vous pouvez effectuer cette opération à partir de la ligne de commande à l’aide de :

    python sql_sample.py

### <a name="connect-to-your-sql-database"></a>Se connecter à la base de données SQL
La fonction [pymssql.connect](http://pymssql.org/en/latest/ref/pymssql.html) est utilisée pour la connexion à la base de données SQL.

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')


### <a name="execute-an-sql-select-statement"></a>Exécuter une instruction SQL SELECT
La fonction [cursor.execute](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.execute) peut être utilisée pour récupérer un jeu de résultats d'une requête à partir d'une base de données SQL. Cette fonction accepte n’importe quelle requête et renvoie un jeu de résultats qui peut être itéré à l’aide de [cursor.fetchone()](http://pymssql.org/en/latest/ref/pymssql.html#pymssql.Cursor.fetchone).

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute('SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC;')
    row = cursor.fetchone()
    while row:
        print str(row[0]) + " " + str(row[1]) + " " + str(row[2])     
        row = cursor.fetchone()


### <a name="insert-a-row-pass-parameters-and-retrieve-the-generated-primary-key"></a>Insérer une ligne, transmettre des paramètres et récupérer la clé primaire générée
Dans la base de données SQL, la propriété [IDENTITY](https://msdn.microsoft.com/library/ms186775.aspx) et l’objet [SEQUENCE](https://msdn.microsoft.com/library/ff878058.aspx) peuvent être utilisés pour générer automatiquement des valeurs de [clé primaire](https://msdn.microsoft.com/library/ms179610.aspx). 

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
    cursor.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('SQL Server Express', 'SQLEXPRESS', 0, 0, CURRENT_TIMESTAMP)")
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

    import pymssql
    conn = pymssql.connect(server='yourserver.database.windows.net', user='yourusername@yourserver', password='yourpassword', database='AdventureWorks')
    cursor = conn.cursor()
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




<!--HONumber=Nov16_HO3-->


