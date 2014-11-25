<properties urlDisplayName="How to create" pageTitle="Création d'un compte de stockage | Azure" metaKeywords="" description="Learn how to create a storage account in the Azure management portal." metaCanonical="" services="storage" documentationCenter="" title="How To Create a Storage Account" solutions="" authors="tamram" manager="adinah" editor="cgronlun" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="tamram" />


<h1><a id="createstorageaccount"></a>Création d'un compte de stockage</h1>

Pour stocker des fichiers et des données dans les services d'objets blob, de tables, de files d'attente et de fichiers d'Azure, il est nécessaire de créer un compte de stockage dans la zone géographique où doivent être stockées les données. Cette rubrique décrit la procédure à suivre pour créer un compte de stockage sur le portail de gestion Azure.

Pour plus d'informations sur la capacité et le débit du compte de stockage, consultez la page [Objectifs d'évolutivité et de performances d'Azure Storage](http://msdn.microsoft.com/fr-fr/library/dn249410.aspx).

> [WACOM.NOTE] Lorsqu'une machine virtuelle Azure est utilisée, un compte de stockage est créé automatiquement sur le lieu de déploiement si vous ne disposez pas déjà de ce type de compte à cet emplacement. Le nom du compte de stockage dépend du nom de la machine virtuelle.

##Sommaire##

* [ Création d'un compte de stockage](#create)
* [Étapes suivantes](#next)

<h2><a id="create"></a> Création d'un compte de stockage</h2>

1. Connectez-vous au [portail de gestion](https://manage.windowsazure.com).

2. Cliquez sur **Create New**, sur **Stockage**, puis sur **Quick Create**.

	![NewStorageAccount](./media/storage-create-storage-account/storage_NewStorageAccount.png)

3. Sous **URL**, entrez un nom de sous-domaine à inclure dans l'URL du compte de stockage. Pour accéder à un objet qui est stocké, ajoutez l'emplacement de l'objet au point de terminaison. Par exemple, l'URL permettant d'accéder à un objet blob peut être http://*mon_compte*.blob.core.windows.net/*mon_conteneur*/*mon_blob*.

4. Sous **Région/Groupe d'affinités**, sélectionnez une zone ou un groupe d'affinités pour le stockage.  Sélectionnez un groupe d'affinités plutôt qu'une zone pour que vos services de stockage soient hébergés dans le même centre de données que les autres services Azure que vous utilisez. Ce choix peut améliorer les performances et n'entraîne aucun frais supplémentaire.

	> [WACOM.NOTE]   pour créer un groupe d'affinités, ouvrez la zone <b>Paramètres</b> du portail de gestion, cliquez sur <b>Groupes d'affinités</b>, puis cliquez sur <b>Ajouter un groupe d'affinités</b> ou sur le bouton <b>Ajouter</b>. Vous pouvez également créer et gérer des groupes d'affinités par le biais de l'API Azure Service Management. Pour plus d'informations, consultez la page <a href="http://msdn.microsoft.com/fr-fr/library/windowsazure/ee460798.aspx">Opérations sur les groupes d'affinités</a>.

5. Si vous disposez de plusieurs abonnements Azure, le champ **Subscription** s'affiche. Dans **Subscription**, entrez l'abonnement Azure à utiliser avec le compte de stockage. Vous pouvez créer jusqu'à cinq comptes de stockage par abonnement.

6. Sous **Replication**, sélectionnez le niveau de réplication souhaité pour votre compte de stockage.

	[WACOM.INCLUDE [storage-replication-options](../includes/storage-replication-options.md)]

6. Cliquez sur **Create Storage Account**.

	La création de votre compte de stockage peut prendre plusieurs minutes. Pour vérifier l'état d'avancement de l'opération, vous pouvez contrôler les notifications au bas du portail. Une fois que le compte de stockage est créé, le statut **Online** lui est associé et il est prêt à être utilisé. 

	![StoragePage](./media/storage-create-storage-account/Storage_StoragePage.png)

<h2><a id="next"></a>Étapes suivantes</h2>

- Pour en savoir plus sur Azure Storage, consultez la documentation de référence sur [azure.com](http://azure.microsoft.com/fr-fr/documentation/services/storage/) et [MSDN](http://msdn.microsoft.com/fr-fr/library/gg433040.aspx). 

- Consultez le [blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/).

