<properties  linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="Get Started" pageTitle="Get started using HDInsight | Azure" metaKeywords="" description="Get started with HDInsight, a big data solution. Learn how to provision clusters, run MapReduce jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Azure HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

# Prise en main d'Azure HDInsight

HDInsight rend [Apache Hadoop][1] accessible en tant que service dans le cloud. L'infrastructure logicielle MapReduce devient ainsi disponible dans un environnement Azure plus simple, plus extensible et plus économique. HDInsight propose également une approche économique de la gestion et du stockage de données à l'aide du stockage d'objets blob Azure.

Dans ce didacticiel, vous allez configurer un cluster HDInsight via le portail de gestion Azure, envoyer une tâche MapReduce Hadoop à l'aide de PowerShell, puis importer les données de sortie de la tâche MapReduce dans Excel pour examen.

> [WACOM.NOTE] Ce didacticiel couvre l'utilisation de clusters Hadoop 1.2 sous HDInsight. Pour accéder au didacticiel faisant appel à des clusters Hadoop 2.2 sous HDInsight, consultez la rubrique [Prise en main des clusters Hadoop 2.2 avec HDInsight](/fr-fr/documentation/articles/hdinsight-get-started-30/).

Parallèlement à la mise à disposition générale d'Azure HDInsight, Microsoft a également publié l'émulateur HDInsight pour Azure, connu auparavant sous le nom de Microsoft HDInsight Developer Preview. Ce produit cible les scénarios de développement et, à ce titre, ne prend en charge que les déploiements sur nœud unique. Pour utiliser l'émulateur HDInsight, consultez la rubrique [Prise en main de l'émulateur HDInsight](/fr-fr/documentation/articles/hdinsight-get-started-emulator/).

**Configuration requise :**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* Un abonnement Azure. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les [formules d'abonnement][2], les [offres spéciales membres][3] ou la [version d'évaluation gratuite][4].
* Un ordinateur exécutant Windows 8, Windows 7, Windows Server 2012 ou Windows Server 2008 R2. Cet ordinateur sera utilisé pour envoyer les tâches MapReduce.
* Office Professionnel Plus 2013, Office 365 ProPlus, l'édition autonome d'Excel 2013 ou Office Professionnel Plus 2010.

**Durée de réalisation estimée :** 30 minutes

## Dans ce didacticiel

* [Configuration d'un environnement local pour l'exécution de PowerShell](#setup)
* [Configuration d'un cluster HDInsight](#provision)
* [Exécution d'un programme MapReduce WordCount](#sample)
* [Connexion aux outils décisionnels Microsoft](#powerquery)
* [Étapes suivantes](#nextsteps)

## <a  id="setup" ></a> Configuration d'un environnement local pour l'exécution de PowerShell

Il existe plusieurs façons d'envoyer des tâches MapReduce vers HDInsight. Dans ce didacticiel, nous allons utiliser Azure PowerShell. Pour installer Azure PowerShell, exécutez [Microsoft Web Platform Installer][5]. Lorsque vous y êtes invité, cliquez sur **Exécuter**, sur **Installer**, puis suivez les instructions. Pour plus d'informations, consultez la rubrique [Installation et configuration d'Azure PowerShell](/fr-fr/documentation/articles/install-configure-powershell/).

Les cmdlets PowerShell requièrent vos informations d'abonnement pour pouvoir gérer vos services.

**Pour vous connecter à votre abonnement à l'aide d'Azure AD**

1.  Ouvrez la console Azure PowerShell, comme indiqué dans la rubrique [Installation d'Azure PowerShell](/fr-fr/documentation/articles/install-configure-powershell/#install).
2.  Exécutez la commande suivante :
    
         Add-AzureAccount

3.  Dans la fenêtre, tapez l'adresse de messagerie et le mot de passe associés à votre compte. Azure authentifie et enregistre les informations d'identification, puis ferme la fenêtre.

Vous pouvez également vous connecter à votre abonnement en employant la méthode par certificat. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell](/fr-fr/documentation/articles/install-configure-powershell/).

## <a  name="provision" ></a>Configuration d'un cluster HDInsight

Le processus de configuration de HDInsight nécessite l'utilisation d'un compte de stockage Azure comme système de fichiers par défaut. Ce compte de stockage doit se situer dans le même centre de données que les ressources de calcul HDInsight. Pour l'heure, les clusters HDInsight ne peuvent être configurés que dans les centres de données suivants :

* Asie du Sud-Est
* Europe du Nord
* Europe de l'Ouest
* Est des États-Unis
* Ouest des États-Unis

Vous devez choisir l'un de ces cinq centres de données pour votre compte de stockage Azure.

**Pour créer un compte de stockage Azure**

1.  Connectez-vous au [portail de gestion Azure][6]. 
2.  Cliquez sur **NEW** dans le coin inférieur gauche, pointez sur **DATA SERVICES**, sur **STORAGE**, puis cliquez sur **QUICK CREATE**.
    
    ![HDI.StorageAccount.QuickCreate](./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png)

3.  Entrez l'**URL**, l'**emplacement** et la **réplication**, puis cliquez sur **CREATE STORAGE ACCOUNT**. Les groupes d'affinités ne sont pas pris en charge. Le nouveau compte de stockage figure dès lors dans la liste de stockage.
4.  Attendez que le **statut** du nouveau compte de stockage passe à **Online**.
5.  Cliquez sur le nouveau compte de stockage dans la liste pour le sélectionner.
6.  Cliquez sur **MANAGE ACCESS KEYS** au bas de la page. 
7.  Notez le **nom du compte de stockage** et la **clé d'accès primaire**. Vous en aurez besoin plus loin dans le didacticiel.

Pour obtenir des instructions détaillées, consultez les rubriques [Création d'un compte de stockage](/fr-fr/documentation/articles/storage-create-storage-account/) et [Utilisation du stockage d'objets blob Azure avec HDInsight](/fr-fr/documentation/articles/hdinsight-use-blob-storage/).

**Pour configurer un cluster HDInsight**

1.  Connectez-vous au [portail de gestion Azure][6].

2.  Cliquez sur **HDInsight** à gauche pour afficher le statut des clusters de votre compte. Dans la capture d'écran suivante, il n'existe aucun cluster HDInsight.
    
    ![HDI.ClusterStatus](./media/hdinsight-get-started/HDI.ClusterStatus.png)

3.  Dans la partie inférieure gauche de l'écran, cliquez sur **NEW**, sur **Data Services**, sur **HDInsight**, puis sur **Quick Create**.
    
    ![HDI.QuickCreateCluster](./media/hdinsight-get-started/HDI.QuickCreateCluster.png)

4.  Entrez ou sélectionnez les valeurs suivantes :
    
    <table  border="1">
     <tr><th>Nom</th>
    <th>Valeur</th>
    </tr>
    
     <tr><td>Cluster Name</td>
    <td>Nom du cluster</td>
    </tr>
    
     <tr><td>Cluster Size</td>
    <td>Nombre de nœuds de données que vous souhaitez déployer. La valeur par défaut est 4, mais le menu déroulant propose également des clusters de 8, 16 et 32 nœuds de données. Vous pouvez spécifier n'importe quel nombre de nœuds de données en utilisant l'option <strong>Custom Create</strong>. Des détails de tarification sur les taux de facturation en fonction des tailles de cluster sont disponibles. Cliquez sur le symbole <strong>?</strong> juste au-dessus de la zone déroulante et suivez le lien dans la fenêtre contextuelle.</td>
    </tr>
    
     <tr><td>Password (cluster admin)</td>
    <td>Mot de passe du compte <i>admin</i>
    . Le nom d'utilisateur spécifié par défaut pour le cluster est « admin » lorsque l'option Quick Create est utilisée. Ce nom ne peut être modifié qu'en utilisant l'Assistant <strong>Custom Create</strong>
    . Le champ de mot de passe doit contenir au moins 10 caractères et doit être constitué d'une majuscule, d'une minuscule, d'un chiffre et d'un caractère spécial.</td>
    </tr>
    
     <tr><td>Storage Account</td>
    <td>Sélectionnez le compte de stockage que vous avez créé dans la zone déroulante. <br  />
    
    
     > [WACOM.NOTE] > Dès lors qu'un compte de stockage est sélectionné, il ne peut plus être modifié. Si le compte de stockage est supprimé, le cluster ne peut plus être utilisé.
    
     L'emplacement du cluster HDInsight n'est pas le même que celui du compte de stockage.
     </td>
    </tr>
    
     </table>

5.  Cliquez sur **Create HDInsight Cluster** dans la partie inférieure droite. À l'issue du processus de configuration, la colonne de statut indique **Running**.

Pour plus d'informations sur l'utilisation de l'option **CUSTOM CREATE**, consultez la rubrique [Configuration de clusters HDInsight](/fr-fr/documentation/articles/hdinsight-provision-clusters/).

## <a  name="sample" ></a>Exécution d'une tâche MapReduce WordCount

Maintenant que vous avez configuré un cluster HDInsight, la prochaine étape consiste à exécuter une tâche MapReduce pour compter le nombre de mots contenus dans un fichier texte.

L'exécution d'une tâche MapReduce nécessite les éléments suivants :

* Un programme MapReduce. Dans ce didacticiel, vous allez utiliser l'exemple WordCount fourni avec la distribution du cluster HDInsight, si bien que vous n'avez pas besoin d'écrire le vôtre. Il se situe à l'emplacement suivant : */example/jars/hadoop-examples.jar*. Pour savoir comment écrire votre propre tâche MapReduce, consultez la rubrique [Développement de programmes MapReduce en Java pour HDInsight](/fr-fr/documentation/articles/hdinsight-develop-deploy-java-mapreduce/).

* Un fichier d'entrée. Vous allez utiliser */example/data/gutenberg/davinci.txt* comme fichier d'entrée. Pour plus d'informations sur le téléchargement de fichiers, consultez la rubrique [Téléchargement de données vers HDInsight](/fr-fr/documentation/articles/hdinsight-upload-data/).
* Un dossier de fichiers de sortie. Vous allez utiliser */example/data/WordCountOutput* comme dossier de fichiers de sortie. Si ce dossier n'existe pas, le système le crée.

Le modèle d'URI pour accéder aux fichiers du stockage d'objets blob est le suivant :

    wasb[s]://<containername>@<storageaccountname>.blob.core.windows.net/<path>

> [WACOM.NOTE] Par défaut, le conteneur d'objets blob utilisé pour le système de fichiers par défaut a le même nom que le cluster HDInsight.

Le modèle d'URI offre à la fois un accès non chiffré avec le préfixe *wasb:* et un accès chiffré SSL avec WASBS. Dans la mesure du possible, nous vous recommandons d'utiliser wasbs, même lorsqu'il s'agit d'accéder à des données qui résident dans le même centre de données Azure.

Étant donné que HDInsight utilise un conteneur de stockage d'objets blob comme système de fichiers par défaut, vous pouvez faire référence à des fichiers et des répertoires à l'intérieur du système de fichiers par défaut à l'aide de chemins d'accès relatifs ou absolus.

Par exemple, pour accéder au fichier hadoop-examples.jar, vous pouvez utiliser l'une des options suivantes :

    ● wasb://<containername>@<storageaccountname>.blob.core.windows.net/example/jars/hadoop-examples.jar
    ● wasb:///example/jars/hadoop-examples.jar
    ● /example/jars/hadoop-examples.jar

L'utilisation du préfixe *wasb://* dans les chemins d'accès de ces fichiers est nécessaire pour indiquer que le stockage d'objets blob Azure est utilisé pour les fichiers d'entrée et de sortie. Le répertoire de sortie prend un chemin d'accès par défaut par rapport au dossier *wasb:///user/<username\>*.

Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight](/fr-fr/documentation/articles/hdinsight-use-blob-storage/).

**Pour exécuter l'exemple WordCount**

1.  Ouvrez **Azure PowerShell**. Pour savoir comment ouvrir la fenêtre de la console Azure PowerShell, consultez la rubrique [Installation et configuration d'Azure PowerShell](/fr-fr/documentation/articles/install-configure-powershell/).

2.  Exécutez les commandes suivantes pour définir les variables.
    
         $subscriptionName = "<SubscriptionName>" 
         $clusterName = "<HDInsightClusterName>"        

3.  Exécutez les commandes suivantes pour créer une définition de tâche MapReduce :

		# Define the MapReduce job
        $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"
    
    Le fichier hadoop-examples.jar est fourni avec la distribution du cluster HDInsight. Il existe deux arguments pour la tâche MapReduce. Le premier est le nom du fichier source ; le deuxième est le chemin d'accès au fichier de sortie. Le fichier source est fourni avec la distribution du cluster HDInsight, tandis que le chemin d'accès au fichier de sortie est créé au moment de l'exécution.

4.  Exécutez la commande suivante pour soumettre la tâche MapReduce :
		
		# Submit the job
        Select-AzureSubscription $subscriptionName
        $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 
    
    En plus de la définition de la tâche MapReduce, vous devez également indiquer le nom du cluster HDInsight sur lequel vous souhaitez exécuter la tâche MapReduce.
    
    *Start-AzureHDInsightJob* est un appel asynchrone. Pour vérifier que la tâche est terminée, utilisez la cmdlet *Wait-AzureHDInsightJob*.

5.  Exécutez la commande suivante pour vérifier que la tâche MapReduce est terminée :
    
         Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

6.  Exécutez la commande suivante pour vérifier si l'exécution de la tâche MapReduce génère des erreurs :

		# Get the job output
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError
    
    La capture d'écran ci-dessous présente la sortie d'une exécution réussie. En cas d'échec, elle aurait comporté des messages d'erreur.
    
    ![HDI.GettingStarted.RunMRJob](./media/hdinsight-get-started/HDI.GettingStarted.RunMRJob.png)

**Pour récupérer les résultats de la tâche MapReduce**

1.  Ouvrez **Azure PowerShell**. 
2.  Exécutez les commandes suivantes pour créer un dossier C:\Tutorials, puis remplacez le répertoire par le dossier suivant :
    
         mkdir \Tutorials
         cd \Tutorials
    
    Le répertoire Azure Powershell par défaut est *C:\Windows\System32\WindowsPowerShell\v1.0*. Par défaut, vous ne
    disposez pas de l'autorisation d'accès en écriture sur ce dossier. Vous devez remplacer le répertoire par un dossier sur lequel vous disposez d'un accès en écriture.

3.  Définissez les trois variables dans les commandes suivantes, puis exécutez-les :
    
         $subscriptionName = "<SubscriptionName>"       
         $storageAccountName = "<StorageAccountName>"   
         $containerName = "<ContainerName>"			   
    
    Le compte de stockage Azure est celui que vous avez créé plus tôt dans ce didacticiel. Il sert à héberger le conteneur d'objets blob utilisé comme système de fichiers par défaut du cluster HDInsight. Le conteneur de stockage d'objets blob et le cluster HDInsight partagent généralement le même nom, à moins que vous spécifiiez un nom différent lors de la configuration du cluster.

4.  Exécutez les commandes suivantes pour créer un objet de contexte de stockage Azure :

		# Create the storage account context object
        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  
    
    *Select-AzureSubscription* sert à définir l'abonnement actif dans le cas où vous disposez de plusieurs abonnements et que l'abonnement par défaut n'est pas celui qui doit être utilisé.

5.  Exécutez la commande suivante pour télécharger la sortie de la tâche MapReduce du conteneur d'objets blob vers la station de travail :


		# Download the job output to the workstation
        Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force
    
    Le dossier *example/data/WordCountOutput* est le dossier de sortie spécifié lors de l'exécution de la tâche MapReduce. *part-r-00000* est le nom de fichier par défaut pour la sortie de la tâche MapReduce. Le fichier est téléchargé dans la même structure de dossiers que le dossier local. Par exemple, dans la capture d'écran suivante, le dossier actif est le dossier racine C. Le fichier est donc téléchargé dans le dossier *C:\example\data\WordCountOutput\*.

6.  Exécutez la commande suivante pour imprimer le fichier de sortie de la tâche MapReduce :
    
         cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"
    
    ![HDI.GettingStarted.MRJobOutput](./media/hdinsight-get-started/HDI.GettingStarted.MRJobOutput.png)
    
    La tâche MapReduce produit un fichier nommé *part-r-00000* avec les mots et les décomptes. Le script utilise la commande findstr pour répertorier tous les mots contenant *"there"*.

> [WACOM.NOTE] Si vous ouvrez *./example/data/WordCountOutput/part-r-00000*, sortie de plusieurs lignes résultant d'une tâche MapReduce, dans le Bloc-notes, vous remarquerez que les sauts de ligne ne sont pas correctement rendus. Ceci est normal.

## <a  name="powerquery" ></a>Connexion aux outils décisionnels Microsoft

Le complément Power Query pour Excel peut être utilisé pour exporter une sortie de HDInsight vers Excel qui, combiné aux outils décisionnels (BI) Microsoft, permet de mieux traiter ou afficher les résultats. Lorsque vous créez un cluster HDInsight, un conteneur par défaut est créé sous le même nom que le cluster dans le compte de stockage qui lui est associé au moment d'être créé. Il est rempli automatiquement par un ensemble de fichiers. L'un de ces fichiers est un exemple de tableau Hive. Dans cette section, nous allons vous montrer comment importer les données contenues dans cette table dans Excel à des fins de consultation et de traitement supplémentaire.

Vous devez avoir installé Excel 2010 ou 2013 pour mener à bien cette partie du didacticiel. Nous allons ici importer la table Hive par défaut fournie avec HDInsight.

**Pour télécharger Microsoft PowerQuery pour Excel**

* Téléchargez Microsoft Power Query pour Excel depuis le [Centre de
  téléchargement Microsoft][7] et installez-le.

**Pour importer des données HDInsight**

1.  Ouvrez Excel et créez un classeur vierge. 
2.  Cliquez dans le menu **Power Query**, cliquez sur **À partir d'autres sources**, puis sur **Depuis Microsoft Windows Azure HDInsight**.
    
    ![HDI.GettingStarted.PowerQuery.ImportData](./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png)

3.  Entrez le **nom de compte** du compte de stockage d'objets blob Azure associé à votre cluster, puis cliquez sur **OK**. Il s'agit du compte de stockage que vous avez créé plus tôt dans le didacticiel.
4.  Entrez la **clé de compte** du compte de stockage d'objets blob Azure, puis cliquez sur **Enregistrer**.
5.  Dans le volet Navigateur situé à droite, double-cliquez sur le nom du conteneur de stockage d'objets blob. Par défaut, le nom du conteneur est identique à celui du cluster.

6.  Repérez **part-r-00000** dans la colonne **Name** (le chemin d'accès est *.../example/data/WordCountOutput*), puis cliquez sur **Binary** à gauche de **part-r-00000**.
    
    ![HDI.GettingStarted.PowerQuery.ImportData2](./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png)

7.  Cliquez avec le bouton droit sur **Column1.1**, puis sélectionnez **Renommer**.
8.  Remplacez le nom par **Word**. 
9.  Répétez le processus pour renommer **Column1.2** en **Count**.
    
    ![HDI.GettingStarted.PowerQuery.ImportData3](./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData3.png)

10. Cliquez sur **Appliquer et fermer** dans le coin supérieur gauche. La requête importe la table Hive dans Excel.

## <a  name="nextsteps" ></a>Étapes suivantes

Dans ce didacticiel, vous avez appris à configurer un cluster avec HDInsight, à exécuter une tâche MapReduce sur ce dernier et à importer les résultats dans Excel, où ils peuvent faire l'objet d'un traitement plus poussé et être affichés sous forme de graphique à l'aide des outils BI. Pour en savoir plus, consultez les articles suivants :

* [Prise en main des clusters Hadoop 2.2 avec
  HDInsight](/fr-fr/documentation/articles/hdinsight-get-started-30/)
* [Prise en main de l'émulateur
  HDInsight](/fr-fr/documentation/articles/hdinsight-get-started-emulator/)
* [Utilisation du stockage d'objets blob Azure avec
  HDInsight](/fr-fr/documentation/articles/hdinsight-use-blob-storage/)
* [Administration de HDInsight à l'aide de
  PowerShell](/fr-fr/documentation/articles/hdinsight-administer-use-powershell/)
* [Téléchargement de données vers
  HDInsight](/fr-fr/documentation/articles/hdinsight-upload-data/)
* [Utilisation de MapReduce avec
  HDInsight](/fr-fr/documentation/articles/hdinsight-use-mapreduce)
* [Utilisation de Hive avec
  HDInsight](/fr-fr/documentation/articles/hdinsight-use-hive/)
* [Utilisation de Pig avec
  HDInsight](/fr-fr/documentation/articles/hdinsight-use-pig/)
* [Utilisation d'Oozie avec
  HDInsight](/fr-fr/documentation/articles/hdinsight-use-oozie/)
* [Développement de programmes de diffusion en continu Hadoop en C# pour
  HDInsight](/fr-fr/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
* [Développement de programmes MapReduce en Java pour
  HDInsight](/fr-fr/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)



[hdinsight-get-started-30]: /fr-fr/documentation/articles/hdinsight-get-started-30/
[hdinsight-provision]: /fr-fr/documentation/articles/hdinsight-provision-clusters/
[hdinsight-admin-powershell]: /fr-fr/documentation/articles/hdinsight-administer-use-powershell/
[hdinsight-upload-data]: /fr-fr/documentation/articles/hdinsight-upload-data/
[hdinsight-mapreduce]: /fr-fr/documentation/articles/hdinsight-use-mapreduce
[hdinsight-hive]: /fr-fr/documentation/articles/hdinsight-use-hive/
[hdinsight-pig]: /fr-fr/documentation/articles/hdinsight-use-pig/
[hdinsight-oozie]: /fr-fr/documentation/articles/hdinsight-use-oozie/
[hdinsight-storage]: /fr-fr/documentation/articles/hdinsight-use-blob-storage/
[hdinsight-emulator]: /fr-fr/documentation/articles/hdinsight-get-started-emulator/
[hdinsight-develop-streaming]: /fr-fr/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/
[hdinsight-develop-mapreduce]: /fr-fr/documentation/articles/hdinsight-develop-deploy-java-mapreduce/

[azure-purchase-options]: https://www.windowsazure.com/en-us/pricing/purchase-options/
[azure-member-offers]: https://www.windowsazure.com/en-us/pricing/member-offers/
[azure-free-trial]: https://www.windowsazure.com/en-us/pricing/free-trial/
[azure-management-portal]: https://manage.windowsazure.com/
[azure-create-storageaccount]: /fr-fr/documentation/articles/storage-create-storage-account/ 

[apache-hadoop]: http://hadoop.apache.org/

[powershell-download]: http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409
[powershell-install-configure]: /fr-fr/documentation/articles/install-configure-powershell/
[powershell-open]: /fr-fr/documentation/articles/install-configure-powershell/#install

[image-hdi-storageaccount-quickcreate]: ./media/hdinsight-get-started/HDI.StorageAccount.QuickCreate.png
[image-hdi-clusterstatus]: ./media/hdinsight-get-started/HDI.ClusterStatus.png
[image-hdi-quickcreatecluster]: ./media/hdinsight-get-started/HDI.QuickCreateCluster.png
[image-hdi-wordcountdiagram]: ./media/hdinsight-get-started/HDI.WordCountDiagram.gif
[image-hdi-gettingstarted-mrjoboutput]: ./media/hdinsight-get-started/HDI.GettingStarted.MRJobOutput.png
[image-hdi-gettingstarted-runmrjob]: ./media/hdinsight-get-started/HDI.GettingStarted.RunMRJob.png
[image-hdi-gettingstarted-powerquery-importdata]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData.png
[image-hdi-gettingstarted-powerquery-importdata2]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData2.png
[image-hdi-gettingstarted-powerquery-importdata3]: ./media/hdinsight-get-started/HDI.GettingStarted.PowerQuery.ImportData3.png
