<properties
 pageTitle="Solution préconfigurée de maintenance prédictive | Microsoft Azure"
 description="Description de la solution préconfigurée de maintenance prédictive Azure IoT."
 services=""
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="na"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="12/01/2015"
 ms.author="stevehob"/>

# Présentation de la solution préconfigurée de maintenance prédictive

La solution préconfigurée de *maintenance prédictive* est une des [solutions préconfigurées][lnk_preconfigured_solutions] incluses dans [Microsoft Azure IoT Suite][lnk_iot_suite]. Cette solution intègre une collecte télémétrique en temps réel des données de l’appareil grâce à un modèle prédictif créé avec [Azure Machine Learning][lnk_machine_learning].


Avec Azure IoT Suite, les entreprises peuvent rapidement et facilement se connecter, surveiller les ressources et analyser les données en temps réel. La solution préconfigurée de gestion prédictive prend les données et utilise des tableaux de bord complets ainsi que des visualisations pour fournir aux entreprises de nouvelles informations pertinentes leur permettant d’accroître leur efficacité et leurs flux de revenus.

## Le scénario

Fabrikam est une compagnie aérienne régionale qui s’efforce d’offrir à ses clients une expérience optimale et à des prix compétitifs. Une des causes de retard des vols est liée à des problèmes de gestion, et la maintenance des moteurs d'avion est particulièrement complexe. Une panne de moteur en plein vol devant être évitée à tout prix, Fabrikam inspecte ses moteurs régulièrement et suit un programme de maintenance planifiée. Mais les moteurs d’avion ne vieillissent pas tous de la même manière. Et certaines opérations de maintenance inutiles sont effectuées sur les moteurs. Plus important encore, les problèmes clouent les appareils au sol jusqu'à ce que l'opération de maintenance soit terminée. Cela entraîne des retards coûteux, en particulier si un appareil est immobilisé sur un site qui ne dispose pas des techniciens qualifiés ou des pièces de rechange nécessaires.

Les appareils de Fabrikam sont équipés de capteurs qui analysent les paramètres du moteur pendant le vol. Fabrikam utilise Azure IoT Suite pour collecter les données de capteurs recueillies à cette occasion. Après de longues années passées à analyser les données liées au fonctionnement et aux pannes des moteurs, les spécialistes de Fabrikam ont modélisé une solution capable de prédire la durée de vie restante ou RUL (Remaining Useful Life) d’un moteur d'avion. Ils ont identifié une corrélation entre les données de quatre des capteurs moteur et l'usure du moteur conduisant à une panne. Tandis que Fabrikam continue à effectuer des inspections régulières pour garantir la sécurité, l’entreprise peut maintenant utiliser les modèles pour calculer la RUL de chaque moteur après que chaque vol à l'aide des données télémétriques collectées par les moteurs pendant le vol. Fabrikam peut désormais anticiper les futurs points de défaillance, la planification de la maintenance et les réparations nécessaires.

En prédisant le moment où une maintenance est requise, Fabrikam peut optimiser ses opérations et réduire ainsi ses coûts. Les coordinateurs de maintenance collaborent avec les planificateurs afin de prévoir une maintenance lorsqu’un appareil est au sol sur un site en garantissant un délai suffisant pour la mise hors service de l'avion ne perturbe pas la planification. Fabrikam peut ainsi planifier le travail des techniciens en veillant à ce que les appareils soient inspectés et réparés sans délai. Les responsables du contrôle des stocks reçoivent des plans de maintenance qui les aident à optimiser le processus de commande et le stock de pièces de rechange. Tout cela permet à Fabrikam de minimiser le temps d’immobilisation des appareils et à réduire les coûts d'exploitation tout en garantissant la sécurité des passagers et de l’équipage.

## Comment la solution de gestion prédictive est générée

Pour comprendre comment les fonctionnalités d’[Azure IoT Suite][lnk_iot_suite] permettent aux clients d’exploiter tout le potentiel de la maintenance prédictive, consultez cette [infographie][lnk_infographic].

Pour afficher ces fonctionnalités en s’appuyant sur les données télémétriques recueillies via les services IoT Suite et pour les présenter aux utilisateurs dans une application web, la solution s'appuie sur un modèle Microsoft Azure Machine Learning existant disponible en tant que modèle. Microsoft a créé un [modèle de régression][lnk_regression_model] et publié le modèle complet, les données<sup>[1]</sup> et des instructions détaillées.

La solution préconfigurée de gestion prédictive Azure IoT utilise le modèle de régression créé à partir de ce modèle ; il est déployé dans votre abonnement Azure et présenté via une API publiée. La solution inclut un sous-ensemble des données de tests représentant 4 (sur un total 100) moteurs et 4 (sur un total 21) flux de données de capteurs, pour fournir un résultat exact du modèle formé.

*[1] A. Saxena and K. Goebel (2008). "Turbofan Engine Degradation Simulation Data Set", NASA Ames Prognostics Data Repository (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), NASA Ames Research Center, Moffett Field, CA*

## Étapes suivantes

Pour en savoir plus sur la gestion de scénarios de gestion prédictive avec Azure IoT, consultez [Saisir la valeur de l'Internet des objets][lnk_capture_value].

[Examinez pas à pas][lnk-predictive-walkthrough] la solution préconfigurée de maintenance prédictive.

[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-overview.md
[lnk_machine_learning]: https://azure.microsoft.com/services/machine-learning/
[lnk_infographic]: https://www.microsoft.com/fr-FR/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3
[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF

<!---HONumber=AcomDC_1217_2015-->