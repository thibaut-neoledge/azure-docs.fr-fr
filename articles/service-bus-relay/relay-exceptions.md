---
title: "Exceptions Microsoft Azure Relay et résolution | Microsoft Docs"
description: "Liste des exceptions Azure Relay et des actions suggérées."
services: service-bus-relay
documentationcenter: na
author: jtaubensee
manager: timlt
editor: 
ms.assetid: 5f9dd02c-cce0-43b3-8eb8-744f0c27f38c
ms.service: service-bus-relay
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 02/02/2017
ms.author: sethm;jotaub
translationtype: Human Translation
ms.sourcegitcommit: bc71995deef3f9795ebd33d9642c7ffa1995021b
ms.openlocfilehash: 346851226dce8ca8e48d3ae291b97e6e43d1ae03


---
# <a name="relay-exceptions"></a>Exceptions Relay

Cet article répertorie certaines exceptions générées par les API Microsoft Azure Relay. Cette référence est susceptible de changer, donc consultez-la régulièrement.

## <a name="exception-categories"></a>Catégories d'exceptions

Les API Relay génèrent des exceptions qui peuvent être classées dans les catégories suivantes, accompagnées de l’action associée que vous pouvez mettre en place pour les résoudre.

1. Erreur de codage utilisateur ([System.ArgumentException](https://msdn.microsoft.com/library/system.argumentexception.aspx), [System.InvalidOperationException](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx), [System.OperationCanceledException](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx), [System.Runtime.Serialization.SerializationException](https://msdn.microsoft.com/library/system.runtime.serialization.serializationexception.aspx)). Action générale : essayez de corriger le code avant de poursuivre.
2. Erreur d’installation ou de configuration ([Microsoft.ServiceBus.Messaging.MessagingEntityNotFoundException](/dotnet/api/microsoft.servicebus.messaging.messagingentitynotfoundexception), [System.UnauthorizedAccessException](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx). Action générale : révisez la configuration et modifiez-la si besoin.
3. Exceptions temporaires ([Microsoft.ServiceBus.Messaging.MessagingException](/dotnet/api/microsoft.servicebus.messaging.messagingexception), [Microsoft.ServiceBus.Messaging.ServerBusyException](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception), [Microsoft.ServiceBus.Messaging.MessagingCommunicationException](/dotnet/api/microsoft.servicebus.messaging.messagingcommunicationexception.aspx)). Action générale : relancez l'opération ou avertissez les utilisateurs.
4. Autres exceptions ([System.Transactions.TransactionException](https://msdn.microsoft.com/library/system.transactions.transactionexception.aspx), [System.TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx), [Microsoft.ServiceBus.Messaging.MessageLockLostException](/dotnet/api/microsoft.servicebus.messaging.messagelocklostexception), [Microsoft.ServiceBus.Messaging.SessionLockLostException](/dotnet/api/microsoft.servicebus.messaging.sessionlocklostexception)). Action générale : propre au type d’exception ; reportez-vous au tableau de la section suivante. 

## <a name="exception-types"></a>Types d'exceptions

Le tableau suivant répertorie les types d'exceptions de la messagerie, leurs causes et les propositions d'actions que vous pouvez effectuer.

| **Type d'exception** | **Description** | **Action suggérée** | **Remarques sur la nouvelle tentative automatique ou immédiate** |
| --- | --- | --- | --- |
| [Délai d'expiration](https://msdn.microsoft.com/library/system.timeoutexception.aspx) |Le serveur n'a pas répondu à l'opération demandée dans le délai spécifié qui est contrôlé par le paramètre [OperationTimeout](/dotnet/api/microsoft.servicebus.messaging.messagingfactorysettings.operationtimeout). Le serveur peut avoir terminé l'opération demandée. Cela peut se produire en raison de délais sur le réseau ou autre infrastructure. |Vérifiez la cohérence de l'état du système et réessayez si nécessaire. Consultez [Exceptions au délai d’expiration](#timeoutexception). |Dans certains cas, l'exécution d'une nouvelle tentative peut aider ; ajouter une logique de nouvelle tentative au code. |
| [Opération non valide](https://msdn.microsoft.com/library/system.invalidoperationexception.aspx) |L'opération utilisateur demandée n'est pas autorisée sur le serveur ou le service. Consultez le message de l'exception pour obtenir plus d'informations. Par exemple, le paramètre [Complete](/dotnet/api/microsoft.servicebus.messaging.brokeredmessage.complete) génère cette exception si le message a été reçu en mode **ReceiveAndDelete** . |Vérifiez le code et consultez la documentation. Assurez-vous que l'opération demandée est valide. |Une nouvelle tentative ne sera pas bénéfique. |
| [Opération annulée](https://msdn.microsoft.com/library/system.operationcanceledexception.aspx) |Une tentative est effectuée pour appeler une opération sur un objet qui a déjà été fermé, abandonné ou supprimé. Dans de rares cas, la transaction ambiante est déjà supprimée. |Vérifiez le code et assurez-vous qu'il n'appelle pas d'opérations sur un objet supprimé. |Une nouvelle tentative ne sera pas bénéfique. |
| [Accès non autorisé](https://msdn.microsoft.com/library/system.unauthorizedaccessexception.aspx) |L'objet [TokenProvider](/dotnet/api/microsoft.servicebus.tokenprovider) n'a pas pu obtenir de jeton, le jeton n'est pas valide ou le jeton ne contient pas les revendications requises pour effectuer l'opération. |Assurez-vous que le fournisseur de jetons est créé avec les valeurs correctes. Vérifiez la configuration du service de contrôle d'accès (ACS). |Dans certains cas, l'exécution d'une nouvelle tentative peut aider ; ajouter une logique de nouvelle tentative au code. |
| [Exception d’argument](https://msdn.microsoft.com/library/system.argumentexception.aspx)<br /> [Argument Null](https://msdn.microsoft.com/library/system.argumentnullexception.aspx)<br />[Argment hors plage](https://msdn.microsoft.com/library/system.argumentoutofrangeexception.aspx) |Un ou plusieurs des arguments fournis à la méthode ne sont pas valides.<br /> L’URI fourni à [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Ceate](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) contient un ou plusieurs segments de chemin d’accès.<br /> Le schéma d’URI fourni à [NamespaceManager](/dotnet/api/microsoft.servicebus.namespacemanager) ou [Ceate](/dotnet/api/microsoft.servicebus.messaging.messagingfactory.create) n’est pas valide. <br />La valeur de la propriété est supérieure à 32 ko. |Vérifiez le code appelant et assurez-vous que les arguments sont corrects. |Une nouvelle tentative ne sera pas bénéfique. |
| [Serveur occupé](/dotnet/api/microsoft.servicebus.messaging.serverbusyexception) |Le service n'est pas en mesure de traiter la demande pour l'instant. |Le client peut attendre pendant une période de temps, puis recommencer l'opération. |Le client peut réessayer après un certain temps. Si une nouvelle tentative provoque une exception différente, vérifiez le comportement de nouvelle tentative de cette exception. |
| [Quota dépassé](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) |L'entité de messagerie a atteint sa taille maximale autorisée. |Créez de l'espace dans l'entité en recevant des messages à partir de l'entité ou de ses files d'attente secondaires. Consultez [QuotaExceededException](#quotaexceededexception). |Une nouvelle tentative peut aider si des messages ont été supprimés entre-temps. |
| [Taille des messages dépassée](/dotnet/api/microsoft.servicebus.messaging.messagesizeexceededexception) |Une charge utile de message dépasse la limite de 256 Ko. Notez que la limite de 256 Ko correspond à la taille totale du message, qui peut inclure des propriétés système et toute surcharge .NET. |Réduisez la taille de la charge utile de message, puis recommencez l'opération. |Une nouvelle tentative ne sera pas bénéfique. |

## <a name="quotaexceededexception"></a>QuotaExceededException

[QuotaExceededException](/dotnet/api/microsoft.servicebus.messaging.quotaexceededexception) indique que le quota d’une entité spécifique a été dépassé.

Pour Relay, cette exception encapsule l’exception [System.ServiceModel.QuotaExceededException](https://msdn.microsoft.com/library/system.servicemodel.quotaexceededexception.aspx), qui indique que le nombre maximal d’écouteurs est dépassé pour ce point de terminaison. Cela est indiqué dans la valeur **MaximumListenersPerEndpoint** du message d’exception.

## <a name="timeoutexception"></a>TimeoutException
Une [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx) indique qu’une opération lancée par l’utilisateur dépasse le délai d’expiration de l’opération. 

Vous devez vérifier la valeur de la propriété [ServicePointManager.DefaultConnectionLimit](https://msdn.microsoft.com/library/system.net.servicepointmanager.defaultconnectionlimit) car cette limite, si elle est atteinte, peut également entraîner une exception [TimeoutException](https://msdn.microsoft.com/library/system.timeoutexception.aspx).

Pour Relay, vous pouvez recevoir des exceptions de délai d’attente lors de la première ouverture d’une connexion d’expéditeur de relais. Deux causes courantes peuvent être à l’origine de cette exception :

1. La valeur [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) est peut-être trop petite (même une fraction de seconde).
2. Le ou les écouteurs de relais locaux ne sont peut-être pas réactifs (ou peuvent rencontrer des problèmes de règles de pare-feu interdisant aux écouteurs d’accepter de nouvelles connexions client) et la valeur [OpenTimeout](https://msdn.microsoft.com/library/wcf.opentimeout.aspx) est inférieure à environ 20 secondes.

Par exemple :

```
'System.TimeoutException’: The operation did not complete within the allotted timeout of 00:00:10. The time allotted to this operation may have been a portion of a longer timeout.
```

### <a name="common-causes"></a>Causes courantes
Il existe deux causes communes pour cette erreur : une configuration incorrecte ou une erreur de service temporaire.

1. **Configuration incorrecte**
    Le délai d’expiration de l’opération est peut-être trop court pour un bon fonctionnement. La valeur par défaut du délai d'expiration de l'opération dans le Kit de développement logiciel (SDK) client est de 60 secondes. Vérifiez si votre code définit une valeur trop petite. Notez que la condition du réseau et l'utilisation du processeur peuvent affecter le temps nécessaire à la réalisation d’une opération particulière. Par conséquent, le délai d'expiration de l'opération ne doit pas être défini sur une valeur très faible.
2. **Erreur temporaire de service**
    Il arrive parfois que Relay subisse des retards de traitement des requêtes, par exemple pendant les périodes de trafic élevé. Dans ce cas, vous pouvez réessayer l'opération après un certain délai, jusqu'à ce que l'opération réussisse. Si la même opération échoue encore après plusieurs tentatives, consultez le [Site d'état des services Azure](https://azure.microsoft.com/status/) pour voir s'il existe des interruptions de service connues.

## <a name="next-steps"></a>Étapes suivantes :
* [FAQ Relay](relay-faq.md)
* [Créer un espace de noms](relay-create-namespace-portal.md)
* [Prise en main de .NET](relay-hybrid-connections-dotnet-get-started.md)
* [Prise en main de Node](relay-hybrid-connections-node-get-started.md)




<!--HONumber=Feb17_HO1-->


