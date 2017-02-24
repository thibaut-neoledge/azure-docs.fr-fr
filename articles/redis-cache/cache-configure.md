---
title: Configuration du Cache Redis Azure | Microsoft Docs
description: "Comprendre la configuration de Redis par défaut pour Cache Redis Azure et apprendre à configurer vos instances de Cache Redis Azure"
services: redis-cache
documentationcenter: na
author: steved0x
manager: douge
editor: tysonn
ms.assetid: d0bf2e1f-6a26-4e62-85ba-d82b35fc5aa6
ms.service: cache
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: cache-redis
ms.workload: tbd
ms.date: 02/10/2017
ms.author: sdanie
translationtype: Human Translation
ms.sourcegitcommit: 1c002b796ed926fb8b0c6cedf3e361816e50b803
ms.openlocfilehash: d51eeebba7ce2a1ca1005954feb0471f6ad7e05b


---
# <a name="how-to-configure-azure-redis-cache"></a>Configuration de Cache Redis Azure
Cette rubrique montre comment consulter et mettre à jour la configuration de vos instances de Cache Redis Azure et présente la configuration du serveur Redis par défaut pour les instances de Cache Redis Azure.

> [!NOTE]
> Pour plus d’informations sur la configuration et l’utilisation des fonctionnalités du cache Premium, voir [Comment configurer la persistance pour un Cache Redis Azure Premium](cache-how-to-premium-persistence.md), [Comment configurer le clustering pour un Cache Redis Azure Premium](cache-how-to-premium-clustering.md) et [Comment configurer la prise en charge de réseau virtuel pour un Cache Redis Azure Premium](cache-how-to-premium-vnet.md).
> 
> 

## <a name="configure-redis-cache-settings"></a>Configuration des paramètres de cache Redis
[!INCLUDE [redis-cache-create](../../includes/redis-cache-browse.md)]

Les paramètres de Cache Redis Azure sont affichés et configurés dans le panneau **Cache Redis** avec le **Menu Ressource**.

![Paramètres de Cache Redis](./media/cache-configure/redis-cache-settings.png)

Vous pouvez afficher et configurer les paramètres suivants à l’aide du **Menu Ressource**.

* [Vue d'ensemble](#overview)
* [Journal d’activité](#activity-log)
* [Contrôle d’accès (IAM)](#access-control-iam)
* [Balises](#tags)
* [Diagnostiquer et résoudre les problèmes](#diagnose-and-solve-problems)
* [Paramètres](#settings)
    * [Clés d’accès](#access-keys)
    * [Paramètres avancés](#advanced-settings)
    * [Redis Cache Advisor](#redis-cache-advisor)
    * [Niveau de tarification](#pricing-tier)
    * [Taille du cluster Redis](#cluster-size)
    * [Persistance des données Redis](#redis-data-persistence)
    * [Planification de mises à jour](#schedule-updates)
    * [Réseau virtuel](#virtual-network)
    * [Propriétés](#properties)
    * [Verrous](#locks)
    * [Script Automation](#automation-script)
* [Administration](#administration)
    * [Importer des données](#importexport)
    * [Exporter des données](#importexport)
    * [Reboot](#reboot)
* [Surveillance](#monitoring)
    * [Mesures Redis](#redis-metrics)
    * [Règles d'alerte](#alert-rules)
    * [Diagnostics](#diagnostics)
* [Paramètres de support et dépannage](#support-amp-troubleshooting-settings)
    * [Intégrité des ressources](#resource-health)
    * [Nouvelle demande de support](#new-support-request)


## <a name="overview"></a>Vue d'ensemble

La **Vue d’ensemble** fournit des informations de base sur votre cache, notamment le nom, les ports, le niveau tarifaire et des mesures de cache sélectionnées.

### <a name="activity-log"></a>Journal d’activité

Cliquez sur **Journaux d’activité** pour afficher les actions effectuées sur votre cache. Vous pouvez également utiliser le filtrage pour développer cette vue afin d’inclure d’autres ressources. Pour plus d’informations sur l’utilisation des journaux d’audit, voir [Consulter les journaux d’événements et d’audit](../monitoring-and-diagnostics/insights-debugging-with-events.md) et [Auditer les opérations avec Resource Manager](../azure-resource-manager/resource-group-audit.md). Pour plus d’informations sur la surveillance des événements du Cache Redis Azure, consultez [Opérations et alertes](cache-how-to-monitor.md#operations-and-alerts).

### <a name="access-control-iam"></a>Contrôle d’accès (IAM)

La section **Contrôle d’accès (IAM)** fournit une prise en charge pour le contrôle d’accès en fonction du rôle (RBAC) dans le portail Azure, pour aider les entreprises à répondre aux exigences de gestion des accès de façon simple et précise. Pour plus d’informations, consultez [Contrôle d’accès en fonction du rôle Azure](../active-directory/role-based-access-control-configure.md).

### <a name="tags"></a>Balises

La section **Balises** vous aide à organiser vos ressources. Pour plus d’informations, voir [Organisation des ressources Azure à l’aide de balises](../azure-resource-manager/resource-group-using-tags.md).


### <a name="diagnose-and-solve-problems"></a>Diagnostiquer et résoudre les problèmes

Cliquez sur **Diagnostiquer et résoudre les problèmes** pour afficher les problèmes courants et les stratégies de résolution associées.



## <a name="settings"></a>Paramètres
La section **Paramètres** vous permet d’accéder aux paramètres suivants et de les configurer pour votre cache.

![Paramètres](./media/cache-configure/redis-cache-general-settings.png)

* [Clés d’accès](#access-keys)
* [Paramètres avancés](#advanced-settings)
* [Redis Cache Advisor](#redis-cache-advisor)
* [Niveau de tarification](#pricing-tier)
* [Taille du cluster Redis](#cluster-size)
* [Persistance des données Redis](#redis-data-persistence)
* [Planification de mises à jour](#schedule-updates)
* [Réseau virtuel](#virtual-network)
* [Propriétés](#properties)
* [Verrous](#locks)
* [Script Automation](#automation-script)



### <a name="access-keys"></a>Clés d’accès
Cliquez sur **Clés d’accès** pour afficher ou régénérer les clés d’accès de votre cache. Ces clés sont utilisées avec le nom d’hôte et les ports à partir du panneau **Propriétés** par les clients se connectant à votre cache.

![Clés d’accès de Cache Redis](./media/cache-configure/redis-cache-manage-keys.png)

### <a name="advanced-settings"></a>Paramètres avancés
Vous pouvez configurer les paramètres suivants dans le panneau **Paramètres avancés**.

* [Ports d’accès](#access-ports)
* [Maxmemory-policy et maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved)
* [Notifications de Keyspace (paramètres avancés)](#keyspace-notifications-advanced-settings)

#### <a name="access-ports"></a>Ports d’accès
Par défaut, l’accès non SSL est désactivé pour les nouveaux caches. Pour activer le port non SSL, cliquez sur **Non** pour **Autoriser l’accès uniquement via SSL** dans le **panneau Paramètres avancés**, puis cliquez sur **Enregistrer**.

![Ports d’accès de Cache Redis](./media/cache-configure/redis-cache-access-ports.png)

#### <a name="maxmemory-policy-and-maxmemory-reserved"></a>Maxmemory-policy et maxmemory-reserved
Les paramètres **stratégie Maxmemory** et **maxmemory-reserved** dans le panneau **Paramètres avancés** configurent les stratégies de mémoire du cache. Le paramètre **maxmemory-policy** configure la stratégie d’éviction du cache et le paramètre **maxmemory-reserved** configure la mémoire réservée pour les processus non cache.

![Stratégie Maxmemory de Cache Redis](./media/cache-configure/redis-cache-maxmemory-policy.png)

**Maxmemory policy** vous permet de choisir parmi les stratégies d’éviction.

* volatile-lru est la valeur par défaut.
* allkeys-lru
* volatile-random
* allkeys-random
* volatile-ttl
* noeviction

Pour plus d’informations sur les stratégies maxmemory, voir [Stratégies d’éviction](http://redis.io/topics/lru-cache#eviction-policies).

Le paramètre **maxmemory-reserved** détermine la quantité de mémoire (en mégaoctets) réservée pour les opérations non cache telles que la réplication pendant le basculement. Il peut également être utilisé lorsque le taux de fragmentation est élevé. La définition de ce paramètre vous permet d’avoir une expérience de serveur Redis plus cohérente lorsque votre charge varie. Cette valeur doit être plus élevée pour les charges de travail comportant de nombreuses écritures. Lorsque la mémoire est réservée pour ces opérations, elle n’est pas disponible pour le stockage des données mises en cache.

> [!IMPORTANT]
> Le paramètre **maxmemory-reserved** est uniquement disponible pour les caches Standard et Premium.
> 
> 

#### <a name="keyspace-notifications-advanced-settings"></a>Notifications de Keyspace (paramètres avancés)
Accédez au panneau **Paramètres avancés** pour configurer les notifications de keyspace Redis. Les notifications de keyspace permettent aux clients de recevoir des notifications lorsque certains événements se produisent.

![Paramètres avancés de Cache Redis](./media/cache-configure/redis-cache-advanced-settings.png)

> [!IMPORTANT]
> Les notifications de keyspace et le paramètre **notify-keyspace-events** sont uniquement disponibles pour les caches Standard et Premium.
> 
> 

Pour plus d’informations, voir [Notifications de keyspace Redis](http://redis.io/topics/notifications). Pour un exemple de code, voir le fichier [KeySpaceNotifications.cs](https://github.com/rustd/RedisSamples/blob/master/HelloWorld/KeySpaceNotifications.cs) dans l’exemple [Hello world](https://github.com/rustd/RedisSamples/tree/master/HelloWorld).


<a name="recommendations"></a>
## <a name="redis-cache-advisor"></a>Redis Cache Advisor
Le panneau **Redis Cache Advisor** affiche des recommandations pour votre cache. Pendant les opérations normales, aucune recommandation n’est affichée. 

![Recommandations](./media/cache-configure/redis-cache-no-recommendations.png)

Si une condition se produit pendant les opérations de votre cache (notamment l’utilisation élevée de la mémoire, la bande passante réseau ou la charge du serveur), une alerte s’affiche dans le panneau **Cache Redis** .

![Recommandations](./media/cache-configure/redis-cache-recommendations-alert.png)

Des informations complémentaires sont disponibles dans le panneau **Recommandations** .

![Recommandations](./media/cache-configure/redis-cache-recommendations.png)

Vous pouvez surveiller ces mesures dans les sections [Graphiques de surveillance](cache-how-to-monitor.md#monitoring-charts) et [Graphiques d’utilisation](cache-how-to-monitor.md#usage-charts) du panneau **Cache Redis**.

Chaque niveau tarifaire est associé à des limites spécifiques concernant les connexions clientes, la mémoire et la bande passante. Si votre cache est proche de la capacité maximale pour ces mesures pendant une période prolongée, une recommandation est créée. Pour plus d’informations sur les mesures et limites évaluées par l’outil **Recommandations** , consultez le tableau suivant.

| Mesure du Cache Redis | Pour plus d’informations, consultez : |
| --- | --- |
| Utilisation de la bande passante réseau |[Performances du cache - Bande passante disponible](cache-faq.md#cache-performance) |
| Clients connectés |[Configuration du serveur Redis par défaut - maxclients](#maxclients) |
| Charge du serveur |[Graphiques d’utilisation - Charge du serveur Redis](cache-how-to-monitor.md#usage-charts) |
| Utilisation de la mémoire |[Performance du cache - Taille](cache-faq.md#cache-performance) |

Pour mettre à niveau votre cache, cliquez sur **Mettre à niveau maintenant** afin de modifier le [niveau tarifaire](#pricing-tier) et de mettre votre cache à l’échelle. Pour plus d’informations sur le choix d’un niveau tarifaire, consultez la rubrique [Que propose Cache Redis et quelle taille dois-je utiliser ?](cache-faq.md#what-redis-cache-offering-and-size-should-i-use).


### <a name="pricing-tier"></a>Niveau tarifaire
Cliquez sur **Niveau de tarification** pour afficher ou modifier le niveau de tarification de votre cache. Pour plus d’informations sur la mise à l’échelle, voir [Mise à l’échelle du cache Redis Azure](cache-how-to-scale.md).

![Niveau de tarification de Cache Redis](./media/cache-configure/pricing-tier.png)

<a name="cluster-size"></a>

### <a name="redis-cluster-size"></a>Taille du cluster Redis
Cliquez sur **Taille du cluster Redis (PREVIEW)** pour changer la taille du cluster pour un cache de niveau Premium en cours d’exécution avec le clustering activé.

> [!NOTE]
> Notez que si le niveau Premium du cache Redis Azure a été publié en disponibilité générale, la fonctionnalité Taille du cluster Redis est actuellement en version préliminaire.
> 
> 

![Taille du cluster Redis](./media/cache-configure/redis-cache-redis-cluster-size.png)

Pour modifier la taille du cluster, utilisez le curseur ou entrez un nombre compris entre 1 et 10 dans la zone de texte **Nombre de partitions**, puis cliquez sur **OK** pour enregistrer.

> [!IMPORTANT]
> Le clustering Redis est disponible uniquement pour les caches de niveau Premium. Pour plus d’informations, consultez [Comment configurer le clustering Redis pour un Cache Redis Azure Premium](cache-how-to-premium-clustering.md).
> 
> 


### <a name="redis-data-persistence"></a>Persistance des données Redis
Cliquez sur **Persistance des données Redis** pour activer, désactiver ou configurer la persistance des données de votre cache Premium.

![Persistance des données Redis](./media/cache-configure/redis-cache-persistence-settings.png)

Pour activer la persistance des données Redis, cliquez sur **Activé** pour activer la sauvegarde RDB (base de données Redis). Pour désactiver la persistance des données Redis, cliquez sur **Désactivé**.

Pour configurer l’intervalle de sauvegarde, sélectionnez une **Fréquence de sauvegarde** dans la liste déroulante. Vous avez le choix entre **15 minutes**, **30 minutes**, **60 minutes**, **6 heures**, **12 heures** et **24 heures**. Cet intervalle débute au moment où l’opération de sauvegarde précédente s’est terminée correctement. Une fois l’intervalle écoulé, une nouvelle sauvegarde est lancée.

Cliquez sur **Compte de stockage** pour sélectionner le compte de stockage à utiliser, puis, dans la liste déroulante **Clé de stockage**, choisissez d’utiliser la **Clé primaire** ou la **Clé secondaire**. Vous devez choisir un compte de stockage situé dans la même région que le cache. Un compte **Stockage Premium** est recommandé, car ce type de stockage offre un débit plus élevé. Chaque fois que la clé de stockage pour votre compte de persistance est régénérée, vous devez choisir de nouveau la clé souhaitée dans la liste déroulante **Clé de stockage**.

Cliquez sur **OK** pour enregistrer la configuration de persistance.

> [!IMPORTANT]
> La persistance des données Redis est disponible uniquement pour les caches de niveau Premium. Pour plus d’informations, consultez [Comment configurer la persistance pour un cache Redis Azure Premium](cache-how-to-premium-persistence.md).
> 
> 

### <a name="schedule-updates"></a>Planifier les mises à jour
Le panneau **Planification de mises à jour** vous permet de désigner une fenêtre de maintenance pour les mises à jour du serveur Redis de votre cache. 

> [!IMPORTANT]
> Notez que la fenêtre de maintenance s’applique uniquement aux mises à jour du serveur Redis et non à toute mise à jour d’Azure ou du système d’exploitation des machines virtuelles qui hébergent le cache.
> 
> 

![Planification de mises à jour](./media/cache-configure/redis-schedule-updates.png)

Pour spécifier une fenêtre de maintenance, vérifiez les jours choisis et spécifiez l’heure de début de la fenêtre de maintenance pour chaque jour, puis cliquez sur **OK**. Notez que l’heure de la maintenance est au format UTC. 

> [!IMPORTANT]
> La planification de mises à jour est uniquement disponible pour les caches de niveau Premium. Pour plus d’informations et pour obtenir des instructions, consultez la page [Administration du cache Redis Azure - Planification de mises à jour](cache-administration.md#schedule-updates).
> 
> 



## <a name="virtual-network"></a>Réseau virtuel
La section **réseau virtuel** permet de configurer les paramètres de réseau virtuel pour votre cache. Pour plus d’informations sur la création d’un cache Premium avec la prise en charge de réseau virtuel et sur la mise à jour de ses paramètres, consultez [Comment configurer la prise en charge de réseau virtuel pour un cache Redis Azure Premium](cache-how-to-premium-vnet.md).

> [!IMPORTANT]
> Les paramètres de réseau virtuel sont disponibles uniquement pour les caches Premium qui ont été configurés avec la prise en charge de réseau virtuel lors de la création du cache. 
> 
> 


### <a name="properties"></a>Propriétés
Cliquez sur **Propriétés** pour afficher des informations sur le cache, y compris le point de terminaison et les ports du cache.

![Propriétés de Cache Redis](./media/cache-configure/redis-cache-properties.png)

### <a name="locks"></a>Verrous
La section **Verrous** vous permet de verrouiller un abonnement, une ressource ou un groupe de ressources afin d’empêcher d’autres utilisateurs de votre organisation de supprimer ou modifier de manière accidentelle des ressources critiques. Pour plus d’informations, consultez [Verrouiller des ressources avec Azure Resource Manager](../azure-resource-manager/resource-group-lock-resources.md).

### <a name="automation-script"></a>Script Automation

Cliquez sur **Script Automation** pour générer et exporter un modèle de vos ressources déployées pour de futurs déploiements. Pour plus d’informations sur le fonctionnement des modèles, consultez [Déployer des ressources à l’aide de modèles Azure Resource Manager](../azure-resource-manager/resource-group-template-deploy.md).

## <a name="administration-settings"></a>Paramètres d’administration
Les paramètres de la section **Administration** vous permettent d’effectuer les tâches administratives suivantes pour votre cache Premium. 

![Administration](./media/cache-configure/redis-cache-administration.png)

* [Importer des données](#importexport)
* [Exporter des données](#importexport)
* [Reboot](#reboot)


> [!IMPORTANT]
> Les paramètres de cette section sont uniquement disponibles pour les caches de niveau Premium.
> 
> 

### <a name="importexport"></a>Importation/Exportation
L’Importation/Exportation est une opération de gestion de données de Cache Redis Azure qui vous permet d’importer ou d’exporter des données depuis ou vers le Cache Redis Azure, en important ou en exportant un instantané de base de données du Cache Redis (RDB) depuis un cache premium vers un objet blob de pages dans un compte Azure Storage. Cela vous permet de migrer entre différentes instances de Cache Redis Azure ou de remplir le cache de données avant utilisation.

L’importation peut servir à récupérer les fichiers RDB compatibles Redis depuis n’importe quel serveur Redis en cours d’exécution sur n’importe quel environnement ou cloud, y compris si Redis est exécuté sur Linux, Windows ou n’importe quel fournisseur de cloud tel qu’Amazon Web Services. Importer des données est un moyen simple de créer un cache pré-rempli de données. Pendant le processus d’importation, le Cache Azure Redis charge les fichiers RDB d’Azure Storage dans la mémoire, puis insère les clés dans le cache.

L’exportation vous permet d’exporter les données stockées dans le Cache Azure Redis vers un ou plusieurs fichier(s) RDB compatible(s). Vous pouvez utiliser cette fonctionnalité pour déplacer des données d’une instance de Cache Redis Azure à une autre, ou vers un autre serveur Redis. Pendant le processus d’exportation, un fichier temporaire est créé sur la machine virtuelle qui héberge l’instance de serveur de Cache Redis Azure, puis téléchargé vers le compte de stockage désigné. Lorsque l’opération d’exportation se termine avec un état de réussite ou d’échec, le fichier temporaire est supprimé.

> [!IMPORTANT]
> L’importation/exportation est uniquement disponible pour les caches de niveau Premium. Pour plus d’informations et pour obtenir des instructions, consultez la rubrique [Importer et exporter des données dans le cache Redis Azure](cache-how-to-import-export-data.md).
> 
> 

### <a name="reboot"></a>Reboot
Le panneau **Redémarrer** vous permet de redémarrer un ou plusieurs nœuds de votre cache. Cela vous permet de tester la résilience de votre application en cas d’échec.

![Reboot](./media/cache-configure/redis-cache-reboot.png)

Si vous avez un cache premium avec activation du clustering, vous pouvez sélectionner les partitions du cache à redémarrer.

![Reboot](./media/cache-configure/redis-cache-reboot-cluster.png)

Pour redémarrer un ou plusieurs nœuds de votre cache, sélectionnez les nœuds souhaités, puis cliquez sur **Redémarrer**. Si vous avez un cache Premium avec activation du clustering, sélectionnez les partitions à redémarrer, puis cliquez sur **Redémarrer**. Après quelques minutes, les nœuds sélectionnés sont redémarrés et sont de nouveau en ligne.

> [!IMPORTANT]
> Le redémarrage est uniquement disponible pour les caches de niveau Premium. Pour plus d’informations et pour obtenir des instructions, consultez la page [Administration du cache Redis Azure - Redémarrage](cache-administration.md#reboot).
> 
> 


## <a name="monitoring"></a>Surveillance

La section **Surveillance** vous permet de configurer les diagnostics et la surveillance de votre cache Redis. Pour plus d’informations sur la surveillance et les diagnostics du cache Redis Azure, voir [Surveillance du cache Redis Azure](cache-how-to-monitor.md).

![Diagnostics](./media/cache-configure/redis-cache-diagnostics.png)

* [Mesures Redis](#redis-metrics)
* [Règles d'alerte](#alert-rules)
* [Diagnostics](#diagnostics)

### <a name="redis-metrics"></a>Mesures Redis
Cliquez sur **Mesures Redis** pour [affichage des mesures](cache-how-to-monitor.md#how-to-view-metrics-and-customize-charts) pour votre cache.

### <a name="alert-rules"></a>Règles d'alerte

Cliquez sur **règles d’alerte** pour configurer des alertes en fonction des métriques du Cache Redis. Pour plus d'informations, consultez [Opérations et alertes](cache-how-to-monitor.md#operations-and-alerts).

### <a name="diagnostics"></a>Diagnostics

Cliquez sur **Diagnostics** pour [configurer le compte de stockage](cache-how-to-monitor.md#enable-cache-diagnostics) utilisé pour stocker les diagnostics de cache.

![Diagnostics de Cache Redis](./media/cache-configure/redis-cache-diagnostics-settings.png)

## <a name="support--troubleshooting-settings"></a>Paramètres de support et dépannage
Les paramètres de la section relative à la **prise en charge et à la résolution des problèmes** proposent des options pour la résolution des problèmes liés à votre cache.

![prise en charge et à la résolution des problèmes](./media/cache-configure/redis-cache-support-troubleshooting.png)

* [Intégrité des ressources](#resource-health)
* [Nouvelle demande de support](#new-support-request)

### <a name="resource-health"></a>Intégrité des ressources
**Resource Health** surveille vos ressources et vous indique si elles s’exécutent comme prévu. Pour plus d’informations sur le service Azure Resource Health, consultez [Vue d’ensemble d’Azure Resource Health](../resource-health/resource-health-overview.md).

> [!NOTE]
> Resource Health ne parvient pas à créer de rapport sur l’intégrité des instances du cache Redis Azure hébergées dans un réseau virtuel. Pour plus d’informations, voir [Toutes les fonctionnalités fonctionnent-elles lorsque vous hébergez un cache dans un réseau virtuel ?](cache-how-to-premium-vnet.md#do-all-cache-features-work-when-hosting-a-cache-in-a-vnet)
> 
> 

### <a name="new-support-request"></a>Nouvelle demande de support
Cliquez sur **Nouvelle demande de support** pour ouvrir une demande de support pour votre cache.





## <a name="default-redis-server-configuration"></a>Configuration du serveur Redis par défaut
Les nouvelles instances de Cache Redis Azure sont configurées avec les valeurs de configuration par défaut Redis suivantes.

> [!NOTE]
> Les paramètres de cette section ne peuvent pas être modifiés à l’aide de la méthode `StackExchange.Redis.IServer.ConfigSet`. Si cette méthode est appelée avec une des commandes de cette section, une exception semblable à la suivante est levée :  
> 
> `StackExchange.Redis.RedisServerException: ERR unknown command 'CONFIG'`
> 
> Toutes les valeurs configurables, comme **max-memory-policy**, peuvent être modifiées via le portail Azure ou via des outils de gestion en ligne de commande, comme Azure CLI ou PowerShell.
> 
> 

| Paramètre | Valeur par défaut | Description |
| --- | --- | --- |
| bases de données |16 |Le nombre de bases de données par défaut est 16, mais vous pouvez en configurer un nombre différent selon le niveau tarifaire.<sup>1</sup> La base de données par défaut est DB 0. Vous pouvez en sélectionner une autre en fonction de la connexion en utilisant `connection.GetDatabase(dbid)` où dbid est un nombre compris entre `0` et `databases - 1`. |
| maxclients |Dépend du niveau tarifaire<sup>2</sup> |Le nombre maximal de clients connectés autorisés en même temps. Une fois la limite atteinte, Redis ferme toutes les nouvelles connexions en envoyant une erreur « nombre maximal de clients atteint ». |
| maxmemory-policy |volatile-lru |La stratégie maxmemory est le paramètre définissant la sélection par Redis des éléments à supprimer lorsque la mémoire maximale (la taille du cache que vous avez sélectionné lorsque vous avez créé le cache) est atteinte. Avec Cache Redis Azure, le paramètre par défaut est volatile-lru, qui supprime les clés avec une expiration définie à l’aide d’un algorithme dernier récemment utilisé (LRU). Ce paramètre peut être configuré dans le portail Azure. Pour plus d’informations, consultez [Maxmemory-policy et maxmemory-reserved](#maxmemory-policy-and-maxmemory-reserved). |
| maxmemory-samples |3 |Les algorithmes LRU et TTL ne sont pas précis mais estimés (afin d’économiser de la mémoire), vous pouvez donc sélectionner également la taille d’échantillon à vérifier. Par exemple, Redis vérifie par défaut trois clés et choisit celle qui a été utilisée il y a le plus longtemps. |
| lua-time-limit |5 000 |Temps d’exécution maximal d’un script Lua en millisecondes. Si la durée d’exécution maximale est atteinte, Redis enregistre qu’un script est toujours en cours d’exécution après la durée maximale autorisée et commence à répondre aux requêtes avec une erreur. |
| lua-event-limit |500 |Il s’agit de la taille maximale de la file d’attente des événements de script. |
| client-output-buffer-limit normalclient-output-buffer-limit pubsub |0 0 032mb 8mb 60 |Les limites de mémoire tampon de sortie client peuvent servir à forcer la déconnexion des clients qui ne lisent pas les données à partir du serveur suffisamment rapidement pour une raison quelconque (une raison courante est qu’un client Pub/Sub ne peut pas consommer les messages aussi rapidement que le serveur de publication les génère). Pour plus d’informations, consultez [http://redis.io/topics/clients](http://redis.io/topics/clients). |

<a name="databases"></a>
<sup>1</sup>La limite de `databases` est différente pour chaque niveau tarifaire du cache Redis Azure, et peut être définie lors de la création du cache. Si aucun paramètre `databases` n’est spécifié lors de la création du cache, la valeur par défaut est 16.

* Caches De base et Standard
  * Cache C0 (250 Mo) : jusqu’à 16 bases de données
  * Cache C1 (1 Go) : jusqu’à 16 bases de données
  * Cache C2 (2,5 Go) : jusqu’à 16 bases de données
  * Cache C3 (6 Go) : jusqu’à 16 bases de données
  * Cache C4 (13 Go) : jusqu’à 32 bases de données
  * Cache C5 (26 Go) : jusqu’à 48 bases de données
  * Cache C6 (53 Go) : jusqu’à 64 bases de données
* Caches Premium
  * P1 (6 à 60 Go) - jusqu’à 16 bases de données
  * P2 (13 à 130 Go) - jusqu’à 32 bases de données
  * P3 (26 à 260 Go) - jusqu’à 48 bases de données
  * P4 (53 à 530 Go) - jusqu’à 64 bases de données
  * Tous les caches de niveau Premium avec cluster Redis activé : le cluster Redis prend uniquement en charge l’utilisation de la base de données 0 pour que la limite `databases` de chaque cache Premium avec cluster Redis activé soit effectivement 1 et que la commande [Sélectionner](http://redis.io/commands/select) ne soit pas autorisée. Pour en savoir plus, voir, [Dois-je apporter des modifications à mon application cliente pour utiliser le clustering ?](#do-i-need-to-make-any-changes-to-my-client-application-to-use-clustering)

> [!NOTE]
> Les paramètres `databases` peut être configuré uniquement lors de la création du cache et uniquement à l’aide de PowerShell, de l’interface de ligne de commande ou d’autres clients de gestion. Pour obtenir un exemple de configuration de `databases` lors de la création du cache à l’aide de PowerShell, voir [New-AzureRmRedisCache](cache-howto-manage-redis-cache-powershell.md#databases).
> 
> 

<a name="maxclients"></a>
<sup>2</sup>`maxclients` est différent pour chaque niveau tarifaire du cache Redis Azure.

* Caches De base et Standard
  * Cache C0 (250 Mo) : jusqu’à 256 connexions
  * Cache C1 (1 Go) : jusqu’à 1 000 connexions
  * Cache C2 (2,5 Go) : jusqu’à 2 000 connexions
  * Cache C3 (6 Go) : jusqu’à 5 000 connexions
  * Cache C4 (13 Go) : jusqu’à 10 000 connexions
  * Cache C5 (26 Go) : jusqu’à 15 000 connexions
  * Cache C6 (53 Go) : jusqu’à 20 000 connexions
* Caches Premium
  * P1 (6 Go - 60 Go) : jusqu’à 7 500 connexions
  * P2 (13 Go - 130 Go) : jusqu’à 15 000 connexions
  * P3 (26 Go - 260 Go) : jusqu’à 30 000 connexions
  * P4 (53 Go - 530 Go) : jusqu’à 40 000 connexions

## <a name="redis-commands-not-supported-in-azure-redis-cache"></a>Commandes Redis non prises en charge dans le Cache Redis Azure
> [!IMPORTANT]
> Étant donné que la configuration et la gestion des instances de cache Redis Azure sont gérées par Microsoft, les commandes suivantes sont désactivées. Si vous essayez de les utiliser, vous recevez un message d’erreur similaire à `"(error) ERR unknown command"`.
> 
> * BGREWRITEAOF
> * BGSAVE
> * CONFIG
> * DEBUG
> * MIGRATE
> * Enregistrer
> * SHUTDOWN
> * SLAVEOF
> * CLUSTER : les commandes d’écriture du cluster sont désactivées, mais celles de lecture seule sont autorisées.
> 
> 

Pour plus d’informations sur les commandes Redis, consultez [http://redis.io/commands](http://redis.io/commands).

## <a name="redis-console"></a>Console Redis
Vous pouvez adresser en toute sécurité des commandes aux instances de Cache Redis Azure à l’aide de la **console Redis**disponible pour les caches Standard et Premium.

> [!IMPORTANT]
> La console Redis ne fonctionne pas avec VNET, la mise en cluster et les bases de données autres que 0. 
> 
> * [Réseau virtuel](cache-how-to-premium-vnet.md) : quand votre cache fait partie d’un réseau virtuel, seuls les clients de ce réseau virtuel peuvent accéder au cache. Étant donné que la Console Redis utilise le client redis-cli.exe hébergé sur des machines virtuelles qui ne font pas partie de votre réseau virtuel, il ne peut pas se connecter à votre cache.
> * [Clustering](cache-how-to-premium-clustering.md) : la Console Redis utilise le client redis-cli.exe, qui ne prend pas en charge le clustering à l’heure actuelle. L’utilitaire redis-cli de la branche [unstable](http://redis.io/download) du dépôt Redis sur GitHub implémente la prise en charge de base quand il est démarré avec le commutateur `-c`. Pour plus d’informations, voir [Playing with the cluster](http://redis.io/topics/cluster-tutorial#playing-with-the-cluster) (Manipulation du cluster) sur [http://redis.io](http://redis.io) dans [Redis cluster tutorial](http://redis.io/topics/cluster-tutorial) (Didacticiel du cluster Redis).
> * la console Redis effectue une nouvelle connexion à la base de données 0 chaque fois que vous envoyez une commande. Vous ne pouvez pas utiliser la commande `SELECT` pour sélectionner une autre base de données, car la base de données est réinitialisée à 0 avec chaque commande. Pour plus d’informations sur l’exécution des commandes Redis, y compris la modification sur une autre base de données, consultez [Comment exécuter des commandes Redis ?](cache-faq.md#how-can-i-run-redis-commands)
> 
> 

Pour accéder à la console Redis, cliquez sur **Console** dans le panneau **Cache Redis**.

![Console Redis](./media/cache-configure/redis-console-menu.png)

Pour exécuter des commandes sur votre instance de cache, tapez simplement la commande souhaitée dans la console.

![Console Redis](./media/cache-configure/redis-console.png)

Pour obtenir la liste des commandes Redis désactivées pour le cache Redis Azure, consultez la section précédente intitulée [Commandes Redis non prises en charge dans le cache Redis Azure](#redis-commands-not-supported-in-azure-redis-cache) . Pour plus d’informations sur les commandes Redis, consultez [http://redis.io/commands](http://redis.io/commands). 

## <a name="move-your-cache-to-a-new-subscription"></a>Déplacement du cache vers un nouvel abonnement
Pour déplacer votre cache vers un nouvel abonnement, cliquez sur **Déplacer**.

![Déplacer le Cache Redis](./media/cache-configure/redis-cache-move.png)

Pour plus d’informations sur le déplacement des ressources d’un groupe de ressources vers un autre et d’un abonnement vers un autre, consultez [Déplacer des ressources vers un nouveau groupe de ressource ou un nouvel abonnement](../azure-resource-manager/resource-group-move-resources.md).

## <a name="next-steps"></a>Étapes suivantes
* Pour plus d'informations sur l'utilisation des commandes Redis, consultez [Exécution des commandes Redis](cache-faq.md#how-can-i-run-redis-commands).




<!--HONumber=Feb17_HO2-->


