<properties 
	pageTitle="Manuel de solution d’analyse télémétrique de véhicule | Microsoft Azure" 
	description="Utilisez les fonctions de Cortana Analytics pour obtenir des informations en temps réel et prédictives sur l’état des véhicules et les habitudes de conduite." 
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
	ms.date="12/02/2015" 
	ms.author="bradsev" />


# Manuel de solution d’analyse télémétrique de véhicule

Ce **menu** contient des liens vers les chapitres de ce manuel.

[AZURE.INCLUDE [cap-vehicle-telemetry-playbook-selector](../../includes/cap-vehicle-telemetry-playbook-selector.md)]

## Vue d'ensemble
Nous avons transféré nos superordinateurs du laboratoire à notre garage ! Ces automobiles de pointe contiennent une multitude de capteurs capables de suivre et de surveiller des millions d'événements chaque seconde. Nous pensons que d’ici 2020, la plupart de ces voitures seront connectées à Internet. Imaginez le potentiel qu’offrent toutes ces données pour améliorer la sécurité, la fiabilité et le plaisir de la conduite. Microsoft a réalisé ce rêve en développant Cortana Analytics.

La technologie Cortana Analytics de Microsoft est une suite avancée d’analyse de Big Data qui vous permet de transformer vos données en action intelligente. Nous souhaitons vous présenter le modèle de solution d’analyse télémétrique de véhicule Cortana Analytics. Cette solution montre comment les concessions, les constructeurs automobiles et les compagnies d'assurance peuvent utiliser les fonctions de Cortana Analytics pour obtenir des informations en temps réel et prédictives sur l’état des véhicules et les habitudes de conduite.

La solution est mise en œuvre comme un [modèle d'architecture lambda](https://en.wikipedia.org/wiki/Lambda_architecture) montrant tout le potentiel de la plateforme Cortana Analytics pour le traitement en temps réel et par lots. Il inclut un simulateur de télématique de véhicule, exploite des concentrateurs d'événements pour la réception de millions d'événements télémétriques de véhicule simulés dans Azure, puis utilise Stream Analytics pour obtenir des informations en temps réel sur la santé du véhicule, et conserve ces données dans un stockage à long terme à des fins d'analyse par lots plus approfondie. Il s’appuie sur Machine Learning pour la détection d’anomalies en temps réel et le traitement par lots afin d’obtenir des informations prédictives. HDInsight transforme les données à l’échelle, tandis que Data Factory gère l’orchestration, la planification, la gestion des ressources et la surveillance du pipeline de traitement par lots. Enfin, Power BI offre à cette solution un tableau de bord complet permettant de visualiser à la fois les données en temps réel et les analyses prédictives.

## Architecture

![](./media/cortana-analytics-playbook-vehicle-telemetry/fig1-vehicle-telemetry-annalytics-solution-architecture.png) *Figure 1 – Architecture de la solution d’analyse télémétrique de véhicule*

Cette solution inclut les **composants Cortana Analytics** suivants et présente leur intégration de bout en bout


- **EVENT HUBS**, pour la réception dans Azure de millions d’événements de télémétrie associés aux véhicules.
- **STREAM ANALYTICS**, pour une visibilité en temps réel sur l’état des véhicules et une conservation de ces données dans un stockage à long terme afin d’enrichir les analyses par lots.
- **MACHINE LEARNING**, pour la détection d’anomalies en temps réel et le traitement par lots afin d’obtenir des informations prédictives.
- **HDINSIGHT** est utilisé pour transformer les données à grande échelle
- **DATA FACTORY** gère l’orchestration, la planification, la gestion des ressources et la surveillance du pipeline de traitement par lots.
- **POWER BI** offre à cette solution un tableau de bord complet permettant de visualiser à la fois les données en temps réel et les analyses prédictives.

Cette solution utilise deux **sources de données** différentes :

- **SIMULATION DES SIGNAUX ET DIAGNOSTICS D’UN VÉHICULE** : un simulateur télématique de véhicule émet des informations de diagnostic et des signaux correspondant à l’état du véhicule et au schéma de conduite à un moment donné dans le temps. 
- **CATALOGUE DE VÉHICULES** : un jeu de données de référence associé à un mappage VIN/modèle.

<!---HONumber=AcomDC_1203_2015-->