<properties linkid="manage-services-hdinsight-howto-work-with-the-interactive-console" urlDisplayName="Interactive Console" pageTitle="How to work with the interactive console in HDInsight | Azure" metaKeywords="" description="In this guide, you'll learn how to perform common tasks such as file upload, run jobs, and visualize data using the interactive console for HDInsight Service." metaCanonical="" services="hdinsight" documentationCenter="" title="HDInsight Interactive JavaScript and Hive Consoles" authors="bradsev" solutions="" manager="paulettm" editor="cgronlun" />

Consoles interactives HDInsight pour JavaScript et Hive
=======================================================

Microsoft Azure HDInsight Service est livré avec des consoles interactives pour JavaScript et Hive. Ces consoles offrent une expérience REPL (read-evaluate-print loop) simple et interactive, pendant laquelle les utilisateurs peuvent entrer des expressions, les évaluer, puis interroger et afficher immédiatement les résultats d'une tâche MapReduce. La console JavaScript exécute les instructions Pig Latin. La console Hive évalue les instructions Hive QL (Hive Query Language). Les deux types d'instructions sont compilés dans des programmes MapReduce. Gérer des tâches Hadoop sur ces consoles est bien plus simple que de se connecter à distance au nœud principal du cluster Hadoop et d'utiliser les programmes MapReduce directement.

**Console JavaScript** : interface de commande fournissant une interface fluide vers l'écosystème Hadoop. Une interface fluide utilise une méthode d'instructions de chaînage qui relaie le contexte d'un appel dans une séquence vers l'appel suivant de cette séquence. La console JavaScript fournit :

-   un accès au cluster Hadoop, à ses ressources, ainsi qu'aux commandes HDFS (système de fichiers distribués Hadoop) ;
-   des capacités de gestion et de manipulation des données entrantes et sortantes du cluster Hadoop ;
-   une interface fluide qui évalue les instructions Pig Latin et JavaScript pour définir une série de programmes MapReduce afin de créer des workflows de traitement de données.

**Console Hive** : Hive est une infrastructure d'entrepôt de données, basée sur Hadoop, qui fournit des capacités de gestion, d'interrogation et d'analyse des données. Elle utilise HiveQL, un dialecte SQL, pour interroger des données stockées dans un cluster Hadoop. La console Hive fournit :

-   un accès au cluster Hadoop, à ses ressources, ainsi qu'aux commandes HDFS ;
-   une implémentation de l'infrastructure Hive pouvant exécuter des instructions HiveQL sur un cluster Hadoop ;
-   un modèle de base de données relationnelle pour HDFS qui vous permet d'interagir avec les données stockées dans le système de fichiers distribués comme si ces données étaient stockées dans des tables. La console JavaScript utilise Pig Latin, un langage de flux de données, tandis que la console Hive utilise HiveQL, un langage de requête.

Les utilisateurs habitués à une approche de création de scripts préfèrent généralement Pig (et la console JavaScript), où une séquence de transformations chaînées (ou fluides) permet de définir un workflow de traitement des données. Ceci est aussi un bon choix si vos données ne sont pas du tout structurées.

Les utilisateurs habitués au SQL et à un environnement de base de données relationnelle préfèrent utiliser Hive (et sa console). Le fait d'utiliser une abstraction de schéma et de table dans Hive entraîne une expérience assez similaire à celle d'un système de gestion de base de données relationnelle (ou SGBDR).

Pig et Hive fournissent des niveaux de langage plus élevés qui sont compilés dans des programmes MapReduce écrits en Java et exécutés sur le HDFS. Si vous avez besoin de capacités de contrôle très précises ou de performances élevées, vous devrez écrire directement vos programmes MapReduce.

Dans ce didacticiel
-------------------

-   [Utilisation de la console JavaScript pour exécuter une tâche MapReduce](#runjob)
-   [Utilisation de la console JavaScript pour afficher les résultats sous forme de graphique](#displayresults)
-   [Utilisation de la console Hive pour exporter les résultats dans une table Hive](#createhivetable)
-   [Utilisation de la console Hive pour interroger les données de la table Hive](#queryhivetable)

Utilisation de la console JavaScript pour exécuter une tâche MapReduce
----------------------------------------------------------------------

Dans cette section, vous allez utiliser la console JavaScript pour exécuter l'exemple WordCount livré avec le service HDInsight. La requête JavaScript exécutée ici utilise l'API fluide en couche sur Pig fournie par la console interactive. Le fichier texte analysé ici est un livre électronique téléchargé sur le site Project Gutenberg, intitulé *The Notebooks of Leonardo Da Vinci*. Un filtre est spécifié pour que les résultats de la tâche MapReduce contiennent uniquement les dix mots les plus fréquents.

1.  Connectez-vous au [portail de gestion](https://manage.windowsazure.com).
2.  Cliquez sur **HDINSIGHT**. La liste des clusters Hadoop déployés s'affiche.
3.  Cliquez sur le cluster HDInsight auquel vous souhaitez vous connecter.
4.  Cliquez sur **Gérer le cluster**.
5.  Entrez vos informations d'identification, puis cliquez sur **Se connecter**.
6.  Dans le portail HDInsight, cliquez sur **Exemples**.

    ![HDI.Tiles.Samples](./media/hdinsight-interactive-console/HDI.TileSamples.PNG)

7.  Sur la page **Galerie d'exemples Hadoop**, cliquez sur le titre **WordCount**.
8.  Dans le coin supérieur droit, cliquez sur **WordCount.js**, puis enregistrez le fichier dans un répertoire local, comme par exemple, le dossier ../téléchargements.

    ![HDI.JsConsole.WordCountDownloads](./media/hdinsight-interactive-console/HDI.JsConsole.WordCountDownloads.PNG)

9.  Dans le coin supérieur gauche, cliquez sur **Azure HDInsight** pour revenir à la page du tableau de bord du cluster.
10. Cliquez sur **Cluster interactif** pour afficher la console JavaScript.

    ![HDI.Tiles.InteractiveConsole](./media/hdinsight-interactive-console/HDI.TileInteractiveConsole.PNG)

11. Dans le coin supérieur droit, cliquez sur **JavaScript**.
12. Exécutez la commande suivante :

    fs.put()

13. Entrez les paramètres suivants dans la fenêtre **Télécharger un fichier** :

    -   **Source :** \_..\\téléchargements\\Wordcount.js
    -   **Destination :** ./WordCount.js/

    ![HDI.JsConsole.UploadJs](./media/hdinsight-interactive-console/HDI.JsConsole.UploadJs.PNG)

    Accédez à l'emplacement du fichier WordCount.js. Le chemin d'accès local complet est requis. Le point situé au début du chemin d'accès de destination est requis, car il fait partie de l'adresse relative dans HDFS.

14. Cliquez sur le bouton **Télécharger**.

15. Exécutez les commandes suivantes pour répertorier le fichier et afficher son contenu :

        #ls
        #cat WordCount.js

    ![HDI.JsConsole.JsCode](./media/hdinsight-interactive-console/HDI.JsConsole.JsCode.PNG)

    Notez que la fonction de mappage JavaScript supprime les lettres majuscules du texte en utilisant la méthode "toLowerCase()" avant de compter le nombre d'occurrences d'un mot dans la fonction de réduction.

16. Exécutez la commande suivante pour répertorier le fichier de données qui sera traité par la tâche MapReduce WordCount :

        #ls /example/data/gutenberg

1.  Exécutez la commande suivante pour exécuter le programme MapReduce :

        pig.from("/example/data/gutenberg/davinci.txt").mapReduce("/user/admin/WordCount.js", "word, count:long").orderBy("count DESC").take(10).to("DaVinciTop10Words")

    Remplacez « admin » par le nom d'utilisateur de connexion actif.

    Remarquez que l'opérateur dot « enchaîne » ensemble les instructions et que le fichier de résultat est nommé *DaVinciTop10Words*. Dans la section suivante, vous allez accéder au fichier de résultat.

    Une fois terminé, le code suivant s'affiche :

        pig.from("/example/data/gutenberg/davinci.txt").mapReduce("/user/admin/WordCount.js", "word, count:long").orderBy("count DESC").take(10).to("DaVinciTop10Words")
        2013-04-25 18:54:28,116 [main] INFO  org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - Opération réussie ! (Afficher le journal)

2.  Faites défiler la page vers la droite, puis cliquez sur **Afficher le journal** si vous voulez observer la progression de la tâche. Ce journal fournira également des diagnostics si la tâche échoue. Lorsque la tâche se termine correctement, le message suivant sera affiché à la fin du journal :

        [main] INFO org.apache.pig.backend.hadoop.executionengine.mapReduceLayer.MapReduceLauncher - Opération réussie ! suivi par un lien vers le fichier journal.

3.  Exécutez la commande suivante pour répertorier le fichier de résultat :

        #ls

    Un dossier DavinciTop10Words est créé.

Utilisation de la console JavaScript pour afficher les résultats sous forme de graphique
----------------------------------------------------------------------------------------

Dans la dernière section, vous avez exécuté une tâche MapReduce pour récupérer les 10 mots les plus utilisés dans un fichier texte. Le fichier de résultat est ./DaVinciTop10Words.

1.  Exécutez les commandes suivantes pour afficher les résultats dans le répertoire DaVinciTop10Words :

         file = fs.read("DaVinciTop10Words")

    Le résultat ressemble à :

         js> file=fs.read("DaVinciTop10Words")
         the    22966
         of 11228
         and    8428
         in 5737
         to 5296
         a  4791
         is 4261
         it 3073
         that   2903
         which  2544

2.  Exécutez la commande suivante pour analyser le contenu du fichier dans un fichier de données :

         data = parse(file.data, "word, count:long")

3.  Exécutez la commande suivante pour représenter graphiquement les données :

         graph.bar(data)

    ![HDI.JsConsole.BarGraphTop10Words](./media/hdinsight-interactive-console/HDI.JsConsole.BarGraphTop10Words.PNG)

Utilisation de la console Hive pour exporter les résultats dans une table Hive
------------------------------------------------------------------------------

Cette section présente la console interactive Hive. Vous allez créer une table Hive à partir du résultat de la tâche MapReduce. La prochaine section explique comment interroger les données d'une table.

**Création de la table Hive**

1.  Dans le coin supérieur droit, cliquez sur Hive pour ouvrir la console Hive.

2.  Entrez la commande suivante pour créer une table à deux colonnes nommée *DaVinciWordCountTable* à partir du résultat de l'exemple WordCount enregistré dans le dossier « DaVinciTop10Words » :

         CREATE EXTERNAL TABLE DaVinciWordCountTable     
         (word STRING,
         count INT) 
         ROW FORMAT DELIMITED FIELDS TERMINATED BY '\t' 
         STORED AS TEXTFILE LOCATION '/user/admin/DaVinciTop10Words';   

    Remplacez « admin » par le nom d'utilisateur de connexion.

    Notez que la table est créée en tant que table EXTERNE pour que le dossier ciblé reste indépendant de la table. De même, notez que vous n'avez qu'à indiquer le dossier contenant le fichier de résultat, pas le nom de ce fichier.

3.  Dans le coin inférieur gauche, cliquez sur **ÉVALUER**.

4.  Entrez les commandes suivantes pour confirmer la création de la table à deux colonnes :

         SHOW TABLES;
         DESCRIBE DaVinciWordCountTable;

5.  Cliquez sur **ÉVALUER**.

    ![HDI.Hive.ShowDescribeTable][hdi-hive-showdescribetable]

Utilisation de la console Hive pour interroger les données de la table Hive
---------------------------------------------------------------------------

1.  Exécutez la commande suivante pour demander quels sont les 10 mots les plus utilisés :

         SELECT word, count
         FROM DaVinciWordCountTable
         ORDER BY count DESC LIMIT 10

    Les résultats de cette requête sont :

         SELECT word, count FROM DaVinciWordCountTable ORDER BY count DESC LIMIT 10
         the 22966
         of 11228
         and 8428
         in 5737
         to 5296
         a 4791
         is 4261
         it 3073
         that 2903
         which 2544

Étapes suivantes
----------------

Vous avez vu comment exécuter une tâche Hadoop à partir de la console JavaScript interactive et comment contrôler les résultats à partir d'une tâche en utilisant cette console. Vous avez également vu comment utiliser la console Hive interactive pour contrôler et traiter les résultats d'une tâche Hadoop en créant et en interrogeant une table contenant le résultat d'un programme MapReduce. Vous avez vu des exemple d'instructions Pig Latin et Hive QL utilisées dans les consoles. Enfin, vous avez vu comment la nature de l'expérience REPL interactive des consoles JavaScript et Hive simplifie l'utilisation d'un cluster Hadoop. Pour en savoir plus, consultez les articles suivants :

-   [Utilisation de Pig avec HDInsight](/fr-fr/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Utilisation de Hive avec HDInsight](/fr-fr/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Utilisation de MapReduce avec HDInsight](/fr-fr/manage/services/hdinsight/using-mapreduce-with-hdinsight/)

[hdi-hive-showdescribetable]: ./media/hdinsight-interactive-console/HDI.Hive.ShowDescribeTable.PNG "Hive Table Confirmation")

