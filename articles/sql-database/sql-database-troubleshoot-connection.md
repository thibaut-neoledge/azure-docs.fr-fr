---
title: "La base de données sur le serveur n’est pas disponible actuellement, connexion à SQL Database | Microsoft Docs"
description: "Dépannage de l’erreur « La base de données sur le serveur n’est pas disponible actuellement » quand une application se connecte à SQL Database."
services: sql-database
documentationcenter: 
author: dalechen
manager: cshepard
editor: 
keywords: "la base de données sur le serveur n’est pas disponible actuellement, connexion à sql database"
ms.assetid: f61999ac-d46b-448a-8830-3b04978d84ec
ms.service: sql-database
ms.custom: troubleshoot
ms.workload: data-management
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 01/20/2017
ms.author: daleche
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: ede7672196e71bc493cd9b758982445e991def1b


---
# <a name="error-database-on-server-is-not-currently-available-when-connecting-to-sql-database"></a>Erreur « La base de données sur le serveur n’est pas disponible actuellement » lors de la connexion à SQL Database
[!INCLUDE [support-disclaimer](../../includes/support-disclaimer.md)]

Lorsqu’une application se connecte à une base de données SQL Azure, le message d’erreur suivant s’affiche :

```
Error code 40613: "Database <x> on server <y> is not currently available. Please retry the connection later. If the problem persists, contact customer support, and provide them the session tracing ID of <z>"
```

> [!NOTE]
> Ce message d’erreur est généralement temporaire (de courte durée).
> 
> 

Cette erreur se produit lorsque la base de données Azure est déplacée (ou reconfigurée) et que votre application perd sa connexion à la base de données SQL. Les événements de reconfiguration de la base de données SQL sont liés à un événement planifié (par exemple, une mise à niveau logicielle) ou à un événement non planifié (par exemple, un arrêt de processus ou un équilibrage de charge). La plupart des événements de reconfiguration sont généralement de courte durée et se terminent en l’espace de 60 secondes maximum. Cependant, ces événements peuvent parfois prendre plus de temps, par exemple lorsqu’une transaction volumineuse entraîne une récupération de longue durée.

## <a name="steps-to-resolve-transient-connectivity-issues"></a>Étapes pour résoudre les problèmes de connectivité transitoire
1. Consultez le [tableau de bord du service Microsoft Azure](https://azure.microsoft.com/status) pour obtenir la liste des coupures prévues qui se sont produites au moment où les erreurs ont été signalées par l’application.
2. Les applications qui se connectent à un service cloud, tel que la base de données SQL Azure, doivent s’attendre à des événements périodiques de reconfiguration et implémenter une logique de nouvelle tentative pour gérer ces erreurs au lieu d’afficher ces événements en tant qu’erreurs de l’application aux utilisateurs. Consultez la section [Erreurs temporaires](sql-database-connectivity-issues.md), ainsi que les bonnes pratiques et les instructions de conception dans [Vue d’ensemble du développement de base de données SQL](sql-database-develop-overview.md) pour obtenir plus d’informations et découvrir les stratégies générales de nouvelle tentative. Passez ensuite en revue les exemples de code dans l’article [Bibliothèques de connexions pour SQL Database et SQL Server](sql-database-libraries.md) pour obtenir des informations spécifiques.
3. Lorsqu’une base de données approche des limites de ressources, cela peut s’apparenter à un problème de connectivité transitoire. Consultez la page [Résolution des problèmes de performances](sql-database-troubleshoot-performance.md).
4. Si les problèmes de connectivité persistent ou si la durée pendant laquelle votre application rencontre une erreur dépasse les 60 secondes ou si plusieurs occurrences de l’erreur s’affichent dans un jour donné, créez une demande de support Azure en sélectionnant **Obtenir de l’aide** sur le site du [support Azure](https://azure.microsoft.com/support/options) .

## <a name="next-steps"></a>Étapes suivantes
* Si vous recevez une erreur différente, consultez le [message d’erreur](sql-database-develop-error-messages.md) pour obtenir des indices sur la cause.
* Si le problème persiste, consultez les instructions dans [Résoudre des problèmes de connexion à Azure SQL Database](sql-database-troubleshoot-common-connection-issues.md).




<!--HONumber=Nov16_HO3-->


