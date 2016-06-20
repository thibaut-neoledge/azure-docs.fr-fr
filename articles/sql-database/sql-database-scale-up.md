<properties
	pageTitle="Modifier les niveaux de service et de performances d’une base de données SQL Azure"
	description="Cet article explique comment faire monter ou descendre en puissance une base de données SQL. Modification du niveau de tarification d’une base de données SQL Azure"
	services="sql-database"
	documentationCenter=""
	authors="stevestein"
	manager="jhubbard"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/29/2016"
	ms.author="sstein"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Modifier les niveaux de service et de performances (niveau de tarification) d’une base de données SQL


> [AZURE.SELECTOR]
- [Portail Azure](sql-database-scale-up.md)
- [PowerShell](sql-database-scale-up-powershell.md)


Les niveaux de service et de performances décrivent les fonctionnalités et ressources disponibles pour votre base de données SQL, et peuvent être mis à jour à mesure que les besoins de votre application évoluent. Pour plus d’informations, voir [Niveaux de service](sql-database-service-tiers.md).

Notez que la modification du niveau de service et/ou de performances d’une base de données crée un réplica de la base de données d’origine au nouveau niveau de performances, puis bascule les connexions vers ce réplica. Aucune donnée n’est perdue lors de ce processus, mais pendant le bref instant où nous basculons vers le réplica, les connexions à la base de données sont désactivées, de sorte que certaines transactions en cours sont susceptibles d’être restaurées. Cette fenêtre de désactivation varie, mais dure moins de 4 secondes en moyenne, et ne dépasse pas 30 secondes dans plus de 99 % des cas. En de très rare occasions, cette durée peut se révéler supérieure, en particulier s’il existe un très grand nombre de transactions en cours au moment où les connexions sont désactivées.

La durée de la totalité du processus de montée en puissance dépend de la taille et du niveau de service de la base de données avant et après la modification. Par exemple, le basculement d’une base de données de 250 Go vers, depuis ou dans un niveau de service Standard ne demande pas plus de 6 heures. Le changement des niveaux de performances d’une base de données de la même taille dans le niveau de service Premium doit s’effectuer en moins de 3 heures.


Exploitez les informations des sections [Mise à jour des bases de données SQL des éditions Web ou Business vers les nouveaux niveaux de service](sql-database-upgrade-server-portal.md) et [Niveaux de service et de performance de base de données SQL Azure](sql-database-service-tiers.md) pour déterminer le niveau de service et de performances adéquat pour votre base de données SQL Microsoft Azure.

- Pour qu’une base de données puisse passer à une version antérieure, sa taille doit être inférieure à la taille maximale autorisée par le niveau de service voulu. 
- Lors de la mise à niveau d’une base de données avec la [géoréplication](sql-database-geo-replication-overview.md) activée, vous devez commencer par mettre à niveau les bases de données secondaires associées vers le niveau de performances souhaité avant de procéder à la mise à niveau de la base de données primaire.
- Avant de passer à un niveau de service inférieur, vous devez arrêter toutes les relations de géoréplication. 
- Les offres de service de restauration sont différentes selon les niveaux de service. Si vous passez à une version antérieure, vous risquez de ne plus pouvoir effectuer de restauration à un moment donné, ou de bénéficier d’une période de rétention des sauvegardes moins étendue. Pour en savoir plus, voir [Sauvegarde et restauration de base de données SQL Azure](sql-database-business-continuity.md).
- La modification de la tarification de votre base de données ne modifie pas la taille maximale de la base de données. Pour modifier la taille maximale de votre base de données, utilisez [Transact-SQL (T-SQL)](https://msdn.microsoft.com/library/mt574871.aspx) ou [PowerShell](https://msdn.microsoft.com/library/mt619433.aspx).
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

<!---HONumber=AcomDC_0608_2016-->