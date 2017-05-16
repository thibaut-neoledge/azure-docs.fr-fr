---
title: "Prise en main d’Azure Data Lake Analytics à l’aide d’Azure PowerShell | Microsoft Docs"
description: "Utilisez Azure PowerShell pour créer un compte Data Lake Analytics, créer un travail Data Lake Analytics avec U-SQL et envoyer le travail. "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: 8a4e901e-9656-4a60-90d0-d78ff2f00656
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 05/04/2017
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: 18d4994f303a11e9ce2d07bc1124aaedf570fc82
ms.openlocfilehash: 6985dff332928d704f30e167c3bddb62bcc6cac1
ms.contentlocale: fr-fr
ms.lasthandoff: 05/09/2017


---
# <a name="tutorial-get-started-with-azure-data-lake-analytics-using-azure-powershell"></a>Prise en main du service Analytique Data Lake Azure à l’aide d’Azure PowerShell
[!INCLUDE [get-started-selector](../../includes/data-lake-analytics-selector-get-started.md)]

Découvrez comment utiliser Azure PowerShell pour créer des comptes Azure Data Lake Analytics, puis envoyer et exécuter des travaux U-SQL. Pour plus d’informations sur Analytique Data Lake, consultez [Présentation d’Analytique Data Lake Azure](data-lake-analytics-overview.md).

## <a name="prerequisites"></a>Composants requis
Avant de commencer ce didacticiel, vous devez disposer des éléments suivants :

* **Un abonnement Azure**. Consultez la page [Obtention d’un essai gratuit d’Azure](https://azure.microsoft.com/pricing/free-trial/).
* **Un poste de travail sur lequel est installé Azure PowerShell**. Consultez [Installation et configuration d’Azure PowerShell](/powershell/azure/overview).

## <a name="preparing-for-the-tutorial"></a>Préparation pour le didacticiel
Pour créer un compte Data Lake Analytics, vous devez définir les éléments suivants :

* **Groupe de ressources Azure** : un compte Data Lake Analytics doit être créé au sein d’un groupe de ressources Azure.
* **Nom du compte Data Lake Analytics** : le nom du compte Data Lake Analytics ne doit contenir que des lettres minuscules et des chiffres.
* **Emplacement**: un des centres de données Azure prenant en charge Data Lake Analytics.
* **Compte Data Lake Store par défaut** : chaque compte Data Lake Analytics possède un compte Data Lake Store par défaut. Ces comptes doivent résider au même emplacement.

Dans ce didacticiel, les extraits de code PowerShell utilisent ces variables pour stocker cette information.

```
$rg = "<ResourceGroupName>"
$adls = "<DataLakeAccountName>"
$adla = "<DataLakeAnalyticsAccountName>"
$location = "East US 2"
```

## <a name="create-a-data-lake-analytics-account"></a>Créer un compte Data Lake Analytics

Si vous ne disposez pas d’un groupe de ressources, créez-en un. 

```
New-AzureRmResourceGroup -Name  $rg -Location $location
```

Chaque compte Data Lake Analytics nécessite un compte Data Lake Store par défaut pour stocker les journaux. Vous pouvez réutiliser un compte ou en créer un. 

```
New-AdlStore -ResourceGroupName $rg -Name $adls -Location $location
```

Lorsqu’un groupe de ressources et un compte Data Lake Store sont disponibles, créez un compte Data Lake Analytics.

```
New-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla -Location $location -DefaultDataLake $adls
```

## <a name="get-information-about-a-data-lake-analytics-account"></a>Obtenir des informations sur un compte Data Lake Analytics

```
Get-AdlAnalyticsAccount -ResourceGroupName $rg -Name $adla  
```

## <a name="submit-a-u-sql-job"></a>Envoyer un travail U-SQL

Créez un fichier texte contenant le script U-SQL suivant.

```
@a  = 
    SELECT * FROM 
        (VALUES
            ("Contoso", 1500.0),
            ("Woodgrove", 2700.0)
        ) AS 
              D( customer, amount );
OUTPUT @a
    TO "/data.csv"
    USING Outputters.Csv();
```

Envoyez le script.

```
Submit-AdlJob -AccountName $adla –ScriptPath "d:\test.usql"Submit
```

## <a name="monitor-u-sql-jobs"></a>Surveiller les travaux U-SQL

Affichez la liste de tous les travaux dans le compte. La sortie comprend les travaux en cours et ceux qui ont été terminés récemment.

```
Get-AdlJob -Account $adla
```

Affichez l’état d’un travail.

```
Get-AdlJob -AccountName $adla -JobId $job.JobId
```

Au lieu d’appeler Get-AdlAnalyticsJob maintes et maintes fois jusqu’à ce qu’un travail aboutisse, vous pouvez utiliser l’applet de commande Wait-AdlJob.

```
Wait-AdlJob -Account $adla -JobId $job.JobId
```

Dès qu’un travail est terminé, vérifiez si le fichier de sortie existe en affichant les fichiers dans un dossier.

```
Get-AdlStoreChildItem -Account $adls -Path "/"
```

Vérifiez l’existence d’un fichier.

```
Test-AdlStoreItem -Account $adls -Path "/data.csv"
```

## <a name="uploading-and-downloading-files"></a>Chargement et téléchargement de fichiers

Téléchargez la sortie du script U-SQL.

```
Export-AdlStoreItem -AccountName $adls -Path "/data.csv"  -Destination "D:\data.csv"
```


Chargez un fichier à utiliser comme entrée pour un script U-SQL.

```
Import-AdlStoreItem -AccountName $adls -Path "D:\data.tsv" -Destination "/data_copy.csv" 
```

## <a name="see-also"></a>Voir aussi
* Pour afficher le même didacticiel en utilisant d’autres outils, cliquez sur les sélecteurs d’onglet en haut de la page.
* Pour connaître U-SQL, voir [Prise en main du langage U-SQL d’Analytique Data Lake Azure](data-lake-analytics-u-sql-get-started.md).
* Pour les tâches de gestion, consultez [Gestion d’Azure Data Lake Analytics à l’aide du portail Azure](data-lake-analytics-manage-use-portal.md).

