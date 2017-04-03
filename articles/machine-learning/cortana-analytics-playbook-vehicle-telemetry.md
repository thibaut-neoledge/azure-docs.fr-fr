---
title: "Prédire l’état des véhicules et les habitudes de conduite | Microsoft Docs"
description: "Utilisez les fonctionnalités de Cortana Intelligence pour obtenir des informations en temps réel et prédictives sur l’état des véhicules et les habitudes de conduite."
services: machine-learning
documentationcenter: 
author: bradsev
manager: jhubbard
editor: cgronlun
ms.assetid: 09fad60b-2f48-488b-8a7e-47d1f969ec6f
ms.service: machine-learning
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 03/24/2017
ms.author: bradsev
translationtype: Human Translation
ms.sourcegitcommit: f497366f8e66ba79b0e5978fde54d0b33048aa8d
ms.openlocfilehash: 3467c5549381f1354987fead424646afe847739c
ms.lasthandoff: 01/24/2017


---
# <a name="vehicle-telemetry-analytics-solution-playbook"></a>Manuel de la solution Vehicle Telemetry Analytics
Ce **menu** contient des liens vers les chapitres de ce manuel. 

[!INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## <a name="overview"></a>Vue d'ensemble
Nous avons transféré nos superordinateurs du laboratoire à notre garage ! Ces automobiles de pointe contiennent une multitude de capteurs capables de suivre et de surveiller des millions d'événements chaque seconde. Nous pensons que d’ici 2020, la plupart de ces voitures seront connectées à Internet. Imaginez le potentiel qu’offrent toutes ces données pour améliorer la sécurité, la fiabilité et le plaisir de la conduite ! Microsoft a réalisé ce rêve en développant Cortana Intelligence.

Cortana Intelligence de Microsoft est une suite de traitement du Big Data et d’analyse avancée entièrement gérée, qui vous permet de convertir vos données en action intelligente. Nous souhaitons vous présenter le modèle de la solution Vehicle Telemetry Analytics Cortana Intelligence. Cette solution montre comment les concessions, les constructeurs automobiles et les compagnies d’assurance peuvent utiliser les fonctionnalités de Cortana Intelligence pour obtenir des informations en temps réel et prédictives sur l’état des véhicules et les habitudes de conduite. 

La solution est implémentée comme un [modèle d’architecture lambda](https://en.wikipedia.org/wiki/Lambda_architecture) montrant tout le potentiel de la plateforme Cortana Intelligence pour le traitement en temps réel et par lots. La solution : 

* fournit un simulateur de télématique des véhicules ;
* utilise Event Hubs pour la réception dans Azure de millions d’événements de télémétrie virtuels associés aux véhicules ; 
* utilise Stream Analytics pour obtenir un aperçu en temps réel de l’intégrité du véhicule ;
* conserve les données dans un stockage à long terme pour une analyse par lot plus riche ; 
* s’appuie sur Machine Learning pour la détection d’anomalies en temps réel et le traitement par lots afin d’obtenir des informations prédictives ;
* utilise HDInsight pour transformer les données à l’échelle et Data Factory pour gérer l’orchestration, la planification, la gestion des ressources et la surveillance du pipeline de traitement par lots ; 
* offre à cette solution un tableau de bord complet permettant de visualiser à la fois les données en temps réel et les analyses prédictives à l’aide de Power BI.

## <a name="architecture"></a>Architecture
![Diagramme d’architecture de solution](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png)
*Figure 1 – Architecture d’analyse de télémétrie de véhicule*

Cette solution inclut les **composants Cortana Intelligence** suivants et présente leur intégration de bout en bout :

* **Event Hubs** , pour la réception dans Azure de millions d’événements de télémétrie associés aux véhicules.
* **STREAM ANALYTICS** , pour une visibilité en temps réel sur l’état des véhicules et une conservation de ces données dans un stockage à long terme afin d’enrichir les analyses par lots.
* **MACHINE LEARNING** , pour la détection d’anomalies en temps réel et le traitement par lots afin d’obtenir des informations prédictives.
* **HDInsight** est utilisé pour transformer les données à grande échelle
* **Data Factory** gère l’orchestration, la planification, la gestion des ressources et la surveillance du pipeline de traitement par lots.
* **POWER BI** offre à cette solution un tableau de bord complet permettant de visualiser à la fois les données en temps réel et les analyses prédictives.

Cette solution utilise deux **sources de données**différentes : 

* **SIMULATION DES SIGNAUX ET DIAGNOSTICS D’UN VÉHICULE**: un simulateur télématique de véhicule émet des informations de diagnostic et des signaux correspondant à l’état du véhicule et au schéma de conduite à un moment donné dans le temps. 
* **CATALOGUE DE VÉHICULES**: un jeu de données de référence associé à un mappage VIN/modèle.


