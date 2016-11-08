---
title: Personnaliser le portail des développeurs dans Gestion des API Azure | Microsoft Docs
description: Découvrez comment personnaliser le portail des développeurs dans Gestion des API Azure.
services: api-management
documentationcenter: ''
author: steved0x
manager: erikre
editor: ''

ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/24/2016
ms.author: sdanie

---
# Personnaliser le portail des développeurs dans Gestion des API Azure
Ce guide vous montre comment modifier l’aspect du portail des développeurs dans Gestion des API Azure pour le rendre cohérent avec votre marque.

## <a name="change-page-headers"> </a>Modification du texte ou logo dans les en-têtes de page
L'un des principaux aspects de la personnalisation de portail est le remplacement du texte en haut de toutes les pages par le nom ou le logo de votre société.

Le contenu du portail des développeurs se modifie grâce au portail des éditeurs, accessible via le portail Azure Classic. Pour accéder au portail des éditeurs d’API, cliquez sur **Gérer** dans le portail Azure Classic de votre service Gestion des API.

![Portail des éditeurs][api-management-management-console]

Le portail des développeurs s’appuie sur un système de gestion de contenu ou CMS. L'en-tête qui s'affiche sur chaque page est un type de contenu spécial nommé widget. Pour modifier le contenu de ce widget, cliquez sur **Widgets** dans le menu **Portail des développeurs** sur la gauche, puis sélectionnez le widget **En-tête** dans la liste.

![Widgets header][api-management-widgets-header]

Le contenu de l'en-tête peut être modifié dans le champ **Corps**. Remplacez le texte par « Portail des développeurs Fabrikam », puis cliquez sur **Enregistrer** en bas de la page.

Vous devriez à présent pouvoir voir le nouvel en-tête sur chaque page du portail des développeurs.

> Pour ouvrir le portail des développeurs quand vous vous trouvez dans le portail des éditeurs, cliquez sur **Portail des développeurs** dans la barre supérieure.
> 
> 

## <a name="change-headers-styling"> </a>Modification du style des en-têtes
Les couleurs, polices, tailles, espacements et autres éléments liés au style de chaque page sur le portail sont définis par des règles de style. Pour modifier les styles, cliquez sur **Apparence** dans le menu **Portail des développeurs** du portail des éditeurs, puis cliquez sur **Commencer la personnalisation** pour activer l’éditeur de style.

Votre navigateur bascule vers une page masquée du portail des développeurs, contenant des échantillons de contenu, avec des exemples de toutes les règles de style utilisées sur le site. Pour ouvrir l'éditeur de style, déplacez votre curseur sur la fine ligne verticale grise sur la partie la plus à gauche de la page. La barre d'outils de l'éditeur doit s'afficher.

![Customization toolbar][api-management-customization-toolbar]

Il existe deux modes principaux de règles de style d’édition : **Modifier toutes les règles** affiche la liste de tous les styles utilisés ; **Choisir élément** vous permet de sélectionner un élément dans la page sur laquelle vous vous trouvez et affiche des styles uniquement pour cet élément.

Dans cette section, nous souhaitons modifier le style des en-têtes uniquement. Cliquez sur l’option **Choisir élément** dans la barre d’outils de l’éditeur de style, puis cliquez sur **Sélectionner un élément à personnaliser**. Les éléments sont mis en évidence quand vous les survolez avec la souris, indiquant quels styles d’élément vous allez commencer à modifier si vous avez cliqué. Passez la souris sur le texte représentant le nom de la société dans l’en-tête (« Portail des développeurs Fabrikam », si vous avez suivi les instructions de la section précédente), puis cliquez dessus. Un ensemble de règles de style nommées et catégorisées s’affiche dans l’éditeur de style.

Chaque règle représente une propriété de style de l'élément sélectionné. Par exemple, pour le texte d'en-tête sélectionné ci-dessus, la taille du texte est en @font-size-h1, tandis que le nom de la police avec des alternatives est en @headings-font-family.

> Si vous connaissez l'[amorçage][amorçage], ces règles sont en fait des [variables LESS][variables LESS] dans le thème d'amorçage utilisé par le portail des développeurs.
> 
> 

Modifions la couleur du texte d'en-tête. Sélectionnez l’entrée dans le champ **@headings-color**, puis tapez **#000000**. Il s'agit du code hexadécimal pour la couleur noire. Suite à cette action, un indicateur de couleur carré apparaît à la fin de la zone de texte. Si vous cliquez sur cet indicateur, un sélecteur de couleurs vous permet de faire votre choix.

![Color picker][api-management-customization-toolbar-color-picker]

Quand vous avez terminé d’apporter des modifications aux styles de l’élément sélectionné, cliquez sur **Aperçu des modifications** pour afficher les résultats à l’écran. Pour l’instant, ils ne sont visibles que pour les administrateurs. Pour rendre ces modifications visibles pour tout le monde, cliquez sur le bouton **Publier** dans l’éditeur de style et confirmez les modifications.

![Publish menu][api-management-customization-toolbar-publish-form]

> Pour modifier les règles de style qui s’appliquent à tout autre élément de la page, suivez la même procédure que pour l’en-tête. Cliquez sur **Choisir élément** dans l’éditeur de style, sélectionnez l’élément qui vous intéresse et commencez à modifier les valeurs des règles de style affichées à l’écran.
> 
> 

## <a name="edit-page-contents"> </a>Modification du contenu d’une page
Le portail des développeurs se compose de pages générées automatiquement telles que les API, produits, applications, problèmes et contenu écrit manuellement. Il est basé sur un système de gestion de contenu. Vous pouvez ainsi créer autant de contenu que nécessaire.

Pour afficher la liste de toutes les pages de contenu existantes, cliquez sur **Contenu** dans le menu **Portail des développeurs** du portail des éditeurs.

![Manage content][api-management-customization-manage-content]

Cliquez sur la page **Bienvenue** pour modifier le texte s’affichant sur la page d’accueil du portail des développeurs. Apportez les modifications de votre choix, générez-en un aperçu si nécessaire, puis cliquez sur **Publier maintenant** pour les rendre visibles pour tous.

> La page d’accueil utilise une configuration spéciale qui lui permet d’afficher une bannière en haut. Cette bannière n’est pas modifiable depuis la section **Contenu**. Pour modifier cette bannière, cliquez sur **Widgets** dans le menu **Portail des développeurs**, sélectionnez **Page d’accueil** dans la liste déroulante **Couche actuelle**, puis ouvrez l’élément **Bannière** sous la section **Proposé**. Le contenu de ce widget peut être modifié comme sur toute autre page.
> 
> 

## <a name="next-steps"> </a>Étapes suivantes
* Découvrez comment personnaliser le contenu des pages du portail des développeurs à l’aide de [modèles](api-management-developer-portal-templates.md).

[Change the text/logo in the page headers]: #change-page-headers
[Change the styling of the headers]: #change-headers-styling
[Edit the contents of a page]: #edit-page-contents
[Next steps]: #next-steps

[Azure Classic Portal]: https://manage.windowsazure.com/

[api-management-management-console]: ./media/api-management-customize-portal/api-management-management-console.png
[api-management-widgets-header]: ./media/api-management-customize-portal/api-management-widgets-header.png
[api-management-customization-toolbar]: ./media/api-management-customize-portal/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-customize-portal/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-customize-portal/api-management-customization-toolbar-publish-form.png
[api-management-customization-manage-content]: ./media/api-management-customize-portal/api-management-customization-manage-content.png


[amorçage]: http://getbootstrap.com/
[variables LESS]: http://getbootstrap.com/css/

<!---HONumber=AcomDC_0831_2016-->