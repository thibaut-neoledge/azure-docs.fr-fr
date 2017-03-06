---
title: "Solutions préconfigurées IoT Azure | Microsoft Docs"
description: "Description des solutions préconfigurées IoT Azure et de leur architecture avec des liens vers des ressources supplémentaires."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 59009f37-9ba0-4e17-a189-7ea354a858a2
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/15/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: a3657f8bb60cd1181740b0700f503b5bd1bd559f
ms.openlocfilehash: a3847f83af1f28e40572af95ff31f44d2f3d6dc4
ms.lasthandoff: 02/27/2017


---
# <a name="what-are-the-azure-iot-suite-preconfigured-solutions"></a>Que sont les solutions préconfigurées Azure IoT Suite ?
Les solutions Azure IoT Suite préconfigurées sont des implémentations de modèles de solution IoT courants que vous pouvez déployer sur Azure en utilisant votre abonnement. Vous pouvez utiliser les solutions préconfigurées :

* Comme point de départ de vos propres solutions IoT.
* Pour en savoir plus sur les modèles courants en matière de développement et de conception d’une solution IoT.

Chaque solution préconfigurée est une implémentation complète de bout en bout qui utilise les appareils simulés pour générer la télémétrie.

En plus de déployer et d’exécuter les solutions dans Azure, vous pouvez télécharger le code source complet, puis personnaliser et étendre la solution pour répondre à vos besoins IoT spécifiques.

> [!NOTE]
> Pour déployer l’une des solutions préconfigurées, visitez [Microsoft Azure IoT Suite][lnk-azureiotsuite]. L’article [Prise en main des solutions préconfigurées IoT][lnk-getstarted-preconfigured] fournit plus d’informations pour vous expliquer comment déployer et exécuter l’une des solutions.
> 
> 

Le tableau suivant montre le mappage entre les solutions et des fonctionnalités IoT spécifiques :

| Solution | Ingestion de données | Identité d’appareil | Gestion des appareils | Commande et contrôle | Règles et actions | Analyse prédictive |
| --- | --- | --- | --- | --- | --- | --- |
| [Surveillance à distance][lnk-getstarted-preconfigured] |Oui |Oui |Oui |Oui |Oui |- |
| [Maintenance prédictive][lnk-predictive-maintenance] |Oui |Oui |- |Oui |Oui |yes |

* *Données*: entrer des données à l'échelle dans le cloud.
* *Identité de l’appareil* : gérez les identités d’appareil uniques et contrôlez l’accès de l’appareil à la solution.
* *Gestion des appareils* : gérez les métadonnées de l’appareil et effectuez des opérations comme le redémarrage d’appareils et la mise à jour du microprogramme.
* *Commande et contrôle*: envoyer des messages à un appareil à partir du cloud pour lui demander de prendre une mesure.
* *Règles et actions*: le serveur principal utilise des règles pour agir sur des données d’appareil spécifiques vers le cloud.
* *Analyse prédictive*: le serveur principal analyse les données d’appareil vers le cloud afin de prédire le moment où des actions spécifiques devraient se produire. Par exemple, l’analyse de la télémétrie d’un moteur d'avion permet de déterminer le moment où la maintenance doit avoir lieu.

## <a name="remote-monitoring-preconfigured-solution-overview"></a>Présentation de la solution préconfigurée de surveillance à distance
Nous avons choisi d’aborder dans cet article la solution préconfigurée de surveillance à distance, car elle illustre de nombreux éléments de conception communs que partagent les autres solutions.

Le schéma suivant illustre les éléments clés de la solution de surveillance à distance. Les sections qui suivent fournissent des informations supplémentaires sur ces éléments.

![Architecture de la solution préconfigurée Surveillance à distance][img-remote-monitoring-arch]

## <a name="devices"></a>Appareils
Lorsque vous déployez la solution préconfigurée de surveillance à distance, quatre appareils sont préconfigurés dans la solution pour simuler un appareil de refroidissement. Ces appareils de simulation intègrent un modèle de température et d’humidité qui génère des données de télémétrie. Ces appareils simulés sont inclus pour :
- Illustrer le trajet de bout en bout des données à travers la solution.
- Fournir une source de données de télémétrie pratique.
- Fournir une cible pour les méthodes ou commandes si vous êtes un développeur de système principal en utilisant la solution comme point de départ pour une implémentation personnalisée.

Les appareils simulés dans la solution peuvent répondre aux communications cloud-à-appareil suivantes :

- *Méthodes ([méthodes directes][lnk-direct-methods])* : une méthode de communication bidirectionnelle pour laquelle un appareil connecté doit répondre immédiatement.
- *Commandes (messages cloud-à-appareil)* : une méthode de communication unidirectionnelle pour laquelle un appareil récupère la commande à partir d’une file d’attente durable.

Pour une comparaison de ces différentes approches, consultez [Conseils pour les communications cloud-à-appareil][lnk-c2d-guidance].

Lorsqu’un appareil se connecte pour la première fois à IoT Hub dans la solution préconfigurée, il envoie un message d’informations sur l’appareil envoyé au hub qui énumère les méthodes auxquelles l’appareil peut répondre. Dans la solution préconfigurée de surveillance à distance, les appareils simulés prennent en charge les méthodes suivantes :

* *Lancer la mise à jour du microprogramme* : cette méthode lance une tâche asynchrone sur l’appareil pour effectuer une mise à jour du microprogramme. La tâche asynchrone utilise les propriétés signalées pour fournir des mises à jour d’état d’application sur le tableau de bord de la solution.
* *Redémarrer* : cette méthode provoque le redémarrage de l’appareil simulé.
* *FactoryReset* : cette méthode déclenche une réinitialisation aux paramètres d’usine sur l’appareil simulé.

Lorsqu’un appareil se connecte pour la première fois à IoT Hub dans la solution préconfigurée, il envoie un message d’informations sur l’appareil envoyé au hub qui énumère les commandes auxquelles l’appareil peut répondre. Dans la solution préconfigurée de surveillance à distance, les appareils simulés prennent en charge les commandes suivantes :

* *Effectuer un test Ping*: l’appareil répond à cette commande par un accusé de réception. Cette commande est utile pour vérifier que l’appareil est toujours actif et en mode écoute.
* *Démarrer la télémétrie*: demande à l’appareil de démarrer l’envoi des données de télémétrie.
* *Démarrer la télémétrie*: demande à l’appareil d’arrêter l’envoi des données de télémétrie.
* *Modifier la température nominale*: contrôle les valeurs de télémétrie reflétant la température simulée envoyées par l’appareil. Cette commande est utile dans le cadre de tests de logique Back-end.
* *Diagnostiquer la télémétrie*: détermine si l’appareil doit envoyer les données de température externe sous la forme de données de télémétrie.
* *Modifier l’état de l’appareil* : définit la propriété des métadonnées d’état de l’appareil renvoyée par l’appareil. Cette commande est utile dans le cadre de tests de logique Back-end.

Vous pouvez ajouter à la solution d’autres appareils de simulation qui génèrent les mêmes données de télémétrie et répondent aux mêmes méthodes et commandes.

En plus de répondre aux commandes et aux méthodes, la solution utilise les [représentations d’appareil][lnk-device-twin]. Les appareils utilisent les représentations pour renvoyer les valeurs de propriété au système principal de la solution. Le tableau de bord de la solution utilise les représentations d’appareil pour définir les nouvelles valeurs de propriété de votre choix sur les appareils. Par exemple, lors du processus de mise à jour du microprogramme, l’appareil simulé signale l’état de la mise à jour à l’aide des propriétés signalées.

## <a name="iot-hub"></a>IoT Hub
Dans cette solution préconfigurée, l’instance IoT Hub correspond à une *passerelle cloud* dans une [architecture de solution IoT][lnk-what-is-azure-iot] standard.

Un IoT Hub reçoit la télémétrie provenant des appareils à un seul système d’extrémité. Un IoT Hub gère également les points de terminaison spécifiques des appareils où chaque appareil peut récupérer les commandes qui lui sont envoyées.

L’IoT Hub met les données de télémétrie qu’il reçoit à disposition via un système d’extrémité de lecture de télémétrie côté service.

La fonctionnalité de gestion des appareils de IoT Hub vous permet de gérer les propriétés de votre appareil à partir du portail de la solution et de planifier des tâches qui effectuent diverses opérations dont les suivantes :

- Redémarrage des appareils
- Changement d’état d’appareil
- Mises à jour de microprogramme

## <a name="azure-stream-analytics"></a>Azure Stream Analytics
Cette solution préconfigurée utilise trois travaux [Azure Stream Analytics][lnk-asa] (ASA) pour filtrer le flux de télémétrie des appareils :

* *Tâche DeviceInfo* : envoie des données à un Event Hub qui achemine les messages relatifs à l’inscription de l’appareil dans le registre d’appareils de la solution (une base de données DocumentDB). Ce message est envoyé lorsqu’un appareil se connecte d’abord ou en réponse à une commande **Modifier l’état de l’appareil**.
* *Tâche Telemetry* : envoie toutes les données de télémétrie brutes vers Azure Blob Storage pour un stockage à froid et calcule les données de télémétrie agrégées qui s’affichent dans le tableau de bord de la solution.
* *Tâche Rules* : filtre le flux de télémétrie sur les valeurs qui dépassent les seuils de la règle et envoie les données vers un hub d’événements. Lorsqu’une règle se déclenche, la vue du tableau de bord du portail de la solution affiche cet événement sous la forme d’une nouvelle ligne dans la table d’historique des alarmes. Ces règles peuvent également déclencher une action basée sur les paramètres définis dans les vues **Règles** et **Actions** dans le portail de la solution.

Dans cette solution préconfigurée, les tâches ASA font partie du **serveur principal de solution IoT** dans une [architecture de solution IoT][lnk-what-is-azure-iot] standard.

## <a name="event-processor"></a>Processeur d’événements
Dans cette solution préconfigurée, le processeur d’événements fait partie du **serveur principal de solution IoT** dans une [architecture de solution IoT][lnk-what-is-azure-iot] standard.

Les tâches ASA **DeviceInfo** et **Rules** envoient leur sortie vers des Event hubs pour une transmission à d’autres services principaux. La solution utilise une instance [EventPocessorHost][lnk-event-processor], qui s’exécute dans une [tâche web][lnk-web-job], pour lire les messages à partir de ces Event hubs. L’événement **EventProcessorHost** utilise :
- Les données **DeviceInfo** pour mettre à jour les données de l’appareil dans la base de données DocumentDB.
- Les données de **Règles** pour appeler l’application logique et mettre à jour l’affichage des alertes dans le portail de la solution.

## <a name="device-identity-registry-device-twin-and-documentdb"></a>Registre d’identité des appareils, représentation d’appareil et DocumentDB
Chaque IoT hub inclut un [registre d’identité des appareils][lnk-identity-registry] qui stocke des clés d’appareils. IoT Hub utilise ces informations pour authentifier les appareils. Un appareil doit être enregistré et disposer d'une clé valide avant de se connecter au concentrateur.

Une [représentation d’appareil][lnk-device-twin] est un document JSON géré par Iot Hub. Une représentation d’appareil contient :

- Les propriétés signalées envoyées par l’appareil vers le hub. Vous pouvez afficher ces propriétés dans le portail de la solution.
- Les propriétés de votre choix que vous souhaitez envoyer à l’appareil. Vous pouvez définir ces propriétés dans le portail de la solution.
- Des balises qui existent uniquement dans la représentation d’appareil et non sur l’appareil. Vous pouvez utiliser ces balises pour filtrer les listes d’appareils dans le portail de la solution.

Cette solution utilise des représentations d’appareil pour gérer les métadonnées d’appareil. La solution utilise également une base de données DocumentDB pour stocker des données d’appareil supplémentaires propres à la solution comme les commandes prises en charge par chaque appareil et l’historique des commandes.

La solution doit également conserver les informations dans le registre d'identité des appareils, synchronisé avec le contenu de la base de données DocumentDB. L’instance **EventProcessorHost** utilise les données de la tâche Stream Analytics **DeviceInfo** pour gérer la synchronisation.

## <a name="solution-portal"></a>Portail de la solution
![portail de la solution][img-dashboard]

Le portail de la solution est une interface utilisateur web qui est déployée dans le cloud dans le cadre de la solution préconfigurée. Il vous permet d’effectuer les opérations suivantes :

* Afficher l’historique de télémétrie et d’alertes dans un tableau de bord.
* Approvisionner de nouveaux appareils.
* Gérer et surveiller des appareils.
* Envoyer des commandes à des appareils spécifiques.
* Des méthodes d’appel sur des appareils spécifiques.
* Gérer les règles et les actions.
* Planifiez l’exécution de travaux sur un ou plusieurs appareils.

Dans cette solution préconfigurée, le portail de la solution fait partie du **serveur principal de solution IoT** et de la **connectivité de traitement et d’entreprise** dans [l’architecture de solution IoT][lnk-what-is-azure-iot] standard.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les architectures de solution IoT, consultez le document [Microsoft Azure IoT services: Reference Architecture][lnk-refarch].

À présent que vous savez en quoi consiste une solution préconfigurée, vous pouvez commencer en déployant la solution préconfigurée de *surveillance à distance* : [Prise en main des solutions préconfigurées][lnk-getstarted-preconfigured].

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: ../event-hubs/event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: ../app-service-web/web-sites-create-web-jobs.md
[lnk-identity-registry]: ../iot-hub/iot-hub-devguide-identity-registry.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md
[lnk-azureiotsuite]: https://www.azureiotsuite.com/
[lnk-refarch]: http://download.microsoft.com/download/A/4/D/A4DAD253-BC21-41D3-B9D9-87D2AE6F0719/Microsoft_Azure_IoT_Reference_Architecture.pdf
[lnk-getstarted-preconfigured]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-c2d-guidance]: ../iot-hub/iot-hub-devguide-c2d-guidance.md
[lnk-device-twin]: ../iot-hub/iot-hub-devguide-device-twins.md
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md

