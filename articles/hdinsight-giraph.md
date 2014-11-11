<properties title="How to use Giraph with HDInsight" pageTitle="How to use Apache Giraph with Azure HDInsight" description="Learn how to use Apache Giraph to perform graph processing with Azure HDInsight" metaKeywords="Azure HDInsight Apache Giraph, hdinsight giraph, hdinsight graph, hadoop giraph, azure hadoop, hadoop graph" services="hdinsight" solutions="big-data" documentationCenter="" authors="larryfr" videoId="" scriptId="" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/14/2014" ms.author="larryfr" />

## Utilisation de Apache Giraph avec Azure HDInsight (Hadoop)

[Apache Giraph][Apache Giraph] permet de traiter des graphiques avec Hadoop et peut être utilisé avec Azure HDInsight.

Les graphiques modélisent les relations entre les objets, telles que les connexions entre routeurs sur un grand réseau comme Internet ou les relations entre individus sur les réseaux sociaux (parfois appelés graphiques sociaux). Le traitement des graphiques permet d'examiner les relations entre les objets d'un graphique, par exemple :

-   identifier les amis potentiels en fonction de vos relations actuelles ;

-   identifier le chemin le plus court entre deux ordinateurs d'un réseau ;

-   calculer le classement de pages web.

## Vous apprendrez à effectuer les opérations suivantes :

-   [Création et déploiement d'Apache Giraph vers un cluster HDInsight][Création et déploiement d'Apache Giraph vers un cluster HDInsight]

-   [Exécution de l'exemple SimpleShortestPathsComputation][Exécution de l'exemple SimpleShortestPathsComputation]

    Pour obtenir une liste d'autres exemples fournis avec Giraph, consultez la section [Package org.apache.giraph.examples][Package org.apache.giraph.examples].

-   [Résolution des problèmes éventuels][Résolution des problèmes éventuels]

## Configuration requise

-   Un cluster Azure HDInsight version 3.0 ou 3.1

-   [Git][Git]

-   Java 1.6

-   [Maven][Maven] 3 ou supérieur

## <span id="build"></span></a>Création et déploiement de Giraph

Giraph n'est pas intégré au cluster HDInsight. Il doit donc être généré à partir de la source. Vous trouverez plus d'informations sur la génération de Giraph sur le [référentiel Giraph][référentiel Giraph].

1.  À cette date (14/07/2014), Giraph nécessite un correctif pour fonctionner avec le système de stockage de fichiers WASB utilisé par HDInsight. Ce correctif a été soumis au projet Apache Giraph, mais n'a pas encore été accepté. Téléchargez-le dans la section **attachments** de [GIRAPH-930][GIRAPH-930] et enregistrez-le dans le lecteur local sous **giraph-930.diff**.

2.  À partir d'une ligne de commande, utilisez la commande Git ci-après pour créer un clone du référentiel Giraph.

        git clone https://github.com/apache/giraph.git

3.  Accédez au répertoire **giraph** créé par l'opération de clonage de l'étape 2.

        cd giraph

4.  Fusionnez le correctif au référentiel local à l'aide de la commande ci-après.

        git apply giraph-930.diff

    Remplacez **giraph-930.diff** par le chemin d'accès au fichier créé à l'étape 1.

5.  Générez Giraph pour votre version de cluster HDInsight à l'aide de l'une des commandes suivantes.

    -   Pour **HDInsight 3.0** (Hadoop 2.2)

            mvn package -Phadoop_0.20.203 - DskipTests

    -   Pour **HDInsight 3.1** (Hadoop 2.4)

            mvn package -Phadoop_0.23 -DskipTests

    Une fois la build terminée, vous trouverez les exemples de fichier JAR à l'adresse **\\giraph\\giraph-examples\\target**.

6.  Téléchargez l'exemple de fichier JAR vers le stockage principal de votre cluster HDInsight avec [Azure PowerShell][Azure PowerShell] et [HDInsight-Tools][HDInsight-Tools].

        Add-HDInsightFile giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar example/jars/giraph.jar clustername

    Remplacez **giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar** par le chemin d'accès et le nom du fichier JAR produit à l'étape précédente et **clustername** par le nom de votre cluster HDInsight. Par exemple, si vous générez le package avec le paramètre `-Phadoop_0.20.203`, le nom du fichier JAR comprend **hadoop-0.20.203**.

    Une fois la commande exécutée, le fichier JAR aura été téléchargé vers wasb:///example/jars/giraph.jar.

    > [WACOM.NOTE] Pour une liste d'utilitaires pouvant servir à télécharger des fichiers vers HDInsight, consultez la page [Téléchargement de données pour les tâches Hadoop dans HDInsight][Téléchargement de données pour les tâches Hadoop dans HDInsight].

## <span id="run"></span></a>Exécution de l'exemple

SimpleShortestPathsComputation montre l'implémentation basique de [Pregel][Pregel] pour trouver le chemin le plus court entre des objets d'un graphique. Procédez comme suit pour télécharger les exemples de données, exécuter une tâche avec l'exemple SimpleShortestPathsComputation et afficher les résultats.

> [WACOM.NOTE] La source de cet exemple et d'autres exemples est disponible dans la [branche de la version 1.1][branche de la version 1.1] du [référentiel GitHub][référentiel Giraph].

1.  Créez un fichier appelé **tiny\_graph.txt**. Il doit contenir les lignes suivantes.

        [0,0,[[1,1],[3,3]]]
        [1,0,[[0,1],[2,2],[3,1]]]
        [2,0,[[1,2],[4,4]]]
        [3,0,[[0,3],[1,1],[4,4]]]
        [4,0,[[3,4],[2,4]]]

    Ces données décrivent une relation entre les objets d'un [graphique dirigé][graphique dirigé] à l'aide du format `[source_id,source_value,[[dest_id], [edge_value],...]]`. Chaque ligne représente une relation entre un objet **source\_id** et un ou plusieurs objets **dest\_id**. La valeur **edge\_value** (ou pondération) peut être vue comme la force ou la distance de la connexion entre **source\_id** et **dest\_id**.

    Dessinées en utilisant la valeur (ou la pondération) comme la distance entre les objets, les données ci-dessus peuvent ressembler à cela.

    ![tiny\_graph.txt drawn as circles with lines of varying distance between][tiny\_graph.txt drawn as circles with lines of varying distance between]

2.  Téléchargez le fichier **tiny\_graph.txt** vers stockage principal de votre cluster HDInsight avec [Azure PowerShell][Azure PowerShell] et [HDInsight-Tools][HDInsight-Tools].

        Add-HDInsightFile tiny_graph.txt example/data/tiny_graph.txt clustername

    Remplacez le nom du cluster par le nom de votre cluster HDInsight.

3.  Utilisez la commande PowerShell suivante pour exécuter l'exemple **SimpleShortstPathsComputation** en utilisant **tiny\_graph.txt** comme fichier d'entrée. Pour cela, il est nécessaire que vous ayez installé et configuré [Azure PowerShell][Azure PowerShell].

        $clusterName = "clustername"
        # Giraph examples JAR
        $jarFile = "wasb:///example/jars/giraph.jar"
        # Arguments for this job
        $jobArguments = "org.apache.giraph.examples.SimpleShortestPathsComputation", `
                        "-ca", "mapred.job.tracker=headnodehost:9010", `
                        "-vif", "org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat", `
                        "-vip", "wasb:///example/data/tinygraph.txt", `
                        "-vof", "org.apache.giraph.io.formats.IdWithValueTextOutputFormat", `
                        "-op",  "wasb:///example/output/shortestpaths", `
                        "-w", "2"
        # Create the definition
        $jobDefinition = New-AzureHDInsightMapReduceJobDefinition `
          -JarFile $jarFile `
          -ClassName "org.apache.giraph.GiraphRunner" `
          -Arguments $jobArguments

        # Run the job, write output to the PowerShell window
        $job = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $jobDefinition
        Write-Host "Wait for the job to complete ..." -ForegroundColor Green
        Wait-AzureHDInsightJob -Job $job
        Write-Host "STDERR"
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardError
        Write-Host "Display the standard output ..." -ForegroundColor Green
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $job.JobId -StandardOutput

    Dans l'exemple ci-dessus, remplacez **clustername** par le nom de votre cluster HDInsight.

### Affichage des résultats

Une fois la tâche terminée, les résultats sont stockés dans le dossier **wasb:///example/out/shotestpaths** sous forme de fichiers **part-m-\#\#\#\#\#**. Utilisez [Azure PowerShell][Azure PowerShell] et [HDInsight-Tools][HDInsight-Tools] pour télécharger les fichiers de sortie.

    Find-HDInsightFile example/output/shortestpaths/part* clustername | foreach-object { Get-HDInsightFile $_.name .  itsfullofstorage }
    Cat example/output/shortestpaths/part*

Cela crée la structure de répertoires **example/output/shortestpaths** dans le répertoire actuel et télécharge les fichiers commençant par **part**. L'applet de commande **Cat** affiche alors le contenu des fichiers, qui doivent apparaître de la façon suivante.

    0   1.0
    4   5.0
    2   2.0
    1   0.0
    3   1.0

L'exemple SimpleShortestPathComputation est codé en dur de façon à commencer avec l'ID d'objet 1 et trouver le chemin le plus court vers les autres objets. Le résultat doit être lu comme `destination_id distance`, où distance est la valeur (ou la pondération) des bords parcourus entre l'ID d'objet 1 et l'ID cible.

En visualisant cela, vous pouvez vérifier les résultats en parcourant les chemins les plus courts entre l'ID 1 et tous les autres objets. Notez que le chemin le plus court entre l'ID 1 et l'ID 4 est 5. Il s'agit de la distance totale entre <span style="color:orange">les ID 1 et 3</span>, puis les <span style="color:red">ID 3 et 4</span>.

![Drawing of objects as circles with shortest paths drawn between][Drawing of objects as circles with shortest paths drawn between]

## <span id="tshoot"></span></a>Résolution des problèmes

### Le répertoire de sortie existe déjà

Les tâches Giraph créent le répertoire de sortie spécifié au moment de l'exécution. Si le répertoire existe déjà, une erreur se produit indiquant que le répertoire de sortie existe déjà.

Si vous souhaitez exécuter une tâche plusieurs fois, vous devez supprimer le répertoire de sortie entre les tâches ou spécifier un répertoire différent pour chaque tâche.

### <span id="cmd"></span></a>Utilisation de la ligne de commande Hadoop

Bien que cet article présente l'exécution d'une tâche Giraph via PowerShell, vous pouvez également exécuter cette tâche à partir de la ligne de commande Hadoop.

> [WACOM.NOTE] La ligne de commande Hadoop n'est disponible qu'en cas de connexion au cluster HDInsight avec le Bureau à distance.
>
> Les sessions Bureau à distance vers les ressources de calcul Azure, telles que le cluster HDInsight, ne peuvent fonctionner qu'à partir de clients Bureau à distance Windows.

Pour vous connecter au cluster HDInsight, procédez comme suit :

1.  À partir du [portail de gestion Azure][portail de gestion Azure], sélectionnez votre cluster HDInsight, puis sélectionnez **Configuration**.

2.  En bas de la page, sélectionnez **Activer Distant** et fournissez le nom d'utilisateur, le mot de passe et la date d'expiration de la connexion Bureau à distance.

3.  Une fois la demande d'activation du Bureau à distance traitée, une nouvelle entrée apparaît dans **Connecter** en bas de la page. Sélectionnez-la pour télécharger le fichier .RDP pour la session Bureau à distance.

4.  Le fichier .RDP peut être enregistré ou ouvert immédiatement pour exécuter le client Bureau à distance. Durant le processus de connexion, il vous sera demandé de fournir le nom d'utilisateur et le mot de passe utilisés lors de l'activation de la connexion Bureau à distance.

5.  Une fois la connexion établie, utilisez l'icône de **ligne de commande Hadoop** du Bureau pour lancer la ligne de commande Hadoop.

6.  L'exemple suivant montre comment copier le fichier **giraph.jar** vers le nœud principal du cluster, puis exécuter la tâche à partir de la ligne de commande Hadoop.

        hadoop fs -copyToLocal wasb:///example/jar/giraph.jar
        hadoop jar giraph.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca "mapred.job.tracker=headnodehost:9010" -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip wasb:///example/data/tinygraph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op wasb:///example/output/shortestpaths -w 2

### Versions antérieures de HDInsight

Si vous voulez utiliser Giraph avec une version antérieure de HDInsight, vous devez le compiler pour la version spécifique de Hadoop prise en charge par cette version. Consultez la page [Nouveautés des versions de cluster HDInsight][Nouveautés des versions de cluster HDInsight] pour déterminer la version de Hadoop correspondant à votre version de HDInsight.

En outre, selon les versions antérieures de HDInsight, vous devrez peut-être exécuter la tâche Giraph à partir de la ligne de commande. Si vous obtenez des erreurs lors de l'exécution de la tâche à partir de PowerShell, essayez de le faire à partir de la [ligne de commande Hadoop][ligne de commande Hadoop].

## Étapes suivantes

Maintenant que vous savez utiliser Giraph avec HDInsight, essayez [Pig][Pig] et [Hive][Hive] avec HDInsight.

  [Apache Giraph]: http://giraph.apache.org
  [Création et déploiement d'Apache Giraph vers un cluster HDInsight]: #build
  [Exécution de l'exemple SimpleShortestPathsComputation]: #run
  [Package org.apache.giraph.examples]: https://giraph.apache.org/apidocs/org/apache/giraph/examples/package-summary.html
  [Résolution des problèmes éventuels]: #tshoot
  [Git]: http://git-scm.com/
  [Maven]: http://maven.apache.org/
  [référentiel Giraph]: https://github.com/apache/giraph
  [GIRAPH-930]: https://issues.apache.org/jira/browse/GIRAPH-930
  [Azure PowerShell]: http://azure.microsoft.com/fr-fr/documentation/articles/install-configure-powershell/
  [HDInsight-Tools]: https://github.com/Blackmist/hdinsight-tools
  [Téléchargement de données pour les tâches Hadoop dans HDInsight]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-upload-data/
  [Pregel]: http://people.apache.org/~edwardyoon/documents/pregel.pdf
  [branche de la version 1.1]: https://github.com/apache/giraph/tree/release-1.1
  [graphique dirigé]: http://en.wikipedia.org/wiki/Directed_graph
  [Drawing of objects as circles with shortest paths drawn between]: .\media\hdinsight-giraph\giraph-graph-out.png
  [portail de gestion Azure]: https://manage.windowsazure.com
  [Nouveautés des versions de cluster HDInsight]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-component-versioning/
  [ligne de commande Hadoop]: #cmd
  [Pig]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-use-pig/
  [Hive]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-use-hive/
