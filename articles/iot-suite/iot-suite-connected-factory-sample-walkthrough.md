---
title: "Procédure pas à pas pour la solution d’usine connectée Azure IoT Suite | Microsoft Docs"
description: "Description de la solution préconfigurée d’usine connectée Azure IoT et de son architecture."
services: 
suite: iot-suite
documentationcenter: 
author: dominicbetts
manager: timlt
editor: 
ms.assetid: 31fe13af-0482-47be-b4c8-e98e36625855
ms.service: iot-suite
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 05/08/2017
ms.author: dobett
ms.translationtype: Human Translation
ms.sourcegitcommit: c785ad8dbfa427d69501f5f142ef40a2d3530f9e
ms.openlocfilehash: 3011fd608ba83561c319e57c8a7b5a4f3c4c2284
ms.contentlocale: fr-fr
ms.lasthandoff: 05/26/2017


---
# <a name="connected-factory-preconfigured-solution-walkthrough"></a>Procédure pas à pas de la solution préconfigurée d’usine connectée

La [solution préconfigurée][lnk-preconfigured-solutions] d’usine connectée IoT Suite est une implémentation d’une solution industrielle de bout en bout qui :

* Se connecte à la fois aux appareils industriels simulés fonctionnant sur des serveurs OPC UA dans des lignes de production simulées et aux appareils fonctionnant sur des serveurs OPC UA réels. Pour plus d’informations sur l’OPC UA, consultez le [FAQ][lnk-faq].
* Affiche les indicateurs de performance clé (KPI) opérationnels et les OEE de ces appareils et des lignes de production.
* Montre comment une application basée sur le cloud peut être utilisée pour interagir avec les systèmes de serveur OPC UA.
* Vous permet de connecter vos propres appareils de serveur OPC UA.
* Vous permet de parcourir et de modifier les données de serveur OPC UA.
* S’intègre avec le service Azure Time Series Insights (TSI) pour fournir des vues personnalisées des données à partir de vos serveurs OPC UA.

Vous pouvez utiliser la solution comme point de départ pour votre propre implémentation et la [personnaliser] [ lnk-customize] pour répondre à vos propres exigences professionnelles.

Cet article vous familiarise avec les éléments clés de la solution d’usine connectée pour vous permettre de comprendre son fonctionnement. Ces connaissances vous aident à :

* Résoudre les problèmes dans la solution.
* Adapter la solution à vos besoins professionnels.
* Concevoir votre propre solution IoT utilisant des services Azure.

## <a name="logical-architecture"></a>Architecture logique

Le schéma suivant décrit les composants logiques de la solution préconfigurée :

![Architecture logique d’usine connectée][connected-factory-logical]

## <a name="simulation"></a>Simulation

Les postes et les systèmes d'exécution de la fabrication (MES) simulés constituent une chaîne de fabrication d’usine. Les appareils simulés et le module Éditeur d’OPC sont basés sur le [Standard OPC UA .NET][lnk-OPC-UA-NET-Standard] publié par la Fondation OPC.

Le Proxy OPC et le module Éditeur d’OPC sont implémentés en tant que modules basés sur [Azure IoT Edge][lnk-Azure-IoT-Gateway]. Chaque ligne de production simulée dispose d’une passerelle désignée associée.

Tous les composants de simulation sont exécutés dans des conteneurs Docker hébergés dans une machine virtuelle Linux Azure. La simulation est configurée pour exécuter huit lignes de production simulées par défaut.

## <a name="simulated-production-line"></a>Ligne de production simulée

Une ligne de production fabrique des parties. Elle est composée de différents postes : un poste d’assembly, un poste de test et un poste de packaging.

La simulation s’exécute et met à jour les données exposées via les nœuds OPC UA. Toutes les stations de ligne de production simulées sont orchestrées par le MES via OPC UA.

## <a name="simulated-manufacturing-execution-system"></a>Simulation de système d’exécution de fabrication

Le MES analyse chaque poste dans la ligne de production via OPC UA pour détecter les modifications d’état des postes. Il appelle des méthodes OPC UA pour contrôler les stations et transmet les produits d’un poste à l’autre jusqu'à la fin de l’opération.

## <a name="gateway-opc-publisher-module"></a>Module Éditeur d’OPC de la passerelle

Le module Éditeur d’OPC se connecte aux serveurs de poste OPC UA et s’abonne aux nœuds OPC à publier. Le module convertit les données de nœud au format JSON, les chiffre et les envoie au IoT Hub sous forme de messages OPC UA Pub/Sub.

Le module Éditeur d’OPC nécessite un port sortant https (443) uniquement et fonctionne avec l’infrastructure d’entreprise existante.

## <a name="gateway-opc-proxy-module"></a>Module proxy OPC de la passerelle

Le module proxy OPC UA de la passerelle « tunnele » les messages OPC UA binaires de commande et de contrôle et nécessite seulement un port sortant https (443). Il est compatible avec l’infrastructure d’entreprise existante, y compris les proxys Web.

Il utilise des méthodes d’appareil IoT Hub pour transférer des données TCP/IP en paquets à la couche d’application et garantit ainsi l’approbation du point de terminaison, le chiffrement des données et l’intégrité à l’aide de SSL/TLS.

Le protocole binaire OPC UA relayé via le proxy utilise le chiffrement et l’authentification UA.

## <a name="azure-time-series-insights"></a>Azure Time Series Insights

Le module Éditeur d’OPC de la passerelle s’abonne à des nœuds de serveur OPC UA pour détecter des modifications dans les valeurs de données. Si une modification de données est détectée dans un des nœuds, ce module envoie des messages à Azure IoT Hub.

IoT Hub fournit une source d’événements à Azure STI. STI stocke les données durant 30 jours en fonction des horodatages associés messages. Ces données incluent :

* OPC UA ApplicationUri
* OPC UA NodeId
* Valeur du nœud
* Source timestamp
* OPC UA DisplayName

Actuellement, STI n’autorise pas les clients à personnaliser la durée pendant laquelle ils souhaitent conserver les données.

Requêtes de STI sur les données de nœud à l’aide d’un SearchSpan (Time.From, Time.To) et des agrégats par OPC UA ApplicationUri ou OPC UA NodeId ou OPC UA DisplayName.

Pour récupérer les données pour les jauges OEE et KPI et les graphiques de séries chronologiques, les données sont agrégées par nombre d’événements, Sum, Avg, Min et Max.

Les séries chronologiques sont créées à l’aide d’un processus différent. Les OOE et les KPI sont calculées à partir des données de base des postes et propagées pour la topologie (lignes de production, usines, entreprise) dans l’application.

En outre, les séries chronologiques pour la topologie OEE et KPI sont calculées dans l’application, chaque fois qu’un intervalle de temps affiché est prêt. Par exemple, l’affichage quotidien est mis à jour chaque heure complète.

L’affichage de séries chronologiques des données de nœud provient directement de STI à l’aide d’une agrégation pour l’intervalle de temps.

## <a name="iot-hub"></a>IoT Hub
[IoT Hub][lnk-IoT Hub] reçoit les données envoyées par le module Éditeur d’OPC au cloud et les rend disponibles au service Azure TSI. 

L’instance IoT Hub de la solution effectue également ce qui suit :
- Gère un registre d’identité qui stocke les ID des modules Éditeur d’OPC et de tous les modules de Proxy OPC.
- Est utilisé en tant que canal de transport pour la communication bidirectionnelle du module Proxy OPC.

## <a name="azure-storage"></a>Azure Storage
La solution utilise le stockage d’objets blob Azure comme stockage sur disque pour la machine virtuelle et pour stocker les données de déploiement.

## <a name="web-app"></a>Application web
L’application web déployée dans le cadre de la solution préconfigurée comprend un client OPC UA intégré, un système de traitement des alertes et de visualisation de télémétrie.

## <a name="next-steps"></a>Étapes suivantes

Vous pouvez poursuivre la prise en main d’IoT Suite en lisant les articles suivants :

* [Autorisations sur le site azureiotsuite.com][lnk-permissions]

[connected-factory-logical]:media/iot-suite-connected-factory-walkthrough/cf-logical-architecture.png

[lnk-preconfigured-solutions]: iot-suite-what-are-preconfigured-solutions.md
[lnk-customize]: iot-suite-guidance-on-customizing-preconfigured-solutions.md
[lnk-IoT Hub]: https://azure.microsoft.com/documentation/services/iot-hub/
[lnk-direct-methods]: ../iot-hub/iot-hub-devguide-direct-methods.md
[lnk-OPC-UA-NET-Standard]:https://github.com/OPCFoundation/UA-.NETStandardLibrary
[lnk-Azure-IoT-Gateway]: https://github.com/azure/iot-edge
[lnk-permissions]: iot-suite-permissions.md
[lnk-faq]: iot-suite-faq.md
