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
   ms.date="08/18/2015"
   ms.author="mcoskun"/>

# Sauvegarde et restauration Reliable Services

Service Fabric est une plateforme haute disponibilité qui réplique l’état sur plusieurs nœuds afin de conserver cette haute disponibilité. Ainsi, même si un nœud du cluster échoue, les services continuent à être disponibles. Bien que cette redondance intégrée fournie par la plateforme suffise pour certains, dans d’autres cas, il est souhaitable que le service sauvegarde les données (sur un magasin externe).

Par exemple, un service peut souhaiter sauvegarder des données dans les scénarios suivants :

* En cas de perte permanente d’un cluster Service Fabric entier ou de tous les nœuds exécutant une partition donnée (par exemple, tous les nœuds du cluster). Cela peut se produire, par exemple, si vous n’êtes pas répliqué géographiquement et que votre cluster entier se trouve dans un centre de données qui tombe en panne.
* Erreurs d’administration dans le cadre desquelles l’état est accidentellement supprimé/endommagé. Cela peut se produire lorsqu’un administrateur disposant de privilèges suffisants supprime le service de manière erronée.
* Bogues dans le service à l’origine de l’endommagement des données. Cela peut se produire lorsque, par exemple, une mise à niveau de service écrivant des données erronées sur une collection a eu lieu, et que le service et les données doivent être restaurés à un état antérieur.
* Traitement des données hors ligne. Il peut être utile de disposer du traitement hors ligne des données pour que le décisionnel survienne séparément du service générant les données.

La fonctionnalité Sauvegarder/Restaurer permet aux services reposant sur l’API Reliable Services de créer et de restaurer des sauvegardes. Les API de sauvegarde fournies par la plateforme permettent aux sauvegardes de l’état d’une partition d’être effectuées sans bloquer les opérations de lecture ou d’écriture. Les API de restauration permettent à l’état d’une partition d’être restauré à partir d’une sauvegarde choisie.



### Procédure de sauvegarde

L’auteur du service contrôle intégralement le moment où les sauvegardes sont effectuées et leur emplacement de stockage.

Pour démarrer une sauvegarde, le service doit appeler **IReliableStateManager.BackupAsync**. Les sauvegardes peuvent uniquement être effectuées à partir de réplicas principaux et ont besoin de bénéficier du statut d’écriture.

Comme illustré ci-dessous, la surcharge la plus simple de **BackupAsync** inclut Func<< BackupInfo  bool >> appelé **backupCallback**.

        await this.StateManager.BackupAsync(this.BackupCallbackAsync);

**BackupInfo** fournit des informations sur la sauvegarde, y compris l’emplacement du dossier où le runtime a enregistré la sauvegarde (BackupInfo.Directory). La fonction de rappel s’attend à déplacer BackupInfo.Directory vers un magasin externe ou un autre emplacement. Cette fonction renvoie également une valeur booléenne qui indique si elle a été en mesure de déplacer correctement le dossier de sauvegarde vers son emplacement cible.

Le code suivant montre comment backupCallback peut être utilisé pour charger la sauvegarde vers Azure Storage :

```C#
        private async Task<bool> BackupCallbackAsync(BackupInfo backupInfo)
        {
            var backupId = Guid.NewGuid();

            await externalBackupStore.UploadBackupFolderAsync(backupInfo.Directory, backupId, CancellationToken.None);

            return true;
        }
```

Dans l’exemple ci-dessus, **ExternalBackupStore** est l’exemple de classe qui sert d’interface avec Azure Blob Storage, et **UploadBackupFolderAsync** est la méthode qui compresse le dossier et le place dans le magasin d’objets blob Azure.

>[AZURE.NOTE]Il ne peut exister qu’un **BackupAsync** par réplica en transit à tout moment. Plusieurs appels **BackupAsync** à la fois lèvent une exception **FabricBackupInProgressException** pour limiter les sauvegardes en transit à une seule.[AZURE.NOTE]Si un réplica bascule pendant une sauvegarde, celle-ci n’a peut-être pas été effectuée. Ainsi, une fois le basculement terminé, il incombe au service de recommencer la sauvegarde en appelant **BackupAsync** si nécessaire.

### Procédure de restauration des données

Il est possible de classer les scénarios de restauration dans lesquels le service en cours d’exécution doit restaurer des données à partir du magasin de sauvegarde selon les éléments suivants :

1. La partition du service a perdu des données. Par exemple, le disque de deux réplicas sur trois pour une partition (y compris le réplica principal) est endommagé/effacé. Les données pour le nouveau principal doivent être restaurées.

2. L’ensemble du service est perdu. Par exemple, un administrateur supprime l’ensemble du service et, par conséquent, le service et les données doivent être restaurés.

3. Le service répliqué endommage les données de l’application (par exemple, en raison d’un bogue dans l’application). Dans ce cas, le service doit être mis à niveau/restauré pour supprimer la cause de l’endommagement, et les données non endommagées doivent être restaurées.

Bien que de nombreuses approches soient possibles, nous vous proposons des exemples sur l’utilisation de RestoreAsync pour récupérer à partir des scénarios ci-dessus.

#### Perte de données de partition

Dans ce cas, le runtime détecterait automatiquement la perte de données et appellerait l’API **OnDataLossAsync**.

L’auteur du service doit effectuer les opérations suivantes pour récupérer : - Remplacer **IReliableStateManager** pour renvoyer un nouveau ReliableStateManager et fournir une fonction de rappel à appeler dans le cas d’un événement de perte de données. - Rechercher la dernière sauvegarde dans l’emplacement externe qui contient les sauvegardes du service - Télécharger la dernière sauvegarde (et la décompresser dans le dossier de sauvegarde si elle était compressée). - Appeler **IReliableStateManager.RestoreAsync** avec le chemin d’accès au dossier de sauvegarde. - Renvoie la valeur true si la restauration a réussi.

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

>[AZURE.NOTE]Par défaut, RestorePolicy est défini sur Sécurisé. Cela signifie que l’API RestoreAsync va échouer avec ArgumentException si elle détecte que le dossier de sauvegarde contient un état qui est antérieur ou égal à l’état contenu dans ce réplica. RestorePolicy.Force peut servir à ignorer cette vérification de sécurité.

#### Service supprimé ou perdu

Si un service est supprimé, il faut tout d’abord recréer le service avant que les données puissent être restaurées. Il est important de créer le service avec la même configuration, par exemple, le schéma de partitionnement, afin que les données puissent être restaurées en toute transparence. Une fois que le service est opérationnel, l’API pour restaurer les données (**OnDataLossAsync** ci-dessus) doit être appelée sur chaque partition de ce service. L’utilisation de **FabricClient.ServiceManager.InvokeDataLossAsync** sur chaque partition est un moyen d’y parvenir. Pour plus d’informations sur **InvokeDataLossAsync**, consultez le chapitre Testabilité.

À ce stade, l’implémentation est identique au scénario ci-dessus. Chaque partition doit restaurer la dernière sauvegarde pertinente à partir du magasin externe. L’inconvénient est que l’ID de partition peut avoir changé, étant donné que le runtime crée les ID de partition de manière dynamique. Par conséquent, le service doit stocker les informations de partition appropriées et le nom du service pour identifier la dernière sauvegarde correcte à restaurer pour chaque partition.


#### Réplication des données d’application endommagées

S’il existe un bogue dans l’application après la mise à niveau de celle-ci, qui peut entraîner l’endommagement des données, par exemple, une mise à niveau de l’application qui peut commencer à mettre à jour chaque enregistrement de numéro de téléphone dans un dictionnaire fiable avec un code de zone non valide. Dans ce cas, les numéros de téléphone non valides seront répliqués étant donné que Service Fabric n’est pas conscient de la nature des données stockées.

La première chose à faire après avoir détecté un bogue si flagrant qui endommage les données est de figer le service au niveau de l’application et, si possible, de mettre à niveau vers une version du code d’application qui exclut le bogue. Cependant, même une fois que le code du service est résolu, les données peuvent être toujours endommagées et les données peuvent avoir besoin d’être restaurées. Dans ce cas, restaurer la dernière sauvegarde peut ne pas suffire, étant donné qu’elle peut également être endommagée. Il convient donc de rechercher la dernière sauvegarde effectuée avant que les données soient endommagées.

L’un des moyens d’y parvenir consiste à déployer un nouveau cluster Service Fabric et à restaurer les sauvegardes de partitions affectées comme dans le scénario de service supprimé ci-dessus. Une fois que vous avez trouvé la sauvegarde antérieure à l’endommagement des données, vous pouvez déplacer/supprimer toutes les sauvegardes de cette partition après cette sauvegarde. Maintenant, lorsque **OnDataLossAsync** est appelé sur la partition dans le cluster de production, la dernière sauvegarde trouvée dans le magasin externe sera celle choisie par le processus ci-dessus.

À présent, les étapes de la section Service supprimé peuvent être utilisées pour restaurer l’état de la sauvegarde du service à l’état préalable à l’endommagement par le code bogué.


>[AZURE.NOTE]Chaque fois que nous effectuons une restauration, il existe un risque que la sauvegarde restaurée soit antérieure à l’état de la partition avant la perte des données. Pour cette raison, la restauration doit uniquement être utilisée en dernier recours pour récupérer autant de données que possible.

>[AZURE.NOTE]La chaîne qui représente le chemin d’accès au dossier de sauvegarde, ainsi que les chemins d’accès aux fichiers dans le dossier de sauvegarde, peut être supérieure à 255 caractères, selon le chemin d’accès FabricDataRoot et la longueur du nom du type d’application. Cela peut pousser des méthodes .Net comme **Directory.Move** à lever des exceptions **PathTooLongException**. Une méthode de contournement consiste à appeler directement les API kernel32 comme **CopyFile**.


## En arrière-plan : plus de détails sur la sauvegarde et restauration

### Sauvegarde

Le gestionnaire d’état fiable offre la possibilité de créer des sauvegardes cohérentes sans bloquer les opérations de lecture et d’écriture requises par le service. Pour ce faire, il utilise un point de contrôle et un mécanisme de persistance de journal. Le gestionnaire d’état fiable prend des points de contrôle (légers) approximatifs à certains points pour soulager la pression dans le journal des transactions et améliorer les temps de récupération. Lorsque IReliableStateManager.**BackupAsync** est appelé, le gestionnaire d’état fiable indique à tous les objets fiables de copier leurs derniers fichiers de point de contrôle dans un dossier de sauvegarde local. Ensuite, le gestionnaire d’état fiable copie tous les enregistrements de journal à partir du pointeur de démarrage vers le dernier enregistrement de journal dans le dossier de sauvegarde. Étant donné que tous les enregistrements de journal jusqu’au dernier sont inclus dans la sauvegarde et que le gestionnaire d’état fiable conserve la journalisation WAL, le gestionnaire d’état fiable garantit que toutes les transactions qui sont validées (CommitAsync correctement renvoyé) sont incluses dans la sauvegarde.

Une transaction qui est validée après l’appel de **BackupAsync** peut figurer ou non dans la sauvegarde. Une fois que le dossier de sauvegarde local a été rempli par la plateforme (c’est-à-dire, que la sauvegarde locale est effectuée par le runtime), le rappel de sauvegarde du service est appelé. Ce rappel est chargé de déplacer le dossier de sauvegarde vers un emplacement externe comme Azure Storage.

### Restauration

Le gestionnaire d’état fiable permet de restaurer une sauvegarde à l’aide de l’API IReliableStateManager.RestoreAsync. La méthode RestoreAsync peut uniquement être appelée à l’intérieur de la méthode OnDataLossAsync. La valeur booléenne renvoyée par OnDataLossAsync indique si le service a restauré son état à partir d’une source externe. Si OnDataLossAsync renvoie true, Service Fabric recrée tous les autres réplicas à partir de ce principal. Le réplica qui reçoit OnDataLossAsync va tout d’abord passer au rôle de principal, mais il ne bénéficiera pas de l’état de lecture ou d’écriture. Cela implique que pour les responsables de l’implémentation de StatefulService, RunAsync ne sera pas appelé avant que OnDataLossAsync se termine correctement. Jusqu’à ce qu’un service termine cette API correctement (en renvoyant true ou false) et termine la reconfiguration pertinente, l’API continuera d’être appelée.

RestoreAsync supprime tout d’abord tout état existant dans le réplica principal sur lequel il a été appelé. Après, le gestionnaire d’état fiable crée tous les objets fiables qui existent dans le dossier de sauvegarde. Ensuite, les objets fiables sont invités à procéder à une restauration à partir de leurs points de contrôle dans le dossier de sauvegarde. Enfin, le gestionnaire d’état fiable récupère son propre état à partir d’enregistrements de journal dans le dossier de sauvegarde et effectue la récupération. Dans le cadre du processus de récupération, les opérations à partir du point de départ qui ont validé des enregistrements de journal dans le dossier de sauvegarde sont relues sur les objets fiables. Cette étape garantit que l’état récupéré est cohérent.

<!---HONumber=Nov15_HO4-->