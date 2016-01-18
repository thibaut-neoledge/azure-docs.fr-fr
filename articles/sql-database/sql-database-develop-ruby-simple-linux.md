<properties
	pageTitle="Se connecter à une base de données SQL à l’aide de Ruby avec TinyTDS sur Ubuntu"
	description="Donnez un exemple de code Ruby que vous pouvez exécuter en tant que client sur Ubuntu Linux pour vous connecter à Base de données SQL Azure."
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
	ms.date="12/17/2015"
	ms.author="andrela"/>


# Se connecter à une base de données SQL à l’aide de Ruby sur Ubuntu Linux


[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Cette rubrique présente un exemple de code Ruby qui s’exécute sur un ordinateur client Ubuntu Linux, pour la connexion à une base de données SQL Azure.

## Composants requis

### Installer les modules requis

Ouvrez votre terminal et installez FreeTDS si vous ne l’avez pas sur votre ordinateur.

    sudo apt-get --assume-yes update
    sudo apt-get --assume-yes install freetds-dev freetds-bin

Une fois votre ordinateur configuré avec FreeTDS, installez Ruby si vous ne l’avez pas déjà sur votre ordinateur.

    sudo apt-get install libgdbm-dev libncurses5-dev automake libtool bison libffi-dev
    curl -L https://get.rvm.io | bash -s stable

Si vous rencontrez des problèmes avec les signatures, exécutez la commande suivante.

    command curl -sSL https://rvm.io/mpapis.asc | gpg --import -

Si les signatures ne présentent aucun problème, exécutez les commandes suivantes.

    source ~/.rvm/scripts/rvm
    rvm install 2.1.2
    rvm use 2.1.2 --default
    ruby -v

Veillez à utiliser la version 2.1.2 ou la machine virtuelle Ruby.

Installez ensuite TinyTDS.

    gem install tiny_tds

### Base de données SQL

Consultez la [page de prise en main](sql-database-get-started.md) pour apprendre à créer un exemple de base de données. Il est important que vous suiviez le guide pour créer un **modèle de base de données AdventureWorks**. Les exemples ci-dessous fonctionnent uniquement avec le **schéma AdventureWorks**.


## Étape 1 : obtenir les informations de connexion

[AZURE.INCLUDE [sql-database-include-connection-string-details-20-portalshots](../../includes/sql-database-include-connection-string-details-20-portalshots.md)]

## Étape 2 : se connecter

La fonction [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) permet de se connecter à Base de données SQL.

    require 'tiny_tds'
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true

## Étape 3 : exécuter une requête

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

## Étape 4 : insérer une ligne

Dans cet exemple, vous allez découvrir comment exécuter une instruction [INSERT](https://msdn.microsoft.com/library/ms174335.aspx) en toute sécurité, passer des paramètres pour protéger votre application des vulnérabilités découlant de [l’injection de code SQL] (https://technet.microsoft.com/library/ms161953(v=sql.105).aspx) et récupérer la valeur de la [Clé primaire](https://msdn.microsoft.com/library/ms179610.aspx) générée automatiquement.

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

<!---HONumber=AcomDC_0107_2016-->