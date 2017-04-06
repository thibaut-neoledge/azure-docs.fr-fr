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
ms.custom: tutorial
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: 
ms.date: 03/23/2017
ms.author: janeng
translationtype: Human Translation
ms.sourcegitcommit: 07635b0eb4650f0c30898ea1600697dacb33477c
ms.openlocfilehash: 313bcf4fbc0ab7f251dd62b1e2151afef8392a55
ms.lasthandoff: 03/28/2017


---

# <a name="design-your-first-azure-sql-database"></a>Concevoir votre première base de données SQL Azure

Ce didacticiel vous apprend à utiliser le portail Azure pour créer une base de données sur un nouveau serveur avec un pare-feu de niveau serveur. Vous utiliserez ensuite SQL Server Management Studio pour créer une table, charger des données dans cette table, interroger la table et lui ajouter un index. Enfin, vous allez utiliser les sauvegardes automatisées du service SQL Database pour restaurer la base de données à un point antérieur dans le temps, avant l’ajout de cette nouvelle table.

Pour effectuer ce didacticiel, assurez-vous que vous avez installé la dernière version de [SQL Server Management Studio](https://msdn.microsoft.com/library/ms174173.aspx) (SSMS) 

## <a name="step-1---log-in-to-the-azure-portal"></a>Étape 1 : Connexion au portail Azure

Connectez-vous au [portail Azure](https://portal.azure.com/).

## <a name="step-2---create-a-sql-database"></a>Étape 2 : Création d’une base de données SQL

Une base de données SQL Azure est créée avec un ensemble défini de [ressources de calcul et de stockage](sql-database-service-tiers.md). La base de données est créée dans un [groupe de ressources Azure](../azure-resource-manager/resource-group-overview.md) et dans un [serveur logique Azure SQL Database](sql-database-features.md). 

Suivez ces étapes pour créer une base de données SQL contenant les exemples de données Adventure Works LT. 

1. Cliquez sur le bouton **Nouveau** dans le coin supérieur gauche du portail Azure.

2. Sélectionnez **Bases de données** dans la page **Nouveau**, puis **Base de données SQL** dans la page **Bases de données**.

3. Remplissez le formulaire SQL Database avec les informations requises : 
   - Nom de la base de données : entrez un nom pour la base de données
   - Abonnement : sélectionnez votre abonnement
   - Groupe de ressources : sélectionnez un nouveau groupe ou un groupe existant
   - Source : sélectionnez **Exemple (AdventureWorksLT)**
   - Serveur : créez un serveur (le nom du **serveur** doit être globalement unique)
   - Pool élastique : sélectionnez **Not now** (Pas maintenant) pour ce démarrage rapide
   - Niveau tarifaire : sélectionnez **20 DTU** et **250** Go de stockage
   - Classement : vous ne pouvez pas modifier cette valeur lors de l’importation de la base de données exemple 
   - Épingler au tableau de bord : cochez cette case

      ![créer une base de données](./media/sql-database-get-started/create-database-s1.png)

4. Lorsque vous avez terminé, cliquez sur **Créer**. L’approvisionnement prend quelques minutes.
5. Une fois le déploiement de la base de données SQL terminé, sélectionnez les **bases de données SQL** dans le tableau de bord ou en sélectionnant **Bases de données SQL** dans le menu de gauche et en cliquant sur votre nouvelle base de données sur la page **Bases de données SQL**. Une page de présentation de votre base de données s’ouvre, affiche le nom de serveur complet (tel que **mynewserver20170313.database.windows.net**) et fournit des options pour poursuivre la configuration.

      ![nouvelle base de données sql](./media/sql-database-get-started/new-database-s1-overview.png) 

## <a name="step-3---create-a-server-level-firewall-rule"></a>Étape 3 : Créer une règle de pare-feu au niveau du serveur

Le service SQL Database crée un pare-feu empêchant les applications et les outils externes de se connecter à votre serveur et à votre base de données. Suivez ces étapes pour créer une [règle de pare-feu au niveau du serveur de base de données SQL](sql-database-firewall-configure.md) pour votre adresse IP afin de permettre la connectivité externe via le pare-feu de base de données SQL. 

1. Cliquez sur **Définir le pare-feu du serveur** dans la barre d’outils de votre base de données. La page **Paramètres de pare-feu** du serveur de base de données SQL s’ouvre. 

      ![règle de pare-feu de serveur](./media/sql-database-get-started/server-firewall-rule.png) 

2. Cliquez sur **Ajouter une adresse IP cliente** dans la barre d’outils, puis cliquez sur **Enregistrer**. Une règle de pare-feu au niveau du serveur est créée pour votre adresse IP actuelle.

3. Cliquez sur **OK**, puis cliquez sur la **X** pour fermer la page Paramètres de pare-feu.

Vous pouvez maintenant vous connecter à la base de données et à son serveur à l’aide de SQL Server Management Studio ou tout autre outil de votre choix.

## <a name="step-4---get-connection-information"></a>Étape 4 : Obtenir les informations de connexion

Obtenez le nom de serveur complet de votre serveur Azure SQL Database dans le portail Azure. Utilisez le nom de serveur complet pour vous connecter à votre serveur avec SQL Server Management Studio.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Sélectionnez **Bases de données SQL** dans le menu de gauche, puis cliquez sur votre base de données dans la page **Bases de données SQL**. 
3. Dans le volet **Essentials** de la page du portail Azure pour votre base de données, recherchez et copiez le **nom du serveur**.

    <img src="./media/sql-database-connect-query-ssms/connection-information.png" alt="connection information" style="width: 780px;" />

## <a name="step-5---connect-to-the-server-using-ssms"></a>Étape 5 : Connexion au serveur à l’aide de SSMS

Utilisez SQL Server Management Studio pour établir une connexion à votre serveur de base de données SQL Azure.

1. Saisissez **SSMS** dans la zone de recherche Windows, puis cliquez sur **Entrée** pour ouvrir SSMS.

2. Dans la fenêtre **Se connecter au serveur**, entrez les valeurs suivantes :
   - **Type de serveur** : spécifiez le moteur de base de données
   - **Nom du serveur** : entrez votre nom de serveur complet, par exemple **mynewserver20170313.database.windows.net**
   - **Authentification** : spécifiez l’authentification SQL Server
   - **Connexion** : entrez votre compte d’administrateur de serveur
   - **Mot de passe** : entrez le mot de passe de votre compte d’administrateur de serveur
 
    <img src="./media/sql-database-connect-query-ssms/connect.png" alt="connect to server" style="width: 780px;" />

3. Cliquez sur **Connecter**. La fenêtre Explorateur d’objets s’ouvre dans SSMS. 

    <img src="./media/sql-database-connect-query-ssms/connected.png" alt="connected to server" style="width: 780px;" />

4. Dans l’Explorateur d’objets, développez **Bases de données**, puis **mySampleDatabase** pour afficher les objets dans la base de données exemple.

## <a name="step-6---create-and-query-a-table"></a>Étape 6 : Création et interrogation d’une table 
1. Dans l’Explorateur d’objets, cliquez avec le bouton droit sur **mySampleDatabase**, puis cliquez sur **Nouvelle requête**. Une fenêtre de requête vide connectée à votre base de données s’ouvre.
2. Dans la fenêtre de requête, exécutez la requête suivante :

   ```sql 
   CREATE TABLE [dbo].[Students]
   (
     [student_id] int, 
     [name] varchar(100),
     [age] int,
     [email] varchar(100),
     [AddressID] int REFERENCES [SalesLT].[Address] (AddressID)
   );
   ```

   Une fois la requête terminée, vous avez créé une table vide appelée Students dans votre base de données.

3. Dans une fenêtre de requête SSMS, exécutez la requête suivante : 

   ```sql
   SELECT name, age, email 
   FROM [dbo].[Students]
   ```

   La table Students ne retourne aucune donnée.

## <a name="step-7---load-data-into-the-table"></a>Étape 7 : Chargement de données dans la table 
1. Ouvrir une fenêtre d’invite de commandes.

2. Exécutez la commande PowerShell suivante pour télécharger un exemple de fichier texte dans votre répertoire actuel.

   ```powershell
   powershell -command "& { (New-Object Net.WebClient).DownloadFile('https://sqldbtutorial.blob.core.windows.net/tutorials/SampleStudentData.txt', 'SampleStudentData.txt'); echo 'Download complete' }" 
   ``` 

3. Une fois cette opération terminée, exécutez la commande suivante pour insérer 1 000 lignes dans la table Students, en remplaçant les valeurs de **ServerName**, **DatabaseName**, **UserName** et **Password** avec les valeurs pour votre environnement.

   ```bcp
   bcp Students in SampleStudentData.txt -S <ServerName> -d <DatabaseName> -U <Username> -P <password> -q -c -t ","
   ```

Vous avez maintenant chargé des exemples de données dans la table que vous avez créée précédemment.

## <a name="step-8---add-an-index-to-a-table"></a>Étape 8 : Ajouter un index à une table
Pour rendre la recherche de valeurs spécifiques dans le tableau plus efficace, créez un index sur la table Students. Un index organise les données de sorte que seule une partie des données doit maintenant être examinée pour rechercher une valeur spécifique.

1. Dans une fenêtre de requête SSMS, exécutez la requête suivante :

   ```sql 
   CREATE NONCLUSTERED INDEX IX_Age ON Students (age);
   ```

2. Dans une fenêtre de requête SSMS, exécutez la requête suivante :

   ```sql
   SELECT name, age, email 
   FROM [dbo].[Students]
   WHERE age > 20
   ```

   Cette requête renvoie le nom, l’âge et l’adresse électronique des étudiants qui ont plus de 20 ans.

## <a name="step-9---restore-a-database-to-a-point-in-time"></a>Étape 9 : Restaurer une base de données à un point dans le temps 
Les bases de données dans Azure ont des [sauvegardes continues](sql-database-automated-backups.md) qui sont effectuées automatiquement toutes les 5 à 10 minutes. Ces sauvegardes permettent de restaurer votre base de données à un point antérieur dans le temps. La restauration d’une base de données vers un autre point dans le temps crée une base de données en double sur le même serveur que la base de données d’origine en date du point dans le temps que vous spécifiez (au sein de la période de rétention pour votre niveau de service). Les étapes suivantes restaurent la base de données à un point situé avant l’ajout de la table **Students**. 

1. Sur la page SQL Database de votre base de données, cliquez sur **Restaurer** dans la barre d’outils. La page **Restauration** s’ouvre.

    <img src="./media/sql-database-design-first-database/restore.png" alt="restore" style="width: 780px;" />

2. Remplissez le formulaire **Restaurer** avec les informations requises :
    * Nom de la base de données : entrez un nom pour la base de données 
    * Point dans le temps : Sélectionnez l’onglet **Point dans le temps** dans le formulaire Restauration 
    * Point de restauration : Sélectionnez une heure avant la modification de la base de données
    * Serveur cible : Vous ne pouvez pas modifier cette valeur lors de la restauration d’une base de données 
    * Pool de base de données élastique : sélectionnez **Aucun**  
    * Niveau tarifaire : sélectionnez **20 DTU** et **250 Go** de stockage.

    <img src="./media/sql-database-design-first-database/restore-point.png" alt="restore-point" style="width: 780px;" />

3. Cliquez sur **OK** pour restaurer la base de données à un point dans le temps avant l’ajout de la table *Students*.

## <a name="next-steps"></a>Étapes suivantes 
Pour obtenir des exemples de PowerShell pour les tâches courantes, consultez [Exemples PowerShell pour SQL Database](sql-database-powershell-samples.md)

