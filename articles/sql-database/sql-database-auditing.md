---
title: "Base de données SQL Azure - Audit | Microsoft Docs"
description: "L’audit Azure SQL Database suit les événements de base de données et les écrit dans un journal d’audit dans votre compte Azure Storage."
services: sql-database
documentationcenter: 
author: ronitr
manager: jhubbard
editor: giladm
ms.assetid: 89c2a155-c2fb-4b67-bc19-9b4e03c6d3bc
ms.service: sql-database
ms.custom: secure and protect
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 10/05/2016
ms.author: ronitr; giladm
translationtype: Human Translation
ms.sourcegitcommit: 5d51a5ef3387b4c00079547b0f44ffe1f96bd77c
ms.openlocfilehash: 8c9da10e6ebc67c464bf7b5e569cb2113a082d0c
ms.lasthandoff: 02/17/2017


---
# <a name="sql-database-auditing-concepts"></a>Concepts d’audit de base de données SQL
L’audit Azure SQL Database suit les événements de base de données et les écrit dans un journal d’audit dans votre compte Azure Storage.

L’audit peut vous aider à respecter une conformité réglementaire, à comprendre l’activité de la base de données et à découvrir des discordances et anomalies susceptibles d’indiquer des problèmes pour l’entreprise ou des violations de la sécurité.

L’audit permet et facilite le respect de normes de conformité, mais il ne garantit pas cette conformité. Pour plus d’informations sur les programmes Azure prenant en charge la conformité aux normes, voir le [Centre de gestion de la confidentialité Azure](https://azure.microsoft.com/support/trust-center/compliance/).

* [Vue d’ensemble de l’audit Azure SQL Database]
* [Configuration de l’audit pour votre base de données]
* [Analyse des journaux et des rapports d’audit]

## <a name="a-idsubheading-1aazure-sql-database-auditing-overview"></a><a id="subheading-1"></a>Vue d’ensemble de l’audit Azure SQL Database
Éléments rendus possibles par l'audit de bases de données :

* **La rétention** d’une piste d’audit d’événements sélectionnés. Définissez les catégories d'actions et d'événements de base de données à auditer.
* **La génération de rapports** sur les activités de la base de données. Vous pouvez utiliser des rapports préconfigurés et un tableau de bord pour une prise en main rapide de la génération de rapports d'activités et d'événements.
* **L'analyse** des rapports. Vous pouvez repérer les événements suspects, les activités inhabituelles et les tendances.

Il existe deux **méthodes d’audit** :

* **Audit d’objets blob** : les journaux sont écrits dans Stockage Blob Azure. Il s’agit d’une méthode d’audit plus récente, qui fournit des performances plus élevées, prend en charge l’audit avec une granularité plus élevée au niveau des objets et est plus économique.
* **Audit des tables** : les journaux sont écrits dans Stockage Table Azure.

> [!IMPORTANT]
> L’introduction du nouvel audit d’objets blob apporte une modification majeure dans la façon dont la stratégie d’audit de serveur est héritée par la base de données. 

Vous pouvez configurer l’audit pour différents types de catégories d’événements.

* Pour configurer et gérer l’audit à l’aide du portail Azure, consultez [Audit à l’aide du portail Azure](sql-database-auditing-portal.md).
* Pour configurer et gérer l’audit à l’aide de PowerShell, consultez [Audit à l’aide de PowerShell](sql-database-auditing-powershell.md).
* Pour configurer et gérer l’audit à l’aide de l’API REST, consultez [Audit à l’aide de l’API REST](sql-database-auditing-rest.md).

<!--For each Event Category, auditing of **Success** and **Failure** operations are configured separately.-->

Une stratégie d’audit peut être définie pour une base de données spécifique ou en tant que stratégie de serveur par défaut. Une stratégie d’audit de serveur par défaut s’applique aux bases de données existantes et à celles qui sont nouvellement créées sur un serveur.

## <a name="blob-auditing"></a>Audit d’objets blob

Si l’audit d’objets blob du serveur est activé, il s’applique toujours à la base de données (toutes les bases de données sur le serveur seront auditées), indépendamment des éléments suivants :
    - Les paramètres d’audit de base de données.
    - Que la case à cocher « Hériter des paramètres du serveur » soit activée ou non dans le panneau de la base de données.

> [!IMPORTANT]
> L’activation de l’audit d’objets blob de la base de données, en plus de l’activation sur le serveur, ne remplacera **pas** ou ne modifiera aucun des paramètres de l’audit d’objets blob du serveur : les deux audits coexisteront. En d’autres termes, la base de données sera auditée deux fois en parallèle (une fois par la stratégie du serveur et une autre fois par la stratégie de la base de données).

Vous devez éviter d’activer à la fois l’audit d’objets blob du serveur et l’audit d’objets blob de la base de données, sauf si :
 * Vous devez utiliser un autre *compte de stockage* ou une autre *période de rétention* pour une base de données spécifique.
 * Vous souhaitez auditer d’autres types ou catégories d’événements pour une base de données spécifique pour les autres bases de données sur ce serveur (par exemple, si des insertions de table doivent être auditées uniquement pour une base de données spécifique).

Sinon, nous vous recommandons d’activer uniquement l’audit d’objets blob au niveau du serveur et de laisser désactivé l’audit au niveau de la base de données pour toutes les bases de données.

## <a name="table-auditing"></a>Audit de table

Si l’audit de table au niveau du serveur est activé, il s’applique uniquement à la base de données si la case à cocher « Hériter des paramètres du serveur » est activée dans le panneau de la base de données (cette option est activée par défaut pour toutes les bases de données qui viennent d’être créées).

- Si la case est *cochée*, toutes les modifications apportées aux paramètres d’audit dans la base de données **remplaceront** les paramètres du serveur pour cette base de données.

- Si la case n’est *pas cochée* et que l’audit au niveau de la base de données est désactivé, la base de données ne sera pas auditée.

## <a name="analyze-audit-logs-and-reports"></a>Analyse des journaux et des rapports d'audit
Les journaux d’audit sont agrégés dans le compte de stockage Azure choisi lors de la configuration.

Vous pouvez explorer les fichiers journaux d’audit avec un outil comme [l’Explorateur de stockage Azure](http://storageexplorer.com/).

## <a name="next-steps"></a>Étapes suivantes

* Pour configurer et gérer l’audit à l’aide du portail Azure, consultez [Configurer l’audit dans le portail Azure](sql-database-auditing-portal.md).
* Pour configurer et gérer l’audit à l’aide de PowerShell, consultez [Configurer l’audit avec PowerShell](sql-database-auditing-powershell.md).
* Pour configurer et gérer l’audit à l’aide de l’API REST, consultez [Configurer l’audit avec l’API REST](sql-database-auditing-rest.md).


<!--Anchors-->
[Vue d’ensemble de l’audit Azure SQL Database]: #subheading-1
[Configuration de l’audit pour votre base de données]: #subheading-2
[Analyse des journaux et des rapports d’audit]: #subheading-3
[Practices for usage in production]: #subheading-5
[Storage Key Regeneration]: #subheading-6
[Automation (PowerShell / REST API)]: #subheading-7
[Blob/Table differences in Server auditing policy inheritance]: (#subheading-8)  

<!--Image references-->
[1]: ./media/sql-database-auditing-get-started/1_auditing_get_started_settings.png
[2]: ./media/sql-database-auditing-get-started/2_auditing_get_started_server_inherit.png
[3]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on.png
[3-tbl]: ./media/sql-database-auditing-get-started/3_auditing_get_started_turn_on_table.png
[4]: ./media/sql-database-auditing-get-started/4_auditing_get_started_storage_details.png
[5]: ./media/sql-database-auditing-get-started/5_auditing_get_started_audited_events.png
[6]: ./media/sql-database-auditing-get-started/6_auditing_get_started_storage_key_regeneration.png
[7]: ./media/sql-database-auditing-get-started/7_auditing_get_started_activity_log.png
[8]: ./media/sql-database-auditing-get-started/8_auditing_get_started_regenerate_key.png
[9]: ./media/sql-database-auditing-get-started/9_auditing_get_started_report_template.png
[10]: ./media/sql-database-auditing-get-started/10_auditing_get_started_blob_view_audit_logs.png
[11]: ./media/sql-database-auditing-get-started/11_auditing_get_started_blob_audit_records.png
[12]: ./media/sql-database-auditing-get-started/12_auditing_get_started_table_audit_records.png

