<properties
	pageTitle="Limites, quotas et contraintes des abonnements et services Microsoft Azure"
	description="Fournit une liste des limites, quotas et contraintes des abonnements et services Azure. Cela inclut des informations sur la façon d’augmenter les limites ainsi que les valeurs maximales."
	services=""
	documentationCenter=""
	authors="rothja"
	manager="jeffreyg"
	editor="monicar"/>

<tags
	ms.service="multiple"
	ms.workload="multiple"
	ms.tgt_pltfrm="na"
	ms.devlang="na"
	ms.topic="article"
	ms.date="04/19/2016"
	ms.author="jroth"/>

# Abonnement Azure et limites, quotas et contraintes du service

## Vue d’ensemble

Ce document présente quelques-unes des limites Microsoft Azure les plus communes. Notez que ce document ne couvre pas tous les services Azure. Dans le temps, ces limites seront étendues et mises à jour pour couvrir une plus grande partie de la plateforme.

> [AZURE.NOTE] Si vous souhaitez élever la limite au-dessus de la **Limite par défaut**, vous pouvez [ouvrir gratuitement un incident auprès du service client](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). Les limites ne peuvent pas être supérieures à la valeur **Limite maximale** dans les tableaux ci-dessous. Si aucune colonne **Limite maximale** n'est présente, la ressource spécifiée ne possède pas de limites ajustables.

## Limites et Azure Resource Manager

Il est désormais possible de combiner plusieurs ressources Azure en un seul groupe de ressources Azure. Lorsque vous utilisez des groupes de ressources, les limites qui étaient auparavant mondiales sont désormais gérées au niveau régional avec Azure Resource Manager. Pour plus d’informations sur les groupes de ressources Azure, consultez la rubrique [Utilisation des groupes de ressources pour gérer vos ressources Azure](azure-portal/resource-group-portal.md).

Dans les limites ci-dessous, un nouveau tableau a été ajouté pour tenir compte des différences de limites lorsque vous utilisez Azure Resource Manager. Par exemple, vous disposez d’une table **Limites d'abonnement** et d’une table **Limites d'abonnement - Azure Resource Manager**. Lorsqu'une limite s'applique aux deux scénarios, elle apparaît uniquement dans le premier tableau. Sauf indication contraire, les limites sont globales dans toutes les régions.

> [AZURE.NOTE] Il est important de souligner que les quotas de ressources dans les groupes de ressources Azure sont accessibles par votre abonnement par région, et non par abonnement, comme les quotas de gestion des services. Nous allons utiliser des quotas de base à titre d'exemple. Si vous avez besoin de demander une augmentation du quota avec la prise en charge de cœurs, vous devez choisir le nombre de noyaux souhaité et les régions concernées, et effectuer une demande spécifique de quotas de base de groupes de ressources Azure pour les volumes et régions choisis. Par conséquent, si vous avez besoin de 30 cœurs en Europe de l'Ouest pour exécuter votre application, vous devez demander spécifiquement 30 cœurs en Europe de l'Ouest. Néanmoins, il n’y aura pas d’augmentation du quota de base dans les autres régions. Seule la région Europe de l'Ouest disposera du quota de 30 cœurs.
<!-- -->
Par conséquent, il peut s’avérer utile de décider de vos quotas de groupes de ressources Azure pour votre charge de travail dans une région, puis de demander ce volume dans chaque région dans laquelle vous envisagez d’effectuer un déploiement. Pour vous aider à découvrir vos quotas actuels pour des régions spécifiques, consultez la page [Dépannage de problèmes de déploiement](./resource-manager-common-deployment-errors.md)


## Limites de service spécifique

- [Active Directory](#active-directory-limits)
- [Gestion des API](#api-management-limits)
- [App Service](#app-service-limits)
- [Application Insights](#application-insights-limits)
- [Cache Redis Azure](#azure-redis-cache-limits)
- [Azure RemoteApp](#azure-remoteapp-limits)
- [Sauvegarde](#backup-limits)
- [Batch](#batch-limits)
- [BizTalk Services](#biztalk-services-limits)
- [CDN](#cdn-limits)
- [Cloud Services](#cloud-services-limits)
- [Data Factory](#data-factory-limits)
- [Data Lake Analytics](#data-lake-analytics-limits)
- [DNS](#dns-limits)
- [Base de données de documents](#documentdb-limits)
- [IoT Hub](#iot-hub-limits)
- [Key Vault](#key-vault-limits)
- [Media Services](#media-services-limits)
- [Mobile Engagement](#mobile-engagement-limits)
- [Mobile Services](#mobile-services-limits)
- [Azure Multi-Factor Authentication](#multi-factor-authentication)
- [Mise en réseau](#networking-limits)
- [Service de hub de notification](#notification-hub-service-limits)
- [Operational Insights](#operational-insights-limits)
- [Groupe de ressources](#resource-group-limits)
- [Scheduler](#scheduler-limits)
- [action](#search-limits)
- [Service Bus](#service-bus-limits)
- [Site Recovery](#site-recovery-limits)
- [Base de données SQL](#sql-database-limits)
- [Stockage](#storage-limits)
- [Système StorSimple](#storsimple-system-limits)
- [Stream Analytics](#stream-analytics-limits)
- [Abonnement](#subscription-limits)
- [Traffic Manager](#traffic-manager-limits)
- [Virtual Machines](#virtual-machines-limits)


### Limites d'abonnement
#### Limites d'abonnement
[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

#### Limites d'abonnement - Azure Resource Manager

Les limites suivantes s'appliquent lorsque vous utilisez Azure Resource Manager et les groupes de ressources Azure. Les limites qui restent identiques avec Azure Resource Manager ne sont pas répertoriées ci-dessous. Reportez-vous au tableau précédent pour les consulter.

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


### Limites de groupe de ressources

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]


### Limites de machines virtuelles
#### Limites de machine virtuelle
[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


#### Limites de machines virtuelles - Azure Resource Manager

Les limites suivantes s'appliquent lorsque vous utilisez Azure Resource Manager et les groupes de ressources Azure. Les limites qui restent identiques avec Azure Resource Manager ne sont pas répertoriées ci-dessous. Reportez-vous au tableau précédent pour les consulter.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]


### Limites de mise en réseau

[AZURE.INCLUDE [expressroute-limits](../includes/expressroute-limits.md)]

#### Limites de mise en réseau
[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

#### Limites de Traffic Manager

[AZURE.INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

#### Limites de DNS

[AZURE.INCLUDE [dns-limits](../includes/dns-limits.md)]

### Limites de stockage

Pour plus d'informations sur les limites des comptes de stockage, consultez [Objectifs de performance et d’extensibilité Azure Storage](../articles/storage/storage-scalability-targets.md).

#### Limites de service de stockage

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

#### Limites du nombre de disques de machine virtuelle

[AZURE.INCLUDE [azure-storage-limits-vm-disks](../includes/azure-storage-limits-vm-disks.md)]

Pour plus d'informations, consultez [Tailles des machines virtuelles](../articles/virtual-machines/virtual-machines-linux-sizes.md).

**Comptes de stockage standard**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-standard](../includes/azure-storage-limits-vm-disks-standard.md)]

**Comptes de stockage Premium**

[AZURE.INCLUDE [azure-storage-limits-vm-disks-premium](../includes/azure-storage-limits-vm-disks-premium.md)]

#### Limites de fournisseur de ressources de stockage

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


### Limites de services cloud

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


### Limites App Service
Les limites App Service suivantes incluent des limites pour les applications Web, les applications mobiles, les applications d'API et les applications logiques.

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

### Limites de planificateur

[AZURE.INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

### Limites Azure Batch

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]

###Limites de BizTalk Services
Le tableau suivant indique les limites d’Azure BizTalk Services.

[AZURE.INCLUDE [biztalk-services-service-limits](../includes/biztalk-services-service-limits.md)]


### Limites de DocumentDB

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]


### Limites Mobile Engagement

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


### Limites Azure Search

Les niveaux tarifaires déterminent la capacité et les limites de votre service de recherche. Les niveaux sont les suivants :

- **Gratuit** : service mutualisé, partagé avec d'autres abonnés Azure, destiné à des projets d'évaluation et de développement de petite taille.
- Le niveau **De base (version préliminaire)** fournit des ressources informatiques dédiées aux charges de production à petite échelle. Ce niveau est actuellement en version préliminaire et bénéficie d'un tarif réduit.
- **Standard (S1 et S2)** : pour les charges de production. Une version à capacité plus importante (**S2**) est disponible sur demande (envoyer un courrier électronique à azuresearch_contact@microsoft.com).

[AZURE.INCLUDE [azure-search-limits-all](../includes/azure-search-limits-all.md)]

Pour en savoir plus sur les autres limites, telles que la taille du document, les clés, les demandes et les réponses, voir [Limites de service dans Azure Search](search/search-limits-quotas-capacity.md).

### Limites de Media Services

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

### Limites de CDN

[AZURE.INCLUDE [cdn-limits](../includes/cdn-limits.md)]

### Limites Mobile Services

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

### Limites du service Notification Hub

[AZURE.INCLUDE [notification-hub-limits](../includes/notification-hub-limits.md)]


### Limites de Service Bus

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

### Limites de hub IoT (IoT Hub)

[AZURE.INCLUDE [azure-iothub-limits](../includes/iot-hub-limits.md)]

### Limites de Data Factory

[AZURE.INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]

### Limites Data Lake Analytics
[AZURE.INCLUDE [azure-data-lake-analytics-limits](../includes/azure-data-lake-analytics-limits.md)]

### Limites Stream Analytics

[AZURE.INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

### Limites d'Active Directory

[AZURE.INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]


### Limites Azure RemoteApp

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

### Limites du système StorSimple

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


### Limites Operational Insights

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

### Limites Azure Backup

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

### Limites Azure Site Recovery

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

### Limites d’Application Insights

[AZURE.INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

### Limites Gestion des API

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

### Limite du service Cache Redis Azure

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

### Limites du coffre de clés

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

### Azure Multi-Factor Authentication
[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

### Limites de base de données SQL

Pour connaître les limites de la base de données SQL, consultez [Limites de ressources de base de données SQL](sql-database/sql-database-resource-limits.md).

## Voir aussi

[Présentation des limites et des augmentations Azure](https://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Tailles de machines virtuelles et services cloud pour Microsoft Azure](virtual-machines/virtual-machines-linux-sizes.md)

[Tailles de services cloud](cloud-services/cloud-services-sizes-specs.md)

<!---HONumber=AcomDC_0427_2016-->