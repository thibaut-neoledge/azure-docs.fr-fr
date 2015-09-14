<properties
	pageTitle="Protéger votre API avec Gestion des API Azure | Microsoft Azure"
	description="Découvrez comment protéger votre API avec les quotas et les stratégies (limite de débit) de limitation."
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
	ms.date="08/05/2015"
	ms.author="sdanie"/>

# Protéger votre API avec des limites de débit à l’aide de la gestion des API Azure

Ce guide vous montre combien il est facile d’ajouter une protection à votre API principale en configurant des limites de débit et des stratégies de quota avec la gestion des API Azure.

Ce didacticiel vous permet de créer une version d’évaluation gratuite d’un produit API qui permet aux développeurs de passer jusqu’à 10 appels par minute dans la limite de 200 appels par semaine vers votre API. Vous pourrez ensuite publier l’API et tester la stratégie de limite de débit

>[AZURE.NOTE]Si vous disposez déjà d’un produit configuré et que vous souhaitez l’utiliser pour ce didacticiel, vous pouvez passer directement à la rubrique [Configuration de la limite de débit d’appels et des stratégies de quota][] et suivre le didacticiel à partir de là, en utilisant votre produit à la place de celui en version d’évaluation gratuite.

## <a name="create-product"> </a>Pour créer un produit.

Au cours de cette étape, vous allez créer un produit en version d'évaluation gratuite qui ne requiert pas d'approbation d'abonnement.

Pour commencer, cliquez sur **Gérer** dans le portail Azure de votre service Gestion des API. Vous accédez au portail des éditeurs Gestion des API.

![Portail des éditeurs][api-management-management-console]

>Si vous n'avez pas encore créé une instance de service Gestion des API, consultez la page [Création d'une instance de service Gestion des API][] dans le didacticiel [Prise en main de Gestion des API Azure][].

Cliquez sur **Produits** dans le menu **Gestion des API** sur la gauche pour afficher la page **Produits**.

![Add product][api-management-add-product]

Cliquez sur **Ajouter un produit** pour afficher la boîte de dialogue **Ajouter un nouveau produit**.

![Add new product][api-management-new-product-window]

Dans la zone **Titre**, saisissez **Version d’évaluation gratuite**.

Dans la zone **Description**, saisissez le texte suivant : **Les abonnés pourront effectuer 10 appels/minute jusqu’à un maximum de 200 appels/semaine, après lesquels l’accès est refusé.**

Les produits de Gestion des API peuvent être protégés ou ouverts. Pour pouvoir utiliser les produits protégés, vous devez vous y abonner au préalable. Les produits ouverts peuvent être utilisés sans abonnement. Vérifiez que la case à cocher **Demander une approbation d’abonnement** est activée afin de créer un produit protégé qui requiert un abonnement. Il s’agit du paramètre par défaut.

Si vous souhaitez qu’un administrateur révise et accepte ou refuse les tentatives d’abonnement à ce produit, sélectionnez **Demander une approbation d’abonnement**. Si la case à cocher n’est pas activée, les tentatives d’abonnement seront automatiquement approuvées. Dans cet exemple, les abonnements sont automatiquement approuvés. N’activez donc pas la case à cocher.

Pour autoriser les comptes de développeur à s’abonner plusieurs fois au nouveau produit, activez la case à cocher **Autoriser plusieurs abonnements simultanés**. Ce didacticiel ne nécessite pas l’utilisation de plusieurs abonnements simultanés, par conséquent, vous pouvez laisser cette case décochée.

Une fois toutes les valeurs saisies, cliquez sur **Enregistrer** pour créer le produit.

![Product added][api-management-product-added]

Par défaut, les nouveaux produits sont visibles pour les utilisateurs dans le groupe **Administrateurs**. Nous allons ajouter le groupe **Développeurs**. Cliquez sur **Version d’évaluation gratuite**, puis sur l’onglet **Visibilité**.

>Dans Gestion des API, les groupes permettent de gérer la visibilité des produits pour les développeurs. Les produits accordent de la visibilité aux groupes. Les développeurs peuvent afficher les produits visibles pour les groupes auxquels ils appartiennent et s'y abonner. Pour plus d'informations, consultez la page [Création et utilisation de groupes dans Gestion des API Azure][].

![Add developers group][api-management-add-developers-group]

Activez la case à cocher **Développeurs**, puis cliquez sur **Enregistrer**.

## <a name="add-api"> </a> Ajout d’une API au produit

Dans cette étape du didacticiel, nous allons ajouter l'API Echo au nouveau produit en version d'évaluation gratuite.

>Chaque instance du service Gestion des API est pré-configurée avec une API Echo qui peut être utilisée pour faire des expériences et en savoir plus sur la gestion des API. Pour plus d'informations, consultez la page [Prise en main de Gestion des API Azure][].

Cliquez sur **Produits** dans le menu **Gestion des API** sur la gauche, puis cliquez sur **Version d’évaluation gratuite** pour configurer le produit.

![Configure product][api-management-configure-product]

Cliquez sur **Ajouter l'API au produit**.

![Add API to product][api-management-add-api]

Sélectionnez **API Echo**, puis cliquez sur **Enregistrer**.

![Add Echo API][api-management-add-echo-api]

## <a name="policies"> </a> Configuration de la limite de débit d’appels et des stratégies de quota

Les limites de débit et les quotas sont configurés dans l'éditeur de stratégie. Cliquez sur **Stratégies** sous le menu **Gestion des API** sur la gauche. Dans la liste **Produit** cliquez sur **Version d’évaluation gratuite**.

![Product policy][api-management-product-policy]

Cliquez sur **Ajouter une stratégie** pour importer le modèle de stratégie et commencer à créer la limite de débit et les stratégies de quota.

![Add policy][api-management-add-policy]

Pour insérer des stratégies, positionnez le curseur sur la section **entrante** ou **sortante** du modèle de stratégie. La limite de débit et les stratégies de quota sont des stratégies entrantes. Positionnez donc le curseur sur l'élément entrant.

![Policy editor][api-management-policy-editor-inbound]

Les deux stratégies que nous ajoutons dans ce didacticiel sont les stratégies [Débit limite d'appel][] et [Définir le quota d'utilisation][].

![Policy statements][api-management-limit-policies]

Une fois le curseur positionné dans l’élément de stratégie **inbound**, cliquez sur la flèche en regard de **Débit de limite d’appel** pour insérer son modèle de stratégie.

	<rate-limit calls="number" renewal-period="seconds">
	<api name="name" calls="number">
	<operation name="name" calls="number" />
	</api>
	</rate-limit>

**Débit de limite d’appel** peut être utilisé au niveau du produit et également aux niveaux de l’API et du nom de fonctionnement individuel. Dans ce didacticiel, seules les stratégies de niveau de produit sont utilisées. Supprimez donc les éléments **api** et **operation** de l’élément **rate-limit** pour ne laisser que l’élément externe **rate-limit**, comme le montre l’exemple suivant.

	<rate-limit calls="number" renewal-period="seconds">
	</rate-limit>

Dans le produit en version d’évaluation gratuite, le débit d’appels maximum autorisé est de 10 appels par minute. Tapez **10** en tant que valeur pour l’attribut **calls** et **60** pour l’attribut **renewal-period**.

	<rate-limit calls="10" renewal-period="60">
	</rate-limit>

Pour configurer la stratégie **Définir le quota d’utilisation**, positionnez votre curseur immédiatement sous l’élément **rate-limit** nouvellement ajouté dans l’élément **inbound**, puis cliquez sur la flèche à gauche de **Définir le quota d’utilisation**.

	<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
	<api name="name" calls="number" bandwidth="kilobytes">
	<operation name="name" calls="number" bandwidth="kilobytes" />
	</api>
	</quota>

Cette stratégie ayant également pour but d'être au niveau du produit, supprimez les éléments de nom **api** et **operation**, comme le montre l'exemple ci-dessous.

	<quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
	</quota>

Les quotas peuvent être basés sur le nombre d’appels par intervalle et/ou par bande passante. Dans ce didacticiel, nous ne définirons pas de limitation de bande passante. Supprimez donc l’attribut **bandwidth**.

	<quota calls="number" renewal-period="seconds">
	</quota>

Dans le produit en version d'évaluation gratuite, le quota est de 200 appels par semaine. Indiquez **200** en tant que valeur pour l’attribut **calls**, puis indiquez **604800** en tant que valeur pour l’attribut **renewal-period**.

	<quota calls="200" renewal-period="604800">
	</quota>

>Les intervalles de stratégie sont spécifiés en secondes. Pour calculer l’intervalle pour une semaine, vous pouvez multiplier le nombre de jours (7) par le nombre d’heures en une journée (24) par le nombre de minutes en une heure (60) par le nombre de secondes en une minute (60) : 7 * 24 * 60 * 60 = 604800.

Lorsque vous avez terminé la configuration de la stratégie, elle doit correspondre à l'exemple ci-dessous.

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

Une fois les stratégies de votre choix configurées, cliquez sur **Enregistrer**.

![Save policy][api-management-policy-save]

## <a name="publish-product"> </a> Publication du produit

Maintenant que les API sont ajoutées et les stratégies configurées, le produit doit être publié pour pouvoir être utilisé par des développeurs. Cliquez sur **Produits** dans le menu **Gestion des API** sur la gauche, puis cliquez sur **Version d’évaluation gratuite** pour configurer le produit.

![Configure product][api-management-configure-product]

Cliquez sur **Publier**, puis sur **Oui, publier** pour confirmer.

![Publish product][api-management-publish-product]

## <a name="subscribe-account"> </a>Abonnement d’un compte de développeur au produit

Maintenant que le produit est publié, il est disponible pour abonnement et utilisation par les développeurs.

>Les administrateurs d'une instance Gestion des API sont automatiquement abonnés à chaque produit. Dans cette étape du didacticiel, nous abonnerons l’un des comptes de développeur non administrateurs au produit en version d’évaluation gratuite. Si votre compte de développeur fait partie du rôle Administrateur, vous pouvez suivre cette étape, même si vous êtes déjà abonné.

Cliquez sur **Utilisateurs** sur le menu **Gestion des API** situé à gauche, puis cliquez sur le nom de votre compte de développeur. Dans cet exemple, nous utilisons le compte de développeur **Clayton Gragg**.

![Configure developer][api-management-configure-developer]

Cliquez sur **Ajouter un abonnement**.

![Add subscription][api-management-add-subscription-menu]

Sélectionnez **Version d’évaluation gratuite**, puis cliquez sur **S’abonner**.

![Add subscription][api-management-add-subscription]

>[AZURE.NOTE]Dans ce didacticiel, l’option permettant plusieurs abonnements simultanés est désactivée pour le produit en version d’évaluation gratuite. Dans le cas contraire, vous auriez été invité à nommer l’abonnement, comme indiqué dans l’exemple suivant.

![Add subscription][api-management-add-subscription-multiple]

Après avoir cliqué sur **S’abonner**, le produit s’affiche dans la liste **Abonnement** de l’utilisateur.

![Subscription added][api-management-subscription-added]

## <a name="test-rate-limit"> </a>Appel d’une opération et test de la limite de débit

Maintenant que le produit en version d'évaluation gratuite est configuré et publié, nous pouvons appeler des opérations et tester la stratégie de limite de débit. Basculez vers le portail de développeur en cliquant sur **Portail des développeurs** dans le menu supérieur droit.

![Portail des développeurs][api-management-developer-portal-menu]

Cliquez sur **API** dans le menu supérieur, puis sélectionnez **API Echo**.

![Portail des développeurs][api-management-developer-portal-api-menu]

Cliquez sur **Ressource GET**, puis sur **Ouvrir la console**.

![Open console][api-management-open-console]

Conservez les valeurs de paramètres par défaut et sélectionnez votre clé d’abonnement pour le produit en version d’évaluation gratuite.

![Subscription key][api-management-select-key]

>[AZURE.NOTE]Si vous possédez plusieurs abonnements, pensez à sélectionner la clé pour la **Version d’évaluation gratuite**, sinon, les stratégies configurées aux étapes précédentes ne seront pas effectives.

Cliquez sur **HTTP Get**, puis affichez la réponse. Notez l'**État de réponse** **200 OK**.

![Operation results][api-management-http-get-results]

Cliquez sur **HTTP Get** à un débit supérieur à la stratégie de limite de débit de 10 appels par minute. Une fois la stratégie de limite de débit dépassée, un état de réponse **429 Trop de requêtes** est renvoyé.

![Operation results][api-management-http-get-429]

Les zones **En-têtes de réponse** et le **Contenu de réponse** indiquent l’intervalle restant avant la réussite des nouvelles tentatives.

Lorsque la stratégie de limite de débit de 10 appels par minute est appliquée, les appels suivants échouent jusqu’à ce que 60 secondes se soient écoulées à partir du premier des 10 appels réussis vers le produit avant le dépassement de la limite de débit. Dans cet exemple, l’intervalle restant est de 43 secondes.

## <a name="next-steps"> </a>Étapes suivantes

-	Consultez les autres rubriques du didacticiel [Prise en main de la configuration avancée des API][].
-	Consultez une démonstration relative à la définition de limites de débit et de quotas dans la vidéo suivante.

> [AZURE.VIDEO rate-limits-and-quotas]


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
[Get started with Azure API Management]: api-management-get-started.md
[Création et utilisation de groupes dans Gestion des API Azure]: api-management-howto-create-groups.md
[View subscribers to a product]: api-management-howto-add-products.md#view-subscribers
[Prise en main de Gestion des API Azure]: api-management-get-started.md
[Création d'une instance de service Gestion des API]: api-management-get-started.md#create-service-instance
[Next steps]: #next-steps

[Create a product]: #create-product
[Configuration de la limite de débit d’appels et des stratégies de quota]: #policies
[Add an API to the product]: #add-api
[Publish the product]: #publish-product
[Subscribe a developer account to the product]: #subscribe-account
[Call an operation and test the rate limit]: #test-rate-limit
[Prise en main de la configuration avancée des API]: api-management-get-started-advanced.md

[Débit limite d'appel]: https://msdn.microsoft.com/library/azure/dn894078.aspx#LimitCallRate
[Définir le quota d'utilisation]: https://msdn.microsoft.com/library/azure/dn894078.aspx#SetUsageQuota

<!---HONumber=September15_HO1-->