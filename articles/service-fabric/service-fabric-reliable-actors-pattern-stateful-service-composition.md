
<properties
   pageTitle="Modèle de conception de composition de service avec état pour les Acteurs fiables"
   description="Modèle de conception Acteurs fiables Service Fabric qui utilise des acteurs avec état pour conserver l'état entre les appels de service ainsi que les précédents résultats de service mis en cache. L'état peut être persistant ou transitoire."
   services="service-fabric"
   documentationCenter=".net"
   authors="vturecek"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="08/05/2015"
   ms.author="vturecek"/>

# Modèle de conception Acteurs fiables : composition de service avec état
Les développeurs ont passé les 15 dernières années à créer des services sans état à N niveaux dans l'entreprise. Ils ont créé des services au-dessus des bases de données, des services d'ordre élevé au-dessus d'autres services ainsi que des moteurs d'orchestration et des intergiciels orientés messages pour coordonner ces services. À mesure que les charges de travail de l'utilisateur évoluent, qu'elles exigent plus d'interactivité ou d'échelle, l'architecture orientée services sans état a commencé à montrer ses limites.

## L'ancienne méthode : Services SOA
Alors que les services SOA évoluaient horizontalement en toute transparence en raison de leur nature sans état, ils ont créé un goulet d'étranglement au niveau du stockage avec l'accès simultané et le débit. L'accès au stockage est devenu de plus en plus onéreux. La plupart des développeurs avaient pour habitude d'introduire une mise en cache dans leurs solutions afin de réduire la demande en stockage, mais cette solution n'était pas sans inconvénients : un autre niveau à gérer, un accès simultané au cache, des limitations et des modifications sémantiques, et enfin des problèmes de cohérence. Comme indiqué précédemment dans le modèle Smart Cache, le modèle d'acteur virtuel fournit une solution parfaite pour cela.

Certains développeurs ont tenté de résoudre le problème en répliquant leur niveau de stockage. Mais cette approche n'a pas évolué correctement et a rapidement atteint les limites CAP. Le deuxième défi concernait l'évolution des besoins ; les utilisateurs finaux et les entreprises recherchent des services interactifs, la norme étant de pouvoir répondre aux demandes en quelques millisecondes plutôt qu'en secondes. Pour y répondre, les développeurs ont commencé a créé des services de façade au-dessus d'autres services, dans certains cas des dizaines de services, pour créer des services centrés sur l'utilisateur. Mais la composition de plusieurs services en aval a rapidement montré des problèmes de latence.

Une fois encore, les développeurs se sont tournés vers les caches et les magasins d'objets en mémoire, avec parfois des implémentations différentes ont répondre aux exigences de performances. Ils ont commencé par créer des processus de travailleur principaux pour créer le cache périodiquement afin de réduire la population à la demande coûteuse du cache. Enfin, ils ont démarré la déconstruction de leurs charges de travail pour isoler les opérations asynchrones des opérations synchrones et obtenir ainsi plus de marge de manœuvre pour les opérations interactives afin de réagir aux modifications d'état, ce qui est particulièrement difficile dans SOA.

Ils ont introduit d'autres niveaux comme des files d'attente et des travailleurs, ajoutant ainsi plus de complexité à leurs solutions. Globalement, les développeurs ont commencé à rechercher des solutions pour créer des « services avec état », en d'autres termes pour combiner « état » et « comportement de service » afin de répondre aux besoins spécifiques et interactifs des utilisateurs. Et c'est là qu'intervient Azure Service Fabric comme niveau de composition de service, et non comme une simple solution de rechange pour ces services.

Le diagramme ci-dessous illustre ce point :

![][1]

## Une meilleure solution avec Actors
Dans le cas de la composition des services, les acteurs peuvent être de type avec état ou sans état.

* Les acteurs sans état peuvent être utilisés comme proxys pour les services sous-jacents. Ces acteurs peuvent évoluer dynamiquement dans l'ensemble du cluster Azure Service Fabric et mettre en cache certaines informations liées au service, par exemple le système d'extrémité lorsqu'il est détecté.
* Un acteur avec état peut maintenir l'état entre les appels de service et mettre en cache les résultats du service précédent. L'état peut être persistant ou transitoire.

Ce modèle s'applique également à plusieurs scénarios ; dans la plupart des cas, les acteurs doivent passer des appels externes pour invoquer une opération sur un service particulier. Examinons un exemple impliquant des applications de commerce électronique modernes. Ces applications s'appuient sur des services qui fournissent diverses fonctionnalités comme la gestion du profil utilisateur, les recommandations, la gestion du panier, la gestion des listes de souhaits, les achats et bien plus encore.

La plupart des développeurs souhaitent appliquer une approche centrée sur l'utilisateur à leur architecture, très similaire au développement d'expériences sociales puisque les opérations de commerce électronique sont principalement axées sur des utilisateurs et des produits. Cela passe généralement par l'envoi d'une façade de services, probablement prise en charge par un cache pour des raisons de performances.

Parlons maintenant d'une approche basée sur l'acteur. Un acteur utilisateur peut représenter à la fois le comportement de l'utilisateur (parcourir le catalogue, plébisciter un produit, ajouter un article au panier, recommander un produit à un ami) ainsi que son état composé : profil, articles dans le panier, liste des articles recommandés par leurs amis, historique des achats, position géographique actuelle, etc.

## Utilisation d'acteurs avec état
Examinons tout d'abord un exemple où l'acteur utilisateur doit remplir son état à partir de plusieurs services. Nous n'allons pas fournir un exemple de code dans ce cas car tout ce que dont nous avons parlé dans le modèle Smart Cache s'applique également ici. Nous pouvons activer l'acteur utilisateur au moment de la connexion en le remplissant avec suffisamment de données à partir de services principaux. Bien sûr, comme nous l'avons vu à maintes reprises précédemment dans ce document, un état complet et partiel peut être prérempli à la demande, sur un minuteur, ou un peu des deux, avant d'être mis en cache dans l'acteur. Pour cet exemple, le profil et la liste de souhaits sont présentés ci-dessous :

![][2]

Par exemple, nous pouvons préremplir l'état des utilisateurs fréquents afin qu'il soit prêt lorsque ces utilisateurs se connectent, ou le remplir au moment où les utilisateurs se connectent pour accéder au service chaque mois. Nous avons étudié ces modèles dans la section Smart Cache.

Lorsque l'utilisateur 23 se connecte, s'il n'est pas déjà activé, l'acteur utilisateur (23) est activé et extrait les informations de profil utilisateur appropriées ainsi que la liste de souhaits des services principaux. L'acteur utilisateur met probablement en cache les informations pour les appels suivants. Et si, par exemple, nous devons ajouter un élément à la liste de souhaits, nous pouvons effectuer les opérations de double écriture ou d'écriture différée comme indiqué plus haut. Deuxièmement, examinons un exemple où l'utilisateur clique sur le bouton « like » pour indiquer qu'il apprécie un produit. Cette action peut nécessiter plusieurs appels à différents services comme illustré ci-dessous : envoyer un « like » au service de catalogue, déclencher l'ensemble suivant de recommandations et peut-être publier une mise à jour sur un réseau social.

En voici une illustration :

![][3]

## Comment se servir de la composition et de la communication asynchrone d'Actors
En fait, Azure Service Fabric Actors excelle lorsque vous souhaitez combiner des opérations de type demande/réponse et des opérations asynchrones. Par exemple, tandis que la commande « Like Product » (J'aime ce produit) place immédiatement l'élément apprécié dans la liste de souhaits de l'utilisateur, la publication sur les réseaux sociaux et le déclenchement de prochain ensemble de recommandations peuvent être des opérations asynchrones qui utilisent des mémoires tampon et des minuteurs.

Un autre avantage clé de l'utilisation d'un acteur utilisateur avec des services vient du fait que les acteurs fournissent un emplacement naturel pour l'état mis en cache et, plus important encore, ils réagissent aux modifications de son état de manière asynchrone. Il s'agit d'un scénario particulièrement difficile avec des services sans état. Par exemple, un utilisateur exécute une série d'actions, peut-être dans le cadre d'un « voyage ». Ces événements peuvent être capturés en temps réel dans l'acteur et nous pouvons assembler un flux à interroger au moment de l'événement ou de façon asynchrone sur un minuteur pour modifier le comportement de l'acteur.

À ce stade, les puristes SOA auront sans doute remarqué qu'il ne s'agit pas de services dans le sens d'acteurs puisque les points de terminaison sont présentés via un protocole indépendant du langage. Azure Service Fabric Actors n'est ni un composant d'interopérabilité ni une plateforme pour l'interopérabilité d'un service. Néanmoins, rien ne nous empêche de considérer la granularité des services de type SOA lorsque nous modélisons nos acteurs ou distinguons les différents problèmes de la même façon. Ces services sont appelés « microservices ». De même, rien ne nous empêche de placer un système d'extrémité REST ou SOAP comme couche d'interopérabilité devant Azure Service Fabric Actors.

La composition d'un service avec état s'applique également aux workflows et pas simplement à des scénarios transactionnels comme le commerce électronique. Azure Service Fabric est conçu comme un moteur de flux de travail/d'orchestration et peut donc être utilisé pour modéliser des workflows impliquant des interactions du service et pour conserver l'état de ces interactions.

Nous consistons qu'un « service sans état » présente des inconvénients dans la création de services évolutifs pour fournir une expérience dynamique. Azure Service Fabric Actors, essentiellement en rassemblant l'état et le comportement, aide les développeurs à créer des expériences évolutives et interactives en s'appuyant sur leurs investissements existants.


## Étapes suivantes
[Modèle : Smart Cache](service-fabric-reliable-actors-pattern-smart-cache.md)

[Modèle : Réseaux distribués et graphiques](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Modèle : Gestion des ressources](service-fabric-reliable-actors-pattern-resource-governance.md)

[Modèle : Internet des objets](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Modèle : Calcul distribué](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Quelques anti-modèles](service-fabric-reliable-actors-anti-patterns.md)

[Introduction à Service Fabric Actors](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-2.png
[3]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-3.png

<!---HONumber=Oct15_HO3-->