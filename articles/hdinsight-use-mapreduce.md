<properties urlDisplayName="MapReduce with Hadoop in HDInsight" pageTitle="Utilisation de Hadoop MapReduce dans HDInsight | Azure" metaKeywords="" description="Learn how to use HDInsight to execute a simple Hadoop MapReduce job." metaCanonical="" services="hdinsight" documentationCenter="" title="Use Hadoop MapReduce in HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="11/12/2014" ms.author="jgao" />



# Utilisation de Hadoop MapReduce dans HDInsight

Hadoop MapReduce est une infrastructure logicielle permettant d'écrire des applications traitant d'importantes quantités de données. Dans ce didacticiel, vous allez utiliser Azure PowerShell sur votre poste de travail pour envoyer vers un cluster HDInsight un programme MapReduce qui compte le nombre d'occurrences d'un mot dans un texte. Ce programme de comptage de mots est écrit en Java et est livré avec le cluster HDInsight.


**Configuration requise :**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

- Un cluster HDInsight. Pour des instructions sur les diverses méthodes disponibles pour créer ce type de cluster, consultez la rubrique [Approvisionnement de clusters HDInsight][hdinsight-provision].

- Un poste de travail sur lequel Azure PowerShell est installé et configuré. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][powershell-install-configure].

##Dans ce didacticiel
1. [Présentation du scénario](#scenario)
2. [Exécution de l'exemple avec Azure PowerShell](#run-sample)	
3. [Code Java pour le programme de comptage de mots MapReduce](#java-code)
4. [Étapes suivantes](#next-steps)	

##<a id="scenario"></a>Présentation du scénario

Le schéma suivant illustre le fonctionnement de MapReduce dans le scénario de comptage de mots :

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]



Le résultat de la tâche MapReduce est un ensemble de paires clé-valeur. La clé est une chaîne indiquant un mot et la valeur est un entier indiquant le nombre total d'occurrences de ce mot dans le texte. Ceci se déroule en deux étapes : 

* Le mappeur prend chaque ligne du texte saisi en tant qu'entrée, et la divise en mots. Il émet une paire clé/valeur à chaque occurrence de travail du mot défini suivi par 1. Le résultat est trié avant d'être envoyé au raccord de réduction. 

* Ce dernier calcule la somme du compte de mots, puis émet une seule paire clé/valeur contenant le mot défini, suivi par la somme de ses occurrences.

L'exécution d'une tâche MapReduce nécessite les éléments suivants :

* Un programme MapReduce. Dans ce didacticiel, vous allez utiliser l'exemple de comptage de mots fourni avec les clusters HDInsight, si bien que vous n'avez pas besoin d'écrire le vôtre. Il se situe à l'emplacement suivant : */example/jars/hadoop-examples.jar*. Le nom du fichier est *hadoop-mapreduce-examples.jar* sur les clusters HDInsight version 3.0. Pour savoir comment écrire votre propre tâche MapReduce, consultez la rubrique [Développement de programmes MapReduce en Java pour HDInsight][hdinsight-develop-MapReduce-jobs].
* Un fichier d'entrée. Vous allez utiliser */example/data/gutenberg/davinci.txt* comme fichier d'entrée. Pour plus d'informations sur le téléchargement de fichiers, consultez la rubrique [Téléchargement de données vers HDInsight][hdinsight-upload-data].
* Un dossier de fichiers de sortie. Vous allez utiliser */example/data/WordCountOutput* comme dossier de fichiers de sortie. Si ce dossier n'existe pas, le système le crée. La tâche MapReduce échoue si le dossier existe.  Si vous souhaitez exécuter la tâche MapReduce une deuxième fois, vous devez supprimer le dossier de résultat ou indiquer un autre dossier.

	
##<a id="run-sample"></a>Exécution de l'exemple avec Azure PowerShell

1.	Ouvrez **Azure PowerShell**. Pour savoir comment ouvrir la fenêtre de la console Azure PowerShell, consultez la rubrique [Installation et configuration d'Azure PowerShell][powershell-install-configure].

3. Définissez les deux variables dans les commandes suivantes, puis exécutez-les :
		
		$subscriptionName = "<SubscriptionName>"   # Azure subscription name
		$clusterName = "<ClusterName>"             # HDInsight cluster name
4. Exécutez la commande suivante et entrez les informations de votre compte Azure :

		Add-AzureAccount
		
5. Exécutez les commandes suivantes pour créer une définition de tâche MapReduce :

		# Define the MapReduce job
		$wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput" 

	> [WACOM.NOTE] Le fichier *hadoop-examples.jar* est fourni avec les clusters HDInsight version 2.1. Ce fichier a été renommé *hadoop-mapreduce.jar* sur les clusters HDInsight version 3.0.
	
	Le fichier hadoop-examples.jar est fourni avec la distribution du cluster HDInsight. Il existe deux arguments pour la tâche MapReduce. Le premier est le nom du fichier source ; le deuxième est le chemin d'accès au fichier de sortie. Le fichier source est fourni avec la distribution du cluster HDInsight, tandis que le chemin d'accès au fichier de sortie est créé au moment de l'exécution.

6. Exécutez la commande suivante pour soumettre la tâche MapReduce :

		# Submit the job
		Select-AzureSubscription $subscriptionName
		$wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600  

	En plus de la définition de la tâche MapReduce, fournissez également le nom du cluster HDInsight sur lequel vous souhaitez exécuter la tâche MapReduce, ainsi que les informations d'identification. Start-AzureHDInsightJob est un appel asynchrone. Pour vérifier que la tâche est terminée, utilisez la cmdlet *Wait-AzureHDInsightJob*.

7. Exécutez la commande suivante pour vérifier que la tâche MapReduce est terminée :

		Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

8. Exécutez la commande suivante pour vérifier si l'exécution de la tâche MapReduce génère des erreurs :	
	
		# Get the job output
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 
		
**Extraction des résultats de la tâche MapReduce**

1. Ouvrez **Azure PowerShell**.
2. Exécutez la commande suivante pour remplacer le répertoire par le répertoire racine c:\ :

		cd \

	Le dossier Azure Powershell par défaut est *C:\Windows\System32\WindowsPowerShell\v1.0*. Par défaut, vous ne disposez pas de l'autorisation d'accès en écriture sur ce dossier. Vous devez remplacer le répertoire par le répertoire racine C:\ ou un dossier sur lequel vous disposez d'une autorisation d'accès en écriture.

2. Définissez les trois variables dans les commandes suivantes, puis exécutez-les :

		$subscriptionName = "<SubscriptionName>"       # Azure subscription name
		
		$storageAccountName = "<StorageAccountName>"   # Azure storage account name
		$containerName = "<ContainerName>"			   # Blob storage container name

		Le compte de stockage Azure est celui que vous avez créé plus tôt dans ce didacticiel. Il sert à héberger le conteneur d'objets blob utilisé comme système de fichiers par défaut du cluster HDInsight.  Le conteneur de stockage d'objets blob et le cluster HDInsight partagent généralement le même nom, à moins que vous spécifiiez un nom différent lors de la configuration du cluster.

3. Exécutez les commandes suivantes pour créer un objet de contexte de stockage Azure :
		
		# Select the current subscription
		Select-AzureSubscription $subscriptionName

		# Create the storage account context object
		$storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
		$storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

	La commande *Select-AzureSubscription* sert à définir l'abonnement actif dans le cas où vous disposez de plusieurs abonnements et que l'abonnement par défaut n'est pas celui qui doit être utilisé. 

4. Exécutez la commande suivante pour télécharger la sortie de la tâche MapReduce du conteneur d'objets blob vers la station de travail :

		# Download the job output to the workstation
		Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

	Le dossier */example/data/WordCountOutput* est le dossier spécifié lors de l'exécution de la tâche MapReduce. *part-r-00000* est le nom de fichier par défaut de la sortie de la tâche MapReduce.  Le fichier est téléchargé dans la même structure de dossiers que le dossier local. Par exemple, dans la capture d'écran suivante, le dossier actif est le dossier racine C.  Le fichier est téléchargé dans le dossier *C:\example\data\WordCountOutput\* . 

5. Exécutez la commande suivante pour imprimer le fichier de sortie de la tâche MapReduce :

		cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"


	La tâche MapReduce produit un fichier nommé *part-r-00000* avec les mots et les décomptes.  Le script utilise la commande findstr pour répertorier tous les mots contenant *"there"*.


Notez que les fichiers de résultat d'une tâche MapReduce sont immuables. Donc, si vous réexécutez cet exemple, vous devrez modifier le nom du fichier de résultat.

##<a id="java-code"></a>Code Java pour le programme de comptage de mots MapReduce

Voici le code source du programme Java de comptage de mots MapReduce :
 
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
 


##<a id="nextsteps"></a>Étapes suivantes
Même si MapReduce est doté de puissantes capacités de diagnostic, il peut être assez difficile à maîtriser. Les autres langages tels que Pig et Hive permettent d'utiliser plus facilement les données stockées dans HDInsight. Pour en savoir plus, consultez les articles suivants :

* [Prise en main d'Azure HDInsight][hdinsight-get-started]
* [Développement de programmes MapReduce en Java pour HDInsight][hdinsight-develop-MapReduce-jobs]
* [Développement de programmes MapReduce de diffusion en continu Hadoop en C# pour HDInsight][hdinsight-develop-streaming]
* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
* [Utilisation de Pig avec HDInsight][hdinsight-use-pig] 
* [Exécution des exemples HDInsight][hdinsight-samples]


[hdinsight-upload-data]: ../hdinsight-upload-data/
[hdinsight-get-started]: ../hdinsight-get-started/
[hdinsight-develop-mapreduce-jobs]: ../hdinsight-develop-deploy-java-mapreduce/
[hdinsight-develop-streaming]: ../hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-use-hive]: ../hdinsight-use-hive/
[hdinsight-use-pig]: ../hdinsight-use-pig/
[hdinsight-samples]: ../hdinsight-run-samples/
[hdinsight-provision]: ../hdinsight-provision-clusters/

[powershell-install-configure]: ../install-and-configure-powershell/

[image-hdi-wordcountdiagram]: ./media/hdinsight-get-started/HDI.WordCountDiagram.gif






<!--HONumber=35.1-->
