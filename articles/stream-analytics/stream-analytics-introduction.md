---
title: "Présentation de Stream Analytics | Microsoft Docs"
description: "Découvrez Stream Analytics, un service géré qui vous permet d’analyser les données de diffusion en continu à partir de l’Internet des objets (IoT) en temps réel."
keywords: "analyse en tant que service, services gérés, traitement des données de diffusion en continu, analyse de diffusion en continu, qu’est-ce que Stream Analytics"
services: stream-analytics
documentationcenter: 
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 613c9b01-d103-46e0-b0ca-0839fee94ca8
ms.service: stream-analytics
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 06/16/2017
ms.author: jeffstok
ms.translationtype: HT
ms.sourcegitcommit: c3ea7cfba9fbf1064e2bd58344a7a00dc81eb148
ms.openlocfilehash: 2fda07b8444b196d831e5e9fe7ade3e5017d36f9
ms.contentlocale: fr-fr
ms.lasthandoff: 07/19/2017

---

# <a name="what-is-stream-analytics"></a>Qu’est-ce que Stream Analytics ?

Azure Stream Analytics est un moteur de traitement des événements entièrement géré qui vous permet de définir des calculs analytiques en temps réel sur la diffusion des données. Les données peuvent provenir d’appareils, de capteurs, de sites web, de flux issus des réseaux sociaux, d’applications, de systèmes d’infrastructure et bien plus encore. 

## <a name="what-can-i-use-stream-analytics-for"></a>Dans quels cas puis-je utiliser Stream Analytics ?

À l’aide de Stream Analytics, vous pouvez examiner des volumes importants de données issus d’appareils ou de processus, extraire des informations à partir du flux de données et rechercher des modèles, des tendances et des relations. Vous pouvez effectuer les tâches de l’application en vous basant sur le contenu des données. Par exemple, vous pouvez déclencher des alertes, lancer des flux de travail d’automatisation, alimenter un outil de création de rapports en informations, tel que Power BI, ou stocker des données pour un examen ultérieur. 

Voici quelques exemples de scénarios Stream Analytics :

* Analyse en temps réel et personnalisée de transactions en actions et propositions d’alertes par des sociétés de services financiers.
* Détection des fraudes en temps réel basée sur l’examen des données relatives aux transactions. 
* Services de protection des données et des identités.
* Analyse des données générées par des capteurs et des déclencheurs intégrés à des objets physiques (Internet des objets ou IoT).
* Analyse de parcours Web.
* Applications de gestion de la relation client (CRM), telles que l’émission d’alertes en cas de dégradation de l’expérience client au cours d’une période donnée.

## <a name="how-does-stream-analytics-work"></a>Comment fonctionne Stream Analytics ?

Le diagramme suivant illustre le pipeline Stream Analytics, en montrant comment les données sont ingérées, analysées, puis envoyées pour la présentation ou l’action. 

![Pipeline Stream Analytics](./media/stream-analytics-introduction/stream_analytics_intro_pipeline.png)

Stream Analytics démarre avec une source de données de diffusion en continu. Les données peuvent être ingérées dans Azure à partir d’un appareil à l’aide d’un concentrateur d’événements Azure ou d’un IoT Hub. Les données peuvent également être extraites d’un magasin de données, tel que le Stockage Blob Azure. 

Pour examiner le flux de données, vous créez un *travail* Stream Analytics indiquant d’où proviennent les données. Le travail spécifie également une *transformation*&mdash;comment rechercher des données, des modèles ou des relations. Pour cette tâche, Stream Analytics prend en charge un langage de requête de type SQL qui vous permet de filtrer,de trier, d’agréger et de joindre les données de diffusion sur une période donnée.

Enfin, le travail spécifie une sortie vers laquelle envoyer les données transformées. Cela vous permet de contrôler les éléments à effectuer en réponse aux informations que vous avez analysées. Par exemple, en réponse à l’analyse, vous pouvez :

* Envoyer une commande pour modifier les paramètres d’un appareil. 
* Envoyer des données vers une file d’attente surveillée par un processus qui prend des mesures en fonction de ce qu’il trouve. 
* Envoyer des données à un tableau de bord Power BI pour la création de rapports.
* Envoyer des données vers un dispositif de stockage, tel que Data Lake Store, une base de données SQL Server ou Stockage Blob ou Table Azure.

Lors de l’exécution d’un travail, vous pouvez surveiller et régler le nombre d’événements traités par seconde. Vous pouvez également faire en sorte que des travaux produisent des journaux de diagnostic pour la résolution de problèmes.

## <a name="key-capabilities-and-benefits"></a>Avantages et fonctionnalités clés

Stream Analytics est conçu pour être facile à utiliser, flexible, évolutif selon le volume du travail et économique.

### <a name="connectivity-to-many-inputs-and-outputs"></a>Connectivité à de nombreuses entrées et sorties

Stream Analytics se connecte directement à [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) et à [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) pour l’ingestion de flux de données, ainsi qu’au [service de stockage BLOB Azure](https://docs.microsoft.com/azure/storage/storage-introduction#blob-storage-accounts) pour l’ingestion de données d’historique. Si vous obtenez des données à partir de concentrateurs d’événements, vous pouvez associer Stream Analytics à d’autres sources de données et moteurs de traitement.

L’entrée de travail peut également inclure des données de référence (données statiques ou à variation lente). Vous pouvez joindre des données de diffusion à ces données de référence pour effectuer des opérations de recherche de la même façon que vous le feriez avec des requêtes de base de données.

La sortie d’un travail Stream Analytics peut être acheminée dans plusieurs directions. Elle peut être écrite dans le stockage, de type objets Blob ou tables du stockage Azure, Azure SQL DB, Azure Data Lake Store ou Azure Cosmos DB. À partir de là, les données peuvent être transmises pour une analyse de lots via Azure HDInsight. Vous pouvez envoyer la sortie vers un autre service pour une utilisation par un autre processus, tels que des concentrateurs d’événements, des rubriques de Service Bus ou des files d’attente. Vous pouvez envoyer la sortie à Power BI pour visualisation.

### <a name="ease-of-use"></a>Simplicité d'utilisation

Pour définir les transformations, vous utilisez un [langage de requête Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx) simple et déclaratif qui vous permet de créer des analyses complexes sans programmation. Le langage de requête considère les données de diffusion comme entrée. Vous pouvez ensuite filtrer et trier les données, agréger des valeurs, effectuer des calculs, joindre des données (au sein d’un flux de données ou à des données de référence) et utiliser des fonctions géospatiales. Vous pouvez modifier les requêtes dans le portail, à l’aide d’IntelliSense et de la vérification de la syntaxe, et tester les requêtes à l’aide d’exemples de données que vous pouvez extraire à partir du flux en direct.

### <a name="extensible-query-language"></a>Langage de requête extensible

Vous pouvez étendre les fonctionnalités du langage de requête en définissant et en appelant des fonctions supplémentaires. Vous pouvez définir des appels de fonction dans le service Azure Machine Learning pour tirer parti des solutions d’Azure Machine Learning. Vous pouvez également intégrer des fonctions JavaScript définies par l’utilisateur afin d’effectuer des calculs complexes dans le cadre d’une requête Stream Analytics.

### <a name="scalability"></a>Extensibilité

Stream Analytics peut gérer jusqu’à 1 Go de données entrantes par seconde. L’intégration à [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/) et [Azure IoT Hub](https://azure.microsoft.com/services/iot-hub/) permet aux travaux d’ingérer des millions d’événements par seconde. Ces événements peuvent provenir d’appareils connectés, de flux de clics, de fichiers journaux, etc. À l’aide de la fonction de partition des concentrateurs d’événements, vous pouvez partitionner des calculs en étapes logiques, chacune avec la possibilité d’être partitionnées pour augmenter l’évolutivité.

### <a name="low-cost"></a>Faible coût

Comme un service cloud, Stream Analytics est optimisé pour vous permettre de travailler à faible coût. Vous payez à l’utilisation en fonction de l’unité de diffusion et de la quantité de données traitées par le système. L’utilisation est définie en fonction du volume d’événements traités et de la quantité de puissance de calcul configurée dans le cluster pour gérer les travaux Stream Analytics.

### <a name="reliability-quick-recovery-and-repeatability"></a>Fiabilité, récupération rapide et répétabilité

En tant que service géré dans le Cloud, Stream Analytics permet d’éviter la perte de données et d’assurer la continuité. En cas de panne, le service propose des capacités de récupération intégrées. Doté d’une capacité de conservation de l’état en interne, le service peut fournir des résultats reproductibles en assurant la possibilité d’archiver des événements et de recommencer un traitement ultérieurement, tout en obtenant les mêmes résultats. Cette fonctionnalité vous permet de revenir en arrière et d’examiner les calculs lors de l’analyse des causes premières, l’analyse de scénarios, etc.

## <a name="next-steps"></a>Étapes suivantes

* Commencez par l’[expérimentation d’entrées et de requêtes à partir d’appareils IoT](stream-analytics-get-started-with-azure-stream-analytics-to-process-data-from-iot-devices.md).
* Créez une [solution Stream Analytics de bout en bout](stream-analytics-real-time-fraud-detection.md) qui examine les métadonnées téléphoniques pour rechercher des appels frauduleux.
* En savoir plus sur le langage de requête de type SQL pour Stream Analytics, ainsi que sur les concepts uniques comme les [fonctions de fenêtre](stream-analytics-window-functions.md).
* Découvrez comment [mettre à l’échelle des travaux Stream Analytics](stream-analytics-scale-jobs.md). 
* Découvrez comment [intégrer Stream Analytics et Azure Machine Learning](stream-analytics-machine-learning-integration-tutorial.md).
* Trouvez les réponses aux questions que vous vous posez sur Stream Analytics dans le [forum Azure Stream Analytics](https://social.msdn.microsoft.com/Forums/home?forum=AzureStreamAnalytics).


