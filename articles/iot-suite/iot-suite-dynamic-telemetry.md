---
title: "Utiliser la télémétrie dynamique | Microsoft Docs"
description: "Suivez ce didacticiel pour savoir comment utiliser la télémétrie dynamique avec la solution préconfigurée de surveillance à distance Azure IoT Suite."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 562799dc-06ea-4cdd-b822-80d1f70d2f09
ms.service: iot-suite
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/09/2017
ms.author: dobett
translationtype: Human Translation
ms.sourcegitcommit: 37a1653ca058c60a39df95f646127bd9e7fdd556
ms.openlocfilehash: 7fe03bcb918997971208554d030264d67bedb1ff


---
# <a name="use-dynamic-telemetry-with-the-remote-monitoring-preconfigured-solution"></a>Utilisation de la télémétrie dynamique avec la solution préconfigurée de surveillance à distance
## <a name="introduction"></a>Introduction
La télémétrie dynamique vous permet de visualiser toutes les données de télémétrie envoyées vers la solution préconfigurée de surveillance à distance. Les appareils simulés déployés avec la solution préconfigurée envoient les données de télémétrie de température et d’humidité, que vous pouvez afficher sur le tableau de bord. Si vous personnalisez les appareils simulés existants, créez des appareils simulés ou connectez des appareils physiques sur la solution préconfigurée vers laquelle vous pouvez envoyer d’autres valeurs de télémétrie comme la température externe, les données RPM ou la vitesse du vent. Vous pouvez ensuite visualiser ces données de télémétrie supplémentaires sur le tableau de bord.

Ce didacticiel utilise un appareil simulé Node.js simple que vous pouvez facilement modifier pour faire des essais avec les données de télémétrie dynamique.

Pour suivre ce didacticiel, vous avez besoin des éléments suivants :

* Un abonnement Azure actif. Si vous ne possédez pas de compte, vous pouvez créer un compte d’évaluation gratuit en quelques minutes. Pour plus d’informations, consultez la rubrique [Version d’évaluation gratuite d’Azure][lnk_free_trial].
* [Node.js][lnk-node] version 0.12.x ou ultérieure.

Vous pouvez suivre ce didacticiel sur n’importe quel système d’exploitation (par exemple, Windows ou Linux) où vous pouvez installer Node.js.

[!INCLUDE [iot-suite-provision-remote-monitoring](../../includes/iot-suite-provision-remote-monitoring.md)]

[!INCLUDE [iot-suite-send-external-temperature](../../includes/iot-suite-send-external-temperature.md)]

## <a name="add-a-telemetry-type"></a>Ajouter un type de télémétrie
L’étape suivante consiste à remplacer les données de télémétrie générées par l’appareil simulé Node.js par un nouveau jeu de valeurs :

1. Arrêtez l’appareil simulé Node.js en tapant **Ctrl+C** dans l’invite de commandes ou l’interpréteur de commandes.
2. Dans le fichier remote_monitoring.js, vous pouvez voir les valeurs de données de base pour la télémétrie existante de température, d’humidité et de température externe. Ajoutez une valeur de données de base pour **rpm** comme suit :
   
    ```
    // Sensors data
    var temperature = 50;
    var humidity = 50;
    var externalTemperature = 55;
    var rpm = 200;
    ```
3. L’appareil simulé Node.js utilise la fonction **generateRandomIncrement** dans le fichier remote_monitoring.js pour ajouter un incrément aléatoire aux valeurs de données de base. Rendez aléatoire la valeur **rpm** en ajoutant une ligne de code après les randomisations existantes comme suit :
   
    ```
    temperature += generateRandomIncrement();
    externalTemperature += generateRandomIncrement();
    humidity += generateRandomIncrement();
    rpm += generateRandomIncrement();
    ```
4. Ajoutez la nouvelle valeur rpm pour la charge utile JSON que l’appareil envoie vers IoT Hub :
   
    ```
    var data = JSON.stringify({
      'DeviceID': deviceId,
      'Temperature': temperature,
      'Humidity': humidity,
      'ExternalTemperature': externalTemperature,
      'RPM': rpm
    });
    ```
5. Exécutez l’appareil simulé Node.js à l’aide de la commande suivante :
   
    ```
    node remote_monitoring.js
    ```
6. Observez le nouveau type de télémétrie RPM qui s’affiche sur le graphique dans le tableau de bord :

![Ajouter les valeurs RPM au tableau de bord][image3]

> [!NOTE]
> Vous devrez peut-être désactiver, puis activer l’appareil Node.js sur la page **Appareils** du tableau de bord pour afficher immédiatement les changements.
> 
> 

## <a name="customize-the-dashboard-display"></a>Personnaliser l’affichage du tableau de bord
Le message **Device-Info** peut inclure des métadonnées sur la télémétrie pouvant être envoyée par l’appareil vers IoT Hub. Ces métadonnées peuvent spécifier les types de télémétrie envoyés par l’appareil. Modifiez la valeur **deviceMetaData** dans le fichier remote_monitoring.js pour inclure une définition **Telemetry** à la suite de la définition **Commands**. L’extrait de code suivant illustre la définition **Commands** (veillez à ajouter un `,` après la définition **Commands**) :

```
'Commands': [{
  'Name': 'SetTemperature',
  'Parameters': [{
    'Name': 'Temperature',
    'Type': 'double'
  }]
},
{
  'Name': 'SetHumidity',
  'Parameters': [{
    'Name': 'Humidity',
    'Type': 'double'
  }]
}],
'Telemetry': [{
  'Name': 'Temperature',
  'Type': 'double'
},
{
  'Name': 'Humidity',
  'Type': 'double'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double'
}]
```

> [!NOTE]
> La solution de surveillance à distance utilise une correspondance non sensible à la casse pour comparer la définition des métadonnées avec les données du flux de télémétrie.
> 
> 

Le fait d’ajouter une définition **Telemetry** comme le montre l’extrait de code précédent ne modifie pas le comportement du tableau de bord. Cependant, les métadonnées peuvent également inclure un attribut **DisplayName** pour personnaliser l’affichage dans le tableau de bord. Mettez à jour la définition des métadonnées **Telemetry** comme le montre l’extrait suivant :

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
{
  'Name': 'ExternalTemperature',
  'Type': 'double',
  'DisplayName': 'Outdoor Temperature (C*)'
}
]
```

La capture d’écran suivante montre de quelle manière ce changement modifie la légende du graphique dans le tableau de bord :

![Personnaliser la légende du graphique][image4]

> [!NOTE]
> Vous devrez peut-être désactiver, puis activer l’appareil Node.js sur la page **Appareils** du tableau de bord pour afficher immédiatement les changements.
> 
> 

## <a name="filter-the-telemetry-types"></a>Filtrer les types de télémétrie
Par défaut, le graphique du tableau de bord affiche toutes les séries de données dans le flux de télémétrie. Vous pouvez utiliser les métadonnées **Device-Info** pour supprimer l’affichage des types de télémétrie spécifiques sur le graphique. 

Pour que le graphique affiche uniquement la télémétrie de température et d’humidité, omettez **ExternalTemperature** dans les métadonnées **Telemetry** **Device-Info** comme suit :

```
'Telemetry': [
{
  'Name': 'Temperature',
  'Type': 'double',
  'DisplayName': 'Temperature (C*)'
},
{
  'Name': 'Humidity',
  'Type': 'double',
  'DisplayName': 'Humidity (relative)'
},
//{
//  'Name': 'ExternalTemperature',
//  'Type': 'double',
//  'DisplayName': 'Outdoor Temperature (C*)'
//}
]
```

**Outdoor Temperature** (Température externe) ne s’affiche plus dans le graphique :

![Filtrer la télémétrie sur le tableau de bord][image5]

Cette modification affecte uniquement l’affichage du graphique. Les données **ExternalTemperature** sont toujours stockées et mises à disposition pour le traitement principal, quel qu’il soit.

> [!NOTE]
> Vous devrez peut-être désactiver, puis activer l’appareil Node.js sur la page **Appareils** du tableau de bord pour afficher immédiatement les changements.
> 
> 

## <a name="handle-errors"></a>des erreurs
Pour qu’un flux de données s’affiche sur le graphique, son **Type** dans les métadonnées **Device-Info** doit correspondre au type de données des valeurs de télémétrie. Par exemple, si les métadonnées spécifient que le **Type** de données d’humidité est **int** et qu’un **double** est trouvé dans le flux de télémétrie, la télémétrie d’humidité ne s’affiche pas sur le graphique. Toutefois, les valeurs **d’humidité** sont toujours stockées et mises à disposition pour le traitement principal, quel qu’il soit.

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous savez comment utiliser la télémétrie dynamique, vous pouvez en savoir plus sur la manière dont les solutions préconfigurées utilisent les informations d’appareil : [Métadonnées relatives aux informations d’appareil dans la solution préconfigurée de surveillance à distance][lnk-devinfo].

[lnk-devinfo]: iot-suite-remote-monitoring-device-info.md

[image3]: media/iot-suite-dynamic-telemetry/image3.png
[image4]: media/iot-suite-dynamic-telemetry/image4.png
[image5]: media/iot-suite-dynamic-telemetry/image5.png

[lnk_free_trial]: http://azure.microsoft.com/pricing/free-trial/
[lnk-node]: http://nodejs.org



<!--HONumber=Feb17_HO2-->


