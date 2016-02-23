<properties
 pageTitle="Présentation de la solution préconfigurée de surveillance à distance | Microsoft Azure"
 description="Description de la solution préconfigurée de surveillance à distance Azure IoT et de son architecture"
 services=""
 suite="iot-suite"
 documentationCenter=""
 authors="stevehob"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-suite"
 ms.devlang="na"
 ms.topic="get-started-article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="10/21/2015"
 ms.author="stevehob"/>

# Présentation de la solution préconfigurée de surveillance à distance

## Introduction

La solution préconfigurée de surveillance à distance IoT Suite est une solution de surveillance de bout en bout adaptée aux entreprises qui exploitent plusieurs ordinateurs distants. La solution combine les principaux services Azure IoT Suite pour fournir une implémentation générique du scénario d’entreprise. Elle offre un excellent point de départ pour les clients qui envisagent d’implémenter ce type de solution IoT en réponse à leurs besoins spécifiques.

## Architecture logique

Le schéma suivant décrit les composants logiques de la solution préconfigurée :

![](media/iot-suite-remote-monitoring-sample-walkthrough/remote-monitoring-architecture.png)


### Simulations d’appareils

Dans la solution préconfigurée, l’appareil simulé représente un système de refroidissement (un climatiseur dans un bâtiment ou une unité de traitement d’air dans une usine, par exemple). Chaque appareil simulé envoie les messages de télémétrie suivants à l’IoT Hub :


| Message | Description |
|----------|-------------|
| Démarrage | Lorsque l’appareil démarre, il envoie un message **device-info** contenant des informations qui le concernent, telles que son identifiant, ses métadonnées, la liste des commandes qu’il peut prendre en charge, ou encore sa configuration actuelle. |


Les appareils simulés envoient les propriétés suivantes sous la forme de métadonnées :

| Propriété | Objectif |
|------------------------|--------- |
| ID de périphérique | Identifiant fourni ou attribué au moment lorsqu’un appareil est créé dans la solution. |
| Fabricant | Fabricant de l'appareil |
| Numéro de modèle | Numéro de modèle de l’appareil |
| Numéro de série | Numéro de série de l’appareil |
| Microprogramme | Version actuelle du microprogramme installé sur l’appareil |
| Plateforme | Architecture de la plate-forme de l’appareil |
| Processeur | Processeur sur lequel est exécuté l’appareil |
| RAM installée | Quantité de RAM installée sur l’appareil |
| État activé du Hub | Propriété d’état IoT Hub de l’appareil |
| Heure de création | Heure à laquelle l’appareil a été créé dans la solution |
| Heure de mise à jour | Heure de dernière mise à jour des propriétés de l’appareil |
| Latitude | Emplacement en latitude de l’appareil |
| Longitude | Emplacement en longitude de l’appareil |

Le simulateur amorce ces propriétés dans les appareils simulés avec des exemples de valeurs. À chaque fois que le simulateur initialise un appareil simulé, l’appareil publie les métadonnées prédéfinies dans IoT Hub. Notez que cette opération remplace les métadonnées mises à jour dans le portail des appareils.


Les appareils simulés peuvent gérer les commandes suivantes envoyées à partir d’un IoT Hub :

| Commande | Description |
|------------------------|-----------------------------------------------------|
| PingDevice | Envoie une commande _ping_ à l’appareil pour vérifier s’il est actif |
| StartTelemetry | Commande à l’appareil d’envoyer des données de télémétrie |
| StopTelemetry | Commande à l’appareil de cesser l’envoi de données de télémétrie |
| ChangeSetPointTemp | Modifie la valeur nominale autour de laquelle sont générées les données aléatoires |
| DiagnosticTelemetry | Déclenche le simulateur d’appareil pour envoyer une valeur de télémétrie supplémentaire (externalTemp) |
| ChangeDeviceState | Modifie une propriété de l’état étendu de l’appareil et envoie le message d’informations sur l’appareil à partir de l’appareil |


L’accusé de réception de la commande de l’appareil est fourni via l’IoT Hub.


### Tâches d’Azure Stream Analytics

La **Tâche 1 : Télémesure** agit sur le flux de télémétrie de l’appareil entrant à l’aide de deux commandes. La première commande transfère tous les messages de télémétrie des appareils vers le stockage persistant d’objets blob. La deuxième commande calcule les valeurs d’humidité moyenne, minimale et maximale sur une fenêtre glissante de cinq minutes. Ces données sont également envoyées au stockage d’objets blob.

La **Tâche 2 : Informations de l’appareil** filtre les messages d’informations de l’appareil en provenance du flux de messages entrants et les envoie à un point de terminaison du Hub d’événements. Un appareil envoie des messages d’informations d’appareil au démarrage et en réponse à une commande **SendDeviceInfo**.

La **Tâche 3 : Règles** compare les valeurs de télémétrie entrantes (température et humidité) aux seuils définis pour chaque appareil. Les valeurs de seuil sont définies dans l’éditeur de règles fourni avec la solution. Chaque paire appareil/valeur est stockée par horodatage dans un objet blob qui est lu dans Stream Analytics en tant que **Données de référence**. La tâche compare toutes les valeurs non vides au seuil défini pour l’appareil. En cas de dépassement de la condition ’>’, la tâche génère un événement d’**alarme** indiquant que le seuil a été dépassé et renseigne l’appareil, la valeur et l’horodatage.

### Processeur d’événements

Le **processeur d’événements** gère les messages d’informations de l’appareil et les réponses aux commandes. Il utilise :

- les messages d’informations de l’appareil pour mettre à jour le registre de périphériques (stocké dans la base de données DocumentDB) avec les dernières informations relatives à l’appareil ;
- les messages de réponse aux commandes pour mettre à jour l’historique des commandes de l’appareil (stocké dans la base de données DocumentDB).

## Allons plus loin

Cette section vous guide à travers les composants de la solution et décrit l’utilisation prévue avec des exemples à l’appui.

### Tableau de bord de surveillance à distance
Cette page de l’application Web utilise les contrôles Javascript PowerBI (voir le [référentiel d’éléments visuels PowerBI](https://www.github.com/Microsoft/PowerBI-visuals)) pour visualiser les données générées par les tâches Stream Analytics dans le stockage d’objets blob.


### Portail d’administration des appareils

Cette application Web vous permet de :

- configurer un nouvel appareil pour définir l’identifiant unique de l’appareil et générer la clé d’authentification ;
- gérer les propriétés de l’appareil, notamment l’affichage des propriétés existantes et l’incorporation de nouvelles propriétés ;
- envoyer des commandes à un appareil ;
- afficher l’historique de commande d’un appareil.

### Observer le comportement de la solution cloud
Vous pouvez afficher vos ressources approvisionnées depuis le [portail Azure](https://portal.azure.com), en accédant au groupe de ressources associé au nom de solution que vous avez spécifié.

![](media/iot-suite-remote-monitoring-sample-walkthrough/azureportal_01.png)

Lors de la première exécution, la solution simule quatre appareils préconfigurés :

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_01.png)

Vous pouvez utiliser le portail d’administration des appareils pour ajouter un nouvel appareil simulé :

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_02.png)

Dans le portail d’administration des appareils, l’appareil apparaît initialement à l’état **Pending** (En attente) :

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_03.png)

Lorsque l’application a terminé de déployer l’appareil simulé, l’appareil passe à l’état **Running** (En cours d’exécution) dans le portail d’administration des appareils, comme vous pouvez le voir dans la capture d’écran suivante. La tâche **DeviceInfo** de Stream Analytics envoie les informations d’état de l’appareil au portail d’administration des appareils.

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_04.png)

Le portail de solutions vous permet d’envoyer à l’appareil des commandes du type **ChangeSetPointTemp** :

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_05.png)

Lorsque l’appareil signale qu’il a correctement exécuté la commande, il passe à l’état **Success** (Terminé) :

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_06.png)

Vous pouvez utiliser le portail de solutions pour rechercher des appareils ayant des caractéristiques spécifiques (par exemple, un numéro de modèle) :

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_07.png)

Vous pouvez désactiver un appareil puis le supprimer :

![](media/iot-suite-remote-monitoring-sample-walkthrough/solutionportal_08.png)

<!---HONumber=AcomDC_0218_2016-->