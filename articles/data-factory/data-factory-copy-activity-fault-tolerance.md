---
title: "Tolérance de panne de l’activité de copie dans Azure Data Factory - Ignorer les lignes incompatibles | Microsoft Docs"
description: "Découvrir la tolérance de panne consistant à ignorer les lignes incompatibles en cours de copie à l’aide d’Azure Data Factory"
services: data-factory
documentationcenter: 
author: linda33wj
manager: jhubbard
editor: monicar
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/19/2017
ms.author: jingwang
ms.translationtype: HT
ms.sourcegitcommit: 0425da20f3f0abcfa3ed5c04cec32184210546bb
ms.openlocfilehash: d613537657af3bbe379a53e92532bf6b184d762b
ms.contentlocale: fr-fr
ms.lasthandoff: 07/20/2017

---
# <a name="copy-activity-fault-tolerance---skip-incompatible-rows"></a>Tolérance de panne de l’activité de copie - Ignorer les lignes incompatibles

Avec l’[activité de copie](data-factory-data-movement-activities.md), vous disposez de différentes options pour traiter les lignes incompatibles lors de la copie de données entre les magasins de données source et récepteur. Vous pouvez choisir d’abandonner et de laisser en échec l’activité de copie en cas de rencontre de données incompatibles (comportement par défaut), ou de continuer à copier toutes les données en ignorant les lignes incompatibles. Vous avez également la possibilité de journaliser les lignes incompatibles dans Stockage Blob Azure afin de pouvoir examiner la cause de l’échec, corriger les données sur la source de données, puis réessayer.

## <a name="supported-scenarios"></a>Scénarios pris en charge
Actuellement, l’activité de copie offre la possibilité de détecter, d’ignorer et de journaliser les situations d’incompatibilité suivantes pendant la copie :

- **Incompatibilité de type de données entre les types natifs source et récepteur**

    Exemple : copie à partir d’un fichier CSV dans Stockage Blob Azure vers Azure SQL Database, alors que le schéma défini dans Azure SQL Database présente trois colonnes de type *INT*. Les lignes comportant des données numériques (par exemple `123,456,789`) dans le fichier CSV source sont copiées avec succès, tandis que les lignes contenant des valeurs non numériques (par exemple `123,456,abc`) sont ignorées en tant que lignes incompatibles.

- **Incompatibilité de nombre de colonnes entre la source et le récepteur**

    Exemple : copie à partir d’un fichier CSV dans Stockage Blob Azure vers Azure SQL Database, alors que le schéma défini dans Azure SQL Database compte six colonnes. Les lignes contenant six colonnes dans le fichier CSV source sont copiées avec succès, tandis que les lignes contenant un autre nombre de colonnes sont ignorées en tant que lignes incompatibles.

- **Violation de clé primaire lors de l’écriture dans une base de données relationnelle**

    Exemple : pour copier à partir de SQL Server vers Azure SQL Database, il existe une clé primaire définie dans la base de données Azure SQL Database réceptrice, mais aucune clé primaire correspondante n’est définie dans le serveur SQL Server source. Les lignes en double qui peuvent exister dans la source ne sont pas autorisées lors de l’écriture dans le récepteur. L’activité de copie ne copie que la première ligne vers le récepteur, ignorant toutes les autres lignes contenant une valeur de clé primaire figurant dans la source et redondantes sur le récepteur.

## <a name="configuration"></a>Configuration
L’exemple suivant fournit la définition JSON spécifiant comment configurer la manière d’ignorer les lignes incompatibles dans le cadre de l’activité de copie :

```json
"typeProperties": {
    "source": {
        "type": "BlobSource"
    },
    "sink": {
        "type": "SqlSink",
    },         
    "enableSkipIncompatibleRow": true,           
    "redirectIncompatibleRowSettings": {
        "linkedServiceName": "BlobStorage",
        "path": "redirectcontainer/erroroutput"
    }
}
```

| Propriété | Description | Valeurs autorisées | Requis |
| --- | --- | --- | --- |
| enableSkipIncompatibleRow | Activer ou non l’option d’ignorer les lignes incompatibles. | true<br/>False (valeur par défaut) | Non |
| redirectIncompatibleRowSettings | Groupe de propriétés qui peuvent être spécifiées lorsque vous souhaitez journaliser les lignes incompatibles. | &nbsp; | Non |
| linkedServiceName | Service lié de Stockage Azure pour stocker le journal contenant toutes les lignes ignorées. | Spécifiez le nom d’un service lié [AzureStorage](data-factory-azure-blob-connector.md#azure-storage-linked-service) ou [AzureStorageSas](data-factory-azure-blob-connector.md#azure-storage-sas-linked-service) faisant référence à l’instance de Stockage Azure que vous utilisez pour stocker le fichier journal. | Non |
| path | Chemin d’accès du fichier journal contenant toutes les lignes ignorées. | Spécifiez le chemin d’accès sur Stockage Blob souhaité pour journaliser les données incompatibles. Si vous ne spécifiez pas le chemin d’accès, le service crée un conteneur à votre place. | Non |

## <a name="monitoring"></a>Surveillance
Une fois l’exécution de l’activité de copie terminée, vous pouvez voir le nombre de lignes ignorées dans la section Surveillance comme suit :

![Ignorer la surveillance des lignes incompatibles](./media/data-factory-copy-activity-fault-tolerance/skip-incompatible-rows-monitoring.png)

Si vous configurez la journalisation des lignes incompatibles afin de pouvoir déterminer celles qui ont été ignorées et la cause racine de l’incompatibilité, le chemin d’accès du fichier journal est le suivant : `https://[your-blob-account].blob.core.windows.net/[path-if-configured]/[copy-activity-run-id]/[auto-generated-GUID].csv`.

Les données d’origine et l’erreur correspondante sont consignées dans le fichier journal. Voici un exemple de contenu de fichier journal :
```
data1, data2, data3, UserErrorInvalidDataValue,Column 'Prop_2' contains an invalid value 'data3'. Cannot convert 'data3' to type 'DateTime'.,
data4, data5, data6, Violation of PRIMARY KEY constraint 'PK_tblintstrdatetimewithpk'. Cannot insert duplicate key in object 'dbo.tblintstrdatetimewithpk'. The duplicate key value is (data4).,
```

## <a name="next-steps"></a>Étapes suivantes
Pour en savoir plus sur l’activité de copie dans Azure Data Factory, voir [Déplacer des données à l’aide de l’activité de copie](data-factory-data-movement-activities.md).
