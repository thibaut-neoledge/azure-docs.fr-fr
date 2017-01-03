---
title: "Créer un pool élastique avec le portail Azure | Microsoft Docs"
description: "Comment ajouter un pool élastique évolutif à votre configuration de base de données SQL pour faciliter l’administration et le partage des ressources entre plusieurs bases de données."
keywords: "base de données évolutive, configuration de la base de données"
services: sql-database
documentationcenter: 
author: ninarn
manager: jhubbard
editor: 
ms.assetid: bf12594b-d258-40e6-a9fc-d8a8710c2d65
ms.service: sql-database
ms.custom: multiple databases
ms.devlang: NA
ms.date: 11/17/2016
ms.author: ninarn
ms.workload: data-management
ms.topic: get-started-article
ms.tgt_pltfrm: NA
translationtype: Human Translation
ms.sourcegitcommit: 16f4e287a955b787a08cc6949094bd0f5224421a
ms.openlocfilehash: 29488482f3e6c5f3dcf0eac04fe7e5e33fb832ce


---
# <a name="create-a-new-elastic-pool-with-the-azure-portal"></a>Créer un pool élastique avec le Portail Azure
> [!div class="op_single_selector"]
> * [Portail Azure](sql-database-elastic-pool-create-portal.md)
> * [PowerShell](sql-database-elastic-pool-create-powershell.md)
> * [C#](sql-database-elastic-pool-create-csharp.md)
>

Cet article vous montre comment créer un [pool élastique](sql-database-elastic-pool.md) évolutif avec le [portail Azure](https://portal.azure.com/). Vous pouvez créer un pool de deux façons. Vous pouvez le faire à partir de zéro si vous connaissez la configuration de pool souhaitée ou si vous commencez par une recommandation issue du service. La base de données SQL est dotée d’une intelligence intégrée qui recommande une configuration de pool si cela semble plus économique pour vous en fonction de la dernière télémétrie d’utilisation de vos bases de données.

Vous pouvez ajouter plusieurs pools à un serveur, mais il est impossible d’ajouter des bases de données de différents serveurs dans le même pool. Pour créer un pool, vous devez disposer au moins d’une base de données sur un serveur V12. Si vous en êtes dépourvu, consultez l’article [Créer votre première base de données SQL Azure](sql-database-get-started.md). Vous pouvez créer un pool avec une seule base de données, mais les pools ne sont rentables que s’ils comportent plusieurs bases de données. Consultez l’article fournissant des [considérations sur les prix et performances pour un pool élastique](sql-database-elastic-pool-guidance.md).

> [!NOTE]
> Les pools élastiques sont mis à la disposition générale dans toutes les régions Azure, à l’exception de l’Inde de l’Ouest, où ils sont actuellement en préversion.  Les pools élastiques seront en disposition générale dès que possible dans cette région.
>
>

## <a name="step-1-create-a-new-pool"></a>Étape 1 : créer un nouveau pool

Cet article explique comment créer un pool à partir d’un panneau **serveur** existant dans le portail. C’est le moyen le plus simple pour déplacer des bases de données existantes dans un pool.

> [!NOTE]
> Vous pouvez également créer un nouveau pool en recherchant **pool élastique SQL** sur le **Marketplace** ou en cliquant sur **+Ajouter** dans le panneau de recherche **Pools élastiques SQL**. Ce workflow d’approvisionnement de pool vous permettra d’indiquer un nouveau serveur ou un serveur existant.
>
>

1. Dans le [portail Azure](http://portal.azure.com/), sous la liste à gauche, cliquez sur **Plus de services** **>** **Serveurs SQL**, puis sur le serveur qui contient les bases de données que vous voulez ajouter à un pool.
2. Cliquez sur **Nouveau pool**.

    ![Ajouter un pool à un serveur](./media/sql-database-elastic-pool-create-portal/new-pool.png)

    **OU**

    Il se peut qu’un message indiquant que des pools élastiques recommandés sont disponibles pour le serveur (V12 uniquement) s’affiche. Cliquez sur le message pour afficher les pools recommandés en fonction de la télémétrie de l’historique d’utilisation de base de données, puis cliquez sur le niveau pour afficher plus de détails et personnaliser le pool. Consultez la section [Comprendre les recommandations relatives au pool](#understand-pool-recommendations) plus loin dans cette rubrique pour savoir sur quoi repose la recommandation.

    ![pool recommandé](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)

3. Le panneau **Pool élastique** s’affiche pour vous permettre de spécifier les paramètres de votre pool. Si vous avez cliqué sur **Nouveau pool** à l’étape précédente, le niveau tarifaire par défaut sera **Standard** et aucune base de données ne sera sélectionnée. Vous pouvez créer un pool vide ou spécifier un ensemble de bases de données existantes à partir de ce serveur, pour les déplacer vers le pool. Si vous créez un pool recommandé, le niveau tarifaire recommandé, les paramètres de performances et la liste des bases de données sont préremplis, mais vous pouvez toujours les modifier.

    ![Configurer un pool élastique](./media/sql-database-elastic-pool-create-portal/configure-elastic-pool.png)

4. Entrez un nom pour le pool élastique, ou conservez la valeur par défaut.

## <a name="step-2-choose-a-pricing-tier"></a>Étape 2 : sélectionner un niveau tarifaire

Le niveau de tarification du pool détermine les fonctionnalités disponibles pour les bases de données élastiques dans le pool, ainsi que le nombre maximal d'eDTU (eDTU MAX) et le stockage (Go) disponibles pour chaque base de données. Pour plus d’informations, voir Niveaux de service.

Pour modifier le niveau tarifaire du pool, cliquez sur **Niveau tarifaire**, sur le niveau tarifaire de votre choix, puis sur **Sélectionner**.

> [!IMPORTANT]
> Une fois que vous avez choisi le niveau tarifaire et validé vos modifications en cliquant sur **OK** à la dernière étape, vous ne pouvez plus modifier le niveau tarifaire du pool. Pour modifier le niveau de tarification d’un pool élastique existant, créez un nouveau pool élastique dans le niveau de tarification souhaité et migrez les bases de données élastiques vers ce nouveau pool.
>
>

![Sélectionner un niveau de tarification](./media/sql-database-elastic-pool-create-portal/pricing-tier.png)

## <a name="step-3-configure-the-pool"></a>Étape 3 : configurer le pool

Après avoir défini le niveau de tarification, cliquez sur Configurer le pool à l’endroit où vous ajoutez des bases de données, définissez la quantité d’eDTU et de stockage (Go de pool), et définissez les nombres minimum et maximum d’eDTU des bases de données élastiques dans le pool.

1. Cliquez sur **Configurer le pool**
2. Sélectionnez les bases de données que vous souhaitez ajouter au pool. Cette étape est facultative lors de la création du pool. Les bases de données peuvent être ajoutées après la création du pool.
    Pour ajouter des bases de données, cliquez sur **Ajouter une base de données** et sur les bases de données que vous voulez ajouter, puis sur le bouton **Sélectionner**.

    ![Ajouter des bases de données](./media/sql-database-elastic-pool-create-portal/add-databases.png)

    Si les bases de données que vous utilisez contiennent suffisamment de données de télémétrie d’historique d’utilisation, le graphique **Utilisation estimée des eDTU et des Go** et le graphique à barres **Utilisation effective des eDTU** sont mis à jour pour vous aider à prendre des décisions en termes de configuration. Le service peut également vous envoyer un message de recommandation pour vous aider à rectifier la taille du pool. Voir [Recommandations dynamiques](#dynamic-recommendations).

3. Utilisez les contrôles de la page **Configurer le pool** pour explorer les paramètres et configurer votre pool. Pour plus d’informations sur les limites de chaque niveau de service, consultez l’article décrivant les [limites des pools élastiques](sql-database-elastic-pool.md#edtu-and-storage-limits-for-elastic-pools-and-elastic-databases). Pour obtenir des conseils détaillés sur le dimensionnement approprié d’un pool, consultez l’article fournissant des [considérations sur les prix et performances pour les pools élastiques](sql-database-elastic-pool-guidance.md). Pour plus d’informations sur les paramètres du pool, consultez [Propriétés du pool élastique](sql-database-elastic-pool.md#elastic-pool-and-elastic-database-properties).

    ![Configurer un pool élastique](./media/sql-database-elastic-pool-create-portal/configure-performance.png)

4. Cliquez sur **Sélectionner** in the **Configure Pool** après avoir modifié des paramètres.
5. Cliquez sur **OK** pour créer le pool.


## <a name="understand-pool-recommendations"></a>Comprendre les recommandations relatives au pool

Le service SQL Database évalue l’historique d’utilisation et recommande un ou plusieurs pools lorsque cela est plus rentable que d’utiliser des bases de données uniques. Chaque recommandation est configurée avec un sous-ensemble unique de bases de données du serveur qui correspond le mieux au pool.

![pool recommandé](./media/sql-database-elastic-pool-create-portal/recommended-pool.png)  

La recommandation relative au pool comprend les éléments suivants :

- Niveau de tarification du pool (De base, Standard ou Premium)
- Valeur **eDTU du pool** appropriée (également appelée eDTU max par pool)
- Paramètres **eDTU max** et **eDTU min** par base de données
- Liste des bases de données recommandées pour le pool

Le service prend en compte les 30 derniers jours de télémétrie lors de la recommandation de pools. Pour qu’une base de données soit considérée comme candidate à un pool élastique, elle doit exister depuis au moins 7 jours. Les bases de données qui figurent déjà dans un pool élastique ne sont pas considérées comme candidates pour les recommandations de pool élastique.

Le service évalue les besoins en ressources et la rentabilité du déplacement des bases de données uniques dans chaque niveau de service vers des pools du même niveau. Par exemple, toutes les bases de données Standard d’un serveur sont évaluées pour leur compatibilité avec un pool élastique Standard. Cela signifie que le service n'effectue aucune recommandation multiniveau telle que le déplacement d'une base de données Standard dans un pool Premium.

### <a name="dynamic-recommendations"></a>Recommandations dynamiques

Après avoir ajouté des bases de données au pool, des recommandations seront générées de façon dynamique en fonction de l’historique d’utilisation des bases de données que vous avez sélectionnées. Ces recommandations seront affichées dans le graphique d’utilisation d’eDTU et de Go ainsi que dans la bannière de recommandation en haut du panneau **Configurer le pool** . Ces recommandations sont destinées à vous aider à créer un pool optimisé pour vos bases de données spécifiques.

![Recommandations dynamiques](./media/sql-database-elastic-pool-create-portal/dynamic-recommendation.png)

## <a name="additional-resources"></a>Ressources supplémentaires

- [Gérer un pool élastique de base de données SQL avec le portail](sql-database-elastic-pool-manage-portal.md)
- [Gérer un pool élastique de base de données SQL avec PowerShell](sql-database-elastic-pool-manage-powershell.md)
- [Gérer un pool élastique de base de données SQL en C#](sql-database-elastic-pool-manage-csharp.md)
- [Montée en charge avec Base de données SQL Azure](sql-database-elastic-scale-introduction.md)



<!--HONumber=Jan17_HO1-->


