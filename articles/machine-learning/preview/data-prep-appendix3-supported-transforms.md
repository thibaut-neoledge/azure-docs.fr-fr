---
title: "Transformations prises en charge disponibles avec la préparation des données Azure Machine Learning | Microsoft Docs"
description: "Ce document fournit la liste complète des transformations disponibles pour la préparation de données Azure ML."
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
ms.openlocfilehash: fd3f8157e27847b99e59465063111a6d6c8c713d
ms.contentlocale: fr-fr
ms.lasthandoff: 09/25/2017

---
# <a name="supported-transforms-for-this-release"></a>Transformations prises en charge pour cette version
Cet article énumère les transformations disponibles pour cette version. 

Les transformations prises en charge pour cette version sont décrites ci-dessous.

## <a name="column-selection"></a>Sélection de colonnes 
La plupart des transformations répertoriées ci-après fonctionnent sur une ou plusieurs colonnes. Pour sélectionner plusieurs colonnes, utilisez Ctrl + clic sur chaque colonne ou MAJ + clic sur une plage de colonnes.

## <a name="transforms-from-main-menu-andor-grid-header"></a>Transformations issues du menu principal et/ou de l’en-tête de grille 
Les transformations sont accessibles dans l’option Transformations dans le menu principal. Vous pouvez également les sélectionner en cliquant avec le bouton droit de la souris sur le nom de la colonne dans la grille de données. Si plusieurs colonnes sont sélectionnées, cliquez avec le bouton droit de la souris sur l’une d’entre elles pour ouvrir le menu des transformations.

Seules les transformations valides pour le type de données sélectionné sont proposées dans ce menu. Le menu principal affiche toutes les transformations mais grise celles qui ne sont pas pertinentes pour les colonnes sélectionnées.

Vous pouvez accéder à quelques transformations contextuelles en cliquant avec le bouton droit de la souris dans une cellule. Il s’agit des transformations Copier, Remplacer et Filtrer. Celles-ci tiennent compte du type de données. Par conséquent, les options pour une colonne numérique sont différentes des options pour une colonne de chaîne.

## <a name="derive-column-by-example"></a>Dériver des colonnes par l’exemple
Cette transformation vous permet de créer une colonne comme dérivé d’une ou de plusieurs colonnes existantes. Pour ce faire, les colonnes d’entrée (sélectionnées) et l’exemple donné sont examinés pour déterminer le résultat à afficher dans la nouvelle colonne. 

Pour l’utiliser, sélectionnez une ou plusieurs colonnes. Ajoutez une nouvelle colonne dérivée (vide) par l’exemple. Saisissez un exemple de ce que vous souhaitez afficher dans la colonne dérivée (en supposant qu’elle est dérivée d’autres colonnes). La technologie « Par l’exemple » tente de renseigner toutes les autres cellules de la colonne. 

Pour obtenir des exemples complexes, il peut être utile de fournir plusieurs exemples. Pour cela, sélectionnez une autre cellule et saisissez un autre exemple.

La technologie « Par l’exemple » utilise les colonnes sélectionnées pour tenter de déterminer la signification d’un exemple. Si aucune colonne n’est sélectionnée lorsque cette transformation est appelée, toutes les cellules de la ligne concernée sont utilisées. Si vous sélectionnez uniquement les colonnes requises, vous obtiendrez des résultats plus précis.

Vous pouvez sélectionner les colonnes avant d’appeler la transformation. Une fois que l’éditeur de transformation est lancé, il est possible de voir quelles sont les colonnes sont sélectionnées comme entrées via les cases situées en haut de chaque colonne. Vous pouvez ajouter et supprimer des colonnes de la sélection en cochant ou désélectionnant les cases situées dans les en-têtes de colonne.

Pour en savoir plus sur la transformation **Dériver des colonnes par l’exemple** et obtenir d’autres exemples, consultez l’[article de référence](data-prep-derive-column-by-example.md).  

## <a name="split-column-by-example"></a>Fractionner des colonnes par l’exemple
Vous pouvez fractionner une colonne entre n autres colonnes grâce à la technologie « Par l’exemple ». Il est possible d’exécuter le fractionnement automatique sur les colonnes générées.

Pour en savoir plus sur la transformation **Fractionner des colonnes par l’exemple** et obtenir d’autres exemples, consultez l’[article de référence](data-prep-split-column-by-example.md).

## <a name="expand-json"></a>Développer JSON

La transformation **Développer JSON** vous permet d’ajouter plusieurs colonnes en développant une colonne avec le texte JSON valide.

Pour en savoir plus sur la transformation **Développer JSON** et obtenir d’autres exemples, consultez l’[article de référence](data-prep-expand-json.md).


## <a name="combine-columns-by-example"></a>Combiner des colonnes par l’exemple

Cette transformation vous permet d’ajouter une nouvelle colonne en combinant les valeurs de plusieurs colonnes. 

Pour en savoir plus sur la transformation **Combiner des colonnes par l’exemple** et obtenir d’autres exemples, consultez l’[article de référence](data-prep-combine-columns-by-example.md).


## <a name="duplicate-column"></a>Dupliquer une colonne
Cette transformation effectue une copie exacte d’une ou de plusieurs colonnes et leur donne un nouveau nom dérivé du nom des colonnes d’origine. Cette transformation prend en charge une ou plusieurs colonnes.

## <a name="text-clustering"></a>Clustering de texte 
Cette transformation est conçue pour accepter et regrouper des valeurs incohérentes qui devraient être identiques.  

Lors de l’exécution de cette transformation, les valeurs d’une colonne sont analysées pour identifier les valeurs identiques. Elles sont ensuite regroupées dans des clusters. À chaque cluster correspond une valeur canonique, qui remplace toutes les instances du cluster et toutes les valeurs d’instance. Les clusters complets peuvent être supprimés, la valeur canonique peut être modifiée. Les instances peuvent être supprimées à partir d’un cluster donné. Par ailleurs, le filtre Seuil du score de similarité utilisé pour regrouper les instances d’un cluster peut être modifié.

Par défaut, cette transformation remplace toutes les valeurs d’instance de cluster par la valeur canonique pour ce cluster, ce qui entraîne la création d’une colonne qui contient les nouvelles valeurs. Il est également possible d’ajouter le score de similarité de chaque instance dans une nouvelle colonne (que vous pouvez nommer) pour l’utiliser plus tard dans le flux de données.

## <a name="replace-values"></a>Remplacer des valeurs
Cette transformation permet de remplacer une chaîne par une autre. La chaîne source peut être une chaîne partielle ou une cellule complète. Ce remplacement peut s’appliquer à une ou plusieurs colonnes. La chaîne de recherche prend en charge la recherche de caractères spéciaux et de caractères normaux. 

## <a name="replace-na-values"></a>Remplacer des valeurs NA
Cette transformation vous permet de remplacer les différentes formes de NA (n/a, NA, null, NaN, etc.) ou les chaînes vides par la même valeur, par souci de cohérence. Ce remplacement peut s’appliquer à une ou plusieurs colonnes. Cette transformation est uniquement répertoriée lorsqu’une colonne est sélectionnée. Elle n’est pas présente dans le menu principal de la transformation si aucune colonne n’est sélectionnée.

## <a name="replace-missing-values"></a>Remplacer des valeurs manquantes
Cette transformation permet de remplacer les données manquantes par la même valeur. Ce remplacement peut s’appliquer à une ou plusieurs colonnes. Cette transformation est uniquement répertoriée lorsqu’une colonne est sélectionnée. Elle n’est pas présente dans le menu principal de la transformation si aucune colonne n’est sélectionnée.

## <a name="replace-error-values"></a>Remplacer des valeurs d’erreur
Cette transformation permet de remplacer les erreurs par la même valeur. Ce remplacement peut s’appliquer à une ou plusieurs colonnes. Cette transformation est uniquement répertoriée lorsqu’une colonne est sélectionnée. Elle n’est pas présente dans le menu principal de la transformation si aucune colonne n’est sélectionnée.

## <a name="trim-string"></a>Découper une chaîne
Cette transformation vous permet de supprimer les espaces de début et de fin (espaces, tabulations, etc.), d’une ou de plusieurs colonnes.

## <a name="adjust-precision"></a>Ajuster la précision
Cette transformation vous permet de définir le nombre de décimales à utiliser dans une colonne numérique.

## <a name="rename-column"></a>Renommer une colonne
Cette transformation vous permet de changer le nom de la colonne. Elle peut également être appelée directement dans l’en-tête de colonne en cliquant sur le nom de la colonne.

## <a name="remove-column"></a>Supprimer une colonne
Cette transformation supprime la ou les colonnes sélectionnées. 

## <a name="keep-column"></a>Conserver une colonne
Cette transformation conserve uniquement la ou les colonnes sélectionnées.

## <a name="handle-path-column"></a>Gérer la colonne des chemins d’accès
Lors de l’importation d’un fichier, une colonne de chemins d’accès est automatiquement ajoutée au jeu de données par l’Assistant. Elle contient le nom de fichier complet correspondant au chemin d’accès au jeu de données. Cette transformation ajoute ou supprime cette colonne supplémentaire du jeu de données.

## <a name="convert-field-type-to-numeric"></a>Convertir le type de champ au format Nombre
Cette transformation convertit le type de la colonne au format Nombre. Indiquez le séparateur si la colonne contient des données non entières. Par défaut, cette transformation n’affiche aucune invite de commande pour le séparateur. Utilisez l’élément de menu **Modifier** pour appeler l’éditeur. Cette modification s’applique à une ou plusieurs colonnes.

## <a name="convert-field-type-to-date"></a>Convertir le type de champ au format Date
Cette transformation convertit le type de la colonne au format Date. Un format de date/heure par défaut est utilisé. Vous pouvez le remplacer à l’aide des directives `strftime`. Il est également possible d’ajouter des valeurs d’heure avec une date fixe.

Par défaut, cette transformation n’affiche aucune invite de commande pour le format. Utilisez l’élément de menu **Modifier** pour appeler l’éditeur. Cette modification s’applique à une ou plusieurs colonnes.

Seules les dates comprises entre 9-22-1677 et 4-11-2262 peuvent être converties au format Date.

## <a name="convert-field-type-to-boolean"></a>Convertir le type de champ au format Booléen
Cette transformation convertit le type de la colonne au format True/False. Cette transformation mappe plusieurs valeurs à la valeur true ou false. Vous pouvez modifier ces mappages. Elle prend également en charge une constante pour mapper les valeurs qui n’existent pas dans les tables de mappage true/false. Cette modification s’applique à une ou plusieurs colonnes.

## <a name="convert-field-type-to-string"></a>Convertir le type de champ au format String
Cette transformation convertit le type de la colonne au format String. Cette modification s’applique à une ou plusieurs colonnes.

## <a name="convert-unix-timestamp-to-datetime"></a>Convertir l’horodatage Unix au format DateTime
Cette transformation comprend le format d’horodatage Unix, même si elle est représentée sous forme de chaîne dans les données, et la convertit correctement au format Date lors de la préparation des données.

## <a name="filter"></a>Filtrer
Cette transformation filtre les lignes en fonction des valeurs présentes dans une ou plusieurs colonnes. Les conditions du filtre dépendent du type de données de chaque colonne. Par conséquent, il est possible de filtrer les colonnes de chaîne en utilisant les conditions « Contient » ou « Ne contient pas ». Pour les colonnes numériques, vous pouvez utiliser les conditions « Supérieur à » et « Inférieur à ».

Si cette transformation est appelée depuis le menu principal ou en cliquant avec le bouton droit de la souris sur l’en-tête d’une colonne, vous pouvez dupliquer les lignes échouées dans un autre flux de données. Par conséquent, le flux de données principal affiche les lignes **incluses** dans le filtre et le nouveau flux de données contient toutes les lignes qui en sont **exclues**.

## <a name="use-first-row-as-headers"></a>Utiliser la première ligne comme en-tête
Cette transformation désigne la première ligne du jeu de données pour nommer la colonne. S’il n’y a pas de données dans la première ligne de certaines colonnes, un nom est généré automatiquement. Avec cette transformation, les données peuvent être importées à partir de la ligne 2 seulement. Selon le paramètre Ignorer les lignes, l’importation peut commencer encore plus bas dans le jeu de données. Cette transformation peut également servir à générer des noms automatiquement.

## <a name="join"></a>Join
Cette transformation est utilisée pour joindre deux flux de données. Il permet de choisir le résultat de l’association, les lignes réussies, les lignes « gauche » ou les lignes « droite » qui ont échoué.

L’Assistant Jointure est lancé à partir d’un seul flux de données qui représente une extrémité de la jointure. Il invite ensuite l’utilisateur à sélectionner un autre flux de données pour désigner l’autre côté de la jointure. Une fois que les deux flux ont été sélectionnés, l’Assistant demande à ce qu’une seule colonne de chaque côté de la jointure soit sélectionnée. Si la jointure a besoin de plus d’une colonne, créez une colonne dérivée avant de lancer l’Assistant pour créer une colonne (concaténée) à utiliser pour la jointure. L’Assistant propose des suggestions et, éventuellement, génère automatiquement la colonne dérivée.

Une fois la jointure terminée, un diagramme Sankey de la jointure est affiché. La largeur des lignes représente le nombre de lignes qui parcourt cette partie du flux de la jointure. Le volet droit permet de sélectionner les lignes réussies et les lignes gauche ou droite qui ont échoué. Vous pouvez également ne choisir qu’une seule branche.

## <a name="append-rows"></a>Ajouter des lignes
Cette transformation ajoute des données à partir d’un autre flux de données dans le flux actuel. Elle mappe les colonnes par position pour ajouter les nouvelles lignes à la fin.

## <a name="append-columns"></a>Ajouter des colonnes
Cette transformation ajoute de nouvelles colonnes à partir d’un autre flux de données au flux actuel. Elle ajoute les nouvelles colonnes à droite et ne tente pas d’aligner les données.

## <a name="summarize"></a>Agréger
Cette transformation sélectionne une ou plusieurs colonnes et calcule les agrégats pour cette combinaison de valeurs uniques.

Agrégats pris en charge : COUNT, SUM, MIN, MAX, MEAN, VARIANCE, STANDARD, DEVIATION. La liste des agrégats pour une colonne donnée est filtrée en fonction des agrégats qui s’appliquent à ce type de données. Les agrégats qui ne s’appliquent pas sont grisés. Par exemple, il n’est pas possible de calculer la moyenne d’une colonne de chaîne, mais il est possible de calculer les valeurs minimale et maximale.

Cette transformation est analogue à la requête GROUP BY dans ANSI-SQL

Une fois l’éditeur disponible, les colonnes à agréger y sont affichés. Ce volet est hiérarchique, il est donc possible de faire des agrégats imbriqués. Le volet de l’éditeur situé en haut à droite permet de sélectionner l’agrégat à appliquer à une colonne. Une colonne peut être agrégée une ou plusieurs fois. Lorsqu’au moins un agrégat est sélectionné, la grille située en bas à droite affiche un aperçu des données sous leur forme agrégée. 

## <a name="remove-duplicates"></a>Supprimer les doublons
Cette transformation supprime toute la ligne qui contient des valeurs en double. Les valeurs en double sont définies par une ou plusieurs colonnes sélectionnées lors de l’appel de la transformation ou de l’utilisation de l’éditeur. Si aucune colonne n’est choisie, les lignes supprimées sont celles qui contiennent uniquement des valeurs identiques.

## <a name="sort"></a>Trier
Cette transformation trie les données d’une ou de plusieurs colonnes par ordre croissant (par défaut) ou décroissant (peut être modifié dans l’éditeur).

Cette transformation est analogue à la requête ORDER BY dans ANSI-SQL.

## <a name="output-transforms"></a>Transformations de sortie
Les transformations ci-après génèrent des données. Plusieurs blocs d’écriture peuvent être créés dans un seul et même flux pour écrire les données à différentes étapes du flux.

### <a name="write-to-csv"></a>Écriture des données au format CSV
Cette transformation écrit les données au format CSV à partir de la position actuelle dans le flux de données.  Il permet de contrôler l’emplacement (local ou distant) et les différents paramètres du fichier.

### <a name="write-to-parquet"></a>Écriture des données au format Parquet
Cette transformation écrit les données au format Parquet à partir de la position actuelle dans le flux de données. Il permet de contrôler l’emplacement (local ou distant) et les différents paramètres du fichier.

## <a name="script-based-transforms"></a>Transformations basées sur le script
Les transformations suivantes utilisent toutes un script (Python) pour exécuter les fonctionnalités absentes du produit principal. 
[Consultez cet article](data-prep-python-extensibility-overview.md) avant d’utiliser l’une des transformations suivantes :

### <a name="add-column-script"></a>Ajouter une colonne (Script)
Permet d’ajouter une colonne aux données à l’aide d’une expression Python. Consultez [cet article](data-prep-appendix10-sample-custom-column-transforms-python.md) pour en savoir plus.

### <a name="advanced-filter-script"></a>Filtre avancé (Script)
Permet d’écrire un filtre Python au niveau de la ligne. Consultez [cet article](data-prep-appendix6-sample-filter-expressions-python.md) pour en savoir plus.

### <a name="transform-dataflow-script"></a>Transformation d’un flux de données (Script)
Permet d’appliquer Python au jeu de données.

Pour en savoir plus, consultez [cet article](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="transform-dataflow-script"></a>Transformation d’un flux de données (Script)
Permet d’appliquer Python à une partition entière de données.

Pour en savoir plus, consultez [cet article](data-prep-appendix7-sample-transform-data-flow-python.md).

### <a name="write-dataflow-script"></a>Écriture d’un flux de données (Script)
Permet d’appliquer Python lors de l’écriture du jeu de données. Pour en savoir plus, consultez [l’annexe 10](data-prep-appendix9-sample-destination-connections-python.md).




