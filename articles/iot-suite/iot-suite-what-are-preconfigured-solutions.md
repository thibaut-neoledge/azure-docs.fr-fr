<properties
 pageTitle="Solutions préconfigurées IoT Azure | Microsoft Azure"
 description="Description des solutions préconfigurées IoT Azure et de leur architecture avec des liens vers des ressources supplémentaires."
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="dominicbetts"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="11/30/2015"
 ms.author="dobett"/>

# Que sont les solutions préconfigurées Azure IoT Suite ?

Les solutions Azure IoT Suite préconfigurées sont des implémentations de modèles de solution IoT courants que vous pouvez déployer sur Microsoft Azure en utilisant votre abonnement Azure. Vous pouvez utiliser les solutions préconfigurées :

- Comme point de départ de vos propres solutions IoT.
- Pour en savoir plus sur les modèles courants en matière de développement et de conception d’une solution IoT.

Chaque solution préconfigurée implémente un scénario IoT courant et est une implémentation complète de bout en bout utilisant les appareils simulés pour générer la télémétrie.

En plus de déployer et d’exécuter les solutions dans Azure, vous pouvez télécharger le code source complet, puis personnaliser et étendre la solution pour répondre à vos besoins IoT spécifiques.

> [AZURE.NOTE] L'article [Prise en main des solutions préconfigurées IoT][lnk-preconf-get-started] explique comment déployer et exécuter une des solutions.

Le tableau suivant montre le mappage entre les solutions et des fonctionnalités IoT spécifiques :

| Solution | Ingestion de données | Identité d’appareil | Commande et contrôle | Règles et actions | Analyse prédictive |
|------------------------|-----|-----|-----|-----|-----|
| [Surveillance à distance][lnk-remote-monitoring] | Oui | Oui | Oui | Oui | - | | [Maintenance prédictive][lnk-predictive-maintenance] | Oui | Oui | Oui | Oui | Oui |

## Présentation de la solution préconfigurée de surveillance à distance

Nous avons choisi d’aborder dans cet article la solution préconfigurée de surveillance à distance car il s’agit de la plus simple des solutions et qu’elle illustre les éléments de conception communs que partagent les autres solutions.

Le schéma suivant illustre les éléments clés de la solution de surveillance à distance. Les sections ci-dessous fournissent des informations supplémentaires sur ces éléments.

![Architecture de la solution préconfigurée Surveillance à distance][img-remote-monitoring-arch]

## Appareils

Lorsque vous déployez la solution préconfigurée de surveillance à distance, le déploiement inclut des instances d’un simulateur de logiciel qui simule un refroidisseur physique. Les appareils simulés envoient les données de télémétrie de température et d’humidité à un point de terminaison IoT Hub. Les appareils simulés répondent également aux commandes suivantes, envoyées à partir du portail de la solution via l’IoT Hub :

- Effectuer un test Ping
- Démarrer la télémétrie
- Arrêter la télémétrie
- Modifier la température nominale
- Diagnostiquer la télémétrie
- Modifier l’état de l’appareil

## IoT Hub

Un IoT Hub reçoit la télémétrie provenant des refroidisseurs à un seul point de terminaison. Un IoT Hub gère également les points de terminaison spécifiques des appareils où chaque appareil peut récupérer les commandes, notamment la commande **Effectuer un test Ping**, qui lui sont envoyées.

L’IoT Hub met les données de télémétrie qu’il reçoit à disposition via un point de terminaison de groupe de consommateurs.

Dans cette solution préconfigurée, l’instance IoT Hub correspond à une *passerelle cloud* dans une [architecture de solution IoT][lnk-what-is-azure-iot] standard.

## Azure Stream Analytics

La solution préconfigurée utilise trois tâches [Azure Stream Analytics][lnk-asa] (ASA) pour filtrer le flux de télémétrie en provenance des refroidisseurs :

- La tâche 1 envoie toutes les données de télémétrie au Blob Storage Azure pour le stockage à froid.
- La tâche 2 filtre le flux de télémétrie pour identifier les messages de réponse aux commandes et les messages de mise à jour de statut d’appareil des appareils, et elle envoie ces messages spécifiques à un point de terminaison Azure Event Hub.
- La tâche 3 filtre le flux de télémétrie pour les valeurs qui déclenchent des alertes. Lorsqu’une valeur déclenche une alerte, la solution affiche la notification dans la table d’historique des alertes dans la vue Tableau de bord du portail de la solution.

Dans cette solution préconfigurée, les tâches ASA font partie du *serveur principal de solution IoT* dans une [architecture de solution IoT][lnk-what-is-azure-iot] standard.

## Processeur d’événements

Une instance [EventPocessorHost][lnk-event-processor], exécutée dans un [WebJob][lnk-web-job], traite les messages de réponse aux commandes et de statut d’appareil identifiés par la tâche 2 ASA, puis stocke ces informations dans une base de données [Azure DocumentDB][lnk-document-db].

Dans cette solution préconfigurée, le processeur d’événements fait partie du *serveur principal de solution IoT* dans une [architecture de solution IoT][lnk-what-is-azure-iot] standard.

## Portail de la solution

![Tableau de bord de solution][img-dashboard]

Le portail de la solution est une interface utilisateur web qui est déployée dans le cloud dans le cadre de la solution préconfigurée. Il vous permet d’effectuer les opérations suivantes :

- Afficher l’historique de télémétrie et d’alertes dans un tableau de bord.
- Approvisionner de nouveaux appareils.
- Gérer et surveiller des appareils.
- Envoyer des commandes à des appareils spécifiques.
- Gérer les règles et les actions.

> [AZURE.NOTE] Le portail de la solution assure également la synchronisation du [Registre d’identités d’appareils][lnk-identity-registry] IoT Hub avec la banque d’informations d’état d’appareils enrichie dans la base de données DocumentDB de la solution.

Dans cette solution préconfigurée, le portail de la solution fait partie du *serveur principal de solution IoT* et de la *connectivité de traitement et d’entreprise* dans une [architecture de solution IoT][lnk-what-is-azure-iot] standard.

## Étapes suivantes

Explorez ces ressources pour en savoir plus sur les solutions IoT préconfigurées :

- [Prise en main des solutions préconfigurées IoT][lnk-preconf-get-started]
- [Présentation de la solution préconfigurée de maintenance prédictive][lnk-predictive-maintenance]

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[img-dashboard]: ./media/iot-suite-what-are-preconfigured-solutions/dashboard.png
[lnk-remote-monitoring]: iot-suite-remote-monitoring-sample-walkthrough.md
[lnk-what-is-azure-iot]: iot-suite-what-is-azure-iot.md
[lnk-asa]: https://azure.microsoft.com/documentation/services/stream-analytics/
[lnk-event-processor]: event-hubs-programming-guide.md#event-processor-host
[lnk-web-job]: web-sites-create-web-jobs.md
[lnk-document-db]: https://azure.microsoft.com/documentation/services/documentdb/
[lnk-identity-registry]: iot-hub-devguide.md#device-identity-registry
[lnk-suite-overview]: iot-suite-overview.md
[lnk-preconf-get-started]: iot-suite-getstarted-preconfigured-solutions.md
[lnk-predictive-maintenance]: iot-suite-predictive-overview.md

<!---HONumber=AcomDC_0218_2016-->