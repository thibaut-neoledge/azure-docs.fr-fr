---
title: "API REST : gérer l’audit de base de données SQL Azure | Microsoft Docs"
description: "Configurez l’audit de base de données SQL Azure en utilisant l’API REST pour suivre les événements de base de données et les écrire dans un journal d’audit, dans votre compte Microsoft Azure Storage."
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
ms.openlocfilehash: 9516dd1ae6a67fb9402591fe1906ee7554c1495c
ms.lasthandoff: 02/17/2017


---
# <a name="configure-and-manage-sql-database-auditing-using-the-rest-api"></a>Configurer et gérer l’audit de base de données SQL à l’aide de l’API REST

Cette rubrique explique comment configurer et gérer l’audit à l’aide de l’API REST. Pour configurer et gérer l’audit à l’aide du portail Azure, consultez [Configurer l’audit dans le portail Azure](sql-database-auditing-portal.md). Pour configurer et gérer l’audit à l’aide de PowerShell, consultez [Configurer l’audit avec PowerShell](sql-database-auditing-powershell.md).

Pour une présentation de l’audit, consultez [Audit de base de données SQL](sql-database-auditing.md).

## <a name="rest-api---blob-auditing"></a>API REST - Audit d’objets blob

   * [Create or Update Database Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt695939.aspx)
   * [Create or Update Server Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt771861.aspx)
   * [Get Database Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt695938.aspx)
   * [Get Server Blob Auditing Policy](https://msdn.microsoft.com/library/azure/mt771860.aspx)
   * [Get Server Blob Auditing Operation Result](https://msdn.microsoft.com/library/azure/mt771862.aspx)


## <a name="rest-api---table-auditing"></a>API REST - Audit de table

   * [Create or Update Database Auditing Policy](https://msdn.microsoft.com/library/azure/mt604471.aspx)
   * [Create or Update Server Auditing Policy](https://msdn.microsoft.com/library/azure/mt604383.aspx)
   * [Get Database Auditing Policy](https://msdn.microsoft.com/library/azure/mt604381.aspx)
   * [Get Server Auditing Policy](https://msdn.microsoft.com/library/azure/mt604382.aspx)

## <a name="next-steps"></a>Étapes suivantes

* Pour configurer et gérer l’audit à l’aide du portail Azure, consultez [Configurer l’audit de base de données dans le portail Azure](sql-database-auditing-portal.md). 
* Pour configurer et gérer l’audit à l’aide de PowerShell, consultez [Configurer l’audit de base de données avec PowerShell](sql-database-auditing-powershell.md).
* Pour une présentation de l’audit, consultez [Audit de base de données](sql-database-auditing.md).
