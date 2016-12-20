---
title: "Créer un index de Recherche Azure à l’aide du portail Azure │ Microsoft Docs"
description: "Créez un index à l’aide du portail Azure."
services: search
manager: jhubbard
author: ashmaka
documentationcenter: 
ms.assetid: c54d8787-6dae-4943-85ed-c8928d2a5caf
ms.service: search
ms.devlang: NA
ms.workload: search
ms.topic: article
ms.tgt_pltfrm: na
ms.date: 08/29/2016
ms.author: ashmaka
translationtype: Human Translation
ms.sourcegitcommit: 2ea002938d69ad34aff421fa0eb753e449724a8f
ms.openlocfilehash: f0d468d0cb2cf76bb90e73d3fef3f6a8c14d1850


---
# <a name="create-an-azure-search-index-using-the-azure-portal"></a>Créer un index Azure Search à l’aide du portail Azure
> [!div class="op_single_selector"]
> * [Vue d'ensemble](search-what-is-an-index.md)
> * [Portail](search-create-index-portal.md)
> * [.NET](search-create-index-dotnet.md)
> * [REST](search-create-index-rest-api.md)
> 
> 

Cet article vous explique comment créer un [index](search-what-is-an-index.md) Azure Search à l’aide du portail Azure.

Avant de suivre ce guide et de passer à la création d’un index, vous devez avoir déjà [créé un service Azure Search](search-create-service-portal.md).

## <a name="i-go-to-your-azure-search-blade"></a>I. Accéder au panneau Azure Search
1. Cliquez sur « Toutes les ressources » dans le menu de gauche du [portail Azure](https://portal.azure.com/#blade/HubsExtension/BrowseResourceBlade/resourceType/Microsoft.Search%2FsearchServices)
2. Sélectionnez votre service Azure Search.

## <a name="ii-add-and-name-your-index"></a>II. Ajouter et nommer l’index
1. Cliquez sur le bouton « Ajouter un index ».
2. Nommez votre index Azure Search. Puisque nous créons un index pour rechercher des hôtels dans ce guide, nous avons nommé notre index « hotels ».
   * Le nom de l’index doit commencer par une lettre et contenir uniquement des lettres minuscules, des chiffres ou des tirets (« - »).
   * Comme c’est le cas pour le nom de votre service, le nom d’index que vous choisissez fait également partie de l’URL de point de terminaison à laquelle vous envoyez vos requêtes HTTP pour l’API Azure Search.
3. Cliquez sur l’entrée « Champs » pour ouvrir un nouveau panneau.

![](./media/search-create-index-portal/add-index.png)

## <a name="iii-create-and-define-the-fields-of-your-index"></a>III. Créer et définir les champs de l’index
1. Lorsque vous sélectionnez l’entrée « Champs », un nouveau panneau s’ouvre avec un formulaire qui vous permet d’entrer la définition de votre index.
2. Ajoutez des champs à l’index à l’aide du formulaire.
   
   * Chaque index Azure Search doit obligatoirement comporter un champ de *clé* de type Edm.String. Ce champ de clé est créé par défaut avec le nom de champ « id ». Pour notre index, nous l'avons remplacé par « hotelId ».
   * Certaines propriétés de votre schéma d’index ne peuvent être définies qu’une seule fois et vous ne pouvez pas les mettre à jour par la suite. Pour cette raison, les mises à jour de schéma qui nécessitent une réindexation, par exemple la modification des types de champ, ne sont pas possibles pour le moment après la configuration initiale.
   * Nous avons soigneusement choisi les valeurs des propriétés pour chaque champ en fonction de la manière dont elles seront vraisemblablement utilisées dans une application. Ne perdez pas de vue votre expérience de recherche ni vos besoins métier lors de la conception de votre index, car chaque champ doit être affecté à des [propriétés correctes](https://msdn.microsoft.com/library/azure/dn798941.aspx). Ces propriétés déterminent les fonctionnalités de recherche (filtrage, facettes, tri, recherche en texte intégral, etc.) qui s’appliqueront à chaque champ. Par conséquent, nous activons la recherche en texte intégral pour ce champ en définissant la propriété « Possibilité de recherche ».
     * Vous pouvez également définir [l’analyseur de langue](https://msdn.microsoft.com/en-us/library/azure/dn879793.aspx) pour chaque champ en cliquant sur l’onglet « Analyseur » en haut du panneau. Comme vous pouvez le voir ci-dessous, nous avons sélectionné un analyseur de français pour un champ de notre index destiné à du texte français.
3. Cliquez sur **OK** dans le panneau « Champs » pour valider vos définitions de champ.
4. Cliquez sur **OK** dans le panneau « Ajouter un index » pour enregistrer et créer l’index que vous venez de définir.

Dans les captures d’écran ci-dessous, vous pouvez voir comment nous avons nommé et défini les champs pour notre index « hotels ».

![](./media/search-create-index-portal/field-definitions.png)

![](./media/search-create-index-portal/set-analyzer.png)

## <a name="next"></a>Suivant
Après avoir créé un index Azure Search, vous pouvez commencer à [télécharger du contenu dans votre index](search-what-is-data-import.md) afin d’y lancer des recherches.




<!--HONumber=Nov16_HO3-->


