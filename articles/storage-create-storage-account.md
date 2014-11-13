<properties urlDisplayName="How to create" pageTitle="Cr&eacute;ation d'un compte de stockage | Azure" metaKeywords="" description="D&eacute;couvrez comment cr&eacute;er un compte de stockage dans le portail de gestion Azure." metaCanonical="" services="storage" documentationCenter="" title="Cr&eacute;ation d'un compte de stockage" solutions="" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />

# <span id="createstorageaccount"></span></a>Création d'un compte de stockage

Pour stocker des fichiers et des données dans les services d'objets blob, de tables, de files d'attente et de fichiers d'Azure, il est nécessaire de créer un compte de stockage dans la zone géographique où doivent être stockées les données. Cette rubrique décrit la procédure à suivre pour créer un compte de stockage sur le portail de gestion Azure.

Pour plus d’informations sur la capacité et le débit du compte de stockage, consultez la page [Objectifs d'évolutivité et de performances d'Azure Storage][Objectifs d'évolutivité et de performances d'Azure Storage].

> [WACOM.NOTE] Lorsqu'une machine virtuelle Azure est utilisée, un compte de stockage est créé automatiquement sur le lieu de déploiement si vous ne disposez pas déjà de ce type de compte à cet emplacement. Le nom du compte de stockage dépend du nom de la machine virtuelle.

## Sommaire

-   [Création d’un compte de stockage][Création d’un compte de stockage]
-   [Étapes suivantes][Étapes suivantes]

## <span id="create"></span></a> Créer un compte de stockage

1.  Connectez-vous au [portail de gestion][portail de gestion].

2.  Cliquez sur **Create New**, sur **Stockage**, puis sur **Quick Create**.

    ![NewStorageAccount][NewStorageAccount]

3.  Sous **URL**, entrez un nom de sous-domaine à inclure dans l'URL du compte de stockage. Pour accéder à un objet qui est stocké, ajoutez l'emplacement de l'objet au point de terminaison. Par exemple, l'URL permettant d'accéder à un objet blob peut être http://*mon\_compte*.blob.core.windows.net/*mon\_conteneur*/*mon\_blob*.

4.  Sous **Région/Groupe d'affinités**, sélectionnez une zone ou un groupe d'affinités pour le stockage. Sélectionnez un groupe d'affinités plutôt qu'une zone pour que vos services de stockage soient hébergés dans le même centre de données que les autres services Azure que vous utilisez. Ce choix peut améliorer les performances et n'entraîne aucun frais supplémentaire.

    > [WACOM.NOTE] Pour créer un groupe d'affinités, ouvrez la zone **Paramètres** du portail de gestion, cliquez sur **Groupes d'affinités**, puis sur **Ajouter un groupe d'affinités** ou sur **Ajouter**. Vous pouvez également créer et gérer des groupes d'affinités par le biais de l'API Azure Service Management. Pour plus d'informations, consultez la page [Opérations sur les groupes d'affinités][Opérations sur les groupes d'affinités].

5.  Si vous disposez de plusieurs abonnements Azure, le champ **Subscription** s'affiche. Dans **Subscription**, entrez l'abonnement Azure à utiliser avec le compte de stockage. Vous pouvez créer jusqu'à cinq comptes de stockage par abonnement.

6.  Sous **Replication**, sélectionnez le niveau de réplication souhaité pour votre compte de stockage.

    [WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

7.  Cliquez sur **Create Storage Account**.

    La création de votre compte de stockage peut prendre plusieurs minutes. Pour vérifier l'état d'avancement de l'opération, vous pouvez contrôler les notifications au bas du portail. Une fois que le compte de stockage est créé, le statut **Online** lui est associé et il est prêt à être utilisé.

    ![StoragePage][StoragePage]

## <span id="next"></span></a>Étapes suivantes

-   Pour en savoir plus sur Azure Storage, consultez la documentation de référence sur [azure.com][azure.com] et [MSDN][MSDN].

-   Consultez le [Blog de l'équipe Azure Storage][Blog de l'équipe Azure Storage].

  [Objectifs d'évolutivité et de performances d'Azure Storage]: http://msdn.microsoft.com/fr-fr/library/dn249410.aspx
  [Création d’un compte de stockage]: #create
  [Étapes suivantes]: #next
  [portail de gestion]: https://manage.windowsazure.com
  [NewStorageAccount]: ./media/storage-create-storage-account/storage_NewStorageAccount.png
  [Opérations sur les groupes d'affinités]: http://msdn.microsoft.com/fr-fr/library/windowsazure/ee460798.aspx
  [StoragePage]: ./media/storage-create-storage-account/Storage_StoragePage.png
  [azure.com]: http://azure.microsoft.com/fr-fr/documentation/services/storage/
  [MSDN]: http://msdn.microsoft.com/fr-fr/library/gg433040.aspx
  [Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
