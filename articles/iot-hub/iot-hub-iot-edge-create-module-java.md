---
title: "Créer un module Azure IoT Edge avec Java | Microsoft Docs"
description: "Ce didacticiel explique comment écrire un module de convertisseur de données BLE en utilisant les derniers packages Maven Azure IoT Edge."
services: iot-hub
author: junyi
manager: timlt
ms.service: iot-hub
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: Java
ms.topic: article
ms.date: 06/28/2017
ms.author: junyi
ms.openlocfilehash: 0c430272225d79737baec2be15ed7c93991cdeac
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 08/03/2017
---
# <a name="create-an-azure-iot-edge-module-with-java"></a>Créer un module Azure IoT Edge avec Java

Ce didacticiel explique comment vous pouvez créer un module pour Azure IoT Edge en Java.

Dans ce didacticiel, nous aborderons la configuration de l’environnement et expliquerons comment écrire un module de convertisseur de données [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) en utilisant les derniers packages Maven Azure IoT Edge.

## <a name="prerequisites"></a>Composants requis

Dans cette section, vous allez configurer votre environnement pour le développement du module IoT Edge. Cela s’applique aux systèmes d’exploitation *Windows 64 bits* et *Linux 64 bits (Ubuntu/Debian 8)*.

Les logiciels suivants sont requis :

* [Client Git](https://git-scm.com/downloads).
* [JDK **x64**](http://www.oracle.com/technetwork/java/javase/downloads/jdk8-downloads-2133151.html).
* [Maven](https://maven.apache.org/install.html).

Ouvrez une fenêtre de terminal de ligne de commande et clonez le référentiel suivant :

1. `git clone https://github.com/Azure-Samples/iot-edge-samples.git`.
2. `cd iot-edge-samples/java/simulated_ble`

## <a name="overall-architecture"></a>Architecture globale

La plateforme Azure IoT Edge adopte fortement [l’architecture de Von Neumann](https://en.wikipedia.org/wiki/Von_Neumann_architecture). Cela signifie que l’ensemble de l’architecture Azure IoT Edge est un système qui traite une entrée et produit une sortie, et que chaque module individuel est également un minuscule sous-système d’entrée-de sortie. Dans ce didacticiel, nous allons présenter les deux modules suivants :

1. Un module qui reçoit un signal [BLE](https://en.wikipedia.org/wiki/Bluetooth_Low_Energy) simulé et qui le convertit en message au format [JSON](https://en.wikipedia.org/wiki/JSON).
2. Un module qui imprime le message [JSON](https://en.wikipedia.org/wiki/JSON) reçu.

L’image suivante affiche le flux de données de bout en bout classique pour ce projet :

![Flux de données entre les trois modules](media/iot-hub-iot-edge-create-module/dataflow.png "Entrée : module BLE simulé ; processeur : module de convertisseur ; sortie : module d’imprimante")

## <a name="understanding-the-code"></a>Présentation du code

### <a name="maven-project-structure"></a>Structure du projet Maven

Comme les packages Azure IoT Edge reposent sur Maven, nous avons besoin de créer une structure de projet Maven classique, qui contient un fichier `pom.xml`.

Le POM hérite du package `com.microsoft.azure.gateway.gateway-module-base`, qui déclare toutes les dépendances requises par un projet de module incluant les fichiers binaires du runtime, le chemin du fichier config et le comportement d’exécution. Cela nous permet de gagner beaucoup de temps et élimine la nécessité d’écrire et de réécrire des centaines de lignes de code indéfiniment.

Nous devons mettre à jour le fichier pom.xml en déclarant les dépendances/plug-ins requis et le nom du fichier config à utiliser par notre module, comme indiqué dans l’extrait de code suivant.

```xml
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 http://maven.apache.org/xsd/maven-4.0.0.xsd">
  <modelVersion>4.0.0</modelVersion>

  <!-- Inherit from parent -->
  <parent>
    <groupId>com.microsoft.azure.gateway</groupId>
    <artifactId>gateway-module-base</artifactId>
    <version>1.0.1</version>
  </parent>
  
  <groupId>com.microsoft.azure.gateway</groupId>
  <artifactId>ble-converter</artifactId>
  <version>1.0</version>
  <packaging>jar</packaging>

  <!-- Set the filename of the Azure IoT Edge configuration located
       under ./src/main/resources/gateway/ which is used in parent -->
  <properties>
    <gw.config.fileName>gw-config.json</gw.config.fileName>
  </properties>

  <!-- Re-declare dependencies used in parent -->
  <dependencies>
    <dependency>
      <groupId>com.microsoft.azure.gateway</groupId>
      <artifactId>gateway-java-binding</artifactId>
    </dependency>
    <dependency>
      <groupId>${dependency.runtime.group}</groupId>
      <artifactId>${dependency.runtime.name}</artifactId>
    </dependency>
  </dependencies>

  <!-- Re-declare plugins used in parent -->
  <build>
    <plugins>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-dependency-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-resources-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.apache.maven.plugins</groupId>
        <artifactId>maven-shade-plugin</artifactId>
      </plugin>
      <plugin>
        <groupId>org.codehaus.mojo</groupId>
        <artifactId>exec-maven-plugin</artifactId>
      </plugin>
    </plugins>
  </build>
</project>
```

### <a name="basic-understanding-of-an-azure-iot-edge-module"></a>Compréhension élémentaire d’un module Azure IoT Edge

Vous pouvez traiter un module Azure IoT Edge comme un processeur de données dont la tâche consiste à : recevoir une entrée, la traiter et générer une sortie.

L’entrée peut être des données issues du matériel (comme un détecteur de mouvement), un message provenant d’autres modules ou toute autre chose (par exemple, un nombre aléatoire généré régulièrement par un minuteur).

La sortie est similaire à l’entrée, elle peut déclencher le comportement du matériel (comme le clignotement d’une LED), un message à d’autres modules ou toute autre chose (par exemple, une impression vers la console).

Les modules communiquent entre eux à l’aide de la classe `com.microsoft.azure.gateway.messaging.Message`. Le **contenu** d’un `Message` est un tableau d’octets qui est capable de représenter n’importe quel type de données de votre choix. Les **propriétés** sont également disponibles dans le `Message` et sont simplement un mappage de chaîne à chaîne. Vous pouvez imaginer les **propriétés** comme étant les en-têtes d’une demande HTTP ou les métadonnées d’un fichier.

Pour développer un module Azure IoT Edge en Java, vous devez créer une classe de module qui hérite de `com.microsoft.azure.gateway.core.GatewayModule` et implémenter les méthodes abstraites `receive()` et `destroy()` requises. À ce stade, vous pouvez également choisir d’implémenter les méthodes `start()` ou `create()` facultatives. L’extrait de code suivant vous montre comment prendre en main la création d’un module Azure IoT Edge.

```java
import com.microsoft.azure.gateway.core.Broker;
import com.microsoft.azure.gateway.core.GatewayModule;
import com.microsoft.azure.gateway.messaging.Message;

public class MyEdgeModule extends GatewayModule {
  public MyEdgeModule(long address, Broker broker, String configuration) {
    /* Let the GatewayModule do the dirty work of initialization. It's also
       a good time to parse your own configuration defined in Azure IoT Edge
       configuration file (typically ./src/main/resources/gateway/gw-config.json) */
    super(address, broker, configuration);
  }

  @Override
  public void start() {
    /* Acquire the resources you need. If you don't
       need any resources, you may omit this method. */
  }

  @Override
  public void destroy() {
    /* It's time to release all resources. This method is required. */
  }

  @Override
  public void receive(Message message) {
    /* Logic to process the input message. This method is required. */
    // ...
    /* Use publish() method to do the output. You are
       allowed to publish your new Message instance. */
    this.publish(message);
  }
}
```

### <a name="converter-module"></a>Module de convertisseur

| Entrée                    | Processeur                              | Sortie                 | Fichier source            |
| ------------------------ | -------------------------------------- | ---------------------- | ---------------------- |
| Message de données de température | Analyser et construire un nouveau message JSON | Structurer le message JSON | `ConverterModule.java` |

Ce module est un module Azure IoT Edge classique. Il accepte des messages de température provenant d’autres modules (un module de matériel, ou dans notre cas, notre module BLE simulé), puis normalise le message de température dans un message JSON structuré (y compris l’ajout de l’ID de message, la définition de la propriété indiquant si nous devons déclencher l’alerte de température, etc.).

```java
@Override
public void receive(Message message) {
  try {
    JSONObject messageFromBle = new JSONObject(new String(message.getContent()));
    double temperature = messageFromBle.getDouble("temperature");
    Map<String, String> inputProperties = message.getProperties();

    HashMap<String, String> properties = new HashMap<>();
    properties.put("source", inputProperties.get("source"));
    properties.put("macAddress", inputProperties.get("macAddress"));
    properties.put("temperatureAlert", temperature > 30 ? "true" : "false");

    String content = String.format(
        "{ \"deviceId\": \"Intel NUC Gateway\", \"messageId\": %d, \"temperature\": %f }",
        ++this.messageCount, temperature);

    this.publish(new Message(content.getBytes(), properties));
  } catch (Exception ex) {
    ex.printStackTrace();
  }
}
```

### <a name="printer-module"></a>Module d’imprimante

| Entrée                          | Processeur | Sortie                     | Fichier source          |
| ------------------------------ | --------- | -------------------------- | -------------------- |
| Tous les messages provenant d’autres modules | N/A       | Enregistrer le message sur la console | `PrinterModule.java` |

Il s’agit d’un module simple et explicite qui génère les messages reçus dans la fenêtre de terminal.

```java
@Override
public void receive(Message message) {
  System.out.println(message.toString());
}
```

### <a name="azure-iot-edge-configuration"></a>Configuration d’Azure IoT Edge

La dernière étape avant d’exécuter les modules consiste à configurer Azure IoT Edge et à établir les connexions entre les modules.

Tout d’abord, nous devons déclarer notre chargeur Java (étant donné qu’Azure IoT Edge prend en charge des chargeurs de différents langages) qui peut, par la suite, être référencé par son `name` dans les sections.

```json
"loaders": [{
  "type": "java",
  "name": "java",
  "configuration": {
    "jvm.options": {
      "library.path": "./"
    }
  }
}]
```

Une fois que nous avons déclaré nos chargeurs, nous devrons également déclarer nos modules. À l’instar de la déclaration des chargeurs, les modules peuvent aussi être référencés par leur attribut `name`. Lors de la déclaration d’un module, nous devons spécifier le chargeur qu’il doit utiliser (qui doit être celui que nous avons défini avant) et le point d’entrée (qui doit être le nom de la classe normalisé de notre module) pour chaque module. Le module `simulated_device` est un module natif qui est inclus dans le package de runtime principal Azure IoT Edge. Vous devez toujours inclure l’élément `args` dans le fichier JSON même si sa valeur est `null`.

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
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/ConverterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  },
  {
    "name": "print",
    "loader": {
      "name": "java",
      "entrypoint": {
        "class.name": "com/microsoft/azure/gateway/PrinterModule",
        "class.path": "./ble-converter-1.0-with-deps.jar"
      }
    },
    "args": null
  }
]
```

À la fin de la configuration, nous établissons les connexions. Chaque connexion est exprimée par `source` et `sink`. Ces valeurs doivent faire référence à un module prédéfini. Le message de sortie du module `source` sera transféré à l’entrée du module `sink`.

```json
"links": [
  {
    "source": "simulated_device",
    "sink": "converter"
  },
  {
    "source": "converter",
    "sink": "print"
  }
]
```

## <a name="running-the-modules"></a>Exécution des modules

Utilisez `mvn package` pour générer tous les éléments dans le dossier `target/`. `mvn clean package` est également recommandé pour créer un build propre.

Utilisez `mvn exec:exec` pour exécuter Azure IoT Edge, et vous pourrez observer que les données de température et toutes les propriétés sont imprimées vers la console à une fréquence fixe.

Si vous souhaitez arrêter l’application, appuyez sur la touche `<Enter>`.

> [!IMPORTANT]
> Il est déconseillé d’utiliser Ctrl + C pour arrêter l’application de passerelle IoT Edge. Cela peut entraîner l’arrêt anormal du processus.

