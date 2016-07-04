<properties
	pageTitle="Surveiller et gérer un pool de bases de données élastique avec le portail Azure | Microsoft Azure"
	description="Découvrez comment utiliser le portail Azure et l’intelligence intégrée de la base de données SQL pour gérer, surveiller et redimensionner un pool de bases de données élastique évolutif pour optimiser les performances de la base de données et gérer les coûts."
	keywords=""
	services="sql-database"
	documentationCenter=""
	authors="ninarn"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="06/22/2016"
	ms.author="ninarn"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Surveiller et gérer un pool de bases de données élastique avec le portail Azure

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-elastic-pool-manage-portal.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


Vous pouvez utiliser le portail Azure pour surveiller et gérer un pool de bases de données élastique et les bases de données du pool. Depuis le portail, vous pouvez surveiller l’utilisation d’un pool élastique et des bases de données que contient ce pool. Vous pouvez également apporter un ensemble de modifications à votre pool élastique et soumettre toutes les modifications en même temps. Ces modifications incluent l’ajout ou la suppression de bases de données, ainsi que le changement des paramètres du pool élastique ou des bases de données.

La capture d’écran ci-dessous montre un exemple de pool élastique. L’affichage inclut :

*  Des graphiques pour la surveillance de l’utilisation des ressources du pool élastique et des bases de données que contient ce pool. 
*  Le bouton **Configurer le pool** pour apporter des modifications au pool élastique. 
*  Le bouton **Créer une base de données**, qui permet de créer une base de données et de l’ajouter au pool élastique actuel.
*  Des tâches élastiques, qui vous aident à gérer un grand nombre de bases de données en exécutant des scripts Transact SQL sur toutes les bases de données d’une liste. 

![Affichage du pool][2]

Pour effectuer les étapes décrites dans cet article, vous avez besoin d’un serveur SQL dans Azure avec au moins une base de données et un pool élastique. Si vous ne disposez pas d’un pool élastique, consultez [Créer un pool](sql-database-elastic-pool-create-portal.md) ; si vous ne disposez pas d’une base de données, consultez le [Didacticiel sur la base de données SQL](sql-database-get-started.md).

## Surveillance du pool élastique

Vous pouvez accéder à un pool particulier pour consulter l’utilisation des ressources de ce dernier. Par défaut, le pool est configuré pour afficher l’utilisation du stockage et des eDTU au cours de la dernière heure. Le graphique peut être configuré pour afficher d’autres métriques sur différentes plages de temps.

1. Sélectionnez un pool à utiliser.
2. Sous **Surveillance du pool élastique**, vous trouverez un graphique intitulé **Utilisation des ressources**. Cliquez sur ce graphique.

	![Surveillance du pool élastique][3]

	Le panneau **Métrique** s’ouvre. Celui-ci affiche une vue détaillée des métriques spécifiées sur la plage de temps définie.

	![Volet Metric][9]

### Pour personnaliser l’affichage du graphique

Vous pouvez modifier le graphique et le panneau Métrique pour afficher d’autres métriques, telles que le pourcentage d’UC, le pourcentage d’E/S des données et le pourcentage d’E/S des fichiers journaux utilisés.
 
2. Dans le panneau Métrique, cliquez sur **Modifier**.

	![Cliquez sur Modifier][6]

- Dans le panneau **Modifier le graphique**, sélectionnez une nouvelle plage de temps (dernière heure, aujourd’hui ou semaine dernière) ou cliquez sur **Personnalisé** pour sélectionner la plage de temps de votre choix au cours des deux dernières semaines. Sélectionnez le type de graphique (bâtons ou linéaire), puis sélectionnez les ressources à surveiller. 

	![Cliquez sur Modifier](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

- Cliquez ensuite sur **OK**.


## Surveillance de la base de données élastique

Il est également possible de surveiller des bases de données individuelles pour identifier des problèmes potentiels.

1. Sous **Surveillance de la base de données élastique**, vous trouverez un graphique qui affiche les métriques de cinq bases de données. Par défaut, le graphique affiche les 5 premières bases de données du pool en termes d’utilisation moyenne des eDTU au cours de la dernière heure. Cliquez sur ce graphique.

	![Surveillance du pool élastique][4]

2. Le panneau **Database resource utilization** (Utilisation des ressources des bases de données) s’affiche. Celui-ci fournit une vue détaillée de l’utilisation des bases de données du pool. À l’aide de la grille dans la partie inférieure du panneau, vous pouvez sélectionner les bases de données du pool de votre choix (jusqu’à 5 bases de données) pour afficher leur utilisation dans le graphique. Vous pouvez également personnaliser les métriques et la plage de temps affichées dans le graphique en cliquant sur **Modifier le graphique**.

	![Panneau Database Resource Utilization (Utilisation des ressources des bases de données)][8]

### Pour personnaliser l’affichage

1. Dans le panneau **Database Resource Utilization** (Utilisation des ressources des bases de données), cliquez sur **Modifier le graphique**.

	![Cliquez sur Modifier le graphique](./media/sql-database-elastic-pool-manage-portal/db-utilization-blade.png)

2. Dans le panneau **Modifier le graphique**, sélectionnez une nouvelle plage de temps (dernière heure ou dernières 24 heures) ou cliquez sur **Personnalisé** pour sélectionner un autre jour au cours des 2 dernières semaines.

	![Cliquez sur Personnalisé](./media/sql-database-elastic-pool-manage-portal/editchart-date-time.png)

3. Cliquez sur la liste déroulante **Compare databases by** (Comparer les bases de données par) pour modifier la métrique à utiliser lors de la comparaison des bases de données.

	![Modifiez le graphique](./media/sql-database-elastic-pool-manage-portal/edit-comparison-metric.png)

### Pour sélectionner les bases de données à surveiller

Dans la liste de base de données du panneau **Database Resource Utilization** (Utilisation des ressources des bases de données), vous pouvez rechercher des bases de données spécifiques en parcourant les différentes pages ou en tapant leur nom. Activez la case à cocher pour sélectionner la base de données.

![Recherchez les bases de données à surveiller][7]
  

## Ajouter une alerte à une ressource de pool

Vous pouvez ajouter des règles à des ressources qui envoient des messages électroniques à des personnes ou des chaînes d’alertes à des points de terminaison d’URL quand la ressource atteint un seuil d’utilisation que vous configurez.

> [AZURE.IMPORTANT]La surveillance de l’utilisation des ressources pour les pools élastiques subit un décalage d’au moins 20 minutes. La définition d’alertes de moins de 30 minutes pour les pools élastiques n’est actuellement pas prise en charge. Les alertes définies pour les pools élastiques sur une période (paramètre appelé « -WindowSize » dans les API PowerShell) de moins de 30 minutes peuvent ne pas se déclencher. Assurez-vous que les alertes que vous définissez pour les pools élastiques utilisent une période (WindowSize) de 30 minutes ou plus.

**Pour ajouter une alerte à une ressource :**

1. Cliquez sur le graphique **Utilisation des ressources** pour ouvrir le panneau **Métrique**. Cliquez sur **Ajouter une alerte**, puis remplissez les informations dans le panneau **Ajouter une règle d’alerte** (la valeur **Ressource** est automatiquement définie comme étant le pool que vous utilisez).
2. Tapez un **nom** et une **description** qui identifient l’alerte pour vous et les destinataires.
3. Choisissez une **métrique** pour l’alerte dans la liste.

    Le graphique affiche dynamiquement l’utilisation des ressources pour cette métrique pour vous aider à choisir un seuil.

4. Choisissez une **condition** (supérieur à, inférieur à, etc.) et un **seuil**.
5. Cliquez sur **OK**.



## Déplacer une base de données dans un pool élastique

Vous pouvez ajouter ou supprimer des bases de données dans un pool existant. Les bases de données peuvent se trouver dans d’autres pools. Toutefois, vous pouvez uniquement ajouter des bases de données qui se trouvent sur le même serveur logique.

1. Dans le panneau du pool, sous **Bases de données élastiques**, cliquez sur **Configurer le pool**.

    ![Cliquez sur Configurer le pool][1]

2. Dans le panneau **Configurer le pool**, cliquez sur **Ajouter au pool**.

	![Cliquez sur Ajouter au pool](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)

	
3. Dans le panneau **Ajouter des bases de données**, sélectionnez les bases de données à ajouter au pool. Puis cliquez sur **Sélectionner**.

	![Sélectionnez les bases de données à ajouter](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    Le panneau **Configurer le pool** répertorie maintenant les bases de données sélectionnées à ajouter, avec leur état défini sur **En attente**.

    ![Ajouts de pool en attente](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. Dans le panneau **Configurer le pool**, cliquez sur **Enregistrer**.

    ![Cliquez sur Enregistrer.](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## Déplacer une base de données hors d’un pool élastique

1. Dans le panneau **Configurer le pool**, sélectionnez les bases de données à supprimer.

    ![liste des bases de données](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. Cliquez sur **Supprimer du pool**.

    ![liste des bases de données](./media/sql-database-elastic-pool-manage-portal/click-remove.png)

    Le panneau **Configurer le pool** répertorie maintenant les bases de données sélectionnées à supprimer, avec leur état défini sur **En attente**.
    
    ![Afficher l’aperçu d’ajout et de suppression de base de données](./media/sql-database-elastic-pool-manage-portal/pending-removal.png)

3. Dans le panneau **Configurer le pool**, cliquez sur **Enregistrer**.

    ![Cliquez sur Enregistrer.](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## Modifier les paramètres de performances d’un pool

Lorsque vous surveillez l’utilisation des ressources d’un pool, vous pouvez constater que certains ajustements sont nécessaires. Les limites de performances ou de stockage du pool ont peut-être besoin d’être modifiées. Vous pouvez aussi vouloir modifier les paramètres des bases de données du pool. Vous pouvez modifier la configuration du pool à tout moment pour obtenir le meilleur compromis entre les performances et le coût. Pour plus d’informations, voir [Quand utiliser un pool de bases de données élastiques ?](sql-database-elastic-pool-guidance.md).

**Pour modifier les eDTU ou les limites par pool et les eDTU par base de données :**

1. Ouvrez le panneau **Configurer le pool**.

    Sous **Paramètres du pool de bases de données élastiques**, utilisez l’un ou l’autre des curseurs pour modifier les paramètres du pool.

    ![Utilisation des ressources du pool élastique](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. Lorsque le paramètre change, l’affichage indique le coût mensuel estimé de la modification.

    ![Mise à jour d’un pool et nouveau coût mensuel](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)


## Créer et gérer des tâches élastiques

Les tâches élastiques vous permettent d'exécuter des scripts Transact-SQL, quel que soit le nombre de bases de données dans le pool. Vous pouvez créer de nouvelles tâches ou gérer les tâches existantes à l’aide du portail.

![Créer et gérer des tâches élastiques][5]


Avant d’utiliser des tâches, installez les composants de tâches élastiques et indiquez vos informations d’identification. Pour en savoir plus, consultez [Vue d'ensemble des tâches de base de données élastiques](sql-database-elastic-jobs-overview.md).

Consultez [Montée en charge avec la base de données SQL Azure](sql-database-elastic-scale-introduction.md) : utilisez les outils de base de données élastique pour monter en charge, déplacer des données, exécuter des requêtes ou créer des transactions.



## Ressources supplémentaires

- [Pools élastiques de bases de données SQL](sql-database-elastic-pool.md)
- [Créer un pool de bases de données élastique avec le portail](sql-database-elastic-pool-create-csharp.md)
- [Créer un pool élastique de bases de données](sql-database-elastic-pool-create-powershell.md)
- [Créer un pool de bases de données élastique avec C#](sql-database-elastic-pool-create-csharp.md)
- [Considérations sur les prix et performances pour un pool de bases de données élastique](sql-database-elastic-pool-guidance.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png
[2]: ./media/sql-database-elastic-pool-manage-portal/basic.png
[3]: ./media/sql-database-elastic-pool-manage-portal/basic-2.png
[4]: ./media/sql-database-elastic-pool-manage-portal/basic-3.png
[5]: ./media/sql-database-elastic-pool-manage-portal/elastic-jobs.png
[6]: ./media/sql-database-elastic-pool-manage-portal/edit-metric.png
[7]: ./media/sql-database-elastic-pool-manage-portal/select-dbs.png
[8]: ./media/sql-database-elastic-pool-manage-portal/db-utilization.png
[9]: ./media/sql-database-elastic-pool-manage-portal/metric.png

<!---HONumber=AcomDC_0622_2016-->