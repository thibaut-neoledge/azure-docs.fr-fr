---
title: "Utilisation d’Azure CLI 2.0 avec le stockage Azure | Microsoft Docs"
description: "Découvrez comment utiliser Azure CLI 2.0 avec le stockage Azure pour créer et gérer des comptes de stockage et utiliser des fichiers et objets blob Azure. Azure CLI 2.0 est un outil multiplateforme écrit en Python."
services: storage
documentationcenter: na
author: mmacy
manager: timlt
editor: tysonn
ms.assetid: 
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: azurecli
ms.topic: article
ms.date: 02/18/2017
ms.author: marsma
translationtype: Human Translation
ms.sourcegitcommit: b0c27ca561567ff002bbb864846b7a3ea95d7fa3
ms.openlocfilehash: 5b77598e76de3508d90b35ce5a1f2ee338aca0c8
ms.lasthandoff: 04/25/2017


---
# <a name="using-the-azure-cli-20-with-azure-storage"></a>Utilisation d’Azure CLI 2.0 avec le stockage Azure

L’interface Azure CLI 2.0 multiplateforme et open source offre un ensemble de commandes dédiées à l’utilisation de la plateforme Azure. Elle offre des fonctionnalités similaires à celles du [portail Azure](https://portal.azure.com), notamment l’accès étendu aux données.

Dans ce guide, nous vous montrons comment utiliser [Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2) pour effectuer plusieurs tâches à l’aide de ressources dans votre compte de stockage Azure. Nous vous recommandons de télécharger et d’installer ou de mettre à niveau la CLI 2.0 vers la dernière version avant d’utiliser ce guide.

Les exemples dans le guide partent du principe que vous utilisez le shell Bash sur Ubuntu, mais les autres plateformes devraient fonctionner de manière similaire. 

[!INCLUDE [storage-cli-versions](../../includes/storage-cli-versions.md)]

## <a name="prerequisites"></a>Composants requis
Ce guide part du principe que vous comprenez les concepts de base de Microsoft Azure Storage. Il suppose également que vous êtes en mesure de satisfaire les exigences de création de compte spécifiées ci-dessous pour Azure et le service Stockage.

### <a name="accounts"></a>Comptes
* **Compte Azure** : si vous ne possédez pas encore d’abonnement Azure, [créez un compte Azure gratuit](https://azure.microsoft.com/free/).
* **Compte de stockage** : voir la section [Créer un compte de stockage](../storage/storage-create-storage-account.md#create-a-storage-account) de l’article [À propos des comptes de stockage Azure](../storage/storage-create-storage-account.md).

### <a name="install-the-azure-cli-20"></a>Installer Azure CLI 2.0

Téléchargez et installez Azure CLI 2.0 en suivant les instructions fournies dans la section [Installer Azure CLI 2.0](/cli/azure/install-az-cli2).

> [!TIP]
> Si vous rencontrez un problème avec l’installation, consultez la section [Dépannage de l’installation](/cli/azure/install-az-cli2#installation-troubleshooting) de l’article et le guide [Dépannage de l’installation](https://github.com/Azure/azure-cli/blob/master/doc/install_troubleshooting.md) sur GitHub.
>

## <a name="working-with-the-cli"></a>Utilisation de la CLI

Une fois la CLI installée, vous pouvez utiliser la commande `az` de votre interface de ligne de commande (invite de ligne de commande, Terminal, Bash) afin d’accéder aux commandes de la CLI Azure. Entrez la commande `az` et vous devriez voir une sortie similaire à ce qui suit :

```
     /\
    /  \    _____   _ _ __ ___
   / /\ \  |_  / | | | \'__/ _ \
  / ____ \  / /| |_| | | |  __/
 /_/    \_\/___|\__,_|_|  \___|


Welcome to the cool new Azure CLI!

Here are the base commands:

    account   : Commands to manage subscriptions.
    acr       : Commands to manage Azure container registries.
    acs       : Commands to manage Azure container services.
    ad        : Synchronize on-premises directories and manage Azure Active Directory (AAD)
                resources.
    appservice: Commands to manage your Azure web apps and App Service plans.
    cloud     : Manage the Azure clouds registered.
    component : Commands to manage and update Azure CLI 2.0 components.
    configure : Configure Azure CLI 2.0 or view your configuration. The command is
                interactive, so just type `az configure` and respond to the prompts.
    container : Set up automated builds and deployments for multi-container Docker applications.
    disk      : Commands to manage 'Managed Disks'.
    feature   : Commands to manage resource provider features, such as previews.
    feedback  : Loving or hating the CLI?  Let us know!
    group     : Commands to manage resource groups.
    image     : Commands to manage custom virtual machine images based on managed disks/snapshots.
    lock
    login     : Log in to access Azure subscriptions.
    logout    : Log out to remove access to Azure subscriptions.
    network   : Manages Network resources.
    policy    : Commands to manage resource policies.
    provider  : Manage resource providers.
    resource  : Generic commands to manage Azure resources.
    role      : Use role assignments to manage access to your Azure resources.
    snapshot  : Commands to manage snapshots.
    storage   : Durable, highly available, and massively scalable cloud storage.
    tag       : Manage resource tags.
    vm        : Provision Linux and Windows virtual machines in minutes.
    vmss      : Create highly available, auto-scalable Linux or Windows virtual machines.
```

Dans l’interface de ligne de commande, exécutez la commande `az storage -h` pour répertorier les commandes du groupe `storage` et ses sous-groupes. Les descriptions des sous-groupes fournissent une vue d’ensemble de la fonctionnalité que la CLI Azure fournit pour l’utilisation de vos ressources de stockage.

```
Group
    az storage: Durable, highly available, and massively scalable cloud storage.

Subgroups:
    account  : Manage storage accounts.
    blob     : Object storage for unstructured data.
    container: Manage blob storage containers.
    cors     : Manage Storage service Cross-Orgin Resource Sharing (CORS).
    directory: Manage file storage directories.
    entity   : Manage table storage entities.
    file     : File shares that use the standard SMB 3.0 protocol.
    logging  : Manage Storage service logging information.
    message  : Manage queue storage messages.
    metrics  : Manage Storage service metrics.
    queue    : Effectively scale apps according to traffic using queues.
    share    : Manage file shares.
    table    : NoSQL key-value storage using semi-structured datasets.
```

## <a name="connect-the-cli-to-your-azure-subscription"></a>Connexion de la CLI à votre abonnement Azure

Pour utiliser les ressources dans votre abonnement Azure, vous devez tout d’abord vous connecter à votre compte Azure avec `az login`. Vous pouvez vous connecter de plusieurs façons :

* **Connexion interactive** : `az login`
* **Connexion avec nom d’utilisateur et mot de passe** : `az login -u johndoe@contoso.com -p VerySecret`
  * Ceci ne fonctionne pas avec les comptes Microsoft ou les comptes qui utilisent l’authentification multifacteur.
* **Connexion avec un principal du service** : `az login --service-principal -u http://azure-cli-2016-08-05-14-31-15 -p VerySecret --tenant contoso.onmicrosoft.com`

## <a name="azure-cli-20-sample-script"></a>Exemple de script CLI 2.0 Azure

Ensuite, nous allons utiliser un petit script shell qui émet plusieurs commandes CLI 2.0 Azure de base pour interagir avec les ressources de stockage Azure. Tout d’abord, le script crée un nouveau conteneur dans votre compte de stockage, puis télécharge un fichier existant (comme un objet blob) dans ce conteneur. Ensuite, il répertorie tous les objets blob dans le conteneur et pour terminer, il télécharge le fichier vers une destination sur l’ordinateur local que vous spécifiez.

```bash
#!/bin/bash
# A simple Azure Storage example script

export AZURE_STORAGE_ACCOUNT=<storage_account_name>
export AZURE_STORAGE_ACCESS_KEY=<storage_account_key>

export container_name=<container_name>
export blob_name=<blob_name>
export file_to_upload=<file_to_upload>
export destination_file=<destination_file>

echo "Creating the container..."
az storage container create -n $container_name

echo "Uploading the file..."
az storage blob upload -f $file_to_upload -c $container_name -n $blob_name

echo "Listing the blobs..."
az storage blob list -c $container_name

echo "Downloading the file..."
az storage blob download -c $container_name -n $blob_name -f $destination_file

echo "Done"
```

**Configuration et exécution du script**

1. Ouvrez votre éditeur de texte préféré, puis copiez et collez le script précédent dans l’éditeur.

2. Ensuite, mettez à jour les variables du script pour refléter vos paramètres de configuration. Remplacez les valeurs suivantes comme indiqué :

   * **\<storage_account_name\>** : nom de votre compte de stockage.
   * **\<storage_account_key\>** : clé d’accès primaire ou secondaire pour votre compte de stockage.
   * **\<container_name\>** : nom du nouveau conteneur à créer, par exemple « azure-cli-sample-container ».
   * **\<blob_name\>** : nom de l’objet blob de destination dans le conteneur.
   * **\<file_to_upload\>** : chemin d’accès au petit fichier sur l’ordinateur local, par exemple « ~/images/HelloWorld.png ».
   * **\<destination_file\>** : chemin d’accès au fichier de destination, par exemple « ~/downloadedImage.png ».

3. Une fois que vous avez mis à jour les variables nécessaires, enregistrez le script et quittez l’éditeur. Les étapes suivantes supposent que vous avez nommé votre script **my_storage_sample.sh**.

4. Rendez le script exécutable, si nécessaire : `chmod +x my_storage_sample.sh`

5. Exécutez le script. Par exemple, dans Bash : `./my_storage_sample.sh`

Vous devriez voir une sortie similaire à ce qui suit et la variable **\<destination_file\>** spécifiée dans le script doit s’afficher sur votre ordinateur local.

```
Creating the container...
Success
---------
True
Uploading the file...                                           Percent complete: %100.0
Listing the blobs...
Name           Blob Type      Length  Content Type              Last Modified
-------------  -----------  --------  ------------------------  -------------------------
test_blob.txt  BlockBlob         771  application/octet-stream  2016-12-21T15:35:30+00:00
Downloading the file...
Name
-------------
test_blob.txt
Done
```

> [!TIP]
> La sortie précédente est au format **table**. Vous pouvez spécifier le format de sortie à utiliser en spécifiant l’argument `--output` dans vos commandes CLI ou en le définissant globalement à l’aide de `az configure`.
>

## <a name="manage-storage-accounts"></a>Gestion des comptes de stockage

### <a name="create-a-new-storage-account"></a>Création d’un nouveau compte de stockage
Pour utiliser le Stockage Azure, vous avez besoin d’un compte de stockage. Vous pouvez créer un nouveau compte de stockage Azure après avoir configuré votre ordinateur pour qu’il se [connecte à votre abonnement](#connect-to-your-azure-subscription).

```azurecli
az storage account create -l <location> -n <account_name> -g <resource_group> --sku <account_sku>
```

* `-l` [Obligatoire] : emplacement. Par exemple, « États-Unis de l’Ouest ».
* `-n` [Obligatoire] : nom du compte de stockage. Le nom doit comporter entre 3 et 24 caractères, lesquels ne peuvent être que des caractères alphanumériques minuscules.
* `-g` [Obligatoire] : nom du groupe de ressources.
* `--sku` [Obligatoire] : référence du compte de stockage. Valeurs autorisées :
  * `Premium_LRS`
  * `Standard_GRS`
  * `Standard_LRS`
  * `Standard_RAGRS`
  * `Standard_ZRS`

### <a name="set-default-azure-storage-account-environment-variables"></a>Définition des variables d’environnement par défaut pour le compte de stockage Azure
Vous pouvez disposer de plusieurs comptes de stockage dans votre abonnement Azure. Pour en sélectionner un à utiliser pour toutes les commandes de stockage suivantes, vous pouvez définir ces variables d’environnement :

```azurecli
export AZURE_STORAGE_ACCOUNT=<account_name>
export AZURE_STORAGE_ACCESS_KEY=<key>
```

Vous pouvez également définir un compte de stockage par défaut via une chaîne de connexion. Commencez par obtenir la chaîne de connexion à l’aide de la commande `show-connection-string` :

```azurecli
az storage account show-connection-string -n <account_name> -g <resource_group>
```

Ensuite, copiez la chaîne de connexion de sortie et définissez la variable d’environnement `AZURE_STORAGE_CONNECTION_STRING` (vous devrez peut-être mettre la chaîne de connexion entre guillemets) :

```azurecli
export AZURE_STORAGE_CONNECTION_STRING=<connection_string>
```

> [!NOTE]
> Tous les exemples des sections suivantes de cet article supposent que vous avez défini les variables d’environnement `AZURE_STORAGE_ACCOUNT` et `AZURE_STORAGE_ACCESS_KEY`.
>

## <a name="create-and-manage-blobs"></a>Créer et gérer des objets blob
Le stockage d’objets blob Azure est un service permettant de stocker de gros volumes de données non structurées, telles que du texte ou des données binaires, accessibles depuis n’importe où dans le monde via HTTP ou HTTPS. Cette section suppose que vous êtes déjà familiarisé avec les concepts du Stockage Blob Azure. Pour obtenir des informations détaillées, consultez [Prise en main du Stockage Blob Azure à l’aide de .NET](storage-dotnet-how-to-use-blobs.md) et [Concepts de service Blob](/rest/api/storageservices/blob-service-concepts).

### <a name="create-a-container"></a>Créer un conteneur
Chaque objet blob du stockage Azure doit se trouver dans un conteneur. Vous pouvez créer un conteneur à l’aide de la commande `az storage container create` :

```azurecli
az storage container create -n <container_name>
```

Vous pouvez définir l’un des trois niveaux d’accès en lecture pour un nouveau conteneur en spécifiant l’argument facultatif `--public-access` :

* `off` (par défaut) : les données du conteneur sont privées et accessibles uniquement par le propriétaire du compte.
* `blob` : accès en lecture public pour les objets blob.
* `container` : accès en lecture et en création de listes public à l’intégralité du conteneur.

Pour plus d’informations, consultez la section [Gestion de l’accès en lecture anonyme aux conteneurs et aux objets blob](storage-manage-access-to-resources.md).

### <a name="upload-a-blob-to-a-container"></a>Chargement d’un objet blob dans un conteneur
Le Stockage Blob Azure prend en charge les objets blob de blocs, d’ajout et de page. Chargez des objets blob dans un conteneur à l’aide de la commande `blob upload` :

```azurecli
az storage blob upload -f <local_file_path> -c <container_name> -n <blob_name>
```

 Sinon, la commande `blob upload` charge par défaut les fichiers *.vhd dans les objets blob de pages ou de blocs. Pour spécifier un autre type lorsque vous chargez un objet blob, vous pouvez utiliser l’argument `--type` ; les valeurs autorisée sont `append`, `block` et `page`.

 Pour plus d’informations sur les différents objets blob, consultez [Présentation des objets blob de blocs, des objets blob d’ajout et des objets blob de pages](/rest/api/storageservices/Understanding-Block-Blobs--Append-Blobs--and-Page-Blobs).

### <a name="download-blobs-from-a-container"></a>Télécharger des objets blob depuis un conteneur
Cet exemple indique comment télécharger un objet blob à partir d’un conteneur :

```azurecli
az storage blob download -c mycontainer -n myblob.png -f ~/mydownloadedblob.png
```

### <a name="copy-blobs"></a>Copier des objets blob
Vous pouvez copier des objets blob au sein d’un compte de stockage, ou vers des comptes de stockage et des régions et ce, de façon asynchrone.

L’exemple suivant indique comment copier des objets blob depuis un compte de stockage et les coller dans un autre. Nous créons tout d’abord un conteneur dans un autre compte, en spécifiant que ses objets blob sont accessibles de manière publique et anonyme. Ensuite, nous chargeons un fichier dans le conteneur et, enfin, nous copions l’objet blob à partir de ce conteneur dans le conteneur **mycontainer** du compte actuel.

```azurecli
az storage container create -n mycontainer2 --account-name <accountName2> --account-key <accountKey2> --public-access blob

az storage blob upload -f ~/Images/HelloWorld.png -c mycontainer2 -n myBlockBlob2 --account-name <accountName2> --account-key <accountKey2>

az storage blob copy start -u https://<accountname2>.blob.core.windows.net/mycontainer2/myBlockBlob2 -b myBlobBlob -c mycontainer
```

L’URL de l’objet blob source (spécifiée par `-u`) doit être accessible au public ou inclure un jeton de signature d’accès partagé.

### <a name="delete-a-blob"></a>Supprimer un objet blob
Pour supprimer un objet blob, utilisez la commande `blob delete` :

```azurecli
az storage blob delete -c <container_name> -n <blob_name>
```

## <a name="create-and-manage-file-shares"></a>Créer et gérer des partages de fichiers
Le Stockage Fichier Azure propose un stockage partagé pour les applications utilisant le protocole SMB. Les services cloud et les machines virtuelles Microsoft Azure, ainsi que les applications locales, peuvent partager des données de fichiers via des partages montés. Vous pouvez gérer des partages de fichiers et des données de fichiers via la CLI Azure. Pour plus d’informations sur Azure File Storage, consultez [Prise en main du stockage de fichiers Azure sur Windows](storage-dotnet-how-to-use-files.md) ou [Utilisation du stockage de fichiers Azure avec Linux](storage-how-to-use-files-linux.md).

### <a name="create-a-file-share"></a>Créer un partage de fichiers
Un partage de fichiers Azure est un partage de fichiers SMB dans Microsoft Azure. Tous les répertoires et fichiers doivent être créés dans un partage de fichiers. Un compte peut contenir un nombre illimité de partages, et un partage peut stocker un nombre illimité de fichiers, dans les limites de capacité du compte de stockage. L’exemple suivant détaille la création d’un partage de fichiers nommé **MonPartage**.

```azurecli
az storage share create -n myshare
```

### <a name="create-a-directory"></a>Créer un répertoire
Un répertoire fournit une structure hiérarchique facultative pour un partage de fichiers Microsoft Azure. L’exemple suivant crée un répertoire nommé **MonRép** dans le partage de fichiers.

```azurecli
az storage directory create -n myDir -s myshare
```

Remarque : ce chemin d’accès au répertoire peut inclure plusieurs niveaux, *par exemple*: **a/b**. Cependant, vous devez vous assurer que tous les répertoires parents existent. Par exemple, pour le chemin d’accès **a/b**, vous devez créer le répertoire **a**, puis le répertoire **b**.

### <a name="upload-a-local-file-to-a-share"></a>Chargement d’un fichier local vers un partage
Dans l’exemple suivant, un fichier est chargé à partir de l’emplacement **~/temp/samplefile.txt** vers la racine du partage de fichiers **myshare**. L’argument `--source` spécifie le fichier local existant à charger.

```azurecli
az storage file upload --share-name myshare --source ~/temp/samplefile.txt
```

Comme pour la création d’un répertoire, vous pouvez spécifier un chemin d’accès au répertoire dans le partage pour charger le fichier vers un répertoire existant dans le partage :

```azurecli
az storage file upload --share-name myshare/myDir --source ~/temp/samplefile.txt
```

Dans le partage, un fichier peut présenter une taille maximale de 1 To.

### <a name="list-the-files-in-a-share"></a>Liste des fichiers dans un partage
Vous pouvez répertorier les fichiers et les répertoires dans un partage à l’aide de la commande `az storage file list` :

```azurecli
# List the files in the root of a share
az storage file list -s myshare

# List the files in a directory within a share
az storage file list -s myshare/myDir

# List the files in a path within a share
az storage file list -s myshare -p myDir/mySubDir/MySubDir2
```

### <a name="copy-files"></a>Copie des fichiers        
Vous pouvez copier un fichier dans un autre, un fichier dans un objet blob ou un objet blob dans un fichier. Par exemple, pour copier un fichier vers un répertoire dans un partage différent :        
        
```azurecli
az storage file copy start \
--source-share share1 --source-path dir1/file.txt \
--destination-share share2 --destination-path dir2/file.txt        
```

## <a name="next-steps"></a>Étapes suivantes
Voici quelques ressources supplémentaires pour en savoir plus sur l’utilisation d’Azure CLI 2.0.

* [Prise en main d’Azure CLI 2.0](https://docs.microsoft.com/cli/azure/get-started-with-az-cli2)
* [Référence des commandes Azure CLI 2.0](/cli/azure)
* [Azure CLI 2.0 sur GitHub](https://github.com/Azure/azure-cli)

