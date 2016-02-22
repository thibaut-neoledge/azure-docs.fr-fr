<properties
	pageTitle="Modifier les niveaux de service et de performances d’une base de données SQL Azure"
	description="Cet article explique comment faire monter ou descendre en puissance une base de données SQL. Modification du niveau de tarification d’une base de données SQL Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="02/02/2015"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Modifier les niveaux de service et de performances (niveau de tarification) d’une base de données SQL

**Base de données unique**

> [AZURE.SELECTOR]
- [Azure Portal](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)

Cet article explique comment modifier les niveaux de service et de performances de votre base de données SQL à l’aide du [portail Azure](https://portal.azure.com).

Exploitez les informations des sections [Mise à jour des bases de données SQL des éditions Web ou Business vers les nouveaux niveaux de service](sql-database-upgrade-new-service-tiers.md) et [Niveaux de service et de performance de base de données SQL Azure](sql-database-service-tiers.md) pour déterminer le niveau de service et de performances adéquat pour votre base de données SQL Microsoft Azure.

> [AZURE.IMPORTANT] La modification des niveaux de service et de performances d’une base de données SQL s’effectue en ligne. Cela signifie que votre base de données doit être en ligne et accessible pendant toute la durée de l’opération, sans interruption de service.

- Pour qu’une base de données puisse passer à une version antérieure, sa taille doit être inférieure à la taille maximale autorisée par le niveau de service voulu. 
- Lors de la mise à niveau d’une base de données avec la [géo-réplication standard](https://msdn.microsoft.com/library/azure/dn758204.aspx) ou la [géo-réplication active](https://msdn.microsoft.com/library/azure/dn741339.aspx), vous devez d'abord mettre à niveau les bases de données secondaires associées vers le niveau de performances souhaité avant la mise à niveau de la base de données principale.
- Avant d’effectuer le passage à une version antérieure depuis un niveau de service Premium, vous devez arrêter toutes les relations de géo-réplication. Vous pouvez suivre les étapes décrites dans la section [Arrêt d’une relation de copie continue](https://msdn.microsoft.com/library/azure/dn741323.aspx) pour arrêter le processus de réplication entre la base de données principale et les bases de données secondaires actives.
- Les offres de service de restauration sont différentes selon les niveaux de service. Si vous passez à une version antérieure, vous risquez de ne plus pouvoir effectuer de restauration à un moment donné, ou de bénéficier d’une période de rétention des sauvegardes moins étendue. Pour en savoir plus, voir [Sauvegarde et restauration de base de données SQL Azure](https://msdn.microsoft.com/library/azure/jj650016.aspx).
- La modification de la tarification de votre base de données ne modifie pas la taille maximale de la base de données. Pour modifier la taille maximale de votre base de données, utilisez [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- Vous pouvez apporter jusqu’à quatre modifications à une base de données individuelle (concernant un niveau de service ou de performances) par période de 24 heures.
- Les nouvelles propriétés de la base de données ne sont appliquées qu’une fois les modifications terminées.


**Pour effectuer ce qui est décrit dans cet article, vous avez besoin des éléments suivants :**

- Un abonnement Azure. Si vous avez besoin d'un abonnement Azure, cliquez simplement sur **VERSION D'ÉVALUATION GRATUITE** en haut de cette page, puis continuez la lecture de cet article.
- base de données SQL Azure. Si vous n’avez pas de base de données SQL, créez-en une en suivant les étapes figurant dans l’article [Créer votre première base de données SQL Azure](sql-database-get-started.md).


## Modifier les niveaux de service et de performances de votre base de données


Ouvrez le panneau SQL Database de la base de données dont vous souhaitez augmenter ou diminuer la taille :

1.	Accédez au [portail Azure](https://portal.azure.com).
2.	Cliquez sur **PARCOURIR TOUT**.
3.	Cliquez sur **Bases de données SQL**.
2.	Cliquez sur la base de données à modifier.
3.	Dans le panneau de la base de données SQL, cliquez sur **Tous les paramètres**, puis sur **Niveau tarifaire (mise à l'échelle de DTU)** :

    ![niveau tarifaire][1]


1.  Sélectionnez un nouveau niveau, puis cliquez sur **Sélectionner** :

    Le fait de cliquer sur **Sélectionner** envoie une demande de mise à l’échelle pour modifier la couche de base de données. En fonction de la taille de votre base de données, l’opération de mise à l’échelle peut prendre plus ou moins longtemps. Cliquez sur la notification pour obtenir les détails et l’état de l’opération de mise à l’échelle.

    > [AZURE.NOTE] La modification de la tarification de votre base de données ne modifie pas la taille maximale de la base de données. Pour modifier la taille maximale de votre base de données, utilisez [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).

    ![sélectionner un niveau de tarification][2]

3.	Dans le ruban gauche, cliquez sur **Notifications** :

    ![notifications][3]

## Vérifier que la base de données est au niveau de tarification sélectionné

   Une fois l’opération de mise à l’échelle terminée, inspectez la base de données et confirmez qu’elle est au niveau désiré :

2.	Cliquez sur **PARCOURIR TOUT**.
3.	Cliquez sur **Bases de données SQL**.
2.	Cliquez sur la base de données mise à jour.
3.	Vérifiez le **Niveau de tarification** et confirmez que le niveau approprié est défini.

    ![nouveau prix][4]


## Étapes suivantes

- Modifier la taille maximale de votre base de données en utilisant [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
- [Faire monter ou descendre en puissance](sql-database-elastic-scale-get-started.md)
- [Se connecter à une base de données SQL et l’interroger avec SSMS](sql-database-connect-query-ssms.md)
- [Exporter une base de données SQL Azure](sql-database-export.md)

## Ressources supplémentaires

- [Vue d'ensemble de la continuité des activités](sql-database-business-continuity.md)
- [Documentation sur la base de données SQL](https://azure.microsoft.com/documentation/services/sql-database/)


<!--Image references-->
[1]: ./media/sql-database-scale-up/pricing-tile.png
[2]: ./media/sql-database-scale-up/choose-tier.png
[3]: ./media/sql-database-scale-up/scale-notification.png
[4]: ./media/sql-database-scale-up/new-tier.png

<!---HONumber=AcomDC_0211_2016-->