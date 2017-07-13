---
title: "Quotas et limites de l’abonnement Azure | Microsoft Docs"
description: "Fournit une liste des abonnements Azure et des limites, quotas et contraintes de service habituels. Cela inclut des informations sur la façon d’augmenter les limites ainsi que les valeurs maximales."
services: 
documentationcenter: 
author: rothja
manager: jeffreyg
editor: 
tags: billing
ms.assetid: 60d848f9-ff26-496e-a5ec-ccf92ad7d125
ms.service: billing
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/27/2017
ms.author: byvinyal
ms.translationtype: Human Translation
ms.sourcegitcommit: 857267f46f6a2d545fc402ebf3a12f21c62ecd21
ms.openlocfilehash: 13f968b5eeaeb74c6364b9b7c29651657f73e052
ms.contentlocale: fr-fr
ms.lasthandoff: 06/28/2017


---
<a id="azure-subscription-and-service-limits-quotas-and-constraints" class="xliff"></a>

# Abonnement Azure et limites, quotas et contraintes de service
Ce document répertorie quelques-unes des limites de Microsoft Azure les plus courantes, parfois appelées des quotas. Ce document ne couvre pas actuellement tous les services Azure. Ces limites seront étendues et mises à jour dans des prochaines versions pour couvrir une plus grande partie de la plateforme.

Pour en savoir plus sur la tarification Azure, consultez [Tarification Azure](https://azure.microsoft.com/pricing/) . Vous pouvez y estimer vos coûts à l’aide de la [Calculatrice de tarification](https://azure.microsoft.com/pricing/calculator/) ou en consultant la page de détails sur la tarification d’un service (par exemple, les [machines virtuelles Windows](https://azure.microsoft.com/pricing/details/virtual-machines/#Windows)). Pour des conseils sur la gestion des coûts, consultez [Éviter les coûts inattendus avec la gestion de la facturation et des coûts dans Azure](billing/billing-getting-started.md).

> [!NOTE]
> Si vous souhaitez élever la limite ou le quota au-dessus de la **Limite par défaut**, [ouvrez gratuitement un incident auprès du service client](azure-supportability/resource-manager-core-quotas-request.md). Les limites ne peuvent pas être supérieures à la valeur **Limite maximale** indiquée dans les tableaux suivants. Si aucune colonne **Limite maximale** n'est présente, la ressource spécifiée ne possède pas de limites ajustables. 
> 
> Les abonnements d’essai gratuit ne permettent pas de bénéficier d’augmentations de la limite ou du quota. Si vous disposez d’un abonnement d’essai gratuit, vous pouvez le mettre à niveau vers un abonnement avec [paiement à l’utilisation](https://azure.microsoft.com/offers/ms-azr-0003p/) . Pour en savoir plus, référez-vous à la rubrique [Mise à niveau de la version d’évaluation gratuite d’Azure vers le paiement à l’utilisation](billing/billing-upgrade-azure-subscription.md).
> 

<a id="limits-and-the-azure-resource-manager" class="xliff"></a>

## Limites et Azure Resource Manager
Il est désormais possible de combiner plusieurs ressources Azure en un seul groupe de ressources Azure. Lorsque vous utilisez des groupes de ressources, les limites qui étaient auparavant mondiales sont désormais gérées au niveau régional avec Azure Resource Manager. Pour plus d’informations sur les groupes de ressources, consultez la [Vue d’ensemble d’Azure Resource Manager](azure-resource-manager/resource-group-overview.md).

Dans les limites ci-dessous, un nouveau tableau a été ajouté pour tenir compte des différences de limites lorsque vous utilisez Azure Resource Manager. Par exemple, vous disposez d’une table **Limites d’abonnement** et d’une table **Limites d’abonnement – Azure Resource Manager**. Lorsqu'une limite s'applique aux deux scénarios, elle apparaît uniquement dans le premier tableau. Sauf indication contraire, les limites sont globales dans toutes les régions.

> [!NOTE]
> Il est important de souligner que les quotas de ressources dans les groupes de ressources Azure sont accessibles par région par le biais de votre abonnement et non par abonnement, comme les quotas de gestion des services. Nous allons utiliser des quotas de base à titre d'exemple. Si vous avez besoin de demander une augmentation du quota avec la prise en charge de cœurs, vous devez choisir le nombre de noyaux souhaité et les régions concernées, et effectuer une demande spécifique de quotas de base de groupes de ressources Azure pour les volumes et régions choisis. Par conséquent, si vous avez besoin de 30 cœurs en Europe de l'Ouest pour exécuter votre application, vous devez demander spécifiquement 30 cœurs en Europe de l'Ouest. Néanmoins, il n’y aura pas d’augmentation du quota de base dans les autres régions. Seule la région Europe de l'Ouest disposera du quota de 30 cœurs.
> <!-- -->
> Par conséquent, il peut s’avérer utile de décider de vos quotas de groupes de ressources Azure pour votre charge de travail dans une région, puis de demander ce volume dans chaque région dans laquelle vous envisagez d’effectuer un déploiement. Pour vous aider à découvrir vos quotas actuels pour des régions spécifiques, consultez la page [Dépannage de problèmes de déploiement](resource-manager-common-deployment-errors.md)
> 
> 

<a id="service-specific-limits" class="xliff"></a>

## Limites de service spécifique
* [Active Directory](#active-directory-limits)
* [Gestion des API](#api-management-limits)
* [App Service](#app-service-limits)
* [Application Gateway](#application-gateway-limits)
* [Application Insights](#application-insights-limits)
* [Automation](#automation-limits)
* [Cache Redis Azure](#azure-redis-cache-limits)
* [Azure RemoteApp](#azure-remoteapp-limits)
* [Sauvegarde](#backup-limits)
* [Batch](#batch-limits)
* [BizTalk Services](#biztalk-services-limits)
* [CDN](#cdn-limits)
* [Cloud Services](#cloud-services-limits)
* [Data Factory](#data-factory-limits)
* [Data Lake Analytics](#data-lake-analytics-limits)
* [Data Lake Store](#data-lake-store-limits)
* [DNS](#dns-limits)
* [Base de données de documents](#documentdb-limits)
* [Concentrateurs d'événements](#event-hubs-limits)
* [IoT Hub](#iot-hub-limits)
* [Key Vault](#key-vault-limits)
* [Log Analytics / Operational Insights](#log-analytics-limits)
* [Media Services](#media-services-limits)
* [Mobile Engagement](#mobile-engagement-limits)
* [Mobile Services](#mobile-services-limits)
* [Surveiller](#monitor-limits)
* [Azure Multi-Factor Authentication](#multi-factor-authentication)
* [Mise en réseau](#networking-limits)
* [Network Watcher](#network-watcher-limits)
* [Service de hub de notification](#notification-hub-service-limits)
* [Groupe de ressources](#resource-group-limits)
* [Scheduler](#scheduler-limits)
* [action](#search-limits)
* [Service Bus](#service-bus-limits)
* [Site Recovery](#site-recovery-limits)
* [Base de données SQL](#sql-database-limits)
* [Stockage](#storage-limits)
* [Système StorSimple](#storsimple-system-limits)
* [Stream Analytics](#stream-analytics-limits)
* [Abonnement](#subscription-limits)
* [Traffic Manager](#traffic-manager-limits)
* [Machines virtuelles](#virtual-machines-limits)
* [Jeux de mise à l’échelle de machine virtuelle](#virtual-machine-scale-sets-limits)

<a id="subscription-limits" class="xliff"></a>

### Limites d’abonnement
<a id="subscription-limits" class="xliff"></a>

#### Limites d’abonnement
[!INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

<a id="subscription-limits---azure-resource-manager" class="xliff"></a>

#### Limites d’abonnement – Azure Resource Manager
Les limites suivantes s'appliquent lorsque vous utilisez Azure Resource Manager et les groupes de ressources Azure. Les limites qui restent identiques avec Azure Resource Manager ne sont pas répertoriées ci-dessous. Reportez-vous au tableau précédent pour les consulter.

Pour plus d’informations sur la gestion des limites sur les demandes Resource Manager, consultez [Limitation des requêtes Resource Manager](resource-manager-request-limits.md).

[!INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]

<a id="resource-group-limits" class="xliff"></a>

### Limites de groupe de ressources
[!INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]

<a id="virtual-machines-limits" class="xliff"></a>

### Limites de machines virtuelles
<a id="virtual-machine-limits" class="xliff"></a>

#### Limites de machine virtuelle
[!INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]

<a id="virtual-machines-limits---azure-resource-manager" class="xliff"></a>

#### Limites de machines virtuelles - Azure Resource Manager
Les limites suivantes s'appliquent lorsque vous utilisez Azure Resource Manager et les groupes de ressources Azure. Les limites qui restent identiques avec Azure Resource Manager ne sont pas répertoriées ci-dessous. Reportez-vous au tableau précédent pour les consulter.

[!INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]

<a id="virtual-machine-scale-sets-limits" class="xliff"></a>

### Limites des jeux de mise à l’échelle de machine virtuelle
[!INCLUDE [virtual-machine-scale-sets-limits](../includes/azure-virtual-machine-scale-sets-limits.md)]

<a id="networking-limits" class="xliff"></a>

### Limites de mise en réseau
[!INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

<a id="networking-limits" class="xliff"></a>

#### Limites de mise en réseau
[!INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

<a id="application-gateway-limits" class="xliff"></a>

#### Limites d’Application Gateway
[!INCLUDE [application-gateway-limits](../includes/application-gateway-limits.md)]

<a id="network-watcher-limits" class="xliff"></a>

#### Limites de Network Watcher
[!INCLUDE [network-watcher-limits](../includes/network-watcher-limits.md)]

<a id="traffic-manager-limits" class="xliff"></a>

#### Limites de Traffic Manager
[!INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

<a id="dns-limits" class="xliff"></a>

#### Limites de DNS
[!INCLUDE [dns-limits](../includes/dns-limits.md)]

<a id="storage-limits" class="xliff"></a>

### Limites de stockage
Pour plus d'informations sur les limites des comptes de stockage, consultez [Objectifs de performance et d’extensibilité Azure Storage](storage/storage-scalability-targets.md).
<!--like # storage accts --> 
<a id="storage-service-limits" class="xliff"></a>

#### Limites de service de stockage
[!INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

<!-- conceptual info about disk limits -- applies to unmanaged and managed -->
<a id="virtual-machine-disk-limits" class="xliff"></a>

#### Limites du nombre de disques de machine virtuelle 
[!INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Pour plus d'informations, consultez [Tailles des machines virtuelles](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json) .

<a id="managed-virtual-machine-disks" class="xliff"></a>

#### Disques de machines virtuelles gérées

[!INCLUDE [azure-storage-limits-vm-disks-managed](../includes/azure-storage-limits-vm-disks-managed.md)]

<a id="unmanaged-virtual-machine-disks" class="xliff"></a>

#### Disques de machines virtuelles non gérées

[!INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

[!INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

<a id="storage-resource-provider-limits" class="xliff"></a>

#### Limites de fournisseur de ressources de stockage
[!INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]

<a id="cloud-services-limits" class="xliff"></a>

### Limites de services cloud
[!INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]

<a id="app-service-limits" class="xliff"></a>

### Limites App Service
Les limites App Service suivantes incluent des limites pour les applications Web, les applications mobiles, les applications d'API et les applications logiques.

[!INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

<a id="scheduler-limits" class="xliff"></a>

### Limites de planificateur
[!INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

<a id="batch-limits" class="xliff"></a>

### Limites Azure Batch
[!INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

<a id="biztalk-services-limits" class="xliff"></a>

### Limites de BizTalk Services
Le tableau suivant indique les limites d’Azure BizTalk Services.

[!INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]

<a id="documentdb-limits" class="xliff"></a>

### Limites de DocumentDB
DocumentDB est une base de données à l’échelle mondiale dans laquelle le débit et le stockage peuvent être mis à l’échelle en pour gérer ce que votre application requiert. Si vous avez des questions sur l’échelle fournie par DocumentDB, envoyez un e-mail à askdocdb@microsoft.com.

<a id="mobile-engagement-limits" class="xliff"></a>

### Limites Mobile Engagement
[!INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]

<a id="search-limits" class="xliff"></a>

### Limites Azure Search
Les niveaux tarifaires déterminent la capacité et les limites de votre service de recherche. Les niveaux sont les suivants :

* *Gratuit* : service mutualisé, partagé avec d’autres abonnés Azure, destiné à des projets d’évaluation et de développement de petite taille.
* *De base* : fournit des ressources de calcul dédiées pour des charges de travail de production à plus petite échelle, avec jusqu’à trois réplicas pour les charges de travail de requête hautement disponible.
* *Standard (S1, S2, S3, S3 haute densité)* est approprié pour des charges de travail de production de plus grande taille. Plusieurs niveaux existent dans la couche standard pour vous permettre de choisir une configuration de ressources parfaitement adaptée à votre profil de charge de travail.

**Limites par abonnement**

[!INCLUDE [azure-search-limits-per-subscription](../includes/azure-search-limits-per-subscription.md)]

**Limites par service de recherche**

[!INCLUDE [azure-search-limits-per-service](../includes/azure-search-limits-per-service.md)]

Pour en savoir plus sur les limites à un niveau plus granulaire, notamment la taille du document, les requêtes par seconde, les clés, les requêtes et les réponses, consultez [Limites de service d’Azure Search](search/search-limits-quotas-capacity.md).

<a id="media-services-limits" class="xliff"></a>

### Limites de Media Services
[!INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

<a id="cdn-limits" class="xliff"></a>

### Limites de CDN
[!INCLUDE [cdn-limits](../includes/cdn-limits.md)]

<a id="mobile-services-limits" class="xliff"></a>

### Limites Mobile Services
[!INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

<a id="monitor-limits" class="xliff"></a>

### Limites de Monitor
[!INCLUDE [monitoring-limits](../includes/monitoring-limits.md)]

<a id="notification-hub-service-limits" class="xliff"></a>

### Limites du service Notification Hub
[!INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]

<a id="event-hubs-limits" class="xliff"></a>

### Limites de concentrateurs d’événements
[!INCLUDE [azure-servicebus-limits](../includes/event-hubs-limits.md)]

<a id="service-bus-limits" class="xliff"></a>

### Limites de Service Bus
[!INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

<a id="iot-hub-limits" class="xliff"></a>

### Limites de hub IoT (IoT Hub)
[!INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

<a id="data-factory-limits" class="xliff"></a>

### Limites de Data Factory
[!INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

<a id="data-lake-analytics-limits" class="xliff"></a>

### Limites Data Lake Analytics
[!INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

<a id="data-lake-store-limits" class="xliff"></a>

### Limite Data Lake Store
[!INCLUDE [azure-data-lake-store-limits](../includes/azure-data-lake-store-limits.md)]

<a id="stream-analytics-limits" class="xliff"></a>

### Limites Stream Analytics
[!INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

<a id="active-directory-limits" class="xliff"></a>

### Limites d'Active Directory
[!INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]

<a id="azure-remoteapp-limits" class="xliff"></a>

### Limites Azure RemoteApp
[!INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

<a id="storsimple-system-limits" class="xliff"></a>

### Limites du système StorSimple
[!INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]

<a id="log-analytics-limits" class="xliff"></a>

### Limites de Log Analytics
[!INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

<a id="backup-limits" class="xliff"></a>

### Limites Azure Backup
[!INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

<a id="site-recovery-limits" class="xliff"></a>

### Limites Azure Site Recovery
[!INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

<a id="application-insights-limits" class="xliff"></a>

### Limites d’Application Insights
[!INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

<a id="api-management-limits" class="xliff"></a>

### Limites Gestion des API
[!INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

<a id="azure-redis-cache-limits" class="xliff"></a>

### Limite du service Cache Redis Azure
[!INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

<a id="key-vault-limits" class="xliff"></a>

### Limites du coffre de clés
[!INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

<a id="multi-factor-authentication" class="xliff"></a>

### Azure Multi-Factor Authentication
[!INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

<a id="automation-limits" class="xliff"></a>

### Limites du service Automation
[!INCLUDE [automation-limits](../includes/azure-automation-service-limits.md)]

<a id="sql-database-limits" class="xliff"></a>

### Limites de base de données SQL
Pour connaître les limites de la base de données SQL, consultez [Limites de ressources de base de données SQL](sql-database/sql-database-resource-limits.md).

<a id="see-also" class="xliff"></a>

## Voir aussi
[Présentation des limites et des augmentations Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Tailles de machines virtuelles et services cloud pour Microsoft Azure](virtual-machines/linux/sizes.md?toc=%2fazure%2fvirtual-machines%2flinux%2ftoc.json)

[Tailles de services cloud](cloud-services/cloud-services-sizes-specs.md)


