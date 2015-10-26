<properties
   pageTitle="Détection de sources de données"
   description="Article de procédure relatif à la détection de ressources de données inscrites avec Azure Data Catalog et abordant notamment la recherche, le filtrage et l’utilisation de la mise en surbrillance des correspondances dans le portail Azure Data Catalog."
   services="data-catalog"
   documentationCenter=""
   authors="steelanddata"
   manager="NA"
   editor=""
   tags=""/>
<tags
   ms.service="data-catalog"
   ms.devlang="NA"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="data-catalog"
   ms.date="08/17/2015"
   ms.author="maroche"/>


# Détection de sources de données

## Introduction
**Microsoft Azure Data Catalog** est un service cloud entièrement géré qui sert de système d'inscription et de détection des sources de données d'entreprise. En d'autres termes, **Microsoft Azure Data Catalog** vise essentiellement à aider les utilisateurs à détecter, comprendre et utiliser des sources de données et permet aux organisations de mieux exploiter leurs données. Une fois qu’une source de données a été inscrite avec **Azure Data Catalog**, ses métadonnées sont indexées par le service. Les utilisateurs peuvent ainsi effectuer des recherches facilement pour détecter les données dont ils ont besoin.

## Recherche et filtrage

Dans **Azure Data Catalog**, la détection utilise deux mécanismes principaux : la recherche et le filtrage.

La recherche est conçue pour être intuitive et puissante. Par défaut, les termes de recherche sont comparés à toutes les propriétés du catalogue, y compris les annotations fournies par les utilisateurs.

Le filtrage est conçu pour compléter la recherche. Les utilisateurs peuvent sélectionner des caractéristiques spécifiques telles que les experts, le type de source de données, le type d’objet et les balises, pour afficher uniquement les ressources de données correspondantes et pour limiter les résultats de la recherche aux ressources correspondantes.

En combinant recherche et filtrage, les utilisateurs peuvent parcourir rapidement les sources de données qui ont été inscrites avec **Azure Data Catalog** pour détecter celles dont ils ont besoin.

## Syntaxe de recherche

La recherche en texte libre par défaut est simple et intuitive. Cependant, les utilisateurs peuvent également utiliser la syntaxe de recherche d’**Azure Data Catalog** pour mieux contrôler les résultats de recherche. La recherche avec **Azure Data Catalog** prend en charge les techniques suivantes :

| Technique | Utilisation | Exemple |
|---------------------------|-----------------------------------------------------------------------------------------------------------------------------------------|-----------------------------------------------------------|
| Recherche de base | Recherche de base à l’aide d’un ou plusieurs termes de recherche. Les résultats sont toutes les ressources correspondant à une propriété quelconque avec un ou plusieurs termes spécifiés. | données de ventes |
| Étendue de la propriété | Renvoie des sources de données uniquement si le terme de recherche est mis en correspondance avec la propriété spécifiée | name:finance |
| Opérateurs booléens | Les opérations booléennes permettent d’étendre ou limiter une recherche | finance NOT entreprise |
| Parenthèses de regroupement | Utilisez des parenthèses pour grouper les parties de la requête à isoler logiquement, notamment lorsque vous appliquez également des opérateurs booléens | name:finance AND (tags:Q1 OR tags:Q2) Opérateurs de comparaison |
| Opérateurs de comparaison | Utilisez des comparaisons autres que l’égalité pour les propriétés comportant des types de données numériques et de date | creationTime>"11/05/2014" |

Pour plus d’informations sur la recherche avec **Azure Data Catalog**, voir [https://msdn.microsoft.com/library/azure/mt267594.aspx](https://msdn.microsoft.com/library/azure/mt267594.aspx).

## Mise en surbrillance des correspondances
Lorsque vous affichez les résultats de la recherche, les propriétés affichées correspondant aux termes de recherche spécifiés (par exemple, le nom de la ressource de données, la description et les balises) sont mises en surbrillance. Il est ainsi plus facile d’identifier la raison pour laquelle une ressource de données spécifique a été retournée par une recherche.

> [AZURE.NOTE]Les utilisateurs peuvent désactiver la mise en surbrillance des correspondances à l’aide du commutateur de mise en surbrillance du portail **Azure Data Catalog**.

Lorsque vous affichez les résultats de la recherche, la raison pour laquelle une ressource de données spécifique est incluse n’est pas toujours évidente, même lorsque la mise en surbrillance des correspondances est activée. Étant donné que toutes les propriétés font l’objet de la recherche par défaut, une ressource de données peut être retournée en cas de détection d’une correspondance avec une propriété figurant dans une colonne. Et dans la mesure où plusieurs utilisateurs peuvent annoter des ressources de données inscrites avec leurs propres balises et descriptions, les métadonnées ne peuvent pas toutes s’afficher dans la liste des résultats de la recherche.

Dans l’affichage en mosaïque par défaut, chaque vignette affichée dans les résultats de la recherche inclut une icône d’affichage des correspondances des termes de recherche, qui permet à l’utilisateur de visualiser rapidement le nombre de correspondances et leur emplacement et de les atteindre s’il le souhaite.

 ![Mise en surbrillance des correspondances et résultats de recherche dans le portail Azure Data Catalog](./media/data-catalog-how-to-discover/search-matches.png)

## Résumé
L’inscription d’une source de données avec **Azure Data Catalog** facilite la détection et la compréhension. Cette étape consiste à copier les métadonnées descriptives et structurelles à partir de la source de données dans le service de catalogue. Une fois qu’une source de données a été inscrite, les utilisateurs peuvent la détecter grâce au filtrage et à la recherche, à partir du portail **Azure Data Catalog**.

<!---HONumber=Oct15_HO3-->