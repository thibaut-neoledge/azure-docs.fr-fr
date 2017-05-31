---
title: "Utilisation de fonctions de fenêtre U-SQL dans des travaux Azure Data Lake Analytics | Microsoft Docs"
description: "Apprenez à utiliser les fonctions de fenêtre U-SQL. "
services: data-lake-analytics
documentationcenter: 
author: saveenr
manager: saveenr
editor: cgronlun
ms.assetid: a5e14b32-d5eb-4f4b-9258-e257359f9988
ms.service: data-lake-analytics
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: big-data`
ms.date: 12/05/2016
ms.author: edmaca
ms.translationtype: Human Translation
ms.sourcegitcommit: e72275ffc91559a30720a2b125fbd3d7703484f0
ms.openlocfilehash: 55d19a00198f1943a8196d31399c617397b4e5d2
ms.contentlocale: fr-fr
ms.lasthandoff: 05/05/2017


---
# <a name="using-u-sql-window-functions-for-azure-data-lake-analytics-jobs"></a>Utilisation des fonctions U-SQL dans les travaux Analytique Data Lake Azure
Les fonctions de fenêtre ont été intégrées à la norme SQL ISO/ANSI en 2003. U-SQL adopte un sous-ensemble de fonctions Windows comme défini par la norme ANSI SQL Standard.

Les fonctions de fenêtre sont utilisées pour faire des calculs dans les ensembles de lignes nommées *fenêtres*. Les fenêtres sont définies par la clause OVER. Les fonctions de fenêtre peuvent résoudre certains scénarios clés de façon très efficace.

Les fonctions de fenêtre sont classées dans les catégories : 

* [Fonctions de rapport d’agrégation](#reporting-aggregation-functions), telles que SUM ou AVG
* [Fonctions de classement](#ranking-functions), par exemple DENSE_RANK, ROW_NUMBER, NTILE et RANK
* [Fonctions analytiques](#analytic-functions), telles que la distribution cumulative, les centiles ou les accès à des données depuis une ligne précédente (dans le même ensemble de résultats) sans utiliser une jointure automatique

## <a name="sample-datasets"></a>Exemples de jeux de données
Ce didacticiel utilise deux jeux de données :

### <a name="the-querylog-sample-dataset"></a>Le jeu de données QueryLog
  
QueryLog présente une liste de ce que les personnes ont recherché dans le moteur de recherche. Chaque journal des requêtes inclut :
  
* Une requête : ce que recherche l’utilisateur
* Une latence : vitesse à laquelle la requête est renvoyée à l’utilisateur, en millisecondes
* Des données verticales : type de contenu qui intéresse l’utilisateur (liens web, images, vidéos).  
 
```
@querylog = 
    SELECT * FROM ( VALUES
        ("Banana"  , 300, "Image" ),
        ("Cherry"  , 300, "Image" ),
        ("Durian"  , 500, "Image" ),
        ("Apple"   , 100, "Web"   ),
        ("Fig"     , 200, "Web"   ),
        ("Papaya"  , 200, "Web"   ),
        ("Avocado" , 300, "Web"   ),
        ("Cherry"  , 400, "Web"   ),
        ("Durian"  , 500, "Web"   ) )
    AS T(Query,Latency,Vertical);
```

## <a name="the-employees-sample-dataset"></a>Exemple de jeu de données Employees
  
Le jeu de données Employé inclut les champs suivants :
  
* EmpID : ID de l’employé
* EmpName : nom de l’employé
* DeptName : nom du service 
* DeptID : ID du service
* Salary : salaire de l’employé

```
@employees = 
    SELECT * FROM ( VALUES
        (1, "Noah",   "Engineering", 100, 10000),
        (2, "Sophia", "Engineering", 100, 20000),
        (3, "Liam",   "Engineering", 100, 30000),
        (4, "Emma",   "HR",          200, 10000),
        (5, "Jacob",  "HR",          200, 10000),
        (6, "Olivia", "HR",          200, 10000),
        (7, "Mason",  "Executive",   300, 50000),
        (8, "Ava",    "Marketing",   400, 15000),
        (9, "Ethan",  "Marketing",   400, 10000) )
    AS T(EmpID, EmpName, DeptName, DeptID, Salary);
```  

## <a name="compare-window-functions-to-grouping"></a>Comparer les fonctions de fenêtre au regroupement
Les concepts de fenêtrage et de regroupement sont proches. Il peut être utile de connaître cette relation.

### <a name="use-aggregation-and-grouping"></a>Utiliser l’agrégation et le regroupement
La requête suivante utilise l’agrégation pour calculer le salaire total de tous les employés :

    @result = 
        SELECT 
            SUM(Salary) AS TotalSalary
        FROM @employees;

Le résultat est une seule ligne avec une seule colonne. 165000 $ est la somme de la valeur Salaire correspondant à la table entière. 

| TotalSalary |
| --- |
| 165000 |


L’instruction suivante utilise la clause GROUP BY pour calculer le salaire total correspondant à chaque service :

    @result=
        SELECT DeptName, SUM(Salary) AS SalaryByDept
        FROM @employees
        GROUP BY DeptName;

Les résultats sont :

| DeptName | SalaryByDept |
| --- | --- |
| Ingénierie |60000 |
| HR |30000 |
| Responsable |50000 |
| Marketing |25000 |

La somme de la colonne SalaryByDept est 165 000 $, qui correspond à la quantité dans le dernier script.

Dans ces deux cas, le nombre de lignes de sortie est inférieur à celui des lignes d’entrée :

* Sans GROUP BY, l’agrégation réduit toutes les lignes dans une seule ligne. 
* Avec GROUP BY, il y a N lignes de sortie, N étant le nombre de valeurs distinctes apparaissant dans les données.  Dans ce cas, la sortie contient quatre lignes.

### <a name="use-a-window-function"></a>Utiliser une fonction de fenêtre
La clause OVER de l’exemple suivant est vide. Ainsi, la fenêtre inclut toutes les lignes. Dans cet exemple, la somme SUM est appliquée à la clause OVER qu’elle précède.

Vous pouvez lire cette requête comme suit : « La somme des salaires sur une fenêtre contenant toutes les lignes ».

    @result=
        SELECT
            EmpName,
            SUM(Salary) OVER( ) AS SalaryAllDepts
        FROM @employees;

Contrairement à GROUP BY, il y a autant de lignes de sortie que de lignes d’entrée : 

| EmpName | TotalAllDepts |
| --- | --- |
| Noah |165000 |
| Sophia |165000 |
| Liam |165000 |
| Emma |165000 |
| Jacob |165000 |
| Olivia |165000 |
| Mason |165000 |
| Ava |165000 |
| Ethan |165000 |

La valeur 165000 (le total de tous les salaires) est placée dans chaque ligne de sortie. Ce total est calculé à partir de la « fenêtre » de toutes les lignes, et inclut donc tous les salaires. 

Les exemples qui suivent montrent comment affiner la « fenêtre » pour répertorier tous les employés, le service et le salaire total pour le service. PARTITION BY est ajouté à la clause OVER.

    @result=
    SELECT
        EmpName, DeptName,
        SUM(Salary) OVER( PARTITION BY DeptName ) AS SalaryByDept
    FROM @employees;

Les résultats sont :

| EmpName | DeptName | SalaryByDep |
| --- | --- | --- |
| Noah |Ingénierie |60000 |
| Sophia |Ingénierie |60000 |
| Liam |Ingénierie |60000 |
| Mason |Responsable |50000 |
| Emma |HR |30000 |
| Jacob |HR |30000 |
| Olivia |HR |30000 |
| Ava |Marketing |25000 |
| Ethan |Marketing |25000 |

Là encore, il existe le même nombre de lignes en entrée qu’en sortie. Toutefois, chaque ligne comporte le salaire total d’un service correspondant.

## <a name="reporting-aggregation-functions"></a>Fonctions de rapport d’agrégation
Les fonctions de fenêtre prennent également en charge les agrégats suivants :

* COUNT
* SUM
* MIN
* MAX
* MOY
* STDEV
* VAR

La syntaxe :

    <AggregateFunction>( [DISTINCT] <expression>) [<OVER_clause>]

Remarque : 

* Par défaut, les fonctions agrégées, excepté COUNT, ignorent les valeurs nulles.
* Lorsque les fonctions d’agrégation sont spécifiées avec la clause OVER, la clause ORDER BY n’est pas autorisée dans la clause OVER.

### <a name="use-sum"></a>Utiliser SUM
L’exemple suivant ajoute le total du salaire par service pour chaque ligne d’entrée :

    @result=
        SELECT 
            *,
            SUM(Salary) OVER( PARTITION BY DeptName ) AS TotalByDept
        FROM @employees;

Voici la sortie :

| EmpID | EmpName | DeptName | DeptID | Salaire | TotalByDept |
| --- | --- | --- | --- | --- | --- |
| 1 |Noah |Ingénierie |100 |10000 |60000 |
| 2 |Sophia |Ingénierie |100 |20000 |60000 |
| 3 |Liam |Ingénierie |100 |30000 |60000 |
| 7 |Mason |Responsable |300 |50000 |50000 |
| 4 |Emma |HR |200 |10000 |30000 |
| 5 |Jacob |HR |200 |10000 |30000 |
| 6 |Olivia |HR |200 |10000 |30000 |
| 8 |Ava |Marketing |400 |15000 |25000 |
| 9 |Ethan |Marketing |400 |10000 |25000 |

### <a name="use-count"></a>NOMBRE d’utilisations
L’exemple suivant ajoute un champ supplémentaire à chaque ligne pour afficher le nombre total d’employés de chaque service.

    @result =
        SELECT *, 
            COUNT(*) OVER(PARTITION BY DeptName) AS CountByDept 
        FROM @employees;

Résultat :

| EmpID | EmpName | DeptName | DeptID | Salaire | CountByDept |
| --- | --- | --- | --- | --- | --- |
| 1 |Noah |Ingénierie |100 |10000 |3 |
| 2 |Sophia |Ingénierie |100 |20000 |3 |
| 3 |Liam |Ingénierie |100 |30000 |3 |
| 7 |Mason |Responsable |300 |50000 |1 |
| 4 |Emma |HR |200 |10000 |3 |
| 5 |Jacob |HR |200 |10000 |3 |
| 6 |Olivia |HR |200 |10000 |3 |
| 8 |Ava |Marketing |400 |15000 |2 |
| 9 |Ethan |Marketing |400 |10000 |2 |

### <a name="use-min-and-max"></a>Utilisez MIN et MAX
L’exemple suivant ajoute un champ supplémentaire à chaque ligne pour afficher le salaire minimum pratiqué dans chaque service.

    @result =
        SELECT 
            *,
            MIN(Salary) OVER( PARTITION BY DeptName ) AS MinSalary
        FROM @employees;

Résultats :

| EmpID | EmpName | DeptName | DeptID | Salaire | MinSalary |
| --- | --- | --- | --- | --- | --- |
| 1 |Noah |Ingénierie |100 |10000 |10000 |
| 2 |Sophia |Ingénierie |100 |20000 |10000 |
| 3 |Liam |Ingénierie |100 |30000 |10000 |
| 7 |Mason |Responsable |300 |50000 |50000 |
| 4 |Emma |HR |200 |10000 |10000 |
| 5 |Jacob |HR |200 |10000 |10000 |
| 6 |Olivia |HR |200 |10000 |10000 |
| 8 |Ava |Marketing |400 |15000 |10000 |
| 9 |Ethan |Marketing |400 |10000 |10000 |

## <a name="ranking-functions"></a>Fonctions de classement
Les fonctions de classement renvoient une valeur de classement (valeur LONG) pour chaque ligne de chaque partition, comme défini par les clauses PARTITION BY et OVER. L’ordre du rang est contrôlé par ORDER BY dans la clause OVER.

Vous trouverez plus loin les fonctions de classement prises en charge :

* RANK
* DENSE_RANK 
* NTILE
* ROW_NUMBER

**Syntaxe :**

    [ RANK() | DENSE_RANK() | ROW_NUMBER() | NTILE(<numgroups>) ]
        OVER (
            [PARTITION BY <identifier, > …[n]]
            [ORDER BY <identifier, > …[n] [ASC|DESC]] 
    ) AS <alias>

* La clause ORDER BY est facultative pour les fonctions de classement. Si cette clause n’est pas spécifiée, U-SQL affecte des valeurs basées sur l’ordre dans lequel il lit un enregistrement, ce qui entraîne la création de valeurs non déterministes pour ROW_NUMBER, RANK, ou DENSE_RANK.
* NTILE exige une expression qui s’évalue en entier positif. Ce nombre indique le nombre de groupes en lequel chaque partition doit être divisée. Cet identificateur est utilisé uniquement avec la fonction de classement de NTILE. 

Pour en savoir plus sur la clause OVER, consultez la [référence U-SQL](http://go.microsoft.com/fwlink/p/?LinkId=691348).

ROW_NUMBER, RANK et DENSE_RANK affectent des numéros de lignes dans une fenêtre. Plutôt qu’aborder les sujets séparément, il est plus intuitif de voir comment ils répondent à la même entrée.

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank, 
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank 
    FROM @querylog;

Notez que les clauses OVER sont identiques. Résultat :

| Requête | Latence : INT | Vertical | RowNumber | RANK | DenseRank |
| --- | --- | --- | --- | --- | --- |
| Banane |300 |Image |1 |1 |1 |
| Cerise |300 |Image |2 |1 |1 |
| Durion |500 |Image |3 |3 |2 |
| Pomme |100 |Web |1 |1 |1 |
| Figue |200 |Web |2 |2 |2 |
| Papaye |200 |Web |3 |2 |2 |
| Figue |300 |Web |4 |4 |3 |
| Cerise |400 |Web |5 |5 |4 |
| Durion |500 |Web |6 |6 |5 |

### <a name="rownumber"></a>ROW_NUMBER
Dans chaque fenêtre (Verticale, Image ou Web), le nombre de lignes est incrémenté de 1, classé par latence.  

![Fonction fenêtre ROW_NUMBER de fenêtre U-SQL](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-row-number-result.png)

### <a name="rank"></a>RANK
Différent de ROW_NUMBER(), le paramètre RANK() utilise la valeur de latence spécifiée dans la clause ORDER BY de la fenêtre.

Le paramètre RANK commence par (1, 1, 3), car les deux premières valeurs de latence sont les mêmes. Puis la valeur suivante est 3, car la valeur de latence est passée à 500. L’élément essentiel, c’est que le nombre RANK passe à la valeur ROW_NUMBER suivante, même si des valeurs en double se voient appliquer le même classement. Vous pouvez voir ce schéma se répéter avec la séquence (2, 2, 4) dans la verticale Web.

![Fonction fenêtre RANK U-SQL](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-rank-result.png)

### <a name="denserank"></a>DENSE_RANK
Le paramètre DENSE_RANK est similaire au paramètre RANK, mais il ne passe pas directement à la valeur ROW_NUMBER suivante. DENSE_RANK accède au numéro suivant dans la séquence. Dans l’exemple, notez les séquences (1, 1, 2) et (2, 2, 3).

![Fonction fenêtre DENSE_RANK U-SQL](./media/data-lake-analytics-use-windowing-functions/u-sql-windowing-function-dense-rank-result.png)

### <a name="remarks"></a>Remarques
* Si le paramètre ORDER BY n’est pas spécifié, la fonction de classement est appliquée à l’ensemble de lignes sans aucun classement, ce qui entraîne un comportement non déterministe.
* Pour être sûr que les lignes renvoyées par une requête à l’aide de ROW_NUMBER respecteront exactement le même ordre avec chaque exécution, vous devez vous assurer que les conditions suivantes sont avérées.
  
  * Les valeurs de la colonne partitionnée sont uniques.
  * Les valeurs des colonnes ORDER BY sont uniques.
  * Les combinaisons de valeurs de la colonne de partition et les colonnes ORDER BY sont uniques.

### <a name="ntile"></a>NTILE
NTILE distribue les lignes d’une partition ordonnée dans un nombre spécifié de groupes. Les groupes sont numérotés à partir de un. 

L’exemple qui suit fractionne l’ensemble de lignes dans chaque partition (verticale) en quatre groupes, selon la latence, et renvoie le nombre de groupes de chaque ligne. 

La verticale Image a trois lignes, donc elle possède trois groupes. 

La verticale Web inclut six lignes, quant à elle.  Les deux lignes supplémentaires sont réparties dans les deux premiers groupes. C’est pour cette raison qu’il existe deux lignes dans les groupes 1 et 2, et seulement une ligne dans les groupes 3 et 4.  

    @result =
        SELECT 
            *,
            NTILE(4) OVER(PARTITION BY Vertical ORDER BY Latency) AS Quartile   
        FROM @querylog;

Résultats :

| Interroger | Latence | Vertical | Quartile |
| --- | --- | --- | --- |
| Banane |300 |Image |1 |
| Cerise |300 |Image |2 |
| Durion |500 |Image |3 |
| Pomme |100 |Web |1 |
| Figue |200 |Web |1 |
| Papaye |200 |Web |2 |
| Figue |300 |Web |2 |
| Cerise |400 |Web |3 |
| Durion |500 |Web |4 |

NTILE prend un paramètre (« numgroups »). Numgroups est un entier positif ou une expression constante longue qui spécifie le nombre de groupes en lequel chaque partition doit être divisée. 

* Si le nombre de lignes présentes dans la partition est divisible par le paramètre numgroups, les groupes auront tous une taille égale. 
* Si le nombre de lignes présentes d’une partition n’est pas divisible par le paramètre numgroups, les groupes auront une taille légèrement différente. Les groupes plus grands viennent avant les plus petits dans l’ordre spécifié par la clause OVER. 

Par exemple :

    100 rows divided into 4 groups: 
    [ 25, 25, 25, 25 ]

    102 rows divided into 4 groups: 
    [ 26, 26, 25, 25 ]

### <a name="top-n-records-per-partition-via-rank-denserank-or-rownumber"></a>Enregistrements Top N par Partition via RANK, DENSE_RANK ou ROW_NUMBER
De nombreux utilisateurs souhaitent uniquement sélectionner une valeur correspondant à TOP N lignes par groupe, ce qui est impossible avec la clause GROUP BY traditionnelle. 

Vous avez vu l’exemple suivant au début de la section des fonctions de classement. Elle n’affiche pas les N premiers enregistrement de chaque partition :

    @result =
    SELECT 
        *,
        ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber,
        RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;

Résultats :

| Interroger | Latence | Vertical | RANK | DenseRank | RowNumber |
| --- | --- | --- | --- | --- | --- |
| Banane |300 |Image |1 |1 |1 |
| Cerise |300 |Image |1 |1 |2 |
| Durion |500 |Image |3 |2 |3 |
| Pomme |100 |Web |1 |1 |1 |
| Figue |200 |Web |2 |2 |2 |
| Papaye |200 |Web |2 |2 |3 |
| Figue |300 |Web |4 |3 |4 |
| Cerise |400 |Web |5 |4 |5 |
| Durion |500 |Web |6 |5 |6 |

### <a name="top-n-with-dense-rank"></a>TOP N avec DENSE RANK
L’exemple suivant renvoie les trois premiers enregistrements de chaque groupe sans espace dans la numérotation de classement séquentiel des lignes de chaque partition.

    @result =
    SELECT 
        *,
        DENSE_RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS DenseRank
    FROM @querylog;

    @result = 
        SELECT *
        FROM @result
        WHERE DenseRank <= 3;

Résultats :

| Interroger | Latence | Vertical | DenseRank |
| --- | --- | --- | --- |
| Banane |300 |Image |1 |
| Cerise |300 |Image |1 |
| Durion |500 |Image |2 |
| Pomme |100 |Web |1 |
| Figue |200 |Web |2 |
| Papaye |200 |Web |2 |
| Figue |300 |Web |3 |

### <a name="top-n-with-rank"></a>TOP N avec RANK
    @result =
        SELECT 
            *,
            RANK() OVER (PARTITION BY Vertical ORDER BY Latency) AS Rank
        FROM @querylog;

    @result = 
        SELECT *
        FROM @result
        WHERE Rank <= 3;

Résultats :    

| Interroger | Latence | Vertical | RANK |
| --- | --- | --- | --- |
| Banane |300 |Image |1 |
| Cerise |300 |Image |1 |
| Durion |500 |Image |3 |
| Pomme |100 |Web |1 |
| Figue |200 |Web |2 |
| Papaye |200 |Web |2 |

### <a name="top-n-with-rownumber"></a>TOP N avec ROW_NUMBER
    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER (PARTITION BY Vertical ORDER BY Latency) AS RowNumber
        FROM @querylog;

    @result = 
        SELECT *
        FROM @result
        WHERE RowNumber <= 3;

Résultats :   

| Interroger | Latence | Vertical | RowNumber |
| --- | --- | --- | --- |
| Banane |300 |Image |1 |
| Cerise |300 |Image |2 |
| Durion |500 |Image |3 |
| Pomme |100 |Web |1 |
| Figue |200 |Web |2 |
| Papaye |200 |Web |3 |

### <a name="assign-globally-unique-row-number"></a>Affecter un numéro de ligne unique global
Il est souvent utile d’affecter un numéro unique à chaque ligne. Les fonctions de classement sont plus conviviales et efficaces que les réducteurs.

    @result =
        SELECT 
            *,
            ROW_NUMBER() OVER () AS RowNumber
        FROM @querylog;

<!-- ################################################### -->
## <a name="analytic-functions"></a>fonctions analytiques
Les fonctions analytiques sont utilisées pour comprendre les distributions de valeurs dans les fenêtres. Le scénario le plus courant pour l’utilisation de fonctions analytiques est le calcul de centiles.

**Prise en charge des fonctions de fenêtre analytique**

* CUME_DIST 
* PERCENT_RANK
* PERCENTILE_CONT
* PERCENTILE_DISC

### <a name="cumedist"></a>CUME_DIST

CUME_DIST calcule la position relative d’une valeur spécifiée dans un groupe de valeurs. Elle calcule le pourcentage de requêtes qui ont une latence inférieure ou égale à la latence de la requête actuelle dans la même verticale. 

Pour une ligne R, en supposant l’utilisation d’un ordre croissant, la valeur CUME_DIST de R correspond au nombre de lignes présentant des valeurs inférieures ou égales à la valeur de R, divisé par le nombre de lignes évaluées dans la partition. 

CUME_DIST renvoie des nombres dans la plage 0 < x < = 1.

**Syntaxe :**

    CUME_DIST() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
    ) AS <alias>

L’exemple suivant utilise la fonction CUME_DIST pour calculer le centile de latence pour chaque requête au sein d’une verticale. 

    @result=
        SELECT 
            *,
            CUME_DIST() OVER(PARTITION BY Vertical ORDER BY Latency) AS CumeDist
        FROM @querylog;

Résultats :

| Interroger | Latence | Vertical | CumeDist |
| --- | --- | --- | --- |
| Durion |500 |Image |1 |
| Banane |300 |Image |0.666666666666667 |
| Cerise |300 |Image |0.666666666666667 |
| Durion |500 |Web |1 |
| Cerise |400 |Web |0.833333333333333 |
| Figue |300 |Web |0.666666666666667 |
| Figue |200 |Web |0.5 |
| Papaye |200 |Web |0.5 |
| Pomme |100 |Web |0.166666666666667 |

Il existe six lignes dans la partition dont la clé de partition est « Web »

* Il existe six lignes présentant une valeur égale ou inférieure à 500, donc la valeur CUME_DIST est égale à 6/6=1.
* Il existe cinq lignes présentant une valeur égale ou inférieure à 400, donc la valeur CUME_DIST est égale à 5/6=0,83.
* Il existe quatre lignes présentant une valeur égale ou inférieure à 300, donc la valeur CUME_DIST est égale à 4/6=0,66.
* Il existe trois lignes présentant une valeur égale ou inférieure à 200, donc la valeur CUME_DIST est égale à 3/6=0,5. Il existe deux lignes avec la même valeur de latence.
* Il existe une ligne présentant une valeur égale ou inférieure à 100, donc la valeur CUME_DIST est égale à 1/6=0,16. 

**Notes d’utilisation :**

* Les valeurs de lien sont toujours évaluées à la même valeur de distribution cumulative.
* Les valeurs NULL sont traitées comme les valeurs les plus basses possible.
* Une clause ORDER BY est requise pour calculer CUME_DIST.
* CUME_DIST s’apparente à la fonction PERCENT_RANK

Remarque : la clause ORDER BY n’est pas autorisée si l’instruction SELECT n’est pas suivie de OUTPUT.

### <a name="percentrank"></a>PERCENT_RANK
PERCENT_RANK calcule le classement relatif d’une ligne dans un groupe de lignes. PERCENT_RANK est utilisé pour évaluer la position relative d’une valeur dans un ensemble de lignes ou une partition. La plage de valeurs retournée par PERCENT_RANK est supérieure à 0 et inférieure ou égale à 1. Contrairement à CUME_DIST, la première ligne de PERCENT_RANK est toujours 0.

** Syntaxe :**

    PERCENT_RANK() 
        OVER (
            [PARTITION BY <identifier, > …[n]]
            ORDER BY <identifier, > …[n] [ASC|DESC] 
        ) AS <alias>

**Remarques**

* La première ligne de n’importe quel jeu a une valeur PERCENT_RANK de 0.
* Les valeurs NULL sont traitées comme les valeurs les plus basses possible.
* Le paramètre PERCENT_RANK requiert une clause ORDER BY.
* CUME_DIST s’apparente à la fonction PERCENT_RANK 

L’exemple suivant utilise la fonction PERCENT_RANK. pour calculer le centile de latence pour chaque requête au sein d’une verticale. 

La clause PARTITION BY est spécifiée pour partitionner les lignes du jeu de résultats défini par la verticale. La clause ORDER BY dans la clause OVER met les lignes dans l’ordre dans chaque partition. 

La valeur retournée par la fonction PERCENT_RANK représente le classement de la latence des requêtes dans un vertical sous forme de pourcentage. 

    @result=
        SELECT 
            *,
            PERCENT_RANK() OVER(PARTITION BY Vertical ORDER BY Latency) AS PercentRank
        FROM @querylog;

Résultats :

| Requête | Latence : INT | Vertical | PercentRank |
| --- | --- | --- | --- |
| Banane |300 |Image |0 |
| Cerise |300 |Image |0 |
| Durion |500 |Image |1 |
| Pomme |100 |Web |0 |
| Figue |200 |Web |0.2 |
| Papaye |200 |Web |0.2 |
| Figue |300 |Web |0,6 |
| Cerise |400 |Web |0,8 |
| Durion |500 |Web |1 |

### <a name="percentilecont--percentiledisc"></a>PERCENTILE_CONT et PERCENTILE_DISC
Ces deux fonctions calculent un centile basé sur une distribution continue ou discrète des valeurs de colonne.

**Syntaxe :**

    [PERCENTILE_CONT | PERCENTILE_DISC] ( numeric_literal ) 
        WITHIN GROUP ( ORDER BY <identifier> [ ASC | DESC ] )
        OVER ( [ PARTITION BY <identifier,>…[n] ] ) AS <alias>

**numeric_literal** - centile à calculer. La valeur doit être comprise entre 0,0 et 1,0.

    WITHIN GROUP (ORDER BY <identifier> [ ASC | DESC ])

Spécifie une liste de valeurs numériques à trier et pour lesquelles calculer le centile. Un seul identificateur de colonne est autorisé. L’expression doit correspondre à un type numérique. Les autres types de données ne sont pas autorisés. L’ordre de tri par défaut est croissant.

    OVER ([ PARTITION BY <identifier,>…[n] ] )

Divise l’ensemble de lignes d’entrée en fonction de la clé de partition à laquelle la fonction centile est appliquée. Pour plus d’informations, voir la section RANKING du présent document.
Remarque : Toutes les valeurs null présentes dans le jeu de données sont ignorées.

**PERCENTILE_CONT** calcule un centile en fonction d’une distribution continue ou discrète de la valeur de colonne. Le résultat est interpolé et peut ne pas être égal à une des valeurs spécifiques de la colonne. 

**PERCENTILE_DISC** calcule le centile selon une répartition discrète des valeurs de colonne. Le résultat est égal à une valeur spécifique de la colonne. En d’autres termes, PERCENTILE_DISC, à l’inverse de PERCENTILE_CONT, retourne toujours une valeur réelle (entrée d’origine).

Vous pouvez voir comment les deux fonctionnent dans l’exemple ci-dessous, qui essaie de trouver la valeur médiane (centile = 0,50) de latence dans chaque Verticale

    @result = 
        SELECT 
            Vertical, 
            Query,
            PERCENTILE_CONT(0.5) 
                WITHIN GROUP (ORDER BY Latency)
                OVER ( PARTITION BY Vertical ) AS PercentileCont50,
            PERCENTILE_DISC(0.5) 
                WITHIN GROUP (ORDER BY Latency) 
                OVER ( PARTITION BY Vertical ) AS PercentileDisc50 

        FROM @querylog;

Résultats :

| Requête | Latence : INT | Vertical | PercentileCont50 | PercentilDisc50 |
| --- | --- | --- | --- | --- |
| Banane |300 |Image |300 |300 |
| Cerise |300 |Image |300 |300 |
| Durion |500 |Image |300 |300 |
| Pomme |100 |Web |250 |200 |
| Figue |200 |Web |250 |200 |
| Papaye |200 |Web |250 |200 |
| Figue |300 |Web |250 |200 |
| Cerise |400 |Web |250 |200 |
| Durion |500 |Web |250 |200 |

Pour PERCENTILE_CONT, comme les valeurs peuvent être interpolées, la valeur médiane pour le web est 250 même si aucune requête dans le site web vertical a une latence de 250 seulement. 

PERCENTILE_DISC n’interpole pas les valeurs, et par conséquent, la valeur médiane pour le Web est de 200 - c’est-à-dire une réelle valeur trouvée dans les lignes d’entrée.

## <a name="see-also"></a>Voir aussi
* [Développer des scripts U-SQL avec Data Lake Tools pour Visual Studio](data-lake-analytics-data-lake-tools-get-started.md)
* [Utiliser les didacticiels interactifs Azure Data Lake Analytics](data-lake-analytics-use-interactive-tutorials.md)
* [Prise en main du langage U-SQL Azure Data Lake Analytics](data-lake-analytics-u-sql-get-started.md)



