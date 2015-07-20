<properties
	pageTitle="Exemple MapReduce Hadoop GraySort 10 Go | Microsoft Azure"
	description="Découvrez comment exécuter un programme GraySort généraliste pour de très gros volumes de données, au minimum 100 To, sur Hadoop avec HDInsight et Azure PowerShell."
	editor="cgronlun"
	manager="paulettm"
	services="hdinsight"
	documentationCenter=""
	authors="bradsev"/>

<tags
	ms.service="hdinsight"
	ms.workload="big-data"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="03/30/2015"
	ms.author="bradsev"/>

# Exemple MapReduce Hadoop GraySort 10 Go dans HDInsight

Cette rubrique d’exemple explique comment exécuter un programme MapReduce Hadoop GraySort généraliste sur Azure HDInsight au moyen d’Azure PowerShell. Un GraySort est un tri de benchmark dont la mesure est le taux de tri (To/minute) obtenu lors du tri de très grandes quantités de données, en général au minimum 100 To.

> [AZURE.NOTE]Les étapes décrites dans ce document nécessitent un cluster HDInsight Windows. Pour plus d’informations sur l’exécution de cet exemple et des autres exemples avec des clusters Linux, consultez la page [Exécution des exemples de Hadoop dans HDInsight](hdinsight-hadoop-run-samples-linux.md)

Cet exemple utilise seulement 10 Go de données afin de pouvoir être exécuté relativement rapidement. Il utilise les applications MapReduce développées par Owen O’Malley et Arun Murthy qui ont remporté en 2009 le benchmark de tri de téraoctets (« daytona ») annuel universel avec un taux de 0,578 To/min (100 To en 173 minutes). Pour plus d'informations à ce sujet et sur d'autres benchmarks de tri, consultez le site [Sortbenchmark](http://sortbenchmark.org/).

Cet exemple utilise trois ensembles de programmes MapReduce :

1. **TeraGen** est un programme MapReduce que vous pouvez utiliser pour générer les lignes de données à trier.
2. **TeraSort** échantillonne les données d'entrée et utilise MapReduce pour trier les données en une commande totale. TeraSort est un tri standard de fonctions MapReduce, sauf pour un partitionneur personnalisé qui utilise une liste triée de clés échantillonnées N-1 définissant le groupe de clés pour chaque réduction. Plus particulièrement, toutes les clés semblables à cet échantillon [i-1] <= key < sample[i] sont envoyées pour réduire i. Cela garantit que les sorties de réduction i sont toutes inférieures aux sorties de réduction i+1.
3. **TeraValidate** est un programme MapReduce qui valide le tri global de la sortie. Il crée un mappage par fichier dans le répertoire de sortie et chaque mappage assure que chaque clé est inférieure ou égale à la précédente. La fonction de mappage génère également des enregistrements des première et dernière clés de chaque fichier et la fonction de réduction assure que la première clé du fichier i est supérieure à la dernière clé du fichier i-1. Un problème est signalé comme une sortie de la réduction avec les clés dans le désordre.

Les formats d’entrée et de sortie utilisés par les trois applications lisent et écrivent les fichiers texte dans le bon format. La réplication de la sortie de la réduction est définie sur 1 au lieu de 3 par défaut, car le concours benchmark ne requiert pas la réplication des données de sortie sur plusieurs nœuds.


**Vous apprendrez à effectuer les opérations suivantes :** * Utilisation d’Azure PowerShell pour exécuter une série de programmes MapReduce sur Azure HDInsight. * Identification d’un programme MapReduce écrit en Java.


**Configuration requise :**

- **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](http://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
- **Un cluster HDInsight**. Pour des instructions sur les diverses méthodes disponibles pour créer ce type de cluster, consultez la page [Approvisionnement de clusters HDInsight](hdinsight-provision-clusters.md).
- **Un poste de travail sur lequel est installé Azure PowerShell**. Consultez la page [Installation et utilisation d’Azure PowerShell](http://azure.microsoft.com/documentation/videos/install-and-use-azure-powershell/).



##Exécution de l'exemple avec Azure PowerShell

Trois tâches sont requises par l'exemple, chacune correspondant à un des programmes MapReduce décrits dans l'introduction :

1. Générez les données à trier en exécutant la tâche MapReduce **TeraGen**.
2. Triez les données en exécutant la tâche MapReduce **TeraSort**.
3. Confirmez que les données ont été correctement triées en exécutant la tâche MapReduce **TeraValidate**.


**Exécution du programme TeraGen**

1. Ouvrez Azure PowerShell. Pour savoir comment ouvrir la fenêtre de la console Azure PowerShell, consultez la rubrique [Installation et configuration d’Azure PowerShell][powershell-install-configure].
2. Définissez les deux variables dans les commandes suivantes, puis exécutez-les :

		# Provide the Azure subscription name and the HDInsight cluster name
		$subscriptionName = "myAzureSubscriptionName"
		$clusterName = "myClusterName"

4. Exécutez la commande suivante pour créer une définition de tâche MapReduce :

		# Create a MapReduce job definition for the TeraGen MapReduce program
		$teragen = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-mapreduce-examples.jar" -ClassName "teragen" -Arguments "-Dmapred.map.tasks=50", "100000000", "/example/data/10GB-sort-input"

	L'argument *"-Dmapred.map.tasks=50"* spécifie que 50 mappages sont créés pour exécuter la tâche. L'argument *100000000* spécifie la quantité de données à générer. Le dernier argument, */example/data/10GB-sort-input*, spécifie le répertoire de sortie dans lequel les résultats sont enregistrés (et qui contient les entrées pour l’étape de tri suivante).

5. Exécutez les commandes suivantes pour envoyer la tâche, attendez qu’elle soit terminée, puis imprimez l’erreur standard :

		# Run the TeraGen MapReduce job
		# Wait for the job to finish
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$teragen | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError


**Exécution du programme TeraSort**

1. Ouvrez Azure PowerShell.
2. Définissez les deux variables dans les commandes suivantes, puis exécutez-les :

		# Provide the Azure subscription name and the HDInsight cluster name
		$subscriptionName = "myAzureSubscriptionName"
		$clusterName = "myClusterName"

3. Exécutez la commande suivante pour définir la tâche MapReduce :

		# Create a MapReduce job definition for the TeraSort MapReduce program
		$terasort = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-mapreduce-examples.jar" -ClassName "terasort" -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-input", "/example/data/10GB-sort-output"

	L'argument *"-Dmapred.map.tasks=50"* spécifie que 50 mappages sont créés pour exécuter la tâche. L'argument *100000000* spécifie la quantité de données à générer. Les deux derniers arguments spécifient les répertoires d'entrée et de sortie.

4. Exécutez la commande suivante pour envoyer la tâche, attendez qu’elle soit terminée, puis imprimez l’erreur standard :

		# Run the TeraSort MapReduce job
		# Wait for the job to finish
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$terasort | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError


**Exécution du programme TeraValidate**

1. Ouvrez Azure PowerShell.
2. Définissez les deux variables dans les commandes suivantes, puis exécutez-les :

		# Provide the Azure subscription name and the HDInsight cluster name
		$subscriptionName = "myAzureSubscriptionName"
		$clusterName = "myClusterName"

3. Exécutez la commande suivante pour définir la tâche MapReduce :

		#	Create a MapReduce job definition for the TeraValidate MapReduce program
		$teravalidate = New-AzureHDInsightMapReduceJobDefinition -JarFile "/example/jars/hadoop-mapreduce-examples.jar" -ClassName "teravalidate" -Arguments "-Dmapred.map.tasks=50", "-Dmapred.reduce.tasks=25", "/example/data/10GB-sort-output", "/example/data/10GB-sort-validate"

	L'argument *"-Dmapred.map.tasks=50"* spécifie que 50 mappages sont créés pour exécuter la tâche. L’argument *"-Dmapred.reduce.tasks=25"* spécifie que 25 tâches de réduction sont créées pour exécuter la tâche. Les deux derniers arguments spécifient les répertoires d'entrée et de sortie.


4. Exécutez les commandes suivantes pour envoyer la tâche MapReduce, attendez qu’elle soit terminée, puis imprimez l’erreur standard :

		# Run the TeraSort MapReduce job
		# Wait for the job to finish
		# Print output and standard error file of the MapReduce job
		Select-AzureSubscription $subscriptionName
		$teravalidate | Start-AzureHDInsightJob -Cluster $clustername | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | Get-AzureHDInsightJobOutput -Cluster $clustername -StandardError


##Code Java du programme MapReduce TeraSort

Le code du programme MapReduce TeraSort est présenté pour l’inspection dans cette section.


	/**
	 * Licensed to the Apache Software Foundation (ASF) under one
	 * or more contributor license agreements.  See the NOTICE file
	 * distributed with this work for additional information
	 * regarding copyright ownership.  The ASF licenses this file
	 * to you under the Apache License, Version 2.0 (the
	 * "License"); you may not use this file except in compliance
	 * with the License.  You may obtain a copy of the License at
	 *
	 *     http://www.apache.org/licenses/LICENSE-2.0
	 *
	 * Unless required by applicable law or agreed to in writing, software
	 * distributed under the License is distributed on an "AS IS" BASIS,
	 * WITHOUT WARRANTIES OR CONDITIONS OF ANY KIND, either express or implied.
	 * See the License for the specific language governing permissions and
	 * limitations under the License.
	 */

	package org.apache.hadoop.examples.terasort;

	import java.io.IOException;
	import java.io.PrintStream;
	import java.net.URI;
	import java.util.ArrayList;
	import java.util.List;

	import org.apache.commons.logging.Log;
	import org.apache.commons.logging.LogFactory;
	import org.apache.hadoop.conf.Configured;
	import org.apache.hadoop.filecache.DistributedCache;
	import org.apache.hadoop.fs.FileSystem;
	import org.apache.hadoop.fs.Path;
	import org.apache.hadoop.io.NullWritable;
	import org.apache.hadoop.io.SequenceFile;
	import org.apache.hadoop.io.Text;
	import org.apache.hadoop.mapred.FileOutputFormat;
	import org.apache.hadoop.mapred.JobClient;
	import org.apache.hadoop.mapred.JobConf;
	import org.apache.hadoop.mapred.Partitioner;
	import org.apache.hadoop.util.Tool;
	import org.apache.hadoop.util.ToolRunner;

	/**
	 * Generates the sampled split points, launches the job,
	 * and waits for it to finish.
	 * <p>
	 * To run the program:
	 * <b>bin/hadoop jar hadoop-examples-*.jar terasort in-dir out-dir</b>
	 */

	public class TeraSort extends Configured implements Tool {
	  private static final Log LOG = LogFactory.getLog(TeraSort.class);

	  /**
	   * A partitioner that splits text keys into roughly equal
	   * partitions in a global sorted order.
	   */

	  static class TotalOrderPartitioner implements Partitioner<Text,Text>{
	    private TrieNode trie;
	    private Text[] splitPoints;

	    /**
	     * A generic trie node
	     */
	    static abstract class TrieNode {
	      private int level;
	      TrieNode(int level) {
	        this.level = level;
	      }
	      abstract int findPartition(Text key);
	      abstract void print(PrintStream strm) throws IOException;
	      int getLevel() {
	        return level;
	      }
	    }

	    /**
	     * An inner trie node that contains 256 children based on the next
	     * character.
	     */
	    static class InnerTrieNode extends TrieNode {
	      private TrieNode[] child = new TrieNode[256];

	      InnerTrieNode(int level) {
	        super(level);
	      }
	      int findPartition(Text key) {
	        int level = getLevel();
	        if (key.getLength() <= level) {
	          return child[0].findPartition(key);
	        }
	        return child[key.getBytes()[level]].findPartition(key);
	      }
	      void setChild(int idx, TrieNode child) {
	        this.child[idx] = child;
	      }
	      void print(PrintStream strm) throws IOException {
	        for(int ch=0; ch < 255; ++ch) {
	          for(int i = 0; i < 2*getLevel(); ++i) {
	            strm.print(' ');
	          }
	          strm.print(ch);
	          strm.println(" ->");
	          if (child[ch] != null) {
	            child[ch].print(strm);
	          }
	        }
	      }
	    }

	    /**
	     * A leaf trie node that does string compares to figure out where the given
	     * key belongs between lower..upper.
	     */
	    static class LeafTrieNode extends TrieNode {
	      int lower;
	      int upper;
	      Text[] splitPoints;
	      LeafTrieNode(int level, Text[] splitPoints, int lower, int upper) {
	        super(level);
	        this.splitPoints = splitPoints;
	        this.lower = lower;
	        this.upper = upper;
	      }
	      int findPartition(Text key) {
	        for(int i=lower; i<upper; ++i) {
	          if (splitPoints[i].compareTo(key) >= 0) {
	            return i;
	          }
	        }
	        return upper;
	      }
	      void print(PrintStream strm) throws IOException {
	        for(int i = 0; i < 2*getLevel(); ++i) {
	          strm.print(' ');
	        }
	        strm.print(lower);
	        strm.print(", ");
	        strm.println(upper);
	      }
	    }


	    /**
	     * Read the cut points from the given sequence file.
	     * @param fs the file system
	     * @param p the path to read
	     * @param job the job config
	     * @return the strings to split the partitions on
	     * @throws IOException
	     */
	    private static Text[] readPartitions(FileSystem fs, Path p,
	                                         JobConf job) throws IOException {
	      SequenceFile.Reader reader = new SequenceFile.Reader(fs, p, job);
	      List<Text> parts = new ArrayList<Text>();
	      Text key = new Text();
	      NullWritable value = NullWritable.get();
	      while (reader.next(key, value)) {
	        parts.add(key);
	        key = new Text();
	      }
	      reader.close();
	      return parts.toArray(new Text[parts.size()]);  
	    }

	    /**
	     * Given a sorted set of cut points, build a trie that will find the correct
	     * partition quickly.
	     * @param splits the list of cut points
	     * @param lower the lower bound of partitions 0..numPartitions-1
	     * @param upper the upper bound of partitions 0..numPartitions-1
	     * @param prefix the prefix that we have already checked against
	     * @param maxDepth the maximum depth we will build a trie for
	     * @return the trie node that will divide the splits correctly
	     */
	    private static TrieNode buildTrie(Text[] splits, int lower, int upper,
	                                      Text prefix, int maxDepth) {
	      int depth = prefix.getLength();
	      if (depth >= maxDepth || lower == upper) {
	        return new LeafTrieNode(depth, splits, lower, upper);
	      }
	      InnerTrieNode result = new InnerTrieNode(depth);
	      Text trial = new Text(prefix);
	      // append an extra byte on to the prefix
	      trial.append(new byte[1], 0, 1);
	      int currentBound = lower;
	      for(int ch = 0; ch < 255; ++ch) {
	        trial.getBytes()[depth] = (byte) (ch + 1);
	        lower = currentBound;
	        while (currentBound < upper) {
	          if (splits[currentBound].compareTo(trial) >= 0) {
	            break;
	          }
	          currentBound += 1;
	        }
	        trial.getBytes()[depth] = (byte) ch;
	        result.child[ch] = buildTrie(splits, lower, currentBound, trial,
	                                     maxDepth);
	      }
	      // pick up the rest
	      trial.getBytes()[depth] = 127;
	      result.child[255] = buildTrie(splits, currentBound, upper, trial,
	                                    maxDepth);
	      return result;
	    }

	    public void configure(JobConf job) {
	      try {
	        FileSystem fs = FileSystem.getLocal(job);
	        Path partFile = new Path(TeraInputFormat.PARTITION_FILENAME);
	        splitPoints = readPartitions(fs, partFile, job);
	        trie = buildTrie(splitPoints, 0, splitPoints.length, new Text(), 2);
	      } catch (IOException ie) {
	        throw new IllegalArgumentException("can't read paritions file", ie);
	      }
	    }

	    public TotalOrderPartitioner() {
	    }

	    public int getPartition(Text key, Text value, int numPartitions) {
	      return trie.findPartition(key);
	    }

	  }

	  public int run(String[] args) throws Exception {
	    LOG.info("starting");
	    JobConf job = (JobConf) getConf();
	    Path inputDir = new Path(args[0]);
	    inputDir = inputDir.makeQualified(inputDir.getFileSystem(job));
	    Path partitionFile = new Path(inputDir, TeraInputFormat.PARTITION_FILENAME);
	    URI partitionUri = new URI(partitionFile.toString() +
	                               "#" + TeraInputFormat.PARTITION_FILENAME);
	    TeraInputFormat.setInputPaths(job, new Path(args[0]));
	    FileOutputFormat.setOutputPath(job, new Path(args[1]));
	    job.setJobName("TeraSort");
	    job.setJarByClass(TeraSort.class);
	    job.setOutputKeyClass(Text.class);
	    job.setOutputValueClass(Text.class);
	    job.setInputFormat(TeraInputFormat.class);
	    job.setOutputFormat(TeraOutputFormat.class);
	    job.setPartitionerClass(TotalOrderPartitioner.class);
	    TeraInputFormat.writePartitionFile(job, partitionFile);
	    DistributedCache.addCacheFile(partitionUri, job);
	    DistributedCache.createSymlink(job);
	    job.setInt("dfs.replication", 1);
	    TeraOutputFormat.setFinalSync(job, true);
	    JobClient.runJob(job);
	    LOG.info("done");
	    return 0;
	  }

	  /**
	   * @param args
	   */

	  public static void main(String[] args) throws Exception {
	    int res = ToolRunner.run(new JobConf(), new TeraSort(), args);
	    System.exit(res);
	  }

	}


##Résumé

Cet exemple a montré comment exécuter une série de tâches MapReduce en utilisant Azure HDInsight où la sortie de données d’une tâche devient l’entrée de la tâche suivante dans la série.

##Étapes suivantes

Pour suivre des didacticiels vous présentant l’exécution d’autres exemples et fournissant des instructions sur l’utilisation des tâches Pig, Hive et MapReduce sur Azure HDInsight avec Azure PowerShell, consultez les rubriques suivantes :

* [Prise en main d’Azure HDInsight][hdinsight-get-started]
* [Exemple : Estimateur de la valeur de Pi][hdinsight-sample-pi-estimator]
* [Exemple : Wordcount][hdinsight-sample-wordcount]
* [Exemple : Diffusion en continu en C#][hdinsight-sample-csharp-streaming]
* [Utilisation de Pig avec HDInsight][hdinsight-use-pig]
* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
* [Documentation du Kit de développement logiciel (SDK) Azure HDInsight][hdinsight-sdk-documentation]

[hdinsight-sdk-documentation]: http://msdnstage.redmond.corp.microsoft.com/library/dn479185.aspx


[powershell-install-configure]: ../install-configure-powershell.md

[hdinsight-get-started]: ../hdinsight-get-started.md

[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-sample-10gb-graysort]: hdinsight-sample-10gb-graysort.md
[hdinsight-sample-csharp-streaming]: hdinsight-sample-csharp-streaming.md
[hdinsight-sample-pi-estimator]: hdinsight-sample-pi-estimator.md
[hdinsight-sample-wordcount]: hdinsight-sample-wordcount.md

[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md

<!---HONumber=July15_HO2-->