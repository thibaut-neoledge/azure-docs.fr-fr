---
title: "Prise en main du service Azure Data Lake Analytics à l’aide de l’interface de ligne de commande Azure (CLI) | Microsoft Docs"
description: "Découvrez comment utiliser l’interface de ligne de commande Azure pour créer un compte Data Lake Analytics, créer un travail Data Lake Analytics avec U-SQL et le soumettre. "
services: data-lake-analytics
documentationcenter: 
author: edmacauley
manager: jhubbard
editor: cgronlun
ms.assetid: 651021d4-4591-4c48-b1ef-3ebc4606d66d
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: hero-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/05/2016
ms.author: edmaca
translationtype: Human Translation
ms.sourcegitcommit: c9440e26a563ed9315225b34709b009d01e065d7
ms.openlocfilehash: ff9b0d9e098fd8c6cde7c63c005c8f1906c6e0f2


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-command-line-interface-cli"></a>Tutoriel : prise en main du service Azure Data Lake Analytics à l’aide de l’interface de ligne de commande Azure (CLI)
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Découvrez comment utiliser l’interface de ligne de commande Azure pour créer des comptes Azure Data Lake Analytics, définir des travaux Data Lake Analytics dans [U-SQL](data-lake-analytics-u-sql-get-started.md), et envoyer des travaux à des comptes Data Lake Analytics. Pour plus d’informations sur Analytique Data Lake, consultez [Présentation d’Analytique Data Lake Azure](data-lake-analytics-overview.md).

Dans ce didacticiel, vous allez développer un travail qui lit un fichier TSV (valeurs séparées par des tabulations) et le convertir en fichier CSV (valeurs séparées par des virgules). Pour suivre ce didacticiel même à l'aide d'autres outils pris en charge, cliquez sur les onglets en haut de cette section.

## <a name="prerequisites"></a>Composants requis
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Interface de ligne de commande Azure**. Consultez [Installation et configuration de l’interface de ligne de commande Azure](../xplat-cli-install.md).
  * Téléchargez et installez les **outils d’interface de ligne de commande Azure** [version préliminaire](https://github.com/MicrosoftBigData/AzureDataLake/releases) pour effectuer cette démonstration.
* **Authentication**, en utilisant la commande  suivante :
  
        azure login
    Pour plus d’informations sur l’authentification à l’aide d’un compte professionnel ou scolaire, consultez la section [Se connecter à un abonnement Azure à partir de l’interface de ligne de commande Azure](../xplat-cli-connect.md).
* **Passez en mode Azure Resource Manager**en exécutant la commande suivante :
  
        azure config mode arm

## <a name="create-data-lake-analytics-account"></a>Créer un compte Analytique Data Lake
Vous devez disposer d’un compte Analytique Data Lake avant de pouvoir exécuter un travail quelconque. Pour créer un compte Analytique Data Lake, vous devez spécifier les éléments suivants :

* **Groupe de ressources Azure**: un compte Data Lake Analytics doit être créé au sein d'un groupe de ressources Azure. [Azure Resource Manager](../azure-resource-manager/resource-group-overview.md) vous permet de manipuler les ressources de votre application sous la forme d’un groupe. Vous pouvez déployer, mettre à jour ou supprimer toutes les ressources de votre application dans le cadre d’une opération unique et coordonnée.  
  
    Pour répertorier les groupes de ressources dans votre abonnement :
  
        azure group list 
  
    Pour créer un groupe de ressources :
  
        azure group create -n "<Resource Group Name>" -l "<Azure Location>"
* **Nom du compte Analytique Data Lake**
* **Emplacement**: un des centres de données Azure prenant en charge Data Lake Analytics.
* **Compte Data Lake par défaut**: chaque compte Data Lake Analytics possède un compte Data Lake par défaut.
  
    Pour répertorier le compte Data Lake existant :
  
        azure datalake store account list
  
    Pour créer un compte Azure Data Lake :
  
        azure datalake store account create "<Data Lake Store Account Name>" "<Azure Location>" "<Resource Group Name>"
  
  > [!NOTE]
  > Le nom de compte Data Lake doit contenir uniquement des lettres minuscules et des chiffres.
  > 
  > 

**Pour créer un compte Analytique Data Lake**

        azure datalake analytics account create "<Data Lake Analytics Account Name>" "<Azure Location>" "<Resource Group Name>" "<Default Data Lake Account Name>"

        azure datalake analytics account list
        azure datalake analytics account show "<Data Lake Analytics Account Name>"            

![Compte d’affichage Data Lake Analytics](./media/data-lake-analytics-get-started-cli/data-lake-analytics-show-account-cli.png)

> [!NOTE]
> Le nom de compte Data Lake Analytics doit contenir uniquement des lettres minuscules et des chiffres.
> 
> 

## <a name="upload-data-to-data-lake-store"></a>Téléchargement de données vers Data Lake Store
Dans ce didacticiel, vous allez traiter des journaux de recherche.  Le journal de recherche peut être stocké dans Data Lake Store ou dans le stockage d’objets blobs Azure. 

Le portail Azure fournit une interface utilisateur pour la copie de fichiers de données d’exemple vers le compte Data Lake par défaut, y compris un fichier de journal de recherche. Voir [Préparer des données sources](data-lake-analytics-get-started-portal.md#prepare-source-data) pour charger les données dans le compte Data Lake Store par défaut.

Pour télécharger des fichiers à l'aide de l’interface de ligne de commande, utilisez la commande suivante :

      azure datalake store filesystem import "<Data Lake Store Account Name>" "<Path>" "<Destination>"
      azure datalake store filesystem list "<Data Lake Store Account Name>" "<Path>"

Analytique Data Lake peut également accéder au stockage d’objets blobs Azure.  Pour télécharger des données dans le Blob Storage Azure, consultez [Utilisation de la CLI Microsoft Azure avec Microsoft Azure Storage](../storage/storage-azure-cli.md).

## <a name="submit-data-lake-analytics-jobs"></a>Envoyer des travaux Analytique Data Lake
Les travaux Data Lake Analytics sont écrits en langage U-SQL. Pour en savoir plus sur U-SQL, consultez [Prise en main du langage U-SQL](data-lake-analytics-u-sql-get-started.md) et [Référence du langage U-SQL](http://go.microsoft.com/fwlink/?LinkId=691348).

**Pour créer un script de travail Analytique Data Lake**

* Créez un fichier texte avec le script U-SQL suivant, puis enregistrez le fichier texte sur votre station de travail :
  
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
  
    Ne modifiez pas les deux chemins d’accès, sauf si vous copiez le fichier source dans un autre emplacement.  L’analyse de données Analytique Data Lake créera le dossier de sortie s’il n’existe pas encore.
  
    Il est plus simple d’utiliser des chemins d’accès relatifs pour les fichiers stockés dans les comptes Data Lake par défaut. Vous pouvez également utiliser des chemins d’accès absolus.  Par exemple : 
  
        adl://<Data LakeStorageAccountName>.azuredatalakestore.net:443/Samples/Data/SearchLog.tsv
  
    Vous devez utiliser des chemins d’accès absolus pour accéder aux fichiers dans les comptes de stockage liés.  La syntaxe des fichiers stockés dans le compte de stockage Azure lié est la suivante :
  
        wasb://<BlobContainerName>@<StorageAccountName>.blob.core.windows.net/Samples/Data/SearchLog.tsv
  
  > [!NOTE]
  > Les conteneurs d'objets Blob Azure avec des autorisations d'accès aux objets Blob publics ou aux conteneurs publics ne sont pas pris en charge actuellement.      
  > 
  > 

**Pour soumettre le travail**

    azure datalake analytics job create  "<Data Lake Analytics Account Name>" "<Job Name>" "<Script>"


Les commandes suivantes peuvent servir à répertorier les travaux, obtenir les détails d'un travail ou annuler les travaux :

```
azure datalake analytics job cancel "<Data Lake Analytics Account Name>" "<Job Id>"
azure datalake analytics job list "<Data Lake Analytics Account Name>"
azure datalake analytics job show "<Data Lake Analytics Account Name>" "<Job Id>"
```

Une fois le travail terminé, vous pouvez utiliser les applets de commande suivantes pour répertorier le fichier et télécharger le fichier :

    azure datalake store filesystem list "<Data Lake Store Account Name>" "/Output"
    azure datalake store filesystem export "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" "<Destination>"
    azure datalake store filesystem read "<Data Lake Store Account Name>" "/Output/SearchLog-from-Data-Lake.csv" <Length> <Offset>

## <a name="see-also"></a>Voir aussi
* Pour afficher le même didacticiel en utilisant d’autres outils, cliquez sur les sélecteurs d’onglet en haut de la page.
* Pour voir une requête plus complexe, consultez [Analyse de journaux des sites web à l'aide d'Azure Data Lake Analytics](data-lake-analytics-analyze-weblogs.md).
* Pour commencer à développer des applications U-SQL, consultez [Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md).
* Pour connaître U-SQL, voir [Prise en main du langage U-SQL d’Analytique Data Lake Azure](data-lake-analytics-u-sql-get-started.md).
* Pour les tâches de gestion, consultez [Gestion d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md).
* Pour obtenir une vue d’ensemble de Data Lake Analytics, consultez [Présentation d’Azure Data Lake Analytics](data-lake-analytics-overview.md).




<!--HONumber=Nov16_HO3-->


