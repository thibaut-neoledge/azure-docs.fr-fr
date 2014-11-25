<properties pageTitle="API Management key concepts" metaKeywords="" description="Learn about APIs, products, roles, groups, and other API Management key concepts." metaCanonical="" services="" documentationCenter="API Management" title="API Management key concepts" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Importation de la définition d'une API avec des opérations dans Gestion des API Azure

Dans Gestion des API Azure (version préliminaire), de nouvelles API peuvent être créées et les opérations ajoutées de façon manuelle. Il est aussi possible d'importer l'API avec l'opération, en une seule fois.

Les API et leurs opérations peuvent être importées dans les formats suivants.

-   WADL
-   Swagger

Ce guide vous présente comment créer une API et importer des opérations en une seule fois.

> Pour plus d'informations sur la création manuelle d'une API et l'ajout d'opérations, consultez les rubriques [Création d'API][Création d'API] et [Ajout d'opérations à une API][Ajout d'opérations à une API].

## Dans cette rubrique

-   [Importation d'une API][Importation d'une API]
-   [Exportation d'une API][Exportation d'une API]
-   [Étapes suivantes][Étapes suivantes]

## <a name="import-api"> </a>Importation d'une API

Pour créer et configurer des API, cliquez sur **Console de gestion** dans le portail Azure de votre service Gestion des API. Vous accéderez au portail d'administration Gestion des API.

> Si vous n'avez pas encore créé une instance de service Gestion des API, consultez la page [Création d'une instance de service Gestion des API][Création d'une instance de service Gestion des API] dans le didacticiel [Prise en main de Gestion des API Azure][Prise en main de Gestion des API Azure].

![Management console][Management console]

Cliquez sur **API** dans le menu **Gestion des API** à gauche, puis sur **Importer l'API**.

![Import API][Import API]

La fenêtre **Importer l'API** comporte trois onglets, qui correspondent à trois façons de définir les spécifications de l'API.

-   **À partir du Presse-papiers** vous permet de coller la spécification API dans la zone de texte spécifiée.
-   **À partir d'un fichier** vous permet de choisir le fichier qui contient la spécification de l'API.
-   **À partir d'une URL** vous permet d'indiquer l'URL menant à la spécification de l'API.

![Import API format][Import API format]

Une fois la spécification de l'API fournie, utilisez les cases d'option à droite pour indiquer le format de la spécification. Les formats suivants sont pris en charge.

-   WADL
-   Swagger

Ensuite, entrez un **suffixe d'URL d'API web**. Il est ajouté à l'URL de base de votre service Gestion des API. L'URL de base est commune à toutes les API hébergées sur chaque instance du service Gestion des API. Gestion des API distingue les API selon leur suffixe. Celui-ci doit donc être unique pour chaque API d'une instance de service Gestion des API.

Une fois toutes les valeurs entrées, cliquez sur **Enregistrer** pour créer l'API et les opérations associées.

## <a name="export-api"> </a>Exportation d'une API

En plus de l'importation de nouvelles API, vous pouvez exporter les définitions de vos API depuis la console de gestion. Pour cela, cliquez sur **Exporter l'API** dans l'onglet **Résumé** de votre **API**.

![Export API][Export API]

Les API peuvent être exportées avec WADL ou Swagger. Sélectionnez le format souhaité, cliquez sur **Enregistrer**, puis choisissez l'emplacement dans lequel enregistrer le fichier.

![Export API format][Export API format]

## <a name="next-steps"> </a>Étapes suivantes

Une fois l'API créée et les opérations importées, vous pouvez vérifier et configurer les paramètres complémentaires, ajouter l'API à un produit et la publier pour la mettre à disposition des développeurs. Pour plus d'informations, consultez les guides suivants :

-   [Configuration des paramètres de l'API][Configuration des paramètres de l'API]
-   [Création et publication d'un produit][Création et publication d'un produit]

  [Création d'API]: ../api-management-howto-create-apis
  [Ajout d'opérations à une API]: ../api-management-howto-add-operations
  [Importation d'une API]: #import-api
  [Exportation d'une API]: #export-api
  [Étapes suivantes]: #next-steps
  [Création d'une instance de service Gestion des API]: ../api-management-get-started/#create-service-instance
  [Prise en main de Gestion des API Azure]: ../api-management-get-started
  [Management console]: ./media/api-management-howto-import-api/api-management-management-console.png
  [Import API]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
  [Import API format]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
  [Export API]: ./media/api-management-howto-import-api/api-management-export-api.png
  [Export API format]: ./media/api-management-howto-import-api/api-management-export-api-format.png
  [Configuration des paramètres de l'API]: ../api-management-howto-create-apis/#configure-api-settings
  [Création et publication d'un produit]: ../api-management-howto-add-products
