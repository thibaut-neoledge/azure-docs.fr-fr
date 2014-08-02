<properties  linkid="manage-services-hdinsight-howto-blob-store" urlDisplayName="Blob Storage with HDInsight" pageTitle="Use Blob storage with HDInsight | Azure" metaKeywords="" description="Learn how HDInsight uses Blob storage as the underlying data store for HDFS and how you can query data from the store." metaCanonical="" services="storage,hdinsight" documentationCenter="" title="Use Azure Blob storage with HDInsight" authors="jgao" solutions="" manager="paulettm" editor="mollybos" />

# Utilisation du stockage d'objets blob Azure avec HDInsight

Azure HDInsight prend en charge le système HDFS (Hadoop Distributed Files System) et le stockage d'objets blob Azure pour stocker des données. Le stockage d'objets blob est une solution de stockage Azure fiable et généraliste. Le stockage d'objets blob fournit une interface HDFS complète offrant une expérience transparente en permettant à l'ensemble complet de composants de l'écosystème Hadoop de fonctionner (par défaut) directement sur les données. Le stockage d'objets blob n'est pas seulement une solution économique : il permet également de supprimer sans risque de perte de données les clusters HDInsight ayant servi aux calculs.

> [WACOM.NOTE] La syntaxe *asv://* n'est pas prise en charge dans les clusters HDInsight version 3.0 et ne sera pas prise en charge dans les versions ultérieures. Cela signifie que toutes les tâches utilisant explicitement la syntaxe “asv://” et envoyées vers des clusters HDInsight version 3.0 échoueront. Vous devez plutôt utiliser la syntaxe *wasb://*. De même, les tâches créées avec un metastore existant contenant des références explicites aux ressources utilisant la syntaxe asv:// et envoyées vers des clusters HDInsight version 3.0 échoueront également. Vous devrez recréer ces metastores en utilisant la syntaxe wasb:// pour adresser les ressources.

> [WACOM.NOTE] HDInsight prend uniquement en charge les objets blob de blocs pour le moment.

> [WACOM.NOTE] La plupart des commandes HDFS telles que **ls**{: data-morhtml="true"}, **copyFromLocal**, **mkdir**, etc., fonctionnent toujours comme prévu. Seules les commandes propres à l'implémentation HDFS native (nommée DFS), telles que **fschk** et **dfsadmin** se comporteront différemment sur le stockage d'objets blob Azure.

Pour plus d'informations sur la configuration d'un cluster HDInsight, consultez [Prise en main de HDInsight](../hdinsight-get-started/) ou [Configuration des clusters HDInsight](../hdinsight-provision-clusters/).

## Dans cet article

* [Architecture de stockage HDInsight](#architecture)
* [Avantages du stockage d'objets blob Azure](#benefits)
* [Préparation d'un conteneur au stockage d'objets blob](#preparingblobstorage)
* [Adressage des fichiers dans le stockage d'objets blob](#addressing)
* [Accès aux objets blob avec PowerShell](#powershell)
* [Étapes suivantes](#nextsteps)

## <a id="architecture" ></a>Architecture de stockage HDInsight

Le schéma suivant résume l'architecture de stockage HDInsight :

![HDI.ASVArch](./media/hdinsight-use-blob-storage/HDI.ASVArch.png
"Architecture de stockage HDInsight")

HDInsight permet d'accéder au système de fichiers distribués (DFS) connecté localement aux nœuds de calcul. Vous pouvez accéder à ce système de fichiers en utilisant l'URI complet. Par exemple :

    hdfs://<namenodehost>/<path>

De plus, HDInsight permet d'accéder aux données du stockage d'objets blob. La syntaxe pour accéder au stockage d'objets blob est la suivante :

    wasb[s]://<containername>@<a ccountname>.blob.core.windows.net/<path>

Hadoop prend en charge une notion de système de fichiers par défaut. Le système de fichiers par défaut comprend un schéma et une autorité par défaut ; il peut également être utilisé pour résoudre des chemins d'accès relatifs. Durant la configuration de HDInsight, un compte Azure Storage et un conteneur de stockage d'objets blob spécifique de ce compte sont désignés en tant que système de fichiers par défaut.

En plus de ce compte de stockage, pendant la configuration, vous pouvez ajouter des comptes de stockage à partir du même abonnement Azure ou depuis d'autres abonnements Azure. Pour plus d'instructions sur l'ajout de comptes de stockage supplémentaires, consultez la rubrique
[Configuration des clusters HDInsight](../hdinsight-provision-clusters/).

* **Conteneurs des comptes de stockage connectés à un cluster :** comme le nom et la clé du compte sont stockés dans le fichier *core-site.xml*, vous disposez d'un accès complet aux objets blob de ces conteneurs.
* **Conteneurs publics ou objets blob publics des comptes de stockage qui NE SONT PAS connectés à un cluster :** vous disposez uniquement d'une autorisation d'accès en lecture aux objets blob de ces conteneurs.


 
  > [WACOM.NOTE] 
  > Un conteneur public vous permet d'obtenir une liste de tous ses objets blob disponibles, ainsi que ses métadonnées. Vous pouvez accéder aux objets blob d'un objet blob public uniquement si vous connaissez leur URL exacte. Pour plus d'informations, consultez la page [Limiter l'accès aux conteneurs et aux objets blob][1].

* **Conteneurs privés des comptes de stockage qui NE SONT PAS connectés à un cluster :** vous ne pouvez pas accéder aux objets blob de ces conteneurs.

Les conteneurs de stockage d'objets blob stockent des données en tant que paires clé/valeur et sans hiérarchie de répertoires. Cependant, vous pouvez utiliser le caractère << / >> dans le nom de la clé pour la faire apparaître comme un fichier stocké dans une structure de répertoires. Par exemple, une clé d'objet blob peut être *input/log1.txt*. Il n'existe pas de répertoire *input*, mais le caractère << / >> figurant dans le nom de la clé lui donne l'aspect d'un chemin d'accès de fichier.

## <a id="benefits" ></a>Avantages du stockage d'objets blob Azure

La réduction des performances entraînée par la séparation des emplacements de stockage et de calcul est compensée par le fait que les clusters de calcul sont configurés à proximité des ressources du compte de stockage dans le centre de données Azure, où le réseau à haut débit permet aux nœuds de calcul d'accéder plus efficacement aux données dans le stockage d'objets blob.

Voici les avantages offerts par le stockage de données dans un stockage d'objets blob au lieu d'un système HDFS :

* **Réutilisation et partage des données :** les données du système HDFS sont situées dans le cluster de calcul. Seules les applications pouvant accéder au cluster de calcul peuvent utiliser les données avec l'API HDFS. Vous pouvez accéder aux données du stockage d'objets blob via les API HDFS ou les [API REST de stockage d'objets blob][2]. Vous pouvez donc utiliser un plus grand nombre d'applications (notamment d'autres clusters HDInsight) et d'outils pour produire et consommer des données.
* **Archivage de données :** le stockage de données dans le stockage d'objets blob permet de supprimer les clusters HDInsight ayant servi aux calculs, sans perte de données utilisateur.
* **Coût de stockage des données :** le stockage à long terme des données dans DFS est plus coûteux que le stockage des données dans un stockage d'objets blob, car le coût d'un cluster de calcul est plus élevé que celui d'un conteneur de stockage d'objets blob. De plus, comme vous n'avez pas à recharger les données pour chaque génération de cluster de calcul, vous faites également des économies sur les chargements de données.
* **Évolution flexible :** même si le système HDFS offre un système de fichiers monté en charge, cette capacité est déterminée par le nombre de nœuds que vous configurez pour votre cluster. Au lieu de procéder ainsi, il est parfois plus simple de profiter des capacités d'évolution flexible du stockage d'objets blob, que vous obtenez automatiquement.
* **Géo-réplication :** vous pouvez géo-répliquer vos conteneurs de stockage d'objets blob via le portail Azure. Si cette fonctionnalité permet la récupération géographique et la redondance des données, un basculement vers un emplacement géo-répliqué affecte sérieusement les performances et peut entraîner des frais supplémentaires. Nous vous recommandons donc de peser sérieusement le pour et le contre avant de choisir la géo-réplication.

Certains packages et tâches MapReduce peuvent créer des résultats intermédiaires que vous ne voulez pas stocker dans un conteneur de stockage d'objets blob. Dans ce cas, vous pouvez toujours choisir de stocker les données dans un système HDFS local. En fait, HDInsight utilise DFS pour plusieurs de ces résultats intermédiaires dans les tâches Hive et d'autres processus.

## <a id="preparingblobstorage" ></a>Préparation d'un conteneur au stockage d'objets blob

Pour utiliser des objets blob, commencez par créer un [compte de stockage Azure](/en-us/manage/services/storage/how-to-create-a-storage-account/). Durant cette opération, vous devez indiquer un centre de données Azure qui stockera les objets que vous créez en utilisant ce compte. Le cluster et le compte de stockage doivent être hébergés dans le même centre de données (les bases de données SQL des metastores Hive et Oozie doivent également se trouver dans le même centre de données). Où qu'il réside, chaque objet blob que vous créez appartient à un conteneur de votre compte de stockage. Ce conteneur peut être un conteneur de stockage d'objets blob existant créé hors de HDInsight ou un conteneur créé pour un cluster HDInsight.

### Création d'un conteneur d'objets blob pour HDInsight avec le portail de gestion

Lorsque vous configurez un cluster HDInsight à partir du portail de gestion Azure, vous disposez de deux options : *quick create* et *custom create*. L'option quick create requiert un compte Azure Storage. Pour obtenir des instructions, consultez le guide [Création d'un compte de stockage](/en-us/manage/services/storage/how-to-create-a-storage-account/).

En utilisant l'option quick create, vous pouvez sélectionner un compte de stockage existant. Le processus de configuration crée un conteneur ayant le même nom que le cluster HDInsight. Ce conteneur sert de système de fichiers par défaut.

![HDI.QuickCreate](./media/hdinsight-use-blob-storage/HDI.QuickCreateCluster.png
"Option Quick Create du cluster HDInsight")

L'option custom create permet de sélectionner un conteneur de stockage d'objets blob existant ou de créer un conteneur par défaut. Par défaut, le nom du conteneur est identique à celui du cluster HDInsight.

![HDI.CustomCreateStorageAccount](./media/hdinsight-use-blob-storage/HDI.CustomCreateStorageAccount.png
"Compte de stockage : option Custom Create")

### Création d'un conteneur avec Azure PowerShell

Vous pouvez utiliser [Azure PowerShell](../install-configure-powershell/) pour créer des conteneurs d'objets blob. Voici un exemple de script PowerShell :

    $subscriptionName = "<SubscriptionName>"
    $storageAccountName = "<WindowsAzureStorageAccountName>"
    $containerName="<BlobContainerToBeCreated>"
    
    Add-AzureAccount # La connexion durera 12 heures.
    Select-AzureSubscription $subscriptionName #seulement nécessaire si vous avez plusieurs abonnements

    # Création d'un objet de contexte de stockage
    $storageAccountkey = get-azurestoragekey $storageAccountName | %{$_.Primary}
    $destContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey  

    # Création d'un conteneur d'objets blob
    New-AzureStorageContainer -Name $containerName -Context $destContext 

## <a id="addressing" ></a>Adressage des fichiers dans le stockage d'objets blob

Le modèle d'URI pour accéder aux fichiers du stockage d'objets blob est le suivant :

    wasb[s]://<BlobStorageContainerName>@<StorageAccountName>.blob.core.windows.net/<path>

> [WACOM.NOTE] La syntaxe pour l'adressage des fichiers sur un émulateur de stockage (HDInsight) est *wasb://<NomDuConteneur>@emulateurdestockage*{: data-morhtml="true"}.

Le modèle d'URI offre à la fois un accès non chiffré avec le préfixe *wasb:* et un accès chiffré SSL avec *wasbs*. Dans la mesure du possible, nous vous recommandons d'utiliser *wasbs*, même lorsqu'il s'agit d'accéder à des données qui résident dans le même centre de données Azure.

<BlobStorageContainerName> identifie le nom du conteneur de stockage d'objets blob. <StorageAccountName> identifie le nom de compte de stockage Azure. Un nom de domaine complet (FQDN) est requis.

Si ni <BlobStorageContainerName> ni <StorageAccountName> n'a été spécifié, le système de fichiers par défaut est utilisé. Pour les fichiers du système de fichiers par défaut, vous pouvez utiliser un chemin d'accès relatif ou absolu. Par exemple, le fichier hadoop-mapreduce-examples.jar fourni avec les clusters HDInsight peut être désigné pour l'une des utilisations suivantes :

    wasb://monconteneur@moncompte.blob.core.windows.net/example/jars/hadoop-mapreduce-examples.jar
    wasb:///example/jars/hadoop-mapreduce-examples.jar
    /example/jars/hadoop-mapreduce-examples.jar

> [WACOM.NOTE] Le nom du fichier est *hadoop-examples.jar*{: data-morhtml="true"} sur les clusters HDInsight version 1.6 et 2.1.

<path> correspond au nom du chemin d'accès du fichier ou du répertoire HDFS. Comme les conteneurs de stockage d'objets blob constituent simplement un magasin de clé-valeur, il n'y a pas de système de fichiers hiérarchique. Un signe << / >> à l'intérieur d'une clé d'objet blob est interprété comme un séparateur de répertoire. Par exemple, le nom d'objet blob pour *hadoop-mapreduce-examples.jar* est :

    example/jars/hadoop-mapreduce-examples.jar

## <a id="powershell" ></a>Accès aux objets blob avec PowerShell

Pour plus d'informations sur l'installation et la configuration d'Azure PowerShell sur votre poste de travail, consultez la page [Installation et configuration d'Azure PowerShell](../install-configure-powershell/). La fenêtre de console Azure PowerShell ou PowerShell_ISE permet d'exécuter les cmdlets PowerShell.

Utilisez la commande suivante pour répertorier les cmdlets relatives aux objets blob :

    Get-Command *blob*

![Blob.PowerShell.cmdlets](./media/hdinsight-use-blob-storage/HDI.PowerShell.BlobCommands.png)

**Exemple PowerShell pour le chargement d'un fichier**

Consultez la rubrique [Téléchargement de données vers HDInsight](../hdinsight-upload-data/).

**Exemple PowerShell pour le téléchargement d'un fichier**

Le script suivant télécharge un objet blob de blocs vers le dossier actuel. Avant d'exécuter le script, remplacez le répertoire par un dossier sur lequel vous disposez d'un accès en écriture.

    $storageAccountName = "<WindowsAzureStorageAccountName>"   # Le compte de stockage utilisé pour le système de fichiers par défaut spécifié durant la configuration.
    $containerName = "<BlobStorageContainerName>"  # Le conteneur de système de fichiers par défaut a le même nom que le cluster.
    $blob = "example/data/sample.log" # Le nom de l'objet blob à télécharger.

    # Utilisez Add-AzureAccount si vous n'êtes pas connecté à votre abonnement Azure
    #Add-AzureAccount # La connexion durera 12 heures

    # Utilisez ces deux commandes si vous avez plusieurs abonnements
    #$subscriptionName = "<SubscriptionName>"       
    #Select-AzureSubscription $subscriptionName
    
    Write-Host "Création d'un objet de contexte ... " -ForegroundColor Green
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    
    Write-Host "Téléchargement de l'objet blob ..." -ForegroundColor Green
    Get-AzureStorageBlobContent -Container $ContainerName -Blob $blob -Context $storageContext -Force
    
    Write-Host "Création d'une liste des fichiers téléchargés ..." -ForegroundColor Green
    cat "./$blob"

**Exemple PowerShell pour la suppression d'un fichier**

    $storageAccountName = "<WindowsAzureStorageAccountName>"   # Le compte de stockage utilisé pour le système de fichiers par défaut spécifié durant la configuration.
    $containerName = "<BlobStorageContainerName>"  # Le conteneur de système de fichiers par défaut a le même nom que le cluster.
    $blob = "example/data/sample.log" # Le nom de l'objet blob à télécharger.

    # Utilisez Add-AzureAccount si vous n'êtes pas connecté à votre abonnement Azure
    #Add-AzureAccount # La connexion durera 12 heures

    # Utilisez ces deux commandes si vous avez plusieurs abonnements
    #$subscriptionName = "<SubscriptionName>"       
    #Select-AzureSubscription $subscriptionName
    
    Write-Host "Création d'un objet de contexte ... " -ForegroundColor Green
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    
    Write-Host "Suppression de l'objet blob ..." -ForegroundColor Green
    Remove-AzureStorageBlob -Container $containerName -Context $storageContext -blob $blob 

**Exemple PowerShell pour la création d'une liste des fichiers d'un
dossier**

    $storageAccountName = "<WindowsAzureStorageAccountName>"   # Le compte de stockage utilisé pour le système de fichiers par défaut spécifié durant la configuration.
    $containerName = "<BlobStorageContainerName>"  # Le conteneur de système de fichiers par défaut a le même nom que le cluster.
    $blobPrefix = "example/data/"

    # Utilisez Add-AzureAccount si vous n'êtes pas connecté à votre abonnement Azure
    #Add-AzureAccount # La connexion durera 12 heures

    # Utilisez ces deux commandes si vous avez plusieurs abonnements
    #$subscriptionName = "<SubscriptionName>"       
    #Select-AzureSubscription $subscriptionName
    
    Write-Host "Création d'un objet de contexte ... " -ForegroundColor Green
    $storageAccountKey = Get-AzureStorageKey $storageAccountName | %{ $_.Primary }
    $storageContext = New-AzureStorageContext -StorageAccountName $storageAccountName -StorageAccountKey $storageAccountKey
    
    Write-Host "Création d'une liste des fichiers dans $blobPrefix ..."
    Get-AzureStorageBlob -Container $containerName -Context $storageContext -prefix $blobPrefix

## <a id="nextsteps" ></a>Étapes suivantes

Dans cet article, vous avez appris comment utiliser le stockage d'objets blob avec HDInsight, mais aussi, que le stockage d'objets blob est un
élément essentiel de HDInsight. Ceci vous permet de créer des solutions
à long terme et évolutives pour acquérir des données d'archivage avec le
stockage d'objets blob Azure et d'utiliser HDInsight pour déverrouiller les informations des données stockées.

Pour en savoir plus, consultez les articles suivants :

* [Prise en main d'Azure HDInsight](../hdinsight-get-started/)
* [Téléchargement de données vers HDInsight](../hdinsight-upload-data/)
* [Utilisation de Hive avec HDInsight](../hdinsight-use-hive/)
* [Utilisation de Pig avec HDInsight](../hdinsight-use-pig/)



[1]: http://msdn.microsoft.com/en-us/library/windowsazure/dd179354.aspx
[2]: http://msdn.microsoft.com/en-us/library/windowsazure/dd135733.aspx