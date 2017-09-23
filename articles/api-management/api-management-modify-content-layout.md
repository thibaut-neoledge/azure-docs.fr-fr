---
title: "Modifier le contenu d’une page dans le portail des développeurs dans Gestion des API Azure | Microsoft Docs"
description: "Découvrez comment modifier le contenu d’une page dans le portail des développeurs dans Gestion des API Azure."
services: api-management
documentationcenter: 
author: antonba
manager: vlvinogr
editor: 
ms.assetid: 186128fe-41c0-4efb-9efe-2478ad4d103f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 02/09/2017
ms.author: antonba
ms.translationtype: Human Translation
ms.sourcegitcommit: ecae1da20551d8372331124b07c4aca2e15f55bb
ms.openlocfilehash: 708c803c36c182ed90e04731b12d4ade00ae7ffb
ms.contentlocale: fr-fr
ms.lasthandoff: 02/23/2017

---
# <a name="modify-the-content-and-layout-of-pages-on-the-developer-portal-in-azure-api-management"></a>Modifier le contenu et la disposition des pages dans le portail des développeurs dans Gestion des API Azure
Il existe trois manières principales pour personnaliser le portail des développeurs dans Gestion des API Azure :

* [Modifier le contenu des pages statiques et des éléments de mise en page][modify-content-layout] (procédure expliquée dans ce guide)
* [Mettre à jour les styles utilisés pour les éléments de page dans le portail des développeurs][customize-styles]
* [Modifier les modèles utilisés pour les pages générées par le portail][portal-templates] (par exemple, documents API, produits, authentification des utilisateurs, etc.)

## <a name="page-structure"> </a>Structure des pages du portail des développeurs

Le portail des développeurs s’appuie sur un système de gestion de contenu. La disposition de chaque page s’appuie sur un ensemble d’éléments de petite page appelés widgets :

![Structure de page du portail des développeurs][api-management-customization-widget-structure]

Tous les widgets sont modifiables. 
* Le contenu de base spécifique à chaque page réside dans le widget « Contenu ». La modification d’une page entraîne la modification du contenu de ce widget.
* Tous les éléments de mise en page sont contenus dans les autres widgets. Les modifications apportées à ces widgets s’appliquent à toutes les pages. Ils sont dénommés « widgets de mise en page ».

Dans la modification de page standard, un utilisateur ne modifie en général que le widget Contenu qui contient un contenu différent pour chaque page.

## <a name="modify-layout-widget"> </a>Modification du contenu d’un widget de mise en page

Le contenu du portail des développeurs se modifie grâce au portail des éditeurs, accessible via le portail Azure. Pour y accéder, cliquez sur **Portail des éditeurs** à partir de la barre d’outils de services de votre instance de gestion des API.

![Portail des éditeurs][api-management-management-console]

Pour modifier le contenu de ce widget, cliquez sur **Widgets** dans le menu **Portail des développeurs** sur la gauche. Pour cet exemple, modifions le contenu du widget d’en-tête. Sélectionnez le widget **En-tête** dans la liste.

![Widgets header][api-management-widgets-header]

Le contenu de l'en-tête peut être modifié dans le champ **Corps** . Changez le texte comme vous le souhaitez, puis cliquez sur **Enregistrer** en bas de la page.

Vous devriez à présent pouvoir voir le nouvel en-tête sur chaque page du portail des développeurs.

> Pour ouvrir le portail des développeurs quand vous vous trouvez dans le portail des éditeurs, cliquez sur **Portail des développeurs** dans la barre supérieure.
> 
> 

## <a name="edit-page-contents"> </a>Modification du contenu d’une page

Pour afficher la liste de toutes les pages de contenu existantes, cliquez sur **Contenu** dans le menu **Portail des développeurs** du portail des éditeurs.

![Manage content][api-management-customization-manage-content]

Cliquez sur la page **Bienvenue** pour modifier le texte s’affichant sur la page d’accueil du portail des développeurs. Apportez les modifications de votre choix, générez-en un aperçu si nécessaire, puis cliquez sur **Publier maintenant** pour les rendre visibles pour tous.

> La page d’accueil utilise une configuration spéciale qui lui permet d’afficher une bannière en haut. Cette bannière n’est pas modifiable depuis la section **Contenu** . Pour modifier cette bannière, cliquez sur **Widgets** dans le menu **Portail des développeurs**, sélectionnez **Page d’accueil** dans la liste déroulante **Couche actuelle**, puis ouvrez l’élément **Bannière** sous la section **Proposé**. Le contenu de ce widget peut être modifié comme sur toute autre page.
> 
> 

## <a name="next-steps"> </a>Étapes suivantes
* [Mettre à jour les styles utilisés pour les éléments de page dans le portail des développeurs][customize-styles]
* [Modifier les modèles utilisés pour les pages générées par le portail][portal-templates] (par exemple, documents API, produits, authentification des utilisateurs, etc.)

[Structure of developer portal pages]: #page-structure
[Modifying the contents of a layout widget]: #modify-layout-widget
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[api-management-customization-widget-structure]: ./media/api-management-modify-content-layout/portal-widget-structure.png
[api-management-management-console]: ./media/api-management-modify-content-layout/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-modify-content-layout/api-management-widgets-header.png
[api-management-customization-manage-content]: ./media/api-management-modify-content-layout/api-management-customization-manage-content.png

