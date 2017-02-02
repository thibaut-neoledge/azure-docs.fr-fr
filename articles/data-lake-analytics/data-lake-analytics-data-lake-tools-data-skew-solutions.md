---
title: "Solutions possibles pour le décalage des données dans Azure Data Lake | Microsoft Docs"
description: "Solutions possibles pour résoudre les problèmes de décalage des données dans Azure Data Lake."
services: data-lake-analytics
documentationcenter: 
author: yanancai
manager: 
editor: 
ms.assetid: 
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data
ms.date: 12/16/2016
ms.author: yanacai
translationtype: Human Translation
ms.sourcegitcommit: f2b2a2230900ad5007500f9efb4c8e7ee0aca165
ms.openlocfilehash: 1ddad5c3dc7006895ee38e06b1134857f95c3e7a


---

# <a name="possible-data-skew-solutions-for-azure-data-lake"></a>Solutions possibles pour le décalage des données dans Azure Data Lake

## <a name="what-is-data-skew-problem"></a>Qu’est-ce que le problème de décalage des données ?

Pour faire simple, le décalage des données correspond à la valeur surreprésentée. Imaginez que vous désignez 50 contrôleurs fiscaux pour traiter le retour des déclarations fiscales, soit une personne pour chaque État des États-Unis. Le contrôleur fiscal du Wyoming pourra rentrer plus tôt chez lui, puisqu’il y a moins d’habitants dans l’État du Wyoming. À l’inverse, le contrôleur fiscal de Californie devra rester plus tard au travail, car le nombre d’habitants de cet État est plus important. 
    ![Exemple de problème de décalage des données](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/data-skew-problem.png) 

Dans le cas ci-dessus, les données ne sont pas réparties équitablement entre les contrôleurs fiscaux, ce qui implique davantage d’heures de travail pour certains. Dans votre travail, vous rencontrez certainement des situations similaires à celle décrite ci-dessus. Un vertex reçoit beaucoup plus de données que les autres, ce qui ralentit son exécution, et potentiellement, l’intégralité du travail. Pire encore, le travail peut échouer, puisque le temps d’exécution des vertex est limité à 5 heures, en plus d’une limite de mémoire de 6 Go.

## <a name="how-to-troubleshoot"></a>Comment résoudre ce problème ?

L’outil vous permet de détecter si votre travail rencontre des problèmes de décalage et de résoudre ce problème grâce aux solutions ci-dessous.

### <a name="solution-1-improve-table-partition"></a>Solution 1 : Améliorer la partition de table

#### <a name="option-1-filter-the-skewed-key-value-in-advance"></a>Option 1 : Filtrer la valeur de clé décalée à l’avance

Si cela n’affecte pas votre logique métier, vous pouvez filtrer la valeur la plus fréquente à l’avance. Par exemple, il existe un grand nombre de 000-000-000 dans le GUID de colonne, mais en réalité, vous ne souhaitez pas d’agrégation de cette valeur. Dans ce cas, vous pouvez écrire “WHERE GUID != “000-000-000”” pour filtrer la valeur la plus fréquente avant l’agrégation.

#### <a name="option-2-pick-a-different-partitiondistribution-key"></a>Option 2 : Sélectionner une clé de distribution/partition différente

Dans l’exemple ci-dessus, si vous souhaitez uniquement vérifier les déclarations partout dans le pays, vous pouvez sélectionner le numéro d’ID comme clé pour améliorer la distribution des données. Parfois, sélectionner une clé de distribution/partition différente permet de distribuer les données de façon plus uniforme, mais vous devez vous assurer que cela n’affecte pas votre logique métier. Par exemple, si vous souhaitez calculer le nombre de déclarations pour chaque État, choisissez État comme clé de partition. Si le problème n’est pas résolu, consultez l’option 3.

#### <a name="option-3-add-more-partitiondistribution-key"></a>Option 3 : Ajouter plus de clés de distribution/partition

En plus d’utiliser État comme clé de partition, vous pouvez utiliser davantage de clés pour le partitionnement (p. ex. Code postal), afin de réduire les tailles de partition des données et ainsi obtenir une distribution plus égale des données.

#### <a name="option-4-round-robin-distribution"></a>Option 4 : Distribution par tourniquet (ROUND ROBIN)

Si vous ne trouvez pas de clé appropriée pour la partition et la distribution, vous pouvez essayer d’utiliser la distribution par tourniquet (ROUND ROBIN). La distribution par tourniquet (ROUND ROBIN) traite de manière égale chaque ligne et les place au hasard dans le compartiment correspondant. Dans ce cas, les données sont distribuées de façon égale, mais sans les données relatives à la localité, ce qui réduit aussi les performances de travail pour certaines opérations. De plus, si vous effectuez malgré tout une agrégation pour la clé décalée, le problème de décalage sera toujours présent. Vous pouvez en savoir plus sur l’utilisation du tourniquet (ROUND ROBIN) [ici](https://msdn.microsoft.com/en-us/library/mt706196.aspx#dis_sch).

### <a name="case-2-improve-query-plan"></a>Solution 2 : Améliorer le plan de requête

#### <a name="option-1-create-statistics-for-column"></a>Option 1 : CREATE STATISTICS pour les colonnes

U-SQL fournit l’instruction CREATE STATISTICS sur les tables, afin de donner plus d’informations à l’optimiseur de requête sur les caractéristiques des données stockées dans une table, telles que la distribution des valeurs, etc. Pour la plupart des requêtes, l’optimiseur de requête génère déjà les statistiques nécessaires à un plan de requête de haute qualité. Mais, vous aurez parfois besoin de créer des statistiques supplémentaires avec CREATE STATISTICS ou de modifier la conception de la requête pour améliorer ses performances. Vous trouverez plus d’informations [ici](https://msdn.microsoft.com/en-us/library/azure/mt771898.aspx).

**Exemple de code :**

    CREATE STATISTICS IF NOT EXISTS stats_SampleTable_date ON SampleDB.dbo.SampleTable(date) WITH FULLSCAN;

>[!NOTE]
>Veuillez noter que les données statistiques ne sont pas mises à jour automatiquement, ce qui signifie que si vous mettez à jour les données d’une table, mais que vous oubliez de recréer les statistiques, les performances de requête peuvent être moins bonnes.
>
>

#### <a name="option-2-use-skewfactor"></a>Option 2 : Utiliser SKEWFACTOR

Dans notre exemple de vérification des déclarations fiscales, si vous souhaitez additionner toutes les déclarations pour chaque État, vous n’avez pas d’autre choix que d’utiliser GROUP BY state, ce qui entraînera un problème de décalage des données. Toutefois, vous pouvez fournir un indicateur de données dans votre requête pour identifier le décalage dans les clés, afin que l’optimiseur puisse optimiser le plan d’exécution pour vous.

En règle générale, vous pouvez définir le paramètre sur 0,5 et 1. La valeur 0,5 indique un décalage peu important, tandis que la valeur 1 fait état d’un décalage conséquent. L’indicateur affecte l’optimisation du plan d’exécution de l’instruction en cours et de toutes les instructions en aval. Il est donc important que vous pensiez à ajouter l’indicateur avant l’agrégation permettant d’identifier le décalage dans les clés éventuel.

    SKEWFACTOR (columns) = x

    Provides hint that given columns have a skew factor x between 0 (no skew) and 1 (very heavy skew).

**Exemple de code :**

    //Adding SKEWFACTOR hint
    @Impressions = 
        SELECT * FROM 
        searchDM.SML.PageView(@start, @end) AS PageView
        OPTION(SKEWFACTOR(Query)=0.5)
        ;

    //Query 1 for key: Query, ClientId
    @Sessions = 
        SELECT 
            ClientId, 
            Query, 
            SUM(PageClicks) AS Clicks
        FROM 
            @Impressions
        GROUP BY
            Query, ClientId
        ;

    // Query 2 for Key: Query
    @Display = 
        SELECT * FROM @Sessions 
            INNER JOIN @Campaigns 
                ON @Sessions.Query == @Campaigns.Query
        ;   

Outre SKEWFACTOR, pour les cas de jonction de clé de décalage, si vous savez que l’autre ensemble de lignes jointes est relativement petit, vous pouvez ajouter l’indicateur ROWCOUNT dans l’instruction U-SQL avant JOIN pour indiquer à l’optimiseur que l’un de vos ensembles de lignes est petit et pour qu’il puisse choisir la stratégie de jonction de diffusion pour améliorer les performances. Toutefois, veuillez noter que l’indicateur ROWCOUNT, bien qu’utile, ne résoudra pas le problème de décalage.

    OPTION(ROWCOUNT = n)

    Identify small row set before join by giving an estimated integer row count.

**Exemple de code :**

    // Unstructured (24 hours daily log impressions)
    @Huge   = EXTRACT ClientId int, ... 
                FROM @"wasb://ads@wcentralus/2015/10/30/{*}.nif"
                ;

    // Small subset (ie: ForgetMe opt out)
    @Small  = SELECT * FROM @Huge 
                WHERE Bing.ForgetMe(x,y,z)
                OPTION(ROWCOUNT=500)
                ;

    // Result (not enough info to determine simple Broadcast join)
    @Remove = SELECT * FROM Bing.Sessions
                INNER JOIN @Small ON Sessions.Client == @Small.Client
                ;

### <a name="case-3-improve-user-defined-reducer-and-combiner"></a>Solution 3 : Améliorer le combinateur et le réducteur définis par l’utilisateur

Parfois, vous écrivez l’opérateur défini par l’utilisateur pour gérer la logique de processus complexe, et un combinateur et un réducteur bien écrits peuvent, dans certains cas, réduire les problèmes de décalage des données.

#### <a name="option-1-use-recursive-reducer-if-possible"></a>Option 1 : Utiliser le réducteur récursif si possible

Par défaut, le réducteur défini par l’utilisateur s’exécute en mode non récursif, ce qui signifie que le travail de réduction pour une clé est distribué dans un seul vertex. Mais le problème qui se présente est que si vos données sont décalées, les ensembles de données volumineux peuvent être traités dans un seul vertex et cette exécution peut prendre un certain temps. 

Pour améliorer les performances, vous pouvez ajouter un attribut dans votre code pour configurer le réducteur en mode récursif, afin que les ensembles de données volumineux puissent être distribués dans plusieurs vertex et s’exécuter en parallèle, et ainsi accélérer votre travail. 

Gardez à l’esprit que, pour faire passer un réducteur en mode non récursif au mode récursif, vous devez vous assurer que votre algorithme est un algorithme d’association. Par exemple, une somme est un algorithme d’association, tandis qu’une médiane ne l’est pas. Vous devez également vous assurer que l’entrée et la sortie du réducteur conservent le même schéma.

**Attribut de réducteur récursif :**

    [SqlUserDefinedReducer(IsRecursive = true)]

**Exemple de code :**

    [SqlUserDefinedReducer(IsRecursive = true)]
    public class TopNReducer : IReducer
    {
        public override IEnumerable<IRow> 
            Reduce(IRowset input, IUpdatableRow output)
        {
            // your reducer code here
        }
    }

#### <a name="option-2-use-row-level-combiner-mode-if-possible"></a>Option 2 : Utiliser le mode de combinateur au niveau des lignes si possible

À l’instar de l’indicateur ROWCOUNT pour les cas de jonction de clés de décalage spécifiques, le mode de combinateur essaie de distribuer les ensembles de valeurs de clés de décalage dans plusieurs vertex pour que le travail puisse être exécuté simultanément. Cela ne permet pas de résoudre le problème de décalage des données, mais peut être utile pour traiter les ensembles de valeurs de clés de décalage volumineux.

Par défaut, le mode de combinateur est défini sur Full, ce qui signifie que l’ensemble de lignes de gauche et l’ensemble de lignes de droite ne peuvent pas être séparés. Définir le mode sur Left/Right/Inner permet de joindre les lignes ; le système sépare les ensembles de lignes correspondants et les distribue dans plusieurs vertex pour les exécuter en parallèle. Toutefois, avant de configurer le mode de combinateur, soyez vigilant et assurez-vous que les ensembles de lignes correspondants peuvent être séparés.

Vous trouverez ci-dessous un exemple d’ensemble de lignes de gauche séparé. Ici, chaque ligne de sortie dépend d’une seule ligne d’entrée de gauche, et potentiellement de toutes les lignes de droite avec la même valeur de clé. Dans ce cas, si vous définissez le mode de combinateur sur Left, le système sépare le grand ensemble de lignes de gauche en plus petits et les distribue dans plusieurs vertex.
![Illustration du mode de combinateur](./media/data-lake-analytics-data-lake-tools-data-skew-solutions/combiner-mode-illustration.png) 
   
>[!NOTE]
>VEUILLEZ NOTER QUE si vous ne configurez pas le mode de combinateur correctement, la combinaison sera moins efficace et les résultats pourront être erronés !
>
>

**Attributs de combinateur :**

- [SqlUserDefinedCombiner(Mode=CombinerMode.Full)]: Every output row potentially depends on all the input rows from left and right with the same key value.

- SqlUserDefinedCombiner(Mode=CombinerMode.Left) : chaque ligne de sortie dépend d’une seule ligne d’entrée de gauche (et potentiellement de toutes les lignes de droite avec la même valeur de clé).

- qlUserDefinedCombiner(Mode=CombinerMode.Right) : chaque ligne de sortie dépend d’une seule ligne d’entrée de droite (et potentiellement de toutes les lignes de gauche avec la même valeur de clé).

- SqlUserDefinedCombiner(Mode=CombinerMode.Inner) : chaque ligne de sortie dépend d’une seule ligne d’entrée de gauche et de droite avec la même valeur de clé.

**Exemple de code :**

    [SqlUserDefinedCombiner(Mode = CombinerMode.Right)]
    public class WatsonDedupCombiner : ICombiner
    {
        public override IEnumerable<IRow> 
            Combine(IRowset left, IRowset right, IUpdatableRow output)
        {
        // your combiner code here
        }
    }


<!--HONumber=Dec16_HO3-->


