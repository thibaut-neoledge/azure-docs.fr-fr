---
title: "Se connecter à Azure SQL Database à l’aide de Python | Microsoft Docs"
description: "Cet article présente un exemple de code Python que vous pouvez utiliser pour vous connecter à la base de données Azure SQL Database et pour interroger cette dernière."
services: sql-database
documentationcenter: 
author: meet-bhagdev
manager: jhubbard
editor: 
ms.assetid: 452ad236-7a15-4f19-8ea7-df528052a3ad
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: hero-article
ms.date: 05/24/2017
ms.author: meetb
ms.translationtype: Human Translation
ms.sourcegitcommit: ff2fb126905d2a68c5888514262212010e108a3d
ms.openlocfilehash: 99195b43a1577f978562864bac5fa12cdeb95d63
ms.contentlocale: fr-fr
ms.lasthandoff: 06/17/2017

---
# <a name="azure-sql-database-use-python-to-connect-and-query-data"></a>Azure SQL Database : utilisez Python pour vous connecter et interroger des données

 Ce guide de démarrage rapide indique comment utiliser [Python](https://python.org) pour se connecter à une base de données SQL Azure, puis utiliser les instructions Transact-SQL pour interroger, insérer, mettre à jour et supprimer des données dans la base de données à partir des plateformes Mac OS, Ubuntu Linux et Windows.

## <a name="prerequisites"></a>Composants requis

Ce guide de démarrage rapide utilise comme point de départ les ressources créées dans l’une de ces instructions de démarrage rapide :

- [Créer une base de données - Portail](sql-database-get-started-portal.md)
- [Créer une base de données - CLI](sql-database-get-started-cli.md)
- [Créer une base de données - PowerShell](sql-database-get-started-powershell.md)

## <a name="install-the-python-and-database-communication-libraries"></a>Installer les bibliothèques de communication de base de données et Python

Les étapes de cette section supposent que vous connaissez le développement avec Python et que vous ne savez pas utiliser la base de données SQL Azure. Si vous êtes novice en développement avec Python, consultez [Créer une application à l’aide de SQL Server](https://www.microsoft.com/sql-server/developer-get-started/) et sélectionnez **Python**, puis sélectionnez votre système d’exploitation.

### <a name="mac-os"></a>**Mac OS**
Ouvrez votre terminal et accédez au répertoire dans lequel vous envisagez de créer votre script python. Entrez les commandes suivantes pour installer **brew**, le **pilote Microsoft ODBC pour Mac** et **pyodbc**. pyodbc utilise le pilote Microsoft ODBC sur Linux pour se connecter aux bases de données SQL.

``` bash
ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"
brew tap microsoft/msodbcsql https://github.com/Microsoft/homebrew-msodbcsql-preview
brew update
brew install msodbcsql 
#for silent install ACCEPT_EULA=y brew install msodbcsql
sudo pip install pyodbc==3.1.1
```

### <a name="linux-ubuntu"></a>**Linux (Ubuntu)**
Ouvrez votre terminal et accédez au répertoire dans lequel vous envisagez de créer votre script python. Entrez les commandes suivantes pour installer le **pilote Microsoft ODBC pour Linux** et **pyodbc**. pyodbc utilise le pilote Microsoft ODBC sur Linux pour se connecter aux bases de données SQL.

```bash
sudo su
curl https://packages.microsoft.com/keys/microsoft.asc | apt-key add -
curl https://packages.microsoft.com/config/ubuntu/16.04/prod.list > /etc/apt/sources.list.d/mssql.list
exit
sudo apt-get update
sudo apt-get install msodbcsql mssql-tools unixodbc-dev
sudo pip install pyodbc==3.1.1
```

### <a name="windows"></a>**Windows**
Installez le [pilote ODBC Microsoft 13.1](https://www.microsoft.com/download/details.aspx?id=53339) (mettez à niveau le pilote si vous y êtes invité). Pyodbc utilise le pilote ODBC Microsoft sous Linux pour se connecter aux bases de données SQL. 

Puis installez **pyodbc** avec pip.

```cmd
pip install pyodbc==3.1.1
```

Vous trouverez des instructions pour activer l’utilisation de pip [ici](http://stackoverflow.com/questions/4750806/how-to-install-pip-on-windows).

## <a name="get-connection-information"></a>Obtenir des informations de connexion

Obtenez les informations de connexion requises pour la connexion à la base de données SQL Azure. Vous aurez besoin du nom du serveur complet, du nom de la base de données et des informations de connexion dans les procédures suivantes.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Bases de données SQL** dans le menu de gauche, puis cliquez sur votre base de données dans la page **Bases de données SQL**. 
3. Sur la page **Vue d’ensemble** de votre base de données, vérifiez le nom complet du serveur, comme indiqué dans l’image ci-dessous. Vous pouvez pointer sur le nom du serveur pour afficher l’option **Cliquez pour copier**. 

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Si vous avez oublié vos informations de connexion au serveur, accédez à la page du serveur SQL Database pour afficher le nom de l’administrateur du serveur et, si nécessaire, réinitialiser le mot de passe.     
   
## <a name="select-data"></a>Sélectionner des données

Utilisez le code suivant pour rechercher les 20 premiers produits par catégorie à l’aide de la fonction [pyodbc.connect](https://github.com/mkleehammer/pyodbc/wiki) et d’une instruction Transact-SQL [SELECT](https://docs.microsoft.com/sql/t-sql/queries/select-transact-sql). La fonction [cursor.execute](https://github.com/mkleehammer/pyodbc/wiki/Cursor) est utilisée pour récupérer un jeu de résultats d’une requête à partir de SQL Database. Cette fonction accepte une requête et renvoie un jeu de résultats qui peut être itéré à l’aide de **cursor.fetchone()**. Remplacez les paramètres du serveur, de la base de données, du nom d’utilisateur et du mot de passe par les valeurs spécifiées lors de la création de la base de données avec les exemples de données AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
cursor.execute("SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName FROM [SalesLT].[ProductCategory] pc JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid")
row = cursor.fetchone()
while row:
    print str(row[0]) + " " + str(row[1])
    row = cursor.fetchone()
```

## <a name="insert-data"></a>Insertion des données
Utilisez le code suivant pour insérer un nouveau produit dans la table SalesLT.Product à l’aide de la fonction [cursor.execute](https://github.com/mkleehammer/pyodbc/wiki/Cursor) et de l’instruction Transact-SQL [INSERT](https://docs.microsoft.com/sql/t-sql/statements/insert-transact-sql). Remplacez les paramètres du serveur, de la base de données, du nom d’utilisateur et du mot de passe par les valeurs spécifiées lors de la création de la base de données avec les exemples de données AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
with cursor.execute("INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) OUTPUT INSERTED.ProductID VALUES ('BrandNewProduct', '200989', 'Blue', 75, 80, '7/1/2016')"): 
    print ('Successfuly Inserted!')
cnxn.commit()
```

## <a name="update-data"></a>Mettre à jour des données
Utilisez le code suivant pour mettre à jour le nouveau produit que vous avez ajouté précédemment à l’aide de la fonction [cursor.execute](https://github.com/mkleehammer/pyodbc/wiki/Cursor) et de l’instruction Transact-SQL [UPDATE](https://docs.microsoft.com/sql/t-sql/queries/update-transact-sql). Remplacez les paramètres du serveur, de la base de données, du nom d’utilisateur et du mot de passe par les valeurs spécifiées lors de la création de la base de données avec les exemples de données AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "UPDATE SalesLT.Product SET ListPrice = ? WHERE Name = ?"
with cursor.execute(tsql,50,'BrandNewProduct'):
    print ('Successfuly Updated!')
cnxn.commit()

```

## <a name="delete-data"></a>Suppression de données
Utilisez le code suivant pour supprimer le nouveau produit que vous avez ajouté précédemment à l’aide de la fonction [cursor.execute](https://github.com/mkleehammer/pyodbc/wiki/Cursor) et de l’instruction Transact-SQL [DELETE](https://docs.microsoft.com/sql/t-sql/statements/delete-transact-sql). Remplacez les paramètres du serveur, de la base de données, du nom d’utilisateur et du mot de passe par les valeurs spécifiées lors de la création de la base de données avec les exemples de données AdventureWorksLT.

```Python
import pyodbc
server = 'your_server.database.windows.net'
database = 'your_database'
username = 'your_username'
password = 'your_password'
driver= '{ODBC Driver 13 for SQL Server}'
cnxn = pyodbc.connect('DRIVER='+driver+';PORT=1433;SERVER='+server+';PORT=1443;DATABASE='+database+';UID='+username+';PWD='+ password)
cursor = cnxn.cursor()
tsql = "DELETE FROM SalesLT.Product WHERE Name = ?"
with cursor.execute(tsql,'BrandNewProduct'):
    print ('Successfuly Deleted!')
cnxn.commit()
```

## <a name="next-steps"></a>Étapes suivantes

- [Concevoir votre première base de données SQL Azure](sql-database-design-first-database.md)
- [Python SQL Driver](https://docs.microsoft.com/sql/connect/python/python-driver-for-sql-server/) (Pilote SQL Python)
- [Centre de développement Python](https://azure.microsoft.com/develop/python/?v=17.23h)


