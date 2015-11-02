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
	ms.date="10/15/2015"
	ms.author="jgao"/>

#Exécuter le programme MapReduce de comptage de mots sur un cluster Hadoop dans HDInsight

Découvrez comment exécuter un programme MapReduce sur un cluster Hadoop dans HDInsight à l’aide d’Azure PowerShell. Le programme est écrit en Java. Il compte les occurrences de mots dans un fichier texte et génère un nouveau fichier texte contenant chaque mot avec la fréquence d’occurrence correspondante.

Le programme est installé sur les clusters. Le fichier de texte analysé dans ce tutoriel est l’édition des Carnets de Léonard de Vinci en livre électronique par le Project Gutenberg.

> [AZURE.NOTE]Les étapes décrites dans ce document nécessitent un client Windows. Pour savoir comment utiliser l’exemple de comptage de mots à partir d’un client Linux, OS X ou Unix, avec un cluster HDInsight basé sur Linux, consultez la page [Utilisation de MapReduce avec Hadoop sur HDInsight avec SSH](hdinsight-hadoop-use-mapreduce-ssh.md) ou [Exécution à distance des tâches MapReduce avec Hadoop sur HDInsight à l’aide de Curl](hdinsight-hadoop-use-mapreduce-curl.md).

**Autres articles associés :**

* [Prise en main d’Azure HDInsight][hdinsight-get-started]
* [Développement de programmes MapReduce en Java pour Hadoop dans HDInsight](hdinsight-develop-deploy-java-mapreduce.md)
* [Envoi de tâches Hadoop dans HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Exemple : GraySort de 10 Go][hdinsight-sample-10gb-graysort]
* [Exemple : estimateur de la valeur de Pi][hdinsight-sample-pi-estimator]
* [Exemple : Diffusion en continu en C#][hdinsight-sample-cs-streaming]

**Conditions préalables** :

- **Un cluster HDInsight**. Pour obtenir des instructions sur les différentes façons de créer ce type de clusters, consultez la page [Prise en main d’Azure HDInsight][hdinsight-get-started] ou [Approvisionnement de clusters HDInsight](hdinsight-provision-clusters.md).
- **Un poste de travail sur lequel est installé Azure PowerShell**. Consultez la page [Installation et utilisation d’Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).

## Exécution de l’exemple avec Azure PowerShell

**Envoi de la tâche MapReduce**

1. Ouvrez **Windows PowerShell ISE**. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][powershell-install-configure].
2. Collez le script PowerShell suivant :

		$subscriptionName = "<Azure Subscription Name>"
		$resourceGroupName = "<Resource Group Name>"
		$clusterName = "<HDInsight cluster name>"             # HDInsight cluster name
		
		Select-AzureRmSubscription $subscriptionName
		
		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureRmHDInsightMapReduceJobDefinition `
									-JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" `
									-ClassName "wordcount" `
									-Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput1"
		
		# Submit the job and wait for job completion
		$cred = Get-Credential -Message "Enter the HDInsight cluster HTTP user credential:" 
		$wordCountJob = Start-AzureRmHDInsightJob `
							-ResourceGroupName $resourceGroupName `
							-ClusterName $clusterName `
							-HttpCredential $cred `
							-JobDefinition $wordCountJobDefinition 
		
		Wait-AzureRmHDInsightJob `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-JobId $wordCountJob.JobId 
		
		# Get the job output
		$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
		$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
		$defaultStorageContainer = $cluster.DefaultStorageContainer
		
		Get-AzureRmHDInsightJobOutput `
			-ResourceGroupName $resourceGroupName `
			-ClusterName $clusterName `
			-HttpCredential $cred `
			-DefaultStorageAccountName $defaultStorageAccount `
			-DefaultStorageAccountKey $defaultStorageAccountKey `
			-DefaultContainer $defaultStorageContainer  `
			-JobId $wordCountJob.JobId `
			-DisplayOutputType StandardError

3. Définissez les 3 premières variables, puis exécutez le script.
		
**Pour récupérer les résultats de la tâche MapReduce**

1. Ouvrez **Windows PowerShell ISE**. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][powershell-install-configure].
2. Collez le script PowerShell suivant :

		$subscriptionName = "<Azure Subscription Name>"
		$resourceGroupName = "<Resource Group Name>"
		$clusterName = "<HDInsight cluster name>"             # HDInsight cluster name

		# Select the current subscription
		Select-AzureSubscription $subscriptionName
		
		# Get the cluster properties
		$cluster = Get-AzureRmHDInsightCluster -ResourceGroupName $resourceGroupName -ClusterName $clusterName
		$defaultStorageAccount = $cluster.DefaultStorageAccount -replace '.blob.core.windows.net'
		$defaultStorageAccountKey = Get-AzureRmStorageAccountKey -ResourceGroupName $resourceGroupName -Name $defaultStorageAccount |  %{ $_.Key1 }
		$defaultStorageContainer = $cluster.DefaultStorageContainer
		
		# Download the job output to the workstation
		$storageContext = New-AzureStorageContext -StorageAccountName $defaultStorageAccount -StorageAccountKey $defaultStorageAccountKey 
		Get-AzureStorageBlobContent -Container $defaultStorageContainer -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
		
		# Display the output file
		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

	La tâche MapReduce produit un fichier nommé *part-r-00000* avec les mots et les décomptes. Le script utilise la commande **findstr** pour répertorier tous les mots contenant *« there »*.

Le résultat du script WordCount doit s'afficher dans la fenêtre de commande :

![Résultats de la fréquence de mots de l'exemple de décompte de mots MapReduce Hadoop dans HDInsight effectuée dans PowerShell.][image-hdi-sample-wordcount-output]

Notez que les fichiers de résultat d'une tâche MapReduce sont immuables. Donc, si vous réexécutez cet exemple, vous devrez modifier le nom du fichier de résultat.

##Code source Java

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

## Étapes suivantes

* [Prise en main d’Azure HDInsight][hdinsight-get-started]
* [Développement de programmes MapReduce en Java pour Hadoop dans HDInsight](hdinsight-develop-deploy-java-mapreduce.md)
* [Envoi de tâches Hadoop dans HDInsight](hdinsight-submit-hadoop-jobs-programmatically.md)
* [Exemple : GraySort de 10 Go][hdinsight-sample-10gb-graysort]
* [Exemple : estimateur de la valeur de Pi][hdinsight-sample-pi-estimator]
* [Exemple : Diffusion en continu en C#][hdinsight-sample-cs-streaming]

[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-cs-streaming]: hdinsight-sample-csharp-streaming.md


[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[powershell-install-configure]: ../install-configure-powershell.md

[image-hdi-sample-wordcount-output]: ./media/hdinsight-sample-wordcount/HDI.Sample.WordCount.Output.png

<!---HONumber=Oct15_HO4-->