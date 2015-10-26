<properties 
	pageTitle="Se connecter à une base de données SQL à l’aide de Ruby avec TinyTDS sur Mac OS X (Yosemite)" 
	description="Donnez un exemple de code Ruby que vous pouvez exécuter sur Mac OS X (Yosemite) pour vous connecter à Azure SQL Database."
	services="sql-database" 
	documentationCenter="" 
	authors="ajlam" 
	manager="jeffreyg" 
	editor=""/>


<tags 
	ms.service="sql-database" 
	ms.workload="sql-database" 
	ms.tgt_pltfrm="na" 
	ms.devlang="ruby" 
	ms.topic="article" 
	ms.date="07/20/2015" 
	ms.author="andrela"/>


# Se connecter à une base de données SQL à l’aide de Ruby sur Mac OS X (Yosemite)

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Cette rubrique présente un exemple de code Ruby qui s’exécute sur un ordinateur Mac exécutant Yosemite, pour la connexion à une base de données Azure SQL Database.

## Installer les modules requis

Ouvrez votre terminal et procédez aux installations suivantes :

**1) Homebrew** : exécutez la commande suivante à partir de votre terminal. Cette commande entraîne le téléchargement du gestionnaire de package Homebrew sur votre ordinateur.

    ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

**2) FreeTDS** : exécutez la commande suivante à partir de votre terminal. La commande installe FreeTDS sur votre ordinateur. L’application est obligatoire pour que TinyTDS fonctione.

    brew install FreeTDS

**3) TinyTDS** : exécutez la commande suivante à partir de votre terminal. Cette commande entraîne l’installation de TinyTDS sur votre ordinateur.

    gem install tiny_tds

## Créer une base de données, récupérer la chaîne de connexion

L’échantillon Ruby utilise la base de données exemple AdventureWorks. Si vous n’avez pas déjà AdventureWorks, vous pouvez découvrir comment la créer dans la rubrique suivante : [Créer votre première base de données SQL Azure](sql-database-get-started.md).

Cette rubrique explique également comment récupérer la chaîne de connexion de la base de données.

## Se connecter à la base de données SQL

La fonction [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) permet de se connecter à la base de données SQL.

    require 'tiny_tds' 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 

## Exécuter une instruction SELECT et récupérer le jeu de résultats

La fonction [TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) permet de récupérer un jeu de résultats d’une requête effectuée dans la base de données SQL. Cette fonction accepte une requête et retourne un jeu de résultats. Une itération est effectuée sur le jeu de résultats en utilisant [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds).

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 
    results = client.execute("select * from SalesLT.Product") 
    results.each do |row| 
    puts row 
    end 

## Insertion d'une ligne, transmission de paramètres et récupération de la valeur de la clé primaire générée

L’exemple de code :

- Transmet les paramètres correspondant aux valeurs à insérer dans une ligne.
- Insère la ligne.
- Récupère la valeur qui a été générée pour la clé primaire.

Dans la base de données SQL, la propriété [IDENTITY](http://msdn.microsoft.com/library/ms186775.aspx) et l’objet [SEQUENCE](http://msdn.microsoft.com/library/ff878058.aspx) peuvent être utilisés pour générer automatiquement des [valeurs de clé primaire](http://msdn.microsoft.com/library/ms179610.aspx).

Pour utiliser TinyTDS avec Azure, il est recommandé d’exécuter plusieurs instructions `SET` pour modifier la façon dont la session en cours gère des informations spécifiques. Les instructions `SET` recommandées sont fournies dans l’exemple de code. Par exemple, `SET ANSI_NULL_DFLT_ON` permet aux nouvelles colonnes créées d’autoriser les valeurs Null même si la possibilité d’utiliser ces valeurs dans ces colonnes n’est pas explicitement définie.

Pour être en harmonie avec le format [datetime](http://msdn.microsoft.com/library/ms187819.aspx) (date et heure) de Microsoft SQL Server, utilisez la fonction [strftime](http://ruby-doc.org/core-2.2.0/Time.html#method-i-strftime) pour effectuer une conversion au format datetime correspondant.

    require 'tiny_tds' 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 
    results = client.execute("SET ANSI_NULLS ON")
    results = client.execute("SET CURSOR_CLOSE_ON_COMMIT OFF")
    results = client.execute("SET ANSI_NULL_DFLT_ON ON")
    results = client.execute("SET IMPLICIT_TRANSACTIONS OFF")
    results = client.execute("SET ANSI_PADDING ON")
    results = client.execute("SET QUOTED_IDENTIFIER ON")
    results = client.execute("SET ANSI_WARNINGS ON")
    results = client.execute("SET CONCAT_NULL_YIELDS_NULL ON")
    require 'date'
    t = Time.now
    curr_date = t.strftime("%Y-%m-%d %H:%M:%S.%L") 
    results = client.execute("INSERT SalesLT.Product (Name, ProductNumber, StandardCost, ListPrice, SellStartDate) 
    OUTPUT INSERTED.ProductID VALUES ('SQL Server Express New', 'SQLEXPRESS New', 0, 0, '#{curr_date}' )")
    results.each do |row| 
    puts row
    end

<!---HONumber=Oct15_HO3-->