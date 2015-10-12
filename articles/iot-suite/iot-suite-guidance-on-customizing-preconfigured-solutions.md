<properties
	pageTitle="Conseils sur la personnalisation de solutions préconfigurées dans Microsoft Azure IoT Suite | Microsoft Azure"
	description="Fournit des conseils sur la personnalisation des solutions préconfigurées Azure IoT Suite."
	services=""
	documentationCenter=".net"
	authors="stevehob"
	manager="kevinmil"
	editor=""/>

<tags
     ms.service="na"
     ms.devlang="na"
     ms.topic="article"
     ms.tgt_pltfrm="na"
     ms.workload="tbd"
     ms.date="09/22/2015"
     ms.author="stevehob"/>

# Personnalisation des solutions préconfigurées
Les solutions préconfigurées fournies avec Azure IoT Suite permettent aux clients de voir les services de la suite qui collaborent pour fournir une solution de bout en bout. À partir de ce point de départ, il existe différents endroits où la personnalisation et l’extension peuvent se produire pour personnaliser la solution pour des scénarios spécifiques. Les sections suivantes décrivent ces points de personnalisation courants.

## Recherche du code source
Le code source de votre solution préconfigurée est disponible sur Github dans les référentiels suivants :

- Surveillance à distance : [https://www.github.com/Azure/azure-iot-remote-monitoring](https://github.com/Azure/azure-iot-remote-monitoring)

Cette source est fournie pour illustrer un modèle pour l’implémentation de la fonctionnalité principale de surveillance à distance à l’aide d’Azure IoT Suite.

## Modification des règles préconfigurées
La solution de surveillance à distance comprend deux [tâches Azure Stream Analytics](http://azure.microsoft.com/documentation/services/stream-analytics) pour implémenter la logique de télémétrie et d’alerte affichée sur le tableau de bord.

La première tâche sélectionne toutes les données du flux de télémétrie entrant et crée deux sorties différentes. La tâche sera nommée [nom\_solution]-Télémétrie.

- La première sortie prend simplement toutes les données par `SELECT *` et les stocke dans des objets blob. Ce stockage d’objets blob est l’emplacement où le tableau de bord lit les valeurs brutes pour créer ses graphiques.
- La deuxième sortie effectue un calcul `AVG()`, `MIN()` et `MAX()` sur une fenêtre glissante de cinq minutes. Ces données sont affichées dans les cadrans du tableau de bord.

À l’aide de l’interface utilisateur de Stream Analytics, vous pouvez modifier ces tâches directement pour modifier la logique ou ajouter une logique spécifique à votre scénario.

La deuxième tâche opère sur les valeurs d’appareil à seuil créées dans la page Règles de la solution. Cette tâche utilise la valeur de seuil définie pour chaque appareil comme Donnée de référence. Elle compare la valeur de seuil pour voir si elle est supérieure (`>`) à la valeur réelle. Vous pouvez modifier cette tâche, par exemple pour changer l’opérateur de comparaison.

***Notez que le tableau de bord de surveillance à distance dépend de données spécifiques. Ainsi, modifier les tâches pourrait provoquer l’échec du tableau de bord.***

## Ajout de vos propres règles
En plus de modifier les tâches Azure Stream Analytics préconfigurées, vous pouvez utiliser le portail Azure pour ajouter de nouvelles tâches ou ajouter de nouvelles requêtes aux tâches existantes.

## Personnalisation des appareils
L’une des activités d’extension les plus courantes consiste à utiliser des appareils spécifiques à votre scénario. Il existe plusieurs méthodes pour travailler avec des appareils, comme la modification d’un appareil simulé pour qu’il corresponde à votre scénario ou l’utilisation du Kit SDK d’appareils Azure IoT pour connecter votre appareil physique à la solution.

Pour obtenir des instructions détaillées sur l’ajout d’appareils à la solution préconfigurée de surveillance à distance, consultez le document suivant : [IoT Suite - Connexion d’appareils](iot-suite-connecting-devices.md)

### Création de votre propre appareil simulé
Le code source de la solution de surveillance à distance (référencé ci-dessus) contient un simulateur .NET. C’est lui qui est configuré dans le cadre de la solution et qui peut être modifié pour envoyer des données de télémétrie ou des métadonnées différentes, ou pour répondre à des commandes différentes.

En outre, nous avons fourni un [exemple de Kit de développement logiciel C](https://github.com/Azure/azure-iot-sdks/c/serializer/samples/remote_monitoring) conçu pour fonctionner avec la solution préconfigurée de surveillance à distance.

### Création et utilisation de votre propre appareil (physique)
Les [Kits de développement logiciel (SDK) Azure IoT](https://github.com/Azure/azure-iot-sdks) fournissent des bibliothèques pour la connexion de nombreux types d’appareils (langages et systèmes d’exploitation) à des solutions IoT.


Pour plus d’informations sur les appareils IoT, consultez le [site de développement Azure IoT](http://azure.microsoft.com/develop/iot) pour y trouver des liens et de la documentation.

<!---HONumber=Oct15_HO1-->