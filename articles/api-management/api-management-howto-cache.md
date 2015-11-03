<properties
	pageTitle="Ajout de la mise en cache pour améliorer les performances de Gestion des API Azure | Microsoft Azure"
	description="Apprenez à améliorer la latence, la consommation de bande passante et la charge du service web pour les appels du service Gestion des API."
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
	ms.topic="get-started-article"
	ms.date="10/27/2015"
	ms.author="sdanie"/>

# Ajout de mise en cache pour améliorer les performances dans Gestion des API Azure

Les opérations dans Gestion des API Azure peuvent être configurées pour mettre en cache la réponse. La mise en cache de la réponse peut réduire de façon importante la latence de l'API, la consommation de bande passante et la charge du service web pour les données qui ne changent pas fréquemment.

Ce guide vous montre comment ajouter une mise en cache de la réponse pour votre API et configurer des stratégies pour les exemples d’opérations de l’API Echo. Vous pouvez ensuite appeler l’opération depuis le portail des développeurs pour vérifier l’action de mise en cache.

## Composants requis

Avant de suivre la procédure décrite dans ce guide, vous devez disposer d’une instance de service de Gestion des API avec une API et un produit configurés. Si vous n'avez pas encore créé une instance de service Gestion des API, consultez la page [Création d'une instance de service Gestion des API][] dans le didacticiel [Prise en main de Gestion des API Azure][].

## <a name="configure-caching"> </a>Configuration d’une opération de mise en cache

Dans cette étape, vous allez consulter les paramètres de mise en cache de l’opération **GET Resource (cached)** de l’exemple d’API Echo.

>[AZURE.NOTE]Chaque instance du service Gestion des API est préconfigurée avec une API Echo qui peut être utilisée pour faire des expériences et en savoir plus sur la gestion des API. Pour plus d'informations, consultez la page [Prise en main de Gestion des API Azure][].

Pour commencer, cliquez sur **Gérer** dans le portail Azure de votre service Gestion des API. Vous accédez au portail des éditeurs Gestion des API.

![Portail des éditeurs][api-management-management-console]

Cliquez sur **API** dans le menu **Gestion des API** à gauche, puis sur **API Echo**.

![API Echo][api-management-echo-api]

Cliquez sur l’onglet **Opérations**, puis sur l’opération **GET Resource (cached)** dans la liste **Opérations**.

![Echo API operations][api-management-echo-api-operations]

Cliquez sur l’onglet **Mise en cache** pour consulter les paramètres de mise en cache de cette opération.

![Caching tab][api-management-caching-tab]

Pour activer la mise en cache pour une opération, activez la case à cocher **Activer**. Dans cet exemple, la mise en cache est activée.

Chaque réponse de l’opération est générée en fonction des valeurs des champs **Variation par paramètres de chaîne de requête** et **Variation par en-têtes**. Si vous souhaitez mettre en cache plusieurs réponses en fonction des paramètres ou des en-têtes de la chaîne de requête, vous pouvez les configurer dans ces deux champs.

**Durée** spécifie l'intervalle d'expiration des réponses mises en cache. Dans cet exemple, l’intervalle est de **3600** secondes, ce qui équivaut à une heure.

Selon la configuration de mise en cache de cet exemple, la première demande envoyée à l’opération **GET Resource (cached)** renvoie une réponse du service principal. Cette réponse sera mise en cache, du fait des en-têtes et des paramètres de chaîne de requête spécifiés. Les autres appels à l'opération comportant des paramètres correspondants recevront la réponse mise en cache, jusqu'à expiration de la durée de mise en cache.

## <a name="caching-policies"> </a>Révision des stratégies de mise en cache

Dans cette étape, vous allez consulter les paramètres de mise en cache de l’opération **GET Resource (cached)** de l’exemple d’API Echo.

Lorsque les paramètres de mise en cache sont configurés pour une opération dans l'onglet **Mise en cache**, les stratégies de mise en cache sont ajoutées pour cette opération. Ces stratégies peuvent être consultées et modifiées dans l'éditeur de stratégies.

Cliquez sur **Stratégies** dans le menu **Gestion des API** à gauche, puis sélectionnez **Echo API / GET Resource (cached)** dans la liste déroulante **Opération**.

![Policy scope operation][api-management-operation-dropdown]

Affiche les stratégies de cette opération dans l'éditeur de stratégies.

![API Management policy editor][api-management-policy-editor]

La définition de stratégie de cette opération comprend les stratégies qui définissent la configuration de la mise en cache que nous avons vues dans l'onglet **Mise en cache** lors de l'étape précédente.

	<policies>
		<inbound>
			<base />
			<cache-lookup vary-by-developer="false" vary-by-developer-groups="false">
				<vary-by-header>Accept</vary-by-header>
				<vary-by-header>Accept-Charset</vary-by-header>
			</cache-lookup>
			<rewrite-uri template="/resource" />
		</inbound>
		<outbound>
			<base />
			<cache-store caching-mode="cache-on" duration="3600" />
		</outbound>
	</policies>

>[AZURE.NOTE]Les modifications apportées aux stratégies de mise en cache dans l’éditeur de stratégies sont affichées sous l’onglet **Mise en cache** d’une opération, et vice-versa.

## <a name="test-operation"> </a>Appel d’une opération et test de la mise en cache

Pour voir la mise en cache en action, nous pouvons appeler l'opération depuis le portail des développeurs. Cliquez sur **Portail de développement** dans le menu supérieur droit.

![Portail des développeurs][api-management-developer-portal-menu]

Cliquez sur **API** dans le menu supérieur, puis sélectionnez **API Echo**.

![API Echo][api-management-apis-echo-api]

>Si vous n'avez qu'une API configurée ou visible dans votre compte, cliquez sur des API pour accéder directement aux opérations associées.

Sélectionnez l’opération **Ressource GET (cached)**, puis cliquez sur **Ouvrir la console**.

![Open console][api-management-open-console]

La console vous permet d'appeler des opérations directement depuis le portail des développeurs.

![Console][api-management-console]

Conservez les valeurs par défaut de **param1** et **param2**.

Sélectionnez la clé souhaitée dans la liste déroulante **subscription-key**. Si votre compte a un seul abonnement, celui-ci est déjà sélectionné.

Entrez **sampleheader:value1** dans la zone de texte des **en-têtes de la demande**.

Cliquez sur **HTTP Get** et notez les en-têtes de réponse.

Entrez **sampleheader:value2** dans la zone de texte des **en-têtes de la demande**, puis cliquez sur **HTTP Get**.

Notez que la valeur de **sampleheader** est toujours **value1** dans la réponse. Essayez d'autres valeurs. Vous constatez que la réponse mise en cache lors du premier appel est renvoyée.

Entrez **25** dans le champ **param2**, puis cliquez sur **HTTP Get**.

Notez que la valeur de **sampleheader** dans la réponse est désormais **value2**. Comme les résultats de l'opération dépendent de la chaîne de requête, la réponse précédemment mise en cache n'est pas renvoyée.

## <a name="next-steps"> </a>Étapes suivantes

-	Consultez les autres rubriques du didacticiel [Prise en main de la configuration avancée des API][].
-	Pour plus d’informations sur les stratégies de mise en cache, voir la section [Stratégies de mise en cache][] dans [Référence de stratégie de Gestion des API][].

[api-management-management-console]: ./media/api-management-howto-cache/api-management-management-console.png
[api-management-echo-api]: ./media/api-management-howto-cache/api-management-echo-api.png
[api-management-echo-api-operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
[api-management-caching-tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
[api-management-operation-dropdown]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
[api-management-policy-editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
[api-management-developer-portal-menu]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
[api-management-apis-echo-api]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
[api-management-open-console]: ./media/api-management-howto-cache/api-management-open-console.png
[api-management-console]: ./media/api-management-howto-cache/api-management-console.png


[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Prise en main de Gestion des API Azure]: api-management-get-started.md
[Prise en main de la configuration avancée des API]: api-management-get-started-advanced.md

[Référence de stratégie de Gestion des API]: https://msdn.microsoft.com/library/azure/dn894081.aspx
[Stratégies de mise en cache]: https://msdn.microsoft.com/library/azure/dn894086.aspx

[Création d'une instance de service Gestion des API]: api-management-get-started.md#create-service-instance

[Configure an operation for caching]: #configure-caching
[Review the caching policies]: #caching-policies
[Call an operation and test the caching]: #test-operation
[Next steps]: #next-steps

<!---HONumber=Nov15_HO1-->