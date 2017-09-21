---
title: "Traitement de l’ordre et du retard des événements avec Azure Stream Analytics | Microsoft Docs"
description: "Découvrez le fonctionnement d’Azure Stream Analytics avec des événements en retard ou en désordre dans les flux de données."
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
ms.translationtype: HT
ms.sourcegitcommit: fda37c1cb0b66a8adb989473f627405ede36ab76
ms.openlocfilehash: cf9a43fbb82a32c92d66f25809916d3ccde1a20d
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---
# <a name="azure-stream-analytics-event-order-handling"></a>Traitement de l’ordre des événements Azure Stream Analytics

Dans un flux de données temporelles d’événements, chaque événement est horodaté. Azure Stream Analytics affecte des horodatages à chaque événement à l’aide de l’heure d’arrivée ou de l’heure de l’application. 

La colonne **System.Timestamp** indique l’horodatage affecté à l’événement. L’heure d’arrivée est affectée à la source d’entrée lorsque l’événement atteint la source. L’heure d’arrivée est **EventEnqueuedTime** pour l’entrée du concentrateur d’événements et l’[heure de dernière modification de l’objet BLOB](https://docs.microsoft.com/en-us/dotnet/api/microsoft.windowsazure.storage.blob.blobproperties.lastmodified?view=azurestorage-8.1.3) pour l’entrée de l’objet BLOB. L’heure de l’application est affectée lorsque l’événement est généré et qu’il fait partie de la charge utile. 

Pour traiter les événements par heure de l’application, utilisez la clause TIMESTAMP BY dans la requête select. En l’absence de la clause TIMESTAMP BY, les événements sont traités par heure d’arrivée. Vous pouvez accéder à l’heure d’arrivée à l’aide de la propriété **EventEnqueuedTime** pour le concentrateur d’événements et à l’aide de la propriété **BlobLastModified** de l’entrée d’objet blob. 

Azure Stream Analytics génère un résultat par ordre d’horodatage et fournit quelques paramètres pour traiter les données en désordre.

![Traitement des événements Stream Analytics](media/stream-analytics-event-handling/stream-analytics-event-handling.png)

Les flux d’entrée qui ne sont pas dans l’ordre sont :
* Triés (et par conséquent *retardés*).
* Ajustés par le système, en fonction de la stratégie spécifiée par l’utilisateur.

Stream Analytics tolère les événements tardifs et en désordre lors du traitement par heure de l’application.

**Tolérance d’arrivée tardive**

* Le paramètre de tolérance d’arrivée tardive est applicable uniquement lors d’un traitement par heure de l’application. Sinon, il est ignoré.
* La tolérance d’arrivée tardive est la différence maximale entre l’heure d’arrivée et l’heure de l’application. Si l’heure de l’application est antérieure à *(Heure d’arrivée - Plage d’arrivée tardive)*, elle est définie sur *(Heure d’arrivée - Plage d’arrivée tardive)*.
* Lorsque plusieurs partitions du même flux d’entrée ou de plusieurs flux d’entrée sont combinées, la tolérance d’arrivée tardive correspond au délai d’attente maximal des nouvelles données pour chaque partition. 

En bref, la plage d’arrivée tardive correspond au délai maximal entre la génération des événements et la réception de l’événement au niveau de la source d’entrée.
Un ajustement en fonction de la tolérance d’arrivée tardive est effectué en premier, puis un ajustement en fonction des événements en désordre. La colonne **System.Timestamp** indique l’horodatage final affecté à l’événement.

**Tolérance pour les événements en désordre**

* Les événements qui arrivent dans le désordre, mais dans la « plage de tolérance des événements en désordre » sont *réorganisés par horodatage*. 
* Les événements arrivant en dehors de la plage de tolérance sont *supprimés* ou *ajustés*.
    * **Ajustés** : les événements sont ajustés de façon à ce qu’ils semblent arrivés à l’heure la plus récente acceptable. 
    * **Supprimés** : ignorés.

Pour réorganiser les événements reçus dans la « plage de tolérance des événements en désordre », la sortie de la requête est *retardée par la plage de tolérance des événements en désordre*.

**Exemple**

Tolérance d’arrivée tardive = 10 minutes<br/>
Tolérance pour les événements en désordre = 3 minutes<br/>
Traitement selon l’heure de l’application<br/>

Événements :

Événement 1 _Heure de l’application_ = 00:00:00, _Heure d’arrivée_ = 00:10:01, _System.Timestamp_ = 00:00:01, ajusté car (_Heure d’arrivée_ - _Heure de l’application_) est supérieur à la tolérance d’arrivée tardive.

Événement 2 _Heure de l’application_ = 00:00:01, _Heure d’arrivée_ = 00:10:01, _System.Timestamp_ = 00:00:01, non ajusté car l’heure de l’application est comprise dans la plage d’arrivée tardive.

Événement 3 _Heure de l’application_ = 00:10:00, _Heure d’arrivée_ = 00:10:02, _System.Timestamp_ = 00:10:00, non ajusté car l’heure de l’application est comprise dans la plage d’arrivée tardive.

Événement 4 _Heure de l’application_ = 00:09:00, _Heure d’arrivée_ = 00:10:03, _System.Timestamp_ = 00:09:00, accepté avec l’horodatage d’origine car l’heure de l’application est comprise dans la tolérance des événements en désordre.

Événement 5 _Heure de l’application_ = 00:06:00, _Heure d’arrivée_ = 00:10:04, _System.Timestamp_ = 00:07:00, ajusté car l’heure de l’application est ultérieure à la tolérance des événements en désordre.



## <a name="get-help"></a>Obtenir de l’aide
Pour une assistance supplémentaire, essayez notre [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/en-US/home?forum=AzureStreamAnalytics).

## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-real-time-fraud-detection.md)
* [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)

