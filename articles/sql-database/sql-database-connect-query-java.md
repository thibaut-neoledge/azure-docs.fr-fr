---
title: "Utilisation de Java pour interroger Azure SQL Database | Microsoft Docs"
description: "Cette rubrique vous explique comment utiliser Java pour créer un programme qui se connecte à une base de données SQL Azure et l’interroger à l’aide d’instructions Transact-SQL."
services: sql-database
documentationcenter: 
author: ajlam
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,develop apps
ms.workload: drivers
ms.tgt_pltfrm: na
ms.devlang: java
ms.topic: hero-article
ms.date: 07/10/2017
ms.author: andrela
ms.translationtype: HT
ms.sourcegitcommit: 54454e98a2c37736407bdac953fdfe74e9e24d37
ms.openlocfilehash: f07f49e64f107ec063bfb755dbb89644f3d00576
ms.contentlocale: fr-fr
ms.lasthandoff: 07/13/2017

---
# <a name="use-java-to-query-an-azure-sql-database"></a>Utilisation de Java pour interroger une base de données SQL Azure

Ce didacticiel de démarrage rapide indique comment utiliser [Java](https://docs.microsoft.com/sql/connect/jdbc/microsoft-jdbc-driver-for-sql-server) pour se connecter à une base de données SQL Azure et utiliser des instructions Transact-SQL pour interroger des données.

## <a name="prerequisites"></a>Composants requis

Pour suivre ce didacticiel de démarrage rapide, vérifiez que vous disposez des éléments prérequis suivants :

- base de données SQL Azure. Ce guide de démarrage rapide utilise les ressources créées dans l’une de ces instructions de démarrage rapide : 

   - [Créer une base de données - Portail](sql-database-get-started-portal.md)
   - [Créer une base de données - CLI](sql-database-get-started-cli.md)
   - [Créer une base de données - PowerShell](sql-database-get-started-powershell.md)

- Une [règle de pare-feu au niveau du serveur](sql-database-get-started-portal.md#create-a-server-level-firewall-rule) pour l’adresse IP publique de l’ordinateur que vous utilisez pour ce didacticiel de démarrage rapide.

- Vous avez installé Java et les logiciels connexes pour votre système d’exploitation.

    - **MacOS** : installez Homebrew et Java, puis Maven. Consultez les [étapes 1.2 et 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/mac/).
    - **Ubuntu** : installez le kit de développement Java, puis installez Maven. Consultez les [étapes 1.2, 1.3 et 1.4](https://www.microsoft.com/sql-server/developer-get-started/java/ubuntu/).
    - **Windows** : installez le kit de développement Java, puis installez Maven. Consultez les [étapes 1.2 et 1.3](https://www.microsoft.com/sql-server/developer-get-started/java/windows/).    

## <a name="sql-server-connection-information"></a>Informations de connexion SQL Server

Obtenez les informations de connexion requises pour la connexion à la base de données SQL Azure. Vous aurez besoin du nom du serveur complet, du nom de la base de données et des informations de connexion dans les procédures suivantes.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Bases de données SQL** dans le menu de gauche, puis cliquez sur votre base de données dans la page **Bases de données SQL**. 
3. Sur la page **Vue d’ensemble** de votre base de données, vérifiez le nom complet du serveur, comme indiqué sur l’image suivante. Vous pouvez survoler le nom du serveur pour afficher l’option **Cliquer pour copier**.  

   ![server-name](./media/sql-database-connect-query-dotnet/server-name.png) 

4. Si vous avez oublié vos informations de connexion au serveur, accédez à la page du serveur SQL Database pour afficher le nom de l’administrateur du serveur.  Si nécessaire, réinitialisez le mot de passe.     

## <a name="create-maven-project-and-dependencies"></a>**Création d’un projet Maven et de dépendances**
1. À partir du terminal, créez un nouveau projet Maven nommé **sqltest**. 

   ```bash
   mvn archetype:generate "-DgroupId=com.sqldbsamples" "-DartifactId=sqltest" "-DarchetypeArtifactId=maven-archetype-quickstart" "-Dversion=1.0.0"
   ```

2. Quand vous y êtes invité, entrez **Y**.
3. Remplacez le répertoire par **sqltest** et ouvrez ***pom.xml*** avec votre éditeur de texte favori.  Ajoutez le **pilote JDBC Microsoft pour SQL Server** aux dépendances de votre projet en utilisant le code suivant :

   ```xml
   <dependency>
       <groupId>com.microsoft.sqlserver</groupId>
       <artifactId>mssql-jdbc</artifactId>
       <version>6.2.1.jre8</version>
   </dependency>
   ```

4. Toujours dans ***pom.xml***, ajoutez les propriétés suivantes à votre projet.  S’il n’y a pas de section Propriétés, vous pouvez les ajouter après les dépendances.

   ```xml
   <properties>
       <maven.compiler.source>1.8</maven.compiler.source>
       <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   ```

5. Enregistrez et fermez le fichier ***pom.xml***.

## <a name="insert-code-to-query-sql-database"></a>Insertion du code pour interroger la base de données SQL

1. Vous disposez déjà d’un fichier appelé ***App.java*** dans votre projet Maven situé à cet emplacement : \sqltest\src\main\java\com\sqlsamples\App.Java

2. Ouvrez le fichier et remplacez son contenu par le code suivant, puis ajoutez les valeurs adéquates pour votre serveur, la base de données, l’utilisateur et le mot de passe.

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.DriverManager;

   public class App {

    public static void main(String[] args) {
    
        // Connect to database
           String hostName = "your_server.database.windows.net";
           String dbName = "your_database";
           String user = "your_username";
           String password = "your_password";
           String url = String.format("jdbc:sqlserver://%s:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", hostName, dbName, user, password);
           Connection connection = null;

           try {
                   connection = DriverManager.getConnection(url);
                   String schema = connection.getSchema();
                   System.out.println("Successful connection - Schema: " + schema);

                   System.out.println("Query data example:");
                   System.out.println("=========================================");

                   // Create and execute a SELECT SQL statement.
                   String selectSql = "SELECT TOP 20 pc.Name as CategoryName, p.name as ProductName " 
                       + "FROM [SalesLT].[ProductCategory] pc "  
                       + "JOIN [SalesLT].[Product] p ON pc.productcategoryid = p.productcategoryid";
                
                   try (Statement statement = connection.createStatement();
                       ResultSet resultSet = statement.executeQuery(selectSql)) {

                           // Print results from select statement
                           System.out.println("Top 20 categories:");
                           while (resultSet.next())
                           {
                               System.out.println(resultSet.getString(1) + " "
                                   + resultSet.getString(2));
                           }
                    connection.close();
                   }                   
           }
           catch (Exception e) {
                   e.printStackTrace();
           }
       }
   }
   ```

## <a name="run-the-code"></a>Exécution du code

1. Exécutez ensuite les commandes suivantes dans l’invite de commandes :

   ```bash
   mvn package
   mvn -q exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

2. Vérifiez que les 20 premières lignes sont renvoyées, puis fermez la fenêtre d’application.


## <a name="next-steps"></a>Étapes suivantes
- [Concevoir votre première base de données SQL Azure](sql-database-design-first-database.md)
- [Pilote JDBC Microsoft pour SQL Server](https://github.com/microsoft/mssql-jdbc)
- [Report issues/ask questions](https://github.com/microsoft/mssql-jdbc/issues) (Signaler des problèmes/poser des questions)


