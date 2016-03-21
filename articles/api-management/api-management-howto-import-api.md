<properties 
	pageTitle="Principaux concepts du service API Management" 
	description="Learn about APIs, products, roles, groups, and other API Management key concepts." 
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
	ms.date="03/04/2016" 
	ms.author="sdanie"/>

# Importation de la définition d'une API avec des opérations dans Gestion des API Azure

Dans Gestion des API Azure, de nouvelles API peuvent être créées et les opérations ajoutées de façon manuelle. Il est aussi possible d’importer l’API avec les opérations, en une seule fois.

Les API et leurs opérations peuvent être importées dans les formats suivants.

-	WADL
-	Swagger

Ce guide vous présente comment créer une API et importer des opérations en une seule fois. Pour plus d'informations sur la création manuelle d'une API et l'ajout d'opérations, consultez les rubriques [Création d'API][] et [Ajout d'opérations à une API][].

## <a name="import-api"> </a>Importation d’une API

Les API sont créées et configurées dans le portail des éditeurs. Pour accéder au portail de publication, cliquez sur **Gérer** dans le portail Azure Classic de votre service Gestion des API. Si vous n'avez pas encore créé une instance de service Gestion des API, consultez la page [Création d'une instance de service Gestion des API][] dans le didacticiel [Prise en main de Gestion des API Azure][].

![Portail des éditeurs][api-management-management-console]

Cliquez sur **API** dans le menu **Gestion des API** à gauche, puis sur **Importer l'API**.

![Import API][api-management-import-apis]

La fenêtre **Importer l'API** comporte trois onglets, qui correspondent à trois façons de définir les spécifications de l'API.

-	**À partir du Presse-papiers** vous permet de coller la spécification API dans la zone de texte spécifiée.
-	**À partir d'un fichier** vous permet de choisir le fichier qui contient la spécification de l'API.
-	**À partir d'une URL** vous permet d'indiquer l'URL menant à la spécification de l'API.

![Import API format][api-management-import-api-clipboard]

Une fois la spécification de l'API fournie, utilisez les cases d'option à droite pour indiquer le format de la spécification. Les formats suivants sont pris en charge.

-	WADL
-	Swagger

Ensuite, entrez un **suffixe d'URL d'API web**. Il est ajouté à l'URL de base de votre service Gestion des API. L'URL de base est commune à toutes les API hébergées sur chaque instance du service Gestion des API. Gestion des API distingue les API selon leur suffixe. Celui-ci doit donc être unique pour chaque API d'une instance de service Gestion des API.

Une fois toutes les valeurs entrées, cliquez sur **Enregistrer** pour créer l'API et les opérations associées.

>[AZURE.NOTE] Pour voir un didacticiel sur l’importation d’une API de calculatrice de base au format Swagger, consultez [Gestion de votre première API dans Gestion des API Azure](api-management-get-started.md).

## <a name="export-api"> </a> Exportation d’une API

En plus de l’importation de nouvelles API, vous pouvez exporter les définitions de vos API depuis le portail des éditeurs. Pour cela, cliquez sur **Exporter l'API** dans l'onglet **Résumé** de votre **API**.

![Export API][api-management-export-api]

Les API peuvent être exportées avec WADL ou Swagger. Sélectionnez le format souhaité, cliquez sur **Enregistrer**, puis choisissez l'emplacement dans lequel enregistrer le fichier.

![Export API format][api-management-export-api-format]

## <a name="next-steps"> </a>Étapes suivantes

Une fois l'API créée et les opérations importées, vous pouvez vérifier et configurer les paramètres complémentaires, ajouter l'API à un produit et la publier pour la mettre à disposition des développeurs. Pour plus d'informations, consultez les guides suivants :

-	[Configuration des paramètres de l’API][]
-	[Création et publication d’un produit][]




[api-management-management-console]: ./media/api-management-howto-import-api/api-management-management-console.png
[api-management-import-apis]: ./media/api-management-howto-import-api/api-management-api-import-apis.png
[api-management-import-api-clipboard]: ./media/api-management-howto-import-api/api-management-import-api-wizard.png
[api-management-export-api]: ./media/api-management-howto-import-api/api-management-export-api.png
[api-management-export-api-format]: ./media/api-management-howto-import-api/api-management-export-api-format.png

[Import an API]: #import-api
[Export an API]: #export-api
[Configure API settings]: #configure-api-settings
[Next steps]: #next-steps

[Prise en main de Gestion des API Azure]: api-management-get-started.md
[Création d'une instance de service Gestion des API]: api-management-get-started.md#create-service-instance

[Ajout d'opérations à une API]: api-management-howto-add-operations.md
[Création et publication d’un produit]: api-management-howto-add-products.md
[Création d'API]: api-management-howto-create-apis.md
[Configuration des paramètres de l’API]: api-management-howto-create-apis.md#configure-api-settings

<!---------HONumber=AcomDC_0309_2016-->