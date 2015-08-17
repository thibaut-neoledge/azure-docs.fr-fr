<properties
   pageTitle="Microsoft Azure Service Fabric - Comment communiquer avec les services"
   description="Cet article décrit comment vous connecter aux services et communiquer avec eux dans les applications Service Fabric."
   services="service-fabric"
   documentationCenter=".net"
   authors="kunaldsingh"
   manager="timlt"
   editor=""/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/29/2015"
   ms.author="kunalds"/>


# Communication avec les services
Dans le monde des microservices, la solution globale est composée de nombreux services différents qui effectuent chacun une tâche spécialisée. Ces microservices communiquent entre eux pour activer le flux de travail de bout en bout. Il existe également des applications clientes qui se connectent aux services et communiquent avec eux. Ce document explique comment Service Fabric vous permet de facilement configurer la communication avec les services que vous écrivez avec Service Fabric.

## Concepts clés
Voici certains concepts clés que vous devez prendre en compte quand vous configurez la communication avec les services.
### La communication est représentée sous la forme client-serveur
Les API de communication de Service Fabric représentent le type d'interaction client-service, même s'il s'agit de deux services s'exécutant dans le même cluster. Un service cible contacté par un client ou un autre service agit comme un serveur et écoute les demandes entrantes. Le client, qui peut être un autre service du cluster, se connecte au serveur et lui passe des appels.
### Déplacement des services
Dans un système distribué, les instances de service que vous exécutez peuvent passer d'un ordinateur à l'autre au fil du temps pour diverses raisons, par exemple, pour l'équilibrage de la charge dans une configuration de mesures de charge pour l'équilibrage des ressources, ou en raison de mises à niveau, de basculements, de mise à l'échelle et d'autres diverses situations. Ce comportement peut entraîner la possible modification des adresses de point de terminaison d'une instance de service. Par conséquent, pour configurer la communication avec l'instance de service, la boucle suivante doit être exécutée. Ces détails sont transparents pour vous si vous utilisez les API de communication fournies par Service Fabric.

* **Résolution** : toutes les instances de services dans Service Fabric ont des URI uniques, par exemple, « fabric:/MyApplication/MyService », qui ne changent pas au fil du temps. Chaque instance de service expose également des points de terminaison qui peuvent changer quand les instances de service sont déplacées. C'est le même principe que pour les sites web, qui ont des URL constantes, mais dont l'adresse IP peut changer. C'est aussi vrai pour le DNS du World Wide Web qui résout des URL de site web en adresses IP. La plateforme Service Fabric possède également un service système qui résout les URI en points de terminaison. Cette étape implique la résolution de l'URI de l'instance de service en un point de terminaison.

* **Connexion** : une fois que l'URI du service a été résolu en adresses de point de terminaison, la prochaine étape consiste à tenter une connexion au service. Cette connexion peut échouer si l'adresse de point de terminaison a été modifiée à la suite d'un mouvement de service, lequel, par exemple, peut être dû à une défaillance d'ordinateur ou à l'équilibrage de la charge.

* **Nouvelle tentative** : si la tentative de connexion échoue, les étapes précédentes de résolution et de connexion doivent être réessayées, et ce cycle se répète jusqu'à ce que la connexion aboutisse.

## Choix de l'API de communication à utiliser
Dans le cadre de Service Fabric, nous fournissons quelques options pour les API de communication et le choix de l'API dépend du modèle de programmation utilisé, de la structure de communication et du langage de programmation dans lequel vos services sont écrits.
### Communication des acteurs fiables
Pour les services écrits à l'aide d'une API d'acteurs fiables, tous les détails de communication sont transparents et la communication prend tout simplement la forme d'appels de méthode sur ActorProxy.

### Options de communication des services fiables
Vous avez le choix entre deux options si votre service a été écrit à l'aide d'une API de services fiables. L'utilisation d'une API de communication Service Fabric spécifique dépend du protocole de communication choisi.

* **Je peux utiliser n'importe quel protocole de communication et je veux un système rapidement opérationnel** : si vous pouvez choisir n'importe quelle infrastructure de communication, l'option idéale est d'utiliser la [pile par défaut](service-fabric-reliable-services-communication-default.md), qui autorise un modèle de type communication d'acteur. Il s'agit de la façon la plus simple et la plus rapide de prendre en main la communication avec les services. Elle permet une communication RPC fortement typée qui fait abstraction de la plupart des détails de la communication, de sorte que l'appel d'une méthode sur un service distant dans votre code équivaut à appeler une méthode sur une instance d'objet local. Ces classes résument la résolution, la connexion, la nouvelle tentative et la gestion des erreurs lors de la configuration du canal de communication et permettent un appel de méthode basé sur le modèle de communication. Pour cela, la classe `ServiceCommunicationListener` doit être utilisée côté serveur et la classe `ServiceProxy` côté client de la communication.

* **HTTP** : pour tirer parti de la flexibilité de la communication HTTP, vous pouvez utiliser des API de communication Service Fabric qui permettent de définir le mécanisme de communication tout en maintenant pour vous la transparence de la logique de résolution, connexion et nouvelle tentative. Par exemple, vous pouvez utiliser l'API Web pour spécifier le mécanisme de communication et tirer parti des classes [`ICommunicationClient` et `ServicePartitionClient`](service-fabric-reliable-services-communication.md) pour configurer la communication.
* **WCF** : si votre code existant utilise WCF comme infrastructure de communication, vous pouvez utiliser WcfCommunicationListener pour le côté serveur et les classes WcfCommunicationClient et ServicePartitionClient pour le client. Pour plus d'informations, consultez [cet article](service-fabric-reliable-services-communication-wcf.md).

* **Autres infrastructures de communication, notamment les protocoles personnalisés** : si vous envisagez d'utiliser une autre infrastructure de communication, notamment des protocoles de communication personnalisés, certaines API de communication Service Fabric vous permettent de rattacher votre pile de communication, tout en maintenant les tâches de découvertes et de connexion transparentes pour vous. Consultez [cet article](service-fabric-reliable-services-communication.md) pour plus de détails.

### Communication entre services écrits dans différents langages de programmation
Toutes les API de communication ServiceFabric sont actuellement disponibles uniquement en C#, par conséquent, si un service est écrit dans un autre langage de programmation comme Java ou Node.JS, vous devez écrire votre propre mécanisme de communication.

## Étapes suivantes
* [Pile de communication par défaut fournie par l'infrastructure des services fiables ](service-fabric-reliable-services-communication-default.md)
* [Modèle de communication des services fiables](service-fabric-reliable-services-communication.md)
* [Prise en main des services d'API Web de Microsoft Azure Service Fabric avec l'auto-hébergement OWIN](service-fabric-reliable-services-communication-webapi.md)
* [Pile de communication WCF pour les services fiables](service-fabric-reliable-services-communication-wcf.md)
 

<!---HONumber=August15_HO6-->