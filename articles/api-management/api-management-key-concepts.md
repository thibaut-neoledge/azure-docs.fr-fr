<properties 
	pageTitle="Principaux concepts du service API Management" 
	description="Learn about APIs, products, roles, groups, and other API Management key concepts." 
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
	ms.date="11/18/2014" 
	ms.author="sdanie"/>

#Principaux concepts du service API Management

API Management aide les organisations à publier des API pour des développeurs externes, partenaires et internes, afin de libérer le potentiel de leurs données et services. Toutes les entreprises cherchent à étendre leurs opérations en tant que plateforme numérique, en créant de nouveaux canaux, en trouvant de nouveaux clients et en suscitant un engagement supérieur auprès de ceux existants. Gestion des API offre les compétences principales pour assurer un programme d'API réussi à travers l'engagement du développeur, les perspectives commerciales, l'analyse, la sécurité et la protection.

Pour utiliser Gestion des API, les administrateurs créent des API. Chaque API se compose d'une ou plusieurs opérations et chacune peut être ajoutée à un produit ou plusieurs. Pour utiliser une API, les développeurs s'abonnent à un produit qui contient cette API, puis ils peuvent appeler l'opération de l'API, sujette à toutes les stratégies d'utilisation pouvant être en vigueur.

Cette rubrique offre une présentation des principaux concepts associés à Gestion des API.

## Dans cette rubrique

-   [API et opérations][]
-   [Produits][]
-   [Groupes][]
-   [Développeurs][]
-   [Stratégies][]
-	[Portail des développeurs][]


## <a name="apis"> </a>API et opérations

Les API sont la base d'une instance du service Gestion des API. Chaque API représente un ensemble d'opérations disponibles pour les développeurs. Chaque API contient une référence au service principal qui implémente l'API, et ses opérations effectuent un mappage vers les opérations implémentées par le service principal. Les opérations dans Gestion des API sont hautement configurables. Elles contrôlent le mappage d'URL, les paramètres de requête et de chemin d'accès, le contenu de la demande et de la réponse, et la mise en cache de la réponse de l'opération. La limite de débit, les quotas et les stratégies de restriction de l'adresse IP peuvent également être implémentés au niveau de l'API ou du fonctionnement individuel.

Pour plus d'informations, consultez [Création d'API][] et [Ajout d'opérations à une API][].


## <a name="products"> </a> Produits

Les produits sont la façon dont les API sont présentées en surface aux développeurs. Les produits dans Gestion des API possèdent une ou plusieurs API et sont configurés avec un titre, une description et des conditions d'utilisation. Lorsqu'un produit est prêt à l'emploi pour les développeurs, il peut être publié. Une fois publié, les développeurs peuvent l'afficher et s'y abonner. L'approbation d'abonnement est configurée au niveau du produit et peut nécessiter l'approbation de l'administrateur ou être automatiquement approuvée.

Les groupes permettent de gérer la visibilité des produits pour les développeurs. Les produits accordent de la visibilité aux groupes. Les développeurs peuvent afficher les produits visibles pour les groupes auxquels ils appartiennent et s'y abonner. 

Pour plus d'informations, consultez [Création et publication d'un produit][].

## <a name="groups"> </a> Groupes

Les groupes permettent de gérer la visibilité des produits pour les développeurs. Le service Gestion des API possède les groupes intégrés suivants.

-	**Administrateurs** - Les administrateurs gèrent les instances du service API Management, créant les API, opérations et produits qui sont utilisés par les développeurs.
-	**Développeurs** - Les développeurs sont les clients qui génèrent des applications grâce à vos API. Les développeurs bénéficient d'un accès au [portail des développeurs][] et génèrent des applications qui appellent les opérations d'une API.
-	**Invités** - Les utilisateurs non authentifiés, tels que les prospects, qui consultent le portail des développeurs d'une instance d'API Management appartiennent à ce groupe. Ils peuvent recevoir certains accès en lecture seule, comme la possibilité d'afficher les API, mais pas de les appeler.

En plus de ces groupes intégrés, les administrateurs peuvent créer des groupes personnalisés. Ces derniers possèdent les mêmes privilèges que le groupe de développeurs intégré, et peuvent être utilisés pour gérer plusieurs groupes de développeurs. Par exemple, vous pouvez créer un groupe personnalisé pour les développeurs qui utiliseront les API à partir d'un produit, et un autre groupe de développeurs qui utiliseront les API à partir d'un autre produit.

Pour plus d'informations, consultez [Création et utilisation de groupes][].

## <a name="developers"> </a> Développeurs

Les développeurs représentent les comptes d'utilisateur dans une instance du service Gestion des API. Ils peuvent être créés ou invités à rejoindre le groupe par les administrateurs, ou ils peuvent s'inscrire au [Portail des développeurs][]. Chaque développeur est membre d'un ou plusieurs groupes, et peut s'abonner aux produits qui accordent de la visibilité à ces groupes.

Lorsque des développeurs s'abonnent à un produit, ils reçoivent les clés principale et secondaire pour le produit. Cette clé est utilisée pour les appels dans les API du produit.

Pour plus d'informations, consultez [Création ou invitation de développeurs][] et [Association de groupes à des développeurs][].

## <a name="policies"> </a> Stratégies

Les stratégies sont une fonctionnalité puissante de Gestion des API. Elles permettent à l'éditeur de modifier le comportement de l'API grâce à la configuration. Les stratégies sont un ensemble d'instructions qui sont exécutées dans l'ordre sur demande ou sur réponse d'une API. Les instructions les plus utilisées comprennent la conversion du format XML au format JSON et la limitation du débit d'appels pour restreindre la quantité d'appels entrants d'un développeur. De nombreuses autres stratégies sont disponibles.

Pour obtenir la liste complète des stratégies du service API Management, consultez [Référence de stratégie][]. Pour plus d'informations sur l'utilisation et la configuration des stratégies, consultez [Stratégies API Management][]. Pour accéder à un didacticiel sur la création d'un produit avec des stratégies de limite de débit et de quota, consultez [Création et configuration de paramètres produit avancés][].


## <a name="developer-portal"> </a> Portail des développeurs

Le portail des développeurs est l'endroit où les développeurs peuvent en savoir plus sur votre API, afficher et appeler des opérations et s'abonner à des produits. Les prospects peuvent consulter le portail des développeurs, afficher les API et opérations et se connecter. L'URL de votre portail des développeurs se trouve sur le tableau de bord du portail Azure pour votre instance du service Gestion des API.

Vous pouvez personnaliser l'aspect de votre portail des développeurs en ajoutant du contenu personnalisé, en personnalisant des styles et en ajoutant votre marque.

[API et opérations]: #apis
[Produits]: #products
[Groupes]: #groups
[Développeurs]: #developers
[Stratégies]: #policies
[Portail des développeurs]: #developer-portal

[Création d'API]: ../api-management-howto-create-apis
[Ajout d'opérations à une API]: ../api-management-howto-add-operations
[Création et publication d'un produit]: ../api-management-howto-add-products
[Création et utilisation de groupes]: ../api-management-howto-create-groups
[Association de groupes aux développeurs]: ../api-management-howto-create-groups/#associate-group-developer
[Création et configuration de paramètres de produit avancés]: ../api-management-howto-product-with-rules
[Création ou invitation de développeurs]: ../api-management-howto-create-or-invite-developers
[Référence de stratégie]: ../api-management-policy-reference
[Stratégies API Management]: ../api-management-howto-policies
[Création d'une instance du service API Management]: ../api-management-get-started/#create-service-instance




<!--HONumber=35.2-->

<!--HONumber=46--> 
 