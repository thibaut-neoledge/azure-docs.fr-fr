---
title: "Prise en main d’Azure Data Lake Store à l’aide de PowerShell | Microsoft Docs"
description: "Utiliser Azure PowerShell pour créer un compte Data Lake Store et effectuer des opérations de base"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: bf85f369-f9aa-4ca1-9ae7-e03a78eb7290
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 03/06/2017
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 988e7fe2ae9f837b661b0c11cf30a90644085e16
ms.openlocfilehash: c00aa4a2e79522a6817a135965f6c218b08e26f8
ms.lasthandoff: 04/06/2017


---
# <a name="get-started-with-azure-data-lake-store-using-azure-powershell"></a>Prise en main d'Azure Data Lake Store avec Azure PowerShell
> [!div class="op_single_selector"]
> * [Portail](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Kit SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [Kit SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Interface de ligne de commande Azure](data-lake-store-get-started-cli.md)
> * [Azure CLI 2.0](data-lake-store-get-started-cli-2.0.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> * [Python](data-lake-store-get-started-python.md)
>
>

Apprenez à utiliser Azure PowerShell pour créer un compte Azure Data Lake Store et effectuer des opérations de base comme créer des dossiers, télécharger des fichiers de données, supprimer votre compte, etc. Pour plus d’informations sur Data Lake Store, consultez [Vue d’ensemble de Data Lake Store](data-lake-store-overview.md).

## <a name="prerequisites"></a>Composants requis
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure PowerShell 1.0 ou version ultérieure**. Consultez [Installation et configuration d’Azure PowerShell](/powershell/azureps-cmdlets-docs).

## <a name="authentication"></a>Authentification
Pour l’authentification auprès de Data Lake Store, cet article utilise une approche plus simple où vous êtes invité à entrer les informations d’identification de votre compte Azure. Le niveau d’accès au compte et au système de fichiers Data Lake Store est alors régi par le niveau d’accès de l’utilisateur connecté. Cependant, il existe d’autres approches pour l’authentification sur Data Lake Store, à savoir **l’authentification de l’utilisateur final** ou **l’authentification de service à service**. Pour plus d’informations sur l’authentification et la procédure associée, consultez [Authenticate with Data Lake Store using Azure Active Directory](data-lake-store-authenticate-using-active-directory.md)(Authentification auprès de Data Lake Store à l’aide d’Azure Active Directory).

## <a name="create-an-azure-data-lake-store-account"></a>Créer un compte Azure Data Lake Store
1. Sur votre Bureau, ouvrez une nouvelle fenêtre Windows PowerShell et entrez l’extrait de code suivant pour vous connecter à votre compte Azure, définir l’abonnement et inscrire le fournisseur Data Lake Store. Lorsque vous êtes invité à vous connecter, vérifiez que vous vous connectez en tant qu’administrateur/propriétaire de l’abonnement :

        # Log in to your Azure account
        Login-AzureRmAccount

        # List all the subscriptions associated to your account
        Get-AzureRmSubscription

        # Select a subscription
        Set-AzureRmContext -SubscriptionId <subscription ID>

        # Register for Azure Data Lake Store
        Register-AzureRmResourceProvider -ProviderNamespace "Microsoft.DataLakeStore"
2. Un compte Azure Data Lake Store est associé à un groupe de ressources Azure. Commencez par créer un groupe de ressources Azure.

        $resourceGroupName = "<your new resource group name>"
        New-AzureRmResourceGroup -Name $resourceGroupName -Location "East US 2"

    ![Créer un groupe de ressources Azure](./media/data-lake-store-get-started-powershell/ADL.PS.CreateResourceGroup.png "Créer un groupe de ressources Azure")
3. Créer un compte Azure Data Lake Store. Le nom que vous spécifiez doit contenir uniquement des lettres minuscules et des chiffres.

        $dataLakeStoreName = "<your new Data Lake Store name>"
        New-AzureRmDataLakeStoreAccount -ResourceGroupName $resourceGroupName -Name $dataLakeStoreName -Location "East US 2"

    ![Créer un compte Azure Data Lake Store](./media/data-lake-store-get-started-powershell/ADL.PS.CreateADLAcc.png "Créer un compte Azure Data Lake Store")
4. Vérifiez que le compte a bien été créé.

        Test-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

    Le résultat doit être **True**.

## <a name="create-directory-structures-in-your-azure-data-lake-store"></a>Créer des structures de répertoires dans votre Azure Data Lake Store
Vous pouvez créer des répertoires sous votre compte Azure Data Lake Store pour gérer et stocker des données.

1. Spécifiez un répertoire racine.

        $myrootdir = "/"
2. Créez un répertoire appelé **mynewdirectory** sous la racine spécifiée.

        New-AzureRmDataLakeStoreItem -Folder -AccountName $dataLakeStoreName -Path $myrootdir/mynewdirectory
3. Vérifiez que le répertoire a bien été créé.

        Get-AzureRmDataLakeStoreChildItem -AccountName $dataLakeStoreName -Path $myrootdir

    Un résultat similaire à ce qui suit s'affiche :

    ![Vérifier le répertoire](./media/data-lake-store-get-started-powershell/ADL.PS.Verify.Dir.Creation.png "Vérifier le répertoire")

## <a name="upload-data-to-your-azure-data-lake-store"></a>Télécharger des données sur votre Azure Data Lake Store
Vous pouvez télécharger vos données sur Data Lake Store directement à la racine ou dans un répertoire que vous avez créé dans le compte. Les extraits de code ci-dessous montrent comment télécharger des exemples de données dans le répertoire (**mynewdirectory**) que vous avez créé dans la section précédente.

Si vous recherchez des exemples de données à charger, vous pouvez récupérer le dossier **Données Ambulance** dans le [Référentiel Git Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Téléchargez le fichier et stockez-le dans un répertoire local sur votre ordinateur, comme C:\sampledata\.

    Import-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path "C:\sampledata\vehicle1_09142014.csv" -Destination $myrootdir\mynewdirectory\vehicle1_09142014.csv


## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Renommer, télécharger et supprimer des données de votre Data Lake Store
Utilisez la commande suivante pour renommer un fichier :

    Move-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014.csv -Destination $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Utilisez la commande suivante pour télécharger un fichier :

    Export-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Path $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv -Destination "C:\sampledata\vehicle1_09142014_Copy.csv"

Utilisez la commande suivante pour supprimer un fichier :

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014_Copy.csv

Quand vous y êtes invité, entrez **Y** pour supprimer l’élément. Si vous avez plusieurs fichiers à supprimer, vous pouvez fournir tous les chemins d'accès séparés par des virgules.

    Remove-AzureRmDataLakeStoreItem -AccountName $dataLakeStoreName -Paths $myrootdir\mynewdirectory\vehicle1_09142014.csv, $myrootdir\mynewdirectoryvehicle1_09142014_Copy.csv

## <a name="delete-your-azure-data-lake-store-account"></a>Supprimer votre compte Azure Data Lake Store
Utilisez la commande suivante pour supprimer votre compte Data Lake Store.

    Remove-AzureRmDataLakeStoreAccount -Name $dataLakeStoreName

Quand vous y êtes invité, entrez **Y** pour supprimer le compte.

## <a name="performance-guidance-while-using-powershell"></a>Guide des performances lors de l’utilisation de PowerShell

Voici les principaux paramètres que vous pouvez définir pour obtenir des performances optimales lors de l’utilisation de PowerShell avec Data Lake Store :

| Propriété            | Default | Description |
|---------------------|---------|-------------|
| PerFileThreadCount  | 10      | Ce paramètre vous permet de choisir le nombre de threads parallèles pour charger ou télécharger chaque fichier. Ce nombre représente le nombre maximal de threads pouvant être alloués par fichier, mais il se peut que vous obteniez moins de threads en fonction du scénario (par exemple, si vous chargez un fichier de 1 Ko, vous obtenez un seul thread même si vous demandez 20 threads).  |
| ConcurrentFileCount | 10      | Ce paramètre est spécifique au chargement ou au téléchargement des dossiers. Il détermine le nombre de fichiers simultanés pouvant être chargés ou téléchargés. Ce nombre représente le nombre maximal de fichiers simultanés pouvant être chargés ou téléchargés en même temps, mais il se peut que vous en obteniez moins en fonction du scénario (par exemple, si vous chargez deux fichiers, vous obtenez deux chargements de fichiers simultanés même si vous en demandez 15). |

**Exemple**

Cette commande télécharge les fichiers à partir d’Azure Data Lake Store sur le disque local de l’utilisateur à l’aide de 20 threads par fichier et 100 fichiers simultanés.

    Export-AzureRmDataLakeStoreItem -AccountName <Data Lake Store account name> -PerFileThreadCount 20-ConcurrentFileCount 100 -Path /Powershell/100GB/ -Destination C:\Performance\ -Force -Recurse

### <a name="how-do-i-determine-the-value-to-set-for-these-parameters"></a>Comment déterminer la valeur à définir pour ces paramètres ?

Voici quelques conseils à suivre.

* **Étape 1 : Déterminer le nombre total de threads** - Vous devez commencer par calculer le nombre total de threads à utiliser. En règle générale, vous devez utiliser 6 threads pour chaque noyau physique.

        Total thread count = total physical cores * 6

    **Exemple**

    Supposons que vous exécutez les commandes PowerShell à partir d’une machine virtuelle D14 avec 16 noyaux

        Total thread count = 16 cores * 6 = 96 threads


* **Étape 2 : Calculer PerFileThreadCount** - Nous calculons PerFileThreadCount en fonction de la taille des fichiers. Pour les fichiers inférieurs à 2,5 Go, il est inutile de modifier ce paramètre, car la valeur par défaut, 10, est suffisante. Pour les fichiers supérieurs à 2,5 Go, vous devez utiliser 10 threads comme base pour les premiers 2,5 Go et ajouter 1 thread pour chaque augmentation de 256 Mo de la taille du fichier. Si vous copiez un dossier contenant différentes tailles de fichiers, envisagez de regrouper ces fichiers par tailles similaires. Les différentes tailles de fichiers ne permettent pas d’obtenir des performances optimales. S’il n’est pas possible de regrouper les tailles de fichiers similaires, vous devez définir PerFileThreadCount en fonction de la plus grande taille de fichier.

        PerFileThreadCount = 10 threads for the first 2.5GB + 1 thread for each additional 256MB increase in file size

    **Exemple**

    Supposons que vous avez 100 fichiers de 1 à 10 Go. Pour l’équation, nous utilisons la taille de fichier la plus grande, 10 Go, qui se lit comme suit.

        PerFileThreadCount = 10 + ((10GB - 2.5GB) / 256MB) = 40 threads

* **Étape 3 : Calculer ConcurrentFilecount** - Utilisez le nombre total de threads et PerFileThreadCount pour calculer ConcurrentFileCount sur la base de l’équation suivante.

        Total thread count = PerFileThreadCount * ConcurrentFileCount

    **Exemple**

    Basé sur les exemples de valeurs utilisés

        96 = 40 * ConcurrentFileCount

    Ainsi, **ConcurrentFileCount** vaut **2.4**, que nous pouvons arrondir à **2**.

### <a name="further-tuning"></a>Paramétrage supplémentaire

Il se peut qu’un paramétrage supplémentaire soit requis en raison des différentes tailles de fichiers à utiliser. Le calcul ci-dessus fonctionne bien si l’ensemble ou la plupart des fichiers sont parmi les plus grands et sont plus proches de la plage de 10 Go. Par contre, s’il y a beaucoup de tailles de fichiers différentes et que la plupart des fichiers sont parmi les plus petits, vous pouvez réduire PerFileThreadCount. En réduisant PerFileThreadCount, nous pouvons augmenter ConcurrentFileCount. Par conséquent, si nous partons du principe que la plupart des fichiers sont plus petits (dans la plage de 5 Go), nous pouvons refaire nos calculs :

    PerFileThreadCount = 10 + ((5GB - 2.5GB) / 256MB) = 20

Ainsi, **ConcurrentFileCount** est maintenant égal à 96/20, soit 4,8, arrondi à **4**.

Vous pouvez continuer à ajuster ces paramètres en augmentant et en diminuant le nombre **PerFileThreadCount** en fonction de la répartition des tailles de fichiers.

### <a name="limitation"></a>Limitation

* **Le nombre de fichiers est inférieur à ConcurrentFileCount** : si le nombre de fichiers chargés est inférieur à la valeur **ConcurrentFileCount** calculée, vous devez réduire la valeur **ConcurrentFileCount** pour qu’elle soit égale au nombre de fichiers. Vous pouvez utiliser les threads restants pour augmenter **PerFileThreadCount**.

* **Trop de threads** : si vous augmentez trop le nombre de threads sans augmenter la taille du cluster, vous courez le risque d’une diminution des performances. Il peut y avoir des problèmes de conflit lors du changement de contexte sur le processeur.

* **Accès concurrentiels insuffisants** : si les accès concurrentiels sont insuffisants, le cluster risque d’être trop petit. Vous pouvez augmenter le nombre de nœuds dans votre cluster pour autoriser plus d’accès simultanés.

* **Erreurs de limitation** : il se peut que vous rencontriez des erreurs de limitation si le nombre d’accès concurrentiels est trop élevé. En cas d’erreurs de limitation, vous devez réduire le nombre d’accès simultanés ou nous contacter.

## <a name="next-steps"></a>Étapes suivantes
* [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)
* [Utiliser Azure Data Lake Analytics avec Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utiliser Azure HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)


