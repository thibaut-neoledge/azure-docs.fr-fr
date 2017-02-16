---
title: Journaux de diagnostic Azure Stream Analytics | Microsoft Docs
description: "Découvrez comment analyser les journaux de diagnostic à partir de travaux Stream Analytics dans Microsoft Azure."
keywords: 
documentationcenter: 
services: stream-analytics
author: jeffstokes72
manager: jhubbard
editor: cgronlun
ms.assetid: 
ms.service: stream-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: data-services
ms.date: 02/01/2017
ms.author: jeffstok
translationtype: Human Translation
ms.sourcegitcommit: f0292efd50721ef58028df778052eb0ed6fcda84
ms.openlocfilehash: 724eba50b7428b0012e8f062e264ce057e2a5287


---
# <a name="job-diagnostic-logs"></a>Journaux de diagnostic des travaux

## <a name="introduction"></a>Introduction
Stream Analytics expose deux types de journaux : 
* Des [journaux d’activité](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-activity-logs) qui sont toujours activés et qui fournissent des informations sur les opérations effectuées sur les travaux.
* Des [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs) qui sont configurables par l’utilisateur et qui fournissent des informations plus détaillées sur tout ce qui concerne le travail, depuis sa création jusqu’à sa suppression en passant par sa mise à jour et son exécution.

## <a name="how-to-enable-diagnostic-logs"></a>Comment activer les journaux de diagnostic
Les journaux de diagnostic sont **désactivés** par défaut. Pour les activer, procédez comme suit :

Connectez-vous au portail Azure et accédez au panneau du travail de streaming et utilisez le panneau « Journaux de Diagnostic » sous « Surveillance ».

![navigation dans le panneau jusqu’aux journaux de diagnostic](./media/stream-analytics-job-diagnostic-logs/image1.png)  

Cliquez ensuite sur le lien « Activer les diagnostics ».

![activer les journaux de diagnostic](./media/stream-analytics-job-diagnostic-logs/image2.png)

Dans les diagnostics ouverts, définissez l’état sur « Activé ».

![modifier l’état des journaux de diagnostic](./media/stream-analytics-job-diagnostic-logs/image3.png)

Configurez la cible d’archivage souhaitée (compte de stockage, Event Hub, Log Analytics) et sélectionnez les catégories de journaux à collecter (Execution, Authoring). Enregistrez ensuite la nouvelle configuration de diagnostic.

Une fois enregistrée, la configuration entre en vigueur après 10 minutes environ. Au terme de ce délai, les journaux commencent à apparaître dans la cible d’archivage configurée que vous pouvez voir dans le panneau « Journaux de Diagnostics » :

![navigation dans le panneau jusqu’aux journaux de diagnostic](./media/stream-analytics-job-diagnostic-logs/image4.png)

Vous trouverez plus d’informations sur la configuration des diagnostics dans la page des [journaux de diagnostic](https://docs.microsoft.com/azure/monitoring-and-diagnostics/monitoring-overview-of-diagnostic-logs).

## <a name="diagnostic-logs-categories"></a>Catégories de journaux de diagnostic
Nous capturons actuellement deux catégories de journaux de diagnostic :

* **Authoring :** capture les journaux liés aux opérations de création de travaux (création, ajout et suppression d’entrées et de sorties, ajout et mise à jour de la requête, démarrage et arrêt du travail).
* **Execution :** capture ce qui se passe pendant l’exécution du travail.
    * Erreurs de connectivité
    * Erreurs de traitement des données
        * Événements non conformes à la définition de la requête (types et valeurs de champs ne correspondant pas, champs manquants, etc.)
        * Erreurs d’évaluation d’expression
    * etc.

## <a name="diagnostic-logs-schema"></a>Schéma des journaux de diagnostic
Tous les journaux sont stockés au format JSON et chaque entrée comprend les champs de chaîne courants suivants :

Nom | Description
------- | -------
time | L’horodatage (heure UTC) du journal.
resourceId | L’ID de la ressource sur laquelle l’opération a eu lieu (en majuscules). Comprend l’ID d’abonnement, le groupe et le nom du travail. Par exemple, `/SUBSCRIPTIONS/6503D296-DAC1-4449-9B03-609A1F4A1C87/RESOURCEGROUPS/MY-RESOURCE-GROUP/PROVIDERS/MICROSOFT.STREAMANALYTICS/STREAMINGJOBS/MYSTREAMINGJOB`
category | La catégorie du journal (`Execution` ou`Authoring`)
operationName | Le nom de l’opération qui est journalisée. Par exemple, `Send Events: SQL Output write failure to mysqloutput`
status | L’état de l’opération. Par exemple : `Failed, Succeeded`.
minimal | Le niveau du journal. Par exemple, `Error, Warning, Informational`
properties | détail spécifique de l’entrée du journal ; sérialisé comme chaîne JSON ; voir ci-dessous pour plus de détails

### <a name="execution-logs-properties-schema"></a>Schéma des propriétés des journaux Execution
Les journaux Execution contiennent des informations sur les événements qui se sont produits pendant l’exécution du travail Stream Analytics.
Selon le type d’événement, les propriétés ont un schéma différent. Les types suivants sont actuellement disponibles :

### <a name="data-errors"></a>Erreurs de données
Toute erreur dans le traitement des données relève de cette catégorie de journaux. Produit principalement pendant l’opération de lecture, de sérialisation et d’écriture de données. Il n’inclut pas les erreurs de connectivité qui sont traitées comme des événements génériques.

Nom | Description
------- | -------
Source | Nom de l’entrée ou de la sortie du travail où l’erreur s’est produite.
Message | Message associé à l’erreur.
Type | Le type d’erreur. Par exemple : `DataConversionError, CsvParserError, ServiceBusPropertyColumnMissingError`, etc.
Données | Contient des données utiles pour localiser avec précision la source de l’erreur. Troncation possible en fonction de la taille.

En fonction de la valeur d’**operationName**, les erreurs de données ont le schéma suivant :
* **Sérialiser des événements** - se produit pendant les opérations de lecture d’événements quand les données en entrée ne répondent pas aux conditions du schéma de requête :
    * Incompatibilité de type pendant la (dé)sérialisation de l’événement : champ à l’origine de l’erreur.
    * Impossible de lire un événement, sérialisation non valide : informations sur l’emplacement où l’erreur s’est produite dans les données d’entrée : nom d’objet blob pour une entrée d’objet blob, décalage et exemple des données.
* **Envoyer des événements** - opérations d’écriture : événement de streaming à l’origine de l’erreur.

### <a name="generic-events"></a>Événements génériques
Tout le reste

Nom | Description
-------- | --------
Error | (facultatif) Informations sur l’erreur, en général des informations sur l’exception si celles-ci sont disponibles.
Message| Message de journal.
Type | Type de message, correspond à la catégorisation interne des erreurs : par exemple, JobValidationError, BlobOutputAdapterInitializationFailure etc.
ID de corrélation : | [GUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) qui identifie de façon unique l’exécution du travail. Toutes les entrées du journal d’exécution générées depuis le démarrage du travail jusqu’à son arrêt ont le même « ID de corrélation ».



## <a name="next-steps"></a>Étapes suivantes
* [Présentation d’Azure Stream Analytics](stream-analytics-introduction.md)
* [Prise en main d’Azure Stream Analytics](stream-analytics-get-started.md)
* [Mise à l'échelle des travaux Azure Stream Analytics](stream-analytics-scale-jobs.md)
* [Références sur le langage des requêtes d'Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn834998.aspx)
* [Références sur l’API REST de gestion d’Azure Stream Analytics](https://msdn.microsoft.com/library/azure/dn835031.aspx)




<!--HONumber=Feb17_HO1-->


