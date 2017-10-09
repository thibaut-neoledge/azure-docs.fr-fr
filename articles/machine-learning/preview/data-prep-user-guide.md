---
title: "Guide d’utilisation détaillé pour la préparation des données Azure Machine Learning | Microsoft Docs"
description: "Ce document fournit une présentation et des détails pour la résolution des problèmes de données avec la préparation des données Azure ML."
services: machine-learning
author: euangMS
ms.author: euang
manager: lanceo
ms.reviewer: 
ms.service: machine-learning
ms.workload: data-services
ms.custom: 
ms.devlang: 
ms.topic: article
ms.date: 09/07/2017
ms.translationtype: HT
ms.sourcegitcommit: c3a2462b4ce4e1410a670624bcbcec26fd51b811
ms.openlocfilehash: 22389ba85edb119acdd21b63f2deae2d71f31373
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="data-preparation-user-guide"></a>Guide de l’utilisateur de préparation des données 
L’expérience de préparation des données fournit un grand nombre de fonctionnalités enrichies. Cet article les examine de manière détaillée.

### <a name="step-execution-history-and-caching"></a>Exécution des étapes, historique et mise en cache 
L’historique des étapes de préparation des données tient à jour une série de caches pour des raisons de performances. Si vous cliquez sur une étape et que vous accédez à un cache, l’étape n’est pas réexécutée. Si vous avez un bloc d’écriture à la fin de l’historique des étapes et que vous parcourez les étapes d’avant en arrière mais sans apporté aucune modification, l’écriture n’est pas déclenchée après la première fois. Si vous 
- Apportez des modifications au bloc d’écriture ou
- Ajoutez un nouveau bloc de transformation et le déplacez au-dessus du bloc d’écriture générant une invalidation du cache ou
- Vous modifiez les propriétés d’un bloc au-dessus du bloc d’écriture générant une invalidation du cache ou
- Sélectionnez l’actualisation d’un échantillon (invalidant ainsi la totalité du cache)

Une nouvelle écriture se produit et remplace la précédente.

### <a name="error-values"></a>Valeurs d’erreur

Il est possible que des transformations de données échouent pour une valeur d’entrée car cette valeur ne peut pas être gérée correctement. Par exemple, en cas d’opération de contrainte de type, la contrainte échoue si la valeur de la chaîne d’entrée ne peut pas être convertie en type cible spécifié. IL se peut qu’une opération de contrainte de type convertisse une colonne de type chaîne en un type numérique ou booléen. De même, vous pourriez tenter de dupliquer une colonne qui n’existe pas (résultat du déplacement de l’opération Supprimer la colonne X avant l’opération Dupliquer la colonne X).

Dans ces cas-là, la préparation des données génère une **Valeur d’erreur** comme sortie. Les valeurs d’erreur indiquent qu’une opération précédente a échoué pour la valeur donnée. En interne, elles sont traitées comme un type valeur de première classe, mais leur présence ne modifie pas le type sous-jacent d’une colonne (même si une colonne contient uniquement des valeurs d’erreur).

Les valeurs d’erreur sont faciles à identifier. Elles sont mises en surbrillance en rouge et portent la mention « Erreur ». Pour déterminer la cause de l’erreur, placez le pointeur de la souris sur une valeur d’erreur pour obtenir une description de l’échec.

Les valeurs d’erreur se propagent. Une fois qu’une valeur d’erreur s’est produite, elle se propage dans la plupart des cas en tant qu’erreur dans la plupart des opérations. Toutefois, il existe actuellement trois façons de les supprimer ou de les remplacer :

1) Remplacer
    -  Cliquez avec le bouton droit sur une colonne et sélectionnez *Replace Error Values* (Remplacer les valeurs d’erreur). Vous pouvez ensuite choisir une valeur de remplacement pour chaque valeur d’erreur figurant dans la colonne.

2) Supprimer
    - La Préparation des données inclut des filtres interactifs pour conserver ou supprimer des valeurs d’erreur.
    - Cliquez avec le bouton droit sur une colonne, puis sélectionnez *Filter Column* (Filtrer la colonne). Pour conserver ou supprimer des valeurs d’erreur, créez un filtre conditionnel avec la condition *« is error »* ou *« is not error »*.

3) Utilisez une expression Python pour opérer de manière conditionnelle sur des valeurs d’erreur. Pour plus d’informations, consultez la [section sur les extensions Python](data-prep-python-extensibility-overview.md).

### <a name="sampling"></a>Échantillonnage
Un fichier Source de données accepte des données brutes à partir d’une ou plusieurs sources, à partir du système de fichiers local ou d’un emplacement distant. Le bloc Échantillon vous permet de spécifier s’il faut utiliser un sous-ensemble des données en générant des échantillons. Le fait d’opérer sur un échantillon de données plutôt que sur un jeu de données volumineux entraîne souvent une amélioration des performances lors de l’exécution des opérations des étapes ultérieures.

Pour chaque fichier Source de données, plusieurs échantillons peuvent être générés et stockés. Cependant, un seul échantillon peut être défini comme échantillon actif. Vous pouvez créer, modifier ou supprimer des échantillons dans l’Assistant Source de données ou en modifiant le bloc Échantillon. Tout fichier de données Préparation des données qui référence une Source de données utilise implicitement l’échantillon spécifié dans le fichier Source de données.

Plusieurs stratégies d’échantillonnage sont disponibles, chacune avec différents paramètres configurables.

#### <a name="top"></a>Top
Vous pouvez appliquer cette stratégie à des fichiers locaux ou distants. Elle prend les N premières lignes (spécifiées par Count) dans la Source de données.

#### <a name="random-n"></a>Random N 
Vous pouvez appliquer cette stratégie uniquement à des fichiers locaux. Elle prend N lignes aléatoires (spécifiées par Count) dans la Source de données. Vous pouvez fournir une valeur de départ spécifique pour être certain que le même échantillon est généré si la valeur Count est identique.

#### <a name="random-"></a>Random % 
Vous pouvez appliquer cette stratégie à des fichiers locaux ou distants. Dans les deux cas, vous devez fournir une probabilité et une valeur de départ, comme avec la stratégie Random N.

Pour les échantillons de fichiers distants, vous devez fournir des paramètres supplémentaires.

- Générateur d’échantillon 
  - Sélectionnez un cluster Spark ou une cible de calcul Docker distante à utiliser pour la génération d’échantillon. Afin d’apparaître dans cette liste, la cible de calcul doit être créée au préalable pour le projet. Pour créer des cibles de calcul, suivez les étapes fournies dans la section « Créer une cible de calcul » de l’article [Utilisation du GPU dans Azure Machine Learning](how-to-use-gpu.md).
- Stockage de l’échantillon 
  - Spécifiez un emplacement de stockage intermédiaire pour stocker l’échantillon distant. Ce chemin doit être un répertoire différent de celui de l’emplacement du fichier d’entrée.

#### <a name="full-file"></a>Full file 
Vous pouvez appliquer cette stratégie uniquement à des fichiers locaux. Le fichier complet est inséré dans la Source de données. Si le fichier est trop volumineux, cette option risque de ralentir les opérations ultérieures dans l’application. Dans ce cas, il peut s’avérer plus judicieux d’utiliser une autre stratégie d’échantillonnage.


### <a name="forking-merging-and-appending"></a>Duplication, fusion et ajout

Quand vous appliquez un filtre à un jeu de données, l’opération fractionne les données en deux jeux de résultats : un qui représente les enregistrements remplissant les conditions du filtre et un autre pour les enregistrements qui échouent. Dans les deux cas, l’utilisateur peut choisir le jeu de résultats à afficher. Il peut ignorer l’autre jeu de données ou le placer dans un nouveau flux de données. Cette dernière option porte le nom de *duplication* (forking).

Pour effectuer une duplication, sélectionnez une colonne, cliquez avec le bouton droit et sélectionnez Filter Column (Filtrer la colonne).
- Sous « I Want To » (Je souhaite), sélectionnez *Keep Rows* (Conserver les lignes) pour afficher le jeu de résultats qui remplit les conditions du filtre, ou *Remove Rows* (Supprimer les lignes) pour afficher le jeu ayant échoué.
- Après « Conditions », sélectionnez *Create Dataflow Containing the Filtered Out Rows* (Créer un flux de données contenant les lignes filtrées) pour dupliquer le jeu de résultats non affiché dans un nouveau flux de données.


Cette pratique a souvent pour but de séparer un jeu de données qui nécessite une préparation supplémentaire. Après avoir mis de côté le jeu de données dupliqué, il est courant de fusionner les données avec le jeu de résultats dans le flux de données d’origine. Pour effectuer une « fusion » (l’inverse d’une opération de « duplication »), exécutez l’une des actions suivantes :
- *Ajouter des lignes*. Fusionner plusieurs flux de données verticalement (d’après les lignes). 
- *Ajouter des colonnes*. Fusionner plusieurs flux de données horizontalement (d’après les colonnes).


>[!NOTE]
>AppendColumns échoue en cas de conflit de colonne.


Après une opération de fusion, un ou plusieurs flux de données seront référencés par un flux de données source. DataPrep émet une notification dans le coin inférieur droit de l’application, sous la liste des étapes.


Toute opération sur le flux de données référencé exige que le flux de données parent actualise l’échantillon utilisé à partir du flux de données référencé. Dans ce cas, une boîte de dialogue de confirmation remplace la notification de référence de flux de données dans le coin inférieur droit. Cette boîte de dialogue confirme que vous devez actualiser le flux de données pour synchroniser les modifications apportées à tout flux de données de dépendance.

### <a name="list-of-appendices"></a>Liste des annexes 
[Annexe 2 - Sources de données prises en charge](data-prep-appendix2-supported-data-sources.md)  
[Annexe 3 - Transformations prises en charge](data-prep-appendix3-supported-transforms.md)  
[Annexe 4 - Inspecteurs pris en charge](data-prep-appendix4-supported-inspectors.md)  
[Annexe 5 - Destinations prises en charge](data-prep-appendix5-supported-destinations.md)  
[Annexe 6 - Exemples d’expressions de filtre dans Python](data-prep-appendix6-sample-filter-expressions-python.md)  
[Annexe 7 - Exemples d’expressions de flux de données de transformation dans Python](data-prep-appendix7-sample-transform-data-flow-python.md)  
[Annexe 8 - Exemples de sources de données dans Python](data-prep-appendix8-sample-source-connections-python.md)  
[Annexe 9 - Exemples de connexions de destination dans Python](data-prep-appendix9-sample-destination-connections-python.md)  
[Annexe 10 - Exemples de transformations de colonnes dans Python](data-prep-appendix10-sample-custom-column-transforms-python.md)  

