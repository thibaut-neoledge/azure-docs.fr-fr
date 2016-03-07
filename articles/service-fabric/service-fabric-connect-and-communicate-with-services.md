<properties
   pageTitle="Communication et connexion aux services dans Azure Service Fabric | Microsoft Azure"
   description="Découvrez comment vous connecter aux services et communiquer avec eux dans les applications Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="mfussell"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="02/12/2016"
   ms.author="mfussell"/>

# Communication avec les services
Dans le monde des microservices, la solution globale est composée de nombreux services différents qui effectuent chacun une tâche spécialisée. Ces microservices communiquent entre eux pour activer le flux de travail de bout en bout. Il existe également des applications clientes qui se connectent aux services et communiquent avec eux. Ce document explique comment Azure Service Fabric vous permet de facilement configurer la communication avec les services que vous écrivez avec Service Fabric.

## Concepts clés
Voici certains concepts clés que vous devez prendre en compte quand vous configurez la communication avec les services.

### La communication est représentée sous la forme client-serveur
Les API de communication de Service Fabric représentent les types d’interactions client-service, même lorsque cette interaction concerne deux services s’exécutant dans le même cluster. Un service cible contacté par un client ou un autre service agit comme un serveur et écoute les demandes entrantes. Le client, qui peut être un autre service du cluster, se connecte au serveur et lui passe des appels.

### Déplacement des services
Dans un système distribué, les instances de service que vous exécutez peuvent déplacer un ordinateur vers un autre au fil du temps. Cela peut se produire pour diverses raisons, notamment l’équilibrage de la ressource en cas de configuration selon les métriques de charge pour l’équilibrage des ressources, les mises à niveau, les basculements et les montées en charge. Ceci a pour conséquence que les adresses de système d’extrémité d’une instance de service peuvent changer. Pour configurer la communication avec l’instance de service, la boucle suivante doit être exécutée. Ces détails sont transparents pour vous si vous utilisez les API de communication fournies par Service Fabric.

* **Résoudre**: toutes les instances de service nommées dans Service Fabric ont des noms uniques représentés par les URI. Par exemple, `"fabric:/MyApplication/MyService"` reste fixe pour le service nommé. Chaque instance de service nommé expose également des systèmes d’extrémité qui peuvent changer quand les instances de service nommé sont déplacées. C’est le même principe que pour les sites web, qui ont des URL constantes, mais dont l’adresse IP peut changer. De la même façon que le DNS du web, qui résout des URL de site web en adresses IP, Service Fabric possède un service système, le Service d’attribution de nom, qui résout les URI en systèmes d’extrémité. Cette étape implique la résolution de l'URI de l'instance de service en un point de terminaison.

* **Connexion** : une fois que l’URI du service nommé a été résolu en une adresse de système d’extrémité, la prochaine étape consiste à tenter une connexion au service. Cette connexion peut échouer si l’adresse de système d’extrémité a été modifiée à la suite d’un mouvement de service, lequel, par exemple, peut être dû à une défaillance d’ordinateur ou à l’équilibrage de la ressource.

* **Nouvelle tentative** : si la tentative de connexion échoue, les étapes précédentes de résolution et de connexion doivent être réessayées, et ce cycle se répète jusqu’à ce que la connexion aboutisse.

## Options d’API de communication
Dans le cadre de Service Fabric, nous fournissons quelques options pour les API de communication. Le choix de l’API dépend du modèle de programmation utilisé, de la structure de communication et du langage de programmation dans lequel vos services sont écrits.

### Communication des acteurs fiables
Pour les services écrits à l’aide de l’API Reliable Actors, tous les détails de communication sont transparents. La communication s’effectue lorsque la méthode appelle l’ActorProxy afin que vous puissiez arrêter la lecture.

### Options de communication des services fiables
Vous avez le choix entre deux options si votre service a été écrit à l’aide d’une API Reliable Services. L’utilisation d’une API de communication Service Fabric spécifique dépend du protocole de communication choisi.

* **Aucun protocole spécifique** : si vous pouvez choisir n’importe quelle infrastructure de communication, mais que vous souhaitez une solution rapidement opérationnelle, l’option idéale est la [pile par défaut](service-fabric-reliable-services-communication-remoting.md) qui permet un modèle de type communication d’acteur. Il s'agit de la façon la plus simple et la plus rapide de prendre en main la communication avec les services. Elle permet une communication RPC fortement typée qui fait abstraction de la plupart des détails de la communication, de sorte que l’appel d’une méthode sur un service distant dans votre code équivaut à appeler une méthode sur une instance d’objet local. Ces classes résument la résolution, la connexion, la nouvelle tentative et la gestion des erreurs lors de la configuration du canal de communication et permettent un appel de méthode basé sur le modèle de communication. Pour cela, utilisez la classe `ServiceRemotingListener` côté serveur et la classe `ServiceProxy` côté client de la communication.

* **HTTP** : pour tirer parti de la flexibilité de la communication HTTP, vous pouvez utiliser des API de communication Service Fabric qui permettent de définir le mécanisme de communication tout en maintenant pour vous la transparence de la logique de résolution, connexion et nouvelle tentative. Par exemple, vous pouvez utiliser l’API Web pour spécifier le mécanisme de communication et tirer parti des classes [`ICommunicationClient` et `ServicePartitionClient`](service-fabric-reliable-services-communication.md) pour configurer la communication.
* **WCF** : si votre code existant utilise WCF comme infrastructure de communication, vous pouvez utiliser `WcfCommunicationListener` pour le côté serveur et les classes `WcfCommunicationClient` et `ServicePartitionClient` pour le client. Pour plus d’informations, consultez cet article sur l’[implémentation WCF de la pile de communication](service-fabric-reliable-services-communication-wcf.md).

* **Autres infrastructures de communication, notamment les protocoles personnalisés** : si vous envisagez d’utiliser une autre infrastructure de communication, notamment des protocoles de communication personnalisés, certaines API de communication Service Fabric vous permettent de rattacher votre pile de communication, tout en maintenant les tâches de découvertes et de connexion transparentes pour vous. Pour plus d’informations, consultez cet article sur le [modèle de communication de service fiable](service-fabric-reliable-services-communication.md).

### Communication entre services écrits dans différents langages de programmation
Toutes les API de communication de Service Fabric ne sont actuellement disponibles qu’en C#. Cela signifie que si un service est écrit dans un autre langage de programmation comme Java ou Node.JS, vous devez écrire votre propre mécanisme de communication.

## Étapes suivantes
* [Pile de communication par défaut fournie par l'infrastructure des services fiables ](service-fabric-reliable-services-communication-remoting.md)
* [Modèle de communication des services fiables](service-fabric-reliable-services-communication.md)
* [Prise en main des services d’API Web de Microsoft Azure Service Fabric avec l’auto-hébergement OWIN](service-fabric-reliable-services-communication-webapi.md)
* [Pile de communication WCF pour Reliable Services](service-fabric-reliable-services-communication-wcf.md)

<!---HONumber=AcomDC_0224_2016-->