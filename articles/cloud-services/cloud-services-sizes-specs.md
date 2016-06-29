<properties
 pageTitle="Tailles de services cloud"
 description="Répertorie les différentes tailles de machines virtuelles des rôles web et de travail des services cloud Azure."
 services="cloud-services"
 documentationCenter=""
 authors="Thraka"
 manager="timlt"
 editor=""/>
<tags
 ms.service="cloud-services"
 ms.devlang="na"
 ms.topic="article"
 ms.tgt_pltfrm="na"
 ms.workload="tbd"
 ms.date="05/25/2016"
 ms.author="adegeo"/>

# Tailles de services cloud

Cette rubrique décrit les tailles et options disponibles pour les instances de rôle de Cloud Services (rôles web et rôles de travail). Il expose également les points à prendre en considération pour le déploiement quand vous planifiez l'utilisation de ces ressources.

Cloud Services est l’un des nombreux types de ressources de calcul proposés par Azure. Cliquez [ici](cloud-services-choose-me.md) pour en savoir plus sur Cloud Services.

> [AZURE.NOTE]Pour connaître les limites d'Azure associées, consultez l'article [Abonnement Azure et limites, quotas et contraintes du service](../azure-subscription-service-limits.md).

## Tailles pour les instances de rôle web et de travail

Les considérations ci-dessous peuvent vous aider à choisir une taille :

* Les instances de machines virtuelles de la série D sont conçues pour exécuter des applications qui nécessitent une puissance de calcul et des performances de disque temporaire supérieures. Ces machines virtuelles se caractérisent par des processeurs plus rapides, un rapport mémoire-cœur plus élevé et un disque SSD pour le disque temporaire. Pour plus d’informations, voir l’annonce suivante sur le blog Azure : [Nouvelles tailles de machines virtuelles de la série D](https://azure.microsoft.com/blog/2014/09/22/new-d-series-virtual-machine-sizes/) (en anglais).  

* La série Dv2, suite de la série D d’origine, comprend un processeur plus puissant. Le processeur de la série Dv2 est environ 35 % plus rapide que le processeur de la série D. Il est basé sur la dernière génération de processeur 2,4 GHz Intel Xeon® E5-2673 v3 (Haswell) et comporte la technologie 2.0 Intel Turbo Boost, et peut atteindre 3,1 GHz. La série Dv2 a les mêmes configurations de disque et de mémoire que la série D.

* Les rôles web et de travail nécessitent davantage d'espace disque temporaire qu'Azure Virtual Machines en raison de la configuration système requise. Les fichiers système réservent 4 Go d'espace pour le fichier d'échange de Windows et 2 Go d'espace pour le fichier de vidage de Windows.

* Le disque du système d'exploitation contient le système d'exploitation invité de Windows et inclut le dossier Program Files (y compris les installations effectuées via les tâches de démarrage, sauf si vous spécifiez un autre disque), les modifications du Registre, le dossier System32 et le .NET Framework.

* Le **disque de stockage temporaire** contient les journaux et les fichiers de configuration Azure, les diagnostics Microsoft Azure (qui incluent les journaux IIS) et les ressources de stockage locales que vous définissez.

* Le **disque d'application** contient votre .cspkg extrait et inclut votre site web, fichiers binaires, processus hôte de rôle, tâches de démarrage, web.config, etc.

* Les tailles de machine virtuelle A8/A10 et A9/A11 présentent les mêmes capacités. Les instances de machine virtuelle A8 et A9 intègrent une carte réseau supplémentaire qui est connectée à un réseau RDMA pour accélérer la communication entre les machines virtuelles. Les instances A8 et A9 sont conçues pour les applications de calcul hautes performances qui nécessitent une communication constante et à faible latence entre les nœuds pendant l'exécution, comme les applications qui utilisent l'interface MPI (Message Passing Interface). Les instances de machine virtuelle A10 et A11 ne sont pas équipées de cette carte réseau supplémentaire. Ces instances sont conçues pour les applications de calcul hautes performances qui n'ont pas besoin d'une communication constante et à faible latence entre les nœuds, également appelées applications paramétriques ou massivement parallèles.

    >[AZURE.NOTE] Si vous envisagez d’utiliser les tailles A8 à A11, lisez [ces](../virtual-machines/virtual-machines-windows-a8-a9-a10-a11-specs.md) informations.

>[AZURE.NOTE] Toutes les tailles de machine fournissent un **disque d'application** qui stocke tous les fichiers provenant de votre package de services cloud ; sa taille est d'environ 1,5 Go.

Veillez à examiner la [tarification](https://azure.microsoft.com/pricing/details/cloud-services/) de la taille de chaque Service Cloud.

## Usage général

Pour les sites web, les bases de données de taille petite à moyenne, et les autres applications utilisées quotidiennement.

>[AZURE.NOTE] La capacité de stockage est représentée avec 1024^3 octets comme unité de mesure pour les Go. Cette unité est parfois appelée gibioctet ou définition en base 2. Quand vous comparez des tailles qui utilisent des systèmes en base différente, n’oubliez pas que les tailles en base 2 peuvent paraître plus petites que celles en base 10, mais que pour une taille spécifique (par exemple, 1 Go), un système en base 2 offre une capacité plus élevée qu’un système en base 10, car 1 024^3 est supérieur à 1 000^3.

| Taille (ID) | Cœurs | RAM | Bande passante réseau | Taille totale du disque |
| --------------- | :-------: | ------: | :-----------: | -------: |
| Très petite | 1 | 0,75 Go | Faible | 19 Go |
| Petite | 1 | 1,75 Go | Modéré | 224 Go |
| Moyenne | 2 | 3,5 Go | Modéré | 489 Go |
| Grande | 4 | 7 Go | Élevée | 999 Go |
| Très grande | 8 | 14 Go | Élevée | 2 039 Go |

>[AZURE.NOTE] **ExtraSmall** à **ExtraLarge** peuvent également être nommés **A0-A4** respectivement.

## Utilisation intensive de la mémoire

Pour les grandes bases de données, les batteries de serveurs SharePoint et les applications à débit élevé.

| Taille (ID) | Cœurs | RAM | Bande passante réseau | Taille totale du disque |
| --------------- | :-------: | ------: | :-----------: | ------:  |
| A5 | 2 | 14 Go | Modéré | 489 Go |
| A6 | 4 | 28 Go | Élevée | 999 Go |
| A7 | 8 | 56 Go | Élevée | 2 039 Go |

## Optimisé pour le réseau avec prise en charge InfiniBand

Disponible dans certains centres de données. Les machines virtuelles A8 et A9 sont dotées de [processeurs Intel® Xeon® E5](http://www.intel.com/content/www/us/en/processors/xeon/xeon-processor-e5-family.html). Ajoute un réseau **InfiniBand** de 32 Gbit/s incluant la technologie d’accès direct à la mémoire à distance (RDMA). Parfaits pour les applications MPI (Message Passing Interface), les clusters à hauts niveaux de performances, la modélisation et les simulations, l'encodage vidéo, ainsi que les autres scénarios nécessitant un réseau important ou de nombreux calculs.

| Taille (ID) | Cœurs | RAM | Bande passante réseau | Taille totale du disque |
| --------------- | :-------: | ------: | :-----------: | ------: |
| A8 | 8 | 56 Go | Élevée | 382 Go |
| A9 | 16 | 112 Go | Très élevée | 382 Go |

## Calcul intensif

Disponible dans certains centres de données. Les machines virtuelles A10 et A11 sont dotées de [processeurs Intel® Xeon® E5](http://www.intel.com/content/www/us/en/processors/xeon/xeon-processor-e5-family.html). Pour les clusters à hauts niveaux de performances, la modélisation et les simulations, l'encodage vidéo, ainsi que les autres scénarios nécessitant un réseau important ou de nombreux calculs. Semblable à la configuration des instances A8 et A9 sans le réseau InfiniBand et la technologie RDMA.

| Taille (ID) | Cœurs | RAM | Bande passante réseau | Taille totale du disque |
| --------------- | :-------: | ------: | :-----------: | ------: |
| A10 | 8 | 56 Go | Élevée | 382 Go |
| A11 | 16 | 112 Go | Très élevée | 382 Go |

## Série D : calcul optimisé

Les machines virtuelles de la série D incluent des disques SSD et des processeurs 60 % plus rapides que ceux de la série A. Elles sont également disponibles pour les rôles web ou les rôles de travail dans Azure Cloud Services. Cette série est idéale pour les applications nécessitant des processeurs plus rapides, de meilleures performances des disques locaux ou des mémoires plus volumineuses.

## Usage général (D)

Pour les sites web, les bases de données de taille petite à moyenne, et les autres applications utilisées quotidiennement.

| Taille (ID) | Cœurs | RAM | Bande passante réseau | Taille totale du disque |
| --------------- | :-------: | ------: | :-----------: | ------: |
| D1 standard | 1 | 3,5 Go | Modéré | 50 Go |
| D2 standard | 2 | 7 Go | Élevée | 100 Go |
| D3 standard | 4 | 14 Go | Élevée | 200 Go |
| D4 standard | 8 | 28 Go | Élevée | 400 Go |

## Utilisation intensive de la mémoire (D)

Pour les grandes bases de données, les batteries de serveurs SharePoint et les applications à débit élevé.

| Taille (ID) | Cœurs | RAM | Bande passante réseau | Taille totale du disque |
| --------------- | :-------: | ------: | :-----------: | ------: |
| D11 standard | 2 | 14 Go | Élevée | 100 Go |
| D12 standard | 4 | 28 Go | Élevée | 200 Go |
| D13 standard | 8 | 56 Go | Élevée | 400 Go |
| D14 standard | 16 | 112 Go | Très élevée | 800 Go |

## Série Dv2 : calcul optimisé

Les instances de la série Dv2 sont la prochaine génération d’instances de la série D pouvant être utilisées comme machines virtuelles et services cloud. Les instances de la série Dv2 auront des processeurs plus puissants et environ 35 % plus rapides que ceux des instances de la série D. Elles disposeront d’une mémoire et de configurations de disque identiques à celles de la série D. Les instances de la série Dv2 sont basées sur la dernière génération de processeur Intel Xeon® E5-2673 v3 (Haswell) de 2,4 GHz et peuvent aller jusqu’à 3,1 GHz avec Intel Turbo Boost Technology 2.0. Les séries Dv2 et D sont idéales pour les applications nécessitant des processeurs plus rapides, de meilleures performances des disques locaux ou des mémoires plus volumineuses. Elles offrent ainsi une combinaison puissante pour de nombreuses applications d’entreprise.

## Usage général (Dv2)

Pour les sites web, les bases de données de taille petite à moyenne, et les autres applications utilisées quotidiennement.

| Taille (ID) | Cœurs | RAM | Bande passante réseau | Taille totale du disque |
| --------------- | :-------: | ------: | :-----------: | ------: |
| Standard\_D1\_v2 | 1 | 3,5 Go | Modéré | 50 Go |
| Standard\_D2\_v2 | 2 | 7 Go | Élevée | 100 Go |
| Standard\_D3\_v2 | 4 | 14 Go | Élevée | 200 Go |
| Standard\_D4\_v2 | 8 | 28 Go | Élevée | 400 Go |
| Standard\_D5\_v2 | 16 | 56 Go | Très élevée | 800 Go |

## Utilisation intensive de la mémoire (Dv2)

Pour les grandes bases de données, les batteries de serveurs SharePoint et les applications à débit élevé

| Taille (ID) | Cœurs | RAM | Bande passante réseau | Taille totale du disque |
| --------------- | :-------: | ------: | :-----------: | -------: |
| Standard\_D11\_v2 | 2 | 14 Go | Élevée | 100 Go |
| Standard\_D12\_v2 | 4 | 28 Go | Élevée | 200 Go |
| Standard\_D13\_v2 | 8 | 56 Go | Élevée | 400 Go |
| Standard\_D14\_v2 | 16 | 112 Go | Très élevée | 800 Go |

## Configurer les tailles pour les Cloud Services

Vous pouvez spécifier la taille de l’ordinateur virtuel d’une instance de rôle dans le cadre du modèle de service décrit par le [fichier de définition de service](cloud-services-model-and-package.md#csdef). La taille du rôle détermine le nombre de cœurs du processeur, la capacité de mémoire et la taille du système de fichiers local qui lui est allouée. Choisissez la taille du rôle en fonction des besoins en ressources de votre application.

Voici un exemple qui montre comment configurer un rôle avec la taille [Standard\_D2](#general-purpose-d) pour une instance de rôle web :

```xml
<WebRole name="WebRole1" vmsize="<mark>Standard_D2</mark>">
...
</WebRole>
```

<!---HONumber=AcomDC_0615_2016-->