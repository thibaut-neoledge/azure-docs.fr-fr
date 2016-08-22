<properties 
	pageTitle="Personnalisation de l'apparence du portail des développeurs dans Gestion des API Azure" 
	description="Personnalisation de l'apparence du portail des développeurs dans Gestion des API Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="08/09/2016" 
	ms.author="sdanie"/>

# Personnalisation de l'apparence du portail des développeurs dans Gestion des API Azure

Les couleurs, polices, tailles, espacements et autres aspects de l'apparence du portail des développeurs sont définis par des règles de style. Il existe des jeux de règles pour chaque élément de structure de la page : l’en-tête, le menu, le corps du contenu, le titre de la page, etc. Dans cette procédure, vous allez découvrir comment modifier ces règles de style.

Pour modifier les règles de style, cliquez sur **Apparence** dans le menu **Portail des développeurs** du portail des éditeurs. Cliquez ensuite sur **Lancer la personnalisation** pour activer l'éditeur de style.

Votre navigateur basculera vers une page masquée du portail des développeurs, contenant des échantillons de contenu, avec des exemples de toutes les règles de style utilisées sur le site. Pour ouvrir l'éditeur de style, déplacez votre curseur sur la fine ligne verticale grise sur la partie la plus à gauche de la page. La barre d’outils de l’éditeur doit s’afficher :

![Customization toolbar][api-management-customization-toolbar]

Il existe deux méthodes principales pour modifier les règles de style : **Modifier toutes les règles** affiche la liste de toutes les règles de style utilisées, alors que **Choisir élément** vous permet de sélectionner un élément sur la page sur laquelle vous vous trouvez et affiche les styles uniquement pour cet élément.

Dans cette section, nous souhaitons modifier le style d’un élément en particulier, celui des titres de page par exemple. Cliquez sur l'option **Choisir élément** dans la barre d'outils de l'éditeur de style, puis cliquez sur **Sélectionner un élément à personnaliser**. Les éléments sont mis en évidence lorsque vous les survolez, indiquant quels styles d’éléments vous allez voir si vous cliquez.

Passez la souris sur le texte représentant le titre de la page dans l'en-tête, puis cliquez sur cet élément. Un ensemble de règles de style nommées et catégorisées s'affichera dans l'éditeur de style.

Chaque règle représente une propriété de style de l'élément sélectionné. Par exemple, pour le texte d'en-tête sélectionné ci-dessus, la taille du texte est en @font-size-h1, tandis que le nom de la police avec des alternatives est en @headings-font-family.

> Si vous connaissez l'[amorçage](http://getbootstrap.com/), ces règles sont en fait des [variables LESS](http://getbootstrap.com/css/) dans le thème d'amorçage utilisé par le portail des développeurs.

Modifions la couleur du texte de titre. Sélectionnez l’entrée dans le champ **@headings-color**, puis tapez #000000. Il s'agit du code hexadécimal pour la couleur noire. Suite à cette action, un indicateur de couleur carré apparaît à la fin de la zone de texte. Si vous cliquez sur cet indicateur, un sélecteur de couleurs vous permet de faire votre choix.

![Color picker][api-management-customization-toolbar-color-picker]

Lorsque vous avez terminé d'apporter des modifications aux styles de l'élément sélectionné, cliquez sur **Aperçu des modifications** pour afficher les résultats à l'écran. Pour l'instant, ils ne sont visibles que pour les administrateurs. Pour rendre ces modifications visibles pour tout le monde, cliquez sur le bouton **Publier** dans l'éditeur de style et confirmez les modifications.

![Publish form][api-management-customization-toolbar-publish-form]

> Pour modifier les règles de style qui s'appliquent à tout autre élément sur la page, suivez la même procédure que pour l'en-tête : cliquez sur **Choisir un élément** dans l'éditeur de style, sélectionnez l'élément qui vous intéresse et commencez à modifier les valeurs des règles de style affichées à l'écran.


[Next steps]: #next-steps


[api-management-customization-toolbar]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar.png
[api-management-customization-toolbar-color-picker]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar-color-picker.png
[api-management-customization-toolbar-publish-form]: ./media/api-management-howto-customize-look-and-feel/api-management-customization-toolbar-publish-form.png

<!---HONumber=AcomDC_0810_2016-->