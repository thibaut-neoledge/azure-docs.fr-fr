---
title: "Enregistrement des recherches et épinglage des ressources de données | Microsoft Docs"
description: "Article de procédure mettant en avant les fonctionnalités d’Azure Data Catalog pour enregistrer des sources de données et épingler des ressources de données en vue d’une réutilisation ultérieure."
services: data-catalog
documentationcenter: 
author: steelanddata
manager: NA
editor: 
tags: 
ms.assetid: 6bd00a81-820d-4b7c-91fa-ab09e575474c
ms.service: data-catalog
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-catalog
ms.date: 01/23/2017
ms.author: maroche
translationtype: Human Translation
ms.sourcegitcommit: 219dcbfdca145bedb570eb9ef747ee00cc0342eb
ms.openlocfilehash: f017776480466979d7f2f9edec2b3ac5caca2321


---
# <a name="how-to-save-searches-and-pin-data-assets"></a>Comment enregistrer des recherches et épingler des ressources de données
## <a name="introduction"></a>Introduction
Microsoft Azure Data Catalog fournit des fonctionnalités de détection des sources de données. Les utilisateurs peuvent rapidement rechercher et filtrer le catalogue pour rechercher des sources de données et comprendre leur rôle respectif. Cela leur permet de trouver plus facilement les données appropriées au travail en cours.

Mais qu’en est-il lorsque les utilisateurs doivent régulièrement travailler avec les mêmes données ? Qu’en est-il lorsque les utilisateurs contribuent régulièrement aux mêmes sources de données du catalogue ? Dans ces situations, il est inefficace d’émettre à plusieurs reprises les mêmes recherches. C’est là que les recherches enregistrées et les ressources de données épinglées peuvent se révéler utiles.

## <a name="saved-searches"></a>Recherches enregistrées
Une recherche enregistrée dans Azure Data Catalog est une définition de recherche par utilisateur réutilisable. Une fois qu’un utilisateur a défini une recherche, y compris les termes de la recherche, les balises et d’autres filtres, il peut l’enregistrer pour l’utiliser ultérieurement. La définition de la recherche enregistrée peut ensuite être réexécutée pour renvoyer les ressources de données qui correspondent aux critères de recherche.

### <a name="creating-a-saved-search"></a>Création d’une recherche enregistrée
Pour créer une recherche enregistrée, entrez tout d’abord les critères de recherche à réutiliser. Cliquez ensuite sur le lien « Enregistrer » dans la zone de « recherche actuelle » du portail Azure Data Catalog.

 ![Sélectionnez Enregistrer pour enregistrer les paramètres de recherche actuels](./media/data-catalog-how-to-save-pin/01-save-option.png)

Lorsque vous y êtes invité, donnez un nom à la recherche enregistrée. Choisissez un nom significatif et une description des ressources de données qui seront renvoyées par la recherche.

 ![Donnez un nom à la recherche enregistrée](./media/data-catalog-how-to-save-pin/02-name.png)

### <a name="managing-saved-searches"></a>Gestion des recherches enregistrées
Une fois qu’un utilisateur a enregistré une ou plusieurs recherches, une option « Recherches enregistrées » s’affiche dans le portail Azure Data Catalog, sous la zone de « recherche actuelle ». Il suffit de la développer pour afficher la liste complète des recherches enregistrées.

 ![Liste des recherches enregistrées](./media/data-catalog-how-to-save-pin/03-list.png)

Sélectionnez une recherche enregistrée dans la liste pour l’exécuter.

Sélectionnez le menu déroulant pour obtenir une liste des options de gestion :

 ![Options de gestion des recherches enregistrées](./media/data-catalog-how-to-save-pin/04-managing.png)

L’option Renommer invite l’utilisateur à entrer un nouveau nom pour la recherche enregistrée. La définition de la recherche reste quant à elle inchangée.

L’option Supprimer invite l’utilisateur à confirmer l’opération, puis supprime la recherche enregistrée de la liste de l’utilisateur.

L’option Enregistrer en tant que valeur par défaut marque la recherche enregistrée comme recherche par défaut pour l’utilisateur. Si l’utilisateur effectue une recherche « vide » à partir de la page d’accueil Azure Data Catalog, la recherche par défaut de l’utilisateur est exécutée. En outre, la recherche marquée comme valeur par défaut s’affiche en haut de la liste des recherches enregistrées.

### <a name="organizational-saved-searches"></a>Recherches enregistrées organisationnelles
Chaque utilisateur peut enregistrer des recherches pour son propre usage. Les administrateurs de Data Catalog peuvent également enregistrer des recherches à destination de tous les utilisateurs de l’organisation. En enregistrant une recherche, ils ont la possibilité de partager la recherche enregistrée au sein de l’entreprise. Si cette option est sélectionnée, la recherche enregistrée figurera dans la liste des recherches accessibles à tous les utilisateurs.

 ![Recherches enregistrées organisationnelles](./media/data-catalog-how-to-save-pin/08-organizational-saved-search.png)

## <a name="pinned-data-assets"></a>Ressources de données épinglées
Les recherches enregistrées permettent aux utilisateurs d’enregistrer et de réutiliser des définitions de recherche. Les ressources de données renvoyées par les recherches peuvent varier au fil du temps, à mesure que le contenu du catalogue évolue. L’épinglage des ressources de données permet aux utilisateurs d’identifier de manière explicite des ressources de données en particulier pour faciliter leur accès, sans avoir besoin d’utiliser une recherche.

Épingler une ressource de données est simple : les utilisateurs cliquent simplement sur l’icône « épingler » de la ressource de données et l’ajoutent ainsi à leur liste. Cette icône s’affiche dans l’angle de la vignette de la ressource, dans l’affichage en mosaïque, ainsi que dans la colonne la plus à gauche de la liste, dans le portail Azure Data Catalog.

![Épinglage d’une ressource de données](./media/data-catalog-how-to-save-pin/05-pinning.png)

Retirer l’épingle d’une ressource de données est simple : les utilisateurs cliquent simplement à nouveau sur l’icône « épingler » pour la retirer.

![Désépinglage d’une ressource de données](./media/data-catalog-how-to-save-pin/06-unpinning.png)

## <a name="my-assets"></a>« Mes ressources »
La page d’accueil du portail Azure Data Catalog comprend une section intitulée « Mes ressources » qui affiche les actifs d’intérêt de l’utilisateur actuel. Cette section inclut à la fois les ressources épinglées et les recherches enregistrées.

![« Mes ressources » dans la page d’accueil](./media/data-catalog-how-to-save-pin/07-my-assets.png)

## <a name="summary"></a>Résumé
Azure Data Catalog offre aux utilisateurs des fonctionnalités qui leur permettent de mieux découvrir les sources de données dont ils ont besoin, et ainsi de passer moins de temps à rechercher les données et plus de temps à les utiliser. Des recherches enregistrées et des ressources de données épinglées sont générées d’après ces fonctionnalités de base afin que les utilisateurs puissent identifier facilement les sources de données avec lesquelles ils comptent travailler régulièrement.



<!--HONumber=Nov16_HO3-->


