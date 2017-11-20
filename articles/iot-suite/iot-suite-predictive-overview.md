---
title: "Présentation de la solution de maintenance prédictive - Azure | Microsoft Docs"
description: "Description de la solution préconfigurée de maintenance prédictive Azure IoT Suite."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: b370b3d7-2ce5-4906-9818-3aeedd471ee3
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/14/2017
ms.author: dobett
ms.openlocfilehash: 36cae39b7eaa0aff5f47f6a2511c7a0593f70b26
ms.sourcegitcommit: 732e5df390dea94c363fc99b9d781e64cb75e220
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/14/2017
---
# <a name="predictive-maintenance-preconfigured-solution-overview"></a>Présentation de la solution préconfigurée de maintenance prédictive

La *solution préconfigurée* de [maintenance prédictive][lnk_preconfigured_solutions] est l’une des solutions préconfigurées incluses dans [Microsoft Azure IoT Suite][lnk_iot_suite]. Cette solution intègre la collecte de données télémétriques en temps réel avec un modèle prédictif créé avec [Azure Machine Learning][lnk-machine-learning].

Avec Azure IoT Suite, vous pouvez rapidement et facilement accéder aux ressources, les surveiller et analyser des données de télémétrie en temps réels dans des tableaux de bords et des visualisations. Dans cette solution de maintenance prédictive, les tableaux de bord ainsi que les visualisations vous fournissent de nouvelles informations pertinentes vous permettant d’accroître votre efficacité et vos flux de revenus.

## <a name="the-scenario"></a>Le scénario

Fabrikam est une compagnie aérienne régionale qui s’efforce d’offrir à ses clients une expérience optimale et à des prix compétitifs. Une des causes de retard des vols est liée à des problèmes de gestion, et la maintenance des moteurs d'avion est particulièrement complexe. Une panne de moteur en plein vol devant être évitée à tout prix, Fabrikam inspecte ses moteurs régulièrement et suit un programme de maintenance planifiée. Mais les moteurs d’avion ne vieillissent pas tous de la même manière. Et certaines opérations de maintenance inutiles sont effectuées sur les moteurs. Plus important encore, les problèmes clouent les appareils au sol jusqu'à ce que l'opération de maintenance soit terminée. Si un appareil est immobilisé sur un site qui ne dispose pas des techniciens qualifiés ni des pièces de rechange nécessaires, ces problèmes peuvent s’avérer particulièrement coûteux.

Les appareils de Fabrikam sont équipés de capteurs qui analysent les paramètres du moteur pendant le vol. Fabrikam utilise la solution de maintenance prédictive pour récupérer les données des capteurs collectées pendant le vol. Après de longues années passées à analyser les données liées au fonctionnement et aux pannes des moteurs, les spécialistes de Fabrikam ont modélisé une solution capable de prédire la durée de vie restante ou RUL (Remaining Useful Life) d’un moteur d'avion. Le modèle se sert d’une corrélation entre les données de quatre des capteurs moteur et l'usure du moteur conduisant à une panne. Tandis que Fabrikam continue à effectuer des inspections régulières pour garantir la sécurité, l’entreprise peut maintenant utiliser les modèles pour calculer la durée de vie utile restante de chaque moteur après chaque vol. Le modèle utilise les données télémétriques collectées par les moteurs pendant le vol. Fabrikam peut désormais anticiper les futurs points de défaillance, la planification de la maintenance et les réparations nécessaires.

> [!NOTE]
> Le modèle de solution utilise les données réelles d’usure du moteur.

En prédisant le moment où une maintenance est requise, Fabrikam peut optimiser ses opérations et réduire ainsi ses coûts.

Les coordinateurs de maintenance collaborent avec les planificateurs pour :

- Prévoir une maintenance lorsqu’un appareil est au sol sur un site donné.
- Garantir un délai suffisant pour que la mise hors service de l’avion ne perturbe pas la planification.
- pour planifier le travail des techniciens en veillant à ce que les appareils sont inspectés et réparés sans délai.

Les responsables du contrôle des stocks reçoivent des plans de maintenance qui les aident à optimiser le processus de commande et le stock de pièces de rechange.

Ces activités permettent à Fabrikam de minimiser le temps d’immobilisation des appareils et de réduire les coûts d’exploitation tout en garantissant la sécurité des passagers et de l’équipage.

Pour comprendre comment les fonctionnalités [d’Azure IoT Suite][lnk_iot_suite] permettent aux clients d’exploiter tout le potentiel de la maintenance prédictive, reportez-vous à cette [infographie][lnk_infographic].

## <a name="how-the-predictive-maintenance-solution-is-built"></a>Comment la solution de gestion prédictive est générée

La solution utilise un modèle Microsoft Azure Machine Learning existant pour afficher ces fonctionnalités en s’appuyant sur les données télémétriques de l’appareil recueillies via les services IoT Suite. Microsoft a créé un [modèle de régression][lnk_regression_model] d’un moteur d’avion basé sur un modèle complet disponible publiquement<sup>\[1\]</sup> et des instructions d’utilisation détaillées.

La solution de maintenance prédictive Azure IoT utilise le modèle de régression créé à partir de ce modèle. Le modèle est déployé dans votre abonnement Azure et exposé via une API générée automatiquement. La solution inclut un sous-ensemble des données de tests représentant 4 (sur un total 100) moteurs et 4 (sur un total 21) flux de données de capteurs. Ces données sont suffisantes pour fournir un résultat exact du modèle formé.

*\[1\] A. Saxena and K. Goebel (2008). « Turbofan Engine Degradation Simulation Data Set », NASA Ames Prognostics Data Repository (http://ti.arc.nasa.gov/tech/dash/pcoe/prognostic-data-repository/), NASA Ames Research Center, Moffett Field, CA*

## <a name="get-started-with-predictive-maintenance"></a>Prise en main de la maintenance prédictive

Ce didacticiel vous montre comment configurer la solution de maintenance prédictive. Il présente également les fonctionnalités de base de la solution de maintenance prédictive. Vous pouvez accéder à la plupart de ces fonctionnalités via le tableau de bord de solution déployé avec la solution préconfigurée.

Pour suivre ce didacticiel, vous avez besoin d’un compte Azure actif.

> [!NOTE]
> Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la rubrique [Version d’évaluation gratuite d’Azure][lnk_free_trial].

1. Connectez-vous à [azureiotsuite.com][lnk-azureiotsuite] à l’aide des informations d’identification de votre compte Azure, puis cliquez sur **+** pour créer une solution.
1. Cliquez sur **Sélectionner** et choisissez la vignette **Maintenance prédictive**.
1. Entrez un **Nom de solution** pour votre solution préconfigurée de maintenance prédictive.
1. Sélectionnez la **région** et **l’abonnement** à utiliser pour configurer la solution.
1. Cliquez sur **Créer une solution** pour commencer le processus de déploiement. L’exécution de ce processus prend généralement plusieurs minutes.

### <a name="wait-for-the-provisioning-process-to-complete"></a>Attendre la fin du processus d’approvisionnement

1. Cliquez sur la vignette de votre solution présentant l’état **Approvisionnement** .
1. Notez les **états d’approvisionnement** à mesure que les services Azure sont déployés dans votre abonnement Azure.
1. Une fois l’approvisionnement terminé, l’état prend la valeur **Prêt**.
1. Cliquez sur la vignette pour visualiser les détails de votre solution dans le volet droit. Dans ce volet, vous pouvez démarrer le tableau de bord de la solution et accéder à l’espace de travail Machine Learning.

> [!NOTE]
> Si vous rencontrez des problèmes lors du déploiement de la solution préconfigurée, consultez les articles [Autorisations sur le site azureiotsuite.com][lnk-permissions] et [Forum Aux Questions][lnk-faq]. Si les problèmes persistent, créez un ticket de service sur le [Portail][lnk-portal].

Certains détails de votre solution semblent-ils faire défaut ? Soumettez vos suggestions concernant les fonctionnalités sur [UserVoice](https://feedback.azure.com/forums/321918-azure-iot).

## <a name="view-the-solution"></a>Afficher la solution

Cette section vous guide à travers l’utilisation de l’interface utilisateur de la solution.

### <a name="predictive-maintenance-dashboard"></a>Tableau de bord de maintenance prédictive

Cette page de l’application web utilise des contrôles Power BI JavaScript (consultez [Référentiel d’éléments visuels Power BI][lnk-powerbi]) pour visualiser :

* Les données de sortie des tâches Stream Analytics dans le Blob Storage.
* La durée de vie utile restante et le nombre de cycles d’un moteur d’avion.

### <a name="observing-the-behavior-of-the-cloud-solution"></a>Observer le comportement de la solution cloud

Dans le portail Azure, accédez au groupe de ressources portant le nom de solution que vous avez choisi, pour afficher vos ressources configurées.

![][img-resource-group]

Lorsque vous approvisionnez la solution préconfigurée, vous recevez un e-mail contenant un lien vers l’espace de travail Machine Learning. Vous pouvez également accéder à l’espace de travail Machine Learning à partir de la page [azureiotsuite.com][lnk-azureiotsuite] de votre solution configurée. Une vignette est disponible sur cette page lorsque le statut de la solution est **Prêt**.

![][img-machine-learning]

Dans le portail de la solution, vous pouvez voir que l’exemple est configuré avec quatre appareils simulés, correspondant à deux avions avec deux moteurs chacun et quatre capteurs par moteur. Lorsque vous accédez au portail de la solution pour la première fois, la simulation est arrêtée.

![][img-simulation-stopped]

Cliquez sur **Démarrer la simulation** pour commencer la simulation. L’historique, les cycles et la durée de vie utile restante du capteur, ainsi que l’historique de durée de vie restante sont renseignés dans le tableau de bord.

![][img-simulation-running]

Lorsque la durée de vie utile restante est inférieure à 160 (seuil arbitraire choisi pour les besoins de la démonstration), le portail de la solution affiche un symbole d’avertissement en regard de la valeur correspondante. Le portail de la solution colore également le moteur d’avion en jaune. Vous remarquerez que les valeurs de durée de vie utile restante tendent globalement à diminuer, mais avec des rebonds à la hausse ou à la baisse. Ce phénomène provient de la variabilité de la durée des cycles et de la précision du modèle.

![][img-simulation-warning]

La simulation complète prend environ 35 minutes pour effectuer 148 cycles. Le seuil de durée de vie utile restante de 160 est atteint pour la première fois à environ 5 minutes, et les deux moteurs atteignent le seuil à environ 8 minutes.

La simulation s’exécute sur le jeu de données complet pour les 148 cycles et se règle sur les valeurs finales de durée de vie utile restante et de cycles.

Vous pouvez arrêter la simulation à tout moment. L’option **Démarrer la simulation** réexécute la simulation à partir du début du jeu de données.

## <a name="next-steps"></a>Étapes suivantes

Pour en savoir plus sur la gestion de scénarios de gestion prédictive avec Azure IoT, consultez [Saisir la valeur de l'Internet des objets][lnk_capture_value].

[Examinez pas à pas][lnk-predictive-walkthrough] la solution de maintenance prédictive.

Vous pouvez également explorer certaines des autres fonctionnalités et capacités des solutions préconfigurées IoT Suite :

* [Forum Aux Questions (FAQ) relatives à IoT Suite][lnk-faq]
* [Sécurisation de l’Internet des objets de bout en bout][lnk-security-groundup]

[img-resource-group]: media/iot-suite-predictive-overview/resource-group.png
[img-simulation-stopped]: media/iot-suite-predictive-overview/simulation-stopped.png
[img-simulation-running]: media/iot-suite-predictive-overview/simulation-running.png
[img-simulation-warning]: media/iot-suite-predictive-overview/simulation-warning.png
[img-machine-learning]: media/iot-suite-predictive-overview/machine-learning.png

[lnk-powerbi]: https://www.github.com/Microsoft/PowerBI-visuals
[lnk-predictive-walkthrough]: iot-suite-predictive-walkthrough.md
[lnk_preconfigured_solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk_iot_suite]: iot-suite-options.md
[lnk_infographic]: https://www.microsoft.com/server-cloud/predictivemaintenance/Index.html
[lnk_regression_model]: http://gallery.cortanaanalytics.com/Collection/Predictive-Maintenance-Template-3

[lnk_capture_value]: http://download.microsoft.com/download/0/7/D/07D394CE-185D-4B96-AC3C-9B61179F7080/Capture_value_from_the_Internet%20of%20Things_with_Predictive_Maintenance.PDF
[lnk-faq]: iot-suite-v1-faq.md
[lnk-security-groundup]: securing-iot-ground-up.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-azureiotsuite]: https://www.azureiotsuite.com
[lnk-permissions]: iot-suite-v1-permissions.md
[lnk-portal]: http://portal.azure.com/
[lnk-machine-learning]: https://azure.microsoft.com/services/machine-learning/