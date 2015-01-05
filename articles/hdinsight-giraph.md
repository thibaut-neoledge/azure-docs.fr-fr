<properties title="How to use Giraph with HDInsight" pageTitle="Utilisation d'Apache Giraph avec Azure HDInsight" description="Découvrez comment utiliser Apache Giraph pour traiter des graphiques avec Azure HDInsight" metaKeywords="Azure HDInsight Apache Giraph, hdinsight giraph, hdinsight graph, hadoop giraph, azure hadoop, hadoop graph" services="hdinsight" solutions="big-data" documentationCenter="" authors="larryfr" videoId="" scriptId="" manager="paulettm" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="08/14/2014" ms.author="larryfr" />

#Utilisation de Apache Giraph avec Azure HDInsight (Hadoop)

[Apache Giraph][giraph] permet de traiter des graphiques avec Hadoop et peut être utilisé avec Azure HDInsight. 

Les graphiques modélisent les relations entre les objets, telles que les connexions entre routeurs sur un grand réseau comme Internet ou les relations entre individus sur les réseaux sociaux (parfois appelés graphiques sociaux). Le traitement des graphiques permet d'examiner les relations entre les objets d'un graphique, par exemple :

* identifier les amis potentiels en fonction de vos relations actuelles ;

* identifier le chemin le plus court entre deux ordinateurs d'un réseau ;

* calculer le classement de pages web.

##Vous apprendrez à effectuer les opérations suivantes :

* [Création et déploiement d'Apache Giraph vers un cluster HDInsight](#build)

* [Exécution de l'exemple SimpleShortestPathsComputation](#run)

	Pour obtenir une liste des autres exemples fournis avec Giraph, consultez la section [Package org.apache.giraph.examples](https://giraph.apache.org/apidocs/org/apache/giraph/examples/package-summary.html).

* [Résolution des problèmes éventuels](#tshoot)

##Configuration requise

* Un cluster Azure HDInsight version 3.0 ou 3.1

* [Git](http://git-scm.com/)

* Java 1.6

* [Maven](http://maven.apache.org/) 3 ou ultérieur

##<a id="build"></a>Création et déploiement de Giraph

Giraph n'est pas intégré au cluster HDInsight. Il doit donc être généré à partir de la source.  Vous trouverez plus d'informations sur la génération de Giraph dans le [référentiel Giraph](https://github.com/apache/giraph).

1. À cette date (14/07/2014), Giraph nécessite un correctif pour fonctionner avec le système de stockage de fichiers WASB utilisé par HDInsight. Ce correctif a été soumis au projet Apache Giraph, mais n'a pas encore été accepté. Téléchargez le correctif depuis la section __pièces jointes__ de [GIRAPH-930](https://issues.apache.org/jira/browse/GIRAPH-930) et enregistrez-le sur le lecteur local dans __giraph-930.diff__.

1. À partir d'une ligne de commande, utilisez la commande Git ci-après pour créer un clone du référentiel Giraph.

		git clone https://github.com/apache/giraph.git

2. Modifiez le répertoire par le répertoire __giraph__ créé par l'opération de clonage à l'étape 2.

		cd giraph

3. Fusionnez le correctif au référentiel local à l'aide de la commande ci-après.

		git apply giraph-930.diff

	Remplacez __giraph-930.diff__ par le chemin d'accès du fichier créé à l'étape 1.

3. Générez Giraph pour votre version de cluster HDInsight à l'aide de l'une des commandes suivantes.

	* Pour __HDInsight 3.0__ (Hadoop 2.2)

			mvn package -Phadoop_0.20.203 - DskipTests

	* Pour __HDInsight 3.1__ (Hadoop 2.4)

			mvn package -Phadoop_0.23 -DskipTests

	Une fois la génération terminée, vous trouverez les exemples de fichier JAR à l'adresse __\\giraph\\giraph-examples\\target__.

4. Téléchargez l'exemple de fichier JAR vers le stockage principal de votre cluster HDInsight avec [Azure PowerShell][aps] et [HDInsight-Tools][tools].

		Add-HDInsightFile giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar example/jars/giraph.jar clustername

	Remplacez __giraph-examples-1.1.0-SNAPSHOT-for-hadoop-0.23.1-jar-with-dependencies.jar__ par le chemin d'accès et le nom du fichier JAR produit à l'étape précédente, et __clustername__ par le nom de votre cluster HDInsight. Par exemple, si vous générez le package avec le paramètre -Phadoop_0.20.203, le nom du fichier JAR comprend __hadoop-0.20.203.

	Une fois la commande exécutée, le fichier JAR aura été téléchargé vers wasb:///example/jars/giraph.jar.

	> [WACOM.NOTE] Pour une liste d'utilitaires pouvant servir à télécharger des fichiers vers HDInsight, consultez la page [Téléchargement de données pour les tâches Hadoop dans HDInsight](http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-upload-data/).

##<a id="run"></a>Exécution de l'exemple

SimpleShortestPathsComputation illustre l'implémentation de base de [Pregel](http://people.apache.org/~edwardyoon/documents/pregel.pdf) pour trouver le chemin le plus court entre des objets dans un graphique. Procédez comme suit pour télécharger les exemples de données, exécuter une tâche avec l'exemple SimpleShortestPathsComputation et afficher les résultats.

> [WACOM.NOTE] La source de cet exemple et d'autres exemples est disponibles dans la [branche release-1.1](https://github.com/apache/giraph/tree/release-1.1) du [référentiel GitHub](https://github.com/apache/giraph).

1. Créez un fichier appelé __tiny\_graph.txt__. Il doit contenir les lignes suivantes.

		[0,0,[[1,1],[3,3]]]
		[1,0,[[0,1],[2,2],[3,1]]]
		[2,0,[[1,2],[4,4]]]
		[3,0,[[0,3],[1,1],[4,4]]]
		[4,0,[[3,4],[2,4]]]

	Ces données décrivent une relation entre les objets d'un [graphique dirigé](http://en.wikipedia.org/wiki/Directed_graph) à l'aide du format `[source_id,source_value,[[dest_id], [edge_value],...]]`. Chaque ligne représente une relation entre un objet __source\_id__ et un ou plusieurs objets __dest\_id__. La valeur __edge\_value__ (ou pondération) peut être vue comme la force ou la distance de la connexion entre __source\_id__ et __dest\_id__. 

	Dessinées en utilisant la valeur (ou la pondération) comme la distance entre les objets, les données ci-dessus peuvent ressembler à cela.

	![tiny_graph.txt drawn as circles with lines of varying distance between](.\media\hdinsight-giraph\giraph-graph.png)


2. Téléchargez le fichier __tiny\_graph.txt__ vers le stockage principal de votre cluster HDInsight avec [Azure PowerShell][aps] et [HDInsight-Tools][tools].

		Add-HDInsightFile tiny_graph.txt example/data/tiny_graph.txt clustername

	Remplacez le nom du cluster par le nom de votre cluster HDInsight.

3. Utilisez la commande PowerShell suivante pour exécuter l'exemple __SimpleShortstPathsComputation__ en utilisant __tiny\_graph.txt__ comme fichier d'entrée. Pour cela, il est nécessaire que vous ayez installé et configuré [Azure PowerShell][aps].

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

	Dans l'exemple ci-dessus, remplacez __clustername__ par le nom de votre cluster HDInsight.

###Affichage des résultats

Une fois la tâche terminée, les résultats sont stockés dans le dossier __wasb:///example/out/shotestpaths__ sous forme de fichiers __part-m-#####__. Utilisez [Azure PowerShell][aps] et [HDInsight-Tools][tools] pour télécharger les fichiers de sortie.

	Find-HDInsightFile example/output/shortestpaths/part* clustername | foreach-object { Get-HDInsightFile $_.name .  itsfullofstorage }
	Cat example/output/shortestpaths/part*

Ceci crée la structure de répertoires __example/output/shortestpaths__ dans le répertoire actuel et télécharge les fichiers commençant par __part__. La cmdlet __Cat__ affiche alors le contenu des fichiers, qui doivent avoir l'aspect suivant.

	0	1.0
	4	5.0
	2	2.0
	1	0.0
	3	1.0

L'exemple SimpleShortestPathComputation est codé en dur de façon à commencer avec l'ID d'objet 1 et trouver le chemin le plus court vers les autres objets. Le résultat doit être lu comme " destination_id distance ", où " distance " est la valeur (ou la pondération) des bords parcourus entre l'ID d'objet 1 et l'ID cible.

En visualisant cela, vous pouvez vérifier les résultats en parcourant les chemins les plus courts entre l'ID 1 et tous les autres objets. Notez que le chemin le plus court entre ID 1 et ID 4 est 5. Il s'agit de la distance totale entre <span style="color:orange">ID 1 et 3</span>, puis <span style="color:red">ID 3 et 4</span>.

![Drawing of objects as circles with shortest paths drawn between](.\media\hdinsight-giraph\giraph-graph-out.png)

##<a id="tshoot"></a>Résolution des problèmes

###Le répertoire de sortie existe déjà

Les tâches Giraph créent le répertoire de sortie spécifié au moment de l'exécution. Si le répertoire existe déjà, une erreur se produit indiquant que le répertoire de sortie existe déjà.

Si vous souhaitez exécuter une tâche plusieurs fois, vous devez supprimer le répertoire de sortie entre les tâches ou spécifier un répertoire différent pour chaque tâche.

###<a id="cmd"></a>Utilisation de la ligne de commande Hadoop

Bien que cet article présente l'exécution d'une tâche Giraph via PowerShell, vous pouvez également exécuter cette tâche à partir de la ligne de commande Hadoop.

> [WACOM.NOTE] La ligne de commande Hadoop n'est disponible qu'en cas de connexion au cluster HDInsight avec le Bureau à distance.
> 
> Les sessions Bureau à distance vers les ressources de calcul Azure, telles que le cluster HDInsight, ne peuvent fonctionner qu'à partir de clients Bureau à distance Windows.

Pour vous connecter au cluster HDInsight, procédez comme suit :

1. À l'aide du [portail de gestion Azure](https://manage.windowsazure.com), sélectionnez votre cluster HDInsight, puis __Configuration__.

2. En bas de la page, sélectionnez __Activer Distant__ et fournissez le nom d'utilisateur, le mot de passe et la date d'expiration de la connexion Bureau à distance.

3. Une fois la demande d'activation du Bureau à distance traitée, une nouvelle entrée apparaît dans __Connecter__ en bas de la page. Sélectionnez-la pour télécharger le fichier .RDP pour la session Bureau à distance.

4. Le fichier .RDP peut être enregistré ou ouvert immédiatement pour exécuter le client Bureau à distance. Durant le processus de connexion, il vous sera demandé de fournir le nom d'utilisateur et le mot de passe utilisés lors de l'activation de la connexion Bureau à distance.

5. Une fois la connexion établie, utilisez l'icône de __ligne de commande Hadoop__ du Bureau pour lancer la ligne de commande Hadoop.

6. L'exemple suivant montre comment copier le fichier __giraph.jar__ vers le nœud principal du cluster, puis exécuter la tâche à partir de la ligne de commande Hadoop.

		hadoop fs -copyToLocal wasb:///example/jar/giraph.jar
		hadoop jar giraph.jar org.apache.giraph.GiraphRunner org.apache.giraph.examples.SimpleShortestPathsComputation -ca "mapred.job.tracker=headnodehost:9010" -vif org.apache.giraph.io.formats.JsonLongDoubleFloatDoubleVertexInputFormat -vip wasb:///example/data/tinygraph.txt -vof org.apache.giraph.io.formats.IdWithValueTextOutputFormat -op wasb:///example/output/shortestpaths -w 2


###Versions antérieures de HDInsight

Si vous voulez utiliser Giraph avec une version antérieure de HDInsight, vous devez le compiler pour la version spécifique de Hadoop prise en charge par cette version. Consultez la page [Nouveautés des versions de cluster fournies par HDInsight](http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-component-versioning/) pour déterminer quelle version de Hadoop correspond à votre version de HDInsight.

En outre, selon les versions antérieures de HDInsight, vous devrez peut-être exécuter la tâche Giraph à partir de la ligne de commande. Si vous obtenez des erreurs lors de l'exécution de la tâche à partir de PowerShell, essayez de le faire à partir de la [ligne de commande Hadoop](#cmd).

##Étapes suivantes

Maintenant que vous savez utiliser Giraph avec HDInsight, essayez [Pig][] et [Hive][] avec HDInsight.

[giraph]: http://giraph.apache.org
[tools]: https://github.com/Blackmist/hdinsight-tools
[aps]: http://azure.microsoft.com/fr-fr/documentation/articles/install-configure-powershell/
[pig]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-use-pig/
[hive]: http://azure.microsoft.com/fr-fr/documentation/articles/hdinsight-use-hive/

<!--HONumber=35.1-->
