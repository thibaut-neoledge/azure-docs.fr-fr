<properties
	pageTitle="Se connecter à une base de données SQL à l’aide de Ruby avec TinyTDS sur Windows"
	description="Donnez un exemple de code Ruby que vous pouvez exécuter sur Windows pour vous connecter à la base de données SQL Azure."
	services="sql-database"
	documentationCenter=""
	authors="meet-bhagdev"
	manager="jeffreyg"
	editor=""/>


<tags
	ms.service="sql-database"
	ms.workload="sql-database"
	ms.tgt_pltfrm="na"
	ms.devlang="ruby"
	ms.topic="article"
	ms.date="12/17/2015"
	ms.author="meetb"/>


# Connexion à la base de données SQL à l'aide de Ruby sur Windows


<!--
Older Selector technique, with dynamic drop-down lists.
 [ A ZURE . I NCLUDE [s ql-database-develop-includes-selector-language-platform-depth](../../inclu des/sql-database-develop-includes-selector-language-platform-depth.m d)]
-->

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]


Cette rubrique présente un exemple de code Ruby qui s’exécute sur un ordinateur Windows exécutant Windows 8.1, pour la connexion à une base de données SQL Azure.

## Composants requis

###Installer les modules requis

Ouvrez votre terminal et procédez aux installations suivantes :

**1) Ruby :** si votre ordinateur ne dispose pas de Ruby, veuillez l'installer. Si vous utilisez Ruby pour la première fois, nous vous recommandons d’exécuter les programmes d’installation Ruby 2.1.X. Ceux-ci fournissent un langage stable et une liste complète des packages (maillons) compatibles et mis à jour. [Accédez à la page de téléchargement de Ruby](http://rubyinstaller.org/downloads/) et téléchargez le programme d'installation 2.1.x approprié. Si vous utilisez un ordinateur 64 bits, téléchargez le programme d’installation **Ruby 2.1.6 (x 64)**. <br/><br/>Une fois le programme d'installation téléchargé, procédez comme suit :


- Double-cliquez sur le fichier pour démarrer le programme d'installation.

- Sélectionnez votre langue et acceptez les termes du contrat.

- Sur l'écran des paramètres d'installation, activez les cases à cocher en regard d’*Ajouter des exécutables Ruby à votre CHEMIN D’ACCÈS*et d’*Associer les fichiers .rb et .rbw à cette installation Ruby*.


**2) DevKit :** téléchargez le DevKit de la [page RubyInstaller](http://rubyinstaller.org/downloads/)

Une fois le téléchargement terminé, procédez comme suit :


- Double-cliquez sur le fichier. Vous devrez ensuite indiquer où vous souhaitez extraire les fichiers.

- Cliquez sur le bouton « ... » et sélectionnez « C:\\DevKit ». Vous devrez certainement créer en premier lieu ce dossier en cliquant sur « Créer un nouveau dossier ».

- Cliquez sur « OK », puis « Extraire », pour procéder à l’extraction des fichiers.


Ouvrez maintenant une invite de commandes et entrez les commandes suivantes :

	> chdir C:\DevKit
	> ruby dk.rb init
	> ruby dk.rb install

Vous disposez dès à présent d’un Ruby entièrement fonctionnel et de RubyGems !


**3) TinyTDS :** accédez à C:\\DevKit et exécutez la commande suivante à partir de votre terminal. Cette commande entraîne l’installation de TinyTDS sur votre ordinateur.

	gem inst tiny_tds --pre

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

Copiez et collez le code suivant dans le fichier : Appelez-le test.rb. Puis, exécutez-le en entrant la commande suivante à partir de l'invite de commandes :

	ruby test.rb

Dans l’exemple de code, la fonction [TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) permet de récupérer un jeu de résultats d’une requête effectuée dans la base de données SQL. Cette fonction accepte une requête et retourne un jeu de résultats. Une itération est effectuée sur le jeu de résultats en utilisant [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds).

    require 'tiny_tds'  
    print 'test'     
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword',
    host: 'yourserver.database.windows.net', port: 1433,
    database: 'AdventureWorks', azure:true
    results = client.execute("SELECT c.CustomerID, c.CompanyName,COUNT(soh.SalesOrderID) AS OrderCount FROM SalesLT.Customer AS c LEFT OUTER JOIN SalesLT.SalesOrderHeader AS soh ON c.CustomerID = soh.CustomerID GROUP BY c.CustomerID, c.CompanyName ORDER BY OrderCount DESC")
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