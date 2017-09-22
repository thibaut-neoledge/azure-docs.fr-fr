---
title: "Mesures Azure Monitor : mesures prises en charge par type de ressource | Microsoft Docs"
description: Liste des mesures disponibles pour chaque type de ressource avec Azure Monitor.
author: anirudhcavale
manager: ashwink
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 63d4ac65-1688-40d1-85c8-7cd408285b0f
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 9/6/2017
ms.author: ancav
ms.translationtype: HT
ms.sourcegitcommit: 9b7316a5bffbd689bdb26e9524129ceed06606d5
ms.openlocfilehash: 6f40f0cdb998bda9d936ecf87089e85713ed9e1b
ms.contentlocale: fr-fr
ms.lasthandoff: 09/08/2017

---
# <a name="supported-metrics-with-azure-monitor"></a>Mesures prises en charge avec Azure Monitor
Azure Monitor offre plusieurs moyens d’interagir avec les mesures, y compris en créant des graphiques dans le portail, en y accédant via l’API REST ou en envoyant des requêtes avec PowerShell ou l’interface CLI. Voici une liste complète de toutes les mesures actuellement offertes par le pipeline de mesure d’Azure Monitor.

> [!NOTE]
> D’autres mesures peuvent être disponibles dans le portail ou via les API héritées. Cette liste ne comprend que les mesures disponibles dans la version préliminaire publique du pipeline de mesures Azure Monitor consolidé.
>
>

## <a name="microsoftanalysisservicesservers"></a>Microsoft.AnalysisServices/servers

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|qpu_metric|QPU|Nombre|Moyenne|QPU. Plage de 0 à 100 pour S1, de 0 à 200 pour S2 et de 0 à 400 pour S4|
|memory_metric|Mémoire|Octets|Moyenne|Mémoire. Plage de 0 à 25 Go pour S1, de 0 à 50 Go pour S2 et de 0 à 100 Go pour S4|
|TotalConnectionRequests|Nombre total de demandes de connexion|Nombre|Moyenne|Nombre total de demandes de connexion. Il s’agit des arrivées.|
|SuccessfullConnectionsPerSec|Connexions réussies par seconde|CountPerSecond|Moyenne|Taux de connexions terminées réussies.|
|TotalConnectionFailures|Nombre total d’échecs de connexion|Nombre|Moyenne|Total des échecs de tentatives de connexion.|
|CurrentUserSessions|Sessions utilisateur actuelles|Nombre|Moyenne|Nombre actuel de sessions utilisateur établies.|
|QueryPoolBusyThreads|Threads occupés du pool de threads de requêtes|Nombre|Moyenne|Nombre de threads occupés dans le pool de threads de requêtes.|
|CommandPoolJobQueueLength|Longueur de la file d’attente des travaux du pool de commandes|Nombre|Moyenne|Nombre de travaux contenus dans la file d’attente du pool de threads de commandes.|
|ProcessingPoolJobQueueLength|Longueur de la file d’attente des travaux du pool de traitement|Nombre|Moyenne|Nombre de travaux autres que d’E/S contenus dans la file d’attente du pool de threads de traitement.|
|CurrentConnections|Connexion : connexions actuelles|Nombre|Moyenne|Nombre actuel de connexions client établies.|
|CleanerCurrentPrice|Mémoire : prix actuel du nettoyage|Nombre|Moyenne|Prix actuel de la mémoire, $/octet/temps, normalisé à 1000.|
|CleanerMemoryShrinkable|Mémoire : mémoire de nettoyage réductible|Octets|Moyenne|Quantité de mémoire, en octets, qui doit être vidée par le nettoyage en arrière-plan.|
|CleanerMemoryNonshrinkable|Mémoire : mémoire de nettoyage non réductible|Octets|Moyenne|Quantité de mémoire, en octets, qui ne doit pas être vidée par le nettoyage en arrière-plan.|
|MemoryUsage|Mémoire : utilisation de la mémoire|Octets|Moyenne|Utilisation de la mémoire du processus serveur telle qu’utilisée dans le calcul du coût de la mémoire de nettoyage. Équivaut au compteur Process\PrivateBytes, plus la taille des données mappées en mémoire, en ignorant la mémoire mappée ou allouée par le moteur d’analyse de mémoire xVelocity (VertiPaq) dépassant la limite de mémoire du moteur xVelocity.|
|MemoryLimitHard|Mémoire : limite de mémoire physique|Octets|Moyenne|Limite de mémoire physique, du fichier de configuration.|
|MemoryLimitHigh|Mémoire : limite de mémoire élevée|Octets|Moyenne|Limite de mémoire élevée, du fichier de configuration.|
|MemoryLimitLow|Mémoire : limite de mémoire basse|Octets|Moyenne|Limite de mémoire basse, du fichier de configuration.|
|MemoryLimitVertiPaq|Mémoire : limite de mémoire VertiPaq|Octets|Moyenne|Limite en mémoire, du fichier de configuration.|
|Quota|Mémoire : quota|Octets|Moyenne|Quota de mémoire actuel, en octets. Le quota de mémoire est également appelé réserve de mémoire ou d’allocation.|
|QuotaBlocked|Mémoire : quota bloqué|Nombre|Moyenne|Nombre actuel de requêtes de quota qui sont bloquées en attendant la libération d’autres quotas de mémoire.|
|VertiPaqNonpaged|Mémoire : réserve non paginée VertiPaq|Octets|Moyenne|Octets de mémoire verrouillée dans la plage de travail pour utilisation par le moteur en mémoire.|
|VertiPaqPaged|Mémoire : réserve paginée VertiPaq|Octets|Moyenne|Octets de mémoire paginée utilisée pour les données en mémoire.|
|RowsReadPerSec|Traitement : lignes lues par seconde|CountPerSecond|Moyenne|Taux de lignes lues à partir de toutes les bases de données relationnelles.|
|RowsConvertedPerSec|Traitement : lignes converties par seconde|CountPerSecond|Moyenne|Taux de lignes converties lors du traitement.|
|RowsWrittenPerSec|Traitement : lignes écrites par seconde|CountPerSecond|Moyenne|Taux de lignes écrites lors du traitement.|
|CommandPoolBusyThreads|Threads : threads occupés du pool commandes|Nombre|Moyenne|Nombre de threads occupés dans le pool de threads de commandes.|
|CommandPoolIdleThreads|Threads : threads inactifs du pool commande|Nombre|Moyenne|Nombre de threads inactifs dans le pool de threads de commandes.|
|LongParsingBusyThreads|Threads : threads d’analyse longue occupés|Nombre|Moyenne|Nombre de threads occupés dans le pool de threads d’analyse longue.|
|LongParsingIdleThreads|Threads : threads d’analyse longue inactifs|Nombre|Moyenne|Nombre de threads inactifs dans le pool de threads d’analyse longue.|
|LongParsingJobQueueLength|Threads : durée de file d’attente des travaux d’analyse longue|Nombre|Moyenne|Nombre de travaux contenus dans la file d’attente du pool de threads d’analyse longue.|
|ProcessingPoolBusyIOJobThreads|Threads : traitement des threads de travail d’E/S occupés du pool|Nombre|Moyenne|Nombre de threads pour les travaux d’E/S en cours d’exécution dans le pool de threads de traitement.|
|ProcessingPoolBusyNonIOThreads|Threads : traitement des threads de travail autres qu’E/S occupés du pool|Nombre|Moyenne|Nombre de threads pour les travaux autres que d’E/S en cours d’exécution dans le pool de threads de traitement.|
|ProcessingPoolIOJobQueueLength|Threads : longueur de la file d’attente des travaux d’E/S du pool de traitement|Nombre|Moyenne|Nombre de travaux d’E/S contenus dans la file d’attente du pool de threads de traitement.|
|ProcessingPoolIdleIOJobThreads|Threads : traitement des threads de travail d’E/S ignorés du pool|Nombre|Moyenne|Nombre de threads inactifs pour les travaux d’E/S le pool de threads de traitement.|
|ProcessingPoolIdleNonIOThreads|Threads : traitement des threads de travail autres qu’E/S inactifs du pool|Nombre|Moyenne|Nombre de threads inactifs le pool de threads de traitement dédiés aux travaux autres qu’E/S.|
|QueryPoolIdleThreads|Threads : threads inactifs du pool de requêtes|Nombre|Moyenne|Nombre de threads inactifs pour les travaux d’E/S le pool de threads de traitement.|
|QueryPoolJobQueueLength|Threads : longueur de file d’attente de travaux du pool de requêtes|Nombre|Moyenne|Nombre de travaux contenus dans la file d’attente du pool de threads de requêtes.|
|ShortParsingBusyThreads|Threads : threads d’analyse courte occupés|Nombre|Moyenne|Nombre de threads occupés dans le pool de threads d’analyse courte.|
|ShortParsingIdleThreads|Threads : threads d’analyse courte inactifs|Nombre|Moyenne|Nombre de threads inactifs dans le pool de threads d’analyse courte.|
|ShortParsingJobQueueLength|Threads : durée de file d’attente des travaux d’analyse courte|Nombre|Moyenne|Nombre de travaux contenus dans la file d’attente du pool de threads d’analyse courte.|
|memory_thrashing_metric|Vidage de mémoire|Pourcentage|Moyenne|Vidage de mémoire moyenne.|
|mashup_engine_qpu_metric|QPU du moteur M|Nombre|Moyenne|Utilisation des QPU par les processus de moteur mashup|
|mashup_engine_memory_metric|Mémoire du moteur M|Octets|Moyenne|Utilisation de la mémoire par les processus de moteur mashup|

## <a name="microsoftapimanagementservice"></a>Microsoft.ApiManagement/service

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|TotalRequests|Nombre total de demandes de la passerelle|Nombre|Total|Nombre de demandes de la passerelle|
|SuccessfulRequests|Demandes de la passerelle ayant abouti|Nombre|Total|Nombre de demandes de la passerelle ayant abouti|
|UnauthorizedRequests|Demandes de la passerelle non autorisées|Nombre|Total|Nombre de demandes de la passerelle non autorisées|
|FailedRequests|Demandes de la passerelle ayant échoué|Nombre|Total|Nombre de défaillances des demandes de la passerelle|
|OtherRequests|Autres demandes de la passerelle|Nombre|Total|Nombre d’autres demandes de la passerelle|
|Duration|Durée globale des demandes de passerelle|Millisecondes|Moyenne, Maximale|Durée globale des demandes de passerelle en millisecondes|
|Capacity|Capacité (préversion)|Pourcentage|Moyenne, Maximale|Métrique d’utilisation pour le service ApiManagement|

## <a name="microsoftautomationautomationaccounts"></a>Microsoft.Automation/automationAccounts

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|TotalJob|Nombre total de travaux|Nombre|Total|Nombre total de travaux|

## <a name="microsoftbatchbatchaccounts"></a>Microsoft.Batch/batchAccounts

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|CoreCount|Nombre de cœurs dédiés|Nombre|Total|Nombre total de cœurs dédiés dans le compte Batch|
|TotalNodeCount|Nombre de nœuds dédiés|Nombre|Total|Nombre total de nœuds dédiés dans le compte Batch|
|LowPriorityCoreCount|Nombre de cœurs à priorité basse|Nombre|Total|Nombre total de cœurs à priorité basse dans le compte Batch|
|TotalLowPriorityNodeCount|Nombre de nœuds à priorité basse|Nombre|Total|Nombre total de nœuds à priorité basse dans le compte Batch|
|CreatingNodeCount|Nombre de nœuds créés|Nombre|Total|Nombre de nœuds en cours de création|
|StartingNodeCount|Nombre de nœuds de départ|Nombre|Total|Nœuds de nœuds de départ|
|WaitingForStartTaskNodeCount|Nombre de nœuds en attente de démarrage de tâche|Nombre|Total|Nombre de nœuds en attente de la fin d’une tâche de démarrage|
|StartTaskFailedNodeCount|Nombre de nœuds pour lesquels le démarrage d’une tâche a échoué|Nombre|Total|Le nombre de nœuds pour lesquels le démarrage d’une tâche a échoué|
|IdleNodeCount|Nombre de nœuds inactifs|Nombre|Total|Le nombre de nœuds inactifs|
|OfflineNodeCount|Nombre de nœuds hors ligne|Nombre|Total|Le nombre de nœuds hors ligne|
|RebootingNodeCount|Nombre de nœuds en cours de redémarrage|Nombre|Total|Le nombre de nœuds en cours de redémarrage|
|ReimagingNodeCount|Nombre de nœuds en cours de réimageage|Nombre|Total|Le nombre de nœuds en cours de réimageage|
|RunningNodeCount|Nombre de nœuds en cours d’exécution|Nombre|Total|Le nombre de nœuds en cours d’exécution|
|LeavingPoolNodeCount|Nombre de nœuds quittant le pool|Nombre|Total|Le nombre de nœuds quittant le pool|
|UnusableNodeCount|Nombre de nœuds inutilisables|Nombre|Total|Le nombre de nœuds inutilisables|
|PreemptedNodeCount|Nombre de nœuds reportés|Nombre|Total|Nombre de nœuds reportés|
|TaskStartEvent|Événements de lancement de tâche|Nombre|Total|Nombre total de tâches ayant démarré|
|TaskCompleteEvent|Événements de fin de tâche|Nombre|Total|Le nombre total de tâches terminées|
|TaskFailEvent|Événements d’échec de tâches|Nombre|Total|Le nombre total de tâches terminées dans un état d’échec|
|PoolCreateEvent|Événements de création de pool|Nombre|Total|Nombre total de pools créés|
|PoolResizeStartEvent|Événements de démarrage de redimensionnement de pool|Nombre|Total|Nombre total de redimensionnements de pool ayant démarré|
|PoolResizeCompleteEvent|Événements de fin de redimensionnement de pool|Nombre|Total|Nombre total de redimensionnements de pool terminés|
|PoolDeleteStartEvent|Événements de démarrage de suppression de pool|Nombre|Total|Nombre total de suppressions de pool ayant démarré|
|PoolDeleteCompleteEvent|Événements de suppression de pool terminés|Nombre|Total|Nombre total de suppressions de pool terminées|

## <a name="microsoftcacheredis"></a>Microsoft.Cache/redis

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|connectedclients|Clients connectés|Nombre|Maximale||
|totalcommandsprocessed|Total des opérations|Nombre|Total||
|cachehits|Présences dans le cache|Nombre|Total||
|cachemisses|Absences dans le cache|Nombre|Total||
|getcommands|Gets|Nombre|Total||
|setcommands|Sets|Nombre|Total||
|evictedkeys|Clés exclues|Nombre|Total||
|totalkeys|Nombre total de clés|Nombre|Maximale||
|expiredkeys|Clés expirées|Nombre|Total||
|usedmemory|Mémoire utilisée|Octets|Maximale||
|usedmemoryRss|Taille de la mémoire résidente utilisée|Octets|Maximale||
|serverLoad|Charge du serveur|Pourcentage|Maximale||
|cacheWrite|Cache d’écriture|BytesPerSecond|Maximale||
|cacheRead|Lecture du cache|BytesPerSecond|Maximale||
|percentProcessorTime|UC|Pourcentage|Maximale||
|connectedclients0|Clients connectés (Shard 0)|Nombre|Maximale||
|totalcommandsprocessed0|Total des opérations (Shard 0)|Nombre|Total||
|cachehits0|Présences dans le cache (Shard 0)|Nombre|Total||
|cachemisses0|Absences dans le cache (Shard 0)|Nombre|Total||
|getcommands0|Gets (Shard 0)|Nombre|Total||
|setcommands0|Sets (Shard 0)|Nombre|Total||
|evictedkeys0|Clés exclues (Shard 0)|Nombre|Total||
|totalkeys0|Nombre total de clés (Shard 0)|Nombre|Maximale||
|expiredkeys0|Clés expirées (Shard 0)|Nombre|Total||
|usedmemory0|Mémoire utilisée (Shard 0)|Octets|Maximale||
|usedmemoryRss0|Mémoire résidente utilisée (Shard 0)|Octets|Maximale||
|serverLoad0|Charge du serveur (Shard 0)|Pourcentage|Maximale||
|cacheWrite0|Cache d’écriture (Shard 0)|BytesPerSecond|Maximale||
|cacheRead0|Cache de lecture (Shard 0)|BytesPerSecond|Maximale||
|percentProcessorTime0|UC (Shard 0)|Pourcentage|Maximale||
|connectedclients1|Clients connectés (Shard 1)|Nombre|Maximale||
|totalcommandsprocessed1|Total des opérations (Shard 1)|Nombre|Total||
|cachehits1|Présences dans le cache (Shard 1)|Nombre|Total||
|cachemisses1|Absences dans le cache (Shard 1)|Nombre|Total||
|getcommands1|Gets (Shard 1)|Nombre|Total||
|setcommands1|Sets (Shard 1)|Nombre|Total||
|evictedkeys1|Clés exclues (Shard 1)|Nombre|Total||
|totalkeys1|Nombre total de clés (Shard 1)|Nombre|Maximale||
|expiredkeys1|Clés expirées (Shard 1)|Nombre|Total||
|usedmemory1|Mémoire utilisée (Shard 1)|Octets|Maximale||
|usedmemoryRss1|Mémoire résidente utilisée (Shard 1)|Octets|Maximale||
|serverLoad1|Charge du serveur (Shard 1)|Pourcentage|Maximale||
|cacheWrite1|Cache d’écriture (Shard 1)|BytesPerSecond|Maximale||
|cacheRead1|Lecture du cache (Shard 1)|BytesPerSecond|Maximale||
|percentProcessorTime1|UC (Shard 1)|Pourcentage|Maximale||
|connectedclients2|Clients connectés (Shard 2)|Nombre|Maximale||
|totalcommandsprocessed2|Total des opérations (Shard 2)|Nombre|Total||
|cachehits2|Présences dans le cache (Shard 2)|Nombre|Total||
|cachemisses2|Absences dans le cache (Shard 2)|Nombre|Total||
|getcommands2|Gets (Shard 2)|Nombre|Total||
|setcommands2|Sets (Shard 2)|Nombre|Total||
|evictedkeys2|Clés exclues (Shard 2)|Nombre|Total||
|totalkeys2|Nombre total de clés (Shard 2)|Nombre|Maximale||
|expiredkeys2|Clés expirées (Shard 2)|Nombre|Total||
|usedmemory2|Mémoire utilisée (Shard 2)|Octets|Maximale||
|usedmemoryRss2|Mémoire résidente utilisée (Shard 2)|Octets|Maximale||
|serverLoad2|Charge du serveur (Shard 2)|Pourcentage|Maximale||
|cacheWrite2|Cache d’écriture (Shard 2)|BytesPerSecond|Maximale||
|cacheRead2|Lecture du cache (Shard 2)|BytesPerSecond|Maximale||
|percentProcessorTime2|UC (Shard 2)|Pourcentage|Maximale||
|connectedclients3|Clients connectés (Shard 3)|Nombre|Maximale||
|totalcommandsprocessed3|Total des opérations (Shard 3)|Nombre|Total||
|cachehits3|Présences dans le cache (Shard 3)|Nombre|Total||
|cachemisses3|Absences dans le cache (Shard 3)|Nombre|Total||
|getcommands3|Gets (Shard 3)|Nombre|Total||
|setcommands3|Sets (Shard 3)|Nombre|Total||
|evictedkeys3|Clés exclues (Shard 3)|Nombre|Total||
|totalkeys3|Nombre total de clés (Shard 3)|Nombre|Maximale||
|expiredkeys3|Clés expirées (Shard 3)|Nombre|Total||
|usedmemory3|Mémoire utilisée (Shard 3)|Octets|Maximale||
|usedmemoryRss3|Mémoire résidente utilisée (Shard 3)|Octets|Maximale||
|serverLoad3|Charge du serveur (Shard 3)|Pourcentage|Maximale||
|cacheWrite3|Cache d’écriture (Shard 3)|BytesPerSecond|Maximale||
|cacheRead3|Lecture du cache (Shard 3)|BytesPerSecond|Maximale||
|percentProcessorTime3|UC (Shard 3)|Pourcentage|Maximale||
|connectedclients4|Clients connectés (Shard 4)|Nombre|Maximale||
|totalcommandsprocessed4|Total des opérations (Shard 4)|Nombre|Total||
|cachehits4|Présences dans le cache (Shard 4)|Nombre|Total||
|cachemisses4|Absences dans le cache (Shard 4)|Nombre|Total||
|getcommands4|Gets (Shard 4)|Nombre|Total||
|setcommands4|Sets (Shard 4)|Nombre|Total||
|evictedkeys4|Clés exclues (Shard 4)|Nombre|Total||
|totalkeys4|Nombre total de clés (Shard 4)|Nombre|Maximale||
|expiredkeys4|Clés expirées (Shard 4)|Nombre|Total||
|usedmemory4|Mémoire utilisée (Shard 4)|Octets|Maximale||
|usedmemoryRss4|Mémoire résidente utilisée (Shard 4)|Octets|Maximale||
|serverLoad4|Charge du serveur (Shard 4)|Pourcentage|Maximale||
|cacheWrite4|Cache d’écriture (Shard 4)|BytesPerSecond|Maximale||
|cacheRead4|Lecture du cache (Shard 4)|BytesPerSecond|Maximale||
|percentProcessorTime4|UC (Shard 4)|Pourcentage|Maximale||
|connectedclients5|Clients connectés (Shard 5)|Nombre|Maximale||
|totalcommandsprocessed5|Total des opérations (Shard 5)|Nombre|Total||
|cachehits5|Présences dans le cache (Shard 5)|Nombre|Total||
|cachemisses5|Absences dans le cache (Shard 5)|Nombre|Total||
|getcommands5|Gets (Shard 5)|Nombre|Total||
|setcommands5|Sets (Shard 5)|Nombre|Total||
|evictedkeys5|Clés exclues (Shard 5)|Nombre|Total||
|totalkeys5|Nombre total de clés (Shard 5)|Nombre|Maximale||
|expiredkeys5|Clés expirées (Shard 5)|Nombre|Total||
|usedmemory5|Mémoire utilisée (Shard 5)|Octets|Maximale||
|usedmemoryRss5|Mémoire résidente utilisée (Shard 5)|Octets|Maximale||
|serverLoad5|Charge du serveur (Shard 5)|Pourcentage|Maximale||
|cacheWrite5|Cache d’écriture (Shard 5)|BytesPerSecond|Maximale||
|cacheRead5|Lecture du cache (Shard 5)|BytesPerSecond|Maximale||
|percentProcessorTime5|UC (Shard 5)|Pourcentage|Maximale||
|connectedclients6|Clients connectés (Shard 6)|Nombre|Maximale||
|totalcommandsprocessed6|Total des opérations (Shard 6)|Nombre|Total||
|cachehits6|Présences dans le cache (Shard 6)|Nombre|Total||
|cachemisses6|Absences dans le cache (Shard 6)|Nombre|Total||
|getcommands6|Gets (Shard 6)|Nombre|Total||
|setcommands6|Sets (Shard 6)|Nombre|Total||
|evictedkeys6|Clés exclues (Shard 6)|Nombre|Total||
|totalkeys6|Nombre total de clés (Shard 6)|Nombre|Maximale||
|expiredkeys6|Clés expirées (Shard 6)|Nombre|Total||
|usedmemory6|Mémoire utilisée (Shard 6)|Octets|Maximale||
|usedmemoryRss6|Mémoire résidente utilisée (Shard 6)|Octets|Maximale||
|serverLoad6|Charge du serveur (Shard 6)|Pourcentage|Maximale||
|cacheWrite6|Cache d’écriture (Shard 6)|BytesPerSecond|Maximale||
|cacheRead6|Lecture du cache (Shard 6)|BytesPerSecond|Maximale||
|percentProcessorTime6|UC (Shard 6)|Pourcentage|Maximale||
|connectedclients7|Clients connectés (Shard 7)|Nombre|Maximale||
|totalcommandsprocessed7|Total des opérations (Shard 7)|Nombre|Total||
|cachehits7|Présences dans le cache (Shard 7)|Nombre|Total||
|cachemisses7|Absences dans le cache (Shard 7)|Nombre|Total||
|getcommands7|Gets (Shard 7)|Nombre|Total||
|setcommands7|Sets (Shard 7)|Nombre|Total||
|evictedkeys7|Clés exclues (Shard 7)|Nombre|Total||
|totalkeys7|Nombre total de clés (Shard 7)|Nombre|Maximale||
|expiredkeys7|Clés expirées (Shard 7)|Nombre|Total||
|usedmemory7|Mémoire utilisée (Shard 7)|Octets|Maximale||
|usedmemoryRss7|Mémoire résidente utilisée (Shard 7)|Octets|Maximale||
|serverLoad7|Charge du serveur (Shard 7)|Pourcentage|Maximale||
|cacheWrite7|Cache d’écriture (Shard 7)|BytesPerSecond|Maximale||
|cacheRead7|Lecture du cache (Shard 7)|BytesPerSecond|Maximale||
|percentProcessorTime7|UC (Shard 7)|Pourcentage|Maximale||
|connectedclients8|Clients connectés (Shard 8)|Nombre|Maximale||
|totalcommandsprocessed8|Total des opérations (Shard 8)|Nombre|Total||
|cachehits8|Présences dans le cache (Shard 8)|Nombre|Total||
|cachemisses8|Absences dans le cache (Shard 8)|Nombre|Total||
|getcommands8|Gets (Shard 8)|Nombre|Total||
|setcommands8|Sets (Shard 8)|Nombre|Total||
|evictedkeys8|Clés exclues (Shard 8)|Nombre|Total||
|totalkeys8|Nombre total de clés (Shard 8)|Nombre|Maximale||
|expiredkeys8|Clés expirées (Shard 8)|Nombre|Total||
|usedmemory8|Mémoire utilisée (Shard 8)|Octets|Maximale||
|usedmemoryRss8|Mémoire résidente utilisée (Shard 8)|Octets|Maximale||
|serverLoad8|Charge du serveur (Shard 8)|Pourcentage|Maximale||
|cacheWrite8|Cache d’écriture (Shard 8)|BytesPerSecond|Maximale||
|cacheRead8|Lecture du cache (Shard 8)|BytesPerSecond|Maximale||
|percentProcessorTime8|UC (Shard 8)|Pourcentage|Maximale||
|connectedclients9|Clients connectés (Shard 9)|Nombre|Maximale||
|totalcommandsprocessed9|Total des opérations (Shard 9)|Nombre|Total||
|cachehits9|Présences dans le cache (Shard 9)|Nombre|Total||
|cachemisses9|Absences dans le cache (Shard 9)|Nombre|Total||
|getcommands9|Gets (Shard 9)|Nombre|Total||
|setcommands9|Sets (Shard 9)|Nombre|Total||
|evictedkeys9|Clés exclues (Shard 9)|Nombre|Total||
|totalkeys9|Nombre total de clés (Shard 9)|Nombre|Maximale||
|expiredkeys9|Clés expirées (Shard 9)|Nombre|Total||
|usedmemory9|Mémoire utilisée (Shard 9)|Octets|Maximale||
|usedmemoryRss9|Mémoire résidente utilisée (Shard 9)|Octets|Maximale||
|serverLoad9|Charge du serveur (Shard 9)|Pourcentage|Maximale||
|cacheWrite9|Cache d’écriture (Shard 9)|BytesPerSecond|Maximale||
|cacheRead9|Cache de lecture (Shard 9)|BytesPerSecond|Maximale||
|percentProcessorTime9|UC (Shard 9)|Pourcentage|Maximale||

## <a name="microsoftclassiccomputevirtualmachines"></a>Microsoft.ClassicCompute/virtualMachines

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Pourcentage|Moyenne|Pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles.|
|Network In|Network In|Octets|Total|Nombre d’octets reçus sur toutes les interfaces réseau par les machines virtuelles (trafic entrant).|
|Network Out|Network Out|Octets|Total|Nombre d’octets envoyés sur toutes les interfaces réseau par les machines virtuelles (trafic sortant).|
|Disk Read Bytes/Sec|Lecture du disque|BytesPerSecond|Moyenne|Moyenne d’octets lus à partir du disque pendant la période d’analyse.|
|Disk Write Bytes/Sec|Écriture sur le disque|BytesPerSecond|Moyenne|Moyenne d’octets écrits sur le disque pendant la période d’analyse.|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Moyenne|E/S de lecture disque par seconde.|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Moyenne|E/S d’écriture sur disque par seconde.|

## <a name="microsoftcognitiveservicesaccounts"></a>Microsoft.CognitiveServices/accounts

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|TotalCalls|Nombre total d’appels|Nombre|Total|Nombre total d’appels.|
|SuccessfulCalls|Appels réussis|Nombre|Total|Nombre d’appels réussis.|
|TotalErrors|Nombre total d’erreurs|Nombre|Total|Nombre total d’appels avec réponse d’erreur (code de réponse HTTP : 4xx ou 5xx).|
|BlockedCalls|Appels bloqués|Nombre|Total|Nombre d’appels ayant dépassé la limite de débit ou de quota.|
|ServerErrors|Erreurs de serveur|Nombre|Total|Nombre d’appels avec erreur interne du service (code de réponse HTTP : 5xx).|
|ClientErrors|Erreurs de client|Nombre|Total|Nombre d’appels avec erreur côté client (code de réponse HTTP : 4xx).|
|DataIn|Données entrantes|Octets|Total|Taille des données entrantes en octets.|
|DataOut|Données sortantes|Octets|Total|Taille des données sortantes en octets.|
|Latency|Latency|Millisecondes|Moyenne|Latence en millisecondes.|

## <a name="microsoftcomputevirtualmachines"></a>Microsoft.Compute/virtualMachines

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Pourcentage|Moyenne|Le pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles|
|Network In|Network In|Octets|Total|Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant)|
|Network Out|Network Out|Octets|Total|Le nombre d’octets envoyés sur toutes les interfaces réseau par les ordinateurs virtuels (trafic sortant)|
|Disk Read Bytes|Disk Read Bytes|Octets|Total|Total d’octets lus à partir du disque pendant la période d’analyse|
|Disk Write Bytes|Disk Write Bytes|Octets|Total|Total d’octets écrits sur le disque pendant la période d’analyse|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Moyenne|E/S de lecture disque par seconde|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Moyenne|E/S d’écriture disque par seconde|

## <a name="microsoftcomputevirtualmachinescalesets"></a>Microsoft.Compute/virtualMachineScaleSets

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Pourcentage|Moyenne|Le pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles|
|Network In|Network In|Octets|Total|Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant)|
|Network Out|Network Out|Octets|Total|Le nombre d’octets envoyés sur toutes les interfaces réseau par les ordinateurs virtuels (trafic sortant)|
|Disk Read Bytes|Disk Read Bytes|Octets|Total|Total d’octets lus à partir du disque pendant la période d’analyse|
|Disk Write Bytes|Disk Write Bytes|Octets|Total|Total d’octets écrits sur le disque pendant la période d’analyse|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Moyenne|E/S de lecture disque par seconde|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Moyenne|E/S d’écriture disque par seconde|

## <a name="microsoftcomputevirtualmachinescalesetsvirtualmachines"></a>Microsoft.Compute/virtualMachineScaleSets/virtualMachines

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|Percentage CPU|Percentage CPU|Pourcentage|Moyenne|Le pourcentage d’unités de calcul affectées actuellement utilisées par des machines virtuelles|
|Network In|Network In|Octets|Total|Le nombre d’octets reçus sur toutes les interfaces réseau par les ordinateurs virtuels (trafic entrant)|
|Network Out|Network Out|Octets|Total|Le nombre d’octets envoyés sur toutes les interfaces réseau par les ordinateurs virtuels (trafic sortant)|
|Disk Read Bytes|Disk Read Bytes|Octets|Total|Total d’octets lus à partir du disque pendant la période d’analyse|
|Disk Write Bytes|Disk Write Bytes|Octets|Total|Total d’octets écrits sur le disque pendant la période d’analyse|
|Disk Read Operations/Sec|Disk Read Operations/Sec|CountPerSecond|Moyenne|E/S de lecture disque par seconde|
|Disk Write Operations/Sec|Disk Write Operations/Sec|CountPerSecond|Moyenne|E/S d’écriture disque par seconde|

## <a name="microsoftcustomerinsightshubs"></a>Microsoft.CustomerInsights/hubs

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|DCIApiCalls|Appels de l’API Insights client|Nombre|Total||
|DCIMappingImportOperationSuccessfulLines|Mappage des lignes de réussite d’opération d’importation|Nombre|Total||
|DCIMappingImportOperationFailedLines|Mappage des lignes en échec d’opération d’importation|Nombre|Total||
|DCIMappingImportOperationTotalLines|Mappage du total des lignes d’opération d’importation|Nombre|Total||
|DCIMappingImportOperationRuntimeInSeconds|Mappage d’exécution d’opération d’importation en secondes|Secondes|Total||
|DCIOutboundProfileExportSucceeded|Exportation réussie de profil de sortie|Nombre|Total||
|DCIOutboundProfileExportFailed|Échec de l’exportation de profil de sortie|Nombre|Total||
|DCIOutboundProfileExportDuration|Durée d’exportation de profil de sortie|Secondes|Total||
|DCIOutboundKpiExportSucceeded|Exportation réussie de l’indicateur KPI sortant|Nombre|Total||
|DCIOutboundKpiExportFailed|Échec de l’exportation de l’indicateur KPI sortant|Nombre|Total||
|DCIOutboundKpiExportDuration|Durée de l’exportation de l’indicateur KPI sortant|Secondes|Total||
|DCIOutboundKpiExportStarted|Exportation démarrée de l’indicateur KPI sortant|Secondes|Total||
|DCIOutboundKpiRecordCount|Nombre d’enregistrements KPI sortants|Secondes|Total||
|DCIOutboundProfileExportCount|Nombre d’exportations de profils de sortie|Secondes|Total||
|DCIOutboundInitialProfileExportFailed|Échec de l’exportation de profil initial de sortie|Secondes|Total||
|DCIOutboundInitialProfileExportSucceeded|Réussite de l’exportation de profil initial de sortie|Secondes|Total||
|DCIOutboundInitialKpiExportFailed|Échec de l’exportation de l’indicateur KPI initial sortant|Secondes|Total||
|DCIOutboundInitialKpiExportSucceeded|Réussite de l’exportation de l’indicateur KPI initial sortant|Secondes|Total||
|DCIOutboundInitialProfileExportDurationInSeconds|Durée d’exportation de profil initial de sortie en secondes|Secondes|Total||
|AdlaJobForStandardKpiFailed|Échec du travail ADLA pour l’indicateur KPI standard en secondes|Secondes|Total||
|AdlaJobForStandardKpiTimeOut|Délai d’expiration du travail ADLA pour l’indicateur KPI standard en secondes|Secondes|Total||
|AdlaJobForStandardKpiCompleted|Achèvement du travail ADLA pour l’indicateur KPI standard en secondes|Secondes|Total||
|ImportASAValuesFailed|Nombre d’importations de valeurs ASA en échec|Nombre|Total||
|ImportASAValuesSucceeded|Nombre d’importations de valeurs ASA réussies|Nombre|Total||

## <a name="microsoftdatalakeanalyticsaccounts"></a>Microsoft.DataLakeAnalytics/accounts

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|JobEndedSuccess|Travaux réussis|Nombre|Total|Nombre de travaux réussis|
|JobEndedFailure|Travaux en échec|Nombre|Total|Nombre de travaux en échec|
|JobEndedCancelled|Travaux annulés|Nombre|Total|Nombre de travaux annulés|
|JobAUEndedSuccess|Durée AU de réussite|Secondes|Total|Durée AU totale des travaux réussis.|
|JobAUEndedFailure|Durée AU d’échec|Secondes|Total|Durée AU totale des travaux en échec|
|JobAUEndedCancelled|Durée AU d’annulation|Secondes|Total|Durée AU totale des travaux annulés|

## <a name="microsoftdatalakestoreaccounts"></a>Microsoft.DataLakeStore/accounts

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|TotalStorage|Stockage total|Octets|Maximale|Volume total de données stockées dans le compte.|
|DataWritten|Données écrites|Octets|Total|Volume total de données écrites dans le compte.|
|DataRead|Données lues|Octets|Total|Volume total de données lues à partir du compte.|
|WriteRequests|Demandes d’écriture|Nombre|Total|Nombre de demandes d’écriture de données sur le compte.|
|ReadRequests|Demandes de lecture|Nombre|Total|Nombre de demandes de lecture de données sur le compte.|

## <a name="microsoftdbformysqlservers"></a>Microsoft.DBforMySQL/servers

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|cpu_percent|Pourcentage d’UC|Pourcentage|Moyenne|Pourcentage d’UC|
|compute_limit|Limite d’unités de calcul|Nombre|Moyenne|Limite d’unités de calcul|
|compute_consumption_percent|Pourcentage d’unités de calcul|Pourcentage|Moyenne|Pourcentage d’unités de calcul|
|memory_percent|Pourcentage de mémoire|Pourcentage|Moyenne|Pourcentage de mémoire|
|io_consumption_percent|Pourcentage d’E/S|Pourcentage|Moyenne|Pourcentage d’E/S|
|storage_percent|Pourcentage de stockage|Pourcentage|Moyenne|Pourcentage de stockage|
|storage_used|Stockage utilisé|Octets|Moyenne|Stockage utilisé|
|storage_limit|Limite de stockage|Octets|Moyenne|Limite de stockage|
|active_connections|Nombre total de connexions actif|Nombre|Moyenne|Nombre total de connexions actif|
|connections_failed|Total de connexions ayant échoué|Nombre|Moyenne|Total de connexions ayant échoué|

## <a name="microsoftdbforpostgresqlservers"></a>Microsoft.DBforPostgreSQL/servers

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|cpu_percent|Pourcentage d’UC|Pourcentage|Moyenne|Pourcentage d’UC|
|compute_limit|Limite d’unités de calcul|Nombre|Moyenne|Limite d’unités de calcul|
|compute_consumption_percent|Pourcentage d’unités de calcul|Pourcentage|Moyenne|Pourcentage d’unités de calcul|
|memory_percent|Pourcentage de mémoire|Pourcentage|Moyenne|Pourcentage de mémoire|
|io_consumption_percent|Pourcentage d’E/S|Pourcentage|Moyenne|Pourcentage d’E/S|
|storage_percent|Pourcentage de stockage|Pourcentage|Moyenne|Pourcentage de stockage|
|storage_used|Stockage utilisé|Octets|Moyenne|Stockage utilisé|
|storage_limit|Limite de stockage|Octets|Moyenne|Limite de stockage|
|active_connections|Nombre total de connexions actif|Nombre|Moyenne|Nombre total de connexions actif|
|connections_failed|Total de connexions ayant échoué|Nombre|Moyenne|Total de connexions ayant échoué|

## <a name="microsoftdevicesiothubs"></a>Microsoft.Devices/IotHubs

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|d2c.telemetry.ingress.allProtocol|Tentatives d’envoi de message de télémétrie|Nombre|Total|Nombre de tentatives d’envoi de messages de télémétrie appareil vers cloud à votre hub IoT|
|d2c.telemetry.ingress.success|Messages de télémétrie envoyés|Nombre|Total|Nombre de messages de télémétrie appareil vers cloud envoyés avec succès à votre hub IoT|
|c2d.commands.egress.complete.success|Commandes terminées|Nombre|Total|Nombre de commandes cloud vers appareil terminées avec succès par l’appareil|
|c2d.commands.egress.abandon.success|Commandes abandonnées|Nombre|Total|Nombre de commandes cloud vers appareil abandonnées par l’appareil|
|c2d.commands.egress.reject.success|Commandes rejetées|Nombre|Total|Nombre de commandes cloud vers appareil rejetées par l’appareil|
|devices.totalDevices|Nombre total d’appareils|Nombre|Total|Nombre d’appareils enregistrés sur votre hub IoT|
|devices.connectedDevices.allProtocol|Appareils connectés|Nombre|Total|Nombre d’appareils connectés à votre hub IoT|
|d2c.telemetry.egress.success|Messages de télémétrie remis|Nombre|Total|Nombre de fois où des messages ont été écrits aux points de terminaison (total)|
|d2c.telemetry.egress.dropped|Messages supprimés|Nombre|Total|Nombre de messages ignorés, car le point de terminaison de livraison était indisponible|
|d2c.telemetry.egress.orphaned|Messages orphelins|Nombre|Total|Nombre de messages ne correspondant à aucun itinéraire, itinéraire de secours compris|
|d2c.telemetry.egress.invalid|Messages non valides|Nombre|Total|Nombre de messages non remis en raison d’une incompatibilité avec le point de terminaison|
|d2c.telemetry.egress.fallback|Messages correspondant à une condition de secours|Nombre|Total|Nombre de messages écrits au point de terminaison de secours|
|d2c.endpoints.egress.eventHubs|Messages remis aux points de terminaison Event Hub|Nombre|Total|Nombre de fois où des messages ont été écrits aux points de terminaison Event Hub|
|d2c.endpoints.latency.eventHubs|Latence des messages des points de terminaison Event Hub|Millisecondes|Moyenne|Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison Event Hub, en millisecondes|
|d2c.endpoints.egress.serviceBusQueues|Messages remis aux points de terminaison de file d’attente Service Bus|Nombre|Total|Nombre de fois où des messages ont été écrits aux points de terminaison de file d’attente Service Bus|
|d2c.endpoints.latency.serviceBusQueues|Latence des messages des points de terminaison de files d’attente Service Bus|Millisecondes|Moyenne|Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison de file d’attente Service Bus, en millisecondes|
|d2c.endpoints.egress.serviceBusTopics|Messages remis aux points de terminaison de rubrique Service Bus|Nombre|Total|Nombre de fois où des messages ont été écrits aux points de terminaison de rubrique Service Bus|
|d2c.endpoints.latency.serviceBusTopics|Latence des messages des points de terminaison de rubriques Service Bus|Millisecondes|Moyenne|Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison de rubrique Service Bus, en millisecondes|
|d2c.endpoints.egress.builtIn.events|Messages remis au point de terminaison intégré (messages/événements)|Nombre|Total|Nombre de fois où des messages ont été écrits au point de terminaison intégré (messages/événements)|
|d2c.endpoints.latency.builtIn.events|Latence de message pour le point de terminaison intégré (messages/événements)|Millisecondes|Moyenne|Latence moyenne entre les entrées de messages vers l’IoT Hub et dans un point de terminaison prédéfini (messages/événements), en millisecondes |
|d2c.endpoints.egress.storage|Messages remis aux points de terminaison de stockage|Nombre|Total|Nombre de fois où des messages ont correctement été écrits sur des points de terminaison de stockage|
|d2c.endpoints.latency.storage|Latence des messages pour les points de terminaison de stockage|Millisecondes|Moyenne|Latence moyenne entre l’entrée des messages dans IoT Hub et l’entrée des message dans un point de terminaison de stockage, en millisecondes|
|d2c.endpoints.egress.storage.bytes|Données écrites dans le stockage|Octets|Total|Volume de données, en octets, écrites sur des points de terminaison de stockage|
|d2c.endpoints.egress.storage.blobs|Objets blob écrits dans le stockage|Nombre|Total|Nombre d’objets blob écrits sur des points de terminaison de stockage|
|d2c.twin.read.success|Lectures de représentations réussies d’appareils|Nombre|Total|Total des lectures de représentations réussies initiées par un appareil.|
|d2c.twin.read.failure|Lectures de représentations d’appareils en échec|Nombre|Total|Total des lectures de représentations en échec initiées par un appareil.|
|d2c.twin.read.size|Taille de la réponse des lectures de représentations des appareils|Octets|Moyenne|Moyenne, minimum et maximum de toutes les lectures de représentations réussies initiées par un appareil.|
|d2c.twin.update.success|Mises à jour de représentations réussies d’appareils|Nombre|Total|Total des mises à jour de représentations réussies initiées par un appareil.|
|d2c.twin.update.failure|Mises à jour de représentations d’appareils en échec|Nombre|Total|Total des mises à jour de représentations en échec initiées par un appareil.|
|d2c.twin.update.size|Taille des mises à jour de représentations d’appareils|Octets|Moyenne|Taille moyenne, minimale et maximale de toutes les mises à jour de représentations réussies initiées par un appareil.|
|c2d.methods.success|Appels de méthode directe réussis|Nombre|Total|Total des appels de méthode directe réussis.|
|c2d.methods.failure|Appels de méthode directe en échec|Nombre|Total|Total des appels de méthode directe en échec.|
|c2d.methods.requestSize|Taille de demande des appels de méthode directe|Octets|Moyenne|Moyenne, minimum et maximum de toutes les demandes de méthode directe réussies.|
|c2d.methods.responseSize|Taille de réponse des appels de méthode directe|Octets|Moyenne|Moyenne, minimum et maximum de toutes les réponses de méthode directe réussies.|
|c2d.twin.read.success|Lectures de représentations réussies de serveur principal|Nombre|Total|Total des lectures de représentations réussies initiées par un serveur principal.|
|c2d.twin.read.failure|Lectures de représentations de serveur principal en échec|Nombre|Total|Total des lectures de représentations en échec initiées par un serveur principal.|
|c2d.twin.read.size|Taille de la réponse des lectures de représentations de serveur principal|Octets|Moyenne|Moyenne, minimum et maximum de toutes les lectures de représentations réussies initiées par un serveur principal.|
|c2d.twin.update.success|Mises à jour de représentations réussies de serveur principal|Nombre|Total|Total des mises à jour de représentations réussies initiées par un serveur principal.|
|c2d.twin.update.failure|Mises à jour de représentations de serveur principal en échec|Nombre|Total|Total des mises à jour de représentations en échec initiées par un serveur principal.|
|c2d.twin.update.size|Taille des mises à jour de représentations de serveur principal|Octets|Moyenne|Taille moyenne, minimale et maximale de toutes les mises à jour de représentations réussies initiées par un serveur principal.|
|twinQueries.success|Requêtes de représentations réussies|Nombre|Total|Total des requêtes de représentations réussies.|
|twinQueries.failure|Requêtes de représentations en échec|Nombre|Total|Total des requêtes de représentations en échec.|
|twinQueries.resultSize|Taille du résultat des requêtes de représentations|Octets|Moyenne|Moyenne, minimum et maximum de la taille du résultat de toutes les requêtes de représentations réussies.|
|jobs.createTwinUpdateJob.success|Créations réussies des travaux de mises à jour de représentations|Nombre|Total|Total des créations réussies de travaux de mises à jour de représentations.|
|jobs.createTwinUpdateJob.failure|Créations des travaux de mises à jour de représentations en échec|Nombre|Total|Total des créations en échec des travaux de mises à jour de représentations.|
|jobs.createDirectMethodJob.success|Créations réussies des travaux d’appel de méthode|Nombre|Total|Total des créations réussies des travaux d’appel de méthode directe.|
|jobs.createDirectMethodJob.failure|Créations des travaux d’appel de méthode en échec|Nombre|Total|Total des créations en échec des travaux d’appel de méthode directe.|
|jobs.listJobs.success|Appels réussis pour répertorier les travaux|Nombre|Total|Total des appels réussis pour répertorier les travaux.|
|jobs.listJobs.failure|Appels en échec pour répertorier les travaux|Nombre|Total|Total des appels en échec pour répertorier les travaux.|
|jobs.cancelJob.success|Annulations de travaux réussies|Nombre|Total|Total des appels réussis pour annuler un travail.|
|jobs.cancelJob.failure|Annulations de travaux en échec|Nombre|Total|Total des appels en échec pour annuler un travail.|
|jobs.queryJobs.success|Requêtes de travaux réussies|Nombre|Total|Total des appels réussis pour interroger les travaux.|
|jobs.queryJobs.failure|Requêtes de travaux en échec|Nombre|Total|Total des appels en échec pour interroger les travaux.|
|jobs.completed|Travaux terminés|Nombre|Total|Total des travaux terminés.|
|jobs.failed|Travaux en échec|Nombre|Total|Total des travaux en échec.|
|d2c.telemetry.ingress.sendThrottle|Nombre d’erreurs de limitation|Nombre|Total|Nombre d’erreurs de limitation causées par des limitations de débit d’appareil|
|dailyMessageQuotaUsed|Nombre total de messages utilisés|Nombre|Moyenne|Nombre total de messages utilisés aujourd'hui|

## <a name="microsofteventhubnamespaces"></a>Microsoft.EventHub/namespaces

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|INREQS|Demandes d’envoi entrantes|Nombre|Total|Nombre total des demandes d’envoi entrantes pour un hub de notification|
|SUCCREQ|Requêtes ayant réussi|Nombre|Total|Nombre total de demandes ayant réussi pour un espace de noms|
|FAILREQ|Demandes ayant échoué|Nombre|Total|Nombre total de demandes ayant échoué pour un espace de noms|
|SVRBSY|Erreurs de serveur occupé|Nombre|Total|Nombre total d’erreurs de serveur occupé pour un espace de noms|
|INTERR|Erreurs internes du serveur|Nombre|Total|Nombre total d’erreurs de serveur internes pour un espace de noms|
|MISCERR|Autres erreurs|Nombre|Total|Nombre total de demandes ayant échoué pour un espace de noms|
|INMSGS|Messages entrants|Nombre|Total|Nombre total de messages entrants pour un espace de noms|
|OUTMSGS|Messages sortants|Nombre|Total|Nombre total de messages sortants pour un espace de noms|
|EHINMBS|Octets entrants|Octets|Total|Débit de messages entrants Event Hub pour un espace de noms|
|EHOUTMBS|Octets sortants|Octets|Total|Nombre total de messages sortants pour un espace de noms|
|EHABL|Archiver les messages de file d’attente|Nombre|Total|Messages d’archive Event Hub dans le backlog pour un espace de noms|
|EHAMSGS|Archiver les messages|Nombre|Total|Messages archivés Event Hub dans un espace de noms|
|EHAMBS|Débit message archive|Octets|Total|Débit de messages archivés Event Hub dans un espace de noms|

## <a name="microsoftlogicworkflows"></a>Microsoft.Logic/workflows

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|RunsStarted|Exécutions démarrées|Nombre|Total|Nombre d’exécutions de flux de travail démarrées.|
|RunsCompleted|Exécutions terminées|Nombre|Total|Nombre d’exécutions de flux de travail terminées.|
|RunsSucceeded|Exécutions réussies|Nombre|Total|Nombre d’exécutions de flux de travail ayant réussi.|
|RunsFailed|Exécutions ayant échoué|Nombre|Total|Nombre d’exécutions de flux de travail ayant échoué.|
|RunsCancelled|Exécutions annulées|Nombre|Total|Nombre d’exécutions de flux de travail annulées.|
|RunLatency|Latence d’exécution|Secondes|Moyenne|Latence des exécutions de flux de travail terminées.|
|RunSuccessLatency|Latence d’exécution ayant réussi|Secondes|Moyenne|Latence des exécutions de flux de travail ayant réussi.|
|RunThrottledEvents|Exécuter événements limités|Nombre|Total|Nombre d’actions de flux de travail ou d’événements limités par déclencheur.|
|RunFailurePercentage|Pourcentage d’échec des exécutions|Pourcentage|Total|Pourcentage d’exécutions de flux de travail ayant échoué.|
|ActionsStarted|Actions démarrées |Nombre|Total|Nombre d’actions de flux de travail démarrées.|
|ActionsCompleted|Actions terminées |Nombre|Total|Nombre d’actions de flux de travail terminées.|
|ActionsSucceeded|Actions ayant réussi |Nombre|Total|Nombre d’actions de flux de travail ayant réussi.|
|ActionsFailed|Actions ayant échoué|Nombre|Total|Nombre d’actions de flux de travail ayant échoué.|
|ActionsSkipped|Actions ignorées |Nombre|Total|Nombre d’actions de flux de travail ignorées.|
|ActionLatency|Latence de l’action |Secondes|Moyenne|Latence des actions de flux de travail terminés.|
|ActionSuccessLatency|Latence de réussite d’action |Secondes|Moyenne|Latence des actions de flux de travail ayant réussi.|
|ActionThrottledEvents|Événements d’action limitée|Nombre|Total|Nombre d’événements limités d’action de flux de travail.|
|TriggersStarted|Déclenchements lancés |Nombre|Total|Nombre de déclencheurs de flux de travail démarrés.|
|TriggersCompleted|Déclenchements terminés |Nombre|Total|Nombre de déclencheurs de flux de travail terminés.|
|TriggersSucceeded|Déclenchements ayant réussi |Nombre|Total|Nombre de déclencheurs de flux de travail ayant réussi.|
|TriggersFailed|Déclenchements ayant échoué |Nombre|Total|Nombre de déclencheurs de flux de travail ayant échoué.|
|TriggersSkipped|Déclenchements ignorés|Nombre|Total|Nombre de déclencheurs de flux de travail ignorés.|
|TriggersFired|Déclenchements activés |Nombre|Total|Nombre de déclencheurs de flux de travail activés.|
|TriggerLatency|Latence de déclencheur |Secondes|Moyenne|Latence des déclenchements de flux de travail terminés.|
|TriggerFireLatency|Latence d’activation de déclencheur |Secondes|Moyenne|Latence des déclencheurs de flux de travail activés.|
|TriggerSuccessLatency|Latence déclencheur ayant réussi |Secondes|Moyenne|Latence des déclencheurs de flux de travail ayant réussi.|
|TriggerThrottledEvents|Événements limités par déclencheur|Nombre|Total|Nombre d’événements de flux de travail limités par déclencheur.|
|BillableActionExecutions|Exécutions d’actions facturables|Nombre|Total|Nombre d’exécutions d’actions de flux de travail facturées.|
|BillableTriggerExecutions|Exécutions de déclencheurs facturables|Nombre|Total|Nombre d’exécutions de déclencheurs de flux de travail facturées.|
|TotalBillableExecutions|Nombre total d’exécutions facturables|Nombre|Total|Nombre d’exécutions de flux de travail facturées.|

## <a name="microsoftnetworkapplicationgateways"></a>Microsoft.Network/applicationGateways

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|Throughput|Throughput|BytesPerSecond|Moyenne|Nombre d’octets par seconde servis par Application Gateway|

## <a name="microsoftnetworkexpressroutecircuits"></a>Microsoft.Network/expressRouteCircuits

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|BytesIn|BytesIn|Nombre|Total|Octets entrant dans Azure|
|BytesOut|BytesOut|Nombre|Total|Octets sortant d’Azure|

## <a name="microsoftnotificationhubsnamespacesnotificationhubs"></a>Microsoft.NotificationHubs/Namespaces/NotificationHubs

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|registration.all|Opération d’inscription|Nombre|Total|Total des opérations d’inscription réussies (requêtes de mises à jour de créations et suppressions). |
|registration.create|Opérations de création d’inscription|Nombre|Total|Total des créations d’inscription réussies.|
|registration.update|Opérations de mise à jour d’inscription|Nombre|Total|Total des mises à jour d’inscription réussies.|
|registration.get|Opérations de lecture d’inscription|Nombre|Total|Total des requêtes d’inscription réussies.|
|registration.delete|Opérations de suppression d’inscription|Nombre|Total|Total des suppressions d’inscription réussies.|
|incoming|Messages entrants|Nombre|Total|Total des appels d’API d’envoi réussis. |
|incoming.scheduled|Notifications Push planifiées envoyées|Nombre|Total|Notifications Push planifiées annulées|
|incoming.scheduled.cancel|Notifications Push planifiées annulées|Nombre|Total|Notifications Push planifiées annulées|
|scheduled.pending|Notifications planifiées en attente|Nombre|Total|Notifications planifiées en attente|
|installation.all|Opérations de gestion de l’installation|Nombre|Total|Opérations de gestion de l’installation|
|installation.get|Opérations d’obtention d’installation|Nombre|Total|Opérations d’obtention d’installation|
|installation.upsert|Opérations de création ou de mise à jour d’installation|Nombre|Total|Opérations de création ou de mise à jour d’installation|
|installation.patch|Opérations d’installation de correctif|Nombre|Total|Opérations d’installation de correctif|
|installation.delete|Opérations de suppression d’installation|Nombre|Total|Opérations de suppression d’installation|
|outgoing.allpns.success|Notifications réussies|Nombre|Total|Total des notifications réussies.|
|outgoing.allpns.invalidpayload|Erreurs de charge utile|Nombre|Total|Nombre d’opérations Push en échec, car le PNS a retourné une erreur de charge utile incorrecte.|
|outgoing.allpns.pnserror|Erreurs système de notification externe|Nombre|Total|Nombre d’opérations Push en échec en raison d’un problème de communication avec le PNS (à l’exclusion des problèmes d’authentification).|
|outgoing.allpns.channelerror|Erreurs de canal|Nombre|Total|Nombre d’opérations Push en échec, car le canal n’était pas valide ni associé à l’application appropriée, limité ou arrivé à expiration.|
|outgoing.allpns.badorexpiredchannel|Erreurs de canal incorrect ou arrivé à expiration|Nombre|Total|Nombre d’opérations Push en échec, car le canal/jeton/registrationId indiqué dans l’inscription est arrivé à expiration ou non valide.|
|outgoing.wns.success|Notifications WNS réussies|Nombre|Total|Total des notifications réussies.|
|outgoing.wns.invalidcredentials|Erreurs d’autorisation WNS (informations d’identification non valides)|Nombre|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci étaient bloquées. (Windows Live ne reconnaît pas les informations d’identification).|
|outgoing.wns.badchannel|Erreur de canal WNS incorrect|Nombre|Total|Nombre d’opérations Push en échec, car l’élément ChannelURI indiqué dans l’inscription n’a pas été reconnu (état WNS : 404 introuvable).|
|outgoing.wns.expiredchannel|Erreur de canal WNS arrivé à expiration|Nombre|Total|Nombre d’opérations Push en échec, car l’élément ChannelURI est arrivé à expiration (état WNS : 410 Supprimé).|
|outgoing.wns.throttled|Notifications WNS limitées|Nombre|Total|Nombre d’opérations Push en échec, car WNS limite cette application (état WNS : 406 Non accepté).|
|outgoing.wns.tokenproviderunreachable|Erreurs d’autorisation WNS (inaccessible)|Nombre|Total|Windows Live n’est pas accessible.|
|outgoing.wns.invalidtoken|Erreurs d’autorisation WNS (jeton non valide)|Nombre|Total|Le jeton fourni à WNS n’est pas valide (état WNS : 401 Non autorisé).|
|outgoing.wns.wrongtoken|Erreurs d’autorisation WNS (jeton incorrect)|Nombre|Total|Le jeton fourni à WNS est valide, mais il concerne une autre application (état WNS : 403 Interdit). Cela peut se produire si l’élément ChannelURI indiqué dans l’inscription est associé à une autre application. Vérifiez que l’application cliente est associée à l’application dont les informations d’identification figurent dans le hub de notification.|
|outgoing.wns.invalidnotificationformat|Format de notification WNS non valide|Nombre|Total|Le format de la notification n’est pas valide (état WNS : 400). Notez que WNS ne rejette pas toutes les charges utiles non valides.|
|outgoing.wns.invalidnotificationsize|Erreur de taille de notification WNS non valide|Nombre|Total|La charge utile de notification est trop grande (état WNS : 413).|
|outgoing.wns.channelthrottled|Canal WNS limité|Nombre|Total|La notification a été supprimée, car l’élément ChannelURI indiqué dans l’inscription est limité (en-tête de réponse WNS : X-WNS-NotificationStatus:channelThrottled).|
|outgoing.wns.channeldisconnected|Canal WNS déconnecté|Nombre|Total|La notification a été supprimée, car l’élément ChannelURI contenu dans l’inscription est limité (en-tête de réponse WNS : X-WNS-DeviceConnectionStatus : déconnecté).|
|outgoing.wns.dropped|Notifications WNS supprimées|Nombre|Total|La notification a été supprimée, car l’élément ChannelURI indiqué dans l’inscription est limité (X-WNS-NotificationStatus : supprimé, mais pas X-WNS-DeviceConnectionStatus : déconnecté).|
|outgoing.wns.pnserror|Erreurs WNS|Nombre|Total|La notification n’a pas été remise en raison d’erreurs de communication avec WNS.|
|outgoing.wns.authenticationerror|Erreurs d’authentification WNS|Nombre|Total|La notification n’a pas été remise en raison d’erreurs de communication avec Windows Live, d’informations d’identification non valides ou d’un jeton incorrect.|
|outgoing.apns.success|Notifications APNS réussies|Nombre|Total|Total des notifications réussies.|
|outgoing.apns.invalidcredentials|Erreurs d’autorisation APNS|Nombre|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci étaient bloquées.|
|outgoing.apns.badchannel|Erreur de canal APNS incorrect|Nombre|Total|Nombre d’opérations Push en échec, car le jeton n’est pas valide (code d’état APNS : 8).|
|outgoing.apns.expiredchannel|Erreur de canal APNS arrivé à expiration|Nombre|Total|Nombre de jetons qui ont été invalidés par le canal de commentaires APNS.|
|outgoing.apns.invalidnotificationsize|Erreur de taille de notification APNS non valide|Nombre|Total|Nombre d’opérations Push en échec, car la charge utile était trop importante (code d’état APNS : 7).|
|outgoing.apns.pnserror|Erreurs APNS|Nombre|Total|Nombre d’opérations Push en échec en raison d’erreurs de communication avec APNS.|
|outgoing.gcm.success|Notifications GCM réussies|Nombre|Total|Total des notifications réussies.|
|outgoing.gcm.invalidcredentials|Erreurs d’autorisation GCM (informations d’identification non valides)|Nombre|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci étaient bloquées.|
|outgoing.gcm.badchannel|Erreur de canal GCM incorrect|Nombre|Total|Nombre d’opérations Push en échec, car l’élément registrationId contenu dans l’inscription n’a pas été reconnu (résultat GCM : inscription non valide).|
|outgoing.gcm.expiredchannel|Erreur de canal GCM arrivé à expiration|Nombre|Total|Nombre d’opérations Push en échec, car l’élément registrationId contenu dans l’inscription est arrivé à expiration (résultat GCM : NotRegistered).|
|outgoing.gcm.throttled|Notifications GCM limitées|Nombre|Total|Nombre d’opérations Push en échec, car GCM a limité cette application (code d’état GCM : 501-599 ou résultat : indisponible).|
|outgoing.gcm.invalidnotificationformat|Format de notification GCM non valide|Nombre|Total|Nombre d’opérations Push en échec, car la charge utile n’était pas mise en forme correctement (résultat GCM : InvalidDataKey ou InvalidTtl).|
|outgoing.gcm.invalidnotificationsize|Erreur de taille de notification GCM non valide|Nombre|Total|Nombre d’opérations Push en échec, car la charge utile était trop importante (résultat GCM : MessageTooBig).|
|outgoing.gcm.wrongchannel|Erreur de canal GCM incorrect|Nombre|Total|Nombre d’opérations Push en échec, car l’élément registrationId contenu dans l’inscription n’est pas associé à l’application actuelle (résultat GCM : InvalidPackageName).|
|outgoing.gcm.pnserror|Erreurs GCM|Nombre|Total|Nombre d’opérations Push en échec en raison d’erreurs de communication avec GCM.|
|outgoing.gcm.authenticationerror|Erreurs d’authentification GCM|Nombre|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies, celles-ci sont bloquées ou l’élément SenderId n’est pas configuré correctement dans l’application (résultat GCM : MismatchedSenderId).|
|outgoing.mpns.success|Notifications MPNS réussies|Nombre|Total|Total des notifications réussies.|
|outgoing.mpns.invalidcredentials|Informations d’identification MPNS non valides|Nombre|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci sont bloquées.|
|outgoing.mpns.badchannel|Erreur de canal incorrect MPNS|Nombre|Total|Nombre d’opérations Push en échec, car l’élément ChannelURI contenu dans l’inscription n’a pas été reconnu (état MPNS : 404 introuvable).|
|outgoing.mpns.throttled|Notifications MPNS limitées|Nombre|Total|Nombre d’opérations Push en échec, car MPNS limite cette application (WNS MPNS : 406 Non accepté).|
|outgoing.mpns.invalidnotificationformat|Format de notification MPNS non valide|Nombre|Total|Nombre d’opérations Push en échec, car la charge utile de la notification était trop importante.|
|outgoing.mpns.channeldisconnected|Canal MPNS déconnecté|Nombre|Total|Nombre d’opérations Push en échec, car l’élément ChannelURI contenu dans l’inscription était déconnecté (état MPNS : 412 introuvable).|
|outgoing.mpns.dropped|Notifications MPNS supprimées|Nombre|Total|Nombre d’opérations Push qui ont été supprimées par MPNS (en-tête de réponse MPNS : X-NotificationStatus : QueueFull ou Suppressed).|
|outgoing.mpns.pnserror|Erreurs MPNS|Nombre|Total|Nombre d’opérations Push en échec en raison d’erreurs de communication avec MPNS.|
|outgoing.mpns.authenticationerror|Erreurs d’authentification MPNS|Nombre|Total|Nombre d’opérations Push en échec, car le PNS n’a pas accepté les informations d’identification fournies ou celles-ci sont bloquées.|
|notificationhub.pushes|Toutes les notifications sortantes|Nombre|Total|Toutes les notifications sortantes du hub de notification|
|incoming.all.requests|Toutes les demandes entrantes|Nombre|Total|Nombre total des demandes entrantes pour un hub de notification|
|incoming.all.failedrequests|Toutes les requêtes entrantes ayant échoué|Nombre|Total|Nombre total des demandes entrantes ayant échoué pour un hub de notification|

## <a name="microsoftsearchsearchservices"></a>Microsoft.Search/searchServices

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|SearchLatency|Latence de recherche|Secondes|Moyenne|Latence moyenne de recherche du service de recherche|
|SearchQueriesPerSecond|Requêtes de recherche par seconde|CountPerSecond|Moyenne|Requêtes de recherche par seconde pour le service de recherche|
|ThrottledSearchQueriesPercentage|Pourcentage de requêtes de recherche limitées|Pourcentage|Moyenne|Pourcentage de requêtes de recherche limitées par le service de recherche|

## <a name="microsoftservicebusnamespaces"></a>Microsoft.ServiceBus/namespaces

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|CPUXNS|Utilisation du processeur par espace de noms|Pourcentage|Maximale|Mesure d’utilisation du processeur de l’espace de noms Service Bus Premium|
|WSXNS|Utilisation de la taille mémoire par espace de noms|Pourcentage|Maximale|Mesure d’utilisation de la mémoire de l’espace de noms Service Bus Premium|

## <a name="microsoftsqlserversdatabases"></a>Microsoft.Sql/servers/databases

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|cpu_percent|Pourcentage UC|Pourcentage|Moyenne|Pourcentage UC|
|physical_data_read_percent|Pourcentage E/S des données|Pourcentage|Moyenne|Pourcentage E/S des données|
|log_write_percent|Pourcentage E/S du journal|Pourcentage|Moyenne|Pourcentage E/S du journal|
|dtu_consumption_percent|Pourcentage DTU|Pourcentage|Moyenne|Pourcentage DTU|
|storage|Taille de base de données totale|Octets|Maximale|Taille de base de données totale|
|connection_successful|Connexions réussies|Nombre|Total|Connexions réussies|
|connection_failed|Connexions ayant échoué|Nombre|Total|Connexions ayant échoué|
|blocked_by_firewall|Bloqué par le pare-feu|Nombre|Total|Bloqué par le pare-feu|
|deadlock|Blocages|Nombre|Total|Blocages|
|storage_percent|Pourcentage de la taille de la base de données|Pourcentage|Maximale|Pourcentage de la taille de la base de données|
|xtp_storage_percent|Pourcentage de stockage OLTP en mémoire|Pourcentage|Moyenne|Pourcentage de stockage OLTP en mémoire|
|workers_percent|Pourcentage de travaux|Pourcentage|Moyenne|Pourcentage de travaux|
|sessions_percent|Pourcentage de sessions|Pourcentage|Moyenne|Pourcentage de sessions|
|dtu_limit|Limite DTU|Nombre|Moyenne|Limite DTU|
|dtu_used|DTU utilisé|Nombre|Moyenne|DTU utilisé|
|dwu_limit|Limite DWU|Nombre|Maximale|Limite DWU|
|dwu_consumption_percent|Pourcentage DWU|Pourcentage|Maximale|Pourcentage DWU|
|dwu_used|DWU utilisé|Nombre|Maximale|DWU utilisé|

## <a name="microsoftsqlserverselasticpools"></a>Microsoft.Sql/servers/elasticPools

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|cpu_percent|Pourcentage UC|Pourcentage|Moyenne|Pourcentage UC|
|database_cpu_percent|Pourcentage UC|Pourcentage|Moyenne|Pourcentage UC|
|physical_data_read_percent|Pourcentage E/S des données|Pourcentage|Moyenne|Pourcentage E/S des données|
|database_physical_data_read_percent|Pourcentage E/S des données|Pourcentage|Moyenne|Pourcentage E/S des données|
|log_write_percent|Pourcentage E/S du journal|Pourcentage|Moyenne|Pourcentage E/S du journal|
|database_log_write_percent|Pourcentage E/S du journal|Pourcentage|Moyenne|Pourcentage E/S du journal|
|dtu_consumption_percent|Pourcentage DTU|Pourcentage|Moyenne|Pourcentage DTU|
|database_dtu_consumption_percent|Pourcentage DTU|Pourcentage|Moyenne|Pourcentage DTU|
|storage_percent|Pourcentage de stockage|Pourcentage|Moyenne|Pourcentage de stockage|
|workers_percent|Pourcentage de travaux|Pourcentage|Moyenne|Pourcentage de travaux|
|database_workers_percent|Pourcentage de travaux|Pourcentage|Moyenne|Pourcentage de travaux|
|sessions_percent|Pourcentage de sessions|Pourcentage|Moyenne|Pourcentage de sessions|
|database_sessions_percent|Pourcentage de sessions|Pourcentage|Moyenne|Pourcentage de sessions|
|eDTU_limit|Limite eDTU|Nombre|Moyenne|Limite eDTU|
|storage_limit|Limite de stockage|Octets|Moyenne|Limite de stockage|
|eDTU_used|eDTU utilisé|Nombre|Moyenne|eDTU utilisé|
|storage_used|Stockage utilisé|Octets|Moyenne|Stockage utilisé|
|database_storage_used|Stockage utilisé|Octets|Moyenne|Stockage utilisé|
|xtp_storage_percent|Pourcentage de stockage OLTP en mémoire|Pourcentage|Moyenne|Pourcentage de stockage OLTP en mémoire|

## <a name="microsoftsqlservers"></a>Microsoft.Sql/servers

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|dtu_consumption_percent|Pourcentage DTU|Pourcentage|Moyenne|Pourcentage DTU|
|database_dtu_consumption_percent|Pourcentage DTU|Pourcentage|Moyenne|Pourcentage DTU|
|storage_used|Stockage utilisé|Octets|Moyenne|Stockage utilisé|
|database_storage_used|Stockage utilisé|Octets|Moyenne|Stockage utilisé|

## <a name="microsoftstreamanalyticsstreamingjobs"></a>Microsoft.StreamAnalytics/streamingjobs

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|ResourceUtilization|Utilisation de % d’unités de diffusion|Pourcentage|Maximale|Utilisation de % d’unités de diffusion|
|InputEvents|Événements d’entrée|Nombre|Total|Événements d’entrée|
|InputEventBytes|Octets des événements d’entrée|Octets|Total|Octets des événements d’entrée|
|LateInputEvents|Événements d’entrée tardifs|Nombre|Total|Événements d’entrée tardifs|
|OutputEvents|Événements de sortie|Nombre|Total|Événements de sortie|
|ConversionErrors|Erreurs de conversion de données|Nombre|Total|Erreurs de conversion de données|
|Errors|Erreurs d’exécution|Nombre|Total|Erreurs d’exécution|
|DroppedOrAdjustedEvents|Événements en désordre|Nombre|Total|Événements en désordre|
|AMLCalloutRequests|Requêtes de fonction|Nombre|Total|Requêtes de fonction|
|AMLCalloutFailedRequests|Requêtes de fonction ayant échoué|Nombre|Total|Requêtes de fonction ayant échoué|
|AMLCalloutInputEvents|Événements de fonction|Nombre|Total|Événements de fonction|

## <a name="microsoftwebserverfarms"></a>Microsoft.Web/serverfarms

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|CpuPercentage|Pourcentage UC|Pourcentage|Moyenne|Pourcentage UC|
|MemoryPercentage|Pourcentage de mémoire|Pourcentage|Moyenne|Pourcentage de mémoire|
|DiskQueueLength|Longueur de file d'attente de disque|Nombre|Total|Longueur de file d'attente de disque|
|HttpQueueLength|Longueur de la file d’attente HTTP|Nombre|Total|Longueur de la file d’attente HTTP|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|

## <a name="microsoftwebsites-excluding-functions"></a>Microsoft.Web/sites (à l’exclusion de Functions)

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|CpuTime|Temps processeur|Secondes|Total|Temps processeur|
|Requests|Requests|Nombre|Total|Requests|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|
|Http101|HTTP 101|Nombre|Total|HTTP 101|
|Http2xx|Http 2xx|Nombre|Total|Http 2xx|
|Http3xx|Http 3xx|Nombre|Total|Http 3xx|
|Http401|Http 401|Nombre|Total|Http 401|
|Http403|Http 403|Nombre|Total|Http 403|
|Http404|Http 404|Nombre|Total|Http 404|
|Http406|Http 406|Nombre|Total|Http 406|
|Http4xx|Http 4xx|Nombre|Total|Http 4xx|
|Http5xx|Erreurs de serveur http|Nombre|Total|Erreurs de serveur http|
|MemoryWorkingSet|Plage de travail de la mémoire|Octets|Moyenne|Plage de travail de la mémoire|
|AverageMemoryWorkingSet|Plage de travail moyenne de la mémoire|Octets|Moyenne|Plage de travail moyenne de la mémoire|
|AverageResponseTime|Temps de réponse moyen|Secondes|Moyenne|Temps de réponse moyen|

## <a name="microsoftwebsites-functions"></a>Microsoft.Web/sites (Functions)

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|
|Http5xx|Erreurs de serveur http|Nombre|Total|Erreurs de serveur http|
|MemoryWorkingSet|Plage de travail de la mémoire|Octets|Moyenne|Plage de travail de la mémoire|
|AverageMemoryWorkingSet|Plage de travail moyenne de la mémoire|Octets|Moyenne|Plage de travail moyenne de la mémoire|
|FunctionExecutionUnits|Unités d’exécution de fonctions|Nombre|Moyenne|Unités d’exécution de fonctions|
|FunctionExecutionCount|Nombre d’exécutions de fonctions|Nombre|Moyenne|Nombre d’exécutions de fonctions|

## <a name="microsoftwebsitesslots"></a>Microsoft.Web/sites/slots

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|CpuTime|Temps processeur|Secondes|Total|Temps processeur|
|Requests|Requests|Nombre|Total|Requests|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|
|Http101|HTTP 101|Nombre|Total|HTTP 101|
|Http2xx|Http 2xx|Nombre|Total|Http 2xx|
|Http3xx|Http 3xx|Nombre|Total|Http 3xx|
|Http401|Http 401|Nombre|Total|Http 401|
|Http403|Http 403|Nombre|Total|Http 403|
|Http404|Http 404|Nombre|Total|Http 404|
|Http406|Http 406|Nombre|Total|Http 406|
|Http4xx|Http 4xx|Nombre|Total|Http 4xx|
|Http5xx|Erreurs de serveur http|Nombre|Total|Erreurs de serveur http|
|MemoryWorkingSet|Plage de travail de la mémoire|Octets|Moyenne|Plage de travail de la mémoire|
|AverageMemoryWorkingSet|Plage de travail moyenne de la mémoire|Octets|Moyenne|Plage de travail moyenne de la mémoire|
|AverageResponseTime|Temps de réponse moyen|Secondes|Moyenne|Temps de réponse moyen|
|FunctionExecutionUnits|Unités d’exécution de fonctions|Nombre|Moyenne|Unités d’exécution de fonctions|
|FunctionExecutionCount|Nombre d’exécutions de fonctions|Nombre|Moyenne|Nombre d’exécutions de fonctions|

## <a name="microsoftwebhostingenvironmentsmultirolepools"></a>Microsoft.Web/hostingEnvironments/multiRolePools

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|Requests|Requests|Nombre|Total|Requests|
|BytesReceived|Données entrantes|Octets|Total|Données entrantes|
|BytesSent|Données sortantes|Octets|Total|Données sortantes|
|Http101|HTTP 101|Nombre|Total|HTTP 101|
|Http2xx|Http 2xx|Nombre|Total|Http 2xx|
|Http3xx|Http 3xx|Nombre|Total|Http 3xx|
|Http401|Http 401|Nombre|Total|Http 401|
|Http403|Http 403|Nombre|Total|Http 403|
|Http404|Http 404|Nombre|Total|Http 404|
|Http406|Http 406|Nombre|Total|Http 406|
|Http4xx|Http 4xx|Nombre|Total|Http 4xx|
|Http5xx|Erreurs de serveur http|Nombre|Total|Erreurs de serveur http|
|AverageResponseTime|Temps de réponse moyen|Secondes|Moyenne|Temps de réponse moyen|
|CpuPercentage|Pourcentage UC|Pourcentage|Moyenne|Pourcentage UC|
|MemoryPercentage|Pourcentage de mémoire|Pourcentage|Moyenne|Pourcentage de mémoire|
|DiskQueueLength|Longueur de file d'attente de disque|Nombre|Total|Longueur de file d'attente de disque|
|HttpQueueLength|Longueur de la file d’attente HTTP|Nombre|Total|Longueur de la file d’attente HTTP|
|ActiveRequests|Requêtes actives|Nombre|Total|Requêtes actives|
|TotalFrontEnds|Nombre total de serveurs frontaux|Nombre|Moyenne|Nombre total de serveurs frontaux|
|SmallAppServicePlanInstances|Workers de plan App Service de petite taille|Nombre|Moyenne|Workers de plan App Service de petite taille|
|MediumAppServicePlanInstances|Workers de plan App Service de taille moyenne|Nombre|Moyenne|Workers de plan App Service de taille moyenne|
|LargeAppServicePlanInstances|Workers de plan App Service de grande taille|Nombre|Moyenne|Workers de plan App Service de grande taille|

## <a name="microsoftwebhostingenvironmentsworkerpools"></a>Microsoft.Web/hostingEnvironments/workerPools

|Mesure|Nom d’affichage de la mesure|Unité|Type d’agrégation|Description|
|---|---|---|---|---|
|WorkersTotal|Nombre total de workers|Nombre|Moyenne|Nombre total de workers|
|WorkersAvailable|Workers disponibles|Nombre|Moyenne|Workers disponibles|
|WorkersUsed|Workers utilisés|Nombre|Moyenne|Workers utilisés|

## <a name="next-steps"></a>Étapes suivantes
* [En savoir plus sur les mesures dans Azure Monitor](monitoring-overview-metrics.md)
* [Créer des alertes sur les mesures](insights-receive-alert-notifications.md)
* [Exporter des mesures vers le stockage, un hub d’événements ou Log Analytics](monitoring-overview-of-diagnostic-logs.md)

