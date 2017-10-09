---
title: "Créer une fabrique de données Azure à l’aide de Python | Microsoft Docs"
description: "Créez une fabrique de données Azure pour copier les données d’un emplacement dans un stockage Blob Azure vers un autre emplacement dans le même stockage Blob."
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: spelluru
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: 
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 09/19/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 9c6f7c8e052aed2ab5aba2ee54065bf40e81a022
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---

# <a name="create-a-data-factory-and-pipeline-using-python"></a>Créer une fabrique de données et un pipeline à l’aide de Python
Azure Data Factory est un service d’intégration de données basé sur le cloud qui vous permet de créer des flux de travail orientés données dans le cloud pour orchestrer et automatiser le déplacement et la transformation des données. Avec Azure Data Factory, vous pouvez créer et planifier des flux de travail orientés données (appelés pipelines) capables d’ingérer des données provenant de différents magasins de données, de traiter/transformer les données à l’aide de services de calcul comme Azure HDInsight Hadoop, Spark, Azure Data Lake Analytics et Azure Machine Learning, et de publier des données de sortie dans des magasins de données tels qu’Azure SQL Data Warehouse pour que des applications décisionnelles (BI) puissent les utiliser. 

Ce guide de démarrage rapide explique comment utiliser Python pour créer une fabrique de données Azure. Le pipeline dans cette fabrique de données copie les données d’un dossier vers un autre dossier dans un stockage Blob Azure.

Si vous n’avez pas d’abonnement Azure, créez un compte [gratuit](https://azure.microsoft.com/free/) avant de commencer.

## <a name="prerequisites"></a>Composants requis

* **Compte Stockage Azure**. Vous utilisez le stockage Blob comme magasins de données **source** et **récepteur**. Si vous n’avez pas de compte de stockage Azure, consultez l’article [Créer un compte de stockage](../storage/common/storage-create-storage-account.md#create-a-storage-account) pour découvrir comment en créer un. 
* **Créez une application dans Azure Active Directory** en suivant [cette instruction](../azure-resource-manager/resource-group-create-service-principal-portal.md#create-an-azure-active-directory-application). Notez les valeurs suivantes que vous utiliserez lors d’étapes ultérieures : **ID d’application**, **clé d’authentification** et **ID de locataire**. Affectez l’application au rôle « **Contributeur**  » en suivant les instructions dans le même article. 

### <a name="create-and-upload-an-input-file"></a>Créer et charger un fichier d’entrée

1. Lancez le Bloc-notes. Copiez le texte suivant et enregistrez-le comme fichier **input.txt** sur votre disque.
    
    ```
    John|Doe
    Jane|Doe
    ```
2.  Utilisez des outils comme l’[explorateur Stockage Azure](http://storageexplorer.com/) pour créer le conteneur **adfv2tutorial** et charger le fichier input.txt sur ce dernier. 

## <a name="install-the-python-package"></a>Installer le package Python
1. Ouvrez un terminal ou une invite de commandes avec des privilèges d’administrateur.  
2. Pour installer le package Python pour Data Factory, exécutez la commande suivante :

    ```
    pip install azure-mgmt-datafactory
    ```

    Le [SDK Python pour Data Factory](https://github.com/Azure/azure-sdk-for-python) prend en charge Python 2.7, 3.3, 3.4, 3.5 et 3.6.
## <a name="create-a-data-factory-client"></a>Créer un client de fabrique de données

1. Créez un fichier nommé **datafactory.py**. Insérez les instructions suivantes pour ajouter des références aux espaces de noms.
    
    ```python
    from azure.common.credentials import ServicePrincipalCredentials
    from msrestazure.azure_cloud import Cloud, CloudEndpoints, CloudSuffixes
    from azure.mgmt.resource import ResourceManagementClient
    from azure.mgmt.datafactory import DataFactoryManagementClient
    from azure.mgmt.datafactory.models import *
    import json
    import time    
    ```
2. Ajoutez les fonctions suivantes qui affichent les informations. 

    ```python
    def print_item(group):
      """Print an Azure object instance."""
      print("\tName: {}".format(group.name))
      print("\tId: {}".format(group.id))
      if hasattr(group, 'location'):
          print("\tLocation: {}".format(group.location))
      if hasattr(group, 'tags'):
          print("\tTags: {}".format(group.tags))
      if hasattr(group, 'properties'):
          print_properties(group.properties)
    
    def print_properties(props):
      """Print a ResourceGroup properties instance."""
      if props and hasattr(props, 'provisioning_state') and props.provisioning_state:
          print("\tProperties:")
          print("\t\tProvisioning State: {}".format(props.provisioning_state))
      print("\n\n")    
    ```
3. Ajoutez le code suivant à la méthode **Main** qui crée une instance de la classe DataFactoryManagementClient. Cet objet vous permet de créer la fabrique de données, un service lié, des jeux de données ainsi qu’un pipeline. Cet objet vous permet également de surveiller les détails de l’exécution du pipeline.

    ```python   
    subscription_id = '<your subscription ID where the factory resides>'
    credentials = ServicePrincipalCredentials(
            client_id='<yourClientId>',
            secret='<YourPassword>',
            tenant='<YourTenandId>'
    )
    resource_client = ResourceManagementClient(credentials, subscription_id)
    adf_client = DataFactoryManagementClient(credentials, subscription_id)
    
    rg_params = {'location':'eastus'}
    df_params = {'location':'eastus'}
    rg_name = '<Your Resource Group Name>'
    df_name = '<Your Data Factory Name>'
    ```

## <a name="create-a-data-factory"></a>Créer une fabrique de données

Ajoutez le code suivant à la méthode **Main** qui crée une **fabrique de données**. 

```python
#Create Resource Group
resource_client.resource_groups.create_or_update(rg_name, rg_params)

#Create Data Factory
df_resource = Factory(location='eastus')
df = adf_client.factories.create_or_update(rg_name, df_name, df_resource)
print_item(df)
while df.provisioning_state != 'Succeeded':
    df = adf_client.factories.get(rg_name, df_name)
    time.sleep(1)
print_item(adf_client.factories.get(rg_name, df_name))
```

## <a name="create-a-linked-service"></a>Créer un service lié

Ajoutez le code suivant à la méthode **Main** qui crée un **service lié Azure Storage**.

Vous allez créer des services liés dans une fabrique de données pour lier vos magasins de données et vos services de calcul à la fabrique de données. Dans ce guide de démarrage rapide, vous devez uniquement créer un service lié Azure Storage pour la source de copie et le magasin récepteur, nommé « AzureStorageLinkedService » dans l’exemple.

```python
#Create Storage Linked Service
ls_name = 'storageLinkedService'

#Replace Storage String with your credentials
storage_string = SecureString('DefaultEndpointsProtocol=https;AccountName=<replace>;AccountKey=<replace>')

ls_azure_storage = AzureStorageLinkedService(connection_string=storage_string)
ls = adf_client.linked_services.create_or_update(rg_name, df_name, ls_name, ls_azure_storage)
print_item(ls)
```
## <a name="create-datasets"></a>Créez les jeux de données
Dans cette section, vous créez deux jeux de données : un pour la source et l’autre pour le récepteur.

### <a name="create-a-dataset-for-source-azure-blob"></a>Créer un jeu de données pour l’objet blob Azure source
Ajoutez le code suivant à la méthode Main qui crée un jeu de données d’objet blob Azure. Pour plus d’informations sur les propriétés du jeu de données d’objet blob Azure, consultez l’article [Connecteur d’objets blob Azure](connector-azure-blob-storage.md#dataset-properties). 

Vous définissez un jeu de données qui représente les données sources dans l’objet blob Azure. Ce jeu de données d’objet blob fait référence au service lié Azure Storage que vous avez créé à l’étape précédente.

```python
#Create Dataset Input
ds_name = 'ds_in'
ds_ls = LinkedServiceReference(ls_name)
blob_path= 'adfv2tutorial/'
blob_filename = 'input.txt'
ds_azure_blob= AzureBlobDataset(ds_ls, folder_path=blob_path, file_name = blob_filename)
ds = adf_client.datasets.create_or_update(rg_name, df_name, ds_name, ds_azure_blob)
print_item(ds)
```

### <a name="create-a-dataset-for-sink-azure-blob"></a>Créer un jeu de données pour l’objet blob Azure récepteur
Ajoutez le code suivant à la méthode Main qui crée un jeu de données d’objet blob Azure. Pour plus d’informations sur les propriétés du jeu de données d’objet blob Azure, consultez l’article [Connecteur d’objets blob Azure](connector-azure-blob-storage.md#dataset-properties). 

Vous définissez un jeu de données qui représente les données sources dans l’objet blob Azure. Ce jeu de données d’objet blob fait référence au service lié Azure Storage que vous avez créé à l’étape précédente.

```python
#Create Dataset Output
dsOut_name = 'ds_out'
output_blobpath = 'output/'
dsOut_azure_blob = AzureBlobDataset(ds_ls, folder_path=output_blobpath)
dsOut = adf_client.datasets.create_or_update(rg_name, df_name, dsOut_name, dsOut_azure_blob)
print_item(dsOut)
```

## <a name="create-a-pipeline"></a>Créer un pipeline

Ajoutez le code suivant à la méthode **Main** qui crée un **pipeline avec une activité de copie**.

```python
#Create 1st activity: Copy Activity
act_name =  'copyBlobtoBlob'
blob_source = BlobSource()
blob_sink = BlobSink()
dsin_ref = DatasetReference(ds_name)
dsOut_ref = DatasetReference(dsOut_name)
copy_activity = CopyActivity(act_name,inputs=[dsin_ref], outputs=[dsOut_ref], source=blob_source, sink=blob_sink)

#Create Pipeline
p_name =  'copyPipeline'
params_for_pipeline = {}
p_obj = PipelineResource(activities=[copy_activity], parameters=params_for_pipeline)
p = adf_client.pipelines.create_or_update(rg_name, df_name, p_name, p_obj)
print_item(p)
```


## <a name="create-a-pipeline-run"></a>Créer une exécution du pipeline

Ajoutez le code suivant à la méthode **Main** qui **déclenche une exécution du pipeline**.

```python
#Create Pipeline Run
print(adf_client.pipelines.create_run(rg_name, df_name, p_name,
    {
    }
))
```

## <a name="run-the-code"></a>Exécution du code
Créez et démarrez l’application, puis vérifiez l’exécution du pipeline.

La console affiche la progression de la création de la fabrique de données, du service lié, des jeux de données, du pipeline et de l’exécution du pipeline. Patientez jusqu’à l’affichage des détails de l’exécution de l’activité de copie avec la taille des données lues/écrites. Utilisez ensuite des outils comme l’[explorateur Stockage Azure](https://azure.microsoft.com/features/storage-explorer/) pour vérifier que les objets blob sont copiés dans « outputBlobPath » depuis « inputBlobPath » comme vous l’avez spécifié dans les variables.


## <a name="clean-up-resources"></a>Supprimer des ressources
Pour supprimer par programmation la fabrique de données, ajoutez les lignes de code suivantes au programme : 

```csharp
adf_client.data_factories.delete(rg_name, df_name)
```

## <a name="next-steps"></a>Étapes suivantes
Le pipeline dans cet exemple copie les données d’un emplacement vers un autre emplacement dans un stockage Blob Azure. Passez en revue les [didacticiels](tutorial-copy-data-dot-net.md) pour en savoir plus sur l’utilisation de Data Factory dans d’autres scénarios. 
