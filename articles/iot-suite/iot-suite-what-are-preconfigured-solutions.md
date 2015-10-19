<properties
 pageTitle="Description des solutions préconfigurées Azure IoT. | Microsoft Azure"
 description="Description des solutions préconfigurées IoT Azure et de leur architecture avec des liens vers des ressources supplémentaires."
 services=""
 documentationCenter=".net"
 authors="aguilaaj"
 manager="kevinmil"
 editor=""/>

<tags
 ms.service="na"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="09/29/2015"
 ms.author="araguila"/>

# Que sont les solutions préconfigurées IoT Azure ?

Vous pouvez déployer des solutions préconfigurées qui implémentent des scénarios IoT (Internet des objets) courants dans Microsoft Azure en utilisant votre abonnement Azure. Vous pouvez utiliser des solutions préconfigurées :

- Comme point de départ de vos propres solutions IoT.
- Pour en savoir plus sur les modèles les plus courants en matière de développement et de conception d’une solution IoT.

Chaque solution préconfigurée implémente un scénario IoT courant et est une implémentation complète de bout en bout.

En plus de déployer et d’exécuter les solutions préconfigurées dans Azure, vous pouvez télécharger le code source complet pour personnaliser et étendre la solution pour répondre à vos besoins spécifiques.

Les solutions préconfigurées disponibles sont les suivantes :

- Surveillance à distance
- Maintenance prédictive

Le tableau suivant montre le mappage entre ces solutions préconfigurées et des fonctionnalités IoT spécifiques :

| Solution | Ingestion de données | Identité d’appareil | Commande et contrôle | Règles et actions | Analyse prédictive |
|------------------------|----------------|-----------------|---------------------|-------------------|----------------------|
| Surveillance à distance | Oui | Oui | Oui | Oui | - | | Maintenance prédictive | Oui | Oui | Oui | Oui | Oui |

## Vue d’ensemble de l’exemple Surveillance à distance

Surveillance à distance est la plus simple des solutions préconfigurées offrant des fonctionnalités complètes. Cette section décrit certaines des fonctionnalités clés de la solution préconfigurée de surveillance à distance par le biais d’une introduction à l’ensemble des solutions préconfigurées.

Le diagramme suivant illustre les principales fonctionnalités de la solution et les sections suivantes. Les sections suivantes fournissent plus d’informations sur les éléments contenus dans le diagramme.

![Architecture de la solution préconfigurée Surveillance à distance][img-remote-monitoring-arch]

### Appareil

L’appareil qui est préconfiguré avec la solution préconfigurée de surveillance à distance est une simulation logicielle d’un refroidisseur qui envoie des données de télémétrie de température et d’humidité. L’appareil peut également répondre à un ensemble de commandes envoyées par le portail de la solution via IoT Hub. Les commandes déjà implémentés dans le simulateur sont les suivantes : Ping Device, Start Telemetry, Stop Telemetry, Change Set Point Temp, Diagnostic Telemetry et Change Device State.

Les refroidisseurs dans cette solution préconfigurée correspondent aux **appareils et sources de données** dans une architecture de solution IoT classique.

### IoT Hub

Un hub IoT reçoit les données de télémétrie en provenance des refroidisseurs à un point de terminaison unique et tient à jour des points de terminaison spécifiques aux appareils où ceux-ci peuvent récupérer des commandes telles que PingDevice.

Le hub IoT expose les données de télémétrie qu’il reçoit via un point de terminaison de groupe de consommateurs.

L’instance d’IoT Hub dans cette solution préconfigurée correspond à l’**application principale IoT** dans une architecture de solution IoT classique.

### Azure Stream Analytics

La solution préconfigurée utilise des tâches [Azure Stream Analytics][] pour filtrer le flux d’événements en provenance des refroidisseurs. Une tâche envoie toutes les données de télémétrie aux objets blob de stockage Azure pour le stockage à froid. La deuxième tâche filtre le flux d’événements pour les messages de réponse de commande et les messages de mise à jour de statut d’appareil et elle envoie ces messages spécifiques à un point de terminaison de hub d’événements Azure. La troisième tâche filtre les alertes déclenchées et les affiche dans le tableau d’historique des alertes dans le tableau de bord du portail de la solution.


### Processeur d’événements

Une instance de processeur d’événements, exécutée dans un projet web, traite les données de statut d’appareil et de réponse de commande et elle stocke ces informations dans une base de données Azure DocumentDB.

### Portail de la solution

Le portail de la solution est une interface utilisateur web qui vous permet de :

- Afficher l'historique de télémétrie et d'alarme dans le tableau de bord.
- Approvisionner de nouveaux appareils.
- Gérer et surveiller des appareils.
- Envoyer des commandes à des appareils spécifiques.
- Gérer les règles et les actions.

> [AZURE.NOTE]L’affichage d’appareils du portail de la solution assure également la synchronisation du Registre d’identités d’appareils IoT Hub avec la banque d’informations d’état d’appareils enrichie dans la base de données DocumentDB.

## Étapes suivantes

Explorez ces ressources pour en savoir plus sur les solutions IoT préconfigurées :

- [Vue d’ensemble des solutions préconfigurées Azure IoT](iot-suite-overview.md)
- [Prise en main des solutions préconfigurées IoT](iot-suite-getstarted-preconfigured-solutions.md)

[img-remote-monitoring-arch]: ./media/iot-suite-what-are-preconfigured-solutions/remote-monitoring-arch1.png
[Azure Stream Analytics]: https://azure.microsoft.com/services/stream-analytics/

<!---HONumber=Oct15_HO2-->