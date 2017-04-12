---
title: "Expressions régulières pour les recherches dans les journaux Log Analytics d’OMS | Microsoft Docs"
description: "Vous pouvez utiliser le mot clé RegEx pour les recherches dans les journaux Log Analytics afin de filtrer les résultats en fonction d’une expression régulière.  Cet article décrit la syntaxe de ces expressions et en fournit plusieurs exemples."
services: log-analytics
documentationcenter: 
author: bwren
manager: carmonm
editor: 
ms.service: log-analytics
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/21/2017
ms.author: bwren
translationtype: Human Translation
ms.sourcegitcommit: 785d3a8920d48e11e80048665e9866f16c514cf7
ms.openlocfilehash: 6c01fe7a791742d283505057a310891a075029ef
ms.lasthandoff: 04/12/2017


---
# <a name="using-regular-expressions-to-filter-log-searches-in-log-analytics"></a>Utilisation d’expressions régulières pour filtrer les recherches dans les journaux Log Analytics

Les [recherches dans les journaux](log-analytics-log-searches.md) vous permettent d’extraire des informations du référentiel Log Analytics.  Les [expressions de filtre](log-analytics-search-reference.md#filter-expressions) vous donnent la possibilité de filtrer les résultats de la recherche en fonction de critères spécifiques.  Le mot clé **RegEx** vous permet de spécifier une expression régulière pour ce filtre.  

Cet article décrit en détail la syntaxe des expressions régulières utilisée par Log Analytics.


## <a name="regex-keyword"></a>Mot clé RegEx

Pour utiliser le mot clé **RegEx** au sein d’une recherche dans les journaux, appliquez la syntaxe ci-après.  Pour déterminer la syntaxe de l’expression régulière proprement dite, utilisez les autres sections de cet article.

    field:Regex("Regular Expression")
    field=Regex("Regular Expression")

Par exemple, si vous souhaitez utiliser une expression régulière destinée à renvoyer les enregistrements d’alerte du type *Warning* (Avertissement) ou *Error* (Erreur), vous devez créer la recherche dans les journaux ci-dessous.

    Type=Alert AlertSeverity=RegEx("Warning|Error")

## <a name="partial-matches"></a>Correspondances partielles
Notez que l’expression régulière doit correspondre à l’intégralité du texte de la propriété.  Les correspondances partielles ne renvoient aucun enregistrement.  Par exemple, si vous tentez de récupérer les enregistrements d’un ordinateur nommé srv01.contoso.com, la recherche dans les journaux ci-dessous ne renverra **aucun** enregistrement.

    Computer=RegEx("srv..")

En effet, seule la première partie du nom correspond à l’expression régulière.  En revanche, les deux recherches dans les journaux ci-après renverront des enregistrements de cet ordinateur, car les expressions régulières correspondent à l’intégralité du nom.

    Computer=RegEx("srv..@")
    Computer=RegEx("srv...contoso.com")

## <a name="characters"></a>Caractères
Cette section décrit les méthodes vous permettant de spécifier différents caractères.

| Caractère | Description | Exemple | Exemples de correspondances |
|:--|:--|:--|:--|
| a | Une seule occurrence du caractère. | Computer=RegEx("srv01.contoso.com") | srv01.contoso.com |
| . | N’importe quel caractère unique. | Computer=RegEx("srv...contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| a? | Zéro ou une seule occurrence du caractère. | Computer=RegEx("srv01?.contoso.com") | srv0.contoso.com<br>srv01.contoso.com |
| a* | Zéro, une ou plusieurs occurrences du caractère. | Computer=RegEx("srv01*.contoso.com") | srv0.contoso.com<br>srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| a+ | Une ou plusieurs occurrences du caractère. | Computer=RegEx("srv01+.contoso.com") | srv01.contoso.com<br>srv011.contoso.com<br>srv0111.contoso.com |
| [*abc*] | Correspondance avec l’un des caractères indiqués entre crochets. | Computer=RegEx("srv0[123].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| [*a*-*z*] | Correspondance avec un seul caractère de la plage.  Peut inclure plusieurs plages. | Computer=RegEx("srv0[1-3].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| [^*abc*] | Aucun des caractères indiqués entre crochets. | Computer=RegEx("srv0[^123].contoso.com") | srv05.contoso.com<br>srv06.contoso.com<br>srv07.contoso.com |
| [^*a*-*z*] | Aucun des caractères de la plage. | Computer=RegEx("srv0[^1-3].contoso.com") | srv05.contoso.com<br>srv06.contoso.com<br>srv07.contoso.com |
| [*n*-*m*] | Correspondance avec une plage de caractères numériques. | Computer=RegEx("srv[01-03].contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |
| @ | N’importe quelle chaîne de caractères. | Computer=RegEx("srv@.contoso.com") | srv01.contoso.com<br>srv02.contoso.com<br>srv03.contoso.com |


## <a name="multiple-occurences-of-character"></a>Plusieurs occurrences d’un caractère
Cette section décrit les méthodes vous permettant de spécifier plusieurs occurrences d’un caractère spécifique.

| Caractère | Description | Exemple | Exemples de correspondances |
|:--|:--|:--|:--|
| a{n} |  *n* occurrences du caractère. | Computer=RegEx("bw-win-sc01{3}.bwren.lab") | bw-win-sc0111.bwren.lab |
| a{n,} |  Au moins *n* occurrences du caractère. | Computer=RegEx("bw-win-sc01{3,}.bwren.lab") | bw-win-sc0111.bwren.lab<br>bw-win-sc01111.bwren.lab<br>bw-win-sc011111.bwren.lab<br>bw-win-sc0111111.bwren.lab |
| a{n,m} |  *n* à *m* occurrences du caractère. | Computer=RegEx("bw-win-sc01{3,5}.bwren.lab") | bw-win-sc0111.bwren.lab<br>bw-win-sc01111.bwren.lab<br>bw-win-sc011111.bwren.lab |


## <a name="logical-expressions"></a>Expressions logiques
Cette section décrit les méthodes vous permettant de spécifier une sélection parmi plusieurs valeurs.

| Caractère | Description | Exemple | Exemples de correspondances |
|:--|:--|:--|:--|
| &#124; | OR logique.  Renvoie un résultat en cas de correspondance avec l’une des expressions. | Type=Alert AlertSeverity=RegEx("Avertissement&#124;Erreur") | Avertissement<br>Erreur |
| & | AND logique.  Renvoie un résultat en cas de correspondance avec les deux expressions. | EventData=regex("(sécurité.\*&.\*réussi.\*)") | Audit de sécurité réussi |


## <a name="literals"></a>Littéraux
Cette section décrit la méthode vous permettant de convertir des caractères spéciaux en caractères littéraux.  Ceci inclut les caractères qui fournissent des fonctionnalités aux expressions régulières telles que ?-\*^\[\]{}\(\)+\|.&.

| Caractère | Description | Exemple | Exemples de correspondances |
|:--|:--|:--|:--|
| \\ | Convertit un caractère spécial en littéral. | Status_CF=\\[Erreur\\]@<br>Status_CF=Erreur\\-@ | [Erreur]Fichier introuvable.<br>Erreur-Fichier introuvable. |


## <a name="next-steps"></a>Étapes suivantes

* Familiarisez-vous avec les [recherches dans les journaux](log-analytics-log-searches.md) pour visualiser et analyser les données dans le référentiel Log Analytics.

