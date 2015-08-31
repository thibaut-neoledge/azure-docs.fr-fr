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
	ms.date="08/09/2015"
	ms.author="jroth"/>

# Abonnement Azure et limites, quotas et contraintes du service

## Vue d'ensemble

Ce document présente quelques-unes des limites Microsoft Azure les plus communes. Notez que ce document ne couvre pas tous les services Azure. Dans le temps, ces limites seront étendues et mises à jour pour couvrir une plus grande partie de la plateforme.

> [AZURE.NOTE]Si vous souhaitez élever la limite au-dessus de la **Limite par défaut**, vous pouvez [ouvrir gratuitement un incident auprès du service client](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/). Les limites ne peuvent pas être supérieures à la valeur **Limite maximale** dans les tableaux ci-dessous. Si aucune colonne **Limite maximale** n'est présente, la ressource spécifiée ne possède pas de limites ajustables.

### Limites et Azure Resource Manager

Il est désormais possible de combiner plusieurs ressources Azure en un seul groupe de ressources Azure. Lorsque vous utilisez des groupes de ressources, les limites qui étaient auparavant mondiales sont désormais gérées au niveau régional avec Azure Resource Manager. Pour plus d’informations sur les groupes de ressources Azure, consultez la rubrique [Utilisation des groupes de ressources pour gérer vos ressources Azure](resource-group-portal.md).

Dans les limites ci-dessous, un nouveau tableau a été ajouté pour tenir compte des différences de limites lorsque vous utilisez Azure Resource Manager. Par exemple, vous disposez d’une table **Limites d'abonnement** et d’une table **Limites d'abonnement - Azure Resource Manager**. Lorsqu'une limite s'applique aux deux scénarios, elle apparaît uniquement dans le premier tableau. Sauf indication contraire, les limites sont globales dans toutes les régions.

> [AZURE.NOTE]Il est important de souligner que les quotas de ressources dans les groupes de ressources Azure sont accessibles par votre abonnement par région, et non par abonnement, comme les quotas de gestion des services. Nous allons utiliser des quotas de base à titre d'exemple. Si vous avez besoin de demander une augmentation du quota avec la prise en charge de cœurs, vous devez choisir le nombre de noyaux souhaité et les régions concernées, et effectuer une demande spécifique de quotas de base de groupes de ressources Azure pour les volumes et régions choisis. Par conséquent, si vous avez besoin de 30 cœurs en Europe de l'Ouest pour exécuter votre application, vous devez demander spécifiquement 30 cœurs en Europe de l'Ouest. Néanmoins, il n’y aura pas d’augmentation du quota de base dans les autres régions. Seule la région Europe de l'Ouest disposera du quota de 30 cœurs. <!-- --> Par conséquent, il peut s’avérer utile de décider de vos quotas de groupes de ressources Azure pour votre charge de travail dans une région, puis de demander ce volume dans chaque région dans laquelle vous envisagez d’effectuer un déploiement. Pour vous aider à découvrir vos quotas actuels pour des régions spécifiques, consultez la page [Dépannage de problèmes de déploiement](resource-group-deploy-debug.md##authentication-subscription-role-and-quota-issues)

## Limites d'abonnement

[AZURE.INCLUDE [azure-subscription-limits](../includes/azure-subscription-limits.md)]

### Limites d'abonnement - Azure Resource Manager

Les limites suivantes s'appliquent lorsque vous utilisez Azure Resource Manager et les groupes de ressources Azure. Les limites qui restent identiques avec Azure Resource Manager ne sont pas répertoriées ci-dessous. Reportez-vous au tableau précédent pour les consulter.

[AZURE.INCLUDE [azure-subscription-limits-azure-resource-manager](../includes/azure-subscription-limits-azure-resource-manager.md)]


## Limites de groupe de ressources

[AZURE.INCLUDE [azure-resource-groups-limits](../includes/azure-resource-groups-limits.md)]


## Limites de machines virtuelles

[AZURE.INCLUDE [azure-virtual-machines-limits](../includes/azure-virtual-machines-limits.md)]


### Limites de machines virtuelles - Azure Resource Manager

Les limites suivantes s'appliquent lorsque vous utilisez Azure Resource Manager et les groupes de ressources Azure. Les limites qui restent identiques avec Azure Resource Manager ne sont pas répertoriées ci-dessous. Reportez-vous au tableau précédent pour les consulter.

[AZURE.INCLUDE [azure-virtual-machines-limits-azure-resource-manager](../includes/azure-virtual-machines-limits-azure-resource-manager.md)]


## Limites de mise en réseau

[AZURE.INCLUDE [azure-virtual-network-limits](../includes/azure-virtual-network-limits.md)]

### Limites de Traffic Manager

[AZURE.INCLUDE [traffic-manager-limits](../includes/traffic-manager-limits.md)]

### Limites de DNS

[AZURE.INCLUDE [dns-limits](../includes/dns-limits.md)]

## Limites de stockage

### Limites de stockage standard

[AZURE.INCLUDE [azure-storage-limits](../includes/azure-storage-limits.md)]

Pour plus d’informations sur les limites des comptes de stockage, consultez [Objectifs de performance et évolutivité d’Azure Storage](../articles/storage/storage-scalability-targets.md).


### Limites Premium Storage

[AZURE.INCLUDE [azure-storage-limits-premium-storage](../includes/azure-storage-limits-premium-storage.md)]


### Limites de stockage - Azure Resource Manager

[AZURE.INCLUDE [azure-storage-limits-azure-resource-manager](../includes/azure-storage-limits-azure-resource-manager.md)]


## Limites de services cloud

[AZURE.INCLUDE [azure-cloud-services-limits](../includes/azure-cloud-services-limits.md)]


## Limites App Service : Web Apps, Mobile Apps, API Apps, Logic Apps

[AZURE.INCLUDE [azure-websites-limits](../includes/azure-websites-limits.md)]

## Limites de planificateur

[AZURE.INCLUDE [scheduler-limits-table](../includes/scheduler-limits-table.md)]

## Limites Azure Batch

[AZURE.INCLUDE [azure-batch-limits](../includes/azure-batch-limits.md)]


## Limites DocumentDB

[AZURE.INCLUDE [azure-documentdb-limits](../includes/azure-documentdb-limits.md)]


## Limites Mobile Engagement

[AZURE.INCLUDE [azure-mobile-engagement-limits](../includes/azure-mobile-engagement-limits.md)]


## Limites Azure Search

[AZURE.INCLUDE [azure-search-limits](../includes/azure-search-limits.md)]

Pour plus d’informations sur les limites d’Azure Search, consultez la page [Limites et contraintes (Azure Search)](https://msdn.microsoft.com/library/azure/dn798934.aspx).

## Limites de base de données SQL

[AZURE.INCLUDE [azure-sql-database-limits](../includes/azure-sql-database-limits.md)]

Pour plus d’informations sur les limites de Base de données SQL, consultez les rubriques suivantes :

 - [Niveaux du service Azure SQL Database (éditions)](http://msdn.microsoft.com/library/azure/dn741340.aspx)
 - [Niveaux de service et niveaux de performances de la base de données SQL Azure](http://msdn.microsoft.com/library/azure/dn741336.aspx)
 - [Quotas d'unités de débit de la base de données (UDBD)](http://msdn.microsoft.com/library/azure/ee336245.aspx#DTUs)
 - [Limites de ressources de la base de données SQL](sql-database/sql-database-resource-limits.md)

## Limites de Media Services

[AZURE.INCLUDE [azure-mediaservices-limits](../includes/azure-mediaservices-limits.md)]

## Limites de CDN

[AZURE.INCLUDE [cdn-limits](../includes/cdn-limits.md)]

## Limites Mobile Services

[AZURE.INCLUDE [mobile-services-limits](../includes/mobile-services-limits.md)]

## Limites de Service Bus

[AZURE.INCLUDE [azure-servicebus-limits](../includes/service-bus-quotas-table.md)]

## Limites de Data Factory

[AZURE.INCLUDE [azure-data-factory-limits](../includes/azure-data-factory-limits.md)]


## Limites Stream Analytics

[AZURE.INCLUDE [stream-analytics-limits-table](../includes/stream-analytics-limits-table.md)]

## Limites d'Active Directory

[AZURE.INCLUDE [AAD-service-limits](../includes/active-directory-service-limits-include.md)]


## Limites Azure RemoteApp

[AZURE.INCLUDE [azure-remoteapp-limits](../includes/azure-remoteapp-limits.md)]

## Limites du système StorSimple

[AZURE.INCLUDE [storsimple-limits-table](../includes/storsimple-limits-table.md)]


## Limites Operational Insights

[AZURE.INCLUDE [operational-insights-limits](../includes/operational-insights-limits.md)]

## Limites Azure Backup

[AZURE.INCLUDE [azure-backup-limits](../includes/azure-backup-limits.md)]

## Limites Azure Site Recovery

[AZURE.INCLUDE [site-recovery-limits](../includes/site-recovery-limits.md)]

## Limites d’Application Insights

[AZURE.INCLUDE [application-insights-limits](../includes/application-insights-limits.md)]

## Limites Gestion des API

[AZURE.INCLUDE [api-management-service-limits](../includes/api-management-service-limits.md)]

## Limite du service Cache Redis Azure

[AZURE.INCLUDE [redis-cache-service-limits](../includes/redis-cache-service-limits.md)]

## Limites du coffre de clés

[AZURE.INCLUDE [key-vault-limits](../includes/key-vault-limits.md)]

## Azure Multi-Factor Authentication
[AZURE.INCLUDE [azure-mfa-service-limits](../includes/azure-mfa-service-limits.md)]

## Voir aussi

[Présentation des limites et des augmentations Azure](http://azure.microsoft.com/blog/2014/06/04/azure-limits-quotas-increase-requests/)

[Tailles de machines virtuelles et services cloud pour Windows Azure](http://msdn.microsoft.com/library/azure/dn197896.aspx)

<!---HONumber=August15_HO8-->