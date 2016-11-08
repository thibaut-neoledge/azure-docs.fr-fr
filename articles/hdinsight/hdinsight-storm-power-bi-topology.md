---
title: Utiliser Apache Storm avec Power BI | Microsoft Docs
description: Créez un rapport Power BI en utilisant les données d’une topologie C# s’exécutant sur un cluster Apache Storm dans HDInsight.
services: hdinsight
documentationcenter: ''
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal

ms.service: hdinsight
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 08/16/2016
ms.author: larryfr

---
# Utilisation de Power BI pour visualiser les données d'une topologie Apache Storm
Power BI vous permet d’afficher visuellement des données sous forme de rapports. Avec les modèles Visual Studio pour Storm sur HDInsight, vous pouvez facilement utiliser les données stockées d’une topologie exécutée sur un cluster Apache Storm sur HDInsight dans SQL Azure, puis visualiser les données à l’aide de Power BI.

Ce document explique comment utiliser Power BI pour créer un rapport à partir des données générées par une topologie Apache Storm et stockées dans une base de données SQL Azure.

## Composants requis
* Un abonnement Azure. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Un utilisateur Azure Active Directory avec un accès [Power BI](https://powerbi.com)
* Visual Studio (l'une des versions suivantes)
  
  * Visual Studio 2012 avec [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)
  * Visual Studio 2013 avec [Update 4](http://www.microsoft.com/download/details.aspx?id=44921) ou [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?linkid=517284&clcid=0x409)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
* Les outils HDInsight pour Visual Studio : consultez [Prise en main des outils HDInsight pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) pour plus d'informations sur l'installation.

## Fonctionnement
Cet exemple contient une topologie Storm en C# qui génère de façon aléatoire les données du journal Internet Information Services (IIS). Ces données sont ensuite écrites dans une base de données SQL à partir de laquelle elles sont utilisées pour générer des rapports dans Power BI.

Voici une liste des fichiers qui implémentent la fonctionnalité principale de cet exemple.

* **SqlAzureBolt.cs**: écrit les informations produites au sein de la topologie Storm dans la base de données SQL.
* **IISLogsTable.sql** : instructions Transact-SQL utilisées pour générer la base de données dans laquelle les données sont stockées.

> [!WARNING]
> Vous devez créer la table dans la base de données SQL avant de démarrer la topologie sur votre cluster HDInsight.
> 
> 

## Télécharger l'exemple
Téléchargez l’[exemple HDInsight C# Storm Power BI](https://github.com/Azure-Samples/hdinsight-dotnet-storm-powerbi). Pour le télécharger, clonez-le/répliquez-le à l'aide de [git](http://git-scm.com/) ou utilisez le lien **Télécharger** pour télécharger un fichier zip de l'archive.

## Créer une base de données
1. Suivez les étapes du document [Didacticiel sur la base de données SQL](../sql-database/sql-database-get-started.md) pour créer une nouvelle base de données SQL.
2. Connectez-vous à la base de données en suivant les étapes décrites dans le document [Connect to a SQL Database with Visual Studio](../sql-database/sql-database-connect-query.md) (Se connecter à une base de données SQL avec Visual Studio).
3. Cliquez avec le bouton droit sur la base de données dans l’Explorateur d’objets et créez une **Nouvelle requête**. Collez le contenu du fichier **IISLogsTable.sql** inclus dans le projet téléchargé dans la fenêtre de requête, puis utilisez Ctrl + Maj + E pour exécuter la requête. Vous devez recevoir un message indiquant que l’exécution des commandes s’est terminée correctement.
   
    Une fois cette opération terminée, vous aurez accès à une nouvelle table nommée **IISLOGS** dans la base de données.

## Configurer l'exemple
1. Dans le [Portail Azure](https://portal.azure.com), sélectionnez votre base de données SQL. À partir de la section **Essentials** du panneau Base de données SQL, sélectionnez **Afficher les chaînes de connexion de la base de données**. Dans la liste qui s’affiche, copiez les informations **ADO.NET (Authentification SQL)**.
2. Ouvrez l'exemple dans Visual Studio. Dans **l’Explorateur de solutions**, ouvrez le fichier **App.config**, puis recherchez l’entrée suivante :
   
        <add key="SqlAzureConnectionString" value="##TOBEFILLED##" />
   
    Remplacez la valeur **TOBEFILLED ## ##** par la chaîne de connexion de base de données copiée à l’étape précédente. Remplacez **{your\_username}** et **{your\_password}** par le nom d’utilisateur et le mot de passe d’accès à la base de données.
3. Enregistrez et fermez les fichiers.

## Déployer l'exemple
1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **StormToSQL** et sélectionnez **Envoyer à Storm sur HDInsight**. Sélectionnez le cluster HDInsight dans la liste déroulante **Cluster Storm** de la boîte de dialogue.
   
   > [!NOTE]
   > L'affichage des noms de serveur dans la liste déroulante **Cluster Storm** peut prendre plusieurs secondes.
   > 
   > Si vous y êtes invité, entrez les informations d’identification de connexion pour votre abonnement Azure. Si vous disposez de plusieurs abonnements, connectez-vous à celui qui contient votre cluster Storm dans HDInsight.
   > 
   > 
2. Une fois la topologie correctement envoyée, les topologies Storm du cluster doivent s'afficher. Sélectionnez l’entrée SqlAzureWriterTopology à partir de la liste pour afficher des informations sur la topologie en cours d’exécution.
   
    ![Les topologies, avec la topologie sélectionnée](./media/hdinsight-storm-power-bi-topology/topologyview.png)
   
    Vous pouvez utiliser cette vue pour voir des informations sur la topologie. Sinon, double-cliquez sur les entrées (telles que SqlAzureBolt) pour afficher des informations spécifiques à un composant dans la topologie.
3. Après quelques minutes d’exécution de la topologie, revenez à la fenêtre de requête SQL utilisée pour créer la base de données. Remplacez les instructions existantes par ce qui suit.
   
        select * from iislogs;
   
    Utilisez Ctrl + Maj + E pour exécuter la requête. Vous devriez recevoir des résultats similaires à ce qui suit.
   
        1    2016-05-27 17:57:14.797    255.255.255.255    /bar    GET    200
        2    2016-05-27 17:57:14.843    127.0.0.1    /spam/eggs    POST    500
        3    2016-05-27 17:57:14.850    123.123.123.123    /eggs    DELETE    200
        4    2016-05-27 17:57:14.853    127.0.0.1    /foo    POST    404
        5    2016-05-27 17:57:14.853    10.9.8.7    /bar    GET    200
        6    2016-05-27 17:57:14.857    192.168.1.1    /spam    DELETE    200
   
    Il s’agit des données qui ont été écrites à partir de la topologie Storm.

## Créer un rapport
1. Connectez-vous au [connecteur de base de données SQL Azure](https://app.powerbi.com/getdata/bigdata/azure-sql-database-with-live-connect) pour Power BI.
2. Dans **Databases** (Bases de données), sélectionnez **Get** (Obtenir).
3. Sélectionnez **Azure SQL Database** (Base de données SQL Microsoft Azure), puis sélectionnez **Connect** (Se connecter).
4. Entrez les informations pour vous connecter à votre base de données SQL Microsoft Azure. Vous trouverez ces informations en accédant au [Portail Azure](https://portal.azure.com) et en sélectionnant votre base de données SQL.
   
   > [!NOTE]
   > Vous pouvez également définir l’intervalle d’actualisation et les filtres personnalisés à l’aide de l’option **Activer les options avancées** de la boîte de dialogue de connexion.
   > 
   > 
5. Une fois connecté, vous verrez un nouveau jeu de données portant le même nom que la base de données à laquelle vous êtes connecté. Sélectionnez ce jeu de données pour commencer à concevoir un rapport.
6. Dans **Champs**, développez l’entrée **IISLOGS**. Sélectionnez la case à cocher **URISTEM**. Cette option permet de créer un rapport répertoriant les ressources URI (/foo, /bar, etc.) enregistrées dans la base de données.
   
    ![Création d’un rapport](./media/hdinsight-storm-power-bi-topology/createreport.png)
7. Ensuite, faites glisser **METHOD** vers le rapport. Le rapport sera mis à jour pour répertorier les ressources et la méthode HTTP correspondante utilisée pour la requête HTTP.
   
    ![ajout des données de la méthode](./media/hdinsight-storm-power-bi-topology/uristemandmethod.png)
8. À partir de la colonne **Visualisations**, sélectionnez l’icône **Champs**, puis sélectionnez la flèche vers le bas en regard de **METHOD** dans la section **Valeurs**. Dans la liste qui s’affiche, sélectionnez **Count**. Le rapport sera modifié pour afficher le nombre d’accès à un URI spécifique.
   
    ![Modification d’un nombre de méthodes](./media/hdinsight-storm-power-bi-topology/count.png)
9. Ensuite, sélectionnez **Histogramme empilé** pour modifier l’affichage des informations.
   
    ![Transformation en graphique empilé](./media/hdinsight-storm-power-bi-topology/stackedcolumn.png)
10. Une fois le rapport souhaité obtenu, utilisez l’entrée **Enregistrer** du menu pour entrer un nom et enregistrer le rapport.

## Arrêt de la topologie
La topologie continue de s'exécuter jusqu'à ce qu'elle soit arrêtée ou supprimée du cluster Storm dans HDInsight. Procédez comme suit pour arrêter la topologie :

1. Dans Visual Studio, retournez dans la Visionneuse de topologies et sélectionnez la topologie.
2. Sélectionnez le bouton **Arrêter** pour arrêter la topologie.
   
    ![Bouton Arrêter dans le résumé de la topologie](./media/hdinsight-storm-power-bi-topology/killtopology.png)

## Supprimer votre cluster
[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## Étapes suivantes
Dans ce document, vous avez appris à envoyer des données d’une topologie Storm vers une base de données SQL, puis à visualiser les données à l’aide de Power BI. Pour plus d’informations sur l’utilisation des autres technologies Azure à l’aide de Storm sur HDInsight, consultez la rubrique suivante :

* [Exemples de topologies pour Storm dans HDInsight](hdinsight-storm-example-topology.md)

<!---HONumber=AcomDC_0914_2016-->