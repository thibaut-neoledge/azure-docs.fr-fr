---
title: "Restrictions et problèmes connus relatifs à l’importation d’API dans la gestion des API Azure | Microsoft Docs"
description: "Détails des problèmes connus et des restrictions relatifs à l’importation dans la gestion des API Azure à l’aide des formats Open API, WSDL ou WADL."
services: api-management
documentationcenter: 
author: mattfarm
manager: vlvinogr
editor: 
ms.assetid: 7a5a63f0-3e72-49d3-a28c-1bb23ab495e2
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 02/08/2017
ms.author: apipm
translationtype: Human Translation
ms.sourcegitcommit: 2c9877f84873c825f96b62b492f49d1733e6c64e
ms.openlocfilehash: ac799d66b5038c207413086b0fa71239ff2a332f
ms.lasthandoff: 03/15/2017


---
# <a name="api-import-restrictions-and-known-issues"></a>Restrictions et problèmes connus relatifs à l’importation d’API
## <a name="about-this-list"></a>À propos de cette liste
Bien que tout soit mis en œuvre pour garantir que l’importation de votre API dans la gestion des API Azure soit aussi transparente et exempte de problèmes que possible, nous imposons parfois des restrictions ou identifions des problèmes qui devront être corrigés pour vous permettre d’importer correctement. Cet article documente ces restrictions et problèmes connus, organisés par format d’importation de l’API.

## <a name="open-api"> </a>Open API/Swagger
En général, si vous recevez des erreurs lors de l’importation de votre document Open API, vérifiez que vous l’avez validé à l’aide du concepteur dans le nouveau portail Azure (Conception - Principal - Open API Specification Editor) ou à l’aide d’un outil tiers tel que <a href="http://www.swagger.io">Swagger Editor</a>.

* **Nom d’hôte** : nous exigeons un attribut de nom d’hôte.
* **Chemin d’accès de base** : nous exigeons un attribut de chemin d’accès de base.
* **Schémas** : nous exigeons un tableau de schéma. 

## <a name="wsdl"> </a>WSDL
Les fichiers WSDL servent à générer des API Pass-through SOAP ou constituent le back-end d’une API SOAP à REST.

* **WSDL:Import** : nous ne prenons actuellement pas en charge les API utilisant cet attribut. Les clients doivent fusionner les éléments importés dans un seul document.
* Les **messages en plusieurs parties** ne sont actuellement pas pris en charge.
* **WCF wsHttpBinding** : les services SOAP créés avec Windows Communication Foundation doivent utiliser basicHttpBinding. wsHttpBinding n’est pas pris en charge.
* **MTOM** : les services utilisant MTOM <em>peuvent</em> fonctionner. Aucune prise en charge officielle n’est disponible pour l’instant.
* Les types de **récursivité** qui sont définis de manière récursive (par exemple, qui font référence à leur propre tableau) ne sont pas pris en charge.

## <a name="wadl"> </a>WADL
Il n’existe actuellement aucun problème connu relatif à l’importation WADL.


[api-management-management-console]: ./media/api-management-howto-add-operations/api-management-management-console.png
[api-management-operations]: ./media/api-management-howto-add-operations/api-management-operations.png
[api-management-add-operation]: ./media/api-management-howto-add-operations/api-management-add-operation.png
[api-management-http-method]: ./media/api-management-howto-add-operations/api-management-http-method.png
[api-management-url-template]: ./media/api-management-howto-add-operations/api-management-url-template.png
[api-management-url-template-rewrite]: ./media/api-management-howto-add-operations/api-management-url-template-rewrite.png
[api-management-description]: ./media/api-management-howto-add-operations/api-management-description.png
[api-management-caching-tab]: ./media/api-management-howto-add-operations/api-management-caching-tab.png
[api-management-request-parameters]: ./media/api-management-howto-add-operations/api-management-request-parameters.png
[api-management-request-body]: ./media/api-management-howto-add-operations/api-management-request-body.png
[api-management-response-code]: ./media/api-management-howto-add-operations/api-management-response-code.png
[api-management-response-body-content-type]: ./media/api-management-howto-add-operations/api-management-response-body-content-type.png
[api-management-response-body]: ./media/api-management-howto-add-operations/api-management-response-body.png


[api-management-contoso-api]: ./media/api-management-howto-add-operations/api-management-contoso-api.png

[api-management-add-new-api]: ./media/api-management-howto-add-operations/api-management-add-new-api.png
[api-management-api-settings]: ./media/api-management-howto-add-operations/api-management-api-settings.png
[api-management-api-settings-credentials]: ./media/api-management-howto-add-operations/api-management-api-settings-credentials.png
[api-management-api-summary]: ./media/api-management-howto-add-operations/api-management-api-summary.png
[api-management-echo-operations]: ./media/api-management-howto-add-operations/api-management-echo-operations.png

[Add an operation]: #add-operation
[Operation caching]: #operation-caching
[Request parameters]: #request-parameters
[Request body]: #request-body
[Responses]: #responses
[Next steps]: #next-steps

[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[How to create and publish a product]: api-management-howto-add-products.md
[How to cache operation results in Azure API Management]: api-management-howto-cache.md

