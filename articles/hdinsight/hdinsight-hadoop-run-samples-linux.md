<properties
    pageTitle="Exécution d’exemples MapReduce Hadoop dans HDInsight basé sur Linux | Microsoft Azure"
    description="Prenez en main les exemples MapReduce à l’aide de HDInsight sur Linux. Utilisez le protocole SSH pour vous connecter au cluster, puis utilisez la commande Hadoop pour exécuter des exemples de tâches."
    services="hdinsight"
    documentationCenter=""
    authors="Blackmist"
    manager="jhubbard"
    editor="cgronlun"
    tags="azure-portal"/>

<tags
    ms.service="hdinsight"
    ms.workload="big-data"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/27/2016"
    ms.author="larryfr"/>





#<a name="run-the-hadoop-samples-in-hdinsight"></a>Exécution des exemples Hadoop dans HDInsight

[AZURE.INCLUDE [samples-selector](../../includes/hdinsight-run-samples-selector.md)]

Les clusters HDInsight sur Linux fournissent un ensemble d’exemples MapReduce pour vous permettre de vous familiariser avec l’exécution de tâches MapReduce Hadoop. Dans ce document, vous allez découvrir les exemples et les procédures disponibles, par le biais de la mise en application de certains d’entre eux.

##<a name="prerequisites"></a>Composants requis

- **Un abonnement Azure**: consultez la page [Obtention d’un essai gratuit Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/)

- **Cluster HDInsight sur Linux**: consultez la rubrique [Prise en main de Hadoop avec Hive dans HDInsight sur Linux](hdinsight-hadoop-linux-tutorial-get-started.md)

- **Client SSH**: pour plus d’informations sur l’utilisation de SSH avec HDInsight, consultez les articles suivants :

    - [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

## <a name="the-samples"></a>Exemples disponibles ##

**Emplacement** : les exemples se trouvent sur le cluster HDInsight sous **/usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar**

**Contenu**: les exemples suivants sont contenus dans cette archive :

- **aggregatewordcount**: programme map/reduce basé sur un agrégat qui compte les mots contenus dans les fichiers d’entrée
- **aggregatewordhist**: programme map/reduce basé sur un agrégat qui calcule l’histogramme des mots contenus dans les fichiers d’entrée
- **bbp**: programme map/reduce qui utilise Bailey-Borwein-Plouffe pour calculer la valeur exacte de Pi
- **dbcount**: exemple de tâche qui compte les journaux d’affichage de pages stockés dans une base de données.
- **distbbp**: programme map/reduce qui utilise une formule de type BBP pour calculer la valeur exacte de Pi
- **grep**: programme map/reduce basé sur un agrégat qui compte les correspondances des expressions régulières dans l’entrée
- **joint**: tâche qui génère des jeux de données à partition égale, triés sur la jointure
- **multifilewc**: tâche qui compte les mots de plusieurs fichiers
- **pentomino**: programme map/reduce de disposition de vignettes permettant de trouver des solutions aux problèmes posés par les pentominos
- **pi**: programme map/reduce qui estime la valeur de Pi à l’aide de la méthode quasi-Monte-Carlo
- **randomtextwriter**: programme map/reduce qui écrit 10 Go de données textuelles aléatoires par nœud
- **randomwriter**: programme map/reduce qui écrit 10 Go de données aléatoires par nœud
- **secondarysort**: exemple définissant un tri secondaire à la réduction
- **sort**: programme map/reduce qui trie les données écrites par l’enregistreur aléatoire
- **sudoku**: solveur de sudoku
- **teragen**: générateur de données pour le programme terasort
- **terasort**: exécution du terasort
- **teravalidate**: vérification des résultats du programme terasort
- **wordcount**: programme map/reduce qui compte les mots contenus dans les fichiers d’entrée
- **wordmean**: programme map/reduce qui compte la longueur moyenne des mots contenus dans les fichiers d’entrée
- **wordmedian**: programme map/reduce qui compte la longueur médiane des mots contenus dans les fichiers d’entrée
- **wordstandarddeviation**: programme map/reduce qui compte l’écart standard de la longueur des mots contenus dans les fichiers d’entrée

**Code source** : pour ces exemples, est inclus dans le cluster HDInsight sous **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples**

> [AZURE.NOTE]  `2.2.4.9-1` constitue la version de la plateforme de données Hortonworks du cluster HDInsight et peut changer au fil de la mise à jour de HDInsight.

## <a name="how-to-run-the-samples"></a>Exécution des exemples ##

1. Connectez-vous à HDInsight à l’aide de SSH comme décrit dans les articles suivants :

    - [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Linux, Unix ou OS X](hdinsight-hadoop-linux-use-ssh-unix.md)

    - [Utilisation de SSH avec Hadoop Linux sur HDInsight à partir de Windows](hdinsight-hadoop-linux-use-ssh-windows.md)

2. À l’invite `username@#######:~$` , utilisez la commande suivante pour répertorier les exemples :

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar

    Cela génère la liste des exemples de la section précédente de ce document.

3. Utilisez la commande suivante pour obtenir de l’aide sur un exemple spécifique. Dans ce cas, l’exemple **wordcount** :

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount

    Le message suivant doit s’afficher :

        Usage: wordcount <in> [<in>...] <out>

    Cela indique que vous pouvez fournir plusieurs chemins d’accès d’entrée pour les documents sources. Le chemin d’accès final correspond à l’emplacement de stockage de la sortie (nombre de mots dans les documents sources).

4. Pour compter tous les mots figurant dans les carnets de Léonard De Vinci qui sont fournis comme exemples de données avec votre cluster, utilisez les éléments suivants :

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/davinciwordcount

    L’entrée de ce travail est lue à partir de **wasbs:///example/data/gutenberg/davinci.txt**.

    La sortie de cet exemple est stockée dans **wasbs:///example/data/davinciwordcount**.

    > [AZURE.NOTE] Comme indiqué dans l’aide de l’exemple wordcount, vous pouvez également spécifier plusieurs fichiers d’entrée. Par exemple, `hadoop jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar wordcount /example/data/gutenberg/davinci.txt /example/data/gutenberg/ulysses.txt /example/data/twowordcount` compte les mots figurant dans les fichiers davinci.txt et ulysses.txt.

5. Une fois la tâche terminée, utilisez la commande suivante pour afficher le résultat généré :

        hdfs dfs -cat /example/data/davinciwordcount/*

    Cela concatène tous les fichiers de sortie générés par la tâche et les affiche. Pour cet exemple de base, il n’existe qu’un seul fichier. Toutefois, dans le cas de plusieurs fichiers, cette commande effectue une itération pour chacun d’eux.

    Le résultat ressemble à ce qui suit :

        zum     1
        zur     1
        zwanzig 1
        zweite  1

    Chaque ligne représente un mot et le nombre d’occurrences dans les données d’entrée.

## <a name="sudoku"></a>sudoku

L’exemple de Sudoku comporte des instructions d’utilisation peu utiles ; « Inclure un puzzle sur la ligne de commande ».

[Sudoku](https://en.wikipedia.org/wiki/Sudoku) est un puzzle logique composé de 3 x 3 grilles. Certaines cellules de la grille comportent des numéros, tandis que d’autres sont vides, l’objectif consiste à résoudre les cellules vides. Le lien ci-dessus comporte davantage d’informations sur le puzzle, mais l’objectif de cet exemple consiste à résoudre les cellules vides. Par conséquent, notre entrée doit être un fichier au format suivant :

- Neuf lignes de neuf colonnes

- Chaque colonne peut contenir un nombre ou `?` (qui indique une cellule vide)

- Les cellules sont séparées par un espace

La façon de construire des puzzles Sudoku est particulière dans la mesure où vous ne pouvez pas répéter un nombre dans une colonne ou une ligne. Il existe un exemple sur le cluster HDInsight qui est construit correctement. Il se trouve sous **/usr/hdp/2.2.4.9-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta** et contient les éléments suivants :

    8 5 ? 3 9 ? ? ? ?
    ? ? 2 ? ? ? ? ? ?
    ? ? 6 ? 1 ? ? ? 2
    ? ? 4 ? ? 3 ? 5 9
    ? ? 8 9 ? 1 4 ? ?
    3 2 ? 4 ? ? 8 ? ?
    9 ? ? ? 8 ? 5 ? ?
    ? ? ? ? ? ? 2 ? ?
    ? ? ? ? 4 5 ? 7 8

> [AZURE.NOTE]  `2.2.4.9-1` du chemin d’accès peut changer au fil des mises à jour du cluster HDInsight.

Pour exécuter cela dans l’exemple de Sudoku, utilisez la commande suivante :

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar sudoku /usr/hdp/2.2.9.1-1/hadoop/src/hadoop-mapreduce-project/hadoop-mapreduce-examples/src/main/java/org/apache/hadoop/examples/dancing/puzzle1.dta

Le contenu doit être identique à ce qui suit :

    8 5 1 3 9 2 6 4 7
    4 3 2 6 7 8 1 9 5
    7 9 6 5 1 4 3 8 2
    6 1 4 8 2 3 7 5 9
    5 7 8 9 6 1 4 2 3
    3 2 9 4 5 7 8 1 6
    9 4 7 2 8 6 5 3 1
    1 8 5 7 3 9 2 6 4
    2 6 3 1 4 5 9 7 8

## <a name="pi-(π)"></a>Pi (π)

L’exemple de Pi utilise une méthode statistique (quasi-Monte-Carlo) pour estimer la valeur de Pi. Des points placés aléatoirement à l’intérieur d’un carré unitaire se retrouvent également à l’intérieur d’un cercle inscrit dans ce carré avec une probabilité égale à l’aire du cercle, Pi/4. La valeur de Pi peut être estimée à partir de la valeur de 4R, où R est le rapport entre le nombre de points situés à l’intérieur du cercle et le nombre total de points situés à l’intérieur du carré. Plus l'échantillon de points est grand, plus l'estimation est précise.

Le mappeur de cet exemple génère un nombre de points de manière aléatoire à l’intérieur d’un carré unitaire, puis compte le nombre de ces points se trouvant à l’intérieur du cercle.

Le réducteur cumule les points comptés par les mappeurs, puis estime la valeur de Pi à partir de la formule 4R, R étant le rapport entre le nombre de points comptés à l’intérieur du cercle et le nombre total de points situés à l’intérieur du carré.

Utilisez la commande suivante pour exécuter cet exemple : Il utilise 16 mappages, avec pour chacun 10 000 000 exemples, pour estimer la valeur de Pi :

    yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar pi 16 10000000

La valeur retournée doit être semblable à **3,14159155000000000000**. Pour référence, voici les 10 chiffres de Pi après la virgule : 3,1415926535.

##<a name="10gb-greysort"></a>Greysort de 10 Go

Un GraySort est un tri de benchmark dont la mesure est le taux de tri (To/minute) obtenu lors du tri de très grandes quantités de données, en général au minimum 100 To.

Cet exemple utilise seulement 10 Go de données afin de pouvoir être exécuté relativement rapidement. Il utilise les applications MapReduce développées par Owen O’Malley et Arun Murthy qui ont remporté en 2009 le benchmark de tri de téraoctets (« daytona ») annuel universel avec un taux de 0,578 To/min (100 To en 173 minutes). Pour plus d'informations à ce sujet et sur d'autres benchmarks de tri, consultez le site [Sortbenchmark](http://sortbenchmark.org/) .

Cet exemple utilise trois ensembles de programmes MapReduce :

- **TeraGen**: programme MapReduce qui génère des lignes de données à trier

- **TeraSort**: échantillonne les données d'entrée et utilise MapReduce pour trier les données en une commande totale.

    TeraSort est un tri standard de fonctions MapReduce, sauf pour un partitionneur personnalisé qui utilise une liste triée de clés échantillonnées N-1 définissant le groupe de clés pour chaque réduction. Plus particulièrement, toutes les clés semblables à cet échantillon [i-1] <= key < sample[i] sont envoyées pour réduire i. Cela garantit que les sorties de réduction i sont toutes inférieures aux sorties de réduction i+1.

- **TeraValidate**: programme MapReduce qui valide le tri global de la sortie

    Il crée un mappage par fichier dans le répertoire de sortie et chaque mappage assure que chaque clé est inférieure ou égale à la précédente. La fonction de mappage génère également des enregistrements des première et dernière clés de chaque fichier et la fonction de réduction assure que la première clé du fichier i est supérieure à la dernière clé du fichier i-1. Un problème est signalé comme une sortie de la réduction avec les clés dans le désordre.

Utilisez les étapes suivantes pour générer des données, trier, puis valider la sortie :

1. Générer 10 Go de données, qui seront stockées dans le stockage du cluster HDInsight par défaut sous **wasbs:///example/data/10GB-sort-input** :

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teragen -Dmapred.map.tasks=50 100000000 /example/data/10GB-sort-input

    `-Dmapred.map.tasks` indique à Hadoop le nombre de tâches de mappage à utiliser pour cette tâche. Les deux derniers paramètres indiquent la tâche pour créer 10 Go de données et pour les stocker sous **wasbs:///example/data/10GB-sort-input**.

2. Exécutez la commande suivante pour trier les données :

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar terasort -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-input /example/data/10GB-sort-output

    `-Dmapred.reduce.tasks` indique à Hadoop le nombre de tâches de réduction à utiliser pour cette tâche. Les deux derniers paramètres sont simplement les emplacements d’entrée et de sortie des données.

3. Pour valider les données générées par le tri, utilisez les éléments suivants :

        yarn jar /usr/hdp/current/hadoop-mapreduce-client/hadoop-mapreduce-examples.jar teravalidate -Dmapred.map.tasks=50 -Dmapred.reduce.tasks=25 /example/data/10GB-sort-output /example/data/10GB-sort-validate

##<a name="next-steps"></a>Étapes suivantes ##

Dans cet article, vous avez appris à exécuter les exemples inclus avec les clusters HDInsight sous Linux. Pour des didacticiels sur l’utilisation de Pig, Hive et MapReduce avec HDInsight, consultez les rubriques suivantes :

* [Utilisation de Pig avec Hadoop sur HDInsight][hdinsight-use-pig]
* [Utilisation de Hive avec Hadoop sur HDInsight][hdinsight-use-hive]
* [Utilisation de MapReduce avec Hadoop sur HDInsight] [hdinsight-use-mapreduce]



[hdinsight-errors]: hdinsight-debug-jobs.md
[hdinsight-use-mapreduce]: hdinsight-use-mapreduce.md
[hdinsight-sdk-documentation]: https://msdn.microsoft.com/library/azure/dn479185.aspx

[hdinsight-submit-jobs]: hdinsight-submit-hadoop-jobs-programmatically.md
[hdinsight-introduction]: hdinsight-hadoop-introduction.md



[hdinsight-samples]: hdinsight-run-samples.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md



<!--HONumber=Oct16_HO2-->


