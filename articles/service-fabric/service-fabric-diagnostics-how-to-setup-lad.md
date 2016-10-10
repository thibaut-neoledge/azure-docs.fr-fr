<properties
   pageTitle="Collecte des journaux avec Linux Azure Diagnostics | Microsoft Azure"
   description="Cet article décrit la procédure de configuration d’Azure Diagnostics pour la collecte de journaux d’un cluster Linux Service Fabric exécuté dans Azure."
   services="service-fabric"
   documentationCenter=".net"
   authors="mani-ramaswamy"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotNet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="09/28/2016"
   ms.author="subramar"/>


# Collecte des journaux avec Azure Diagnostics

> [AZURE.SELECTOR]
- [Windows](service-fabric-diagnostics-how-to-setup-wad.md)
- [Linux](service-fabric-diagnostics-how-to-setup-lad.md)

Lorsque vous exécutez un cluster Service Fabric dans Azure, il peut être intéressant de collecter les journaux de tous les nœuds pour les regrouper dans un emplacement central. La centralisation des journaux facilite l’analyse et la résolution des problèmes concernant vos services, votre application, voire le cluster lui-même. L’une des façons de charger et collecter les journaux consiste à utiliser l’extension Azure Diagnostics, qui charge les journaux dans Azure Storage. Vous pouvez lire les événements à partir du stockage et les placer dans un produit tel que [Elasticsearch](service-fabric-diagnostic-how-to-use-elasticsearch.md) ou une autre solution d’analyse de journaux.

## Les différentes sources de journaux que vous pourriez souhaiter collecter
1. **Journaux Service Fabric :** ils sont émis par la plateforme via LTTng et chargés dans votre compte de stockage. Les journaux peuvent porter sur les événements opérationnels ou liés au runtime émis par la plate-forme. Ces journaux sont stockés à l’emplacement désigné dans votre manifeste de cluster (recherchez la balise « AzureTableWinFabETWQueryable », puis l’élément « StoreConnectionString » pour obtenir les informations du compte de stockage).
2. **Événements de l’application :** événements émis à partir de votre code de services. Vous pouvez utiliser une solution de journalisation qui écrit les fichiers journaux textuels, par exemple [LTTng](http://lttng.org). Consultez la documentation relative à LTTng sur le traçage de votre application.


## Déployer les extensions de diagnostic
La première étape de la collecte de journaux consiste à déployer l’extension Diagnostics sur chaque machine virtuelle du cluster Service Fabric. L’extension Diagnostics collecte les journaux sur chaque machine virtuelle et les charge sur le compte de stockage que vous spécifiez. Les étapes varient selon que vous utilisez le portail Azure ou Azure Resource Manager.

### Déploiement de l’extension Diagnostics dans le cadre de la création d’un cluster 
Pour déployer l’extension Diagnostics sur les machines virtuelles du cluster dans le cadre de la création d’un cluster, définissez « Diagnostics » sur **Activé**. Une fois le cluster créé, ce paramètre ne peut pas être modifié à l’aide du portail.

Configurez Linux Azure Diagnostics pour collecter les fichiers et les placer sur le compte de stockage des clients. Ce processus est détaillé dans le scénario 3 de l’article [Utilisation de l’extension de diagnostic Linux pour analyser les données de performances et de diagnostic d’une machine virtuelle Linux](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md), intitulé « Charger vos propres fichiers journaux ». En suivant cette procédure, vous pouvez accéder aux traces, qui peuvent être chargés sur le visualiseur de votre choix.


Vous pouvez également déployer l’extension Diagnostics à l’aide d’Azure Resource Manager. Ce processus, identique pour Windows et Linux, est documenté pour les clusters Windows, dans la section [Collecte des journaux avec Linux Azure Diagnostics](service-fabric-diagnostics-how-to-setup-wad.md).

Vous pouvez également utiliser OMS, comme indiqué dans la section [OMS log analytics with linux (Analyse des journaux OMS avec Linux)](https://blogs.technet.microsoft.com/hybridcloud/2016/01/28/operations-management-suite-log-analytics-with-linux/).

Une fois cette configuration terminée, l’agent Linux Azure Diagnostics analyse les fichiers journaux spécifiés. Chaque fois qu’une nouvelle ligne est ajoutée au fichier, elle crée une entrée dans le journal syslog, qui est envoyée au stockage spécifié par le client.


## Étapes suivantes
Consultez la [documentation relative à LTTng](http://lttng.org/docs) et la section [Utilisation de l’extension de diagnostic Linux pour analyser les données de performances et de diagnostic d’une machine virtuelle Linux](../virtual-machines/virtual-machines-linux-classic-diagnostic-extension.md) pour identifier plus précisément les événements que vous devez consulter lors de la résolution des problèmes.

<!---HONumber=AcomDC_0928_2016-->