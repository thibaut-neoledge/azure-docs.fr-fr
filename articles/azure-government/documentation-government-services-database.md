---
title: "Bases de données Azure Government | Microsoft Docs"
description: This provides a comparision of features and guidance on developing applications for Azure Government
services: azure-government
cloud: gov
documentationcenter: 
author: ryansoc
manager: zakramer
ms.assetid: a1e173a9-996a-4091-a2e3-6b1e36da9ae1
ms.service: azure-government
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: azure-government
ms.date: 11/14/2016
ms.author: ryansoc
translationtype: Human Translation
ms.sourcegitcommit: c94e643565374f852633254a94c2138713cc290e
ms.openlocfilehash: 58d6295057c71dad488e04be25cc9a85d73bcd20


---
# <a name="azure-government-databases"></a>Bases de données Azure Government
## <a name="sql-database"></a>Base de données SQL
Pour obtenir des conseils supplémentaires sur la configuration de visibilité des métadonnées, ainsi que les meilleures pratiques de protection, voir le <a href="https://msdn.microsoft.com/en-us/library/bb510589.aspx"> Centre de sécurité Microsoft pour le moteur de base de données SQL </a> et la [ documentation publique des bases de données SQL Azure ](../sql-database/index.md).

### <a name="variations"></a>Variantes
SQL Database V12 est mis à la disposition générale dans Azure Government.

L’adresse des serveurs SQL Azure dans Azure Government est différente :

| Type de service | Azure (public) | Azure Government |
| --- | --- | --- |
| Base de données SQL |*.database.windows.net |*.database.usgovcloudapi.net |

### <a name="considerations"></a>Considérations
Les informations suivantes identifient les limites d’Azure Government pour Azure SQL :

| Données réglementées/contrôlées autorisées | Données réglementées/contrôlées non autorisées |
| --- | --- |
| Toutes les données stockées et traitées dans Microsoft Azure SQL peuvent contenir des données réglementées Azure Government. Vous devez utiliser des outils de base de données pour le transfert de données Azure Government réglementées. |Les métadonnées Azure SQL ne peuvent pas contenir de données contrôlées à l’exportation. Ces métadonnées incluent toutes les données de configuration entrées lors de la création et la gestion de votre produit de stockage.  N’entrez pas de données réglementées/contrôlées dans les champs suivants : Nom de base de données, Nom d’abonnement, Groupes de ressources, Nom de serveur, Identifiant d’administrateur du serveur, Noms de déploiements, Noms de ressources, Balises de ressources |

## <a name="azure-redis-cache"></a>Cache Redis Azure
Pour plus d’informations sur ce service et son utilisation, consultez la [documentation publique du Cache Redis Azure](../redis-cache/index.md).

### <a name="variations"></a>Variantes
Les URL d’accès et de gestion du Cache Redis Azure dans Azure Government sont différentes :

| Type de service | Azure (public) | Azure Government |
| --- | --- | --- |
| Point de terminaison du cache |*.redis.cache.windows.net |*.redis.cache.usgovcloudapi.net |
| Portail Azure |https://portal.azure.com |https://portal.azure.us |

> [!NOTE]
> Tous vos scripts et codes doivent prendre en compte les points de terminaison et environnements appropriés. Pour plus d’informations, voir [Comment se connecter au cloud Azure Government](../redis-cache/cache-howto-manage-redis-cache-powershell.md#how-to-connect-to-azure-government-cloud-or-azure-china-cloud).
> 
> 

### <a name="considerations"></a>Considérations
Les informations suivantes identifient les limites d’Azure Government pour Cache Redis Azure :

| Données réglementées/contrôlées autorisées | Données réglementées/contrôlées non autorisées |
| --- | --- |
| Toutes les données stockées et traitées dans Cache Redis Azure peuvent contenir des données réglementées Azure Government. |Les métadonnées de Cache Redis Azure ne peuvent pas contenir de données contrôlées à l’exportation. N’entrez pas de données réglementées/contrôlées dans les champs suivants : Nom du cache, Nom du réseau virtuel, Nom de l’abonnement, Groupes de ressources, Balises de ressources, Propriétés Redis. |

## <a name="next-steps"></a>Étapes suivantes
Pour obtenir des informations supplémentaires et des mises à jour, inscrivez-vous au <a href="https://blogs.msdn.microsoft.com/azuregov/">blog Microsoft Azure Government</a>.




<!--HONumber=Nov16_HO3-->


