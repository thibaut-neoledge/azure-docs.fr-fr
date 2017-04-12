---
title: "Vue d’ensemble du cycle de vie de Reliable Services dans Azure Service Fabric | Microsoft Docs"
description: "En savoir plus sur les différents événements de cycle de vie de Reliable Services dans Service Fabric"
services: Service-Fabric
documentationcenter: .net
author: masnider
manager: timlt
editor: vturecek;
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: dotnet
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/05/2017
ms.author: masnider;
translationtype: Human Translation
ms.sourcegitcommit: eeb56316b337c90cc83455be11917674eba898a3
ms.openlocfilehash: 1237f467cf993170eaf1345134fb732d4f0b1171
ms.lasthandoff: 04/03/2017

---

# <a name="reliable-services-lifecycle-overview"></a>Vue d’ensemble du cycle de vie de Reliable Services
> [!div class="op_single_selector"]
> * [C# sur Windows](service-fabric-reliable-services-lifecycle.md)
> * [Java sur Linux](service-fabric-reliable-services-lifecycle-java.md)
>
>

Lorsque l’on songe aux cycles de vie de Reliable Services, les principes de base du cycle de vie sont les plus importants. En général :

* Lors du démarrage
  * Les services sont construits
  * Ils ont l’opportunité de se construire et de renvoyer zéro ou plusieurs écouteurs
  * Les écouteurs retournés sont ouverts, ce qui permet la communication avec le service
  * La méthode RunAsync du service est appelée, ce qui permet au service d’exécuter des tâches de longue durée ou d’arrière-plan
* Lors de l’arrêt
  * Le jeton d’annulation passé à RunAsync est annulé et les écouteurs sont fermés
  * Une fois cette opération terminée, l’objet de service lui-même est détruit

Il existe plus d’informations sur l’ordre exact de ces événements. En particulier, l’ordre des événements peut varier légèrement selon que le Reliable Service est un service sans état ou avec état. En outre, pour les services avec état, nous devons gérer le scénario d’échange principal. Au cours de cette séquence, le rôle principal est transféré vers un autre réplica (ou revient) sans que le service ne s’arrête. Enfin, nous devons penser aux conditions d’erreur ou d’échec.

## <a name="stateless-service-startup"></a>Démarrage de service sans état
Le cycle de vie d’un service sans état est assez simple. Voici l’ordre des événements :

1. Le service est construit
2. Ensuite, deux choses se produisent en parallèle :
    - `StatelessService.CreateServiceInstanceListeners()` est appelée et les écouteurs retournés sont ouverts (`ICommunicationListener.OpenAsync()` est appelée sur chaque écouteur)
    - La méthode RunAsync (`StatelessService.RunAsync()`) du service est appelée
3. Le cas échéant, la méthode OnOpenAsync du service est appelée (c’est-à-dire `StatelessService.OnOpenAsync()` est appelée. Il s’agit d’un remplacement rare, mais il est disponible).

Il est important de noter qu’il n’existe pas d’ordre particulier entre les appels pour créer et ouvrir les écouteurs et RunAsync. Les écouteurs peuvent s’ouvrir avant que la méthode RunAsync ne soit démarrée. De même, la méthode RunAsync peut être appelée avant que les écouteurs de communication ne soient ouverts ou même construits. Si une synchronisation est nécessaire, elle est considérée comme un exercice à exécuter par le responsable d’implémentation. Solutions courantes :

* Parfois, les écouteurs ne peuvent pas fonctionner avant que d’autres informations ne soient créées ou un travail effectué. Pour les services sans état, ce travail peut généralement s’exécuter dans le constructeur du service, au cours de l’appel `CreateServiceInstanceListeners()`, ou dans le cadre de la construction de l’écouteur lui-même.
* Parfois, le code dans RunAsync ne souhaite pas démarrer tant que les écouteurs ne sont pas ouverts. Dans ce cas, une coordination supplémentaire est nécessaire. Une solution courante consiste en un indicateur dans les écouteurs, indiquant quand ceux-ci ont terminé, qui est vérifié dans RunAsync avant de poursuivre le travail réel.

## <a name="stateless-service-shutdown"></a>Arrêt de service sans état
Lorsque vous arrêtez un service sans état, le même modèle est suivi dans l’ordre inverse :

1. En parallèle
    - Les écouteurs ouverts sont fermés (`ICommunicationListener.CloseAsync()` est appelée sur chaque écouteur)
    - Le jeton d’annulation passé à `RunAsync()` est annulé (vérifier que la propriété `IsCancellationRequested` du jeton d’annulation renvoie la valeur true et, si elle est appelée, la méthode `ThrowIfCancellationRequested` du jeton renvoie `OperationCanceledException`)
2. Lorsque `CloseAsync()` se termine sur chaque écouteur et que `RunAsync()` se termine également, la méthode `StatelessService.OnCloseAsync()` du service est appelée, le cas échéant (il s’agit à nouveau d’un remplacement rare).
3. Lorsque `StatelessService.OnCloseAsync()` se termine, l’objet de service est détruit

## <a name="stateful-service-startup"></a>Démarrage de service avec état
Les services avec état ont un modèle semblable aux services sans état, avec quelques modifications. Lors du démarrage d’un service avec état, l’ordre des événements est le suivant :

1. Le service est construit
2. `StatefulServiceBase.OnOpenAsync()` est appelée. (Ceci est rarement remplacé dans le service.)
3. Si le réplica du service en question est le Principal, alors les événements suivants se produisent en parallèle, sinon le service passe à l’étape 4
    - `StatefulServiceBase.CreateServiceReplicaListeners()` est appelée et les écouteurs retournés sont ouverts (`ICommunicationListener.OpenAsync()` est appelée sur chaque écouteur)
    - La méthode RunAsync (`StatefulServiceBase.RunAsync()`) du service est appelée
4. Une fois tous les appels `OpenAsync()` de l’écouteur du réplica terminés et `RunAsync()` démarrée (ou ces étapes ont été ignorées car il s’agit d’un réplica secondaire), `StatefulServiceBase.OnChangeRoleAsync()` est appelée. (Ceci est rarement remplacé dans le service.)

Comme pour les services sans état, il n’y a aucune coordination entre l’ordre dans lequel les écouteurs sont créés et ouverts, et la méthode RunAsync appelée. Les solutions sont similaires, avec un cas supplémentaire : supposons que les appels arrivant sur les écouteurs de communication requièrent des informations conservées dans certaines [Collections fiables](service-fabric-reliable-services-reliable-collections.md) pour fonctionner. Étant donné que les écouteurs de communication peuvent s’ouvrir avant que les collections fiables ne soient accessibles en lecture ou en écriture, et avant que RunAsync ne démarre, une coordination supplémentaire est nécessaire. La solution la plus simple et la plus courante est que les écouteurs de communication retournent un code d’erreur que le client utilise pour relancer la requête.

## <a name="stateful-service-shutdown"></a>Arrêt de service avec état
Comme pour les services sans état, les événements de cycle de vie lors de l’arrêt sont les mêmes que lors du démarrage, mais dans l’ordre inverse. Lorsqu’un service avec état est arrêté, les événements suivants se produisent :

1. En parallèle
    - Les écouteurs ouverts sont fermés (`ICommunicationListener.CloseAsync()` est appelée sur chaque écouteur)
    - Le jeton d’annulation passé à `RunAsync()` est annulé (vérifier que la propriété `IsCancellationRequested` du jeton d’annulation renvoie la valeur true et, si elle est appelée, la méthode `ThrowIfCancellationRequested` du jeton renvoie `OperationCanceledException`)
2. Une fois que `CloseAsync()` se termine sur chaque écouteur et que `RunAsync()` se termine également (ce qui ne doit être nécessaire que si ce réplica de service était le Principal), la méthode `StatefulServiceBase.OnChangeRoleAsync()` du service est appelée. (Ceci est rarement remplacé dans le service.)
3. Une fois la méthode `StatefulServiceBase.OnChangeRoleAsync()` terminée, la méthode `StatefulServiceBase.OnCloseAsync()` est appelée (là encore, il s’agit d’un remplacement rare, mais il est disponible).
3. Une fois `StatefulServiceBase.OnCloseAsync()` terminée, l’objet de service est détruit.

## <a name="stateful-service-primary-swaps"></a>Échanges de réplica principal de service avec état
Pendant l’exécution d’un service avec état, seuls les réplicas principaux de ce service avec état ont leurs écouteurs de communication ouverts et leur méthode RunAsync appelée. Les réplicas secondaires sont construits mais ne reçoivent aucun autre appel. Toutefois, pendant l’exécution d’un service avec état, le réplica principal peut changer. Que cela signifie-t-il en termes des événements du cycle de vie qu’un réplica peut voir ? Le comportement que voit le réplica avec état varie selon qu’il s’agit du réplica qui est rétrogradé ou promu au cours de l’échange.

### <a name="for-the-primary-being-demoted"></a>Pour le réplica principal rétrogradé
Service Fabric nécessite que ce réplica arrête de traiter des messages et quitte tout travail en arrière-plan qu’il exécute. Par conséquent, cette étape est similaire à l’arrêt du service. La différence est que le service n’est pas détruit ou fermé, car il est conservé en tant que réplica secondaire. Les API suivantes sont appelées :

1. En parallèle
    - Les écouteurs ouverts sont fermés (`ICommunicationListener.CloseAsync()` est appelée sur chaque écouteur)
    - Le jeton d’annulation passé à `RunAsync()` est annulé (vérifier que la propriété `IsCancellationRequested` du jeton d’annulation renvoie la valeur true et, si elle est appelée, la méthode `ThrowIfCancellationRequested` du jeton renvoie `OperationCanceledException`)
2. Une fois `CloseAsync()` terminée sur chaque écouteur et `RunAsync()` également terminée, la méthode `StatefulServiceBase.OnChangeRoleAsync()` du service est appelée. (Ceci est rarement remplacé dans le service.)

### <a name="for-the-secondary-being-promoted"></a>Pour le réplica secondaire promu
De même, Service Fabric nécessite que ce réplica commence à écouter les messages sur le réseau (si c’est son rôle) et qu’il démarre les tâches en arrière-plan qui le concernent. Par conséquent, ce processus est similaire à la création du service, à ceci près que le réplica lui-même existe déjà. Les API suivantes sont appelées :

1. En parallèle
    - `StatefulServiceBase.CreateServiceReplicaListeners()` est appelée et les écouteurs retournés sont ouverts (`ICommunicationListener.OpenAsync()` est appelée sur chaque écouteur)
    - La méthode RunAsync (`StatefulServiceBase.RunAsync()`) du service est appelée
4. Une fois tous les appels `OpenAsync()` de l’écouteur du réplica terminés et `RunAsync()` démarrée (ou ces étapes ont été ignorées car il s’agit d’un réplica secondaire), `StatefulServiceBase.OnChangeRoleAsync()` est appelée. (Ceci est rarement remplacé dans le service.)

## <a name="notes-on-service-lifecycle"></a>Remarques sur le cycle de vie du service
* La méthode `RunAsync()` et l’appel `CreateServiceReplicaListeners/CreateServiceInstanceListeners` sont facultatifs. Un service peut avoir l’un des deux, les deux ou aucun. Par exemple, si le service effectue tout son travail en réponse aux appels d’utilisateur, il est inutile d’implémenter `RunAsync()`. Seuls les écouteurs de communication et le code associé sont nécessaires. De même, la création et le renvoi d’écouteurs de communication sont facultatifs, car le service peut n’avoir que du travail en arrière-plan à exécuter et n’a donc besoin d’implémenter que `RunAsync()`
* Il est valide pour un service de terminer `RunAsync()` correctement et d’en revenir. Cela n’est pas considéré comme une condition d’échec et correspond à la fin du travail en arrière-plan du service. Pour les Reliable Services avec état, `RunAsync()` est appelée à nouveau si le service a été rétrogradé depuis la fonction principale, puis promu de nouveau à la fonction principale.
* Si un service quitte `RunAsync()` en levant une exception inattendue, il s’agit d’un échec, l’objet de service est arrêté et une erreur d’intégrité est signalée.
* S’il n’existe pas de limite de temps sur le renvoi de ces méthodes, vous perdez immédiatement la capacité d’écrire dans les Collections fiables, et vous ne pouvez donc pas effectuer de travail réel. Il est recommandé de procéder au renvoi aussi rapidement que possible dès la réception de la demande d’annulation. Si votre service ne répond pas à ces appels d’API dans un délai raisonnable, Service Fabric peut mettre fin à votre service. En général, cela se produit uniquement lors de mises à niveau d’application ou lorsqu’un service est en cours de suppression. Par défaut, ce délai d’attente est de 15 minutes.
* Pour les services avec état, il existe une option supplémentaire sur ServiceReplicaListeners qui leur permet de démarrer sur des réplicas secondaires. Cela est rare, mais la seule différence dans les cycles de vie est que `CreateServiceReplicaListeners()` est appelée (et les écouteurs qui en résultent sont ouverts) même si le réplica est secondaire. De même, si le réplica est ultérieurement converti en un réplica principal, les écouteurs sont fermés, détruits et de nouveaux sont créés et ouverts dans le cadre de la promotion en tant que réplica principal.
* Les échecs dans le chemin d’accès `OnCloseAsync()` entraînent l’appel de `OnAbort()` qui constitue une opportunité de dernière chance pour le service de nettoyer et de libérer les ressources demandées.

## <a name="next-steps"></a>Étapes suivantes
* [Présentation de Reliable Services](service-fabric-reliable-services-introduction.md)
* [Démarrage rapide de Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Utilisation avancée de Reliable Services](service-fabric-reliable-services-advanced-usage.md)

