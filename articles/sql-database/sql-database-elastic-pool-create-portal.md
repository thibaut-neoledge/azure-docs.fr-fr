<properties
	pageTitle="Créer des pools de base de données élastique évolutive| Microsoft Azure"
	description="Comment ajouter un pool de base de données élastique évolutive à votre configuration de base de données SQL pour faciliter l’administration et le partage des ressources entre plusieurs bases de données."
	keywords="base de données évolutive, configuration de la base de données"
	services="sql-database"
	documentationCenter=""
	authors="jeffgoll"
	manager="jeffreyg"
	editor=""/>

<tags
	ms.service="sql-database"
	ms.devlang="NA"
	ms.date="03/24/2016"
	ms.author="jeffreyg"
	ms.workload="data-management"
	ms.topic="get-started-article"
	ms.tgt_pltfrm="NA"/>


# Créer un pool de base de données élastique évolutif pour les bases de données SQL avec le portail Azure

> [AZURE.SELECTOR]
- [Portail Azure](sql-database-elastic-pool-create-portal.md)
- [PowerShell](sql-database-elastic-pool-create-powershell.md)
- [C#](sql-database-elastic-pool-create-csharp.md)

Cet article vous montre comment créer un [pool de base de données élastique](sql-database-elastic-pool.md) évolutif avec le [portail Azure](https://portal.azure.com/). Vous pouvez créer un pool de deux façons. Vous pouvez le faire à partir de zéro si vous connaissez la configuration de pool souhaitée ou si vous commencez par une recommandation issue du service. La base de données SQL est dotée d’une intelligence intégrée qui recommande une configuration de pool si cela semble plus économique pour vous en fonction de la dernière télémétrie d’utilisation de vos bases de données.

Vous pouvez ajouter plusieurs pools à un serveur, mais il est impossible d’ajouter des bases de données de différents serveurs dans le même pool. Pour créer un pool, vous devez disposer au moins d’une base de données sur un serveur V12. Si vous en êtes dépourvu, consultez l’article [Créer votre première base de données SQL Azure](sql-database-get-started.md). Vous pouvez créer un pool avec une seule base de données, mais les pools ne sont rentables que s’ils comportent plusieurs bases de données. Consultez l’article [Considérations sur les prix et performances pour un pool de bases de données élastique](sql-database-elastic-pool-guidance.md).

> [AZURE.NOTE] Les pools sont uniquement disponibles avec des serveurs de base de données SQL V12. Si vous avez des bases de données sur un serveur V11, vous pouvez [utiliser un script PowerShell pour les identifier comme candidats à un pool](sql-database-elastic-pool-database-assessment-powershell.md) sur un serveur V12, puis [utiliser PowerShell pour mettre à niveau vers V12 et créer un pool](sql-database-upgrade-server-powershell.md) en une seule étape.

##Créer un pool
1. Dans le [portail Azure](http://portal.azure.com/), cliquez sur **Serveurs SQL**, puis sur le serveur qui contient les bases de données que vous souhaitez ajouter à un pool.
2. Cliquez sur **Nouveau pool**.

    ![Ajouter un pool à un serveur](./media/sql-database-elastic-pool-create-portal/new-pool.png)

    **OU**

    Cliquez sur le message pour afficher les pools recommandés en fonction de la télémétrie de l’historique d’utilisation de base de données, puis cliquez sur le niveau pour afficher plus de détails et personnaliser le pool. Consultez la section [Comprendre les recommandations relatives au pool](#understand-pool-recommendations) plus loin dans cette rubrique pour savoir sur quoi repose la recommandation.

    ![pool recommandé](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

    Le panneau **Pool de base de données élastique** s’affiche ; il correspond à l’emplacement où vous allez configurer le pool. Si vous avez cliqué sur **Nouveau pool** à l’étape précédente, le portail choisit un **Pool standard** sous **Niveau tarifaire**, ainsi qu’un **Nom** unique et une configuration par défaut pour le pool. Si vous avez choisi un pool recommandé, le niveau et la configuration recommandés du pool sont déjà sélectionnés, mais vous pouvez toujours les modifier.

    ![Configurer un pool élastique](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

3. Pour modifier le niveau tarifaire du pool, cliquez sur **Niveau tarifaire**, sur le niveau tarifaire de votre choix, puis sur **Sélectionner**.

    > [AZURE.IMPORTANT] Une fois que vous avez choisi le niveau tarifaire et validé vos modifications en cliquant sur **OK** à la dernière étape, vous ne pouvez plus modifier le niveau tarifaire du pool.

4. Cliquez sur **Configurer le pool**, où vous pouvez ajouter des bases de données et choisir les paramètres de ressources du pool.
5. Pour ajouter des bases de données, cliquez successivement sur **Ajouter une base de données**, les bases de données que vous souhaitez ajouter, et le bouton **Sélectionner**.

    ![Ajouter des bases de données](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    Si les bases de données que vous utilisez disposent de données de télémétrie d’historique d’utilisation suffisantes, le graphique **Utilisation estimée des eDTU et des Go** et le graphique à barres **Utilisation effective des eDTU ** sont mis à jour pour vous aider à prendre des décisions en termes de configuration. Le service peut également vous envoyer un message de recommandation pour vous aider à rectifier la taille du pool. Consultez la section [Comprendre les recommandations relatives au pool](#understand-pool-recommendations).

    ![recommandations dynamiques](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

6. Utilisez les contrôles de la page **Configurer le pool** pour explorer les paramètres et configurer votre pool en fonction des recommandations suivantes :

    ![Configurer un pool élastique](./media/sql-database-elastic-pool-create-portal/configure-performance.png)

    | Paramètre de performance | Description |
    | :--- | :--- |
    | **eDTU du pool** et **Go du pool** (par paramètre du pool)| eDTU max qui sont à la disposition de toutes les bases de données du pool et qui sont partagées par celles-ci. Les eDTU max disponibles dans un pool dépendent du niveau de tarification (niveau de service). Le paramètre **eDTU du pool** est mis en corrélation avec le stockage disponible pour le pool. Pour chaque eDTU que vous allouez au pool, vous obtenez une quantité fixe de stockage de base de données, et inversement. |
    | **eDTU min** (par paramètre de base de données)| Nombre minimal d’eDTU du pool garanti pour toutes les bases de données à tout moment. Le paramètre **eDTU min** est généralement défini sur n’importe quelle valeur comprise entre 0 et l’utilisation moyenne eDTU de l’historique par base de données. Il s’agit d’un paramètre global qui s’applique à toutes les bases de données du pool. |
    | **eDTU max** (par paramètre de base de données) | Nombre maximal d’eDTU qu’une base de données du pool peut utiliser. Vous pouvez définir ce plafond au maximum sur la valeur du paramètre **eDTU du pool**. Définissez une valeur suffisamment élevée pour le paramètre **eDTU max** par base de données afin de gérer des rafales ou des pics max en fonction du pic d’utilisation de la base de données. Une certaine allocation excessive du groupe est attendue dans la mesure où le pool prend généralement en compte des modèles de creux et de pics d’utilisation des bases de données dans lesquels toutes les bases de données ne connaissent pas simultanément des pics d’utilisation. **Exemple :** supposons que le pic d’utilisation par base de données soit défini sur 50 eDTU et que seuls 20 % des 100 bases de données du groupe connaissent simultanément un pic d’utilisation. Si le nombre d’eDTU maximal par base de données est défini sur 50 eDTU, vous pouvez envisager une allocation 5 fois plus élevée du pool et définir le paramètre **eDTU du pool** sur 1 000. Le paramètre **eDTU max** n’est pas une garantie de ressource pour une base de données. Il s’agit d’un plafond du nombre d’eDTU qui peut être atteint s’il est défini. Il s’agit d’un paramètre global qui s’applique à toutes les bases de données du pool. |

    Pour plus d’informations sur les limites de chaque niveau de service, consultez l’article [Référence du pool de base de données élastique](sql-database-elastic-pool-reference.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases). Pour obtenir des conseils détaillés sur la définition d’une taille de pool sur mesure, consultez l’article [Considérations sur les prix et performances pour un pool de bases de données élastique](sql-database-elastic-pool-guidance.md).

7. Lorsque vous avez terminé, cliquez sur **Sélectionner**, puis sur **OK** pour créer le pool.

##Comprendre les recommandations relatives au pool
Le service SQL Database évalue l’historique d’utilisation et recommande un ou plusieurs pools lorsque cela est plus rentable que d’utiliser des bases de données uniques. Chaque recommandation est configurée avec un sous-ensemble unique de bases de données du serveur qui correspond le mieux au pool. La recommandation relative au pool comprend les éléments suivants :

- Niveau de tarification du pool (De base, Standard ou Premium)
- Valeur **eDTU du pool** appropriée (également appelée eDTU max par pool)
- Paramètres **eDTU max** et **eDTU min** par base de données
- Liste des bases de données recommandées pour le pool

Le service prend en compte les 30 derniers jours de télémétrie lors de la recommandation de pools. Pour qu’une base de données soit considérée comme candidate à un pool élastique de données, elle doit exister depuis au moins 7 jours. Les bases de données qui se trouvent déjà dans un pool élastique de bases de données ne sont pas considérées comme candidates pour les recommandations de pool élastique de bases de données.

Le service évalue les besoins en ressources et la rentabilité du déplacement des bases de données uniques dans chaque niveau de service vers des pools du même niveau. Par exemple, toutes les bases de données Standard d’un serveur sont évaluées pour leur compatibilité avec un pool élastique Standard. Cela signifie que le service n'effectue aucune recommandation multiniveau telle que le déplacement d'une base de données Standard dans un pool Premium.

## Ressources supplémentaires

- [Gérer un pool élastique de base de données SQL avec le portail](sql-database-elastic-pool-manage-portal.md)
- [Gérer un pool élastique de base de données SQL avec PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [Gérer un pool élastique de base de données SQL en C#](sql-database-client-library.md)
- [Référence de base de données élastique](sql-database-elastic-pool-reference.md)

<!---HONumber=AcomDC_0330_2016-->