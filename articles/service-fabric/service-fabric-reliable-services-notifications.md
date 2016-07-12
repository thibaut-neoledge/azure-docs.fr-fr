<properties
   pageTitle="Notifications Reliable Service | Microsoft Azure"
   description="Documentation conceptuelle relative aux notifications d’un service fiable Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="masnider,vturecek"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="06/24/2016"
   ms.author="mcoskun"/>

# Notifications Reliable Services

Les notifications permettent aux clients de suivre les modifications apportées à l’objet qui les intéresse. Il existe deux types d’objet prenant en charge la notification : **Gestionnaire d’état fiable** et **Dictionnaire fiable**.

Raisons courantes d’utiliser les notifications

- Construction de vues matérialisées comme des index secondaires ou des vues filtrées agrégées de l’état du réplica. Par exemple, un index trié de toutes les clés d’un dictionnaire fiable.
- Envoi de données de surveillance comme le nombre d’utilisateurs ajoutés dans la dernière heure.

Les notifications sont déclenchées dans le cadre de l’application d’opérations. Comme elles font partie de l’application de l’opération, il est important de noter que la gestion des notifications doit se terminer aussi rapidement que possible et qu’aucune opération coûteuse ne doit être effectuée dans des événements synchrones.

## Notification du Gestionnaire d’état fiable

Le Gestionnaire d’état fiable fournit des notifications pour les événements suivants

- Transaction
    - Validation
- Gestionnaire d’état
    - Reconstruction
    - Ajout d’un état fiable
    - Suppression d’un état fiable

Le Gestionnaire d’état fiable effectue le suivi à la volée des transactions en cours. Le seul changement d’état de transaction qui déclenchera une notification est une transaction en cours de validation.

Le Gestionnaire d’état fiable gère une collection d’états fiables comme Dictionnaire fiable et File d’attente fiable. Le Gestionnaire d’état fiable déclenche des notifications lorsque cette collection est modifiée : un état fiable est ajouté, supprimé, ou la collection entière a été reconstruite. La collection du Gestionnaire d’état fiable est reconstruite dans trois cas

- Récupération : lorsqu’un réplica démarre, il récupère son état précédent à partir du disque. À la fin de la récupération, il déclenche un événement **NotifyStateManagerChangedEventArgs** qui contient l’ensemble des données **IReliableState** récupérées.
- Copie complète : avant qu’un réplica puisse rejoindre le jeu de configuration, il doit d’abord être créé. Dans certains cas, il peut être nécessaire d’appliquer une copie complète de l’état du Gestionnaire d’état fiable du réplica principal au réplica secondaire inactif. Le Gestionnaire d’état fiable sur le réplica secondaire déclenche un événement **NotifyStateManagerChangedEventArgs** qui contient le jeu de données **IReliableState** acquis à partir du site principal.
- Restauration : dans les scénarios de récupération d’urgence, l’état du réplica peut être restauré à partir d’une sauvegarde à l’aide de **RestoreAsync**. Dans ces cas, le Gestionnaire d’état fiable sur le réplica principal déclenche un événement **NotifyStateManagerChangedEventArgs** qui contient le jeu de données **IReliableState** restauré à partir de la sauvegarde.

### Utilisation des notifications du Gestionnaire d’état fiable
Pour vous inscrire aux notifications de transactions et/ou notifications du Gestionnaire d’état, l’utilisateur doit s’inscrire auprès de l’événement **TransactionChanged** ou **StateManagerChanged** sur le Gestionnaire d’état fiable, respectivement. Le constructeur de votre élément StatefulService est un endroit courant où enregistrer ces gestionnaires d’événements. En effet, en s’inscrivant sur le constructeur, le client est assuré de ne manquer aucune notification suite à un changement survenu pendant la vie de l’élément **IReliableStateManager**.

```C#
public MyService(StatefulServiceContext context)
    : base(MyService.EndpointName, context, CreateReliableStateManager(context))
{
    this.StateManager.TransactionChanged += this.OnTransactionChangedHandler;
    this.StateManager.StateManagerChanged += this.OnStateManagerChangedHandler;
}
```

Le gestionnaire d’événements **TransactionChanged** utilise **NotifyTransactionChangedEventArgs** pour fournir des détails sur l’événement. Il contient la propriété Action (par exemple, **NotifyTransactionChangedAction.Commit**) qui spécifie le type de modification, et la propriété Transaction qui fournit une référence à la transaction qui a changé.

>[AZURE.NOTE] Aujourd'hui, les événements TransactionChanged sont déclenchés uniquement si la propriété Transaction est validée ; par conséquent, la propriété Action sera égale à NotifyTransactionChangedAction.Commit. Toutefois, d’autres propriétés Actions peuvent être ajoutées dans les futures mises à jour. Par conséquent, nous vous recommandons de choisir la propriété Action et de traiter l’événement uniquement s’il correspond à ce que vous attendez.

Voici un exemple de gestionnaire d’événements **TransactionChanged**

```C#
private void OnTransactionChangedHandler(object sender, NotifyTransactionChangedEventArgs e)
{
    if (e.Action == NotifyTransactionChangedAction.Commit)
    {
        this.lastCommitLsn = e.Transaction.CommitSequenceNumber;
        this.lastTransactionId = e.Transaction.TransactionId;

        this.lastCommittedTransactionList.Add(e.Transaction.TransactionId);
    }
}
```

Le gestionnaire d’événements **StateManagerChanged** utilise **NotifyStateManagerChangedEventArgs** pour fournir des détails sur l’événement. **NotifyStateManagerChangedEventArgs** comporte deux sous-classes : NotifyStateManagerRebuildEventArgs et NotifyStateManagerSingleEntityChangedEventArgs. La propriété Action dans **NotifyStateManagerChangedEventArgs** devrait être utilisée pour convertir l’événement **NotifyStateManagerChangedEventArgs** dans la sous-classe correcte

- NotifyStateManagerChangedAction.Rebuild: NotifyStateManagerRebuildEventArgs
- NotifyStateManagerChangedAction.Add et Remove: NotifyStateManagerSingleEntityChangedEventArgs

Voici un exemple de gestionnaire de notification **StateManagerChanged**

```C#
public void OnStateManagerChangedHandler(object sender, NotifyStateManagerChangedEventArgs e)
{
    if (e.Action == NotifyStateManagerChangedAction.Rebuild)
    {
        this.ProcessStataManagerRebuildNotification(e);

        return;
    }

    this.ProcessStateManagerSingleEntityNotification(e);
}
```

## Notifications Dictionnaire fiable

Dictionnaire fiable fournit une notification pour les événements suivants

- Reconstruction : appelée lorsque **ReliableDictionary** a récupéré son état à partir du disque local, d’une copie sur le serveur principal ou d’une sauvegarde.
- Effacement : appelée lorsque l’état de **ReliableDictionary** a été désactivé à l’aide de la méthode **ClearAsync**.
- Ajout : appelée lorsqu’un élément a été ajouté à **ReliableDictionary**.
- Mise à jour : appelée lorsqu’un élément de **IReliableDictionary** a été mis à jour.
- Suppression : appelée lorsqu’un élément de **IReliableDictionary** a été supprimé.

### Utilisation des notifications Dictionnaire fiable
Pour s’inscrire et recevoir des notifications Dictionnaire fiable, l’utilisateur doit s’inscrire à l’aide du gestionnaire d’événements **DictionaryChanaged** sur **IReliableDictionary**. L’ajout d’une notification **ReliableStateManager.StateManagerChanged** est une situation courante au cours de laquelle l’utilisateur s’inscrit à l’aide de ces gestionnaires d’événements. En effet, l’inscription au moment de l’ajout de **IReliableDictionary** à **IReliableStateManager** permet en effet de s’assurer de ne jamais manquer une notification.

```C#
private void ProcessStateManagerSingleEntityNotification(NotifyStateManagerChangedEventArgs e)
{
    var operation = e as NotifyStateManagerSingleEntityChangedEventArgs;

    if (operation.Action == NotifyStateManagerChangedAction.Add)
    {
        if (operation.ReliableState is IReliableDictionary<TKey, TValue>)
        {
            var dictionary = (IReliableDictionary<TKey, TValue>)operation.ReliableState;
            dictionary.RebuildNotificationAsyncCallback = this.OnDictionaryRebuildNotificationHandlerAsync;
            dictionary.DictionaryChanged += this.OnDictionaryChangedHandler;
            }
        }
    }
}
```

>[AZURE.NOTE] ProcessStateManagerSingleEntityNotification est l’exemple de méthode appelée par l’exemple OnStateManagerChangedHandler ci-dessus.

Notez que le code ci-dessus définit IReliableNotificationAsyncCallback et **DictionaryChanged**. Comme **NotifyDictionaryRebuildEventArgs** contient un élément **IAsyncEnumerable** qui doit être énuméré de façon asynchrone, des notifications de reconstruction sont déclenchées via **RebuildNotificationAsyncCallback** au lieu de **OnDictionaryChangedHandler**.

```C#
public async Task OnDictionaryRebuildNotificationHandlerAsync(
    IReliableDictionary<TKey, TValue> origin,
    NotifyDictionaryRebuildEventArgs<TKey, TValue> rebuildNotification)
{
    this.secondaryIndex.Clear();

    var enumerator = e.State.GetAsyncEnumerator();
    while (await enumerator.MoveNextAsync(CancellationToken.None))
    {
        this.secondaryIndex.Add(enumerator.Current.Key, enumerator.Current.Value);
    }
}
```

>[AZURE.NOTE] Dans le code ci-dessus, dans le cadre du traitement de la notification de reconstruction, l’état agrégé est d’abord désactivé. Étant donné que la collection fiable est en cours de reconstruction avec un nouvel état, toutes les notifications précédentes sont sans intérêt.

Le gestionnaire d’événements **DictionaryChanged** utilise **NotifyDictionaryChangedEventArgs** pour fournir des détails sur l’événement. **NotifyDictionaryChangedEventArgs** a cinq sous-classes. La propriété Action dans **NotifyDictionaryChangedEventArgs** devrait être utilisée pour convertir l’événement **NotifyDictionaryChangedEventArgs** dans la sous-classe correcte

- NotifyDictionaryChangedAction.Rebuild: NotifyDictionaryRebuildEventArgs
- NotifyDictionaryChangedAction.Clear: NotifyDictionaryClearEventArgs
- NotifyDictionaryChangedAction.Add et Remove: NotifyDictionaryItemAddedEventArgs
- NotifyDictionaryChangedAction.Update: NotifyDictionaryItemUpdatedEventArgs
- NotifyDictionaryChangedAction.Remove: NotifyDictionaryItemRemovedEventArgs

```C#
public void OnDictionaryChangedHandler(object sender, NotifyDictionaryChangedEventArgs<TKey, TValue> e)
{
    switch (e.Action)
    {
        case NotifyDictionaryChangedAction.Clear:
            var clearEvent = e as NotifyDictionaryClearEventArgs<TKey, TValue>;
            this.ProcessClearNotification(clearEvent);
            return;

        case NotifyDictionaryChangedAction.Add:
            var addEvent = e as NotifyDictionaryItemAddedEventArgs<TKey, TValue>;
            this.ProcessAddNotification(addEvent);
            return;

        case NotifyDictionaryChangedAction.Update:
            var updateEvent = e as NotifyDictionaryItemUpdatedEventArgs<TKey, TValue>;
            this.ProcessUpdateNotification(updateEvent);
            return;

        case NotifyDictionaryChangedAction.Remove:
            var deleteEvent = e as NotifyDictionaryItemRemovedEventArgs<TKey, TValue>;
            this.ProcessRemoveNotification(deleteEvent);
            return;

        default:
            break;
    }
}
```

## Recommandations

- Complétez les événements de notification aussi rapidement que possible.
- N’exécutez aucune opération coûteuse (par exemple des opérations d’E/S) dans le cadre d’événements synchrones.
- Vérifiez le type d’action avant de traiter l’événement. De nouveaux types d’action peuvent être ajoutés par la suite.

Voici quelques points à retenir :
- Les notifications sont déclenchées dans le cadre de l’exécution d’une opération. Par exemple, une notification de restauration, sera déclenchée en tant que dernière étape de la restauration, et cette restauration ne se terminera pas tant que l’événement de notification n’est pas traité.
- Comme les notifications sont déclenchées dans le cadre des opérations d’application, les clients ne voient que les notifications des opérations validées localement. Notez que dans la mesure où les opérations sont garanties uniquement pour être validées localement (en d’autres termes consignées), elles risquent de ne pas pouvoir être annulées par la suite.
- Lors d’une opération de rétablissement, une seule notification sera déclenchée pour chaque opération appliquée. Cela signifie que si une transaction T1 inclut les opérations Create(X), Delete(X), Create(X), vous recevez une notification pour la création de X, une autre pour la suppression de X et encore une autre pour la création de X, dans cet ordre.
- Pour les transactions contenant plusieurs opérations, les opérations seront appliquées dans l’ordre de leur réception sur le serveur principal par l’utilisateur.
- Lors du traitement d’une mauvaise, certaines opérations peuvent être annulées. Des notifications seront déclenchées pour ces opérations d’annulation afin de ramener le réplica à un état stable. Les notifications d’annulation présentent une importante différence en ce sens que les événements avec des clés en double seront agrégés. Par exemple, si l’opération T1 ci-dessus est annulée, l’utilisateur ne verra qu’une seule notification Delete(X).

## Étapes suivantes

- [Démarrage rapide de Reliable Services](service-fabric-reliable-services-quick-start.md)
- [Sauvegarde et restauration de Reliable Services (récupération d’urgence)](service-fabric-reliable-services-backup-restore.md)
- [Référence du développeur pour les Collections fiables](https://msdn.microsoft.com/library/azure/microsoft.servicefabric.data.collections.aspx)

<!---HONumber=AcomDC_0629_2016-->