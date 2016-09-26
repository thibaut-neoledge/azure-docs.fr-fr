<properties
   pageTitle="MapReduce avec Hadoop sur HDInsight | Microsoft Azure"
   description="Apprenez à exécuter des tâches MapReduce sur Hadoop dans des clusters HDInsight. Vous exécuterez une opération de comptage de mots de base implémentée en tant que tâche MapReduce en Java."
   services="hdinsight"
   documentationCenter=""
   authors="Blackmist"
   manager="jhubbard"
   editor="cgronlun"
	tags="azure-portal"/>

<tags
   ms.service="hdinsight"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="big-data"
   ms.date="08/23/2016"
   ms.author="larryfr"/>

# Utilisation de MapReduce sur Hadoop sur HDInsight

[AZURE.INCLUDE [mapreduce-selector](../../includes/hdinsight-selector-use-mapreduce.md)]

Dans cet article, vous allez apprendre à exécuter des tâches MapReduce sur Hadoop dans des clusters HDInsight. Nous exécutons une opération de comptage de mots de base implémentée en tant que tâche MapReduce en Java.

##<a id="whatis"></a>Qu'est-MapReduce ?

Hadoop MapReduce est une infrastructure logicielle qui permet d'écrire des tâches traitant d'importantes quantités de données. Les données d'entrée sont divisées en blocs indépendants, qui sont ensuite traitées en parallèle sur les nœuds de votre cluster. Une tâche MapReduce se compose de deux fonctions :

* **Mappeur** : il consomme les données d'entrée, les analyse (généralement avec les opérations de tri et de filtre) et émet des tuples (paires clé-valeur)
* **Raccord de réduction** : il consomme les tuples émis par le Mappeur et effectue une opération de synthèse qui crée un résultat plus petit, combiné à partir des données du Mappeur

Un exemple de tâche MapReduce de comptage de mots de base est illustré dans le diagramme suivant :

![HDI.WordCountDiagram][image-hdi-wordcountdiagram]

Le résultat de cette tâche indique le nombre d’occurrences de chaque mot dans le texte qui a été analysé.

* Le mappeur prend chaque ligne du texte saisi en tant qu'entrée, et la divise en mots. Il émet une paire clé/valeur pour chaque occurrence de mot ou si le mot est suivi d’un 1. Le résultat est trié avant d'être envoyé au raccord de réduction.

* Ce dernier calcule la somme du compte de mots, puis émet une seule paire clé/valeur contenant le mot défini, suivi par la somme de ses occurrences.

MapReduce peut être implémenté dans plusieurs langages. Java est l'implémentation la plus courante. Ce langage est utilisé à titre d’exemple dans ce document.

### Diffusion en continu Hadoop

Les langages ou infrastructures basés sur Java et la Machine virtuelle Java (par exemple, Scalding ou en cascade) peuvent être exécutés directement comme une tâche MapReduce, similaire à une application Java. D'autres, tels que c# ou Python ou des exécutables autonomes, doivent utiliser la diffusion en continu Hadoop.

La diffusion en continu Hadoop communique avec le mappeur et le raccord de réduction sur les propriétés STDIN et STDOUT - le mappeur et le raccord de réduction lisent les données ligne par ligne à partir de STDIN et écrivent le résultat dans STDOUT. Chaque ligne lue ou émise par le mappeur et le raccord de réduction doit être au format d'une paire clé/valeur, délimitée par un caractère de tabulation :

    [key]/t[value]

Pour plus d’informations, consultez [Diffusion en continu Hadoop](http://hadoop.apache.org/docs/r1.2.1/streaming.html).

Pour obtenir des exemples d'utilisation de diffusion en continu Hadoop avec HDInsight, consultez les rubriques suivantes :

* [Développement de tâches MapReduce Python](hdinsight-hadoop-streaming-python.md)

##<a id="data"></a>À propos de l'exemple de données

Dans cet exemple, pour l’exemple de données vous allez utiliser les Carnets de Léonard de Vinci, qui sont fournis comme document texte dans votre cluster HDInsight.

L’exemple de données est stocké dans le module de stockage d'objets blob Azure, que HDInsight utilise comme système de fichiers par défaut pour les clusters Hadoop. HDInsight peut accéder aux fichiers stockés dans un stockage d'objets blob à l'aide du préfixe **wasb**. Par exemple, pour accéder au fichier sample.log, vous devez utiliser la syntaxe suivante :

	wasbs:///example/data/gutenberg/davinci.txt

Étant donné que le stockage d’objets blob Azure est le stockage par défaut pour HDInsight, vous pouvez également accéder au fichier en utilisant **/example/data/gutenberg/davinci.txt**.

> [AZURE.NOTE] Dans la syntaxe précédente, **wasbs:///** permet d'accéder à des fichiers stockés dans le conteneur de stockage par défaut de votre cluster HDInsight. Si vous avez indiqué d'autres comptes de stockage lors de l'approvisionnement du cluster et que vous souhaitez accéder aux fichiers qui y sont stockés, vous pouvez accéder aux données en indiquant le nom du conteneur et l'adresse du compte de stockage. Par exemple : **wasbs://mycontainer@mystorage.blob.core.windows.net/example/data/gutenberg/davinci.txt**.

##<a id="job"></a>À propos de l'exemple MapReduce

La tâche MapReduce qui est utilisée dans cet exemple se trouve dans **wasbs://example/jars/hadoop-mapreduce-examples.jar**, et est fourni avec votre cluster HDInsight. Il contient un exemple de comptage de mots que vous exécuterez pour **davinci.txt**.

> [AZURE.NOTE] Dans les clusters HDInsight 2.1, l'emplacement du fichier est **wasbs:///example/jars/hadoop-examples.jar**.

Pour référence, voici le code Java pour la tâche MapReduce de comptage de mots :

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

Pour savoir comment écrire votre propre tâche MapReduce, consultez [Développement de programmes MapReduce en Java pour HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md).

##<a id="run"></a>Exécution de la tâche MapReduce

HDInsight peut exécuter des tâches HiveQL à l’aide de différentes méthodes. Utilisez le tableau suivant pour découvrir la méthode qui vous convient, puis cliquez sur le lien pour obtenir une présentation détaillée.

| **Élément à utiliser...** | **...pour faire cela** | ...avec ce **système d’exploitation cluster** | ...depuis ce **système d’exploitation cluster** |
|:-------------------------------------------------------------------|:--------------------------------------------------------|:------------------------------------------|:-----------------------------------------|
| [SSH](hdinsight-hadoop-use-mapreduce-ssh.md) | Utilisation de la commande Hadoop via **SSH** | Linux | Linux, Unix, Mac OS X ou Windows |
| [Curl](hdinsight-hadoop-use-mapreduce-curl.md) | Envoyer la tâche à distance à l'aide de **REST** | Linux ou Windows | Linux, Unix, Mac OS X ou Windows |
| [Windows PowerShell](hdinsight-hadoop-use-mapreduce-powershell.md) | Envoyer la tâche à distance à l'aide de **Windows PowerShell** | Linux ou Windows | Windows |
| [Bureau à distance](hdinsight-hadoop-use-mapreduce-remote-desktop) | Utilisation de la commande Hadoop via le **bureau à distance** | Windows | Windows |

##<a id="nextsteps"></a>Étapes suivantes

Même si MapReduce est doté de puissantes capacités de diagnostic, il peut être assez difficile à maîtriser. Il existe plusieurs infrastructures Java qui facilitent la définition d’applications MapReduce, ainsi que des technologies telles que Pig et Hive, qui facilitent également l’utilisation des données dans HDInsight. Pour en savoir plus, consultez les articles suivants :

* [Développement de programmes MapReduce en Java pour HDInsight](hdinsight-develop-deploy-java-mapreduce-linux.md)

* [Développement de programmes MapReduce de diffusion en continu Python pour HDInsight](hdinsight-hadoop-streaming-python.md)

* [Développement de tâches MapReduce Scalding avec Apache Hadoop dans HDInsight](hdinsight-hadoop-mapreduce-scalding.md)

* [Utilisation de Hive avec HDInsight][hdinsight-use-hive]

* [Utilisation de Pig avec HDInsight][hdinsight-use-pig]

* [Exécution des exemples HDInsight][hdinsight-samples]


[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-get-started]: hdinsight-hadoop-linux-tutorial-get-started.md
[hdinsight-develop-mapreduce-jobs]: hdinsight-develop-deploy-java-mapreduce-linux.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-samples]: hdinsight-run-samples.md
[hdinsight-provision]: hdinsight-provision-clusters.md

[powershell-install-configure]: ../powershell-install-configure.md

[image-hdi-wordcountdiagram]: ./media/hdinsight-use-mapreduce/HDI.WordCountDiagram.gif

<!---HONumber=AcomDC_0914_2016-->