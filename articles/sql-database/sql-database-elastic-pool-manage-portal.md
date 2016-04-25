<properties
	pageTitle="Surveiller, gérer et dimensionner un pool de bases de données élastique."
	description="Découvrez comment utiliser le portail Azure et l’intelligence intégrée de la base de données SQL pour gérer, surveiller et redimensionner un pool de bases de données élastique évolutif pour optimiser les performances de la base de données et gérer les coûts."
	keywords=""
	services="sql-database"
	documentationCenter=""
	authors="sidneyh"
	manager="jhubbard"
	editor="cgronlun"/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="04/01/2016"
	ms.author="sidneyh"
	ms.workload="data-management"
	ms.topic="article"
	ms.tgt_pltfrm="NA"/>


# Surveiller, gérer et dimensionner un pool de bases de données élastique avec le portail Azure

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-elastic-pool-manage-portal.md)
- [C#](sql-database-elastic-pool-manage-csharp.md)
- [PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [T-SQL](sql-database-elastic-pool-manage-tsql.md)


Cet article décrit comment utiliser le portail Azure pour surveiller, gérer et redimensionner un pool de bases de données élastique et les bases de données dans le pool. La Base de données SQL dispose d’une intelligence intégrée qui analyse la télémétrie de l’historique de l’utilisation et recommande de façon proactive un pool pour les bases de données quand il se révèle plus rentable. Vous pouvez également ajouter et supprimer des bases de données avant de valider les modifications et voir l’impact sur le stockage et les performances du pool.

Pour effectuer les étapes décrites dans cet article, vous aurez besoin de bases de données et d’un pool. Consultez [Créer un pool](sql-database-elastic-pool-create-portal.md) si vous avez déjà des bases de données et le [didacticiel des bases de données SQL](sql-database-get-started) si vous n’en avez pas.

**Pour choisir un pool à utiliser :**

- Dans le [portail Azure](https://portal.azure.com) cliquez sur **Parcourir**, sur **Pools élastiques SQL**, puis sur le pool que vous souhaitez utiliser dans la liste.

##Surveiller l’utilisation des ressources d’un pool
Après avoir sélectionné un pool à utiliser, sous **Surveillance du pool élastique**, un graphique et des vignettes dynamiques affichent des informations sur l’utilisation importantes pour votre pool.

![Surveiller un pool élastique](./media/sql-database-elastic-pool-manage-portal/monitor-elastic-pool.png)

**Pour modifier la plage de dates, le type de graphique (à barres ou en courbes) ou les ressources affichées :**

- Cliquez sur **Modifier**, choisissez les paramètres que vous souhaitez, puis cliquez sur **Enregistrer** pour mettre à jour le graphique.

**Pour modifier les vignettes dynamiques :**

- Cliquez sur **Ajouter des vignettes** puis sélectionnez les vignettes souhaitées dans la galerie de vignettes qui apparaît sur la gauche.

##Ajouter une alerte à une ressource de pool
Vous pouvez ajouter des règles à des ressources qui envoient des messages électroniques à des personnes ou des chaînes d’alertes à des points de terminaison d’URL quand la ressource atteint un seuil d’utilisation que vous configurez.

**Pour ajouter une alerte à une ressource :**

1. Cliquez sur le graphique **Utilisation des ressources** pour ouvrir le panneau **Métrique**, sur **Ajouter une alerte**, puis remplissez les informations dans le panneau **Ajouter une règle d’alerte** (la valeur **Ressource** est automatiquement définie comme étant le pool que vous utilisez).
2. Tapez un **Nom** et une **Description** qui identifie l’alerte pour vous et les destinataires.
3. Choisissez une **Métrique** pour l’alerte dans la liste.

    Le graphique affiche dynamiquement l’utilisation des ressources pour cette métrique pour vous aider à choisir un seuil.

4. Choisissez une **Condition** (supérieur à, inférieur à, etc.) et un **Seuil**.
5. Cliquez sur **OK**.

##Modifier l’eDTU par pool et l’eDTU par base de données
Quand vous affichez l’utilisation des ressources d’un pool, vous pouvez constater que le pool nécessite un paramètre d’eDTU différent, ou que des bases de données individuelles dans le pool nécessitent des paramètres d’eDTU différents. Vous pouvez modifier la configuration du pool à tout moment pour obtenir le meilleur compromis entre les performances et le coût. Pour plus d’informations, consultez [Considérations sur les prix et performances](sql-database-elastic-pool-guidance.md).

**Pour modifier l’eDTU par pool et l’eDTU par base de données :**

1. Cliquez sur **Configurer le pool** pour ouvrir le panneau **Configurer les performances**.

    Sous **Paramètres du pool de bases de données élastique**, un graphique affiche la tendance récente d’utilisation du stockage et des eDTU en pourcentage de capacité pour le pool.

    ![Utilisation des ressources du pool élastique](./media/sql-database-elastic-pool-manage-portal/resize-pool.png)

2. Cliquez sur un autre **eDTU du pool** et vous verrez le coût mensuel estimé de la modification que vous souhaitez apporter. Le graphique est mis à jour pour afficher les valeurs de l’utilisation prévue avec la nouvelle valeur eDTU max que vous avez sélectionnée.

    ![Mise à jour d’un pool et nouveau coût mensuel](./media/sql-database-elastic-pool-manage-portal/pool-change-edtu.png)

3. Sous **Paramètre de la base de données élastique**, un graphique à barres affiche l’utilisation d’eDTU de chaque base de données dans le pool.

4. Cliquez sur **eDTU max** pour définir la valeur maximale et sur **eDTU min** pour définir le nombre minimal d’eDTU pour les bases de données dans le pool.

    ![Mise à jour des valeurs eDTU max et eDTU min d’une base de données élastique](./media/sql-database-elastic-pool-manage-portal/change-db-edtuminmax.png)

##Ajouter et supprimer des bases de données

Après avoir créé un pool, vous pouvez ajouter des bases de données au pool ou en supprimer. Vous pouvez uniquement ajouter des bases de données sur le même serveur SQL.

**Pour ajouter des bases de données :**

1. Dans le panneau pour le pool, sous **Bases de données élastiques**, cliquez sur le lien qui indique le nombre de bases de données dans le pool.

    ![liste des bases de données](./media/sql-database-elastic-pool-manage-portal/db-listing.png)

2. Dans le panneau **Bases de données élastiques**, cliquez sur **Ajouter une base de données**, sur les bases de données que vous souhaitez ajouter et sur le bouton **Sélectionner**.

    Le panneau **Bases de données élastiques** répertorie maintenant la base de données que vous venez d’ajouter, avec **DTU moy** et l’utilisation du stockage en tant que **Taille (Go)**, ainsi qu’un état **En attente**. Les valeurs de l’utilisation du pool vous montrent maintenant ce que les valeurs **Nouveau** seront si vous enregistrez vos modifications.

    ![pool recommandé](./media/sql-database-elastic-pool-manage-portal/add-remove-databases.png)

3. Cliquez sur **Enregistrer** puis sur **OK** quand le portail vous indique que la demande a été envoyée. Le nombre de bases de données dans le pool s’affiche dans le panneau pour le pool quand l’opération est terminée.

**Pour supprimer des bases de données :**

1. Dans le panneau pour le pool, sous **Bases de données élastiques**, cliquez sur le lien qui indique le nombre de bases de données dans le pool.

    ![liste des bases de données](./media/sql-database-elastic-pool-manage-portal/db-listing.png)

2. Dans le panneau **Bases de données élastiques**, dans la liste des bases de données dans le pool, cliquez sur les bases de données que vous souhaitez supprimer, puis sur **Supprimer les bases de données**.

    Les valeurs de l’utilisation du pool vous montrent maintenant ce que les valeurs **Nouveau** seront si vous enregistrez vos modifications.

3. Cliquez sur **Enregistrer** puis sur **OK** quand le portail vous indique que la demande a été envoyée. Le nombre de bases de données dans le pool s’affiche dans le panneau pour le pool quand l’opération est terminée.

## Créer une nouvelle base de données dans un pool

Dans le panneau pour la base de données, cliquez sur **Créer une base de données**. Comme la base de données SQL est déjà configurée pour le serveur approprié et le pool, cliquez sur d’autres paramètres pour les sélectionner, puis sur **OK** pour créer une base de données dans le pool.

   ![créer des bases de données élastiques](./media/sql-database-elastic-pool-portal/create-database.png)

##Créer et gérer des tâches élastiques

Les tâches élastiques vous permettent d'exécuter des scripts Transact-SQL, quel que soit le nombre de bases de données dans le pool. Avant d’utiliser des tâches, installez les composants de tâches élastiques et indiquez vos informations d’identification. Pour en savoir plus, consultez [Vue d'ensemble des tâches de base de données élastiques](sql-database-elastic-jobs-overview.md).

## Ressources supplémentaires

- [Pools élastiques de bases de données SQL](sql-database-elastic-pool.md)
- [Créer un pool de bases de données élastique avec le portail](sql-database-elastic-pool-create-csharp.md)
- [Créer un pool élastique de bases de données](sql-database-elastic-pool-create-powershell.md)
- [Créer un pool de bases de données élastique avec C#](sql-database-elastic-pool-create-csharp.md)
- [Considérations sur les prix et performances pour un pool de bases de données élastique](sql-database-elastic-pool-guidance.md)

<!---HONumber=AcomDC_0413_2016-->