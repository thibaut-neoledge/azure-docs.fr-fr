<!--author=SharS last changed: 9/17/15-->

Dans cette procédure, vous allez :

1. [préparer l'exécution de l'exécutable du chargé de maintenance](#to-prepare-to-run-the-maintainer) ;

2. [préparer la base de données de contenu et la corbeille pour la suppression immédiate des objets BLOB orphelins](#to-prepare-the-content-database-and-recycle-bin-to-immediately-delete-orphaned-blobs) ;

3. [exécuter Maintainer.exe](#to-run-the-maintainer) ;

4. [rétablir la base de données de contenu et les paramètres de la corbeille](#to-revert-the-content-database-and-recycle-bin-settings).

#### Pour préparer l'exécution du chargé de maintenance

1. Sur le serveur Web frontal, ouvrez SharePoint 2013 Management Shell en tant qu'administrateur.

2. Accédez au dossier *disque de démarrage*:\\Program Files\\Microsoft SQL Remote Blob Storage 10.50\\Maintainer.

3. Renommez **Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config** en **web.config**.

4. Utilisez `aspnet_regiis -pdf connectionStrings` pour déchiffrer le fichier web.config.

5. Dans le fichier web.config déchiffré, sous le nœud `connectionStrings`, ajoutez la chaîne de connexion pour votre instance de SQL Server et le nom de base de données de contenu. Consultez l’exemple qui suit.

    `<add name=”RBSMaintainerConnectionWSSContent” connectionString="Data Source=SHRPT13-SQL12\SHRPT13;Initial Catalog=WSS_Content;Integrated Security=True;Application Name=";Remote Blob Storage Maintainer for WSS_Content";" providerName="System.Data.SqlClient" />`

6. Utilisez `aspnet_regiis –pef connectionStrings` pour chiffrer à nouveau le fichier web.config.

7. Renommez web.config en Microsoft.Data.SqlRemoteBlobs.Maintainer.exe.config.

#### Préparation de la base de données de contenu et de la Corbeille pour la suppression immédiate des objets blob orphelins

1. Sur SQL Server, dans SQL Management Studio, exécutez les requêtes suivantes de mise à jour de la base de données de contenu cible : 

       `use WSS_Content`

       `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ’time 00:00:00’`

       `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’time 00:00:00’`

2. Sur le serveur Web frontal, sous **Administration centrale**, modifiez les **Paramètres généraux de l'application Web** pour la base de données de contenu souhaitée afin de désactiver temporairement la Corbeille. Cette action vide également la Corbeille de toutes les collections de sites liées. Pour ce faire, cliquez sur **Administration centrale** -> **Gestion des applications** -> **Applications Web (gérer les applications Web)** -> **SharePoint - 80** -> **Paramètres généraux de l'application**. Définissez l’**état de la Corbeille** sur **OFF**.

    ![Paramètres généraux de l'application Web](./media/storsimple-sharepoint-adapter-garbage-collection/HCS_WebApplicationGeneralSettings-include.png)

#### Exécution du chargé de maintenance

- Sur le serveur Web frontal, dans SharePoint 2013 Management Shell, exécutez le chargé de maintenance comme suit :

      `Microsoft.Data.SqlRemoteBlobs.Maintainer.exe -ConnectionStringName RBSMaintainerConnectionWSSContent -Operation GarbageCollection -GarbageCollectionPhases rdo`

    >[AZURE.NOTE]Seule l’opération `GarbageCollection` est prise en charge par StorSimple pour l'instant. Notez également que les paramètres émis pour Microsoft.Data.SqlRemoteBlobs.Maintainer.exe respectent la casse.
 
#### Rétablissement du contenu de la base de données et des paramètres de la Corbeille

1. Sur SQL Server, dans SQL Management Studio, exécutez les requêtes suivantes de mise à jour de la base de données de contenu cible :

      `use WSS_Content`

      `exec mssqlrbs.rbs_sp_set_config_value ‘garbage_collection_time_window’ , ‘days 30’`

      `exec mssqlrbs.rbs_sp_set_config_value ‘delete_scan_period’ , ’days 30’`

      `exec mssqlrbs.rbs_sp_set_config_value ‘orphan_scan_period’ , ’days 30’`

2. Sur le serveur Web frontal, sous **Administration centrale**, modifiez les **Paramètres généraux de l'application Web** pour la base de données de contenu souhaitée afin d’activer à nouveau la Corbeille. Pour ce faire, cliquez sur **Administration centrale** -> **Gestion des applications** -> **Applications Web (gérer les applications Web)** -> **SharePoint - 80** -> **Paramètres généraux de l'application**. Définissez l’état de la Corbeille sur **ON**.

<!---HONumber=AcomDC_0107_2016-->