---
title: "Mettre à l’échelle des travaux Stream Analytics pour augmenter le débit | Microsoft Docs"
description: "Découvrez comment mettre à l’échelle des travaux Stream Analytics en configurant des partitions d’entrée, en réglant la définition de requête et en configurant les unités de diffusion en continu d’un travail."
keywords: "diffusion en continu de données, traitement de données de diffusion en continu, régler l’analyse"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 7e857ddb-71dd-4537-b7ab-4524335d7b35
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/22/2017
ms.author: jeffstok
ms.translationtype: Human Translation
ms.sourcegitcommit: 6dbb88577733d5ec0dc17acf7243b2ba7b829b38
ms.openlocfilehash: 9a2b16fc6dff687e2a1fa03c9194d50711f53476
ms.contentlocale: fr-fr
ms.lasthandoff: 07/04/2017


---
# <a name="scale-azure-stream-analytics-jobs-to-increase-stream-data-processing-throughput"></a>Mettre à l’échelle des tâches Azure Stream Analytics pour augmenter le débit de traitement des données de flux
Cet article vous indique comment régler une requête Stream Analytics pour augmenter le débit des travaux Stream Analytics. Vous allez découvrir comment mettre à l’échelle des travaux Stream Analytics en configurant des partitions d’entrée, en réglant la définition de la requête d’analyse et en calculant et en configurant les *unités de diffusion en continu* d’un travail. 

## <a name="what-are-the-parts-of-a-stream-analytics-job"></a>Quelles sont les parties d’un travail Stream Analytics ?
La définition d’une tâche Stream Analytics se compose d’entrées, d’une requête et d’une sortie. Les entrées correspondent à l’emplacement où le travail lit le flux de données. La requête permet de transformer le flux d’entrée de données, et la sortie correspond à l’emplacement où le travail envoie ses résultats.  

Un travail nécessite au moins une source d’entrée pour la diffusion de données en continu. La source d’entrée de flux de données peut être stockée dans un concentrateur Azure Event Hub ou dans un stockage d’objets blob Azure. Pour plus d’informations, consultez [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md) et [Prise en main de l’utilisation d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md).

## <a name="partitions-in-event-hubs-and-azure-storage"></a>Partitions dans les concentrateurs d’événements et le stockage Azure
La mise à l’échelle d’un travail Stream Analytics tire parti des partitions dans l’entrée ou la sortie. Le partitionnement vous permet de répartir les données en sous-ensembles basés sur une clé de partition. Un processus qui consomme les données (par exemple, un travail Stream Analytics) peut consommer et écrire différentes partitions en parallèle, ce qui augmente le débit. Lorsque vous utilisez Stream Analytics, vous pouvez tirer parti du partitionnement dans les concentrateurs d’événements et dans le stockage d’objets blob. 

Pour plus d’informations sur les partitions, consultez les articles suivants :

* [Vue d’ensemble des fonctionnalités des concentrateurs d’événements](../event-hubs/event-hubs-features.md#partitions)
* [Partitionnement des données](https://docs.microsoft.com/azure/architecture/best-practices/data-partitioning#partitioning-azure-blob-storage)


## <a name="streaming-units-sus"></a>Unités de diffusion en continu (SU)
Les unités de diffusion en continu représentent les ressources et la puissance de calcul requises pour exécuter un travail Azure Stream Analytics. Ces unités permettent de décrire la capacité relative de traitement des événements basée sur une mesure mixte du processeur, de la mémoire et des taux de lecture et d’écriture. Chaque unité SU correspond à un débit d’environ 1 Mo/s. 

Le choix du nombre d’unités SU requises pour un travail particulier dépend de la configuration de la partition pour les entrées et de la requête définie pour le travail. Vous pouvez sélectionner votre quota en unités SU pour un travail. Par défaut, chaque abonnement Azure dispose d’un quota de 50 unités SU pour tous les travaux d’analyse d’une région spécifique. Pour augmenter ce quota d’unités SU pour vos abonnements, contactez le [Support Microsoft](http://support.microsoft.com). Valeurs valides pour les unités SU par travail : 1, 3, 6 et au-dessus par incréments de 6.

## <a name="embarrassingly-parallel-jobs"></a>Travaux massivement parallèles
Un travail *massivement parallèle* est le scénario le plus évolutif d’Azure Stream Analytics. Elle permet de connecter une partition de l’entrée à une instance de la requête, puis de connecter celle-ci à une partition de la sortie. Ce parallélisme comporte les exigences suivantes :

1. Si votre logique de requête dépend de la clé qui est actuellement traitée par la même instance de requête, vous devez vous assurer que les événements atteignent la même partition de votre entrée. Pour les concentrateurs d’événements, cela signifie que les données d’événement doivent posséder la valeur **PartitionKey** définie. Par ailleurs, vous pouvez utiliser des expéditeurs partitionnés. Pour le stockage d’objets blob, cela signifie que les événements sont envoyés vers le même dossier de partition. Si votre logique de requête ne requiert pas la même clé pour être traitée par la même instance de requête, vous pouvez ignorer cette condition. Un exemple de cette logique serait une requête simple du type select/project/filter.  

2. Une fois les données disposées dans l’entrée, vous devez vérifier que votre requête est partitionnée. Vous devez utiliser **Partition By** à toutes les étapes. Les étapes multiples sont autorisées, mais elles doivent être partitionnées à l’aide de la même clé. Pour le moment, la clé de partitionnement doit être définie sur **PartitionId** afin que le travail soit entièrement parallèle.  

3. Actuellement, seuls les concentrateurs d’événements et le stockage d’objets blob prennent en charge les sorties partitionnées. Pour la sortie de concentrateur Event Hub, vous devez configurer la clé de partition et la définir sur **PartitionId**. Pour la sortie de stockage d’objets blob, aucune action n’est nécessaire.  

4. Le nombre de partitions d’entrée doit être égal à celui des partitions de sortie. Actuellement, la sortie du stockage d’objets blob ne prend pas en charge les partitions, mais cela ne pose pas de problèmes, car elle hérite du schéma de partitionnement de la requête en amont. Voici des exemples de valeurs de partition qui permettent la création d’un travail entièrement parallèle :  

   * 8 partitions d’entrée de concentrateur Event Hub et 8 partitions de sortie de concentrateur Event Hub
   * 8 partitions d’entrée de concentrateur Event Hub et une sortie de stockage d’objets blob  
   * 8 partitions d’entrée de stockage d’objets blob et une sortie de stockage d’objets blob  
   * 8 partitions d’entrée de stockage d’objets blob et 8 partitions de sortie de concentrateur Event Hub  

Les sections ci-après présentent quelques exemples de parallélisme massif.

### <a name="simple-query"></a>Requête simple

* Entrée : concentrateur Event Hub avec 8 partitions
* Sortie : concentrateur Event Hub avec 8 partitions

Requête :

    SELECT TollBoothId
    FROM Input1 Partition By PartitionId
    WHERE TollBoothId > 100

Cette requête est un filtre simple. Par conséquent, nous n’avons pas à nous préoccuper du partitionnement de l’entrée qui est envoyée au concentrateur Event Hub. Notez que la requête inclut **Partition By PartitionId**. Elle répond donc à l’exigence n°2 indiquée précédemment. Pour la sortie, nous devons configurer la sortie de concentrateur Event Hub dans le travail afin que la clé de partition soit définie sur **PartitionId**. La dernière vérification consiste à s’assurer que le nombre de partitions d’entrée est égal au nombre de partitions de sortie.

### <a name="query-with-a-grouping-key"></a>Requête avec clé de regroupement

* Entrée : concentrateur Event Hub avec 8 partitions
* Sortie : stockage d’objets blob

Requête :

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Cette requête comporte une clé de regroupement. Par conséquent, la même clé doit être traitée par la même instance de la requête, ce qui signifie que les événements doivent être envoyés au concentrateur Event Hub de manière partitionnée. Mais quelle clé convient-il d’utiliser ? **PartitionId** est un concept de logique de travail. Comme la clé qui nous importe réellement est **TollBoothId**, la valeur **PartitionKey** des données d’événement doit être **TollBoothId**. Pour ce faire, nous définissons **Partition By** sur **PartitionId** dans la requête. Étant donné que la sortie est un stockage d’objets blob, nous n’avons pas à nous soucier de la configuration d’une valeur de clé de partition, conformément à l’exigence n°4.

### <a name="multi-step-query-with-a-grouping-key"></a>Requête à plusieurs étapes avec clé de regroupement
* Entrée : concentrateur Event Hub avec 8 partitions
* Sortie : instance de concentrateur Event Hub avec 8 partitions

Requête :

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Cette requête contient une clé de regroupement. La même clé doit donc être traitée par la même instance de la requête. Nous pouvons utiliser la même stratégie que dans l’exemple précédent. Dans ce cas, la requête comporte plusieurs étapes. Chaque étape inclut-elle **Partition By PartitionId** ? Oui, la requête répond à l’exigence n°3. Pour la sortie, nous devons définir la clé de partition sur **PartitionId**, comme indiqué précédemment. Nous pouvons également observer que la sortie dispose du même nombre de partitions que l’entrée.

## <a name="example-scenarios-that-are-not-embarrassingly-parallel"></a>Exemples de scénarios qui n’impliquent *pas* un parallélisme massif

Dans la section précédente, nous vous avons présenté certains scénarios impliquant un parallélisme massif. Dans cette section, nous étudions des cas dans lesquels toutes les exigences ne sont pas remplies pour un parallélisme massif. 

### <a name="mismatched-partition-count"></a>Nombre de partitions d’entrée et de sortie différent
* Entrée : concentrateur Event Hub avec 8 partitions
* Sortie : concentrateur Event Hub avec 32 partitions

Dans ce cas, le type de requête importe peu. Si le nombre de partitions d’entrée ne correspond pas au nombre de partitions de sortie, la topologie n’est pas massivement parallèle.

### <a name="not-using-event-hubs-or-blob-storage-as-output"></a>Sortie ne correspondant pas à un concentrateur Event Hub ni à un stockage d’objets blob
* Entrée : concentrateur Event Hub avec 8 partitions
* Sortie : Power BI

Pour le moment, la sortie Power BI ne prend pas en charge le partitionnement. Par conséquent, ce scénario n’est pas de type massivement parallèle.

### <a name="multi-step-query-with-different-partition-by-values"></a>Requête à plusieurs étapes avec différentes valeurs Partition By
* Entrée : concentrateur Event Hub avec 8 partitions
* Sortie : concentrateur Event Hub avec 8 partitions

Requête :

    WITH Step1 AS (
    SELECT COUNT(*) AS Count, TollBoothId, PartitionId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1 Partition By TollBoothId
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Comme vous pouvez le voir, la deuxième étape utilise **TollBoothId** comme clé de partitionnement. Cette étape n’est pas la même que la première. Nous devons donc apporter quelques modifications. 

Les exemples précédents décrivent des travaux Stream Analytics qui respectent (ou pas) une topologie de type massivement parallèle. S’ils la respectent, ils présentent alors le potentiel pour une mise à l’échelle maximale. Pour les travaux qui ne correspondent pas à l’un de ces profils, des conseils de mise à l’échelle seront disponibles dans les futures mises à jour. Pour le moment, suivez les instructions générales indiquées dans les sections suivantes.

## <a name="calculate-the-maximum-streaming-units-of-a-job"></a>Calcul du nombre maximum d'unités de diffusion en continu pour un travail
Le nombre total d'unités de diffusion en continu qui peut être utilisé par un travail Stream Analytics varie selon le nombre d'étapes de la requête définie pour le travail et le nombre de partitions pour chaque étape.

### <a name="steps-in-a-query"></a>Étapes dans une requête
Une requête peut avoir une ou plusieurs étapes. Chaque étape est une sous-requête définie par le mot-clé **WITH**. La requête qui se trouve en dehors du mot-clé **WITH** (une seule requête) est également comptabilisée comme une étape, par exemple, l’instruction **SELECT** dans la requête suivante :

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute,3), TollBoothId

Cette requête compte deux étapes.

> [!NOTE]
> Cette requête est approfondie plus loin dans cet article.
>  

### <a name="partition-a-step"></a>Partitionnement d'une étape
Les conditions suivantes doivent être respectées pour procéder au partitionnement d'une étape :

* La source d'entrée doit être partitionnée. 
* L’instruction **SELECT** de la requête doit lire à partir d’une source d’entrée partitionnée.
* La requête de l’étape doit contenir le mot-clé **Partition By**.

Lorsqu’une requête est partitionnée, les événements d’entrée sont traités et agrégés dans des groupes de partition distincts, et les événements de sortie sont générés pour chacun des groupes. Si vous souhaitez procéder à un agrégat combiné, vous devez créer une deuxième étape non partitionnée à agréger.

### <a name="calculate-the-max-streaming-units-for-a-job"></a>Calcul des unités de diffusion en continu maximum pour un travail
Toutes les étapes non partitionnées ensemble peuvent être mises à l’échelle jusqu’à atteindre six unités SU par travail Stream Analytics. Pour ajouter des unités SU, une étape doit être partitionnée. Chaque partition peut comporter six unités SU.

<table border="1">
<tr><th>Requête</th><th>Nombre d’unités SU maximal pour le travail</th></td>

<tr><td>
<ul>
<li>La requête contient une étape.</li>
<li>L'étape n'est pas partitionnée.</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>Le flux de données d'entrée est partitionné par 3.</li>
<li>La requête contient une étape.</li>
<li>L'étape est partitionnée.</li>
</ul>
</td>
<td>18</td></tr>

<tr><td>
<ul>
<li>La requête contient 2 étapes.</li>
<li>Aucune des étapes n'est partitionnée.</li>
</ul>
</td>
<td>6</td></tr>

<tr><td>
<ul>
<li>Le flux de données d'entrée est partitionné par 3.</li>
<li>La requête contient 2 étapes. L'étape d'entrée est partitionnée et la deuxième étape ne l'est pas.</li>
<li>L’instruction <strong>SELECT</strong> lit dans l’entrée partitionnée.</li>
</ul>
</td>
<td>24 (18 pour les étapes partitionnées + 6 pour les étapes non partitionnées)</td></tr>
</table>

### <a name="examples-of-scaling"></a>Exemples de mise à l’échelle

La requête suivante calcule le nombre de voitures, dans une fenêtre de trois minutes, qui traversent un poste de péage pourvu de trois cabines de péage. Cette requête peut être mise à l’échelle jusqu’à six unités SU.

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Pour utiliser plus d’unités SU pour la requête, le flux de données d’entrée et la requête doivent être partitionnés. Comme la partition de flux de données est définie sur 3, la requête modifiée suivante peut être mise à l’échelle jusqu’à compter 18 unités SU :

    SELECT COUNT(*) AS Count, TollBoothId
    FROM Input1 Partition By PartitionId
    GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId

Lorsqu'une requête est partitionnée, les événements d'entrée sont traités et agrégées dans des groupes de partition distincts. Les événements de sortie sont également générés pour chacun des groupes. Le partitionnement peut provoquer des résultats inattendus si le champ **GROUP BY** n’est pas la clé de partition dans le flux de données d’entrée. Par exemple, dans la requête précédente, le champ **TollBoothId** n’est pas la clé de partition **d’Input1**. Le résultat est le suivant : les données de la cabine de péage « TollBooth 1 » peuvent être réparties dans plusieurs partitions.

Chaque partition **Input1** est traitée séparément par Stream Analytics. En conséquence, plusieurs enregistrements du nombre de voitures pour la même cabine de péage sont créés dans la même fenêtre bascule. Si la clé de partition d’entrée ne peut pas être modifiée, ce problème peut être résolu en ajoutant une étape non partitionnée, comme dans l’exemple suivant :

    WITH Step1 AS (
        SELECT COUNT(*) AS Count, TollBoothId
        FROM Input1 Partition By PartitionId
        GROUP BY TumblingWindow(minute, 3), TollBoothId, PartitionId
    )

    SELECT SUM(Count) AS Count, TollBoothId
    FROM Step1
    GROUP BY TumblingWindow(minute, 3), TollBoothId

Cette requête peut être mise à l’échelle jusqu’à comporter 24 unités SU.

> [!NOTE]
> Si vous joignez deux flux de données, assurez-vous qu’ils sont partitionnés par la clé de partition de la colonne que vous utilisez pour créer les jointures. Assurez-vous également d’avoir le même nombre de partitions dans les deux flux de données.
> 
> 

## <a name="configure-stream-analytics-streaming-units"></a>Configurer des unités de diffusion en continu Stream Analytics

1. Connectez-vous au [portail Azure](https://portal.azure.com).
2. Dans la liste des ressources, recherchez le travail Stream Analytics que vous souhaitez mettre à l’échelle, puis ouvrez-le.
3. Dans le panneau du travail, sous **Configurer**, cliquez sur **Échelle**.

    ![Configuration d’un travail Stream Analytics sur le portail Azure][img.stream.analytics.preview.portal.settings.scale]

4. Définissez les unités SU pour le travail à l’aide du curseur. Notez que vous êtes limité à des paramètres d’unité SU spécifiques.


## <a name="monitor-job-performance"></a>Surveillance des performances du travail
À l’aide du portail Azure, vous pouvez suivre le débit d’un travail :

![Surveillance des travaux Azure Stream Analytics][img.stream.analytics.monitor.job]

Calculez le débit prévu pour la charge de travail. Si le débit est plus faible que prévu, paramétrez la partition d’entrée ainsi que la requête, puis ajoutez des unités SU à votre travail.


## <a name="visualize-stream-analytics-throughput-at-scale-the-raspberry-pi-scenario"></a>Visualiser le débit Stream Analytics à l’échelle : scénario Raspberry Pi
Pour vous aider à comprendre comment les travaux Stream Analytics sont mis à l’échelle, nous avons effectué une expérience basée sur l’entrée d’un appareil Raspberry Pi. Cette expérience nous permet d’observer l’effet de plusieurs unités SU et partitions sur le débit.

Dans ce scénario, l’appareil envoie des données de capteur (clients) à un concentrateur Event Hub. Stream Analytics traite les données et envoie une alerte ou des statistiques en tant que sortie à un autre concentrateur Event Hub. 

Le client envoie des données de capteur au format JSON. La sortie des données est également au format JSON. Voici à quoi ressemblent les données :

    {"devicetime":"2014-12-11T02:24:56.8850110Z","hmdt":42.7,"temp":72.6,"prss":98187.75,"lght":0.38,"dspl":"R-PI Olivier's Office"}

La requête suivante permet d’envoyer une alerte quand lorsqu’une lumière est éteinte :

    SELECT AVG(lght),
     "LightOff" as AlertText
    FROM input TIMESTAMP
    BY devicetime
     WHERE
        lght< 0.05 GROUP BY TumblingWindow(second, 1)

### <a name="measure-throughput"></a>Mesurer le débit

Dans ce contexte, le débit correspond à la quantité de données d’entrée traitées par Stream Analytics au cours d’une durée fixe. (Nous avons mesuré le débit pendant 10 minutes.) Pour obtenir le meilleur débit de traitement pour les données d’entrée, l’entrée de flux de données et la requête ont été partitionnées. Nous avons inclus **COUNT()** dans la requête pour mesurer le nombre d’événements d’entrée traités. Pour vous assurer que le travail n’attende pas simplement les événements d’entrée, chaque partition du concentrateur Event Hub d’entrée a été préchargée avec environ 300 Mo de données d’entrée.

Le tableau suivant montre les résultats que nous avons observés lorsque nous avons augmenté le nombre d’unités SU et le nombre de partitions correspondant dans les concentrateurs d’événements.  

<table border="1">
<tr><th>Partitions d'entrée</th><th>Partitions de sortie</th><th>Unités de diffusion en continu</th><th>Débit soutenu
</th></td>

<tr><td>12</td>
<td>12</td>
<td>6</td>
<td>4,06 Mo/s</td>
</tr>

<tr><td>12</td>
<td>12</td>
<td>12</td>
<td>8,06 Mo/s</td>
</tr>

<tr><td>48</td>
<td>48</td>
<td>48</td>
<td>38,32 Mo/s</td>
</tr>

<tr><td>192</td>
<td>192</td>
<td>192</td>
<td>172,67 Mo/s</td>
</tr>

<tr><td>480</td>
<td>480</td>
<td>480</td>
<td>454,27 Mo/s</td>
</tr>

<tr><td>720</td>
<td>720</td>
<td>720</td>
<td>609,69 Mo/s</td>
</tr>
</table>

Et le graphique suivant présente une visualisation de la relation entre les unités SU et le débit.

![img.stream.analytics.perfgraph][img.stream.analytics.perfgraph]

## <a name="get-help"></a>Obtenir de l’aide
Pour obtenir une assistance, consultez le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics)

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d'Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

<!--Image references-->

[img.stream.analytics.monitor.job]: ./media/stream-analytics-scale-jobs/StreamAnalytics.job.monitor-NewPortal.png
[img.stream.analytics.configure.scale]: ./media/stream-analytics-scale-jobs/StreamAnalytics.configure.scale.png
[img.stream.analytics.perfgraph]: ./media/stream-analytics-scale-jobs/perf.png
[img.stream.analytics.streaming.units.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsStreamingUnitsExample.jpg
[img.stream.analytics.preview.portal.settings.scale]: ./media/stream-analytics-scale-jobs/StreamAnalyticsPreviewPortalJobSettings-NewPortal.png   

<!--Link references-->

[microsoft.support]: http://support.microsoft.com
[azure.management.portal]: http://manage.windowsazure.com
[azure.event.hubs.developer.guide]: http://msdn.microsoft.com/library/azure/dn789972.aspx

[stream.analytics.introduction]: stream-analytics-introduction.md
[stream.analytics.get.started]: stream-analytics-real-time-fraud-detection.md
[stream.analytics.query.language.reference]: http://go.microsoft.com/fwlink/?LinkID=513299
[stream.analytics.rest.api.reference]: http://go.microsoft.com/fwlink/?LinkId=517301


