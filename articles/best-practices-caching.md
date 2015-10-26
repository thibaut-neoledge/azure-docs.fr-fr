<properties
   pageTitle="Recommandations en matière de cache | Microsoft Azure"
   description="Conseils sur la mise en cache pour améliorer les performances et l'extensibilité."
   services=""
   documentationCenter="na"
   authors="dragon119"
   manager="masimms"
   editor=""
   tags=""/>

<tags
   ms.service="best-practice"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="04/28/2015"
   ms.author="masashin"/>

# Recommandations en matière de cache

![](media/best-practices-caching/pnp-logo.png)

La mise en cache est une technique courante qui vise à améliorer les performances et l'extensibilité d'un système en copiant temporairement les données fréquemment consultées dans un stockage rapide situé près de l'application. Si ce système de stockage rapide des données se trouve plus près de l'application que la source d'origine, la mise en cache peut améliorer considérablement les temps de réponse des applications clientes en fournissant des données plus rapidement. La mise en cache est plus efficace lorsqu'une instance cliente lit à plusieurs reprises les mêmes données, surtout si les données restent relativement statiques et que le magasin de données d'origine est lent par rapport à la vitesse du cache, est soumis à un niveau élevé de contention ou est situé loin lorsque le temps de réponse du réseau peut ralentir l'accès.

## Mise en cache dans les applications distribuées

Les applications distribuées implémentent généralement une des deux stratégies suivantes ou les deux lors de la mise en cache des données :

- Utilisation d'un cache privé, où les données sont stockées localement sur l'ordinateur exécutant une instance d'une application ou d’un service.
- Utilisation d'un cache partagé, agissant comme une source commune accessible par plusieurs ordinateurs et/ou processus.

Dans les deux cas, la mise en cache peut être effectuée côté client (par le processus fournissant l'interface utilisateur d’un système, comme une application de bureau ou un navigateur web) et/ou côté serveur (par le processus fournissant les services d'entreprise exécutés à distance).

### Mise en cache privée

Le type de cache le plus simple est un magasin en mémoire, contenu dans l'espace d'adressage d'un processus unique et accessible directement par le code qui s'exécute dans ce processus. Ce type de cache est accessible très rapidement. Il peut fournir une stratégie extrêmement efficace pour le stockage de faibles volumes de données statiques, car la taille d'un cache est généralement limitée par le volume de mémoire disponible sur l'ordinateur qui héberge le processus. Si vous avez besoin de mettre en cache davantage d’informations que ce qui est physiquement possible en mémoire, vous pouvez écrire les données mises en cache dans le système de fichiers local. L’accès sera nécessairement plus lent que celui des données conservées en mémoire, mais il sera toujours plus rapide et plus fiable que la récupération de données sur un réseau.

Si plusieurs instances d'une application utilisant ce modèle s'exécutent simultanément, chaque instance d'application a son propre cache indépendant contenant sa propre copie des données.

Vous devez considérer le cache comme un instantané des données d'origine à un moment donné dans le passé. Si ces données ne sont pas statiques, il est probable que différentes instances d'application contiendront différentes versions des données dans leurs caches. Par conséquent, la même requête effectuée par ces instances peut renvoyer des résultats différents, comme illustré dans la Figure 1.

![Utilisation d'un cache en mémoire dans différentes instances d'une application](media/best-practices-caching/Figure1.png)

_Figure 1 : Utilisation d'un cache en mémoire dans différentes instances d'une application_

### Shared Caching

L’utilisation d'un cache partagé peut aider à atténuer le problème résultant du fait que les données peuvent différer dans chaque cache, comme cela peut se produire avec la mise en cache en mémoire. La mise en cache partagé garantit que différentes instances d'application ont la même vue des données en cache en incluant le cache dans un emplacement distinct, généralement hébergé dans le cadre d'un service distinct, comme illustré à la Figure 2.

![Utilisation d'un cache partagé](media/best-practices-caching/Figure2.png)

_Figure 2 : Utilisation d'un cache partagé_

Un avantage important de l'utilisation de la mise en cache partagé est l'extensibilité qu'elle peut fournir. De nombreux services de cache partagé sont implémentés à l'aide d'un cluster de serveurs et utilisent des logiciels qui distribuent les données dans le cluster de manière transparente. Une instance d'application envoie simplement une demande au service de cache et l'infrastructure sous-jacente est chargée de déterminer l'emplacement des données mises en cache dans le cluster. Vous pouvez facilement faire évoluer le cache en ajoutant des serveurs.

Les inconvénients de la mise en cache partagé sont que l’accès au cache est plus lent, car il n'est plus conservé localement dans chaque instance d'application. En outre, la nécessité d'implémenter un service de cache distinct peut ajouter de la complexité à la solution.

## Considérations sur l’utilisation de la mise en cache

Les sections suivantes décrivent plus en détail les éléments à prendre en compte pour la conception et l’utilisation d'un cache.

### Quand mettre cache en cache les données ?

La mise en cache peut améliorer considérablement les performances, l'extensibilité et la disponibilité. Plus le volume de données est important, plus le nombre d'utilisateurs devant accéder à ces données augmente, plus la mise en cache devient avantageuse. Elle réduit en effet la latence et la contention associées à la gestion d'importants volumes de demandes simultanées dans le magasin de données d'origine. Par exemple, une base de données peut prendre en charge un nombre limité de connexions simultanées, mais la récupération des données à partir d'un cache partagé plutôt que depuis la base de données sous-jacente permet à une application cliente d’accéder à ces données, même si le nombre de connexions disponibles est actuellement épuisé. En outre, si la base de données devient indisponible, les applications clientes peuvent continuer en utilisant les données mises en cache.

Vous devez envisager la mise en cache des données qui sont lues fréquemment, mais rarement modifiées (les données ont une plus grande proportion d’opérations de lecture que d’opérations d'écriture). Toutefois, vous ne devez pas utiliser le cache comme magasin faisant autorité constitué d’informations critiques. Vous devez vous assurer que toutes les modifications que votre application ne peut pas se permettre de perdre sont toujours enregistrées dans un magasin de données persistantes. De cette façon, si le cache n'est pas disponible, votre application peut continuer à fonctionner en faisant appel au magasin de données et vous ne perdez pas d’informations importantes.

### Types de données et de stratégies d’alimentation du cache

L’important lors de l’utilisation du cache est effectivement de déterminer les données les plus appropriées à mettre en cache et l’heure la plus adaptée pour cette opération. Les données peuvent être ajoutées au cache à la demande lorsqu’elles sont récupérées pour la première fois par une application, afin que les besoins de l'application analysent les données une seule fois à partir du magasin de données et que les accès ultérieurs soient satisfaits en utilisant le cache.

Ou bien, un cache peut être partiellement ou entièrement rempli de données à l'avance, généralement au démarrage de l'application (cette approche est appelée essaimage). Toutefois, il peut être déconseillé d'implémenter l'essaimage d'un cache volumineux, car cette approche peut imposer une charge élevée et soudaine sur le magasin de données d'origine lorsque l’exécution de l'application commence.

Souvent, une analyse des modèles d'utilisation peut aider à décider s'il faut entièrement ou partiellement préremplir un cache et les données qui doivent être mises en cache. Par exemple, il serait probablement utile d'alimenter le cache avec les données de profil utilisateur statique pour les clients qui utilisent régulièrement l'application (par exemple tous les jours), mais pas pour les clients qui utilisent l'application uniquement une seule fois par semaine.

En général, la mise en cache fonctionne bien avec les données non modifiables ou qui changent rarement. En voici quelques exemples : informations de référence telles que le produit et le prix dans une application de commerce électronique ou des ressources statiques partagées qui sont coûteuses à construire. Une partie ou la totalité des données peut être chargée dans le cache au démarrage de l'application afin de réduire la sollicitation des ressources et d'améliorer les performances. Il peut également être approprié d'avoir un processus en arrière-plan qui met à jour régulièrement les données de référence dans le cache pour vérifier qu'elle sont à jour ou qui actualise le cache lorsque les données de référence changent.

La mise en cache peut être moins utile pour les données dynamiques, bien qu'il existe quelques exceptions à prendre en compte (voir la section Mise en cache des données hautement dynamiques plus loin dans ce guide pour plus d'informations). Lorsque les données d’origine changent régulièrement, soit les informations mises en cache peuvent devenir obsolètes très rapidement, soit la surcharge résultant du maintien de la synchronisation du cache avec le magasin de données d'origine réduit l'efficacité de la mise en cache. Notez qu’un cache n'inclut pas nécessairement les données complètes d’une entité. Par exemple, si un élément de données représente un objet comme le client d’une banque avec le nom, l'adresse et le solde de compte associés, certains de ces éléments peuvent rester statiques (nom et adresse), tandis que d'autres (par exemple, le solde du compte) peuvent être plus dynamiques. Dans ces situations, il peut être utile de mettre en cache les parties statiques des données et de récupérer (ou de calculer) uniquement les informations restantes lorsque cela est nécessaire.

Le test des performances et l’analyse de l'utilisation doivent être effectués pour déterminer si le préremplissage et/ou le chargement à la demande du cache sont appropriés. La décision doit être basée sur une combinaison entre la volatilité et le modèle d'utilisation des données. L’utilisation du cache et l’analyse des performances sont particulièrement importantes dans les applications qui rencontrent de lourdes charges de travail et doivent être hautement évolutives. Par exemple, dans les scénarios hautement évolutifs, il peut être judicieux d'amorcer le cache pour réduire la charge sur le magasin de données aux heures de pointe.

La mise en cache évite également de répéter des calculs lorsque l'application s'exécute. Si une opération transforme les données ou effectue un calcul complexe, elle peut enregistrer les résultats de l'opération dans le cache. Si le même calcul est requis par la suite, l'application peut extraire les résultats du cache.

Une application peut modifier des données conservées dans un cache, mais vous devez considérer le cache comme un magasin de données temporaire qui peut disparaître à tout moment. Ne stockez pas de données importantes dans le cache uniquement, mais assurez-vous de conserver les informations contenues dans le magasin de données d'origine. De cette façon, si le cache devient indisponible, vous réduisez le risque de perte de données.

### Mise en cache de données hautement dynamiques

Le stockage des informations qui changent rapidement dans un magasin de données permanentes peut imposer une surcharge au système. Par exemple, prenons un appareil qui signale continuellement un état ou tout autre mesure. Si une application choisit de ne pas mettre en cache ces données sur la base que les informations en cache seront presque toujours obsolètes, cela peut être vrai également lors du stockage et de la récupération d'informations à partir du magasin de données. En effet, pendant le délai nécessaire à l’enregistrement et à l’analyse de ces données, des dernières peuvent avoir changé. Dans un cas comme celui-ci, tenez compte des avantages du stockage des informations dynamiques directement dans le cache plutôt que dans le magasin de données permanentes. Si les données ne sont pas critiques et ne nécessitent pas d’audit, il importe peu si un changement occasionnel est perdu.

### Gestion de l’expiration des données dans un cache

Dans la plupart des cas, les données conservées dans un cache sont une copie des données contenues dans le magasin de données d'origine. Les données du magasin de données d'origine peuvent changer après leur mise en cache, entraînant l’expiration des données mises en cache. De nombreux systèmes de mise en cache permettent de configurer le cache de manière à faire expirer les données et à réduire la période pendant laquelle les données peuvent être obsolètes.

Lorsque les données mises en cache arrivent à expiration, elles sont supprimées du cache, et l'application doit récupérer les données à partir du magasin de données d'origine (elle peut remettre les informations qui viennent d'être lues dans le cache). Vous pouvez définir une stratégie d'expiration par défaut lorsque vous configurez le cache. Dans de nombreux services de cache, vous pouvez également stipuler la date d'expiration des objets individuels lorsque vous les stockez par programme dans le cache. Certains caches permettent de spécifier la période d'expiration en tant que valeur absolue ou comme valeur décalée qui provoque la suppression de l'élément du cache s’il n'est pas consulté dans le délai spécifié. Ce paramètre remplace les stratégies d'expiration du cache, mais uniquement pour les objets spécifiés.

> [AZURE.NOTE]Réfléchissez attentivement à la période d'expiration du cache et aux objets qu'il contient. Si cette période est trop courte, les objets expirent trop rapidement et vous limitez les avantages de l'utilisation du cache. Si la période est trop longue, les données risquent de devenir obsolètes.

Il est également possible que le cache se remplisse si les données sont autorisées à rester résidentes pendant une longue période. Dans ce cas, les demandes d’ajout de nouveaux éléments au cache peuvent entraîner la suppression forcée de certains éléments. On parle alors d’éviction. Les services de cache suppriment généralement les données les moins récemment utilisées (LRU), mais vous pouvez généralement remplacer cette stratégie et empêcher l’éviction des éléments. Toutefois, si vous adoptez cette approche, le risque est que votre cache dépasse la mémoire disponible. Dans ce cas, une application qui tente d'ajouter un élément au cache échoue avec une exception.

Certaines implémentations de la mise en cache peuvent fournir des stratégies d'éviction supplémentaires. Elles incluent généralement la stratégie des éléments utilisés récemment (en supposant que les données ne seront pas requises à nouveau), la stratégie du premier entré, premier sorti (les données les plus anciennes sont supprimées en premier) ou la suppression explicite basée sur un événement déclenché (comme la modification des données).

### Annulation de la validité des données dans un cache côté client

Les données conservées dans un cache côté client sont généralement considérées comme étant en dehors de la responsabilité du service qui fournit les données au client. Un service ne peut pas forcer directement un client à ajouter ou à supprimer des informations dans un cache côté client. Cela signifie qu'il est possible qu'un client qui utilise un cache mal configuré (par exemple, avec des stratégies d’expiration incorrectement implémentées) continue à utiliser des informations obsolètes mises en cache localement lorsque les informations contenues dans la source de données d'origine ont changé.

Si vous générez une application web qui traite les données via une connexion HTTP, vous pouvez implicitement forcer un client web (par exemple, un navigateur ou un proxy web) à extraire les informations les plus récentes si une ressource est mise à jour en modifiant l'URI associé. Les clients web utilisent généralement l'URI d'une ressource comme clé dans le cache côté client. Par conséquent, lors de la modification de l'URI, le client web ignore les versions précédemment mises en cache d'une ressource et extrait la nouvelle version à la place.

## Gestion de l'accès concurrentiel dans un cache

Les caches sont souvent conçus pour être partagés par plusieurs instances d'une application. Chaque instance de l'application peut lire et modifier les données du cache. Par conséquent, les problèmes d’accès concurrentiel qui surviennent avec n'importe quel magasin de données partagées sont également applicables à un cache. Lorsqu’une application a besoin de modifier les données mises en cache, assurez-vous que les mises à jour effectuées par une des instances de l'application ne remplacent pas aveuglément les modifications apportées par une autre instance.

Selon la nature des données et la probabilité de collisions, vous pouvez adopter une des deux approches d'accès concurrentiel suivantes :

- __Accès concurrentiel optimiste.__ L'application vérifie si les données du cache ont été modifiées depuis leur récupération, juste avant la mise à jour. Si les données sont toujours identiques, la modification peut être apportée. Dans le cas contraire, l'application doit décider s'il faut les mettre à jour (la logique métier qui pilote cette décision sera spécifique de l'application). Cette approche est appropriée pour les situations où les mises à jour sont peu fréquentes ou celles où les collisions sont peu probables.
- __Accès concurrentiel pessimiste.__ L'application verrouille les données du cache lors de leur récupération pour empêcher qu'une autre instance les modifie. Ce processus garantit que les collisions ne peuvent pas se produire, mais peut bloquer les autres instances qui doivent traiter les mêmes données. L'accès concurrentiel pessimiste peut affecter l'extensibilité de la solution et doit être utilisé uniquement pour les opérations à durée de vie limitée. Cette approche peut être appropriée pour les situations où les collisions sont plus susceptibles de se produire, en particulier si une application met à jour plusieurs éléments du cache et que vous devez vous assurer que ces modifications sont appliquées de manière cohérente.

### Implémentation de l'extensibilité et de la haute disponibilité et amélioration des performances

Un cache ne doit pas être le référentiel principal des données. C'est le rôle du magasin de données d'origine à partir duquel le cache est rempli. Le magasin de données d'origine est responsable de la persistance des données.

Veillez à ne pas introduire de dépendances critiques sur la disponibilité d'un service de cache partagé dans vos solutions. Une application doit être en mesure de continuer à fonctionner si le service qui fournit le cache partagé n'est pas disponible. Elle ne doit pas se bloquer ou échouer en attendant la reprise du service de cache. Par conséquent, l'application doit pouvoir détecter la disponibilité du service de cache et revenir au magasin de données d'origine si le cache n'est pas accessible. Le [modèle de disjoncteur](http://msdn.microsoft.com/library/dn589784.aspx) est utile pour gérer ce scénario. Le service qui fournit le cache peut être récupéré et dès qu'il est disponible, le cache peut être rempli à nouveau, car les données sont lues à partir du magasin de données d'origine, en suivant une stratégie telle que le [mode de type cache-aside](http://msdn.microsoft.com/library/dn589799.aspx).

Toutefois, revenir au magasin de données d'origine en cas d’indisponibilité temporaire du cache peut avoir un impact en termes d'extensibilité sur le système. Pendant que le magasin de données est en cours de restauration, le magasin de données d'origine peut être assailli de demandes de données, ce qui entraîne des délais d'attente et l’échec des connexions. Une stratégie à envisager est d’implémenter un cache local privé dans chaque instance d'une application avec le cache partagé auquel toutes les instances de l'application accèdent. Lorsque l'application récupère un élément, elle peut d'abord vérifier sa mémoire cache locale, le cache partagé puis le magasin de données d'origine. Le cache local peut être rempli à l'aide des données du cache partagé ou de la base de données si le cache partagé n'est pas disponible. Cette approche nécessite une configuration soigneuse pour éviter que le cache local soit trop périmé par rapport au cache partagé, mais elle agit comme une mémoire tampon si le cache partagé est inaccessible. La figure 3 illustre cette structure.

![Utilisation d'un cache local et privé avec un cache partagé](media/best-practices-caching/Caching3.png) _Figure 3 : Utilisation d'un cache local privé avec un cache partagé_

Pour prendre en charge des caches volumineux qui contiennent des données à long terme, certains services de cache offrent une option de haute disponibilité qui implémente le basculement automatique si le cache devient indisponible. Cette approche implique généralement la réplication des données mises en cache d’un serveur de cache principal vers un serveur de cache secondaire, et le basculement vers le serveur secondaire en cas d'échec du serveur principal ou de perte de la connectivité. Pour réduire la latence associée à l’écriture vers plusieurs destinations, lorsque les données sont écrites dans le cache sur le serveur principal, la réplication vers le serveur secondaire peut se produire en mode asynchrone. Cette approche peut avoir comme conséquence que certaines informations mises en cache soient perdues en cas de défaillance, mais la proportion de ces données doit être faible par rapport à la taille globale du cache.

Si un cache partagé est volumineux, il peut être avantageux de partitionner les données mises en cache sur des nœuds pour réduire les risques de contention et améliorer l'extensibilité. De nombreux caches partagés prennent en charge la possibilité d'ajouter (et de supprimer) dynamiquement des nœuds et de rééquilibrer les données sur plusieurs partitions. Cette approche peut impliquer le clustering par lequel la collection de nœuds est présentée aux applications clientes en tant que cache transparent et unique. Toutefois, en interne, les données sont dispersées entre les nœuds suivant une stratégie de distribution prédéfinie qui équilibre la charge uniformément. Le [document de conseils sur le partitionnement des données](http://msdn.microsoft.com/library/dn589795.aspx), disponible sur le site web de Microsoft fournit des informations sur les stratégies de partitionnement possibles.

Le clustering peut également augmenter la disponibilité du cache. Si un nœud échoue, le reste du cache est toujours accessible. Le clustering est fréquemment utilisé avec la réplication et le basculement. Chaque nœud peut être répliqué et le réplica peut être mis en ligne rapidement si le nœud échoue.

De nombreuses opérations de lecture et d'écriture impliquent probablement des valeurs ou objets de données uniques. Toutefois, il peut arriver qu'il soit nécessaire stocker ou de récupérer rapidement de grands volumes de données. Par exemple, amorcer un cache peut impliquer l'écriture de centaines ou de milliers d'éléments dans le cache, ou une application peut avoir besoin de récupérer un grand nombre d'éléments associés dans le cache dans le cadre de la même demande. Plusieurs caches à grande échelle fournissent des opérations par lots à cette fin, en permettant à une application cliente d’empaqueter un volume important d'éléments en une seule demande, réduisant ainsi la surcharge associée à l'exécution d'un grand nombre de petites demandes.

## Mise en cache et cohérence finale

Le mode de type cache-aside dépend de l'instance de l'application remplissant le cache qui a accès à la version la plus récente et cohérente des données. Cela peut ne pas être le cas dans un système qui implémente la cohérence finale (par exemple, un magasin de données répliqué). Une des instances d'une application peut modifier un élément de données et invalider la version mise en cache de cet élément. Une autre instance de l'application peut tenter de lire cet élément à partir du cache, ce qui entraîne une opération non réussie dans le cache. Elle lit alors les données du magasin de données et les ajoute au cache. Toutefois, si le magasin de données n'a pas été entièrement synchronisé avec les autres réplicas, l'instance d'application peut lire et remplir le cache avec l'ancienne valeur.

Pour plus d'informations sur la gestion de la cohérence des données, consultez la page de conseils sur la cohérence des données sur le site web de Microsoft.

### Protection des données mises en cache

Quel que soit le service de cache que vous utilisez, vous devez envisager la manière dont vous pouvez protéger les données contenues dans le cache contre un accès non autorisé. Il existe deux problèmes principaux :

- La confidentialité des données dans le cache.
- La confidentialité des données lorsqu'elles circulent entre le cache et l'application utilisant le cache.

Pour protéger les données dans le cache, le service de cache peut implémenter un mécanisme d'authentification nécessitant que les applications s’identifient et une stratégie d'autorisation qui spécifie quelles identités peuvent accéder aux données dans le cache, ainsi que les opérations (de lecture et d’écriture) que ces identités sont autorisées à effectuer. Pour réduire les surcharges associées à la lecture et à l’écriture de données, une fois que l’identité a obtenu un accès en écriture et/ou en lecture au cache, elle peut utiliser toutes les données du cache. Si vous devez restreindre l'accès à des sous-ensembles de données mises en cache, vous pouvez effectuer les opérations suivantes :

- Fractionner le cache en partitions (à l'aide de différents serveurs de cache) et accorder uniquement aux identités l’accès aux partitions qu'elles sont autorisées à utiliser, ou
- Chiffrer les données dans chaque sous-ensemble en utilisant des clés différentes et fournir uniquement les clés de chiffrement aux identités qui doivent avoir accès à chaque sous-ensemble. Une application cliente peut toujours être en mesure de récupérer toutes les données dans le cache, mais elle pourra uniquement déchiffrer les données pour lesquelles elle possède les clés.

Pour protéger les données circulant vers et depuis le cache, vous êtes dépendant des fonctionnalités de sécurité fournies par l'infrastructure réseau que les applications clientes utilisent pour se connecter au cache. Si le cache est implémenté à l'aide d'un serveur local au sein de l’organisation qui héberge les applications clientes, l'isolation du réseau lui-même peut se suffire à elle-même. Si le cache est distant et requiert une connexion TCP ou HTTP via un réseau public (comme Internet), vous devez envisager d'implémenter SSL.

## Considérations sur l'implémentation de la mise en cache avec Microsoft Azure

Azure fournit le Cache Redis Azure. Il s'agit d'une implémentation du Cache Redis open source qui s'exécute en tant que service dans un centre de données Azure. Il fournit un service de mise en cache qui est accessible à partir de n'importe quelle application Azure, que l'application soit implémentée comme un service cloud, un site web, ou soit située à l'intérieur d'une machine virtuelle Azure. Les caches peuvent être partagés par les applications clientes qui possèdent la clé d'accès appropriée.

Redis est une solution de mise en cache hautes performances qui fournit disponibilité, extensibilité et sécurité. En général, elle s'exécute en tant que service sur un ou plusieurs ordinateurs dédiés et tente de stocker autant d'informations que possible dans la mémoire pour assurer un accès rapide. Cette architecture est conçue pour fournir une latence faible et un haut débit en réduisant la nécessité d'effectuer des opérations d'E/S lentes.

Le Cache Redis Azure est compatible avec la plupart des API utilisées par les applications clientes. Si vous avez des applications existantes qui utilisent déjà Redis localement, le Cache Redis Azure fournit un chemin de migration rapide pour la mise en cache dans le cloud.

> [AZURE.NOTE]Azure fournit également le service de cache géré. Ce service est basé sur le moteur de cache Microsoft AppFabric. Il vous permet de créer un cache distribué qui peut être partagé par les applications faiblement couplées. Le cache est hébergé sur des serveurs hautes performances exécutés dans un centre de données Azure. Toutefois, cette option n'est plus recommandée et elle est fournie uniquement pour prendre en charge des applications existantes qui ont été générées afin de l'utiliser. Pour tout nouveau développement, utilisez plutôt le Cache Redis Azure.
>
> En outre, Azure prend en charge la mise en cache In-Role Cache. Cette fonctionnalité vous permet de créer un cache propre à un service cloud. Le cache est hébergé par les instances d'un rôle web ou de travail. Il est accessible uniquement par les rôles fonctionnant au sein de la même unité de déploiement de service cloud (une unité de déploiement est le jeu d'instances de rôle déployées comme service cloud dans une région spécifique). Le cache est mis en cluster, et toutes les instances du rôle situées dans l’unité de déploiement hébergeant le cache font partie du même cluster de cache. Les applications existantes qui utilisent la mise en cache In-Role Cache peuvent continuer à le faire, mais la migration vers le Cache Redis Azure peut apporter des avantages supplémentaires. Pour plus d'informations sur le choix entre l’utilisation du Cache Redis Azure et celle du cache In-Role Cache, consultez la page [Choix de l'offre Azure Cache appropriée](http://msdn.microsoft.com/library/azure/dn766201.aspx) sur le site web de Microsoft.


### Fonctionnalités de Redis

Redis est plus qu'un serveur de cache simple. Il fournit une base de données en mémoire distribuée avec un jeu de commandes complet qui prend en charge de nombreux scénarios courants, comme décrit dans la section Exemples d'utilisation du Cache Redis plus loin dans ce document. Cette section décrit quelques-unes des fonctionnalités clés que fournit Redis.

### Redis comme base de données en mémoire

Redis prend en charge les opérations de lecture et d'écriture. Contrairement à de nombreux caches (qui doivent être considérés comme des magasins de données temporaires), les écritures peuvent être protégées contre la défaillance du système en les stockant régulièrement dans un fichier d'instantané local ou dans un fichier journal en mode Ajout uniquement. Toutes les écritures sont asynchrones et ne bloquent pas les clients qui lisent et écrivent des données. Lorsque Redis commence à s’exécuter, il lit les données à partir du fichier journal ou de l'instantané et les utilise pour construire le cache en mémoire. Pour plus d'informations, consultez la page [Persistance Redis](http://redis.io/topics/persistence) sur le site web Redis.

> [AZURE.NOTE]Redis ne garantit pas que toutes les écritures seront enregistrées en cas de défaillance irrémédiable, mais au pire vous perdrez uniquement quelques secondes de données. N'oubliez pas qu'un cache n'est pas destiné à agir comme source de données faisant autorité. Il est responsable des applications qui utilisent le cache pour s’assurer que les données critiques sont correctement enregistrées dans un magasin de données adéquat. Pour plus d’information, reportez-vous au mode de type cache-aside.

#### Types de données Redis

Redis est un magasin de clés-valeurs, où les valeurs peuvent contenir des types simples ou des structures de données complexes telles que des hachages, des listes et des ensembles. Il prend en charge un ensemble d'opérations atomiques pour ces types de données. Les clés peuvent être permanentes ou marquées avec une durée de vie limitée suite à laquelle la clé et la valeur correspondante sont automatiquement supprimées du cache. Pour plus d'informations sur les valeurs et clés Redis, consultez la page de [présentation des types de données et abstractions Redis](http://redis.io/topics/data-types-intro) sur le site web Redis.

#### Réplication et clustering Redis

Redis prend en charge la réplication maître/subordonné pour garantir la disponibilité et conserver le débit. Les opérations d'écriture vers un nœud principal Redis sont répliquées sur un ou plusieurs nœuds subordonnés et les opérations de lecture peuvent être gérées par le maître et l’un des subordonnés. Dans le cas d'une partition réseau, les subordonnés peuvent continuer à fournir les données, puis se resynchroniser en toute transparence avec le maître lorsque la connexion est rétablie. Pour plus d'informations, visitez la page [Réplication](http://redis.io/topics/replication) sur le site web Redis.

Redis fournit également le clustering, qui vous permet de partitionner les données en partitions de manière transparente sur les serveurs et de répartir la charge. Cette fonctionnalité améliore l'extensibilité, car de nouveaux serveurs Redis peuvent être ajoutés et les données réparties à mesure que la taille du cache augmente. En outre, chaque serveur du cluster peut être répliqué à l'aide de la réplication maître/subordonné pour garantir la disponibilité sur chaque nœud du cluster. Pour plus d'informations sur le clustering et le partitionnement, visitez le [didacticiel sur le cluster Redis](http://redis.io/topics/cluster-tutorial) sur le site web Redis.

> [AZURE.NOTE]Actuellement, le Cache Redis Azure ne prend pas en charge le clustering. Si vous souhaitez créer un cluster Redis, vous pouvez créer votre propre serveur Redis personnalisé. Pour plus d'informations, consultez la section Création d'un cache Redis personnalisé plus loin dans ce document.

### Utilisation de la mémoire Redis

Le cache Redis a une taille limitée selon les ressources disponibles sur l'ordinateur hôte. Lorsque vous configurez un serveur Redis, vous pouvez spécifier la quantité maximale de mémoire qu'il peut utiliser. Une clé dans un cache Redis peut être configurée avec un délai d'expiration après lequel elle est automatiquement supprimée du cache. Cette fonctionnalité peut empêcher le cache en mémoire d’être rempli avec des données anciennes ou obsolètes.

À mesure que la mémoire se remplit, Redis peut automatiquement supprimer les clés et leurs valeurs en suivant un certain nombre de stratégies. La valeur par défaut est LRU (dernier récemment utilisé), mais vous pouvez également sélectionner d’autres stratégies, telles que l’éviction aléatoire des clés ou la désactivation de l'éviction (auquel cas, les tentatives d'ajout d’éléments au cache échoueront si ce dernier est plein). Pour plus d’informations, consultez la page [Utilisation de Redis comme cache LRU](http://redis.io/topics/lru-cache).

### Transactions et lots Redis

Redis permet à une application cliente d’envoyer une série d'opérations qui lisent et écrivent des données dans le cache en tant que transaction atomique. Toutes les commandes de la transaction sont assurées d'être exécutées de façon séquentielle et aucune commande émise par d'autres clients simultanés ne sera insérée entre elles. Toutefois, il ne s’agit pas de transactions réelles, car une base de données relationnelle les exécute. Le traitement des transactions se compose de deux étapes : la mise en file d’attente puis l’exécution des commandes. Durant la phase de mise en file d'attente des commandes, les commandes qui composent la transaction sont envoyées par le client. Si une erreur se produit à ce stade (par exemple, une erreur de syntaxe ou un nombre incorrect de paramètres) Redis refuse de traiter toute la transaction et l'ignore. Pendant la phase d'exécution, Redis exécute chaque commande en file d'attente dans l'ordre. En cas d'échec d'une commande au cours de cette phase, Redis passe à la commande en file d'attente suivante et ne restaure pas les effets des commandes qui ont déjà été exécutées. Cette forme simplifiée de transaction permet de maintenir les performances et d'éviter les problèmes de performances causés par un conflit. Redis implémente une forme de verrouillage optimiste pour aider à maintenir la cohérence. Pour plus d'informations sur les transactions et le verrouillage avec Redis, visitez la page [Transactions](http://redis.io/topics/transactions) sur le site web de Redis.

Redis prend également en charge le traitement par lots non transactionnel des demandes. Le protocole Redis que les clients utilisent pour envoyer des commandes à un serveur Redis permet à un client d'envoyer une série d'opérations dans le cadre de la même demande. Cela peut aider à réduire la fragmentation des paquets sur le réseau. Lorsque le lot est traité, chaque commande est exécutée. Contrairement à une transaction, si une de ces commandes est mal formée, elle est rejetée, mais les commandes restantes sont effectuées. Il n'existe également aucune garantie concernant l'ordre dans lequel les commandes du lot sont traitées.

### Sécurité Redis

Redis se concentre strictement sur l’offre d’un accès rapide aux données et est conçu pour s'exécuter au sein d'un environnement approuvé et accessible uniquement par les clients approuvés. Redis prend uniquement en charge un modèle de sécurité limitée basé sur l'authentification par mot de passe (il est possible de supprimer l'authentification, même si cela n'est pas recommandé). Tous les clients authentifiés partagent le même mot de passe global et ont accès aux mêmes ressources. Si vous avez besoin d'une sécurité de connexion plus complète, vous devez implémenter votre propre couche de sécurité devant le serveur Redis et toutes les demandes des clients doivent passer via cette couche supplémentaire. Redis ne doit pas être directement exposé aux clients non approuvés ou non authentifiés.

Vous pouvez restreindre l'accès aux commandes en les désactivant ou en les renommant (et en fournissant uniquement les nouveaux noms aux clients dotés de privilèges).

Redis ne gère pas directement les différentes formes de chiffrement des données. Tout l’encodage doit être effectué par les applications clientes. En outre, Redis ne fournit pas de forme de sécurité du transport. Par conséquent, si vous avez besoin de protéger les données lorsqu'elles transitent sur le réseau, vous devez implémenter un proxy SSL.

Pour plus d’informations, consultez la page [Sécurité Redis](http://redis.io/topics/security) sur le site web de Redis.

> [AZURE.NOTE]Le Cache Redis Azure fournit sa propre couche de sécurité via laquelle les clients se connectent. Les serveurs Redis sous-jacents ne sont pas exposés au réseau public.

### Utilisation du Cache Redis Azure

Le Cache Redis Azure permet d'accéder aux serveurs Redis s'exécutant sur les serveurs hébergés dans un centre de données Azure. Il agit comme une façade qui fournit de la sécurité et un contrôle de l’accès. Vous pouvez approvisionner un cache à l'aide du portail de gestion Azure. Le portail fournit un certain nombre de configurations prédéfinies, allant d'un cache de 53 Go exécuté en tant que service dédié qui prend en charge les communications SSL (pour la confidentialité) à la réplication maître/subordonné avec un contrat SLA offrant une disponibilité de 99,9 %, à un cache de 250 Mo sans réplication (aucune garantie de disponibilité) en cours d'exécution sur du matériel partagé.

À l'aide du portail de gestion Azure, vous pouvez également configurer la stratégie d'éviction du cache et contrôler l'accès au cache en ajoutant des utilisateurs aux rôles fournis : Propriétaire, Collaborateur, Lecteur. Ces rôles définissent les opérations que les membres peuvent effectuer. Par exemple, les membres du rôle Propriétaire ont un contrôle complet du cache (y compris la sécurité) et de son contenu, les membres du rôle Collaborateur peuvent lire et écrire des informations dans le cache, et les membres du rôle Lecteur peuvent uniquement récupérer des données à partir du cache.

La plupart des tâches d'administration sont effectuées via le portail de gestion Azure. Pour cette raison, la plupart des commandes d'administration disponibles dans la version standard de Redis ne sont pas disponibles, y compris la possibilité de modifier la configuration par programme, d’arrêter le serveur Redis, de configurer des subordonnés supplémentaires ou de forcer l’enregistrement des données sur le disque.

Le portail de gestion Azure inclut un affichage graphique pratique qui vous permet d’analyser les performances du cache. Par exemple, vous pouvez afficher le nombre de connexions établies, le nombre de demandes effectuées, le volume des lectures et des écritures, ainsi que le nombre de correspondances dans le cache par rapport aux opérations non réussies dans le cache. À l'aide de ces informations, vous pouvez déterminer l'efficacité du cache et basculer vers une autre configuration si nécessaire ou modifier la stratégie d'éviction. En outre, vous pouvez créer des alertes qui envoient des messages électroniques à un administrateur si une ou plusieurs mesures critiques sont comprises dans une plage attendue. Par exemple, si le nombre d’opérations non réussies dans le cache dépasse la valeur spécifiée dans la dernière heure, un administrateur peut être averti que le cache est peut-être trop petit ou que des données risquent d’être supprimées trop rapidement.

Vous pouvez également surveiller le processeur, la mémoire et l'utilisation du réseau pour le cache.

> [AZURE.NOTE]Le Cache Redis Azure est destiné à agir exclusivement comme cache plutôt que comme base de données. Par conséquent, il n'implémente pas actuellement la persistance Redis.

Pour plus d'informations et pour obtenir des exemples montrant comment créer et configurer un Cache Redis Azure, visitez la page [Tour d’horizon du Cache Redis Azure](http://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) sur le blog Azure.

## État de session de mise en cache et sortie HTML

Si vous concevez des applications web ASP.NET qui s’exécutent à l'aide de rôles web Azure, vous pouvez enregistrer la sortie HTML et les informations d'état de session dans un Cache Redis Azure. Le fournisseur d'état de session du Cache Redis Azure vous permet de partager des informations de session entre différentes instances d'une application web ASP.NET. Il est très utile dans le cas d’une batterie de serveurs web où l’affinité client-serveur n'est pas disponible et où la mise en cache des données de session en mémoire ne serait pas appropriée.

L’utilisation du fournisseur d'état de session avec le Cache Redis Azure offre plusieurs avantages, notamment :

- Le fournisseur d’état de session peut partager l'état de session avec un grand nombre d'instances d'une application web ASP.NET, en fournissant une extensibilité améliorée.
- Il prend en charge un accès contrôlé et simultané aux mêmes données d'état de session pour plusieurs lecteurs et un seul enregistreur.
- Il peut utiliser la compression pour économiser de la mémoire et améliorer les performances réseau.

Pour plus d'informations, visitez la page [Fournisseur d'état de session ASP.NET pour le Cache Redis Azure](http://msdn.microsoft.com/library/azure/dn690522.aspx) sur le site web de Microsoft.

> [AZURE.NOTE]N'utilisez pas le fournisseur d'état de session pour le Cache Redis Azure pour les applications ASP.NET qui s'exécutent en dehors de l'environnement Microsoft Azure. La latence de l’accès au cache depuis l’extérieur d'Azure peut éliminer les avantages en matière de performances de la mise en cache de données.

De même, le fournisseur de caches de sortie pour le Cache Redis Azure vous permet d'enregistrer les réponses HTTP générées par une application web ASP.NET. L’utilisation du fournisseur de caches de sortie avec le Cache Redis Azure peut améliorer les temps de réponse des applications qui restituent une sortie HTML complexe. Les instances d'application générant des réponses similaires peuvent utiliser des fragments de sortie partagés dans le cache au lieu de générer entièrement cette sortie HTML. Pour plus d'informations, visitez la page [Fournisseur de caches de sortie ASP.NET pour le Cache Redis Azure](http://msdn.microsoft.com/library/azure/dn798898.aspx) sur le site web de Microsoft.

## Création d'un cache Redis personnalisé

Le Cache Redis Azure agit comme façade des serveurs Redis sous-jacents. Actuellement, il prend en charge un ensemble fixe de configurations, mais ne fournit pas le clustering Redis. Si vous avez besoin d'une configuration avancée qui n'est pas couverte par le Cache Redis Azure (par exemple, un cache supérieur à 53 Go), vous pouvez créer et héberger vos propres serveurs Redis à l'aide de machines virtuelles Azure. Il s’agit d’un processus potentiellement complexe, car vous devrez peut-être créer plusieurs machines virtuelles en tant que nœuds principaux et secondaires si vous souhaitez implémenter la réplication. En outre, si vous souhaitez créer un cluster, vous devez avoir plusieurs serveurs maîtres et subordonnés. Une topologie de réplication de cluster minimale qui fournit un degré élevé de disponibilité et d’extensibilité comprend au moins 6 machines virtuelles organisées sous forme de paires de 3 serveurs maîtres/subordonnés (un cluster doit contenir au moins 3 nœuds maîtres). Chaque paire maître/subordonné doit être située à proximité pour réduire la latence, mais chaque jeu de paires peut être exécuté dans différents centres de données Azure situés dans différentes régions, si vous souhaitez stocker des données mises en cache à proximité des applications qui sont susceptibles de les utiliser. Le billet de blog sur l’[exécution de Redis sur une machine virtuelle Linux CentOS dans Azure](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) sur le site web de Microsoft présente un exemple décrivant comment créer et configurer un nœud Redis en tant que machine virtuelle Azure.

Notez que si vous implémentez votre propre cache Redis de cette façon, vous êtes responsable de la surveillance, de la gestion et de la sécurisation du service.

## Partitionnement d’un cache Redis

Le partitionnement du cache implique le fractionnement du cache sur plusieurs ordinateurs. Cette structure présente plusieurs avantages par rapport à l'utilisation d'un serveur de cache unique, notamment :

- Création d'un cache beaucoup plus volumineux que ce qui peut être stocké sur un serveur unique.
- Distribution des données entre serveurs, en améliorant la disponibilité. Si un serveur tombe en panne ou deviennent inaccessible, seules les données qu'il contient sont indisponibles ; les données des serveurs restants sont toujours accessibles. Pour un cache, cela n’est pas indispensable, car les données mises en cache sont seulement une copie temporaire des données contenues dans une base de données. En outre, les données mises en cache sur un serveur qui devient inaccessible peuvent être mises en cache sur un autre serveur.
- Répartition de la charge entre les serveurs, ce qui améliore les performances et l'extensibilité.
- Localisation des données à proximité des utilisateurs qui y accèdent, ce qui réduit la latence.

Pour un cache, la forme la plus courante est le partitionnement. Dans cette stratégie, chaque partition est un cache Redis à part entière. Les données sont dirigées vers une partition spécifique à l'aide de la logique de partitionnement, qui peut utiliser plusieurs méthodes. Le [modèle de partitionnement](http://msdn.microsoft.com/library/dn589797.aspx) fournit des informations supplémentaires sur l'implémentation du partitionnement.

Pour implémenter le partitionnement dans un cache Redis, vous pouvez adopter une des approches suivantes :

- _Routage des demandes côté serveur._ Dans cette technique, une application cliente envoie une demande à un des serveurs Redis qui composent le cache (probablement le serveur le plus proche). Chaque serveur Redis stocke les métadonnées décrivant la partition qu’il contient, et contient également les informations relatives aux partitions présentes sur d’autres serveurs. Le serveur Redis examine la demande du client et, si elle peut être résolue localement, procède à l’opération demandée. Autrement, il transmet la demande au serveur approprié. Ce modèle est mis en œuvre à l’aide du clustering Redis et est décrit plus en détail dans le [didacticiel sur les clusters Redis](http://redis.io/topics/cluster-tutorial) sur le site web de Redis. Le clustering Redis est transparent pour les applications clientes et il est possible d’ajouter des serveurs Redis supplémentaires au cluster (et aux données ayant fait l’objet d’un nouveau partitionnement) sans devoir reconfigurer les clients.

  >[AZURE.IMPORTANT]Le Cache Redis Azure ne prend pas en charge le clustering Redis. Si vous souhaitez implémenter cette approche, vous devez créer un cache Redis personnalisé comme décrit précédemment.

- _Partitionnement côté client._ Dans ce modèle, l'application cliente contient la logique (éventuellement sous la forme d'une bibliothèque) qui achemine les demandes vers le serveur Redis approprié. Cette approche peut être utilisée avec le Cache Redis Azure. Créez plusieurs Caches Redis Azure (un pour chaque partition de données) et implémentez la logique côté client qui achemine les demandes vers le cache adéquat. Si le schéma de partitionnement est modifié (par exemple, si des caches Redis Azure supplémentaires sont créés), il faut reconfigurer les applications clientes.

- _Partitionnement assisté par un proxy._ Dans ce schéma, les applications clientes envoient des demandes à un service proxy intermédiaire qui comprend la façon dont les données sont partitionnées puis achemine la demande ver le serveur Redis approprié. Cette approche est valable également avec un Cache Redis Azure. Le service proxy peut être implémenté en tant que service cloud Azure. Cette approche nécessite un niveau supplémentaire de complexité pour implémenter le service et l’exécution des demandes peut prendre plus de temps qu'en utilisant le partitionnement côté client.

La page sur le [partitionnement et le fractionnement des données entre plusieurs instances Redis](http://redis.io/topics/partitioning) sur le site web de Redis fournit des informations supplémentaires concernant la mise en œuvre du partitionnement avec Redis.

### Implémentation d'applications clientes de cache Redis

Redis prend en charge les applications clientes écrites dans plusieurs langages de programmation. Si vous générez de nouvelles applications à l'aide de .NET Framework, l'approche recommandée consiste à utiliser la bibliothèque cliente StackExchange.Redis. Cette bibliothèque fournit un modèle objet .NET Framework qui résume les détails de connexion à un serveur Redis, l'envoi de commandes et la réception des réponses. Elle est disponible dans Visual Studio comme package NuGet. Vous pouvez utiliser cette bibliothèque pour vous connecter à un cache Redis Azure ou un cache Redis personnalisé hébergé sur une machine virtuelle.

Pour vous connecter à un serveur Redis, utilisez la méthode `Connect` statique de la classe `ConnectionMultiplexer`. La connexion créée par cette méthode est conçue pour être utilisée pendant toute la durée de vie de l'application cliente et la même connexion peut être utilisée par plusieurs threads simultanés. Ne vous reconnectez/déconnectez pas à chaque fois que vous effectuez une opération Redis, car cela peut dégrader les performances. Vous pouvez spécifier les paramètres de connexion, tels que l'adresse de l'hôte Redis et le mot de passe. Si vous utilisez le cache Redis Azure, le mot de passe correspond à la clé primaire ou secondaire générée pour le cache Redis Azure à l'aide du portail de gestion Azure.

Une fois que vous vous êtes connecté au serveur Redis, vous pouvez obtenir un handle sur la base de données Redis qui agit comme cache. La connexion Redis fournit la méthode `GetDatabase` correspondante. Vous pouvez ensuite récupérer les éléments dans le cache et stocker des données dans le cache à l'aide des méthodes `StringGet` et `StringSet`. Ces méthodes attendent une clé en tant que paramètre. Elles renvoient l'élément du cache qui a une valeur correspondante (`StringGet`) ou ajoutent l'élément au cache avec cette clé (`StringSet`).

Selon l'emplacement du serveur Redis, de nombreuses opérations peuvent entraîner une latence pendant laquelle une demande est transmise au serveur et une réponse est renvoyée au client. La bibliothèque StackExchange fournit des versions asynchrones de la plupart des méthodes qu'elle expose pour aider les applications clientes à rester réactives. Ces méthodes prennent en charge le [modèle asynchrone basé sur des tâches](http://msdn.microsoft.com/library/hh873175.aspx) dans .NET Framework.

L'extrait de code suivant montre une méthode nommée `RetrieveItem` qui illustre un exemple d'implémentation du mode de type cache-aside basé sur Redis et la bibliothèque StackExchange. La méthode accepte une valeur de clé de chaîne et tente de récupérer l'élément correspondant à partir du cache Redis en appelant la méthode `StringGetAsync` (version asynchrone de `StringGet`). Si l'élément est introuvable, il est extrait de la source de données sous-jacente à l'aide de la méthode `GetItemFromDataSourceAsync` (méthode locale qui ne fait pas partie de la bibliothèque StackExchange), puis ajouté au cache à l'aide de la méthode `StringSetAsync`. Il pourra ainsi être récupéré plus rapidement la prochaine fois.

```csharp
// Connect to the Azure Redis cache
ConfigurationOptions config = new ConfigurationOptions();
config.EndPoints.Add("<your DNS name>.redis.cache.windows.net");
config.Password = "<Redis cache key from management portal>";
ConnectionMultiplexer redisHostConnection = ConnectionMultiplexer.Connect(config);
IDatabase cache = redisHostConnection.GetDatabase();
...
private async Task<string> RetrieveItem(string itemKey)
{
    // Attempt to retrieve the item from the Redis cache
    string itemValue = await cache.StringGetAsync(itemKey);

    // If the value returned is null, the item was not found in the cache
    // So retrieve the item from the data source and add it to the cache
    if (itemValue == null)
    {
        itemValue = await GetItemFromDataSourceAsync(itemKey);
        await cache.StringSetAsync(itemKey, itemValue);
    }

    // Return the item
    return itemValue;
}
```

Les méthodes `StringGet` et `StringSet` ne sont pas limitées à l'extraction ou au stockage de valeurs de chaîne. Elles peuvent accepter n'importe quel élément sérialisé en tant que tableau d'octets. Si vous avez besoin d'enregistrer un objet .NET, vous pouvez le sérialiser en tant que flux d'octets et utiliser la méthode StringSet pour l'écrire dans le cache. De même, vous pouvez lire un objet à partir du cache à l'aide de la méthode StringGet et le désérialiser en tant qu’objet .NET. Le code suivant illustre un ensemble de méthodes d'extension pour l'interface IDatabase (la méthode GetDatabase d'une connexion Redis renvoie un objet `IDatabase`), ainsi qu’un exemple de code qui utilise ces méthodes pour lire et écrire un objet BlogPost dans le cache :

```csharp
public static class RedisCacheExtensions
{
    public static async Task<T> GetAsync<T>(this IDatabase cache, string key)
    {
        return Deserialize<T>(await cache.StringGetAsync(key));
    }

    public static async Task<object> GetAsync(this IDatabase cache, string key)
    {
        return Deserialize<object>(await cache.StringGetAsync(key));
    }

    public static async Task SetAsync(this IDatabase cache, string key, object value)
    {
        await cache.StringSetAsync(key, Serialize(value));
    }

    static byte[] Serialize(object o)
    {
        byte[] objectDataAsStream = null;

        if (o != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream())
            {
                binaryFormatter.Serialize(memoryStream, o);
                objectDataAsStream = memoryStream.ToArray();
            }
        }

        return objectDataAsStream;
    }

    static T Deserialize<T>(byte[] stream)
    {
        T result = default(T);

        if (stream != null)
        {
            BinaryFormatter binaryFormatter = new BinaryFormatter();
            using (MemoryStream memoryStream = new MemoryStream(stream))
            {
                result = (T)binaryFormatter.Deserialize(memoryStream);
            }
        }

        return result;
    }
}
```

Le code suivant illustre une méthode nommée `RetrieveBlogPost` qui utilise ces méthodes d'extension pour lire et écrire un objet `BlogPost` sérialisable dans le cache selon le mode de type cache-aside :

```csharp
// The BlogPost type
[Serializable]
private class BlogPost
{
    private HashSet<string> tags = new HashSet<string>();

    public BlogPost(int id, string title, int score, IEnumerable<string> tags)
    {
        this.Id = id;
        this.Title = title;
        this.Score = score;
        this.tags = new HashSet<string>(tags);
    }

    public int Id { get; set; }
    public string Title { get; set; }
    public int Score { get; set; }
    public ICollection<string> Tags { get { return this.tags; } }
}
...
private async Task<BlogPost> RetrieveBlogPost(string blogPostKey)
{
    BlogPost blogPost = await cache.GetAsync<BlogPost>(blogPostKey);
    if (blogPost == null)
    {
        blogPost = await GetBlogPostFromDataSourceAsync(blogPostKey);
        await cache.SetAsync(blogPostKey, blogPost);
    }

    return blogPost;
}
```

Redis prend en charge la mise en pipeline des commandes si une application cliente envoie plusieurs demandes asynchrones. Redis peut appliquer un multiplexage aux demandes à l'aide de la même connexion plutôt que de recevoir et de répondre aux commandes dans une séquence stricte. Cette approche permet de réduire la latence grâce à une utilisation plus efficace du réseau. L'extrait de code suivant montre un exemple qui récupère simultanément les détails de deux clients. Le code envoie deux demandes, effectue d'autres traitements (non illustrés), puis attend de recevoir les résultats. La méthode Wait de l'objet cache est similaire à la méthode Task.Wait .NET Framework :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
var task1 = cache.StringGetAsync("customer:1");
var task2 = cache.StringGetAsync("customer:2");
...
var customer1 = cache.Wait(task1);
var customer2 = cache.Wait(task2);
```

La page [Développement pour le cache Redis Azure](http://msdn.microsoft.com/library/azure/dn690520.aspx) sur le site web de Microsoft fournit des informations supplémentaires sur l'écriture d'applications clientes qui peuvent utiliser le cache Redis Azure. Des informations supplémentaires sont disponibles sur la page [Utilisation de base](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) du site web StackExchange.Redis. La page [Pipelines et multiplexeur](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) du même site web fournit des informations sur les opérations asynchrones et la mise en pipeline avec Redis et la bibliothèque StackExchange. La section Exemples d'utilisation pour la mise en cache Redis plus loin dans ce guide fournit des exemples de techniques plus avancées que vous pouvez appliquer aux données conservées dans un cache Redis.

## Exemples d'utilisation pour la mise en cache Redis

L'utilisation la plus simple de Redis pour la mise en cache concerne le stockage de paires clé/valeur, où la valeur est une chaîne non interprétée de longueur arbitraire qui peut contenir des données binaires. Il s’agit essentiellement d’un tableau d'octets qui peut être traité en tant que chaîne. Ce scénario a été illustré dans la section Implémentation des applications clientes du cache Redis plus haut dans ce guide. Notez que les clés contiennent également des données non interprétées. Vous pouvez ainsi utiliser les informations binaires comme clé. Toutefois, plus la clé est grande, plus l'espace de stockage nécessaire est élevé, et plus les opérations de recherche prennent du temps. Pour simplifier l'utilisation et faciliter la maintenance, concevez votre keyspace soigneusement et utilisez des clés explicites (mais pas détaillées). Par exemple, utilisez des clés structurées telles que « client:100 » pour représenter la clé pour le client avec l'ID 100 plutôt que simplement « 100 ». Ce schéma permet de facilement faire la différence entre les valeurs qui stockent les différents types de données. Par exemple, vous pouvez également utiliser la clé « orders:100 » pour représenter la clé pour la commande avec l'ID 100.

Indépendamment des chaînes binaires unidimensionnelles, une valeur d’une paire clé/valeur Redis peut également contenir davantage d’informations plus structurées, y compris des listes, des ensembles (triés et non triés) et des hachages. Redis fournit un ensemble de commandes complet qui permet de manipuler ces types. Bon nombre de ces commandes sont accessibles par les applications .NET Framework via une bibliothèque cliente telle que StackExchange. La page [Présentation des types de données et abstractions Redis](http://redis.io/topics/data-types-intro) du site web de Redis fournit une vue plus détaillée de ces types et des commandes que vous pouvez utiliser pour les manipuler.

Cette section récapitule quelques utilisations habituelles pour ces types de données et commandes.

### Exécution d’opérations atomiques et par lots

Redis prend en charge une série d'opérations get et set atomiques sur les valeurs de chaîne. Ces opérations suppriment les risques de conditions de concurrence possibles qui peuvent se produire lors de l'utilisation de commandes `GET` et `SET` distinctes. Les opérations disponibles sont les suivantes :

- `INCR`, `INCRBY`, `DECR` et `DECRBY` effectuent des opérations d'incrémentation et de décrémentation atomiques sur les valeurs de données numériques entières. La bibliothèque StackExchange fournit des versions surchargées des méthodes `IDatabase.StringIncrementAsync` et `IDatabase.StringDecrementAsync` pour effectuer ces opérations et renvoyer la valeur résultante stockée dans le cache. L'extrait de code suivant illustre l’utilisation de ces méthodes :

  ```csharp ConnectionMultiplexer redisHostConnection = ...; IDatabase cache = redisHostConnection.GetDatabase(); ... await cache.StringSetAsync("data:counter", 99); ... long oldValue = await cache.StringIncrementAsync("data:counter"); // Increment by 1 (the default) // oldValue should be 100

  long newValue = await cache.StringDecrementAsync("data:counter", 50); // Decrement by 50 // newValue should be 50 ```

- `GETSET` récupère la valeur associée à une clé et la remplace par une nouvelle valeur. La bibliothèque StackExchange donne accès à cette opération via la méthode `IDatabase.StringGetSetAsync`. L'extrait de code ci-dessous montre un exemple de cette méthode. Ce code renvoie la valeur actuelle associée à la clé « data:counter » de l'exemple précédent et réinitialise la valeur de cette clé à zéro, tout cela dans le cadre d’une même opération :

  ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  IDatabase cache = redisHostConnection.GetDatabase();
  ...
  string oldValue = await cache.StringGetSetAsync("data:counter", 0);
  ```

- `MGET` et `MSET` peuvent renvoyer ou modifier un ensemble de valeurs de chaîne en une seule opération. Les méthodes `IDatabase.StringGetAsync` et `IDatabase.StringSetAsync` sont surchargées pour prendre en charge cette fonctionnalité, comme illustré dans l'exemple suivant :

  ```csharp ConnectionMultiplexer redisHostConnection = ...; IDatabase cache = redisHostConnection.GetDatabase(); ... // Create a list of key/value pairs var keysAndValues = new List<KeyValuePair<RedisKey  RedisValue>>() { new KeyValuePair<RedisKey  RedisValue>("data:key1", "value1"), new KeyValuePair<RedisKey  RedisValue>("data:key99", "value2"), new KeyValuePair<RedisKey  RedisValue>("data:key322", "value3") };

  // Store the list of key/value pairs in the cache cache.StringSet(keysAndValues.ToArray()); ... // Find all values that match a list of keys RedisKey keys = { "data:key1", "data:key99", "data:key322"}; RedisValue values = null; values = cache.StringGet(keys); // values should contain { "value1", "value2", "value3" } ```

Vous pouvez également combiner plusieurs opérations en une seule opération Redis comme décrit dans la section Transactions et lots Redis de ce guide. La bibliothèque StackExchange prend en charge les transactions via l’interface `ITransaction`. Vous pouvez créer un objet ITransaction à l'aide de la méthode IDatabase.CreateTransaction et appeler des commandes vers la transaction en utilisant l’objet `ITransaction` fourni par les méthodes. L’interface `ITransaction` fournit l'accès à un ensemble similaire de méthodes, comme l’interface `IDatabase`, sauf que toutes les méthodes sont asynchrones ; elles ne sont effectuées que lorsque la méthode `ITransaction.Execute` est appelée. La valeur renvoyée par la méthode execute indique si la transaction a été créée avec succès (true) ou a échoué (false).

L'extrait de code suivant montre un exemple qui incrémente et décrémente deux compteurs dans le cadre d’une même transaction :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
ITransaction transaction = cache.CreateTransaction();
var tx1 = transaction.StringIncrementAsync("data:counter1");
var tx2 = transaction.StringDecrementAsync("data:counter2");
bool result = transaction.Execute();
Console.WriteLine("Transaction {0}", result ? "succeeded" : "failed");
Console.WriteLine("Result of increment: {0}", tx1.Result);
Console.WriteLine("Result of decrement: {0}", tx2.Result);
```

N'oubliez pas que les transactions Redis sont peu probables dans les bases de données relationnelles. La méthode Execute met simplement en file d’attente toutes les commandes qui composent la transaction à des fins d'exécution, et si l’une d’elles est mal formée, la transaction est abandonnée. Si toutes les commandes ont été mises en file d’attente, chaque commande est exécutée en mode asynchrone. Si une commande échoue, les autres continuent le traitement. Pour vérifier qu'une commande s'est bien terminée, vous devez extraire les résultats de la commande à l'aide de la propriété Result de la tâche correspondante, comme illustré dans l'exemple ci-dessus. La lecture de la propriété Result est bloquée jusqu'à ce que la tâche soit terminée.

Pour plus d'informations, consultez la page [Transactions Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) sur le site web StackExchange.Redis.

Pour effectuer des opérations de traitement par lots, vous pouvez utiliser l'interface IBatch de la bibliothèque StackExchange. Cette interface fournit l'accès à un ensemble similaire de méthodes, comme l’interface IDatabase, sauf que toutes les méthodes sont asynchrones. Vous créez un objet IBatch à l'aide de la méthode IDatabase.CreateBatch, puis exécutez le traitement par lots à l'aide de la méthode IBatch.Execute, comme illustré dans l'exemple suivant. Ce code définit simplement une valeur de chaîne, incrémente et décrémente les compteurs utilisés dans l'exemple précédent et affiche les résultats :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
IBatch batch = cache.CreateBatch();
batch.StringSetAsync("data:key1", 11);
var t1 = batch.StringIncrementAsync("data:counter1");
var t2 = batch.StringDecrementAsync("data:counter2");
batch.Execute();
Console.WriteLine("{0}", t1.Result);
Console.WriteLine("{0}", t2.Result);
```

Il est important de comprendre que contrairement à une transaction, si une commande d’un traitement par lots échoue car elle est mal formée, les autres commandes peuvent toujours s'exécuter. La méthode IBatch.Execute ne renvoie pas d’indication de réussite ou d'échec.

### Exécution d'opérations de cache « Fire and forget »

Redis prend en charge les opérations « Fire and forget » à l'aide d'indicateurs de commande. Dans ce cas, le client lance simplement une opération mais n’est pas intéressé par le résultat et n'attend pas la fin de la commande. L'exemple ci-dessous montre comment exécuter la commande INCR en tant qu’opération « Fire and forget » :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
await cache.StringSetAsync("data:key1", 99);
...
cache.StringIncrement("data:key1", flags: CommandFlags.FireAndForget);
```

### Expiration automatique des clés

Lorsque vous stockez un élément dans un cache Redis, vous pouvez spécifier un délai après lequel l'élément est automatiquement supprimé du cache. Vous pouvez également interroger la durée restante avant expiration d’une clé à l'aide de la commande `TTL`, disponible pour les applications StackExchange à l'aide de la méthode IDatabase.KeyTimeToLive.

L'extrait de code suivant montre l’exemple de définition d'un délai d'expiration de 20 secondes sur une clé et l'interrogation de la durée de vie restante de la clé :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration time of 20 seconds
await cache.StringSetAsync("data:key1", 99, TimeSpan.FromSeconds(20));
...
// Query how much time a key has left to live
// If the key has already expired, the KeyTimeToLive function returns a null
TimeSpan? expiry = cache.KeyTimeToLive("data:key1");
```

Vous pouvez également définir le délai d'expiration sur une date et une heure spécifiques à l'aide de la commande EXPIRE, disponible dans la bibliothèque StackExchange en tant que méthode KeyExpireAsync :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Add a key with an expiration date of midnight on 1st January 2015
await cache.StringSetAsync("data:key1", 99);
await cache.KeyExpireAsync("data:key1",
    new DateTime(2015, 1, 1, 0, 0, 0, DateTimeKind.Utc));
...
```

> _Conseil :_ vous pouvez supprimer manuellement un élément du cache à l'aide de la commande DEL, disponible via la bibliothèque StackExchange en tant que méthode IDatabase.KeyDeleteAsync.

### Utilisation de balises pour mettre en corrélation de manière croisée des éléments mis en cache

Un ensemble Redis est constitué de plusieurs éléments qui partagent une clé unique. Vous pouvez créer un ensemble à l'aide de la commande SADD. Vous pouvez récupérer les éléments d’un ensemble à l'aide de la commande SMEMBERS. La bibliothèque StackExchange implémente la commande SADD via la méthode IDatabase.SetAddAsync et la commande SMEMBERS avec la méthode IDatabase.SetMembersAsync. Vous pouvez également combiner les ensembles existants pour créer de nouveaux ensembles à l'aide des commandes SDIFF (différence ensembliste), SINTER (intersection ensembliste) et SUNION (union ensembliste). La bibliothèque StackExchange unifie ces opérations dans la méthode IDatabase.SetCombineAsync. Le premier paramètre de cette méthode spécifie l'opération ensembliste à effectuer.

Les extraits de code suivants montrent comment les ensembles peuvent être utiles pour rapidement stocker et récupérer des collections d'éléments connexes. Ce code utilise le type BlogPost décrit dans la section Implémentation d’applications clientes de cache Redis. Un objet BlogPost contient quatre champs : un ID, un titre, un score de classement et une collection de balises. Le premier extrait de code ci-dessous montre les exemples de données utilisés pour remplir une liste C# d'objets BlogPost :

```csharp
List<string[]> tags = new List<string[]>()
{
    new string[] { "iot","csharp" },
    new string[] { "iot","azure","csharp" },
    new string[] { "csharp","git","big data" },
    new string[] { "iot","git","database" },
    new string[] { "database","git" },
    new string[] { "csharp","database" },
    new string[] { "iot" },
    new string[] { "iot","database","git" },
    new string[] { "azure","database","big data","git","csharp" },
    new string[] { "azure" }
};

List<BlogPost> posts = new List<BlogPost>();
int blogKey = 0;
int blogPostId = 0;
int numberOfPosts = 20;
Random random = new Random();
for (int i = 0; i < numberOfPosts; i++)
{
    blogPostId = blogKey++;
    posts.Add(new BlogPost(
        blogPostId,               // Blog post ID
        string.Format(CultureInfo.InvariantCulture, "Blog Post #{0}",
            blogPostId),          // Blog post title
        random.Next(100, 10000),  // Ranking score
        tags[i % tags.Count]));   // Tags – assigned from a collection
                                  // in the tags list
}
```

Vous pouvez stocker les balises pour chaque objet BlogPost en tant qu'ensemble dans un cache Redis et associer chaque ensemble avec l’ID de l’objet BlogPost. Cela permet à une application de trouver rapidement toutes les balises qui appartiennent à un billet de blog spécifique. Pour activer la recherche dans le sens opposé et rechercher tous les billets de blog qui partagent une balise spécifique, vous pouvez créer un autre ensemble qui contient les billets de blog faisant référence à l'ID de balise dans la clé :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
// Tags are easily represented as Redis Sets
foreach (BlogPost post in posts)
{
    string redisKey = string.Format(CultureInfo.InvariantCulture,
        "blog:posts:{0}:tags", post.Id);
    // Add tags to the blog post in redis
    await cache.SetAddAsync(
        redisKey, post.Tags.Select(s => (RedisValue)s).ToArray());

    // Now do the inverse so we can figure how which blog posts have a given tag.
    foreach (var tag in post.Tags)
    {
        await cache.SetAddAsync(string.Format(CultureInfo.InvariantCulture,
            "tag:{0}:blog:posts", tag), post.Id);
    }
}
```

Ces structures permettent d'effectuer de nombreuses requêtes courantes de manière très efficace. Par exemple, vous pouvez rechercher et afficher toutes les balises du billet de blog 1 comme suit :

```csharp
// Show the tags for blog post #1
foreach (var value in await cache.SetMembersAsync("blog:posts:1:tags"))
{
    Console.WriteLine(value);
}
```

Vous pouvez rechercher toutes les balises qui sont communes aux billets de blog 1 et 2 en effectuant une opération d'intersection ensembliste, comme suit :

```csharp
// Show the tags in common for blog posts #1 and #2
foreach (var value in await cache.SetCombineAsync(SetOperation.Intersect, new RedisKey[]
    { "blog:posts:1:tags", "blog:posts:2:tags" }))
{
    Console.WriteLine(value);
}
```

Vous pouvez également rechercher tous les billets de blog qui contiennent une balise spécifique :

```csharp
// Show the ids of the blog posts that have the tag "iot".
foreach (var value in await cache.SetMembersAsync("tag:iot:blog:posts"))
{
    Console.WriteLine(value);
}
```

### Recherche d’éléments consultés récemment

Un problème courant rencontré par de nombreuses applications est de trouver les éléments consultés le plus récemment. Par exemple, un blog peut souhaiter afficher des informations sur les billets de blog les plus récemment consultés. Vous pouvez implémenter cette fonctionnalité en utilisant une liste Redis. Celle-ci contient plusieurs éléments qui partagent la même clé, mais elle agit comme une file d'attente à deux extrémités. Vous pouvez transmettre les éléments vers l’une des extrémités de la liste à l'aide des commandes LPUSH (transmission gauche) et RPUSH (transmission droite). Vous pouvez récupérer des éléments à partir d’une des extrémités de la liste en utilisant les commandes LPOP et RPOP. Vous pouvez également renvoyer un ensemble d'éléments à l'aide des commandes LRANGE et RRANGE. Les extraits de code ci-dessous montrent comment vous pouvez effectuer ces opérations à l'aide de la bibliothèque StackExchange. Ce code utilise le type BlogPost issu des exemples précédents. Lorsqu'un billet de blog est lu par un utilisateur, son titre est transmis à une liste associée à la clé « blog:recent\_posts » dans le cache Redis en utilisant la méthode IDatabase.ListLeftPushAsync :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:recent_posts";
BlogPost blogPost = ...; // reference to the blog post that has just been read
await cache.ListLeftPushAsync(
    redisKey, blogPost.Title); // push the blog post onto the list
```

Lors de la lecture des autres billets de blog, leurs titres sont envoyés à la même liste. La liste est triée dans l’ordre dans lequel les billets de blog ont été ajoutés. Ceux ajoutés le plus récemment sont envoyés à l'extrémité gauche de la liste (si le même billet de blog est lu plusieurs fois, plusieurs entrées sont créées dans la liste). Vous pouvez afficher les titres des billets de blog les plus récemment consultés à l'aide de la méthode IDatabase.ListRange. Cette méthode prend la clé qui contient la liste, un point de départ et un point de fin. L’extrait de code suivant récupère le titre des 10 billets de blog (éléments de 0 à 9) à l’extrémité la plus à gauche de la liste :

```csharp
// Show latest ten posts
foreach (string postTitle in await cache.ListRangeAsync(redisKey, 0, 9))
{
    Console.WriteLine(postTitle);
}
```

Notez que ListRangeAsync ne supprime pas les éléments de la liste. Pour ce faire, vous pouvez utiliser les méthodes IDatabase.ListLeftPopAsync et IDatabase.ListRightPopAsync.

Pour empêcher la liste de croître indéfiniment, vous pouvez régulièrement éliminer les éléments de la liste. L'extrait de code ci-dessous supprime tout, à l’exception des 5 éléments les plus à gauche dans la liste :

```csharp
await cache.ListTrimAsync(redisKey, 0, 5);
```

### Implémentation d'un classement

Par défaut, les éléments d’un ensemble ne sont pas maintenus dans un ordre spécifique. Vous pouvez créer un ensemble ordonné en utilisant la commande ZADD (méthode IDatabase.SortedSetAdd dans la bibliothèque StackExchange). Les éléments sont classés à l'aide d'une valeur numérique appelée « score » fournie en tant que paramètre à la commande. L'extrait de code suivant ajoute le titre d'un billet de blog à une liste ordonnée. Dans l'exemple, chaque billet de blog comporte également un champ de score qui contient le classement du billet de blog.

```csharp
ConnectionMultiplexer redisHostConnection = ...;
IDatabase cache = redisHostConnection.GetDatabase();
...
string redisKey = "blog:post_rankings";
BlogPost blogPost = ...; // reference to a blog post that has just been rated
await cache.SortedSetAddAsync(redisKey, blogPost.Title, blogpost.Score);
```

Vous pouvez récupérer les titres de billet de blog et les scores dans l’ordre croissant à l'aide de la méthode IDatabase.SortedSetRangeByRankWithScores :

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(redisKey))
{
    Console.WriteLine(post);
}
```

> [AZURE.NOTE]La bibliothèque StackExchange fournit également la méthode IDatabase.SortedSetRangeByRankAsync qui renvoie les données dans l'ordre des scores, mais ne renvoie pas de scores.

Vous pouvez également récupérer des éléments dans l'ordre décroissant des scores et limiter le nombre d'éléments renvoyés en fournissant des paramètres supplémentaires à la méthode IDatabase.SortedSetRangeByRankWithScoresAsync. L'exemple suivant affiche les titres et les scores des 10 premiers billets de blog classés :

```csharp
foreach (var post in await cache.SortedSetRangeByRankWithScoresAsync(
                               redisKey, 0, 9, Order.Descending))
{
    Console.WriteLine(post);
}
```

L'exemple suivant utilise la méthode IDatabase.SortedSetRangeByScoreWithScoresAsync qui vous permet de limiter les éléments renvoyés à ceux qui se situent dans une plage de scores donnée :

```csharp
// Blog posts with scores between 5000 and 100000
foreach (var post in await cache.SortedSetRangeByScoreWithScoresAsync(
                               redisKey, 5000, 100000))
{
    Console.WriteLine(post);
}
```

### Messagerie à l'aide de canaux

Outre son utilisation comme cache de données, un serveur Redis fournit la messagerie via un mécanisme éditeur/abonné hautes performances. Les applications clientes peuvent s'abonner à un canal, et les autres applications ou services peuvent publier des messages dans le canal. Les applications abonnées reçoivent ensuite ces messages pour les traiter.

Pour vous abonner au canal, Redis fournit la commande SUBSCRIBE. Cette commande attend le nom d'un ou de plusieurs canaux sur lesquels l'application acceptera les messages. La bibliothèque StackExchange inclut l'interface ISubscription qui permet à une application .NET Framework de s'abonner aux canaux et de publier vers ces derniers. Vous créez un objet ISubscription à l'aide de la méthode GetSubscriber de la connexion au serveur Redis, puis écoutez des messages sur un canal à l'aide de la méthode SubscribeAsync de cet objet. L'exemple de code suivant montre comment s'abonner à un canal nommé « messages:blogPosts » :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
await subscriber.SubscribeAsync("messages:blogPosts", (channel, message) =>
{
    Console.WriteLine("Title is: {0}", message);
});
```

Le premier paramètre de la méthode Subscribe est le nom du canal. Ce nom suit les mêmes conventions que celles utilisées par des clés dans le cache. Il peut contenir des données binaires, bien qu'il soit conseillé d'utiliser des chaînes relativement courtes et explicites afin de garantir la facilité de gestion et de bonnes performances. Notez également que l'espace de noms utilisé par les canaux est différent de celui utilisé par les clés. Vous pouvez avoir des canaux et des clés qui portent le même nom, bien que cela risque de rendre votre code d'application plus difficile à gérer.

Le deuxième paramètre est un délégué Action. Ce délégué s'exécute en mode asynchrone chaque fois qu'un nouveau message apparaît dans le canal. Cet exemple affiche simplement le message dans la console (le message contient le titre d'un billet de blog).

Pour publier vers un canal, une application peut utiliser la commande Redis PUBLISH. La bibliothèque StackExchange fournit la méthode IServer.PublishAsync pour effectuer cette opération. L'extrait de code suivant montre comment publier un message sur le canal « messages:blogPosts » :

```csharp
ConnectionMultiplexer redisHostConnection = ...;
ISubscriber subscriber = redisHostConnection.GetSubscriber();
...
BlogPost blogpost = ...;
subscriber.PublishAsync("messages:blogPosts", blogPost.Title);
```

Il existe plusieurs points que vous devez comprendre sur le mécanisme éditeur/abonné :

- Plusieurs abonnés peuvent s'abonner au même canal. Ils recevront alors tous les messages publiés vers ce canal.
- Les abonnés ne reçoivent que les messages qui ont été publiés après le début de leur abonnement. Les canaux ne sont pas mis en mémoire tampon et une fois qu'un message est publié, l'infrastructure Redis l’envoie à chaque abonné, puis le supprime.
- Par défaut, les messages sont reçus par les abonnés dans l'ordre dans lequel ils sont envoyés. Dans un système très actif avec un grand nombre de messages et de nombreux abonnés et éditeurs, garantir la remise de messages séquentielle peut ralentir les performances du système. Si chaque message est indépendant et que l'ordre est sans importance, vous pouvez activer un traitement simultané par le système Redis qui peut aider à améliorer la réactivité. Vous pouvez obtenir cela dans un client StackExchange en définissant l’élément PreserveAsyncOrder de la connexion utilisée par l'abonné sur false : ```csharp
  ConnectionMultiplexer redisHostConnection = ...;
  redisHostConnection.PreserveAsyncOrder = false;
  ISubscriber subscriber = redisHostConnection.GetSubscriber();
  ```

## Conseils et modèles connexes

Le modèle suivant peut également être pertinent lors de l’implémentation de la mise en cache dans vos applications :

- [Mode de type cache-aside](http://msdn.microsoft.com/library/dn589799.aspx) : ce mode décrit comment charger des données à la demande dans un cache à partir d'un magasin de données. Il permet également de maintenir la cohérence entre les données mises en cache et les données du magasin d'origine.
- Le [modèle de partitionnement](http://msdn.microsoft.com/library/dn589797.aspx) fournit des informations sur l'implémentation du partitionnement horizontal afin d'améliorer l'extensibilité lors du stockage et de l'accès à de grands volumes de données.

## Informations complémentaires

- Page [Classe MemoryCache](http://msdn.microsoft.com/library/system.runtime.caching.memorycache.aspx) sur le site web de Microsoft.
- Page [Cache Microsoft Azure](http://msdn.microsoft.com/library/windowsazure/gg278356.aspx) sur le site web de Microsoft.
- Page [Choix de l'offre Azure Cache appropriée](http://msdn.microsoft.com/library/azure/dn766201.aspx) sur le site web de Microsoft.
- Page [Modèle de configuration](http://msdn.microsoft.com/library/windowsazure/hh914149.aspx) sur le site web de Microsoft.
- Page [Modèle asynchrone basé sur les tâches](http://msdn.microsoft.com/library/hh873175.aspx) sur le site web de Microsoft.
- Page [Pipelines et multiplexeurs](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/PipelinesMultiplexers.md) sur le référentiel GitHub StackExchange.Redis.
- Page [Persistance Redis](http://redis.io/topics/persistence) sur le site web de Redis.
- Page [Réplication](http://redis.io/topics/replication) sur le site web de Redis.
- Page [Didacticiel sur le cluster Redis](http://redis.io/topics/cluster-tutorial) sur le site web de Redis.
- Page sur le [partitionnement et la manière de fractionner des données entre plusieurs instances Redis](http://redis.io/topics/partitioning) sur le site web de Redis.
- Page [Utilisation de Redis comme cache LRU](http://redis.io/topics/lru-cache) sur le site web de Redis.
- Page [Transactions](http://redis.io/topics/transactions) sur le site web de Redis.
- Page [Sécurité Redis](http://redis.io/topics/security) sur le site web de Redis.
- Page [Tour d’horizon du Cache Redis Azure](http://azure.microsoft.com/blog/2014/06/04/lap-around-azure-redis-cache-preview/) sur le blog Azure.
- Billet de blog [Exécution de Redis sur une machine virtuelle Linux CentOS](http://blogs.msdn.com/b/tconte/archive/2012/06/08/running-redis-on-a-centos-linux-vm-in-windows-azure.aspx) dans Azure sur le site web de Microsoft.
- Page [Fournisseur d'état de session ASP.NET pour le Cache Redis Azure](http://msdn.microsoft.com/library/azure/dn690522.aspx) sur le site web de Microsoft.
- Page [Fournisseur de caches de sortie ASP.NET pour le Cache Redis Azure](http://msdn.microsoft.com/library/azure/dn798898.aspx) sur le site web de Microsoft.
- Page [Développement pour le cache Redis Azure](http://msdn.microsoft.com/library/azure/dn690520.aspx) sur le site Azure.
- Page [Présentation des abstractions et des types de données Redis](http://redis.io/topics/data-types-intro) sur le site web de Redis.
- Page [Utilisation de base](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Basics.md) sur le site web StackExchange.Redis.
- Page [Transactions Redis](https://github.com/StackExchange/StackExchange.Redis/blob/master/Docs/Transactions.md) dans le référentiel StackExchange.Redis.
- Page [Guide de partitionnement des données](http://msdn.microsoft.com/library/dn589795.aspx) sur le site web de Microsoft.

<!---HONumber=Oct15_HO3-->