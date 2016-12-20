---
title: Utiliser les vues Ambari pour travailler avec Hive sur HDInsight (Hadoop) | Microsoft Docs
description: "Découvrez comment utiliser la vue Hive à partir de votre navigateur web pour envoyer des requêtes Hive. La vue Hive fait partie de l’interface utilisateur web Ambari fournie avec votre cluster HDInsight sous Linux."
services: hdinsight
documentationcenter: 
author: Blackmist
manager: jhubbard
editor: cgronlun
tags: azure-portal
ms.assetid: 1abe9104-f4b2-41b9-9161-abbc43de8294
ms.service: hdinsight
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 10/28/2016
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: 4cdc1f0a8958edd23f8df02c4d16d3f60fe648bd


---
# <a name="use-the-hive-view-with-hadoop-in-hdinsight"></a>Utilisez la vue Hive avec Hadoop dans HDInsight
[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Ambari est un utilitaire de gestion et de surveillance fourni avec les clusters HDInsight sous Linux. L’une des fonctionnalités offertes par Ambari est une interface utilisateur web qui peut être utilisée pour exécuter des requêtes Hive. Il s’agit de la **vue Hive**, qui fait partie des vues Ambari fournies avec votre cluster HDInsight.

> [!NOTE]
> Ambari offre de nombreuses fonctionnalités qui ne seront pas traitées dans ce document. Pour plus d’informations, consultez [Gérer des clusters HDInsight à l’aide de l’interface utilisateur web d’Ambari](hdinsight-hadoop-manage-ambari.md).
> 
> 

## <a name="prerequisites"></a>Conditions préalables
* Un cluster HDInsight sous Linux Pour plus d’informations sur la création d’un cluster, consultez [Prise en main de HDInsight sous Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

## <a name="open-the-hive-view"></a>Ouvrir la vue Hive
Pour accéder à Ambari Views à partir du portail Azure, sélectionnez votre cluster HDInsight, puis sélectionnez **Vues Ambari** dans la section **Liens rapides**.

![section liens rapides](./media/hdinsight-hadoop-use-hive-ambari-view/quicklinks.png)

Vous pouvez également accéder directement à Ambari en accédant à https://CLUSTERNAME.azurehdinsight.net dans un navigateur web (où **CLUSTERNAME** est le nom de votre cluster HDInsight), puis sélectionner l’ensemble de carrés dans le menu de la page (en regard du lien **Admin** et du bouton à gauche de la page) pour dresser la liste des vues disponibles. Sélectionnez la vue **Hive**.

![Sélection des vues ambari](./media/hdinsight-hadoop-use-hive-ambari-view/selecthiveview.png).

> [!NOTE]
> En accédant à Ambari, vous êtes invité à vous authentifier sur le site : Saisissez l’administrateur (`admin` par défaut), le nom du compte et le mot de passe que vous avez utilisés lors de la création du cluster.
> 
> 

Une page similaire à celle ci-dessous doit s'afficher :

![Image de la page de vue hive, contenant une section de l’éditeur de requête](./media/hdinsight-hadoop-use-hive-ambari-view/hiveview.png)

## <a name="view-tables"></a>Afficher les tables
Dans la section **Explorateur de bases de données** de la page, sélectionnez l’entrée **par défaut** dans l’onglet **Bases de données**. Cette opération affiche une liste de tables dans la base de données par défaut. Pour un nouveau cluster HDInsight, une seule table doit exister : **hivesampletable**.

![explorateur de base de données avec la base de données par défaut étendue](./media/hdinsight-hadoop-use-hive-ambari-view/databaseexplorer.png)

Étant donné que les nouvelles tables sont ajoutées à travers la procédure décrite dans ce document, vous pouvez utiliser l’icône Actualiser dans l’angle supérieur droit de l’Explorateur de base de données pour actualiser la liste des tables disponibles.

## <a name="a-namehivequeryaquery-editor"></a><a name="hivequery"></a>Éditeur de requête
Pour exécuter une requête Hive sur les données incluses avec le cluster, suivez la procédure décrite ci-dessous à partir de la vue Hive.

1. Dans la section **Éditeur de requêtes** de la page, collez les instructions HiveQL suivantes dans la feuille de calcul :
   
        DROP TABLE log4jLogs;
        CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
        ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
        STORED AS TEXTFILE LOCATION 'wasbs:///example/data/';
        SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;
   
    Ces instructions effectuent les opérations suivantes :
   
   * **DROP TABLE** : supprime la table et le fichier de données, au cas où la table existe déjà.
   * **CREATE EXTERNAL TABLE** : crée une nouvelle table « externe » dans Hive. Les tables externes stockent uniquement la définition de table dans Hive ; les données restent dans l'emplacement d'origine.
   * **ROW FORMAT** : indique à Hive comment les données mises en forme. Dans ce cas, les champs de chaque journal sont séparés par un espace.
   * **STORED AS TEXTFILE LOCATION** : indique à Hive où sont stockées les données (répertoire example/data) et qu'elles sont stockées sous forme de texte.
   * **SELECT** : sélectionne toutes les lignes dont la colonne t4 contient la valeur [ERROR].
     
     > [!NOTE]
     > Les tables externes doivent être utilisées lorsque les données sous-jacentes sont censées être mises à jour par une source externe (comme, par exemple, dans le cas d’un processus de téléchargement automatique des données) ou par une autre opération MapReduce, mais vous souhaitez toujours que les requêtes Hive utilisent les données les plus récentes. La suppression d'une table externe ne supprime *pas* les données, mais seulement la définition de table.
     > 
     > 
2. Utilisez le bouton **Exécuter** au bas de l’éditeur de requête pour démarrer la requête. Le bouton doit s’afficher en orange et indiquer le libellé **Arrêter l’exécution**. Une section de **résultats du processus de requête** doit apparaître en dessous de l’éditeur de requêtes et afficher des informations sur la tâche.
   
   > [!IMPORTANT]
   > Certains navigateurs peuvent ne pas actualiser correctement le fichier journal ou les informations de résultats. Si vous exécutez une tâche et que celle-ci semble s’exécuter indéfiniment sans mettre à jour le journal ou renvoyer des résultats, essayez d’utiliser Mozilla FireFox ou Google Chrome.
   > 
   > 
3. Une fois la requête terminée, la section **Résultats du processus de requête** affiche les résultats de l’opération. Le bouton **Arrêter l’exécution** s’affichera de nouveau en vert avec le libellé **Exécuter**. Les informations suivantes devraient s’afficher dans l’onglet **Résultats** :
   
        sev       cnt
        [ERROR]   3
   
    L’onglet **Journaux** peut être utilisé pour afficher les informations de journalisation créées par la tâche. Vous pouvez les utiliser pour résoudre des problèmes sur une requête.
   
   > [!TIP]
   > Notez la présence de la boîte de dialogue déroulante **Enregistrer les résultats** en haut à gauche de la section **Résultats du processus de requête** ; vous pouvez utiliser ce menu pour télécharger les résultats ou pour les enregistrer dans un stockage HDInsight sous la forme d’un fichier CSV.
   > 
   > 
4. Sélectionnez les quatre premières lignes de cette requête, puis sélectionnez **Exécuter**. Notez qu’aucun résultat n’est renvoyé à la fin de la tâche. En effet, si vous utilisez le bouton **Exécuter** alors que vous avez sélectionné une partie de la requête, seules les instructions sélectionnées seront exécutées. Dans ce cas, la sélection n’inclut pas l’instruction finale qui consiste à extraire des lignes de la table. Si vous sélectionnez uniquement cette ligne et que vous utilisez le bouton **Exécuter**, vous devriez obtenir les résultats attendus.
5. Utilisez le bouton **Nouvelle feuille de calcul** au bas de **l’Éditeur de requête** pour créer une feuille de calcul. Dans la nouvelle feuille de calcul, entrez les instructions HiveQL suivantes :
   
        CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
        INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
   
    Ces instructions effectuent les opérations suivantes :
   
   * **CREATE TABLE IF NOT EXISTS** : crée une table, si elle n'existe pas déjà. Le mot-clé **EXTERNAL** n’étant pas utilisé, il s’agit d’une table interne, stockée dans l’entrepôt de données Hive et gérée intégralement par Hive. Contrairement aux tables externes, la suppression d’une table interne entraîne également la suppression des données sous-jacentes.
   * **STORED AS ORC** : stocke les données dans un format ORC (Optimized Row Columnar). Il s'agit d'un format particulièrement efficace et optimisé pour le stockage de données Hive.
   * **INSERT OVERWRITE ... SELECT** : sélectionne les lignes de la table **log4jLogs** qui contiennent [ERROR], puis insère les données dans la table **errorLogs**.
     
     Utilisez le bouton **Exécuter** pour exécuter cette requête. L’onglet **Résultats** ne contiendra aucune information puisque cette requête ne renvoie aucune ligne. En revanche, l’état **OPÉRATION RÉUSSIE** devrait s’afficher.

### <a name="hive-settings"></a>Paramètres Hive
Sélectionnez l’icône **Paramètres** à droite de l’éditeur.

![icônes](./media/hdinsight-hadoop-use-hive-ambari-view/settings.png)

Les paramètres peuvent être utilisés pour modifier différents paramètres Hive, par exemple, passer du moteur d’exécution Tez (par défaut) à MapReduce pour Hive.

### <a name="visual-explain"></a>Visual Explain
Sélectionnez l’icône **Visual Explain** à droite de l’éditeur.

![icônes](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplainicon.png)

Il s’agit de la vue **Visual Explain** de la requête, qui peut être utile pour comprendre le déroulement de requêtes complexes. Vous pouvez afficher un équivalent textuel de cette vue à l’aide du bouton **Expliquer** de l’éditeur de requête.

![image de visual explain](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplain.png)

### <a name="tez"></a>Tez
Sélectionnez l'icône **Tez** à droite de l'éditeur.

![icônes](./media/hdinsight-hadoop-use-hive-ambari-view/tez.png)

Cette opération affiche le graphe orienté acyclique (DAG) utilisé par Tez pour cette requête, s’il est disponible. Si vous souhaitez afficher le DAG pour les requêtes que vous avez exécutées par le passé ou déboguer le processus Tez, utilisez plutôt la [vue Tez](hdinsight-debug-ambari-tez-view.md) .

### <a name="notifications"></a>Notifications
Sélectionnez l’icône **Notifications** à droite de l’éditeur.

![icônes](./media/hdinsight-hadoop-use-hive-ambari-view/notifications.png)

Les notifications sont des messages qui sont générés lors de l’exécution des requêtes. Par exemple, vous recevez une notification lorsqu’une requête est soumise, ou lorsqu’une erreur se produit.

## <a name="saved-queries"></a>Requêtes enregistrées
1. À partir de l’éditeur de requête, créez une feuille de calcul et entrez la requête suivante :
   
        SELECT * from errorLogs;
   
    Exécutez la requête pour vérifier qu’elle fonctionne. Vous obtenez les résultats suivants :
   
        errorlogs.t1     errorlogs.t2     errorlogs.t3     errorlogs.t4     errorlogs.t5     errorlogs.t6     errorlogs.t7
        2012-02-03     18:35:34     SampleClass0     [ERROR]     incorrect     id     
        2012-02-03     18:55:54     SampleClass1     [ERROR]     incorrect     id     
        2012-02-03     19:25:27     SampleClass4     [ERROR]     incorrect     id
2. Cliquez sur le bouton **Enregistrer sous** au bas de l’éditeur. Nommez cette requête **Errorlogs** et sélectionnez **OK**. Notez que la feuille de calcul prend le nom **Errorlogs**.
3. Sélectionnez l’onglet **Requêtes enregistrées** en haut de la page Vue Hive. Notez que la requête **Errorlogs** est désormais répertoriée comme une requête enregistrée. Elle restera dans la liste jusqu’à ce que vous la supprimiez. Sélectionnez le nom pour ouvrir la requête dans l’éditeur de requête.

## <a name="query-history"></a>Historique des requêtes
Le bouton **Historique** en haut de la vue Hive vous permet d’afficher les requêtes que vous avez exécutées précédemment. Utilisez-le maintenant et sélectionnez certaines des requêtes que vous avez exécutées précédemment. Lorsque vous sélectionnez une requête, elle s’ouvre dans l’éditeur de requête.

## <a name="user-defined-functions-udf"></a>Fonctions définies par l’utilisateur (UDF)
Hive peut également être étendu via des **fonctions définies par l'utilisateur (UDF)**. Une fonction UDF vous permet d'implémenter une fonctionnalité ou une logique qui n'est pas facilement modelée en HiveQL.

Lorsque vous ajoutez une fonction UDF dans le cadre des instructions HiveQL dans votre requête, l’onglet UDF en haut de la vue Hive vous permet de déclarer et d’enregistrer un ensemble d’UDF qui peuvent être utilisées avec **l’éditeur de requête**.

Une fois que vous avez ajouté une fonction UDF à la vue Hive, un bouton **Insérer des fonctions UDF** s’affiche en bas de **l’éditeur de requête**. La sélection de ce bouton affiche une liste déroulante des UDF définies dans la vue Hive. La sélection d’une fonction UDF ajoute des instructions HiveQL à votre requête pour activer l’UDF.

Par exemple, si vous avez défini une fonction UDF avec les propriétés suivantes :

* Nom de ressource : myudfs
* Chemin d’accès à la ressource : wasbs:///myudfs.jar
* Nom de la fonction UDF : myawesomeudf
* Nom de la classe UDF : com.myudfs.Awesome

L’utilisation du bouton **Insérer des fonctions UDF** va afficher une entrée nommée **myudfs**, avec une autre liste déroulante pour chaque fonction UDF définie pour cette ressource. Dans le cas présent, **myawesomeudf**. La sélection de cette entrée va ajouter le code suivant au début de la requête :

    add jar wasbs:///myudfs.jar;

    create temporary function myawesomeudf as 'com.myudfs.Awesome';

Vous pouvez ensuite utiliser la fonction UDF dans votre requête. Par exemple, `SELECT myawesomeudf(name) FROM people;`.

Pour plus d’informations sur l’utilisation des fonctions UDF avec Hive sur HDInsight, consultez les articles suivants :

* [Utilisation de Python avec Hive et Pig dans HDInsight](hdinsight-python.md)
* [Comment ajouter une UDF personnalisée Hive à HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a name="a-idnextstepsanext-steps"></a><a id="nextsteps"></a>Étapes suivantes
Pour obtenir des informations générales sur Hive dans HDInsight.

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)
* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)




<!--HONumber=Nov16_HO3-->


