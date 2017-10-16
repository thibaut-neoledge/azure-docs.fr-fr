---
title: "Modifier les paramètres de cluster Azure Service Fabric | Microsoft Docs"
description: "Cet article décrit les paramètres de structure et les stratégies de mise à niveau de la structure que vous pouvez personnaliser."
services: service-fabric
documentationcenter: .net
author: chackdan
manager: timlt
editor: 
ms.assetid: 7ced36bf-bd3f-474f-a03a-6ebdbc9677e2
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/15/2017
ms.author: chackdan
ms.openlocfilehash: cedfba78d8d1186618cf4f61154c5d88a00eb12b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="customize-service-fabric-cluster-settings-and-fabric-upgrade-policy"></a>Personnaliser les paramètres de cluster Service Fabric et la stratégie de mise à niveau de la structure
Ce document vous explique comment personnaliser les différents paramètres et la stratégie de mise à niveau de la structure pour votre cluster Service Fabric. Vous pouvez les personnaliser sur le [portail Azure](https://portal.azure.com) ou à l’aide d’un modèle Azure Resource Manager.

> [!NOTE]
> Tous les paramètres ne sont pas disponibles dans le portail. Si un paramètre répertorié ci-dessous n’est pas disponible via le portail, personnalisez-le à l’aide d’un modèle Azure Resource Manager.
> 

## <a name="customize-cluster-settings-using-resource-manager-templates"></a>Personnaliser les paramètres de cluster à l’aide de modèles Resource Manager
Les étapes ci-dessous montrent comment ajouter un nouveau paramètre *MaxDiskQuotaInMB* à la section *Diagnostics*.

1. Accédez à https://resources.azure.com.
2. Accédez à votre abonnement en développant **Abonnements** -> **Groupes de ressources** -> **Microsoft.ServiceFabric** -> **\<Nom de votre cluster>**.
3. Dans l’angle supérieur droit, sélectionnez **Lecture/écriture**.
4. Sélectionnez **Modifier**, mettez à jour l’élément JSON `fabricSettings` et ajoutez un nouvel élément :

```
      {
        "name": "Diagnostics",
        "parameters": [
          {
            "name": "MaxDiskQuotaInMB",
            "value": "65536"
          }
        ]
      }
```

Voici une liste des paramètres Fabric que vous pouvez personnaliser, classés par section.

## <a name="diagnostics"></a>Diagnostics
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| ConsumerInstances |String |Liste des instances de consommateur DCA. |
| ProducerInstances |String |Liste des instances de producteur DCA. |
| AppEtwTraceDeletionAgeInDays |Entier (valeur par défaut : 3) |Délai en jours à l’issue duquel nous supprimons les anciens fichiers ETL contenant les traces ETW d’application. |
| AppDiagnosticStoreAccessRequiresImpersonation |Valeur booléenne (valeur par défaut : true) |Indique si l’emprunt d’identité est requis lors de l’accès aux magasins de diagnostics pour le compte de l’application. |
| MaxDiskQuotaInMB |Entier (valeur par défaut : 65536) |Quota de disque (en Mo) pour les fichiers journaux de Windows Fabric. |
| DiskFullSafetySpaceInMB |Entier (valeur par défaut : 1024) |Espace disque restant (en Mo) à protéger contre toute utilisation par DCA. |
| ApplicationLogsFormatVersion |Entier (valeur par défaut : 0) |Version du format des journaux de l’application. Valeurs prises en charge : 0 et 1. La version 1 comprend davantage de champs de l’enregistrement d’événement ETW que la version 0. |
| ClusterId |string |ID unique du cluster. Valeur générée lors de la création du cluster. |
| EnableTelemetry |Valeur booléenne (valeur par défaut : true) |Ce paramètre active ou désactive la télémétrie. |
| EnableCircularTraceSession |Valeur booléenne (valeur par défaut : false) |Indicateur spécifiant si les sessions de trace circulaire doivent être utilisées. |

## <a name="traceetw"></a>Trace/Etw
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| Level |Entier (valeur par défaut : 4) |Le niveau etw de la trace accepte les valeurs 1, 2, 3, 4. Pour assurer la prise en charge, vous devez conserver le niveau de trace sur 4 |

## <a name="performancecounterlocalstore"></a>PerformanceCounterLocalStore
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| IsEnabled |Valeur booléenne (valeur par défaut : true) |Indicateur spécifiant si la collection de compteurs de performance sur le nœud local est activée. |
| SamplingIntervalInSeconds |Entier (valeur par défaut : 60) |Intervalle d’échantillonnage des compteurs de performances collectés. |
| Counters |string |Liste séparée par des virgules, des compteurs de performance à collecter. |
| MaxCounterBinaryFileSizeInMB |Entier (valeur par défaut : 1) |Taille maximale (en Mo) de chaque fichier binaire de compteur de performances. |
| NewCounterBinaryFileCreationIntervalInMinutes |Entier (valeur par défaut : 10) |Durée maximale (en secondes) après lequel un fichier binaire de compteur de performances est créé. |

## <a name="setup"></a>Paramétrage
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| FabricDataRoot |String |Répertoire racine des données Service Fabric. La valeur par défaut pour Azure est d:\svcfab |
| FabricDataRoot |String |Répertoire racine du journal Service Fabric. Il s’agit de l’emplacement des journaux et des traces de SF. |
| ServiceRunAsAccountName |String |Nom du compte sous lequel exécuter le service hôte Fabric. |
| ServiceStartupType |String |Type de démarrage du service hôte Fabric. |
| SkipFirewallConfiguration |Valeur booléenne (valeur par défaut : false) |Indique si les paramètres de pare-feu doivent être définis par le système ou non. Cela s’applique uniquement si vous utilisez le pare-feu Windows. Si vous utilisez des pare-feu tiers, vous devez alors ouvrir les ports que le système et les applications doivent utiliser |

## <a name="transactionalreplicator"></a>TransactionalReplicator
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| MaxCopyQueueSize |Valeur Uint (valeur par défaut : 16384) |Valeur maximale définissant la taille initiale de la file d’attente qui gère les opérations de réplication. Ce nombre doit être une puissance de 2. Si, pendant l’exécution, la file d’attente atteint cette taille, les opérations sont limitées entre les réplicateurs principal et secondaire. |
| BatchAcknowledgementInterval | Durée en secondes (valeur par défaut : 0.015) | Spécifiez la durée en secondes. Détermine le délai respecté par le réplicateur après la réception d’une opération et avant de renvoyer un accusé de réception. Les autres opérations reçues pendant cette période verront leur accusé de réception renvoyé dans un seul message, ce qui diminue le trafic réseau mais peut potentiellement réduire le débit du réplicateur. |
| MaxReplicationMessageSize |Valeur Uint (valeur par défaut : 52428800) | Taille maximale des messages des opérations de réplication. Valeur par défaut : 50 Mo. |
| ReplicatorAddress |Chaîne (valeur par défaut : "localhost:0") | Point de terminaison sous la forme d’une chaîne « IP:port» utilisée par le réplicateur Windows Fabric pour se connecter à d’autres réplicas afin d’envoyer/recevoir des opérations. |
| InitialPrimaryReplicationQueueSize |Valeur Uint (valeur par défaut : 64) | Valeur définissant la taille initiale de la file d’attente qui gère les opérations de réplication sur le réplicateur principal. Ce nombre doit être une puissance de 2.|
| MaxPrimaryReplicationQueueSize |Valeur Uint (valeur par défaut : 8192) |Nombre maximum d’opérations pouvant exister dans la file d’attente de réplication principale. Ce nombre doit être une puissance de 2. |
| MaxPrimaryReplicationQueueMemorySize |Valeur Uint (valeur par défaut : 0) |Valeur maximale de la file d’attente de réplication principale en octets. |
| InitialSecondaryReplicationQueueSize |Valeur Uint (valeur par défaut : 64) |Valeur définissant la taille initiale de la file d’attente qui gère les opérations de réplication sur le réplicateur secondaire. Ce nombre doit être une puissance de 2. |
| MaxSecondaryReplicationQueueSize |Valeur Uint (valeur par défaut : 16384) |Nombre maximum d’opérations pouvant exister dans la file d’attente de réplication secondaire. Ce nombre doit être une puissance de 2. |
| MaxSecondaryReplicationQueueMemorySize |Valeur Uint (valeur par défaut : 0) |Valeur maximale de la file d’attente de réplication secondaire en octets. |
| SecondaryClearAcknowledgedOperations |Valeur booléenne (valeur par défaut : false) |Valeur booléenne qui contrôle si les opérations sur le réplicateur secondaire sont effacées, une fois leur réception confirmée au réplicateur principal (vidées sur le disque). Le réglage de ce paramètre sur TRUE augmente le nombre de lectures de disque sur le nouveau réplica principal, lors du traitement des réplicas après un basculement. |
| MaxMetadataSizeInKB |Entier (valeur par défaut : 4) |Taille maximale des métadonnées du flux de journal. |
| MaxRecordSizeInKB |Valeur Uint (valeur par défaut : 1024) | Taille maximale de l’enregistrement du flux de journal. |
| CheckpointThresholdInMB |Entier (valeur par défaut : 50) |Un point de contrôle est initialisé lorsque l’utilisation du journal dépasse cette valeur. |
| MaxAccumulatedBackupLogSizeInMB |Entier (valeur par défaut : 800) |Taille cumulée maximale (en Mo) des journaux de sauvegarde dans une séquence de journaux de sauvegarde donnée. Des demandes de sauvegarde incrémentielle échouent si la sauvegarde incrémentielle génère un journal de sauvegarde qui provoque la cumulation des journaux de sauvegarde, étant donné que la sauvegarde complète pertinente est supérieure à cette taille. Dans ce cas, l’utilisateur doit effectuer une sauvegarde complète. |
| MaxWriteQueueDepthInKB |Entier (valeur par défaut : 0) | Nombre entier correspondant à la profondeur de la file d’attente d’écritures que le journal de base peut utiliser de la manière spécifiée, en kilo-octets, pour le journal associé à ce réplica. Cette valeur est le nombre maximum d’octets pouvant être en attente pendant les mises à jour du journal de base. Ce peut être 0 pour que le journal de base calcule une valeur appropriée, ou un multiple de 4. |
| SharedLogId |String |Identificateur du journal partagé. Cette valeur est un GUID et doit être propre à chaque journal partagé. |
| SharedLogPath |String |Chemin d’accès au journal partagé. Si cette valeur n’est pas spécifiée, le journal partagé par défaut est utilisé. |
| SlowApiMonitoringDuration |Durée en secondes, la valeur par défaut est 300 | Spécifiez la durée de l’API avant l’émission d’un événement d’avertissement.|
| MinLogSizeInMB |Entier (valeur par défaut : 0) |Taille minimale du journal des transactions. Le journal ne peut pas être tronqué à une taille inférieure à ce paramètre. 0 indique que le réplicateur détermine la taille minimale du journal en fonction d’autres paramètres. Si vous augmentez cette valeur, vous augmentez la possibilité de faire des copies partielles et des sauvegardes incrémentielles, car la probabilité de la troncation des enregistrements de journaux pertinents est réduite. |

## <a name="fabricclient"></a>FabricClient
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| NodeAddresses |Chaîne (valeur par défaut : "") |Collection d’adresses (chaînes de connexion) sur différents nœuds pouvant servir à communiquer avec le service d’attribution de noms. Au début, le client se connecte en sélectionnant une des adresses de façon aléatoire. Si plusieurs chaînes de connexion sont fournies et qu’une échoue en raison d’une erreur de communication ou de délai d’attente, le client utilise l’adresse suivante dans la liste. Pour plus d’informations sur la sémantique des nouvelles tentatives, consultez la section sur les nouvelles tentatives concernant l’adresse du service d’attribution de noms. |
| ConnectionInitializationTimeout |Durée en secondes (valeur par défaut : 2) |Spécifiez la durée en secondes. Délai d’expiration de connexion à respecter pour chaque client avant de tenter d’établir une connexion à la passerelle. |
| PartitionLocationCacheLimit |Entier (valeur par défaut : 100000) |Nombre de partitions mises en cache pour la résolution de service (0 pour aucune limite). |
| ServiceChangePollInterval |Durée en secondes (valeur par défaut : 120) |Spécifiez la durée en secondes. L’intervalle entre deux interrogations consécutives de modifications de service est différent entre le client et la passerelle pour les rappels de notifications de modification de service enregistrés. |
| KeepAliveIntervalInSeconds |Entier (valeur par défaut : 20) |Durée pendant laquelle le transport FabricClient envoie des messages de conservation d’activité à la passerelle. À 0, le paramètre keepAlive est désactivé. Cette valeur doit être un entier positif. |
| HealthOperationTimeout |Durée en secondes (valeur par défaut : 120) |Spécifiez la durée en secondes. Délai d’expiration d’un message de rapport envoyé au Gestionnaire d’intégrité. |
| HealthReportSendInterval |Durée en secondes, la valeur par défaut est 30 |Spécifiez la durée en secondes. Délai à l’issue duquel le composant concerné envoie les rapports d’intégrité cumulés au Gestionnaire d’intégrité. |
| HealthReportRetrySendInterval |Durée en secondes, la valeur par défaut est 30 |Spécifiez la durée en secondes. Délai à l’issue duquel le composant concerné envoie des rapports d’intégrité cumulés au Gestionnaire d’intégrité. |
| RetryBackoffInterval |Durée en secondes (valeur par défaut : 3) |Spécifiez la durée en secondes. Délai de temporisation à respecter avant de retenter l’opération. |
| MaxFileSenderThreads |Valeur Uint (valeur par défaut : 10) |Nombre maximum de fichiers transférés en parallèle. |

## <a name="common"></a>Courant
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| PerfMonitorInterval |Durée en secondes (valeur par défaut : 1) |Spécifiez la durée en secondes. Interface de surveillance des performances. Une valeur nulle ou négative désactive la surveillance. |

## <a name="healthmanager"></a>HealthManager
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| EnableApplicationTypeHealthEvaluation |Valeur booléenne (valeur par défaut : false) |Stratégie d’évaluation de l’intégrité du cluster : activer pour chaque évaluation de l’intégrité du type d’application. |

## <a name="fabricnode"></a>FabricNode
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| StateTraceInterval |Durée en secondes, la valeur par défaut est 300 |Spécifiez la durée en secondes. Durée du suivi d’état sur chaque nœud et les nœuds actifs sur FM/FMM. |

## <a name="nodedomainids"></a>NodeDomainIds
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| UpgradeDomainId |Chaîne (valeur par défaut : "") |Décrit le domaine de mise à niveau auquel un nœud appartient. |
| PropertyGroup |NodeFaultDomainIdCollection |Décrit les domaines d’erreur auquel un nœud appartient. Le domaine par défaut est défini par un URI qui décrit l’emplacement du nœud dans le centre de données.  Les URI de domaine d’erreur sont au format de:/de suivi d’un segment de chemin d’accès d’URI.|

## <a name="nodeproperties"></a>NodeProperties
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| PropertyGroup |NodePropertyCollectionMap |Collection de paires clé-valeur de chaîne pour les propriétés du nœud. |

## <a name="nodecapacities"></a>NodeCapacities
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| PropertyGroup |NodeCapacityCollectionMap |Collection des capacités du nœud pour les différentes mesures. |

## <a name="fabricnode"></a>FabricNode
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| StartApplicationPortRange |Entier (valeur par défaut : 0) |Début des ports d’application gérés par le sous-système d’hébergement. Requis si EndpointFilteringEnabled a la valeur true dans Hosting. |
| EndApplicationPortRange |Entier (valeur par défaut : 0) |Fin (non inclusive) des ports d’application gérés par le sous-système d’hébergement. Requis si EndpointFilteringEnabled a la valeur true dans Hosting. |
| ClusterX509StoreName |Chaîne (valeur par défaut : "My") |Nom du magasin de certificats X.509 qui contient le certificat de cluster utilisé pour sécuriser les communications internes au cluster. |
| ClusterX509FindType |Chaîne (valeur par défaut : "FindByThumbprint") |Indique comment rechercher le certificat de cluster dans le magasin spécifié par les valeurs ClusterX509StoreName prises en charge : « FindByThumbprint » ; « FindBySubjectName » avec « FindBySubjectName ». S’il y a plusieurs correspondances, celui ayant la date d’expiration la plus lointaine est utilisé. |
| ClusterX509FindValue |Chaîne (valeur par défaut : "") |Valeur de filtre de recherche utilisée pour localiser le certificat de cluster. |
| ClusterX509FindValueSecondary |Chaîne (valeur par défaut : "") |Valeur de filtre de recherche utilisée pour localiser le certificat de cluster. |
| ServerAuthX509StoreName |Chaîne (valeur par défaut : "My") |Nom du magasin de certificats X.509 qui contient le certificat de serveur pour le service d’entrée. |
| ServerAuthX509FindType |Chaîne (valeur par défaut : "FindByThumbprint") |Indique comment rechercher le certificat de serveur dans le magasin spécifié par la valeur ServerAuthX509StoreName prise en charge : FindByThumbprint; FindBySubjectName. |
| ServerAuthX509FindValue |Chaîne (valeur par défaut : "") |Valeur de filtre de recherche utilisée pour localiser le certificat de serveur. |
| ServerAuthX509FindValueSecondary |Chaîne (valeur par défaut : "") |Valeur de filtre de recherche utilisée pour localiser le certificat de serveur. |
| ClientAuthX509StoreName |Chaîne (valeur par défaut : "My") |Nom du magasin de certificats X.509 qui contient le certificat pour le rôle d’administrateur par défaut FabricClient. |
| ClientAuthX509FindType |Chaîne (valeur par défaut : "FindByThumbprint") |Indique comment rechercher le certificat de serveur dans le magasin spécifié par la valeur ClientAuthX509StoreName prise en charge : FindByThumbprint; FindBySubjectName. |
| ClientAuthX509FindValue |Chaîne (valeur par défaut : "") | Valeur de filtre de recherche utilisée pour localiser le certificat du rôle d’administrateur par défaut FabricClient. |
| ClientAuthX509FindValueSecondary |Chaîne (valeur par défaut : "") |Valeur de filtre de recherche utilisée pour localiser le certificat du rôle d’administrateur par défaut FabricClient. |
| UserRoleClientX509StoreName |Chaîne (valeur par défaut : "My") |Nom du magasin de certificats X.509 qui contient le certificat pour le rôle d’utilisateur par défaut FabricClient. |
| UserRoleClientX509FindType |Chaîne (valeur par défaut : "FindByThumbprint") |Indique comment rechercher le certificat dans le magasin spécifié par la valeur UserRoleClientX509StoreName prise en charge : FindByThumbprint; FindBySubjectName. |
| UserRoleClientX509FindValue |Chaîne (valeur par défaut : "") |Valeur de filtre de recherche utilisée pour localiser le certificat du rôle d’utilisateur par défaut FabricClient. |
| UserRoleClientX509FindValueSecondary |Chaîne (valeur par défaut : "") |Valeur de filtre de recherche utilisée pour localiser le certificat du rôle d’utilisateur par défaut FabricClient. |

## <a name="paas"></a>PaaS
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| ClusterId |Chaîne (valeur par défaut : "") |Magasin de certificats X509 utilisé par l’infrastructure pour protéger la configuration. |

## <a name="fabrichost"></a>FabricHost
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| StopTimeout |Durée en secondes, la valeur par défaut est 300 |Spécifiez la durée en secondes. Délai d’attente pour l’activation, la désactivation et la mise à niveau du service hébergé. |
| StartTimeout |Durée en secondes, la valeur par défaut est 300 |Spécifiez la durée en secondes. Délai d’expiration pour le démarrage de fabricactivationmanager. |
| ActivationRetryBackoffInterval |Durée en secondes (valeur par défaut : 5) |Spécifiez la durée en secondes. Intervalle d’interruption à chaque échec d’activation. À chaque échec d’activation continue, le système retente l’activation jusqu’à atteindre la valeur du paramètre MaxActivationFailureCount. L’intervalle avant nouvelle tentative pour chaque tentative est une combinaison de l’échec de l’activation continue et de l’intervalle d’interruption d’activation. |
| ActivationMaxRetryInterval |Durée en secondes, la valeur par défaut est 300 |Spécifiez la durée en secondes. Intervalle maximum avant une nouvelle tentative d’activation. À chaque échec continu, l’intervalle avant nouvelle tentative est calculé de la manière suivante : Min (ActivationMaxRetryInterval; Nombre d’échecs continus * ActivationRetryBackoffInterval). |
| ActivationMaxFailureCount |Entier (valeur par défaut : 10) |Nombre maximum pour lequel le système va relancer l’activation ayant échoué avant d’abandonner. |
| EnableServiceFabricAutomaticUpdates |Valeur booléenne (valeur par défaut : false) |Ce paramètre permet d’activer les mises à jour automatiques de l’infrastructure via Windows Update. |
| EnableServiceFabricBaseUpgrade |Valeur booléenne (valeur par défaut : false) |Ce paramètre active la mise à jour de base du serveur. |
| EnableRestartManagement |Valeur booléenne (valeur par défaut : false) |Ce paramètre active le redémarrage du serveur. |


## <a name="failovermanager"></a>FailoverManager
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| UserReplicaRestartWaitDuration |Durée en secondes (valeur par défaut : 60.0 * 30) |Spécifiez la durée en secondes. Lorsqu’un réplica persistant tombe en panne, Windows Fabric attend pendant cette durée que le réplica revienne en ligne avant de créer d’autres réplicas de remplacement (ce qui nécessite une copie de l’état). |
| QuorumLossWaitDuration |Durée en secondes (valeur par défaut : MaxValue) |Spécifiez la durée en secondes. Il s’agit de la durée maximale pour laquelle nous autorisons une partition à être dans un état de perte de quorum. Si la partition est toujours en perte de quorum au-delà de cette durée, elle est rétablie en considérant les réplicas arrêtés comme perdus. Notez que cela peut potentiellement provoquer une perte de données. |
| UserStandByReplicaKeepDuration |Durée en secondes (valeur par défaut : 3600.0 * 24 * 7) |Spécifiez la durée en secondes. Lorsqu’un réplica persistant n’est plus défaillant, il peut avoir déjà été remplacé. Ce minuteur détermine la durée pendant laquelle le FM conserve le réplica en attente avant de le rejeter. |
| UserMaxStandByReplicaCount |Entier (valeur par défaut : 1) |Nombre maximum par défaut de réplicas en attente que le système conserve pour les services de l’utilisateur. |

## <a name="namingservice"></a>NamingService
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| TargetReplicaSetSize |Entier (valeur par défaut : 7) |Nombre de jeux de réplicas pour chaque partition de la banque du magasin du service d’attribution de noms. En augmentant le nombre de jeux de réplicas, vous augmentez le niveau de fiabilité des informations contenues dans le magasin du Service d’attribution de noms, ce qui diminue le risque de perte d’informations suite à des défaillances de nœud, moyennant une charge accrue sur Windows Fabric et le temps nécessaire pour mettre à jour les données d’attribution de noms.|
|MinReplicaSetSize | Entier (valeur par défaut : 3) | Nombre minimum de réplicas du Service d’attribution de noms requis dans lesquels effectuer des écritures pour finaliser une mise à jour. Si le nombre de réplicas actifs dans le système est inférieur à cette valeur, le système de fiabilité refuse les mises à jour du magasin du Service d’attribution de noms jusqu’à ce que les réplicas soient restaurés. Cette valeur ne doit jamais être supérieure à TargetReplicaSetSize. |
|ReplicaRestartWaitDuration | Durée en secondes (valeur par défaut : 60.0 * 30)| Spécifiez la durée en secondes. Ce minuteur démarre lorsqu’un réplica du service d’attribution de noms tombe en panne.  Lorsqu’il arrive à expiration, le FM commence à remplacer les réplicas qui sont arrêtés (il ne les considère pas encore comme perdus). |
|QuorumLossWaitDuration | Durée en secondes (valeur par défaut : MaxValue) | Spécifiez la durée en secondes. Ce minuteur démarre lorsqu’un service d’attribution de noms passe en perte de quorum.  Lorsque ce délai est écoulé, le FM considère les réplicas arrêtés comme perdus et tente de rétablir le quorum. Notez que cela peut entraîner une perte de données. |
|StandByReplicaKeepDuration | Durée en secondes (valeur par défaut : 3600.0*2) | Spécifiez la durée en secondes. Lorsqu’un réplica du Service d’attribution de noms n’est plus défaillant, il peut avoir déjà été remplacé.  Ce minuteur détermine la durée pendant laquelle le FM conserve le réplica en attente avant de le rejeter. |
|PlacementConstraints | Chaîne (valeur par défaut : "") | Contrainte de placement pour le Service d’attribution de noms. |
|ServiceDescriptionCacheLimit | Entier (valeur par défaut : 0) | Nombre maximum d’entrées maintenues dans le cache de description du service LRU au niveau du magasin du Service d’attribution de noms (0 correspond à aucune limite). |
|RepairInterval | Durée en secondes (valeur par défaut : 5) | Spécifiez la durée en secondes. Durée pendant laquelle la réparation d’une incohérence d’attribution de noms entre le propriétaire de l’autorité et le propriétaire du nom démarre. |
|MaxNamingServiceHealthReports | Entier (valeur par défaut : 10) | Nombre maximum d’opérations lentes que le magasin du Service d’attribution de noms signale comme défectueuses à la fois. Si vous spécifiez 0, toutes les opérations lentes sont envoyées. |
| MaxMessageSize |Entier, valeur par défaut : 4*1024*1024 |Taille maximale des messages pour la communication de nœuds clients lors de l’utilisation du service d’attribution de noms. Atténuation d’attaque par déni de service. Valeur par défaut : 4 Mo. |
| MaxFileOperationTimeout |Durée en secondes, la valeur par défaut est 30 |Spécifiez la durée en secondes. Délai d’attente maximum autorisé pour l’opération du service de magasin de fichiers. Les requêtes spécifiant un délai d’expiration supérieur seront rejetées. |
| MaxOperationTimeout |Durée en secondes (valeur par défaut : 600) |Spécifiez la durée en secondes. Délai d’attente maximum autorisé pour les opérations du client. Les requêtes spécifiant un délai d’expiration supérieur seront rejetées. |
| MaxClientConnections |Entier, valeur par défaut : 1000 |Nombre maximum autorisé de connexions clientes par passerelle. |
| ServiceNotificationTimeout |Durée en secondes, la valeur par défaut est 30 |Spécifiez la durée en secondes. Délai d’expiration utilisé lors de l’envoi des notifications du service au client. |
| MaxOutstandingNotificationsPerClient |Entier, valeur par défaut : 1000 |Nombre maximum de notifications en attente avant la fermeture forcée de l’inscription du client par la passerelle. |
| MaxIndexedEmptyPartitions |Entier, valeur par défaut : 1000 |Nombre maximum de partitions vides qui resteront indexées dans le cache de notifications pour synchroniser les clients qui se reconnectent. Toutes les partitions vides au-delà de ce nombre seront supprimées de l’index dans l’ordre croissant des versions de recherche. Les clients qui se reconnectent peuvent toujours synchroniser et recevoir les mises à jour manquantes des partitions vides, mais le protocole de synchronisation devient plus coûteux. |
| GatewayServiceDescriptionCacheLimit |Entier (valeur par défaut : 0) |Nombre maximum d’entrées maintenues dans le cache de description du service LRU au niveau de la passerelle d’attribution de noms (0 correspond à aucune limite). |
| PartitionCount |Entier (valeur par défaut : 3) |Nombre de partitions du magasin du Service d’attribution de noms à créer. Chaque partition possède sa clé de partition unique qui correspond à son index. Les clés de partition [0; PartitionCount) existent donc. En augmentant le nombre de partitions du Services d’attribution de noms, vous augmentez l’étendue sur laquelle intervient le Service d’attribution de noms, tout en diminuant le volume moyen de données stockées par un jeu de réplicas de secours, moyennant une utilisation accrue des ressources (car les réplicas de service PartitionCount*ReplicaSetSize doivent être maintenus).|

## <a name="runas"></a>RunAs
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| RunAsAccountName |Chaîne (valeur par défaut : "") |Indique le nom du compte RunAs. Ce paramètre n’est nécessaire que pour le type de compte DomainUser ou ManagedServiceAccount. Les valeurs autorisées sont "domain\user" ou "user@domain". |
|RunAsAccountType|Chaîne (valeur par défaut : "") |Indique le type du compte RunAs. Ce paramètre est nécessaire pour toutes les sections RunAs. Les valeurs autorisées DomainUser/NetworkService/ManagedServiceAccount/LocalSystem.|
|RunAsPassword|Chaîne (valeur par défaut : "") |Indique le mot de passe du compte RunAs. Ce paramètre n’est nécessaire que pour le type de compte DomainUser. |

## <a name="runasfabric"></a>RunAs_Fabric
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| RunAsAccountName |Chaîne (valeur par défaut : "") |Indique le nom du compte RunAs. Ce paramètre n’est nécessaire que pour le type de compte DomainUser ou ManagedServiceAccount. Les valeurs autorisées sont "domain\user" ou "user@domain". |
|RunAsAccountType|Chaîne (valeur par défaut : "") |Indique le type du compte RunAs. Ce paramètre est nécessaire pour toutes les sections RunAs. Les valeurs autorisées sont LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem. |
|RunAsPassword|Chaîne (valeur par défaut : "") |Indique le mot de passe du compte RunAs. Ce paramètre n’est nécessaire que pour le type de compte DomainUser. |

## <a name="runashttpgateway"></a>RunAs_HttpGateway
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| RunAsAccountName |Chaîne (valeur par défaut : "") |Indique le nom du compte RunAs. Ce paramètre n’est nécessaire que pour le type de compte DomainUser ou ManagedServiceAccount. Les valeurs autorisées sont "domain\user" ou "user@domain". |
|RunAsAccountType|Chaîne (valeur par défaut : "") |Indique le type du compte RunAs. Ce paramètre est nécessaire pour toutes les sections RunAs. Les valeurs autorisées sont LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem. |
|RunAsPassword|Chaîne (valeur par défaut : "") |Indique le mot de passe du compte RunAs. Ce paramètre n’est nécessaire que pour le type de compte DomainUser. |

## <a name="runasdca"></a>RunAs_DCA
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| RunAsAccountName |Chaîne (valeur par défaut : "") |Indique le nom du compte RunAs. Ce paramètre n’est nécessaire que pour le type de compte DomainUser ou ManagedServiceAccount. Les valeurs autorisées sont "domain\user" ou "user@domain". |
|RunAsAccountType|Chaîne (valeur par défaut : "") |Indique le type du compte RunAs. Ce paramètre est nécessaire pour toutes les sections RunAs. Les valeurs autorisées sont LocalUser/DomainUser/NetworkService/ManagedServiceAccount/LocalSystem. |
|RunAsPassword|Chaîne (valeur par défaut : "") |Indique le mot de passe du compte RunAs. Ce paramètre n’est nécessaire que pour le type de compte DomainUser. |

## <a name="httpgateway"></a>HttpGateway
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
|IsEnabled|Valeur booléenne (valeur par défaut : false) | Active/désactive le paramètre httpgateway. Le paramètre Httpgateway est désactivé par défaut et cette configuration doit être définie pour l’activer. |
|ActiveListeners |Valeur Uint (valeur par défaut : 50) | Nombre de lectures à publier dans la file d’attente du serveur HTTP. Ce paramètre contrôle le nombre de requêtes concurrentes que la passerelle HTTP peut satisfaire. |
|MaxEntityBodySize |Valeur Uint (valeur par défaut : 4194304) |  Ce paramètre indique la taille maximum du corps que l’on peut attendre d’une requête HTTP. Valeur par défaut : 4 Mo. Le paramètre httpgateway ne peut pas traiter une requête si son corps a une taille supérieure à cette valeur. La taille minimum du bloc de lectures est de 4096 octets. Cette valeur doit donc être supérieure ou égale à 4096. |
|HttpGatewayHealthReportSendInterval |Durée en secondes, la valeur par défaut est 30 | Spécifiez la durée en secondes. Délai à l’issue duquel la passerelle HTTP envoie les rapports d’intégrité cumulés à Health Manager. |

## <a name="ktllogger"></a>KtlLogger
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
|AutomaticMemoryConfiguration |Entier (valeur par défaut : 1) | Indicateur spécifiant si les paramètres de mémoire doivent être configurés automatiquement et dynamiquement. Si vous spécifiez 0, les paramètres de configuration de la mémoire sont utilisés directement et ne sont pas modifiés par les conditions du système. Si vous spécifiez 1, les paramètres de mémoire sont configurés automatiquement et peuvent varier selon les conditions du système. |
|WriteBufferMemoryPoolMinimumInKB |Entier (valeur par défaut : 8388608) |Nombre de Ko à attribuer initialement au pool de mémoires-tampons d’écritures. Utilisez 0 pour spécifier aucune limite. La valeur par défaut doit être cohérente avec la valeur du paramètre SharedLogSizeInMB ci-dessous. |
|WriteBufferMemoryPoolMaximumInKB | Entier (valeur par défaut : 0) |Nombre de Ko maximum que le pool de mémoires-tampons d’écritures peut atteindre. Spécifiez 0 pour indiquer aucune limite. |
|MaximumDestagingWriteOutstandingInKB | Entier (valeur par défaut : 0) | Nombre de Ko que le journal partagé peut atteindre, en plus du journal dédié. Spécifiez 0 pour indiquer aucune limite.
|SharedLogPath |Chaîne (valeur par défaut : "") | Chemin et nom de fichier du conteneur du journal partagé. Utilisez "" pour utiliser le chemin par défaut sous la racine des données de structure. |
|SharedLogId |Chaîne (valeur par défaut : "") |GUID unique du conteneur du journal partagé. Utilisez "" si vous choisissez le chemin par défaut sous la racine des données de structure. |
|SharedLogSizeInMB |Entier (valeur par défaut : 8192) | Nombre de Mo à allouer dans le conteneur de journal partagé. |

## <a name="applicationgatewayhttp"></a>ApplicationGateway/Http
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
|IsEnabled |Valeur booléenne (valeur par défaut : false) | Active/désactive le paramètre HttpApplicationGateway. Le paramètre Httpgateway est désactivé par défaut et cette configuration doit être définie pour l’activer. |
|NumberOfParallelOperations | Valeur Uint (par défaut : 5000) | Nombre de lectures à publier dans la file d’attente du serveur HTTP. Ce paramètre contrôle le nombre de requêtes concurrentes que la passerelle HTTP peut satisfaire. |
|DefaultHttpRequestTimeout |Durée en secondes. La valeur par défaut est 120 |Spécifiez la durée en secondes.  Indique le délai d’expiration par défaut des requêtes HTTP traitées par la passerelle HTTP. |
|ResolveServiceBackoffInterval |Durée en secondes (valeur par défaut : 5) |Spécifiez la durée en secondes.  Indique l’interface d’interruption par défaut avant de retenter une opération du service de résolution ayant échoué. |
|BodyChunkSize |Valeur Uint (valeur par défaut : 16384) |  Indique la taille en octets du bloc utilisé pour lire le corps. |
|GatewayAuthCredentialType |Chaîne (valeur par défaut : "None") | Indique le type d’informations d’identification de sécurité à utiliser comme point d’extrémité de la passerelle d’application HTTP. Les valeurs autorisées sont None/X509. |
|GatewayX509CertificateStoreName |Chaîne (valeur par défaut : "My") | Nom du magasin de certificats X.509 qui contient le certificat de la passerelle d’application HTTP. |
|GatewayX509CertificateFindType |Chaîne (valeur par défaut : "FindByThumbprint") | Indique comment rechercher un certificat dans le magasin spécifié par GatewayX509CertificateStoreName. Valeur prise en charge : FindByThumbprint; FindBySubjectName. |
|GatewayX509CertificateFindValue | Chaîne (valeur par défaut : "") | Valeur du filtre de recherche utilisée pour localiser le certificat de la passerelle d’application HTTP. Ce certificat est configuré sur le point d’extrémité HTTPS et peut également servir à vérifier l’identité de l’application au besoin par les services. Le paramètre FindValue est consulté en premier. S’il n’existe pas, FindValueSecondary est consulté. |
|GatewayX509CertificateFindValueSecondary | Chaîne (valeur par défaut : "") |Valeur du filtre de recherche utilisée pour localiser le certificat de la passerelle d’application HTTP. Ce certificat est configuré sur le point d’extrémité HTTPS et peut également servir à vérifier l’identité de l’application au besoin par les services. Le paramètre FindValue est consulté en premier. S’il n’existe pas, FindValueSecondary est consulté.|

## <a name="management"></a>Gestion
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| ImageStoreConnectionString |SecureString | Chaîne de connexion à la racine d’ImageStore. |
| ImageStoreMinimumTransferBPS | Entier (valeur par défaut : 1024) |Débit de transfert minimum entre le cluster et ImageStore. Cette valeur permet de déterminer le délai d’expiration lors de l’accès au magasin ImageStore externe. Ne modifiez cette valeur que si la latence entre le cluster et ImageStore est élevée afin de laisser davantage de temps au cluster pour effectuer des téléchargements à partir du magasin ImageStore externe. |
|AzureStorageMaxWorkerThreads | Entier (valeur par défaut : 25) | Nombre maximum de threads de travail en parallèle. |
|AzureStorageMaxConnections | Entier (valeur par défaut : 5000) | Nombre maximum de connexions simultanées au stockage Azure. |
|AzureStorageOperationTimeout | Durée en secondes (valeur par défaut : 6000) | Spécifiez la durée en secondes. Délai permettant de finaliser l’opération xstore. |
|ImageCachingEnabled | Valeur booléenne (valeur par défaut : true) | Cette configuration nous permet d’activer ou de désactiver la mise en cache. |
|DisableChecksumValidation | Valeur booléenne (valeur par défaut : false) | Cette configuration nous permet d’activer ou de désactiver la validation de la somme de contrôle pendant l’approvisionnement de l’application. |
|DisableServerSideCopy | Valeur booléenne (valeur par défaut : false) | Cette configuration active ou désactive la copie côté serveur du package d’application sur le magasin ImageStore pendant l’approvisionnement de l’application. |

## <a name="healthmanagerclusterhealthpolicy"></a>HealthManager/ClusterHealthPolicy
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| ConsiderWarningAsError |Valeur booléenne (valeur par défaut : false) |Stratégie d’évaluation d’intégrité de cluster : avertissements traités comme des erreurs. |
| MaxPercentUnhealthyNodes | Entier (valeur par défaut : 0) |Stratégie d’évaluation d’intégrité de cluster : pourcentage maximum de nœuds défaillants autorisés pour que le cluster soit fonctionnel. |
| MaxPercentUnhealthyApplications | Entier (valeur par défaut : 0) |Stratégie d’évaluation d’intégrité de cluster : pourcentage maximum d’applications défaillantes autorisées pour que le cluster soit fonctionnel. |
|MaxPercentDeltaUnhealthyNodes | Entier (valeur par défaut : 10) |Stratégie d’évaluation d’intégrité de mise à niveau de cluster : pourcentage maximum du delta de nœuds défaillants autorisés pour que le cluster soit fonctionnel. |
|MaxPercentUpgradeDomainDeltaUnhealthyNodes | Entier (valeur par défaut : 15) |Stratégie d’évaluation d’intégrité de mise à niveau de cluster : pourcentage maximum de delta de nœuds défaillants autorisés pour que le cluster soit fonctionnel.|

## <a name="faultanalysisservice"></a>FaultAnalysisService
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| TargetReplicaSetSize |Entier (valeur par défaut : 0) |NOT_PLATFORM_UNIX_START Paramètre TargetReplicaSetSize pour FaultAnalysisService. |
| MinReplicaSetSize |Entier (valeur par défaut : 0) |Paramètre MinReplicaSetSize pour FaultAnalysisService. |
| ReplicaRestartWaitDuration |Durée en secondes (valeur par défaut : 60 minutes)|Spécifiez la durée en secondes. Paramètre ReplicaRestartWaitDuration pour FaultAnalysisService. |
| QuorumLossWaitDuration | Durée en secondes (valeur par défaut : MaxValue) |Spécifiez la durée en secondes. Paramètre QuorumLossWaitDuration pour FaultAnalysisService. |
| StandByReplicaKeepDuration| Durée en secondes (valeur par défaut : 60*24*7 minutes) |Spécifiez la durée en secondes. Paramètre StandByReplicaKeepDuration pour FaultAnalysisService. |
| PlacementConstraints | Chaîne (valeur par défaut : "")| Paramètre PlacementConstraints pour FaultAnalysisService. |
| StoredActionCleanupIntervalInSeconds | Entier (valeur par défaut : 3600) |Fréquence de nettoyage du magasin.  Seules les actions dans un état terminal et celles qui exécutées il y a au moins CompletedActionKeepDurationInSeconds secondes seront supprimées. |
| CompletedActionKeepDurationInSeconds | Entier (valeur par défaut : 604800) | Durée approximative de conservation des actions dans un état terminal.  Cela dépend également de StoredActionCleanupIntervalInSeconds, car le travail de nettoyage ne s’effectue que pendant cette durée. 604800 correspond à 7 jours. |
| StoredChaosEventCleanupIntervalInSeconds | Entier (valeur par défaut : 3600) |Fréquence à laquelle le système évalue la nécessité du nettoyage. Si le nombre d’événements est supérieur à 30000, le nettoyage se déclenche. |

## <a name="filestoreservice"></a>FileStoreService
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| NamingOperationTimeout |Durée en secondes, la valeur par défaut est 60 |Spécifiez la durée en secondes. Délai pour effectuer l’opération d’attribution de noms. |
| QueryOperationTimeout | Durée en secondes, la valeur par défaut est 60 |Spécifiez la durée en secondes. Délai pour effectuer l’opération de requête. |
| MaxCopyOperationThreads | Valeur Uint (valeur par défaut : 0) | Nombre maximum de fichiers parallèles que le réplica secondaire peut copier à partir du réplica principal. 0 == nombre de cœurs. |
| MaxFileOperationThreads | Valeur Uint (valeur par défaut : 100) | Nombre maximum de threads parallèles autorisés à effectuer des opérations sur les fichiers (copie/déplacement) dans le réplica principal. 0 == nombre de cœurs. |
| MaxStoreOperations | Valeur Uint (valeur par défaut : 4096) |Nombre maximum d’opérations parallèles de transaction de magasin autorisées sur le réplica principal. 0 = nombre de cœurs. |
| MaxRequestProcessingThreads | Valeur Uint (valeur par défaut : 200) |Nombre maximum de threads parallèles autorisés pour traiter les requêtes dans le réplica principal. 0 = nombre de cœurs. |
| MaxSecondaryFileCopyFailureThreshold | Valeur Uint (valeur par défaut : 25)| Nombre maximum de nouvelles tentatives de copie de fichier sur le réplica secondaire avant d’abandonner. |
| AnonymousAccessEnabled | Valeur booléenne (valeur par défaut : true) |Activez ou désactivez l’accès anonyme aux partages FileStoreService. |
| PrimaryAccountType | Chaîne (valeur par défaut : "") |Type de compte principal du principal utilisé pour contrôler l’accès aux partages FileStoreService. |
| PrimaryAccountUserName | Chaîne (valeur par défaut : "") |Nom d’utilisateur du compte principal du principal utilisé pour contrôler l’accès aux partages FileStoreService. |
| PrimaryAccountUserPassword | Valeur SecureString (valeur par défaut : vide) |Mot de passe du compte principal du principal utilisé pour contrôler l’accès aux partages FileStoreService. |
| FileStoreService | PrimaryAccountNTLMPasswordSecret | Valeur SecureString (valeur par défaut : vide) | Secret de mot de passe utilisé pour générer le même mot de passe en cas d’authentification NTLM. |
| PrimaryAccountNTLMX509StoreLocation | Chaîne (valeur par défaut : "LocalMachine")| Emplacement du certificat X509 utilisé pour générer le code HMAC sur le PrimaryAccountNTLMPasswordSecret en cas d’authentification NTLM. |
| PrimaryAccountNTLMX509StoreName | Chaîne (valeur par défaut : "MY")| Nom du certificat X509 utilisé pour générer le code HMAC sur le PrimaryAccountNTLMPasswordSecret en cas d’authentification NTLM. |
| PrimaryAccountNTLMX509Thumbprint | Chaîne (valeur par défaut : "")|Empreinte du certificat X509 utilisé pour générer le code HMAC sur le PrimaryAccountNTLMPasswordSecret en cas d’authentification NTLM. |
| SecondaryAccountType | Chaîne (valeur par défaut : "")| Type de compte secondaire du principal utilisé pour contrôler l’accès aux partages FileStoreService. |
| SecondaryAccountUserName | Chaîne (valeur par défaut : "")| Nom d’utilisateur du compte secondaire du principal utilisé pour contrôler l’accès aux partages FileStoreService. |
| SecondaryAccountUserPassword | Valeur SecureString (valeur par défaut : vide) |Mot de passe du compte secondaire du principal utilisé pour contrôler l’accès aux partages FileStoreService.  |
| SecondaryAccountNTLMPasswordSecret | Valeur SecureString (valeur par défaut : vide) | Secret de mot de passe utilisé comme valeur initiale pour générer le même mot de passe en cas d’authentification NTLM. |
| SecondaryAccountNTLMX509StoreLocation | Chaîne (valeur par défaut : "LocalMachine") |Emplacement du certificat X509 utilisé pour générer le code HMAC sur le SecondaryAccountNTLMPasswordSecret en cas d’authentification NTLM. |
| SecondaryAccountNTLMX509StoreName | Chaîne (valeur par défaut : "MY") |Nom du certificat X509 utilisé pour générer le code HMAC sur le SecondaryAccountNTLMPasswordSecret en cas d’authentification NTLM. |
| SecondaryAccountNTLMX509Thumbprint | Chaîne (valeur par défaut : "")| Empreinte du certificat X509 utilisé pour générer le code HMAC sur le SecondaryAccountNTLMPasswordSecret en cas d’authentification NTLM. |

## <a name="imagestoreservice"></a>ImageStoreService
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| Activé |Valeur booléenne (valeur par défaut : false) |Indicateur d’activation d’ImageStoreService. |
| TargetReplicaSetSize | Entier (valeur par défaut : 7) |Paramètre TargetReplicaSetSize pour ImageStoreService. |
| MinReplicaSetSize | Entier (valeur par défaut : 3) |Paramètre MinReplicaSetSize pour ImageStoreService. |
| ReplicaRestartWaitDuration | Durée en secondes (valeur par défaut : 60.0 * 30) | Spécifiez la durée en secondes. Paramètre ReplicaRestartWaitDuration pour ImageStoreService. |
| QuorumLossWaitDuration | Durée en secondes (valeur par défaut : MaxValue) | Spécifiez la durée en secondes. Paramètre QuorumLossWaitDuration pour ImageStoreService. |
| StandByReplicaKeepDuration | Durée en secondes (valeur par défaut : 3600.0*2) | Spécifiez la durée en secondes. Paramètre StandByReplicaKeepDuration pour ImageStoreService. |
| PlacementConstraints | Chaîne (valeur par défaut : "") | Paramètre PlacementConstraints pour ImageStoreService. |
| ClientUploadTimeout | Durée en secondes (valeur par défaut : 1800) |Spécifiez la durée en secondes. Délai d’expiration de la requête de chargement de niveau supérieur dans ImageStoreService. |
| ClientCopyTimeout | Durée en secondes (valeur par défaut : 1800) | Spécifiez la durée en secondes. Délai d’expiration de la requête de copie de niveau supérieur dans ImageStoreService. |
| ClientDownloadTimeout | Durée en secondes (valeur par défaut : 1800) | Spécifiez la durée en secondes. Délai d’expiration de la requête de téléchargement de niveau supérieur dans ImageStoreService. |
| ClientListTimeout | Durée en secondes (valeur par défaut : 600) | Spécifiez la durée en secondes. Délai d’expiration de la requête de liste de niveau supérieur dans ImageStoreService. |
| ClientDefaultTimeout | Durée en secondes (valeur par défaut : 180) | Spécifiez la durée en secondes. Délai d’expiration de toutes les requêtes autres que de chargement/déchargement (existence ou suppression) dans ImageStoreService. |

## <a name="imagestoreclient"></a>ImageStoreClient
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| ClientUploadTimeout |Durée en secondes (valeur par défaut : 1800) | Spécifiez la durée en secondes. Délai d’expiration de la requête de chargement de niveau supérieur dans ImageStoreService. |
| ClientCopyTimeout | Durée en secondes (valeur par défaut : 1800) | Spécifiez la durée en secondes. Délai d’expiration de la requête de copie de niveau supérieur dans ImageStoreService. |
|ClientDownloadTimeout | Durée en secondes (valeur par défaut : 1800) | Spécifiez la durée en secondes. Délai d’expiration de la requête de téléchargement de niveau supérieur dans ImageStoreService. |
|ClientListTimeout | Durée en secondes (valeur par défaut : 600) |Spécifiez la durée en secondes. Délai d’expiration de la requête de liste de niveau supérieur dans ImageStoreService. |
|ClientDefaultTimeout | Durée en secondes (valeur par défaut : 180) | Spécifiez la durée en secondes. Délai d’expiration de toutes les requêtes autres que de chargement/déchargement (existence ou suppression) dans ImageStoreService. |

## <a name="tokenvalidationservice"></a>TokenValidationService
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| Fournisseurs |Chaîne (valeur par défault : "DSTS") |Liste séparée par des virgules, des fournisseurs de validation de jeton à activer (fournisseurs valides : DSTS, AAD). Pour l’instant, un seul fournisseur peut être activé à la fois. |

## <a name="upgradeorchestrationservice"></a>UpgradeOrchestrationService
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| TargetReplicaSetSize |Entier (valeur par défaut : 0) |Paramètre TargetReplicaSetSize pour UpgradeOrchestrationService. |
| MinReplicaSetSize |Entier (valeur par défaut : 0) | Paramètre MinReplicaSetSize pour UpgradeOrchestrationService.
| ReplicaRestartWaitDuration | Durée en secondes (valeur par défaut : 60 minutes)| Spécifiez la durée en secondes. Paramètre ReplicaRestartWaitDuration pour UpgradeOrchestrationService. |
| QuorumLossWaitDuration | Durée en secondes (valeur par défaut : MaxValue) | Spécifiez la durée en secondes. Paramètre QuorumLossWaitDuration pour UpgradeOrchestrationService. |
| StandByReplicaKeepDuration | Durée en secondes (valeur par défaut : 60*24*7 minutes) | Spécifiez la durée en secondes. Paramètre StandByReplicaKeepDuration pour UpgradeOrchestrationService. |
| PlacementConstraints | Chaîne (valeur par défaut : "") | Paramètre PlacementConstraints pour UpgradeOrchestrationService. |
| AutoupgradeEnabled | Valeur booléenne (valeur par défaut : true) | Interrogation automatique et action de mise à niveau basée sur un fichier d’état d’objectif. |
| UpgradeApprovalRequired | Valeur booléenne (valeur par défaut : false) | La configuration de la mise à niveau du code requiert une autorisation de l’administrateur. |

## <a name="upgradeservice"></a>UpgradeService
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| PlacementConstraints |Chaîne (valeur par défaut : "") |Paramètre PlacementConstraints pour le service de mise à niveau. |
| TargetReplicaSetSize | Entier (valeur par défaut : 3) | Paramètre TargetReplicaSetSize pour UpgradeService. |
| MinReplicaSetSize | Entier (valeur par défaut : 2) | Paramètre MinReplicaSetSize pour UpgradeService. |
| CoordinatorType | Chaîne (valeur par défault : "WUTest")| Paramètre CoordinatorType pour UpgradeService. |
| BaseUrl | Chaîne (valeur par défaut : "") |Paramètre BaseUrl pour UpgradeService. |
| ClusterId | Chaîne (valeur par défaut : "") | Paramètre ClusterId pour UpgradeService. |
| X509StoreName | Chaîne (valeur par défaut : "My")| Paramètre X509StoreName pour UpgradeService. |
| X509StoreLocation | Chaîne (valeur par défaut : "") | Paramètre X509StoreLocation pour UpgradeService. |
| X509FindType | Chaîne (valeur par défaut : "")| Paramètre X509FindType pour UpgradeService. |
| X509FindValue | Chaîne (valeur par défaut : "") | Paramètre X509FindValue pour UpgradeService. |
| X509SecondaryFindValue | Chaîne (valeur par défaut : "") | Paramètre X509SecondaryFindValue pour UpgradeService. |
| OnlyBaseUpgrade | Valeur booléenne (valeur par défaut : false) | Paramètre OnlyBaseUpgrade pour UpgradeService. |
| TestCabFolder | Chaîne (valeur par défaut : "") | Paramètre TestCabFolder pour UpgradeService. |

## <a name="securityclientaccess"></a>Security/ClientAccess
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| CreateName |Chaîne (valeur par défault : "Admin") |Configuration de la sécurité pour la création d’URI d’attribution de noms. |
| DeleteName |Chaîne (valeur par défault : "Admin") |Configuration de la sécurité pour la suppression d’URI d’attribution de noms. |
| PropertyWriteBatch |Chaîne (valeur par défault : "Admin") |Configuration de la sécurité pour les opérations d’écriture de la propriété d’attribution de noms. |
| CreateService |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour la création de services. |
| CreateServiceFromTemplate |Chaîne (valeur par défault : "Admin") |Configuration de la sécurité pour la création de services à partir d’un modèle. |
| UpdateService |Chaîne (valeur par défault : "Admin") |Configuration de la sécurité pour les mises à niveau de services. |
| DeleteService  |Chaîne (valeur par défault : "Admin") |Configuration de la sécurité pour la suppression de services. |
| ProvisionApplicationType |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour l’approvisionnement du type d’application. |
| CreateApplication |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour la création d’applications. |
| DeleteApplication |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour la suppression d’applications. |
| UpgradeApplication |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour démarrer ou arrêter des mises à niveau d’application. |
| RollbackApplicationUpgrade |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour annuler des mises à niveau d’application. |
| UnprovisionApplicationType |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour annuler l’approvisionnement du type d’application. |
| MoveNextUpgradeDomain |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour reprendre des mises à niveau d’application avec un domaine de mise à niveau explicite. |
| ReportUpgradeHealth |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour reprendre des mises à niveau d’application à leur état d’avancement actuel. |
| ReportHealth |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour créer un rapport d’intégrité. |
| ProvisionFabric |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour l’approvisionnement de MSI et/ou du manifeste de cluster. |
| UpgradeFabric |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour démarrer des mises à niveau de cluster. |
| RollbackFabricUpgrade |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour annuler des mises à niveau de cluster. |
| UnprovisionFabric |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour annuler l’approvisionnement de MSI et/ou du manifeste de cluster. |
| MoveNextFabricUpgradeDomain |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour reprendre des mises à niveau de cluster avec un domaine de mise à niveau explicite. |
| ReportFabricUpgradeHealth |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour reprendre des mises à niveau de cluster à leur état d’avancement actuel. |
| StartInfrastructureTask |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour démarrer des tâches d’infrastructure. |
| FinishInfrastructureTask |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour terminer des tâches d’infrastructure. |
| ActivateNode |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour activer un nœud. |
| DeactivateNode |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour désactiver un nœud. |
| DeactivateNodesBatch |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour désactiver plusieurs nœuds. |
| RemoveNodeDeactivations |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour annuler la désactivation sur plusieurs nœuds. |
| GetNodeDeactivationStatus |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour vérifier l’état de désactivation. |
| NodeStateRemoved |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour signaler l’état de nœud supprimé. |
| RecoverPartition |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour restaurer une partition. |
| RecoverPartitions |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour restaurer des partitions. |
| RecoverServicePartitions |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour restaurer des partitions de service. |
| RecoverSystemPartitions |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour restaurer des partitions du service système. |
| ReportFault |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour signaler une défaillance. |
| InvokeInfrastructureCommand |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour les commandes de gestion des tâches d’infrastructure. |
| FileContent |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour le transfert de fichiers clients du magasin d’images (externe vers le cluster). |
| FileDownload |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour le lancement du téléchargement de fichiers clients du magasin d’images (externe vers le cluster). |
| InternalList |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour l’opération de liste de fichiers clients deumagasin d’images (interne). |
| Supprimer |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour l’opération de suppression de clients du magasin d’images (interne). |
| Télécharger |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour l’opération de chargement de clients du magasin d’images. |
| GetStagingLocation |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour récupérer l’emplacement intermédiaire du client du magasin d’images. |
| GetStoreLocation |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour récupérer l’emplacement du client du magasin d’images. |
| NodeControl |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour démarrer, arrêter et redémarrer des nœuds. |
| CodePackageControl |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour redémarrer des packages de code. |
| UnreliableTransportControl |Chaîne (valeur par défault : "Admin") | Transport non fiable pour ajouter et supprimer des comportements. |
| MoveReplicaControl |Chaîne (valeur par défault : "Admin") | Déplacez un réplica. |
| PredeployPackageToNode |Chaîne (valeur par défault : "Admin") | API de pré-déploiement. |
| StartPartitionDataLoss |Chaîne (valeur par défault : "Admin") | Provoque la perte de données sur une partition. |
| StartPartitionQuorumLoss |Chaîne (valeur par défault : "Admin") | Provoque la perte de quorum sur une partition. |
| StartPartitionRestart |Chaîne (valeur par défault : "Admin") | Redémarre simultanément certains ou l’ensemble des réplicas d’une partition. |
| CancelTestCommand |Chaîne (valeur par défault : "Admin") | Annule une commande de test si elle est en cours. |
| StartChaos |Chaîne (valeur par défault : "Admin") | Démarre Chaos si ce n’est déjà fait. |
| StopChaos |Chaîne (valeur par défault : "Admin") | Arrête Chaos s’il a été démarré. |
| StartNodeTransition |Chaîne (valeur par défault : "Admin") | Configuration de la sécurité pour démarrer une transition de nœud. |
| StartClusterConfigurationUpgrade |Chaîne (valeur par défault : "Admin") | Déclenche StartClusterConfigurationUpgrade sur une partition. |
| GetUpgradesPendingApproval |Chaîne (valeur par défault : "Admin") | Déclenche GetUpgradesPendingApproval sur une partition. |
| StartApprovedUpgrades |Chaîne (valeur par défault : "Admin") | Déclenche StartApprovedUpgrades sur une partition. |
| Ping |Chaîne (valeur par défault : "Admin\)|\|User") | Configuration de la sécurité pour les tests ping de clients |
| Interroger |Chaîne (valeur par défault : "Admin\)|\|User") | Configuration de la sécurité pour les requêtes. |
| NameExists |Chaîne (valeur par défault : "Admin\)|\|User") | Configuration de la sécurité pour la vérification de l’existence des URI d’attribution de noms. |
| EnumerateSubnames |Chaîne (valeur par défault : "Admin\)|\|User") | Configuration de la sécurité pour l’énumération d’URI d’attribution de noms. |
| EnumerateProperties |Chaîne (valeur par défault : "Admin\)|\|User") | Configuration de la sécurité pour l’énumération de propriétés d’attribution de noms. |
| PropertyReadBatch |Chaîne (valeur par défault : "Admin\)|\|User") | Configuration de la sécurité pour les opérations de lecture de propriétés d’attribution de noms. |
| GetServiceDescription |Chaîne (valeur par défault : "Admin\)|\|User") | Configuration de la sécurité pour la lecture des descriptions de service et les notifications de sondage de longue durée. |
| ResolveService |Chaîne (valeur par défault : "Admin\)|\|User") | Configuration de la sécurité pour la résolution de services en fonction de la réclamation. |
| ResolveNameOwner |Chaîne (valeur par défault : "Admin\)|\|User") | Configuration de la sécurité pour résoudre le propriétaire de l’URI d’attribution de noms. |
| ResolvePartition |Chaîne (valeur par défault : "Admin\)|\|User") | Configuration de la sécurité pour résoudre les services du système. |
| ServiceNotifications |Chaîne (valeur par défault : "Admin\)|\|User") | Configuration de la sécurité pour les notifications de service basées sur l’événement. |
| PrefixResolveService |Chaîne (valeur par défault : "Admin\)|\|User") | Configuration de la sécurité pour la résolution de préfixe de service en fonction de la réclamation. |
| GetUpgradeStatus |Chaîne (valeur par défault : "Admin\)|\|User") | Configuration de la sécurité pour interroger l’état de mise à niveau de l’application. |
| GetFabricUpgradeStatus |Chaîne (valeur par défault : "Admin\)|\|User") | Configuration de la sécurité pour interroger l’état de mise à niveau du cluster. |
| InvokeInfrastructureQuery |Chaîne (valeur par défault : "Admin\)|\|User") | Configuration de la sécurité pour interroger les tâches d’infrastructure. |
| Énumérer |Chaîne (valeur par défault : "Admin\)|\|User") | Configuration de la sécurité pour l’opération de liste de fichiers clients du magasin d’images. |
| ResetPartitionLoad |Chaîne (valeur par défault : "Admin\)|\|User") | Configuration de la sécurité pour la charge de réinitialisation d’une unité failoverUnit. |
| ToggleVerboseServicePlacementHealthReporting | Chaîne (valeur par défault : "Admin\)|\|User") | Configuration de la sécurité pour l’activation ou la désactivation du rapport d’intégrité détaillé sur le placement du service. |
| GetPartitionDataLossProgress | Chaîne (valeur par défault : "Admin\)|\|User") | Récupère l’état d’avancement de l’invocation d’un appel d’API de perte de données. |
| GetPartitionQuorumLossProgress | Chaîne (valeur par défault : "Admin\)|\|User") | Récupère l’état d’avancement de l’invocation d’un appel d’API de perte de quorum. |
| GetPartitionRestartProgress | Chaîne (valeur par défault : "Admin\)|\|User") | Récupère l’état d’avancement de l’invocation d’un appel d’API de redémarrage de partition. |
| GetChaosReport | Chaîne (valeur par défault : "Admin\)|\|User") | Récupère l’état de Chaos pendant une période donnée. |
| GetNodeTransitionProgress | Chaîne (valeur par défault : "Admin\)|\|User") | Configuration de la sécurité pour obtenir l’état d’avancement d’une commande de transition de nœud. |
| GetClusterConfigurationUpgradeStatus | Chaîne (valeur par défault : "Admin\)|\|User") | Déclenche GetClusterConfigurationUpgradeStatus sur une partition. |
| GetClusterConfiguration | Chaîne (valeur par défault : "Admin\)|\|User") | Déclenche GetClusterConfiguration sur une partition. |

## <a name="reconfigurationagent"></a>ReconfigurationAgent
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| ApplicationUpgradeMaxReplicaCloseDuration | Durée en secondes (valeur par défaut : 900) |Spécifiez la durée en secondes. Délai respecté par le système avant de mettre fin à des hôtes de service dont des réplicas sont bloqués en fermeture. |
| ServiceApiHealthDuration | Durée en secondes (valeur par défaut : 30 minutes) | Spécifiez la durée en secondes. ServiceApiHealthDuration définit le délai d’attente à respecter pour exécuter une API de service avant qu’elle soit signalée comme défectueuse. |
| ServiceReconfigurationApiHealthDuration | Durée en secondes, la valeur par défaut est 30 | Spécifiez la durée en secondes. ServiceReconfigurationApiHealthDuration définit le délai à l’issue duquel un service en reconfiguration est signalé comme défectueux. |
| PeriodicApiSlowTraceInterval | Durée en secondes (valeur par défaut : 5 minutes) | Spécifiez la durée en secondes. PeriodicApiSlowTraceInterval définit la période pendant laquelle les appels d’API lents sont retracés par le moniteur d’API. |
| NodeDeactivationMaxReplicaCloseDuration | Durée en secondes (valeur par défaut : 900) | Spécifiez la durée en secondes. Délai maximum à respecter avant de mettre fin à un hôte de service qui bloque la désactivation de nœud. |
| FabricUpgradeMaxReplicaCloseDuration | Durée en secondes (valeur par défaut : 900) | Spécifiez la durée en secondes. Délai maximum à respecter par RA avant d’arrêter l’hôte de service du réplica qui ne se ferme pas. |
| IsDeactivationInfoEnabled | Valeur booléenne (valeur par défaut : true) | Détermine si RA doit utiliser les informations de désactivation pour désigner à nouveau le réplica principal. Pour les nouveaux clusters, cette configuration doit être définie sur « True ». Pour les clusters existants en cours de mise à niveau, consultez les notes de publication afin de savoir comment activer cette option. |

## <a name="placementandloadbalancing"></a>PlacementAndLoadBalancing
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| TraceCRMReasons |Valeur booléenne (valeur par défaut : true) |Spécifie si les raisons des mouvements effectués par CRM doivent être envoyés au canal des événements opérationnels. |
| ValidatePlacementConstraint | Valeur booléenne (valeur par défaut : true) | Spécifie si l’expression PlacementConstraint d’un service est validée lors de la mise à jour du paramètre ServiceDescription d’un service. |
| PlacementConstraintValidationCacheSize | Entier (valeur par défaut : 10000) | Limite la taille de la table utilisée pour la validation et la mise en cache rapides des expressions de contrainte de placement. |
|VerboseHealthReportLimit | Entier (valeur par défaut : 20) | Définit le nombre de fois que le placement d’un réplica doit être annulé avant qu’un avertissement d’intégrité ne soit émis (si le rapport d’intégrité détaillé est activé). |
|ConstraintViolationHealthReportLimit | Entier (valeur par défaut : 50) | Définit le nombre de fois qu’un réplica ne respectant pas la contrainte ne doit pas être corrigé de façon permanente avant que des diagnostics soient effectués et que des rapports d’intégrité soient émis. |
|DetailedConstraintViolationHealthReportLimit | Entier (valeur par défaut : 200) | Définit le nombre de fois qu’un réplica ne respectant pas la contrainte ne doit pas être corrigé de façon permanente avant que des diagnostics soient effectués et que des rapports d’intégrité détaillés soient émis. |
|DetailedVerboseHealthReportLimit | Entier (valeur par défaut : 200) | Définit le nombre de fois qu’un réplica non placé doit être non placé de façon permanente avant que des rapports d’intégrité détaillés soient émis. |
|ConsecutiveDroppedMovementsHealthReportLimit | Entier (valeur par défaut : 20) | Définit le nombre de fois consécutives que des mouvements émis par ResourceBalancer sont supprimés avant que des diagnostics soient effectués et que des avertissements d’intégrité soient émis. Negative : aucun avertissement émis sous cette condition. |
|DetailedNodeListLimit | Entier (valeur par défaut : 15) | Définit le nombre de nœuds par contrainte à inclure avant la troncation dans les rapports de réplica non placé. |
|DetailedPartitionListLimit | Entier (valeur par défaut : 15) | Définit le nombre de partitions par entrée de diagnostic pour une contrainte à inclure avant la troncation dans Diagnostics. |
|DetailedDiagnosticsInfoListLimit | Entier (valeur par défaut : 15) | Définit le nombre d’entrées de diagnostic (avec informations détaillées) par contrainte à inclure avant la troncation dans Diagnostics.|
|PLBRefreshGap | Durée en secondes (valeur par défaut : 1) | Spécifiez la durée en secondes. Définit le délai minimum à respecter avant que PLB ne réactualise l’état. |
|MinPlacementInterval | Durée en secondes (valeur par défaut : 1) | Spécifiez la durée en secondes. Définit le délai minimum à respecter avant deux passes de placement consécutives. |
|MinConstraintCheckInterval | Durée en secondes (valeur par défaut : 1) | Spécifiez la durée en secondes. Définit le délai minimum à respecter avant deux passes de vérification de contrainte consécutives. |
|MinLoadBalancingInterval | Durée en secondes (valeur par défaut : 5) | Spécifiez la durée en secondes. Définit le délai minimum à respecter avant deux passes d’équilibrage consécutives. |
|BalancingDelayAfterNodeDown | Durée en secondes (valeur par défaut : 120) |Spécifiez la durée en secondes. Ne démarrez pas l’équilibrage des activités pendant cette période après un événement d’arrêt de nœud. |
|BalancingDelayAfterNewNode | Durée en secondes (valeur par défaut : 120) |Spécifiez la durée en secondes. Ne démarrez pas l’équilibrage des activités pendant cette période après l’ajout d’un nouveau nœud. |
|ConstraintFixPartialDelayAfterNodeDown | Durée en secondes (valeur par défaut : 120) | Spécifiez la durée en secondes. Ne corrigez pas les violations de contrainte FaultDomain et UpgradeDomain pendant cette période après un événement d’arrêt de nœud. |
|ConstraintFixPartialDelayAfterNewNode | Durée en secondes (valeur par défaut : 120) | Spécifiez la durée en secondes. Ne corrigez pas les violations de contrainte FaultDomain et UpgradeDomain pendant cette période après l’ajout d’un nouveau nœud. |
|GlobalMovementThrottleThreshold | Valeur Uint (valeur par défaut : 1000) | Nombre maximum de mouvements autorisés dans la phase d’équilibrage de l’intervalle écoulé spécifié par GlobalMovementThrottleCountingInterval. |
|GlobalMovementThrottleThresholdForPlacement | Valeur Uint (valeur par défaut : 0) | Nombre maximum de mouvements autorisés dans la phase d’équilibrage de l’intervalle écoulé spécifié par GlobalMovementThrottleCountingInterval. 0 indique aucune limite.|
|GlobalMovementThrottleThresholdForBalancing | Valeur Uint (valeur par défaut : 0) | Nombre maximum de mouvements autorisés dans la phase d’équilibrage de l’intervalle écoulé spécifié par GlobalMovementThrottleCountingInterval. 0 indique aucune limite. |
|GlobalMovementThrottleCountingInterval | Durée en secondes (valeur par défaut : 600) | Spécifiez la durée en secondes. Indiquez la durée de l’intervalle écoulé pendant lequel effectuer le suivi des mouvements de réplica de domaine (utilisés avec GlobalMovementThrottleThreshold). Réglez cette valeur sur 0 pour ignorer complètement la limitation globale. |
|MovementPerPartitionThrottleThreshold | Valeur Uint (valeur par défaut : 50) | Aucun mouvement d’équilibrage n’est effectué pour une partition si le nombre d’équilibrage des réplicas de cette partition a atteint ou dépassé la valeur MovementPerFailoverUnitThrottleThreshold pendant la période écoulée spécifiée par MovementPerPartitionThrottleCountingInterval. |
|MovementPerPartitionThrottleCountingInterval | Durée en secondes (valeur par défaut : 600) | Spécifiez la durée en secondes. Indiquez la durée de l’intervalle écoulé pendant lequel effectuer le suivi des mouvements de réplica pour chaque partition (utilisée avec MovementPerPartitionThrottleThreshold). |
|PlacementSearchTimeout | Durée en secondes (valeur par défaut est 0.5) | Spécifiez la durée en secondes. Lors du placement de services, effectuez une recherche au moins aussi longue avant de renvoyer un résultat. |
|UseMoveCostReports | Valeur booléenne (valeur par défaut : false) | Demande à LB d’ignorer l’élément de coût de la fonction de score, ce qui génère un nombre potentiellement important de déplacements et, donc, un placement mieux équilibré. |
|PreventTransientOvercommit | Valeur booléenne (valeur par défaut : false) | Détermine si PLB doit immédiatement compter sur les ressources qui seront libérées par les déplacements initiés. Par défaut, PLB peut initier un mouvement sortant et un mouvement entrant sur le même nœud, ce qui peut créer une survalidation temporaire. Le réglage de ce paramètre sur true empêche ce type de survalidation et désactive la défragmentation à la demande (également appelée placementWithMove). |
|InBuildThrottlingEnabled | Valeur booléenne (valeur par défaut : false) | Détermine si la limitation intégrée est activée. |
|InBuildThrottlingAssociatedMetric | Chaîne (valeur par défaut : "") | Nom de la mesure associée à cette limitation. |
|InBuildThrottlingGlobalMaxValue | Entier (valeur par défaut : 0) |Nombre maximum de réplicas intégrés autorisés globalement. |
|SwapPrimaryThrottlingEnabled | Valeur booléenne (valeur par défaut : false)| Détermine si la limitation de basculement vers le réplica principal est activée. |
|SwapPrimaryThrottlingAssociatedMetric | Chaîne (valeur par défaut : "")| Nom de la mesure associée à cette limitation. |
|SwapPrimaryThrottlingGlobalMaxValue | Entier (valeur par défaut : 0) | Nombre maximum de réplicas principaux basculés autorisés globalement. |
|PlacementConstraintPriority | Entier (valeur par défaut : 0) | Détermine la priorité de la contrainte de placement : 0 : dure ; 1 : souple ; valeur négative : à ignorer. |
|PreferredLocationConstraintPriority | Entier (valeur par défaut : 2)| Détermine la priorité de la contrainte de placement préférée : 0 : dure ; 1 : souple ; 2 : optimisation ; valeur négative : à ignorer. |
|CapacityConstraintPriority | Entier (valeur par défaut : 0) | Détermine la priorité de la contrainte de capacité : 0 : dure ; 1 : souple ; valeur négative : à ignorer. |
|AffinityConstraintPriority | Entier (valeur par défaut : 0) | Détermine la priorité de la contrainte d’affinité : 0 : dure ; 1 : souple ; valeur négative : à ignorer. |
|FaultDomainConstraintPriority | Entier (valeur par défaut : 0) | Détermine la priorité de la contrainte de domaine d’erreur : 0 : dure ; 1 : souple ; valeur négative : à ignorer. |
|UpgradeDomainConstraintPriority | Entier (valeur par défaut : 1)| Détermine la priorité de la contrainte de domaine de mise à niveau : 0 : dure ; 1 : souple ; valeur négative : à ignorer. |
|ScaleoutCountConstraintPriority | Entier (valeur par défaut : 0) | Détermine la priorité de la contrainte de nombre de montées en charge : 0 : dure ; 1 : souple ; valeur négative : à ignorer. |
|ApplicationCapacityConstraintPriority | Entier (valeur par défaut : 0) | Détermine la priorité de la contrainte de capacité : 0 : dure ; 1 : souple ; valeur négative : à ignorer. |
|MoveParentToFixAffinityViolation | Valeur booléenne (valeur par défaut : false) | Paramètre déterminant si les réplicas parents peuvent être déplacés pour corriger les contraintes d’affinité.|
|MoveExistingReplicaForPlacement | Valeur booléenne (valeur par défaut : true) |Paramètre déterminant si le réplica doit être déplacé pendant le placement. |
|UseSeparateSecondaryLoad | Valeur booléenne (valeur par défaut : true) | Paramètre déterminant s’il convient d’utiliser une charge secondaire différente. |
|PlaceChildWithoutParent | Valeur booléenne (valeur par défaut : true) | Paramètre déterminant si le réplica de service enfant peut être placé si aucun réplica parent n’est actif. |
|PartiallyPlaceServices | Valeur booléenne (valeur par défaut : true) | Détermine si tous les réplicas de service dans le cluster seront placés selon le principe du « tout ou rien », en fonction du nombre limité de nœuds appropriés pour eux.|
|InterruptBalancingForAllFailoverUnitUpdates | Valeur booléenne (valeur par défaut : false) | Détermine si n’importe quel type de mise à jour des unités de basculement doit interrompre l’exécution lente ou rapide de l’équilibrage. Avec la valeur « false », l’exécution de l’équilibrage est interrompue si FailoverUnit est créé/supprimé, a des réplicas manquants, a modifié l’emplacement du réplica principal ou a modifié le nombre de réplicas. L’exécution de l’équilibrage n’est pas interrompue si FailoverUnit a des réplicas supplémentaires, a modifié un indicateur de réplica, n’a modifié que la version de la partition ou dans tout autre cas. |

## <a name="security"></a>Sécurité
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| ClusterProtectionLevel |None ou EncryptAndSign |None (par défaut) pour les clusters non sécurisés, EncryptAndSign pour les clusters sécurisés. |

## <a name="hosting"></a>Hébergement
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| ServiceTypeRegistrationTimeout |Durée en secondes, la valeur par défaut est 300 |Durée maximale autorisée pour que ServiceType soit inscrit auprès de la structure |
| ServiceTypeDisableFailureThreshold |Nombre entier, la valeur par défaut est 1 |Il s’agit du seuil pour le nombre d’échecs après lequel FailoverManager (FM) reçoit une notification de désactiver le type de service sur ce nœud et d’essayer un autre nœud pour le positionnement. |
| ActivationRetryBackoffInterval |Durée en secondes, la valeur par défaut est 5 |Intervalle d’interruption sur chaque échec d’activation. Sur chaque cas d’échec d’activation continue, le système retente l’activation pour une durée équivalente à MaxActivationFailureCount. L’intervalle avant nouvelle tentative pour chaque tentative est une combinaison de l’échec de l’activation continue et de l’intervalle d’interruption d’activation. |
| ActivationMaxRetryInterval |Durée en secondes, la valeur par défaut est 300 |Pour chaque cas d’échec d’activation continue, le système retente l’activation pour une durée équivalente à ActivationMaxFailureCount. ActivationMaxRetryInterval spécifie l’intervalle de temps d’attente avant une nouvelle tentative après chaque échec d’activation |
| ActivationMaxFailureCount |Nombre entier, la valeur par défaut est 10 |Nombre de fois où les nouvelles tentatives système ont échoué à l’activation avant d’abandonner |

## <a name="failovermanager"></a>FailoverManager
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| PeriodicLoadPersistInterval |Durée en secondes, la valeur par défaut est 10 |Ce paramètre détermine la fréquence à laquelle FM vérifie la présence de nouveaux rapports de charge |

## <a name="federation"></a>Fédération
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| LeaseDuration |Durée en secondes, la valeur par défaut est 30 |Durée de bail entre un nœud et ses voisins. |
| LeaseDurationAcrossFaultDomain |Durée en secondes, la valeur par défaut est 30 |Durée de bail entre un nœud et ses voisins dans des domaines d’erreur. |

## <a name="clustermanager"></a>ClusterManager
| **Paramètre** | **Valeurs autorisées** | **Conseils ou brève description** |
| --- | --- | --- |
| UpgradeStatusPollInterval |Durée en secondes, la valeur par défaut est 60 |Fréquence d’interrogation de l’état de mise à niveau de l’application. Cette valeur détermine le taux de mise à jour pour tout appel GetApplicationUpgradeProgress |
| UpgradeHealthCheckInterval |Durée en secondes, la valeur par défaut est 60 |Fréquence des contrôles de l’état d’intégrité durant une mise à niveau de l’application surveillée |
| FabricUpgradeStatusPollInterval |Durée en secondes, la valeur par défaut est 60 |Fréquence d’interrogation de l’état de mise à niveau de la structure. Cette valeur détermine le taux de mise à jour pour tout appel GetFabricUpgradeProgress |
| FabricUpgradeHealthCheckInterval |Durée en secondes, la valeur par défaut est 60 |Fréquence des contrôles de l’état d’intégrité durant une mise à niveau de la structure surveillée |
|InfrastructureTaskProcessingInterval | Durée en secondes, la valeur par défaut est 10 |Spécifiez la durée en secondes. Intervalle de traitement utilisé par l’ordinateur d’état de traitement de la tâche infrastructure. |
|TargetReplicaSetSize |Entier (valeur par défaut : 7) |Paramètre TargetReplicaSetSize pour ClusterManager. |
|MinReplicaSetSize |Entier (valeur par défaut : 3) |Paramètre MinReplicaSetSize pour ClusterManager. |
|ReplicaRestartWaitDuration |Durée en secondes (valeur par défaut : 60.0 * 30)|Spécifiez la durée en secondes. Paramètre ReplicaRestartWaitDuration pour ClusterManager. |
|QuorumLossWaitDuration |Durée en secondes (valeur par défaut : MaxValue) | Spécifiez la durée en secondes. Paramètre QuorumLossWaitDuration pour ClusterManager. |
|StandByReplicaKeepDuration | Durée en secondes (valeur par défaut : 3600.0 * 2)|Spécifiez la durée en secondes. Paramètre StandByReplicaKeepDuration pour ClusterManager. |
|PlacementConstraints | Chaîne (valeur par défaut : "") |Paramètre PlacementConstraints pour ClusterManager. |
|SkipRollbackUpdateDefaultService | Valeur booléenne (valeur par défaut : false) |CM ne rétablira pas les services par défaut mis à jour, lors de l’annulation de la mise à niveau de l’application. |
|EnableDefaultServicesUpgrade | Valeur booléenne (valeur par défaut : false) |Active la mise à niveau des services par défaut lors de la mise à niveau de l’application. Les descriptions de service par défaut sont remplacées après la mise à niveau. |
|InfrastructureTaskHealthCheckWaitDuration |Durée en secondes (valeur par défaut : 0)| Spécifiez la durée en secondes. Délai à respecter avant de commencer les vérifications d’intégrité après le post-traitement d’une tâche d’infrastructure. |
|InfrastructureTaskHealthCheckStableDuration | Durée en secondes (valeur par défaut : 0)| Spécifiez la durée en secondes. Délai à respecter pour examiner les contrôles d’intégrité passés avec succès consécutivement avant que le post-traitement d’une tâche d’infrastructure aboutisse. L’observation d’un contrôle d’intégrité ayant échoué réinitialise ce minuteur. |
|InfrastructureTaskHealthCheckRetryTimeout | Durée en secondes, la valeur par défaut est 60 |Spécifiez la durée en secondes. Délai à respecter pour relancer les contrôles d’intégrité ayant échoué, après le post-traitement d’une tâche d’infrastructure. L’examen d’un contrôle d’intégrité réussi réinitialise ce minuteur. |
|ImageBuilderTimeoutBuffer |Durée en secondes (valeur par défaut : 3) |Spécifiez la durée en secondes. Délai à respecter pour autoriser le renvoi au client des erreurs de délai d’expiration propres à Image Builder. Si cette mémoire tampon est trop petite, le délai d’expiration du client s’écoule avant le serveur et reçoit une erreur générique de délai d’expiration. |
|MinOperationTimeout | Durée en secondes, la valeur par défaut est 60 |Spécifiez la durée en secondes. Délai d’expiration global minimum pour le traitement interne des opérations sur ClusterManager. |
|MaxOperationTimeout |Durée en secondes (valeur par défaut : MaxValue) | Spécifiez la durée en secondes. Délai d’expiration global maximum pour le traitement interne des opérations sur ClusterManager. |
|MaxTimeoutRetryBuffer | Durée en secondes (valeur par défaut : 600) |Spécifiez la durée en secondes. Délai d’expiration maximum lorsque les nouvelles tentatives internes dues aux délais d’expiration sont <Original Timeout> + <MaxTimeoutRetryBuffer>. Un délai d’expiration supplémentaire est ajouté en incréments de MinOperationTimeout. |
|MaxCommunicationTimeout |Durée en secondes (valeur par défaut : 600) |Spécifiez la durée en secondes. Délai d’expiration maximum pour les communications internes entre ClusterManager et d’autres services système (comme le Service d’attribution de noms, le Gestionnaire de basculement, etc.). Ce délai d’expiration doit être inférieur au MaxOperationTimeout global (car il peut y avoir plusieurs communications entre les composants du système pour chaque opération de client). |
|MaxDataMigrationTimeout |Durée en secondes (valeur par défaut : 600) |Spécifiez la durée en secondes. Délai d’expiration maximum pour les opérations de récupération de migration de données après une mise à niveau de l’infrastructure. |
|MaxOperationRetryDelay |Durée en secondes (valeur par défaut : 5)| Spécifiez la durée en secondes. Délai maximum des nouvelles tentatives internes lorsque des défaillances sont rencontrées. |
|ReplicaSetCheckTimeoutRollbackOverride |Durée en secondes (valeur par défaut : 1200) | Spécifiez la durée en secondes. Si ReplicaSetCheckTimeout est défini sur la valeur maximale de DWORD, il est remplacé par la valeur de cette configuration dans le cadre de la restauration. La valeur utilisée pour la restauration par progression n’est pas remplacée. |
|ImageBuilderJobQueueThrottle |Entier (valeur par défaut : 10) |Limitation du nombre de threads pour la file d’attente de travaux proxy Image Builder sur les demandes d’application. |

## <a name="next-steps"></a>Étapes suivantes
Lisez les articles suivants pour plus d’informations sur la gestion des clusters :

[Ajouter, restaurer, supprimer des certificats de votre cluster Azure ](service-fabric-cluster-security-update-certs-azure.md) 

