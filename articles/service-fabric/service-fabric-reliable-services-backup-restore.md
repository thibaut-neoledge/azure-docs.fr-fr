<properties
   pageTitle="Sauvegarde et restauration Reliable Services | Microsoft Azure"
   description="Documentation conceptuelle relative à la sauvegarde et à la restauration Reliable Services Service Fabric"
   services="service-fabric"
   documentationCenter=".net"
   authors="mcoskun"
   manager="timlt"
   editor="subramar,jessebenson"/>

<tags
   ms.service="service-fabric"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="03/03/2016"
   ms.author="mcoskun"/>

# Sauvegarder et restaurer Reliable Services

Azure Service Fabric est une plateforme haute disponibilité qui réplique l’état sur plusieurs nœuds afin de conserver cette haute disponibilité. Ainsi, même si un nœud du cluster échoue, les services continuent à être disponibles. Bien que cette redondance intégrée fournie par la plateforme suffise pour certains, dans d’autres cas, il est souhaitable que le service sauvegarde les données (dans un magasin externe).

Par exemple, un service peut souhaiter sauvegarder des données dans les scénarios suivants :

* En cas de perte permanente d’un cluster Service Fabric complet ou de tous les nœuds exécutant une partition donnée.

* Erreurs d’administration dans le cadre desquelles l’état est accidentellement supprimé ou endommagé. Par exemple, cela peut se produire si un administrateur disposant de privilèges suffisants supprime le service par erreur.

* Bogues dans le service qui provoquent l’altération des données. Par exemple, cela peut se produire lorsqu’une mise à niveau de code de service écrit des données erronées dans une collection fiable. Dans ce cas, le code et les données devront peut-être être restaurés à un état antérieur.

* Traitement des données hors connexion. Il peut être utile de disposer du traitement des données hors connexion pour le décisionnel qui a lieu séparément du service qui génère les données.

La fonctionnalité Sauvegarder/Restaurer permet aux services reposant sur l’API Reliable Services de créer et de restaurer des sauvegardes. Les API de sauvegarde fournies par la plateforme permettent aux sauvegardes de l’état d’une partition d’être effectuées sans bloquer les opérations de lecture ou d’écriture. Les API de restauration permettent à l’état d’une partition d’être restauré à partir d’une sauvegarde choisie.


## Opération de sauvegarde

Le créateur du service contrôle intégralement le moment auquel les sauvegardes sont effectuées et leur emplacement de stockage.

Pour démarrer une sauvegarde, le service doit appeler **IReliableStateManager.BackupAsync**. Les sauvegardes peuvent uniquement être effectuées à partir de réplicas principaux et ont besoin de bénéficier du statut d’écriture.

Comme illustré ci-dessous, la surcharge la plus simple de **BackupAsync** inclut Func<< BackupInfo  bool >> appelé **backupCallback**.

```C#
await this.StateManager.BackupAsync(this.BackupCallbackAsync);
```

**BackupInfo** fournit des informations sur la sauvegarde, y compris l’emplacement du dossier où le runtime a enregistré la sauvegarde (**BackupInfo.Directory**). La fonction de rappel s’attend à déplacer **BackupInfo.Directory** vers un magasin externe ou un autre emplacement. Cette fonction renvoie également une valeur booléenne qui indique si elle a été en mesure de déplacer correctement le dossier de sauvegarde vers son emplacement cible.

Le code suivant montre comment backupCallback peut être utilisé pour charger la sauvegarde vers Azure Storage :

```C#
private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo)
{
    var backupId = Guid.NewGuid();

    await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, CancellationToken.None);

    return true;
}
```

Dans l’exemple ci-dessus, **ExternalBackupStore** est l’exemple de classe qui sert d’interface avec le stockage des objets blob Azure, et **UploadBackupFolderAsync** est la méthode qui compresse le dossier et le place dans le magasin d’objets blob Azure.

Notez les points suivants :

- Il ne peut exister qu’un seul **BackupAsync** par réplica en transit à un instant donné. Plusieurs appels **BackupAsync** à la fois lèvent une exception **FabricBackupInProgressException** pour limiter les sauvegardes en transit à une seule.

- Si un réplica bascule pendant une sauvegarde, celle-ci n’a peut-être pas été effectuée. Ainsi, une fois le basculement terminé, il incombe au service de redémarrer la sauvegarde en appelant **BackupAsync** si nécessaire.

## Opération de restauration

En règle générale, vous devrez peut-être effectuer une restauration dans les cas suivants :


- La partition du service a perdu des données. Par exemple, le disque de deux réplicas sur trois pour une partition (y compris le réplica principal) est endommagé ou effacé. Le nouveau réplica principal peut être amené à restaurer des données à partir d’une sauvegarde.

- L’ensemble du service est perdu. Par exemple, un administrateur supprime l’ensemble du service, et le service et les données doivent donc être restaurés.

- Le service a répliqué des données d’application endommagées (par exemple, en raison d’un bogue dans l’application). Dans ce cas, le service doit être mis à niveau ou restauré pour supprimer la cause de l’endommagement, et les données non endommagées doivent être restaurées.

Bien que de nombreuses approches soient possibles, nous vous proposons des exemples sur l’utilisation de **RestoreAsync** pour récupérer à partir des scénarios ci-dessus.

## Perte de données de partition

Dans ce cas, le runtime détecte automatiquement la perte de données et appelle l’API **OnDataLossAsync**.

Le créateur du service doit effectuer les opérations suivantes pour assurer la récupération :

- Remplacer **CreateReliableStateManager** pour renvoyer un nouveau **ReliableStateManager** et fournir une fonction de rappel à utiliser dans le cas d’un événement de type perte de données.

- Rechercher la dernière sauvegarde dans l’emplacement externe qui contient les sauvegardes du service.

- Si l’état de la dernière sauvegarde est derrière le nouveau réplica principal, renvoyer l’état False. Cela permet de s’assurer que les données du nouveau réplica principal ne sont pas remplacées par des données plus anciennes.

- Télécharger la dernière sauvegarde (et la décompresser dans le dossier de sauvegarde le cas échéant).

- Appeler **IReliableStateManager.RestoreAsync** avec le chemin d’accès du dossier de sauvegarde.

- Renvoyer l’état True en cas de succès de la restauration.

Voici un exemple d’implémentation de la méthode **OnDataLossAsync** avec le remplacement de **IReliableStateManager**.

```C#
protected override IReliableStateManager CreateReliableStateManager()
{
    return new ReliableStateManager(new ReliableStateManagerConfiguration(
            onDataLossEvent: this.OnDataLossAsync));
}

protected override async Task<bool> OnDataLossAsync(CancellationToken cancellationToken)
{
    var backupFolder = await this.externalBackupStore.DownloadLastBackupAsync(cancellationToken);

    await this.StateManager.RestoreAsync(backupFolder);

    return true;
}
```

>[AZURE.NOTE] Par défaut, RestorePolicy est défini sur Sécurisé. Cela signifie que l’API **RestoreAsync** va échouer avec ArgumentException si elle détecte que le dossier de sauvegarde contient un état antérieur ou égal à l’état contenu dans ce réplica. **RestorePolicy.Force** peut servir à ignorer cette vérification de sécurité.

## Service supprimé ou perdu

Si un service est supprimé, vous devez d’abord le recréer pour que les données puissent être restaurées. Il est important de créer le service avec la même configuration, par exemple, le schéma de partitionnement, afin que les données puissent être restaurées en toute transparence. Une fois que le service est opérationnel, l’API pour restaurer les données (**OnDataLossAsync** ci-dessus) doit être appelée sur chaque partition de ce service. L’utilisation de **FabricClient.ServiceManager.InvokeDataLossAsync** sur chaque partition est un moyen d’y parvenir.

À ce stade, l’implémentation est identique au scénario ci-dessus. Chaque partition doit restaurer la dernière sauvegarde pertinente à partir du magasin externe. L’inconvénient est que l’ID de partition peut avoir changé, étant donné que le runtime crée des ID de partition de manière dynamique. Par conséquent, le service doit stocker les informations de partition appropriées et le nom du service pour identifier la dernière sauvegarde correcte à restaurer pour chaque partition.


## Réplication des données d’application endommagées

Si la mise à niveau de l’application récemment déployée comporte un bogue, des données risquent d’être endommagées. Par exemple, une mise à niveau de l’application peut commencer à mettre à jour les enregistrements de numéro de téléphone d’un Dictionnaire fiable en utilisant un indicatif non valide. Dans ce cas, les numéros de téléphone non valides seront répliqués, car Service Fabric ne connaît pas la nature des données stockées.

La première chose que vous devez faire après avoir détecté un bogue si flagrant qui endommage les données est de figer le service au niveau de l’application et, si possible, de procéder à une mise à niveau vers une version du code d’application qui exclut le bogue. Cependant, même une fois que le code du service est résolu, les données peuvent être toujours endommagées et les données peuvent avoir besoin d’être restaurées. Dans ce cas, restaurer la dernière sauvegarde peut ne pas suffire, étant donné qu’elle peut également être endommagée. Il convient donc de rechercher la dernière sauvegarde effectuée avant que les données ne soient endommagées.

Si vous ne savez pas exactement quelles mises à jour sont endommagées, vous pouvez déployer un nouveau cluster Service Fabric et restaurer les sauvegardes des partitions affectées comme dans le scénario de « service supprimé ou perdu » ci-dessus. Pour chaque partition, lancer la restauration de sauvegardes de la plus récente à la plus ancienne. Une fois que vous avez trouvé une sauvegarde ne présentant pas de corruption, déplacez/supprimez toutes les sauvegardes de partition plus récentes (que cette sauvegarde). Répétez ce processus pour chaque partition. Maintenant, lorsque **OnDataLossAsync** est appelé sur la partition dans le cluster de production, la dernière sauvegarde trouvée dans le magasin externe est celle choisie par le processus ci-dessus.

À présent, les étapes de la section « Service supprimé ou perdu » peuvent être utilisées pour restaurer l’état de la sauvegarde du service à l’état préalable à l’endommagement par le code bogué.

Notez les points suivants :

- Lorsque vous effectuez une restauration, il existe un risque que la sauvegarde restaurée soit antérieure à l’état de la partition avant la perte des données. Pour cette raison, vous ne devez effectuer une restauration qu’en dernier recours pour récupérer autant de données que possible.

- La chaîne qui représente le chemin d’accès du dossier de sauvegarde et les chemins d’accès des fichiers dans le dossier de sauvegarde peut être supérieure à 255 caractères, selon le chemin d’accès FabricDataRoot et la longueur du nom du type d’application. Cela peut pousser des méthodes .NET comme **Directory.Move** à lever l’exception **PathTooLongException**. Une solution de contournement consiste à appeler directement les API kernel32 comme **CopyFile**.


## En arrière-plan : plus de détails sur la sauvegarde et restauration

### Sauvegarde
Le gestionnaire d’état fiable offre la possibilité de créer des sauvegardes cohérentes sans bloquer les opérations de lecture ou d’écriture. Pour ce faire, il utilise un point de contrôle et un mécanisme de persistance de journal. Le gestionnaire d’état fiable prend des points de contrôle (légers) approximatifs à certains points pour soulager la pression dans le journal des transactions et améliorer les temps de récupération. Lorsque **IReliableStateManager.BackupAsync** est appelé, le gestionnaire d’état fiable indique à tous les objets fiables de copier leurs derniers fichiers de point de contrôle dans un dossier de sauvegarde local. Ensuite, le gestionnaire d’état fiable copie tous les enregistrements de journal à partir du pointeur de démarrage vers le dernier enregistrement de journal dans le dossier de sauvegarde. Comme tous les enregistrements de journal, y compris le dernier, sont inclus dans la sauvegarde et que le gestionnaire d’état fiable conserve la journalisation WAL (write-ahead logging), le gestionnaire d’état fiable garantit que toutes les transactions qui sont validées (**CommitAsync** correctement renvoyé) sont incluses dans la sauvegarde.

Une transaction validée après l’appel de **BackupAsync** peut figurer ou non dans la sauvegarde. Une fois que le dossier de sauvegarde local a été rempli par la plateforme (c’est-à-dire que la sauvegarde locale est effectuée par le runtime), le rappel de sauvegarde du service est appelé. Ce rappel est chargé de déplacer le dossier de sauvegarde vers un emplacement externe comme Azure Storage.

### Restauration

Le gestionnaire d’état fiable permet de restaurer une sauvegarde à l’aide de l’API **IReliableStateManager.RestoreAsync**. La méthode **RestoreAsync** peut uniquement être appelée à l’intérieur de la méthode **OnDataLossAsync**. La valeur booléenne renvoyée par **OnDataLossAsync** indique si le service a été restauré à cet état à partir d’une source externe. Si **OnDataLossAsync** renvoie la valeur true, Service Fabric recrée tous les autres réplicas à partir de ce réplica principal. Service Fabric s’assure que les réplicas qui vont recevoir **OnDataLossAsync** effectuent tout d’abord une transition vers le rôle principal, mais ne se voient pas accorder le statut de lecture ou d’écriture. Pour les responsables de l’implémentation de StatefulService, cela implique que la méthode **RunAsync** ne soit pas appelée tant que la méthode **OnDataLossAsync** ne s’est pas terminée correctement. Ensuite, **OnDataLossAsync** est appelée sur le nouveau réplica principal. L’API continuera d’être appelée jusqu’à ce que l’API se termine correctement (en renvoyant true ou false) et termine la reconfiguration concernée.


La méthode **RestoreAsync** supprime tout d’abord tout état existant dans le réplica principal sur lequel elle a été appelée. Le gestionnaire d’état fiable crée ensuite tous les objets fiables qui existent dans le dossier de sauvegarde. Les objets fiables sont alors invités à procéder à une restauration à partir de leurs points de contrôle dans le dossier de sauvegarde. Enfin, le gestionnaire d’état fiable récupère son propre état à partir d’enregistrements de journal dans le dossier de sauvegarde, puis effectue la récupération. Dans le cadre de la récupération, les opérations commençant à partir du point de départ qui ont validé des enregistrements de journal dans le dossier de sauvegarde sont relues dans les objets fiables. Cette étape garantit que l’état récupéré est cohérent.

<!------HONumber=AcomDC_0309_2016-->