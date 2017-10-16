---
title: "Personnaliser les styles dans le portail des développeurs dans Gestion des API Azure | Microsoft Docs"
description: "Découvrez comment modifier les styles utilisés pour n’importe quelle page dans le portail des développeurs dans Gestion des API Azure."
services: api-management
documentationcenter: 
author: vladvino
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
ms.openlocfilehash: 89baf60d0204a1701e93309f09b90bc94c4ca57b
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
---
# <a name="customize-the-styling-of-the-developer-portal-in-azure-api-management"></a>Personnaliser le style du portail des développeurs dans Gestion des API Azure
Il existe trois manières principales pour personnaliser le portail des développeurs dans Gestion des API Azure :

* [Modifier le contenu des pages statiques et des éléments de mise en page][modify-content-layout]
* [Mettre à jour les styles utilisés pour les éléments de page dans le portail des développeurs][customize-styles] (procédure expliquée dans ce guide)
* [Modifier les modèles utilisés pour les pages générées par le portail][portal-templates] (par exemple, documents API, produits, authentification des utilisateurs, etc.)

## <a name="change-headers-styling"></a>Modifier le style des éléments de page

Les couleurs, polices, tailles, espacements et autres éléments liés au style de chaque page sur le portail sont définis par des règles de style. 

La modification des règles de style s’effectue à partir du **portail des développeurs** lorsque vous êtes connecté en tant qu’administrateur. Pour y accéder, ouvrez tout d’abord le portail Azure et cliquez sur **Portail des éditeurs** à partir de la barre d’outils de services de votre instance de gestion des API.

![Portail des éditeurs][api-management-management-console]

Cliquez ensuite sur le **portail des développeurs** en haut à droite. 

![Lien vers le portail des développeurs dans le portail des éditeurs][api-management-pp-dp-link]

Pour ouvrir la barre d’outils de personnalisation, déplacez votre souris sur l’icône de personnalisation (ou sélectionnez-la) et cliquez sur « styles » dans la barre d’outils.

![Bouton de la barre d’outils de personnalisation][api-management-customization-toolbar-button]

Il existe principalement deux méthodes pour modifier les règles de style : vous pouvez parcourir la liste de toutes les règles de style utilisées, sachant qu’elle s’affiche par défaut, puis modifier un style selon vos besoins, ou vous pouvez choisir **Select an element on the page** (Sélectionner un élément sur la page), puis cliquez n’importe où sur la page pour afficher uniquement les styles de cet élément.

![Customization toolbar][api-management-customization-toolbar]

Cliquez sur l’option **Select an element on the page** (Sélectionner un élément sur la page) pour cet exemple.  Les éléments sont mis en évidence quand vous les survolez avec la souris, indiquant quels styles d’élément vous allez commencer à modifier si vous avez cliqué. Déplacez la souris sur le texte dans l’en-tête (en général, le nom de la société se trouve là), puis cliquez dessus. Un ensemble de règles de style nommées et catégorisées s’affiche dans l’éditeur de style. Chaque règle représente une propriété de style de l'élément sélectionné. Par exemple, pour le texte d’en-tête sélectionné ci-dessus, la taille du texte est en @font-size-h1, tandis que le nom de la police avec d’autres options est en @headings-font-family.

> Si vous connaissez [l'amorçage][bootstrap], ces règles sont en fait des [variables LESS][LESS variables] dans le thème d'amorçage utilisé par le portail des développeurs.
> 
> 

Modifions la couleur du texte d'en-tête. Sélectionnez l’entrée dans le champ **@headings-color** , puis tapez **#000000**. Il s'agit du code hexadécimal pour la couleur noire. Suite à cette action, un indicateur de couleur carré apparaît à la fin de la zone de texte. Si vous cliquez sur cet indicateur, un sélecteur de couleurs vous permet de faire votre choix.

![Color picker][api-management-customization-toolbar-color-picker]

Un aperçu des modifications apportées est disponible en temps réel. Toutefois, seuls les administrateurs y ont accès. Pour rendre ces modifications visibles pour tout le monde, cliquez sur le bouton **Publier** dans l’éditeur de style et confirmez les modifications.

![Publish menu][api-management-customization-toolbar-publish-form]

> Pour modifier les règles de style qui s’appliquent à tout autre élément de la page, suivez la même procédure que pour l’en-tête. Cliquez sur **Select an element on the page** (Sélectionner un élément sur la page) dans l’éditeur de style, sélectionnez l’élément qui vous intéresse et commencez à modifier les valeurs des règles de style affichées à l’écran.
> 
> 


## <a name="next-steps"></a>Étapes suivantes
* Découvrez comment personnaliser le contenu des pages du portail des développeurs à l’aide de [modèles](api-management-developer-portal-templates.md).

[Change the styling of the headers]: #change-headers-styling
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-styles/api-management-management-console.png
[api-management-pp-dp-link]: ./media/api-management-customize-styles/api-management-pp-dp-link.png
[api-management-customization-toolbar-button]: ./media/api-management-customize-styles/api-management-customization-toolbar-button.png
[api-management-customization-toolbar]: ./media/api-management-customize-styles/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-styles/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-styles/api-management-customization-toolbar-publish-form.png

[modify-content-layout]: api-management-modify-content-layout.md
[customize-styles]: api-management-customize-styles.md
[portal-templates]: api-management-developer-portal-templates.md

[bootstrap]: http://getbootstrap.com/
[LESS variables]: http://getbootstrap.com/css/
