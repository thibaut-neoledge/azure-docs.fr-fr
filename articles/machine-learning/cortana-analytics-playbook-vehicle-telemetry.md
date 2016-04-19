<properties 
	pageTitle="Manuel de la solution Vehicle Telemetry Analytics | Microsoft Azure" 
	description="Utilisez les fonctionnalités de Cortana Intelligence pour obtenir des informations en temps réel et prédictives sur l’état des véhicules et les habitudes de conduite." 
	services="machine-learning" 
	documentationCenter="" 
	authors="bradsev" 
	manager="paulettm" 
	editor="cgronlun" />

<tags 
	ms.service="machine-learning" 
	ms.workload="data-services" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="03/04/2016" 
	ms.author="bradsev" />


# Manuel de la solution Vehicle Telemetry Analytics

Ce **menu** contient des liens vers les chapitres de ce manuel.

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## Vue d'ensemble
Nous avons transféré nos superordinateurs du laboratoire à notre garage ! Ces automobiles de pointe contiennent une multitude de capteurs capables de suivre et de surveiller des millions d'événements chaque seconde. Nous pensons que d’ici 2020, la plupart de ces voitures seront connectées à Internet. Imaginez le potentiel qu’offrent toutes ces données pour améliorer la sécurité, la fiabilité et le plaisir de la conduite. Microsoft a réalisé ce rêve en développant Cortana Intelligence.

Cortana Intelligence de Microsoft est une suite de traitement du Big Data et d’analyse avancée entièrement gérée, qui vous permet de convertir vos données en action intelligente. Nous souhaitons vous présenter le modèle de la solution Vehicle Telemetry Analytics Cortana Intelligence. Cette solution montre comment les concessions, les constructeurs automobiles et les compagnies d’assurance peuvent utiliser les fonctionnalités de Cortana Intelligence pour obtenir des informations en temps réel et prédictives sur l’état des véhicules et les habitudes de conduite.

La solution est implémentée comme un [modèle d’architecture lambda](https://en.wikipedia.org/wiki/Lambda_architecture) montrant tout le potentiel de la plateforme Cortana Intelligence pour le traitement en temps réel et par lots. Elle inclut un simulateur de télématique de véhicule, exploite des hubs d'événements pour recevoir des millions d'événements télémétriques de véhicule simulés dans Azure, utilise Stream Analytics pour obtenir des informations en temps réel sur l’état du véhicule, puis conserve ces données dans un stockage à long terme à des fins d'analyse par lots plus approfondie. Elle s’appuie sur Machine Learning pour la détection d’anomalies en temps réel et le traitement par lots afin d’obtenir des informations prédictives. HDInsight transforme les données à l’échelle, tandis que Data Factory gère l’orchestration, la planification, la gestion des ressources et la surveillance du pipeline de traitement par lots. Enfin, Power BI offre à cette solution un tableau de bord complet permettant de visualiser à la fois les données en temps réel et les analyses prédictives.

## Architecture

![](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png) *Figure 1 – Architecture de la solution Vehicle Telemetry Analytics*

Cette solution inclut les **composants Cortana Intelligence** suivants et présente leur intégration de bout en bout.


- **Event Hubs**, pour la réception dans Azure de millions d’événements de télémétrie associés aux véhicules.
- **Stream Analytics**, pour une visibilité en temps réel sur l’état des véhicules et une conservation de ces données dans un stockage à long terme afin d’enrichir les analyses par lots.
- **Machine Learning**, pour la détection d’anomalies en temps réel et le traitement par lots afin d’obtenir des informations prédictives.
- **HDInsight** est utilisé pour transformer les données à grande échelle
- **Data Factory** gère l’orchestration, la planification, la gestion des ressources et la surveillance du pipeline de traitement par lots.
- **Power BI** offre à cette solution un tableau de bord complet permettant de visualiser à la fois les données en temps réel et les analyses prédictives.

Cette solution utilise deux **sources de données** différentes :

- **Simulation des signaux et diagnostics d’un véhicule** : un simulateur télématique de véhicule émet des informations de diagnostic et des signaux correspondant à l’état du véhicule et au schéma de conduite à un moment donné. 
- **Catalogue de véhicules** : jeu de données de référence associé à un mappage VIN/modèle.

<!---HONumber=AcomDC_0406_2016-->