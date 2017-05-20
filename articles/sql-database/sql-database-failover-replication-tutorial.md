---
title: "Répliquer et basculer votre solution Azure SQL Database | Microsoft Docs"
description: "Découvrez comment configurer votre base de données et votre application SQL Azure pour le basculement vers une base de données répliquée et tester le basculement."
services: sql-database
documentationcenter: 
author: anosov1960
manager: jstrauss
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-failover
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 04/18/2017
ms.author: sashan
ms.translationtype: Human Translation
ms.sourcegitcommit: 71fea4a41b2e3a60f2f610609a14372e678b7ec4
ms.openlocfilehash: 7e5b82da402ab9d20410746c2b6c6b3aaab5b754
ms.contentlocale: fr-fr
ms.lasthandoff: 05/10/2017


---

# <a name="replicate-and-failover-an-azure-sql-database-solution"></a>Répliquer et basculer une solution Azure SQL Database

Dans ce didacticiel, vous configurez une application et une base de données SQL Azure pour le basculement vers une région distante, puis testez votre plan de basculement. 

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

Pour suivre ce didacticiel, vérifiez que vous disposez des éléments suivants :

- La dernière version de [SSMS](https://docs.microsoft.com/sql/ssms/download-sql-server-management-studio-ssms) (SQL Server Management Studio). L’installation de SSMS installe également la version la plus récente de SQLPackage, un utilitaire en ligne de commande qui peut être utilisé pour automatiser de nombreuses tâches de développement de bases de données. 
- Une base de données Azure à migrer. Ce didacticiel utilise la base de données AdventureWorksLT nommée **mySampleDatabase créée dans les démarrages rapides suivants :

   - [Créer une base de données - Portail](sql-database-get-started-portal.md)
   - [Créer une base de données - CLI](sql-database-get-started-cli.md)

## <a name="create-azure-active-directory-users-optional"></a>Créer des utilisateurs Azure Active Directory (facultatif)

Dans cette étape, vous créez ou identifiez des utilisateurs Azure Active Directory à ajouter en tant qu’utilisateurs à votre serveur logique et votre exemple de base de données Azure SQL Database.
- Si votre abonnement fait partie d’un environnement d’entreprise Azure Active Directory avec des comptes d’utilisateur existants, identifiez 3 comptes d’utilisateur à utiliser en tant qu’administrateur Active Directory, administrateur de l’application et utilisateur de l’application passez à l’étape 3 : Créer des identifiants de connexion et des utilisateurs SQL Database. 
- Si votre abonnement ne fait pas partie d’un environnement d’entreprise Azure Active Directory ou fait partie d’un environnement d’entreprise Active Directory Azure sans comptes d’utilisateur existants et vous êtes autorisé à créer de nouveaux comptes d’utilisateur Azure Active Directory :

1. Connectez-vous au [portail Azure](http://portal.azure.com).
2. Dans le menu de gauche, cliquez sur **Autres services**.
3. Dans la zone de texte de filtre, tapez **Azure**, puis sélectionnez **Azure Active Directory**.
4. Dans le volet **Tâches rapides** de la page **Azure Active Directory**, cliquez sur **Ajouter un utilisateur**.
5. Dans le formulaire **Utilisateur**, créez l’utilisateur suivant :
   - Nom : **ad-admin**
   - Nom d’utilisateur : **ad-admin@yourdomain** (Yopu4708)
6. Sélectionnez la case à cocher **Afficher le mot de passe** et consignez le mot de passe de ce compte d’utilisateur pour pouvoir l’utiliser ultérieurement.
7. Cliquez sur **Create**.
8. Répétez les 3 étapes précédentes pour créer les 2 nouveaux utilisateurs suivants :
   - Nom : **app-admin**
   - Nom d’utilisateur : **app-admin@yourdomain** (Buju4319)
   - Nom : **app-user**
   - Nom d’utilisateur : **app-user@yourdomain** (Nonu4001).

9. Ouvrez une nouvelle fenêtre de navigateur et connectez-vous au portail à l’aide du compte **ad-admin** que vous venez de créer.
10. Dans la page **Mettre à jour votre mot de passe**, entrez le mot de passe généré par le système dans le champ **Mot de passe actuel**. 
11. Dans les champs **Nouveau mot de passe** et **Confirmer le mot de passe**, entrez le mot de passe de votre choix.
12. Cliquez sur **Mettre à jour le mot de passe et se connecter**.

## <a name="configure-sql-database-integration-with-azure-active-directory"></a>Configurer l’intégration de SQL Database avec Azure Active Directory

1. Dans le menu de gauche, cliquez sur **Autres services**, tapez **sql** dans la zone de texte de filtre, puis sélectionnez **Serveurs SQL**.
2. Dans la page **Serveurs SQL**, cliquez sur votre serveur SQL Database.
3. Dans le volet Essentials de la page **Vue d’ensemble** de votre serveur, cliquez sur **Non configuré** sous **Administrateur Active Directory**.
4. Dans la page **Administrateur Active Directory**, cliquez sur **Définir l’administrateur**.
5. Sélectionnez le compte Azure Active Directory **ad-admin** (ou un autre compte préexistant, par exemple votre propre compte) à utiliser en tant qu’administrateur de serveur pour votre serveur Azure SQL Database.
6. Cliquez sur **Sélectionner**.
7. Cliquez sur **Save**.

## <a name="create-users-with-permissions-for-your-database"></a>Créer des utilisateurs avec des autorisations pour votre base de données

<TO DO: need to change script to grant app-user sufficient permissions to perform operation in java app>

Utilisez SQL Server Management Studio pour vous connecter à votre base de données et créer des comptes d’utilisateur. Ces comptes d’utilisateur seront répliqués automatiquement vers votre serveur secondaire. Vous devrez peut-être configurer une règle de pare-feu si vous vous connectez à partir d’un client à une adresse IP pour laquelle vous n’avez pas encore configuré de pare-feu. Pour connaître la procédure, consultez [Création d’une base de données SQL à l’aide du portail Azure](sql-database-get-started-portal.md).

1. Ouvrez SQL Server Management Studio.
2. Définissez le mode d’**authentification** sur **Authentification par mot de passe Active Directory**.
3. Connectez-vous à votre serveur à l’aide du compte d’administrateur de serveur Active Directory Azure que vous venez de créer. 
4. Dans l’Explorateur d’objets, développez **Bases de données système**, cliquez avec le bouton droit sur **mySampleDatabase**, puis cliquez sur **Nouvelle requête**.
5. Dans la fenêtre de requête, exécutez la requête suivante pour créer des comptes d’utilisateur dans votre base de données en accordant des autorisations **db_owner** aux deux comptes d’administrateur. Remplacez l’espace réservé au nom de domaine par votre domaine.

   ```tsql
   --Create Azure AD user account
   CREATE USER [app-admin@<yourdomain>] FROM EXTERNAL PROVIDER;
   --Add Azure AD user to db_owner role
   ALTER ROLE db_owner ADD MEMBER [app-admin@<yourdomain>]; 
   --Create additional Azure AD user account
   CREATE USER [app-user@<yourdomain>] FROM EXTERNAL PROVIDER;
   --Create SQL user account
   CREATE USER app_admin WITH PASSWORD = 'MyStrongPassword1';
   --Add SQL user to db_owner role
   ALTER ROLE db_owner ADD MEMBER app_admin; 
   --Create additional SQL user
   CREATE USER app_user WITH PASSWORD = 'MyStrongPassword1';
   ```

## <a name="create-database-level-firewall"></a>Créer un pare-feu au niveau de la base de données

Utilisez SQL Server Management Studio pour créer une règle de pare-feu au niveau de la base de données pour votre base de données. Cette règle de pare-feu au niveau de la base de données sera répliquée automatiquement vers votre serveur secondaire. À des fins de test, vous pouvez créer une règle de pare-feu pour toutes les adresses IP (0.0.0.0 et 255.255.255.255), créer une règle de pare-feu pour l’adresse IP spécifique avec laquelle vous avez créé la règle de pare-feu de serveur ou configurer une ou plusieurs règles de pare-feu pour les adresses IP des ordinateurs que vous souhaitez utiliser pour tester ce didacticiel.  

- Dans la fenêtre de requête ouverte, remplacez la requête précédente par la requête suivante, en remplaçant les adresses IP par les adresses IP appropriées pour votre environnement. 

   ```tsql
   -- Create database-level firewall setting for your publich IP address
   EXECUTE sp_set_database_firewall_rule N'mySampleDatabase','0.0.0.1','0.0.0.1';
   ```  

## <a name="create-a-failover-group"></a>Créer un groupe de basculement 

Choisissez une région de basculement, créez un serveur vide dans cette région, puis créez un groupe de basculement entre votre serveur existant et le nouveau serveur vide.

1. Remplissez les variables.

   ```powershell
   $secpasswd = ConvertTo-SecureString "yourstrongpassword" -AsPlainText -Force
   $mycreds = New-Object System.Management.Automation.PSCredential (“ServerAdmin”, $secpasswd)
   $myresourcegroup = "<your resource group>"
   $mylocation = "<resource group location>"
   $myserver = "<your existing server>"
   $mydatabase = "<your existing database>"
   $mydrlocation = "<your disaster recovery location>"
   $mydrserver = "<your disaster recovery server>"
   $myfailovergroup = "<your failover group"
   ```

2. Créez un serveur de sauvegarde vide dans votre région de basculement.

   ```powershell
   $mydrserver = New-AzureRmSqlServer -ResourceGroupName $myresourcegroup -Location $mydrlocation -ServerName $mydrserver -ServerVersion "12.0" -SqlAdministratorCredentials $mycreds
   ```

3. Créez un groupe de basculement.

   ```powershell
   $myfailovergroup = New-AzureRMSqlDatabaseFailoverGroup –ResourceGroupName $myresourcegroup -ServerName "$myserver" -PartnerServerName $mydrserver  –FailoverGroupName $myfailovergroupname –FailoverPolicy "Automatic" -GracePeriodWithDataLossHours 2
   ```

4. Ajoutez votre base de données au groupe de basculement.

   ```powershell
   $mydrserver | Add-AzureRMSqlDatabaseToFailoverGroup –FailoverGroupName $myfailovergroup  -Database $mydatabase
   ```

## <a name="add-empty-backup-server-to-domain"></a>Ajouter un serveur de sauvegarde vide au domaine

1. Dans le portail Azure, cliquez sur **Autres services**, tapez **sql** dans la zone de texte de filtre, puis sélectionnez **Serveurs SQL**.
2. Dans la page **Serveurs SQL**, cliquez sur votre nouveau serveur de récupération d’urgence SQL Database.
3. Dans le volet Essentials de la page **Vue d’ensemble** de votre serveur, cliquez sur **Non configuré** sous **Administrateur Active Directory**.
4. Dans la page **Administrateur Active Directory**, cliquez sur **Définir l’administrateur**.
5. Sélectionnez le compte Azure Active Directory **ad-admin** (ou un autre compte préexistant, par exemple votre propre compte) à utiliser en tant qu’administrateur de serveur pour votre nouveau serveur de récupération d’urgence Azure SQL Database.
6. Cliquez sur **Sélectionner**.
7. Cliquez sur **Save**.

## <a name="prepare-client-tier"></a>Préparer le niveau client

1. Créez un profil Traffic Manager avec le profil de basculement AWProfile.
2. Configuration de l’analyse

   ```powershell
   $profile = New-AzureRmTrafficManagerProfile -Name AWProfile -ResourceGroupName MYRG -TrafficRoutingMethod Failover -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
   $webapp1 = Get-AzureRMWebApp -Name WebappUSWest
   Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
   $webapp2 = Get-AzureRMWebApp -Name WebappUSEast
   Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
   Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
   ```

## <a name="deploy-java-application-and-connect-to-database"></a>Déployer l’application Java et se connecter à la base de données

<In progress>Consultez [Se connecter à l’aide de Java](sql-database-connect-query-java.md).

<TO DO: change user to app-user>

1. Installez Java 8.
2. Installez Maven.
3. Créer un projet Maven.
4. Ajoutez le code suivant au fichier pom.xml. 

   - Dépendance

      ```java
      <dependency>
         <groupId>com.microsoft.sqlserver</groupId>
         <artifactId>mssql-jdbc</artifactId>
         <version>6.1.0.jre8</version>
       </dependency>
      ```
   - Niveau de langage

      ```java
      <properties>
         <maven.compiler.source>1.8</maven.compiler.source>
         <maven.compiler.target>1.8</maven.compiler.target>
      </properties>
      ```

   - Option de génération pour prendre en charge les fichiers de manifeste dans les fichiers JAR

      ```java
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
5. Ajoutez le code suivant dans le fichier App.java :

   ```java
   package com.sqldbsamples;

   import java.sql.Connection;
   import java.sql.Statement;
   import java.sql.PreparedStatement;
   import java.sql.ResultSet;
   import java.sql.Timestamp;
   import java.sql.DriverManager;
   import java.util.Date;
   import java.util.concurrent.TimeUnit;

   public class App {

      private static final String PRIMARY_HOST_HAME = "your_primary_server_name";
      private static final String SECONDARY_HOST_NAME = "your_secondary_server_name";
      private static final String PRIMARY_HOST_HAME = "janengsampleserver";
      private static final String SECONDARY_HOST_NAME = PRIMARY_HOST_HAME;
    
      private static final String DB_NAME = "mySampleDatabase";
      private static final String USER = "ServerAdmin";
      private static final String PASSWORD = "ChangeYourAdminPassword1";

      private static final String PRIMARY_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", PRIMARY_HOST_HAME, DB_NAME, USER, PASSWORD);
      private static final String SECONDARY_URL = String.format("jdbc:sqlserver://%s.database.windows.net:1433;database=%s;user=%s;password=%s;encrypt=true;hostNameInCertificate=*.database.windows.net;loginTimeout=30;", SECONDARY_HOST_NAME, DB_NAME, USER, PASSWORD);

      public static void main(String[] args) {
         System.out.println("#######################################");
         System.out.println("## GEO DISTRIBUTED DATABASE TUTORIAL ##");
         System.out.println("#######################################");
         System.out.println(""); 

         // todo: get the max id from the table and initialize INSERT COUNTER with it so that the code will always run (avoid duplicate keys)  
         int highWaterMark = getHighWaterMarkId();

         try {
            for(int i = 1; i < 1000; i++) {
                //  loop will run for about 1h
                System.out.print(i + ": insert on primary " + (insertData((highWaterMark + i))?"successful":"failed"));
                TimeUnit.SECONDS.sleep(1);
                System.out.print(", read from secondary " + (selectData((highWaterMark + i))?"successful":"failed") + "\n");
                TimeUnit.SECONDS.sleep(3);
            }
         } catch(Exception e) {
            e.printStackTrace();
      }
   }

   private static boolean insertData(int id) {
      // Insert data into the product table with a unique product name that we can use to find the product again later
      String sql = "INSERT INTO SalesLT.Product (Name, ProductNumber, Color, StandardCost, ListPrice, SellStartDate) VALUES (?,?,?,?,?,?);";

      try (Connection connection = DriverManager.getConnection(PRIMARY_URL); 
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         pstmt.setInt(2, 200989 + id + 10000);
         pstmt.setString(3, "Blue");
         pstmt.setDouble(4, 75.00);
         pstmt.setDouble(5, 89.99);
         pstmt.setTimestamp(6, new Timestamp(new Date().getTime()));
         return (1 == pstmt.executeUpdate());
      } catch (Exception e) {
         return false;
      }
   }

   private static boolean selectData(int id) {
      // Query the data that was previously inserted into the primary database from the geo replicated database
      String sql = "SELECT Name, Color, ListPrice FROM SalesLT.Product WHERE Name = ?";

      try (Connection connection = DriverManager.getConnection(SECONDARY_URL); 
              PreparedStatement pstmt = connection.prepareStatement(sql)) {
         pstmt.setString(1, "BrandNewProduct" + id);
         try (ResultSet resultSet = pstmt.executeQuery()) {
            return resultSet.next();
         }
      } catch (Exception e) {
         return false;
      }
   }

   private static int getHighWaterMarkId() {
      // Query the high water mark id that is stored in the table to be able to make unique inserts 
      String sql = "SELECT MAX(ProductId) FROM SalesLT.Product";
      int result = 1;
        
      try (Connection connection = DriverManager.getConnection(SECONDARY_URL); 
              Statement stmt = connection.createStatement();
              ResultSet resultSet = stmt.executeQuery(sql)) {
         if (resultSet.next()) {
             result =  resultSet.getInt(1);
            }
         } catch (Exception e) {
          e.printStackTrace();
         }
         return result;
      }
   }
   ```
6. Enregistrez le fichier.

## <a name="compile-and-run"></a>Compilation et exécution

1. Accédez à la console et exécutez :

   ```java
   mvn package
   ```
2. Lorsque vous avez terminé, exécutez l’application à l’aide de la commande suivante (elle s’exécutera pendant une heure environ, sauf si vous l’arrêtez manuellement) :

   ```
   mvn -q -e exec:java "-Dexec.mainClass=com.sqldbsamples.App"
   ```

   Si l’exécution réussit, la sortie se présentera ainsi :

   #######################################
   ## <a name="geo-distributed-database-tutorial"></a>GEO DISTRIBUTED DATABASE TUTORIAL ##
   #######################################

   1. insert on primary successful, read from secondary successful
   2. insert on primary successful, read from secondary successful
   3. insert on primary successful, read from secondary successful

## <a name="perform-dr-drill"></a>Effectuer un test de récupération d’urgence

1. Appelez un basculement manuel du groupe de basculement à l’aide du basculement forcé. Pour éviter la perte de données au cours du test, vous devez supprimer -AllowDataLoss.

   ```powershell
   $fg | Switch-AzureRMSqlDatabaseFailoverGroup -AllowDataLoss
   ```

2.    Désactivez le point de terminaison primaire dans le profil Traffic Manager (pour déclencher le basculement du point de terminaison) : 

   ```powershell
   Disable-AzureRmTrafficManagerEndpoint -Name webapp1ep -Type AzureEndpoints -ProfileName $profile.Name -ResourceGroupName MYRG -Force
   ```

3.    Exécutez à nouveau l’application.


## <a name="relocate-application-to-primary-region"></a>Déplacer l’application dans la région primaire

1.    Appelez un basculement convivial manuel du groupe de basculement. Ne spécifiez pas -AllowDataLoss.

   ```powershell
   $fg | Switch-AzureRMSqlDatabaseFailoverGroup 
   ```

2.    Désactivez le point de terminaison secondaire (webapp2ep) dans le profil Traffic Manager (pour déclencher le basculement du point de terminaison) : 

   ```powershell
   Disable-AzureRmTrafficManagerEndpoint -Name webapp2ep -Type AzureEndpoints -ProfileName $profile.Name -ResourceGroupName MYRG -Force
   ```

3.    Exécutez à nouveau l’application.

## <a name="troubleshoot-failover"></a>Résoudre les problèmes de basculement 

Déterminez quelle région est à présent primaire pour vous assurer que le basculement a eu lieu. Le rôle indique s’il s’agit de la région primaire/secondaire.

   ```powershell
   $fg = Get-AzureRMSqlDatabaseFailoverGroup -ResourceGroupName "myrg" -ServerName "AWserver" 
   print $fg.role
   ```

## <a name="next-steps"></a>Étapes suivantes 

- Bientôt sur vos écrans
