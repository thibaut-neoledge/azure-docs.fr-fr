<properties
 pageTitle="Vue d’ensemble de la gestion des appareils | Microsoft Azure"
 description="Vue d'ensemble de la gestion des appareils Azure IoT Hub : représentations d’appareils physiques, requêtes d’appareils, travaux d'appareils"
 services="iot-hub"
 documentationCenter=""
 authors="juanjperez"
 manager="timlt"
 editor=""/>

<tags
 ms.service="iot-hub"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="na"
 ms.date="04/29/2016"
 ms.author="juanpere"/>

# Vue d’ensemble de la gestion des appareils Azure IoT Hub (version préliminaire)

La gestion des appareils Azure IoT Hub permet une gestion des appareils basée sur IoT pour gérer à distance, configurer et mettre à jour vos appareils.

Il existe trois principaux concepts concernant la gestion des appareils dans Azure IoT :

1.  **Représentation d’appareil physique :** la représentation de l’appareil physique dans IoT Hub.

2.  **Requêtes d’appareils** : vous permettent de rechercher des représentations d’appareils et d’obtenir une compréhension agrégée de différentes représentations d’appareils. Par exemple, vous pouvez lancer une requête pour rechercher toutes les représentations d’appareils avec une version de microprogramme 1.0.

3.  **Travaux d’appareils** : action à exécuter sur un ou plusieurs appareils physiques, par exemple la mise à jour du microprogramme, le redémarrage et la réinitialisation aux paramètres d’usine.

## Représentation d’appareil physique

Il s’agit de la représentation d’un appareil physique dans Azure IoT. L’objet **Microsoft.Azure.Devices.Device** est utilisé pour la représentation de l’appareil physique.

![][img-twin]

La représentation de l’appareil physique se compose des éléments suivants :

1.  **Champs d’appareil :** les champs d’appareil sont des propriétés prédéfinies utilisées à la fois pour la messagerie IoT Hub et la gestion des appareils. Ils aident IoT Hub à identifier et à connecter les appareils physiques. Les champs d’appareils ne sont pas synchronisés sur l’appareil et sont exclusivement stockés dans la représentation de l'appareil physique. Les champs d'appareil incluent l’ID de l’appareil ainsi que des informations d'authentification.

2.  **Propriétés de l’appareil :** les propriétés de l’appareil représentent un dictionnaire prédéfini de propriétés décrivant l’appareil physique. L’appareil physique est le maître de chaque propriété de l’appareil et le magasin de référence de chaque valeur correspondante. Une représentation « cohérente » de ces propriétés est stockée dans la représentation d’appareil au sein du cloud. La cohérence et l’actualisation sont soumises aux paramètres de synchronisation, décrits à la rubrique [Didacticiel : utilisation d’une représentation d’appareil][lnk-tutorial-twin]. Les exemples de propriétés de l’appareil incluent notamment la version du microprogramme, le niveau de batterie et le nom du fabricant.

3.  **Propriétés du service :** les propriétés du service représentent des paires **&lt;clé,valeur&gt;** que le développeur ajoute au dictionnaire de propriétés du service. Ces propriétés étendent le modèle de données pour la représentation de l’appareil physique, vous permettant de mieux caractériser votre appareil. Les propriétés du service ne sont pas synchronisées sur l’appareil et sont exclusivement stockées dans la représentation de l'appareil physique dans le cloud. Un exemple de propriété de service est **&lt;NextServiceDate, 11/12/2017&gt;**, qui permet de rechercher des appareils selon la prochaine date de service.

4.  **Balises :** les balises représentent un sous-ensemble de propriétés de service composées de chaînes arbitraires plutôt que de propriétés du dictionnaire. Elles peuvent servir à annoter des représentations d’appareils physiques ou à regrouper des appareils. Les balises ne sont pas synchronisées sur l’appareil et sont exclusivement stockées dans la représentation de l'appareil physique. Par exemple, si votre représentation d’appareil physique représente un camion physique, vous pouvez ajouter une balise pour chaque type de cargaison dans le camion : **pommes**, **oranges** et **bananes**.

## Requêtes d’appareils

Dans la section précédente, vous avez appris les différents composants de la représentation de l’appareil physique. À présent, nous allons découvrir comment rechercher des représentations d’appareils physiques dans le registre d’appareil IoT Hub en fonction de propriétés de l’appareil, de propriétés de service ou de balises. Vous pouvez par exemple utiliser une requête pour rechercher les appareils qui doivent être mis à jour. Vous pouvez interroger tous les appareils avec une version de microprogramme spécifique et traiter les résultats obtenus à l’aide d'une action spécifique (appelée travail d’appareil IoT Hub et expliquée dans la section suivante).

Vous pouvez interroger à l’aide de balises et de propriétés :

-   Pour rechercher des représentations d’appareils à l’aide de balises, vous transmettez un tableau de chaînes et la requête retourne l’ensemble des appareils qui sont balisés avec l’ensemble de ces chaînes.

-   Pour rechercher des représentations d’appareils à l’aide des propriétés du service ou des propriétés de l’appareil, vous utilisez une expression de requête JSON. L'exemple ci-dessous montre comment interroger tous les appareils comportant la propriété d'appareil avec la clé **FirmwareVersion** et la valeur **1.0**. Vous pouvez constater que le **type** de la propriété est **device** (appareil), indiquant que vous exécutez une requête basée sur les propriétés de l’appareil et non sur les propriétés du service :

  ```
  {                           
      "filter": {                  
        "property": {                
          "name": "FirmwareVersion",   
          "type": "device"             
        },                           
        "value": "1.0",              
        "comparisonOperator": "eq",  
        "type": "comparison"         
      },                           
      "project": null,             
      "aggregate": null,           
      "sort": null                 
  }
  ```

## Travaux d’appareils

Le concept suivant de la gestion des appareils présente les travaux d’appareils, qui permettent une coordination d’orchestrations à plusieurs étapes sur de multiples appareils.

Pour le moment, il existe six types de travaux d’appareil qui sont fournis par la gestion des appareils Azure IoT Hub (nous allons ajouter des travaux supplémentaires en fonction des besoins des clients) :

- **Mise à jour du microprogramme** : met à jour le microprogramme (ou l’image de système d’exploitation) sur l’appareil physique.
- **Redémarrage** : redémarre l’appareil physique.
- **Réinitialisation aux paramètres d’usine** : rétablit le microprogramme (ou l’image du système d'exploitation) de l’appareil physique vers une image de sauvegarde d’usine, stockée sur l’appareil.
- **Mise à jour de la configuration** : configure l’agent client IoT Hub en cours d’exécution sur l’appareil physique.
- **Lecture de la propriété de l’appareil** : obtient la dernière valeur d’une propriété de l’appareil sur l’appareil physique.
- **Écriture de la propriété de l’appareil :** modifie une propriété de l’appareil sur l’appareil physique.

Pour plus d’informations sur l’utilisation de chacun de ces travaux, consultez la [documentation sur les API pour C# et node.js][lnk-apidocs].

Un travail peut s’appliquer à plusieurs appareils. Lorsque vous démarrez un travail, un travail enfant associé est créé pour chacun de ces appareils. Un travail enfant s’applique à un seul appareil. Chaque travail enfant comporte un pointeur vers son travail parent. Le travail parent n’est qu’un conteneur pour les travaux enfants, il n’implémente aucune logique pour distinguer les types d’appareils (par exemple la mise à jour d’un appareil Edison Intel par rapport à la mise à jour d’un appareil Raspberry Pi). Le diagramme suivant illustre la relation entre un travail parent, ses enfants et les appareils physiques associés.

![][img-jobs]

Vous pouvez interroger l’historique des travaux afin de comprendre l’état des travaux que vous avez démarrés. Pour des exemples de requêtes, consultez [notre bibliothèque de requêtes][lnk-query-samples].

## Implémentation d’appareil

Les concepts du côté service étant couverts, nous allons aborder la création d’un appareil physique géré. La bibliothèque cliente DM Azure IoT Hub vous permet de gérer vos appareils IoT avec Azure IoT Hub. Vous pouvez notamment effectuer un redémarrage, rétablir les paramètres d’usine et mettre à jour le microprogramme. Actuellement, nous fournissons une bibliothèque C indépendante de la plateforme, mais nous proposerons bientôt la prise en charge d’autres langages.

La bibliothèque cliente DM a deux grandes responsabilités dans la gestion des appareils :

- Synchronisation des propriétés sur l’appareil physique avec celles de la représentation d’appareil physique correspondante dans IoT Hub
- Orchestration des travaux d’appareil envoyés par IoT Hub à l’appareil

Pour plus d’informations concernant ces responsabilités et l’implémentation sur l’appareil physique, consultez la rubrique [Présentation de la bibliothèque cliente de gestion des appareils Azure IoT Hub pour C][lnk-library-c].

## Étapes suivantes

Pour implémenter des applications clientes sur un large éventail de plateformes matérielles et de systèmes d’exploitation d’appareils, vous pouvez utiliser les Kits de développement logiciel (SDK) d’appareils IoT. Ces kits incluent des bibliothèques qui facilitent l’envoi de données de télémétrie vers un IoT Hub et la réception de commandes cloud-à-appareils. Avec les Kits de développement logiciel (SDK), vous avez le choix parmi un grand nombre de protocoles réseau pour communiquer avec IoT Hub. Pour plus d’informations, consultez la rubrique [plus d’informations sur les kits de développement logiciel (SDK) d’appareils][lnk-device-sdks].

Pour plus d’informations sur les fonctionnalités de gestion des appareils Azure IoT Hub, consultez le didacticiel [Prise en main de la gestion des appareils Azure IoT Hub][lnk-get-started].

<!-- Images and links -->
[img-twin]: media/iot-hub-device-management-overview/image1.png
[img-jobs]: media/iot-hub-device-management-overview/image2.png
[img-client]: media/iot-hub-device-management-overview/image3.png

[lnk-lwm2m]: http://technical.openmobilealliance.org/Technical/technical-information/release-program/current-releases/oma-lightweightm2m-v1-0
[lnk-library-c]: iot-hub-device-management-library.md
[lnk-get-started]: iot-hub-device-management-get-started.md
[lnk-tutorial-twin]: iot-hub-device-management-device-twin.md
[lnk-apidocs]: http://azure.github.io/azure-iot-sdks/
[lnk-query-samples]: https://github.com/Azure/azure-iot-sdks/blob/dmpreview/doc/get_started/dm_queries/query-samples.md
[lnk-device-sdks]: https://github.com/Azure/azure-iot-sdks

<!---HONumber=AcomDC_0713_2016-->