---
title: "Vue d’ensemble du cycle de vie de Reliable Services dans Azure Service Fabric | Microsoft Docs"
description: "En savoir plus sur les différents événements de cycle de vie de Reliable Services dans Service Fabric"
services: Service-Fabric
documentationcenter: java
author: PavanKunapareddyMSFT
manager: timlt
ms.assetid: 
ms.service: Service-Fabric
ms.devlang: java
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/30/2017
ms.author: pakunapa;
ms.openlocfilehash: 80eb68346dd05c256c60725eb082aa0651fe7cbd
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: fr-FR
ms.lasthandoff: 10/11/2017
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
  * La méthode runAsync du service est appelée, ce qui permet au service d’exécuter des tâches de longue durée ou d’arrière-plan
* Lors de l’arrêt
  * Le jeton d’annulation passé à runAsync est annulé et les écouteurs sont fermés
  * Une fois cette opération terminée, l’objet de service lui-même est détruit

Il existe plus d’informations sur l’ordre exact de ces événements. En particulier, l’ordre des événements peut varier légèrement selon que le Reliable Service est un service sans état ou avec état. En outre, pour les services avec état, nous devons gérer le scénario d’échange principal. Au cours de cette séquence, le rôle principal est transféré vers un autre réplica (ou revient) sans que le service ne s’arrête. Enfin, nous devons penser aux conditions d’erreur ou d’échec.

## <a name="stateless-service-startup"></a>Démarrage de service sans état
Le cycle de vie d’un service sans état est assez simple. Voici l’ordre des événements :

1. Le service est construit
2. Ensuite, deux choses se produisent en parallèle :
    - `StatelessService.createServiceInstanceListeners()` est appelée et les écouteurs retournés sont ouverts (`CommunicationListener.openAsync()` est appelée sur chaque écouteur)
    - La méthode runAsync (`StatelessService.runAsync()`) du service est appelée
3. Si elle est présente, la méthode onOpenAsync propre au service est appelée (en l’occurrence, c’est `StatelessService.onOpenAsync()` qui est appelée). Il s’agit d’un remplacement rare, mais il est disponible).

Il est important de noter qu’il n’existe pas d’ordre particulier entre les appels pour créer et ouvrir les écouteurs, et runAsync. Les écouteurs peuvent s’ouvrir avant que la méthode runAsync ne soit démarrée. De même, runAsync peut être appelée avant que les écouteurs de communication ne soient ouverts ou même construits. Si une synchronisation est nécessaire, elle est considérée comme un exercice à exécuter par le responsable d’implémentation. Solutions courantes :

* Parfois, les écouteurs ne peuvent pas fonctionner avant que d’autres informations ne soient créées ou un travail effectué. Pour les services sans état, ce travail peut généralement s’exécuter dans le constructeur du service, au cours de l’appel `createServiceInstanceListeners()`, ou dans le cadre de la construction de l’écouteur lui-même.
* Parfois, le code de runAsync ne démarre pas tant que les écouteurs ne sont pas ouverts. Dans ce cas, une coordination supplémentaire est nécessaire. Une solution courante consiste à utiliser un indicateur dans les écouteurs, indiquant quand ceux-ci ont terminé, ce qui est vérifié dans runAsync avant de poursuivre le travail réel.

## <a name="stateless-service-shutdown"></a>Arrêt de service sans état
Lorsque vous arrêtez un service sans état, le même modèle est suivi dans l’ordre inverse :

1. En parallèle
    - Les écouteurs ouverts sont fermés (`CommunicationListener.closeAsync()` est appelée sur chaque écouteur)
    - Le jeton d’annulation passé à `runAsync()` est annulé (en vérifiant que la propriété `isCancelled` du jeton d’annulation retourne la valeur true et que, si elle est appelée, la méthode `throwIfCancellationRequested` du jeton lève une `CancellationException`).
2. Lorsque `closeAsync()` se termine sur chaque écouteur et que `runAsync()` se termine également, la méthode `StatelessService.onCloseAsync()` du service est appelée, le cas échéant (il s’agit à nouveau d’un remplacement rare).
3. Lorsque `StatelessService.onCloseAsync()` se termine, l’objet de service est détruit

## <a name="notes-on-service-lifecycle"></a>Remarques sur le cycle de vie du service
* La méthode `runAsync()` et l’appel `createServiceInstanceListeners` sont facultatifs. Un service peut avoir l’un des deux, les deux ou aucun. Par exemple, si le service effectue tout son travail en réponse aux appels d’utilisateur, il est inutile d’implémenter `runAsync()`. Seuls les écouteurs de communication et le code associé sont nécessaires. De même, la création et le renvoi d’écouteurs de communication sont facultatifs, car le service peut n’avoir que du travail en arrière-plan à exécuter et n’a donc besoin d’implémenter que `runAsync()`
* Il est valide pour un service de terminer `runAsync()` correctement et d’en revenir. Cela n’est pas considéré comme une condition d’échec et correspond à la fin du travail en arrière-plan du service. Pour les Reliable Services avec état, `runAsync()` est appelée à nouveau si le service a été rétrogradé depuis la fonction principale, puis promu de nouveau à la fonction principale.
* Si un service quitte `runAsync()` en levant une exception inattendue, il s’agit d’un échec, l’objet de service est arrêté et une erreur d’intégrité est signalée.
* S’il n’existe pas de limite de temps sur le retour de ces méthodes, vous perdez immédiatement la possibilité d’écrire, et vous ne pouvez donc pas effectuer de travail réel. Il est recommandé de procéder au renvoi aussi rapidement que possible dès la réception de la demande d’annulation. Si votre service ne répond pas à ces appels d’API dans un délai raisonnable, Service Fabric peut mettre fin à votre service. En général, cela se produit uniquement lors de mises à niveau d’application ou lorsqu’un service est en cours de suppression. Par défaut, ce délai d’attente est de 15 minutes.
* Les échecs dans le chemin d’accès `onCloseAsync()` entraînent l’appel de `onAbort()` qui constitue une opportunité de dernière chance pour le service de nettoyer et de libérer les ressources demandées.

> [!NOTE]
> Les services fiables avec état ne sont pas encore pris en charge par Java.
>
>

## <a name="next-steps"></a>Étapes suivantes
* [Présentation de Reliable Services](service-fabric-reliable-services-introduction.md)
* [Démarrage rapide de Reliable Services](service-fabric-reliable-services-quick-start.md)
* [Utilisation avancée de Reliable Services](service-fabric-reliable-services-advanced-usage.md)
