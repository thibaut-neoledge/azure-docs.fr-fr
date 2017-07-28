---
title: "Vue d’ensemble d’Azure IoT Edge | Microsoft Docs"
description: "Décrit les concepts architecturaux clés dans Azure IoT Edge tels que les passerelles, les modules et les répartiteurs."
services: iot-hub
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 06/02/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: 9edcaee4d051c3dc05bfe23eecc9c22818cf967c
ms.openlocfilehash: ecdd56c91a8fc2011b3d7abe93b9d27c1e1e0bef
ms.contentlocale: fr-fr
ms.lasthandoff: 06/08/2017


---
# <a name="azure-iot-edge-architectural-concepts"></a>Concepts architecturaux d’Azure IoT Edge

Avant d’examiner un exemple de code ou de créer votre propre passerelle de champ à l’aide d’IoT Edge, vous devez comprendre les concepts fondamentaux qui étayent l’architecture IoT Edge.

## <a name="iot-edge-modules"></a>Modules IoT Edge

Vous créez une passerelle avec Azure IoT Edge en créant et en assemblant des *modules IoT Edge*. Les modules s’échangent des données par le biais de *messages* . Un module reçoit un message, exécute une action sur celui-ci, le transforme éventuellement en un nouveau message, puis le publie sur d'autres modules pour un traitement ultérieur. Certains modules peuvent uniquement produire de nouveaux messages et ne jamais traiter les messages entrants. Une chaîne de modules crée un pipeline de traitement des données dans lequel chaque module exécute une transformation de données en un point unique sur ce pipeline.

![Chaîne de modules dans la passerelle créée avec Azure IoT Edge][1]

IoT Edge est composé des éléments suivants :

* Des modules pré-écrits qui exécutent des fonctions de passerelles courantes.
* Les interfaces qu’un développeur peut utiliser pour écrire des modules personnalisés.
* L'infrastructure nécessaire pour déployer et exécuter un ensemble de modules.

Le SDK fournit une couche d’abstraction qui vous permet de créer des passerelles qui s’exécutent sur différents systèmes d’exploitation et plateformes.

![Couche d’abstraction Azure IoT Edge][2]

## <a name="messages"></a>Messages

L’approche consistant à utiliser des modules pour s’échanger des messages représente un moyen pratique de conceptualiser le fonctionnement d'une passerelle, mais elle ne reflète pas exactement ce qui se produit. Les modules IoT Edge utilisent un répartiteur pour communiquer entre eux. Les modules publient des messages sur le répartiteur (en utilisant des modèles de messagerie tels qu’un bus ou les options de publication et d’abonnement), puis laissent le répartiteur acheminer le message vers les modules connectés.

Un module utilise la fonction **Broker_Publish** pour publier un message sur le répartiteur. Le répartiteur remet les messages à un module en appelant une fonction de rappel. Un message se compose d'un ensemble de propriétés de clés/valeurs et d’un contenu transmis sous forme d’un bloc de mémoire.

![Rôle du répartiteur dans Azure IoT Edge][3]

## <a name="message-routing-and-filtering"></a>Routage et filtrage des messages

Deux méthodes permettent d’acheminer des messages vers les modules IoT Edge adéquats :

* Vous pouvez transmettre un ensemble de liens au répartiteur pour permettre à ce dernier de connaître la source et le récepteur de chaque module.
* Un module peut appliquer un filtre sur les propriétés du message.

Un module doit agir sur un message uniquement si le message lui est destiné. Les liens et le filtrage des messages permettent de créer efficacement un pipeline de messages.

## <a name="next-steps"></a>Étapes suivantes

Pour voir l’application de ces concepts dans un exemple que vous pouvez exécuter, consultez [Explorer l’architecture Azure IoT Edge][lnk-hello-world].

<!-- Images -->
[1]: media/iot-hub-iot-edge-overview/modules.png
[2]: media/iot-hub-iot-edge-overview/modules_2.png
[3]: media/iot-hub-iot-edge-overview/messages_1.png

<!-- Links -->
[lnk-hello-world]: ./iot-hub-linux-iot-edge-get-started.md
