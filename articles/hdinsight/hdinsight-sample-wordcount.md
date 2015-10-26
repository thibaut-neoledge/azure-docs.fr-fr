<properties
	pageTitle="Exemple de comptage de mots Hadoop MapReduce dans HDInsight | Microsoft Azure"
	description="Exécutez un exemple de comptage de mots MapReduce sur un cluster Hadoop dans HDinsight. Le programme, écrit en Java, compte les occurrences de mots dans un fichier texte."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	tags="azure-portal"
	authors="mumian"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="07/09/2015"
	ms.author="jgao"/>

#Exécution d’un exemple de comptage de mots MapReduce écrit en Java sur un cluster Hadoop dans HDinsight

Ce didacticiel vous explique comment exécuter un exemple de comptage de mots MapReduce sur un cluster Hadoop dans HDInsight. Ce programme est écrit en Java. Il compte les occurrences de mots dans un fichier texte et génère un nouveau fichier texte contenant chaque mot avec la fréquence d'occurrence correspondante. Le fichier de texte analysé dans cet exemple est l'édition des Carnets de Léonard de Vinci en livre électronique par le Project Gutenberg.

> [AZURE.NOTE]Les étapes décrites dans ce document nécessitent un client Windows. Pour savoir comment utiliser l’exemple de comptage de mots à partir d’un client Linux, OS X ou Unix, avec un cluster HDInsight basé sur Linux, consultez la page [Utilisation de MapReduce avec Hadoop sur HDInsight avec SSH](hdinsight-hadoop-use-mapreduce-ssh.md) ou [Exécution à distance des tâches MapReduce avec Hadoop sur HDInsight à l’aide de Curl](hdinsight-hadoop-use-mapreduce-curl.md).

**Vous apprendrez à effectuer les opérations suivantes :**

* utiliser Azure PowerShell pour exécuter un programme MapReduce sur un cluster HDInsight ;
* écrire des programmes MapReduce en Java.


**Conditions préalables** :

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).

- **Un cluster HDInsight**. Pour obtenir des instructions sur les différentes façons de créer ce type de clusters, consultez la page [Prise en main d’Azure HDInsight][hdinsight-get-started] ou [Approvisionnement de clusters HDInsight](hdinsight-provision-clusters.md).

- **Un poste de travail sur lequel est installé Azure PowerShell**. Consultez la page [Installation et utilisation d’Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



## <a id="run-sample"></a>Exécution de l’exemple avec Azure PowerShell</h2>

**Envoi de la tâche MapReduce**

1.	Ouvrez la console **Azure PowerShell**. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][powershell-install-configure].

3. Définissez les deux variables dans les commandes suivantes, puis exécutez-les :

		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name

5. Exécutez la commande suivante pour créer une définition de tâche MapReduce :

		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

	> [AZURE.NOTE]Le fichier *hadoop-Examples.jar* est fourni avec les clusters HDInsight version 2.1. Ce fichier a été renommé *hadoop-mapreduce.jar* sur les clusters HDInsight version 3.0.

	Le fichier hadoop-mapreduce-examples.jar est fourni avec le cluster HDInsight. Il existe deux arguments pour la tâche MapReduce. Le premier est le nom du fichier source ; le deuxième est le chemin d'accès au fichier de sortie. Le fichier source est fourni avec le cluster HDInsight, tandis que le chemin d'accès au fichier de sortie est créé au moment de l'exécution.

6. Exécutez la commande suivante pour soumettre la tâche MapReduce :

		# Submit the job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	En plus de la définition de la tâche MapReduce, fournissez également spécifier le nom du cluster HDInsight sur lequel vous souhaitez exécuter la tâche MapReduce.

8. Exécutez la commande suivante pour vérifier si l'exécution de la tâche MapReduce génère des erreurs  :

		# Get the job output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError

**Extraction des résultats de la tâche MapReduce**

1. Ouvrez la console **Azure PowerShell**.
2. Définissez les trois variables dans les commandes suivantes, puis exécutez-les :

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		$storageAccountName = "<StorageAccountName>"   # Azure storage account name
		$containerName = "<ContainerName>"			   # Blob storage container name

	Le compte de stockage Azure est celui que vous avez créé plus tôt dans ce didacticiel. Il sert à héberger le conteneur d'objets blob utilisé comme système de fichiers par défaut du cluster HDInsight. Le conteneur de stockage d’objets blob Azure et le cluster HDInsight partagent généralement le même nom, à moins que vous spécifiiez un nom différent lors de la configuration du cluster.

3. Exécutez les commandes suivantes pour créer un objet de contexte de stockage Azure :

		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Create the storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	**Select-AzureSubscription** sert à définir l’abonnement actif dans le cas où vous disposez de plusieurs abonnements et que l'abonnement par défaut n'est pas celui qui doit être utilisé.

4. Exécutez la commande suivante pour télécharger la sortie de la tâche MapReduce du conteneur d'objets blob vers la station de travail :

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	Le dossier */example/data/WordCountOutput* est le dossier de résultat spécifié lors de l'exécution de la tâche MapReduce. *part-r-00000* est le nom de fichier par défaut pour le résultat de la tâche MapReduce. Le fichier est téléchargé dans la même structure de dossiers dans le dossier local. Par exemple, dans la capture d'écran suivante, le dossier actif est le dossier racine C. Le fichier est donc téléchargé dans le dossier *C:\\example\\data\\WordCountOutput\*.

5. Exécutez la commande suivante pour imprimer le fichier de sortie de la tâche MapReduce :

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"


	La tâche MapReduce produit un fichier nommé *part-r-00000* avec les mots et les décomptes. Le script utilise la commande **findstr** pour répertorier tous les mots contenant *« there »*.

Le résultat du script WordCount doit s'afficher dans la fenêtre de commande :

![Résultats de la fréquence de mots de l'exemple de décompte de mots MapReduce Hadoop dans HDInsight effectuée dans PowerShell.][image-hdi-sample-wordcount-output]

Notez que les fichiers de résultat d'une tâche MapReduce sont immuables. Donc, si vous réexécutez cet exemple, vous devrez modifier le nom du fichier de résultat.

## <a id="java-code"></a>Code Java du programme WordCount MapReduce</h2>



	package org.apache.hadoop.examples;
	import java.io.IOException;
	import java.util.StringTokenizer;
	import org.apache.hadoop.conf.Configuration;
	import org.apache.hadoop.fs.Path;
	import org.apache.hadoop.io.IntWritable;
	import org.apache.hadoop.io.Text;
	import org.apache.hadoop.mapreduce.Job;
	import org.apache.hadoop.mapreduce.Mapper;
	import org.apache.hadoop.mapreduce.Reducer;
	import org.apache.hadoop.mapreduce.lib.input.FileInputFormat;
	import org.apache.hadoop.mapreduce.lib.output.FileOutputFormat;
	import org.apache.hadoop.util.GenericOptionsParser;

	public class WordCount {

  	public static class TokenizerMapper
       extends Mapper<Object, Text, Text, IntWritable>{

    private final static IntWritable one = new IntWritable(1);
    private Text word = new Text();

    public void map(Object key, Text value, Context context
                    ) throws IOException, InterruptedException {
      StringTokenizer itr = new StringTokenizer(value.toString());
      while (itr.hasMoreTokens()) {
        word.set(itr.nextToken());
        context.write(word, one);
      	}
      }
  	}

  	public static class IntSumReducer
       extends Reducer<Text,IntWritable,Text,IntWritable> {
    private IntWritable result = new IntWritable();

    public void reduce(Text key, Iterable<IntWritable> values,
                       Context context
                       ) throws IOException, InterruptedException {
      int sum = 0;
      for (IntWritable val : values) {
        sum += val.get();
      }
      result.set(sum);
      context.write(key, result);
      }
  	}

  	public static void main(String[] args) throws Exception {
    Configuration conf = new Configuration();
    String[] otherArgs = new GenericOptionsParser(conf, args).getRemainingArgs();
    if (otherArgs.length != 2) {
      System.err.println("Usage: wordcount <in> <out>");
      System.exit(2);
    	}
    Job job = new Job(conf, "word count");
    job.setJarByClass(WordCount.class);
    job.setMapperClass(TokenizerMapper.class);
    job.setCombinerClass(IntSumReducer.class);
    job.setReducerClass(IntSumReducer.class);
    job.setOutputKeyClass(Text.class);
    job.setOutputValueClass(IntWritable.class);
    FileInputFormat.addInputPath(job, new Path(otherArgs[0]));
    FileOutputFormat.setOutputPath(job, new Path(otherArgs[1]));
    System.exit(job.waitForCompletion(true) ? 0 : 1);
  	}
  	}



Dans ce didacticiel, vous avez vu comment exécuter un programme MapReduce qui compte les occurrences de mots dans un fichier texte avec HDInsight et Azure PowerShell.

## <a id="next-steps"></a>Étapes suivantes</h2>

Pour suivre des didacticiels exécutant d'autres exemples et fournissant des instructions sur l'utilisation des tâches Pig, Hive et MapReduce sur Azure HDInsight avec Azure PowerShell, consultez les rubriques suivantes :

* [Premiers pas avec Azure HDInsight][hdinsight-get-started]
* [Exemple : GraySort de 10 Go][hdinsight-sample-10gb-graysort]
* [Exemple : estimateur de la valeur de Pi][hdinsight-sample-pi-estimator]
* [Exemple : Diffusion en continu en C#][hdinsight-sample-cs-streaming]
* [Utilisation de Pig avec HDInsight][hdinsight-use-pig]
* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
* [Documentation du Kit de développement logiciel (SDK) Azure HDInsight][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx

[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-cs-streaming]: hdinsight-sample-csharp-streaming.md


[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-sample-wordcount-output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png

<!---HONumber=Oct15_HO3-->