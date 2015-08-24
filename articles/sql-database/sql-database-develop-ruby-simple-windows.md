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
	ms.date="08/04/2015" 
	ms.author="mebha"/>


# Connexion à la base de données SQL à l'aide de Ruby sur Windows

[AZURE.INCLUDE [sql-database-develop-includes-selector-language-platform-depth](../../includes/sql-database-develop-includes-selector-language-platform-depth.md)]

Cette rubrique présente un exemple de code Ruby qui s’exécute sur un ordinateur Windows exécutant Windows 8.1, pour la connexion à une base de données SQL Azure.

## Installer les modules requis

Ouvrez votre terminal et procédez aux installations suivantes :

**1) Ruby :** si votre ordinateur ne dispose pas de Ruby, veuillez l'installer. Si vous utilisez Ruby pour la première fois, nous vous recommandons d’exécuter les programmes d’installation Ruby 2.1.X. Ceux-ci fournissent un langage stable et une liste complète des packages (maillons) compatibles et mis à jour. [Accédez à la page de téléchargement de Ruby]() et téléchargez le programme d'installation 2.1.x approprié. Si vous utilisez un ordinateur 64 bits, téléchargez le programme d’installation **Ruby 2.1.6 (x 64)**. <br/><br/>Une fois le programme d'installation téléchargé, procédez comme suit :


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

## Créer une base de données, récupérer la chaîne de connexion

L’échantillon Ruby utilise l’exemple de la base de données `AdventureWorks`. Si vous ne possédez pas déjà `AdventureWorks`, vous pouvez découvrir comment le créer en consultant la rubrique [Créer votre première base de données SQL Azure](sql-database-get-started.md).

Cette rubrique explique également comment récupérer la chaîne de connexion de la base de données.

## Se connecter à la base de données SQL

La fonction [TinyTDS::Client](https://github.com/rails-sqlserver/tiny_tds) permet de se connecter à la base de données SQL.

    require 'tiny_tds' 
    client = TinyTds::Client.new username: 'yourusername@yourserver', password: 'yourpassword', 
    host: 'yourserver.database.windows.net', port: 1433, 
    database: 'AdventureWorks', azure:true 

## Exécuter une instruction SELECT et récupérer le jeu de résultats

Copiez et collez le code suivant dans le fichier : Appelez-le test.rb. Puis, exécutez-le en entrant la commande suivante à partir de l'invite de commandes :

	ruby test.rb

Dans l’exemple de code, la fonction [TinyTds::Result](https://github.com/rails-sqlserver/tiny_tds) permet de récupérer un jeu de résultats d’une requête effectuée dans la base de données SQL. Cette fonction accepte une requête et retourne un jeu de résultats. Une itération est effectuée sur le jeu de résultats en utilisant [result.each do |row|](https://github.com/rails-sqlserver/tiny_tds).

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

Pour utiliser TinyTDS avec Azure, il est recommandé d’exécuter plusieurs instructions `SET` pour modifier la façon dont la session en cours gère des informations spécifiques. Les instructions `SET` recommandées sont fournies dans l’exemple de code. Par exemple, `SET ANSI_NULL_DFLT_ON` permet aux nouvelles colonnes créées d’autoriser les valeurs Null, même si la possibilité d’utiliser ces valeurs dans ces colonnes n’est pas explicitement définie.

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

<!---HONumber=August15_HO7-->