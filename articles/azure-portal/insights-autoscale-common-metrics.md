<properties
	pageTitle="Azure Insights : Métriques courantes pour la mise à l’échelle automatique d’Azure Insights | Microsoft Azure"
	description="Découvrez les métriques utilisées pour la mise à l’échelle automatique de vos instances Cloud Services, Virtual Machines et Web Apps."
	authors="kamathashwin"
	manager=""
	editor=""
	services="monitoring"
	documentationCenter="monitoring"/>

<tags
	ms.service="monitoring"
	ms.workload="na"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="08/02/2016"
	ms.author="ashwink"/>

# Métriques courantes pour la mise à l’échelle automatique d’Azure Insights

La fonction de mise à l’échelle automatique d’Azure Insights vous permet de diminuer ou d’augmenter la taille des instances en fonction des données de télémétrie (métriques). Ce document décrit les métriques courantes que vous pouvez utiliser. Dans le portail Azure pour les services cloud et les batteries de serveurs, vous pouvez choisir les métriques de la ressource à mettre à l’échelle. Toutefois, vous pouvez également choisir des métriques à partir d’une autre ressource à mettre à l’échelle.

Voici comment rechercher et répertorier les métriques dont vous avez besoin pour la mise à l’échelle. Les éléments suivants s’appliquent également pour la mise à l’échelle des jeux de mise à l’échelle de machine virtuelle.

## Calcul des métriques
Par défaut, les machines virtuelles v2 d’Azure sont fournies avec une extension de diagnostics configurée et les métriques suivantes activées.

- [Métriques invitées pour les machines virtuelles v2 Windows](#compute-metrics-for-windows-vm-v2-as-a-guest-os)
- [Métriques invitées pour les machines virtuelles v2 Linux](#compute-metrics-for-linux-vm-v2-as-a-guest-os)

Vous pouvez utiliser l’API/PoSH/CLI `Get MetricDefinitions` pour afficher les métriques disponibles pour votre ressource VMSS.

Si vous utilisez des jeux de mise à l’échelle de machine virtuelle et qu’une métrique que vous cherchez n’est pas répertoriée, il est probable qu’elle soit *désactivée* dans votre extension de diagnostics.

Si une métrique particulière n’est pas en cours d’échantillonnage ou de transfert vers la fréquence souhaitée, vous pouvez mettre à jour la configuration des diagnostics.

Si l’un des deux cas ci-dessus s’applique, consultez la page [Utiliser PowerShell pour activer Azure Diagnostics sur une machine virtuelle exécutant Windows](../virtual-machines/virtual-machines-windows-ps-extensions-diagnostics.md) pour savoir comment utiliser PowerShell pour configurer et mettre à jour votre extension de diagnostics de machine virtuelle Microsoft Azure afin d’activer la métrique. Cet article inclut également un exemple de fichier de configuration de diagnostics.

### Calculer les métriques pour une machine virtuelle Windows v2 en tant que système d’exploitation invité

Lorsque vous créez une machine virtuelle v2 dans Azure, les diagnostics sont effectués grâce à l’extension Diagnostics.

Vous pouvez utiliser la commande suivante dans PowerShell pour générer une liste des métriques.


```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Vous pouvez créer une alerte pour les métriques suivantes.

|Nom de métrique|	Unité|
|---|---|
|\\Processor(\_Total)\\% Processor Time |Pourcentage|
|\\Processor(\_Total)\\% temps privilégié |Pourcentage|
|\\Processor(\_Total)\\% temps utilisateur |Pourcentage|
|\\Processor Information(\_Total)\\Fréquence du processeur |Nombre|
|\\System\\Processus|	Nombre|
|\\Process(\_Total)\\Nombre de threads|	Nombre|
|\\Process(\_Total)\\Nombre de handles |Nombre|
|\\Memory\\% octets validés en cours d’utilisation |Pourcentage|
|\\Memory\\Octets disponibles|	Octets|
|\\Memory\\Octets validés |Octets|
|\\Memory\\Limite de mémoire dédiée|	Octets|
|\\Memory\\Octets de réserve paginée|	Octets|
|\\Memory\\Octets de réserve non paginée|	Octets|
|\\PhysicalDisk(\_Total)\\Pourcentage du temps disque|	Pourcentage|
|\\PhysicalDisk(\_Total)\\Pourcentage du temps de lecture du disque|	Pourcentage|
|\\PhysicalDisk(\_Total)\\Pourcentage du temps écriture du disque|	Pourcentage|
|\\PhysicalDisk(\_Total)\\Disk Transfers/sec |Nombre par seconde|
|\\PhysicalDisk(\_Total)\\Lectures disque/s |Nombre par seconde|
|\\PhysicalDisk(\_Total)\\Écritures disque/s |Nombre par seconde|
|\\PhysicalDisk(\_Total)\\Octets disque/s |Octets par seconde|
|\\PhysicalDisk(\_Total)\\Lectures disque, octets/s|	Octets par seconde|
|\\PhysicalDisk(\_Total)\\Écritures disque, octets/s |Octets par seconde|
|\\PhysicalDisk(\_Total)\\Longueur moyenne Longueur de file d'attente de disque|	Nombre|
|\\PhysicalDisk(\_Total)\\Longueur moyenne de file d’attente lecture disque|	Nombre|
|\\PhysicalDisk(\_Total)\\Longueur moyenne de file d’attente écriture disque |Nombre|
|\\LogicalDisk(\_Total)\\% d’espace libre|	Pourcentage|
|\\LogicalDisk(\_Total)\\Mégaoctets libres|	Nombre|



### Calculer les métriques pour une machine virtuelle Linux v2 en tant que système d’exploitation invité

Lorsque vous créez une machine virtuelle v2 dans Azure, les diagnostics sont activés par défaut grâce à l’extension Diagnostics.

Vous pouvez utiliser la commande suivante dans PowerShell pour générer une liste des métriques.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

 Vous pouvez créer une alerte pour les métriques suivantes.

|Nom de métrique|	Unité|
|---|---|
|\\Mémoire\\Mémoire disponible |Octets|
|\\Mémoire\\Pourcentage de mémoire disponible|	Pourcentage|
|\\Mémoire\\Mémoire utilisée|	Octets|
|\\Mémoire\\Pourcentage de mémoire utilisée|	Pourcentage|
|\\Mémoire\\Pourcentage de mémoire utilisée par le cache |Pourcentage|
|\\Mémoire\\Pages par seconde|	Nombre par seconde|
|\\Mémoire\\Pages lues par seconde|	Nombre par seconde|
|\\Mémoire\\Pages écrites par seconde |Nombre par seconde|
|\\Mémoire\\Échanges disponibles |Octets|
|\\Mémoire\\Pourcentage d’échanges disponibles|	Pourcentage|
|\\Mémoire\\Échanges utilisés |Octets|
|\\Mémoire\\Pourcentage d’échanges utilisés|	Pourcentage|
|\\Processeur\\Pourcentage de temps d’inactivité|	Pourcentage|
|\\Processeur\\Pourcentage de temps d’utilisateur|	Pourcentage|
|\\Processeur\\Pourcentage de temps d’efficacité |Pourcentage|
|\\Processeur\\Pourcentage de temps privilégié |Pourcentage|
|\\Processeur\\Pourcentage de temps d’interruption|	Pourcentage|
|\\Processeur\\Pourcentage de temps d’appels de procédure différés (DPC)|	Pourcentage|
|\\Processeur\\Pourcentage de temps de processeur |Pourcentage|
|\\Processeur\\Pourcentage de temps d’attente |Pourcentage|
|\\Disque physique\\Octets par seconde|	Octets par seconde|
|\\Disque physique\\Octets lus par seconde|	Octets par seconde|
|\\Disque physique\\Octets écrits par seconde|	Octets par seconde|
|\\Disque physique\\Transferts par seconde |Nombre par seconde|
|\\Disque physique\\Lectures par seconde |Nombre par seconde|
|\\Disque physique\\Écritures par seconde |Nombre par seconde|
|\\Disque physique\\Temps de lecture moyen|	Secondes|
|\\Disque physique\\Temps d’écriture moyen |Secondes|
|\\Disque physique\\Temps de transfert moyen|	Secondes|
|\\Disque physique\\Longueur moyenne de la file d’attente du disque|	Nombre|
|\\Interface réseau\\Octets transmis |Octets|
|\\Interface réseau\\Octets reçus |Octets|
|\\Interface réseau\\Paquets transmis |Nombre|
|\\Interface réseau\\Paquets reçus |Nombre|
|\\Interface réseau\\Total des octets |Octets|
|\\Interface réseau\\Total des erreurs Rx|	Nombre|
|\\Interface réseau\\Total des erreurs Tx|	Nombre|
|\\Interface réseau\\Total des collisions|	Nombre|




## Métriques web couramment utilisées (batterie de serveurs)

Vous pouvez également effectuer la mise à l’échelle en fonction des métriques de serveur web courantes, telles que la longueur de file d’attente HTTP. Son nom de métrique est **Longueur de file d’attente HTTP**. La section suivante répertorie les métriques de batterie de serveurs (Web Apps) disponibles.

### Métriques Web Apps

Vous pouvez utiliser la commande suivante dans PowerShell pour générer une liste des métriques Web Apps.

```
Get-AzureRmMetricDefinition -ResourceId <resource_id> | Format-Table -Property Name,Unit
```

Ces métriques permettent d’émettre une alerte ou de procéder à un mise à l’échelle.

|Nom de métrique|	Unité|
|---|---|
|Pourcentage UC|	Pourcentage|
|Pourcentage mémoire |Pourcentage|
|Longueur de file d’attente du disque|	Nombre|
|Longueur de file d’attente HTTP|	Nombre|
|Octets reçus|	Octets|
|Octets envoyés|	Octets|


## Métriques couramment utilisées dans Azure Storage
Vous pouvez procéder à une mise à l’échelle en fonction de la métrique Longueur de file d’attente, qui correspond au nombre de messages dans la file d’attente de stockage. La métrique Longueur de file d’attente de stockage est une métrique spéciale et le seuil appliqué sera le nombre de messages par instance. Cela veut dire que si vous avez deux instances et que le seuil est défini sur 100, la mise à l’échelle aura lieu lorsque la file d’attente contiendra 200 messages. Chaque instance recevra 100 messages.

Vous pouvez configurer cela dans le panneau **Paramètres** du Portail Azure. Pour les jeux de mise à l’échelle de machine virtuelle, vous pouvez mettre à jour le paramètre Mise à l’échelle automatique dans le modèle ARM afin d’utiliser *metricName* avec la valeur *ApproximateMessageCount*, puis transmettre l’ID de la file d’attente de stockage avec la valeur *metricResourceUri*.


```
"metricName": "ApproximateMessageCount",
 "metricNamespace": "",
 "metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ClassicStorage/storageAccounts/mystorage/services/queue/queues/mystoragequeue"
 ```

## Métriques Service Bus généralement utilisées

Vous pouvez procéder à une mise à l’échelle en fonction de la longueur de la file d’attente Service Bus, autrement dit en fonction du nombre de messages présents dans cette file d’attente. La longueur de la file d’attente Service Bus est une métrique particulière. Le seuil appliqué correspond au nombre de messages par instance. Cela veut dire que si vous avez deux instances et que le seuil est défini sur 100, la mise à l’échelle aura lieu lorsque la file d’attente contiendra 200 messages. Chaque instance recevra 100 messages.

Pour les jeux de mise à l’échelle de machine virtuelle, vous pouvez mettre à jour le paramètre Mise à l’échelle automatique dans le modèle ARM afin d’utiliser *metricName* avec la valeur *ApproximateMessageCount*, puis transmettre l’ID de la file d’attente de stockage avec la valeur *metricResourceUri*.

```
"metricName": "MessageCount",
 "metricNamespace": "",
"metricResourceUri": "/subscriptions/s1/resourceGroups/rg1/providers/Microsoft.ServiceBus/namespaces/mySB/queues/myqueue"
```

>[AZURE.NOTE] Le concept de groupe de ressources n’existe pas pour Service Bus, mais Azure Resource Manager crée un groupe de ressources par défaut par région. Le groupe de ressources est généralement affiché au format « [Région] Service Bus par défaut ». Par exemple, « Est des États-Unis Service Bus par défaut », « Ouest des États-Unis Service Bus par défaut », « Est de l’Australie Service Bus par défaut », etc.

<!---HONumber=AcomDC_0803_2016-->