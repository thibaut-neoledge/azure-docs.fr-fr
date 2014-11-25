<properties pageTitle="How to cache operation results in Azure API Management" metaKeywords="" description="Learn how to improve the latency, bandwidth consumption, and web service load for API Management service calls." metaCanonical="" services="" documentationCenter="API Management" title="How to cache operation results in Azure API Management" authors="sdanie" solutions="" manager="" editor="" />

<tags ms.service="api-management" ms.workload="mobile" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="sdanie" />

# Mise en cache des résultats d'opérations dans Gestion des API Azure

Les opérations dans Gestion des API Azure (version préliminaire) peuvent être configurées pour mettre en cache la réponse. La mise en cache de la réponse peut réduire de façon importante la latence de l'API, la consommation de bande passante et la charge du service web pour les données qui ne changent pas fréquemment.

Dans ce didacticiel, vous allez consulter les paramètres et les stratégies de mise en cache pour un des exemples d'opérations de l'API Echo, puis appeler cette opération dans le portail des développeurs pour voir la mise en cache en action.

## Dans cette rubrique

-   [Configuration d'une opération de mise en cache][Configuration d'une opération de mise en cache]
-   [Révision des stratégies de mise en cache][Révision des stratégies de mise en cache]
-   [Appel d'une opération et test de la mise en cache][Appel d'une opération et test de la mise en cache]
-   [Étapes suivantes][Étapes suivantes]

## <a name="configure-caching"> </a>Configuration d'une opération de mise en cache

Dans cette étape, vous allez consulter les paramètres de mise en cache de l'opération **GET Resource (cached)** de l'exemple d'API Echo.

> Chaque instance du service Gestion des API est pré-configurée avec une API Echo qui peut être utilisée pour faire des expériences et en savoir plus sur la gestion des API. Pour plus d'informations, consultez la page [Prise en main de Gestion des API Azure][Prise en main de Gestion des API Azure].

Pour commencer, cliquez sur **Console de gestion** dans le portail Azure pour votre service Gestion des API. Vous accéderez au portail d'administration Gestion des API.

![API Management console][API Management console]

> Si vous n'avez pas encore créé une instance de service Gestion des API, consultez la page [Création d'une instance de service Gestion des API][Création d'une instance de service Gestion des API] dans le didacticiel [Prise en main de Gestion des API Azure][Prise en main de Gestion des API Azure].

Cliquez sur **API** dans le menu **Gestion des API** à gauche, puis sur **API Echo**.

![Echo API][Echo API]

Cliquez sur l'onglet **Opérations**, puis sur l'opération **GET Resource (cached)** dans la liste **Opérations**.

![Echo API operations][Echo API operations]

Sélectionnez l'onglet **Mise en cache** pour consulter les paramètres de mise en cache de cette opération.

![Caching tab][Caching tab]

Pour activer la mise en cache pour une opération, activez la case à cocher **Activer**. Dans cet exemple, la mise en cache est activée.

Chaque réponse de l'opération est générée en fonction des valeurs des champs **Variation par paramètres de chaîne de requête** et **Variation par en-têtes**. Si vous souhaitez mettre en cache plusieurs réponses en fonction des paramètres ou des en-têtes de la chaîne de requête, vous pouvez les configurer dans ces deux champs.

**Durée** spécifie l'intervalle d'expiration des réponses mises en cache. Dans cet exemple, l'intervalle est de **3600** secondes, ce qui équivaut à une heure.

Selon la configuration de mise en cache de cet exemple, la première demande envoyée à l'opération **GET Resource (cached)** renvoie une réponse du service principal. Cette réponse sera mise en cache, du fait des en-têtes et des paramètres de chaîne de requête spécifiés. Les autres appels à l'opération comportant des paramètres correspondants recevront la réponse mise en cache, jusqu'à expiration de la durée de mise en cache.

## <a name="caching-policies"> </a>Révision des stratégies de mise en cache

Lorsque les paramètres de mise en cache sont configurés pour une opération dans l'onglet **Mise en cache**, les stratégies de mise en cache sont ajoutées pour cette opération. Ces stratégies peuvent être consultées et modifiées dans l'éditeur de stratégies.

Cliquez sur **Stratégies** dans le menu **Gestion des API** à gauche, puis sélectionnez **Echo API / GET Resource (cached)** dans la liste déroulante **Opération**.

![Policy scope operation][Policy scope operation]

Affiche les stratégies de cette opération dans l'éditeur de stratégies.

![API Management policy editor][API Management policy editor]

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

> Les modifications apportées aux stratégies de mise en cache dans l'éditeur de stratégies sont affichées dans l'onglet **Mise en cache** de l'opération, et vice-versa.

## <a name="test-operation"> </a>Appel d'une opération et test de la mise en cache

Pour voir la mise en cache en action, nous pouvons appeler l'opération depuis le portail des développeurs. Cliquez sur **Portail des développeurs** dans le menu en haut à droite

![Portail des développeurs][Portail des développeurs]

Cliquez sur **API** dans le menu supérieur et sélectionnez **API Echo**.

![Echo API][1]

> Si vous n'avez qu'une API configurée ou visible dans votre compte, cliquez sur des API pour accéder directement aux opérations associées.

Sélectionnez l'opération **Ressource GET (cached)** et cliquez sur **Ouvrir la console**.

![Open console][Open console]

La console vous permet d'appeler des opérations directement depuis le portail des développeurs.

![Console][Console]

Conservez les valeurs par défaut de **param1** et **param2**.

Sélectionnez la clé souhaitée dans la liste déroulante **subscription-key**. Si votre compte a un seul abonnement, il est déjà sélectionné.

Entrez **sampleheader:value1** dans la zone de texte des **en-têtes de la demande**.

Cliquez sur **HTTP Get** et notez les en-têtes de réponse.

Entrez **sampleheader:value2** dans la zone de texte des **en-têtes de la demande**, puis cliquez sur **HTTP Get**.

Notez que la valeur de **sampleheader** est toujours **value1** dans la réponse. Essayez d'autres valeurs. Vous constatez que la réponse mise en cache lors du premier appel est renvoyée.

Entrez **25** dans le champ **param2**, puis cliquez sur **HTTP Get**.

Notez que la valeur de **sampleheader** dans la réponse est désormais **value2**. Comme les résultats de l'opération dépendent de la chaîne de requête, la réponse précédemment mise en cache n'est pas renvoyée.

## <a name="next-steps"> </a>Étapes suivantes

-   Consultez les autres rubriques du didacticiel [Prise en main de la configuration avancée des API][Prise en main de la configuration avancée des API].
-   Pour plus d'informations sur les stratégies de mise en cache, consultez la section [Stratégies de mise en cache][Stratégies de mise en cache] dans [Référence de stratégie de Gestion des API][Référence de stratégie de Gestion des API].

  [Configuration d'une opération de mise en cache]: #configure-caching
  [Révision des stratégies de mise en cache]: #caching-policies
  [Appel d'une opération et test de la mise en cache]: #test-operation
  [Étapes suivantes]: #next-steps
  [Prise en main de Gestion des API Azure]: ../api-management-get-started
  [API Management console]: ./media/api-management-howto-cache/api-management-management-console.png
  [Création d'une instance de service Gestion des API]: ../api-management-get-started/#create-service-instance
  [Echo API]: ./media/api-management-howto-cache/api-management-echo-api.png
  [Echo API operations]: ./media/api-management-howto-cache/api-management-echo-api-operations.png
  [Caching tab]: ./media/api-management-howto-cache/api-management-caching-tab.png
  [Policy scope operation]: ./media/api-management-howto-cache/api-management-operation-dropdown.png
  [API Management policy editor]: ./media/api-management-howto-cache/api-management-policy-editor.png
  [Portail des développeurs]: ./media/api-management-howto-cache/api-management-developer-portal-menu.png
  [1]: ./media/api-management-howto-cache/api-management-apis-echo-api.png
  [Open console]: ./media/api-management-howto-cache/api-management-open-console.png
  [Console]: ./media/api-management-howto-cache/api-management-console.png
  [Prise en main de la configuration avancée des API]: ../api-management-get-started-advanced
  [Stratégies de mise en cache]: ../api-management-policy-reference/#caching-policies
  [Référence de stratégie de Gestion des API]: ../api-management-policy-reference
