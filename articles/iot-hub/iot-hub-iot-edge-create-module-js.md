---
title: "Créer un module Azure IoT Edge avec Node.js | Microsoft Docs"
description: "Ce didacticiel explique comment écrire un module de convertisseur de données BLE en utilisant les derniers packages NPM Azure IoT Edge et le générateur Yeoman."
services: iot-hub
author: sushi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: js
ms.topic: article
ms.date: 06/28/2017
ms.author: sushi
ms.translationtype: Human Translation
ms.sourcegitcommit: 6efa2cca46c2d8e4c00150ff964f8af02397ef99
ms.openlocfilehash: 12e0702c31f36368fd95127abeede1a0d4ff2c87
ms.contentlocale: fr-fr
ms.lasthandoff: 07/01/2017

---
# <a name="create-an-azure-iot-edge-module-with-nodejs"></a>Créer un module Azure IoT Edge avec Node.js

Ce didacticiel explique comment créer un module pour Azure IoT Edge en JS.

Dans ce didacticiel, nous aborderons la configuration de l’environnement et expliquerons comment écrire un module de convertisseur de données [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) en utilisant les derniers packages NPM Azure IoT Edge.

## <a name="prerequisites"></a>Composants requis

Dans cette section, vous configurez votre environnement pour le développement du module IoT Edge. Cela s’applique aux systèmes d’exploitation *Windows 64 bits* et *Linux 64 bits (Ubuntu 14+)*.

Les logiciels suivants sont requis :
* [Client Git](https://git-scm.com/downloads).
* [LTS Node](https://nodejs.org).
* `npm install -g yo`.
* `npm install -g generator-az-iot-gw-module`

## <a name="architecture"></a>Architecture

La plateforme Azure IoT Edge adopte fortement [l’architecture de Von Neumann](https://en.wikipedia.org/wiki/Von_Neumann_architecture). Cela signifie que l’ensemble de l’architecture Azure IoT Edge est un système qui traite une entrée et produit une sortie, et que chaque module individuel est également un minuscule sous-système d’entrée-de sortie. Dans ce didacticiel, nous présentons les deux modules suivants :

1. Un module qui reçoit un signal [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) simulé et le convertit en message au format [JSON](https://en.wikipedia.org/wiki/JSON).
2. Un module qui imprime le message [JSON](https://en.wikipedia.org/wiki/JSON) reçu.

L’image suivante affiche le flux de données de bout en bout classique pour ce projet :

![Flux de données entre les trois modules](media/iot-hub-iot-edge-create-module/dataflow.png "Entrée : module BLE simulé ; processeur : module de convertisseur ; sortie : module d’imprimante")

## <a name="set-up-the-environment"></a>Configurer l’environnement
Nous vous montrons ci-après comment configurer rapidement l’environnement pour commencer à écrire votre premier module de convertisseur BLE avec JS.

### <a name="create-module-project"></a>Créer le projet de module
1. Ouvrez une fenêtre de ligne de commande et exécutez `yo az-iot-gw-module`.
2. Suivez les étapes à l’écran pour terminer l’initialisation de votre projet de module.

### <a name="project-structure"></a>Structure de projet
Un projet de module JS est constitué des composants suivants :

`modules` - Les fichiers source du module JS personnalisé. Remplacez les valeurs par défaut `sensor.js` et `printer.js` par vos propres fichiers de module.

`app.js` - Le fichier d’entrée pour démarrer l’instance Edge.

`gw.config.json` - Le fichier config pour personnaliser les modules à charger par Edge.

`package.json` - Les informations de métadonnées pour le projet de module.

`README.md` - La documentation de base pour le projet de module.


### <a name="package-file"></a>Fichier de package

Cet élément `package.json` déclare toutes les informations de métadonnées requises par un projet de module incluant le nom, la version, l’entrée, les scripts, le runtime et les dépendances de développement.

L’extrait de code suivant illustre la configuration de l’exemple de projet de convertisseur BLE.
```json
{
  "name": "converter",
  "version": "1.0.0",
  "description": "BLE data converter sample for Azure IoT Edge.",
  "repository": {
    "type": "git",
    "url": "https://github.com/Azure-Samples/iot-edge-samples"
  },
  "main": "app.js",
  "scripts": {
    "start": "node app.js"
  },
  "author": "Microsoft Corporation",
  "license": "MIT",
  "dependencies": {
  },
  "devDependencies": {
    "azure-iot-gateway": "~1.1.3"
  }
}
```


### <a name="entry-file"></a>Fichier d’entrée
L’élément `app.js` définit la façon dont l’instance Edge est initialisée. Ici, nous n’avons pas besoin d’apporter de modifications.

```javascript
(function() {
  'use strict';

  const Gateway = require('azure-iot-gateway');
  let config_path = './gw.config.json';

  // node app.js
  if (process.argv.length < 2) {
    throw 'Calling pattern should be node app.js.';
  }

  const gw = new Gateway(config_path);
  gw.run();
})();
```

### <a name="interface-of-module"></a>Interface de module
Vous pouvez traiter un module Azure IoT Edge comme un processeur de données dont la tâche consiste à : recevoir une entrée, la traiter et générer une sortie.

L’entrée peut être des données issues du matériel (comme un détecteur de mouvement), un message provenant d’autres modules ou toute autre chose (par exemple, un nombre aléatoire généré régulièrement par un minuteur).

La sortie est similaire à l’entrée, elle peut déclencher le comportement du matériel (comme le clignotement d’une LED), un message à d’autres modules ou toute autre chose (par exemple, une impression vers la console).

Les modules communiquent entre eux à l’aide de l’objet `message`. Le **contenu** d’un `message` est un tableau d’octets qui est capable de représenter n’importe quel type de données de votre choix. Les **propriétés** sont également disponibles dans le `message` et sont simplement un mappage de chaîne à chaîne. Vous pouvez imaginer les **propriétés** comme étant les en-têtes d’une demande HTTP ou les métadonnées d’un fichier.

Pour développer un module Azure IoT Edge en JS, vous devez créer un nouvel objet de module qui implémente les méthodes requises `receive()`. À ce stade, vous pouvez également choisir d’implémenter les méthodes `create()`, `start()` ou `destroy()` facultatives. L’extrait de code suivant vous montre la structure de l’objet de module JS.

```javascript
'use strict';

module.exports = {
  broker: null,
  configuration: null,

  create: function (broker, configuration) {
    // Default implementation.
    this.broker = broker;
    this.configuration = configuration;

    return true;
  },

  start: function () {
    // Produce
  },

  receive: function (message) {
    // Consume
  },

  destroy: function () {
  }
};
```

### <a name="converter-module"></a>Module de convertisseur
| Entrée                    | Processeur                              | Sortie                 | Fichier source            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| Message de données de température | Analyser et construire un nouveau message JSON | Structurer le message JSON | `converter.js` |

Ce module est un module Azure IoT Edge classique. Il accepte des messages de température provenant d’autres modules (un module de matériel, ou dans notre cas, notre module BLE simulé), puis normalise le message de température dans un message JSON structuré (y compris l’ajout de l’ID de message, la définition de la propriété indiquant si nous devons déclencher l’alerte de température, etc.).

```javascript
receive: function (message) {
  // Initialize the messageCount in global object at first time.
  if (!global.messageCount) {
    global.messageCount = 0;
  }

  // Read the content and properties objects from message.
  let rawContent = JSON.parse(Buffer.from(message.content).toString('utf8'));
  let rawProperties = message.properties;

  // Generate new properties object.
  let newProperties = {
    source: rawProperties.source,
    macAddress: rawProperties.macAddress,
    temperatureAlert: rawContent.temperature > 30 ? 'true' : 'false'
  };

  // Generate new content object.
  let newContent = {
    deviceId: 'Intel NUC Gateway',
    messageId: ++global.messageCount,
    temperature: rawContent.temperature
  };

  // Publish the new message to broker.
  this.broker.publish(
    {
      properties: newProperties,
      content: new Uint8Array(Buffer.from(JSON.stringify(newContent), 'utf8'))
    }
  );
},
```

### <a name="printer-module"></a>Module d’imprimante
| Entrée                          | Processeur | Sortie                     | Fichier source          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| Tous les messages provenant d’autres modules | N/A       | Enregistrer le message sur la console | `printer.js` |

Ce module est simple, explicite et génère les messages reçus (propriété, contenu) dans la fenêtre de terminal.

```javascript
receive: function (message) {
  let properties = JSON.stringify(message.properties);
  let content = Buffer.from(message.content).toString('utf8');

  console.log(`printer.receive.properties - ${properties}`);
  console.log(`printer.receive.content - ${content}\n`);
}
```

### <a name="configuration"></a>Configuration
La dernière étape avant d’exécuter les modules consiste à configurer Azure IoT Edge et à établir les connexions entre les modules.

Tout d’abord, nous devons déclarer notre chargeur `node` (étant donné qu’Azure IoT Edge prend en charge des chargeurs de différents langages) qui peut, par la suite, être référencé par son `name` dans les sections.

```json
"loaders": [
  {
    "type": "node",
    "name": "node"
  }
]
```

Une fois que nous avons déclaré nos chargeurs, nous devons également déclarer nos modules. À l’instar de la déclaration des chargeurs, les modules peuvent aussi être référencés par leur attribut `name`. Lors de la déclaration d’un module, nous devons spécifier le chargeur qu’il doit utiliser (qui doit être celui que nous avons défini avant) et le point d’entrée (qui doit être le nom de la classe normalisé de notre module) pour chaque module. Le module `simulated_device` est un module natif qui est inclus dans le package de runtime principal Azure IoT Edge. Ajoutez l’élément `args` dans le fichier JSON même si sa valeur est `null`.

```json
"modules": [
  {
    "name": "simulated_device",
    "loader": {
      "name": "native",
      "entrypoint": {
        "module.path": "simulated_device"
      }
    },
    "args": {
      "macAddress": "01:02:03:03:02:01",
      "messagePeriod": 500
    }
  },
  {
    "name": "converter",
    "loader": {
      "name": "node",
      "entrypoint": {
        "main.path": "modules/converter.js"
      }
    },
    "args": null
  },
  {
    "name": "printer",
    "loader": {
      "name": "node",
      "entrypoint": {
        "main.path": "modules/printer.js"
      }
    },
    "args": null
  }
]
```

À la fin de la configuration, nous établissons les connexions. Chaque connexion est exprimée par `source` et `sink`. Ces valeurs doivent faire référence à un module prédéfini. Le message de sortie du module `source` est transféré à l’entrée du module `sink`.

```json
"links": [
  {
    "source": "simulated_device",
    "sink": "converter"
  },
  {
    "source": "converter",
    "sink": "printer"
  }
]
```

## <a name="running-the-modules"></a>Exécution des modules
1. `npm install`
2. `npm start`

Si vous souhaitez arrêter l’application, appuyez sur la touche `<Enter>`.

> [!IMPORTANT]
> Il est déconseillé d’utiliser Ctrl + C pour arrêter l’application IoT Edge. Cela peut entraîner l’arrêt anormal du processus.

