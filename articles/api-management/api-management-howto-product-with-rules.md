<properties 
	pageTitle="Création et configuration de paramètres de produit avancés dans Gestion des API Azure" 
	description="Apprenez à configurer un produit avec des stratégies de quota et de débit maximal." 
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
	ms.date="02/11/2015" 
	ms.author="sdanie"/>

# Création et configuration de paramètres de produit avancés dans Gestion des API Azure

Dans Gestion des API Azure (version préliminaire), les produits sont hautement configurables pour répondre aux exigences des éditeurs d'API. Cette rubrique explique comment configurer certains des paramètres produit les plus avancés, dont la limite de débit et les stratégies de quota.

Ce didacticiel vous permet de créer un produit en version d'évaluation gratuite (qui autorise jusqu'à 10 appels par minute avec un maximum de 200 appels par semaine), de le publier et de tester la stratégie de limite de débit.

## Dans cette rubrique

-   [Création d'un produit][Création d'un produit]
-   [Ajout d'une API au produit][Ajout d'une API au produit]
-   [Configuration de la limite de débit d'appels et des stratégies de quota][Configuration de la limite de débit d'appels et des stratégies de quota]
-   [Publication du produit][Publication du produit]
-   [Abonnement d'un compte de développeur au produit][Abonnement d'un compte de développeur au produit]
-   [Appel d'une opération et test de la limite de débit][Appel d'une opération et test de la limite de débit]
-   [Étapes suivantes][Étapes suivantes]

## <a name="create-product"> </a>Création d'un produit

Au cours de cette étape, vous allez créer un produit en version d'évaluation gratuite qui ne requiert pas d'approbation d'abonnement.

Pour commencer, cliquez sur **Console de gestion** dans le portail Azure pour votre service Gestion des API. Vous accéderez au portail d'administration Gestion des API.

> Si vous n'avez pas encore créé une instance de service Gestion des API, consultez la page [Création d'une instance de service Gestion des API][Création d'une instance de service Gestion des API] dans le didacticiel [Prise en main de Gestion des API Azure][Prise en main de Gestion des API Azure].

![API Management console][API Management console]

Cliquez sur **Produits** dans le menu **Gestion des API** sur la gauche pour afficher la page **Produits**.

![Add product][Add product]

Cliquez sur **Ajouter un produit** pour afficher la fenêtre contextuelle **Ajouter un nouveau produit**.

![Add new product][Add new product]

Tapez **Version d'évaluation gratuite** dans la zone de texte **Titre**.

Tapez **Les abonnés pourront effectuer 10 appels/minute jusqu'à un maximum de 200 appels/semaine, après lesquels l'accès est refusé.** dans la zone de texte **Description**.

Si vous souhaitez qu'un administrateur révise et accepte ou refuse les tentatives d'abonnement à ce produit, cochez **Demander une approbation d'abonnement**. Si la case n'est pas cochée, les tentatives d'abonnement seront automatiquement approuvées. Dans cet exemple, les abonnements sont automatiquement approuvés. Ne cochez donc pas la case.

Une fois toutes les valeurs saisies, cliquez sur **Enregistrer** pour créer le produit.

![Product added][Product added]

Par défaut, les nouveaux produits sont visibles pour les utilisateurs dans le groupe **Administrateurs**. Nous allons ajouter le groupe **Développeurs**. Cliquez sur **Version d'évaluation gratuite**, puis sélectionnez l'onglet **Visibilité**.

> Dans Gestion des API, les groupes permettent de gérer la visibilité des produits pour les développeurs. Les produits accordent de la visibilité aux groupes. Les développeurs peuvent afficher les produits visibles pour les groupes auxquels ils appartiennent et s'y abonner. Pour plus d'informations, consultez la page [Création et utilisation de groupes dans Gestion des API Azure][Création et utilisation de groupes dans Gestion des API Azure].

![Add developers group][Add developers group]

Cochez le groupe **Développeurs** et cliquez sur **Enregistrer**.

## <a name="add-api"> </a>Ajout d'une API au produit

Dans cette étape du didacticiel, nous allons ajouter l'API Echo au nouveau produit en version d'évaluation gratuite.

> Chaque instance du service Gestion des API est pré-configurée avec une API Echo qui peut être utilisée pour faire des expériences et en savoir plus sur la gestion des API. Pour plus d'informations, consultez la page [Prise en main de Gestion des API Azure][Prise en main de Gestion des API Azure].

Cliquez sur **Produits** dans le menu **Gestion des API** sur la gauche et cliquez sur **Version d'évaluation gratuite** pour configurer le produit.

![Configure product][Configure product]

Cliquez sur **Ajouter l'API au produit**.

![Add API to product][Add API to product]

Cochez la case située en regard de **API Echo** et cliquez sur **Enregistrer**.

![Add Echo API][Add Echo API]

## <a name="policies"> </a>Configuration de la limite de débit d'appels et des stratégies de quota

Les limites de débit et les quotas sont configurés dans l'éditeur de stratégie. Cliquez sur **Stratégies** dans le menu **Gestion des API** sur la gauche et sélectionnez **Version d'évaluation gratuite** dans la liste déroulante **Produit de portée de stratégie**.

![Product policy][Product policy]

Cliquez sur **Ajouter une stratégie** pour importer le modèle de stratégie et commencer à créer la limite de débit et la stratégie de quota.

![Add policy][Add policy]

Pour insérer des stratégies, positionnez le curseur sur la section **entrante** ou **sortante** du modèle de stratégie. La limite de débit et les stratégies de quota sont des stratégies entrantes. Positionnez donc le curseur sur l'élément entrant.

![Policy editor][Policy editor]

Les deux stratégies que nous ajoutons dans ce didacticiel sont les stratégies **Débit limite d'appel** et **Définir le quota d'utilisation**.

![Policy statements][Policy statements]

Une fois le curseur positionné dans l'élément de stratégie **entrante**, cliquez sur la flèche en regard de **Débit de limite d'appel** pour insérer son modèle de stratégie.

    <rate-limit calls="number" renewal-period="seconds">
    <api name="name" calls="number">
    <operation name="name" calls="number" />
    </api>
    </rate-limit>

**Débit de limite d'appel** peut être utilisé au niveau du produit et également aux niveaux de l'API et du nom de fonctionnement individuel. Dans ce didacticiel, seules les stratégies de niveau de produit sont utilisées. Supprimez donc les éléments **api** et **operation** de l'élément **rate-limit** comme le montre l'exemple ci-dessous.

    <rate-limit calls="number" renewal-period="seconds">
    </rate-limit>

Dans le produit en **version d'évaluation gratuite**, le débit d'appels maximum autorisé est de 10 appels par minute. Tapez **10** en tant que valeur pour l'attribut d'appels et **60** pour l'attribut **renewal-period**.

    <rate-limit calls="10" renewal-period="60">
    </rate-limit>

Pour configurer la stratégie **Définir le quota d'utilisation**, positionnez votre curseur immédiatement sous l'élément **rate-limit** nouvellement ajouté dans l'élément **entrant** et cliquez sur la flèche à gauche de **Définir le quota d'utilisation**.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    <api name="name" calls="number" bandwidth="kilobytes">
    <operation name="name" calls="number" bandwidth="kilobytes" />
    </api>
    </quota>

Cette stratégie ayant également pour but d'être au niveau du produit, supprimez les éléments de nom **api** et **operation**, comme le montre l'exemple ci-dessous.

    <quota calls="number" bandwidth="kilobytes" renewal-period="seconds">
    </quota>

Les quotas peuvent être basés sur le nombre d'appels par intervalle, par bande passante ou les deux. Dans ce didacticiel, nous ne définirons pas de limitation de bande passante. Supprimez donc l'attribut **bandwidth**.

    <quota calls="number" renewal-period="seconds">
    </quota>

Dans le produit en **version d'évaluation gratuite**, le quota est de 200 appels par semaine. Indiquez **200** en tant que valeur pour l'attribut d'appels et indiquez **604800** en tant que valeur pour la période de renouvellement.

    <quota calls="200" bandwidth="kilobytes" renewal-period="604800">
    </quota>

> Les intervalles de stratégie sont spécifiés en secondes. Pour calculer l'intervalle pour une semaine, vous pouvez multiplier le nombre de jours (7) par le nombre d'heures en une journée (24) par le nombre de minutes en une heure (60) par le nombre de secondes en une minute (60). 7 * 24 * 60 * 60 = 604800.

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

![Save policy][Save policy]

## <a name="publish-product"> </a> Publication du produit

Maintenant que les API sont ajoutées et les stratégies configurées, le produit est prêt à être utilisé par des développeurs. Le produit doit être publié pour pouvoir être utilisé par des développeurs. Cliquez sur **Produits** dans le menu **Gestion des API** sur la gauche et cliquez sur **Version d'évaluation gratuite** pour configurer le produit.

![Configure product][Configure product]

Cliquez sur **Publier**, puis sur **Oui, publier** pour confirmer.

![Publish product][Publish product]

## <a name="subscribe-account"> </a>Abonnement d'un compte de développeur au produit

Maintenant que le produit est publié, il est disponible pour abonnement et utilisation par les développeurs.

> Les administrateurs d'une instance Gestion des API sont automatiquement abonnés à chaque produit. Dans cette étape du didacticiel, nous abonnerons l'un des comptes de développeur non-administrateurs au produit en version d'évaluation gratuite. Si votre compte de développeur fait partie du rôle Administrateur, vous pouvez suivre cette étape, même si vous êtes déjà abonné.

Cliquez sur **Développeurs** sur le menu **Gestion des API** sur la gauche et cliquez sur le nom de votre compte de développeur. Dans cet exemple, nous utilisons le compte de développeur **Clayton Gragg**.

![Configure developer][Configure developer]

Cliquez sur **Ajouter un abonnement**.

![Add subscription][Add subscription]

Cochez la case située en regard de **Version d'évaluation gratuite** et cliquez sur **S'abonner**.

![Add subscription][1]

## <a name="test-rate-limit"> </a>Appel d'une opération et test de la limite de débit

Maintenant que le produit en version d'évaluation gratuite est configuré et publié, nous pouvons appeler des opérations et tester la stratégie de limite de débit. Basculez vers le portail de développeur en cliquant sur **Portail des développeurs** dans le menu situé en haut à droite.

![Portail des développeurs][Portail des développeurs]

Cliquez sur **API** dans le menu supérieur et sélectionnez **API Echo**.

![Portail des développeurs][2]

> Si vous n'avez qu'une API configurée ou visible dans votre compte, cliquez sur des API pour accéder directement aux opérations associées.

Sélectionnez l'opération **Ressource GET** et cliquez sur **Ouvrir la console**.

![Open console][Open console]

Conservez les valeurs de paramètres par défaut et sélectionnez votre clé d'abonnement pour le produit en **version d'évaluation gratuite**.

![Subscription key][Subscription key]

> Si vous possédez plusieurs abonnements, pensez à sélectionner la clé pour **Version d'évaluation gratuite** ou les stratégies qui ont été configurées dans les étapes précédentes ne seront pas effectives.

Cliquez sur **HTTP Get** et affichez la réponse. Notez l'**État de réponse** **200 OK**.

![Operation results][Operation results]

Cliquez sur **HTTP Get** à un débit supérieur à la stratégie de limite de débit de 10 appels par minute. Une fois la stratégie de limite de débit dépassée, un état de réponse **429 Trop de requêtes** est renvoyé.

![Operation results][3]

Les **En-têtes de réponse** et le **Contenu de réponse** indiquent l'intervalle restant avant la réussite des nouvelles tentatives.

Lorsque la stratégie de limite de débit de 10 appels par minute est appliquée, les appels suivants échouent jusqu'à ce que 60 secondes se soient écoulées à partir du premier des 10 appels réussis vers le produit avant le dépassement de la limite de débit. Dans cet exemple, l'intervalle restant est de 43 secondes.

## <a name="next-steps"> </a>Étapes suivantes

-   Consultez les autres rubriques du didacticiel [Prise en main de la configuration avancée des API][Prise en main de la configuration avancée des API].

  [Création d'un produit]: #create-product
  [Ajout d'une API au produit]: #add-api
  [Configuration de la limite de débit d'appels et des stratégies de quota]: #policies
  [Publication du produit]: #publish-product
  [Abonnement d'un compte de développeur au produit]: #subscribe-account
  [Appel d'une opération et test de la limite de débit]: #test-rate-limit
  [Étapes suivantes]: #next-steps
  [Création d'une instance de service Gestion des API]: ../api-management-get-started/#create-service-instance
  [Prise en main de Gestion des API Azure]: ../api-management-get-started
  [API Management console]: ./media/api-management-howto-product-with-rules/api-management-management-console.png
  [Add product]: ./media/api-management-howto-product-with-rules/api-management-add-product.png
  [Add new product]: ./media/api-management-howto-product-with-rules/api-management-new-product-window.png
  [Product added]: ./media/api-management-howto-product-with-rules/api-management-product-added.png
  [Création et utilisation de groupes dans Gestion des API Azure]: ../api-management-howto-create-groups
  [Add developers group]: ./media/api-management-howto-product-with-rules/api-management-add-developers-group.png
  [Configure product]: ./media/api-management-howto-product-with-rules/api-management-configure-product.png
  [Add API to product]: ./media/api-management-howto-product-with-rules/api-management-add-api.png
  [Add Echo API]: ./media/api-management-howto-product-with-rules/api-management-add-echo-api.png
  [Product policy]: ./media/api-management-howto-product-with-rules/api-management-product-policy.png
  [Add policy]: ./media/api-management-howto-product-with-rules/api-management-add-policy.png
  [Policy editor]: ./media/api-management-howto-product-with-rules/api-management-policy-editor-inbound.png
  [Policy statements]: ./media/api-management-howto-product-with-rules/api-management-limit-policies.png
  [Save policy]: ./media/api-management-howto-product-with-rules/api-management-policy-save.png
  [Publish product]: ./media/api-management-howto-product-with-rules/api-management-publish-product.png
  [Configure developer]: ./media/api-management-howto-product-with-rules/api-management-configure-developer.png
  [Add subscription]: ./media/api-management-howto-product-with-rules/api-management-add-subscription-menu.png
  [1]: ./media/api-management-howto-product-with-rules/api-management-add-subscription.png
  [Portail des développeurs]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-menu.png
  [2]: ./media/api-management-howto-product-with-rules/api-management-developer-portal-api-menu.png
  [Open console]: ./media/api-management-howto-product-with-rules/api-management-open-console.png
  [Subscription key]: ./media/api-management-howto-product-with-rules/api-management-select-key.png
  [Operation results]: ./media/api-management-howto-product-with-rules/api-management-http-get-results.png
  [3]: ./media/api-management-howto-product-with-rules/api-management-http-get-429.png
  [Prise en main de la configuration avancée des API]: ../api-management-get-started-advanced

<!--HONumber=46--> 
 