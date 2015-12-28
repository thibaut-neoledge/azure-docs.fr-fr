<properties 
	pageTitle="Référence sur les stratégies Gestion des API Azure" 
	description="Découvrez les stratégies disponibles pour configurer Gestion des API." 
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
	ms.date="12/16/2015" 
	ms.author="sdanie"/>

# Référence sur les stratégies Gestion des API Azure

Cette section fournit un index des stratégies dans la [référence des stratégies de gestion des API][]. Pour plus d'informations sur l'ajout et la configuration des stratégies, consultez la page [Stratégies dans Gestion des API][].

Les expressions de stratégie peuvent être utilisées comme valeurs d’attribut ou valeurs de texte dans l’une des stratégies de Gestion des API, sauf si la stratégie le spécifie autrement. Certaines stratégies, telles que les stratégies [Contrôler le flux][] et [Définir la variable][], sont basées sur des expressions de stratégie. Pour plus d’informations, consultez les rubriques [Stratégies avancées][], [Expressions de stratégie][].

## Index de référence de stratégie

-	[Accès aux stratégies de restriction][]
	-	[Check HTTP header][] : applique l’existence et/ou la valeur d’un en-tête HTTP.
	-	[Limit call rate by subscription][] : empêche les pics d’utilisation de l’API en limitant le débit d’appels par abonnement.
	-	[Limit call rate by key](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) : empêche les pics d’utilisation de l’API en limitant le débit d’appels par clé.
	-	[Restrict caller IPs][] : filtre (autorise/rejette) les appels de certaines adresses IP spécifiques et/ou de certaines plages d’adresses.
	-	[Set usage quota by subscription][] : vous permet d’appliquer un volume d’appel et/ou un quota de bande passante renouvelable ou illimité par abonnement.
	-	[Set usage quota by key](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey) : vous permet d’appliquer un volume d’appel et/ou un quota de bande passante renouvelable ou illimité par clé.
	-	[Validate JWT][] : applique l’existence et la validité d’un JWT extrait d’un en-tête HTTP ou d’un paramètre de requête spécifié.
-	[Stratégies avancées][]
	-	[Control flow][] : applique de manière conditionnelle les instructions de stratégie en fonction des résultats de l’évaluation des [expressions][] booléennes.
	-	[Forward request][] : transfère la demande vers le service principal.
	-	[Log to Event Hub][] : envoie des messages au format spécifié à une cible de message définie par une entité [Enregistreur](https://msdn.microsoft.com/library/azure/mt592020.aspx#Logger).
	-	[Return response](https://msdn.microsoft.com/library/azure/dn894085.aspx#ReturnResponse) : abandonne l’exécution du pipeline et renvoie la réponse indiquée directement à l’appelant.
	-	[Send one way request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendOneWayRequest) : envoie une demande à l’URL indiquée sans attendre de réponse.
	-	[Send request](https://msdn.microsoft.com/library/azure/dn894085.aspx#SendRequest) : envoie une demande à l’URL indiquée.
	-	[Set request method](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetRequestMethod) : permet de modifier la méthode HTTP d’une demande.
	-	[Set status](https://msdn.microsoft.com/library/azure/dn894085.aspx#SetStatus) : permet de définir le code d’état HTTP sur la valeur indiquée.
	-	[Set variable][] : conserve une valeur dans une variable de [contexte][] nommée pour y accéder ultérieurement.
-	[Stratégies d’authentification][]
	-	[Authenticate with Basic][] : authentification avec un service principal à l’aide de l’authentification de base.
	-	[Authenticate with client certificate][] : authentification avec un service principal à l’aide de certificats clients.
-	[Stratégies de mise en cache][] 
	-	[Get from cache][] : effectue une recherche dans le cache et renvoie une réponse mise en cache valide si elle est disponible.
	-	[Store to cache][] : met en cache la réponse en fonction de la configuration de contrôle de cache spécifiée.
	-	[Get value from cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#GetFromCacheByKey) : récupère un élément mis en cache par clé.
	-	[Store value in cache](https://msdn.microsoft.com/library/azure/dn894086.aspx#StoreToCacheByKey) : stocke un élément mis en cache par clé.
-	[Stratégies interdomaines][] 
	-	[Allow cross-domain calls][] : rend l'API accessible depuis les navigateurs clients utilisant Adobe Flash et Microsoft Silverlight.
	-	[CORS][] : ajoute une prise en charge partage des ressources cross-origin (CORS) à une opération ou une API afin de permettre les appels interdomaines depuis les navigateurs clients.
	-	[JSONP][] : ajoute une prise en charge de JSON avec remplissage (JSONP) à une opération ou une API afin de permettre les appels interdomaines depuis les navigateurs clients utilisant JavaScript.
-	[Stratégies de transformation][] 
	-	[Convert JSON to XML][] : convertit le corps de la demande ou de la réponse de JSON en XML.
	-	[Convert XML to JSON][] :convertit le corps de la demande ou de la réponse de XML en JSON.
	-	[Find and replace string in body][] : recherche une sous-chaîne de demande ou de réponse et la remplace par une autre sous-chaîne.
	-	[Mask URLs in content][] : réécrit (masque) les liens dans le corps de la réponse et dans l’en-tête de l’emplacement afin qu’ils pointent vers un lien équivalent via la passerelle.
	-	[Set backend service][] : modifie le service principal pour une demande entrante.
	-	[Set body][] : définit le corps du message pour les demandes entrantes et sortantes.
	-	[Set HTTP header][] : affecte une valeur à un en-tête de réponse et/ou de demande existant ou bien ajoute un nouvel en-tête de réponse et/ou de demande.
	-	[Set query string parameter][] : ajoute, supprime un paramètre de chaîne de requête de la demande ou le remplace par une autre valeur.
	-	[URL de réécriture][] : convertit une URL de demande de sa forme publique en une forme attendue par le service web.

## Étapes suivantes

Pour plus d’informations sur les expressions de stratégie, regardez la vidéo suivante.

> [AZURE.VIDEO policy-expressions-in-azure-api-management]

[Accès aux stratégies de restriction]: https://msdn.microsoft.com/library/azure/dn894078.aspx
[Check HTTP header]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#CheckHTTPHeader
[Limit call rate by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#LimitCallRate
[Restrict caller IPs]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#RestrictCallerIPs
[Set usage quota by subscription]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#SetUsageQuota
[Validate JWT]: https://msdn.microsoft.com/library/azure/034febe3-465f-4840-9fc6-c448ef520b0f#ValidateJWT

[Stratégies avancées]: https://msdn.microsoft.com/library/azure/dn894085.aspx
[Control flow]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Contrôler le flux]: https://msdn.microsoft.com/library/azure/dn894085.aspx#choose
[Set variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[Définir la variable]: https://msdn.microsoft.com/library/azure/dn894085.aspx#set_variable
[expressions]: https://msdn.microsoft.com/library/azure/dn910913.aspx
[contexte]: https://msdn.microsoft.com/library/azure/ea160028-fc04-4782-aa26-4b8329df3448#ContextVariables
[Forward request]: https://msdn.microsoft.com/library/azure/dn894085.aspx#ForwardRequest
[Log to Event Hub]: https://msdn.microsoft.com/library/azure/dn894085.aspx#log-to-eventhub

[Stratégies d’authentification]: https://msdn.microsoft.com/library/azure/dn894079.aspx
[Authenticate with Basic]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#Basic
[Authenticate with client certificate]: https://msdn.microsoft.com/library/azure/061702a7-3a78-472b-a54a-f3b1e332490d#ClientCertificate
[Stratégies de mise en cache]: https://msdn.microsoft.com/library/azure/dn894086.aspx
[Get from cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#GetFromCache
[Store to cache]: https://msdn.microsoft.com/library/azure/8147199c-24d8-439f-b2a9-da28a70a890c#StoreToCache

[Stratégies interdomaines]: https://msdn.microsoft.com/library/azure/dn894084.aspx
[Allow cross-domain calls]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#AllowCrossDomainCalls
[CORS]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#CORS
[JSONP]: https://msdn.microsoft.com/library/azure/7689d277-8abe-472a-a78c-e6d4bd43455d#JSONP

[Stratégies de transformation]: https://msdn.microsoft.com/library/azure/dn894083.aspx
[Convert JSON to XML]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertJSONtoXML
[Convert XML to JSON]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#ConvertXMLtoJSON
[Find and replace string in body]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#Findandreplacestringinbody
[Mask URLs in content]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#MaskURLSContent
[Set backend service]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetBackendService
[Set body]: https://msdn.microsoft.com/library/azure/dn894083.aspx#SetBody
[Set HTTP header]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetHTTPheader
[Set query string parameter]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#SetQueryStringParameter
[URL de réécriture]: https://msdn.microsoft.com/library/azure/7406a8ce-5f9c-4fae-9b0f-e574befb2ee9#RewriteURL



[Stratégies dans Gestion des API]: api-management-howto-policies.md
[référence des stratégies de gestion des API]: https://msdn.microsoft.com/library/azure/dn894081.aspx

[Expressions de stratégie]: https://msdn.microsoft.com/library/azure/dn910913.aspx

 

<!---HONumber=AcomDC_1217_2015-->