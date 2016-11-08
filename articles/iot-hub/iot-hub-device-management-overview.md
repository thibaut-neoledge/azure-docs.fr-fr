---
title: Vue d’ensemble de la gestion des appareils | Microsoft Docs
description: Vue d’ensemble de la gestion des appareils Azure IoT Hub
services: iot-hub
documentationcenter: ''
author: bzurcher
manager: timlt
editor: ''

ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 09/16/2016
ms.author: bzurcher

---
# Vue d’ensemble de la gestion des appareils Azure IoT Hub (version préliminaire)
## Approche d’Azure en matière de gestion des appareils IoT
La gestion des appareils Azure IoT Hub fournit aux appareils et aux serveurs principaux les fonctionnalités et le modèle d’extensibilité leur permettant de tirer parti de la gestion des appareils IoT pour le large éventail d’appareils et de protocoles de l’IoT. Les appareils de l’IoT vont de capteurs très limités à des passerelles plus puissantes qui assurent la prise en charge d’autres appareils et protocoles, en passant par des microcontrôleurs à fonction unique. Les solutions IoT varient aussi considérablement en termes d’applications et de domaines verticaux, avec des cas d’utilisation uniques pour les opérateurs de chaque domaine. Les solutions IoT peuvent exploiter les solutions peuvent tirer parti des fonctionnalités, des modèles et des bibliothèques de code de la gestion des appareils IoT Hub pour permettre la gestion des appareils pour un ensemble hétéroclite d’appareils et d’utilisateurs.

## Introduction
Une composante essentielle de la création d’une solution IoT efficace consiste à fournir une stratégie portant sur la manière dont les opérateurs assurent la gestion continue de leur parc d’appareils. Les opérateurs IoT ont besoin d’outils et d’applications à la fois simples et fiables, et qui leur permettent de se concentrer sur les aspects plus stratégiques de leur travail. Azure IoT Hub vous fournit les composantes de base requises pour créer des applications IoT qui facilitent la mise en œuvre des modèles de gestion d’appareils les plus importants.

Les appareils sont considérés comme gérés par IoT Hub lorsqu’ils exécutent une application simple, appelée agent de gestion des appareils, qui les connecte en toute sécurité au cloud. Le code d’agent permet à un opérateur côté application de vérifier l’état des appareils et d’effectuer des opérations de gestion telles que l’application de modifications de configuration réseau ou le déploiement de mises à jour de microprogramme, et ce, à distance.

## Principes de gestion des appareils IoT
L’IoT implique un ensemble unique de problèmes de gestion ; une solution doit tenir compte des principes suivants de gestion des appareils IoT :

![][img-dm_principles]

* **Mise à l’échelle et automatisation** : l’IoT nécessite des outils simples à même d’automatiser les tâches de routine et de permettre à un personnel opérationnel relativement réduit de gérer des millions d’appareils. Au quotidien, les opérateurs doivent pouvoir gérer les opérations d’appareils à distance, en bloc et être avertis uniquement lorsque les problèmes qui surviennent nécessitent leur attention directe.
* **Transparence et compatibilité** : l’écosystème d’appareils IoT est incroyablement varié. Les outils de gestion doivent être adaptés pour prendre en charge une multitude de classes, de plateformes et de protocoles d’appareils. Les opérateurs doivent pouvoir prendre en charge tous les appareils, depuis les puces intégrées limitées à processus unique jusqu’aux ordinateurs puissants et entièrement fonctionnels.
* **Prise en compte du contexte** : les environnements IoT sont dynamiques et en perpétuelle évolution. La fiabilité du service est primordiale. Les opérations de gestion des appareils doivent tenir compte des fenêtres de maintenance des SLA, de l’état du réseau et de l’alimentation, des conditions d’utilisation et de la géolocalisation des appareils pour garantir que les temps d’arrêt liés à la maintenance n’affectent pas les opérations d’exploitation critiques ou n’engendrent pas de risques.
* **Prise en charge de nombreux rôles** : la prise en charge des flux de travail et des processus uniques des rôles opérationnels liés à l’IoT est cruciale. Le personnel opérationnel doit également travailler en harmonie avec les contraintes spécifiques des départements IT internes et faire remonter les informations pertinentes sur les opérations des appareils aux responsables et aux autres rôles de gestion.

## Cycle de vie des appareils IoT
Bien que les projets IoT diffèrent considérablement, il existe un ensemble de modèles communs pour la gestion des appareils. Dans Azure IoT, ces modèles sont identifiés au sein du cycle de vie des appareils IoT, qui se compose de cinq phases distinctes :

![][img-device_lifecycle]

1. **Planification** : permettre aux opérateurs de créer un schéma de propriétés d’appareils afin qu’ils puissent facilement et précisément rechercher et cibler un groupe d’appareils pour les opérations de gestion en bloc.
   
    *Composantes de base associées* : [Prise en main des représentations d’appareils][lnk-twins-getstarted], [Utilisation des propriétés des représentations][lnk-twin-properties]
2. **Approvisionnement** : authentifier les nouveaux appareils en toute sécurité auprès d’IoT Hub et permettre aux opérateurs de détecter immédiatement les fonctionnalités et l’état actuel des appareils.
   
    *Composantes de base associées* : [Prise en main d’IoT Hub][lnk-hub-getstarted], [Utilisation des propriétés des représentations][lnk-twin-properties]
3. **Configuration** : rendre possibles les modifications de configuration et les mises à jour de microprogramme en bloc sur les appareils, tout en assurant intégrité et sécurité.
   
    *Composantes de base associées* : [Utilisation des propriétés des représentations][lnk-twin-properties], [Méthodes cloud-à-appareil][lnk-c2d-methods], [Planifier et diffuser des travaux][lnk-jobs]
4. **Surveillance** : surveiller l’intégrité globale du parc d’appareils et l’état des déploiements de mises à jour en cours pour avertir les opérateurs des problèmes susceptibles de nécessiter leur attention.
   
    *Composantes de base associées* : [Utilisation des propriétés des représentations][lnk-twin-properties]
5. **Mise hors service** : remplacer ou retirer des appareils après une défaillance, un cycle de mise à niveau ou à la fin de leur durée de vie.
   
    *Composantes de base associées* :

## Modèle de gestion des appareils IoT Hub
IoT Hub permet de mettre en œuvre l’ensemble suivant de modèles (initiaux) de gestion des appareils. Comme illustré dans les [didacticiels][lnk-get-started], vous pouvez étendre ces modèles pour les adapter à votre scénario précis et concevoir de nouveaux modèles pour les autres scénarios en partant de ces modèles de base.

1. **Redémarrage** : l’application principale informe l’appareil via une méthode appareil-à-cloud qu’un redémarrage a été lancé. L’appareil utilise les propriétés de représentation d’appareil signalées pour mettre à jour l’état de redémarrage de l’appareil.
   
    ![][img-reboot_pattern]
2. **Réinitialisation aux paramètres d’usine** : l’application principale informe l’appareil via une méthode appareil-à-cloud qu’une réinitialisation aux paramètres d’usine a été lancée. L’appareil utilise les propriétés de représentation d’appareil signalées pour mettre à jour l’état de réinitialisation aux paramètres d’usine de l’appareil.
   
    ![][img-facreset_pattern]
3. **Configuration** : l’application principale utilise les propriétés de représentation d’appareil souhaitées pour configurer le logiciel en cours d’exécution sur l’appareil. L’appareil utilise les propriétés de représentation d’appareil signalées pour mettre à jour l’état de configuration de l’appareil.
   
    ![][img-config_pattern]
4. **Mise à jour du microprogramme** : l’application principale informe l’appareil via une méthode appareil-à-cloud qu’une mise à jour du microprogramme a été lancée. L’appareil met en œuvre un processus en plusieurs étapes pour télécharger le package du microprogramme, appliquer ce package et enfin se reconnecter au service IoT Hub. Tout au long de ce processus, l’appareil utilise les propriétés de représentation d’appareil signalées pour mettre à jour la progression et l’état de l’appareil.
   
    ![][img-fwupdate_pattern]
5. **Signalement de la progression et de l’état** : l’application principale exécute des requêtes sur les représentations d’appareils sur un ensemble d’appareils afin de rendre compte de l’état et de la progression des actions en cours d’exécution sur l’appareil.
   
    ![][img-report_progress_pattern]

## Étapes suivantes
À l’aide des composantes de base fournies par Azure IoT Hub, les développeurs peuvent créer des applications IoT satisfaisant les exigences uniques des opérateurs IoT à chaque phase du cycle de vie des appareils.

Pour plus d’informations sur les fonctionnalités de gestion des appareils Azure IoT Hub, consultez le didacticiel [Prise en main de la gestion des appareils Azure IoT Hub][lnk-get-started].

<!-- Images and links -->
[img-dm_principles]: media/iot-hub-device-management-overview/image4.png
[img-device_lifecycle]: media/iot-hub-device-management-overview/image5.png
[img-config_pattern]: media/iot-hub-device-management-overview/configuration-pattern.png
[img-facreset_pattern]: media/iot-hub-device-management-overview/facreset-pattern.png
[img-fwupdate_pattern]: media/iot-hub-device-management-overview/fwupdate-pattern.png
[img-reboot_pattern]: media/iot-hub-device-management-overview/reboot-pattern.png
[img-report_progress_pattern]: media/iot-hub-device-management-overview/report-progress-pattern.png

[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-twins-getstarted]: iot-hub-node-node-twin-getstarted.md
[lnk-twin-properties]: iot-hub-node-node-twin-how-to-configure.md
[lnk-hub-getstarted]: iot-hub-csharp-csharp-getstarted.md
[lnk-c2d-methods]: iot-hub-c2d-methods.md
[lnk-jobs]: iot-hub-schedule-jobs.md

<!---HONumber=AcomDC_1005_2016-->