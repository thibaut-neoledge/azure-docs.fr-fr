<properties
	pageTitle="Créer un service Azure Search dans le portail Azure | Microsoft Azure | Service de recherche cloud"
	description="Ajouter un service Azure Search gratuit ou standard à un abonnement existant à l’aide du portail Azure. Azure Search est un service de recherche hébergé dans le cloud dédié aux applications personnalisées."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="na"
	ms.workload="search"
	ms.topic="hero-article"
	ms.tgt_pltfrm="na"
	ms.date="02/18/2016"
	ms.author="heidist"/>

# Créer un service Azure Search dans le portail Azure

Microsoft Azure Search est un service de recherche cloud hébergé qui permet d'incorporer des fonctionnalités de recherche dans des applications personnalisées. Il fournit un moteur de recherche et assure le stockage de vos données de recherche, dont l’accès et la gestion peuvent s’effectuer à l’aide du portail Azure, d’un Kit de développement logiciel (SDK) .NET ou d’une API REST. Les principales fonctionnalités incluent la saisie semi-automatique des requêtes, les correspondances, la mise en surbrillance des résultats, la navigation à facettes, les profils de score et le support multilingue. Pour en savoir plus sur Azure Search, consultez [Présentation d’Azure Search](search-what-is-azure-search.md).

Azure Search est disponible à plusieurs niveaux de prix allant du niveau gratuit (partagé) au niveau standard, où le prix dépend de la capacité que vous souhaitez.

## Ajouter gratuitement Azure Search à votre abonnement

En tant qu’administrateur, vous pouvez ajouter le service Azure Search à un abonnement Azure existant gratuitement quand vous choisissez le service partagé. Vous pouvez vous connecter à un [abonnement d’essai gratuit](../../includes/free-trial-note.md) pour commencer votre évaluation.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la barre de lancement, cliquez sur **Nouveau** > **Données + stockage** > **Recherche**.

     ![][1]

3. Définissez le nom du service, le niveau tarifaire, le groupe de ressources, l’abonnement et l’emplacement. Ces paramètres sont requis et ne sont plus modifiables une fois le service approvisionné.

     ![][2]

	- Le **nom du service** doit être unique, en minuscules et contenir moins de 60 caractères sans espaces. Ce nom devient partie intégrante du point de terminaison de votre service Azure Search (par exemple, « https://**nom-de-mon-service**.search.windows.net »). Consultez [Règles d'affectation des noms](https://msdn.microsoft.com/library/azure/dn857353.aspx) pour plus d'informations sur les conventions d'affectation des noms.

	- Le **niveau de tarification** détermine la capacité et la facturation. Les deux niveaux offrent les mêmes fonctionnalités, mais à des niveaux de ressources différents.

		- La **version gratuite** s'applique aux clusters partagés avec d'autres abonnés. Elle offre une capacité suffisante pour tester les didacticiels et écrire du code de validation technique, mais elle n'est pas destinée aux applications de production. En général, le déploiement d'un service gratuit ne prend que quelques minutes.
		- La version **standard** s'applique aux ressources dédiées et est hautement évolutive. À l'origine, un service standard est fourni avec un réplica et une partition, mais vous pouvez ajuster la capacité une fois le service créé. Le déploiement d’un service standard prend plus de temps, environ 15 minutes.

	- Les **groupes de ressources** sont des conteneurs pour les services et les ressources utilisés dans un but commun. Par exemple, si vous créez une application de recherche personnalisée basée sur Azure Search, la fonctionnalité Web Apps dans Azure App Service Azure et le stockage d’objets blob Azure, vous pouvez créer un groupe de ressources qui réunit ces services dans les pages de gestion du portail.

	- L’**abonnement** vous permet de choisir votre abonnement, si vous en avez plusieurs.

	- L’**emplacement** correspond à la zone géographique du centre de données. Actuellement, toutes les ressources doivent s’exécuter dans le même centre de données. La répartition de ressources entre plusieurs centres de données n’est pas prise en charge.

4. Cliquez sur **Créer** pour approvisionner le service.

Surveillez les notifications dans la barre de lancement. Une notification s'affiche lorsque le service est prêt à être utilisé.

<a id="sub-3"></a>
## Ajouter un service de recherche de niveau Standard pour obtenir des ressources dédiées

De nombreux clients commencent par le service gratuit avant de basculer au niveau Standard pour prendre en charge de plus grandes charges de travail. Le niveau Standard vous permet d’accéder aux ressources dédiées d’un centre de données Azure réservé à votre seul usage.

Les opérations Azure Search nécessitent à la fois des réplicas de stockage et de service. Contrairement au service gratuit qui ne permet pas d’ajouter des ressources, le niveau Standard vous permet d’évoluer pour prendre en charge davantage de stockage ou de requêtes en augmentant la ressource la plus importante de vos charges de travail, quelle qu’elle soit.

Pour utiliser le niveau Standard, vous devez créer un nouveau service de recherche à ce niveau de tarification. Vous pouvez répéter les étapes précédentes de cet article pour créer un nouveau service Azure Search. Notez que la configuration des ressources dédiées peut prendre du temps, jusqu'à 15 minutes voire plus.

Il n'existe aucune mise à niveau sur place de la version gratuite. Le passage à l'offre standard, avec son potentiel en matière de mise à l'échelle, requiert un nouveau service. Il vous faudra recharger les index et les documents utilisés par votre application de recherche.

Un service Azure Search standard commence avec un réplica et une partition, mais il peut facilement être remis à l’échelle à des niveaux de ressource plus élevés.

1.	Une fois le service créé, revenez au tableau de bord des services.

2.	Cliquez sur la vignette **Mise à l'échelle**.

3.	Utilisez les curseurs pour ajouter des réplicas, des partitions, ou les deux.

Les réplicas et les partitions supplémentaires sont facturés en tant qu'unités de recherche. Le nombre total d’unités de recherche requises pour prendre en charge une configuration de ressource particulière s’affiche sur la page, à mesure que vous ajoutez des ressources.

Vous pouvez consulter les [Informations de tarification](http://go.microsoft.com/fwlink/p/?LinkID=509792) pour obtenir les informations de facturation par unité. Consultez [Limites et contraintes](search-limits-quotas-capacity.md) pour choisir des combinaisons de partitions et de réplicas.

<a id="sub-2"></a>
## Recherche du nom du service et des clés api de votre service Azure Search

Une fois le service créé, vous pouvez revenir au portail Azure pour obtenir l’URL ou la `api-key`. Pour vous connecter à votre service Azure Search, vous devez entrer l’URL et une `api-key` afin d’authentifier l’appel.

1. Dans la barre de lancement, cliquez sur **Accueil** puis sur le service Azure Search pour ouvrir le tableau de bord du service.

2. Le tableau de bord des services affiche des vignettes contenant des informations essentielles, ainsi que l'icône de clé permettant d'accéder aux clés administrateur.

  	![][3]

3. Copiez l'URL du service et une clé d’administration. Vous en aurez besoin pour la tâche suivante, la [Vérification de la disponibilité du service](#sub-4).


<a id="sub-4"></a>
## Vérifier la disponibilité du service

La dernière étape de la configuration d’Azure Search consiste à confirmer que votre service est opérationnel et accessible à partir d’une application cliente. Vous pouvez utiliser [Fiddler et Azure Search](search-fiddler.md) pour vérifier la disponibilité du service.

<!--Next steps and links -->
<a id="next-steps"></a>
## Étapes suivantes

Maintenant que votre service est créé, vous pouvez passer aux étapes suivantes : créer un [index](search-what-is-an-index.md), [interroger un index](search-query-overview.md), créer et gérer des applications de recherche utilisant Azure Search.

- [Création d’un index Azure Search dans le portail Azure](search-create-index-portal.md)

- [Interrogation d’un index Azure Search à l’aide de Search Explorer dans le portail Azure](search-explorer.md)

- [Prise en main d'Azure Search dans .NET](search-get-started-dotnet.md)

- [Utilisation d'Azure Search dans .NET](search-howto-dotnet-sdk.md)

- [Gestion de votre solution de recherche dans Microsoft Azure](search-manage.md)




<!--Anchors-->
[Find the service name and api-keys of your Azure Search service]: #sub-2
[Upgrade to the standard tier]: #sub-3
[Test service operations]: #sub-4
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/search-create-service-portal/create-search-portal-1.PNG
[2]: ./media/search-create-service-portal/create-search-portal-2.PNG
[3]: ./media/search-create-service-portal/create-search-portal-3.PNG

<!---HONumber=AcomDC_0224_2016-->