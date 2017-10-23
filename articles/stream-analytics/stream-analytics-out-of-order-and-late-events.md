---
title: "Traitement de l’ordre et du retard des événements avec Azure Stream Analytics | Microsoft Docs"
description: "Découvrez le fonctionnement de Stream Analytics avec des événements en retard ou en désordre dans les flux de données."
keywords: "Événements en désordre, en retard"
documentationcenter: 
services: stream-analytics
author: samacha
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 04/20/2017
ms.author: samacha
ms.openlocfilehash: cf57bd12a62b3de8ac49b26ce7cdc40aec0b6738
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="azure-stream-analytics-event-order-handling"></a>Traitement de l’ordre des événements Azure Stream Analytics

Dans un flux de données temporelles d’événements, chaque événement est horodaté. Azure Stream Analytics affecte un horodatage à chaque événement à l’aide de l’heure d’arrivée ou de l’heure de l’application. La colonne "System.Timestamp" indique l’horodatage affecté à l’événement. Une heure d’arrivée est affectée à la source d’entrée quand l’événement atteint la source. L’heure d’arrivée est EventEnqueuedTime pour l’entrée Event Hub et [l’heure de dernière modification de l’objet blob](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) pour l’entrée de l’objet blob. L’heure de l’application est affectée quand l’événement est généré et qu’il fait partie de la charge utile. Pour traiter les événements selon l’heure de l’application, utilisez la clause "Timestamp by" dans la requête select. En l’absence de la clause "Timestamp by", les événements sont traités par heure d’arrivée. Vous pouvez accéder à l’heure d’arrivée à l’aide de la propriété EventEnqueuedTime pour le hub d’événements et à l’aide de la propriété BlobLastModified pour l’entrée de l’objet blob. Azure Stream Analytics génère un résultat par ordre d’horodatage et fournit quelques paramètres pour traiter les données en désordre.

![Traitement des événements Stream Analytics](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

Les flux d’entrée qui ne sont pas dans l’ordre sont :
* Triés (et par conséquent **retardés**).
* Ajustés par le système, en fonction de la stratégie spécifiée par l’utilisateur.

Stream Analytics tolère les événements tardifs et en désordre lors du traitement selon l’heure de l’application.

**Tolérance d’arrivée tardive**

* Ce paramètre est applicable uniquement lors d’un traitement par heure de l’application, sinon il est ignoré.
* Il s’agit de la différence maximale entre l’heure d’arrivée et l’heure de l’application. Si l’heure de l’application est antérieure à (Heure d’arrivée - Plage d’arrivée tardive), elle est définie sur (Heure d’arrivée - Plage d’arrivée tardive)
* Lorsque plusieurs partitions du même flux d’entrée ou de plusieurs flux d’entrée sont combinées, la tolérance d’arrivée tardive correspond au délai d’attente maximal des nouvelles données pour chaque partition. 

En bref, la plage d’arrivée tardive correspond au délai maximal entre la génération de l’événement et la réception de l’événement au niveau de la source d’entrée.
Un ajustement en fonction de la tolérance d’arrivée tardive est effectué en premier, puis un ajustement en fonction des événements en désordre. La colonne **System.Timestamp** indique l’horodatage final affecté à l’événement.

**Tolérance pour les événements en désordre**

* Les événements qui arrivent dans le désordre, mais dans la « plage de tolérance pour les événements en désordre » définie sont **réorganisés par horodatage**. 
* Les événements arrivant en dehors de la plage de tolérance sont **supprimés ou ajustés**.
    * **Ajustés** : les événements sont ajustés de façon à ce qu’ils semblent arrivés à l’heure la plus récente acceptable. 
    * **Supprimés** : ignorés.

Pour réorganiser les événements reçus dans la « plage de tolérance pour les événements en désordre », la sortie de la requête est **retardée par la plage de tolérance pour les événements en désordre**.

**Exemple**

Tolérance d’arrivée tardive = 10 minutes<br/>
Tolérance pour les événements en désordre = 3 minutes<br/>
Traitement selon l’heure de l’application<br/>

Événements :

Événement 1 _Heure de l’application_ = 00:00:00, _Heure d’arrivée_ = 00:10:01, _System.Timestamp_ = 00:00:01, ajusté car (_Heure d’arrivée_ - _Heure de l’application_) est supérieur à la tolérance d’arrivée tardive.

Événement 2 _Heure de l’application_ = 00:00:01, _Heure d’arrivée_ = 00:10:01, _System.Timestamp_ = 00:00:01, non ajusté car l’heure de l’application est comprise dans la plage d’arrivée tardive.

Événement 3 _Heure de l’application_ = 00:10:00, _Heure d’arrivée_ = 00:10:02, _System.Timestamp_ = 00:10:00, non ajusté car l’heure de l’application est comprise dans la plage d’arrivée tardive.

Événement 4 _Heure de l’application_ = 00:09:00, _Heure d’arrivée_ = 00:10:03, _System.Timestamp_ = 00:09:00, accepté avec l’horodatage d’origine car l’heure de l’application est comprise dans la tolérance pour les événements en désordre.

Événement 5 _Heure de l’application_ = 00:06:00, _Heure d’arrivée_ = 00:10:04, _System.Timestamp_ = 00:07:00, ajusté car l’heure de l’application est ultérieure à la tolérance pour les événements en désordre.



## <a name="get-help"></a>Obtenir de l’aide
Pour une assistance supplémentaire, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Étapes suivantes
* [Présentation de Stream Analytics](stream-analytics-introduction.md)
* [Prise en main de Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l’échelle des travaux Stream Analytics](stream-analytics-scale-jobs.md)
* [Informations de référence sur le langage de requête Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion de Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)
