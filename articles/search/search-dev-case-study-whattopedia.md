<properties 
	pageTitle="Étude de cas d’un développeur Azure Search : Comment WhatToPedia.com a créé un portail infomédia sur Microsoft Azure | Microsoft Azure | Service de recherche cloud hébergé" 
	description="Apprenez à créer un portail d'information et un moteur de méta recherche à l'aide du service Azure Search, un service de recherche cloud hébergé pour les développeurs." 
	services="search, sql-database,  storage, web-sites" 
	documentationCenter="" 
	authors="HeidiSteen" 
	manager="mblythe"/>

<tags 
	ms.service="search" 
	ms.devlang="NA" 
	ms.topic="article" 
	ms.tgt_pltfrm="na" 
	ms.workload="search" 
	ms.date="02/18/2016" 
	ms.author="heidist"/>

# Étude de cas d’un développeur Azure Search

## Comment [WhatToPedia.com](http://whattopedia.com/) a créé un portail infomédia sur Microsoft Azure

 ![][6] &nbsp;&nbsp;&nbsp; <font size="9">Une idée lumineuse</font>


Notre idée consiste à créer un portail d'informations permettant à des acheteurs de contacter des revendeurs grâce à une recherche pertinente et très ciblée, semblable à celle qu'utilisent les agences de voyages pour proposer des hôtels, des restaurants et des divertissements aux clients partant pour des destinations inconnues.

Le portail que nous souhaitons créer offrira une expérience exceptionnelle pour la recherche de données sur les revendeurs d'un marché donné, et aidera les clients à trouver des magasins selon l'emplacement et les services proposés par le revendeur. Nous amorcerons le moteur de recherche avec un jeu de données initial, puis nous développerons sa valeur au fil du temps avec l'aide des revendeurs abonnés qui publient des informations relatives à leur activité. Les promotions, les nouveaux produits, les marques renommées et les services spécialisés internes sont des exemples de données qui augmentent la valeur de notre site. Ces données sont automatiquement collectées et intégrées au corpus de recherche lorsque le revendeur s'inscrit en tant qu'abonné. La publicité et le modèle d'abonnement constituent la source de revenus de notre nouvelle activité.

La recherche sera le principal modèle d'interaction avec l'utilisateur, sur une plateforme 100 % cloud. À des fins d'économie d'échelle et de réduction des coûts, presque toutes les opérations, de la mise en place du portail au contrôle de la source, s'effectueront via un service en ligne. À l'aide d'un moteur de recherche qui fournit d'emblée les fonctionnalités nécessaires, nous pouvons rapidement créer une application de recherche, sans avoir à créer et à gérer nous-mêmes un moteur de recherche.

## Ce que créons

WhatToPedia est une start-up spécialisée dans l'infomédia. Nous avons créé le site [WhatToPedia.com](http://whattopedia.com/), actuellement en test sur un marché en Europe du Nord avec une date de mise en service fixée au 2 février 2015. Notre clientèle est principalement constituée de magasins traditionnels qui ont besoin d'une présence en ligne abordable et facile à gérer.

Notre tâche consiste à attirer les clients grâce à une expérience de recherche en ligne exceptionnelle qui affiche les résultats pour une ville ou un voisinage, des marques, des noms de magasins ou des types de magasins. Attirer les clients déclenche une réaction en chaîne et incite les revendeurs à s'abonner à notre site portail. Les abonnements sont payants mais restent abordables.

 ![][7]

Après avoir souscrit un abonnement, un revendeur reçoit un profil (créé initialement par nous à partir de données achetées) et le met à jour avec des informations supplémentaires concernant les promotions, les marques proposées ou les annonces. Les services internes, par exemple les langues parlées, les devises acceptées ou les produits détaxés peuvent être automatique signalés pour attirer plus facilement les clients qui recherchent ces services.

## Qui sommes-nous ?

Je m'appelle Thomas Segato (Microsoft Consulting) et j'ai collaboré avec Jesper Boelling, développeur principal chez WhatToPedia, pour concevoir la solution.

WhatToPedia est une start-up qui teste son nouveau portail d'entreprise en Suède, marché où la plupart des 60 000 revendeurs représentent des PME traditionnelles. Le client européen parle plusieurs langues et utilise différentes devises, et nous créons donc des solutions adaptées à ce type de client multilingue. Nous avons cherché - et trouvé - un moteur de recherche qui prend en charge nos besoins multilingues : Azure Search.

Azure Search a joué un rôle capital dans notre projet. Avant de découvrir Azure Search, nous dépensions une énergétique considérable à essayer de créer notre propre moteur de recherche. L'utilisation d'Azure Search comme service en ligne a permis de lever le principal obstacle technique et administratif de notre solution, accélérant ainsi la mise sur le marché de notre produit, avec une expérience de recherche plus robuste.

## Comment nous l'avons fait

Notre objectif était de créer une infrastructure complète uniquement basée sur des services de cloud. Microsoft a été choisi comme plateforme stratégique car il s'agit d'un fournisseur offrant les services nécessaires (à la fois pour la collaboration et le développement), une mise à l'échelle à la demande et un prix abordable.
 
### Composants de haut niveau

Nous avons créé une véritable entreprise, pas simplement un site. Tous ces efforts nécessitaient une gamme complète d'outils et d'applications. Nous avons adopté Visual Studio et Visual Studio Team Services pour le développement, Team Foundation Service (TFS) Online pour le contrôle de la source et la gestion des processus Scrum, Office 365 pour la communication et la collaboration, et bien entendu Microsoft Azure pour toutes les opérations liées au site et le stockage. L'ID de Visual Studio a fourni directement les données à Azure, tandis que l'intégration à TFS Online a permis d'accroître la productivité.

Le diagramme ci-dessous montre les composants de haut niveau utilisés dans l'infrastructure WhatToPedia.

   ![][8]

### Comment nous utilisons Microsoft Azure

En examinant les zones vertes du diagramme précédent, vous verrez que la solution WhatToPedia s'appuie sur les services suivants :

- [Azure Search](https://azure.microsoft.com/services/search/)
- [Sites Web Azure avec MVC 4](https://azure.microsoft.com/services/websites/)
- [WebJobs Azure pour les tâches planifiées](../app-service-web/websites-webjobs-resources.md)
- [Base de données SQL Azure](https://azure.microsoft.com/services/sql-database/)
- [Stockage Azure BLOB](https://azure.microsoft.com/services/storage/)
- [Service de messagerie SendGrid](https://azure.microsoft.com/marketplace/partners/sendgrid/sendgrid-azure/)

Les données et la recherche constituent le cœur de la solution. Le flux de données entre le fournisseur Revendeur et le client final est illustré ci-dessous :

  ![][9]

Le stockage des données principales concerne les informations sur le revendeur et la comptabilité dans la base de données SQL Azure. Il inclut le jeu de données initial ainsi que les informations spécifiques au revendeur ajoutées au fil du temps. Nous utilisons un WebJob Azure pour intégrer les mises à jour de la base de données SQL au corpus de recherche dans Azure Search.

### Couche de présentation

Le portail est un site Web Azure, implémenté dans MVC 4 et [Twitter Bootstrap](http://en.wikipedia.org/wiki/Bootstrap_%28front-end_framework%29). Nous avons choisi MVC car il offre une approche beaucoup plus nette du format HTML que le développement basé sur des formulaires ASP.NET. Pour éviter d'avoir à créer des applications pour plusieurs appareils et à gérer plusieurs plateformes mobiles, nous avons choisi Twitter Bootstrap pour prendre en charge l'ensemble des appareils et plateformes.

### Authentification, autorisations et données sensibles

Les clients parcourent le site de façon anonyme. Par conséquent, les clients n'ont pas besoin de se connecter, et nous ne conservons aucune donnée sur le consommateur.

La situation est différente avec les revendeurs. Dans ce cas, nous stockons différentes données publiques telles que les profils, les informations de facturation et le contenu multimédia qu'ils souhaitent présenter sur le site. Chaque revendeur qui s'abonne au site reçoit un identifiant utilisateur permettant d'identifier cet utilisateur avant de mettre à jour le profil de l'abonné. Nous conservons de façon sécurisée toutes les données sur l'abonné dans la base de données SQL Azure et le système de stockage Azure BLOB. Nous avons opté pour un modèle d'authentification basé sur des formulaires .NET. Nous avons choisi cette approche pour sa simplicité ; nous n'avions pas besoin des rôles, de la prise en charge de l'interface utilisateur et d'autres fonctionnalités superflues proposés par d'autres approches.

Pour s'assurer que les revendeurs ne voient que les données qui leur appartiennent, nous leur avons attribué à chacun un ID, qui sera utilisé pour toutes les opérations de lecture et d'écriture impliquant des données spécifiques au revendeur. Avec cette approche, nous avons constaté que nous n'avions pas besoin d'accorder des autorisations de base de données aux revendeurs individuels. Tous les revendeurs interagissent avec le système en utilisant un rôle de base de données unique, l'ID revendeur étant notre technique d'isolation des données.

Comme notre activité repose essentiellement sur des opérations effectuées en aval (augmenter le chiffre d'affaires des revendeurs, créer des offres incitatives pour la publicité et les abonnements), nous n'avons pas à gérer les achats en ligne. Vous ne trouverez donc aucun panier d'achat sur notre site, ce qui réduit nos exigences de sécurité.

Nous avons également simplifié nos processus en externalisant nos opérations de facturation et de comptabilité fournisseurs. En transmettant directement les informations de paiement clients à un tiers ([SveaWebPay](http://www.sveawebpay.se/)), nous réduisons les risques liés au stockage et à la protection des données sensibles dans nos banques de données.

### Moteur de recherche

Le cœur de notre solution est le moteur de recherche reposant sur le service Azure Search. Nous avions commencé par créer un moteur de recherche personnalisé, mais au cours de ce processus, nous avons réalisé la complexité et les efforts qu'un tel projet impliquait et avons donc envisagé d'autres pistes.

Voici les fonctionnalités de base les plus importantes selon nous :

- Filtres
- Navigation à facettes
- Amélioration des résultats
- Pagination avec AJAX

Une recherche sur Internet nous a fait découvrir la vidéo suivante, qui nous a donné l'idée de tester Azure Search : [Deep Dive at TechEd Europe](http://channel9.msdn.com/events/TechEd/Europe/2014/DBI-B410)

Après avoir visionné la vidéo, nous avions tous les éléments en main pour créer un prototype. Comme nous disposions déjà d'un modèle de données dans MVC, la création du prototype était simple car les données contenaient des termes pouvant faire l'objet d'une recherche, et nous avions déjà défini nos exigences concernant le tri, la création de facettes et le filtrage des données.

Voici comment nous avons créé le prototype.

**Configuration du service Azure Search**

1. Connexion au portail Azure Classic et ajout du service de recherche à notre abonnement. Nous avons utilisé la version partagée (fournie gratuitement avec notre abonnement).
2. Création d'un index. Pour le prototype, nous avons utilisé l'interface utilisateur du portail pour définir les champs de recherche et pour créer les profils de score. Notre profil de score est basé sur des données de localisation : pays | ville | adresse (voir : Ajout de profils de score).
3. Copie de l'URL du service et de la clé api administrateur dans nos fichiers de configuration. Cette clé se trouve sur la page du service de recherche du portail, et sert à authentifier le service.
	
**Développement d'une tâche d'indexeur de recherche – Console Windows**

1. Lecture de tous les revendeurs à partir de la base de données.
2. Appel de l'API du service Azure Search pour télécharger un par un les revendeurs (voir : http://msdn.microsoft.com/library/azure/dn798930.aspx).
3. Définition dans la base de données d'une propriété indiquant que le revendeur est configuré pour l'indexation incrémentielle. Pour cela, nous avons ajouté un champ 'indexeur' qui stocke l'état de l'index de chaque profil (indexé ou non). 

Consultation de l'annexe pour connaître l'extrait de code qui constitue la tâche de l'indexeur.

**Développement d'un portail Web de recherche – MVC**

1. Appel au service Azure Search pour obtenir tous les documents de la recherche (voir : http://msdn.microsoft.com/library/azure/dn798927.aspx)
2. Extraction des éléments suivants à partir de la réponse du service de recherche (en utilisant json.net http://james.newtonking.com/json)
   - Résultats
   - Facettes
   - Nombre de résultats
   - Développement d'une interface utilisateur pour l'affichage des résultats de la recherche, des facettes et des nombres (informations déjà disponibles).

Voici le code que nous avons utilisé pour obtenir les résultats d'Azure Search :

    string requestUrl = 
    string.Format("https://{0}.search.windows.net/indexes/profiles/docs?searchMode=all&$count=true&search={1}&facet=city,count:20&facet=category&$top=10&$skip={2}&api-version=2014-07-31-Preview{3}", Config.SearchServiceName, EscapeODataString(q), skip, filter);
      var response = client.GetAsync(requestUrl).Result; //  + Uri.EscapeDataString("hennes")
      response.EnsureSuccessStatusCode();
     dynamic json = JsonConvert.DeserializeObject(response.Content.ReadAsStringAsync().Result);

**Amélioration par emplacement**

L'élément le plus important à vérifier dans le prototype a été l'ajout à la requête d'un mot clé de recherche d'emplacement. Pour notre portail, il est essentiel que si un utilisateur saisit un nom de ville dans la requête de recherche, les revendeurs de cette ville soient prioritaires par rapport aux revendeurs dont la ville figure comme mot clé ville dans la description. Pour cette exigence, nous avons utilisé un profil de score pour classer le champ Ville au-dessus des autres champs.

**Prise en charge multilingue**

Il nous fallait afficher les résultats de la recherche dans la langue correspondante, tout en permettant de rechercher les mêmes résultats dans d'autres langues. Le problème impliquait deux conditions :

- Rechercher des termes dans plusieurs langues
- Afficher les résultats de la recherche dans la langue correspondante

Nous avons résolu la question de la présentation en ajoutant un document pour chaque langue avec le texte localisé et une propriété de langue. Lorsqu'un utilisateur saisit un terme de recherche, nous utilisons les expressions `$filter` pour filtrer les résultats dans la langue choisie par l'utilisateur.

Chacun de ces documents possède une propriété masquée appelée « cities » et basée sur le type de collection. Cette propriété stocke les noms de villes dans toutes les langues, ce qui permet d'effectuer une recherche multilingue.

###Stockage des données

Toutes les données (profil, abonnement et comptabilité) sont stockées dans la base de données SQL. Tous les fichiers multimédias sont stockés dans le stockage Azure BLOB, y compris les images et vidéos fournies par le revendeur. L'utilisation d'un stockage BLOB distinct isole les effets du téléchargement de fichiers ; les fichiers ne résident jamais sur le site Web et il n'est donc pas nécessaire de reconstruire le site chaque fois que nous ajoutons des fichiers.

Autre avantage important qu'offre ce type de stockage : plusieurs développeurs peuvent partager un stockage de développement unique. L'une des exigences du projet WhatToPedia était de pouvoir créer un environnement de développement en moins de 15 minutes, y compris les vidéos, les images et les données des revendeurs. La récupération des dernières données de TFS Online, l'exécution d'un script SQL et l'exécution de la tâche d'importation permettent de créer rapidement un environnement complet. Cette méthode améliore également la mise en place d'un environnement intermédiaire.

###WebJobs

Nous utilisons Azure WebJobs pour mettre à jour les données de l'index. En créant une tâche d'indexeur de recherche, la partie indexation était très facile à intégrer à notre solution. La seule modification du code apportée à l'indexeur a été l'ajout d'un champ `Indexed` à notre modèle de données pour indiquer l'état de l'index. Lorsqu'un nouveau profil est ajouté ou mis à jour, le champ `Indexed` est défini sur false. Il en va de même si le site du revendeur change ses données de profil via le portail.

La tâche recherche toutes les lignes dont la valeur `Indexed` est définie sur false. Une fois la ligne trouvée, le document est transmis à Azure Search, puis le champ `Indexed` est défini sur true. Nous n'avions pas à choisir entre l'ajout ou la mise à jour des données car le service Azure Search se chargeait de tout. Si vous ajoutez un document déjà présent, le service effectue automatiquement la mise à jour.

Toutes les tâches web ont été développées sous forme d'applications de console qui peuvent être téléchargées sur les sites web Azure en tant que fichiers ZIP, décompressés puis planifiés.

La tâche est programmée pour s'exécuter toutes les 5 minutes comme une tâche web planifiée. Nous avons calculé que le service permet de transférer 3 000 documents en trois minutes environ, ce qui répond à nos exigences.

> [AZURE.NOTE] Un prototype de fonctionnalité d'indexeur a récemment été introduit dans Azure Search. Cette fonctionnalité a été développée trop tard pour figurer dans notre première version, mais elle semble être aussi efficace que notre tâche d'indexeur pour résoudre le même problème, en automatisant les opérations de chargement de données.


###Stratégie de sauvegarde

Nous avons conçu une stratégie de sauvegarde à plusieurs niveaux pour récupérer les données dans différents scénarios, d'une panne très grave à la récupération d'une transaction individuelle. Les actifs à protéger incluent trois types de données (site web, données des abonnés et fichiers multimédias).

Tout d'abord, en conservant le code source du site web dans TFS Online, nous savons que si le site tombe en panne, nous pouvons le recréer en le republiant à partir de TFS.

Les données des abonnés stockées dans la base de données SQL Azure sont les informations les plus sensibles. Nous les stockons à l'aide de la fonctionnalité intégrée (voir [Sauvegarde et restauration de la base de données SQL Azure](http://msdn.microsoft.com/library/azure/jj650016.aspx)). La planification de la sauvegarde consiste en une sauvegarde complète de la base de données une fois par semaine, des sauvegardes différentielles de base de données une fois par jour, et des sauvegardes du journal des transactions toutes les 5 minutes. Étant donné la taille des données, cette solution est largement suffisante pour nos volumes de données actuels et planifiés.

Troisièmement, nous stockons les fichiers image et vidéo dans le stockage Azure BLOB. Nous recherchons toujours le meilleur plan de sauvegarde pour ces données, en considérant Cloudberry Explorer for Azure comme une solution potentielle. Pour l'instant, nous utilisons un WebJob pour copier les images et les vidéos vers un autre emplacement.

##Nos enseignements

Comme nous possédions déjà les données, la validation technique n'a posé aucun problème. En quelques heures, nous avions un prototype à facettes ainsi que des compteurs, une pagination, des profils classés et des résultats de recherche. Les résultats de la recherche étaient si précis que nous avons décidé de supprimer certains des filtres proposés au client final.

La surprise plus importante pour nous a été la vitesse à laquelle nous avons assimilé Azure Search et les avantages que nous avons pu en tirer. La validation technique n'a littéralement demandé que quelques heures (voir la remarque ci-dessous), avec le remplacement de 500 lignes de code par 3 lignes de code dans l'application frontale (plus un nouveau WebJob) et de meilleurs résultats.

Auparavant, notre code instaurait une pagination, des décomptes et d'autres comportements typiques d'une recherche. Avec Azure Search, les résultats que nous obtenons incluent les résultats de la recherche, les facettes, les données de pagination et les décomptes, c'est-à-dire tous les éléments nécessaires que nous devions nous-mêmes fournir. Cette solution a également introduit une navigation à facettes intégrée améliorée, ce que ne proposait pas notre solution d'origine.

Le plus grand défi au cours de la mise en œuvre venait du fait qu'il s'agissait d'une version préliminaire et qu'il était difficile de trouver des informations et des expériences partagées. Après avoir rassemblé les pièces du puzzle, nous avons découvert que le service Azure Search était relativement simple en raison de l'API REST et du format de données JSON. Nous pouvions lancer le framework directement à partir de la plupart des plug-ins source ouverts comme JQuery JSON.Net, et utiliser des outils tels que Fiddler pour effectuer des tests rapides et le débogage.

> [AZURE.NOTE] Outre la préparation des données, le fait que les développeurs du prototype comprenaient déjà le fonctionnement de la technologie de recherche a amélioré notre productivité et notre capacité à exploiter les fonctionnalités intégrées. Si l'on considère la construction des requêtes de recherche, la navigation à facettes, les filtres, etc., on s'attendrait à ce que la phase du prototype soit plus longue.

###Contrôle des facettes sur la page de présentation de la recherche

La validation technique nous a appris qu'il était important de planifier soigneusement les facettes. Après le chargement d'une grande quantité de données dans la solution, nous avons constaté que le volume important de facettes était trop élevé pour être présenté aux utilisateurs.

Nous avons résolu le problème en limitant le paramètre spécifiant le nombre de facettes. Ce paramètre impose une limite sur le nombre de facettes renvoyées à l'utilisateur. Vous trouverez [ici](search-faceted-navigation.md) un lien vers une discussion concernant ce paramètre.

###WebJobs pour la planification des tâches

Azure Search n'a pas été notre unique agréable surprise. Nous avons découvert que l'utilisation de WebJobs pour automatiser nos opérations de chargement de données dans Azure Search était nettement supérieure à notre approche précédente, qui nécessitait une machine virtuelle dédiée exécutant un planificateur Windows, avec des tâches planifiées pour la mise à jour de l'index de recherche. WebJobs a été plus simple à configurer, plus facile à déboguer et, bien sûr, beaucoup moins cher par rapport à l'achat d'une machine virtuelle dédiée.

###Azure BLOB Storage Explorer pour la mise à jour des images

Nous avons constaté que l'utilisation d'[Azure BLOB Storage Explorer](https://azurestorageexplorer.codeplex.com/) (disponible sur codeplex) à être très utile pour la gestion des mises à jour des images et des vidéos sur le site. Nous l'utilisons comme un outil de développement pour mettre à jour manuellement les images et les vidéos qui composent notre site principal. Azure BLOB Storage Explorer offre plus de souplesse que le déploiement des modifications dans le portail et supprime une itération complète de test chaque fois que nous devons mettre à jour une image.

##Un dernier mot

Nous tenons à remercier l'extraordinaire équipe de WhatToPedia de nous avoir permis de raconter leur histoire.

Nous espérons que cette étude de cas vous a été utile. Si vous décidez d'utiliser Azure Search, je vous recommande les quelques ressources suivantes qui faciliteront votre travail :

- [Forum MSDN consacré à Azure Search](https://social.msdn.microsoft.com/forums/azure/home?forum=azuresearch)
- [StackOverflow also has a tag](http://stackoverflow.com/questions/tagged/azure-search)
- [Page de documentation sur Azure.com](https://azure.microsoft.com/documentation/services/search/)
- [Documentation Azure Search sur MSDN](http://msdn.microsoft.com/library/azure/dn798933.aspx)


##Annexe : WebJob d'indexeur de recherche

Le code suivant génère l'indexeur mentionné dans la section sur la création du prototype.

        static void Main(string[] args)
        {
            int success = 0;
            int errors = 0;

            Log.Write("Starting job","", System.Diagnostics.TraceLevel.Info);

            var serviceName = ConfigurationManager.AppSettings["SearchServiceName"];
            var serviceKey = ConfigurationManager.AppSettings["SearchServiceKey"];

            HttpClient client = new HttpClient();
            client.DefaultRequestHeaders.Add("api-key", serviceKey);

            var db = new DB(Config.ConectionString);

            var recreateIndex = false;
            Boolean.TryParse(ConfigurationManager.AppSettings["RecreateIndex"], out recreateIndex);

            if(recreateIndex)
            {
                Log.Write("Recreating index and set all to no index", "", System.Diagnostics.TraceLevel.Info);
                db.SetAllToNotIndexed();
                RecreateIndex(serviceName, client);
            }            
            
            var profiles = db.Profiles.Where(p=>!p.Indexed).ToList();

            Log.Write(string.Format("Indexing {0} profiles",profiles.Count),"", System.Diagnostics.TraceLevel.Info);

            var cities = db.Cities.ToList();
            var categories = db.Tags.Where(p=>p.ParentId==null).ToList();            

            foreach (var profile in profiles)
            {
                Log.Write(string.Format("Indexing profile {0}", profile.Name),"",profile.ProfileId,0,System.Diagnostics.TraceLevel.Verbose);

                try
                {
                    var city = cities.Where(p => p.CityId == profile.CityId);
                    var category = categories.Where(p => p.TagId == profile.CategoryId);

                    var cityse = city.Where(p => p.Lang == "se").FirstOrDefault();
                    var cityen = city.Where(p => p.Lang == "en").FirstOrDefault();
                    var categoryse = category.Where(p => p.Lang == "se").FirstOrDefault();
                    var categoryen = category.Where(p => p.Lang == "en").FirstOrDefault();

                    var citysename = cityse == null ? "" : cityse.Name;
                    var cityenname = cityen == null ? "" : cityen.Name;
                    var categorysename = categoryse == null ? "" : categoryse.Name;
                    var categoryenname = categoryen == null ? "" : categoryen.Name;

                    var tags = db.GetTagsFromProfile(profile.ProfileId);

                    var batch = new
                    {
                        value = new[] 
                    { 
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_en",
                            profileid = profile.ProfileId.ToString(),
                            city = cityenname,
                            category = categoryenname,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "en",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        },
                        new 
                        { 
                            id = profile.ProfileId.ToString()+"_se",
                            profileid = profile.ProfileId.ToString(),
                            city = citysename,
                            category = categorysename,
                            address = profile.Adress1,
                            email = profile.Email,
                            name = profile.Name,
                            lang = "se",
                            brands = profile.Brands,
                            descen=profile.DescEn,
                            descse=profile.DescSe,
                            orgnumber=profile.OrgNumber,
                            phone=profile.Phone,
                            zip=profile.Zip,
                            cities = city.Select(p=>p.Name).ToArray(),
                            categories = category.Select(p=>p.Name).ToArray(),
                            cityid = profile.CityId.ToString(),
                            tags=tags.ToArray()
                        }
                    },
                    };

                    var response = client.PostAsync("https://" + serviceName + ".search.windows.net/indexes/profiles/docs/index?api-version=2014-10-20-Preview", new StringContent(JsonConvert.SerializeObject(batch), Encoding.UTF8, "application/json")).Result;
                    response.EnsureSuccessStatusCode();

                    db.Entry(profile).State = System.Data.Entity.EntityState.Modified;
                    profile.Indexed = true;
                    db.SaveChanges();
                    success++;
                }
                catch(Exception ex)
                {
                    Log.Write("Error indexing profile", ex.Message, profile.ProfileId, 0, System.Diagnostics.TraceLevel.Verbose);
                    errors++;
                }
            }
            if(errors > 0)
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Error);
            }
            else
            {
                Log.Write(string.Format("Job ended success ({0}), errors ({1})", success, errors), "", System.Diagnostics.TraceLevel.Info);
            }
            
        }

        static void RecreateIndex( string ServiceName, HttpClient client)
        {
            var index = new
            {
                name = "profiles",
                fields = new[] 
                { 
                    new { name = "id",              type = "Edm.String",         key = true,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "profileid",       type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "cityid",          type = "Edm.String",         key = false,  searchable = false, filterable = false, sortable = false, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "city",            type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = true,  facetable = true, retrievable = true,  suggestions = true  },
                    new { name = "category",        type = "Edm.String",         key = false, searchable = true,  filterable = true, sortable = false, facetable = true, retrievable = true,  suggestions = false  },
                    new { name = "address",         type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "email",           type = "Edm.String",         key = false, searchable = true,  filterable = false, sortable = true, facetable = false, retrievable = true,  suggestions = false },
                    new { name = "name",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true, suggestions = true },
                    new { name = "lang",            type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = false,  facetable = false,  retrievable = true, suggestions = false },
                    new { name = "brands",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descen",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "descse",          type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "orgnumber",       type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "phone",           type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "zip",             type = "Edm.String",         key = false, searchable = true,  filterable = true,  sortable = true,  facetable = false,  retrievable = true,  suggestions = false },
                    new { name = "cities",          type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                   new { name = "categories",      type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false },
                    new { name = "tags",            type = "Collection(Edm.String)",         key = false, searchable = true,  filterable = false,  sortable = false,  facetable = false,  retrievable = false, suggestions = false }
                    
                }
            };

            var url = "https://" + ServiceName + ".search.windows.net/indexes/?api-version=2014-10-20-Preview";

            var deleteUrl = "https://" + ServiceName + ".search.windows.net/indexes/profiles?api-version=2014-10-20-Preview";

            try
            {
                var deleteResponseIndex = client.DeleteAsync(deleteUrl).Result;
                deleteResponseIndex.EnsureSuccessStatusCode();
            }
            catch (Exception ex)
            {

            }

            var responseIndex = client.PostAsync(url, new StringContent(JsonConvert.SerializeObject(index), Encoding.UTF8, "application/json")).Result;
            responseIndex.EnsureSuccessStatusCode();            
          


<!--Anchors-->
[Subheading 1]: #subheading-1
[Subheading 2]: #subheading-2
[Subheading 3]: #subheading-3
[Subheading 4]: #subheading-4
[Subheading 5]: #subheading-5
[Next steps]: #next-steps

<!--Image references-->
[6]: ./media/search-dev-case-study-whattopedia/lightbulb.png
[7]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Search-Bageri.png
[8]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Stack.png
[9]: ./media/search-dev-case-study-whattopedia/WhatToPedia-Archicture.png


<!--Link references-->
[Link 1 to another azure.microsoft.com documentation topic]: ../virtual-machines-windows-tutorial.md
[Link 2 to another azure.microsoft.com documentation topic]: ../web-sites-custom-domain-name.md
[Link 3 to another azure.microsoft.com documentation topic]: ../storage-whatis-account.md
 

<!---HONumber=AcomDC_0224_2016-->