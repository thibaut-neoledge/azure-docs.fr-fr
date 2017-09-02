---
title: "Déploiement d’une application SaaS mutualisée avec Azure SQL Database | Microsoft Docs"
description: "Déployez une application SaaS mutualisée avec Azure SQL Database."
services: sql-database
documentationcenter: 
author: AyoOlubeko
manager: jhubbard
editor: monicar
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: mvc,scale out apps
ms.devlang: na
ms.topic: tutorial
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 05/08/2017
ms.author: AyoOlubek
ms.translationtype: Human Translation
ms.sourcegitcommit: 3716c7699732ad31970778fdfa116f8aee3da70b
ms.openlocfilehash: 0aea69d86a51c38c99a72f46737de1eea27bef83
ms.contentlocale: fr-fr
ms.lasthandoff: 06/30/2017

---

# <a name="implement-a-multi-tenant-saas-application-using-azure-sql-database"></a>Déployer une application SaaS mutualisée à l’aide d’Azure SQL Database

Une application mutualisée désigne une application hébergée dans un environnement cloud, qui fournit le même ensemble de services à des centaines ou milliers de locataires qui ne partagent pas ou ne voient pas les données d’autrui. Il peut par exemple s’agir d’une application SaaS qui fournit des services aux locataires dans un environnement hébergé dans le cloud. Ce modèle isole les données pour chaque locataire et optimise la répartition des ressources de coûts. 

Ce didacticiel montre comment créer une application SaaS mutualisée à l’aide d’Azure SQL Database.

Ce didacticiel vous apprendra à effectuer les opérations suivantes :
> [!div class="checklist"]
> * Configuration d’un environnement de base de données pour la prise en charge d’une application SaaS mutualisée, à l’aide du modèle « base de données par locataire »
> * Création d’un catalogue de locataires
> * Configuration d’une base de données de locataires et inscription de cette base de données dans le catalogue de locataires
> * Configuration d’un exemple d’application Java 
> * Accès aux bases de données de locataires à l’aide d’une simple application console Java
> * Suppression d’un locataire

Si vous ne disposez pas d’abonnement Azure, créez un [compte gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Composants requis

Pour suivre ce didacticiel, vérifiez que vous disposez des éléments suivants :

* La dernière version de PowerShell et le [dernier Kit de développement logiciel (SDK) Azure PowerShell](http://azure.microsoft.com/downloads/)

* La dernière version de [SQL Server Management Studio](http://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms). L’installation de SQL Server Management Studio installe également la version la plus récente de SQLPackage, un utilitaire de ligne de commande qui peut être utilisé pour automatiser de nombreuses tâches de développement de bases de données.

* Un ordinateur disposant de [Java Runtime Environment (JRE) 8](http://www.oracle.com/technetwork/java/javase/downloads/jre8-downloads-2133155.html) et du [dernier kit de développement JAVA (JDK)](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html). 

* [Apache Maven](https://maven.apache.org/download.cgi). Maven servira à gérer les dépendances et à générer, tester et exécuter l’exemple de projet Java.

## <a name="set-up-data-environment"></a>Configuration de l’environnement des données

Vous allez approvisionner une base de données par locataire. Le modèle « base de données par locataire » constitue la meilleure manière d’isoler les locataires tout en réduisant les coûts DevOps. Pour optimiser le coût des ressources cloud, vous devrez également configurer les bases de données de locataires dans un pool élastique, de manière à optimiser le rapport prix/performances d’un groupe de bases de données. Pour en savoir plus sur les autres modèles d’approvisionnement de bases de données, [cliquez ici](sql-database-design-patterns-multi-tenancy-saas-applications.md#multi-tenant-data-models).

Suivez ces étapes pour créer un serveur SQL et un pool élastique qui hébergeront toutes vos bases de données de locataires. 

1. Créez des variables pour stocker les valeurs qui seront utilisées dans la suite de ce didacticiel. Veillez à modifier la variable d’adresse IP pour inclure votre adresse IP 
   
   ```PowerShell 
   # Set an admin login and password for your database
   $adminlogin = "ServerAdmin"
   $password = "ChangeYourAdminPassword1"
   
   # Create random unique names for logical server and tenants
   $servername = "server-$(Get-Random)"
   $tenant1 = "geolamice"
   $tenant2 = "ranplex"
   
   # Store current client IP address (modify to include your IP address)
   $startIpAddress = 0.0.0.0 
   $endIpAddress = 0.0.0.0
   ```
   
2. Connectez-vous à Azure et créez un serveur SQL et un pool élastique 
   
   ```PowerShell
   # Login to Azure 
   Login-AzureRmAccount
   
   # Create resource group 
   New-AzureRmResourceGroup -Name "myResourceGroup" -Location "northcentralus"
   
   # Create logical SQL Server with firewall rules 
   New-AzureRmSqlServer -ResourceGroupName "myResourceGroup" `
       -ServerName $servername `
       -Location "northcentralus" `
       -SqlAdministratorCredentials $(New-Object -TypeName System.Management.Automation.PSCredential -ArgumentList $adminlogin, $(ConvertTo-SecureString -String $password -AsPlainText -Force))
   
   New-AzureRmSqlServerFirewallRule -ResourceGroupName $resourcegroupname `
       -ServerName $servername `
       -FirewallRuleName "singleAddress" -StartIpAddress $startIpAddress -EndIpAddress $endIpAddress
   
   # Create elastic pool 
   New-AzureRmSqlElasticPool -ResourceGroupName "myResourceGroup"
       -ServerName $servername `
       -ElasticPoolName "myElasticPool" `
       -Edition "Standard" `
       -Dtu 50 `
       -DatabaseDtuMin 10 `
       -DatabaseDtuMax 20
   ```
   
## <a name="create-tenant-catalog"></a>Création du catalogue de locataires 

Dans une application SaaS mutualisée, il est important de savoir où sont stockées les informations d’un locataire. Ces informations sont généralement stockées dans une base de données de catalogue. Cette base de données contient un mappage entre un locataire et une base de données dans laquelle sont stockées les données du locataire.  Le modèle de base s’applique aussi bien pour les bases de données mutualisées que pour les bases de données à un seul locataire.

Suivez ces étapes pour créer une base de données de catalogue pour l’exemple d’application SaaS.

```PowerShell
# Create empty database in pool
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName "tenantCatalog" `
    -ElasticPoolName "myElasticPool"

# Create table to track mapping between tenants and their databases
$commandText = "
CREATE TABLE Tenants
(
   TenantId         INT IDENTITY PRIMARY KEY,
   TenantName       NVARCHAR(128) NOT NULL,
   TenantDatabase   NVARCHAR(128) NOT NULL
);

CREATE INDEX IX_TenantName ON Tenants (TenantName);"

Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database "tenantCatalog" `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection
```

## <a name="provision-database-for-tenant1-and-register-in-tenant-catalog"></a>Approvisionnement de la base de données pour le « locataire 1 » et inscription de cette base dans le catalogue du locataire 
Utilisez Powershell pour approvisionner une base de données pour un nouveau locataire appelé « locataire1 » et pour inscrire ce locataire dans le catalogue. 

```PowerShell
# Create empty database in pool for 'tenant1'
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName $tenant1 `
    -ElasticPoolName "myElasticPool"

# Create table WhoAmI and insert tenant name into the table 
$commandText = "
CREATE TABLE WhoAmI (TenantName NVARCHAR(128) NOT NULL);
INSERT INTO WhoAmI VALUES ('Tenant $tenant1');"

Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database $tenant1 `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection

# Register 'tenant1' in the tenant catalog 
$commandText = "
INSERT INTO Tenants VALUES ('$tenant1', '$tenant1');"
Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database "tenantCatalog" `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection
```

## <a name="provision-database-for-tenant2-and-register-in-tenant-catalog"></a>Approvisionnement de la base de données pour le « locataire 2 » et inscription de cette base dans le catalogue du locataire
Utilisez Powershell pour approvisionner une base de données pour un nouveau locataire appelé « locataire2 » et pour inscrire ce locataire dans le catalogue. 

```PowerShell
# Create empty database in pool for 'tenant2'
New-AzureRmSqlDatabase  -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName $tenant2 `
    -ElasticPoolName "myElasticPool"

# Create table WhoAmI and insert tenant name into the table 
$commandText = "
CREATE TABLE WhoAmI (TenantName NVARCHAR(128) NOT NULL);
INSERT INTO WhoAmI VALUES ('Tenant $tenant2');"

Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database $tenant2 `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection

# Register tenant 'tenant2' in the tenant catalog 
$commandText = "
INSERT INTO Tenants VALUES ('$tenant2', '$tenant2');"
Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database "tenantCatalog" `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection
```

## <a name="set-up-sample-java-application"></a>Configuration d’un exemple d’application Java 

1. Créez un projet Maven. Dans une fenêtre d’invite de commandes, entrez la commande suivante :
   
   ```
   mvn archetype:generate -DgroupId=com.microsoft.sqlserver -DartifactId=mssql-jdbc -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false
   ```
   
2. Ajoutez la dépendance, le niveau de langage et l’option de génération pour prendre en charge les fichiers manifeste JARS dans le fichier pom.xml :
   
   ```XML
   <dependency>
         <groupId>com.microsoft.sqlserver</groupId>
         <artifactId>mssql-jdbc</artifactId>
         <version>6.1.0.jre8</version>
   </dependency>
   
   <properties>
         <maven.compiler.source>1.8</maven.compiler.source>
         <maven.compiler.target>1.8</maven.compiler.target>
   </properties>
   
   <build>
        <plugins>
           <plugin>
              <groupId>org.apache.maven.plugins</groupId>
              <artifactId>maven-jar-plugin</artifactId>
              <version>3.0.0</version>
              <configuration>
                 <archive>
                    <manifest>
                       <mainClass>com.sqldbsamples.App</mainClass>
                    </manifest>
                 </archive>
              </configuration>
           </plugin>
        </plugins>
   </build>
   ```

3. Ajoutez le code suivant dans le fichier App.java :

   ```java 
   package com.sqldbsamples;
   
   import java.util.Map;
   
   import java.util.HashMap;
   
   import java.io.BufferedReader;
   
   import java.io.InputStreamReader;
   
   import java.sql.Connection;
   
   import java.sql.Statement;
   
   import java.sql.PreparedStatement;
   
   import java.sql.ResultSet;
   
   import java.sql.DriverManager;
   
   public class App {
   
   private static final String SERVER_NAME = "your-server-name";
   
   private static final String CATALOG_DB_NAME = "tenantCatalog";
   
   private static final String USER = "ServerAdmin";
   
   private static final String PASSWORD = "ChangeYourAdminPassword1";
   
   private static final String CATALOG_DB_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", SERVER_NAME, CATALOG_DB_NAME, USER, PASSWORD);
   
   private static final String CMD_LIST = "LIST";

   private static final String CMD_QUERY = "QUERY";

   private static final String CMD_QUIT = "QUIT";
   
   public static void main(String[] args) {
   
   System.out.println("\n############################");
   
   System.out.println("## SAAS DATABASE TUTORIAL ##");
   
   System.out.println("############################\n");
   
   System.out.println("OPTIONS");
   
   System.out.println(" " + CMD_LIST + " - list tenants");
   
   System.out.println(" " + CMD_QUERY + " <NAME> - connect and tenant query tenant <NAME>");
   
   System.out.println(" " + CMD_QUIT + " - quit the application\n");
   
   try (BufferedReader br = new BufferedReader(new InputStreamReader(System.in))) {
   
   while(true) {
   
   String[] input = br.readLine().split("\\s");
   
   if (null != input && input.length > 0) {
   
   if (input[0].equalsIgnoreCase(CMD_LIST)) {
   
   listTenants();
   
   } else if (input[0].toLowerCase().startsWith(CMD_QUERY.toLowerCase()) && input.length == 2) {
   
   queryTenant(input[1].trim());
   
   } else if (input[0].equalsIgnoreCase(CMD_QUIT)) {
   
   break;
   
   } else {
   
   System.out.println(" -> Command not supported");
   
   }
   
   }
   
   System.out.println("");
   
   }
   
   } catch (Exception e) {
   
   System.out.println(e.getMessage());
   
   e.printStackTrace();
   
   }
   
   }
   
   private static void listTenants() {
   
   // List all tenants that currently exist in the system
   
   String sql = "SELECT TenantName FROM Tenants";
   
   try (Connection connection = DriverManager.getConnection(CATALOG_DB_URL);
   
   Statement stmt = connection.createStatement();
   
   ResultSet resultSet = stmt.executeQuery(sql)) {
   
   while (resultSet.next()) {
   
   System.out.println(" -> " + resultSet.getString(1));
   
   }
   
   } catch (Exception e) {
   
   System.out.println(e.getMessage());
   
   e.printStackTrace();
   
   }
   
   }
   
   private static void queryTenant(String name) {
   
   // Query the data that was previously inserted into the primary database from the geo replicated database
   
   String url = null;
   
   String sql = "SELECT TenantDatabase FROM Tenants WHERE TenantName = ?";
   
   try (Connection connection = DriverManager.getConnection(CATALOG_DB_URL);
   
   PreparedStatement pstmt = connection.prepareStatement(sql)) {
   
   pstmt.setString(1, name);
   
   try (ResultSet resultSet = pstmt.executeQuery()) {
   
   if (resultSet.next()) {
   
   url = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", SERVER_NAME, resultSet.getString(1), USER, PASSWORD);
   
   }
   
   }
   
   } catch (Exception e) {
   
   System.out.println(e.getMessage());
   
   e.printStackTrace();
   
   }
   
   if (null != url) {
   
   String tenantSql = "SELECT TenantName FROM WhoAmI";
   
   try (Connection connection = DriverManager.getConnection(url);
   
   Statement stmt = connection.createStatement();

   ResultSet resultSet = stmt.executeQuery(tenantSql)) {
   
   while (resultSet.next()) {
   
   System.out.println(" -> Entry in table WhoAmI in tenant " + name + " is: " + resultSet.getString(1));
   
   }
   
   } catch (Exception e) {
   
   System.out.println(e.getMessage());
   
   e.printStackTrace();
   
   }
   
   } else {
   
   System.out.println(" -> Tenant " + name + " not found");
   
   }
   
   }
   
   }
   ```

4. Enregistrez le fichier.

5. Accédez à la console de commandes et exécutez la commande suivante :

   ```bash
   mvn package
   ```

6. Lorsque vous avez terminé, exécutez la commande suivante pour exécuter l’application : 
   
   ```
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```
   
Si l’exécution réussit, la sortie se présentera ainsi :

```
############################

## SAAS DATABASE TUTORIAL ##

############################

OPTIONS

LIST - list tenants

QUERY <NAME> - connect and tenant query tenant <NAME>

QUIT - quit the application

* List the tenants

* Query tenants you created
```

## <a name="delete-first-tenant"></a>Suppression du premier locataire 
Utilisez PowerShell pour supprimer la base de données de locataires et l’entrée du catalogue correspondant au premier locataire.

```PowerShell
# Remove 'tenant1' from catalog 
$commandText = "DELETE FROM Tenants WHERE TenantName = '$tenant1';"
Invoke-SqlCmd `
    -Username $adminlogin `
    -Password $password `
    -ServerInstance ($servername + ".database.windows.net") `
    -Database "tenantCatalog" `
    -ConnectionTimeout 30 `
    -Query $commandText `
    -EncryptConnection

# Delete database 
Remove-AzureRmSqlDatabase -ResourceGroupName "myResourceGroup" `
    -ServerName $servername `
    -DatabaseName $tenant1
```

Essayez de vous connecter au « locataire1 » à l’aide de l’application Java. Vous obtiendrez une erreur indiquant que le client n’existe pas.

## <a name="next-steps"></a>Étapes suivantes 

Dans ce didacticiel, vous avez appris à effectuer les opérations suivantes :
> [!div class="checklist"]
> * Configuration d’un environnement de base de données pour la prise en charge d’une application SaaS mutualisée, à l’aide du modèle « base de données par locataire »
> * Création d’un catalogue de locataires
> * Configuration d’une base de données de locataires et inscription de cette base de données dans le catalogue de locataires
> * Configuration d’un exemple d’application Java 
> * Accès aux bases de données de locataires à l’aide d’une simple application console Java
> * Suppression d’un locataire

* Pour obtenir des exemples de PowerShell pour les tâches courantes, consultez [Exemples PowerShell pour SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-powershell-samples)

* Pour accéder à des modèles de conception pour les applications SaaS mutualisées, consultez les [Modèles de conception](https://docs.microsoft.com/azure/sql-database/sql-database-design-patterns-multi-tenancy-saas-applications)

* Pour des exemples Java associés aux tâches courantes dans Azure, consultez le [Centre de développement Java](https://azure.microsoft.com/develop/java/)




