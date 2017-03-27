---
title: Utiliser Apache Storm avec Power BI | Microsoft Docs
description: "Créez un rapport Power BI en utilisant les données d’une topologie C# s’exécutant sur un cluster Apache Storm dans HDInsight."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 36fe3b9c-5232-4464-8d75-95403b6da7a1
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/01/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 7c28fda22a08ea40b15cf69351e1b0aff6bd0a95
ms.openlocfilehash: 75ab31176abaeed2865a77689a5733666f95a253
ms.lasthandoff: 03/07/2017


---
# <a name="use-power-bi-to-visualize-data-from-an-apache-storm-topology"></a>Utilisation de Power BI pour visualiser les données d'une topologie Apache Storm

Power BI vous permet d’afficher visuellement des données sous forme de rapports. Ce document fournit un exemple d’utilisation d’Apache Storm sur HDInsight pour générer des données pour Power BI.

> [!NOTE]
> Bien que les étapes décrites dans ce document reposent sur un environnement de développement Windows avec Visual Studio, le projet compilé peut être soumis à un cluster HDInsight basé sur Linux ou sur Windows. Seuls les clusters basés sur Linux créés après le 28/10/2016 prennent en charge les topologies SCP.NET.
> 
> Pour utiliser une topologie C# avec un cluster basé sur Linux, mettez à jour le package NuGet Microsoft.SCP.Net.SDK utilisé par votre projet vers la version 0.10.0.6 ou une version supérieure. La version du package doit également correspondre à la version principale de Storm installée sur HDInsight. Par exemple, Storm sur les versions 3.3 et 3.4 de HDInsight utilise Storm 0.10.x, tandis que HDInsight 3.5 utilise Storm 1.0.x.
> 
> Les topologies C# sur les clusters basés sur Linux doivent utiliser .NET 4.5, et utiliser Mono pour s’exécuter sur le cluster HDInsight. La plupart des éléments fonctionnent. Toutefois, vous devez consulter le document [Compatibilité Mono](http://www.mono-project.com/docs/about-mono/compatibility/) pour identifier les éventuelles incompatibilités.
> 
> Pour obtenir une version Java de ce projet, qui fonctionne avec HDInsight Linux ou Windows, consultez [Traitement des événements Azure Event Hubs avec Storm sur HDInsight (Java)](hdinsight-storm-develop-java-event-hub-topology.md).

## <a name="prerequisites"></a>Composants requis

* Un utilisateur Azure Active Directory avec un accès [Power BI](https://powerbi.com).
* Un cluster HDInsight. Pour plus d’informations, consultez [Prise en main de Storm sur HDInsight](hdinsight-apache-storm-tutorial-get-started-linux.md).

  > [!IMPORTANT]
  > Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour en savoir plus, consultez le paragraphe [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-32-and-33-nearing-deprecation-date).

* Visual Studio (l'une des versions suivantes)
  
  * Visual Studio 2012 avec [Update 4](http://www.microsoft.com/download/details.aspx?id=39305)
  * Visual Studio 2013 avec [Update 4](http://www.microsoft.com/download/details.aspx?id=44921) ou [Visual Studio 2013 Community](http://go.microsoft.com/fwlink/?linkid=517284&clcid=0x409)
  * [Visual Studio 2015](https://www.visualstudio.com/downloads/download-visual-studio-vs.aspx)
  * Visual Studio 2017 (toute édition)

* Les outils HDInsight pour Visual Studio : consultez [Prise en main des outils HDInsight pour Visual Studio](hdinsight-hadoop-visual-studio-tools-get-started.md) pour plus d'informations sur l'installation.

## <a name="how-it-works"></a>Fonctionnement

Cet exemple contient une topologie Storm en C# qui génère de façon aléatoire les données du journal Internet Information Services (IIS). Ces données sont ensuite écrites dans une base de données SQL à partir de laquelle elles sont utilisées pour générer des rapports dans Power BI.

Les fichiers suivants implémentent la fonctionnalité principale de cet exemple :

* **SqlAzureBolt.cs**: écrit les informations produites au sein de la topologie Storm dans la base de données SQL.
* **IISLogsTable.sql**: instructions Transact-SQL utilisées pour générer la base de données dans laquelle les données sont stockées.

> [!WARNING]
> Créez la table dans la base de données SQL avant de démarrer la topologie sur votre cluster HDInsight.

## <a name="download-the-example"></a>Télécharger l'exemple

Téléchargez l’ [exemple HDInsight C# Storm Power BI](https://github.com/Azure-Samples/hdinsight-dotnet-storm-powerbi). Pour le télécharger, clonez-le/répliquez-le à l'aide de [git](http://git-scm.com/)ou utilisez le lien **Télécharger** pour télécharger un fichier zip de l'archive.

## <a name="create-a-database"></a>Créer une base de données

1. Pour créer une base de données, suivez les étapes du document [Didacticiel sur la base de données SQL](../sql-database/sql-database-get-started.md).

2. Connectez-vous à la base de données en suivant les étapes décrites dans le document [Se connecter à la base de données SQL avec Visual Studio](../sql-database/sql-database-connect-query.md).

3. Dans l’Explorateur d’objets, cliquez avec le bouton droit sur la base de données et sélectionnez **Nouvelle requête**. Collez le contenu du fichier **IISLogsTable.sql** inclus dans le projet téléchargé dans la fenêtre de requête, puis utilisez Ctrl + Maj + E pour exécuter la requête. Vous devez recevoir un message indiquant que l’exécution des commandes s’est terminée correctement.

## <a name="configure-the-sample"></a>Configurer l'exemple

1. Dans le [Portail Azure](https://portal.azure.com), sélectionnez votre base de données SQL. À partir de la section **Essentials** du panneau Base de données SQL, sélectionnez **Afficher les chaînes de connexion de la base de données**. Dans la liste qui s’affiche, copiez les informations **ADO.NET (Authentification SQL)** .

2. Ouvrez l'exemple dans Visual Studio. Dans **l’Explorateur de solutions**, ouvrez le fichier **App.config**, puis recherchez l’entrée suivante :
   
        <add key="SqlAzureConnectionString" value="##TOBEFILLED##" />
   
    Remplacez la valeur **TOBEFILLED ## ##** par la chaîne de connexion de base de données copiée à l’étape précédente. Remplacez **{your\_username}** et **{your\_password}** par le nom d’utilisateur et le mot de passe d’accès à la base de données.

3. Enregistrez et fermez les fichiers.

## <a name="deploy-the-sample"></a>Déployer l'exemple

1. Dans **l’Explorateur de solutions**, cliquez avec le bouton droit sur le projet **StormToSQL** et sélectionnez **Envoyer à Storm sur HDInsight**. Sélectionnez le cluster HDInsight dans la liste déroulante **Cluster Storm** de la boîte de dialogue.
   
   > [!NOTE]
   > L'affichage des noms de serveur dans la liste déroulante **Cluster Storm** peut prendre plusieurs secondes.
   > 
   > Si vous y êtes invité, entrez les informations d’identification de connexion pour votre abonnement Azure. Si vous disposez de plusieurs abonnements, connectez-vous à celui qui contient votre cluster Storm dans HDInsight.

2. Une fois la topologie envoyée, la __Visionneuse de topologies__ apparaît. Pour afficher cette topologie, sélectionnez l’entrée SqlAzureWriterTopology dans la liste.
   
    ![Les topologies, avec la topologie sélectionnée](./media/hdinsight-storm-power-bi-topology/topologyview.png)
   
    Vous pouvez utiliser cette vue pour afficher des informations sur la topologie. Sinon, double-cliquez sur une entrée (telle que SqlAzureBolt) pour afficher des informations spécifiques à un composant dans la topologie.

3. Après quelques minutes d’exécution de la topologie, revenez à la fenêtre de requête SQL utilisée pour créer la base de données. Remplacez les instructions existantes par la requête suivante :
   
        select * from iislogs;
   
    Utilisez Ctrl+Maj+E pour exécuter la requête. Vous devriez recevoir des résultats similaires à ce qui suit :
   
        1    2016-05-27 17:57:14.797    255.255.255.255    /bar    GET    200
        2    2016-05-27 17:57:14.843    127.0.0.1    /spam/eggs    POST    500
        3    2016-05-27 17:57:14.850    123.123.123.123    /eggs    DELETE    200
        4    2016-05-27 17:57:14.853    127.0.0.1    /foo    POST    404
        5    2016-05-27 17:57:14.853    10.9.8.7    /bar    GET    200
        6    2016-05-27 17:57:14.857    192.168.1.1    /spam    DELETE    200
   
    Ces données ont été écrites à partir de la topologie Storm.

## <a name="create-a-report"></a>Créer un rapport

1. Connectez-vous au [connecteur de base de données SQL Azure](https://app.powerbi.com/getdata/bigdata/azure-sql-database-with-live-connect) pour Power BI.

2. Dans **Databases** (Bases de données), sélectionnez **Get** (Obtenir).

3. Sélectionnez **Azure SQL Database** (Base de données SQL Microsoft Azure), puis sélectionnez **Connect** (Se connecter).

4. Entrez les informations pour vous connecter à votre base de données SQL Microsoft Azure. Vous trouverez ces informations en accédant au [portail Azure](https://portal.azure.com) et en sélectionnant votre base de données SQL.
   
   > [!NOTE]
   > Vous pouvez également définir l’intervalle d’actualisation et les filtres personnalisés à l’aide de l’option **Activer les options avancées** de la boîte de dialogue de connexion.
 
5. Une fois connecté, vous verrez un nouveau jeu de données portant le même nom que la base de données à laquelle vous êtes connecté. Sélectionnez ce jeu de données pour commencer à concevoir un rapport.

6. Dans **Champs**, développez l’entrée **IISLOGS**. Sélectionnez la case à cocher **URISTEM**. Cette option permet de créer un rapport répertoriant les ressources URI (/foo, /bar, etc.) enregistrées dans la base de données.
   
    ![Création d’un rapport](./media/hdinsight-storm-power-bi-topology/createreport.png)

7. Ensuite, faites glisser **METHOD** vers le rapport. Le rapport sera mis à jour pour répertorier les ressources et la méthode HTTP correspondante utilisée pour la requête HTTP.
   
    ![ajout des données de la méthode](./media/hdinsight-storm-power-bi-topology/uristemandmethod.png)

8. À partir de la colonne **Visualisations**, sélectionnez l’icône **Champs**, puis sélectionnez la flèche vers le bas en regard de **METHOD** dans la section **Valeurs**. Dans la liste qui s’affiche, sélectionnez **Count**. Le rapport est modifié pour afficher le nombre d’accès à un URI spécifique.
   
    ![Modification d’un nombre de méthodes](./media/hdinsight-storm-power-bi-topology/count.png)

9. Ensuite, sélectionnez **Histogramme empilé** pour modifier l’affichage des informations.
   
    ![Transformation en graphique empilé](./media/hdinsight-storm-power-bi-topology/stackedcolumn.png)

10. Pour enregistrer le rapport, sélectionnez **Enregistrer** et entrez un nom pour le rapport.

## <a name="stop-the-topology"></a>Arrêt de la topologie

La topologie continue de s’exécuter jusqu’à ce qu’elle soit arrêtée ou supprimée du cluster Storm dans HDInsight. Procédez comme suit pour arrêter la topologie :

1. Dans Visual Studio, retournez dans la Visionneuse de topologies et sélectionnez la topologie.

2. Sélectionnez le bouton **Arrêter** pour arrêter la topologie.
   
    ![Bouton Arrêter dans le résumé de la topologie](./media/hdinsight-storm-power-bi-topology/killtopology.png)

## <a name="delete-your-cluster"></a>Supprimer votre cluster

[!INCLUDE [delete-cluster-warning](../../includes/hdinsight-delete-cluster-warning.md)]

## <a name="next-steps"></a>Étapes suivantes

Dans ce document, vous avez appris à envoyer des données d’une topologie Storm vers une base de données SQL, puis à visualiser les données à l’aide de Power BI. Pour plus d’informations sur l’utilisation des autres technologies Azure à l’aide de Storm sur HDInsight, consultez la rubrique suivante :

* [Exemples de topologies pour Storm dans HDInsight](hdinsight-storm-example-topology.md)


