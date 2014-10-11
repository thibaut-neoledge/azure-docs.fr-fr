<properties linkid="manage-services-hdinsight-develop-hadoop-streaming-programs-for-hdinsight" urlDisplayName="" pageTitle="Develop C# Hadoop streaming programs for HDInsight | Azure" metaKeywords="hdinsight hdinsight development, hadoop development, hdinsight deployment, development, deployment, tutorial, MapReduce" description="Learn how to develop Hadoop streaming MapReduce programs in C#, and how to deploy them to Azure HDInsight." metaCanonical="" services="hdinsight" documentationCenter="" title="Develop C# Hadoop streaming programs for HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

<tags ms.service="hdinsight" ms.workload="big-data" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="jgao"></tags>

# Développement de programmes de diffusion en continu Hadoop en C# pour HDInsight

Hadoop fournit une API de diffusion en continu pour MapReduce qui vous permet d'écrire des fonctions de mappage et de réduction dans d'autres langages que Java. This tutorial walks you through an end-to-end scenario from developing/testing a Hadoop streaming MapReduce program using C# on an HDInsight emulator, to running the MapReduce job on Azure HDInsight, and then to retrieving the results.

**Configuration requise :**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

-   Émulateur HDInsight Azure installé. Pour obtenir des instructions, consultez la page [Prise en main de l'émulateur HDInsight][].
-   Azure PowerShell installé sur l'ordinateur d'émulation. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][].
-   Abonnement Azure actif. Pour obtenir des instructions, consultez les pages traitant des [options d'achat][], des [offres spéciales membres][] ou de la [version d'évaluation gratuite][].

## Dans cet article

-   [Développement d'un programme de diffusion en continu Hadoop pour le comptage de mots dans C#][]
-   [Test du programme sur l'émulateur][]
-   [Téléchargement des données et des applications vers le stockage d'objets blob Azure][]
-   [Exécution du programme MapReduce sur Azure HDInsight][]
-   [Extraction des résultats MapReduce][]
-   [Étapes suivantes][]

## <a name="develop"></a>Développement d'un programme de diffusion en continu Hadoop pour le comptage de mots dans C#

La solution de comptage de mots contient deux projets d'application console : le mappeur et le raccord de réduction. L'application mappeur diffuse chaque mot dans la console et l'application raccord de réduction compte le nombre de mots diffusés depuis un document. Le mappeur et le raccord de réduction peuvent tous les deux lire les caractères à partir du flux d'entrée standard (stdin), puis écrire dans le flux de sortie standard (stdout).

**Création d'une application console C#**

1.  Ouvrez Visual Studio 2013.
2.  Cliquez sur **FICHIER**, sur **Nouveau**, puis sur **Projet**.
3.  Tapez ou sélectionnez les valeurs suivantes :

    <table border="1">
    <tr><td>Champ             </td><td>Valeur                                </td></tr>
    <tr><td>Modèle            </td><td>Visual C#/Windows/Application console</td></tr>
    <tr><td>Nom               </td><td>WordCountMapper                       </td></tr>
    <tr><td>Emplacement       </td><td>C:\Tutorials                         </td></tr>
    <tr><td>Nom de la solution</td><td>WordCount                             </td></tr>
	</table>

4.  Cliquez sur **OK** pour créer le projet.

**Création du programme mappeur**

1.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur **Program.cs**, puis cliquez sur **Renommer**.
2.  Renommez le fichier **WordCountMapper.cs**, puis appuyez sur **﻿﻿ENTRÉE**.
3.  Cliquez sur **Oui** pour confirmer les changement de noms de toutes les références.
4.  Double-cliquez sur **WordCountMapper.cs** pour l'ouvrir.
5.  Ajoutez les instructions using suivantes :

        using System.IO;

6.  Remplacez la fonction Main() par le code suivant :

        static void Main(string[] args)
        {
            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            string line;
            string[] words;

            while ((line = Console.ReadLine()) != null)
            {
                words = line.Split(' ');

                foreach (string word in words)
                    Console.WriteLine(word.ToLower());
            }
        }

7.  Cliquez sur **﻿GÉNÉRER**, puis sur **Générer la solution** pour compiler le programme mappeur.

**Création du programme raccord de réduction**

1.  Dans Visual Studio 2013, cliquez sur **﻿﻿FICHIER**, sur **Ajouter**, puis sur **Nouveau projet**.
2.  Tapez ou sélectionnez les valeurs suivantes :

    <table border="1">
    <tr><td>Champ      </td><td>Valeur                                </td></tr>
    <tr><td>Modèle     </td><td>Visual C#/Windows/Application console</td></tr>
    <tr><td>Nom        </td><td>WordCountReducer                      </td></tr>
    <tr><td>Emplacement</td><td>C:\Tutorials\WordCount              </td></tr>
	</table>

3.  Cliquez sur **OK** pour créer le projet.
4.  Dans l'Explorateur de solutions, cliquez avec le bouton droit sur **Program.cs**, puis cliquez sur **Renommer**.
5.  Renommez le fichier **WordCountReducer.cs**, puis appuyez sur **﻿﻿ENTRÉE**.
6.  Cliquez sur **Oui** pour confirmer les changement de noms de toutes les références.
7.  Double-cliquez sur **WordCountReducer.cs** pour l'ouvrir.
8.  Ajoutez les instructions using suivantes :

        using System.IO;

9.  Remplacez la fonction Main() par le code suivant :

        static void Main(string[] args)
        {
            string word, lastWord = null;
            int count = 0;

            if (args.Length > 0)
            {
                Console.SetIn(new StreamReader(args[0]));
            }

            while ((word = Console.ReadLine()) != null)
            {
                if (word != lastWord)
                {
                    if(lastWord != null)
                        Console.WriteLine("{0}[{1}]", lastWord, count);

                    count = 1;
                    lastWord = word;
                }
                else
                {
                    count += 1; 
                }
            }
            Console.WriteLine(count);
        }

10. Cliquez sur **﻿GÉNÉRER**, puis sur **Générer la solution** pour compiler la solution.

Les exécutables mappeur et raccord de réduction sont situés sur :

-   C:\\Tutorials\\WordCount\\WordCountMapper\\bin\\Debug\\WordCountMapper.exe
-   C:\\Tutorials\\WordCount\\WordCountReducer\\bin\\Debug\\WordCountReducer.exe

## <a name="test"></a>Test du programme sur l'émulateur

Cette section contient les procédures suivantes :

1.  Téléchargement de données vers l'émulateur HDFS
2.  Téléchargement du mappeur et du raccord de réduction sur l'émulateur HDFS
3.  Envoi d'une tâche MapReduce de comptage de mots
4.  Vérification de l'état de la tâche
5.  Extraction des résultats de la tâche

L'émulateur HDInsight utilise HDFS en tant que système de fichiers par défaut. Vous pouvez éventuellement configurer l'émulateur HDInsight pour utiliser le stockage d'objets blob Azure. Pour plus d'informations, consultez la page [Prise en main de l'émulateur HDInsight][1]. Dans cette section, vous allez utiliser la commande HDFS copyFromLocal pour télécharger les fichiers. La section suivante montre comment télécharger des fichiers en utilisant Azure PowerShell. Pour découvrir d'autres méthodes, consultez la rubrique [Téléchargement de données vers HDInsight][].

Ce didacticiel utilise la structure de dossiers suivante :

<table border="1">
<tr><td>Dossier                    </td><td>Remarque                                                                </td></tr>
<tr><td>\\WordCount                </td><td>Dossier racine du projet de comptage de mots.                           </td></tr>
<tr><td>\\WordCount\\Apps          </td><td>Dossier contenant les exécutables de mappeur et du raccord de réduction.</td></tr>
<tr><td>\\WordCount\\Input         </td><td>Dossier des fichiers source MapReduce.                                  </td></tr>
<tr><td>\\WordCount\\Output        </td><td>Dossier des fichiers de résultat MapReduce.                             </td></tr>
<tr><td>\\WordCount\\MRStatusOutput</td><td>Dossier de résultat de la tâche.                                        </td></tr>
</table>

Ce didacticiel utilise les fichiers .txt situés dans le répertoire %hadoop\_home%.

> [WACOM.NOTE] Les commandes HDFS Hadoop sont sensibles à la casse.

**Copie des fichiers texte vers le HDFS de l'émulateur**

1.  Dans la fenêtre de ligne de commande Hadoop, exécutez la commande suivante pour créer un répertoire pour les fichiers d'entrée :

        hadoop fs -mkdir /WordCount/Input

    Le chemin utilisé ici est le chemin d'accès relatif. Il équivaut à ceci :

        hadoop fs -mkdir hdfs://localhost:8020/WordCount/Input

2.  Exécutez la commande suivante pour copier certains fichiers texte vers le dossier d'entrée sur le HDFS :

        hadoop fs -copyFromLocal %hadoop_home%\*.txt \WordCount\Input

3.  Utilisez la commande suivante pour répertorier les fichiers téléchargés :

        hadoop fs -ls \WordCount\Input

    Vous devriez voir à peu près huit fichiers .txt.

**Déploiement du mappeur et du raccord de réduction sur l'émulateur HDFS**

1.  Ouvrez la ligne de commande Hadoop à partir de votre Bureau.
2.  Exécutez les commandes suivantes :

        hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe /WordCount/Apps/WordCountMapper.exe
        hadoop fs -copyFromLocal C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe /WordCount/Apps/WordCountReducer.exe

3.  Exécutez la commande suivante pour répertorier les fichiers téléchargés

        hadoop fs -lsr /WordCount/Apps

    Vous devriez voir les deux fichiers .exe.

**Exécution de la tâche MapReduce avec PowerShell HDInsight**

1.  Ouvrez Azure PowerShell. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell][].
2.  Exécutez les commandes suivantes pour définir les variables :

        $clusterName = "http://localhost:50111"

        $mrMapper = "WordCountMapper.exe"
        $mrReducer = "WordCountReducer.exe"
        $mrMapperFile = "/WordCount/Apps/WordCountMapper.exe"
        $mrReducerFile = "/WordCount/Apps/WordCountReducer.exe"
        $mrInput = "/WordCount/Input/"
        $mrOutput = "/WordCount/Output"
        $mrStatusOutput = "/WordCount/MRStatusOutput"

    The HDInsight emulator cluster name is "<http://localhost:50111>".

3.  Exécutez les commandes suivantes pour définir la tâche de diffusion en continu :

        $mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
        $mrJobDef.Files.Add($mrMapperFile)
        $mrJobDef.Files.Add($mrReducerFile)

4.  Exécutez la commande suivante pour créer un objet d'informations d'identification :

        $creds = Get-Credential -Message "Enter password" -UserName "hadoop"

    Vous êtes invité à entrer le mot de passe. Le mot de passe peut être une chaîne quelconque. Le nom d'utilisateur doit être « hadoop ».

5.  Exécutez les commandes suivantes pour envoyer la tâche MapReduce et attendez qu'elle soit terminée :

        $mrJob = Start-AzureHDInsightJob -Cluster $clusterName -Credential $creds -JobDefinition $mrJobDef
        Wait-AzureHDInsightJob -Credential $creds -job $mrJob -WaitTimeoutInSeconds 3600

    Une fois la tâche terminée, le résultat doit ressembler à celui-ci :

        StatusDirectory : /WordCount/MRStatusOutput
        ExitCode        : 
        Name            : mrWordCountStreamingJob
        Query           : 
        State           : Completed
        SubmissionTime  : 11/15/2013 7:18:16 PM
        Cluster         : http://localhost:50111
        PercentComplete : map 100%  reduce 100%
        JobId           : job_201311132317_0034

    Vous connaissez donc l'ID de la tâche, par exemple : job-201311132317-0034.

**Vérification de l'état de la tâche**

1.  Sur le Bureau, cliquez sur **Hadoop MapReduce Status**, ou accédez à **<http://localhost:50030/jobtracker.jsp>**.
2.  Recherchez la tâche en utilisant son ID sous l'une des trois sections suivantes : **Tâches terminées**, **Tâches en cours d'exécution**, **Tâches retirées**.
3.  Si une tâche échoue, accédez à ses informations détaillées pour y chercher des informations utiles à son débogage.

**Affichage du résultat à partir du HDFS**

1.  Ouvrez la ligne de commande Hadoop.
2.  Exécutez les commandes suivantes pour afficher le résultat :

        hadoop fs -ls /WordCount/Output/
        hadoop fs -cat /WordCount/Output/part-00000

    Pour afficher la page, ajoutez « |more » à la fin de la commande.

## <span id="upload"></span></a>Téléchargement de données vers le stockage d'objets blob Azure

Azure HDInsight utilise le stockage d'objets blob Azure comme système de fichiers par défaut. Vous pouvez configurer un cluster HDInsight pour utiliser un autre stockage d'objets blob pour les fichiers de données. Dans cette section, vous allez créer un compte de stockage, puis télécharger les fichiers de données vers le stockage d'objets blob. Les fichiers de données sont des fichiers .txt contenus dans le répertoire %hadoop\_home%.

**Création d'un stockage d'objets blob et d'un conteneur**

1.  Ouvrez Azure PowerShell.
2.  Définissez les variables, puis exécutez ces commandes :

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"  
        $containerName = "<ContainerName>"
        $location = "<MicrosoftDataCenter>"  # For example, "East US"

3.  Exécutez la commande suivante pour créer un compte de stockage et un conteneur de stockage d'objets blob sur le compte

        # Select Azure subscription
        Select-AzureSubscription $subscriptionName

        # Create a storage account
        New-AzureStorageAccount -StorageAccountName $storageAccountName -location $location

        # Create a Blob storage container
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $destContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  
        New-AzureStorageContainer -Name $containerName -Context $destContext

4.  Exécutez les commandes suivantes pour vérifier le compte de stockage et le conteneur :

        Get-AzureStorageAccount -StorageAccountName $storageAccountName
        Get-AzureStorageContainer -Context $destContext

**Téléchargement des fichiers de données**

1.  Ouvrez Azure PowerShell.
2.  Définissez les trois premières variables, puis exécutez les commandes suivantes :

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"  
        $containerName = "<ContainerName>"

        $localFolder = "c:\Hadoop\hadoop-1.1.0-SNAPSHOT"
        $destFolder = "WordCount/Input"

    Notez que le dossier des fichiers source est **c:\\Hadoop\\hadoop-1.1.0-SNAPSHOT** et le dossier de destination est **WordCount/Input**.

3.  Exécutez les commandes suivantes pour obtenir une liste des fichiers .txt contenus dans le dossier des fichiers source :

        # Get a list of the txt files
        $filesAll = Get-ChildItem $localFolder
        $filesTxt = $filesAll | where {$_.Extension -eq ".txt"}

4.  Exécutez les commandes suivantes pour créer un objet de contexte de stockage :

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

5.  Exécutez les commandes suivantes pour copier les fichiers :

        # Copy the file from local workstation to the Blob container        
        foreach ($file in $filesTxt){

            $fileName = "$localFolder\$file"
            $blobName = "$destFolder/$file"

            write-host "Copying $fileName to $blobName"

            Set-AzureStorageBlobContent -File $fileName -Container $containerName -Blob $blobName -Context $destContext
        }

6.  Utilisez la commande suivante pour répertorier les fichiers téléchargés :

        # List the uploaded files in the Blob storage container
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $destFolder

**Téléchargement des applications de comptage de mots**

1.  Ouvrez Azure PowerShell.
2.  Définissez les trois premières variables, puis exécutez les commandes suivantes :

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<AzureStorageAccountName>"  
        $containerName = "<ContainerName>"

        $mapperFile = "C:\Tutorials\WordCount\WordCountMapper\bin\Debug\WordCountMapper.exe"
        $reducerFile = "C:\Tutorials\WordCount\WordCountReducer\bin\Debug\WordCountReducer.exe"
        $blobFolder = "WordCount/Apps"

    Notez que le dossier de destination est **WordCount/Apps**.

3.  Exécutez les commandes suivantes pour créer un objet de contexte de stockage :

        # Create a storage context object
        Select-AzureSubscription $subscriptionName
        $storageaccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
        $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageaccountkey

4.  Exécutez les commandes suivantes pour copier les applications :

        Set-AzureStorageBlobContent -File $mapperFile -Container $containerName -Blob "$blobFolder/WordCountMapper.exe" -Context $destContext
        Set-AzureStorageBlobContent -File $reducerFile -Container $containerName -Blob "$blobFolder/WordCountReducer.exe" -Context $destContext

5.  Utilisez la commande suivante pour répertorier les fichiers téléchargés :

        # List the uploaded files in the Blob storage container
        Get-AzureStorageBlob -Container $containerName  -Context $destContext -Prefix $blobFolder

    Les deux fichiers doivent être répertoriés ici.

## <a name="run"></a>Exécution du programme MapReduce sur Azure HDInsight

Le script PowerShell suivant effectue les tâches suivantes :

1.  Approvisionnement d'un cluster HDInsight

    1.  Création d'un compte de stockage utilisé en tant que système de fichiers de cluster HDInsight par défaut
    2.  Création d'un conteneur de stockage d'objets blob
    3.  Création d'un cluster HDInsight

2.  Envoi de la tâche MapReduce

    1.  Création d'une définition de tâche MapReduce de diffusion en continu
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

        # The storage account and the HDInsight cluster variables
        $subscriptionName = "<AzureSubscriptionName>"
        $serviceNameToken = "<ServiceNameTokenString>"
        $storageAccountName_Data = "<TheDataStorageAccountName>"
        $containerName_Data = "<TheDataBlobStorageContainerName>"
        $location = "<MicrosoftDataCenter>"     ### must match the data storage account location
        $clusterNodes = 1

        $clusterName = $serviceNameToken + "hdicluster"

        $storageAccountName_Default = $serviceNameToken + "hdistore"
        $containerName_Default =  $serviceNameToken + "hdicluster"

        # The streaming MapReduce job variables
        $mrMapper = "WordCountMapper.exe"
        $mrReducer = "WordCountReducer.exe"
        $mrMapperFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountMapper.exe"
        $mrReducerFile = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Apps/WordCountReducer.exe"
        $mrInput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Input/"
        $mrOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/Output/"
        $mrStatusOutput = "wasb://$containerName_Data@$storageAccountName_Data.blob.core.windows.net/WordCount/MRStatusOutput/"

        Select-AzureSubscription $subscriptionName

        #=============================
        # Create a storage account
        Write-Host "Create a storage account" -ForegroundColor Green
        New-AzureStorageAccount -StorageAccountName $storageAccountName_Default -location $location

        #=============================
        # Create a Blob storage container
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

        New-AzureHDInsightCluster -Subscription $subscriptionName  -Name $clusterName -Location $location -Config $config

        #=============================
        # Create a streaming MapReduce job definition
        Write-Host "Create a streaming MapReduce job definition" -ForegroundColor Green

        $mrJobDef = New-AzureHDInsightStreamingMapReduceJobDefinition -JobName mrWordCountStreamingJob -StatusFolder $mrStatusOutput -Mapper $mrMapper -Reducer $mrReducer -InputPath $mrInput -OutputPath $mrOutput
        $mrJobDef.Files.Add($mrMapperFile)
        $mrJobDef.Files.Add($mrReducerFile)

        #=============================
        # Run a streaming MapReduce job
        Write-Host "Run a streaming MapReduce job" -ForegroundColor Green
        $mrJob = Start-AzureHDInsightJob -Cluster $clusterName -Subscription $subscriptionName -JobDefinition $mrJobDef 
        Wait-AzureHDInsightJob -Subscription $subscriptionName -Job $mrJob -WaitTimeoutInSeconds 3600 

        Get-AzureHDInsightJobOutput -Cluster $clusterName -Subscription $subscriptionName -JobId $mrJob.JobId -StandardError 
        Get-AzureHDInsightJobOutput -Cluster $clusterName -Subscription $subscriptionName -JobId $mrJob.JobId -StandardOutput

        #=============================
        # Delete the HDInsight cluster
        Write-Host "Delete the HDInsight cluster" -ForegroundColor Green
        Remove-AzureHDInsightCluster -Name $clusterName -Subscription $subscriptionName 

        # Delete the storage account
        Write-Host "Delete the storage account" -ForegroundColor Green
        Remove-AzureStorageAccount -StorageAccountName $storageAccountName_Default

3.  Définissez les quatre premières variables du script. $serviceNameToken est utilisé pour le nom du cluster HDInsight, le nom du compte de stockage et le nom du conteneur de stockage d'objets blob. Comme le nom du service doit contenir entre 3 et 24 caractères et que le script ajoute une chaîne contenant jusqu'à 10 caractères aux noms, vous devez limiter la chaîne à 14 caractères maximum. Vous devez uniquement utiliser des minuscules pour $serviceNameToken. $storageAccountName\_Data et $containerName\_Data correspondent au compte de stockage et au conteneur utilisés pour le stockage des fichiers de données et des applications. $location doit correspondre à l'emplacement du compte de stockage des données.
4.  Consultez le reste des variables.
5.  Enregistrez le fichier de script.
6.  Ouvrez Azure PowerShell.
7.  Exécutez les commandes suivantes pour définir la stratégie d'exécution sur remotesigned :

        PowerShell -File <FileName> -ExecutionPolicy RemoteSigned

8.  Lorsque vous y êtes invité, entrez le nom d'utilisateur et le mot de passe du cluster HDInsight. Comme vous allez supprimer le cluster à la fin du script et que vous n'aurez plus besoin de ce nom d'utilisateur et de ce mot de passe, ils peuvent correspondre à n'importe quelle chaîne. Si vous ne voulez pas être invité à saisir les informations d'identification, consultez la page [Utilisation des mots de passe, des chaînes sécurisées et des informations d'identification dans Windows PowerShell][]

For a HDInsight .NET SDK sample on submitting Hadoop streaming jobs, see [Submit Hadoop jobs programmatically][].

## <a name="retrieve"></a>Extraction du résultat de la tâche MapReduce

Cette section montre comment télécharger et afficher le résultat. Pour obtenir les informations sur l'affichage des résultats sur Excel, consultez les articles [Connexion d'Excel à HDInsight avec le pilote ODBC Microsoft Hive][] et [Connexion d'Excel à HDInsight avec Power Query][].

**Extraction du résultat**

1.  Ouvrez la fenêtre Azure PowerShell.
2.  Définissez les valeurs, puis exécutez ces commandes :

        $subscriptionName = "<AzureSubscriptionName>"
        $storageAccountName = "<TheDataStorageAccountName>"
        $containerName = "<TheDataBlobStorageContainerName>"
        $blobName = "WordCount/Output/part-00000"

3.  Exécutez les commandes suivantes pour créer un objet de contexte de stockage Azure :

        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext –StorageAccountName $storageAccountName –StorageAccountKey $storageAccountKey  

4.  Exécutez les commandes suivantes pour télécharger et afficher le résultat :

        Get-AzureStorageBlobContent -Container $ContainerName -Blob $blobName -Context $storageContext -Force
        cat "./$blobName" | findstr "there"

## <span id="nextsteps"></span></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à développer une tâche MapReduce de diffusion en continu Hadoop, à tester l'application sur un émulateur HDInsight et à écrire un script PowerShell pour approvisionner un cluster HDInsight et exécuter une tâche MapReduce sur le cluster. Pour en savoir plus, consultez les articles suivants :

-   [Prise en main d'Azure HDInsight][]
-   [Prise en main de l'émulateur HDInsight][]
-   [Développement de programmes MapReduce en Java pour HDInsight][]
-   [Utilisation du stockage d'objets blob Azure avec HDInsight][]
-   [Administration de HDInsight à l'aide de PowerShell][]
-   [Téléchargement de données vers HDInsight][]
-   [Utilisation de Hive avec HDInsight][]
-   [Utilisation de Pig avec HDInsight][]

  [Prise en main de l'émulateur HDInsight]: ../hdinsight-get-started-emulator/
  [Installation et configuration d'Azure PowerShell]: ../install-configure-powershell/
  [options d'achat]: http://azure.microsoft.com/en-us/pricing/purchase-options/
  [offres spéciales membres]: http://azure.microsoft.com/en-us/pricing/member-offers/
  [version d'évaluation gratuite]: http://azure.microsoft.com/en-us/pricing/free-trial/
  [Développement d'un programme de diffusion en continu Hadoop pour le comptage de mots dans C#]: #develop
  [Test du programme sur l'émulateur]: #test
  [Téléchargement des données et des applications vers le stockage d'objets blob Azure]: #upload
  [Exécution du programme MapReduce sur Azure HDInsight]: #run
  [Extraction des résultats MapReduce]: #retrieve
  [Étapes suivantes]: #nextsteps
  [1]: ../hdinsight-get-started-emulator/#blobstorage
  [Téléchargement de données vers HDInsight]: ../hdinsight-upload-data/
  [Utilisation des mots de passe, des chaînes sécurisées et des informations d'identification dans Windows PowerShell]: http://social.technet.microsoft.com/wiki/contents/articles/4546.working-with-passwords-secure-strings-and-credentials-in-windows-powershell.aspx
  [Submit Hadoop jobs programmatically]: ../hdinsight-submit-hadoop-jobs-programmatically/
  [Connexion d'Excel à HDInsight avec le pilote ODBC Microsoft Hive]: ../hdinsight-connect-excel-hive-ODBC-driver/
  [Connexion d'Excel à HDInsight avec Power Query]: ../hdinsight-connect-excel-power-query/
  [Prise en main d'Azure HDInsight]: ../hdinsight-get-started/
  [Développement de programmes MapReduce en Java pour HDInsight]: ../hdinsight-develop-deploy-java-mapreduce/
  [Utilisation du stockage d'objets blob Azure avec HDInsight]: ../hdinsight-use-blob-storage/
  [Administration de HDInsight à l'aide de PowerShell]: ../hdinsight-administer-use-powershell/
  [Utilisation de Hive avec HDInsight]: ../hdinsight-use-hive/
  [Utilisation de Pig avec HDInsight]: ../hdinsight-use-pig/
