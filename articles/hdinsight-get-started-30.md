<properties linkid="manage-services-hdinsight-get-started-hdinsight" urlDisplayName="Get Started" pageTitle="Get started using Hadoop 2.2 clusters with HDInsight | Azure" metaKeywords="" description="Get started using Hadoop 2.2 clusters with HDInsight, a big data solution. Learn how to provision clusters, run MapReduce jobs, and output data to Excel for analysis." metaCanonical="" services="hdinsight" documentationCenter="" title="Get started using Azure HDInsight" authors="jgao" solutions="" manager="paulettm" editor="cgronlun" />

Prise en main des clusters Hadoop 2.2 avec HDInsight
====================================================

HDInsight rend [Apache Hadoop](http://hadoop.apache.org/) accessible en tant que service dans le cloud. L'infrastructure logicielle MapReduce devient ainsi disponible dans un environnement Azure plus simple, plus extensible et plus économique. HDInsight propose également une approche économique de la gestion et du stockage de données à l'aide du stockage d'objets blob Azure.

Dans ce didacticiel, vous allez approvisionner un cluster HDInsight via le portail de gestion Azure, envoyer une tâche MapReduce Hadoop à l'aide de PowerShell, puis importer les données de sortie de la tâche MapReduce dans Excel pour examen.

> [WACOM.NOTE] Ce didacticiel couvre l'utilisation de clusters Hadoop 2.2 sous HDInsight. Pour accéder au didacticiel faisant appel à des clusters Hadoop 1.2 sous HDInsight, consultez la rubrique [Prise en main d'Azure HDInsight](/en-us/documentation/articles/hdinsight-get-started/).

> [WACOM.NOTE] La syntaxe *asv://* n'est pas prise en charge dans les clusters HDInsight version 3.0 et ne sera pas prise en charge dans les versions ultérieures. Vous devez plutôt utiliser la syntaxe *wasb://*.

Parallèlement à la mise à disposition générale d'Azure HDInsight, Microsoft a également publié l'émulateur HDInsight pour Azure, connu auparavant sous le nom de Microsoft HDInsight Developer Preview. Ce produit cible les scénarios de développement et, à ce titre, ne prend en charge que les déploiements sur nœud unique. Pour utiliser l'émulateur HDInsight, consultez la rubrique [Prise en main de l'émulateur HDInsight](/en-us/documentation/articles/hdinsight-get-started-emulator/).

**Conditions préalables :**

Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

-   Un abonnement Azure. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les [formules d'abonnement](https://www.windowsazure.com/en-us/pricing/purchase-options/), les [offres spéciales membres](https://www.windowsazure.com/en-us/pricing/member-offers/) ou la [version d'évaluation gratuite](https://www.windowsazure.com/en-us/pricing/free-trial/).
-   Un ordinateur exécutant Windows 8, Windows 7, Windows Server 2012 ou Windows Server 2008 R2. Cet ordinateur sera utilisé pour envoyer les tâches MapReduce.
-   Office Professionnel Plus 2013, Office 365 ProPlus, l'édition autonome d'Excel 2013 ou Office Professionnel Plus 2010.

**Durée de réalisation estimée :** 30 minutes

Dans ce didacticiel
-------------------

-   [Configuration d'un environnement local pour l'exécution de PowerShell](#setup)
-   [Approvisionnement d'un cluster HDInsight](#provision)
-   [Exécution d'une tâche MapReduce WordCount](#sample)
-   [Connexion aux outils décisionnels Microsoft](#powerquery)
-   [Étapes suivantes](#nextsteps)

Configuration d'un environnement local pour l'exécution de PowerShell
---------------------------------------------------------------------

Il existe plusieurs façons d'envoyer des tâches MapReduce vers HDInsight. Dans ce didacticiel, nous allons utiliser Azure PowerShell. Pour installer Azure PowerShell, exécutez [Microsoft Web Platform Installer](http://go.microsoft.com/fwlink/p/?linkid=320376&clcid=0x409). Lorsque vous y êtes invité, cliquez sur **Exécuter**, sur **Installer**, puis suivez les instructions. Pour plus d'informations, consultez la rubrique [Installation et configuration d'Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

Les cmdlets PowerShell requièrent vos informations d'abonnement pour pouvoir gérer vos services.

**Pour vous connecter à votre abonnement à l'aide d'Azure AD**

1.  Ouvrez la console Azure PowerShell, comme indiqué dans la rubrique [Installation d'Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/#install).
2.  Exécutez la commande suivante :

        Add-AzureAccount

3.  Dans la fenêtre, tapez l'adresse de messagerie et le mot de passe associés à votre compte. Azure authentifie et enregistre les informations d'identification, puis ferme la fenêtre.

Vous pouvez également vous connecter à votre abonnement en employant la méthode par certificat. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

Approvisionnement d'un cluster HDInsight
----------------------------------------

Le processus d'approvisionnement de HDInsight nécessite l'utilisation d'un compte Azure Storage comme système de fichiers par défaut. Ce compte de stockage doit se situer dans le même centre de données que les ressources de calcul HDInsight. Pour l'heure, les clusters HDInsight ne peuvent être approvisionnés que dans les centres de données suivants :

-   Asie du Sud-Est
-   Europe du Nord
-   Europe de l'Ouest
-   Est des États-Unis
-   Ouest des États-Unis

Vous devez choisir l'un de ces cinq centres de données pour votre compte Azure Storage.

**Pour créer un compte Azure Storage**

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/).
2.  Cliquez sur **NOUVEAU** dans le coin inférieur gauche, pointez sur **SERVICES DE DONNÉES**, sur **STOCKAGE**, puis cliquez sur **CRÉATION RAPIDE**.

    ![HDI.StorageAccount.QuickCreate](./media/hdinsight-get-started-3.0/HDI.StorageAccount.QuickCreate.png)

3.  Entrez l'**URL**, l'**emplacement** et la **réplication**, puis cliquez sur **CRÉER UN COMPTE DE STOCKAGE**. Les groupes d'affinités ne sont pas pris en charge. Le nouveau compte de stockage figure dès lors dans la liste de stockage.
4.  Attendez que le **statut** du nouveau compte de stockage passe à **Online**.
5.  Cliquez sur le nouveau compte de stockage dans la liste pour le sélectionner.
6.  Cliquez sur **GÉRER LES CLÉS D'ACCÈS** dans le bas de la page.
7.  Notez le **nom du compte de stockage** et la **clé d'accès primaire**. Vous en aurez besoin plus loin dans le didacticiel.

Pour obtenir des instructions détaillées, consultez les rubriques
[Création d'un compte de stockage](/en-us/documentation/articles/storage-create-storage-account/) et [Utilisation du stockage d'objets blob Azure avec HDInsight](/en-us/documentation/articles/hdinsight-use-blob-storage/).

L'approvisionnement de clusters HDInsight 3.0 n'est prise en charge actuellement qu'à l'aide de l'option de création personnalisée.

**Pour approvisionner un cluster HDInsight**

1.  Connectez-vous au [portail de gestion Azure](https://manage.windowsazure.com/).

2.  Cliquez sur **HDINSIGHT** à gauche pour afficher les clusters HDInsight sous votre compte. Dans la capture d'écran suivante, il n'existe aucun cluster HDInsight.

    ![HDI.ClusterStatus](./media/hdinsight-get-started-3.0/HDI.ClusterStatus.png)

3.  Dans la partie inférieure gauche de l'écran, cliquez sur **NOUVEAU**, sur **SERVICES DE DONNÉES**, sur **HDINSIGHT**, puis sur **CRÉATION PERSONNALISÉE**.

    ![HDI.CustomCreateCluster](./media/hdinsight-get-started-3.0/HDI.CustomCreateCluster.png)

4.  Dans l'onglet Détails du cluster, entrez ou sélectionnez les valeurs suivantes :

	<table border="1">
	<tr><th>Nom</th><th>Valeur</th></tr>
	<tr><td><strong>Nom du cluster</strong></td><td>Nom du cluster.</td></tr>
	<tr><td><strong>Nœuds de données</strong></td><td>Nombre de nœuds de données que vous souhaitez déployer. À des fins de test, créez un cluster à nœud unique.  <br />La limite de taille de cluster varie pour les abonnements Azure. Contactez le support de facturation Azure pour augmenter la limite.</td></tr>
	<tr><td><strong>Version de HDInsight</strong></td><td>Choisissez <strong>3.0</strong> pour créer un cluster Hadoop 2.2 dans HDInsight.</td></tr>
	<tr><td><strong>Région</strong></td><td>Choisissez la même région que pour le compte de stockage que vous avez créé dans la dernière procédure. HDInsight requiert en effet que le compte de stockage soit situé dans la même région. Plus loin dans la configuration, vous ne pourrez choisir qu'un compte de stockage situé dans la région spécifiée ici.
	</td></tr>
	</table>

5.  Cliquez sur la flèche vers la droite dans le coin inférieur droit pour configurer l'utilisateur du cluster.
6.  Dans l'onglet Configuration de l'utilisateur du cluster, entrez un **nom d'utilisateur** et un **mot de passe** pour le compte d'utilisateur de cluster HDInsight. Outre ce compte, vous pouvez créer un compte utilisateur RDP après l'approvisionnement du cluster, de sorte que vous pouvez activer un Bureau à distance dans le cluster. Pour obtenir des instructions, consultez la rubrique [Administration de HDInsight à l'aide du portail de gestion](/en-us/documentation/articles/hdinsight-administer-use-management-portal/).
7.  Cliquez sur la flèche vers la droite dans le coin inférieur droit pour configurer le compte de stockage.
8.  Dans l'onglet Compte de stockage, entrez ou sélectionnez les valeurs suivantes :

	<table border="1">
	<tr><th>Nom</th><th>Valeur</th></tr>
	<tr><td>Compte de stockage</td><td>Sélectionnez <strong>Utiliser le stockage existant</strong>. Vous pouvez également utiliser le portail de gestion pour créer un nouveau compte de stockage, si vous n'en avez pas créé.</td></tr>
	<tr><td>Nom du compte</td><td>Spécifiez le compte de stockage que vous avez créé dans la dernière procédure de ce didacticiel. Notez que seuls les comptes de stockage de la même région s'affichent dans la zone de liste.</td></tr>
	<tr><td>Conteneur par défaut</td><td>Sélectionnez <strong>Créer un conteneur par défaut</strong>. Lorsque cette option est sélectionnée, le nom du conteneur par défaut est identique à celui du cluster.</td></tr>
	<tr><td>Comptes de stockage supplémentaires</td><td>Sélectionnez <strong>0</strong>. Vous pouvez connecter le cluster à 7 autres comptes de stockage maximum.</td></tr>
	</table>

9.  Cliquez sur l'icône en forme de coche dans le coin inférieur droit pour créer le cluster. À l'issue du processus d'approvisionnement, la colonne de statut indique **En cours d'exécution**.

Exécution d'une tâche MapReduce WordCount
-----------------------------------------

Maintenant que vous avez approvisionné un cluster HDInsight, la prochaine étape consiste à exécuter une tâche MapReduce pour compter le nombre de mots contenus dans un fichier texte.

L'exécution d'une tâche MapReduce nécessite les éléments suivants :

-   Un programme MapReduce. Dans ce didacticiel, vous allez utiliser l'exemple WordCount fourni avec la distribution du cluster HDInsight, si bien que vous n'avez pas besoin d'écrire le vôtre. Il se situe à l'emplacement suivant : */example/jars/hadoop-mapreduce-examples.jar*. Pour savoir comment écrire votre propre tâche MapReduce, consultez la rubrique [Développement de programmes MapReduce en Java pour HDInsight](/en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/).

-   Un fichier d'entrée. Vous allez utiliser */example/data/gutenberg/davinci.txt* comme fichier d'entrée. Pour plus d'informations sur le téléchargement de fichiers, consultez la rubrique [Téléchargement de données vers HDInsight](/en-us/documentation/articles/hdinsight-upload-data/).
-   Un dossier de fichiers de sortie. Vous allez utiliser */example/data/WordCountOutput* comme dossier de fichiers de sortie. Si ce dossier n'existe pas, le système le crée.

Le modèle d'URI pour accéder aux fichiers du stockage d'objets blob est le suivant :

    wasb[s]://<containername>@<storageaccountname>.blob.core.windows.net/<path>

> [WACOM.NOTE] Par défaut, le conteneur d'objets blob utilisé pour le système de fichiers par défaut a le même nom que le cluster HDInsight.

Le modèle d'URI offre à la fois un accès non chiffré avec le préfixe *wasb:* et un accès chiffré SSL avec wasbs. Dans la mesure du possible, nous vous recommandons d'utiliser wasbs, même lorsqu'il s'agit d'accéder à des données qui résident dans le même centre de données Azure.

Étant donné que HDInsight utilise un conteneur de stockage d'objets blob comme système de fichiers par défaut, vous pouvez faire référence à des fichiers et des répertoires à l'intérieur du système de fichiers par défaut à l'aide de chemins d'accès relatifs ou absolus.

Par exemple, pour accéder au fichier hadoop-mapreduce-examples.jar, vous pouvez utiliser l'une des options suivantes :

    ● wasb://<containername>@<storageaccountname>.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    ● wasb:///example/jars/hadoop-examples.jar
    ● /example/jars/hadoop-mapreduce-examples.jar

L'utilisation du préfixe *wasb://* dans les chemins d'accès de ces fichiers est nécessaire pour indiquer que le stockage d'objets blob Azure est utilisé pour les fichiers d'entrée et de sortie. Le répertoire de sortie prend un chemin d'accès par défaut par rapport au dossier *wasb:///user/&lt;username\>*.

Pour plus d'informations, consultez la rubrique [Utilisation du stockage d'objets blob Azure avec HDInsight](/en-us/documentation/articles/hdinsight-use-blob-storage/).

**Pour exécuter l'exemple WordCount**

1.  Ouvrez **Azure PowerShell**. Pour savoir comment ouvrir la fenêtre de la console Azure PowerShell, consultez la rubrique [Installation et configuration d'Azure PowerShell](/en-us/documentation/articles/install-configure-powershell/).

2.  Exécutez les commandes suivantes pour définir les variables.

        $subscriptionName = "<SubscriptionName>" 
        $clusterName = "<HDInsightClusterName>"        

3.  Exécutez les commandes suivantes pour créer une définition de tâche MapReduce :

        # Définition de la tâche MapReduce
        $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-mapreduce-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    Le fichier hadoop-mapreduce-examples.jar est fourni avec la distribution du cluster HDInsight. Il existe deux arguments pour la tâche MapReduce. Le premier est le nom du fichier source ; le deuxième est le chemin d'accès au fichier de sortie. Le fichier source est fourni avec la distribution du cluster HDInsight, tandis que le chemin d'accès au fichier de sortie est créé au moment de l'exécution.

4.  Exécutez la commande suivante pour soumettre la tâche MapReduce :

        # Envoi de la tâche
        Select-AzureSubscription $subscriptionName
        $wordCountJob = Start-AzureHDInsightJob -Cluster $clusterName -JobDefinition $wordCountJobDefinition 

    En plus de la définition de la tâche MapReduce, vous devez également indiquer le nom du cluster HDInsight sur lequel vous souhaitez exécuter la tâche MapReduce.

    *Start-AzureHDInsightJob* est un appel asynchrone. Pour vérifier que la tâche est terminée, utilisez la cmdlet *Wait-AzureHDInsightJob*.

5.  Exécutez la commande suivante pour vérifier que la tâche MapReduce est terminée :

        Wait-AzureHDInsightJob -Job $wordCountJob -WaitTimeoutInSeconds 3600 

6.  Exécutez la commande suivante pour vérifier si l'exécution de la tâche MapReduce génère des erreurs :

        # Réception du résultat de la tâche
        Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $wordCountJob.JobId -StandardError 

    La capture d'écran ci-dessous présente la sortie d'une exécution réussie. En cas d'échec, elle aurait comporté des messages d'erreur.

    ![HDI.GettingStarted.RunMRJob](./media/hdinsight-get-started-3.0/HDI.GettingStarted.RunMRJob.png)

**Pour récupérer les résultats de la tâche MapReduce**

1.  Ouvrez **Azure PowerShell**.
2.  Exécutez les commandes suivantes pour créer un dossier C:\\Tutorials, puis remplacez le répertoire par le dossier suivant :

        mkdir \Tutorials
        cd \Tutorials

    Le répertoire Azure Powershell par défaut est *C:\\Windows\\System32\\WindowsPowerShell\\v1.0*. Par défaut, vous ne disposez pas de l'autorisation d'accès en écriture sur ce dossier. Vous devez remplacer le répertoire par un dossier sur lequel vous disposez d'un accès en écriture.

3.  Définissez les trois variables dans les commandes suivantes, puis exécutez-les :

        $subscriptionName = "<SubscriptionName>"       
        $storageAccountName = "<StorageAccountName>"   
        $containerName = "<ContainerName>"              

    Le compte Azure Storage est celui que vous avez créé plus tôt dans ce didacticiel. Il sert à héberger le conteneur d'objets blob utilisé comme système de fichiers par défaut du cluster HDInsight. Le conteneur de stockage d'objets blob et le cluster HDInsight partagent généralement le même nom, à moins que vous ne spécifiiez un nom différent lors de l'approvisionnement du cluster.

4.  Exécutez les commandes suivantes pour créer un objet de contexte de stockage Azure :

        # Création de l'objet de contexte de compte de stockage
        Select-AzureSubscription $subscriptionName
        $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
        $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    *Select-AzureSubscription* sert à définir l'abonnement actif dans le cas où vous disposez de plusieurs abonnements et que l'abonnement par défaut n'est pas celui qui doit être utilisé.

5.  Exécutez la commande suivante pour télécharger la sortie de la tâche MapReduce du conteneur d'objets blob vers la station de travail :

        # Téléchargement du résultat de la tâche vers le poste de travail
        Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    Le dossier *example/data/WordCountOutput* est le dossier de sortie spécifié lors de l'exécution de la tâche MapReduce. *part-r-00000* est le nom de fichier par défaut pour la sortie de la tâche MapReduce. Le fichier est téléchargé dans la même structure de dossiers que le dossier local. Par exemple, dans la capture d'écran suivante, le dossier actif est le dossier racine C. Le fichier est donc téléchargé dans le dossier *C:\\example\\data\\WordCountOutput\\*.

6.  Exécutez la commande suivante pour imprimer le fichier de sortie de la tâche MapReduce :

        cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

    ![HDI.GettingStarted.MRJobOutput](./media/hdinsight-get-started-3.0/HDI.GettingStarted.MRJobOutput.png)

    La tâche MapReduce produit un fichier nommé *part-r-00000* avec les mots et les décomptes. Le script utilise la commande findstr pour répertorier tous les mots contenant « *there* ».

> [WACOM.NOTE] Si vous ouvrez *./example/data/WordCountOutput/part-r-00000*, sortie de plusieurs lignes résultant d'une tâche MapReduce, dans le Bloc-notes, vous remarquerez que les sauts de ligne ne sont pas correctement rendus. Ceci est normal.

Connexion aux outils décisionnels Microsoft
-------------------------------------------

Le complément Power Query pour Excel peut être utilisé pour exporter une sortie de HDInsight vers Excel qui, combiné aux outils décisionnels (BI) Microsoft, permet de mieux traiter ou afficher les résultats. Lorsque vous créez un cluster HDInsight, un conteneur par défaut est créé sous le même nom que le cluster dans le compte de stockage qui lui est associé au moment d'être créé. Il est rempli automatiquement par un ensemble de fichiers. L'un de ces fichiers est un exemple de tableau Hive. Dans cette section, nous allons vous montrer comment importer les données contenues dans cette table dans Excel à des fins de consultation et de traitement supplémentaire.

Vous devez avoir installé Excel 2010 ou 2013 pour mener à bien cette partie du didacticiel. Nous allons ici importer la table Hive par défaut fournie avec HDInsight.

**Pour télécharger Microsoft PowerQuery pour Excel**

-   Téléchargez Microsoft Power Query pour Excel depuis le [Centre de téléchargement Microsoft](http://www.microsoft.com/en-us/download/details.aspx?id=39379) et installez-le.

**Pour importer des données HDInsight**

1.  Ouvrez Excel et créez un classeur vierge.
2.  Cliquez dans le menu **Power Query**, cliquez sur **À partir d'autres sources**, puis sur **Depuis Microsoft Windows Azure HDInsight**.

    ![HDI.GettingStarted.PowerQuery.ImportData](./media/hdinsight-get-started-3.0/HDI.GettingStarted.PowerQuery.ImportData.png)

3.  Entrez le **nom de compte** du compte de stockage d'objets blob Azure associé à votre cluster, puis cliquez sur **OK**. Il s'agit du compte de stockage que vous avez créé plus tôt dans le didacticiel.
4.  Entrez la **clé de compte** du compte de stockage d'objets blob Azure, puis cliquez sur **Enregistrer**.
5.  Dans le volet Navigateur situé à droite, double-cliquez sur le nom du conteneur de stockage d'objets blob. Par défaut, le nom du conteneur est identique à celui du cluster.

6.  Repérez **part-r-00000** dans la colonne **Name** (le chemin d'accès est *.../example/data/WordCountOutput*), puis cliquez sur **Binary** à gauche de **part-r-00000**.

    ![HDI.GettingStarted.PowerQuery.ImportData2](./media/hdinsight-get-started-3.0/HDI.GettingStarted.PowerQuery.ImportData2.png)

7.  Cliquez avec le bouton droit sur **Column1.1**, puis sélectionnez **Renommer**.
8.  Remplacez le nom par **Word**.
9.  Répétez le processus pour renommer **Column1.2** en **Count**.

    ![HDI.GettingStarted.PowerQuery.ImportData3](./media/hdinsight-get-started-3.0/HDI.GettingStarted.PowerQuery.ImportData3.png)

10. Cliquez sur **Appliquer et fermer** dans le coin supérieur gauche. La requête importe la table Hive dans Excel.

Étapes suivantes
----------------

Dans ce didacticiel, vous avez appris à approvisionner un cluster avec HDInsight, à exécuter une tâche MapReduce sur ce dernier et à importer les résultats dans Excel, où ils peuvent faire l'objet d'un traitement plus poussé et être affichés sous forme de graphique à l'aide des outils BI. Pour en savoir plus, consultez les articles suivants :

-   [Prise en main de HDInsight](/en-us/documentation/articles/hdinsight-get-started/)
-   [Prise en main de l'émulateur HDInsight](/en-us/documentation/articles/hdinsight-get-started-emulator/)
-   [Utilisation du stockage d'objets blob Azure avec HDInsight](/en-us/documentation/articles/hdinsight-use-blob-storage/)
-   [Administration de HDInsight à l'aide de PowerShell](/en-us/documentation/articles/hdinsight-administer-use-powershell/)
-   [Téléchargement de données vers HDInsight](/en-us/documentation/articles/hdinsight-upload-data/)
-   [Utilisation de Hive avec HDInsight](/en-us/documentation/articles/hdinsight-use-hive/)
-   [Utilisation de Pig avec HDInsight](/en-us/documentation/articles/hdinsight-use-pig/)
-   [Utilisation d'Oozie avec HDInsight](/en-us/documentation/articles/hdinsight-use-oozie/)
-   [Développement de programmes MapReduce de diffusion en continu Hadoop en C\# pour HDInsight](/en-us/documentation/articles/hdinsight-hadoop-develop-deploy-streaming-jobs/)
-   [Développement de programmes MapReduce en Java pour HDInsight](/en-us/documentation/articles/hdinsight-develop-deploy-java-mapreduce/)

