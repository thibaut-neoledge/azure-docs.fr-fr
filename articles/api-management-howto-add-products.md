<properties pageTitle="How create and publish a product in Azure API Management" metaKeywords="" description="Learn how to create and publish products in Azure API Management." metaCanonical="" services="" documentationCenter="API Management" title="How create and publish a product in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie"></tags>

# Création et publication d'un produit dans Gestion des API Azure

Dans Gestion des API Azure (version préliminaire), un produit contient une ou plusieurs API, ainsi qu'un quota et des conditions d'utilisation. Une fois le produit publié, les développeurs peuvent s'y abonner et commencer à utiliser ses API. Cette rubrique constitue un guide pour créer un produit, ajouter une API et le publier pour les développeurs.

## Dans cette rubrique

-   [Création d'un produit][]
-   [Ajout d'API à un produit][]
-   [Ajout d'informations descriptives à un produit][]
-   [Publication d'un produit][]
-   [Rendre un produit visible pour les développeurs][]
-   [Affichage des abonnés à un produit][]
-   [Étapes suivantes][]

## <a name="create-product"> </a>Création d'un produit

Les opérations sont ajoutées et configurées dans une API dans la console de gestion. Pour accéder à la console de gestion, cliquez sur **Console de gestion** dans le portail Azure de votre service Gestion des API.

> Si vous n'avez pas encore créé une instance de service Gestion des API, consultez la page [Création d'une instance de service Gestion des API][] dans le didacticiel [Prise en main de Gestion des API Azure][].

![API Management console][]

Cliquez sur **Produits** dans le menu à gauche pour afficher la page **Produits**, puis cliquez sur **Ajouter un produit**.

![Produits][]

![New product][]

Entrez un nom décrivant le produit dans le champ **Nom**, ainsi qu'une description du produit dans le champ **Description**.

Cochez **Demander une approbation d'abonnement** si vous souhaitez qu'un administrateur révise et accepte ou refuse les tentatives d'abonnement à ce produit. Si la case n'est pas cochée, les tentatives d'abonnement seront automatiquement approuvées. Pour plus d'informations sur les abonnements, consultez la section [Affichage des abonnés à un produit][].

![Produits][1]

> Par défaut, les nouveaux produits ne sont pas publiés et ne sont visibles que pour les utilisateurs du groupe **Administrateurs**.

Pour configurer un produit, cliquez sur son nom dans l'onglet **Produits**.

## <a name="add-apis"> </a>Ajout d'API à un produit

La page **Produits** contient quatre liens de configuration : **Résumé**, **Paramètres**, **Visibilité** et **Développeurs**. L'onglet **Résumé** est celui dans lequel vous pouvez ajouter des API et publier un produit, ou en annuler la publication.

![Résumé][]

Avant de publier votre produit, vous devez ajouter une ou plusieurs API. Pour cela, cliquez sur **Ajouter l'API au produit**.

![Add APIs][]

Sélectionnez les API voulues, puis cliquez sur **Enregistrer**.

## <a name="add-description"> </a>Ajout d'informations descriptives à un produit

L'onglet **Paramètres** vous permet d'ajouter des informations détaillées sur le produit, comme son objectif, les API auxquelles il permet l'accès, ainsi que d'autres informations utiles. Le contenu est destiné aux développeurs qui appelleront l'API. Il peut être sous forme de texte brut ou au format HTML.

![Product settings][]

Sélectionnez **Demander une approbation d'abonnement** si vous souhaitez approuver manuellement toutes les demandes d'abonnement au produit. Par défaut, tous les abonnements sont acceptés automatiquement.

Vous pouvez si vous le souhaitez remplir le champ **Conditions d'utilisation** qui décrit les conditions d'utilisation que les abonnés doivent accepter pour pouvoir utiliser le produit.

## <a name="publish-product"> </a>Publication d'un produit

Avant de pouvoir appeler les API dans un produit, ce produit doit être publié. Dans l'onglet **Résumé** du produit, cliquez sur **Publier**, puis sur **Oui, publier** pour confirmer. Pour rendre privé un produit préalablement publié, cliquez sur **Annuler la publication**.

![Publish product][]

## <a name="make-visible"> </a>Rendre un produit visible pour les développeurs

L'onglet **Visibilité** vous permet de choisir quels rôles sont en mesure de voir le produit sur le portail des développeurs et de s'abonner à ce produit.

![Product visibility][]

Pour activer ou désactiver la visibilité d'un produit pour les développeurs d'un groupe, cochez ou décochez la case à côté du groupe, puis cliquez sur **Enregistrer**.

> Pour plus d'informations, consultez la page [Création et utilisation de groupes pour gérer les comptes de développeurs dans Gestion des API Azure][].

## <a name="view-subscribers"> </a>Affichage des abonnés à un produit

L'onglet **Développeurs** présente la liste des développeurs qui se sont abonnés au produit. Les détails et les paramètres de chaque développeur peuvent être consultés en cliquant sur le nom du développeur. Dans cet exemple, aucun développeur ne s'est encore abonné au produit.

![Développeurs][]

## <a name="next-steps"> </a>Étapes suivantes

Une fois les API souhaitées ajoutées et le produit publié, les développeurs peuvent s'abonner au produit et commencer à appeler les API. Pour suivre le didacticiel présentant comment configurer ces éléments, ainsi que certains paramètres de produit avancés, consultez la page [Création et configuration de paramètres de produit avancés dans Gestion des API Azure][].

  [Création d'un produit]: #create-product
  [Ajout d'API à un produit]: #add-apis
  [Ajout d'informations descriptives à un produit]: #add-description
  [Publication d'un produit]: #publish-product
  [Rendre un produit visible pour les développeurs]: #make-visible
  [Affichage des abonnés à un produit]: #view-subscribers
  [Étapes suivantes]: #next-steps
  [Création d'une instance de service Gestion des API]: ../api-management-get-started/#create-service-instance
  [Prise en main de Gestion des API Azure]: ../api-management-get-started
  [API Management console]: ./media/api-management-howto-add-products/api-management-management-console.png
  [Produits]: ./media/api-management-howto-add-products/api-management-products.png
  [New product]: ./media/api-management-howto-add-products/api-management-add-new-product.png
  [1]: ./media/api-management-howto-add-products/api-management-products-page.png
  [Résumé]: ./media/api-management-howto-add-products/api-management-new-product-summary.png
  [Add APIs]: ./media/api-management-howto-add-products/api-management-add-apis-to-product.png
  [Product settings]: ./media/api-management-howto-add-products/api-management-product-settings.png
  [Publish product]: ./media/api-management-howto-add-products/api-management-publish-product.png
  [Product visibility]: ./media/api-management-howto-add-products/api-management-product-visibility.png
  [Création et utilisation de groupes pour gérer les comptes de développeurs dans Gestion des API Azure]: ../api-management-howto-create-groups
  [Développeurs]: ./media/api-management-howto-add-products/api-management-developer-list.png
  [Création et configuration de paramètres de produit avancés dans Gestion des API Azure]: ../api-management-howto-product-with-rules
