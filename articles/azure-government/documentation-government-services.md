---
title: Services Azure Government disponibles | Microsoft Docs
description: "Fournit une vue d’ensemble des services disponibles dans Azure Government."
services: azure-government
cloud: gov
documentationcenter: 
author: smichelotti
manager: liki
ms.assetid: a453a23c-bc0f-4203-9075-0f579dea7e23
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 02/13/2017
ms.author: stemi
translationtype: Human Translation
ms.sourcegitcommit: d9dad6cff80c1f6ac206e7fa3184ce037900fc6b
ms.openlocfilehash: 21c2a0faad87b84058093f02c831b374a644b4b6
ms.lasthandoff: 03/06/2017


---
# <a name="available-services-on-azure-government"></a>Services disponibles dans Azure Government
Azure Government développe en permanence les services disponibles.  Ces services sont déployés avec le même code que celui utilisé dans Public Azure.  Cette section décrit les services qui sont actuellement disponibles sur Azure Government, notamment deux types d’informations essentiels :

* **Variantes :** variations en raison de fonctionnalités qui n’ont pas encore été déployées, ou de propriétés propres à l’environnement d’administration (par exemple, les URL).  
* **Considérations :** informations d’implémentation propres à Government afin de s’assurer que les données restent dans les limites de la conformité.

Tout ce que vous devez savoir sur ces services figure dans leur documentation générale.

Pour obtenir la liste actualisée des services, consultez les [produits par région](https://azure.microsoft.com/regions/services/). 

Dans les tableaux suivants, les services spécifiés comme compatibles avec Resource Manager ont des fournisseurs de ressources et peuvent être gérés à l’aide de PowerShell. Pour plus d’informations sur les fournisseurs Resource Manager, les versions d’API et les schémas, consultez [cette page](../azure-resource-manager/resource-manager-supported-services.md). Les services spécifiés comme étant disponibles dans le portail peuvent être gérés dans [portail Azure Government](https://portal.azure.us/). 


## <a name="computedocumentation-government-computemd"></a>[Calcul](documentation-government-compute.md)

| Service | Resource Manager activé | Portail |
| --- | --- | --- |
| [Machines virtuelles](documentation-government-compute.md#virtual-machines) | Oui | Oui |
| Batch | Oui | Oui |
| Services cloud | Oui | Oui |
| Service Fabric | Oui | Oui |
| Groupes de machines virtuelles identiques (VMSS) | Oui | Oui |


## <a name="networkingdocumentation-government-networkingmd"></a>[Mise en réseau](documentation-government-networking.md)

| Service | Resource Manager activé | Portail |
| --- | --- | --- |
| [ExpressRoute](documentation-government-networking.md#expressroute-private-connectivity) | Oui | Oui |
| Réseau virtuel | Oui | Oui |
| [Équilibreur de charge](documentation-government-networking.md#support-for-load-balancer) | Oui | Oui |
| [Traffic Manager](documentation-government-networking.md#support-for-traffic-manger) | Oui | Oui |
| [Passerelle VPN](documentation-government-networking.md#support-for-vpn-gateway) | Oui | Oui |
| Application Gateway | Oui | Oui |
| ExpressRoute | Oui | Oui |



## <a name="storagedocumentation-government-services-storagemd"></a>[Stockage](documentation-government-services-storage.md)

| Service | Resource Manager activé | Portail |
| --- | --- | --- |
| [Stockage - Blobs](documentation-government-services-storage.md#azure-storage) | Oui | Oui |
| [Stockage - Tables](documentation-government-services-storage.md#azure-storage) | Oui | Oui |
| [Stockage - Files d’attente](documentation-government-services-storage.md#azure-storage) | Oui | Oui |
| [Stockage - Fichiers](documentation-government-services-storage.md#azure-storage) | Oui | Oui |
| [Stockage - Disques](documentation-government-services-storage.md#azure-storage) | Oui | Oui |
| [StorSimple](documentation-government-services-storage.md) | Oui | Oui |
| [Sauvegarde](documentation-government-services-storage.md#azure-storage) | Oui | Oui |
| [Site Recovery](documentation-government-services-storage.md#azure-storage) | Oui | Oui |
| [Import/Export](documentation-government-services-storage.md#azure-storage) | Oui | Non |



## <a name="web--mobiledocumentation-government-services-webandmobilemd"></a>[Web et mobilité](documentation-government-services-webandmobile.md)

| Service | Resource Manager activé | Portail |
| --- | --- | --- |
| [App Service - Web Apps](documentation-government-services-webandmobile.md#app-services) | Oui | Oui |
| [App Service - API Apps](documentation-government-services-webandmobile.md#app-services) | Oui | Oui |
| [App Service - Mobile Apps](documentation-government-services-webandmobile.md#app-services) | Oui | Oui |
| Media Services | Oui | Oui |


## <a name="databasesdocumentation-government-services-databasemd"></a>[Bases de données](documentation-government-services-database.md)

| Service | Resource Manager activé | Portail |
| --- | --- | --- |
| [Base de données SQL](documentation-government-services-database.md#sql-database) | Oui | Oui |
| SQL Data Warehouse | Oui | Oui |
| SQL Server Stretch Database | Oui | Oui |
| [Cache Redis](documentation-government-services-database.md#azure-redis-cache) | Oui | Oui |


## <a name="intelligence--analyticsdocumentation-government-services-intelligenceandanalyticsmd"></a>[Intelligence et analyse](documentation-government-services-intelligenceandanalytics.md)

| Service | Resource Manager activé | Portail |
| --- | --- | --- |
| [HDInsights](documentation-government-services-intelligenceandanalytics.md#hdinsight) | Oui | Oui |
| [Power BI Pro](documentation-government-services-intelligenceandanalytics.md#power-bi) | Non | Non (Portail d’administration Office 365) |


## <a name="internet-of-things-iot"></a>Internet des objets (IoT)

| Service | Resource Manager activé | Portail |
| --- | --- | --- |
| Event Hubs | Oui | Oui |
| Notification Hubs | Non | Non (accédez au [portail hérité](https://manage.windowsazure.us/)) |


## <a name="enterprise-integration"></a>Intégration d’entreprise

| Service | Resource Manager activé | Portail |
| --- | --- | --- |
| Service Bus | Oui | Oui |
| [StorSimple](documentation-government-services-storage.md) | Oui | Oui |
| SQL Server Stretch Database | Oui | Oui |



## <a name="security--identitydocumentation-government-services-securityandidentitymd"></a>[Sécurité et identité](documentation-government-services-securityandidentity.md)

| Service | Resource Manager activé | Portail |
| --- | --- | --- |
| Azure Active Directory | Oui | Oui |
| [Key Vault](documentation-government-services-securityandidentity.md#key-vault) | Oui | Non (bientôt disponible) |
| Multi-Factory Authentication | Oui | Oui |


## <a name="intelligence--analytics"></a>Intelligence et analyse

| Service | Resource Manager activé | Portail |
| --- | --- | --- |
| Power BI | Oui | Non |
| HDInsight | Oui | Oui |



## <a name="monitoring--managementdocumentation-government-services-monitoringandmanagementmd"></a>[Surveillance et gestion](documentation-government-services-monitoringandmanagement.md)

| Service | Resource Manager activé | Portail |
| --- | --- | --- |
| [Automation](documentation-government-services-monitoringandmanagement.md#automation) | Oui | Oui |
| [Sauvegarde](documentation-government-services-backup.md) | Oui | Oui |
| [Log Analytics](documentation-government-services-monitoringandmanagement.md#log-analytics) | Oui | Oui |
| [Site Recovery](documentation-government-services-monitoringandmanagement.md#site-recovery) | Oui | Oui |
| Scheduler | Oui | Non |
| Surveillance et diagnostics | Oui | Oui |




## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations supplémentaires et des mises à jour, inscrivez-vous au [blog Microsoft Azure Government](https://blogs.msdn.microsoft.com/azuregov/).


