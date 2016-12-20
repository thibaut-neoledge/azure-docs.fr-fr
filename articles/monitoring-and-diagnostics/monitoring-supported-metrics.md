---
title: "Mesures Azure Monitor : mesures prises en charge par type de ressource | Microsoft Docs"
description: Liste des mesures disponibles pour chaque type de ressource avec Azure Monitor.
author: johnkemnetz
manager: rboucher
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 63d4ac65-1688-40d1-85c8-7cd408285b0f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/26/2016
ms.author: johnkem
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: fd07342dad07e70a09f372c9c6c116376630e6f8


---
# <a name="supported-metrics-with-azure-monitor"></a>Mesures prises en charge avec Azure Monitor
Azure Monitor offre plusieurs moyens d’interagir avec les mesures, y compris en créant des graphiques dans le portail, en y accédant via l’API REST ou en envoyant des requêtes avec PowerShell ou l’interface CLI. Voici une liste complète de toutes les mesures actuellement offertes par le pipeline de mesure d’Azure Monitor.

> [!NOTE]
> D’autres mesures peuvent être disponibles dans le portail ou via les API héritées. Cette liste ne comprend que les mesures disponibles dans la version préliminaire publique du pipeline de mesures Azure Monitor consolidé.
> 
> 

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts
| Mesure | Nom d’affichage de la mesure | Unité | Type d’agrégation | Description |
| --- | --- | --- | --- | --- |
| CoreCount |Nombre de cœurs |Nombre |Total |Nombre total de cœurs dans le compte Batch |
| TotalNodeCount |Nombre de nœuds |Nombre |Total |Nombre total de nœuds dans le compte Batch |
| CreatingNodeCount |Nombre de nœuds créés |Nombre |Total |Nombre de nœuds en cours de création |
| StartingNodeCount |Nombre de nœuds de départ |Nombre |Total |Nœuds de nœuds de départ |
| WaitingForStartTaskNodeCount |Nombre de nœuds en attente de démarrage de tâche |Nombre |Total |Nombre de nœuds en attente de la fin d’une tâche de démarrage |
| StartTaskFailedNodeCount |Nombre de nœuds pour lesquels le démarrage d’une tâche a échoué |Nombre |Total |Le nombre de nœuds pour lesquels le démarrage d’une tâche a échoué |
| IdleNodeCount |Nombre de nœuds inactifs |Nombre |Total |Le nombre de nœuds inactifs |
| OfflineNodeCount |Nombre de nœuds hors ligne |Nombre |Total |Le nombre de nœuds hors ligne |
| RebootingNodeCount |Nombre de nœuds en cours de redémarrage |Nombre |Total |Le nombre de nœuds en cours de redémarrage |
| ReimagingNodeCount |Nombre de nœuds en cours de réimageage |Nombre |Total |Le nombre de nœuds en cours de réimageage |
| RunningNodeCount |Nombre de nœuds en cours d’exécution |Nombre |Total |Le nombre de nœuds en cours d’exécution |
| LeavingPoolNodeCount |Nombre de nœuds quittant le pool |Nombre |Total |Le nombre de nœuds quittant le pool |
| UnusableNodeCount |Nombre de nœuds inutilisables |Nombre |Total |Le nombre de nœuds inutilisables |
| TaskStartEvent |Événements de lancement de tâche |Nombre |Total |Nombre total de tâches ayant démarré |
| TaskCompleteEvent |Événements de fin de tâche |Nombre |Total |Le nombre total de tâches terminées |
| TaskFailEvent |Événements d’échec de tâches |Nombre |Total |Le nombre total de tâches terminées dans un état d’échec |
| PoolCreateEvent |Événements de création de pool |Nombre |Total |Nombre total de pools créés |
| PoolResizeStartEvent |Événements de démarrage de redimensionnement de pool |Nombre |Total |Nombre total de redimensionnements de pool ayant démarré |
| PoolResizeCompleteEvent |Événements de fin de redimensionnement de pool |Nombre |Total |Nombre total de redimensionnements de pool terminés |
| PoolDeleteStartEvent |Événements de démarrage de suppression de pool |Nombre |Total |Nombre total de suppressions de pool ayant démarré |
| PoolDeleteCompleteEvent |Événements de suppression de pool terminés |Nombre |Total |Nombre total de suppressions de pool terminées |

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis
| Mesure | Nom d’affichage de la mesure | Unité | Type d’agrégation | Description |
| --- | --- | --- | --- | --- |
| connectedclients |Clients connectés |Nombre |Maximale | |
| totalcommandsprocessed |Total des opérations |Nombre |Total | |
| cachehits |Présences dans le cache |Nombre |Total | |
| cachemisses |Absences dans le cache |Nombre |Total | |
| getcommands |Gets |Nombre |Total | |
| setcommands |Sets |Nombre |Total | |
| evictedkeys |Clés exclues |Nombre |Total | |
| totalkeys |Nombre total de clés |Nombre |Maximale | |
| expiredkeys |Clés expirées |Nombre |Total | |
| usedmemory |Mémoire utilisée |Octets |Maximale | |
| usedmemoryRss |Taille de la mémoire résidente utilisée |Octets |Maximale | |
| serverLoad |Charge du serveur |Pourcentage |Maximale | |
| cacheWrite |Cache d’écriture |Octets par seconde |Maximale | |
| cacheRead |Lecture du cache |Octets par seconde |Maximale | |
| percentProcessorTime |UC |Pourcentage |Maximale | |
| connectedclients0 |Clients connectés (Shard 0) |Nombre |Maximale | |
| totalcommandsprocessed0 |Total des opérations (Shard 0) |Nombre |Total | |
| cachehits0 |Présences dans le cache (Shard 0) |Nombre |Total | |
| cachemisses0 |Absences dans le cache (Shard 0) |Nombre |Total | |
| getcommands0 |Gets (Shard 0) |Nombre |Total | |
| setcommands0 |Sets (Shard 0) |Nombre |Total | |
| evictedkeys0 |Clés exclues (Shard 0) |Nombre |Total | |
| totalkeys0 |Nombre total de clés (Nœud 0) |Nombre |Maximale | |
| expiredkeys0 |Clés expirées (Shard 0) |Nombre |Total | |
| usedmemory0 |Mémoire utilisée (Shard 0) |Octets |Maximale | |
| usedmemoryRss0 |Mémoire résidente utilisée (Shard 0) |Octets |Maximale | |
| serverLoad0 |Charge du serveur (Shard 0) |Pourcentage |Maximale | |
| cacheWrite0 |Cache d’écriture (Shard 0) |Octets par seconde |Maximale | |
| cacheRead0 |Cache de lecture (Shard 0) |Octets par seconde |Maximale | |
| percentProcessorTime0 |UC (Shard 0) |Pourcentage |Maximale | |
| connectedclients1 |Clients connectés (Shard 1) |Nombre |Maximale | |
| totalcommandsprocessed1 |Total des opérations (Shard 1) |Nombre |Total | |
| cachehits1 |Présences dans le cache (Shard 1) |Nombre |Total | |
| cachemisses1 |Absences dans le cache (Shard 1) |Nombre |Total | |
| getcommands1 |Gets (Shard 1) |Nombre |Total | |
| setcommands1 |Sets (Shard 1) |Nombre |Total | |
| evictedkeys1 |Clés exclues (Shard 1) |Nombre |Total | |
| totalkeys1 |Nombre total de clés (Nœud 1) |Nombre |Maximale | |
| expiredkeys1 |Clés expirées (Shard 1) |Nombre |Total | |
| usedmemory1 |Mémoire utilisée (Shard 1) |Octets |Maximale | |
| usedmemoryRss1 |Mémoire résidente utilisée (Shard 1) |Octets |Maximale | |
| serverLoad1 |Charge du serveur (Shard 1) |Pourcentage |Maximale | |
| cacheWrite1 |Cache d’écriture (Shard 1) |Octets par seconde |Maximale | |
| cacheRead1 |Lecture du cache (Shard 1) |Octets par seconde |Maximale | |
| percentProcessorTime1 |UC (Shard 1) |Pourcentage |Maximale | |
| connectedclients2 |Clients connectés (Shard 2) |Nombre |Maximale | |
| totalcommandsprocessed2 |Total des opérations (Shard 2) |Nombre |Total | |
| cachehits2 |Présences dans le cache (Shard 2) |Nombre |Total | |
| cachemisses2 |Absences dans le cache (Shard 2) |Nombre |Total | |
| getcommands2 |Gets (Shard 2) |Nombre |Total | |
| setcommands2 |Sets (Shard 2) |Nombre |Total | |
| evictedkeys2 |Clés exclues (Shard 2) |Nombre |Total | |
| totalkeys2 |Nombre total de clés (Nœud 2) |Nombre |Maximale | |
| expiredkeys2 |Clés expirées (Shard 2) |Nombre |Total | |
| usedmemory2 |Mémoire utilisée (Shard 2) |Octets |Maximale | |
| usedmemoryRss2 |Mémoire résidente utilisée (Shard 2) |Octets |Maximale | |
| serverLoad2 |Charge du serveur (Shard 2) |Pourcentage |Maximale | |
| cacheWrite2 |Cache d’écriture (Shard 2) |Octets par seconde |Maximale | |
| cacheRead2 |Lecture du cache (Shard 2) |Octets par seconde |Maximale | |
| percentProcessorTime2 |UC (Shard 2) |Pourcentage |Maximale | |
| connectedclients3 |Clients connectés (Shard 3) |Nombre |Maximale | |
| totalcommandsprocessed3 |Total des opérations (Shard 3) |Nombre |Total | |
| cachehits3 |Présences dans le cache (Shard 3) |Nombre |Total | |
| cachemisses3 |Absences dans le cache (Shard 3) |Nombre |Total | |
| getcommands3 |Gets (Shard 3) |Nombre |Total | |
| setcommands3 |Sets (Shard 3) |Nombre |Total | |
| evictedkeys3 |Clés exclues (Shard 3) |Nombre |Total | |
| totalkeys3 |Nombre total de clés (Nœud 3) |Nombre |Maximale | |
| expiredkeys3 |Clés expirées (Shard 3) |Nombre |Total | |
| usedmemory3 |Mémoire utilisée (Shard 3) |Octets |Maximale | |
| usedmemoryRss3 |Mémoire résidente utilisée (Shard 3) |Octets |Maximale | |
| serverLoad3 |Charge du serveur (Shard 3) |Pourcentage |Maximale | |
| cacheWrite3 |Cache d’écriture (Shard 3) |Octets par seconde |Maximale | |
| cacheRead3 |Lecture du cache (Shard 3) |Octets par seconde |Maximale | |
| percentProcessorTime3 |UC (Shard 3) |Pourcentage |Maximale | |
| connectedclients4 |Clients connectés (Shard 4) |Nombre |Maximale | |
| totalcommandsprocessed4 |Total des opérations (Shard 4) |Nombre |Total | |
| cachehits4 |Présences dans le cache (Shard 4) |Nombre |Total | |
| cachemisses4 |Absences dans le cache (Shard 4) |Nombre |Total | |
| getcommands4 |Gets (Shard 4) |Nombre |Total | |
| setcommands4 |Sets (Shard 4) |Nombre |Total | |
| evictedkeys4 |Clés exclues (Shard 4) |Nombre |Total | |
| totalkeys4 |Nombre total de clés (Nœud 4) |Nombre |Maximale | |
| expiredkeys4 |Clés expirées (Shard 4) |Nombre |Total | |
| usedmemory4 |Mémoire utilisée (Shard 4) |Octets |Maximale | |
| usedmemoryRss4 |Mémoire résidente utilisée (Shard 4) |Octets |Maximale | |
| serverLoad4 |Charge du serveur (Shard 4) |Pourcentage |Maximale | |
| cacheWrite4 |Cache d’écriture (Shard 4) |Octets par seconde |Maximale | |
| cacheRead4 |Lecture du cache (Shard 4) |Octets par seconde |Maximale | |
| percentProcessorTime4 |UC (Shard 4) |Pourcentage |Maximale | |
| connectedclients5 |Clients connectés (Shard 5) |Nombre |Maximale | |
| totalcommandsprocessed5 |Total des opérations (Shard 5) |Nombre |Total | |
| cachehits5 |Présences dans le cache (Shard 5) |Nombre |Total | |
| cachemisses5 |Absences dans le cache (Shard 5) |Nombre |Total | |
| getcommands5 |Gets (Shard 5) |Nombre |Total | |
| setcommands5 |Sets (Shard 5) |Nombre |Total | |
| evictedkeys5 |Clés exclues (Shard 5) |Nombre |Total | |
| totalkeys5 |Nombre total de clés (Nœud 5) |Nombre |Maximale | |
| expiredkeys5 |Clés expirées (Shard 5) |Nombre |Total | |
| usedmemory5 |Mémoire utilisée (Shard 5) |Octets |Maximale | |
| usedmemoryRss5 |Mémoire résidente utilisée (Shard 5) |Octets |Maximale | |
| serverLoad5 |Charge du serveur (Shard 5) |Pourcentage |Maximale | |
| cacheWrite5 |Cache d’écriture (Shard 5) |Octets par seconde |Maximale | |
| cacheRead5 |Lecture du cache (Shard 5) |Octets par seconde |Maximale | |
| percentProcessorTime5 |UC (Shard 5) |Pourcentage |Maximale | |
| connectedclients6 |Clients connectés (Shard 6) |Nombre |Maximale | |
| totalcommandsprocessed6 |Total des opérations (Shard 6) |Nombre |Total | |
| cachehits6 |Présences dans le cache (Shard 6) |Nombre |Total | |
| cachemisses6 |Absences dans le cache (Shard 6) |Nombre |Total | |
| getcommands6 |Gets (Shard 6) |Nombre |Total | |
| setcommands6 |Sets (Shard 6) |Nombre |Total | |
| evictedkeys6 |Clés exclues (Shard 6) |Nombre |Total | |
| totalkeys6 |Nombre total de clés (Nœud 6) |Nombre |Maximale | |
| expiredkeys6 |Clés expirées (Shard 6) |Nombre |Total | |
| usedmemory6 |Mémoire utilisée (Shard 6) |Octets |Maximale | |
| usedmemoryRss6 |Mémoire résidente utilisée (Shard 6) |Octets |Maximale | |
| serverLoad6 |Charge du serveur (Shard 6) |Pourcentage |Maximale | |
| cacheWrite6 |Cache d’écriture (Shard 6) |Octets par seconde |Maximale | |
| cacheRead6 |Lecture du cache (Shard 6) |Octets par seconde |Maximale | |
| percentProcessorTime6 |UC (Shard 6) |Pourcentage |Maximale | |
| connectedclients7 |Clients connectés (Shard 7) |Nombre |Maximale | |
| totalcommandsprocessed7 |Total des opérations (Shard 7) |Nombre |Total | |
| cachehits7 |Présences dans le cache (Shard 7) |Nombre |Total | |
| cachemisses7 |Absences dans le cache (Shard 7) |Nombre |Total | |
| getcommands7 |Gets (Shard 7) |Nombre |Total | |
| setcommands7 |Sets (Shard 7) |Nombre |Total | |
| evictedkeys7 |Clés exclues (Shard 7) |Nombre |Total | |
| totalkeys7 |Nombre total de clés (Nœud 7) |Nombre |Maximale | |
| expiredkeys7 |Clés expirées (Shard 7) |Nombre |Total | |
| usedmemory7 |Mémoire utilisée (Shard 7) |Octets |Maximale | |
| usedmemoryRss7 |Mémoire résidente utilisée (Shard 7) |Octets |Maximale | |
| serverLoad7 |Charge du serveur (Shard 7) |Pourcentage |Maximale | |
| cacheWrite7 |Cache d’écriture (Shard 7) |Octets par seconde |Maximale | |
| cacheRead7 |Lecture du cache (Shard 7) |Octets par seconde |Maximale | |
| percentProcessorTime7 |UC (Shard 7) |Pourcentage |Maximale | |
| connectedclients8 |Clients connectés (Shard 8) |Nombre |Maximale | |
| totalcommandsprocessed8 |Total des opérations (Shard 8) |Nombre |Total | |
| cachehits8 |Présences dans le cache (Shard 8) |Nombre |Total | |
| cachemisses8 |Absences dans le cache (Shard 8) |Nombre |Total | |
| getcommands8 |Gets (Shard 8) |Nombre |Total | |
| setcommands8 |Sets (Shard 8) |Nombre |Total | |
| evictedkeys8 |Clés exclues (Shard 8) |Nombre |Total | |
| totalkeys8 |Nombre total de clés (Nœud 8) |Nombre |Maximale | |
| expiredkeys8 |Clés expirées (Shard 8) |Nombre |Total | |
| usedmemory8 |Mémoire utilisée (Shard 8) |Octets |Maximale | |
| usedmemoryRss8 |Mémoire résidente utilisée (Shard 8) |Octets |Maximale | |
| serverLoad8 |Charge du serveur (Shard 8) |Pourcentage |Maximale | |
| cacheWrite8 |Cache d’écriture (Shard 8) |Octets par seconde |Maximale | |
| cacheRead8 |Lecture du cache (Shard 8) |Octets par seconde |Maximale | |
| percentProcessorTime8 |UC (Shard 8) |Pourcentage |Maximale | |
| connectedclients9 |Clients connectés (Shard 9) |Nombre |Maximale | |
| totalcommandsprocessed9 |Total des opérations (Shard 9) |Nombre |Total | |
| cachehits9 |Présences dans le cache (Shard 9) |Nombre |Total | |
| cachemisses9 |Absences dans le cache (Shard 9) |Nombre |Total | |
| getcommands9 |Gets (Shard 9) |Nombre |Total | |
| setcommands9 |Sets (Shard 9) |Nombre |Total | |
| evictedkeys9 |Clés exclues (Shard 9) |Nombre |Total | |
| totalkeys9 |Nombre total de clés (Nœud 9) |Nombre |Maximale | |
| expiredkeys9 |Clés expirées (Shard 9) |Nombre |Total | |
| usedmemory9 |Mémoire utilisée (Shard 9) |Octets |Maximale | |
| usedmemoryRss9 |Mémoire résidente utilisée (Shard 9) |Octets |Maximale | |
| serverLoad9 |Charge du serveur (Shard 9) |Pourcentage |Maximale | |
| cacheWrite9 |Cache d’écriture (Shard 9) |Octets par seconde |Maximale | |
| cacheRead9 |Cache de lecture (Shard 9) |Octets par seconde |Maximale | |
| percentProcessorTime9 |UC (Shard 9) |Pourcentage |Maximale | |

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts
| Mesure | Nom d’affichage de la mesure | Unité | Type d’agrégation | Description |
| --- | --- | --- | --- | --- |
| NumberOfCalls |Nombre total d’appels d’API |Nombre |Total |Nombre total d’appels d’API. |
| NumberOfFailedCalls |Nombre total d’appels d’API ayant échoué |Nombre |Total |Nombre total d’appels d’API ayant échoué. |

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines
| Mesure | Nom d’affichage de la mesure | Unité | Type d’agrégation | Description |
| --- | --- | --- | --- | --- |
| Pourcentage UC |Pourcentage UC |Pourcentage |Moyenne |Le pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles |
| Entrée réseau |Entrée réseau |Octets |Total |Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant) |
| Sortie réseau |Sortie réseau |Octets |Total |Le nombre d’octets envoyés sur toutes les interfaces réseau par les ordinateurs virtuels (trafic sortant) |
| Octets lus disque |Octets lus disque |Octets |Total |Total d’octets lus à partir du disque pendant la période d’analyse |
| Octets écrits disque |Octets écrits disque |Octets |Total |Total d’octets écrits sur le disque pendant la période d’analyse |
| Opérations de lecture disque/s |Opérations de lecture disque/s |Nombre par seconde |Moyenne |E/S de lecture disque par seconde |
| Opérations d’écriture disque/s |Opérations d’écriture disque/s |Nombre par seconde |Moyenne |E/S d’écriture disque par seconde |

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets
| Mesure | Nom d’affichage de la mesure | Unité | Type d’agrégation | Description |
| --- | --- | --- | --- | --- |
| Pourcentage UC |Pourcentage UC |Pourcentage |Moyenne |Le pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles |
| Entrée réseau |Entrée réseau |Octets |Total |Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant) |
| Sortie réseau |Sortie réseau |Octets |Total |Le nombre d’octets envoyés sur toutes les interfaces réseau par les ordinateurs virtuels (trafic sortant) |
| Octets lus disque |Octets lus disque |Octets |Total |Total d’octets lus à partir du disque pendant la période d’analyse |
| Octets écrits disque |Octets écrits disque |Octets |Total |Total d’octets écrits sur le disque pendant la période d’analyse |
| Opérations de lecture disque/s |Opérations de lecture disque/s |Nombre par seconde |Moyenne |E/S de lecture disque par seconde |
| Opérations d’écriture disque/s |Opérations d’écriture disque/s |Nombre par seconde |Moyenne |E/S d’écriture disque par seconde |

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines
| Mesure | Nom d’affichage de la mesure | Unité | Type d’agrégation | Description |
| --- | --- | --- | --- | --- |
| Pourcentage UC |Pourcentage UC |Pourcentage |Moyenne |Le pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles |
| Entrée réseau |Entrée réseau |Octets |Total |Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant) |
| Sortie réseau |Sortie réseau |Octets |Total |Le nombre d’octets envoyés sur toutes les interfaces réseau par les ordinateurs virtuels (trafic sortant) |
| Octets lus disque |Octets lus disque |Octets |Total |Total d’octets lus à partir du disque pendant la période d’analyse |
| Octets écrits disque |Octets écrits disque |Octets |Total |Total d’octets écrits sur le disque pendant la période d’analyse |
| Opérations de lecture disque/s |Opérations de lecture disque/s |Nombre par seconde |Moyenne |E/S de lecture disque par seconde |
| Opérations d’écriture disque/s |Opérations d’écriture disque/s |Nombre par seconde |Moyenne |E/S d’écriture disque par seconde |

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs
| Mesure | Nom d’affichage de la mesure | Unité | Type d’agrégation | Description |
| --- | --- | --- | --- | --- |
| d2c.telemetry.ingress.allProtocol |Tentatives d’envoi de message de télémétrie |Nombre |Total |Nombre de tentatives d’envoi de messages de télémétrie appareil vers cloud vers votre hub IoT |
| d2c.telemetry.ingress.success |Messages de télémétrie envoyés |Nombre |Total |Nombre de messages de télémétrie appareil vers cloud envoyés avec succès vers votre hub IoT |
| c2d.commands.egress.complete.success |Commandes terminées |Nombre |Total |Nombre de commandes cloud vers appareil terminées correctement par l’appareil |
| c2d.commands.egress.abandon.success |Commandes abandonnées |Nombre |Total |Nombre de commandes cloud vers appareil abandonnées par l’appareil |
| c2d.commands.egress.reject.success |Commandes rejetées |Nombre |Total |Nombre de commandes cloud vers appareil rejetées par l’appareil |
| devices.totalDevices |Nombre total d’appareils |Nombre |Total |Nombre d’appareils enregistrés sur votre hub IoT |
| devices.connectedDevices.allProtocol |Appareils connectés |Nombre |Total |Nombre d’appareils connectés à votre hub IoT |

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces
| Mesure | Nom d’affichage de la mesure | Unité | Type d’agrégation | Description |
| --- | --- | --- | --- | --- |
| INREQS |Demandes entrantes |Nombre |Total |Débit de messages entrants Event Hub pour un espace de noms |
| SUCCREQ |Requêtes ayant réussi |Nombre |Total |Nombre total de demandes ayant réussi pour un espace de noms |
| FAILREQ |Demandes ayant échoué |Nombre |Total |Nombre total de demandes ayant échoué pour un espace de noms |
| SVRBSY |Erreurs de serveur occupé |Nombre |Total |Nombre total d’erreurs de serveur occupé pour un espace de noms |
| INTERR |Erreurs internes du serveur |Nombre |Total |Nombre total d’erreurs de serveur internes pour un espace de noms |
| MISCERR |Autres erreurs |Nombre |Total |Nombre total de demandes ayant échoué pour un espace de noms |
| INMSGS |Messages entrants |Nombre |Total |Nombre total de messages entrants pour un espace de noms |
| OUTMSGS |Messages sortants |Nombre |Total |Nombre total de messages sortants pour un espace de noms |
| EHINMBS |Octets entrants par seconde |Octets par seconde |Total |Débit de messages entrants Event Hub pour un espace de noms |
| EHOUTMBS |Octets sortants par seconde |Octets par seconde |Total |Nombre total de messages sortants pour un espace de noms |
| EHABL |Archiver les messages de file d’attente |Nombre |Total |Messages d’archive Event Hub dans le backlog pour un espace de noms |
| EHAMSGS |Archiver les messages |Nombre |Total |Messages archivés Event Hub dans un espace de noms |
| EHAMBS |Débit message archive |Octets par seconde |Total |Débit de messages archivés Event Hub dans un espace de noms |

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows
| Mesure | Nom d’affichage de la mesure | Unité | Type d’agrégation | Description |
| --- | --- | --- | --- | --- |
| RunsStarted |Exécutions démarrées |Nombre |Total |Nombre d’exécutions de flux de travail démarrées. |
| RunsCompleted |Exécutions terminées |Nombre |Total |Nombre d’exécutions de flux de travail terminées. |
| RunsSucceeded |Exécutions réussies |Nombre |Total |Nombre d’exécutions de flux de travail ayant réussi. |
| RunsFailed |Exécutions ayant échoué |Nombre |Total |Nombre d’exécutions de flux de travail ayant échoué. |
| RunsCancelled |Exécutions annulées |Nombre |Total |Nombre d’exécutions de flux de travail annulées. |
| RunLatency |Latence d’exécution |Secondes |Moyenne |Latence des exécutions de flux de travail terminées. |
| RunSuccessLatency |Latence d’exécution ayant réussi |Secondes |Moyenne |Latence des exécutions de flux de travail ayant réussi. |
| RunThrottledEvents |Exécuter événements limités |Nombre |Total |Nombre d’actions de flux de travail ou d’événements limités par déclencheur. |
| RunFailurePercentage |Pourcentage d’échec des exécutions |Pourcentage |Total |Pourcentage d’exécutions de flux de travail ayant échoué. |
| ActionsStarted |Actions démarrées |Nombre |Total |Nombre d’actions de flux de travail démarrées. |
| ActionsCompleted |Actions terminées |Nombre |Total |Nombre d’actions de flux de travail terminées. |
| ActionsSucceeded |Actions ayant réussi |Nombre |Total |Nombre d’actions de flux de travail ayant réussi. |
| ActionsFailed |Actions ayant échoué |Nombre |Total |Nombre d’actions de flux de travail ayant échoué. |
| ActionsSkipped |Actions ignorées |Nombre |Total |Nombre d’actions de flux de travail ignorées. |
| ActionLatency |Latence de l’action |Secondes |Moyenne |Latence des actions de flux de travail terminés. |
| ActionSuccessLatency |Latence de réussite d’action |Secondes |Moyenne |Latence des actions de flux de travail ayant réussi. |
| ActionThrottledEvents |Événements d’action limitée |Nombre |Total |Nombre d’événements limités d’action de flux de travail. |
| TriggersStarted |Déclenchements lancés |Nombre |Total |Nombre de déclencheurs de flux de travail démarrés. |
| TriggersCompleted |Déclenchements terminés |Nombre |Total |Nombre de déclencheurs de flux de travail terminés. |
| TriggersSucceeded |Déclenchements ayant réussi |Nombre |Total |Nombre de déclencheurs de flux de travail ayant réussi. |
| TriggersFailed |Déclenchements ayant échoué |Nombre |Total |Nombre de déclencheurs de flux de travail ayant échoué. |
| TriggersSkipped |Déclenchements ignorés |Nombre |Total |Nombre de déclencheurs de flux de travail ignorés. |
| TriggersFired |Déclenchements activés |Nombre |Total |Nombre de déclencheurs de flux de travail activés. |
| TriggerLatency |Latence de déclencheur |Secondes |Moyenne |Latence des déclenchements de flux de travail terminés. |
| TriggerFireLatency |Latence d’activation de déclencheur |Secondes |Moyenne |Latence des déclencheurs de flux de travail activés. |
| TriggerSuccessLatency |Latence déclencheur ayant réussi |Secondes |Moyenne |Latence des déclencheurs de flux de travail ayant réussi. |
| TriggerThrottledEvents |Événements limités par déclencheur |Nombre |Total |Nombre d’événements de flux de travail limités par déclencheur. |
| BillableActionExecutions |Exécutions d’actions facturables |Nombre |Total |Nombre d’exécutions d’actions de flux de travail facturées. |
| BillableTriggerExecutions |Exécutions de déclencheurs facturables |Nombre |Total |Nombre d’exécutions de déclencheurs de flux de travail facturées. |
| TotalBillableExecutions |Nombre total d’exécutions facturables |Nombre |Total |Nombre d’exécutions de flux de travail facturées. |

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways
| Mesure | Nom d’affichage de la mesure | Unité | Type d’agrégation | Description |
| --- | --- | --- | --- | --- |
| Débit |Débit |Octets par seconde |Moyenne | |

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices
| Mesure | Nom d’affichage de la mesure | Unité | Type d’agrégation | Description |
| --- | --- | --- | --- | --- |
| SearchLatency |Latence de recherche |Secondes |Moyenne |Latence moyenne de recherche du service de recherche |
| SearchQueriesPerSecond (Recherches par seconde) |Requêtes de recherche par seconde |Nombre par seconde |Moyenne |Requêtes de recherche par seconde pour le service de recherche |
| ThrottledSearchQueriesPercentage |Pourcentage de requêtes de recherche limitées |Pourcentage |Moyenne |Pourcentage de requêtes de recherche limitées par le service de recherche |

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces
| Mesure | Nom d’affichage de la mesure | Unité | Type d’agrégation | Description |
| --- | --- | --- | --- | --- |
| CPUXNS |Utilisation du processeur par espace de noms |Pourcentage |Maximale |Mesure d’utilisation du processeur de l’espace de noms Service Bus Premium |
| WSXNS |Utilisation de la taille mémoire par espace de noms |Pourcentage |Maximale |Mesure d’utilisation de la mémoire de l’espace de noms Service Bus Premium |

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases
| Mesure | Nom d’affichage de la mesure | Unité | Type d’agrégation | Description |
| --- | --- | --- | --- | --- |
| cpu_percent |Pourcentage UC |Pourcentage |Moyenne |Pourcentage UC |
| physical_data_read_percent |Pourcentage E/S des données |Pourcentage |Moyenne |Pourcentage E/S des données |
| log_write_percent |Pourcentage E/S du journal |Pourcentage |Moyenne |Pourcentage E/S du journal |
| dtu_consumption_percent |Pourcentage DTU |Pourcentage |Moyenne |Pourcentage DTU |
| storage |Taille de base de données totale |Octets |Maximale |Taille de base de données totale |
| connection_successful |Connexions réussies |Nombre |Total |Connexions réussies |
| connection_failed |Connexions ayant échoué |Nombre |Total |Connexions ayant échoué |
| blocked_by_firewall |Bloqué par le pare-feu |Nombre |Total |Bloqué par le pare-feu |
| deadlock |Blocages |Nombre |Total |Blocages |
| storage_percent |Pourcentage de la taille de la base de données |Pourcentage |Maximale |Pourcentage de la taille de la base de données |
| xtp_storage_percent |Pourcentage de stockage OLTP en mémoire (aperçu) |Pourcentage |Moyenne |Pourcentage de stockage OLTP en mémoire (aperçu) |
| workers_percent |Pourcentage de travaux |Pourcentage |Moyenne |Pourcentage de travaux |
| sessions_percent |Pourcentage sessions |Pourcentage |Moyenne |Pourcentage sessions |
| dtu_limit |Limite DTU |Nombre |Moyenne |Limite DTU |
| dtu_used |DTU utilisé |Nombre |Moyenne |DTU utilisé |
| service_level_objective |Objectif de niveau de service de la base de données |Nombre |Total |Objectif de niveau de service de la base de données |
| dwu_limit |limite dwu |Nombre |Maximale |limite dwu |
| dwu_consumption_percent |Pourcentage DWU |Pourcentage |Moyenne |Pourcentage DWU |
| dwu_used |DWU utilisé |Nombre |Moyenne |DWU utilisé |

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools
| Mesure | Nom d’affichage de la mesure | Unité | Type d’agrégation | Description |
| --- | --- | --- | --- | --- |
| cpu_percent |Pourcentage UC |Pourcentage |Moyenne |Pourcentage UC |
| physical_data_read_percent |Pourcentage E/S des données |Pourcentage |Moyenne |Pourcentage E/S des données |
| log_write_percent |Pourcentage E/S du journal |Pourcentage |Moyenne |Pourcentage E/S du journal |
| dtu_consumption_percent |Pourcentage DTU |Pourcentage |Moyenne |Pourcentage DTU |
| storage_percent |Pourcentage de stockage |Pourcentage |Moyenne |Pourcentage de stockage |
| workers_percent |Pourcentage de travaux |Pourcentage |Moyenne |Pourcentage de travaux |
| sessions_percent |Pourcentage sessions |Pourcentage |Moyenne |Pourcentage sessions |
| eDTU_limit |Limite eDTU |Nombre |Moyenne |Limite eDTU |
| storage_limit |Limite de stockage |Octets |Moyenne |Limite de stockage |
| eDTU_used |eDTU utilisé |Nombre |Moyenne |eDTU utilisé |
| storage_used |Stockage utilisé |Octets |Moyenne |Stockage utilisé |

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs
| Mesure | Nom d’affichage de la mesure | Unité | Type d’agrégation | Description |
| --- | --- | --- | --- | --- |
| ResourceUtilization |Utilisation de % d’unités de diffusion |Pourcentage |Maximale |Utilisation de % d’unités de diffusion |
| InputEvents |Événements d’entrée |Nombre |Total |Événements d’entrée |
| InputEventBytes |Octets des événements d’entrée |Octets |Total |Octets des événements d’entrée |
| LateInputEvents |Événements d’entrée tardifs |Nombre |Total |Événements d’entrée tardifs |
| OutputEvents |Événements de sortie |Nombre |Total |Événements de sortie |
| ConversionErrors |Erreurs de conversion de données |Nombre |Total |Erreurs de conversion de données |
| Erreurs |Erreurs d’exécution |Nombre |Total |Erreurs d’exécution |
| DroppedOrAdjustedEvents |Événements en désordre |Nombre |Total |Événements en désordre |
| AMLCalloutRequests |Requêtes de fonction |Nombre |Total |Requêtes de fonction |
| AMLCalloutFailedRequests |Requêtes de fonction ayant échoué |Nombre |Total |Requêtes de fonction ayant échoué |
| AMLCalloutInputEvents |Événements de fonction |Nombre |Total |Événements de fonction |

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms
| Mesure | Nom d’affichage de la mesure | Unité | Type d’agrégation | Description |
| --- | --- | --- | --- | --- |
| Pourcentage UC |Pourcentage UC |Pourcentage |Moyenne |Pourcentage UC |
| Pourcentage mémoire |Pourcentage de mémoire |Pourcentage |Moyenne |Pourcentage de mémoire |
| Longueur de file d’attente du disque |Longueur de file d'attente de disque |Nombre |Total |Longueur de file d'attente de disque |
| Longueur de file d’attente HTTP |Longueur de la file d’attente HTTP |Nombre |Total |Longueur de la file d’attente HTTP |
| Octets reçus |Données entrantes |Octets |Total |Données entrantes |
| Octets envoyés |Données sortantes |Octets |Total |Données sortantes |

## <a name="microsoftwebsites"></a>Microsoft.Web/sites
| Mesure | Nom d’affichage de la mesure | Unité | Type d’agrégation | Description |
| --- | --- | --- | --- | --- |
| CpuTime |Temps processeur |Secondes |Total |Temps processeur |
| Requêtes |Requêtes |Nombre |Total |Requêtes |
| Octets reçus |Données entrantes |Octets |Total |Données entrantes |
| Octets envoyés |Données sortantes |Octets |Total |Données sortantes |
| Http2xx |Http 2xx |Nombre |Total |Http 2xx |
| Http3xx |Http 3xx |Nombre |Total |Http 3xx |
| Http401 |Http 401 |Nombre |Total |Http 401 |
| Http403 |Http 403 |Nombre |Total |Http 403 |
| Http404 |Http 404 |Nombre |Total |Http 404 |
| Http406 |Http 406 |Nombre |Total |Http 406 |
| Http4xx |Http 4xx |Nombre |Total |Http 4xx |
| Http5xx |Erreurs de serveur http |Nombre |Total |Erreurs de serveur http |
| MemoryWorkingSet |Plage de travail de la mémoire |Octets |Total |Plage de travail de la mémoire |
| AverageMemoryWorkingSet |Plage de travail moyenne de la mémoire |Octets |Moyenne |Plage de travail moyenne de la mémoire |
| AverageResponseTime |Temps de réponse moyen |Secondes |Moyenne |Temps de réponse moyen |

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots
| Mesure | Nom d’affichage de la mesure | Unité | Type d’agrégation | Description |
| --- | --- | --- | --- | --- |
| CpuTime |Temps processeur |Secondes |Total |Temps processeur |
| Requêtes |Requêtes |Nombre |Total |Requêtes |
| Octets reçus |Données entrantes |Octets |Total |Données entrantes |
| Octets envoyés |Données sortantes |Octets |Total |Données sortantes |
| Http2xx |Http 2xx |Nombre |Total |Http 2xx |
| Http3xx |Http 3xx |Nombre |Total |Http 3xx |
| Http401 |Http 401 |Nombre |Total |Http 401 |
| Http403 |Http 403 |Nombre |Total |Http 403 |
| Http404 |Http 404 |Nombre |Total |Http 404 |
| Http406 |Http 406 |Nombre |Total |Http 406 |
| Http4xx |Http 4xx |Nombre |Total |Http 4xx |
| Http5xx |Erreurs de serveur http |Nombre |Total |Erreurs de serveur http |
| MemoryWorkingSet |Plage de travail de la mémoire |Octets |Total |Plage de travail de la mémoire |
| AverageMemoryWorkingSet |Plage de travail moyenne de la mémoire |Octets |Moyenne |Plage de travail moyenne de la mémoire |
| AverageResponseTime |Temps de réponse moyen |Secondes |Moyenne |Temps de réponse moyen |

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur les mesures dans Azure Monitor](monitoring-overview.md#monitoring-sources)
* [Créer des alertes sur les mesures](insights-receive-alert-notifications.md)
* [Exporter des mesures vers le stockage, un hub d’événements ou Log Analytics](monitoring-overview-of-diagnostic-logs.md)




<!--HONumber=Nov16_HO3-->


