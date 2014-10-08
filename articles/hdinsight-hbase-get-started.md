<properties linkid="manage-services-hdinsight-hbase-get-started-hdinsight-hadoop" urlDisplayName="Get Started" pageTitle="Get started using HBase with Hadoop in HDInsight | Azure" metaKeywords="" description="Get started using HBase with Hadoop in HDInsight. learn how to created HBase tables and query them with Hive." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using HBase with Hadoop in HDInsight" authors="bradsev" solutions="big-data" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/21/2014" ms.author="bradsev"></tags>

# Prise en main de HBase avec Hadoop dans HDInsight

HBase est une base de données NoSQL à faible latence qui permet le traitement transactionnel en ligne des données volumineuses (« Big Data »). HBase est proposé en tant que cluster géré intégré à l'environnement Azure. Les clusters sont configurés de façon à stocker les données directement dans le stockage d'objets blob Azure, ce qui fournit une faible latence et une élasticité accrue en matière de choix performances/coût. Cela permet aux clients de créer des sites web interactifs fonctionnant avec des jeux de données volumineux, de créer des services stockant les données de capteur et de télémétrie provenant de millions de points de terminaison, et d'analyser ces données avec des tâches Hadoop.

Dans ce didacticiel, vous allez apprendre à créer et à interroger des tables HBase avec HDInsight. Les procédures suivantes sont décrites :

-   approvisionner un cluster HBase en utilisant le portail Azure ;
-   activer et utiliser RDP pour accéder au shell HBase et l'utiliser pour créer un exemple de table HBase, ajouter des lignes et lister les lignes dans la table ;
-   créer une table Hive qui peut être mappée à une table HBase existante et utiliser HiveQL pour interroger les données de la table HBase ;
-   utiliser le Kit de développement logiciel (SDK) .NET pour créer une table HBase, lister les tables HBase dans votre compte et ajouter et récupérer les lignes de vos tables.

> [WACOM.NOTE] HBase est actuellement disponible uniquement en version préliminaire pour une utilisation avec les clusters HDInsight 3.0 sur HDInsight (basé sur Hadoop 2.2.0). Pour obtenir des informations de version, consultez la rubrique [Nouveautés des versions de cluster Hadoop fournies par HDInsight][]

Dans la version préliminaire, nous vous recommandons de stocker en dehors du cluster une copie de sauvegarde de la version d'origine des données utilisées dans le cluster HBase. En effet, le format des fichiers de base de données pourrait être modifié dans les versions ultérieures, ce qui rendrait impossible la prise en charge ou la mise à niveau des formats de fichiers de données actuels utilisés dans la version préliminaire.

**Configuration requise :**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

-   Un abonnement Azure. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les [formules d'abonnement][], les [offres spéciales membres][] ou la [version d'évaluation gratuite][].
-   Un compte de stockage Azure. Pour obtenir des instructions, consultez la rubrique [Création d'un compte de stockage][].
-   Copie de Visual Studio.

**Durée de réalisation estimée :** 30 minutes

## Dans ce didacticiel

-   [Approvisionnement d'un cluster HBase dans le portail Azure][]
-   [Création d'un exemple de table HBase à partir du shell HBase][]
-   [Utilisation de Hive pour interroger une table HBase][]
-   [Utilisation des API C\# HBase pour créer une table HBase et en extraire les données][]
-   [Résumé][]

## <a name="create-hbase-cluster"></a>Approvisionnement d'un cluster HBase dans le portail Azure

Cette section décrit comment approvisionner un cluster HBase en utilisant le portail Azure.

**Approvisionnement d'un cluster HDInsight dans le portail Azure**

1.  Connectez-vous au [portail de gestion Azure][].

2.  Cliquez sur **HDInsight** à gauche pour afficher le statut des clusters de votre compte, puis sur l'icône **+Nouveau** en bas à gauche.

    ![][]

3.  Cliquez sur l'icône HDInsight dans la deuxième colonne en partant de la gauche, puis sur l'option HBase dans la colonne suivante. Spécifiez les valeurs de CLUSTERNAME et CLUSTER SIZE, le nom du compte de stockage et un mot de passe pour le nouveau cluster HBase.

    ![][1]

4.  Cliquez sur l'icône en forme de coche en bas à gauche pour créer le cluster HBase.

## <a name="create-sample-table"></a>Création d'un exemple de table HBase à partir du shell HBase

Cette section décrit comment activer et utiliser RDP pour accéder au shell HBase et l'utiliser pour créer un exemple de table HBase, ajouter des lignes et lister les lignes dans la table.

Vous devez avoir terminé la procédure indiquée dans la première section et créé un cluster HBase.

**Activation de la connexion RDP avec le cluster HBase**

1.  Pour activer une connexion Bureau à distance avec le cluster HDInsight, sélectionnez le cluster HBase que vous avez créé et cliquez sur l'onglet **CONFIGURATION**. Cliquez sur le bouton **ACTIVER DISTANT** en bas de la page pour activer la connexion RDP avec le cluster.
2.  Indiquez les informations d'identification et la date d'expiration dans l'Assistant **CONFIGURER LE BUREAU À DISTANCE** et cliquez sur la coche entourée en bas à droite. (L'opération peut prendre quelques minutes.)
3.  Pour vous connecter au cluster HDInsight, cliquez sur le bouton **CONNECTER** en bas de l'onglet **CONFIGURATION**.

**Ouverture du shell HBase**

1.  Dans votre session RDP, cliquez sur le raccourci **Invite de commandes Hadoop** sur le Bureau.

2.  Définissez le dossier sur le répertoire d'accueil HBase :

        cd %HBASE_HOME%\bin

3.  Ouvrez le shell HBase :

        hbase shell

**Création d'un exemple de table, ajout de données et extraction des données**

1.  Création d'un exemple de table :

        create 'sampletable', 'cf1'

2.  Ajout d'une ligne à l'exemple de table :

        put 'sampletable', 'row1', 'cf1:col1', 'value1'

3.  Liste des lignes dans l'exemple de table :

        scan 'sampletable'

## <a name="hive-query"></a>Utilisation de Hive pour interroger une table HBase

Maintenant qu'un cluster HBase est approvisionné et qu'une table est créée, vous pouvez l'interroger avec Hive. Cette section crée une table Hive qui peut être mappée à la table HBase et l'utilise pour interroger les données de votre table HBase.

**Ouverture d'un tableau de bord de cluster**

1.  Connectez-vous au [portail de gestion Azure][].
2.  Cliquez sur **HDINSIGHT** dans le volet de gauche. La liste des clusters créés s'affiche, avec celui que vous avez créé dans la dernière section.
3.  Cliquez sur le nom du cluster où vous voulez exécuter la tâche Hive
4.  Cliquez sur **GÉRER LE CLUSTER** en bas de page pour ouvrir le tableau de bord du cluster. Une page web s'ouvre dans un autre onglet du navigateur.
5.  Entrez le nom d'utilisateur et le mot de passe du compte d'utilisateur Hadoop. Le nom d'utilisateur par défaut est **admin**, le mot de passe est celui que vous avez entré pendant l'approvisionnement. Le tableau de bord ressemble à ceci :

    ![][2]

**Exécution d'une requête Hive**

1.  Pour créer une table Hive avec un mappage vers la table HBase, entrez le script HiveQL ci-dessous dans la fenêtre de la console Hive et cliquez sur le bouton **SOUMETTRE**. Avant d'exécuter cette instruction, vous devez avoir créé l'exemple de table référencé ici dans HBase en utilisant le shell HBase.

        SET hbase.zookeeper.quorum=zookeepernode0,zookeepernode1,zookeepernode2;  
        CREATE EXTERNAL TABLE hbasesampletable(rowkey STRING, col1 STRING, col2 STRING)
        STORED BY 'org.apache.hadoop.hive.hbase.HBaseStorageHandler'
        WITH SERDEPROPERTIES ('hbase.columns.mapping' = ':key,cf1:col1,cf1:col2')
        TBLPROPERTIES ('hbase.table.name' = 'sampletable');

2.  Pour exécuter une requête Hive sur les données de HBase, entrez le script HiveQL ci-dessous dans la fenêtre de la console Hive et cliquez sur le bouton **SOUMETTRE**.

        SET hbase.zookeeper.quorum=zookeepernode0,zookeepernode1,zookeepernode2;
        SET hive.aux.jars.path=file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hive-hbase-handler-0.12.0.2.0.9.0-1677.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hbase-server-0.96.0.2.0.9.0-1677-hadoop2.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hbase-protocol-0.96.0.2.0.9.0-1677-hadoop2.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/htrace-core-2.01.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/hbase-client-0.96.0.2.0.9.0-1677-hadoop2.jar,file:///C:/Apps/dist/hive-0.12.0.2.0.9.0-1677/lib/guava-12.0.1.jar;
        SELECT count(*) FROM hbasesampletable;

3.  Pour extraire les résultats de la requête Hive, lorsque la tâche est terminée, cliquez sur le lien **Afficher les détails** dans la fenêtre **Session de la tâche**.

Remarque : le lien du shell HBase passe les onglets sur le **Shell HBase**.

**Accès au fichier de sortie**

1.  Dans le tableau de bord du cluster, en haut, cliquez sur **Fichiers**.
2.  Cliquez sur **Templeton-Job-Status**.
3.  Cliquez sur la valeur GUID qui a la dernière heure de modification juste après l'heure de début de la tâche notée précédemment. Notez cette valeur GUID. Vous en aurez besoin dans la prochaine section.
4.  Le fichier **stdout** contient les données dont vous aurez besoin dans la prochaine section. Pour télécharger une copie du fichier de données, au besoin, cliquez sur **stdout**.

## <a name="hbase-powershell"></a>Utilisation des API C\# HBase pour créer une table HBase et en extraire les données

Marlin est une couche fine sur l'API REST, qui est chargée d'interagir avec HBase en utilisant ProtoBuf en C\#. Le projet Marlin doit être téléchargé dans github et conçu pour utiliser le Kit de développement logiciel (SDK) HBase.

1.  Suivez les étapes décrites sous Téléchargement du projet Marlin sur la [page du projet Marlin][]. Décompressez-le dans un répertoire local.

2.  Ouvrez le projet dans Visual Studio. Ouvrez l'Assistant Gérer les packages NuGet par les menus **OUTILS** -\> **Gestionnaire de package de bibliothèque** et sélectionnez **Gérer les packages NuGet pour la solution...**

    ![][3]

3.  Dans la zone de recherche en ligne, en haut à droite, recherchez protobuf-net et installez la version v2.0.0.68. Pour générer le projet Marlin, cliquez avec le bouton droit de la souris sur le projet **Marlin** dans l'**Explorateur de solutions** et sélectionnez **Générer**.

4.  Extrayez le fichier marlin.dll résultant et ajoutez-le à votre projet C\#.

5.  Créez une instance de Marlin en utilisant les informations d'identification du cluster et extrayez la version du cluster :

        var credentials = ClusterCredentials.Create("https://yourclustername.azurehdinsight.net/", "user", "password");
            var marlin = new Marlin(credentials);
        // retrieve the version as a test
        var version = marlin.GetVersion();
        Console.WriteLine(version);

6.  Pour lister les tables dans le cluster, utilisez le code suivant :

        var tables = marlin.ListTables();
        foreach(var tableName in tables.name) 
                Console.WriteLine(tableName);

7.  Pour créer une table HBase, utilisez ce code :

        var schema = new TableSchema();
        schema.name = "sampletable";
        schema.columns.Add(new ColumnSchema() { name = "cf1" });
        schema.columns.Add(new ColumnSchema() { name = "cf2" });
        marlin.CreateTable(schema);

8.  Pour extraire une ligne d'après sa clé, spécifiez le nom de la table et une clé de ligne pour extraire une valeur de ligne.

        var cells = marlin.GetCells("sampletable", "row1");
        var row = cells.rows[0];
            foreach(var val in row.values) 
            {
               Console.WriteLine(Encoding.UTF8.GetString(val.data));
            }

9.  Pour stocker une nouvelle ligne de données, utilisez le code suivant :

        CellSet set = new CellSet();
        CellSet.Row row = new CellSet.Row() { key = BitConverter.GetBytes(1337) };
            var value = new Cell()
                    {
                        column = Encoding.UTF8.GetBytes("cf1:d"),
                        data = Encoding.UTF8.GetBytes("Hello World!")
                    };
            row.values.Add(value);
            set.rows.Add(row);
        marlin.StoreCells("sampletable", set);

## <a name="summary"></a>Résumé

Dans ce didacticiel, vous avez appris à approvisionner un cluster HBase, à créer des tables et à afficher les données de ces tables à partir du shell HBase. Vous avez également appris à utiliser Hive pour interroger les données des tables HBase et à utiliser les API C\# HBase pour créer une table HBase et en extraire les données.

  [Nouveautés des versions de cluster Hadoop fournies par HDInsight]: ../hdinsight-component-versioning/
  [formules d'abonnement]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [offres spéciales membres]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [version d'évaluation gratuite]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Création d'un compte de stockage]: http://azure.microsoft.com/en-us/documentation/articles/storage-create-storage-account/
  [Approvisionnement d'un cluster HBase dans le portail Azure]: #create-hbase-cluster
  [Création d'un exemple de table HBase à partir du shell HBase]: #create-sample-table
  [Utilisation de Hive pour interroger une table HBase]: #hive-query
  [Utilisation des API C\# HBase pour créer une table HBase et en extraire les données]: #hbase-powershell
  [Résumé]: #summary
  [portail de gestion Azure]: https://manage.windowsazure.com/
  []: http://i.imgur.com/PmGynKZ.jpg
  [1]: http://i.imgur.com/ecxbB9K.jpg
  [2]: http://i.imgur.com/tMwXlj9.jpg
  [page du projet Marlin]: https://github.com/thomasjungblut/marlin
  [3]: http://i.imgur.com/hUNoJDJ.jpg
