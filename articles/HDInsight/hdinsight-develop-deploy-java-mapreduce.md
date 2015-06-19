<properties 
	pageTitle="Développement de programmes MapReduce en Java pour Hadoop | Microsoft Azure" 
	description="Découvrez la façon de développer des programmes MapReduce en Java dans l'émulateur HDInsight et de les déployer dans HDInsight." 
	services="hdinsight" 
	editor="cgronlun" 
	manager="paulettm" 
	authors="nitinme" 
	documentationCenter=""/>

<tags 
	ms.service="hdinsight" 
	ms.workload="big-data" 
	ms.tgt_pltfrm="na" 
	ms.devlang="Java" 
	ms.topic="article" 
	ms.date="04/01/2015" 
	ms.author="nitinme"/>

# Développement de programmes MapReduce en Java pour Hadoop dans HDInsight
Ce didacticiel vous accompagne tout au long d’un scénario de bout en bout permettant de développer une tâche MapReduce Hadoop de comptage de mots en Java au moyen d’Apache Maven. Il vous explique également comment tester l’application dans l’émulateur HDInsight pour Azure, puis comment la déployer et l’exécuter sur un cluster HDInsight Azure.

##<a name="prerequisites"></a>Configuration requise

Avant de commencer ce didacticiel, vous devez avoir effectué les tâches suivantes :

- Installation de l’émulateur HDInsight Pour obtenir des instructions, consultez la page [Prise en main de l'émulateur HDInsight][hdinsight-emulator]. Vérifiez que tous les services requis sont en cours d’exécution. Sur l’ordinateur où est installé l’émulateur HDInsight, lancez la ligne de commande Hadoop à partir du raccourci sur le bureau, naviguez jusqu’à **C:\\hdp**, puis exécutez la commande **start_local_hdp_services.cmd**.	
- Azure PowerShell installé sur l'ordinateur d'émulation. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][powershell-install-configure].
- Installation de la plateforme Java JDK 7 ou version ultérieure sur l'ordinateur d'émulation. Cet élément est déjà disponible sur l'ordinateur d'émulation.
- Installation et configuration d'[Apache Maven](http://maven.apache.org/).
- Abonnement Azure actif. Pour obtenir des instructions, consultez les pages traitant des [options d'achat][azure-purchase-options], des [offres spéciales membres][azure-member-offers] ou de la [version d'évaluation gratuite][azure-free-trial].


##<a name="develop"></a>Utilisation d’Apache Maven pour créer un programme MapReduce en Java

Créez une application MapReduce de comptage de mots. Il s'agit d'une application toute simple qui compte les occurrences de chaque mot dans un ensemble de saisies donné. Dans cette section, nous effectuerons les tâches suivantes :

1. Création d’un projet au moyen d’Apache Maven
2. Mise à jour du modèle d’objet du projet (POM)
3. Création de l’application MapReduce de comptage de mots
4. Génération et package de l'application

**Pour créer un projet au moyen de Maven**

1. Créez un répertoire **C:\Tutorials\WordCountJava\**. 
2. À partir de la ligne de commande de votre environnement de développement, définissez les répertoires à l’emplacement que vous avez créé.
3. Utilisez la commande __mvn__, installée avec Maven, pour générer la structure du projet.

		mvn archetype:generate -DgroupId=org.apache.hadoop.examples -DartifactId=wordcountjava -DarchetypeArtifactId=maven-archetype-quickstart -DinteractiveMode=false

	Cela crée un répertoire dans le répertoire actuel, avec le nom spécifié par le paramètre __artifactID__ (**wordcountjava** dans cet exemple). Ce répertoire contiendra les éléments suivants :

	* __pom.xml__ - Le [modèle d’objet du projet (POM)](http://maven.apache.org/guides/introduction/introduction-to-the-pom.html)qui contient les informations et la configuration utilisées pour générer le projet.

	* __src__ - Le répertoire contenant le répertoire __main\\java\\org\\apache\\hadoop\\examples__ dans lequel vous créerez l’application.
3. Supprimez le fichier __src\\test\\java\\org\\apache\\hadoop\\examples\\apptest.java__, car il ne sera pas utilisé dans cet exemple.

**Pour mettre à jour le POM**

1. Modifiez le fichier __pom.xml__ et ajoutez l’élément suivant dans la section `<dependencies>` :

		<dependency>
		  <groupId>org.apache.hadoop</groupId>
          <artifactId>hadoop-mapreduce-examples</artifactId>
          <version>2.5.1</version>
        </dependency>
    	<dependency>
      	  <groupId>org.apache.hadoop</groupId>
      	  <artifactId>hadoop-mapreduce-client-common</artifactId>
      	  <version>2.5.1</version>
    	</dependency>
    	<dependency>                                                                                     
      	  <groupId>org.apache.hadoop</groupId>                                                                                                       
      	  <artifactId>hadoop-common</artifactId>                                                                                                         
      	  <version>2.5.1</version>                                                                                            
    	</dependency>

	Ceci indique à Maven que le projet a besoin des bibliothèques (figurant dans <artifactId>) avec une version spécifique (figurant dans <version>). Au moment de la compilation, il sera téléchargé à partir du référentiel Maven par défaut. Vous pouvez utiliser la [recherche du référentiel Maven](http://search.maven.org/#artifactdetails%7Corg.apache.hadoop%7Chadoop-mapreduce-examples%7C2.5.1%7Cjar) pour en afficher davantage.

2. Ajoutez le code suivant au fichier __pom.xml__. Il doit être contenu entre les balises `<project>...</project>` dans le fichier, par exemple entre `</dependencies>` et `</project>`.

		<build>
  		  <plugins>
    		<plugin>
      		  <groupId>org.apache.maven.plugins</groupId>
      		  <artifactId>maven-shade-plugin</artifactId>
      		  <version>2.3</version>
      		  <configuration>
        		<transformers>
          		  <transformer implementation="org.apache.maven.plugins.shade.resource.ApacheLicenseResourceTransformer">
		          </transformer>
        		</transformers>
      		  </configuration>
      		  <executions>
        		<execution>
          		  <phase>package</phase>
          			<goals>
            		  <goal>shade</goal>
          			</goals>
        	    </execution>
      		  </executions>
      	    </plugin>       
  		  </plugins>
	    </build>

	Cela configure le [plug-in Maven Shade](http://maven.apache.org/plugins/maven-shade-plugin/), qui est utilisé pour empêcher la duplication de licence dans le fichier JAR généré par Maven. Il est utilisé, car les fichiers de licence dupliqués entraînent une erreur à l'exécution sur le cluster HDInsight. Le fait d’utiliser le plug-in Maven Shade avec l’implémentation `ApacheLicenseResourceTransformer` permet d’éviter cette erreur.

	Le plug-in Maven Shade produit également un uberjar (parfois appelé fatjar) contenant toutes les dépendances requises par l’application.

3. Enregistrez le fichier __pom.xml__.

**Pour créer l’application de comptage de mots**

1. Accédez au répertoire __wordcountjava\\src\\main\\java\\org\\apache\\hadoop\\examples__ et renommez le fichier __app.java__ en __WordCount.java__.
2. Ouvrez le Bloc-notes.
2. Copiez et collez le programme suivant dans le Bloc-notes :

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

	Notez que le nom du package est **org.apache.hadoop.examples** et que le nom de la classe est **WordCount**. Vous utiliserez ces noms lors de l’envoi de la tâche MapReduce.
	
3. Enregistrez le fichier.

**Pour générer et mettre en package l’application**

1. Ouvrez une invite de commandes et indiquez le répertoire __wordcountjava__.

2. Utilisez la commande suivante pour générer un fichier JAR contenant l’application :

		mvn clean package

	Cela nettoie les artefacts de build précédents, télécharge toute dépendance non encore installée, puis génère et met l’application en package.

3. Une fois la commande exécutée, le répertoire __wordcountjava\\target__ contient un fichier appelé __wordcountjava-1.0-SNAPSHOT.jar__.

	> [AZURE.NOTE]Le fichier __wordcountjava-1.0-SNAPSHOT.jar__ est un uberjar.


##<a name="test"></a>Test du programme sur l’émulateur

Le test de la tâche MapReduce sur l'émulateur HDInsight inclut les procédures suivantes :

1. Téléchargement des fichiers de données pour le Hadoop Distributed File System (HDFS) sur l’émulateur
2. Création d'un groupe d'utilisateurs local
3. Exécution d’une tâche MapReduce de comptage des mots
4. Extraction des résultats de la tâche

L’émulateur HDInsight utilise HDFS en tant que système de fichiers par défaut. Vous pouvez éventuellement configurer l'émulateur HDInsight pour utiliser le stockage d'objets blob Azure. Pour plus d'informations, consultez la page [Prise en main de l'émulateur HDInsight][hdinsight-emulator-wasb].

Ce didacticiel utilise la commande **copyFromLocal** HDFS pour le téléchargement des fichiers de données vers HDFS. La section suivante montre comment télécharger des fichiers vers le stockage d’objets blob Azure en utilisant Azure PowerShell. Pour connaître les autres méthodes de téléchargement des fichiers sur le stockage d'objets blob Azure, consultez la page [Téléchargement de données vers HDInsight][hdinsight-upload-data].

Ce didacticiel utilise la structure de dossiers HDFS suivante :

<table border="1">
<tr><td>Dossier</td><td>Remarque</td></tr>
<tr><td>/WordCount</td><td>Dossier racine du projet de comptage de mots. </td></tr>
<tr><td>/WordCount/Apps</td><td>Dossier contenant les exécutables de mappeur et du raccord de réduction.</td></tr>
<tr><td>/WordCount/Input</td><td>Dossier des fichiers source MapReduce.</td></tr>
<tr><td>/WordCount/Output</td><td>Dossier des fichiers de résultat MapReduce.</td></tr>
<tr><td>/WordCount/MRStatusOutput</td><td>Dossier de résultat de la tâche.</td></tr>
</table>

Ce didacticiel utilise les fichiers .txt situés dans le répertoire %hadoop_home% en tant que fichiers de données.

> [AZURE.NOTE]Les commandes HDFS Hadoop sont sensibles à la casse.

**Copie des fichiers de données vers le HDFS de l’émulateur**

1. Ouvrez la ligne de commande Hadoop à partir de votre Bureau. La ligne de commande Hadoop est installée par le programme d’installation de l’émulateur.
2. Dans la fenêtre de ligne de commande Hadoop, exécutez la commande suivante pour créer un répertoire pour les fichiers d’entrée :

		hadoop fs -mkdir /WordCount
		hadoop fs -mkdir /WordCount/Input

	Le chemin utilisé ici est le chemin d'accès relatif. Il équivaut à ceci :

		hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

3. Exécutez la commande suivante pour copier certains fichiers texte vers le dossier d'entrée sur le HDFS :

		hadoop fs -copyFromLocal C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common*.txt /WordCount/Input

	La tâche MapReduce compte les mots de ces fichiers.

4. Exécutez la commande suivante pour répertorier et vérifier les fichiers téléchargés :

		hadoop fs -ls /WordCount/Input

**Création d’un groupe d’utilisateurs local**

Pour exécuter correctement la tâche MapReduce dans le cluster, vous devez créer un groupe d’utilisateurs appelé hdfs. À ce groupe, vous devez également ajouter un utilisateur appelé hadoop et l’utilisateur local avec lequel vous vous connectez à l’émulateur. Utilisez les commandes suivantes à partir d'une invite de commandes avec élévation de privilèges :

		# Add a user group called hdfs		
		net localgroup hdfs /add

		# Adds a user called hadoop to the group
		net localgroup hdfs hadoop /add

		# Adds the local user to the group
		net localgroup hdfs <username> /add

**Exécution de la tâche MapReduce en utilisant la ligne de commande Hadoop**

1. Ouvrez la ligne de commande Hadoop à partir de votre Bureau.
2. Exécutez la commande suivante pour supprimer la structure de dossiers /WordCount/Output de HDFS. /WordCount/Output est le dossier de sortie de la tâche MapReduce de comptage de mots. La tâche MapReduce échoue si le dossier existe déjà. Cette étape est requise si vous exécutez la tâche pour la deuxième fois.

		hadoop fs -rm - r /WordCount/Output

2. Exécutez la commande suivante :

		hadoop jar C:\Tutorials\WordCountJava\wordcountjava\target\wordcountjava-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /WordCount/Input /WordCount/Output

	Une fois la tâche terminée, le résultat obtenu doit ressembler à la capture d’écran suivante :

	![HDI.EMulator.WordCount.Run][image-emulator-wordcount-run]

	Comme vous pouvez le voir sur cette capture d’écran, le mappage et la réduction sont terminés à 100 %. L'ID de la tâche est également répertorié. Vous pouvez récupérer un rapport identique en utilisant le raccourci **État Hadoop MapReduce** depuis votre bureau, puis en recherchant le même ID de tâche.

Vous pouvez également exécuter une tâche MapReduce en utilisant Azure PowerShell. Pour plus d'informations, consultez la page [Prise en main de l'émulateur HDInsight][hdinsight-emulator].

**Affichage du résultat à partir du HDFS**

1. Ouvrez la ligne de commande Hadoop.
2. Exécutez les commandes suivantes pour afficher le résultat :

		hadoop fs -ls /WordCount/Output/
		hadoop fs -cat /WordCount/Output/part-r-00000

	Pour afficher la page, ajoutez « |more » à la fin de la commande. Ou utilisez la commande **findstr** pour rechercher un modèle de chaîne :

		hadoop fs -cat /WordCount/Output/part-r-00000 | findstr "there"

Vous avez développé une tâche MapReduce de comptage de mots et vous l’avez testée sur l’émulateur. La prochaine étape consiste à déployer cette tâche sur Azure HDInsight.



##<a id="upload"></a>Téléchargement des données et de l’application vers le stockage d’objets blob Azure
Azure HDInsight utilise le stockage d'objets blob pour stocker des données. Lorsqu'un cluster HDInsight est approvisionné, un conteneur de stockage d'objets blob Azure est utilisé pour stocker les fichiers du système. Vous pouvez utiliser ce conteneur par défaut ou utiliser un autre conteneur (soit sur le même compte Azure Storage, soit sur un autre compte de stockage situé dans le même centre de données que le cluster) pour le stockage des fichiers de données.

Dans ce didacticiel, vous allez créer un conteneur sur un compte de stockage distinct pour les fichiers de données et l’application MapReduce. Les fichiers de données sont les fichiers texte du répertoire **C:\\hdp\\hadoop-2.4.0.2.1.3.0-1981\\share\\doc\\hadoop\\common** de votre station de travail d'émulation.

**Création d’un stockage d’objets blob et d’un conteneur**

1. Ouvrez Azure PowerShell.
2. Définissez les variables, puis exécutez ces commandes :

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"
		$location = "<MicrosoftDataCenter>"  # For example, "East US"

	La variable **$subscripionName** est associée à votre abonnement Azure. Vous devez nommer les éléments **$storageAccountName_Data** et **$containerName_Data**. Pour connaître les restrictions relatives aux attributions de noms, consultez la page [Affectation de noms et références aux conteneurs, objets BLOB et métadonnées](http://msdn.microsoft.com/library/windowsazure/dd135715.aspx).

3. Exécutez les commandes suivantes pour créer un compte de stockage et un conteneur de stockage d’objets blob sur le compte :

		# Select an Azure subscription
		Select-AzureSubscription $subscriptionName
		
		# Create a Storage account
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Data -location $location
				
		# Create a Blob storage container
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  
		New-AzureStorageContainer -Name $containerName_Data -Context $destContext

4. Exécutez les commandes suivantes pour vérifier le compte de stockage et le conteneur :

		Get-AzureStorageAccount -StorageAccountName $storageAccountName_Data
		Get-AzureStorageContainer -Context $destContext

**Téléchargement des fichiers de données**

1. Ouvrez Azure PowerShell.
2. Définissez les trois premières variables, puis exécutez les commandes suivantes :

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"

		$localFolder = "C:\hdp\hadoop-2.4.0.2.1.3.0-1981\share\doc\hadoop\common"
		$destFolder = "WordCount/Input"

	Les variables **$storageAccountName_Data** et **$containerName_Data** sont les mêmes que celles définies dans la dernière procédure.

	Notez que le dossier des fichiers source est **c:\\Hadoop\\hadoop-1.1.0-SNAPSHOT** et le dossier de destination est **WordCount/Input**.

3. Exécutez les commandes suivantes pour obtenir une liste des fichiers .txt contenus dans le dossier des fichiers sources :

		# Get a list of the .txt files
		$filesAll = Get-ChildItem $localFolder
		$filesTxt = $filesAll | where {$_.Extension -eq ".txt"}
		
4. Exécutez les commandes suivantes pour créer un objet de contexte de stockage :

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5. Exécutez les commandes suivantes pour copier les fichiers :

		# Copy the files from the local workstation to the Blob container        
		foreach ($file in $filesTxt){
		 
		    $fileName = "$localFolder\$file"
		    $blobName = "$destFolder/$file"
		
		    write-host "Copying $fileName to $blobName"
		
		    Set-AzureStorageBlobContent -File $fileName -Container $containerName_Data -Blob $blobName -Context $destContext
		}

6. Utilisez les commandes suivantes pour répertorier les fichiers téléchargés :

		# List the uploaded files in the Blob storage container
        Write-Host "The Uploaded data files:" -BackgroundColor Green
		Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $destFolder

	Vous devez voir les fichiers de données texte téléchargés.

**Téléchargement de l’application de comptage de mots**

1. Ouvrez Azure PowerShell.
2. Définissez les trois premières variables, puis exécutez les commandes suivantes :

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<AzureStorageAccountName>"  
		$containerName_Data = "<ContainerName>"

		$jarFile = "C:\Tutorials\WordCountJava\wordcountjava\target\wordcountjava-1.0-SNAPSHOT.jar"
		$blobFolder = "WordCount/jars"

	Les variables **$storageAccountName_Data** et **$containerName_Data** sont les mêmes que celles définies dans la dernière procédure, ce qui signifie que vous allez télécharger à la fois le fichier de données et l’application vers le même conteneur sur le même compte de stockage.

	Notez que le dossier de destination est **WordCount/jars**.

4. Exécutez les commandes suivantes pour créer un objet de contexte de stockage :

		# Create a storage context object
		Select-AzureSubscription $subscriptionName
		$storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
		$destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5. Exécutez la commande suivante pour copier les applications :

		Set-AzureStorageBlobContent -File $jarFile -Container $containerName_Data -Blob "$blobFolder/WordCount.jar" -Context $destContext

6. Utilisez les commandes suivantes pour répertorier les fichiers téléchargés :

		# List the uploaded files in the Blob storage container
        Write-Host "The Uploaded application file:" -BackgroundColor Green
		Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $blobFolder

	Le fichier JAR doit être répertorié ici.

##<a name="run"></a>Exécution de la tâche MapReduce sur Azure HDInsight

Dans cette section, vous allez créer un script Azure PowerShell qui effectue les tâches suivantes :

1. Approvisionnement d'un cluster HDInsight
	
	1. Création d’un compte de stockage utilisé en tant que système de fichiers de cluster HDInsight par défaut
	2. Création d'un conteneur d'objets blob 
	3. Création d'un cluster HDInsight

2. Envoi de la tâche MapReduce

	1. Création d'une définition de tâche MapReduce
	2. Envoi d'une tâche MapReduce
	3. Attente de l’arrêt de la tâche
	4. Affichage de l'erreur standard
	5. Affichage du résultat standard

3. Suppression du cluster

	1. Suppression du cluster HDInsight
	2. Suppression du compte de stockage utilisé en tant que système de fichiers de cluster HDInsight par défaut


**Exécution du script Azure PowerShell**

1. Ouvrez le Bloc-notes.
2. Copiez et collez le code suivant :
		
		# The Storage account and the HDInsight cluster variables
		$subscriptionName = "<AzureSubscriptionName>"
		$stringPrefix = "<StringForPrefix>"
		$location = "<MicrosoftDataCenter>"     ### Must match the data Storage account location
		$clusterNodes = <NumberOFNodesInTheCluster>

		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		
		$clusterName = $stringPrefix + "hdicluster"
		
		$storageAccountName_Default = $stringPrefix + "hdistore"
		$containerName_Default =  $stringPrefix + "hdicluster"

		# The MapReduce job variables
		$jarFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/jars/WordCount.jar"
		$className = "org.apache.hadoop.examples.WordCount"
		$mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
		$mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
		$mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"
		
		# Create a PSCredential object. The user name and password are hardcoded here. You can change them if you want.
		$password = ConvertTo-SecureString "Pass@word1" -AsPlainText -Force
		$creds = New-Object System.Management.Automation.PSCredential ("Admin", $password) 
		
		Select-AzureSubscription $subscriptionName
		
		#=============================
		# Create a Storage account used as the default file system
		Write-Host "Create a storage account" -ForegroundColor Green
		New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location
		
		#=============================
		# Create a Blob storage container used as the default file system
		Write-Host "Create a Blob storage container" -ForegroundColor Green
		$storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
		$destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default
		
		New-AzureStorageContainer -Name $containerName_Default -Context $destContext
		
		#=============================
		# Create an HDInsight cluster
		Write-Host "Create an HDInsight cluster" -ForegroundColor Green
		$storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		
		$config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
		    Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
		    Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data
		
		New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $creds -Config $config
		
		#=============================
		# Create a MapReduce job definition
		Write-Host "Create a MapReduce job definition" -ForegroundColor Green
		$mrJobDef = New-AzureHDInsightMapReduceJobDefinition -JobName mrWordCountJob  -JarFile $jarFile -ClassName $className -Arguments $mrInput, $mrOutput -StatusFolder /WordCountStatus
		
		#=============================
		# Run the MapReduce job
		Write-Host "Run the MapReduce job" -ForegroundColor Green
		$mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef 
		Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600 
		
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError 
		Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput
				
		#=============================
		# Delete the HDInsight cluster
		Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
		Remove-AzureHDInsightCluster -Name $clusterName  
		
		# Delete the default file system Storage account
		Write-Host "Delete the storage account" -ForegroundColor Green
		Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3. Définissez les six premières variables du script. La variable **$stringPrefix** permet d’ajouter la chaîne spécifiée sous forme de préfixe au nom du cluster HDInsight, au nom du compte de stockage et au nom du conteneur de stockage d’objets blob. Étant donné que ces noms doivent comprendre entre 3 et 24 caractères, vérifiez que la chaîne que vous spécifiez et que les noms utilisés par ce script n’excèdent pas ensemble la limite de caractères. Vous devez uniquement utiliser des minuscules pour **$stringPrefix**.
 
	Les variables **$storageAccountName_Data** et **$containerName_Data** correspondent au compte de stockage et au conteneur utilisés pour le stockage des fichiers de données et de l’application. La variable **$location** doit correspondre à l’emplacement du compte de stockage des données.

4. Consultez le reste des variables.
5. Enregistrez le fichier de script.
6. Ouvrez Azure PowerShell.
7. Exécutez la commande suivante pour définir la stratégie d’exécution sur RemoteSigned :

		PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8. Lorsque vous y êtes invité, entrez le nom d’utilisateur et le mot de passe du cluster HDInsight. Comme vous allez supprimer le cluster à la fin du script et que vous n’aurez plus besoin de ce nom d’utilisateur et de ce mot de passe, ils peuvent correspondre à n’importe quelle chaîne. Si vous ne voulez pas être invité à saisir les informations d’identification, consultez la page [Utilisation des mots de passe, des chaînes sécurisées et des informations d’identification dans Windows PowerShell][powershell-PSCredential].


##<a name="retrieve"></a>Extraction du résultat de la tâche MapReduce
Cette section montre comment télécharger et afficher le résultat. Pour obtenir des informations sur l’affichage des résultats dans Excel, consultez les rubriques [Connexion d’Excel à HDInsight avec le pilote ODBC Microsoft Hive][hdinsight-ODBC] et [Connexion d’Excel à HDInsight avec Power Query][hdinsight-power-query].


**Extraction du résultat**

1. Ouvrez la fenêtre Azure PowerShell.
2. Remplacez le répertoire par **C:\\Tutorials\\WordCountJava**. Le dossier Azure Powershell par défaut est **C:\\Windows\\System32\\WindowsPowerShell\\v1.0**. Les cmdlets que vous allez exécuter vont télécharger le fichier de résultat vers le dossier en cours. Vous n'êtes pas autorisé à télécharger les fichiers vers les dossiers du système.
2. Exécutez les commandes suivantes pour définir les valeurs :

		$subscriptionName = "<AzureSubscriptionName>"
		$storageAccountName_Data = "<TheDataStorageAccountName>"
		$containerName_Data = "<TheDataBlobStorageContainerName>"
		$blobName = "WordCount/Output/part-r-00000"
	
3. Exécutez les commandes suivantes pour créer un objet de contexte de stockage Azure :
		
		Select-AzureSubscription $subscriptionName
		$storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
		$storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  

4. Exécutez les commandes suivantes pour télécharger et afficher le résultat :

		Get-AzureStorageBlobContent -Container $containerName_Data -Blob $blobName -Context $storageContext -Force
		cat "./$blobName" | findstr "there"

Une fois la tâche terminée, vous pouvez soit exporter les données vers SQL Server ou une base de données SQL Azure en utilisant [Sqoop][hdinsight-use-sqoop], soit exporter les données vers Excel.

##<a id="nextsteps"></a>Étapes suivantes
Dans ce didacticiel, vous avez appris à développer une tâche MapReduce en Java, à tester l’application sur l’émulateur HDInsight et à écrire un script Azure PowerShell pour approvisionner un cluster HDInsight et exécuter une tâche MapReduce sur le cluster. Pour en savoir plus, consultez les articles suivants :

- [Développement de programmes MapReduce de diffusion en continu Hadoop en C# pour HDInsight][hdinsight-develop-streaming]
- [Prise en main d’Azure HDInsight][hdinsight-get-started]
- [Prise en main de l’émulateur HDInsight][hdinsight-emulator]
- [Utilisation du stockage d’objets blob Azure avec HDInsight][hdinsight-storage]
- [Administration de HDInsight à l’aide d’Azure PowerShell][hdinsight-admin-powershell]
- [Téléchargement de données vers HDInsight][hdinsight-upload-data]
- [Utilisation de Hive avec HDInsight][hdinsight-use-hive]
- [Utilisation de Pig avec HDInsight][hdinsight-use-pig]
- [Connexion d’Excel à HDInsight à l’aide de Power Query][hdinsight-power-query]
- [Connexion d’Excel à HDInsight à l’aide du pilote ODBC Microsoft Hive][hdinsight-ODBC]

[azure-purchase-options]: http://azure.microsoft.com/pricing/purchase-options/
[azure-member-offers]: http://azure.microsoft.com/pricing/member-offers/
[azure-free-trial]: http://azure.microsoft.com/pricing/free-trial/

[hdinsight-use-sqoop]: hdinsight-use-sqoop.md
[hdinsight-ODBC]: hdinsight-connect-excel-hive-ODBC-driver.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[hdinsight-develop-streaming]: hdinsight-hadoop-develop-deploy-streaming-jobs.md

[hdinsight-get-started]: ../hdinsight-get-started.md
[hdinsight-emulator]: ../hdinsight-get-started-emulator.md
[hdinsight-emulator-wasb]: ../hdinsight-get-started-emulator.md#blobstorage
[hdinsight-upload-data]: hdinsight-upload-data.md
[hdinsight-storage]: ../hdinsight-use-blob-storage.md
[hdinsight-admin-powershell]: hdinsight-administer-use-powershell.md
[hdinsight-use-hive]: hdinsight-use-hive.md
[hdinsight-use-pig]: hdinsight-use-pig.md
[hdinsight-power-query]: hdinsight-connect-excel-power-query.md

[powershell-PSCredential]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
[powershell-install-configure]: ../install-configure-powershell.md



[image-emulator-wordcount-compile]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Compile-Java-MapReduce.png
[image-emulator-wordcount-run]: ./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Run-Java-MapReduce.png


<!--HONumber=54--> 