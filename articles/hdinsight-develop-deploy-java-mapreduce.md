<properties linkid="manage-services-hdinsight-develop-Java-MapReduce-programs-for-HDInsight" urlDisplayName="HDInsight Tutorials" pageTitle="Develop Java MapReduce programs for HDInsight | Azure" metaKeywords="hdinsight, hdinsight development, hadoop development, hdinsight deployment, development, deployment, tutorial, MapReduce, Java" description="Learn how to develop Java MapReduce programs on HDInsight emulator, how to deploy them to HDInsight." services="hdinsight" title="Develop Java MapReduce programs for HDInsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" authors="jgao" />

Développement de programmes MapReduce en Java pour HDInsight
============================================================

Ce didacticiel vous familiarise avec un scénario de bout en bout, allant du développement et du test d'une tâche MapReduce de comptage de mots sur un émulateur HDInsight, à son déploiement et à son exécution sur Azure HDInsight.

**Configuration requise :**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

-   Émulateur HDInsight Azure installé. Pour obtenir des instructions, consultez la page [Prise en main de l'émulateur HDInsight](/en-us/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/).
-   Azure PowerShell installé sur l'ordinateur d'émulation. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell](/fr-fr/documentation/articles/install-configure-powershell/).
-   Abonnement Azure actif. Pour obtenir des instructions, consultez les pages traitant des [options d'achat](https://www.windowsazure.com/en-us/pricing/purchase-options/), des [offres spéciales membres](https://www.windowsazure.com/en-us/pricing/member-offers/) ou de la [version d'évaluation gratuite](https://www.windowsazure.com/en-us/pricing/free-trial/).

Dans cet article
----------------

-   [Développement d'un programme MapReduce de comptage de mots dans Java](#develop)
-   [Test du programme sur l'émulateur](#test)
-   [Téléchargement des fichiers de données et de l'application vers le stockage d'objets blob Azure](#upload)
-   [Exécution du programme MapReduce sur Azure HDInsight](#run)
-   [Extraction des résultats MapReduce](#retrieve)
-   [Étapes suivantes](#nextsteps)

Développement d'un programme MapReduce de comptage de mots dans Java
--------------------------------------------------------------------

L'application de comptage de mots est une application toute simple qui compte les occurrences de chaque mot dans un ensemble de saisies donné.

**Exécution de la tâche MapReduce de comptage des mots dans Java**

1.  Ouvrez le Bloc-notes.
2.  Copiez et collez le programme suivant dans le Bloc-notes.

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
             System.exit(job.waitForCompletion(true) 
         0 : 1);
           }
         }

    Notez que le nom du package est **org.apache.hadoop.examples** et que le nom de la classe est **WordCount**. Vous utiliserez ces noms lors de l'envoi de la tâche MapReduce.

3.  Enregistrez le fichier dans l'emplacement **c:\\Tutorials\\WordCountJava\\WordCount.java**. Créez cette structure de dossiers si elle n'existe pas.

L'émulateur HDInsight est livré avec le compilateur *javac*.

**Compilation du programme MapReduce**

1.  Ouvrez l'invite de commandes.
2.  Remplacez le répertoire par **c:\\Tutorials\\WordCountJava**. Il s'agit du dossier du programme MapReduce de comptage de mots.
3.  Exécutez la commande suivante pour vérifier que les deux fichiers jar existent bien :

         dir %hadoop_home%\hadoop-core-1.1.0-SNAPSHOT.jar
         dir %hadoop_home%\lib\commons-cli-1.2.jar

4.  Exécutez la commande suivante pour compiler le programme :

         C:\Hadoop\java\bin\javac -classpath %hadoop_home%\hadoop-core-1.1.0-SNAPSHOT.jar;%hadoop_home%\lib\commons-cli-1.2.jar WordCount.java

    Javac se trouve dans le dossier C:\\Hadoop\\java\\bin. Le dernier paramètre correspond au programme Java situé dans le dossier en cours. Le compilateur crée 3 fichiers de classe dans le dossier en cours.

5.  Exécutez la commande suivante pour créer un fichier jar :

         C:\Hadoop\java\bin\jar -cvf WordCount.jar *.class

    Cette commande crée un fichier WordCount.jar dans le dossier en cours.

    ![HDI.EMulator.WordCount.Compile](./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Compile-Java-MapReduce.png)

Test du programme sur l'émulateur
---------------------------------

Le test de la tâche MapReduce sur l'émulateur inclut les procédures suivantes :

1.  Téléchargement des fichiers de données sur le HDFS de l'émulateur
2.  Envoi d'une tâche MapReduce de comptage de mots
3.  Vérification de l'état de la tâche
4.  Extraction des résultats de la tâche

L'émulateur HDInsight utilise HDFS en tant que système de fichiers par défaut. Vous pouvez éventuellement configurer l'émulateur HDInsight pour utiliser le stockage d'objets blob Azure. Pour plus d'informations, consultez la page [Prise en main de l'émulateur HDInsight](/en-us/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/#blobstorage).

Ce didacticiel utilise la commande *copyFromLocal* HDFS pour le téléchargement des fichiers de données vers HDFS. La section suivante montre comment télécharger des fichiers en utilisant Azure PowerShell vers le stockage d'objets blob Azure. Pour connaître les autres méthodes de téléchargement des fichiers sur le stockage d'objets blob Azure, consultez la page [Téléchargement de données vers HDInsight](/en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/).

Ce didacticiel utilise la structure de dossiers HDFS suivante :

<table border="1">
<tr><td>Dossier</td><td>Remarque</td></tr>
<tr><td>/WordCount</td><td>Dossier racine du projet de comptage de mots. </td></tr>
<tr><td>/WordCount/Apps</td><td>Dossier contenant les exécutables de mappeur et du raccord de réduction.</td></tr>
<tr><td>/WordCount/Input</td><td>Dossier des fichiers source MapReduce.</td></tr>
<tr><td>/WordCount/Output</td><td>Dossier des fichiers de résultat MapReduce.</td></tr>
<tr><td>/WordCount/MRStatusOutput</td><td>Dossier de résultat de la tâche.</td></tr>
</table>

Ce didacticiel utilise les fichiers .txt situés dans le répertoire %hadoop\_home% en tant que fichiers de données.

> [WACOM.NOTE] Les commandes HDFS Hadoop sont sensibles à la casse.

**Copie des fichiers de données vers le HDFS de l'émulateur**

1.  Ouvrez la ligne de commande Hadoop à partir de votre Bureau. La ligne de commande Hadoop est installée par le programme d'installation de l'émulateur.
2.  Dans la fenêtre de ligne de commande Hadoop, exécutez la commande suivante pour créer un répertoire pour les fichiers d'entrée :

         hadoop fs -mkdir /WordCount/Input

    Le chemin utilisé ici est le chemin d'accès relatif. Il équivaut à ceci :

         hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

3.  Exécutez la commande suivante pour copier certains fichiers texte vers le dossier d'entrée sur le HDFS :

         hadoop fs -copyFromLocal %hadoop_home%\*.txt /WordCount/Input

    La tâche MapReduce compte les mots de ces fichiers.

4.  Exécutez la commande suivante pour répertorier et vérifier les fichiers téléchargés :

         hadoop fs -ls /WordCount/Input

    Vous devriez voir à peu près huit fichiers .txt.

**Exécution de la tâche MapReduce en utilisant la ligne de commande Hadoop**

1.  Ouvrez la ligne de commande Hadoop à partir de votre Bureau.
2.  Exécutez la commande suivante pour supprimer la structure des dossiers /WordCount/Output de HDFS. /WordCount/Output correspond au dossier de résultat de la tâche de comptage de mots MapReduce. La tâche MapReduce échoue si le dossier existe déjà. Cette étape est requise si vous exécutez la tâche pour la deuxième fois.

         hadoop fs -rmr /WordCount/Output

3.  Exécutez la commande suivante :

         hadoop jar c:\Tutorials\WordCountJava\wordcount\target\wordcount-1.0-SNAPSHOT.jar org.apache.hadoop.examples.WordCount /WordCount/Input /WordCount/Output

    Une fois la commande terminée, le résultat obtenu doit ressembler à la capture d'écran suivante :

    ![HDI.EMulator.WordCount.Run](./media/hdinsight-develop-deploy-java-mapreduce/HDI-Emulator-Run-Java-MapReduce.png)

    Comme vous pouvez le voir sur cette capture d'écran, le mappage et la réduction sont terminés à 100 %. L'ID de la tâche, job\_201312092021\_0002, est également répertorié. Vous pouvez récupérer un rapport identique en utilisant le raccourci **Hadoop MapReduce Status** depuis votre Bureau, puis en recherchant l'ID de la tâche.

Vous pouvez également exécuter une tâche MapReduce en utilisant Azure PowerShell. Pour plus d'informations, consultez la page [Prise en main de l'émulateur HDInsight](/en-us/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/).

**Affichage du résultat à partir du HDFS**

1.  Ouvrez la ligne de commande Hadoop.
2.  Exécutez les commandes suivantes pour afficher le résultat :

         hadoop fs -ls /WordCount/Output/
         hadoop fs -cat /WordCount/Output/part-00000

    Pour afficher la page, ajoutez « |more » à la fin de la commande. Ou utilisez la commande findstr pour rechercher un modèle de chaîne :

         hadoop fs -cat /WordCount/Output/part-00000 | findstr "there"

Jusqu'à présent, vous avez développé une tâche MapReduce de comptage de mots et vous l'avez testée sur l'émulateur. La prochaine étape consiste à déployer cette tâche sur Azure HDInsight.

Téléchargement de données vers le stockage d'objets blob Azure
--------------------------------------------------------------

Azure HDInsight utilise le stockage d'objets blob pour stocker des données. Lorsqu'un cluster HDInsight est approvisionné, un conteneur de stockage d'objets blob Azure est utilisé pour stocker les fichiers du système. Vous pouvez soit utiliser ce conteneur par défaut, soit utiliser un autre conteneur (soit sur le même compte de stockage Azure ou sur un autre compte de stockage situé sur le même centre de données que le cluster) pour le stockage des fichiers de données.

Dans ce didacticiel, vous allez créer un conteneur sur un compte de stockage distinct pour les fichiers de données et l'application MapReduce. Les fichiers de données sont des fichiers texte contenus dans le répertoire %hadoop\_home% de votre poste de travail.

**Création d'un stockage d'objets blob et d'un conteneur**

1.  Ouvrez Azure PowerShell.
2.  Définissez les variables, puis exécutez ces commandes :

         $subscriptionName = "<AzureSubscriptionName>"
         $storageAccountName_Data = "<AzureStorageAccountName>"  
         $containerName_Data = "<ContainerName>"
         $location = "<MicrosoftDataCenter>"  # Par exemple, "Est des États-Unis"

    L'élément **$subscripionName** est associé à votre abonnement Azure. Vous devez nommer les éléments **$storageAccountName\_Data** et **$containerName\_Data**. Pour connaître les restrictions relatives aux attributions de noms, consultez la page [Affectation de noms et références aux conteneurs, objets BLOB et métadonnées](http://msdn.microsoft.com/fr-fr/library/windowsazure/dd135715.aspx).

3.  Exécutez la commande suivante pour créer un compte de stockage et un conteneur de stockage d'objets blob sur le compte

         # Sélection d'abonnement Azure
         Select-AzureSubscription $subscriptionName
            
         # Création d'un compte de stockage
         New-AzureStorageAccount -StorageAccountName $storageAccountName_Data -location $location
                    
         # Création d'un conteneur de stockage d'objets blob
         $storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
         $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  
         New-AzureStorageContainer -Name $containerName_Data -Context $destContext

4.  Exécutez les commandes suivantes pour vérifier le compte de stockage et le conteneur :

         Get-AzureStorageAccount -StorageAccountName $storageAccountName_Data
         Get-AzureStorageContainer -Context $destContext

**Téléchargement des fichiers de données**

1.  Ouvrez Azure PowerShell.
2.  Définissez les trois premières variables, puis exécutez les commandes suivantes :

         $subscriptionName = "<AzureSubscriptionName>"
         $storageAccountName_Data = "<AzureStorageAccountName>"  
         $containerName_Data = "<ContainerName>"

         $localFolder = "c:\Hadoop\hadoop-1.1.0-SNAPSHOT"
         $destFolder = "WordCount/Input"

    **$storageAccountName\_Data** et **$containerName\_Data** ont la même valeur que celle définie dans la dernière procédure.

    Notez que le dossier des fichiers source est **c:\\Hadoop\\hadoop-1.1.0-SNAPSHOT** et le dossier de destination est **WordCount/Input**.

3.  Exécutez les commandes suivantes pour obtenir une liste des fichiers .txt contenus dans le dossier des fichiers source :

         # Obtention d'une liste des fichiers .txt
         $filesAll = Get-ChildItem $localFolder
         $filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

4.  Exécutez les commandes suivantes pour créer un objet de contexte de stockage :

         # Création d'un objet de contexte de stockage
         Select-AzureSubscription $subscriptionName
         $storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

5.  Exécutez les commandes suivantes pour copier les fichiers :

         # Copie du fichier depuis le poste de travail local vers le conteneur d'objets blob        
         foreach ($file in $filesTxt){
             
             $fileName = "$localFolder\$file"
             $blobName = "$destFolder/$file"
            
             write-host "Copying $fileName to $blobName"
            
             Set-AzureStorageBlobContent -File $fileName -Container $containerName_Data -Blob $blobName -Context $destContext
         }

6.  Utilisez la commande suivante pour répertorier les fichiers téléchargés :

         # Création d'une liste des fichiers téléchargés dans le conteneur de stockage d'objets blob
         Write-Host "The Uploaded data files:" -BackgroundColor Green
         Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $destFolder

    Vous devez voir 8 fichiers .txt de données.

**Téléchargement de l'application de comptage de mots**

1.  Ouvrez Azure PowerShell.
2.  Définissez les trois premières variables, puis exécutez les commandes suivantes :

         $subscriptionName = "<AzureSubscriptionName>"
         $storageAccountName_Data = "<AzureStorageAccountName>"  
         $containerName_Data = "<ContainerName>"

         $jarFile = "C:\Tutorials\WordCountJava\WordCount.jar"
         $blobFolder = "WordCount/jars"

    **$storageAccountName\_Data** et **$containerName\_Data** ont les mêmes valeurs que celles définies dans la dernière procédure, ce qui signifie que vous allez télécharger à la fois le fichier de données et l'application vers le même conteneur sur le même compte de stockage.

    Notez que le dossier de destination est **WordCount/jars**.

3.  Exécutez les commandes suivantes pour créer un objet de contexte de stockage :

         # Création d'un objet de contexte de stockage
         Select-AzureSubscription $subscriptionName
         $storageaccountkey = get-azurestoragekey $storageAccountName_Data | %{$_.Primary}
         $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName_Data -StorageAccountKey $storageaccountkey

4.  Exécutez les commandes suivantes pour copier les applications :

         Set-AzureStorageBlobContent -File $jarFile -Container $containerName_Data -Blob "$blobFolder/WordCount.jar" -Context $destContext

5.  Utilisez la commande suivante pour répertorier les fichiers téléchargés :

         # Création d'une liste des fichiers téléchargés dans le conteneur de stockage d'objets blob
         Write-Host "The Uploaded application file:" -BackgroundColor Green
         Get-AzureStorageBlob -Container $containerName_Data -Context $destContext -Prefix $blobFolder

    Le fichier .jar doit être répertorié ici.

Exécution du programme MapReduce sur Azure HDInsight
----------------------------------------------------

Le script PowerShell suivant effectue les tâches suivantes :

1.  Approvisionnement d'un cluster HDInsight

    1.  Création d'un compte de stockage utilisé en tant que système de fichiers de cluster HDInsight par défaut
    2.  Création d'un conteneur de stockage d'objets blob
    3.  Création d'un cluster HDInsight

2.  Envoi de la tâche MapReduce

    1.  Création d'une définition de tâche MapReduce
    2.  Envoi d'une tâche MapReduce
    3.  Attente de la fin de la tâche
    4.  Affichage de l'erreur standard
    5.  Affichage du résultat standard

3.  Suppression du cluster

    1.  Suppression du cluster HDInsight
    2.  Suppression du compte de stockage utilisé en tant que système de fichiers de cluster HDInsight par défaut

**Exécution du script PowerShell**

1.  Ouvrez le Bloc-notes.
2.  Copiez et collez le code suivant :

         # Compte de stockage et variables du cluster HDInsight
         $subscriptionName = "<WindowsAzureSubscriptionName>"
         $serviceNameToken = "<ServiceNameTokenString>"
         $location = "<MicrosoftDataCenter>"     ### doit correspondre à l'emplacement du compte de stockage de données
         $clusterNodes = <NumberOFNodesInTheCluster>

         $storageAccountName_Data = "<TheDataStorageAccountName>"
         $containerName_Data = "<TheDataBlobStorageContainerName>"
            
         $clusterName = $serviceNameToken + "hdicluster"
            
         $storageAccountName_Default = $serviceNameToken + "hdistore"
         $containerName_Default =  $serviceNameToken + "hdicluster"

         # Variables de la tâche MapReduce
         $jarFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/jars/WordCount.jar"
         $className = "org.apache.hadoop.examples.WordCount"
         $mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
         $mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
         $mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"
            
         # Créez un objet PSCredential. Le nom d'utilisateur et le mot de passe sont codés en dur ici.  Vous pouvez les modifier si vous voulez.
         $password = ConvertTo-SecureString "Pass@word1" -AsPlainText -Force
         $creds = New-Object System.Management.Automation.PSCredential ("Admin", $password) 
            
         Select-AzureSubscription $subscriptionName
            
         #=============================
         # Création d'un compte de stockage utilisé en tant que système de fichiers par défaut
         Write-Host "Create a storage account" -ForegroundColor Green
         New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location
            
         #=============================
         # Création d'un conteneur de stockage d'objets blob utilisé en tant que système de fichiers par défaut
         Write-Host "Create a Blob storage container" -ForegroundColor Green
         $storageAccountKey_Default = Get-AzureStorageKey $storageAccountName_Default | %{ $_.Primary }
         $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Default –StorageAccountKey $storageAccountKey_Default
            
         New-AzureStorageContainer -Name $containerName_Default -Context $destContext
            
         #=============================
         # Création d'un cluster HDInsight
         Write-Host "Create an HDInsight cluster" -ForegroundColor Green
         $storageAccountKey_Data = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
            
         $config = New-AzureHDInsightClusterConfig -ClusterSizeInNodes $clusterNodes |
             Set-AzureHDInsightDefaultStorage -StorageAccountName "$storageAccountName_Default.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Default -StorageContainerName $containerName_Default |
             Add-AzureHDInsightStorage -StorageAccountName "$storageAccountName_Data.blob.core.windows.net" -StorageAccountKey $storageAccountKey_Data
            
         New-AzureHDInsightCluster -Name $clusterName -Location $location -Credential $creds -Config $config
            
         #=============================
         # Création d'une définition de tâche MapReduce
         Write-Host "Create a MapReduce job definition" -ForegroundColor Green
         $mrJobDef = New-AzureHDInsightMapReduceJobDefinition -JobName mrWordCountJob  -JarFile $jarFile -ClassName $className -Arguments $mrInput, $mrOutput -StatusFolder /WordCountStatus
            
         #=============================
         # Exécution de la tâche MapReduce
         Write-Host "Run the MapReduce job" -ForegroundColor Green
         $mrJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $mrJobDef 
         Wait-AzureHDInsightJob -Job $mrJob -WaitTimeoutInSeconds 3600 
            
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardError 
         Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $mrJob.JobId -StandardOutput
                    
         #=============================
         # Suppression du cluster HDInsight
         Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
         Remove-AzureHDInsightCluster -Name $clusterName  
            
         # Suppression du compte de stockage de système de fichiers par défaut
         Write-Host "Delete the storage account" -ForegroundColor Green
         Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3.  Définissez les six premières variables du script. **$serviceNameToken** est utilisé pour le nom du cluster HDInsight, le nom du compte de stockage par défaut et le nom du conteneur de stockage d'objets blob par défaut. Comme le nom du service doit contenir entre 3 et 24 caractères et que le script ajoute une chaîne contenant jusqu'à 10 caractères aux noms, vous devez limiter la chaîne à 14 caractères maximum. Et \$serviceNameToken doit contenir uniquement des minuscules. **$storageAccountName\_Data** et **$containerName\_Data** correspondent au compte de stockage et au conteneur utilisés pour le stockage des fichiers de données et de l'application. **$location** doit correspondre à l'emplacement du compte de stockage des données.
4.  Consultez le reste des variables.
5.  Enregistrez le fichier de script.
6.  Ouvrez Azure PowerShell.
7.  Exécutez les commandes suivantes pour définir la stratégie d'exécution sur remotesigned :

         PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8.  Lorsque vous y êtes invité, entrez le nom d'utilisateur et le mot de passe du cluster HDInsight. Comme vous allez supprimer le cluster à la fin du script et que vous n'aurez plus besoin de ce nom d'utilisateur et de ce mot de passe, ils peuvent correspondre à n'importe quelle chaîne. Si vous ne voulez pas être invité à saisir les informations d'identification, consultez la page [Utilisation des mots de passe, des chaînes sécurisées et des informations d'identification dans Windows PowerShell](http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx)

Extraction du résultat de la tâche MapReduce
--------------------------------------------

Cette section montre comment télécharger et afficher le résultat. Pour obtenir les informations sur l'affichage des résultats sur Excel, consultez les articles [Connexion d'Excel à HDInsight avec le pilote ODBC Microsoft Hive](/en-us/manage/services/hdinsight/connect-excel-with-hive-ODBC/) et [Connexion d'Excel à HDInsight avec Power Query](/en-us/manage/services/hdinsight/connect-excel-with-power-query/).

**Extraction du résultat**

1.  Ouvrez la fenêtre Azure PowerShell.
2.  Remplacez le répertoire par **C:\\Tutorials\\WordCountJava**. Le dossier Azure Powershell par défaut est **C:\\Windows\\System32\\WindowsPowerShell\\v1.0**. Les cmdlets que vous allez exécuter vont télécharger le fichier de résultat vers le dossier en cours. Vous n'êtes pas autorisé à télécharger les fichiers vers les dossiers du système.
3.  Exécutez les commandes suivantes pour définir les valeurs :

         $subscriptionName = "<WindowsAzureSubscriptionName>"
         $storageAccountName_Data = "<TheDataStorageAccountName>"
         $containerName_Data = "<TheDataBlobStorageContainerName>"
         $blobName = "WordCount/Output/part-r-00000"

4.  Exécutez les commandes suivantes pour créer un objet de contexte de stockage Azure :

         Select-AzureSubscription $subscriptionName
         $storageAccountKey = Get-AzureStorageKey $storageAccountName_Data | %{ $_.Primary }
         $storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName_Data –StorageAccountKey $storageAccountKey  

5.  Exécutez les commandes suivantes pour télécharger et afficher le résultat :

         Get-AzureStorageBlobContent -Container $containerName_Data -Blob $blobName -Context $storageContext -Force
         cat "./$blobName" | findstr "there"

Une fois la tâche terminée, vous pouvez soit exporter les données vers SQL Server ou une base de données SQL Azure en utilisant [Sqoop](../hdinsight-use-sqoop/), soit exporter les données vers Excel.

Étapes suivantes
----------------

Dans ce didacticiel, vous avez appris à développer une tâche MapReduce Java, à tester l'application sur un émulateur HDInsight et à écrire un script PowerShell pour approvisionner un cluster HDInsight et exécuter une tâche MapReduce sur le cluster. Pour en savoir plus, consultez les articles suivants :

-   [Développement de programmes MapReduce de diffusion en continu Hadoop en C\# pour HDInsight](/fr-fr/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
-   [Prise en main d'Azure HDInsight](/en-us/manage/services/hdinsight/get-started-hdinsight/)
-   [Prise en main de l'émulateur HDInsight](/en-us/manage/services/hdinsight/get-started-with-windows-azure-hdinsight-emulator/)
-   [Utilisation du stockage d'objets blob Azure avec HDInsight](/en-us/manage/services/hdinsight/howto-blob-store/)
-   [Administration de HDInsight à l'aide de PowerShell](/en-us/manage/services/hdinsight/administer-hdinsight-using-powershell/)
-   [Téléchargement de données vers HDInsight](/en-us/manage/services/hdinsight/howto-upload-data-to-hdinsight/)
-   [Utilisation de Hive avec HDInsight](/en-us/manage/services/hdinsight/using-hive-with-hdinsight/)
-   [Utilisation de Pig avec HDInsight](/en-us/manage/services/hdinsight/using-pig-with-hdinsight/)
-   [Connexion d'Excel à HDInsight à l'aide de Power Query](/fr-fr/documentation/articles/hdinsight-connect-excel-power-query/)
-   [Connexion d'Excel à HDInsight à l'aide du pilote ODBC Microsoft Hive](../hdinsight-connect-excel-hive-ODBC-driver/)

