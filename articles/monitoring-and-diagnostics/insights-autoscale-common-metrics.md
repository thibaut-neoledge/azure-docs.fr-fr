---
title: "Mesures courantes pour la mise à l’échelle automatique dans Azure Monitor | Microsoft Docs"
description: "Découvrez les métriques utilisées pour la mise à l’échelle automatique de vos instances Cloud Services, Virtual Machines et Web Apps."
author: kamathashwin
manager: carolz
editor: 
services: monitoring-and-diagnostics
documentationcenter: monitoring-and-diagnostics
ms.assetid: 189b2a13-01c8-4aca-afd5-90711903ca59
ms.service: monitoring-and-diagnostics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/6/2016
ms.author: ashwink
translationtype: Human Translation
ms.sourcegitcommit: 197ebd6e37066cb4463d540284ec3f3b074d95e1
ms.openlocfilehash: bc9d9aa1cbe704de5f7fb960f1467aa522acd0b5
ms.lasthandoff: 03/31/2017


---
# <a name="azure-monitor-autoscaling-common-metrics"></a>Métriques courantes pour la mise à l’échelle automatique d’Azure Monitor
La fonction de mise à l’échelle automatique d’Azure Monitor vous permet de diminuer ou d’augmenter la taille des instances en fonction des données de télémétrie (métriques). Ce document décrit les métriques courantes que vous pouvez utiliser. Dans le portail Azure pour les services cloud et les batteries de serveurs, vous pouvez choisir les métriques de la ressource à mettre à l’échelle. Toutefois, vous pouvez également choisir des métriques à partir d’une autre ressource à mettre à l’échelle.

Les informations suivantes s’appliquent pour la mise à l’échelle des jeux de mise à l’échelle de machine virtuelle.

> [!NOTE]
> Ces informations s’appliquent uniquement aux machines virtuelles et jeux de mise à l’échelle de machine virtuelle basés sur Resource Manager. 
> 

## <a name="compute-metrics-for-resource-manager-based-vms"></a>Calcul des métriques pour les machines virtuelles basées sur Resource Manager
Par défaut, les machines virtuelles et jeux de mise à l’échelle de machine virtuelle basés sur Resource Manager émettent des métriques de base (niveau hôte). En outre, lorsque vous configurez la collecte de données de diagnostic pour une machine virtuelle ou des jeux de mise à l’échelle de machine virtuelle Azure, l’extension de diagnostic Azure émet également les compteurs de performances du système d’exploitation invité (communément appelés « Métriques de système d’exploitation invité »).  Vous utilisez toutes ces métriques dans les règles de mise à l’échelle automatique. 

Vous pouvez utiliser l’API/PoSH/CLI `Get MetricDefinitions` pour afficher les métriques disponibles pour votre ressource VMSS. 

Si vous utilisez des jeux de mise à l’échelle de machine virtuelle et qu’une métrique que vous cherchez n’est pas répertoriée, il est probable qu’elle soit *désactivée* dans votre extension de diagnostics.

Si une métrique particulière n’est pas en cours d’échantillonnage ou de transfert vers la fréquence souhaitée, vous pouvez mettre à jour la configuration des diagnostics.

Si l’un des deux cas ci-dessus s’applique, consultez la page [Utiliser PowerShell pour activer Azure Diagnostics sur une machine virtuelle exécutant Windows](../virtual-machines/windows/ps-extensions-diagnostics.md) pour savoir comment utiliser PowerShell pour configurer et mettre à jour votre extension de diagnostics de machine virtuelle Windows Azure afin d’activer la métrique. Cet article inclut également un exemple de fichier de configuration de diagnostics.

### <a name="host-metrics-for-resource-manager-based-windows-and-linux-vms"></a>Métriques de l’hôte pour les machines virtuelles Windows et Linux basées sur Resource Manager
Les métriques de niveau hôte suivantes sont émies par défaut pour les machines virtuelles et jeux de mise à l’échelle de machine virtuelle Azure dans les instances Windows et Linux. Ces métriques décrivent votre machine virtuelle Azure, mais sont collectées à partir de l’hôte de la machine Virtuelle Azure plutôt que via l’agent installé sur la machine virtuelle invitée. Vous pouvez utiliser ces métriques dans les règles de mise à l’échelle automatique. 

- [Métriques de l’hôte pour les machines virtuelles Windows et Linux basées sur Resource Manager](monitoring-supported-metrics.md#microsoftcomputevirtualmachines)
- [Métriques de l’hôte pour les jeux de mise à l’échelle de machine virtuelle Windows et Linux basées sur Resource Manager](monitoring-supported-metrics.md#microsoftcomputevirtualmachinescalesets)

### <a name="guest-os-metrics-resource-manager-based-windows-vms"></a>Métriques de SE invité pour machines virtuelles Windows basées sur Resource Manager
Lorsque vous créez une machine virtuelle dans Azure, les diagnostics sont effectués grâce à l’extension Diagnostics. L’extension de diagnostics émet un ensemble de métriques extraites de la machine virtuelle. Cela signifie que vous pouvez automatiser la mise à l’échelle des métriques qui ne sont pas émises par défaut.

Vous pouvez utiliser la commande suivante dans PowerShell pour générer une liste des métriques.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Vous pouvez créer une alerte pour les métriques suivantes :

| Nom de métrique | Unité |
| --- | --- |
| \Processor(_Total)\% temps processeur |Pourcentage |
| \Processor(_Total)\\% temps privilégié |Pourcentage |
| \Processor(_Total)\\% temps utilisateur |Pourcentage |
| \Processor Information(_Total)\Fréquence du processeur |Nombre |
| \System\Processus |Nombre |
| \Process(_Total)\Nombre de threads |Nombre |
| \Process(_Total)\Nombre de handles |Nombre |
| \Memory\\% octets validés en cours d’utilisation |Pourcentage |
| \Memory\Octets disponibles |Octets |
| \Memory\Octets validés |Octets |
| \Memory\Limite de mémoire dédiée |Octets |
| \Memory\Octets de réserve paginée |Octets |
| \Memory\Octets de réserve non paginée |Octets |
| \PhysicalDisk(_Total)\\% temps disque |Pourcentage |
| \PhysicalDisk(_Total)\\% temps de lecture du disque |Pourcentage |
| \PhysicalDisk(_Total)\\% temps écriture du disque |Pourcentage |
| \PhysicalDisk(_Total)\Disk Transfers/sec |Nombre par seconde |
| \PhysicalDisk(_Total)\Lectures disque/s |Nombre par seconde |
| \PhysicalDisk(_Total)\Écritures disque/s |Nombre par seconde |
| \PhysicalDisk(_Total)\Octets disque/s |Octets par seconde |
| \PhysicalDisk(_Total)\Lectures disque, octets/s |Octets par seconde |
| \PhysicalDisk(_Total)\Écritures disque, octets/s |Octets par seconde |
| \PhysicalDisk(_Total)\Longueur moyenne Longueur de file d'attente de disque |Nombre |
| \PhysicalDisk(_Total)\Longueur moyenne de file d’attente lecture disque |Nombre |
| \PhysicalDisk(_Total)\Longueur moyenne de file d’attente écriture disque |Nombre |
| \LogicalDisk(_Total)\\% espace libre |Pourcentage |
| \LogicalDisk(_Total)\Mégaoctets libres |Nombre |

### <a name="guest-os-metrics-linux-vms"></a>Métriques de SE invité pour les machines virtuelles Linux
Lorsque vous créez une machine virtuelle dans Azure, les diagnostics sont activés par défaut grâce à l’extension Diagnostics.

Vous pouvez utiliser la commande suivante dans PowerShell pour générer une liste des métriques.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Vous pouvez créer une alerte pour les métriques suivantes :

| Nom de métrique | Unité |
| --- | --- |
| \Mémoire\Mémoire disponible |Octets |
| \Mémoire\Pourcentage de mémoire disponible |Pourcentage |
| \Mémoire\Mémoire utilisée |Octets |
| \Mémoire\Pourcentage de mémoire utilisée |Pourcentage |
| \Mémoire\Pourcentage de mémoire utilisée par le cache |Pourcentage |
| \Mémoire\Pages par seconde |Nombre par seconde |
| \Mémoire\Pages lues par seconde |Nombre par seconde |
| \Mémoire\Pages écrites par seconde |Nombre par seconde |
| \Mémoire\Échanges disponibles |Octets |
| \Mémoire\Pourcentage d’échanges disponibles |Pourcentage |
| \Mémoire\Échanges utilisés |Octets |
| \Mémoire\Pourcentage d’échanges utilisés |Pourcentage |
| \Processeur\Pourcentage de temps d’inactivité |Pourcentage |
| \Processeur\Pourcentage de temps d’utilisateur |Pourcentage |
| \Processeur\Pourcentage de temps d’efficacité |Pourcentage |
| \Processeur\Pourcentage de temps privilégié |Pourcentage |
| \Processeur\Pourcentage de temps d’interruption |Pourcentage |
| \Processeur\Pourcentage de temps d’appels de procédure différés (DPC) |Pourcentage |
| \Processeur\Pourcentage de temps de processeur |Pourcentage |
| \Processeur\Pourcentage de temps d’attente |Pourcentage |
| \Disque physique\Octets par seconde |Octets par seconde |
| \Disque physique\Octets lus par seconde |Octets par seconde |
| \Disque physique\Octets écrits par seconde |Octets par seconde |
| \Disque physique\Transferts par seconde |Nombre par seconde |
| \Disque physique\Lectures par seconde |Nombre par seconde |
| \Disque physique\Écritures par seconde |Nombre par seconde |
| \Disque physique\Temps de lecture moyen |Secondes |
| \Disque physique\Temps d’écriture moyen |Secondes |
| \Disque physique\Temps de transfert moyen |Secondes |
| \Disque physique\Longueur moyenne de la file d’attente du disque |Nombre |
| \Interface réseau\Octets transmis |Octets |
| \Interface réseau\Octets reçus |Octets |
| \Interface réseau\Paquets transmis |Nombre |
| \Interface réseau\Paquets reçus |Nombre |
| \Interface réseau\Total des octets |Octets |
| \Interface réseau\Total des erreurs Rx |Nombre |
| \Interface réseau\Total des erreurs Tx |Nombre |
| \Interface réseau\Total des collisions |Nombre |

## <a name="commonly-used-web-server-farm-metrics"></a>Métriques web couramment utilisées (batterie de serveurs)
Vous pouvez également effectuer la mise à l’échelle en fonction des métriques de serveur web courantes, telles que la longueur de file d’attente HTTP. Son nom de métrique est **Longueur de file d’attente HTTP**.  La section suivante répertorie les métriques de batterie de serveurs (Web Apps) disponibles.

### <a name="web-apps-metrics"></a>Métriques Web Apps
Vous pouvez utiliser la commande suivante dans PowerShell pour générer une liste des métriques Web Apps.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Ces métriques permettent d’émettre une alerte ou de procéder à un mise à l’échelle.

| Nom de métrique | Unité |
| --- | --- |
| Pourcentage UC |Pourcentage |
| Pourcentage mémoire |Pourcentage |
| Longueur de file d’attente du disque |Nombre |
| Longueur de file d’attente HTTP |Nombre |
| Octets reçus |Octets |
| Octets envoyés |Octets |

## <a name="commonly-used-storage-metrics"></a>Métriques couramment utilisées dans Azure Storage
Vous pouvez procéder à une mise à l’échelle en fonction de la métrique Longueur de file d’attente, qui correspond au nombre de messages dans la file d’attente de stockage. La longueur de file d’attente de stockage est une métrique spéciale et le seuil appliqué sera le nombre de messages par instance. Par exemple, si vous avez deux instances et que le seuil est défini sur 100, la mise à l’échelle aura lieu lorsque la file d’attente contiendra 200 messages. Cela peut être 100 messages par instance, 120 et 80 ou toute autre combinaison qui correspond à 200 ou plus. 

Configurez de paramètre dans le panneau **Paramètres** du Portail Azure. Pour les jeux de mise à l’échelle de machine virtuelle, vous pouvez mettre à jour le paramètre Mise à l’échelle automatique dans le modèle Resource Manager afin d’utiliser *metricName* avec la valeur *ApproximateMessageCount*, puis transmettre l’ID de la file d’attente de stockage avec la valeur *metricResourceUri*.

Par exemple, avec un compte de stockage classique, le paramètre de mise à l’échelle automatique metricTrigger peut inclure :

```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ClassicStorage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
 ```

Pour un compte de stockage (non classique), le paramètre metricTrigger peut inclure :

```
"metricName": "ApproximateMessageCount",
"metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.Storage/storageAccounts/STORAGE_ACCOUNT_NAME/services/queue/queues/QUEUE_NAME"
```

## <a name="commonly-used-service-bus-metrics"></a>Métriques Service Bus généralement utilisées
Vous pouvez procéder à une mise à l’échelle en fonction de la longueur de la file d’attente Service Bus, autrement dit en fonction du nombre de messages présents dans cette file d’attente. La longueur de la file d’attente Service Bus est une métrique particulière. Le seuil correspond au nombre de messages par instance. Par exemple, si vous avez deux instances et que le seuil est défini sur 100, la mise à l’échelle aura lieu lorsque la file d’attente contiendra 200 messages. Cela peut être 100 messages par instance, 120 et 80 ou toute autre combinaison qui correspond à 200 ou plus. 

Pour les jeux de mise à l’échelle de machine virtuelle, vous pouvez mettre à jour le paramètre Mise à l’échelle automatique dans le modèle Resource Manager afin d’utiliser *metricName* avec la valeur *ApproximateMessageCount*, puis transmettre l’ID de la file d’attente de stockage avec la valeur *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/SUBSCRIPTION_ID/resourceGroups/RES_GROUP_NAME/providers/Microsoft.ServiceBus/namespaces/SB_NAMESPACE/queues/QUEUE_NAME"
```

> [!NOTE]
> Le concept de groupe de ressources n’existe pas pour Service Bus, mais Azure Resource Manager crée un groupe de ressources par défaut par région. Le groupe de ressources est généralement affiché au format « [Région] Service Bus par défaut ». Par exemple, « Est des États-Unis Service Bus par défaut », « Ouest des États-Unis Service Bus par défaut », « Est de l’Australie Service Bus par défaut », etc.
> 
> 


