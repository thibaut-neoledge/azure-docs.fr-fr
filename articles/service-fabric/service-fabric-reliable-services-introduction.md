<properties
   pageTitle="Présentation du modèle de programmation Service fiable de Service Fabric"
   description="Apprenez-en plus sur le modèle de programmation Service fiable de Service Fabric et commencez à écrire vos propres services."
   services="Service-Fabric"
   documentationCenter=".net"
   authors="masnider"
   manager="timlt"
   editor="jessebenson"/>

<tags
   ms.service="Service-Fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="NA"
   ms.workload="NA"
   ms.date="04/13/2015"
   ms.author="masnider;jesseb"/>

# Présentation des services fiables
Service Fabric simplifie l'écriture et la gestion des services fiables avec et sans état. Ce guide traite des sujets suivants :

1. Le modèle de programmation Service fiable pour les services avec et sans état
2. Les différents choix que vous devez faire lors de l'écriture d'un service fiable
3. Différents scénarios et exemples d'utilisation des Services fiables et comment ils sont écrits.

Les Services fiables sont l'un des modèles de programmation disponibles sous Service Fabric. Pour plus d'informations sur le modèle de programmation Acteurs fiables, consultez l'[introduction](../service-fabric/service-fabric-reliable-actors-introduction.md).

Dans Service Fabric, un service se compose de la configuration, du code d'application et éventuellement, d'un état.

Service Fabric gère la durée de vie des services, depuis la configuration et le déploiement jusqu'à la mise à niveau et la suppression au moyen de la [gestion des applications Service Fabric](../service-fabric/service-fabric-deploy-remove-applications.md).

## Définition des services fiables
Les services fiables vous offrent un modèle de programmation simple, puissant, de niveau supérieur pour vous aider à exprimer ce qui est important pour votre application. Avec le modèle de programmation Service fiable, vous obtenez :

1. Pour les services avec état, le modèle de programmation Service fiable vous permet de stocker l'état de manière cohérente et fiable directement dans le service à l'aide des Collections fiables : un ensemble simple de classes de collection hautement disponible qui sera familier pour toute personne ayant déjà utilisé des collections C#. Traditionnellement, les services nécessitaient des systèmes externes pour la gestion d'état fiable. Avec les Collections fiables, vous pouvez stocker l'état à proximité du calcul avec la disponibilité élevée et la fiabilité que vous attendez de la part de magasins externes hautement disponibles.

2. Un modèle simple pour l'exécution de votre propre code qui ressemble aux modèles de programmation qui vous sont familiers : votre code dispose d'un point d'entrée bien défini et d'un cycle de vie facile à gérer.

3. Un modèle de communication enfichable : utilisez le transport de votre choix, comme HTTP avec l'[API Web](../service-fabric/service-fabric-reliable-services-communication-webapi.md), WebSockets, des protocoles TCP personnalisés, etc. Les Services fiables fournissent d'excellentes options prêtes à l'emploi que vous pouvez utiliser ou vous permettent d'utiliser vos propres options.

## Pourquoi les Services fiables sont-ils différents ?
Les Services fiables dans Service Fabric sont différents des services que vous avez peut-être écrits auparavant. Service Fabric permet de garantir la fiabilité, la disponibilité, la cohérence et l'évolutivité.

+ <u>Fiabilité</u> : votre service continuera de fonctionner, même en cas de problèmes dans l'environnement, comme des pannes d'ordinateurs ou des problèmes réseau.

+ <u>Disponibilité</u> : votre service sera accessible et réactif (il est possible d'avoir des services qui fonctionnent mais qui sont introuvables ou inaccessibles).

+ <u>Évolutivité</u> : les services sont dissociés du matériel spécifique et peuvent croître ou diminuer en fonction des besoins au moyen de l'ajout ou de la suppression de matériel ou de ressources virtuelles. Les services peuvent être facilement partitionnés (surtout dans le cas des services avec état) pour s'assurer que les parties indépendants du service puissent s'adapter et répondre aux défaillances indépendamment. Enfin, Service Fabric encourage les services à être légers en autorisant des milliers de services à être configurés dans un processus unique, plutôt que de nécessiter ou de consacrer des instances entières du système d'exploitation à une instance unique d'une charge de travail spécifique.

+ <u>Cohérence</u> : cela signifie que toutes les informations stockées dans ce service sont cohérentes (ceci ne concerne que les services avec état ; plus d'informations sur ce point ultérieurement)

## Cycle de vie du service
Que votre service soit avec état ou sans état, les Services fiables fournissent un cycle de vie simple qui vous permet de rattacher rapidement votre code et de vous lancer. Pour démarrer votre service, il vous suffit d'implémenter une ou deux méthodes.

+ CreateCommunicationListener : c'est là que le service définit la pile de communications à utiliser. La pile de communications, comme l'[API Web](../service-fabric/service-fabric-reliable-services-communication-webapi.md), est utilisée pour définir les points de terminaison d'écoute pour le service (comment les clients y accèdent), ainsi que l'interaction entre les messages et le reste du code de service.

+ RunAsync : c'est là que votre service peut travailler. Le jeton d'annulation fourni est un signal indiquant quand ce travail doit s'arrêter. Par exemple, si vous avez un service qui doit sans cesse extraire des messages d'une file d'attente ReliableQueue et les traiter, c'est là que se passe ce travail.

Les principaux événements du cycle de vie d'un Service fiable sont les suivants : 1. L'objet de service (élément dérivé de StatelessService ou StatefulService) est construit. 2. La méthode CreateCommunicationListener est appelée, donnant au service une chance de renvoyer un écouteur de communication de son choix. + Notez que cela est facultatif, bien que la plupart des services exposent un système d'extrémité directement. 3. Une fois l'écouteur CommunicationListener créé, il est ouvert + CommunicationListeners ont une méthode nommée Open(), qui est appelée à ce stade et qui renvoie l'adresse d'écoute pour le service. Si votre Service fiable utilise l'un des écouteurs ICommunicationListeners intégrés, alors celui-ci est géré pour vous. 4. Une fois l'écouteur de communication défini sur Open(), RunAsync() sur le service principal est appelé. + Notez que RunAsync est facultatif ; si le service effectue tout son travail directement en réponse aux appels utilisateur uniquement, il est inutile d'implémenter RunAsync().

Quand le service est arrêté (quand il est supprimé ou simplement déplacé d'un emplacement spécifique), l'ordre d'appel est identique. Premièrement Close() est appelé sur CommunicationListener, puis le jeton d'annulation qui a été transmis à RunAsync() est annulé.

## Exemples de services
En se basant sur ce modèle de programmation, observons rapidement deux services pour voir comment ces éléments s'imbriquent.

### Services fiables sans état
Un service sans état est un service dans lequel aucun état n'est conservé, ou l'état qui est présent est entièrement jetable et ne nécessite aucune synchronisation, réplication, persistance ou haute disponibilité.

Prenons pour exemple une calculatrice qui n'a pas de mémoire et qui reçoit tous les termes et les opérations à effectuer simultanément.

Dans ce cas, la tâche RunAsync() du service peut être vide, car le service n'a besoin d'effectuer aucun traitement de tâche en arrière-plan. Quand le service Calculatrice est créé, il renvoie un CommunicationListener (par exemple, l'[API Web](../service-fabric/service-fabric-reliable-services-communication-webapi.md)) qui ouvre un point de terminaison d'écoute sur un port. Ce point de terminaison d'écoute se raccorde aux différentes méthodes (ex : « Add(n1, n2) ») qui définissent l'API publique de la calculatrice.

Lorsqu'un appel est effectué à partir d'un client, la méthode appropriée est appelée et le service Calculatrice effectue les opérations sur les données fournies et renvoie le résultat. Il ne stocke aucun état.

Le fait de ne pas stocker d'état interne rend cet exemple de calculatrice très simple. Cependant, la plupart des services ne sont pas réellement sans état. Au lieu de cela, ils externalisent leur état à un autre magasin (par exemple, toute application Web qui s'appuie sur la conservation de l'état de session dans un cache ou magasin de stockage n'est pas complètement sans état).

Un exemple courant illustrant comment les services sans état sont utilisés dans Service Fabric est celui d'un serveur frontal qui expose l'API publique pour une application Web. Le service frontal communique ensuite avec les services avec état pour terminer la requête d'un utilisateur. Dans ce cas, les appels des clients sont dirigés vers un port connu (comme le port 80) que le service sans état écoute. Ce service sans état reçoit l'appel et détermine si l'appel provient d'une partie de confiance, ainsi que le service auquel l'appel est destiné. Ensuite, le service sans état transfère l'appel à la partition correcte du service avec état et attend une réponse. Lorsqu'il reçoit une réponse, il répond au client d'origine.

### Services fiables avec état
Un service avec état est un service qui doit avoir une partie de l'état sans cesse cohérente et présente pour que le service fonctionne. Prenons pour exemple un service qui calcule constamment une moyenne mobile d'une valeur en fonction de mises à jour qu'il reçoit. Pour ce faire, il doit avoir l'ensemble actuel des requêtes entrantes qu'il doit traiter, ainsi que la moyenne actuelle. Tout service qui récupère, traite et stocke des informations dans un magasin externe (comme les objets Blob Azure ou un magasin Table) est un service avec état : il conserve seulement son état dans le magasin d'état externe.

Actuellement, la plupart des services stockent leur état en externe car le magasin externe fournit la fiabilité, la disponibilité, l'évolutivité et la cohérence pour cet état. Dans Service Fabric, les services avec état ne sont pas obligés de stocker leur état en externe, car Service Fabric s'occupe de ces exigences pour le code de service et l'état du service.

Supposons que nous voulions écrire un service qui a pris des requêtes pour une série de conversions qui devaient être effectuées sur une image, et l'image qui devait être convertie. Ce service renverrait un CommunicationListener (par exemple, WebAPI) qui ouvre un port de communication et permet les soumissions via une API comme `ConvertImage(Image i, IList<Conversion> conversions)`. Dans cette API, le service pourrait prendre les informations et stocker la requête dans une ReliableQueue, puis renvoyer un jeton au client afin d'effectuer le suivi de la requête (dans la mesure où les requêtes peuvent prendre du temps).

Dans ce service, la tâche RunAsync pourrait être plus complexe : le service disposerait d'une boucle à l'intérieur de sa tâche RunAsync pour extraire les requêtes de la file ReliableQueue, effectuer les conversions répertoriées et stocker les résultats dans un ReliableDictionary, de façon que les clients puissent obtenir leurs images converties. Afin de garantir que, même en cas de problème, l'image n'est pas perdue, ce service fiable se sortirait de la file d'attente, exécuterait les conversions et stockerait le résultat dans une Transaction afin que le message ne soit réellement supprimé de la file d'attente et les résultats stockés dans le dictionnaire de résultats qu'une fois les conversions terminées. En cas de problème au milieu (comme l'ordinateur sur lequel cette instance de code est exécutée), la requête reste dans la file d'attente jusqu'à ce qu'elle soit à nouveau traitée.

Une chose à noter concernant ce service est qu'il ressemble être un service .NET normal. La seule différence est que les structures de données utilisées (ReliableQueue et ReliableDictionary) sont fournies par Service Fabric et elles sont donc plus fiables, disponibles et cohérentes.

## Utilisation des API de Services fiables
Si l'un des éléments suivants correspond aux besoins de votre service d'application, alors vous devez songer à utiliser les API de Services fiables :

- Vous devez fournir un comportement d'application entre plusieurs unités d'état (par exemple, commandes et éléments de ligne de commande)

- L'état de votre application peut être naturellement modélisé sous forme de files d'attente et de dictionnaires fiables

- L'état doit être hautement disponible avec accès à faible latence

- Votre application doit contrôler la concurrence ou la granularité d'opérations traitées sur un ou plusieurs regroupements fiables

- Vous souhaitez gérer les communications ou contrôler le schéma de partitionnement de votre service

- Votre code a besoin d'un environnement d'exécution libre de threads

- Votre application a besoin de créer ou de détruire les files d'attente ou les dictionnaires fiables de manière dynamique lors de l'exécution

- Vous devez contrôler par programme les fonctionnalités de sauvegarde et de restauration fournies par Service Fabric pour l'état* du service

- Votre application doit conserver l'historique des modifications pour ses unités d'état*

- Vous souhaitez développer ou utiliser des fournisseurs d'état personnalisé* développés par une tierce partie

> [AZURE.NOTE]*Fonctionnalités disponibles au moment de la disponibilité générale du Kit de développement logiciel (SDK)


## Étapes suivantes
+ [Démarrage rapide des Services fiables](../service-fabric/service-fabric-reliable-services-quick-start.md)
+ [Découvrir l'utilisation avancée des Services fiables](service-fabric-reliable-services-advanced-usage.md)
+ [Lire le modèle de programmation Acteurs fiables](../service-fabric/service-fabric-reliable-actors-introduction.md)
 

<!---HONumber=July15_HO2-->