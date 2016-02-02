
<properties
   pageTitle="Modèle de composition de service avec état | Microsoft Azure"
   description="Le modèle de conception Service Fabric Reliable Actors qui utilise des acteurs avec état pour conserver l’état entre les appels de service, ainsi que mettre en cache les précédents résultats de service."
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

# Modèle de conception Reliable Actors : composition du service avec état

Les développeurs ont passé les 15 dernières années à créer des services sans état à N niveaux dans l'entreprise. Ils ont créé des services par-dessus des bases de données. Ils ont créé des services de haut niveau par-dessus d’autres services. Ensuite, ils ont créé des moteurs d’orchestration et des intergiciels (middleware) orientés messages pour coordonner ces services. Au fil de l’évolution des charges de travail utilisateur, pour répondre aux besoins accrus d’interactivité ou d’échelle, l’architecture orientée services (SOA) sans état a commencé à montrer ses faiblesses.

## L’ancienne méthode : services SOA

Alors que les services SOA évoluaient horizontalement en toute transparence en raison de leur nature sans état, ils ont créé un goulet d’étranglement en termes d’accès simultané et de débit au niveau du stockage. L’accès au stockage est alors devenu de plus en plus cher. L’usage a voulu que la plupart des développeurs introduisent une mise en cache dans leur solution pour réduire la demande sur le stockage. Cette solution n’a pas été sans inconvénients toutefois. Elle a exigé de gérer un autre niveau, un accès simultané au cache, des limitations et modifications sémantiques, ainsi que de la cohérence. Comme décrit dans le [modèle du cache intelligent](service-fabric-reliable-actors-pattern-smart-cache.md), le modèle d’acteur virtuel fournit une solution parfaite à ces problèmes. Certains développeurs essaient de résoudre leur problèmes de SOA en répliquant le niveau de stockage. Toutefois, cette approche ne permet pas une bonne évolutivité et atteint rapidement les limites du protocole d’alerte commun.

La deuxième difficulté s’est axée autour des configurations requises en constante évolution. Les utilisateurs et les entreprises exigent des services interactifs capables de répondre aux demandes en quelques millisecondes, plutôt que quelques secondes, et que cette rapidité soit la norme. Pour satisfaire cette exigence, les développeurs ont commencé à créer des services de façade par-dessus d’autres services. Dans certains cas, des dizaines de services de façade ont été conçus pour créer des services centrés sur l’utilisateur. Mais l’ajout de plusieurs services en aval crée rapidement des problèmes de latence.

Les développeurs ont également adopté des caches et des magasins d’objets en mémoire. Dans certains cas, ils ont utilisé des implémentations différentes pour répondre aux exigences de performance. Par cette approche, les développeurs créent généralement des processus de travail dorsaux pour créer le cache régulièrement. Ceux-ci réduisent le coûteux remplissage du cache à la demande. Ensuite, ils déconstruisent leurs charges de travail pour isoler les opérations asynchrones des synchrones. Cela laisse plus de place aux opérations interactives pour réagir aux modifications d’état, ce qui s’avère particulièrement difficile dans une SOA.

Ils introduisent souvent d’autres niveaux également, comme des files d’attente et des rôles de travail. Ceux-ci peuvent compliquer encore plus leurs solutions.

Globalement, les développeurs recherchent des solutions pour créer des « services avec état » qui combinent « état » et « comportement de service » afin de répondre aux besoins spécifiques et interactifs des utilisateurs. Et c’est là qu’intervient le modèle Azure Service Fabric Reliable Actors en tant que niveau de composition de service, et non comme une simple solution de rechange pour ces services.

Le diagramme ci-dessous illustre ce point :

![Acteurs fiables, composition de service et persistance d’état][1]

## Implémenter de meilleures solutions avec les acteurs

Pour composer des services, les acteurs peuvent être de type avec état ou sans état.

* Les acteurs sans état peuvent être utilisés comme proxys pour les services sous-jacents. Ces acteurs peuvent évoluer dynamiquement dans l’ensemble du cluster Service Fabric et mettre en cache certaines informations liées au service. Ces informations peuvent inclure son point de terminaison une fois qu’il est détecté.
* Les acteurs avec état peuvent maintenir l’état entre les appels de service et mettre en cache les précédents résultats de service. L'état peut être persistant ou transitoire.

Ce modèle s’applique à de nombreux scénarios. Dans la plupart des cas, un acteur doit passer un appel externe pour invoquer une opération sur un service particulier. À titre d’illustration, examinons un exemple issu d’une application de commerce électronique moderne. De telles applications s’appuient sur des services qui fournissent diverses fonctionnalités, notamment la gestion du profil utilisateur, les suggestions de produits, la gestion du panier, la gestion des listes de souhaits et les achats.

La plupart des développeurs spécialisés en commerce électronique essaient d’appliquer une approche centrée sur l’utilisateur à leur architecture, ce qui ressemble au développement d’une architecture pour des expériences sociales. En effet, les expériences de commerce électronique tournent principalement autour des utilisateurs et des produits. Les solutions des développeurs sont généralement élaborées en envoyant une façade de services qui, pour des raisons de performance, est vraisemblablement prise en charge par un cache.

Comparez cette approche à une approche basée sur l’acteur. Un acteur utilisateur peut représenter à la fois le comportement de l’utilisateur (par exemple, parcourir le catalogue, plébisciter un produit, ajouter un article au panier ou recommander un produit à un ami). Mais il peut également représenter l’état composé de l’utilisateur, notamment le profil de l’utilisateur, les articles dans le panier, les articles recommandés par des amis, l’historique des achats de l’utilisateur et la géolocalisation actuelle de l’utilisateur.

## Renseigner un état à l’aide d’acteurs avec état

Examinons tout d’abord un exemple où l’acteur utilisateur doit renseigner son état à partir de plusieurs services. Nous n’allons pas fournir d’exemple de code dans ce cas car tout ce que nous avons décrit dans le [modèle du cache intelligent](service-fabric-reliable-actors-pattern-smart-cache.md) s’applique également ici. Un acteur utilisateur peut être activé à la connexion et renseigné avec suffisamment de données à partir de services principaux. Les états complet et partiel peuvent également être préremplis à la demande, sur un minuteur ou les deux, puis ils peuvent être mis en cache dans l’acteur. Pour cet exemple, les services **Profil** et **Liste de souhaits** sont illustrés ci-dessous :

![Services Profil et Liste de souhaits][2]

Les développeurs peuvent préremplir l’état des utilisateurs fréquents et le préparer quand ils se connectent. Ils peuvent également remplir l’état au moment de la connexion pour les utilisateurs qui visitent le service chaque mois. Vous pouvez également étudier ces modèles dans la section du cache intelligent.

Quand l’utilisateur 23 (comme indiqué dans l’image ci-dessus) se connecte, l’acteur utilisateur (23) est activé, s’il ne l’a pas déjà été. L’acteur utilisateur extrait ensuite les informations de profil utilisateur et la liste de souhaités appropriés auprès des services principaux. L’acteur utilisateur met aussi probablement en cache les informations pour les appels suivants. Si, par exemple, un article a besoin d’être ajouté à la liste de souhaits, l’opération est rendue possible par un cache en écriture ou un cache à double écriture, comme expliqué précédemment.

Intéressons-nous maintenant à un exemple dans lequel un utilisateur clique sur le bouton **J’aime** pour plébisciter un produit. Cette action peut exiger plusieurs appels à plusieurs services. Ces actions peuvent inclure l’envoi d’un « J’aime » au service de catalogue, le déclenchement de l’ensemble suivant de recommandations et la publication d’une mise à jour sur un réseau social.

En voici une illustration :

![Plébisciter un produit et services Liste de souhaits, Profil et Catalogue][3]

## Utiliser des acteurs dans la composition et la communication asynchrone
Le modèle de programmation Service Fabric Reliable Actors convient parfaitement quand un développeur veut composer des opérations de type demande/réponse avec des opérations asynchrones. Par exemple, tandis que le fait de plébisciter un produit l’ajoute immédiatement à la liste de souhaits de l’utilisateur, une publication sur les réseaux sociaux et le déclenchement de l’ensemble suivant de recommandations peuvent être effectués de manière asynchrone à l’aide de mémoires tampons et de minuteurs.

Un autre avantage à utiliser un acteur utilisateur pour les services est que l’acteur fournit un emplacement naturel pour l’état mis en cache. Plus important encore, l’acteur réagit également aux modifications dans son état de manière asynchrone. Il s'agit d'un scénario particulièrement difficile avec des services sans état. Par exemple, un utilisateur peut exécuter une série d’actions dans le cadre d’un « parcours utilisateur » et ces événements peuvent être capturés en temps réel dans un acteur. Un flux peut ensuite être assemblé, puis interrogé au moment de l’événement ou de façon asynchrone sur un minuteur pour modifier le comportement de l’acteur.

À ce stade, les puristes de la SOA auront sans doute remarqué qu’il ne s’agit pas de services dans le sens d’acteurs en tant que points de terminaison exposés par l’intermédiaire d’un protocole indépendant du langage. Le modèle Service Fabric Reliable Actors n’est ni un composant d’interopérabilité ni une plateforme pour l’interopérabilité d’un service. Pourtant, rien n’empêche les développeurs de penser en termes de granularité des services de type SOA quand ils modélisent des acteurs ou la séparation des préoccupations. Ces services sont appelés microservices. Rien n’empêche non plus les développeurs de placer un point de terminaison REST ou SOAP en tant que couche d’interopérabilité devant Service Fabric Reliable Actors.

De plus, la composition d’un service avec état s’applique également aux workflows et pas simplement à des scénarios transactionnels comme le commerce électronique. Service Fabric est conçu comme un moteur de workflow/d’orchestration. Il peut servir à modéliser des workflow qui impliquent des interactions de service et à conserver l’état de ces interactions.

Comme vous pouvez le voir, un « service sans état » présente des inconvénients dans la création de services évolutifs qui fournissent une expérience dynamique. En rassemblant l’état et le comportement, le modèle de programmation Service Fabric Reliable Actors aide les développeurs à créer des expériences évolutives et interactives en s’appuyant sur leurs investissements existants.


## Étapes suivantes

[Modèle : cache intelligent](service-fabric-reliable-actors-pattern-smart-cache.md)

[Modèle : réseaux distribués et graphiques](service-fabric-reliable-actors-pattern-distributed-networks-and-graphs.md)

[Modèle : gestion des ressources](service-fabric-reliable-actors-pattern-resource-governance.md)

[Modèle : Internet des objets](service-fabric-reliable-actors-pattern-internet-of-things.md)

[Modèle : calcul distribué](service-fabric-reliable-actors-pattern-distributed-computation.md)

[Quelques anti-modèles](service-fabric-reliable-actors-anti-patterns.md)

[Présentation des Acteurs fiables Service Fabric](service-fabric-reliable-actors-introduction.md)


<!--Image references-->
[1]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-1.png
[2]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-2.png
[3]: ./media/service-fabric-reliable-actors-pattern-stateful-service-composition/stateful-service-composition-3.png

<!---HONumber=AcomDC_0121_2016-->