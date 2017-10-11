---
title: Foire aux questions Azure SQL Data Warehouse | Microsoft Docs
description: "Cet article répertorie les questions fréquemment posées par les clients et les développeurs sur Azure SQL Data Warehouse."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: johnmac
editor: 
ms.assetid: 812CA525-3BF3-49DF-8DF3-FB4342464F4F
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: overview
ms.date: 3/1/2017
ms.author: elbutter;barbkess
ms.openlocfilehash: 4c00710ecc0c91f8407eca81b78176075fcbd6ad
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: fr-FR
ms.lasthandoff: 07/11/2017
---
# <a name="sql-data-warehouse-frequently-asked-questions"></a>Foire aux questions SQL Data Warehouse

## <a name="general"></a>Généralités

Q : Quelles sont les solutions offertes par SQL DW en matière de sécurité des données ?

R. SQL DW propose plusieurs solutions de protection des données, comme le chiffrement TDE et les audits. Pour plus d’informations, consultez [Sécurité].

Q : Où puis-je trouver les normes juridiques et commerciales auxquelles SQL DW est conforme ?

R. Consultez la page [Conformité Microsoft] pour connaître les différentes offres de conformité par produit, notamment SOC et ISO. Tout d’abord, sélectionnez le titre de conformité de votre choix, puis développez Azure dans la section des services cloud qui figurent dans le champ d’application de Microsoft, sur le côté droit de la page, pour voir quels sont les services Azure conformes.

Q : Puis-je connecter Power BI ?

R. Oui. Bien que Power BI prenne en charge les requêtes directes avec SQL DW, il n’est pas destiné à traiter un grand nombre d’utilisateurs ou de données en temps réel. Pour une utilisation en production de Power BI, nous recommandons d’utiliser Power BI en complément d’Azure Analysis Services ou d’Analysis Service IaaS. 

Q : Quelles sont les limites de capacité de SQL Data Warehouse ?

R. Consultez notre page [Limites de capacité] actuelle. 

Q : Pourquoi ma Mise à l’échelle/Pause/Reprise est-elle si longue ?

R : Un certain nombre de facteurs peuvent influencer la durée des opérations de gestion du calcul. Une cause fréquente des opérations avec une durée d’exécution longue est l’annulation de transactions. Quand une opération de mise à l’échelle ou de pause est lancée, toutes les sessions entrantes sont bloquées et les requêtes sont vidées. Afin de laisser le système dans un état stable, les transactions doivent être annulées avant qu’une opération puisse commencer. Plus le nombre et la taille du journal des transactions sont importants, plus longtemps l’opération sera bloquée en attente de la restauration du système à un état stable.

## <a name="user-support"></a>Service client

Q : J’ai une suggestion de fonctionnalité, où dois-je l’envoyer ?

R. Si vous souhaitez faire une demande de fonctionnalité, envoyez-la sur notre page [UserVoice].

Q : Comment faire x ?

R. Pour obtenir de l’aide concernant le développement avec SQL Data Warehouse, vous pouvez poser des questions sur notre page [Stack Overflow]. 

Q : Comment envoyer un ticket de support ?

R. Les [tickets de support] peuvent être déposés sur le Portail Azure.

## <a name="sql-languagefeature-support"></a>Prise en charge de fonctionnalités / langages SQL 

Q : Quels sont les types de données pris en charge par SQL Data Warehouse ?

R. Consultez la page [Types de données] SQL Data Warehouse.

Q : Quelles fonctionnalités de table prenez-vous en charge ?

R. SQL Data Warehouse prend en charge de nombreuses fonctionnalités, mais pas toutes ; celles qui ne sont pas prises en charge sont documentées dans [Fonctionnalités de table non prises en charge].

## <a name="tooling-and-administration"></a>Outils et administration

Q : Prenez-vous en charge les projets de base de données dans Visual Studio ?

R. À l’heure actuelle, nous ne prenons pas en charge les projets de base de données dans Visual Studio pour SQL Data Warehouse. Si vous souhaitez voter pour obtenir cette fonctionnalité, visitez notre UserVoice [Demande de fonctionnalités pour les projets de base de données].

Q : SQL Data Warehouse prend-il en charge les API REST ?

R. Oui. La plupart des fonctionnalités REST utilisables avec SQL Database sont également disponibles avec SQL Data Warehouse. Vous trouverez des informations sur les API sur les pages de documentation REST ou sur [MSDN].


## <a name="loading"></a>Chargement

Q : Quels pilotes clients prenez-vous en charge ?

R. La prise en charge des pilotes pour DW est détaillée sur la page [Chaînes de connexion].

Q : Quels sont les formats de fichier pris en charge par PolyBase avec SQL Data Warehouse ?

R : Orc, RC, Parquet et le texte plat délimité

Q : À quoi puis-je me connecter à partir de SQL DW à l’aide de PolyBase ? 

R : [Azure Data Lake Store] et [Azure Storage Blobs].

Q : Les automates à pile sont-ils possibles lors de la connexion à Azure Storage Blobs ou à  ADLS ? 

R : Non, SQL DW PolyBase interagit uniquement avec les composants de stockage. 

Q : Puis-je me connecter à HDI ?

R : HDI peut utiliser ADLS ou WASB comme couche HDFS. Si vous avez l’un des deux comme couche HDFS, vous pouvez charger ces données dans SQL DW. Toutefois, vous ne peut pas générer d’automate à pile vers l’instance HDI. 

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur SQL Data Warehouse dans son ensemble, consultez notre page [Vue d’ensemble].


<!-- Article references -->
[UserVoice]: https://feedback.azure.com/forums/307516-sql-data-warehouse
[Chaînes de connexion]: ./sql-data-warehouse-connection-strings.md
[Stack Overflow]: http://stackoverflow.com/questions/tagged/azure-sqldw
[tickets de support]: ./sql-data-warehouse-get-started-create-support-ticket.md
[Sécurité]: ./sql-data-warehouse-overview-manage-security.md
[Conformité Microsoft]: https://www.microsoft.com/en-us/trustcenter/compliance/complianceofferings
[Limites de capacité]: ./sql-data-warehouse-service-capacity-limits.md
[Types de données]: ./sql-data-warehouse-tables-data-types.md
[Fonctionnalités de table non prises en charge]: ./sql-data-warehouse-tables-overview.md#unsupported-table-features
[Azure Data Lake Store]: ./sql-data-warehouse-load-from-azure-data-lake-store.md
[Azure Storage Blobs]: ./sql-data-warehouse-load-from-azure-blob-storage-with-polybase.md
[Demande de fonctionnalités pour les projets de base de données]: https://feedback.azure.com/forums/307516-sql-data-warehouse/suggestions/13313247-database-project-from-visual-studio-to-support-azu
[MSDN]: https://msdn.microsoft.com/en-us/library/azure/mt163685.aspx
[Vue d’ensemble]: ./sql-data-warehouse-overview-faq.md