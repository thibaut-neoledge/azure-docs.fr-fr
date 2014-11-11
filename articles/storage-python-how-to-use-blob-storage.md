<properties linkid="develop-python-blob-service" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Python) | Microsoft Azure" metaKeywords="Azure blob service Python, Azure blobs Python" description="Learn how to use the Azure Blob service to upload, list, download, and delete blobs." metaCanonical="" disqusComments="1" umbracoNaviHide="0" services="storage" documentationCenter="Python" title="How to use the Blob service from Python" authors="huvalo" videoId="" scriptId="" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="01/01/1900" ms.author="huvalo" />

# Utilisation du service de stockage d'objets blob à partir de Python

Ce guide décrit le déroulement de scénarios courants dans le cadre de
l'utilisation du service de stockage d'objets blob Azure. Les exemples sont écrits à l'aide de
l'API Python. Les scénarios traités incluent le **téléchargement (vers une cible)**, la **création de listes**, le
**téléchargement (à partir d'une source)** et la
**suppression** d'objets blob. Pour plus d'informations sur les
objets blob, consultez la section [Étapes suivantes][Étapes suivantes].

## Sommaire

[Présentation du stockage d'objets blob][Présentation du stockage d'objets blob]
 [Concepts][Concepts]
 [Création d'un compte de stockage Azure][Création d'un compte de stockage Azure]
 [Création d'un conteneur][Création d'un conteneur]
 [Téléchargement d'un objet blob dans un conteneur][Téléchargement d'un objet blob dans un conteneur]
 [Création d'une liste d'objets blob dans un conteneur][Création d'une liste d'objets blob dans un conteneur]
 [Téléchargement d'objets blob][Téléchargement d'objets blob]
 [Suppression d'un objet blob][Suppression d'un objet blob]
 [Chargement et téléchargement d'objets blob volumineux][Chargement et téléchargement d'objets blob volumineux]
 [Étapes suivantes][Étapes suivantes]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a name="create-account"> </a>Création d'un compte Azure Storage

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-container"> </a> Création d'un conteneur

**Remarque :** si vous devez installer Python ou les bibliothèques clientes, consultez le [Guide d'installation de Python][Guide d'installation de Python].

L'objet **BlobService** permet d'utiliser des conteneurs et des objets blob. Le code
suivant crée un objet **BlobService**. Ajoutez ce qui suit au
début de chaque fichier Python dans lequel vous souhaitez accéder à Azure Storage par programme :

    from azure.storage import *

Le code suivant crée un objet **BlobService** en utilisant le nom et la clé du compte de stockage. Remplacez « myaccount » et « mykey » par le compte et la clé réels.

    blob_service = BlobService(account_name='myaccount', account_key='mykey')

Tous les objets blob de stockage résident dans un conteneur. Si le conteneur n'existe pas, vous pouvez utiliser un objet **BlobService** pour le créer :

    blob_service.create_container('mycontainer')

Le nouveau conteneur est privé par défaut et vous devez indiquer votre clé d'accès de stockage (comme précédemment) pour télécharger des objets blob depuis ce conteneur. Si vous voulez que les fichiers du conteneur soient publics, vous pouvez créer le conteneur et transmettre le niveau d'accès public en utilisant le code suivant :

    blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

Vous pouvez également modifier un conteneur déjà créé à l'aide du code suivant :

    blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

Après cette modification, tous les utilisateurs d'Internet peuvent afficher les objets blob d'un conteneur public, mais vous êtes le seul à pouvoir les modifier ou les supprimer.

## <a name="upload-blob"> </a> Téléchargement d'un objet blob dans un conteneur

Pour télécharger un objet blob, utilisez les méthodes **put\_block\_blob\_from\_path**, **put\_block\_blob\_from\_file**, **put\_block\_blob\_from\_bytes** ou **put\_block\_blob\_from\_text**. Il existe des méthodes de haut niveau qui exécutent la segmentation nécessaire lorsque la taille des données dépasse 64 Mo.

**put\_block\_blob\_from\_path** télécharge le contenu d'un fichier depuis le chemin spécifié. **put\_block\_blob\_from\_file** télécharge le contenu d'un fichier/flux déjà ouvert. **put\_block\_blob\_from\_bytes** télécharge un tableau d'octets. **put\_block\_blob\_from\_text** télécharge la valeur de texte spécifiée en utilisant l'encodage spécifié (par défaut, UTF-8).

Dans l'exemple suivant, le contenu du fichier **task1.txt** est téléchargé dans l'objet blob **myblob**.

    blob_service.put_block_blob_from_path('mycontainer', 'myblob', 'task1.txt')

## <a name="list-blob"> </a> Création d'une liste d'objets blob dans un conteneur

Pour créer la liste des objets blob d'un conteneur, utilisez la méthode **list\_blobs** avec une boucle
**for** pour afficher le nom de chaque objet blob du conteneur. Le code suivant génère le **nom** et l'**url** de chaque objet blob d'un conteneur sur la console.

    blobs = blob_service.list_blobs('mycontainer')
    for blob in blobs:
        print(blob.name)
        print(blob.url)

## <a name="download-blobs"> </a> Téléchargement d'objets blob

Pour télécharger les données d'un objet blob, utilisez **get\_blob\_to\_path**, **get\_blob\_to\_file**, **get\_blob\_to\_bytes** ou **get\_blob\_to\_text**. Il existe des méthodes de haut niveau qui exécutent la segmentation nécessaire lorsque la taille des données dépasse 64 Mo.

L'exemple suivant illustre l'utilisation de **get\_blob\_to\_path** pour télécharger le contenu de l'objet blob **myblob** et le stocker dans le fichier **out-task1.txt** :

    blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-task1.txt')

## <a name="delete-blobs"> </a> Suppression d'un objet blob

Pour supprimer un objet blob, appelez **delete\_blob**.

    blob_service.delete_blob('mycontainer', 'myblob') 

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez appris les bases du stockage d'objets blob, suivez ces liens pour apprendre des tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure][Stockage et accessibilité des données dans Azure]
-   Consultez le [blog de l'équipe Azure Storage][blog de l'équipe Azure Storage]

  [Étapes suivantes]: #next-steps
  [Présentation du stockage d'objets blob]: #what-is
  [Concepts]: #concepts
  [Création d'un compte de stockage Azure]: #create-account
  [Création d'un conteneur]: #create-container
  [Téléchargement d'un objet blob dans un conteneur]: #upload-blob
  [Création d'une liste d'objets blob dans un conteneur]: #list-blob
  [Téléchargement d'objets blob]: #download-blobs
  [Suppression d'un objet blob]: #delete-blobs
  [Chargement et téléchargement d'objets blob volumineux]: #large-blobs
  [howto-blob-storage]: ../includes/howto-blob-storage.md
  [create-storage-account]: ../includes/create-storage-account.md
  [Guide d'installation de Python]: ../python-how-to-install/
  [Stockage et accessibilité des données dans Azure]: http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433040.aspx
  [blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
