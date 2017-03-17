---
title: Intelligence et analyse Azure Governement | Microsoft Docs
description: This provides a comparision of features and guidance on developing applications for Azure Government
services: azure-government
cloud: gov
documentationcenter: 
author: MeganYount
manager: zakramer
ms.assetid: 4b7720c1-699e-432b-9246-6e49fb77f497
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 12/06/2016
ms.author: MeganYount
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 0233aa66bc4f4f135456ec15bd09756e63192b14
ms.lasthandoff: 03/06/2017


---
# <a name="azure-government-intelligence--analytics"></a>Intelligence et analyse Azure Governement
Cet article décrit les services d’intelligence et d’analyse, les variations et les éléments à prendre en compte pour l’environnement Azure Government.

## <a name="hdinsight"></a>HDInsight
HDInsight sur Linux Standard est mis à la disposition générale dans Azure Government. Vous pouvez voir une démonstration sur la création de solutions orientées sur les données sur Azure Government à l’aide de HDInsight <a href=https://channel9.msdn.com/Blogs/Azure/Cognitive-Services-HDInsight-and-Power-BI-on-Azure-Government/>ici</a>.

HDInsight sur Linux Premium sera prochainement disponible.

### <a name="variations"></a>Variantes
Actuellement, les fonctionnalités HDInsight suivantes ne sont pas disponibles dans Azure Government.

* HDInsight n’est pas disponible sur Windows.
* Azure Data Lake Store n’est actuellement pas disponible dans Azure Government. Le Stockage Blob Azure est la seule option de stockage disponible pour l’instant.

Les URL de Log Analytics sont différentes dans Azure Government :

| Type de service | Azure (public) | Azure Government |
| --- | --- | --- |
| Cluster HDInsight | \*.azurehdinsight.net | \*.azurehdinsight.us |

Pour plus d’informations, consultez la [documentation publique sur HDInsight](../hdinsight/hdinsight-hadoop-introduction.md).

## <a name="power-bi"></a>Power BI
Power BI US Government est disponible dans le cadre des abonnements Office 365 Government Community. Vous pouvez en apprendre davantage sur Power BI US Government <a href=https://powerbi.microsoft.com/en-us/documentation/powerbi-service-govus-overview/>ici</a>. Vous pouvez voir une démonstration sur la création de solutions orientées sur les données sur Azure Government à l’aide de Power BI <a href=https://channel9.msdn.com/Blogs/Azure/Cognitive-Services-HDInsight-and-Power-BI-on-Azure-Government/>ici</a>.

Power BI Embedded sera disponible prochainement.

### <a name="variations"></a>Variantes

Les URL pour Power BI sont différentes dans US Government :

| Type de service | Power BI Commercial | Power BI US Government |
| --- | --- | --- |
| Power BI URL | app.powerbi.com | app.powerbigov.us |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations supplémentaires et des mises à jour, inscrivez-vous au <a href="https://blogs.msdn.microsoft.com/azuregov/">blog Microsoft Azure Government. </a>

