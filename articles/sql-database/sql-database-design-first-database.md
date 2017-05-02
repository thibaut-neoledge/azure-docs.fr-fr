---
title: "Concevez votre première solution Azure SQL Database | Microsoft Docs"
description: "Apprenez à concevoir votre première base de données SQL Azure."
services: sql-database
documentationcenter: 
author: janeng
manager: jstrauss
editor: 
tags: 
ms.assetid: 
ms.service: sql-database
ms.custom: tutorial-develop
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 03/30/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 0c4554d6289fb0050998765485d965d1fbc6ab3e
ms.openlocfilehash: 933b262f3c587229a194c3259fc5c13b75ecd050
ms.lasthandoff: 04/13/2017


---

# <a name="design-your-first-azure-sql-database"></a>Concevoir votre première base de données SQL Azure

Dans ce didacticiel, vous générez une base de données pour une université afin qu’elle puisse effectuer le suivi des notes des étudiants et de l’inscription aux cours. Ce didacticiel montre comment utiliser le [portail Azure](https://portal.azure.com/) et [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) pour créer une base de données SQL Azure sur un serveur logique Azure SQL Database, ajouter des tables à la base de données, charger des données dans les tables et interroger la base de données. Il montre également comment utiliser les capacités de [limite de restauration dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore) de SQL Database pour restaurer la base de données à un point antérieur dans le temps.

Pour effectuer ce didacticiel, assurez-vous que vous avez installé la dernière version de [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS). 

## <a name="step-1-log-in-to-the-azure-portal"></a>Étape 1 : Se connecter au portail Azure

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="step-2-create-a-blank-sql-database-in-azure"></a>Étape 2 : Créer une base de données SQL vide dans Azure

Une base de données SQL Azure est créée avec un ensemble défini de [ressources de calcul et de stockage](sql-database-service-tiers.md). La base de données est créée dans un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) et dans un [serveur logique Azure SQL Database](sql-database-features.md). 

Pour créer une base de données SQL vide, suivez la procédure suivante. 

1. Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail Azure.

2. Sélectionnez **Bases de données** dans la page **Nouveau**, puis **Base de données SQL** dans la page **Bases de données**. 

    ![créer une base de données vide](./media/sql-database-design-first-database/create-empty-database.png)

3. Remplissez le formulaire de base de données SQL avec les informations suivantes, comme indiqué dans l’illustration précédente :     

   - Nom de la base de données : **mySampleDatabase**
   - Groupe de ressources : **myResourceGroup**
   - Source : **base de données vide**

4. Cliquez sur **Serveur** pour créer et configurer un serveur pour votre nouvelle base de données. Remplissez le **formulaire Nouveau serveur** en spécifiant un nom de serveur global unique, fournissez un nom pour la connexion d’administrateur serveur et spécifiez le mot de passe de votre choix. 

    ![create database-server](./media//sql-database-design-first-database/create-database-server.png)
5. Cliquez sur **Sélectionner**.

6. Cliquez sur **Niveau tarifaire** pour spécifier le niveau de service et le niveau de performances pour votre nouvelle base de données. Pour ce didacticiel, sélectionnez **20 DTU** et **250** Go de stockage.

    ![create database-s1](./media/sql-database-design-first-database/create-empty-database-pricing-tier.png)

7. Cliquez sur **Apply**.  

8. Cliquez sur **Créer** pour approvisionner la base de données. L’approvisionnement prend environ une minute et demie. 

9. Dans la barre d’outils, cliquez sur **Notifications** pour surveiller le processus de déploiement.

    ![notification](./media/sql-database-get-started-portal/notification.png)


## <a name="step-3-create-a-server-level-firewall-rule"></a>Étape 3 : Créer une règle de pare-feu au niveau du serveur

Les bases de données Azure SQL Database sont protégées par un pare-feu. Par défaut, toutes les connexions au serveur et aux bases de données du serveur sont rejetées. Suivez la procédure suivante pour créer une [règle de pare-feu au niveau du serveur SQL Database](sql-database-firewall-configure.md) pour votre serveur afin d’autoriser les connexions depuis l’adresse IP de votre client. 

1. Une fois le déploiement terminé, cliquez sur **Bases de données SQL** dans le menu de gauche et cliquez sur votre nouvelle base de données, **mySampleDatabase**, sur la page **Bases de données SQL**. La page de présentation de votre base de données s’ouvre, affiche le nom de serveur complet (tel que **mynewserver-20170313.database.windows.net**) et fournit des options pour poursuivre la configuration.

      ![règle de pare-feu de serveur](./media/sql-database-design-first-database/server-firewall-rule.png) 

2. Cliquez sur **Définir le pare-feu du serveur** dans la barre d’outils, comme illustré sur l’image précédente. La page **Paramètres de pare-feu** du serveur de base de données SQL s’ouvre. 

3. Cliquez sur **Ajouter une adresse IP cliente** dans la barre d’outils, puis cliquez sur **Enregistrer**. Une règle de pare-feu au niveau du serveur est créée pour votre adresse IP actuelle.

      ![définir la règle de pare-feu de serveur](./media/sql-database-design-first-database/server-firewall-rule-set.png) 

4. Cliquez sur **OK**, puis cliquez sur la **X** pour fermer la page **Paramètres de pare-feu**.

Vous pouvez maintenant vous connecter à la base de données et à son serveur à l’aide de SQL Server Management Studio ou tout autre outil de votre choix.

> [!NOTE]
> SQL Database communique par le biais du port 1433. Si vous essayez de vous connecter à partir d’un réseau d’entreprise, le trafic sortant sur le port 1433 peut ne pas être autorisé par le pare-feu de votre réseau. Dans ce cas, vous ne pourrez pas vous connecter à votre serveur Azure SQL Database, sauf si votre service informatique ouvre le port 1433.
>

## <a name="step-4---get-connection-information"></a>Étape 4 : Obtenir les informations de connexion

Obtenez le nom de serveur complet de votre serveur Azure SQL Database dans le portail Azure. Utilisez le nom de serveur complet pour vous connecter à votre serveur avec SQL Server Management Studio.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Bases de données SQL** dans le menu de gauche, puis cliquez sur votre base de données dans la page **Bases de données SQL**. 
3. Dans le volet **Essentials** de la page du portail Azure pour votre base de données, recherchez et copiez le **nom du serveur**.

    ![informations de connexion](./media/sql-database-connect-query-ssms/connection-information.png) 

## <a name="step-5---connect-to-your-database-using-sql-server-management-studio"></a>Étape 5 : Se connecter à votre base de données à l’aide de SQL Server Management Studio

Utilisez [SQL Server Management Studio](https://docs.microsoft.com/sql/ssms/sql-server-management-studio-ssms) pour établir une connexion à votre serveur Azure SQL Database.

1. Ouvrez SQL Server Management Studio.

2. Dans la fenêtre **Se connecter au serveur**, entrez les valeurs suivantes :
   - **Type de serveur** : spécifiez le moteur de base de données
   - **Nom du serveur** : entrez votre nom de serveur complet, par exemple **mynewserver20170313.database.windows.net**
   - **Authentification** : spécifiez l’authentification SQL Server
   - **Connexion** : entrez votre compte d’administrateur de serveur
   - **Mot de passe** : entrez le mot de passe de votre compte d’administrateur de serveur


   <img src="./media/sql-database-connect-query-ssms/connect.png" alt="connect to server" style="width: 780px;" />

3. Cliquez sur **Options** dans la boîte de dialogue **Se connecter au serveur**. Dans la section **Se connecter à la base de données**, entrez **mySampleDatabase** pour vous connecter à cette base de données.

   ![connexion à la base de données sur le serveur](./media/sql-database-connect-query-ssms/options-connect-to-db.png)  

4. Cliquez sur **Connecter**. La fenêtre Explorateur d’objets s’ouvre dans SSMS. 

5. Dans l’Explorateur d’objets, développez **Bases de données**, puis **mySampleDatabase** pour afficher les objets dans la base de données exemple.

   ![objets de base de données](./media/sql-database-connect-query-ssms/connected.png)  

## <a name="step-6---create-tables-in-the-database"></a>Étape 6 : Créer des tables dans la base de données 

Créez un schéma de base de données avec quatre tables qui modélisent un système de gestion des étudiants pour les universités à l’aide de [Transact-SQL](https://docs.microsoft.com/sql/t-sql/language-reference) :

- Personne
- Cours
- Étudiant
- Attribuez à ce modèle un système de gestion des étudiants pour les universités

Le diagramme suivant montre comment ces tables sont liées entre elles. Certaines de ces tables référencent des colonnes d’autres tables. Par exemple, la table Étudiant référence la colonne **PersonId** de la table **Personne**. Étudiez le diagramme pour comprendre comment les tables présentées dans ce didacticiel sont liées entre elles. Pour découvrir comment créer des tables de base de données efficaces, consultez [Créer des tables de base de données efficaces](https://msdn.microsoft.com/library/cc505842.aspx). Pour plus d’informations sur le choix des types de données, consultez [Types de données](https://docs.microsoft.com/sql/t-sql/data-types/data-types-transact-sql).

> [!NOTE]
> Vous pouvez également utiliser le [concepteur de tables dans SQL Server Management Studio](https://msdn.microsoft.com/library/hh272695.aspx) pour créer et concevoir vos tables. 

![Relations entre les tables](./media/sql-database-design-first-database/tutorial-database-tables.png)

1. Dans l’Explorateur d’objets, cliquez avec le bouton droit sur **mySampleDatabase**, puis cliquez sur **Nouvelle requête**. Une fenêtre de requête vide connectée à votre base de données s’ouvre.

2. Dans la fenêtre de requête, exécutez la requête suivante pour créer quatre tables dans votre base de données : 

   ```sql 
   -- Create Person table

    CREATE TABLE Person
    (
      PersonId      INT IDENTITY PRIMARY KEY,
      FirstName     NVARCHAR(128) NOT NULL,
      MiddelInitial NVARCHAR(10),
      LastName      NVARCHAR(128) NOT NULL,
      DateOfBirth   DATE NOT NULL
    )
   
   -- Create Student table
 
    CREATE TABLE Student
    (
      StudentId INT IDENTITY PRIMARY KEY,
      PersonId  INT REFERENCES Person (PersonId),
      Email     NVARCHAR(256)
    )
    
   -- Create Course table
 
    CREATE TABLE Course
    (
      CourseId  INT IDENTITY PRIMARY KEY,
      Name      NVARCHAR(50) NOT NULL,
      Teacher   NVARCHAR(256) NOT NULL
    ) 

   -- Create Credit table
 
    CREATE TABLE Credit
    (
      StudentId   INT REFERENCES Student (StudentId),
      CourseId    INT REFERENCES Course (CourseId),
      Grade       DECIMAL(5,2) CHECK (Grade <= 100.00),
      Attempt     TINYINT,
      CONSTRAINT  [UQ_studentgrades] UNIQUE CLUSTERED
      (
        StudentId, CourseId, Grade, Attempt
      )
    )
   ```

![créez des tables](./media/sql-database-design-first-database/create-tables.png)

3. Développez le nœud « tables » dans l’Explorateur d’objets SQL Server Management Studio pour afficher les tables que vous avez créées.

   ![tables ssms-créées](./media/sql-database-design-first-database/ssms-tables-created.png)

## <a name="step-7---load-data-into-the-tables"></a>Étape 7 : Charger des données dans les tables

1. Créez un dossier nommé **SampleTableData** dans le dossier Téléchargements pour y stocker les exemples de données pour votre base de données. 

2. Cliquez sur les liens suivants et enregistrez-les dans le dossier **SampleTableData**. 

   - [SampleCourseData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCourseData)
   - [SamplePersonData](https://sqldbtutorial.blob.core.windows.net/tutorials/SamplePersonData)
   - [SampleStudentData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData)
   - [SampleCreditData](https://sqldbtutorial.blob.core.windows.net/tutorials/SampleCreditData)

3. Ouvrez une fenêtre d’invite de commandes et accédez au dossier SampleTableData.

4. Exécutez les commande suivantes pour insérer des exemples de données dans les tables, en remplaçant les valeurs de **ServerName**, **DatabaseName**, **UserName** et **Password** avec les valeurs pour votre environnement.
  
   ```bcp
   bcp Course in SampleCourseData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Person in SamplePersonData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Student in SampleStudentData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   bcp Credit in SampleCreditData -S <ServerName>.database.windows.net -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   ```

Vous avez maintenant chargé des exemples de données dans les tables que vous avez créées précédemment.

## <a name="step-8---query-the-tables"></a>Étape 8 : Interroger les tables

Exécutez les requêtes suivantes pour récupérer des informations à partir des tables de base de données. Consultez [Écriture des requêtes SQL](https://technet.microsoft.com/library/bb264565.aspx) pour en savoir plus sur l’écriture des requêtes SQL. La première requête réunit les quatre tables pour rechercher tous les étudiants inscrits au cours de « Dominick Pope » pour lequel ils ont une note supérieure à 75 %. La deuxième requête réunit les quatre tables et recherche tous les cours que « Noe Coleman » a déjà suivis.

1. Dans une fenêtre de requête SQL Server Management Studio, exécutez la requête suivante :

   ```sql 
   -- Find the students taught by Dominick Pope who have a grade higher than 75%

    SELECT  person.FirstName,
        person.LastName,
        course.Name,
        credit.Grade
    FROM  Person AS person
        INNER JOIN Student AS student ON person.PersonId = student.PersonId
        INNER JOIN Credit AS credit ON student.StudentId = credit.StudentId
        INNER JOIN Course AS course ON credit.CourseId = course.courseId
    WHERE course.Teacher = 'Dominick Pope' 
        AND Grade > 75
   ```

2. Dans une fenêtre de requête SQL Server Management Studio, exécutez la requête suivante :

   ```sql
   -- Find all the courses in which Noe Coleman has ever enrolled

    SELECT  course.Name,
        course.Teacher,
        credit.Grade
    FROM  Course AS course
        INNER JOIN Credit AS credit ON credit.CourseId = course.CourseId
        INNER JOIN Student AS student ON student.StudentId = credit.StudentId
        INNER JOIN Person AS person ON person.PersonId = student.PersonId
    WHERE person.FirstName = 'Noe'
        AND person.LastName = 'Coleman'
   ```

## <a name="step-9---restore-a-database-to-a-previous-point-in-time"></a>Étape 9 : Restaurer une version antérieure d’une base de données 

Imaginez que vous avez supprimé une table par inadvertance. Il s’agit de quelque chose que vous ne pouvez pas récupérer facilement. Azure SQL Database vous permet de revenir à n’importe quel moment des 35 derniers jours et de restaurer ce moment pour obtenir une nouvelle base de données. Vous pouvez utiliser cette base de données pour récupérer vos données supprimées. Les étapes suivantes restaurent la base de données à un point situé avant l’ajout des tables.

1. Sur la page SQL Database de votre base de données, cliquez sur **Restaurer** dans la barre d’outils. La page **Restauration** s’ouvre.

   ![restauration](./media/sql-database-design-first-database/restore.png)

2. Remplissez le formulaire **Restaurer** avec les informations requises :
    * Nom de la base de données : entrez un nom pour la base de données 
    * Point dans le temps : Sélectionnez l’onglet **Point dans le temps** dans le formulaire Restauration 
    * Point de restauration : Sélectionnez une heure avant la modification de la base de données
    * Serveur cible : Vous ne pouvez pas modifier cette valeur lors de la restauration d’une base de données 
    * Pool de base de données élastique : sélectionnez **Aucun**  
    * Niveau tarifaire : sélectionnez **20 DTU** et **250 Go** de stockage.

   ![point de restauration](./media/sql-database-design-first-database/restore-point.png)

3. Cliquez sur **OK** pour restaurer la base de données [à un point dans le temps](sql-database-recovery-using-backups.md#point-in-time-restore) avant l’ajout des tables. La restauration d’une base de données vers un autre point dans le temps crée une base de données en double sur le même serveur que la base de données d’origine en date du point dans le temps que vous spécifiez, à condition qu’il se trouve au sein de la période de rétention pour votre [niveau de service](sql-database-service-tiers.md).

## <a name="next-steps"></a>Étapes suivantes 

Pour obtenir des exemples de PowerShell pour les tâches courantes, consultez [Exemples PowerShell pour SQL Database](sql-database-powershell-samples.md)

