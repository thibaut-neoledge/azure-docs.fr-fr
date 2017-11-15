---
title: "Azure Service Fabric - Surveillance des performances avec l’extension Microsoft Azure Diagnostics | Microsoft Docs"
description: Utilisez Microsoft Azure Diagnostics pour collecter les compteurs de performances de vos clusters Azure Service Fabric.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/05/2017
ms.author: dekapur
ms.openlocfilehash: f71c483eba201eae91c15b84a2ed42fcb68ae575
ms.sourcegitcommit: 6a6e14fdd9388333d3ededc02b1fb2fb3f8d56e5
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/07/2017
---
# <a name="performance-monitoring-with-windows-azure-diagnostics-extension"></a>Surveillance des performances avec l’extension Microsoft Azure Diagnostics

Ce document explique comment configurer la collecte des compteurs de performances via Microsoft Azure Diagnostics pour les clusters Windows. Pour les clusters Linux, configurez [l’agent OMS](service-fabric-diagnostics-oms-agent.md) afin de collecter les compteurs de performances de vos nœuds. 

 > [!NOTE]
> Pour cette procédure, l’extension Microsoft Azure Diagnostics doit être déployée sur votre cluster. Si ce n’est pas le cas, accédez à la rubrique [Agrégation et collecte d’événements à l’aide de Microsoft Azure Diagnostics](service-fabric-diagnostics-event-aggregation-wad.md) et suivez les étapes de la section *Déployer l’extension Diagnostics*.

## <a name="collect-performance-counters-via-the-wadcfg"></a>Collecter des compteurs de performances via le WadCfg

Pour collecter les compteurs de performances via Microsoft Azure Diagnostics, vous devez modifier la configuration dans le modèle Resource Manager de votre cluster. Suivez ces étapes pour ajouter un compteur de performances à collecter dans votre modèle, puis exécutez une mise à niveau des ressources Resource Manager.

1. Recherchez la configuration Microsoft Azure Diagnostics dans les modèles de votre cluster (recherchez `WadCfg`). Vous allez ajouter des compteurs de performances à collecter sous `DiagnosticMonitorConfiguration`.

2. Définissez votre configuration pour collecter les compteurs de performances en ajoutant la section suivante à votre `DiagnosticMonitorConfiguration`. 

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": []
    }
    ```

    `scheduledTransferPeriod` définit à quelle fréquence les valeurs des compteurs collectés sont transférées vers votre table de stockage Azure et tous les récepteurs configurés. 

3. Ajoutez les compteurs de performances à collecter au `PerformanceCounterConfiguration` qui a été déclaré à l’étape précédente. Chaque compteur à collecter est défini avec un `counterSpecifier`, un `sampleRate`, un `unit` et un `annotation`, ainsi qu’avec tous les `sinks` applicables. Voici un exemple de compteur de performances concernant le *temps processeur total* (durée d’utilisation de l’UC pour les opérations de traitement) :

    ```json
    "PerformanceCounters": {
        "scheduledTransferPeriod": "PT1M",
        "PerformanceCounterConfiguration": [
            {
                "counterSpecifier": "\\Processor(_Total)\\% Processor Time",
                "sampleRate": "PT1M",
                "unit": "Percent",
                "annotation": [
                ],
                "sinks": ""
            }
        ]
    }
    ```

    Vous pouvez modifier le taux d’échantillonnage du compteur selon vos besoins. Le format utilisé est le suivant : `PT<time><unit>`. Par conséquent, si vous souhaitez que le compteur soit collecté à chaque seconde, vous devez définir le taux d’échantillonnage ainsi : `"sampleRate": "PT15S"`.

    De même, vous pouvez collecter plusieurs autres compteurs de performances en les ajoutant à la liste située dans `PerformanceCounterConfiguration`. Même si vous pouvez utiliser `*` pour spécifier des groupes de compteurs de performances portant un nom similaire, l’envoi de tous les compteurs via un récepteur (vers Application Insights) nécessite de les déclarer séparément. 

4. Une fois que vous avez ajouté les compteurs de performances à collecter, vous devez mettre à niveau votre ressource de cluster afin que ces modifications soient répercutées dans le cluster en cours d’exécution. Enregistrez le `template.json` modifié, puis ouvrez PowerShell. Vous pouvez mettre à niveau votre cluster à l’aide de `New-AzureRmResourceGroupDeployment`. L’appel nécessite le nom du groupe de ressources, le fichier de modèle mis à jour et le fichier de paramètres. Ensuite, il demande à Resource Manager d’apporter les modifications nécessaires aux ressources que vous avez mises à jour. Une fois connecté à votre compte et au bon abonnement, utilisez la commande suivante pour exécuter la mise à niveau :

    ```sh
    New-AzureRmResourceGroupDeployment -ResourceGroupName <ResourceGroup> -TemplateFile <PathToTemplateFile> -TemplateParameterFile <PathToParametersFile> -Verbose
    ```

5. Une fois la mise à niveau terminée (au bout de 15 à 45 minutes), Microsoft Azure Diagnostics doit collecter les compteurs de performances et les envoyer à une table du compte de stockage déclaré dans `WadCfg`.

## <a name="next-steps"></a>Étapes suivantes
* Pour voir vos compteurs de performances dans Application Insights, ajoutez le récepteur Application Insights à votre `WadCfg`. Pour configurer le récepteur d’Application Insights, lisez la section *Ajouter le récepteur AI au modèle Resource Manager* de la rubrique [Analyse et visualisation d’événements avec Application Insights](service-fabric-diagnostics-event-analysis-appinsights.md).
* Collectez d’autres compteurs de performances pour votre cluster. Consultez [Mesures de performances](service-fabric-diagnostics-event-generation-perf.md) pour obtenir la liste des compteurs à collecter.
* [Utilisez les fonctionnalités de surveillance et de diagnostics avec une machine virtuelle Windows et des modèles Azure Resource Manager](../virtual-machines/windows/extensions-diagnostics-template.md) pour apporter des modifications à votre `WadCfg` (y compris pour la configuration de comptes de stockage supplémentaires vers lesquels envoyer des données de diagnostics).