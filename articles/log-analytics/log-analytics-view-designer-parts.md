---
title: Concepteur de vues de Log Analytics | Microsoft Docs
description: Le Concepteur de vues de Log Analytics permet de créer dans la console OMS des vues personnalisées contenant différentes visualisations des données du référentiel OMS. Cet article fournit une référence relative aux paramètres de chacun des composants de visualisation disponibles dans vos vues personnalisées.
services: log-analytics
documentationcenter: ''
author: bwren
manager: jwhit
editor: ''

ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 09/27/2016
ms.author: bwren

---
# <a name="log-analytics-view-designer-visualization-part-reference"></a>Référence des composants de visualisation du Concepteur de vues de Log Analytics
Le Concepteur de vues de Log Analytics permet de créer dans la console OMS des vues personnalisées contenant différentes visualisations de données du référentiel OMS. Cet article fournit une référence relative aux paramètres de chacun des composants de visualisation disponibles dans vos vues personnalisées.

Autres articles disponibles concernant le Concepteur de vues :

* [Concepteur de vues](log-analytics-view-designer.md) - vue d’ensemble du Concepteur de vues et des procédures de création et de modification des vues personnalisées.
* [Référence de vignette](log-analytics-view-designer-tiles.md) - référence des paramètres pour chacune des vignettes utilisables dans vos vues personnalisées. 

Le tableau suivant décrit les différents types de vignettes disponibles dans le Concepteur de vues.  Les sections suivantes décrivent en détail chaque type de vignette et ses propriétés.

| Type de vue | Description |
|:--- |:--- |
| [Liste de requêtes](#list-of-queries-part) |Affiche une liste des requêtes de recherche dans le journal.  L’utilisateur peut cliquer sur chaque requête pour afficher ses résultats.  La vue inclut une requête par défaut, et vous pouvez cliquer sur **+ Requête** pour ajouter des requêtes supplémentaires. |
| [Nombre et liste](#number-amp-list-part) |L’en-tête affiche une valeur indiquant le nombre d’enregistrements obtenus à partir d’une requête de recherche dans le journal.  La liste affiche les dix premiers résultats d’une requête, avec des barres horizontales indiquant la valeur relative d’une colonne numérique. |
| [Deux nombres et liste](#two-numbers-amp-list-part) |L’en-tête affiche deux valeurs indiquant les nombres d’enregistrements obtenus à partir de requêtes de recherche distinctes dans le journal.  La liste affiche les dix premiers résultats d’une requête, avec des barres horizontales indiquant la valeur relative d’une colonne numérique. |
| [Anneau et liste](#donut-amp-list-part) |L’en-tête affiche un nombre résumé à partir d’une colonne de valeur dans une requête de journal.  L’anneau affiche sous forme graphique les résultats des trois premiers enregistrements. |
| [Deux chronologies et liste](#two-timelines-amp-list-part) |L’en-tête affiche les résultats de deux requêtes de journal dans le temps, sous forme d’histogrammes avec une légende affichant un nombre résumé à partir d’une colonne de valeur dans une requête de journal.  La liste affiche les dix premiers résultats d’une requête, avec des barres horizontales indiquant la valeur relative d’une colonne numérique. |
| [Informations](#information-part) |L’en-tête affiche un texte statique et un lien facultatif.  La liste affiche un ou plusieurs éléments contenant un texte statique et un titre. |
| [Graphique en courbes, légende et liste](#line-chart-callout-amp-list-part) |L’en-tête affiche un graphique en courbes avec plusieurs séries à partir d’une requête de journal dans le temps, et une légende avec une valeur de synthèse.  La liste affiche les dix premiers résultats d’une requête, avec des barres horizontales indiquant la valeur relative d’une colonne numérique. |
| [Graphique en courbes et liste](#line-chart-amp-list-part) |L’en-tête affiche un graphique en courbes avec plusieurs séries à partir d’une requête de journal dans le temps.  La liste affiche les dix premiers résultats d’une requête, avec des barres horizontales indiquant la valeur relative d’une colonne numérique. |
| [Graphique en courbes et graphique sparkline](#line-chart-amp-sparklines-part) |L’en-tête affiche un graphique en courbes avec plusieurs séries à partir d’une requête de journal dans le temps.  La liste affiche les dix premiers résultats d’une requête avec une visualisation sous forme de graphique sparkline de la valeur d’une colonne numérique sur la période sélectionnée. |

## <a name="list-of-queries-part"></a>Liste de parties de requêtes
Affiche une liste des requêtes de recherche dans le journal.  L’utilisateur peut cliquer sur chaque requête pour afficher ses résultats.  

![Liste de vues de requêtes](media/log-analytics-view-designer/view-list-queries.png)

| Paramètre | Description |
|:--- |:--- |
| **Généralités** | |
| Intitulé |Texte à afficher en haut de la vue. |
| Nouveau groupe |Sélectionnez cette option pour créer un groupe dans la vue commençant à la vue actuelle. |
| Filtres présélectionnés |Liste avec virgules de séparation des propriétés à inclure dans le volet de filtre de gauche quand l’utilisateur sélectionne une requête. |
| Mode d’affichage |Vue initiale affichée lorsque la requête est sélectionnée.  L’utilisateur peut sélectionner toute vue disponible après l’ouverture de la requête. |
| **Requêtes** | |
| Requête de recherche |Requête à exécuter. |
| Nom convivial |Nom descriptif de la requête à afficher à l’utilisateur. |

## <a name="number-&-list-part"></a>Nombre et composant de liste
L’en-tête affiche une valeur indiquant le nombre d’enregistrements obtenus à partir d’une requête de recherche dans le journal.  La liste affiche les dix premiers résultats d’une requête, avec des barres horizontales indiquant la valeur relative d’une colonne numérique.

![Liste de vues de requêtes](media/log-analytics-view-designer/view-number-list.png)

| Paramètre | Description |
|:--- |:--- |
| **Généralités** | |
| Titre du groupe |Texte à afficher en haut de la vue. |
| Nouveau groupe |Sélectionnez cette option pour créer un groupe dans la vue commençant à la vue actuelle. |
| Icône |Fichier image à afficher à côté du résultat dans l’en-tête. |
| Icône Utiliser |Sélectionnez cette option pour que l’icône s’affiche. |
| **Titre** | |
| Légende |Texte à afficher en haut de l’en-tête. |
| Requête |Requête à exécuter pour l’en-tête.  Affiche le décompte du nombre d’enregistrements retournés par la requête. |
| **Liste** | |
| Requête |Requête à exécuter pour obtenir la liste.  Les deux premières propriétés des dix premiers enregistrements dans les résultats s’affichent.  La première propriété doit être une valeur de texte et la seconde une valeur numérique.  Les barres sont créées automatiquement en fonction de la valeur relative de la colonne numérique.<br><br>Utilisez la commande de tri dans la requête pour trier les enregistrements de la liste.  L’utilisateur peut cliquer sur Afficher tout pour exécuter la requête et renvoyer tous les enregistrements. |
| Couleur |Couleur des barres. |
| Séparateur de noms et de valeurs |Délimiteur de caractère unique si vous souhaitez analyser la propriété de texte en plusieurs valeurs.  Si vous spécifiez un délimiteur, vous pouvez fournir des noms pour chaque champ, en les séparant par le même délimiteur quand dans le champ Nom.<br><br>Par exemple, imaginez une propriété appelée location incluant des valeurs telles que *Redmond-Building 41* et *Bellevue-Building12*.  Vous pouvez spécifier – en tant que séparateur de noms et de valeurs, et *City-Building* en tant que nom.  Chaque valeur est alors analysée en deux propriétés respectivement nommées *City* et *Building*. |
| Requête de navigation |Requête à exécuter lorsque l’utilisateur sélectionne un élément dans la liste.  Utilisez {selected item} pour inclure la syntaxe de l’élément sélectionné par l’utilisateur. <br><br>Par exemple, si la requête comprend une colonne nommée Computer et que la requête de navigation est {selected item}, une requête telle que Computer=”MyComputer” est exécutée quand l’utilisateur sélectionne un ordinateur.  Si la requête de navigation est Type=Event {selected item}, la requête Type=Event Computer=”MyComputer” est exécutée. |
| **Liste** |**> Titres des colonnes** |
| Nom |Texte à afficher en haut de la première colonne de la liste. |
| Valeur |Texte à afficher en haut de la deuxième colonne de la liste. |

## <a name="two-numbers-&-list-part"></a>Deux nombres et composant de liste
L’en-tête affiche deux valeurs indiquant les nombres d’enregistrements obtenus à partir de requêtes de recherche distinctes dans le journal.  La liste affiche les dix premiers résultats d’une requête, avec des barres horizontales indiquant la valeur relative d’une colonne numérique.

![Deux nombres et affichage de liste](media/log-analytics-view-designer/view-two-numbers-list.png)

| Paramètre | Description |
|:--- |:--- |
| **Généralités** | |
| Titre du groupe |Texte à afficher en haut de la vue. |
| Nouveau groupe |Sélectionnez cette option pour créer un groupe dans la vue commençant à la vue actuelle. |
| Icône |Fichier image à afficher à côté du résultat dans l’en-tête. |
| Icône Utiliser |Sélectionnez cette option pour que l’icône s’affiche. |
| **Titre** | |
| Légende |Texte à afficher en haut de l’en-tête. |
| Requête |Requête à exécuter pour l’en-tête.  Affiche le décompte du nombre d’enregistrements retournés par la requête. |
| **Deuxième titre** | |
| Légende |Texte à afficher en haut de l’en-tête. |
| Requête |Requête à exécuter pour l’en-tête.  Affiche le décompte du nombre d’enregistrements retournés par la requête. |
| **Liste** | |
| Requête |Requête à exécuter pour obtenir la liste.  Les deux premières propriétés des dix premiers enregistrements dans les résultats s’affichent.  La première propriété doit être une valeur de texte et la seconde une valeur numérique.  Les barres sont créées automatiquement en fonction de la valeur relative de la colonne numérique.<br><br>Utilisez la commande de tri dans la requête pour trier les enregistrements de la liste.  L’utilisateur peut cliquer sur Afficher tout pour exécuter la requête et renvoyer tous les enregistrements. |
| Couleur |Couleur des barres. |
| Séparateur de noms et de valeurs |Délimiteur de caractère unique si vous souhaitez analyser la propriété de texte en plusieurs valeurs.  Si vous spécifiez un délimiteur, vous pouvez fournir des noms pour chaque champ, en les séparant par le même délimiteur quand dans le champ Nom.<br><br>Par exemple, imaginez une propriété appelée location incluant des valeurs telles que Redmond-Building 41 et Bellevue-Building12.  Vous pouvez spécifier – en tant que séparateur de noms et de valeurs, et City-Building en tant que nom.  Chaque valeur est alors analysée en deux propriétés respectivement nommées City et Building. |
| Requête de navigation |Requête à exécuter lorsque l’utilisateur sélectionne un élément dans la liste.  Utilisez {selected item} pour inclure la syntaxe de l’élément sélectionné par l’utilisateur. <br><br>Par exemple, si la requête comprend une colonne nommée Computer et que la requête de navigation est {selected item}, une requête telle que Computer=”MyComputer” est exécutée quand l’utilisateur sélectionne un ordinateur.  Si la requête de navigation est Type=Event {selected item}, la requête Type=Event Computer=”MyComputer” est exécutée. |
| **Liste** |**> Titres des colonnes** |
| Nom |Texte à afficher en haut de la première colonne de la liste. |
| Valeur |Texte à afficher en haut de la deuxième colonne de la liste. |

## <a name="donut-&-list-part"></a>Anneau et composant de liste
L’en-tête affiche un nombre résumé à partir d’une colonne de valeur dans une requête de journal.  L’anneau affiche sous forme graphique les résultats des trois premiers enregistrements.

![Anneau et affichage de liste](media/log-analytics-view-designer/view-donut-list.png)

| Paramètre | Description |
|:--- |:--- |
| **Généralités** | |
| Titre du groupe |Texte à afficher en haut de la vignette. |
| Nouveau groupe |Sélectionnez cette option pour créer un groupe dans la vue commençant à la vue actuelle. |
| Icône |Fichier image à afficher à côté du résultat dans l’en-tête. |
| Icône Utiliser |Sélectionnez cette option pour que l’icône s’affiche. |
| **En-tête** | |
| Intitulé |Texte à afficher en haut de l’en-tête. |
| Sous-titre |Texte à afficher sous le titre en haut de l’en-tête. |
| **Anneau** | |
| Requête |Requête à exécuter pour obtenir l’anneau.  La première propriété doit être une valeur de texte et la seconde une valeur numérique. |
| **Anneau** |**> Centrer** |
| Texte |Texte à afficher sous la valeur contenue dans l’anneau. |
| Opération |Opération à effectuer sur la valeur de propriété à résumer en une valeur unique.<br><br>- Sum : addition des valeurs de tous les enregistrements.<br>- Percentage : pourcentage des enregistrements retournés par les valeurs figurant dans **Valeurs de résultat utilisées dans l’opération relative au centre** pour le nombre total d’enregistrements dans la requête. |
| Valeurs de résultat utilisées dans l’opération relative au centre |Vous pouvez cliquer sur le signe plus (+) pour ajouter une ou plusieurs valeurs.  Les résultats de la requête sont alors limités aux enregistrements dont vous avez spécifié les valeurs de propriété.  Si aucune valeur n’est ajoutée, tous les enregistrements sont inclus dans la requête. |
| **Liste** | |
| Requête |Requête à exécuter pour obtenir la liste.  Affiche le décompte du nombre d’enregistrements retournés par la requête. |
| Couleur |Couleur des barres. |
| Séparateur de noms et de valeurs |Délimiteur de caractère unique si vous souhaitez analyser la propriété de texte en plusieurs valeurs.  Si vous spécifiez un délimiteur, vous pouvez fournir des noms pour chaque champ, en les séparant par le même délimiteur quand dans le champ Nom.<br><br>Par exemple, imaginez une propriété appelée location incluant des valeurs telles que *Redmond-Building 41* et *Bellevue-Building12*.  Vous pouvez spécifier – en tant que séparateur de noms et de valeurs, et *City-Building* en tant que nom.  Chaque valeur est alors analysée en deux propriétés respectivement nommées *City* et *Building*. |
| Requête de navigation |Requête à exécuter lorsque l’utilisateur sélectionne un élément dans la liste.  Utilisez *{selected item}* pour inclure la syntaxe de l’élément sélectionné par l’utilisateur.<br><br>Par exemple, si la requête comprend une colonne nommée *Computer* et que la requête de navigation est *{selected item}*, une requête telle que *Computer=”MyComputer”* est exécutée quand l’utilisateur sélectionne un ordinateur.  Si la requête de navigation est *Type=Event {selected item}*, la requête *Type=Event Computer=”MyComputer”* est exécutée. |
| **Liste** |**> Titres des colonnes** |
| Nom |Texte à afficher en haut de la première colonne de la liste. |
| Valeur |Texte à afficher en haut de la deuxième colonne de la liste. |

## <a name="two-timelines-&-list-part"></a>Deux chronologies et composant de liste
L’en-tête affiche les résultats de deux requêtes de journal dans le temps, sous forme d’histogrammes avec une légende affichant un nombre résumé à partir d’une colonne de valeur dans une requête de journal.  La liste affiche les dix premiers résultats d’une requête, avec des barres horizontales indiquant la valeur relative d’une colonne numérique.   

![Deux chronologies et affichage de liste](media/log-analytics-view-designer/view-two-timelines-list.png)

| Paramètre | Description |
|:--- |:--- |
| **Généralités** | |
| Titre du groupe |Texte à afficher en haut de la vignette. |
| Nouveau groupe |Sélectionnez cette option pour créer un groupe dans la vue commençant à la vue actuelle. |
| Icône |Fichier image à afficher à côté du résultat dans l’en-tête. |
| Icône Utiliser |Sélectionnez cette option pour que l’icône s’affiche. |
| **Premier graphique** | |
| Légende |Texte à afficher dans la légende de la première série. |
| Couleur |Couleur à utiliser pour les colonnes de la première série. |
| Requête |Requête à exécuter pour la première série.  Le décompte du nombre d’enregistrements sur chaque intervalle de temps est représenté par les colonnes de graphique. |
| Opération |Opération à effectuer sur la valeur de propriété à résumer en une valeur unique pour la légende.<br><br>- Average : moyenne des valeurs de tous les enregistrements.<br>- Count : nombre de tous les enregistrements retournés par la requête.<br>- First Sample : valeur du premier intervalle inclus dans le graphique.<br>- Last Sample : valeur du dernier intervalle inclus dans le graphique.<br>- Max : valeur maximale des intervalles inclus dans le graphique. |
| Requête |Requête à exécuter pour obtenir le graphique en courbes.  La première propriété doit être une valeur de texte et la seconde une valeur numérique.  Il s’agit généralement d’une requête qui utilise le mot clé **measure** pour résumer les résultats.  Si la requête utilise le mot clé **interval**, l’axe des abscisses (X) du graphique utilise cet intervalle de temps.  Si la requête n’inclut pas le mot clé **interval**, des intervalles horaires sont utilisés pour l’axe des abscisses (X). |
| **Graphique en courbes** |**> Axe des Y** |
| Utiliser l’échelle logarithmique |Sélectionnez cette option pour utiliser une échelle logarithmique pour l’axe des ordonnées (Y). |
| Units |Spécifiez les unités à utiliser pour exprimer les valeurs retournées par la requête.  Ces informations sont utilisées pour afficher sur le graphique des étiquettes indiquant les types de valeurs et, le cas échéant, pour convertir des valeurs.  Le type d’unité spécifie la catégorie de l’unité, et définit les valeurs de type Unité actuelle disponibles.  Si vous sélectionnez une valeur pour l’option Convertir en, les valeurs numériques sont converties du type Unité actuelle au type Convertir en. |
| Étiquette personnalisée |Texte à afficher pour l’axe des X en regard de l’étiquette du type d’unité.  Si aucune étiquette n’est spécifiée, le type d’unité s’affiche. |
| **Liste** | |
| Requête |Requête à exécuter pour obtenir la liste.  Affiche le décompte du nombre d’enregistrements retournés par la requête. |
| Couleur |Couleur des barres. |
| Séparateur de noms et de valeurs |Délimiteur de caractère unique si vous souhaitez analyser la propriété de texte en plusieurs valeurs.  Si vous spécifiez un délimiteur, vous pouvez fournir des noms pour chaque champ, en les séparant par le même délimiteur quand dans le champ Nom.<br><br>Par exemple, imaginez une propriété appelée location incluant des valeurs telles que *Redmond-Building 41* et *Bellevue-Building12*.  Vous pouvez spécifier – en tant que séparateur de noms et de valeurs, et *City-Building* en tant que nom.  Chaque valeur est alors analysée en deux propriétés respectivement nommées *City* et *Building*. |
| Requête de navigation |Requête à exécuter lorsque l’utilisateur sélectionne un élément dans la liste.  Utilisez *{selected item}* pour inclure la syntaxe de l’élément sélectionné par l’utilisateur.<br><br>Par exemple, si la requête comprend une colonne nommée *Computer* et que la requête de navigation est *{selected item}*, une requête telle que *Computer=”MyComputer”* est exécutée quand l’utilisateur sélectionne un ordinateur.  Si la requête de navigation est *Type=Event {selected item}*, la requête *Type=Event Computer=”MyComputer”* est exécutée. |
| **Liste** |**> Titres des colonnes** |
| Nom |Texte à afficher en haut de la première colonne de la liste. |
| Valeur |Texte à afficher en haut de la deuxième colonne de la liste. |

## <a name="information-part"></a>Partie des informations
L’en-tête affiche un texte statique et un lien facultatif.  La liste affiche un ou plusieurs éléments contenant un texte statique et un titre.

![Vue Informations](media/log-analytics-view-designer/view-information.png)

| Paramètre | Description |
|:--- |:--- |
| **Généralités** | |
| Titre du groupe |Texte à afficher en haut de la vignette. |
| Nouveau groupe |Sélectionnez cette option pour créer un groupe dans la vue commençant à la vue actuelle. |
| Couleur |Couleur d’arrière-plan de l’en-tête. |
| **En-tête** | |
| Image |Fichier image à afficher dans l’en-tête. |
| Étiquette |Texte à afficher dans l’en-tête. |
| **En-tête** |**> Lien** |
| Étiquette |Texte du lien. |
| Url |URL du lien. |
| **Éléments d’information** | |
| Intitulé |Texte à afficher pour le titre de chaque élément. |
| Contenu |Texte à afficher pour chaque élément. |

## <a name="line-chart,-callout,-&-list-part"></a>Graphique en courbes, légende et composant de liste
L’en-tête affiche un graphique en courbes avec plusieurs séries à partir d’une requête de journal dans le temps, et une légende avec une valeur de synthèse.  La liste affiche les dix premiers résultats d’une requête, avec des barres horizontales indiquant la valeur relative d’une colonne numérique.

![Graphique en courbes, légende et affichage de liste](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Paramètre | Description |
|:--- |:--- |
| **Généralités** | |
| Titre du groupe |Texte à afficher en haut de la vignette. |
| Nouveau groupe |Sélectionnez cette option pour créer un groupe dans la vue commençant à la vue actuelle. |
| Icône |Fichier image à afficher à côté du résultat dans l’en-tête. |
| Icône Utiliser |Sélectionnez cette option pour que l’icône s’affiche. |
| **En-tête** | |
| Intitulé |Texte à afficher en haut de l’en-tête. |
| Sous-titre |Texte à afficher sous le titre en haut de l’en-tête. |
| **Graphique en courbes** | |
| Requête |Requête à exécuter pour obtenir le graphique en courbes.  La première propriété doit être une valeur de texte et la seconde une valeur numérique.  Il s’agit généralement d’une requête qui utilise le mot clé **measure** pour résumer les résultats.  Si la requête utilise le mot clé **interval**, l’axe des abscisses (X) du graphique utilise cet intervalle de temps.  Si la requête n’inclut pas le mot clé **interval**, des intervalles horaires sont utilisés pour l’axe des abscisses (X). |
| **Graphique en courbes** |**> Légende** |
| Titre de la légende |Texte à afficher au-dessus de la valeur de la légende. |
| Nom de la série |Valeur de propriété pour la série à utiliser pour la valeur de la légende.  Si aucune série n’est fournie, tous les enregistrements de la requête sont utilisés. |
| Opération |Opération à effectuer sur la valeur de propriété à résumer en une valeur unique pour la légende.<br><br>- Average : moyenne des valeurs de tous les enregistrements.<br>- Count : nombre de tous les enregistrements retournés par la requête.<br>- Last Sample : valeur du dernier intervalle inclus dans le graphique.<br>- Max : valeur maximale des intervalles inclus dans le graphique.<br>-Min : valeur minimale des intervalles inclus dans le graphique.<br>-Sum : somme des valeurs de tous les enregistrements. |
| **Graphique en courbes** |**> Axe des Y** |
| Utiliser l’échelle logarithmique |Sélectionnez cette option pour utiliser une échelle logarithmique pour l’axe des ordonnées (Y). |
| Units |Spécifiez les unités à utiliser pour exprimer les valeurs retournées par la requête.  Ces informations sont utilisées pour afficher sur le graphique des étiquettes indiquant les types de valeurs et, le cas échéant, pour convertir des valeurs.  Le type d’unité spécifie la catégorie de l’unité, et définit les valeurs de type Unité actuelle disponibles.  Si vous sélectionnez une valeur pour l’option Convertir en, les valeurs numériques sont converties du type Unité actuelle au type Convertir en. |
| Étiquette personnalisée |Texte à afficher pour l’axe des X en regard de l’étiquette du type d’unité.  Si aucune étiquette n’est spécifiée, le type d’unité s’affiche. |
| **Liste** | |
| Requête |Requête à exécuter pour obtenir la liste.  Affiche le décompte du nombre d’enregistrements retournés par la requête. |
| Couleur |Couleur des barres. |
| Séparateur de noms et de valeurs |Délimiteur de caractère unique si vous souhaitez analyser la propriété de texte en plusieurs valeurs.  Si vous spécifiez un délimiteur, vous pouvez fournir des noms pour chaque champ, en les séparant par le même délimiteur quand dans le champ Nom.<br><br>Par exemple, imaginez une propriété appelée location incluant des valeurs telles que *Redmond-Building 41* et *Bellevue-Building12*.  Vous pouvez spécifier – en tant que séparateur de noms et de valeurs, et *City-Building* en tant que nom.  Chaque valeur est alors analysée en deux propriétés respectivement nommées *City* et *Building*. |
| Requête de navigation |Requête à exécuter lorsque l’utilisateur sélectionne un élément dans la liste.  Utilisez *{selected item}* pour inclure la syntaxe de l’élément sélectionné par l’utilisateur.<br><br>Par exemple, si la requête comprend une colonne nommée *Computer* et que la requête de navigation est *{selected item}*, une requête telle que *Computer=”MyComputer”* est exécutée quand l’utilisateur sélectionne un ordinateur.  Si la requête de navigation est *Type=Event {selected item}*, la requête *Type=Event Computer=”MyComputer”* est exécutée. |
| **Liste** |**> Titres des colonnes** |
| Nom |Texte à afficher en haut de la première colonne de la liste. |
| Valeur |Texte à afficher en haut de la deuxième colonne de la liste. |

## <a name="line-chart-&-list-part"></a>Graphique en courbes et composant de liste
L’en-tête affiche un graphique en courbes avec plusieurs séries à partir d’une requête de journal dans le temps.  La liste affiche les dix premiers résultats d’une requête, avec des barres horizontales indiquant la valeur relative d’une colonne numérique.

![Affichage graphique et vue liste](media/log-analytics-view-designer/view-line-chart-callout-list.png)

| Paramètre | Description |
|:--- |:--- |
| **Généralités** | |
| Titre du groupe |Texte à afficher en haut de la vignette. |
| Nouveau groupe |Sélectionnez cette option pour créer un groupe dans la vue commençant à la vue actuelle. |
| Icône |Fichier image à afficher à côté du résultat dans l’en-tête. |
| Icône Utiliser |Sélectionnez cette option pour que l’icône s’affiche. |
| **En-tête** | |
| Intitulé |Texte à afficher en haut de l’en-tête. |
| Sous-titre |Texte à afficher sous le titre en haut de l’en-tête. |
| **Graphique en courbes** | |
| Requête |Requête à exécuter pour obtenir le graphique en courbes.  La première propriété doit être une valeur de texte et la seconde une valeur numérique.  Il s’agit généralement d’une requête qui utilise le mot clé **measure** pour résumer les résultats.  Si la requête utilise le mot clé **interval**, l’axe des abscisses (X) du graphique utilise cet intervalle de temps.  Si la requête n’inclut pas le mot clé **interval**, des intervalles horaires sont utilisés pour l’axe des abscisses (X). |
| **Graphique en courbes** |**> Axe des Y** |
| Utiliser l’échelle logarithmique |Sélectionnez cette option pour utiliser une échelle logarithmique pour l’axe des ordonnées (Y). |
| Units |Spécifiez les unités à utiliser pour exprimer les valeurs retournées par la requête.  Ces informations sont utilisées pour afficher sur le graphique des étiquettes indiquant les types de valeurs et, le cas échéant, pour convertir des valeurs.  Le type d’unité spécifie la catégorie de l’unité, et définit les valeurs de type Unité actuelle disponibles.  Si vous sélectionnez une valeur pour l’option Convertir en, les valeurs numériques sont converties du type Unité actuelle au type Convertir en. |
| Étiquette personnalisée |Texte à afficher pour l’axe des X en regard de l’étiquette du type d’unité.  Si aucune étiquette n’est spécifiée, le type d’unité s’affiche. |
| **Liste** | |
| Requête |Requête à exécuter pour obtenir la liste.  Affiche le décompte du nombre d’enregistrements retournés par la requête. |
| Couleur |Couleur des barres. |
| Séparateur de noms et de valeurs |Délimiteur de caractère unique si vous souhaitez analyser la propriété de texte en plusieurs valeurs.  Si vous spécifiez un délimiteur, vous pouvez fournir des noms pour chaque champ, en les séparant par le même délimiteur quand dans le champ Nom.<br><br>Par exemple, imaginez une propriété appelée location incluant des valeurs telles que *Redmond-Building 41* et *Bellevue-Building12*.  Vous pouvez spécifier – en tant que séparateur de noms et de valeurs, et *City-Building* en tant que nom.  Chaque valeur est alors analysée en deux propriétés respectivement nommées *City* et *Building*. |
| Requête de navigation |Requête à exécuter lorsque l’utilisateur sélectionne un élément dans la liste.  Utilisez *{selected item}* pour inclure la syntaxe de l’élément sélectionné par l’utilisateur.<br><br>Par exemple, si la requête comprend une colonne nommée *Computer* et que la requête de navigation est *{selected item}*, une requête telle que *Computer=”MyComputer”* est exécutée quand l’utilisateur sélectionne un ordinateur.  Si la requête de navigation est *Type=Event {selected item}*, la requête *Type=Event Computer=”MyComputer”* est exécutée. |
| **Liste** |**> Titres des colonnes** |
| Nom |Texte à afficher en haut de la première colonne de la liste. |
| Valeur |Texte à afficher en haut de la deuxième colonne de la liste. |

## <a name="line-chart-&-sparklines-part"></a>Graphique en courbes et composant sparkline
L’en-tête affiche un graphique en courbes avec plusieurs séries à partir d’une requête de journal dans le temps.  La liste affiche les dix premiers résultats d’une requête avec une visualisation sous forme de graphique sparkline de la valeur d’une colonne numérique sur la période sélectionnée.

![Graphique en courbes et sparkline](media/log-analytics-view-designer/view-line-chart-sparklines.png)

| Paramètre | Description |
|:--- |:--- |
| **Généralités** | |
| Titre du groupe |Texte à afficher en haut de la vignette. |
| Nouveau groupe |Sélectionnez cette option pour créer un groupe dans la vue commençant à la vue actuelle. |
| Icône |Fichier image à afficher à côté du résultat dans l’en-tête. |
| Icône Utiliser |Sélectionnez cette option pour que l’icône s’affiche. |
| **En-tête** | |
| Intitulé |Texte à afficher en haut de l’en-tête. |
| Sous-titre |Texte à afficher sous le titre en haut de l’en-tête. |
| **Graphique en courbes** | |
| Requête |Requête à exécuter pour obtenir le graphique en courbes.  La première propriété doit être une valeur de texte et la seconde une valeur numérique.  Il s’agit généralement d’une requête qui utilise le mot clé **measure** pour résumer les résultats.  Si la requête utilise le mot clé **interval**, l’axe des abscisses (X) du graphique utilise cet intervalle de temps.  Si la requête n’inclut pas le mot clé **interval**, des intervalles horaires sont utilisés pour l’axe des abscisses (X). |
| **Graphique en courbes** |**> Axe des Y** |
| Utiliser l’échelle logarithmique |Sélectionnez cette option pour utiliser une échelle logarithmique pour l’axe des ordonnées (Y). |
| Units |Spécifiez les unités à utiliser pour exprimer les valeurs retournées par la requête.  Ces informations sont utilisées pour afficher sur le graphique des étiquettes indiquant les types de valeurs et, le cas échéant, pour convertir des valeurs.  Le type d’unité spécifie la catégorie de l’unité, et définit les valeurs de type Unité actuelle disponibles.  Si vous sélectionnez une valeur pour l’option Convertir en, les valeurs numériques sont converties du type Unité actuelle au type Convertir en. |
| Étiquette personnalisée |Texte à afficher pour l’axe des X en regard de l’étiquette du type d’unité.  Si aucune étiquette n’est spécifiée, le type d’unité s’affiche. |
| **Liste** | |
| Requête |Requête à exécuter pour obtenir la liste.  Affiche le décompte du nombre d’enregistrements retournés par la requête. |
| Opération |Opération à effectuer sur chaque propriété dans la liste pour calculer les valeurs du graphique sparkline.<br><br>- Last Sample : dernière valeur de la série sur l’intervalle de temps.<br>- Max : valeur maximale de la série sur l’intervalle de temps.<br>- Min : valeur minimale de la série sur l’intervalle de temps.<br>- Sum : somme des valeurs de la série sur l’intervalle de temps.<br>- Summary : utilise la même commande de mesure que la requête dans l’en-tête. |
| Requête de navigation |Requête à exécuter lorsque l’utilisateur sélectionne un élément dans la liste.  Utilisez *{selected item}* pour inclure la syntaxe de l’élément sélectionné par l’utilisateur.<br><br>Par exemple, si la requête comprend une colonne nommée *Computer* et que la requête de navigation est *{selected item}*, une requête telle que *Computer=”MyComputer”* est exécutée quand l’utilisateur sélectionne un ordinateur.  Si la requête de navigation est *Type=Event {selected item}*, la requête *Type=Event Computer=”MyComputer”* est exécutée. |
| **Liste** |**> Titres des colonnes** |
| Nom |Texte à afficher en haut de la première colonne de la liste. |
| Valeur |Texte à afficher en haut de la deuxième colonne de la liste. |

## <a name="next-steps"></a>Étapes suivantes
* En savoir plus sur la [Recherche dans les journaux](log-analytics-log-searches.md) pour prendre en charge les requêtes dans des composants de visualisation.

<!--HONumber=Oct16_HO2-->


