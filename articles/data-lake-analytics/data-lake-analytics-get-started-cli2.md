---
title: "Prise en main d’Azure Data Lake Analytics à l’aide de l’interface de ligne de commande Azure 2.0 | Microsoft Docs"
description: "Découvrez comment utiliser l’interface de ligne de commande Azure 2.0 pour créer un compte Data Lake Analytics, créer un travail Data Lake Analytics avec U-SQL et le soumettre. "
services: data-lake-analytics
documentationcenter: 
author: mumian
manager: jhubbard
editor: cgronlun
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 04/06/2017
ms.author: jgao
translationtype: Human Translation
ms.sourcegitcommit: 538f282b28e5f43f43bf6ef28af20a4d8daea369
ms.openlocfilehash: 109460cecc4e11c729203af97c9bf1c22b90e61a
ms.lasthandoff: 04/07/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-cli-20-preview"></a>Didacticiel : Prise en main du service Azure Data Lake Analytics à l’aide de l’interface de ligne de commande Azure 2.0 (version préliminaire)
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Découvrez comment utiliser l’interface de ligne de commande Azure 2.0 pour créer des comptes Azure Data Lake Analytics, définir des travaux Data Lake Analytics dans [U-SQL](data-lake-analytics-u-sql-get-started.md), et envoyer des travaux à des comptes Data Lake Analytics. Pour plus d’informations sur Analytique Data Lake, consultez [Présentation d’Analytique Data Lake Azure](data-lake-analytics-overview.md).

Dans ce didacticiel, vous développez un travail qui lit un fichier TSV (valeurs séparées par des tabulations) et le convertit en fichier CSV (valeurs séparées par des virgules). Pour suivre ce didacticiel même à l’aide d’autres outils pris en charge, utilisez la liste déroulante en haut de cette section.

## <a name="prerequisites"></a>Composants requis
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure CLI 2.0**. Consultez [Installation et configuration de l’interface de ligne de commande Azure](https://docs.microsoft.com/cli/azure/install-azure-cli).
* **Activer Data Lake Store/Analytics CLI 2.0 (version préliminaire)**. Data Lake Store et Data Lake Analytics CLI 2.0 sont toujours en version préliminaire. Exécutez les commandes suivantes pour ouvrir les éléments suivants :

    ```azurecli
    az component update --add dls
    az component update --add dla 
    ```

## <a name="log-in-to-azure"></a>Connexion à Azure

Pour vous connecter à votre abonnement Azure :

```azurecli
az login
```

Vous êtes invité à accéder à une URL, et à entrer un code d’authentification.  Ensuite, suivez les instructions pour entrer vos informations d’identification.

Une fois que vous êtes connecté, la commande de connexion répertorie vos abonnements.

Pour utiliser un abonnement spécifique :

```azurecli
az account set --subscription <subscription id>
```

## <a name="create-data-lake-analytics-account"></a>Créer un compte Analytique Data Lake
Vous devez disposer d’un compte Data Lake Analytics avant de pouvoir exécuter des travaux quelconques. Pour créer un compte Data Lake Analytics, vous devez spécifier les éléments suivants :

* **Groupe de ressources Azure**. Un compte Data Lake Analytics doit être créé au sein d’un groupe de ressources Azure. [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) vous permet de manipuler les ressources de votre application sous la forme d’un groupe. Vous pouvez déployer, mettre à jour ou supprimer toutes les ressources de votre application dans le cadre d’une opération unique et coordonnée.  
  
    Pour répertorier les groupes de ressources existants dans votre abonnement :
  
    ```azurecli
    az group list 
    ```

    Pour créer un groupe de ressources :
  
    ```azurecli
    az group create --name "<Resource Group Name>" --location "<Azure Location>"
    ```

* **Nom du compte Data Lake Analytics**. Chaque compte Data Lake Analytics porte un nom.
* **Emplacement**. Utilisez l’un des centres de données Azure prenant en charge Data Lake Analytics.
* **Compte Data Lake Store par défaut** : chaque compte Data Lake Analytics possède un compte Data Lake Store par défaut.
  
    Pour répertorier le compte Data Lake Store existant :

    ```azurecli
    az dls account list
    ```
  
    Pour créer un compte Data Lake Store :
  
    ```azurecli
    az dls account create --account "<Data Lake Store Account Name>" --resource-group "<Resource Group Name>"
    ```

Utilisez la syntaxe suivante pour créer un compte Data Lake Analytics :

```azurecli
az dla account create --account "<Data Lake Analytics Account Name>" --resource-group "<Resource Group Name>" --location "<Azure location>" --default-data-lake-store "<Default Data Lake Store Account Name>"
```

Après avoir créé un compte, vous pouvez utiliser les commandes suivantes pour répertorier les comptes et afficher leurs détails :

```azurecli
az dla account list
az dla account show --account "<Data Lake Analytics Account Name>"            
```

## <a name="upload-data-to-data-lake-store"></a>Téléchargement de données vers Data Lake Store
Dans ce didacticiel, vous traitez des journaux de recherche.  Le journal de recherche peut être stocké dans Data Lake Store ou dans le stockage d’objets blobs Azure. 

Le portail Azure fournit une interface utilisateur pour la copie de fichiers de données d’exemple vers le compte Data Lake Store par défaut, y compris un fichier de journal de recherche. Voir [Préparer des données sources](data-lake-analytics-get-started-portal.md#prepare-source-data) pour charger les données dans le compte Data Lake Store par défaut.

Pour charger des fichiers à l’aide de l’interface de ligne de commande 2.0, utilisez les commandes suivantes :

```azurecli
az dls file upload --account "<Data Lake Store Account Name>" --source-path "<Source File Path>" --destination-path "<Destination File Path>"
az dls file list --account "<Data Lake Store Account Name>" --path "<Path>"
```

Analytique Data Lake peut également accéder au stockage d’objets blobs Azure.  Pour télécharger des données dans le Blob Storage Azure, consultez [Utilisation de la CLI Microsoft Azure avec Microsoft Azure Storage](../storage/storage-azure-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Envoyer des travaux Analytique Data Lake
Les travaux Data Lake Analytics sont écrits en langage U-SQL. Pour en savoir plus sur U-SQL, consultez [Prise en main du langage U-SQL](data-lake-analytics-u-sql-get-started.md) et [Référence du langage U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Pour créer un script de travail Analytique Data Lake**

Créez un fichier texte avec le script U-SQL suivant, puis enregistrez le fichier texte sur votre station de travail :
  
    @searchlog =
        EXTRACT UserId          int,
                Start           DateTime,
                Region          string,
                Query           string,
                Duration        int?,
                Urls            string,
                ClickedUrls     string
        FROM "/Samples/Data/SearchLog.tsv"
        USING Extractors.Tsv();

    OUTPUT @searchlog   
        TO "/Output/SearchLog-from-Data-Lake.csv"
    USING Outputters.Csv();
  
Ce script U-SQL lit le fichier de données source avec **Extractors.Tsv()**, puis crée un fichier csv à l’aide d’**Outputters.Csv()**. 

Ne modifiez pas les deux chemins d’accès, sauf si vous copiez le fichier source dans un autre emplacement.  Data Lake Analytics créera le dossier de sortie s'il n'existe pas encore.

Il est plus simple d’utiliser des chemins d’accès relatifs pour les fichiers stockés dans les comptes Data Lake Store par défaut. Vous pouvez également utiliser des chemins d’accès absolus.  Par exemple :

    adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv

Vous devez utiliser des chemins d’accès absolus pour accéder aux fichiers dans les comptes de stockage liés.  La syntaxe des fichiers stockés dans le compte de stockage Azure lié est la suivante :

    wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
  
  > [!NOTE]
  > Les conteneurs d'objets Blob Azure avec des autorisations d'accès aux objets Blob publics ou aux conteneurs publics ne sont pas pris en charge actuellement.      
  > 
  > 

**Pour soumettre les travaux**

Pour soumettre un travail, utilisez la syntaxe suivante.

```azurecli
az dla job submit --account "<Data Lake Analytics Account Name>" --job-name "<Job Name>" --script "<Script Path and Name>"
```

Par exemple :

```azurecli
az dla job submit --account "myadlaaccount" --job-name "myadlajob" --script @"C:\DLA\myscript.txt"
```

**Pour répertorier les travaux et afficher leurs détails**

```azurecli
az dla job list --account "<Data Lake Analytics Account Name>"
az dla job show --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

**Pour annuler les travaux**

```azurecli
az dla job cancel --account "<Data Lake Analytics Account Name>" --job-identity "<Job Id>"
```

##<a name="retrieve-job-results"></a>Récupérer les résultats de travaux

Une fois qu’un travail est terminé, vous pouvez utiliser les commandes suivantes pour répertorier les fichiers sortants et télécharger les fichiers :

```azurecli
az dls fs list --account "<Data Lake Store Account Name>" --source-path "/Output" --destination-path "<Destintion>"
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" 
az dls fs preview --account "<Data Lake Store Account Name>" --path "/Output/SearchLog-from-Data-Lake.csv" --length 128 --offset 0
az dls fs downlod --account "<Data Lake Store Account Name>" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "<Destination Path and File Name>"
```

Par exemple :

```azurecli
az dls fs downlod --account "myadlsaccount" --source-path "/Output/SearchLog-from-Data-Lake.csv" --destintion-path "C:\DLA\myfile.csv"
```

## <a name="next-steps"></a>Étapes suivantes

* Pour afficher le même didacticiel en utilisant d’autres outils, cliquez sur les sélecteurs d’onglet en haut de la page.
* Pour afficher le document de référence Data Lake Analytics CLI 2.0, consultez [Data Lake Analytics - az dla](https://docs.microsoft.com/cli/azure/dla).
* Pour afficher le document de référence Data Lake Store CLI 2.0, consultez [Data Lake Store - az dls](https://docs.microsoft.com/cli/azure/dls).
* Pour voir une requête plus complexe, consultez [Analyse de journaux des sites web à l'aide d'Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Pour commencer à développer des applications U-SQL, consultez [Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Pour connaître U-SQL, voir [Prise en main du langage U-SQL d’Analytique Data Lake Azure](data-lake-analytics-u-sql-get-started.md).
* Pour les tâches de gestion, consultez [Gestion d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md).
* Pour obtenir une vue d’ensemble de l’analyse de données Analytique Data Lake, consultez [Présentation d’Analytique Data Lake Azure](data-lake-analytics-overview.md).


