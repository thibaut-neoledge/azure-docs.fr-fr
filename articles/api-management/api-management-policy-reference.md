---
title: "Référence sur les stratégies Gestion des API Azure"
description: "Découvrez les stratégies disponibles pour configurer Gestion des API."
services: api-management
documentationcenter: 
author: vladvino
manager: erikre
editor: 
ms.assetid: 9d4ac609-b221-4032-93ae-e27a1254d43d
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/15/2016
ms.author: apimpm
ms.translationtype: Human Translation
ms.sourcegitcommit: 94e13ac6fec09081484a2f7f5d7bc1871822743f
ms.openlocfilehash: adc0c4415e10ddd0b4994cecef17f026546e91a1
ms.contentlocale: fr-fr
ms.lasthandoff: 07/06/2017


---
# <a name="azure-api-management-policy-reference"></a>Référence sur les stratégies Gestion des API Azure
Cette section fournit un index des stratégies dans la [Référence des stratégies de gestion des API][API Management policy reference]. Pour plus d’informations sur l’ajout et la configuration des stratégies, consultez la page [Stratégies dans Gestion des API][Policies in API Management].

Les expressions de stratégie peuvent être utilisées comme valeurs d’attribut ou valeurs de texte dans l’une des stratégies de Gestion des API, sauf si la stratégie le spécifie autrement. Certaines stratégies, telles que les stratégies [Contrôler le flux][Control flow] et [Définir la variable][Set variable], sont basées sur des expressions de stratégies. Pour plus d’informations, consultez les pages [Stratégies avancées][Advanced policies] et [Expressions de stratégie][Policy expressions].

## <a name="policy-reference-index"></a>Index de référence de stratégie
* [Accès aux stratégies de restriction][Access restriction policies]
  * [Check HTTP header][Check HTTP header] : applique l’existence et/ou la valeur d’un en-tête HTTP.
  * [Limit call rate by subscription][Limit call rate by subscription] : empêche les pics d’utilisation de l’API en limitant le débit d’appels par abonnement.
  * [Limit call rate by key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) : empêche les pics d’utilisation de l’API en limitant le débit d’appels par clé.
  * [Restrict caller IPs][Restrict caller IPs] : filtre (autorise/rejette) les appels de certaines adresses IP et/ou plages d’adresses en particulier.
  * [Set usage quota by subscription][Set usage quota by subscription] : vous permet d’appliquer un volume d’appels et/ou un quota de bande passante renouvelable ou illimité par abonnement.
  * [Set usage quota by key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) : vous permet d’appliquer un volume d’appel et/ou un quota de bande passante renouvelable ou illimité par clé.
  * [Validate JWT][Validate JWT] : applique l’existence et la validité d’un JWT extrait d’un en-tête HTTP ou d’un paramètre de requête spécifié.
* [Stratégies avancées][Advanced policies]
  * [Control flow][Control flow] : applique de manière conditionnelle les instructions des stratégies en fonction des résultats de l’évaluation des [expressions][expressions] booléennes.
  * [Forward request][Forward request] : transfère la demande au service principal.
  * [Log to Event Hub][Log to Event Hub] : envoie des messages au format spécifié à une cible de message définie par une entité [Enregistreur](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger).
  * [Retry](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Retry) : effectue une nouvelle tentative d’exécution des instructions de stratégie incluses, si la condition est remplie et jusqu’à ce qu’elle le soit. L’exécution se répète à intervalles réguliers et ce jusqu’au nombre de tentatives défini.
  * [Return response](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) : abandonne l’exécution du pipeline et renvoie la réponse indiquée directement à l’appelant.
  * [Send one way request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) : envoie une demande à l’URL indiquée sans attendre de réponse.
  * [Send request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) : envoie une demande à l’URL indiquée.
  * [Set request method](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) : permet de modifier la méthode HTTP d’une demande.
  * [Set status](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) : permet de définir le code d’état HTTP sur la valeur indiquée.
  * [Set variable][Set variable] : conserve une valeur dans une variable de [contexte][context] nommée pour permettre d’y accéder ultérieurement.
  * [Trace](https://msdn.microsoft.com/en-us/library/dn894085.aspx#Trace) : ajoute une chaîne à la sortie de l’[inspecteur d’API](api-management-howto-api-inspector.md).
  * [Wait](https://msdn.microsoft.com/library/azure/dn894085.aspx#Wait) : attend l’exécution de la demande d’envoi intégré, la récupération de la valeur du cache ou le contrôle des stratégies du flux pour continuer.
* [Stratégies d’authentification][Authentication policies]
  * [Authenticate with Basic][Authenticate with Basic] : authentification avec un service principal à l’aide de l’authentification de base.
  * [Authenticate with client certificate][Authenticate with client certificate] : authentification avec un service principal à l’aide de certificats clients.
* [Stratégies de mise en cache][Caching policies] 
  * [Get from cache][Get from cache] : effectue une recherche dans le cache et renvoie une réponse mise en cache valide si elle est disponible.
  * [Store to cache][Store to cache] : met en cache la réponse en fonction de la configuration de contrôle de cache spécifiée.
  * [Get value from cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) : récupère un élément mis en cache par clé.
  * [Store value in cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) : stocke un élément mis en cache par clé.
  * [Remove value from cache](https://msdn.microsoft.com/en-us/library/dn894086.aspx#RemoveCacheByKey) : supprime un élément du cache par clé.
* [Stratégies interdomaines][Cross domain policies] 
  * [Allow cross-domain calls][Allow cross-domain calls] : rend l’API accessible à partir des navigateurs clients utilisant Adobe Flash et Microsoft Silverlight.
  * [CORS][CORS] : ajoute une prise en charge partage des ressources cross-origin (CORS) à une opération ou une API afin de permettre les appels interdomaines à partir des navigateurs clients.
  * [JSONP][JSONP] : ajoute une prise en charge de JSON avec remplissage (JSONP) à une opération ou une API afin de permettre les appels interdomaines à partir des navigateurs clients utilisant JavaScript.
* [Stratégies de transformation][Transformation policies] 
  * [Convert JSON to XML][Convert JSON to XML] : convertit le corps de la demande ou de la réponse de JSON en XML.
  * [Convert XML to JSON][Convert XML to JSON] : convertit le corps de la demande ou de la réponse de XML en JSON.
  * [Find and replace string in body][Find and replace string in body] : recherche une sous-chaîne de demande ou de réponse et la remplace par une autre sous-chaîne.
  * [Mask URLs in content][Mask URLs in content] : réécrit (masque) les liens dans le corps de la réponse afin qu’ils pointent vers un lien équivalent par l’intermédiaire de la passerelle.
  * [Set backend service][Set backend service] : modifie le service principal pour une demande entrante.
  * [Set body][Set body] : définit le corps du message pour les demandes entrantes et sortantes.
  * [Set HTTP header][Set HTTP header] : affecte une valeur à un en-tête de réponse et/ou de demande existant ou bien ajoute un nouvel en-tête de réponse et/ou de demande.
  * [Set query string parameter][Set query string parameter] : ajoute, supprime un paramètre de chaîne de requête de la demande ou le remplace par une autre valeur.
  * [URL de réécriture][Rewrite URL] : convertit une URL de demande de sa forme publique en une forme attendue par le service web.

## <a name="next-steps"></a>Étapes suivantes
Pour plus d’informations sur les expressions de stratégie, regardez la vidéo suivante.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Policy-Expressions-in-Azure-API-Management/player]
> 
> 

[Access restriction policies]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Check HTTP header]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Limit call rate by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Restrict caller IPs]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Set usage quota by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Validate JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Advanced policies]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[context]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Forward request]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Log to Event Hub]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Authentication policies]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Authenticate with Basic]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Authenticate with client certificate]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Caching policies]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Get from cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Store to cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Cross domain policies]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Allow cross-domain calls]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Transformation policies]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Convert JSON to XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Convert XML to JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Find and replace string in body]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[Mask URLs in content]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Set backend service]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Set body]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[Set HTTP header]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Set query string parameter]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[Rewrite URL]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Policies in API Management]: api-management-howto-policies.md
[API Management policy reference]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Policy expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx



