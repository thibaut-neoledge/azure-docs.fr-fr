---
title: Présentation du modèle de programmation Reliable Services de Service Fabric | Microsoft Docs
description: Apprenez-en plus sur le modèle de programmation Service fiable de Service Fabric et commencez à écrire vos propres services.
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek; mani-ramaswamy

ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 03/25/2016
ms.author: masnider;vturecek

---
# Présentation de Reliable Services
Azure Service Fabric simplifie l’écriture et la gestion de Reliable Services avec et sans état. Ce document abordera les thèmes suivants :

* Le modèle de programmation Reliable Services pour les services avec et sans état.
* Les choix que vous devez faire lors de l’écriture d’un modèle Reliable Services.
* Des scénarios et exemples d’utilisation du modèle Reliable Services et de son mode d’écriture.

Les Services fiables sont l'un des modèles de programmation disponibles sous Service Fabric. Pour plus d’informations sur le modèle de programmation Reliable Actors, consultez l’article [Présentation du modèle Reliable Actors de Service Fabric](service-fabric-reliable-actors-introduction.md).

Dans Service Fabric, un service se compose de la configuration, du code d’application et éventuellement, d’un état.

Service Fabric gère la durée de vie des services, depuis l’approvisionnement et le déploiement jusqu’à la mise à niveau et la suppression au moyen de la [gestion des applications Service Fabric](service-fabric-deploy-remove-applications.md).

## Définition de Reliable Services
Reliable Services vous offre un modèle de programmation global, simple et puissant qui vous aidera à faire ressortir ce qui est important dans votre application. Avec le modèle de programmation Reliable Services, vous obtenez :

* Pour les services avec état, le modèle de programmation Reliable Services vous permet de stocker de manière cohérente et fiable votre droit d’état dans votre service à l’aide des Collections fiables. Il s’agit d’un ensemble de classes de collection hautement disponibles, qui sera familier à tous ceux qui ont utilisé les collections C#. Traditionnellement, les services nécessitaient des systèmes externes pour la gestion d’état fiable. Avec les Collections fiables, vous pouvez stocker l’état à proximité du calcul avec la haute disponibilité et la fiabilité que vous attendez de la part de magasins externes hautement disponibles, tout en bénéficiant des autres améliorations de latence qu’offre la colocation du calcul et de l’état.
* Un modèle simple pour exécuter votre propre code qui ressemble aux modèles de programmation auxquels vous êtes habitué. Votre code comporte un point d’entrée bien défini, et son cycle de vie est facile à gérer.
* Un modèle de communication enfichable. Utilisez le transport de votre choix, comme HTTP avec l’[API Web](service-fabric-reliable-services-communication-webapi.md), WebSockets, des protocoles TCP personnalisés, etc. Le modèle Reliable Services fournit de remarquables options prêtes à l’emploi que vous pouvez utiliser. Vous pouvez également définir les vôtres.

## En quoi le modèle Reliable Services est-il différent ?
Le modèle Reliable Services de Service Fabric est différent des services que vous avez peut-être écrits auparavant. Service Fabric fournit la fiabilité, la disponibilité, la cohérence et l'évolutivité.

* **Fiabilité** : votre service continue de fonctionner, même dans des environnements non fiables où vos ordinateurs peuvent rencontrer des pannes ou des problèmes réseau.
* **Disponibilité** : votre service est accessible et réactif. (Cela ne signifie pas que vous ne pouvez pas disposer de services introuvables ou inaccessibles à partir de l’extérieur.)
* **Évolutivité** : les services sont dissociés du matériel spécifique, et peuvent croître ou diminuer en fonction des besoins au moyen de l’ajout ou de la suppression de matériel ou de ressources virtuelles. Les services peuvent être facilement partitionnés (surtout dans le cas des services avec état) pour s'assurer que les parties indépendantes du service puissent s'adapter et répondre aux défaillances indépendamment. Enfin, Service Fabric encourage les services à être légers en autorisant des milliers de services à être configurés dans un processus unique, plutôt que de nécessiter ou de consacrer des instances entières du système d'exploitation à une instance unique d'une charge de travail spécifique.
* **Cohérence** : toutes les informations stockées dans ce service sont assurées d’être cohérentes (cela ne concerne que les services avec état ; plus d’informations sur ce point ultérieurement).

## Cycle de vie du service
Que votre service soit avec état ou sans état, Reliable Services fournit un cycle de vie simple qui vous permet de rattacher rapidement votre code et de vous lancer. Il vous suffit d’implémenter une ou deux méthodes pour que votre service soit opérationnel.

* **CreateServiceReplicaListeners/CreateServiceInstanceListeners** : emplacement dans lequel le service définit la pile de communication qu’il souhaite utiliser. La pile de communication, par exemple l’[API Web](service-fabric-reliable-services-communication-webapi.md), définit le point de terminaison d’écoute ou les points de terminaison du service (mode d’accès des clients). Elle définit également comment les messages qui s’affichent finissent par interagir avec le reste du code de service.
* **RunAsync** : emplacement où votre service exécute sa logique métier. Le jeton d'annulation fourni est un signal indiquant quand ce travail doit s'arrêter. Par exemple, si l’un de vos services doit sans cesse extraire des messages d’une File d’attente fiable et les traiter, c’est là que se passe ce travail.

### Démarrage du service
Principaux événements du cycle de vie d’un modèle Reliable Services :

1. L’objet de service (élément dérivé du service sans état ou du service avec état) est construit.
2. La méthode `CreateServiceReplicaListeners`/`CreateServiceInstanceListeners` est appelée, ce qui permet au service de renvoyer un ou plusieurs écouteurs de communication de son choix.
   
   * Notez que cela est facultatif, bien que la plupart des services exposent directement un point de terminaison.
3. Il est ouvert une fois les écouteurs créés.
   
   * Les écouteurs de communication utilisent la méthode `OpenAsync()`, qui est appelée à ce stade et qui renvoie l’adresse d’écoute pour le service. Si votre modèle Reliable Services utilise l’un des écouteurs ICommunicationListeners intégrés, celui-ci est géré pour vous.
4. Une fois l’écouteur de communication ouvert, la méthode `RunAsync()` est appelée sur le service principal.
   
   * Notez que la méthode `RunAsync()` est facultative. Si le service effectue tout son travail directement en réponse aux appels d’utilisateur uniquement, il est inutile d’implémenter `RunAsync()`.

### Arrêt du service
Lorsque le service est en cours d’arrêt (pour être supprimé, mis à niveau ou déplacé), l’ordre d’appel est mis en miroir : tout d’abord, le jeton d’annulation détenu par `RunAsync()` est annulé, puis la méthode `CloseAsync()` est appelée sur les écouteurs de communication.

Il convient de noter quelques points importants sur l’arrêt des services avec état :

* Service Fabric ne promeut pas un autre réplica de votre service à l’état principal tant que les méthodes `CloseAsync` et `RunAsync` n’ont pas été renvoyées. Si vous utilisez un écouteur de communication intégré, la méthode `CloseAsync` est gérée pour vous.
* S’il n’existe pas de limite de temps sur le renvoi de ces méthodes, vous perdez immédiatement la capacité d’écrire dans les Collections fiables, et vous ne pouvez donc pas effectuer de travail réel. Il est recommandé de procéder au renvoi aussi rapidement que possible dès la réception de la demande d’annulation.

## Exemples de service
En se basant sur ce modèle de programmation, observons rapidement deux services pour voir comment ces éléments s'imbriquent.

### Services fiables sans état
Un service sans état est un service dans lequel aucun état n’est conservé, ou l’état qui est présent est entièrement jetable et ne nécessite aucune synchronisation, réplication, persistance ou haute disponibilité.

Prenons pour exemple une calculatrice dépourvue de mémoire et qui reçoit tous les termes et les opérations à effectuer simultanément.

Dans ce cas, la méthode RunAsync() du service peut être vide, car le service n’a besoin d’effectuer aucun traitement de tâche en arrière-plan. Quand le service Calculatrice est créé, il renvoie un ICommunicationListener (par exemple, l’[API Web](service-fabric-reliable-services-communication-webapi.md)) qui ouvre un point de terminaison d’écoute sur un port. Ce point de terminaison d’écoute se raccorde aux différentes méthodes (exemple : « Add(n1, n2) ») qui définissent l’API publique de la calculatrice.

Lorsqu’un appel est effectué à partir d’un client, la méthode appropriée est appelée ; le service Calculatrice effectue les opérations sur les données fournies et renvoie le résultat. Il ne stocke aucun état.

Le fait de ne pas stocker d’état interne rend cet exemple de calculatrice très simple, mais la plupart des services ne sont pas réellement sans état. Ils externalisent leur état vers un autre magasin. (Par exemple, toute application web qui s’appuie sur la conservation de l’état de session dans un magasin de stockage ou un cache n’est pas complètement sans état.)

Un exemple courant illustrant comment les services sans état sont utilisés dans Service Fabric est celui d'un serveur frontal qui expose l'API publique pour une application Web. Le service frontal communique ensuite avec les services avec état pour terminer une demande d’utilisateur. Dans ce cas, les appels des clients sont dirigés vers un port connu, comme le port 80, où le service sans état écoute. Ce service sans état reçoit l’appel et détermine s’il provient d’une partie de confiance, ainsi que le service auquel il est destiné. Le service sans état transfère alors l’appel vers la partition correcte du service avec état et attend une réponse. Lorsqu’il reçoit une réponse, il répond au client d’origine.

### Services fiables avec état
Un service avec état est un service qui doit avoir une partie de l'état sans cesse cohérente et présente pour que le service fonctionne. Prenons pour exemple un service qui calcule constamment une moyenne mobile d’une valeur en fonction des mises à jour qu’il reçoit. Pour ce faire, il doit disposer de l’ensemble actuel des requêtes entrantes qu’il doit traiter, ainsi que la moyenne actuelle. Tout service qui récupère, traite et stocke des informations dans un magasin externe (comme un magasin de tables ou d’objets blob Azure) est un service avec état. Il conserve seulement son état dans le magasin d’état externe.

Actuellement, la plupart des services stockent leur état en externe, car le magasin externe fournit la fiabilité, la disponibilité, l’évolutivité et la cohérence pour cet état. Dans Service Fabric, les services avec état ne sont pas tenus de stocker leur état en externe, car Service Fabric s’occupe de ces exigences pour le code de service et l’état du service.

Exemple : nous voulons écrire un service qui prend des requêtes pour une série de conversions qui doivent être effectuées sur une image, et l’image doit être convertie. Ce service renverrait un CommunicationListener (par exemple, l’API Web) qui ouvre un port de communication et autorise les soumissions via une API comme `ConvertImage(Image i, IList<Conversion> conversions)`. Dans cette API, le service pourrait prendre les informations et stocker la requête dans une File d’attente fiable, puis renvoyer un jeton au client afin d’effectuer le suivi de la requête (dans la mesure où les requêtes peuvent prendre du temps).

Dans ce service, RunAsync pourrait être plus complexe. Le service pourrait disposer d’une boucle à l’intérieur de sa méthode RunAsync pour extraire les requêtes de la file IReliableQueue, effectuer les conversions répertoriées et stocker les résultats dans IReliableDictionary, afin que les clients puissent obtenir leurs images converties. Pour garantir que, même en cas de problème, l’image n’est pas perdue, ce Reliable Service se sortirait de la file d’attente, exécuterait les conversions et stockerait le résultat dans une transaction. Dans ce cas, le message est réellement supprimé de la file d’attente et les résultats sont stockés dans le dictionnaire de résultats lorsque les conversions sont terminées. En cas de problème dans l’intermédiaire (par exemple l’ordinateur sur lequel cette instance de code est exécutée), la requête reste dans la file d’attente jusqu’à ce qu’elle soit à nouveau traitée.

Concernant ce service, il convient de noter qu’il ressemble à un service .NET normal. La seule différence est que les structures de données utilisées (IReliableQueue et IReliableDictionary) sont fournies par Service Fabric ; elles sont donc plus fiables, disponibles et cohérentes.

## Quand utiliser les API Reliable Services
Si l’un des éléments suivants correspond aux besoins de votre service d’application, vous devez songer à utiliser les API Reliable Services :

* Vous devez fournir un comportement d’application entre plusieurs unités d’état (par exemple, commandes et éléments de ligne de commande).
* L’état de votre application peut être naturellement modélisé sous forme de Files d’attente et de Dictionnaires fiables
* L’état doit être hautement disponible avec accès à faible latence.
* Votre application doit contrôler la concurrence ou la granularité des opérations traitées dans une ou plusieurs Collections fiables.
* Vous souhaitez gérer les communications ou contrôler le schéma de partitionnement de votre service.
* Votre code a besoin d’un environnement d’exécution libre de threads.
* Votre application doit créer ou détruire des Files d’attente ou des Dictionnaires fiables de manière dynamique lors de l’exécution.
* Vous devez contrôler par programme les fonctionnalités de sauvegarde et de restauration fournies par Service Fabric pour l’état* du service.
* Votre application doit conserver l’historique des modifications pour ses unités d’état*.
* Vous souhaitez développer ou utiliser des fournisseurs d’état personnalisé* développés par une tierce partie.

> [!NOTE]
> *Fonctionnalités disponibles au moment de la disponibilité générale du Kit de développement logiciel (SDK).
> 
> 

## Étapes suivantes
* [Démarrage rapide de Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Utilisation avancée de Reliable Services](service-fabric-reliable-services-advanced-usage.md)
* [Modèle de programmation Reliable Actors](service-fabric-reliable-actors-introduction.md)

<!---HONumber=AcomDC_0406_2016-->