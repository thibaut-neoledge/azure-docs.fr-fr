<properties linkid="develop-python-blob-service" urlDisplayName="Blob Service" pageTitle="How to use blob storage (Python) | Microsoft Azure" metaKeywords="Azure blob service Python, Azure blobs Python" description="Learn how to use the Azure Blob service to upload, list, download, and delete blobs." metaCanonical="" disqusComments="1" umbracoNaviHide="0" services="storage" documentationCenter="Python" title="How to use the Blob service from Python" authors="" videoId="" scriptId="" />

Utilisation du service de stockage d'objets blob à partir de Python
===================================================================

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du service de stockage d'objets blob Azure. Les exemples sont écrits en utilisant l'API Python. Les scénarios traités incluent le **téléchargement (vers une cible)**, la **création de listes**, le **téléchargement (à partir d'une source)** et la **suppression** d'objets blob. Pour plus d'informations sur les objets blob, consultez la section [Étapes suivantes](#next-steps).

Sommaire
--------

[Présentation du stockage d'objets blob](#what-is)
 [Concepts](#concepts)
 [Création d'un compte de stockage Azure](#create-account)
 [Création d'un conteneur](#create-container)
 [Téléchargement d'un objet blob dans un conteneur](#upload-blob)
 [Création d'une liste d'objets blob dans un conteneur](#list-blob)
 [Téléchargement d'objets blob](#download-blobs)
 [Suppression d'un objet blob](#delete-blobs)
 [Chargement et téléchargement d'objets blob volumineux](#large-blobs)
 [Étapes suivantes](#next-steps)

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

Création d'un compte Azure Storage
----------------------------------

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

Création d'un conteneur
-----------------------

**Remarque :** si vous devez installer Python ou les bibliothèques clientes, consultez le [Guide d'installation de Python](../python-how-to-install/).

L'objet **BlobService** permet d'utiliser des conteneurs et des objets blob. Le code suivant crée un objet **BlobService**. Ajoutez ce qui suit vers le début de chaque fichier Python dans lequel vous souhaitez accéder à Azure Storage par programme :

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

Téléchargement d'un objet blob dans un conteneur
------------------------------------------------

Pour télécharger un fichier vers un objet blob, utilisez la méthode **put\_blob** pour créer l'objet blob, en utilisant un flux de fichier comme contenu de l'objet blob. Commencez par créer un fichier nommé **task1.txt** (du contenu arbitraire suffit) et stockez-le dans le même répertoire que votre fichier Python.

    myblob = open(r'task1.txt', 'r').read()
    blob_service.put_blob('mycontainer', 'myblob', myblob, x_ms_blob_type='BlockBlob')

Création d'une liste d'objets blob dans un conteneur
----------------------------------------------------

Pour créer la liste des objets blob d'un conteneur, utilisez la méthode **list\_blobs** avec une boucle **for** pour afficher le nom de chaque objet blob du conteneur. Le code suivant génère le **nom** et l'**url** de chaque objet blob d'un conteneur sur la console.

    blobs = blob_service.list_blobs('mycontainer')
    for blob in blobs:
        print(blob.name)
        print(blob.url)

Téléchargement d'objets blob
----------------------------

Pour télécharger des objets blob, utilisez la méthode **get\_blob** pour transférer les contenus d'objets blob vers un objet de flux pouvant être rendu persistant dans un fichier local.

    blob = blob_service.get_blob('mycontainer', 'myblob')
    with open(r'out-task1.txt', 'w') as f:
        f.write(blob)

Suppression d'un objet blob
---------------------------

Pour supprimer un objet blob, appelez **delete\_blob**.

    blob_service.delete_blob('mycontainer', 'myblob') 

Chargement et téléchargement d'objets blob volumineux
-----------------------------------------------------

La taille maximale d'un objet blob de blocs est de 200 Go. Les objets blob de moins de 64 Mo peuvent être chargés ou téléchargés à l'aide d'un seul appel à **put\_blob** ou **get\_blob**, comme indiqué précédemment. Les objets blob de plus de 64 Mo doivent être chargés ou téléchargés dans des blocs de 4 Mo ou moins.

Le code suivant montre des exemples de fonctions permettant de charger ou de télécharger des objets blob de blocs de n'importe quelle taille.

    import base64

    chunk_size = 4 * 1024 * 1024

    def upload(blob_service, container_name, blob_name, file_path):
        blob_service.create_container(container_name, None, None, False)
        blob_service.put_blob(container_name, blob_name, '', 'BlockBlob')

        block_ids = []
        index = 0
        with open(file_path, 'rb') as f:
            while True:
                data = f.read(chunk_size)
                if data:
                    length = len(data)
                    block_id = base64.b64encode(str(index))
                    blob_service.put_block(container_name, blob_name, data, block_id)
                    block_ids.append(block_id)
                    index += 1
                else:
                    break

        blob_service.put_block_list(container_name, blob_name, block_ids)

    def download(blob_service, container_name, blob_name, file_path):
        props = blob_service.get_blob_properties(container_name, blob_name)
        blob_size = int(props['content-length'])

        index = 0
        with open(file_path, 'wb') as f:
            while index < blob_size:
                chunk_range = 'bytes={}-{}'.format(index, index + chunk_size - 1)
                data = blob_service.get_blob(container_name, blob_name, x_ms_range=chunk_range)
                length = len(data)
                index += length
                if length > 0:
                    f.write(data)
                    if length < chunk_size:
                        break
                else:
                    break

Si vous avez besoin d'objets blob de plus de 200 Go, vous pouvez utiliser un objet blob de pages plutôt qu'un objet blob de blocs. La taille maximale d'un objet blob de pages est de 1 To, avec des pages s'alignant aux limites de page de 512 octets. Utilisez **put\_blob** pour créer un objet blob de pages, **put\_page** pour écrire dedans et **get\_blob** pour le lire.

Étapes suivantes
----------------

Maintenant que vous avez appris les bases du stockage d'objets blob, suivez ces liens pour apprendre des tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure](http://msdn.microsoft.com/fr-fr/library/windowsazure/gg433040.aspx)
-   Consultez le [blog de l'équipe Azure Storage](http://blogs.msdn.com/b/windowsazurestorage/)

