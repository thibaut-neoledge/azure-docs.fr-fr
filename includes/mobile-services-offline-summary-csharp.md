Pour pouvoir prendre en charge les fonctionnalités hors connexion des services mobiles, nous avons utilisé l'interface `IMobileServiceSyncTable` et initialisé `MobileServiceClient.SyncContext` avec un magasin local. Dans ce cas, le magasin local était une base de données SQLite.

Les opérations CRUD normales pour les services mobiles fonctionnent comme si l'application était toujours connectée, mais toutes les opérations ont lieu sur base du magasin local.

Lorsque nous avons voulu synchroniser le magasin local avec le serveur, nous avons utilisé les méthodes `IMobileServiceSyncTable.PullAsync` et `MobileServiceClient.SyncContext.PushAsync`.

*  Pour transmettre par push les modifications au serveur, nous avons appelé `IMobileServiceSyncContext.PushAsync()`. Cette méthode est membre de `IMobileServicesSyncContext` à la place de la table de synchronisation parce qu'elle envoie par Push les modifications sur toutes les tables.

    Seuls les enregistrements qui ont été modifiés d'une certaine façon localement (par le biais d'opérations CUD) seront envoyés au serveur.
   
* Pour envoyer par pull les données d'une table sur le serveur vers l'application, nous avons appelé  `IMobileServiceSyncTable.PullAsync`.

    Une opération Pull émet toujours d'abord une opération Push. Cela a pour but de garantir que toutes les tables dans le magasin local, ainsi que les relations, restent cohérentes.

    Il existe également des surcharges de `PullAsync()` qui permettent de spécifier une requête afin de filtrer les données stockées sur le client. Si une requête n'est pas transmise, `PullAsync()` extrait toutes les lignes de la table (ou requête) correspondante. Vous pouvez transmettre la requête pour filtrer uniquement les modifications avec lesquelles votre application doit se synchroniser.

* Pour activer la synchronisation incrémentielle, transmettez un ID de requête à `PullAsync()`. L'ID de requête est utilisé pour stocker le dernier horodatage des résultats de la dernière opération d'extraction. L'ID de requête doit être une chaîne descriptive unique pour chaque requête logique de votre application. Si la requête possède un paramètre, alors la même valeur de paramètre doit faire partie de l'ID de requête.

    Par exemple, si vous filtrez selon userid, il doit faire partie de l'ID de la requête :

        await PullAsync("todoItems" + userid, syncTable.Where(u => u.UserId = userid));

    Si vous souhaitez désactiver la synchronisation incrémentielle, transmettez `null` en tant qu'ID de requête. Dans ce cas, tous les enregistrements seront extraits à chaque appel `PullAsync`, ce qui est potentiellement inefficace.

* Pour supprimer des enregistrements du magasin local du périphérique lorsqu'ils ont été supprimés dans la base de données de votre service mobile, vous devez activer la [Suppression réversible]. Sinon, votre application doit appeler périodiquement `IMobileServiceSyncTable.PurgeAsync()` afin de vider le magasin local.

<!--HONumber=49-->