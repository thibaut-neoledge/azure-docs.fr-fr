<properties
	pageTitle="Flux de travail typique pour le développement de Azure Search | Microsoft Azure"
	description="Un flux de travail ou une feuille de route pour créer un prototype et des applications de production intégrant Azure Search"
	services="search"
	documentationCenter=""
	authors="HeidiSteen"
	manager="mblythe"
	editor=""/>

<tags
	ms.service="search"
	ms.devlang="rest-api"
	ms.workload="search"
	ms.topic="get-started-article" 
	ms.tgt_pltfrm="na"
	ms.date="07/08/2015"
	ms.author="heidist"/>

# Flux de travail typique de développement Azure Search

Cet article propose une feuille de route pour inclure Azure Search en tant que composant de recherche dans votre application personnalisée Qu’il s’agisse de tests ou d’une installation définitive, ces recommandations sont destinées à vous aider dans l’intégration d’Azure Search dans votre projet de développement personnalisé.

Les sections suivantes décrivent un flux de travail typique pour la création d’un prototype initial qui vous aidera à évaluer dans quelle mesure Azure Search répond aux prérequis de votre application en termes d’outil de recherche. La seconde partie de cet article couvre les décisions de conception importantes dans le cadre d’une initiative de développement d’application plus poussée.

Avant de commencer à créer un prototype, nous vous recommandons de visualiser un de nos didacticiels de prise en main ou cette [présentation vidéo approfondie d’une heure](http://azure.microsoft.com/documentation/videos/tech-ed-europe-2014-azure-search-deep-dive/). Les didacticiels de prise en main sont proposés en langages [.NET](search-get-started-dotnet.md), [Java](search-get-started-java.md) et [Node.JS](search-get-started-nodejs.md).

##Développement du prototype

Les instructions suivantes indiquent comment créer rapidement un prototype réussi. Elles incluent la configuration d’un service, la définition d’un schéma pour votre index, le chargement de documents dans l’index et la fonction d’interrogation de l’index.

Pour les applications avec données volatiles (comme, par exemple, en cas de changements rapides de l’inventaire ou du contenu), votre prototype doit également inclure un composant de mise à jour des documents.

   ![][1]

###Étape 1 : Configuration du service

Azure Search est un service en ligne entièrement géré disponible via un abonnement Azure. [Une fois inscrit sur Azure](http://azure.microsoft.com/pricing/free-trial/), l’ajout du service de recherche est très rapide. Consultez [Créer un service de recherche sur le portail](search-create-service-portal.md) pour des instructions sur l’ajout d’un service de recherche à votre abonnement.

Deux niveaux de tarification sont disponibles. Nous vous recommandons d’utiliser le service partagé (gratuit) pour créer votre prototype, le seul inconvénient étant que vous ne pourrez travailler qu’avec un sous-ensemble limité de vos données. Le service partagé est gratuit pour les abonnés (par le biais de la version d’essai ou d’un abonnement normal) et rapide à installer. Le nombre d’index et de documents que vous pouvez utiliser est toutefois limité à 3 index ou 10 000 documents par index ou 50 Mo de stockage total, quelle que soit la première limite atteinte.

###Étape 2 : Création de l'index

Une fois le service créé, vous êtes prêt à créer l’index, en commençant par la définition de son schéma.

Le portail vous permet de créer rapidement et facilement un index. Chaque document doit disposer au minimum d’une clé unique et d’un champ contenant des données pouvant faire l’objet d’une recherche. Pour commencer, consultez [Création d’un index sur le portail](search-create-index-portal.md).

> [AZURE.NOTE]**À l’intérieur d’un index Azure Search**
>
> Un *index* regroupe des données organisées et persistantes qui servent de *corpus de recherche* pour toutes les opérations de recherche ultérieures. Votre corpus de recherche est stocké dans le cloud dans le cadre de votre abonnement au service de recherche, qui permet l’exécution rapide et régulière d’opérations de recherche. Dans la terminologie de recherche, un élément de votre corpus de recherche est appelé un *document*, et la somme totale de tous les documents est la *collection de documents*.
>
>Un *schéma d'index* définit tous les champs dans un document par nom, type de données et attributs qui spécifient si le champ peut faire l’objet de recherches, de filtrage, de facettes, et ainsi de suite.
>
> Outre la structure du document, un schéma d’index spécifie également les profils de score, qui fournissent les critères pour améliorer un score de recherche, et les paramètres de configuration qui activent les requêtes prédictives (générateur de suggestions) et CORS pour les requêtes interdomaines. **Pour les prototypes, nous vous recommandons de commencer simplement en spécifiant les champs d’un document**, puis d’ajouter les autres fonctionnalités de façon incrémentielle (voir l’étape 5 pour une liste de fonctionnalités supplémentaires à ajouter ultérieurement).
>
> Pour prendre un exemple concret, considérez une application de commerce électronique. L’index de recherche devrait contenir tous les produits ou services qui peuvent faire l’objet d’une recherche dans votre application (tout ce qui est renvoyé dans les résultats de la recherche). À chaque référence (SKU) devrait correspondre un document. Chaque document devrait inclure le nom du produit, la marque, les tailles, le prix, les coloris, et même des références aux images ou autres fichiers de ressources que vous souhaitez voir apparaître dans les résultats de recherche.

###Étape 3 : Chargement de documents

Après avoir enregistré l’index dans Azure Search, vous pouvez remplir l’index avec les documents. Au cours de cette étape, les données sont téléchargées, tokénisées et stockées dans les structures de données (comme les index inversés) conçues pour les charges de travail de recherche.

Les données téléchargées doivent correspondre au schéma que vous avez défini à l’étape précédente. Les données de document sont représentées sous la forme d'un ensemble de paires clé/valeur pour chaque champ, au format JSON. Si votre schéma spécifie un champ identifiant (clé), un champ nom, un champ numéro et un champ URL (ce que nous vous recommandons si des images externes font partie de vos résultats de recherche), tous les documents que vous ajoutez dans l’index doivent avoir une valeur (ou null) pour chaque champ.

Il existe plusieurs manières de charger des documents, mais toutes ne requièrent pas actuellement une API. Pour la plupart des prototypes, cette étape est celle qui peut prendre le plus de temps en raison d’une exigence de codage. Les options sont décrites ci-dessous.

> [AZURE.NOTE]N’oubliez pas qu’en utilisant le service partagé, vous êtes limité à 10 000 documents par index. Veillez à réduire votre ensemble de données afin de respecter cette limite. Pour plus d'informations, consultez [Limites et contraintes](https://msdn.microsoft.com/library/dn798934.aspx).

####Comment charger des données dans un index

Une approche consiste à utiliser un indexeur. Pour Azure DocumentDB ou les sources de données relationnelles SQL Server dans Azure (notamment la base de données Azure SQL ou SQL Server dans Azure VM), vous pouvez utiliser les [indexeurs](https://msdn.microsoft.com/library/dn946891.aspx) pour extraire des documents à partir d’une source de données prise en charge. Des exemples de code qui utilisent des indexeurs pour le chargement de documents sont indiqués dans ces didacticiels de prise en main : [.NET](search-get-started-dotnet.md), [Java](search-get-started-java.md) et [Node.JS](search-get-started-nodejs.md).

Une seconde option consiste à écrire un programme simple à l'aide de l'API REST ou de la bibliothèque .NET qui charge les documents :

- [Ajout, mise à jour ou suppression de documents (API REST)](https://msdn.microsoft.com/library/dn798930.aspx)
- [Classe DocumentOperationsExtensions](https://msdn.microsoft.com/library/microsoft.azure.search.documentoperationsextensions.aspx)

Une troisième option efficace pour les ensembles de données très réduits consiste à télécharger les documents à l’aide de [Fiddler](search-fiddler.md) ou [Chrome Postman](search-chrome-postman.md).

Une quatrième option, peut-être la plus simple, consiste à emprunter le code à partir de l’[exemple d’API REST C# Adventure Works](https://azuresearchadventureworksdemo.codeplex.com/) qui charge les documents à partir d'une base de données (.mdf) intégrée dans la solution, ou l’[exemple d’API REST C# Scoring Profiles](https://azuresearchscoringprofiles.codeplex.com/) qui charge des données à partir de fichiers de données JSON inclus dans la solution.

> [AZURE.TIP]Vous pouvez modifier et exécuter l’[exemple de profils de scores](https://azuresearchscoringprofiles.codeplex.com/), en remplaçant les fichiers de données JSON et le fichier schema.json avec les données qui sont valides pour votre application.

###Étape 4 : Documents de requête

Une fois les documents chargés dans l’index, vous pouvez écrire votre première requête.

Le moyen le plus rapide pour obtenir des résultats de recherche initiaux à l’aide de votre service de recherche consiste à utiliser [Fiddler](search-fiddler.md) ou [Chrome Postman](search-chrome-postman.md) pour afficher une réponse, mais vous souhaiterez probablement écrire un code simple d'interface utilisateur pour afficher les résultats dans un format lisible.

Les API pour les opérations de recherche incluent :

- [Opération de recherche de documents](https://msdn.microsoft.com/library/dn798927.aspx)
- [Classe SearchIndexClient](https://msdn.microsoft.com/library/microsoft.azure.search.searchindexclient.aspx)

Les requêtes de recherche dans Azure Search peuvent être très simples. Inclure `search=*` sur l'URI renvoie les 50 premiers éléments de votre corpus de recherche ; spécifier `search=<some phrase>` lance une recherche de texte intégral sur l'expression et renvoie jusqu'à 50 documents, en supposant qu'il existe au moins 50 documents qui contiennent une correspondance sur le terme d'entrée.

La valeur par défaut est 50 documents. Vous pouvez modifier le nombre d’éléments renvoyés à l’aide du paramètre de requête `$Count`. Ce paramètre est documenté dans [Rechercher des Documents](https://msdn.microsoft.com/library/dn798927.aspx).

> [AZURE.TIP]Vous trouverez une liste des exemples de requête plus complète dans [Rechercher des Documents](https://msdn.microsoft.com/library/dn798927.aspx), mais vous pouvez également consulter l’article [Référence de syntaxe](https://msdn.microsoft.com/library/dn798920.aspx) pour passer en revue la liste des opérateurs pris en charge.

###Étape 5 : Explorer davantage de fonctionnalités

Maintenant que vous disposez d’un service et d’un index, vous pouvez tester des fonctionnalités supplémentaires pour améliorer l'expérience de recherche. Une courte liste de fonctionnalités à étudier est décrite ci-dessous.

Les **pages de recherche** incluent souvent le nombre de documents dans un ensemble de résultats, ou utilisent la pagination pour diviser les résultats en nombres plus faciles à gérer. Consultez [Pagination](search-pagination-page-layout.md) pour plus d'informations.

**searchMode=all** est un paramètre de requête qui modifie la façon dont Azure Search évalue l'opérateur NOT. Par défaut, les requêtes qui incluent NOT (-) ne réduisent pas le nombre de résultats mais l’augmentent. Vous pouvez définir ce paramètre pour modifier la façon dont l’opérateur est évalué. Cette opération est documentée dans [Rechercher des Documents](https://msdn.microsoft.com/library/dn798927.aspx) ou [Énumération SearchMode](https://msdn.microsoft.com/library/microsoft.azure.search.models.searchmode.aspx).

Les **profils de score** sont utilisés pour améliorer les scores de recherche, afin que les éléments qui répondent aux critères prédéfinis apparaissent dans les premiers résultats de recherche. Consultez [Prise en main des profils de score](search-get-started-scoring-profiles.md) pour accéder à cette fonctionnalité.

Les **filtres** sont utilisés pour limiter les résultats de la recherche en fournissant des critères supplémentaires sur la sélection. Les expressions de filtre sont placées dans la requête. Consultez [Rechercher des documents](https://msdn.microsoft.com/library/dn798927.aspx) pour plus d'informations.

La **navigation à facettes** est utilisée pour le filtrage autonome. Azure Search génère et renvoie la structure, et votre code restitue la structure de navigation à facettes dans une page de résultats. Consultez [Navigation à facettes](search-faceted-navigation.md) pour plus d'informations.

Les **générateurs de suggestion** font référence aux requêtes prédictives ou avec saisie semi-automatique qui renvoient des suggestions de termes à mesure l'utilisateur saisit les premières lettres d'une expression de recherche. Consultez [Opération de suggestion](https://msdn.microsoft.com/library/dn798936.aspx) ou [Classe de générateurs de suggestions](https://msdn.microsoft.com/library/microsoft.azure.search.models.suggester.aspx) pour plus d'informations.

Les **analyseurs de langage** fournissent les règles linguistiques utilisées pendant l'analyse de texte. L'analyseur de langage par défaut pour Azure Search est Lucene English, mais vous pouvez utiliser un autre ou même plusieurs analyseurs en les spécifiant dans votre index. Les analyseurs Lucene sont disponibles dans toutes les API. Les processeurs de langage naturel de Microsoft sont disponibles uniquement dans l’[API REST 2015-02-28-Preview](search-api-2015-02-28-preview.md). Consultez [Support multilingue](https://msdn.microsoft.com/library/dn879793.aspx) pour plus d'informations.

###Étape 6 : Mise à jour d'index et de documents

Certaines des fonctionnalités que vous souhaitez évaluer peuvent nécessiter une mise à jour de votre index, ce qui a souvent pour conséquence de devoir mettre à jour vos documents.

Si vous devez mettre à jour un index ou des documents, par exemple pour ajouter un générateur de suggestions ou spécifier des analyseurs de langue sur les champs que vous avez ajoutés à cet effet, consultez les liens suivants pour des instructions :

- [Mise à jour d'un index (API REST)](https://msdn.microsoft.com/library/dn800964.aspx)
- [Mise à jour d'un indexeur (API REST)](https://msdn.microsoft.com/library/dn946892.aspx)
- [Ajout, mise à jour ou suppression d’opération de documents (API REST)](https://msdn.microsoft.com/library/dn798930.aspx)
- [Classe d’index (bibliothèque .NET)](https://msdn.microsoft.com/library/microsoft.azure.search.models.index.aspx)
- [Classe de documents (bibliothèque .NET)](https://msdn.microsoft.com/library/microsoft.azure.search.models.document.aspx)

Une fois que vous avez créé un prototype qui établit la validation technique, vous pouvez passer au niveau supérieur en créant un projet de développement qui prend en charge les charges de travail de production.

##Développement d’applications

Pour passer à la phase suivante, vous devez décider quelles API utiliser, comment gérer les documents et la fréquence des téléchargements, et s’il faut inclure des ressources externes dans vos résultats de recherche.

La conception de votre solution doit toujours inclure toutes les étapes décrites pour les prototypes, mais au lieu de privilégier la méthode la plus rapide, nous vous recommandons de considérer les approches les plus compatibles avec votre solution globale.

###Choix d’une API

Azure Search fournit deux modèles de programmation : la bibliothèque .NET pour le code géré et une API REST pour les langages de programmation tels que Java, JavaScript ou un autre langage qui ne cible pas Microsoft .NET Framework.

Actuellement, un petit sous-ensemble de fonctionnalités n’existe pas encore dans la bibliothèque .NET. Même si vous préférez écrire du code géré, vous devrez donc peut-être utiliser l'API REST pour obtenir les fonctionnalités souhaitées. Les fonctionnalités qui sont uniquement disponibles dans l'API REST incluent :

- [Processeurs de langage naturel Microsoft - aperçu uniquement](../search-api-2015-02-28-preview/)
- [Fonctionnalité moreLikeThis - aperçu uniquement](../search-api-2015-02-28-preview/)
- [API de gestion](https://msdn.microsoft.com/library/dn832684.aspx)

Vous pouvez consulter régulièrement l’article [Nouveautés](search-latest-updates.md) pour surveiller les changements de statut des fonctionnalités.

###Détermination des méthodes de synchronisation des données : émission (Push) ou extraction (Pull)

Les modèles d’émission et d’extraction font référence à la façon dont les documents sont mis à jour dans l’index. Le scénario détermine souvent le modèle qui vous convient.

Si votre entreprise a une activité de commerce en ligne, vous aurez probablement besoin d’un modèle d’émission afin de pouvoir transmettre ou copier toute modification de l’inventaire dans votre base de données OLTP et votre index Azure Search. Lorsqu'une référence (SKU) spécifique arrive à expiration, ou qu’une taille ou un coloris devient indisponible, vous pouvez mettre à jour l'index aussi rapidement que possible pour éviter que vos clients ne soient frustrés. Seuls les modèles d’émission peuvent fournir des mises à jour presque en temps réel de votre index de recherche.

Azure Search n’inclut aucun mécanisme spécifique pour l'implémentation d'un modèle d’émission. Le code de votre application, dans la couche de données, doit gérer l'opération de mise à jour des documents à l'aide de l’[API REST](https://msdn.microsoft.com/library/dn798935.aspx) ou de la [bibliothèque .NET](https://msdn.microsoft.com/library/dn951165.aspx) pour mettre à jour des documents dans la collection. En tant que détail d'implémentation, l’utilisation d’une référence (SKU) de produit pour la clé de document peut vous aider.

Les modèles d'extraction sont généralement des opérations planifiées qui extraient des données à partir de sources de données externes. Dans Azure Search, un modèle d'extraction est disponible via des [indexeurs](https://msdn.microsoft.com/library/azure/dn946891.aspx), qui sont à leur tour disponibles pour les sources de données spécifiques : Azure DocumentDB ou base de données SQL Azure (et SQL Server sur des machines virtuelles Azure).

###Chargement de documents par lots

Il est conseillé d'ajouter des documents par lots afin d'améliorer le débit. Vous pouvez créer des lots contenant jusqu'à 1 000 documents, en supposant que leur taille moyenne est d'environ 1 à 2 Ko.

Il existe un code d'état global pour la demande POST. Les codes d'état sont soit HTTP 200 (Réussite) soit HTTP 207 (Multi-état) s'il y a un mélange de documents ayant abouti et d'autres ayant échoué. Outre le code d'état de la demande POST, Azure Search gère un champ d'état pour chaque document. Dans le cas d'un téléchargement par lots, vous devez disposer d'une méthode permettant d'obtenir un état qui indique, pour chaque document, si l'insertion s'est déroulée correctement ou a échoué. Le champ d'état fournit cette information. La valeur est définie sur false si le chargement du document a échoué.

Dans des conditions de surcharge, les échecs de téléchargement ne sont pas rares. Si cela se produit, le code d'état global est 207, ce qui indique une réussite partielle, et la propriété Status des documents dont l'indexation a échoué est définie sur false.

> [AZURE.NOTE]Lorsque le service reçoit des documents, ceux-ci sont placés en file d'attente en vue de l'indexation. Il se peut qu'ils ne soient pas inclus immédiatement dans les résultats de la recherche. Dans des conditions de charge normales, l'indexation des documents prend généralement quelques secondes.

Lors de la mise à jour d'un index, vous pouvez combiner plusieurs actions (insertion, fusion, suppression) au sein d'un même lot, éliminant ainsi les allers-retours. Pour l'heure, Azure Search ne prend pas en charge les mises à jour partielles (HTTP PATCH). Si vous devez mettre à jour un index, vous devez donc renvoyer sa définition.

###Intégration de données externes dans les résultats de recherche

Azure Search utilise le stockage interne pour les index et les documents utilisés dans les opérations de recherche. L'analyse de texte et de l'index dépend de la disponibilité rapide de tous les champs pouvant faire l'objet d'une recherche et des attributs associés.

Toutefois, tous les champs d'un document ne peuvent pas faire l'objet d'une recherche. Par exemple, si votre application est un catalogue en ligne de musique ou de vidéos, il est conseillé de stocker les fichiers binaires dans Azure BLOB service ou sous une autre forme de stockage. Les fichiers binaires ne pouvant pas faire l'objet d'une recherche, il n'est pas nécessaire de les conserver dans l'espace de stockage Azure Search. Même si vous devez stocker des images, des vidéos et des fichiers audio dans d'autres services ou emplacements, vous devez inclure un champ indiquant l'URL de l'emplacement du fichier. De cette façon, vous pouvez renvoyer les données externes dans le cadre de vos résultats de recherche.

Pour utiliser des données externes, vous devez définir un champ dans l'index qui stocke un pointeur d'URL vers le fichier de données externe. Si vous exécutez une requête de [Recherche de documents](https://msdn.microsoft.com/library/dn798929.aspx) ou incluez le champ dans les résultats de recherche, le fichier binaire apparaît dans le contexte d'un document.

###Planification de la capacité

L’une des fonctionnalités Azure Search les plus intéressantes est la facilité avec laquelle vous pouvez augmenter ou réduire les ressources selon la demande. Si cette fonctionnalité n'élimine pas le besoin de planifier la capacité, elle réduit la plupart des risques. Vous n'êtes pas tenu d’utiliser du matériel supplémentaire ou du matériel inadéquat pour exécuter vos charges de travail de recherche.

Au cours de la dernière étape, passez en revue les niveaux de ressource existants pour les réplicas et les partitions, et pour déterminer si des ajustements sont nécessaires. Le moyen le plus simple d'ajuster la capacité est sur le [portail de gestion Azure](https://ms.portal.azure.com/).

N’oubliez pas que seul le niveau de tarification standard peut être augmenté ou diminué. En outre, selon le degré d'ajustement, le déploiement de clusters supplémentaires pour votre service peut prendre de quelques minutes à plusieurs heures.

> [AZURE.NOTE]La capacité peut être ajustée par la programmation à l'aide de l'API REST de gestion. Pour plus d'informations, consultez [API REST de gestion](https://msdn.microsoft.com/library/azure/dn832684.aspx).


<!--Image references-->
[1]: ./media/search-workflow/AzSearch-Workflow.png
 

<!---HONumber=August15_HO6-->