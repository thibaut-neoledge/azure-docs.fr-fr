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
	ms.date="05/02/2016"
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


Vous pouvez utiliser le portail Azure pour surveiller, gérer et configurer un pool de bases de données élastique et les bases de données dans le pool. La Base de données SQL dispose d’une intelligence intégrée qui analyse l’historique de l’utilisation de toutes les bases de données sur un serveur (qu’elles se trouvent dans des pools ou non) et recommande un pool pour les bases de données quand il se révèle plus rentable.

Le portail vous permet de modifier les paramètres de base de données et de pool, de prévisualiser les modifications, puis de valider toutes les modifications simultanément. Vous pouvez afficher un aperçu des modifications telles que l’ajout et la suppression de bases de données. L’effet potentiel sur les performances et la tarification s’affiche également.

Pour effectuer les étapes décrites dans cet article, vous aurez besoin de quelques bases de données et d’un pool. Si vous avez des bases de données existantes, consultez [Créer un pool](sql-database-elastic-pool-create-portal.md) ; si vous ne disposez pas d’une base de données, consultez le [Didacticiel sur la base de données SQL](sql-database-get-started.md).

## Sélectionner un pool à utiliser

1. Sur le [portail Azure](https://portal.azure.com), cliquez sur **Parcourir**.
2. Cliquez sur **Pools élastiques SQL**.
3. Dans la liste, cliquez sur le pool à utiliser.

## Déplacer une base de données dans un pool élastique

Vous pouvez ajouter ou supprimer des bases de données dans un pool existant. Les bases de données peuvent se trouver dans d’autres pools. Toutefois, vous pouvez uniquement ajouter des bases de données qui se trouvent sur le même serveur logique.

1. Dans le panneau du pool, sous **Bases de données élastiques**, cliquez sur **Configurer le pool**.

    ![Cliquez sur Configurer le pool][1]

2. Dans le panneau **Configurer le pool**, cliquez sur **Ajouter au pool**.

	![Cliquez sur Ajouter au pool](./media/sql-database-elastic-pool-manage-portal/add-to-pool.png)

	
3. Dans le panneau **Ajouter des bases de données**, sélectionnez les bases de données à ajouter au pool. Puis cliquez sur **Sélectionner**.

	![Sélectionnez les bases de données à ajouter](./media/sql-database-elastic-pool-manage-portal/add-databases-pool.png)

    Le panneau **Configurer un pool** contient maintenant la base de données que vous venez d’ajouter, avec son état défini sur **En attente**.

    ![Ajouts de pool en attente](./media/sql-database-elastic-pool-manage-portal/pending-additions.png)

3. Dans le panneau « Configurer le pool », cliquez sur **Enregistrer**.

    ![Cliquez sur Enregistrer.](./media/sql-database-elastic-pool-manage-portal/click-save.png)

## Déplacer une base de données hors d’un pool élastique

1. Dans le panneau **Configurer le pool**, sélectionnez les bases de données à supprimer.

    ![liste des bases de données](./media/sql-database-elastic-pool-manage-portal/select-pools-removal.png)

2. Cliquez sur **Supprimer du pool**.

    ![liste des bases de données](./media/sql-database-elastic-pool-manage-portal/remove-from-pool.png)

	Les bases de données sélectionnées s’affichent dans l’interface utilisateur en tant que « bases de données sélectionnées à supprimer ».


## Surveiller l’utilisation des ressources d’un pool


1. Sélectionnez un pool à utiliser.
2. Sous **Surveillance du pool élastique**, un graphique et des vignettes dynamiques affichent des informations importantes sur l’utilisation de votre pool.

![Surveiller un pool élastique](./media/sql-database-elastic-pool-manage-portal/monitor-elastic-pool.png)

**Pour modifier le graphique et l’affichage :**

- Cliquez sur **Modifier**.

	![Cliquez sur Modifier](./media/sql-database-elastic-pool-manage-portal/edit-resource-utlization.png)

- Dans le panneau **Modifier le graphique**, sélectionnez une nouvelle plage de temps (dernière heure, aujourd’hui ou semaine dernière), ou cliquez sur **Personnalisé** pour définir un intervalle de temps différent. Sélectionnez le type de graphique (bâtons ou linéaire), puis sélectionnez les ressources à surveiller.

	![Cliquez sur Modifier](./media/sql-database-elastic-pool-manage-portal/edit-chart.png)

- Cliquez ensuite sur **OK**.


## Ajouter une alerte à une ressource de pool

Vous pouvez ajouter des règles à des ressources qui envoient des messages électroniques à des personnes ou des chaînes d’alertes à des points de terminaison d’URL quand la ressource atteint un seuil d’utilisation que vous configurez.

**Pour ajouter une alerte à une ressource :**

1. Cliquez sur le graphique **Utilisation des ressources** pour ouvrir le panneau **Métrique**, sur **Ajouter une alerte**, puis remplissez les informations dans le panneau **Ajouter une règle d’alerte** (la valeur **Ressource** est automatiquement définie comme étant le pool que vous utilisez).
2. Tapez un **Nom** et une **Description** qui identifie l’alerte pour vous et les destinataires.
3. Choisissez une **Métrique** pour l’alerte dans la liste.

    Le graphique affiche dynamiquement l’utilisation des ressources pour cette métrique pour vous aider à choisir un seuil.

4. Choisissez une **Condition** (supérieur à, inférieur à, etc.) et un **Seuil**.
5. Cliquez sur **OK**.

## Modifier les paramètres de performances d’un pool

Lorsque vous surveillez l’utilisation des ressources d’un pool, vous pouvez constater que le pool a besoin d’eDTU supplémentaires. Ou que des bases de données individuelles dans le pool requièrent différents paramètres eDTU. Vous pouvez modifier la configuration du pool à tout moment pour obtenir le meilleur compromis entre les performances et le coût. Voir [Quand utiliser un pool de base de données élastique ?](sql-database-elastic-pool-guidance.md) pour plus d’informations.

**Pour modifier l’eDTU par pool et les eDTU par base de données :**

1. Ouvrez le panneau **Configurer le pool**.

    Sous **Paramètres du pool de bases de données élastique**, utilisez les curseurs pour modifier les paramètres du pool.

    ![Utilisation des ressources du pool élastique](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. Lorsque le paramètre change, l’affichage indique le coût mensuel estimé de la modification.

    ![Mise à jour d’un pool et nouveau coût mensuel](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)

## Afficher un aperçu des opérations de base de données

Vous pouvez prévisualiser l’ajout et la suppression de bases de données avant de valider l’action dans le panneau **Configurer le pool** :

![Afficher l’aperçu d’ajout et de suppression de base de données](./media/sql-database-elastic-pool-manage-portal/pools-tab.png).


## Créer et gérer des tâches élastiques

Les tâches élastiques vous permettent d'exécuter des scripts Transact-SQL, quel que soit le nombre de bases de données dans le pool. Avant d’utiliser des tâches, installez les composants de tâches élastiques et indiquez vos informations d’identification. Pour en savoir plus, consultez [Vue d'ensemble des tâches de base de données élastiques](sql-database-elastic-jobs-overview.md).

Consultez [Montée en charge avec Base de données SQL Azure](sql-database-elastic-scale-introduction.md) : utilisez les outils de base de données élastique pour monter en charge, déplacer des données, exécuter des requêtes ou créer des transactions.


## Ressources supplémentaires

- [Pools élastiques de bases de données SQL](sql-database-elastic-pool.md)
- [Créer un pool de bases de données élastique avec le portail](sql-database-elastic-pool-create-csharp.md)
- [Créer un pool élastique de bases de données](sql-database-elastic-pool-create-powershell.md)
- [Créer un pool de bases de données élastique avec C#](sql-database-elastic-pool-create-csharp.md)
- [Considérations sur les prix et performances pour un pool de bases de données élastique](sql-database-elastic-pool-guidance.md)


<!--Image references-->
[1]: ./media/sql-database-elastic-pool-manage-portal/configure-pool.png

<!---HONumber=AcomDC_0504_2016-->