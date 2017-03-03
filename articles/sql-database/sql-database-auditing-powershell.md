---
title: "PowerShell : gérer l’audit de base de données SQL Azure | Microsoft Docs"
description: "Configurez l’audit de base de données SQL Azure avec PowerShell pour suivre les événements de base de données et les écrire dans un journal d’audit, dans votre compte Microsoft Azure Storage."
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
ms.openlocfilehash: 9839dfb02478593de9a6db59d2a462d2b64f957e
ms.lasthandoff: 02/17/2017


---
# <a name="configure-and-manage-sql-database-auditing-using-powershell"></a>Configurer et gérer l’audit de base de données SQL avec PowerShell

La section suivante explique comment configurer et gérer l’audit avec PowerShell. Pour configurer et gérer l’audit à l’aide du portail Azure, consultez [Configurer l’audit dans le portail Azure](sql-database-auditing-portal.md). Pour configurer et gérer l’audit à l’aide de l’API REST, consultez [Configurer l’audit avec l’API REST](sql-database-auditing-rest.md).

Pour une présentation de l’audit, consultez [Audit de base de données SQL](sql-database-auditing.md).

## <a name="powershell-cmdlets"></a>Applets de commande PowerShell

   * [Get-AzureRMSqlDatabaseAuditingPolicy][101]
   * [Get-AzureRMSqlServerAuditingPolicy][102]
   * [Remove-AzureRMSqlDatabaseAuditing][103]
   * [Remove-AzureRMSqlServerAuditing][104]
   * [Set-AzureRMSqlDatabaseAuditingPolicy][105]
   * [Set-AzureRMSqlServerAuditingPolicy][106]
   * [Use-AzureRMSqlServerAuditingPolicy][107]

## <a name="next-steps"></a>Étapes suivantes

* Pour configurer et gérer l’audit à l’aide du portail Azure, consultez [Configurer l’audit de base de données dans le portail Azure](sql-database-auditing-portal.md). 
* Pour configurer et gérer l’audit à l’aide de PowerShell, consultez [Configurer l’audit de base de données avec l’API REST](sql-database-auditing-rest.md).
* Pour une présentation de l’audit, consultez [Audit de base de données](sql-database-auditing.md).


[101]: https://msdn.microsoft.com/en-us/library/azure/mt603731(v=azure.200).aspx
[102]: https://msdn.microsoft.com/en-us/library/azure/mt619329(v=azure.200).aspx
[103]: https://msdn.microsoft.com/en-us/library/azure/mt603796(v=azure.200).aspx
[104]: https://msdn.microsoft.com/en-us/library/azure/mt603574(v=azure.200).aspx
[105]: https://msdn.microsoft.com/en-us/library/azure/mt603531(v=azure.200).aspx
[106]: https://msdn.microsoft.com/en-us/library/azure/mt603794(v=azure.200).aspx
[107]: https://msdn.microsoft.com/en-us/library/azure/mt619353(v=azure.200).aspx

