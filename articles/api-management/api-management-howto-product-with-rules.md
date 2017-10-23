---
title: "Protéger votre API avec Gestion des API Azure | Microsoft Docs"
description: "Découvrez comment protéger votre API avec les quotas et les stratégies (limite de débit) de limitation."
services: api-management
documentationcenter: 
author: juliako
manager: erikre
editor: 
ms.assetid: 450dc368-d005-401d-ae64-3e1a2229b12f
ms.service: api-management
ms.workload: mobile
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 12/15/2016
ms.author: apimpm
ms.translationtype: HT
ms.sourcegitcommit: 47ba7c7004ecf68f4a112ddf391eb645851ca1fb
ms.openlocfilehash: 300b1d762a61c810dbffde5aaacd8a85f12c9fca
ms.contentlocale: fr-fr
ms.lasthandoff: 09/14/2017

---
# <a name="protect-your-api-with-rate-limits-using-azure-api-management"></a>Protéger votre API avec des limites de débit à l’aide de la gestion des API Azure
Ce guide vous montre combien il est facile d’ajouter une protection à votre API principale en configurant des limites de débit et des stratégies de quota avec la gestion des API Azure.

Dans ce didacticiel, vous allez créer une version d’évaluation gratuite d’un produit API qui permet aux développeurs de passer jusqu’à 10 appels par minute dans la limite de 200 appels par semaine vers votre API en utilisant les stratégies consistant à [Limiter la fréquence des appels par abonnement](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) et à [Définir le quota d’utilisation par abonnement](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota). Vous pourrez ensuite publier l’API et tester la stratégie de limite de débit

Pour consulter des scénarios de limitation plus avancés utilisant les stratégies [limiter-fréquence-par-clé](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRateByKey) et [quota-par-clé](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuotaByKey), consultez l’article [Limitation de requêtes avancée avec la gestion des API Azure](api-management-sample-flexible-throttling.md).

## <a name="create-product"> </a>Pour créer un produit.
Dans cette étape, vous allez créer un produit en version d’évaluation gratuite, qui ne requiert aucune approbation d’abonnement.

> [!NOTE]
> Si vous disposez déjà d’un produit configuré et que vous souhaitez l’utiliser pour ce didacticiel, vous pouvez passer directement à la rubrique [Configuration de la limite de débit d’appels et des stratégies de quota][Configure call rate limit and quota policies] et suivre le didacticiel à partir de là, en utilisant votre produit à la place de celui en version d’évaluation gratuite.
> 
> 

Pour commencer, cliquez sur **Portail des éditeurs** dans le portail Azure de votre service Gestion des API.

![Portail des éditeurs][api-management-management-console]

> Si vous n’avez pas encore créé d’instance de service Gestion des API, consultez la page [Création d'une instance du service API Management][Create an API Management service instance] dans le didacticiel [Gérer votre première API dans Gestion des API Azure][Manage your first API in Azure API Management].
> 
> 

Cliquez sur **Produits** dans le menu **Gestion des API** sur la gauche pour afficher la page **Produits**.

![Add product][api-management-add-product]

Cliquez sur **Ajouter un produit** pour afficher la boîte de dialogue **Ajouter un nouveau produit**.

![Ajouter un nouveau produit][api-management-new-product-window]

Dans la zone **Titre**, saisissez **Version d’évaluation gratuite**.

Dans la zone **Description**, saisissez le texte suivant : **Les abonnés pourront effectuer 10 appels/minute jusqu’à un maximum de 200 appels/semaine, après lesquels l’accès est refusé.**

Les produits de Gestion des API peuvent être protégés ou ouverts. Pour pouvoir utiliser les produits protégés, vous devez vous y abonner au préalable. Les produits ouverts sont utilisables sans abonnement. Vérifiez que la case à cocher **Require subscription (Abonnement obligatoire)** est activée afin de créer un produit protégé qui requiert un abonnement. Il s’agit du paramètre par défaut.

Si vous souhaitez qu’un administrateur vérifie et accepte ou refuse les tentatives d’abonnement à ce produit, sélectionnez **Require subscription approval (Approbation d'abonnement obligatoire)**. Si la case à cocher n’est pas activée, les tentatives d’abonnement sont approuvées automatiquement. Dans cet exemple, les abonnements sont approuvés automatiquement. Donc, inutile d’activer la case à cocher.

Pour autoriser les comptes de développeur à s’abonner plusieurs fois au nouveau produit, activez la case à cocher **Allow multiple simultaneous subscriptions (Autoriser plusieurs abonnements simultanés)**. Ce didacticiel ne nécessite pas l’utilisation de plusieurs abonnements simultanés. Vous pouvez donc laisser cette case décochée.

Une fois toutes les valeurs saisies, cliquez sur **Enregistrer** pour créer le produit.

![Product added][api-management-product-added]

Par défaut, les nouveaux produits sont visibles pour les utilisateurs dans le groupe **Administrateurs** . Nous allons ajouter le groupe **Développeurs** . Cliquez sur **Version d’évaluation gratuite**, puis sur l’onglet **Visibilité**.

> Dans Gestion des API, les groupes permettent de gérer la visibilité des produits pour les développeurs. Les produits accordent de la visibilité aux groupes. Les développeurs peuvent afficher les produits visibles pour les groupes auxquels ils appartiennent et s'y abonner. Pour plus d'informations, consultez la page [Création et utilisation de groupes dans Gestion des API Azure][How to create and use groups in Azure API Management].
> 
> 

![Add developers group][api-management-add-developers-group]

Activez la case à cocher **Développeurs**, puis cliquez sur **Enregistrer**.

## <a name="add-api"> </a>Ajout d’une API au produit
Dans cette étape du didacticiel, nous allons ajouter l'API Echo au nouveau produit en version d'évaluation gratuite.

> Chaque instance du service Gestion des API est pré-configurée avec une API Echo qui peut être utilisée pour faire des expériences et en savoir plus sur la gestion des API. Pour plus d’informations, consultez [Gérer votre première API dans Gestion des API Azure][Manage your first API in Azure API Management].
> 
> 

Cliquez sur **Produits** dans le menu **Gestion des API** sur la gauche, puis cliquez sur **Version d’évaluation gratuite** pour configurer le produit.

![Configure product][api-management-configure-product]

Cliquez sur **Ajouter l'API au produit**.

![Ajouter l'API au produit][api-management-add-api]

Sélectionnez **API Echo**, puis cliquez sur **Enregistrer**.

![Add Echo API][api-management-add-echo-api]

## <a name="policies"> </a>Configuration de la limite de débit d’appels et des stratégies de quota
Les limites de débit et les quotas sont configurés dans l'éditeur de stratégie. Les deux stratégies que nous ajouterons dans ce didacticiel sont les stratégies [Limiter la fréquence des appels par abonnement](https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate) et [Définir le quota d’utilisation par abonnement](https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota). Elles doivent être appliquées dans l’étendue du produit.

Cliquez sur **Stratégies** sous le menu **Gestion des API** sur la gauche. Dans la liste **Produit** cliquez sur **Version d’évaluation gratuite**.

![Product policy][api-management-product-policy]

Cliquez sur **Ajouter une stratégie** pour importer le modèle de stratégie et commencer à créer la limite de débit et les stratégies de quota.

![Add policy][api-management-add-policy]

La limite de débit et les stratégies de quota sont des stratégies entrantes. Positionnez donc le curseur sur l'élément entrant.

![Policy editor][api-management-policy-editor-inbound]

Parcourez la liste déroulante des stratégies et recherchez l’entrée de la stratégie **Limiter la fréquence des appels par abonnement**.

![Policy statements][api-management-limit-policies]

Une fois le curseur positionné dans l’élément de stratégie **inbound**, cliquez sur la flèche en regard de **Limiter la fréquence des appels par abonnement** pour insérer son modèle de stratégie.

```xml
<rate-limit calls="number" renewal-period="seconds">
<api name="name" calls="number">
<operation name="name" calls="number" />
</api>
</rate-limit>
```

Comme vous pouvez le voir à partir de l’extrait de code, la stratégie permet de définir des limites pour les opérations et les API du produit. Dans ce didacticiel, nous n’utiliserons pas cette fonctionnalité, supprimez donc les éléments **api** et **operation** de l’élément **rate-limit** pour ne laisser que l’élément externe **rate-limit**, comme le montre l’exemple suivant.

```xml
<rate-limit calls="number" renewal-period="seconds">
</rate-limit>
```

Dans le produit en version d’évaluation gratuite, le débit d’appels maximum autorisé est de 10 appels par minute. Tapez **10** en tant que valeur pour l’attribut **calls** et **60** pour l’attribut **renewal-period**.

```xml
<rate-limit calls="10" renewal-period="60">
</rate-limit>
```

Pour configurer la stratégie **Définir le quota d’utilisation par abonnement**, positionnez votre curseur immédiatement sous l’élément **rate-limit** nouvellement ajouté dans l’élément **inbound**, puis recherchez et cliquez sur la flèche à gauche de **Définir le quota d’utilisation par abonnement**.

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
<api name="name" calls="number" bandwidth="kilobytes">
<operation name="name" calls="number" bandwidth="kilobytes" />
</api>
</quota>
```

Comme la stratégie **Limiter la fréquence des appels par abonnement**, la stratégie **Définir le quota d’utilisation par abonnement** permet de définir des plafonds pour les opérations et les API sur le produit. Dans ce didacticiel, nous n’utiliserons pas cette fonctionnalité, supprimez donc les éléments **api** et **operation** de l’élément **quota**, comme le montre l’exemple suivant.

```xml
<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
</quota>
```

Les quotas peuvent être basés sur le nombre d’appels par intervalle et/ou par bande passante. Dans ce didacticiel, nous ne définirons pas de limitation de bande passante. Supprimez donc l’attribut **bandwidth**.

```xml
<quota calls="number" renewal-period="seconds">
</quota>
```

Dans le produit en version d'évaluation gratuite, le quota est de 200 appels par semaine. Indiquez **200** en tant que valeur pour l’attribut **calls**, puis indiquez **604800** en tant que valeur pour l’attribut **renewal-period**.

```xml
<quota calls="200" renewal-period="604800">
</quota>
```

> Les intervalles de stratégie sont spécifiés en secondes. Pour calculer l’intervalle pour une semaine, vous pouvez multiplier le nombre de jours (7) par le nombre d’heures dans une journée (24) par le nombre de minutes dans une heure (60) par le nombre de secondes dans une minute (60) : 7 * 24 * 60 * 60 = 604 800.
> 
> 

Lorsque vous avez terminé la configuration de la stratégie, elle doit correspondre à l'exemple ci-dessous.

```xml
<policies>
    <inbound>
        <rate-limit calls="10" renewal-period="60">
        </rate-limit>
        <quota calls="200" renewal-period="604800">
        </quota>
        <base />

</inbound>
<outbound>

    <base />

    </outbound>
</policies>
```

Une fois les stratégies de votre choix configurées, cliquez sur **Enregistrer**.

![Save policy][api-management-policy-save]

## <a name="publish-product"> </a> Publication du produit
Maintenant que les API sont ajoutées et les stratégies configurées, le produit doit être publié pour pouvoir être utilisé par des développeurs. Cliquez sur **Produits** dans le menu **Gestion des API** sur la gauche, puis cliquez sur **Version d’évaluation gratuite** pour configurer le produit.

![Configure product][api-management-configure-product]

Cliquez sur **Publier**, puis sur **Oui, publier** pour confirmer.

![Publish product][api-management-publish-product]

## <a name="subscribe-account"> </a>Abonnement d’un compte de développeur au produit
Maintenant que le produit est publié, il est disponible pour abonnement et utilisation par les développeurs.

> Les administrateurs d'une instance Gestion des API sont automatiquement abonnés à chaque produit. Dans cette étape du didacticiel, nous abonnerons l’un des comptes de développeur non administrateurs au produit en version d’évaluation gratuite. Si votre compte de développeur fait partie du rôle Administrateur, vous pouvez suivre cette étape, même si vous êtes déjà abonné.
> 
> 

Cliquez sur **Utilisateurs** sur le menu **Gestion des API** situé à gauche, puis cliquez sur le nom de votre compte de développeur. Dans cet exemple, nous utilisons le compte de développeur **Clayton Gragg** .

![Configure developer][api-management-configure-developer]

Cliquez sur **Ajouter un abonnement**.

![Ajouter un abonnement][api-management-add-subscription-menu]

Sélectionnez **Version d’évaluation gratuite**, puis cliquez sur **S’abonner**.

![Ajouter un abonnement][api-management-add-subscription]

> [!NOTE]
> Dans ce didacticiel, l’option autorisant plusieurs abonnements simultanés est désactivée pour le produit en version d’évaluation gratuite. Dans le cas contraire, vous auriez été invité à indiquer le nom de l’abonnement, comme dans l’exemple suivant.
> 
> 

![Ajouter un abonnement][api-management-add-subscription-multiple]

Après avoir cliqué sur **S’abonner**, le produit s’affiche dans la liste **Abonnement** de l’utilisateur.

![Abonnement ajouté][api-management-subscription-added]

## <a name="test-rate-limit"> </a>Appel d’une opération et test de la limite de débit
Maintenant que le produit en version d'évaluation gratuite est configuré et publié, nous pouvons appeler des opérations et tester la stratégie de limite de débit.
Basculez vers le portail de développeur en cliquant sur **Portail des développeurs** dans le menu supérieur droit.

![Portail des développeurs][api-management-developer-portal-menu]

Cliquez sur **API** dans le menu supérieur, puis sélectionnez **API Echo**.

![Portail des développeurs][api-management-developer-portal-api-menu]

Cliquez sur **Ressource GET**, puis sur **Essayez-le**.

![Open console][api-management-open-console]

Conservez la valeur par défaut des paramètres et sélectionnez votre clé d’abonnement pour le produit en version d’évaluation gratuite.

![Clé d’abonnement][api-management-select-key]

> [!NOTE]
> Si vous possédez plusieurs abonnements, pensez à sélectionner la clé pour la **Version d’évaluation gratuite**, sinon, les stratégies configurées aux étapes précédentes ne seront pas effectives.
> 
> 

Cliquez sur **Envoyer**, puis affichez la réponse. Notez l'**État de réponse** **200 OK**.

![Operation results][api-management-http-get-results]

Cliquez sur **Envoyer** à une fréquence supérieure à la stratégie de limite de fréquence de 10 appels par minute. Une fois la stratégie de limite de débit dépassée, un état de réponse **429 Trop de requêtes** est renvoyé.

![Operation results][api-management-http-get-429]

Le **Contenu de réponse** indique l’intervalle restant avant la réussite des nouvelles tentatives.

Lorsque la stratégie de limite de débit de 10 appels par minute est appliquée, les appels suivants échouent jusqu’à ce que 60 secondes se soient écoulées à partir du premier des 10 appels réussis vers le produit avant le dépassement de la limite de débit. Dans cet exemple, l’intervalle restant est de 54 secondes.

## <a name="next-steps"> </a>Étapes suivantes
* Consultez une démonstration relative à la définition de limites de débit et de quotas dans la vidéo suivante.

> [!VIDEO https://channel9.msdn.com/Blogs/AzureApiMgmt/Rate-Limits-and-Quotas/player]
> 
> 

[api-management-management-console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
[api-management-add-product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
[api-management-new-product-window]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
[api-management-product-added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
[api-management-add-policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
[api-management-policy-editor-inbound]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
[api-management-limit-policies]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
[api-management-policy-save]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
[api-management-configure-product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
[api-management-add-api]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
[api-management-add-echo-api]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
[api-management-developer-portal-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
[api-management-publish-product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
[api-management-configure-developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
[api-management-add-subscription-menu]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
[api-management-add-subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
[api-management-developer-portal-api-menu]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
[api-management-open-console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
[api-management-http-get]: ./media/api-management-howto-product-with-rules/api-management-http-get.png
[api-management-http-get-results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
[api-management-http-get-429]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
[api-management-product-policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
[api-management-add-developers-group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
[api-management-select-key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
[api-management-subscription-added]: ./media/api-management-howto-product-with-rules/api-management-subscription-added.png
[api-management-add-subscription-multiple]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-multiple.png

[How to add operations to an API]: api-management-howto-add-operations.md
[How to add and publish a product]: api-management-howto-add-products.md
[Monitoring and analytics]: ../api-management-monitoring.md
[Add APIs to a product]: api-management-howto-add-products.md#add-apis
[Publish a product]: api-management-howto-add-products.md#publish-product
[Manage your first API in Azure API Management]: api-management-get-started.md
[How to create and use groups in Azure API Management]: api-management-howto-create-groups.md
[View subscribers to a product]: api-management-howto-add-products.md#view-subscribers
[Get started with Azure API Management]: api-management-get-started.md
[Create an API Management service instance]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps

[Create a product]: #create-product
[Configure call rate limit and quota policies]: #policies
[Add an API to the product]: #add-api
[Publish the product]: #publish-product
[Subscribe a developer account to the product]: #subscribe-account
[Call an operation and test the rate limit]: #test-rate-limit

[Limit call rate]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Set usage quota]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota

