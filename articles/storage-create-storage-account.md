<properties linkid="manage-services-how-to-create-a-storage-account" urlDisplayName="How to create" pageTitle="How to create a storage account | Azure" metaKeywords="" description="Learn how to create a storage account in the Azure management portal." metaCanonical="" services="storage" documentationCenter="" title="How To Create a Storage Account" solutions="" authors="tamram" manager="mbaldwin" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# <span id="createstorageaccount"></span></a>Création d'un compte de stockage

To store files and data in the Blob, Table, Queue, and File services in Azure, you must create a storage account in the geographic region where you want to store the data. Cette rubrique décrit la procédure à suivre pour créer un compte de stockage sur le portail de gestion Azure.

For details about storage account capacity and throughput, see [Azure Storage Scalability and Performance Targets][Azure Storage Scalability and Performance Targets].

> [WACOM.NOTE] For an Azure virtual machine, a storage account is created automatically in the deployment location if you do not already have a storage account in that location. Le nom du compte de stockage dépend du nom de la machine virtuelle.

## Sommaire

-   [Création d’un compte de stockage][Création d’un compte de stockage]
-   [Étapes suivantes][Étapes suivantes]

## <span id="create"></span></a> Créer un compte de stockage

1.  Connectez-vous au [portail de gestion][portail de gestion].

2.  Cliquez sur **Create New**, sur **Stockage**, puis sur **Quick Create**.

    ![NewStorageAccount][NewStorageAccount]

3.  Sous **URL**, entrez un nom de sous-domaine à inclure dans l'URL du compte de stockage. Pour accéder à un objet qui est stocké, ajoutez l'emplacement de l'objet au point de terminaison. For example, the URL for accessing a blob might be http://*myaccount*.blob.core.windows.net/*mycontainer*/*myblob\*.

4.  Sous **Région/Groupe d'affinités**, sélectionnez une zone ou un groupe d'affinités pour le stockage. Sélectionnez un groupe d'affinités plutôt qu'une zone pour que vos services de stockage soient hébergés dans le même centre de données que les autres services Azure que vous utilisez. Ce choix peut améliorer les performances et n'entraîne aucun frais supplémentaire.

    > [WACOM.NOTE] To create an affinity group, open the **Settings** area of the Management Portal, click **Affinity Groups**, and then click either **Add an affinity group** or the **Add** button. Vous pouvez également créer et gérer des groupes d'affinités par le biais de l'API Azure Service Management. Pour plus d'informations, consultez la page [Opérations sur les groupes d'affinités][Opérations sur les groupes d'affinités].

5.  Si vous disposez de plusieurs abonnements Azure, le champ **Subscription** s'affiche. Dans **Subscription**, entrez l'abonnement Azure à utiliser avec le compte de stockage. Vous pouvez créer jusqu'à cinq comptes de stockage par abonnement.

6.  Sous **Replication**, sélectionnez le niveau de réplication souhaité pour votre compte de stockage.

    [WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

7.  Cliquez sur **Create Storage Account**.

    La création de votre compte de stockage peut prendre plusieurs minutes. Pour vérifier l'état d'avancement de l'opération, vous pouvez contrôler les notifications au bas du portail. Une fois que le compte de stockage est créé, le statut **Online** lui est associé et il est prêt à être utilisé.

    ![StoragePage][StoragePage]

## <span id="next"></span></a>Étapes suivantes

-   To learn more about Azure Storage, see the Azure Storage documentation on [azure.com][azure.com] and on [MSDN][MSDN].

-   Consultez le [Blog de l'équipe Azure Storage][Blog de l'équipe Azure Storage].

  [Azure Storage Scalability and Performance Targets]: http://msdn.microsoft.com/fr-fr/library/dn249410.aspx
  [Création d’un compte de stockage]: #create
  [Étapes suivantes]: #next
  [portail de gestion]: https://manage.windowsazure.com
  [NewStorageAccount]: ./media/storage-create-storage-account/storage_NewStorageAccount.png
  [Opérations sur les groupes d'affinités]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee460798.aspx
  [storage-replication-options]: ../includes/storage-replication-options.md
  [StoragePage]: ./media/storage-create-storage-account/Storage_StoragePage.png
  [azure.com]: http://azure.microsoft.com/fr-fr/documentation/services/storage/
  [MSDN]: http://msdn.microsoft.com/fr-fr/library/gg433040.aspx
  [Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
