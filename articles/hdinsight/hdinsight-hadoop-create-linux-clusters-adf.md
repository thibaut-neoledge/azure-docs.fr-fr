---
title: "Créer des clusters Hadoop à la demande à l’aide de Data Factory - Azure HDInsight | Microsoft Docs"
description: "Découvrez comment créer des clusters Hadoop à la demande dans HDInsight avec Azure Data Factory."
services: hdinsight
documentationcenter: 
tags: azure-portal
author: spelluru
manager: jhubbard
editor: cgronlun
ms.assetid: 1f3b3a78-4d16-4d99-ba6e-06f7bb185d6a
ms.service: hdinsight
ms.custom: hdinsightactive
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 07/20/2017
ms.author: spelluru
ms.openlocfilehash: b9b73f6691af957e42236ef9a223411a0296f96f
ms.sourcegitcommit: f8437edf5de144b40aed00af5c52a20e35d10ba1
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/03/2017
---
# <a name="create-on-demand-hadoop-clusters-in-hdinsight-using-azure-data-factory"></a>Créer des clusters Hadoop à la demande dans HDInsight avec Azure Data Factor
[!INCLUDE [selector](../../includes/hdinsight-create-linux-cluster-selector.md)]

[Azure Data Factory](../data-factory/introduction.md) est un service d’intégration de données cloud qui gère et automatise le déplacement et la transformation des données. Il peut créer un cluster Hadoop HDInsight juste-à-temps pour traiter une tranche de données d’entrée et supprimer le cluster à l’issue du traitement. Voici quelques-uns des avantages liés à l’utilisation d’un cluster Hadoop HDInsight à la demande :

- Vous payez uniquement pour le temps d’exécution du travail sur le cluster Hadoop HDInsight (ainsi que pour une brève durée d’inactivité configurable). La facturation des clusters HDInsight est calculée au prorata des minutes écoulées, que vous les utilisiez ou non. Lorsque vous utilisez un service lié HDInsight à la demande dans Data Factory, les clusters sont créés à la demande. Et les clusters sont automatiquement supprimés lorsque les tâches sont terminées. Par conséquent, vous ne payez que pour le temps d’exécution du travail et pour la courte durée d’inactivité (paramètre de durée de vie [TTL, Time to Live]).
- Vous pouvez créer un workflow à l’aide d’un pipeline Data Factory. Par exemple, vous pouvez faire en sorte que le pipeline copie des données d’un serveur SQL Server local vers un stockage Blob Azure, puis qu’il traite ces données en exécutant un script Hive et un script Pig sur un cluster Hadoop HDInsight à la demande. Ensuite, copiez les données résultantes dans un entrepôt de données Azure SQL Data Warehouse pour que les applications décisionnelles puissent les consommer.
- Vous pouvez planifier une exécution périodique du workflow (horaire, quotidienne, hebdomadaire, mensuelle, etc.).

Dans Azure Data Factory, une fabrique de données peut comporter un ou plusieurs pipelines de données. Un pipeline de données comprend une ou plusieurs activités. Il existe deux types d’activités : les [activités de déplacement des données](../data-factory/copy-activity-overview.md) et les [activités de transformation des données](../data-factory/transform-data.md). Vous utilisez les activités de déplacement des données (pour l’instant, uniquement l’activité de copie) pour déplacer des données d’une banque de données source vers une banque de données de destination. Vous utilisez les activités de transformation des données pour transformer/traiter les données. L’activité Hive HDInsight est l’une des activités de transformation prises en charge par Data Factory. Dans ce didacticiel, vous utilisez l’activité de transformation Hive.

Vous pouvez configurer une activité Hive pour qu’elle utilise votre propre cluster Hadoop HDInsight ou un cluster Hadoop HDInsight à la demande. Dans ce didacticiel, l’activité Hive figurant dans le pipeline de fabrique de données est configurée pour utiliser un cluster HDInsight à la demande. Par conséquent, lorsque l’activité s’exécute pour traiter une tranche de données, le déroulement des opérations est le suivant :

1. Un cluster Hadoop HDInsight est automatiquement créé juste-à-temps à votre intention pour traiter la tranche.  
2. Les données d’entrée sont traitées par l’exécution d’un script HiveQL sur le cluster.
3. Le cluster Hadoop HDInsight est supprimé à l’issue du traitement et reste inactif pendant l’intervalle de temps configuré (paramètre timeToLive). Si la tranche de données suivante peut être traitée au cours de cette durée d’inactivité timeToLive, elle est traitée à l’aide du même cluster.  

Dans ce didacticiel, le script HiveQL associé à l’activité Hive effectue les opérations suivantes :

1. Il crée une table externe qui référence les données brutes de journal Web stockées dans un stockage Blob Azure.
2. Il partitionne les données brutes par année et par mois.
3. Il stocke les données partitionnées dans le stockage Blob Azure.

Dans ce didacticiel, le script HiveQL associé à l’activité Hive crée une table externe qui référence les données brutes de journal Web stockées dans le stockage Blob Azure. Voici les échantillons de lignes pour chaque mois du fichier d’entrée.

```
2014-01-01,02:01:09,SAMPLEWEBSITE,GET,/blogposts/mvc4/step2.png,X-ARR-LOG-ID=2ec4b8ad-3cf0-4442-93ab-837317ece6a1,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,53175,871
2014-02-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
2014-03-01,02:01:10,SAMPLEWEBSITE,GET,/blogposts/mvc4/step7.png,X-ARR-LOG-ID=d7472a26-431a-4a4d-99eb-c7b4fda2cf4c,80,-,1.54.23.196,Mozilla/5.0+(Windows+NT+6.3;+WOW64)+AppleWebKit/537.36+(KHTML,+like+Gecko)+Chrome/31.0.1650.63+Safari/537.36,-,http://weblogs.asp.net/sample/archive/2007/12/09/asp-net-mvc-framework-part-4-handling-form-edit-and-post-scenarios.aspx,\N,200,0,0,30184,871
```

Le script HiveQL partitionne les données brutes par année et par mois. Il crée trois dossiers de sortie en fonction de l’entrée précédente. Chaque dossier contient un fichier avec les entrées correspondant à chaque mois.

```
adfgetstarted/partitioneddata/year=2014/month=1/000000_0
adfgetstarted/partitioneddata/year=2014/month=2/000000_0
adfgetstarted/partitioneddata/year=2014/month=3/000000_0
```

Pour obtenir la liste des activités de transformation de données de Data Factory en plus de l’activité Hive, consultez [Transformation et analyse en utilisant Azure Data Factory](../data-factory/transform-data.md).

> [!NOTE]
> Actuellement, vous ne pouvez créer qu’un cluster HDInsight version 3.2 à partir d’Azure Data Factory.

## <a name="prerequisites"></a>Composants requis
Avant de commencer à suivre les instructions de cet article, vous devez disposer des éléments suivants :

* [Abonnement Azure](https://azure.microsoft.com/documentation/videos/get-azure-free-trial-for-testing-hadoop-in-hdinsight/).
* Azure PowerShell.

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-powershell.md)]

### <a name="prepare-storage-account"></a>Préparer le compte de stockage
Vous pouvez utiliser jusqu’à trois comptes de stockage dans ce scénario :

- le compte de stockage par défaut du cluster HDInsight
- le compte de stockage des données d’entrée
- le compte de stockage des données de sortie

Pour simplifier ce didacticiel, vous utilisez un seul compte de stockage pour ces trois fonctions. L’exemple de script Azure PowerShell de cette section exécute les tâches suivantes :

1. Connectez-vous à Azure.
2. Création d’un groupe de ressources Azure.
3. Création d’un compte Azure Storage.
4. Création d’un conteneur d’objets blob dans le compte de stockage.
5. Copie des deux fichiers suivants dans le conteneur d’objets blob :

   * Fichier d’entrée : [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log)
   * Script HiveQL : [https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql](https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql)

     Les deux fichiers sont stockés dans un conteneur d’objets blob public.


**Pour préparer le stockage et copier les fichiers à l’aide d’Azure PowerShell :**
> [!IMPORTANT]
> Spécifiez des noms pour le groupe de ressources Azure et le compte de stockage Azure qui seront créés par le script.
> Notez le **nom du groupe de ressources**, le **nom du compte de stockage** et la **clé du compte de stockage** générés en sortie par le script. Vous aurez besoin de ces informations dans la section suivante.

```powershell
$resourceGroupName = "<Azure Resource Group Name>"
$storageAccountName = "<Azure Storage Account Name>"
$location = "East US 2"

$sourceStorageAccountName = "hditutorialdata"  
$sourceContainerName = "adfhiveactivity"

$destStorageAccountName = $storageAccountName
$destContainerName = "adfgetstarted" # don't change this value.

####################################
# Connect to Azure
####################################
#region - Connect to Azure subscription
Write-Host "`nConnecting to your Azure subscription ..." -ForegroundColor Green
try{Get-AzureRmContext}
catch{Login-AzureRmAccount}
#endregion

####################################
# Create a resource group, storage, and container
####################################

#region - create Azure resources
Write-Host "`nCreating resource group, storage account and blob container ..." -ForegroundColor Green

New-AzureRmResourceGroup -Name $resourceGroupName -Location $location
New-AzureRmStorageAccount `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName `
    -type Standard_LRS `
    -Location $location

$destStorageAccountKey = (Get-AzureRmStorageAccountKey `
    -ResourceGroupName $resourceGroupName `
    -Name $destStorageAccountName)[0].Value

$sourceContext = New-AzureStorageContext `
    -StorageAccountName $sourceStorageAccountName `
    -Anonymous
$destContext = New-AzureStorageContext `
    -StorageAccountName $destStorageAccountName `
    -StorageAccountKey $destStorageAccountKey

New-AzureStorageContainer -Name $destContainerName -Context $destContext
#endregion

####################################
# Copy files
####################################
#region - copy files
Write-Host "`nCopying files ..." -ForegroundColor Green

$blobs = Get-AzureStorageBlob `
    -Context $sourceContext `
    -Container $sourceContainerName

$blobs|Start-AzureStorageBlobCopy `
    -DestContext $destContext `
    -DestContainer $destContainerName

Write-Host "`nCopied files ..." -ForegroundColor Green
Get-AzureStorageBlob -Context $destContext -Container $destContainerName
#endregion

Write-host "`nYou will use the following values:" -ForegroundColor Green
write-host "`nResource group name: $resourceGroupName"
Write-host "Storage Account Name: $destStorageAccountName"
write-host "Storage Account Key: $destStorageAccountKey"

Write-host "`nScript completed" -ForegroundColor Green
```

Si vous avez besoin d’aide avec le script PowerShell, consultez l’article [Utilisation d’Azure PowerShell avec le service Stockage Azure](../storage/common/storage-powershell-guide-full.md). Si vous préférez utiliser l’interface de ligne de commande Azure, consultez la section [Annexe](#appendix) relative au script d’interface de ligne de commande Azure.

**Pour examiner le compte de stockage et son contenu**

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Groupes de ressources** dans le volet de gauche.
3. Double-cliquez sur le nom du groupe de ressources que vous avez créé dans votre script PowerShell. Utilisez le filtre si la liste des groupes de ressources est trop longue.
4. Dans la mosaïque **Ressources** , vous devez voir une ressource, sauf si vous partagez le groupe de ressources avec d’autres projets. Cette ressource correspond au compte de stockage avec le nom que vous avez spécifié précédemment. Cliquez sur le nom du compte de stockage.
5. Cliquez sur la mosaïque **Objets Blob** .
6. Cliquez sur le conteneur **adfgetstarted** . Vous voyez deux dossiers : **inputdata** et **script**.
7. Ouvrez le dossier et vérifiez les fichiers des deux dossiers. Le dossier inputdata contient le fichier input.log avec les données d’entrée, tandis que le dossier script contient le fichier de script HiveQL.

## <a name="create-a-data-factory-using-resource-manager-template"></a>Créer une fabrique de données à l’aide du modèle Resource Manager
Avec le compte de stockage, les données d’entrée et le script HiveQL préparé, vous êtes prêt à créer une fabrique de données Azure. Il existe plusieurs méthodes pour créer la fabrique de données. Dans ce didacticiel, vous créez une fabrique de données en déployant un modèle Azure Resource Manager à l’aide du Portail Azure. Vous pouvez également déployer un modèle Resource Manager en utilisant [l’interface de ligne de commande Azure](../azure-resource-manager/resource-group-template-deploy-cli.md) et [Azure PowerShell](../azure-resource-manager/resource-group-template-deploy.md#deploy-local-template). Pour les autres méthodes de création de fabriques de données, consultez la page [Didacticiel : créer votre première fabrique de données](../data-factory/quickstart-create-data-factory-dot-net.md).

1. Cliquez sur l’image suivante pour vous connecter à Azure et ouvrir le modèle Resource Manager dans le portail Azure. Le modèle se trouve dans https://hditutorialdata.blob.core.windows.net/adfhiveactivity/data-factory-hdinsight-on-demand.json. Pour obtenir des informations détaillées sur les entités définies dans le modèle, consultez la section [Entités Data Factory dans le modèle](#data-factory-entities-in-the-template). 

    <a href="https://portal.azure.com/#create/Microsoft.Template/uri/https%3A%2F%2Fhditutorialdata.blob.core.windows.net%2Fadfhiveactivity%2Fdata-factory-hdinsight-on-demand.json" target="_blank"><img src="./media/hdinsight-hadoop-create-linux-clusters-adf/deploy-to-azure.png" alt="Deploy to Azure"></a>
2. Sélectionnez l’option **Utiliser existant** pour le paramètre **Groupe de ressources**, puis sélectionnez le nom du groupe de ressources que vous avez créé à l’étape précédente (à l’aide du script PowerShell).
3. Entrez un nom pour la fabrique de données (**Nom de la fabrique de données**). Ce nom doit être globalement unique.
4. Entrez le **nom du compte de stockage** et la **clé du compte de stockage** que vous avez notés à l’étape précédente.
5. Après avoir lu les **termes et conditions**, cochez la case **J’accepte les termes et conditions mentionnés ci-dessus**.
6. Sélectionnez l’option **Épingler au tableau de bord**.
6. Cliquez sur **Acheter/Créer**. La vignette **Déploiement du modèle de déploiement** apparaît sur le tableau de bord. Attendez que le panneau **Groupe de ressources** de votre groupe de ressources s’affiche. Pour ouvrir le volet du groupe de ressources, vous pouvez également cliquer sur la vignette libellée avec le nom de votre groupe de ressources.
6. Si le panneau du groupe de ressources n’est pas encore ouvert, cliquez sur la vignette pour ouvrir le groupe de ressources. Vous devez maintenant voir une autre ressource de fabrique de données en plus de la ressource du compte de stockage.
7. Cliquez sur le nom de votre fabrique de données (valeur que vous avez spécifiée pour le paramètre **Nom de la fabrique de données**).
8. Dans le panneau Data Factory, cliquez sur la vignette **Diagramme**. Le diagramme montre une activité avec un jeu de données d’entrée et un jeu de données de sortie :

    ![Diagramme du pipeline d’activité Hive à la demande HDInsight avec Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-pipeline-diagram.png)

    Les noms sont définis dans le modèle Resource Manager.
9. Double-cliquez sur **AzureBlobOutput**.
10. Dans **Tranches récemment mises à jour**, une tranche doit s’afficher. Si l’état est **En cours**, attendez jusqu’à ce qu’il passe à **Prêt**. La création d’un cluster HDInsight nécessite environ **20 minutes**.

### <a name="check-the-data-factory-output"></a>Vérifier la sortie de la fabrique de données

1. Utilisez la même procédure dans la dernière session pour vérifier les conteneurs du conteneur adfgetstarted. Il existe deux nouveaux conteneurs en plus de **adfgetsarted**:

   * Un conteneur dont le nom est conforme au modèle suivant : `adf<yourdatafactoryname>-linkedservicename-datetimestamp`. Il s’agit du conteneur par défaut pour le cluster HDInsight.
   * adfjobs : ce conteneur est le conteneur des journaux de travaux Azure Data Factory (ADF).

     La sortie de la fabrique de données est stockée dans le conteneur **adfgetstarted**, comme vous l’avez configuré dans le modèle Resource Manager.
2. Cliquez sur **adfgetstarted**.
3. Double-cliquez sur **partitioneddata**. Un dossier **year=2014** s’affiche, car tous les journaux Web datent de l’année 2014.

    ![Sortie du pipeline d’activité Hive à la demande HDInsight avec Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-year.png)

    Si vous ouvrez la liste, vous devez voir trois dossiers pour janvier, février et mars. Il y a un journal pour chaque mois.

    ![Sortie du pipeline d’activité Hive à la demande HDInsight avec Azure Data Factory](./media/hdinsight-hadoop-create-linux-clusters-adf/hdinsight-adf-output-month.png)

## <a name="data-factory-entities-in-the-template"></a>Entités Data Factory dans le modèle
Le modèle Resource Manager de niveau supérieur d’une fabrique de données ressemble à ceci :

```json
{
    "contentVersion": "1.0.0.0",
    "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "parameters": { ...
    },
    "variables": { ...
    },
    "resources": [
        {
            "name": "[parameters('dataFactoryName')]",
            "apiVersion": "[variables('apiVersion')]",
            "type": "Microsoft.DataFactory/datafactories",
            "location": "westus",
            "resources": [
                { ... },
                { ... },
                { ... },
                { ... }
            ]
        }
    ]
}
```

### <a name="define-data-factory"></a>Définir une fabrique de données
Vous définissez une fabrique de données dans le modèle Resource Manager, comme indiqué dans l’exemple suivant :  

```json
"resources": [
{
    "name": "[parameters('dataFactoryName')]",
    "apiVersion": "[variables('apiVersion')]",
    "type": "Microsoft.DataFactory/datafactories",
    "location": "westus",
}
```
L’élément dataFactoryName est le nom de la fabrique de données que vous spécifiez lorsque vous déployez le modèle. Pour l’instant, Data Factory est uniquement pris en charge dans les régions États-Unis de l’Est, États-Unis de l’Ouest et Europe du Nord.

### <a name="defining-entities-within-the-data-factory"></a>Définition d’entités dans la fabrique de données
Les entités Data Factory suivantes sont définies dans le modèle JSON :

* [Service lié Azure Storage](#azure-storage-linked-service)
* [Service lié à la demande HDInsight](#hdinsight-on-demand-linked-service)
* [Jeu de données d'entrée d'objet Blob Azure](#azure-blob-input-dataset)
* [Jeu de données de sortie d’objet Blob Azure](#azure-blob-output-dataset)
* [Pipeline de données avec une activité de copie](#data-pipeline)

#### <a name="azure-storage-linked-service"></a>Service lié Azure Storage
Le service lié Stockage Azure relie votre compte de stockage Azure à la fabrique de données. Dans ce didacticiel, le même compte de stockage est utilisé comme compte de stockage HDInsight par défaut, comme stockage des données d’entrée et comme stockage des données de sortie. Par conséquent, vous ne définissez qu’un seul service lié Stockage Azure. Dans la définition du service lié, vous spécifiez le nom et la clé de votre compte de stockage Azure. Consultez [Service lié Stockage Azure](../data-factory/connector-azure-blob-storage.md) pour en savoir plus sur les propriétés JSON utilisées pour définir un service lié Stockage Azure.

```json
{
    "name": "[variables('storageLinkedServiceName')]",
    "type": "linkedservices",
    "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureStorage",
        "typeProperties": {
            "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('storageAccountName'),';AccountKey=',parameters('storageAccountKey'))]"
        }
    }
}
```
La propriété **connectionString** utilise les paramètres storageAccountName et storageAccountKey. Vous renseignez ces paramètres lors du déploiement du modèle.  

#### <a name="hdinsight-on-demand-linked-service"></a>Service lié à la demande HDInsight
Dans la définition du service lié HDInsight à la demande, vous spécifiez les valeurs des paramètres de configuration que le service Data Factory utilise pour créer un cluster Hadoop HDInsight au moment de l’exécution. Consultez l’article [Services liés de calcul](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service) pour en savoir plus sur les propriétés JSON utilisées pour définir un service lié à la demande HDInsight.  

```json

{
    "type": "linkedservices",
    "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "HDInsightOnDemand",
        "typeProperties": {
            "version": "3.5",
            "clusterSize": 1,
            "timeToLive": "00:05:00",
            "osType": "Linux",
            "sshUserName": "myuser",                            
            "sshPassword": "MyPassword!",
            "linkedServiceName": "[variables('storageLinkedServiceName')]"
        }
    }
}
```
Notez les points suivants :

* Data Factory crée un cluster HDInsight **Linux** à votre intention.
* Le cluster Hadoop HDInsight est créé dans la même région que le compte de stockage.
* Notez le paramètre *timeToLive* . La fabrique de données supprime automatiquement le cluster quand celui-ci est inactif pendant 30 minutes.
* Le cluster HDInsight crée un **conteneur par défaut** dans le stockage d’objets blob que vous avez spécifié dans le JSON (**linkedServiceName**). HDInsight ne supprime pas ce conteneur lorsque le cluster est supprimé. Ce comportement est normal. Avec le service lié HDInsight à la demande, un cluster HDInsight est créé à chaque fois qu’une tranche doit être traitée, à moins qu’il n’existe un cluster activé (**timeToLive**), et est supprimé une fois le traitement activé.

Pour plus d’informations, voir [Service lié à la demande Azure HDInsight](../data-factory/compute-linked-services.md#azure-hdinsight-on-demand-linked-service) .

> [!IMPORTANT]
> Comme un nombre croissant de tranches sont traitées, vous voyez un grand nombre de conteneurs dans votre stockage d’objets blob Azure. Si vous n’en avez pas besoin pour dépanner les travaux, il se peut que vous deviez les supprimer pour réduire les frais de stockage. Le nom de ces conteneurs suit un modèle : « **nomdevotrefabriquededonnéesadf**-**nomduservicelié**-horodatage ». Utilisez des outils tels que [Microsoft Storage Explorer](http://storageexplorer.com/) pour supprimer des conteneurs dans votre stockage d’objets blob Azure.

#### <a name="azure-blob-input-dataset"></a>Jeu de données d'entrée d'objet Blob Azure
Dans la définition du jeu de données d’entrée, vous spécifiez les noms du conteneur d’objets blob, du dossier et du fichier contenant les données d’entrée. Consultez [Propriétés du jeu de données d’objet blob Azure](../data-factory/connector-azure-blob-storage.md) pour en savoir plus sur les propriétés JSON permettant de définir un jeu de données d’objets blob Azure.

```json

{
    "type": "datasets",
    "name": "[variables('blobInputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "fileName": "input.log",
            "folderPath": "adfgetstarted/inputdata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1
        },
        "external": true,
        "policy": {}
    }
}

```

Notez les paramètres spécifiques ci-après dans la définition JSON :

```json
"fileName": "input.log",
"folderPath": "adfgetstarted/inputdata",
```

#### <a name="azure-blob-output-dataset"></a>Jeu de données de sortie d’objet Blob Azure
Dans la définition du jeu de données de sortie, vous spécifiez les noms du conteneur d’objets blob et du dossier contenant les données de sortie. Consultez [Propriétés du jeu de données d’objet blob Azure](../data-factory/connector-azure-blob-storage.md) pour en savoir plus sur les propriétés JSON permettant de définir un jeu de données d’objets blob Azure.  

```json

{
    "type": "datasets",
    "name": "[variables('blobOutputDatasetName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "type": "AzureBlob",
        "linkedServiceName": "[variables('storageLinkedServiceName')]",
        "typeProperties": {
            "folderPath": "adfgetstarted/partitioneddata",
            "format": {
                "type": "TextFormat",
                "columnDelimiter": ","
            }
        },
        "availability": {
            "frequency": "Month",
            "interval": 1,
            "style": "EndOfInterval"
        }
    }
}
```

L’élément folderPath spécifie le chemin du dossier qui contient les données de sortie :

```json
"folderPath": "adfgetstarted/partitioneddata",
```

Le paramètre [dataset availability](../data-factory/concepts-datasets-linked-services.md) se présente comme suit :

```json
"availability": {
    "frequency": "Month",
    "interval": 1,
    "style": "EndOfInterval"
},
```

Dans Azure Data Factory, la disponibilité du jeu de données de résultats conditionne le pipeline. Dans cet exemple, la tranche est produite le dernier jour de chaque mois (EndOfInterval). 

#### <a name="data-pipeline"></a>Pipeline de données
Vous définissez un pipeline qui transforme les données en exécutant le script Hive sur un cluster Azure HDInsight à la demande. Consultez [Pipeline JSON](../data-factory/concepts-pipelines-activities.md) pour obtenir des descriptions des éléments JSON permettant de définir un pipeline dans cet exemple.

```json
{
    "type": "datapipelines",
    "name": "[parameters('dataFactoryName')]",
    "dependsOn": [
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('storageLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedServices/', variables('hdInsightOnDemandLinkedServiceName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobInputDatasetName'))]",
        "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/datasets/', variables('blobOutputDatasetName'))]"
    ],
    "apiVersion": "[variables('apiVersion')]",
    "properties": {
        "description": "Azure Data Factory pipeline with an Hadoop Hive activity",
        "activities": [
            {
                "type": "HDInsightHive",
                "typeProperties": {
                    "scriptPath": "adfgetstarted/script/partitionweblogs.hql",
                    "scriptLinkedService": "[variables('storageLinkedServiceName')]",
                    "defines": {
                        "inputtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/inputdata')]",
                        "partitionedtable": "[concat('wasb://adfgetstarted@', parameters('storageAccountName'), '.blob.core.windows.net/partitioneddata')]"
                    }
                },
                "inputs": [
                    {
                        "name": "AzureBlobInput"
                    }
                ],
                "outputs": [
                    {
                        "name": "AzureBlobOutput"
                    }
                ],
                "policy": {
                    "concurrency": 1,
                    "retry": 3
                },
                "name": "RunSampleHiveActivity",
                "linkedServiceName": "HDInsightOnDemandLinkedService"
            }
        ],
        "start": "2016-01-01T00:00:00Z",
        "end": "2016-01-31T00:00:00Z",
        "isPaused": false
    }
}
```

Le pipeline ne contient qu’une seule activité, l’activité HDInsightHive. Étant donné que les dates de début et de fin appartiennent toutes deux au mois de janvier 2016, le traitement ne porte que sur les données d’un seul mois (une tranche). Puisque les deux éléments *start* et *end* de l’activité correspondent à une date située dans le passé, Data Factory traite immédiatement les données du mois. Si la fin est une date à venir, la fabrique de données crée une autre tranche en temps voulu. Pour plus d’informations, consultez [Planification et exécution avec Data Factory](../data-factory/v1/data-factory-scheduling-and-execution.md).

## <a name="clean-up-the-tutorial"></a>Nettoyage du didacticiel

### <a name="delete-the-blob-containers-created-by-on-demand-hdinsight-cluster"></a>Supprimer les conteneurs d’objets blob créés par le cluster HDInsight à la demande
Avec le service lié HDInsight à la demande, un cluster HDInsight est créé à chaque fois qu’une tranche doit être traitée, à moins qu’il existe un cluster activé (timeToLive). Le cluster est supprimé une fois le traitement terminé. Pour chaque cluster, Azure Data Factory crée un conteneur d’objets blob dans le stockage Blob Azure utilisé comme compte de stockage par défaut pour le cluster. Bien que le cluster HDInsight soit supprimé, le conteneur de stockage d’objets blob par défaut et le compte de stockage associé ne sont pas supprimés. Ce comportement est normal. Comme un nombre croissant de tranches sont traitées, vous voyez un grand nombre de conteneurs dans votre stockage d’objets blob Azure. Si vous n’en avez pas besoin pour dépanner les travaux, il se peut que vous deviez les supprimer pour réduire les frais de stockage. Les noms de ces conteneurs sont conformes au modèle suivant : `adfyourdatafactoryname-linkedservicename-datetimestamp`.

Supprimez les dossiers **adfjobs** et **adfyourdatafactoryname-linkedservicename-datetimestamp**. Le conteneur adfjobs contient les journaux de travaux d’Azure Data Factory.

### <a name="delete-the-resource-group"></a>Supprimer le groupe de ressources
[Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) est utilisé pour déployer, gérer et surveiller votre solution en tant que groupe.  La suppression d’un groupe de ressources supprime tous les composants qu’il contient.  

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Cliquez sur **Groupes de ressources** dans le volet de gauche.
3. Cliquez sur le nom du groupe de ressources que vous avez créé dans votre script PowerShell. Utilisez le filtre si la liste des groupes de ressources est trop longue. Il ouvre le groupe de ressources dans un nouveau panneau.
4. Dans la mosaïque **Ressources**, vous devez voir le compte de stockage par défaut et la fabrique de données, sauf si vous partagez le groupe de ressources avec d’autres projets.
5. Cliquez sur **Supprimer** dans la partie supérieure du panneau. Ce faisant, vous supprimez le compte de stockage et les données stockées dans ce dernier.
6. Entrez le nom du groupe de ressources pour confirmer la suppression, puis cliquez sur **Supprimer**.

Si vous ne souhaitez pas supprimer le compte de stockage en même temps que le groupe de ressources, envisagez l’architecture suivante en séparant les données métiers du compte de stockage par défaut. Dans ce cas, vous disposez d’un groupe de ressources pour le compte de stockage avec les données métiers, et d’un autre groupe de ressources pour le compte de stockage par défaut pour le service lié HDInsight et la fabrique de données. La suppression du second groupe de ressources n’a aucune incidence sur le compte de stockage des données métiers. Pour ce faire :

* Ajoutez le code suivant au groupe de ressources de niveau supérieur avec la ressource Microsoft.DataFactory/datafactories dans votre modèle Resource Manager. Ce code crée un compte de stockage :

    ```json
    {
        "name": "[parameters('defaultStorageAccountName')]",
        "type": "Microsoft.Storage/storageAccounts",
        "location": "[parameters('location')]",
        "apiVersion": "[variables('defaultApiVersion')]",
        "dependsOn": [ ],
        "tags": {

        },
        "properties": {
            "accountType": "Standard_LRS"
        }
    },
    ```
* Ajoutez un nouveau point de service lié au nouveau compte de stockage :

    ```json
    {
        "dependsOn": [ "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]" ],
        "type": "linkedservices",
        "name": "[variables('defaultStorageLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "AzureStorage",
            "typeProperties": {
                "connectionString": "[concat('DefaultEndpointsProtocol=https;AccountName=',parameters('defaultStorageAccountName'),';AccountKey=',listKeys(resourceId('Microsoft.Storage/storageAccounts', variables('defaultStorageAccountName')), variables('defaultApiVersion')).key1)]"
            }
        }
    },
    ```
* Configurez le LinkedService HDInsight à la demande avec un dependsOn supplémentaire et un additionalLinkedServiceNames :

    ```json
    {
        "dependsOn": [
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('defaultStorageLinkedServiceName'))]",
            "[concat('Microsoft.DataFactory/dataFactories/', parameters('dataFactoryName'), '/linkedservices/', variables('storageLinkedServiceName'))]"

        ],
        "type": "linkedservices",
        "name": "[variables('hdInsightOnDemandLinkedServiceName')]",
        "apiVersion": "[variables('apiVersion')]",
        "properties": {
            "type": "HDInsightOnDemand",
            "typeProperties": {
                "version": "3.5",
                "clusterSize": 1,
                "timeToLive": "00:05:00",
                "osType": "Linux",
                "sshUserName": "myuser",                            
                "sshPassword": "MyPassword!",
                "linkedServiceName": "[variables('storageLinkedServiceName')]",
                "additionalLinkedServiceNames": "[variables('defaultStorageLinkedServiceName')]"
            }
        }
    },            
    ```
## <a name="next-steps"></a>Étapes suivantes
Dans cet article, vous avez appris comment utiliser Azure Data Factory pour créer un cluster HDInsight à la demande pour traiter des tâches Hive. En savoir plus :

* [Didacticiel Hadoop : prise en main de Hadoop sous Linux dans HDInsight](hadoop/apache-hadoop-linux-tutorial-get-started.md)
* [Création de clusters Hadoop basés sur Linux dans HDInsight](hdinsight-hadoop-provision-linux-clusters.md)
* [Documentation HDInsight](https://azure.microsoft.com/documentation/services/hdinsight/)
* [Documentation Data Factory](https://azure.microsoft.com/documentation/services/data-factory/)

## <a name="appendix"></a>Annexe

### <a name="azure-cli-script"></a>Script d’interface de ligne de commande Azure
Pour suivre ce didacticiel, vous pouvez utiliser l’interface de ligne de commande Azure plutôt qu’Azure PowerShell. Pour utiliser l’interface de ligne de commande Azure, commencez par installer cette interface en suivant les instructions suivantes :

[!INCLUDE [use-latest-version](../../includes/hdinsight-use-latest-cli.md)]

#### <a name="use-azure-cli-to-prepare-the-storage-and-copy-the-files"></a>Utiliser l’interface de ligne de commande Azure pour préparer le stockage et copier les fichiers

```
azure login

azure config mode arm

azure group create --name "<Azure Resource Group Name>" --location "East US 2"

azure storage account create --resource-group "<Azure Resource Group Name>" --location "East US 2" --type "LRS" <Azure Storage Account Name>

azure storage account keys list --resource-group "<Azure Resource Group Name>" "<Azure Storage Account Name>"
azure storage container create "adfgetstarted" --account-name "<Azure Storage AccountName>" --account-key "<Azure Storage Account Key>"

azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/inputdata/input.log" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
azure storage blob copy start "https://hditutorialdata.blob.core.windows.net/adfhiveactivity/script/partitionweblogs.hql" --dest-account-name "<Azure Storage Account Name>" --dest-account-key "<Azure Storage Account Key>" --dest-container "adfgetstarted"
```

Le nom du conteneur est *adfgetstarted*. Gardez-le tel quel. Dans le cas contraire, vous devez mettre à jour le modèle Resource Manager. Si vous avez besoin d’aide avec ce script d’interface de ligne de commande, consultez [Utilisation de la CLI Microsoft Azure avec Microsoft Azure Storage](../storage/common/storage-azure-cli.md).
