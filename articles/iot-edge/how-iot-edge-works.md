---
title: "Qu'est-ce qu’Azure IoT Edge | Microsoft Docs"
description: "Présentation du service Azure IoT Edge"
services: iot-Edge
documentationcenter: 
author: kgremban
manager: timlt
editor: chipalost
ms.assetid: 
ms.service: iot-hub
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/15/2017
ms.author: kgremban
ms.custom: 
ms.openlocfilehash: 5f69041572729d1458a22a855128639056d61586
ms.sourcegitcommit: 9a61faf3463003375a53279e3adce241b5700879
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 11/15/2017
---
# <a name="what-is-azure-iot-edge---preview"></a>Qu'est-ce qu’Azure IoT Edge - préversion

Azure IoT Edge déplace l’analytique et la logique métier personnalisée du cloud vers les appareils, afin que votre organisation puisse se concentrer sur les perspectives commerciales plutôt que sur la gestion des données. Permettez à votre solution de mettre réellement à l’échelle en configurant votre logiciel IoT, en le déployant sur des appareils grâce à des conteneurs standard et en le surveillant à partir du cloud.

L’analytique apporte de la valeur métier dans les solutions IoT, mais toutes les analyses ne doivent pas se trouver sur le cloud. Si vous souhaitez qu’un périphérique réponde aux situations d’urgence aussi rapidement que possible, vous pouvez utiliser la détection d’anomalies sur le périphérique lui-même. De même, si vous souhaitez réduire les coûts de bande passante et éviter de transférer des téraoctets de données brutes, vous pouvez effectuer l’agrégation et le nettoyage des données localement. Vous envoyez ensuite les informations sur le cloud. 

Azure IoT Edge est constitué de trois composants :
* Les modules IoT Edge sont des conteneurs qui exécutent les services Azure, les services tiers ou votre propre code. Ils sont déployés sur des appareils IoT Edge et s’exécutent localement sur ces appareils. 
* Le runtime IoT Edge s’exécute sur chaque périphérique IoT Edge et gère les modules déployés sur chaque périphérique. 
* Une interface basée sur le cloud permet de surveiller et de gérer des périphériques IoT Edge à distance.

## <a name="iot-edge-modules"></a>Modules IoT Edge

Les modules IoT Edge sont des unités d’exécution, actuellement implémentés en tant que conteneurs compatibles Docker, qui exécutent votre logique métier en périphérie. Plusieurs modules peuvent être configurés pour communiquer entre eux, créant ainsi un pipeline de traitement des données. Vous pouvez développer des modules personnalisés ou créer des packages de certains services Azure dans des modules qui fournissent des analyses en mode hors connexion et en périphérie. 

### <a name="artificial-intelligence-on-the-edge"></a>Intelligence artificielle en périphérie

Azure IoT Edge vous permet de déployer des IA de traitement des événements complexes (CEP), d’apprentissage automatique, de reconnaissance d’images et d’autres IA de valeur élevée sans enregistrement en interne. Les services Azure comme Azure Functions, Azure Stream Analytics et Azure Machine Learning peuvent tous être exécutés en local via Azure IoT Edge. Toutefois, vous n’êtes pas limité aux services Azure. Toute personne peut créer des modules IA et rendre leur utilisation possible par la communauté. 

### <a name="bring-your-own-code"></a>Apportez votre propre code

Azure IoT Edge prend également en charge le déploiement de votre propre code sur vos périphériques. Azure IoT Edge fonctionne sur le même modèle de programmation que les autres services Azure IoT. Le même code peut être exécuté sur un appareil ou dans le cloud. Azure IoT Edge prend en charge Linux et Windows, vous pouvez donc coder sur la plateforme de votre choix. Il prend en charge Java, .NET Core 2.0, Node.js, C et Python, vos développeurs peuvent ainsi coder dans un langage qu’ils connaissent déjà et utiliser la logique métier existante sans avoir à l’écrire à partir de zéro.

## <a name="iot-edge-runtime"></a>Runtime IoT Edge

Le runtime Azure IoT Edge active les logiques cloud et personnalisées sur les périphériques IoT Edge. Il se trouve sur l’appareil IoT Edge et effectue des opérations de gestion et de communication. Le runtime effectue plusieurs fonctions :

* Il installe et met à jour des charges de travail sur l’appareil.
* Il gère les normes de sécurité Azure IoT Edge sur l’appareil.
* Il garantit que les modules IoT Edge sont toujours en cours d’exécution.
* Il envoie des rapports d’intégrité du module dans le cloud pour la surveillance à distance.
* Il facilite la communication entre les appareils feuilles en aval et le périphérique IoT Edge.
* Il facilite la communication entre les modules et le périphérique IoT Edge.
* Il facilite la communication entre le périphérique IoT Edge et le cloud.

![Le runtime de IoT Edge envoie des informations et des rapports à IoT Hub][1]

Les choix liés à l’utilisation d’un appareil Azure IoT Edge vous reviennent entièrement. Le runtime est souvent utilisé pour déployer des IA sur des passerelles qui agrègent des données et les traitent à partir de plusieurs autres appareils locaux, mais ce n’est qu’une option. Les appareils feuilles peuvent également être des appareils Azure IoT Edge, qu’ils soient connectés à une passerelle ou directement dans le cloud.

Le runtime Azure IoT Edge s’exécute sur un grand nombre d’appareils IoT pour permettre l’utilisation du runtime de plusieurs façons. Il prend en charge les systèmes d’exploitation Linux et Windows ainsi que des détails matériels abstraits. Utilisez un périphérique plus petit qu’un Raspberry Pi 3, si vous ne traitez pas de grands volumes de données ou montez en puissance en optant pour un serveur industrialisé permettant d’exécuter des charges de travail gourmandes en ressources.

## <a name="iot-edge-cloud-interface"></a>Interface cloud IoT Edge

La gestion du cycle de vie des logiciels pour les appareils d’entreprise est complexe. Il est encore plus difficile de gérer le cycle de vie des logiciels pour des millions d’appareils IoT hétérogènes. Les charges de travail doivent être créées et configurées pour un type particulier de périphérique, déployées à l’échelle pour les millions d’appareils dans votre solution et surveillées pour intercepter tous les périphériques présentant un dysfonctionnement. Ces activités ne peuvent pas être effectuées sur chaque appareil et doivent être appliquées à grande échelle.

Azure IoT Edge s’intègre parfaitement avec Azure IoT Suite pour fournir un plan de contrôle pour les besoins de votre solution. Les services cloud permettent aux utilisateurs de :

* Créer et configurer une charge de travail à exécuter sur un type d’appareil spécifique.
* Envoyer une charge de travail à un ensemble d’appareils.
* Analyser les charges de travail en cours d’exécution sur des périphériques dans le champ.

![Télémétrie, informations et les actions d’appareils sont coordonnées avec le cloud][2]

## <a name="next-steps"></a>Étapes suivantes

Essayez ces concepts en [déployant IoT Edge sur un appareil simulé][lnk-quickstart].

<!-- Images -->
[1]: ./media/how-iot-edge-works/runtime.png
[2]: ./media/how-iot-edge-works/cloud-interface.png

<!-- Links -->
[lnk-quickstart]: quickstart.md