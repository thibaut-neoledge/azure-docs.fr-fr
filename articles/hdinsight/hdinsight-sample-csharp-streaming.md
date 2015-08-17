<properties
	pageTitle="Exemple Hadoop WordCount (comptage de mots) de diffusion en continu C# | Microsoft Azure"
	description="Comment écrire des programmes MapReduce en C# qui utilisent l’interface de diffusion en continu Hadoop et comment les exécuter sur HDInsight en utilisant des cmdlets PowerShell."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="dotnet"
	ms.topic="article"
	ms.date="07/09/2015" 
	ms.author="jgao"/>

# Exemple MapReduce WordCount (comptage de mots) de diffusion en continu C# dans Hadoop sur HDInsight

Hadoop fournit une API de diffusion en continu pour MapReduce qui vous permet d’écrire des fonctions de mappage et de réduction dans d’autres langages que Java. Ce didacticiel explique comment écrire des programmes MapReduce en C# qui utilise l’interface de diffusion en continu Hadoop et comment exécuter les programmes dans Azure HDInsight à l’aide des cmdlets Azure PowerShell.

> [AZURE.NOTE]Les étapes de ce didacticiel s’appliquent uniquement aux clusters Azure HDInsight Windows. Pour obtenir un exemple de diffusion en continu pour les clusters HDInsight Linux, consultez la rubrique [Développement de programmes de diffusion en continu Python pour HDInsight](hdinsight-hadoop-streaming-python.md).

Dans cet exemple, le mappeur et le raccord de réduction sont des exécutables qui lisent les saisies depuis [stdin][stdin-stdout-stderr] (ligne par ligne), puis émettent leur résultat vers [stdout][stdin-stdout-stderr]. Le programme compte tous les mots dans le texte.

Lorsqu'un exécutable est spécifié pour les **mappeurs**, chaque tâche de mappeur lance l'exécutable en tant que processus distinct lorsque le mappeur est initialisé. Durant son exécution, la tâche du mappeur convertit votre saisie en lignes et les utilise pour alimenter le stdin du processus.

Entre-temps, le mappeur collecte les résultats en forme de lignes depuis le stdout du processus. Il convertit chaque ligne en une paire clé/valeur, qui est collectée en tant que résultat du mappeur. Par défaut, la valeur va du début d’une ligne jusqu’à son premier caractère de tabulation, tandis que la valeur occupe le reste de la ligne (à l’exception du caractère de tabulation). Si la ligne ne contient pas de caractère de tabulation, elle constitue intégralement la clé, tandis que la valeur est nulle.

Lorsqu'un exécutable est spécifié pour les **raccords de réduction**, chaque tâche de raccord de réduction lance l'exécutable en tant que processus distinct lorsque le raccord de réduction est initialisé. Durant son exécution, la tâche du raccord de réduction convertit ses paires clé/valeur en lignes et les utilise pour alimenter le [stdin][stdin-stdout-stderr] du processus.

Entre-temps, le raccord de réduction collecte les résultats en forme de lignes depuis le [stdout][stdin-stdout-stderr] du processus. Il convertit chaque ligne en une paire clé/valeur, qui est collectée en tant que résultat du raccord de réduction. Par défaut, la valeur va du début d’une ligne jusqu’à son premier caractère de tabulation, tandis que la valeur occupe le reste de la ligne (à l’exception du caractère de tabulation).

Pour plus d’informations sur l’interface de diffusion en continu Hadoop, consultez la page [Diffusion Hadoop][hadoop-streaming].

**Ce didacticiel vous apprendra à effectuer les opérations suivantes :**

* utiliser Azure PowerShell pour exécuter un programme de diffusion C# afin d’analyser les données contenues dans un fichier sur HDInsight ;
* écrire du code C# utilisant l’interface de diffusion en continu Hadoop.


**Conditions préalables** :

Avant de commencer la lecture cet article, vous devez disposer des éléments suivants :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Un cluster HDInsight**. Pour des instructions sur les diverses méthodes disponibles pour créer ce type de cluster, consultez la page [Approvisionnement de clusters HDInsight](hdinsight-provision-clusters.md).
- **Un poste de travail sur lequel est installé Azure PowerShell**. Consultez la page [Installation et utilisation d’Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



## <a id="run-sample"></a>Exécution de l’exemple avec Azure PowerShell

**Pour exécuter la tâche MapReduce, procédez comme suit :**

1.	Ouvrez **Azure PowerShell**. Pour savoir comment ouvrir la fenêtre de la console Azure PowerShell, consultez la page [Installation et configuration d’Azure PowerShell][powershell-install-configure].

3. Définissez les deux variables dans les commandes suivantes, puis exécutez-les :

		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name


2. Exécutez la commande suivante pour définir la tâche MapReduce :

		# Create a MapReduce job definition for the streaming job.
		$streamingWC = New-AzureHDInsightStreamingMapReduceJobDefinition -Files "/example/apps/wc.exe", "/example/apps/cat.exe" -InputPath "/example/data/gutenberg/davinci.txt" -OutputPath "/example/data/StreamingOutput/wc.txt" -Mapper "cat.exe" -Reducer "wc.exe"

	Les paramètres indiquent les fonctions de mappeur et de raccord de réduction, ainsi que le fichier d’entrée et les fichiers de sortie.

5. Exécutez les commandes suivantes pour exécuter la tâche MapReduce, attendre la fin de la tâche, puis imprimer le message d’erreur standard :

		# Run the C# Streaming MapReduce job.
		# Wait for the job to complete.
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$streamingWC | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError

6. Exécutez les commandes suivantes pour afficher les résultats du comptage de mots :

		$subscriptionName = "<SubscriptionName>"
		$storageAccountName = "<StorageAccountName>"
		$containerName = "<ContainerName>"

		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Blob storage container and account name
      $storageAccountKey = Get-AzureStorageKey -StorageAccountName $storageAccountName | %{ $\_.Primary } $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey

		# Retrieve the output
		Get-AzureStorageBlobContent -Container $containerName -Blob "example/data/StreamingOutput/wc.txt/part-00000" -Context $storageContext -Force

		# The number of words in the text is:
		cat ./example/data/StreamingOutput/wc.txt/part-00000

	Notez que les fichiers de résultat d'une tâche MapReduce sont immuables. Donc, si vous réexécutez cet exemple, vous devrez modifier le nom du fichier de résultat.


## <a id="java-code"></a>Code C# pour la diffusion en continu Hadoop


Le programme MapReduce utilise l’application cat.exe en tant qu’interface de mappage pour diffuser le texte dans la console et l’application wc.exe en tant qu’interface de réduction pour compter le nombre de mots diffusés à partir d’un document. Le mappeur et le raccord de réduction peuvent tous les deux lire les caractères à partir du flux d’entrée standard (stdin), puis écrire dans le flux de sortie standard (stdout).



	// The source code for the cat.exe (Mapper).

	using System;
	using System.IO;

	namespace cat
	{
	    class cat
	    {
	        static void Main(string[] args)
	        {
	            if (args.Length > 0)
	            {
	                Console.SetIn(new StreamReader(args[0]));
	            }

	            string line;
	            while ((line = Console.ReadLine()) != null)
	            {
	                Console.WriteLine(line);
	            }
	        }
	    }
	}



Le code de mappeur du fichier cat.cs utilise un objet [StreamReader][streamreader] pour lire les caractères du flux entrant dans la console, qui, de son côté, écrit le flux de sortie standard avec la méthode [Console.Writeline][console-writeline] statique.


	// The source code for wc.exe (Reducer) is:

	using System;
	using System.IO;
	using System.Linq;

	namespace wc
	{
	    class wc
	    {
	        static void Main(string[] args)
	        {
	            string line;
	            var count = 0;

	            if (args.Length > 0){
	                Console.SetIn(new StreamReader(args[0]));
	            }

	            while ((line = Console.ReadLine()) != null) {
	                count += line.Count(cr => (cr == ' ' || cr == '\n'));
	            }
	            Console.WriteLine(count);
	        }
	    }
	}


Le code du raccord de réduction du fichier wc.cs utilise un objet [StreamReader][streamreader] pour lire les caractères du flux d'entrée standard écrit par le mappeur cat.exe. Comme il lit les caractères avec la méthode [Console.Writeline][console-writeline], il délimite les mots en se basant sur les espaces et les caractères de fin de ligne situés à la fin de chaque mot. Il écrit ensuite le total dans le flux de sortie standard avec la méthode [Console.Writeline][console-writeline].


## <a id="summary"></a>Résumé

Dans ce didacticiel, vous avez vu comment déployer une tâche MapReduce sur HDInsight avec la diffusion Hadoop.

## <a id="next-steps"></a>Étapes suivantes


Pour suivre des didacticiels exécutant d’autres exemples et fournissant des instructions sur l’utilisation des tâches Pig, Hive et MapReduce sur Azure HDInsight avec Azure PowerShell, consultez les rubriques suivantes :

* [Prise en main d’Azure HDInsight][hdinsight-get-started]
* [Exemple : estimateur de la valeur de Pi][hdinsight-sample-pi-estimator]
* [Exemple : statistiques][hdinsight-sample-wordcount]
* [Exemple : GraySort de 10 Go][hdinsight-sample-10gb-graysort]
* [Utilisation de Pig avec HDInsight][hdinsight-use-pig]
* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
* [Documentation du Kit de développement logiciel (SDK) Azure HDInsight][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[hadoop-streaming]: http://wiki.apache.org/hadoop/HadoopStreaming
[streamreader]: http://msdn.microsoft.com/library/system.io.streamreader.aspx
[console-writeline]: http://msdn.microsoft.com/library/system.console.writeline
[stdin-stdout-stderr]: http://msdn.microsoft.com/library/3x292kth(v=vs.110).aspx

[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
 

<!---HONumber=August15_HO6-->