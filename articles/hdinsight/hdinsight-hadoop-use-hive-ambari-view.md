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
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 02/08/2017
ms.author: larryfr
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 511d6dd1933f44cd0cb5ba800972a7c112a24c04
ms.lasthandoff: 04/12/2017


---
# <a name="use-the-hive-view-with-hadoop-in-hdinsight"></a>Utilisez la vue Hive avec Hadoop dans HDInsight

[!INCLUDE [hive-selector](../../includes/hdinsight-selector-use-hive.md)]

Ambari est un utilitaire de gestion et de surveillance fourni avec les clusters HDInsight sous Linux. L’une des fonctionnalités offertes par Ambari est une interface utilisateur web qui peut être utilisée pour exécuter des requêtes Hive. Il s’agit de la **vue Hive**, qui fait partie des vues Ambari fournies avec votre cluster HDInsight.

> [!NOTE]
> Ambari offre de nombreuses fonctionnalités qui ne sont pas traitées dans ce document. Pour plus d’informations, consultez [Gérer des clusters HDInsight à l’aide de l’interface utilisateur web d’Ambari](hdinsight-hadoop-manage-ambari.md).

## <a name="prerequisites"></a>Conditions préalables

* Un cluster HDInsight sous Linux Pour plus d’informations sur la création d’un cluster, consultez [Prise en main de HDInsight sous Linux](hdinsight-hadoop-linux-tutorial-get-started.md).

> [!IMPORTANT]
> Les étapes décrites dans ce document nécessitent un cluster HDInsight utilisant Linux. Linux est le seul système d’exploitation utilisé sur HDInsight version 3.4 ou supérieure. Pour en savoir plus, consultez le paragraphe [Obsolescence de HDInsight sous Windows](hdinsight-component-versioning.md#hdi-version-33-nearing-deprecation-date).

## <a name="open-the-hive-view"></a>Ouvrir la vue Hive

Pour accéder à Ambari Views à partir du portail Azure, sélectionnez votre cluster HDInsight, puis sélectionnez **Vues Ambari** dans la section **Liens rapides**.

![section liens rapides](./media/hdinsight-hadoop-use-hive-ambari-view/quicklinks.png)

Vous pouvez aussi ouvrir directement Ambari en accédant à https://CLUSTERNAME.azurehdinsight.net dans un navigateur web. Remplacez **CLUSTERNAME** par le nom de votre cluster HDInsight. Sélectionnez l’ensemble de carrés dans le menu de la page en regard du lien **Admin** pour répertorier les vues disponibles. Sélectionnez la vue **Hive**.

![Sélection des vues Ambari](./media/hdinsight-hadoop-use-hive-ambari-view/selecthiveview.png).

> [!NOTE]
> En accédant à Ambari, vous êtes invité à vous authentifier sur le site. Saisissez l’administrateur (`admin` par défaut), le nom du compte et le mot de passe que vous avez utilisés lors de la création du cluster.

Une page similaire à celle ci-dessous doit s'afficher :

![Image de la page de vue hive, contenant une section de l’éditeur de requête](./media/hdinsight-hadoop-use-hive-ambari-view/hiveview.png)

## <a name="view-tables"></a>Afficher les tables
Dans la section **Explorateur de bases de données** de la page, sélectionnez l’entrée **par défaut** dans l’onglet **Bases de données**. Cette opération affiche une liste de tables dans la base de données par défaut. Pour un nouveau cluster HDInsight, une seule table doit exister : **hivesampletable**.

![explorateur de base de données avec la base de données par défaut étendue](./media/hdinsight-hadoop-use-hive-ambari-view/databaseexplorer.png)

Étant donné que les tables sont ajoutées à travers la procédure décrite dans ce document, vous pouvez utiliser l’icône Actualiser dans l’angle supérieur droit de l’Explorateur de base de données pour actualiser la liste.

## <a name="hivequery"></a>Éditeur de requête

Pour exécuter une requête Hive, utilisez les étapes suivantes à partir de l’affichage Hive.

1. Dans la section **Éditeur de requêtes** de la page, collez les instructions HiveQL suivantes dans la feuille de calcul :

    ```hiveql
    DROP TABLE log4jLogs;
    CREATE EXTERNAL TABLE log4jLogs(t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string)
    ROW FORMAT DELIMITED FIELDS TERMINATED BY ' '
    STORED AS TEXTFILE LOCATION '/example/data/';
    SELECT t4 AS sev, COUNT(*) AS cnt FROM log4jLogs WHERE t4 = '[ERROR]' GROUP BY t4;
    ```

    Ces instructions effectuent les opérations suivantes :

   * **DROP TABLE** : supprime la table et le fichier de données, au cas où la table existe déjà.

   * **CREATE EXTERNAL TABLE** : crée une nouvelle table « externe » dans Hive.
   Les tables externes stockent uniquement la définition de table dans Hive. Les données restent à l'emplacement d'origine.

   * **ROW FORMAT** : indique à Hive le mode de formatage des données. Dans ce cas, les champs de chaque journal sont séparés par un espace.

   * **STORED AS TEXTFILE LOCATION** : indique à Hive où sont stockées les données (répertoire example/data) et qu'elles sont stockées sous forme de texte.

   * **SELECT** : sélectionne toutes les lignes dont la colonne t4 contient la valeur [ERROR].

     > [!NOTE]
     > Les tables externes doivent être utilisées lorsque vous vous attendez à ce que les données sous-jacentes soient mises à jour par une source externe, par exemple, par un processus de téléchargement de données automatisé ou une autre opération MapReduce. La suppression d'une table externe ne supprime *pas* les données, mais seulement la définition de la table.

2. Pour démarrer la requête, utilisez le bouton **Exécuter** au bas de l’éditeur de requête. Il devient orange et affiche **Arrêter l’exécution**. Une section de **résultats du processus de requête** doit apparaître en dessous de l’éditeur de requêtes et afficher des informations sur la tâche.

   > [!IMPORTANT]
   > Certains navigateurs peuvent ne pas actualiser correctement le fichier journal ou les informations de résultats. Si vous exécutez une tâche et que celle-ci semble s’exécuter indéfiniment sans mettre à jour le journal ou renvoyer des résultats, essayez d’utiliser Mozilla FireFox ou Google Chrome.

3. Une fois la requête terminée, la section de **résultats du processus de requête** affiche les résultats de l’opération. Le bouton **Arrêter l’exécution** s’affiche de nouveau en vert avec le libellé **Exécuter** à la fin de l’exécution de la requête. Les informations suivantes devraient s’afficher dans l’onglet **Résultats** :

        sev       cnt
        [ERROR]   3

    L’onglet **Journaux** peut être utilisé pour afficher les informations de journalisation créées par la tâche.

   > [!TIP]
   > La boîte de dialogue déroulante **Enregistrer les résultats** dans le coin supérieur gauche de la section **Résultats du processus de requête** vous permet de télécharger ou d’enregistrer les résultats.

4. Sélectionnez les quatre premières lignes de cette requête, puis sélectionnez **Exécuter**. Notez qu’aucun résultat n’est renvoyé à la fin de la tâche. Si vous utilisez le bouton **Exécuter** alors que vous avez sélectionné une partie de la requête, seules les instructions sélectionnées sont exécutées. Dans ce cas, la sélection n’inclut pas l’instruction finale qui consiste à extraire des lignes de la table. Si vous sélectionnez uniquement cette ligne et que vous utilisez le bouton **Exécuter**, vous devriez obtenir les résultats attendus.

5. Pour ajouter une nouvelle feuille de calcul, utilisez le bouton **Nouvelle feuille de calcul** au bas de **l’Éditeur de requête**. Dans la nouvelle feuille de calcul, entrez les instructions HiveQL suivantes :

    ```hiveql
    CREATE TABLE IF NOT EXISTS errorLogs (t1 string, t2 string, t3 string, t4 string, t5 string, t6 string, t7 string) STORED AS ORC;
    INSERT OVERWRITE TABLE errorLogs SELECT t1, t2, t3, t4, t5, t6, t7 FROM log4jLogs WHERE t4 = '[ERROR]';
    ```

  Ces instructions effectuent les opérations suivantes :

   * **CREATE TABLE IF NOT EXISTS** : crée une table, si elle n'existe pas déjà. Étant donné que le mot clé **EXTERNAL** n’est pas utilisé, une table interne est créée. Une table interne est stockée dans l’entrepôt de données Hive et gérée intégralement par Hive. Contrairement aux tables externes, la suppression d’une table interne entraîne également la suppression des données sous-jacentes.

   * **STORED AS ORC** : stocke les données dans un format ORC (Optimized Row Columnar). Il s'agit d'un format particulièrement efficace et optimisé pour le stockage de données Hive.

   * **INSERT OVERWRITE ... SELECT** : sélectionne les lignes de la table **log4jLogs** qui contiennent [ERROR], puis insère les données dans la table **errorLogs**.

     Utilisez le bouton **Exécuter** pour exécuter cette requête. L’onglet **Résultats** ne contient pas d’informations lorsque la requête ne retourne aucune ligne. L’état doit être **SUCCEEDED** une fois la requête terminée.

### <a name="hive-settings"></a>Paramètres Hive

Sélectionnez l’icône **Paramètres** à droite de l’éditeur.

![Icône Paramètres de l’affichage Hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-settings-icon.png)

Les paramètres peuvent être utilisés pour modifier différents paramètres Hive, par exemple, pour passer du moteur d’exécution Tez (par défaut) à MapReduce pour Hive.

### <a name="visualization"></a>Visualisation

Sélectionnez l’icône __Visualisation__ à droite de l’éditeur.

![Icône Visualisation de l’affichage Hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visualization-icon.png)

L’interface de visualisation s’ouvre, et vous pouvez y créer des visualisations de données retournées par la requête. Voici un exemple de visualisation à l’aide des données de la table `hivesampletable` fournie avec HDInsight.

![Exemple de visualisation](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visualization.png)

### <a name="visual-explain"></a>Visual Explain

Sélectionnez l’icône **Visual Explain** à droite de l’éditeur.

![Icône Visual Explain de l’affichage Hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-visual-explain-icon.png)

Il s’agit de la vue **Visual Explain** de la requête, qui peut être utile pour comprendre le déroulement de requêtes complexes. Vous pouvez afficher un équivalent textuel de cette vue à l’aide du bouton **Expliquer** de l’éditeur de requête.

![image de visual explain](./media/hdinsight-hadoop-use-hive-ambari-view/visualexplain.png)

### <a name="tez"></a>Tez

Sélectionnez l'icône **Tez** à droite de l'éditeur.

![Icône Tez de l’affichage Hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-tez-icon.png)

Cette opération affiche le graphe orienté acyclique (DAG) utilisé par Tez pour cette requête, s’il est disponible. Si vous souhaitez afficher le DAG pour les requêtes que vous avez exécutées par le passé ou déboguer le processus Tez, utilisez plutôt la [vue Tez](hdinsight-debug-ambari-tez-view.md) .

### <a name="notifications"></a>Notifications

Sélectionnez l’icône **Notifications** à droite de l’éditeur.

![Icône Notifications de l’affichage Hive](./media/hdinsight-hadoop-use-hive-ambari-view/hive-view-notifications-icon.png)

Les notifications sont des messages qui sont générés lors de l’exécution des requêtes. Par exemple, vous recevez une notification lorsqu’une requête est soumise, ou lorsqu’une erreur se produit.

## <a name="saved-queries"></a>Requêtes enregistrées

1. À partir de l’éditeur de requête, créez une feuille de calcul et entrez la requête suivante :

    ```hiveql
    SELECT * from errorLogs;
    ```

    Exécutez la requête pour vérifier qu’elle fonctionne. Le résultat ressemble à l’exemple qui suit :

        errorlogs.t1     errorlogs.t2     errorlogs.t3     errorlogs.t4     errorlogs.t5     errorlogs.t6     errorlogs.t7
        2012-02-03     18:35:34     SampleClass0     [ERROR]     incorrect     id     
        2012-02-03     18:55:54     SampleClass1     [ERROR]     incorrect     id     
        2012-02-03     19:25:27     SampleClass4     [ERROR]     incorrect     id

2. Cliquez sur le bouton **Enregistrer sous** au bas de l’éditeur. Nommez cette requête **Errorlogs** et sélectionnez **OK**. La feuille de calcul prend le nom **Errorlogs**.

3. Sélectionnez l’onglet **Requêtes enregistrées** en haut de la page Vue Hive. **Errorlogs** est désormais répertoriée comme une requête enregistrée. Elle restera dans la liste jusqu’à ce que vous la supprimiez. Sélectionnez le nom pour ouvrir la requête dans l’éditeur de requête.

## <a name="query-history"></a>Historique des requêtes

Le bouton **Historique** en haut de la vue Hive vous permet d’afficher les requêtes que vous avez exécutées précédemment. Utilisez-le maintenant et sélectionnez certaines des requêtes que vous avez exécutées précédemment. Lorsque vous sélectionnez une requête, elle s’ouvre dans l’éditeur de requête.

## <a name="user-defined-functions-udf"></a>Fonctions définies par l’utilisateur (UDF)

Hive peut également être étendu via des **fonctions définies par l'utilisateur (UDF)**. Une fonction UDF vous permet d'implémenter une fonctionnalité ou une logique qui n'est pas facilement modelée en HiveQL.

L’onglet UDF en haut de l’affichage Hive vous permet de déclarer et d’enregistrer un ensemble d’UDF qui peuvent être utilisées avec **l’éditeur de requête**.

Une fois que vous avez ajouté une fonction UDF à l’affichage Hive, un bouton **Insérer des fonctions UDF** s’affiche au bas de **l’éditeur de requête**. La sélection de ce bouton affiche une liste déroulante des UDF définies dans l’affichage Hive. La sélection d’une fonction UDF ajoute des instructions HiveQL à votre requête pour activer l’UDF.

Par exemple, si vous avez défini une fonction UDF avec les propriétés suivantes :

* Nom de ressource : myudfs

* Chemin d’accès à la ressource : /myudfs.jar

* Nom de la fonction UDF : myawesomeudf

* Nom de la classe UDF : com.myudfs.Awesome

L’utilisation du bouton **Insérer des fonctions UDF** affiche une entrée nommée **myudfs** avec une autre liste déroulante pour chaque fonction UDF définie pour cette ressource. Dans le cas présent, **myawesomeudf**. La sélection de cette entrée ajoute le code suivant au début de la requête :

```hiveql
add jar /myudfs.jar;
create temporary function myawesomeudf as 'com.myudfs.Awesome';
```

Vous pouvez ensuite utiliser la fonction UDF dans votre requête. Par exemple, `SELECT myawesomeudf(name) FROM people;`.

Pour plus d’informations sur l’utilisation des fonctions UDF avec Hive sur HDInsight, consultez les articles suivants :

* [Utilisation de Python avec Hive et Pig dans HDInsight](hdinsight-python.md)
* [Comment ajouter une UDF personnalisée Hive à HDInsight](http://blogs.msdn.com/b/bigdatasupport/archive/2014/01/14/how-to-add-custom-hive-udfs-to-hdinsight.aspx)

## <a id="nextsteps"></a>Étapes suivantes
Pour obtenir des informations générales sur Hive dans HDInsight.

* [Utilisation de Hive avec Hadoop sur HDInsight](hdinsight-use-hive.md)

Pour plus d’informations sur d’autres méthodes de travail avec Hadoop sur HDInsight :

* [Utilisation de Pig avec Hadoop sur HDInsight](hdinsight-use-pig.md)
* [Utilisation de MapReduce avec Hadoop sur HDInsight](hdinsight-use-mapreduce.md)

