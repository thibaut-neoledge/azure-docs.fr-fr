<properties linkid="manage-services-hdinsight-administer-hdinsight-using-powershell" urlDisplayName="HDInsight Administration" pageTitle="Administer HDInsight using PowerShell | Azure" metaKeywords="hdinsight, hdinsight administration, hdinsight administration azure" description="Learn how to perform administrative tasks for the HDInsight clusters using PowerShell." services="hdinsight" umbracoNaviHide="0" disqusComments="1" editor="cgronlun" manager="paulettm" title="Administer HDInsight using PowerShell" authors="bradsev" />

Administration de HDInsight à l'aide de PowerShell
==================================================

Azure PowerShell est un environnement de création de scripts vous permettant de contrôler et d'automatiser le déploiement et la gestion de vos charges de travail dans Azure. Cet article vous indique comment gérer les clusters HDInsignt en utilisant la console locale Azure PowerShell par le biais de Windows PowerShell. Pour la liste des cmdlets PowerShell pour HDInsight, consultez la rubrique [Référence des cmdlets HDInsight](http://msdn.microsoft.com/fr-fr/library/windowsazure/dn479228.aspx).

**Configuration requise :**

Avant de commencer la lecture de cet article, vous devez disposer des éléments suivants :

-   Un abonnement Azure. Azure est une plateforme disponible par abonnement. Les cmdlets HDInsight PowerShell permettent d'effectuer les tâches associées à votre abonnement. Pour plus d'informations sur la façon de se procurer un abonnement, consultez les [formules d'abonnement](https://www.windowsazure.com/en-us/pricing/purchase-options/), les [offres spéciales membres](https://www.windowsazure.com/en-us/pricing/member-offers/) ou la [version d'évaluation gratuite](https://www.windowsazure.com/en-us/pricing/free-trial/).

-   Un poste de travail sur lequel est installé Azure PowerShell. Pour obtenir des instructions, consultez la rubrique [Installation et configuration d'Azure PowerShell](/fr-fr/documentation/articles/install-configure-powershell/).

Dans cet article
----------------

-   [Approvisionnement d'un cluster](#provision)
-   [Énumération et affichage des clusters](#listshow)
-   [Suppression d'un cluster](#delete)
-   [Octroi/révocation de l'accès aux services HTTP](#httpservices)
-   [Envoi de tâches MapReduce](#mapreduce)
-   [Envoi de tâches Hive](#hive)
-   [Téléchargement de données vers le stockage d'objets blob](#upload)
-   [Téléchargement de données de sortie MapReduce à partir du stockage d'objets blob](#download)

Approvisionnement d'un cluster HDInsight
----------------------------------------

HDInsight utilise un conteneur de stockage d'objets blob Azure comme système de fichiers par défaut. Un compte de stockage Azure et un conteneur de stockage sont nécessaires avant de pouvoir créer un cluster HDInsight.

**Pour créer un compte de stockage Azure**

Après avoir importé le fichier de paramètres de publication, vous pouvez utiliser la commande suivante pour créer un compte de stockage :

    # Créez un compte de stockage Azure
    $storageAccountName = "<StorageAcccountName>"
    $location = "<Microsoft data center>"           # Par exemple, « West US » (Ouest des États-Unis)

    New-AzureStorageAccount -StorageAccountName $storageAccountName -Location $location

> [WACOM.NOTE] Ce compte de stockage doit se situer dans le même centre de données que le cluster HDInsight. Pour l'heure, les clusters HDInsight ne peuvent être approvisionnés que dans les centres de données suivants :

><ul>
<li>Asie du Sud-Est
<li>Europe du Nord
<li>Europe de l'Ouest
<li>Est des États-Unis
<li>Ouest des États-Unis
</ul\>

Pour plus d'informations sur la création d'un compte de stockage Azure au moyen du portail de gestion, consultez la rubrique [Création d'un compte de stockage](/en-us/manage/services/storage/how-to-create-a-storage-account/).

Si vous disposez déjà d'un compte de stockage mais que vous ne connaissez ni le nom ni la clé du compte, vous pouvez utiliser les commandes suivantes pour récupérer les informations :

    # Affichez la liste des comptes de stockage pour l'abonnement actif
    Get-AzureStorageAccount
    # Affichez la liste des clés d'un compte de stockage
    Get-AzureStorageKey <StorageAccountName>

Pour plus d'informations sur l'obtention d'informations au moyen du portail de gestion, consultez la section *Affichage, copie et régénération des clés d'accès de stockage* de la rubrique [Gestion des comptes de stockage](/en-us/manage/services/storage/how-to-manage-a-storage-account/).

**Pour créer un conteneur de stockage Azure**

PowerShell n'est pas en mesure de créer un conteneur de stockage d'objets blob lors de l'approvisionnement de HDInsight. Vous pouvez en créer un à l'aide du script suivant :

    $storageAccountName = "<StorageAccountName>"
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $containerName="<ContainerName>"

    # Créez un objet de contexte de stockage
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName 
                                           -StorageAccountKey $storageAccountKey  

    # Créez un conteneur de stockage d'objets blob
    New-AzureStorageContainer -Name $containerName -Context $destContext

**Pour approvisionner un cluster**

Une fois que le compte de stockage et le conteneur d'objets blob sont prêts, vous êtes prêt à créer un cluster.

    $storageAccountName = "<StorageAccountName>"
    $containerName = "<ContainerName>"

    $clusterName = "<HDInsightClusterName>"
    $location = "<MicrosoftDataCenter>"
    $clusterNodes = <ClusterSizeInNodes>

    # Obtention de la clé de compte de stockage
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }

    # Créez un cluster HDInsight
    New-AzureHDInsightCluster -Name $clusterName -Location $location -DefaultStorageAccountName "$storageAccountName.blob.core.windows.net" -DefaultStorageAccountKey $storageAccountKey -DefaultStorageContainerName $containerName  -ClusterSizeInNodes $clusterNodes

La capture d'écran suivante représente l'exécution du script :

![HDI.PS.Provision](./media/hdinsight-administer-use-powershell/HDI.PS.Provision.png)

Énumération et affichage des détails de cluster
-----------------------------------------------

Utilisez les commandes suivantes pour énumérer et afficher les détails de cluster :

**Pour afficher la liste de tous les clusters de l'abonnement actif**

    Get-AzureHDInsightCluster 

**Pour afficher les détails du cluster spécifique de l'abonnement actif**

    Get-AzureHDInsightCluster -Name <ClusterName> 

Suppression d'un cluster
------------------------

Utilisez la commande suivante pour supprimer un cluster :

    Remove-AzureHDInsightCluster -Name <ClusterName> 

Octroi/suppression de l'accès aux services HTTP
-----------------------------------------------

Les clusters HDInsight disposent des services Web HTTP suivants (tous ces services ont des points de terminaison RESTful) :

-   ODBC
-   JDBC
-   Ambari
-   Oozie
-   Templeton

Par défaut, l'accès à ces services est octroyé. Vous avez la possibilité de supprimer/octroyer l'accès. Pour cela, vous pouvez par exemple utiliser la commande suivante :

    Revoke-AzureHDInsightHttpServicesAccess -Name hdiv2 -Location "East US"

Dans l'exemple, *hdiv2* correspond au nom d'un cluster HDInsight.

> [WACOM.NOTE] En octroyant/révoquant l'accès, vous réinitialisez le nom d'utilisateur et le mot de passe du cluster.

Vous pouvez également effectuer cette opération à l'aide du portail de gestion Azure. Consultez la rubrique [Administration de HDInsight à l'aide du portail de gestion](/fr-fr/documentation/articles/hdinsight-administer-use-management-portal/).

Envoi de tâches MapReduce
-------------------------

La distribution du cluster HDInsight intègre certains exemples MapReduce. L'un de ces exemples permet de compter la fréquence des mots dans les fichiers source.

**Pour envoyer une tâche MapReduce**

Le script PowerShell suivant envoie l'exemple de tâche de comptage des mots :

     $clusterName = "<HDInsightClusterName>"            

    # Définissez la tâche MapReduce
    $wordCountJobDefinition = New-AzureHDInsightMapReduceJobDefinition -JarFile "wasb:///example/jars/hadoop-examples.jar" -ClassName "wordcount" -Arguments "wasb:///example/data/gutenberg/davinci.txt", "wasb:///example/data/WordCountOutput"

    # Exécutez la tâche et affichez l'erreur standard 
    $wordCountJobDefinition | Start-AzureHDInsightJob -Cluster $clusterName | Wait-AzureHDInsightJob -WaitTimeoutInSeconds 3600 | %{ Get-AzureHDInsightJobOutput -Cluster $clusterName -JobId $_.JobId -StandardError}

> [WACOM.NOTE] Le fichier *hadoop-examples.jar* est fourni avec les clusters HDInsight version 2.1. Ce fichier a été renommé *hadoop-mapreduce.jar* sur les clusters HDInsight version 3.0.

Pour plus d'informations sur le préfixe WASB, consultez la rubrique [Utilisation du stockage d'objets blob Azure pour HDInsight][hdinsight- stockage].

**Pour télécharger le résultat de la tâche MapReduce**

Le script PowerShell suivant extrait le résultat de la tâche MapReduce de la dernière procédure :

    $storageAccountName = "<StorageAccountName>"   
    $containerName = "<ContainerName>"             
        
    # Créez l'objet de contexte du compte de stockage
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    # Téléchargez le résultat vers l'ordinateur local
    Get-AzureStorageBlobContent -Container $ContainerName -Blob example/data/WordCountOutput/part-r-00000 -Context $storageContext -Force

    # Affichez le résultat
    cat ./example/data/WordCountOutput/part-r-00000 | findstr "there"

Pour plus d'informations sur le développement et l'exécution des tâches MapReduce, consultez l'article [Utilisation de MapReduce avec HDInsight](/fr-fr/documentation/articles/hdinsight-use-mapreduce/).

Envoi de tâches Hive
--------------------

La distribution du cluster HDInsight intègre un exemple de table Hive intitulé *hivesampletable*. Vous pouvez utiliser la requête HiveQL « show tables » pour afficher la liste des tables sur un cluster.

**Pour envoyer une tâche Hive**

Le script suivant envoie une tâche Hive afin d'afficher la liste des tables Hives :

     $clusterName = "<HDInsightClusterName>"               

    # Requête HiveQL
    $querystring = @"
        SHOW TABLES;
        SELECT * FROM hivesampletable 
            WHERE Country='United Kingdom'
            LIMIT 10;
    "@

    Use-AzureHDInsightCluster -Name $clusterName
    Invoke-Hive $querystring

La tâche Hive affiche d'abord les tables Hive créées sur le cluster et les données renvoyées par l'exemple de table Hive hivesampletable.

Pour plus d'informations sur l'utilisation de Hive, consultez l'article [Utilisation de Hive avec HDInsight](/fr-fr/documentation/articles/hdinsight-use-hive/).

Téléchargement de données vers le stockage d'objets blob
--------------------------------------------------------

Consultez la rubrique [Téléchargement de données vers HDInsight](/fr-fr/documentation/articles/hdinsight-upload-data/).

Téléchargement de données de sortie MapReduce à partir du stockage d'objets blob Azure
--------------------------------------------------------------------------------------

Consultez la section [Envoi de tâches MapReduce](#mapreduce) de cet article.

Voir aussi
----------

-   [Documentation de référence des cmdlets HDInsight](http://msdn.microsoft.com/fr-fr/library/windowsazure/dn479228.aspx)
-   [Administration de HDInsight à l'aide du portail de gestion](/fr-fr/documentation/articles/hdinsight-administer-use-management-portal/)
-   [Administration de HDInsight à l'aide de l'interface de ligne de commande](/fr-fr/documentation/articles/hdinsight-administer-use-command-line/)
-   [Approvisionnement de clusters HDInsight](/fr-fr/documentation/articles/hdinsight-provision-clusters/)
-   [Téléchargement de données vers HDInsight](/fr-fr/documentation/articles/hdinsight-upload-data/)
-   [Envoi de tâches Hadoop par programme](/fr-fr/documentation/articles/hdinsight-submit-hadoop-jobs-programmatically/)
-   [Prise en main d'Azure HDInsight](/fr-fr/documentation/articles/hdinsight-get-started/)

