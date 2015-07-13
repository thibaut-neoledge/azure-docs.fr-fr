<properties 
	pageTitle="Ajout d'opérations à une API dans Gestion des API Azure" 
	description="Découvrez comment ajouter des opérations à une API dans Gestion des API Azure." 
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
	ms.date="06/16/2015" 
	ms.author="sdanie"/>

# Ajout d'opérations à une API dans Gestion des API Azure

Pour qu'une API puisse être utilisée dans Gestion des API, vous devez ajouter des opérations. Ce guide présente comment ajouter et configurer différents types d'opérations pour une API dans Gestion des API.

## <a name="add-operation"> </a>Ajout d’une opération

Les opérations sont ajoutées et configurées dans une API sur le portail des éditeurs. Pour accéder au portail des éditeurs, cliquez sur **Gérer** dans le portail Azure de votre service Gestion des API.

![Portail des éditeurs][api-management-management-console]

>Si vous n'avez pas encore créé une instance de service Gestion des API, consultez la page [Création d'une instance de service Gestion des API][] dans le didacticiel [Prise en main de Gestion des API Azure][].

Sélectionnez l'API souhaitée dans le portail Gestion des API, puis sélectionnez l'onglet **Opérations**.

![Opérations][api-management-operations]

Cliquez sur **Ajouter une opération** pour ajouter une nouvelle opération. **Nouvelle opération** s'affiche et l'onglet **Signature** est sélectionné par défaut.

![Add operation][api-management-add-operation]

Spécifiez le **verbe HTTP** en le choisissant dans la liste déroulante.

![HTTP method][api-management-http-method]

Définissez le modèle d'URL en tapant un fragment d'URL comprenant un ou plusieurs segments de chemin d'URL et aucun ou plusieurs paramètres de chaîne de requête. Le modèle d'URL, ajouté à l'URL de base de l'API, identifie de façon unique l'opération HTTP. Il peut contenir une ou plusieurs parties variables identifiées par des accolades. Ces parties variables sont appelées paramètres de modèle. Des valeurs leur sont affectées de façon dynamique à partir de l'URL de la demande lorsque la demande est traitée par la plateforme Gestion des API.

![URL template][api-management-url-template]

Si vous le souhaitez, spécifiez le **modèle de réécriture de l'URL**. Ceci vous permet d'utiliser le modèle d'URL standard pour traiter les demandes entrantes dans la partie frontale, tout en faisant appel à la partie principale via une URL convertie selon le modèle de réécriture. Les paramètres du modèle d'URL doivent être utilisés dans le modèle de réécriture. L'exemple qui suit montre comment un type de contenu codé sous forme de segment de chemin dans le service web de l'exemple précédent peut être utilisé comme paramètre de requête dans l'API publiée via la plateforme Gestion des API à l'aide des modèles d'URL.

![URL template rewrite][api-management-url-template-rewrite]

Les appelants de l’opération utilisent le format `/customers?customerid=ALFKI`, qui est mappé sur `/Customers('ALFKI')` lors de l’appel du service principal.


**Nom d'affichage** et **Description** fournissent une description de l'opération et offrent des informations aux développeurs utilisant cette API dans le portail des développeurs.

![Description][api-management-description]

La description de l'opération peut être ajoutée en texte brut ou au format HTML dans la zone de texte **Description**.

## <a name="operation-caching"> </a>Mise en cache de l’opération

La mise en cache de la réponse réduit le temps de latence perçu par les consommateurs de l'API, réduit la bande passante consommée et allège la charge sur le service web HTTP qui utilise l'API.

Pour activer facilement et rapidement la mise en cache pour une opération, sélectionnez l'onglet **Mise en cache** et activez la case à cocher **Activer**.

![Mise en cache][api-management-caching-tab]

**Durée** spécifie la durée pendant laquelle la réponse de l'opération reste dans le cache. La valeur par défaut est de 3600 secondes (1 heure).

Les clés de cache permettent de faire la distinction entre les réponses, afin que la réponse correspondant à chaque clé de cache obtienne sa propre valeur mise en cache. Vous pouvez également entrer des paramètres de chaîne de requête spécifiques et/ou des en-têtes HTTP à utiliser pour calculer les valeurs de clés de cache dans les zones de texte **Variation par paramètres de chaîne de requête** et **Variation par en-têtes**. Si aucune valeur n’est spécifiée, l’URL complète de la demande et les valeurs d’en-tête HTTP suivantes sont utilisées pour générer la clé de cache : **Accept** et **Accept-Charset**.

>Pour plus d'informations sur la mise en cache et les stratégies associée, consultez la page [Mise en cache des résultats d'opérations dans Gestion des API Azure][].


## <a name="request-parameters"> </a>Paramètres de la demande

Les paramètres de l'opération sont gérés dans l'onglet Paramètres. Les paramètres spécifiés dans **Modèle d'URL**, dans l'onglet **Signature**, sont automatiquement ajoutés et ne peuvent être changés qu'en modifiant le modèle d'URL. D'autres paramètres peuvent être ajoutés manuellement.

Pour ajouter un nouveau paramètre de requête, cliquez sur **Ajouter des paramètres de requête** et entrez les informations suivantes :

-	**Nom** : nom du paramètre.
-	**Description** : courte description du paramètre (facultatif).
-	**Type** : type de paramètre, sélectionné dans la liste déroulante.
-	**Valeurs** : valeurs qui peuvent être affectées à ce paramètre. Une des valeurs peut être marquée comme valeur par défaut (facultatif).
-	**Obligatoire** : activez la case à cocher pour rendre le paramètre obligatoire. 

![Paramètres de demande][api-management-request-parameters]

## <a name="request-body"> </a>Corps de la demande

Si l'opération l'autorise (par exemple PUT, POST) et qu'elle nécessite un corps, vous pouvez fournir un exemple dans un des formats pris en charge (JSON, XML, etc.).

>Le corps de la demande est utilisé uniquement pour information et n'est pas validé.

Pour entrer le corps de la demande, passez dans l'onglet **Corps**.

Cliquez sur **Ajouter une représentation**, tapez le nom du type de contenu (par exemple application/json), sélectionnez-le dans la liste déroulante, puis collez l'exemple souhaité au format sélectionné dans la zone de texte.

![Corps de la demande][api-management-request-body]

En plus des représentations, vous pouvez également spécifier une description dans la zone de texte **Description**.

## <a name="responses"> </a>Réponses

Il est conseillé de fournir des exemples de réponses pour tous les codes d'état que l'opération peut produire. Chaque code d'état peut avoir plusieurs exemples de corps de réponse, un pour chacun des types de contenu pris en charge.

Pour ajouter une réponse, cliquez sur **Ajouter**, puis tapez le code d'état souhaité. Dans cet exemple, le code d'état est **200 OK**. Une fois le code affiché dans la liste déroulante, sélectionnez-le. Le code de réponse est alors créé et ajouté à votre opération.

![Response code][api-management-response-code]

Cliquez sur **Ajouter une représentation**, commencez à taper le nom du type de contenu (par exemple application/json), puis sélectionnez-le dans la liste déroulante.

![Body content type][api-management-response-body-content-type]

Collez l'exemple de corps de la réponse au format sélectionné dans la zone de texte.

![Response body][api-management-response-body]

Si vous le souhaitez, vous pouvez spécifier une description dans la zone de texte **Description**.

Une fois l'opération configurée, cliquez sur **Enregistrer**.


## <a name="next-steps"> </a>Étapes suivantes

Une fois les opérations ajoutées à une API, l'étape suivante est l'association de l'API à un produit et sa publication, afin que les développeurs puissent appeler ses opérations.

-	[Création et publication d’un produit][]

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

[Prise en main de Gestion des API Azure]: api-management-get-started.md
[Création d'une instance de service Gestion des API]: api-management-get-started.md#create-service-instance

[How to add operations to an API]: api-management-howto-add-operations.md
[Création et publication d’un produit]: api-management-howto-add-products.md
[Mise en cache des résultats d'opérations dans Gestion des API Azure]: api-management-howto-cache.md

<!---HONumber=62-->