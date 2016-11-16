---
title: Prise en main de Data Lake Store avec une interface de ligne de commande multiplateforme | Microsoft Docs
description: "Utilisation de l&quot;interface de ligne de commande multi-plateforme Azure pour créer un compte Data Lake Store et effectuer des opérations de base"
services: data-lake-store
documentationcenter: 
author: nitinme
manager: jhubbard
editor: cgronlun
ms.assetid: 380788f3-041d-4ae5-b6be-37ca74ca333d
ms.service: data-lake-store
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 09/27/2016
ms.author: nitinme
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: b4b2449f00e298385579c4d7b229ceea18dcc598


---
# <a name="get-started-with-azure-data-lake-store-using-azure-command-line"></a>Prise en main d'Azure Data Lake Store avec l'interface de ligne de commande Azure
> [!div class="op_single_selector"]
> * [Portail](data-lake-store-get-started-portal.md)
> * [PowerShell](data-lake-store-get-started-powershell.md)
> * [Kit SDK .NET](data-lake-store-get-started-net-sdk.md)
> * [Kit SDK Java](data-lake-store-get-started-java-sdk.md)
> * [API REST](data-lake-store-get-started-rest-api.md)
> * [Interface de ligne de commande Azure](data-lake-store-get-started-cli.md)
> * [Node.JS](data-lake-store-manage-use-nodejs.md)
> 
> 

Apprenez à utiliser l'interface de ligne de commande Azure pour créer un compte Azure Data Lake Store et effectuer des opérations de base comme la création de dossiers, le téléchargement de fichiers de données, la suppression de votre compte, etc. Pour plus d’informations sur Data Lake Store, consultez [Vue d’ensemble de Data Lake Store](data-lake-store-overview.md).

L’interface de ligne de commande Azure est implémentée dans Node.js. Elle peut être utilisée sur toute plateforme prenant en charge Node.js, y compris Windows, Mac et Linux. L’interface de ligne de commande Azure est open source. Le code source est géré dans GitHub sur <a href= "https://github.com/azure/azure-xplat-cli">https://github.com/azure/azure-xplat-cli</a>. Cet article aborde uniquement l'utilisation de l'interface de ligne de commande Azure dans Data Lake Store. Pour une aide générale sur l’utilisation de l’interface de ligne de commande Azure, consultez la rubrique [Utiliser l’interface de ligne de commande Azure][azure-command-line-tools].

## <a name="prerequisites"></a>Composants requis
Avant de commencer cet article, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la rubrique [Obtenir une version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Azure CLI** - Consultez la rubrique [Installer et configurer l’interface de ligne de commande Azure](../xplat-cli-install.md) pour obtenir des informations sur l’installation et la configuration. N'oubliez pas de redémarrer votre ordinateur une fois l'interface de ligne de commande installée.

## <a name="authentication"></a>Authentification
Pour l’authentification auprès de Data Lake Store, cet article utilise une approche plus simple où vous vous connectez en tant qu’utilisateur final. Le niveau d’accès au compte et au système de fichiers Data Lake Store est alors régi par le niveau d’accès de l’utilisateur connecté. Cependant, il existe d’autres approches pour l’authentification auprès de Data Lake Store, à savoir **l’authentification de l’utilisateur final** ou **l’authentification de service à service**. Pour plus d’informations sur l’authentification et la procédure associée, consultez [Authenticate with Data Lake Store using Azure Active Directory](data-lake-store-authenticate-using-active-directory.md)(Authentification auprès de Data Lake Store à l’aide d’Azure Active Directory).

## <a name="login-to-your-azure-subscription"></a>Connexion à votre abonnement Azure
1. Suivez les étapes décrites dans [Se connecter à un abonnement Azure à partir de l’interface de ligne de commande Azure (Azure CLI)](../xplat-cli-connect.md) et connectez-vous à votre abonnement à l’aide de la méthode `azure login`.
2. Répertoriez les abonnements associés à votre compte à l’aide de la commande `azure account list`.
   
        info:    Executing command account list
        data:    Name              Id                                    Current
        data:    ----------------  ------------------------------------  -------
        data:    Azure-sub-1       ####################################  true
        data:    Azure-sub-2       ####################################  false
   
    Dans la sortie ci-dessus, **Azure-sub-1** est actuellement activé, et l’autre abonnement est **Azure-sub-2**. 
3. Sélectionnez l’abonnement avec lequel vous souhaitez travailler. Pour travailler avec l’abonnement Azure-sub-2, utilisez la commande `azure account set`.
   
        azure account set Azure-sub-2

## <a name="create-an-azure-data-lake-store-account"></a>Créer un compte Azure Data Lake Store
Ouvrez une invite de commande, un shell ou une session de terminal et exécutez les commandes suivantes.

1. Passez en mode Gestionnaire de ressources Azure en exécutant la commande suivante :
   
        azure config mode arm
2. Créez un groupe de ressources. Dans la commande suivante, définissez des valeurs de paramètre que vous souhaitez utiliser.
   
        azure group create <resourceGroup> <location>
   
    Si le nom de l'emplacement contient des espaces, entourez-le de guillemets. Par exemple, « East US 2 ».
3. Créez le compte Data Lake Store.
   
        azure datalake store account create <dataLakeStoreAccountName> <location> <resourceGroup>

## <a name="create-folders-in-your-data-lake-store"></a>Création de dossiers dans votre Data Lake Store
Vous pouvez créer des dossiers dans votre compte Azure Data Lake Store pour gérer et stocker des données. Utilisez la commande suivante pour créer un dossier nommé « mynewfolder » à la racine du Data Lake Store.

    azure datalake store filesystem create <dataLakeStoreAccountName> <path> --folder

Par exemple :

    azure datalake store filesystem create mynewdatalakestore /mynewfolder --folder

## <a name="upload-data-to-your-data-lake-store"></a>Chargement de données dans votre Data Lake Store
Vous pouvez charger vos données dans Data Lake Store directement à la racine ou dans un dossier que vous avez créé dans le compte. Les extraits de code ci-dessous montrent comment charger des exemples de données dans le dossier (**mynewfolder**) que vous avez créé dans la section précédente.

Si vous recherchez des exemples de données à charger, vous pouvez récupérer le dossier **Données Ambulance** dans le [Référentiel Git Azure Data Lake](https://github.com/MicrosoftBigData/usql/tree/master/Examples/Samples/Data/AmbulanceData). Téléchargez le fichier et stockez-le dans un répertoire local sur votre ordinateur, comme C:\sampledata\..

    azure datalake store filesystem import <dataLakeStoreAccountName> "<source path>" "<destination path>"

Par exemple :

    azure datalake store filesystem import mynewdatalakestore "C:\SampleData\AmbulanceData\vehicle1_09142014.csv" "/mynewfolder/vehicle1_09142014.csv"


## <a name="list-files-in-data-lake-store"></a>Affichage de la liste de fichiers dans Data Lake Store
Utilisez la commande suivante pour afficher les fichiers dans un compte Data Lake Store.

    azure datalake store filesystem list <dataLakeStoreAccountName> <path>

Par exemple :

    azure datalake store filesystem list mynewdatalakestore /mynewfolder

Le résultat doit ressembler à ceci :

    info:    Executing command datalake store filesystem list
    data:    accessTime: 1446245025257
    data:    blockSize: 268435456
    data:    group: NotSupportYet
    data:    length: 1589881
    data:    modificationTime: 1446245105763
    data:    owner: NotSupportYet
    data:    pathSuffix: vehicle1_09142014.csv
    data:    permission: 777
    data:    replication: 0
    data:    type: FILE
    data:    ------------------------------------------------------------------------------------
    info:    datalake store filesystem list command OK

## <a name="rename-download-and-delete-data-from-your-data-lake-store"></a>Renommer, télécharger et supprimer des données de votre Data Lake Store
* Utilisez la commande suivante **pour renommer un fichier** :
  
        azure datalake store filesystem move <dataLakeStoreAccountName> <path/old_file_name> <path/new_file_name>
  
    Par exemple :
  
        azure datalake store filesystem move mynewdatalakestore /mynewfolder/vehicle1_09142014.csv /mynewfolder/vehicle1_09142014_copy.csv
* Utilisez la commande suivante **pour télécharger un fichier**. Assurez-vous que le chemin de destination que vous spécifiez existe.
  
        azure datalake store filesystem export <dataLakeStoreAccountName> <source_path> <destination_path>
  
    Par exemple :
  
        azure datalake store filesystem export mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv "C:\mysampledata\vehicle1_09142014_copy.csv"
* Utilisez la commande suivante **pour supprimer un fichier** :
  
        azure datalake store filesystem delete <dataLakeStoreAccountName> <path>
  
    Par exemple :
  
        azure datalake store filesystem delete mynewdatalakestore /mynewfolder/vehicle1_09142014_copy.csv
  
    Lorsque vous y êtes invité, entrez **Y** pour supprimer l'élément.

## <a name="view-the-access-control-list-for-a-folder-in-data-lake-store"></a>Affichage de la liste de contrôle d'accès pour un dossier dans le Data Lake Store
Utilisez les commandes suivantes pour afficher les listes de contrôle d'accès dans un dossier Data Lake Store. Dans la version actuelle, les listes de contrôle d'accès ne peuvent être définies que dans la racine du Data Lake Store. Le paramètre de chemin d'accès ci-dessous sera donc toujours la racine (/).

    azure datalake store permissions show <dataLakeStoreName> <path>

Par exemple :

    azure datalake store permissions show mynewdatalakestore /


## <a name="delete-your-data-lake-store-account"></a>Suppression de votre compte Data Lake Store
Utilisez la commande suivante pour supprimer un compte Data Lake Store.

    azure datalake store account delete <dataLakeStoreAccountName>

Par exemple :

    azure datalake store account delete mynewdatalakestore

Quand vous y êtes invité, entrez **Y** pour supprimer le compte.

## <a name="next-steps"></a>Étapes suivantes
* [Sécuriser les données dans Data Lake Store](data-lake-store-secure-data.md)
* [Utiliser Azure Data Lake Analytics avec Data Lake Store](../data-lake-analytics/data-lake-analytics-get-started-portal.md)
* [Utiliser Azure HDInsight avec Data Lake Store](data-lake-store-hdinsight-hadoop-use-portal.md)

[azure-command-line-tools]: ../xplat-cli-install.md



<!--HONumber=Nov16_HO2-->


