<properties linkid="manage-services-how-to-create-a-storage-account" urlDisplayName="How to create" pageTitle="How to create a storage account | Azure" metaKeywords="" description="Learn how to create a storage account in the Azure management portal." metaCanonical="" services="storage" documentationCenter="" title="How To Create a Storage Account" authors="tysonn" solutions="" manager="paulettm" editor="cgronlun" />

Création d'un compte de stockage
================================

Pour stocker des fichiers et des données dans les services d'objets blob, de table et de file d'attente d'Azure, il est nécessaire de créer un compte de stockage dans la zone géographique où doivent être stockées les données. Un compte de stockage peut contenir jusqu'à 200 To de données et il est possible de créer jusqu'à vingt comptes par abonnement Azure. Pour plus d’informations, consultez la page [Objectifs de performance et évolutivité d'Azure Storage](http://msdn.microsoft.com/fr-fr/library/dn249410.aspx).

Cette rubrique décrit la procédure à suivre pour créer un compte de stockage sur le portail de gestion Azure.

**Remarque**

Lorsqu'une machine virtuelle Azure est utilisée, un compte de stockage est créé automatiquement sur le lieu de déploiement si vous ne disposez pas déjà de ce type de compte à cet emplacement. Le nom du compte de stockage dépend du nom de la machine virtuelle.

Sommaire
--------

-   [Création d’un compte de stockage](#create)
-   [Étapes suivantes](#next)

Création d'un compte de stockage
--------------------------------

1.  Connectez-vous au [portail de gestion](https://manage.windowsazure.com).

2.  Cliquez sur **Create New**, sur **Stockage**, puis sur **Quick Create**.

    ![NewStorageAccount](./media/storage-create-storage-account/storage_NewStorageAccount.png)

3.  Sous **URL**, entrez un nom de sous-domaine à inclure dans l'URL du compte de stockage. Pour accéder à un objet qui est stocké, ajoutez l'emplacement de l'objet au point de terminaison. Par exemple, l'URL permettant d'accéder à un objet blob peut être http://*mon\_compte*.blob.core.windows.net/*mon\_conteneur*/*mon\_blob*.

4.  Sous **Région/Groupe d'affinités**, sélectionnez une zone ou un groupe d'affinités pour le stockage. Sélectionnez un groupe d'affinités plutôt qu'une zone pour que vos services de stockage soient hébergés dans le même centre de données que les autres services Azure que vous utilisez. Ce choix peut améliorer les performances et n'entraîne aucun frais supplémentaire.

    > [WACOM.NOTE] 
        Pour créer un groupe d'affinités, ouvrez la zone **Réseaux** du portail de gestion, cliquez sur **Groupes d'affinités**, puis cliquez sur **Créer un nouveau groupe d'affinités** ou sur **Create**. Vous pouvez utiliser les groupes d'affinités créés sur le précédent portail de gestion. Pour ouvrir l'autre portail, cliquez sur **Preview** dans la barre de titre, puis sur **Take me to the previous portal**. (Pour revenir sur le portail en cours, cliquez sur **View the Preview Portal** au bas du portail.) Vous pouvez également créer et gérer des groupes d'affinités par le biais de l'API Azure Service Management. Pour plus d'informations, consultez la page [Opérations sur les groupes d'affinités](http://msdn.microsoft.com/fr-fr/library/windowsazure/ee460798.aspx).

5.  Si vous disposez de plusieurs abonnements Azure, le champ **Subscription** s'affiche. Dans **Subscription**, entrez l'abonnement Azure à utiliser avec le compte de stockage. Vous pouvez créer jusqu'à cinq comptes de stockage par abonnement.

6.  Sous **Replication**, sélectionnez le niveau de réplication souhaité pour votre compte de stockage.

    Le niveau de réplication **Geo-Redundant** est défini par défaut. Avec la réplication géo-redondante, votre compte de stockage et toutes les données qu'il contient basculent vers un emplacement secondaire en cas de sinistre majeur sur le site principal. Azure attribue un emplacement secondaire dans la même zone et aucune modification n'est possible. Lorsqu'il y a basculement, l'emplacement secondaire devient l'emplacement principal du compte de stockage et vos données sont répliquées vers un nouvel emplacement secondaire.

    Pour pouvoir lire les données depuis l'emplacement secondaire, vous pouvez sélectionner la réplication **Read-Access Geo-Redundant**. Cette option assure la réplication géo-redondante et autorise l'accès en lecture seule aux données répliquées dans l'emplacement secondaire. La réplication géo-redondante avec accès en lecture offre un accès aux données, qu'elles se situent dans l'emplacement principal ou secondaire, si l'un des deux n'est pas disponible.

    L'option de réplication **Read Access Geo-Redundant** est disponible en tant que version préliminaire. Cette option permet l’accès en lecture seule aux données répliquées dans l'emplacement secondaire. La réplication géo-redondante avec accès en lecture offre un accès aux données, qu'elles se situent dans l'emplacement principal ou secondaire, si l'un des deux n'est pas disponible.

    > [WACOM.NOTE]
        Pour utiliser la réplication géo-redondante avec accès en lecture seule, vous devez demander manuellement l'activation de la fonction pour votre abonnement. Pour demander que cette fonction soit activée pour votre abonnement, consultez la page [Fonctionnalités préliminaires d'Azure](https://account.windowsazure.com/PreviewFeatures). Pour plus de détails sur la réplication géo-redondante avec accès en lecture seule, consultez le [Blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/archive/2013/12/04/introducing-read-access-geo-replicated-storage-ra-grs-for-windows-azure-storage.aspx). Si elle n'est pas activée en tant que fonction préliminaire pour votre abonnement, l'option permettant de la sélectionner pour votre compte de stockage est désactivée.

    Pour plus d'informations sur la tarification relative à la réplication du compte de stockage, consultez la page [Tarification – Stockage](http://www.windowsazure.com/fr-fr/pricing/details/storage/).

7.  Cliquez sur **Create Storage Account**.

    La création de votre compte de stockage peut prendre plusieurs minutes. Pour vérifier l'état d'avancement de l'opération, vous pouvez contrôler les notifications au bas du portail. Une fois que le compte de stockage est créé, le statut **Online** lui est associé et il est prêt à être utilisé.

    ![StoragePage](./media/storage-create-storage-account/Storage_StoragePage.png)

Étapes suivantes
----------------

-   Pour en savoir plus sur les services de stockage Azure, consultez les pages [Vue d'ensemble du stockage cloud](http://www.windowsazure.com/fr-fr/develop/net/fundamentals/cloud-storage/) et [Objets blob, files d'attente et tables](http://msdn.microsoft.com/fr-fr/library/gg433040.aspx).

-   Consultez le [Blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/).

-   Configurez vos applications afin d'utiliser les services d'objets blob, de table et de file d'attente Azure. Le [Centre de développement Azure](http://www.windowsazure.com/fr-fr/develop/overview/) fournit les manuels d'utilisation des services de stockage d'objets blob, de table et de file d'attente avec vos applications .NET, Node.js, Java et PHP. Pour obtenir les instructions propres à un langage de programmation, reportez-vous au manuel d'utilisation de ce langage.


