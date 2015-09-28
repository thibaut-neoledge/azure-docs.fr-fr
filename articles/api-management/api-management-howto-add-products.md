<properties 
	pageTitle="Création et publication d'un produit dans Gestion des API Azure" 
	description="Apprenez à créer et à publier des produits dans Gestion des API Azure." 
	services="api-management" 
	documentationCenter="" 
	authors="steved0x" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="api-management" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="09/15/2015" 
	ms.author="sdanie"/>

# Création et publication d'un produit dans Gestion des API Azure

Dans Gestion des API Azure, un produit contient une ou plusieurs API, ainsi qu’un quota et des conditions d’utilisation. Une fois le produit publié, les développeurs peuvent s'y abonner et commencer à utiliser ses API. Cette rubrique constitue un guide pour créer un produit, ajouter une API et le publier pour les développeurs.

## <a name="create-product"> </a>Création d’un produit

Les opérations sont ajoutées et configurées dans une API sur le portail des éditeurs. Pour accéder au portail des éditeurs, cliquez sur **Gérer** dans le portail Azure de votre service Gestion des API.

![Portail des éditeurs][api-management-management-console]

>Si vous n'avez pas encore créé une instance de service Gestion des API, consultez la page [Création d'une instance de service Gestion des API][] dans le didacticiel [Prise en main de Gestion des API Azure][].

Cliquez sur **Produits** dans le menu à gauche pour afficher la page **Produits**, puis cliquez sur **Ajouter un produit**.

![Produits][api-management-products]

![New product][api-management-add-new-product]

Entrez un nom décrivant le produit dans le champ **Nom**, ainsi qu'une description du produit dans le champ **Description**.

Les produits de Gestion des API peuvent être **Ouverts** ou **Protégés**. Les produits protégés doivent faire l’objet d’un abonnement avant de pouvoir être utilisés, alors que les produits ouverts peuvent être utilisés sans abonnement. Cochez la case **Demander une approbation d’abonnement** pour créer un produit protégé qui requiert un abonnement. Il s’agit du paramètre par défaut.

Cochez **Demander une approbation d'abonnement** si vous souhaitez qu'un administrateur révise et accepte ou refuse les tentatives d'abonnement à ce produit. Si la case n'est pas cochée, les tentatives d'abonnement seront automatiquement approuvées. Pour plus d'informations sur les abonnements, consultez la section [Affichage des abonnés à un produit][].

Pour autoriser les comptes de développeur à s’abonner plusieurs fois au produit, cochez la case **Autoriser plusieurs abonnements**. Si cette case n’est pas cochée, chaque compte de développeur ne peut s’abonner qu’une seule fois pour le produit.

![Abonnements multiples illimités][api-management-unlimited-multiple-subscriptions]

Pour limiter le nombre d’abonnements simultanés, cochez la case **Limiter le nombre d’abonnements simultanés à** et entrez le nombre limite d’abonnements. Dans l’exemple suivant, les abonnements simultanés sont limités à quatre par compte de développeur.

![Quatre abonnements multiples][api-management-four-multiple-subscriptions]

Une fois que toutes les nouvelles options du produit sont configurées, cliquez sur **Enregistrer** pour créer ce dernier.

![Produits][api-management-products-page]

>Par défaut, les nouveaux produits ne sont pas publiés et ne sont visibles que pour les utilisateurs du groupe **Administrateurs**.

Pour configurer un produit, cliquez sur son nom dans l'onglet **Produits**.

## <a name="add-apis"> </a>Ajout d’API à un produit

La page **Produits** contient quatre liens de configuration : **Résumé**, **Paramètres**, **visibilité**, et **Abonnés**. L’onglet **Résumé** est celui dans lequel vous pouvez ajouter des API et publier un produit, ou en annuler la publication.

![Résumé][api-management-new-product-summary]

Avant de publier votre produit, vous devez ajouter une ou plusieurs API. Pour cela, cliquez sur **Ajouter l'API au produit**.

![Add APIs][api-management-add-apis-to-product]

Sélectionnez les API voulues, puis cliquez sur **Enregistrer**.

## <a name="add-description"> </a>Ajout d’informations descriptives à un produit

L'onglet **Paramètres** vous permet d'ajouter des informations détaillées sur le produit, comme son objectif, les API auxquelles il permet l'accès, ainsi que d'autres informations utiles. Le contenu est destiné aux développeurs qui appelleront l'API. Il peut être sous forme de texte brut ou au format HTML.

![Product settings][api-management-product-settings]

Cochez **Demander une approbation d’abonnement** pour créer un produit protégé dont l’utilisation requiert un abonnement, ou décochez la case à cocher pour créer un produit ouvert qui peut être appelé sans abonnement.

Sélectionnez **Demander une approbation d'abonnement** si vous souhaitez approuver manuellement toutes les demandes d'abonnement au produit. Par défaut, tous les abonnements sont acceptés automatiquement.

Pour autoriser les comptes de développeur à s’abonner plusieurs fois au produit, cochez la case **Autoriser plusieurs abonnements** et définissez une limite si besoin. Si cette case n’est pas cochée, chaque compte de développeur ne peut s’abonner qu’une seule fois pour le produit.

Vous pouvez si vous le souhaitez remplir le champ **Conditions d'utilisation** qui décrit les conditions d'utilisation que les abonnés doivent accepter pour pouvoir utiliser le produit.

## <a name="publish-product"> </a>Publication d’un produit

Avant de pouvoir appeler les API dans un produit, ce produit doit être publié. Dans l'onglet **Résumé** du produit, cliquez sur **Publier**, puis sur **Oui, publier** pour confirmer. Pour rendre privé un produit préalablement publié, cliquez sur **Annuler la publication**.

![Publish product][api-management-publish-product]

## <a name="make-visible"> </a>Rendre un produit visible pour les développeurs

L'onglet **Visibilité** vous permet de choisir quels rôles sont en mesure de voir le produit sur le portail des développeurs et de s'abonner à ce produit.

![Product visibility][api-management-product-visiblity]

Pour activer ou désactiver la visibilité d'un produit pour les développeurs d'un groupe, cochez ou décochez la case à côté du groupe, puis cliquez sur **Enregistrer**.

>Pour plus d'informations, consultez la page [Création et utilisation de groupes pour gérer les comptes de développeurs dans Gestion des API Azure][].

## <a name="view-subscribers"> </a>Affichage des abonnés à un produit

L’onglet **Abonnés** présente la liste des développeurs qui se sont abonnés au produit. Les détails et les paramètres de chaque développeur peuvent être consultés en cliquant sur le nom du développeur. Dans cet exemple, aucun développeur ne s'est encore abonné au produit.

![Développeurs][api-management-developer-list]

## <a name="next-steps"> </a>Étapes suivantes

Une fois les API souhaitées ajoutées et le produit publié, les développeurs peuvent s'abonner au produit et commencer à appeler les API. Pour suivre le didacticiel présentant comment configurer ces éléments, ainsi que certains paramètres de produit avancés, consultez la page [Création et configuration de paramètres de produit avancés dans Gestion des API Azure][].

Pour plus d’informations sur l’utilisation des produits, consultez la vidéo suivante.

> [AZURE.VIDEO using-products]

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[Affichage des abonnés à un produit]: #view-subscribers
[Next steps]: #next-steps

[api-management-management-console]: ./media/api-management-howto-add-products/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-add-products/api-management-add-product.png
[api-management-add-new-product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
[api-management-unlimited-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-unlimited-multiple-subscriptions.png
[api-management-four-multiple-subscriptions]: ./media/api-management-howto-add-products/api-management-four-multiple-subscriptions.png
[api-management-products-page]: ./media/api-management-howto-add-products/api-management-products-page.png
[api-management-new-product-summary]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
[api-management-add-apis-to-product]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
[api-management-product-settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
[api-management-publish-product]: ./media/api-management-howto-add-products/api-management-publish-product.png
[api-management-product-visiblity]: ./media/api-management-howto-add-products/api-management-product-visibility.png
[api-management-developer-list]: ./media/api-management-howto-add-products/api-management-developer-list.png



[api-management-products]: ./media/api-management-howto-add-products/api-management-products.png
[api-management-]: ./media/api-management-howto-add-products/
[api-management-]: ./media/api-management-howto-add-products/


[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[Prise en main de Gestion des API Azure]: api-management-get-started.md
[Création d'une instance de service Gestion des API]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps
[Création et utilisation de groupes pour gérer les comptes de développeurs dans Gestion des API Azure]: api-management-howto-create-groups.md
[Création et configuration de paramètres de produit avancés dans Gestion des API Azure]: api-management-howto-product-with-rules.md

<!---HONumber=Sept15_HO3-->