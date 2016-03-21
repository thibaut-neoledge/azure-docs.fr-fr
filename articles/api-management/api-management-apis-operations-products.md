<properties 
	pageTitle="Création d'API, d'opérations et de produits dans le service Gestion des API Azure" 
	description="Apprenez à créer des API, des opérations et des produits dans Gestion des API." 
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
	ms.date="02/01/2016" 
	ms.author="sdanie"/>

# Création d'API, d'opérations et de produits dans le service Gestion des API Azure

Dans le service Gestion des API Azure, les API et leurs opérations sont ajoutées aux produits, où elles peuvent être utilisées par les développeurs générant des applications utilisant les API. Les guides de cette section présentent comment créer une API, y ajouter des opérations et associer l'API à un produit, puis la publier pour que les développeurs puissent l'utiliser.

## <a name="create-apis"> </a>Création d’API

Une API du service Gestion des API représente un ensemble d'opérations qui peuvent être appelées par des applications clientes. Les API sont créées dans le portail de publication.

Ce guide présente comment créer et configurer une nouvelle API dans Gestion des API.

-   [Création d’API][]

## <a name="add-operations"> </a>Ajout d’opérations à une API

Pour qu'une API puisse être utilisée dans Gestion des API, vous devez ajouter des opérations. Ce guide présente comment ajouter et configurer différents types d'opérations pour une API dans Gestion des API.

-   [Ajout d’opérations à une API][]

Une API et ses opérations peuvent également être importées en une étape, au format WADL ou Swagger.

-	[Importation de la définition d’une API avec des opérations][]

## <a name="add-product"> </a>Création et publication d’un produit

Dans Gestion des API, un produit contient une ou plusieurs API, ainsi qu'un quota et des conditions d'utilisation. Une fois le produit publié, les développeurs peuvent s'y abonner et commencer à utiliser ses API. Ces rubriques constituent un guide pour créer un produit, l'ajouter à une API et le publier pour les développeurs.

-   [Ajout et publication d’un produit][]
-	[Création et configuration de paramètres de produit avancés][]

[Create a product]: #create-product
[Add APIs to a product]: #add-apis
[Add descriptive information to a product]: #add-description
[Publish a product]: #publish-product
[Make a product visible to developers]: #make-visible
[View subscribers to a product]: #view-subscribers
[Next steps]: #next-steps

[api-management-]: ./media/

[Création d’API]: api-management-howto-create-apis.md
[Ajout d’opérations à une API]: api-management-howto-add-operations.md
[Ajout et publication d’un produit]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Importation de la définition d’une API avec des opérations]: api-management-howto-import-api.md
[Création et configuration de paramètres de produit avancés]: api-management-howto-product-with-rules.md

<!---HONumber=AcomDC_0309_2016-->