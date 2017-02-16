---
title: "Portail Azure : modification du niveau tarifaire d’Azure SQL Database | Microsoft Docs"
description: "Apprenez à utiliser le portail Azure pour modifier le niveau de performances et le niveau de service d’Azure SQL Database et augmenter ou réduire les ressources et la tarification de votre base de données SQL."
services: sql-database
documentationcenter: 
author: stevestein
manager: jhubbard
editor: 
ms.assetid: cbd67e88-08d5-40e2-a223-0fb0c718a782
ms.service: sql-database
ms.custom: monitor and tune
ms.devlang: NA
ms.date: 10/12/2016
ms.author: sstein
ms.workload: data-management
ms.topic: article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 2b55b6b4475abdbc1985d8ac370b3b612b77eb0e
ms.openlocfilehash: 88104e0684281f77da497ef4dd608c6a0c64b274


---
# <a name="change-the-service-tier-and-performance-level-pricing-tier-of-a-sql-database-using-the-azure-portal"></a>Modification des niveaux de service et de performances (niveau de tarification) d’une base de données SQL à l’aide du portail Azure
> [!div class="op_single_selector"]
> * [**Portail Azure**](sql-database-scale-up.md)
> * [PowerShell](sql-database-scale-up-powershell.md)> 
> 

Les niveaux de service et de performances décrivent les fonctionnalités et ressources disponibles pour votre base de données SQL, et peuvent être mis à jour à mesure que les besoins de votre application évoluent. Pour plus d’informations, voir [Niveaux de service](sql-database-service-tiers.md).

Notez que la modification du niveau de service et/ou de performances d’une base de données crée un réplica de la base de données d’origine au nouveau niveau de performances, puis bascule les connexions vers ce réplica. Aucune donnée n’est perdue lors de ce processus, mais pendant le bref instant où nous basculons vers le réplica, les connexions à la base de données sont désactivées, de sorte que certaines transactions en cours sont susceptibles d’être restaurées. Cette fenêtre de désactivation varie, mais dure moins de 4 secondes en moyenne, et ne dépasse pas 30 secondes dans plus de 99 % des cas. En de très rare occasions, cette durée peut se révéler supérieure, en particulier s’il existe un très grand nombre de transactions en cours au moment où les connexions sont désactivées.  

Dans la plupart des cas, la durée de l’ensemble du processus d’augmentation prendra moins de 5 minutes. 

Exploitez les informations des sections [Niveaux de service et de performance de base de données SQL Azure](sql-database-service-tiers.md) pour déterminer le niveau de service et de performances adéquat pour votre base de données SQL Microsoft Azure.

* Pour qu’une base de données puisse passer à une version antérieure, sa taille doit être inférieure à la taille maximale autorisée par le niveau de service voulu. 
* Lors de la mise à niveau d’une base de données avec la [géoréplication](sql-database-geo-replication-overview.md) activée, vous devez commencer par mettre à niveau les bases de données secondaires associées vers le niveau de performances souhaité avant de procéder à la mise à niveau de la base de données primaire.
* Avant de passer à un niveau de service inférieur, vous devez arrêter toutes les relations de géoréplication. 
* Les offres de service de restauration sont différentes selon les niveaux de service. Si vous passez à une version antérieure, vous risquez de ne plus pouvoir effectuer de restauration à un moment donné, ou de bénéficier d’une période de rétention des sauvegardes moins étendue. Pour en savoir plus, voir [Sauvegarde et restauration de base de données SQL Azure](sql-database-business-continuity.md).
* La modification de la tarification de votre base de données ne modifie pas la taille maximale de la base de données. Pour modifier la taille maximale de votre base de données, utilisez [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
* Les nouvelles propriétés de la base de données ne sont appliquées qu’une fois les modifications terminées.

**Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :**

* base de données SQL Azure. Si vous n’avez pas de base de données SQL, créez-en une en suivant les étapes figurant dans l’article [Créer votre première base de données SQL Azure](sql-database-get-started.md).

## <a name="change-the-service-tier-and-performance-level-of-your-database"></a>Modifier les niveaux de service et de performances de votre base de données
Ouvrez le panneau SQL Database de la base de données dont vous souhaitez augmenter ou diminuer la taille :

1. Dans le [portail Azure](https://portal.azure.com), cliquez sur **Autres services** > **Bases de données SQL**.
2. Cliquez sur la base de données à modifier.
3. Dans le volet **Base de données SQL**, cliquez sur **Niveau tarifaire (mise à l’échelle de DTU)** :
   
   ![Niveau de tarification][1]
4. Sélectionnez un nouveau niveau, puis cliquez sur **Sélectionner** :
   
   Le fait de cliquer sur **Sélectionner** envoie une demande de mise à l’échelle pour modifier le niveau de tarification. En fonction de la taille de votre base de données, l’opération de mise à l’échelle peut prendre plus ou moins longtemps (voir les informations en haut de cet article).
   
   > [!NOTE]
   > La modification de la tarification de votre base de données ne modifie pas la taille maximale de la base de données. Pour modifier la taille maximale de votre base de données, utilisez [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
   > 
   > 
   
   ![sélectionner un niveau de tarification][2]
5. Cliquez sur l’icône de notification (cloche), dans le coin supérieur droit :
   
   ![Notifications][3]
   
   Cliquez sur le texte de notification pour ouvrir le volet d’informations où vous pouvez afficher l’état de la demande.

## <a name="next-steps"></a>Étapes suivantes
* Modifiez la taille maximale de votre base de données en utilisant [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
* [Faire monter ou descendre en puissance](sql-database-elastic-scale-get-started.md)
* [Se connecter à une base de données SQL et l’interroger avec SSMS](sql-database-connect-query-ssms.md)
* [Exporter une base de données SQL Azure](sql-database-export.md)

## <a name="additional-resources"></a>Ressources supplémentaires
* [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
* [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)

<!--Image references-->
[1]: ./media/sql-database-scale-up/new-tier.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png



<!--HONumber=Jan17_HO1-->


