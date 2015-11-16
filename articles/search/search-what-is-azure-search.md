<properties
	pageTitle="Présentation d’Azure Storage"
	description="Azure Search est un service de recherche cloud hébergé. Apprenez-en davantage dans cette présentation technique et ce résumé des fonctionnalités."
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""
    tags="azure-portal"/>

<tags
	ms.service="search"
	ms.devlang="NA"
	ms.workload="search"
	ms.topic="article" 
	ms.tgt_pltfrm="na"
	ms.date="11/04/2015"
	ms.author="heidist"/>

# Présentation d’Azure Storage

Azure Search Service est un service de recherche cloud hébergé, entièrement géré, qui permet aux développeurs de créer des applications de recherche avancées à l'aide d'un Kit de développement logiciel (SDK) .NET ou d'API REST. Il inclut une recherche en texte intégral étendue à votre contenu, des comportements de recherche plus avancés similaires à ceux des moteurs de recherche web commerciaux, notamment des suggestions de requête en fonction d’une saisie de terme partiel, le surlignage des correspondances et la navigation à facettes. La prise en charge de la langue naturelle est intégrée et utilise des règles linguistiques appropriées à la langue spécifiée.

Vous pouvez faire évoluer votre service pour répondre à des besoins de recherche ou de capacité de stockage accrus. Par exemple, les revendeurs peuvent augmenter la capacité pour répondre aux volumes supplémentaires associés aux périodes de soldes ou aux promotions.

Azure Search est un service basé sur les API destiné aux développeurs et intégrateurs système qui maîtrisent les services web et HTTP. Azure Search facilite la gestion d'un service de recherche de cloud et simplifie la création d'applications web basée sur la recherche et d’applications mobiles.

##Fonctionnement

Azure Search est un [service PaaS](https://wikipedia.org/wiki/Platform_as_a_service) qui délègue la gestion du serveur et de l'infrastructure à Microsoft, et vous laisse un service prêt à l'emploi que vous remplissez avec des données de recherche, puis auquel vous accédez à partir de votre application. Selon la façon dont vous configurez le service, vous utiliserez le service gratuit partagé avec d'autres abonnés Azure Search ou le niveau de tarification Standard, qui offre des ressources dédiées utilisées uniquement par votre service. La recherche Standard est évolutive, avec des options pour répondre aux demandes croissantes en stockage ou en charges de requêtes.

Azure Search stocke vos données dans un index qui peut être consulté via des requêtes de texte intégral. Le schéma de ces index peut être créé dans le portail Azure, ou par programme à l'aide de la bibliothèque cliente ou des API REST. Une fois le schéma défini, vous pouvez télécharger vos données vers le service Azure Search où elles seront indexées par la suite.

Vous pouvez utiliser des modèles d’émission ou de collecte pour télécharger des données vers l'index. Le modèle de collecte est fourni par des indexeurs qui peuvent être configurés pour des mises à jour à la demande ou planifiées (consultez la rubrique [Opérations d'indexeur (API REST du service Azure Search)](https://msdn.microsoft.com/library/azure/dn946891.aspx)), ce qui vous permet de recevoir facilement les données et les modifications de données à partir d'une base de données Azure DocumentDB, Azure SQL ou SQL Server hébergée dans une machine virtuelle Azure. Le modèle d’émission est fourni via le Kit de développement logiciel (SDK) ou les API REST permettant d’envoyer les documents mis à jour à un index. Vous pouvez émettre des données à partir de n'importe quel groupe de données, à condition d’utiliser le format JSON. Consultez la rubrique [Ajout, mise à jour ou suppression de documents](https://msdn.microsoft.com/library/azure/dn798930.aspx) ou [Utilisation du Kit de développement logiciel (SDK) .NET](search-howto-dotnet-sdk.md) pour obtenir des conseils sur le chargement des données.

Certains développeurs choisiront un indexeur pour sa facilité d’utilisation. Pour d’autres développeurs, le modèle d’émission vaut bien un peu de travail supplémentaire. Le choix du modèle d'émission repose sur deux critères. Tout d'abord, vous évitez la charge supplémentaire que les mécanismes de type robot imposent à vos serveurs de données. Deuxièmement, vous évitez la latence inhérente à une indexation planifiée. Lors de la période des fêtes et des soldes, vos recherches doivent refléter instantanément l'état du stock disponible. Un modèle d'émission peut vous fournir ce degré de précision.

##Ce que vous allez créer et stocker

Le workflow typique consiste à créer le schéma et les documents dans un environnement de développement local, puis à utiliser le Kit de développement logiciel (SDK) .NET ou l'API REST pour télécharger, traiter et finalement interroger les données. Toutes les données indexées sont conservées dans Azure Search pour améliorer les performances et garantir la cohérence des opérations de recherche.

Vous pouvez utiliser l'éditeur intégré au portail pour créer le schéma d'index et les profils de score, ce qui est idéal pour les prototypes. Les développeurs qui ont besoin d'une approche reproductible et automatisée préféreront créer l'index dans le code. Comme les dernières fonctionnalités sont d’abord ajoutées à l'API, une approche par programmation peut être votre unique option, selon les exigences de votre application.

Lorsque vous créez le schéma, vous définissez les champs et leurs attributs pris en charge dans votre application de recherche. Les champs contiennent des données pouvant faire l’objet d’une recherche, tels que les noms de produits, descriptions, commentaires des clients, marques, prix, notifications promotionnelles, et ainsi de suite. Les attributs précisent les types d'opérations qui peuvent être effectuées. Les attributs les plus couramment utilisés indiquent par exemple si un champ prend en charge la recherche en texte intégral (searchable=true), les filtres (filterable=true) ou les facettes (facetable=true).

Les champs contiennent également des données qui ne peuvent pas faire l’objet d’une recherche et associées à votre application de recherche, notamment les valeurs utilisées en interne dans les filtres et les profils de score, ou peut-être des liens URL vers un contenu stocké dans d’autres plateformes de stockage (par exemple, des fichiers vidéo ou image conservés dans le stockage BLOB). Un exemple souvent cité d'un champ interne uniquement est une marge bénéficiaire ou une autre valeur qui indique un potentiel de revenus. Votre application de recherche doit peut-être promouvoir des éléments spécifiques qui apportent un avantage financier supérieur à votre société. Votre schéma peut inclure des attributs de champ autorisant ces types de comportements de recherche.

Les documents représentent les données détaillées retournées par le moteur de recherche dans les résultats de la recherche. Par exemple, si votre application de recherche est un catalogue en ligne, vous disposez d’un document pour chaque référence SKU et la page des résultats de la recherche sera générée à l'aide des valeurs retournées à partir des documents correspondant aux termes de recherche.

##Voir en action

Regardez nos vidéos pour en savoir plus sur les scénarios et les fonctionnalités. Visitez [Azure Search : didacticiels, démonstrations vidéo et exemples](search-video-demo-tutorial-list.md) pour obtenir des liens vers le contenu vidéo.

##Détail des fonctionnalités

Azure Search fournit de la valeur dans plusieurs catégories, notamment la mise en service et la mise à l'échelle, la programmabilité, l’accès et le contrôle, et dans les fonctionnalités que vous choisissez d'implémenter dans votre application de recherche personnalisée.

La liste de contrôle des fonctionnalités suivante peut vous aider à vérifier si Azure Search répond aux besoins de votre application de recherche. Vous trouverez de nouvelles annonces de fonctionnalités dans la section des dernières mises à jour apportées à Azure Search. Vous pouvez également consulter la page sur les demandes de fonctionnalité Azure Search pour vérifier l'état des fonctionnalités pas encore implémentées.

Vous avez des questions sur une fonctionnalité spécifique ? Consultez le [forum Azure Search sur MSDN](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch). Vous pouvez également consulter la [page sur les demandes de fonctionnalité Azure Search](http://feedback.azure.com/forums/263029-azure-search) pour vérifier l'état des fonctionnalités pas encore implémentées.

###Fonctionnalités et restrictions sur la capacité et la mise à l’échelle

Le service peut fonctionner dans le cadre d’un déploiement de service standard ou partagé. La recherche standard prend en charge des ressources dédiées qui peuvent être mises à l'échelle en fonction des charges de travail. Le service partagé est gratuit et conçu pour tester la fonctionnalité des services, sans aucune garantie de performances.

**Évolutivité** par incréments du nombre de stockages et de documents (partitions) ou dans une charge de requête (réplicas). Chaque réplica exécute une seule copie d'un index. La haute disponibilité requiert 2 réplicas pour les charges de travail de requête et 3 pour les charges de travail en lecture-écriture (requêtes et l'indexation). Pour plus d'informations sur la planification de la capacité, consultez la rubrique Limites et contraintes (Azure Search).

Azure Search étend automatiquement les index et les documents sur les partitions que vous avez allouées à ce service. Cela signifie que vous ne peut pas ancrer des index et des documents à un ensemble de partitions et de réplicas.

Les partitions et les réplicas sont des ressources à l’échelle du service, dont tous les index sont en cours d'exécution sur l’ensemble les réplicas. Si vous avez besoin d’isoler un index ou que vous devez respecter des conditions pour la dispersion géographique des services et des ressources dans différents centres de données, vous pouvez créer un second service.

Il existe des limites au niveau du stockage et du nombre d'index et de documents chargés dans le service. La première limite atteinte constituera votre limite réelle : stockage physique épuisé ou limite supérieure du nombre d’index et de documents atteinte. Pour plus d’informations, consultez [Limites et contraintes (Azure Search)](search-limits-quotas-capacity.md).

###Programmabilité

L’API REST se compose de requêtes et de réponses HTTP, avec un contenu au format JSON. Il existe une API pour l’accès au service, et une API pour la gestion du service. Consultez les rubriques [API REST du service Azure Search](https://msdn.microsoft.com/library/azure/dn798935.aspx) et [API REST de gestion Azure Search](https://msdn.microsoft.com/library/azure/dn832684.aspx) pour plus d'informations.

Le Kit de développement logiciel (SDK) .NET inclut des classes qui facilitent l’utilisation de votre service Azure Search, sans avoir à travailler directement aux formats HTTP et JSON. Pour plus d’informations, consultez la rubrique [Utilisation du Kit de développement logiciel (SDK) .NET Azure Search](search-howto-dotnet-sdk.md).

###Accès et contrôle

La recherche est accessible uniquement via le protocole HTTPS.

L'authentification pour accéder à Azure Search à partir de votre application hôte s’effectue est via une clé API administrateur dans un en-tête HTTP. Il n'existe aucun modèle d'authentification ou d'autorisation par utilisateur. Toutefois, vous pouvez utiliser $filter pour restreindre l'accès selon l’identité de l’utilisateur. Vous pouvez également utiliser plusieurs clés API de requêtes et les affecter à différentes applications clientes. Pour plus d’informations sur la gestion des clés, consultez la rubrique [Gestion de votre service de recherche sur Microsoft Azure](search-manage.md). Consultez la rubrique [Recherche de documents (API REST du service Azure Search)](https://msdn.microsoft.com/library/azure/dn798927.aspx) pour plus d'informations sur $filter.

###Index et documents

Vous pouvez utiliser plusieurs index (consultez la rubrique [Limites et contraintes (Azure Search)](search-limits-quotas-capacity.md) pour connaître les limites en fonction des niveaux de tarification). Notez que la jointure d’index n’est pas prise en charge actuellement. Une demande de recherche ne peut spécifier qu’un seul index.

Les **documents** contiennent des champs et des attributs connexes. Les champs incluent le texte pouvant faire l’objet d’une recherche, les valeurs utilisées le plus fréquemment (voire exclusivement) dans les filtres et les profils de score, et très probablement des URL ou des pointeurs vers le contenu, par exemple des images, dans d’autres magasins de données. De nombreuses applications de recherche utilisent plusieurs formes de stockage. Les images ou les vidéos peuvent être stockées de façon plus économique dans d'autres médias de stockage, notamment Azure Blob.

Les **indexeurs** permettent de planifier les mises à jour d'index à partir de données modifiées dans une base de données SQL Azure, SQL Server sur des machines virtuelles Azure, ou Azure DocumentDB. Consultez la rubrique [Opérations de l’indexeur (API REST du service Azure Search)]((https://msdn.microsoft.com/library/azure/dn946891.aspx) pour plus d’informations.

Les **requêtes** peuvent être formulées à l'aide de la syntaxe OData pour les filtres booléens et d’une syntaxe de requête simple pour la recherche en texte intégral. Consultez les rubriques [Syntaxe d'expression OData pour Azure Search](https://msdn.microsoft.com/library/azure/dn798921.aspx) et [Syntaxe de requête simple dans Azure Search](https://msdn.microsoft.com/library/azure/dn798920.aspx) pour plus d'informations.

Vous pouvez rechercher des documents spécifiques en fonction de leur ID pour récupérer rapidement des éléments spécifiques. Cela est utile quand un utilisateur clique sur un résultat de recherche en particulier et que vous voulez rechercher des détails spécifiques sur ce document. Consultez la rubrique [Recherche de document (API REST du service Azure Search)](https://msdn.microsoft.com/library/azure/dn798929.aspx) pour plus d'informations.

##Fonctionnalités de l'application de recherche

La **recherche en texte intégral** (activée par défaut pour les champs de texte) est activée pour n'importe quel champ dont l'attribut peut faire l’objet d’une recherche. La recherche en texte intégral repose sur la correspondance des préfixes, ce qui signifie que les correspondances sont basées sur la première partie d'un terme de recherche et non sur le milieu ou la fin du mot. Consultez la rubrique [Création d’un index (API REST du service Azure Search)](https://msdn.microsoft.com/library/azure/dn798941.aspx) ou [Création d’un index dans le portail](search-create-index-portal.md) pour plus d’informations sur la définition d’un index.

Les **profils de score** servent à construire des modèles de classement qui optimisent la recherche en fonction des objectifs de l'entreprise. Par exemple, vous souhaiterez peut-être que les nouveaux produits ou les produits en promotion apparaissent en priorité dans les résultats de la recherche. Vous pouvez également créer des profils de score à l'aide de balises pour obtenir un score personnalisé en fonction de préférences de recherche de client que vous avez suivies et stockées séparément. Pour plus d’informations, consultez [Ajouter des profils de score à un index de recherche (API REST du Service Azure Search)](https://msdn.microsoft.com/library/azure/dn798928.aspx).

Le **support multilingue**, disponible pour cinquante langues, utilise plusieurs options de piles de traitement du langage naturel, notamment les célèbres analyseurs Lucene et les propres analyseurs de Microsoft qui alimentent Microsoft Office et Bing (version préliminaire uniquement). Consultez les rubriques [Support multilingue (API REST du service Azure Search)](https://msdn.microsoft.com/library/azure/dn879793.aspx) pour Lucene, et [API REST du service Azure Search : version 2015-02-28-Preview](search-api-2015-02-28-Preview.md) pour les processeurs de langage naturel.

La **navigation à facettes** fait référence à une arborescence de catégorisation utilisée pour la recherche autonome, souvent basée sur des marques, des modèles, des tailles ou d’autres catégories pertinentes pour vos données. La navigation à facettes est implémentée via des attributs dans votre index, combinée avec un champ de facette fourni dans une requête. Consultez [Navigation à facettes](search-faceted-navigation.md) pour plus d'informations.

Les **suggestions** des requêtes avec saisie automatique ou prédictives sont prises en charge via les attributs de votre index. Azure Search prend en charge à la fois les correspondances approximatives et les correspondances infixes (correspondance d’une partie du contenu du champ). Vous pouvez effectuer les deux ; elles ne sont pas mutuellement exclusives. Consultez les rubriques [Création d’un index (API REST du service Azure Search)](https://msdn.microsoft.com/library/azure/dn798941.aspx) pour plus d'informations sur l'activation des suggestions, et [Suggestions (API REST du service Azure Search)](https://msdn.microsoft.com/library/azure/dn798936.aspx) pour leur utilisation.

Les **filtres** peuvent être utilisés pour implémenter la navigation à facettes, dans la construction de la requête, ou globalement pour limiter les opérations de recherche afin de filtrer les critères que vous établissez dans le code. Les filtres sont activés pour des champs spécifiques via votre schéma d'index et via le paramètre de recherche $Filter. Consultez les rubriques[Création d’un index (API REST du service Azure Search)](https://msdn.microsoft.com/library/azure/dn798941.aspx) et [Recherche de documents (API REST du service Azure Search)](https://msdn.microsoft.com/library/azure/dn798927.aspx) pour plus d'informations.

Un **tri** est également activé pour des champs spécifiques via le schéma d'index, puis implémenté comme un paramètre de recherche via le paramètre $orderBy. Ici aussi, consultez les rubriques[Création d’un index (API REST du service Azure Search)](https://msdn.microsoft.com/library/azure/dn798941.aspx) et [Recherche de documents (API REST du service Azure Search)](https://msdn.microsoft.com/library/azure/dn798927.aspx) pour plus d'informations.

Le **nombre** de résultats de recherche renvoyés pour une requête, et la possibilité de limiter le nombre de résultats renvoyés simultanément, sont implémentés via $top et $skip. Consultez la rubrique [Recherche de documents (API REST de service Azure Search)](https://msdn.microsoft.com/library/azure/dn798927.aspx) pour plus d'informations.

Les **correspondances mises en surbrillance**, spécifiées via le paramètre de mise en surbrillance des requêtes, vous permettent de montrer à un utilisateur un extrait de texte mettant en surbrillance des mots clés trouvés dans les termes de recherche saisis par l'utilisateur. Consultez la rubrique [Recherche de documents (API REST du service Azure Search)](https://msdn.microsoft.com/library/azure/dn798927.aspx) pour en savoir plus sur les paramètres de la requête.

##Rapports et analyse

L'utilisation des ressources apparaît sur le tableau de bord du service et vous offre un aperçu du stockage utilisé.

L’utilisation du stockage, le nombre de documents et le nombre d'index sont disponibles dans le portail. Vous pouvez également utiliser l'API. Consultez la rubrique [Obtenir des statistiques d'index (API REST de service Azure Search)](https://msdn.microsoft.com/library/azure/dn798942.aspx) pour plus d'informations.

Il n'existe aucun mécanisme intégré pour mesurer le débit des requêtes ou d'autres opérations de service. En outre, il n'existe actuellement aucune prise en charge de la journalisation ou de l'analyse des résultats de recherche (par exemple, récupération d'une liste des termes de recherche n’ayant renvoyé aucun résultat ou création de rapports sur l'origine des demandes de recherche).

##Faites un essai

Consultez la rubrique [Création d’un service Azure Search](search-create-service-portal.md) pour configurer le service ou [Tester Azure App Service](http://go.microsoft.com/fwlink/p/?LinkId=618214) pour une session gratuite d'une heure sans installation ni abonnement obligatoire.

Vous pouvez également essayer ces didacticiels :

[Utilisation d’Azure Search dans .NET](search-howto-dotnet-sdk.md) [Prise en main d’Azure Search .NET](search-get-started-dotnet.md) [Azure Search : didacticiels, démonstrations vidéo et exemples](search-video-demo-tutorial-list.md)

<!---HONumber=Nov15_HO2-->