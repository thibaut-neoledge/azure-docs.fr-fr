---
title: "Haute disponibilité et récupération d’urgence Azure IoT Hub | Microsoft Docs"
description: "Décrit les options Azure et IoT Hub qui vous aident à créer des solutions Azure IoT à haute disponibilité dotées de fonctionnalités de récupération d’urgence."
services: iot-hub
documentationcenter: 
author: fsautomata
manager: timlt
editor: 
ms.assetid: ae320e58-aa20-45b9-abdc-fa4faae8e6dd
ms.service: iot-hub
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 11/16/2016
ms.author: elioda
translationtype: Human Translation
ms.sourcegitcommit: 64e69df256404e98f6175f77357500b562d74318
ms.openlocfilehash: 76c3187549e1821908263c30e394db26ee6f75e6


---
# <a name="iot-hub-high-availability-and-disaster-recovery"></a>Haute disponibilité et récupération d’urgence IoT Hub :
En tant que service Azure, IoT Hub offre la haute disponibilité à l’aide de redondances au niveau de la région Azure, sans aucun travail supplémentaire requis par la solution. La plateforme Microsoft Azure inclut également des fonctionnalités pour vous aider à générer des solutions offrant des fonctionnalités de récupération d’urgence ou une disponibilité inter-régions. Si vous voulez fournir une haute disponibilité globale inter-régions aux appareils et aux utilisateurs, vous devez concevoir et préparer vos solutions de manière à tirer parti de ces fonctionnalités de récupération d’urgence d’Azure. L’article [Guide technique de la résilience Azure](../resiliency/resiliency-technical-guidance.md) décrit les fonctionnalités intégrées Azure permettant la continuité d’activité et la récupération d’urgence. Le document [Récupération d’urgence et haute disponibilité pour les applications Azure][Disaster recovery and high availability for Azure applications] contient des recommandations d’architecture concernant les stratégies pour les applications Azure, permettant de bénéficier de la haute disponibilité et de la récupération d’urgence.

## <a name="azure-iot-hub-dr"></a>Récupération d’urgence Azure IoT Hub
En plus de la haute disponibilité intra-région, IoT Hub implémente des mécanismes de basculement pour la récupération d’urgence qui ne nécessitent aucune intervention de l’utilisateur. La récupération d’urgence IoT Hub est lancée automatiquement et a un objectif de temps de récupération (RTO) de 2 à 26 heures, ainsi que les objectifs de point de récupération (RPO) suivants.

| Fonctionnalités | RPO |
| --- | --- |
| Disponibilité du service pour les opérations de Registre et de communication |Perte éventuelle du CName |
| Données d’identité dans le registre des identités |Perte de données entre&0; et&5; minutes |
| Messages appareil-à-cloud |Perte de tous les messages non lus |
| Messages de surveillance des opérations |Perte de tous les messages non lus |
| Messages cloud-à-appareil |Perte de données entre&0; et&5; minutes |
| File d’attente de rétroaction cloud-à-appareil |Perte de tous les messages non lus |

## <a name="regional-failover-with-iot-hub"></a>Basculement régional avec IoT Hub
Un traitement complet des topologies de déploiement dans des solutions IoT dépasserait la portée de cet article. L’article traite du modèle de déploiement avec *basculement régional* pour les besoins de haute disponibilité et de récupération d’urgence.

Dans un modèle de basculement régional, le serveur principal de solution s’exécute essentiellement dans un emplacement de centre de données, tandis qu’un IoT Hub et un serveur secondaires sont déployés dans un autre emplacement de centre de données. Si l’IoT Hub du centre de données principal subit une panne ou si la connectivité réseau de l’appareil au centre de données principal est interrompue. Les appareils utilisent un point de terminaison secondaire à chaque fois que la passerelle principale ne peut pas être atteinte. Grâce à la capacité de basculement entre régions, il est possible d’améliorer la disponibilité de la solution au-delà de la haute disponibilité au niveau d’une seule région.

À un niveau supérieur, pour implémenter un modèle de basculement régional avec IoT Hub, vous avez besoin des éléments suivants :

* **Un hub IoT secondaire et un appareil logique de routage secondaire**: en cas d’interruption de service dans votre région principale, les appareils doivent commencer à se connecter à votre région secondaire. Étant donné l’état de la plupart des services impliqués, il est courant pour les administrateurs de solution de déclencher le processus de basculement inter-régions. La meilleure façon de communiquer le nouveau point de terminaison aux appareils tout en conservant le contrôle du processus consiste à consulter régulièrement un service de *conciergerie* pour connaître le point de terminaison actif en cours. Le service de concierge peut être une application web répliquée accessible à l’aide de techniques de redirection DNS (par exemple, l’utilisation d’[Azure Traffic Manager][Azure Traffic Manager]).
* **Réplication du registre des identités** : pour être utilisable, l’IoT Hub secondaire doit contenir toutes les identités des appareils pouvant se connecter à la solution. La solution doit conserver des sauvegardes géo-répliquées d’identités d’appareils et les télécharger dans le hub IoT secondaire avant de basculer le point de terminaison actif des appareils. La fonctionnalité d’exportation des identités d’appareil d’IoT Hub est utile dans ce contexte. Pour plus d’informations, consultez[Guide du développeur IoT Hub - Registre des identités][IoT Hub developer guide - identity registry].
* **Fusion logique** : lorsque la région principale redevient disponible, l’ensemble des états et des données qui ont été créés dans le site secondaire doit revenir à la région primaire. Ces états et ces données ont essentiellement trait aux identités des appareils et aux métadonnées d’application qui doivent être fusionnées avec l’IoT Hub principal et d’autres magasins spécifiques de l’application dans la région primaire. Pour simplifier cette étape, vous devez utiliser des opérations idempotentes. Des opérations idempotentes minimisent les effets indésirables de la distribution cohérente éventuelle d’événements, et des doublons ou des livraisons d’événements hors d’usage. En outre, la logique d’application doit être conçue pour tolérer les éventuelles incohérences ou de légers retards. Cette situation peut se produire en raison du temps additionnel que le système prend pour se réparer en fonction des objectifs de points de récupération (RPO).

## <a name="next-steps"></a>Étapes suivantes
Suivez ces liens pour en savoir plus sur Azure IoT Hub :

* [Prise en main des IoT Hubs (didacticiel)][lnk-get-started]
* [Qu’est-ce qu’Azure IoT Hub ?][What is Azure IoT Hub?]

[Disaster recovery and high availability for Azure applications]: ../resiliency/resiliency-disaster-recovery-high-availability-azure-applications.md
[Azure Business Continuity Technical Guidance]: https://azure.microsoft.com/documentation/articles/resiliency-technical-guidance/
[Azure Traffic Manager]: https://azure.microsoft.com/documentation/services/traffic-manager/
[IoT Hub developer guide - identity registry]: iot-hub-devguide-identity-registry.md

[lnk-get-started]: iot-hub-csharp-csharp-getstarted.md
[What is Azure IoT Hub?]: iot-hub-what-is-iot-hub.md



<!--HONumber=Jan17_HO4-->


