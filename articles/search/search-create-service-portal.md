<properties
	pageTitle="Création d&#39;un service Azure Search dans le portail"
	description="Ajout d&#39;un service Azure Search gratuit ou standard à un abonnement existant à l&#39;aide du portail de gestion"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article" 
	ms.tgt_pltfrm="na"
	ms.date="07/08/2015"
	ms.author="heidist"/>

# Création d'un service Azure Search dans le portail

Microsoft Azure Search est un nouveau service qui permet d'incorporer des fonctionnalités de recherche dans des applications personnalisées. Il fournit le moteur de recherche et assure le stockage de vos données, dont l'accès et la gestion se fait à l'aide d'un Kit de développement logiciel (SDK) .NET ou d'une API REST. Les principales fonctionnalités incluent la saisie semi-automatique des requêtes, les correspondances, la navigation à facettes et le support multilingue. Pour plus d'informations sur le processus de recherche, consultez [Présentation d'Azure Search](fundamentals-azure-search-chappell/).

##Ajout d'un service à votre abonnement  

En tant qu'administrateur, vous pouvez ajouter le service Search à un abonnement existant gratuitement lorsque vous choisissez le service partagé, ou à un tarif standard lorsque vous choisissez des ressources dédiées.

1. Connectez-vous au [portail Azure](https://portal.azure.com).

2. Dans la barre de lancement, cliquez sur **Nouveau** | **Données + stockage** | **Recherche**.

     ![][1]

3. Définissez le nom du service, le niveau de tarification, le groupe de ressources, l'abonnement et l'emplacement. Ces paramètres sont requis et ne sont plus modifiables une fois le service approvisionné.

     ![][2]

	- Le **nom du service** doit être unique, en minuscules et contenir moins de 15 caractères sans espaces. Il devient partie intégrante du point de terminaison de votre service Azure Search. Consultez [Règles d'affectation des noms](https://msdn.microsoft.com/library/azure/dn857353.aspx) pour plus d'informations sur les conventions d'affectation des noms.

	- Le **niveau de tarification** détermine la capacité et la facturation. Les deux niveaux offrent les mêmes fonctionnalités, mais à des niveaux de ressources différents.

		- La **version gratuite** s'applique aux clusters partagés avec d'autres abonnés. Elle offre une capacité suffisante pour tester les didacticiels et écrire du code de validation technique, mais elle n'est pas destinée aux applications de production. En général, le déploiement d'un service gratuit ne prend que quelques minutes.
		- La version **standard** s'applique aux ressources dédiées et est hautement évolutive. À l'origine, un service standard est fourni avec un réplica et une partition, mais vous pouvez ajuster la capacité une fois le service créé. Le déploiement d'un service standard prend plus de temps, environ quinze minutes.

	- Les **groupes de ressources** sont des conteneurs pour les services et les ressources utilisés dans un but commun. Par exemple, si vous créez une application de recherche personnalisée basée sur Azure Search, Sites web Azure ou le stockage d'objets blob, vous pouvez créer un groupe de ressources qui réunit ces services dans les pages de gestion du portail.

	- L’**abonnement** vous permet de choisir votre abonnement, si vous en avez plusieurs.

	- L’**emplacement** correspond à la zone géographique du centre de données. Actuellement, toutes les ressources doivent s'exécuter dans le même centre de données. La répartition de ressources entre plusieurs centres de données n'est pas prise en charge.

4. Cliquez sur **Créer** pour approvisionner le service.

Surveillez les notifications dans la barre de lancement. Une notification s'affiche lorsque le service est prêt à être utilisé.

<a id="sub-2"></a>
##Recherche du nom du service et des clés api de votre service Azure Search

Une fois le service créé, vous pouvez revenir au portail pour obtenir l'URL ou la `api-key`. Pour vous connecter à votre service de recherche, vous devez saisir l'URL et une `api-key` afin d’authentifier l'appel.

1. Dans la barre de lancement, cliquez sur **Accueil** puis sur le service de recherche pour ouvrir le tableau de bord du service.

2. Le tableau de bord des services affiche des vignettes contenant des informations essentielles, ainsi que l'icône de clé permettant d'accéder aux clés administrateur.

  	![][3]

3. Copiez l'URL du service et une clé administrateur. Vous en aurez besoin pour la tâche suivante, [Test des opérations de service](#sub-4).

<a id="sub-3"></a>
##Mise à niveau vers le niveau standard

De nombreux clients commencent par le service gratuit puis évoluent vers le niveau standard lorsque les performances des requêtes ou du stockage ne suffisent plus à exécuter les charges de travail. Le niveau standard vous permet d'accéder aux ressources dédiées d'un centre de données Azure réservé à votre seul usage. Les opérations de recherche nécessitent à la fois des réplicas de stockage et de service. Lorsque vous vous inscrivez à une recherche standard, vous pouvez optimiser la configuration du service pour utiliser une plus grande partie de la ressource qui est la plus importante pour votre scénario.

Pour utiliser le niveau standard, répétez les étapes précédentes de cet article pour créer un service de recherche, en choisissant le niveau de tarification Standard. Notez que la configuration des ressources dédiées peut prendre du temps, jusqu'à 15 minutes voire plus.

Il n'existe aucune mise à niveau sur place de la version gratuite. Le passage à l'offre standard, avec son potentiel en matière de mise à l'échelle, requiert un nouveau service. Il vous faudra recharger les index et les documents utilisés par votre application de recherche.

Un service de recherche standard commence avec un réplica et une partition, mais il peut facilement être remis à l'échelle à des niveaux de ressource plus élevés.

1.	Une fois le service créé, revenez au tableau de bord des services.

2.	Cliquez sur la vignette **Mise à l'échelle**.

3.	Utilisez les curseurs pour ajouter des réplicas, des partitions, ou les deux.

Les réplicas et les partitions supplémentaires sont facturés en tant qu'unités de recherche. Le nombre total d'unités de recherche requises pour prendre en charge une configuration de ressource particulière s'affiche sur la page, à mesure que vous ajoutez des ressources.

Vous pouvez consulter les [Informations de tarification](http://go.microsoft.com/fwlink/p/?LinkID=509792) pour obtenir les informations de facturation par unité. Consultez [Limites et contraintes](http://msdn.microsoft.com/library/azure/dn798934.aspx) pour savoir comment configurer des combinaisons de partitions et de réplicas.

<a id="sub-4"></a>
##Test des opérations de service

La dernière étape de la configuration de Search consiste à confirmer que votre service est opérationnel et accessible à partir d'une application cliente. Vous pouvez utiliser un des liens ci-dessous pour vérifier la disponibilité du service sans saisir de code.

- [Utilisation de Chrome Postman avec Azure Search](search-chrome-postman.md)
- [Utilisation de Telerik Fiddler avec Azure Search](search-fiddler.md)

<!--Next steps and links -->
<a id="next-steps"></a>
##Étapes suivantes

Les informations supplémentaires suivantes vous expliquent comment créer et gérer des applications de recherche utilisant Azure Search.

- [Utilisation d'Azure Search dans .NET](search-howto-dotnet-sdk.md)

- [Gestion de votre solution de recherche dans Microsoft Azure](search-manage.md)

- [Azure Search sur MSDN](http://msdn.microsoft.com/library/dn798933.aspx)

- [Vidéo Channel 9 : Présentation d'Azure Search](http://channel9.msdn.com/Shows/Data-Exposed/Introduction-To-Azure-Search)


<!--Anchors-->
[Find the service name and api-keys of your Azure Search service]: #sub-2
[Upgrade to standard tier]: #sub-3
[Test service operations]: #sub-4
[Next steps]: #next-steps

<!--Image references-->
[1]: ./media/search-create-service-portal/create-search-portal-1.PNG
[2]: ./media/search-create-service-portal/create-search-portal-2.PNG
[3]: ./media/search-create-service-portal/create-search-portal-3.PNG

<!---HONumber=July15_HO2-->