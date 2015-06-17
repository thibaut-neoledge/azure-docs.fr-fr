<properties 
	pageTitle="Utilisation du stockage d'objets blob à partir de Python | Microsoft Azure" 
	description="Découvrez comment utiliser le service BLOB Azure à partir de Python pour charger, répertorier, télécharger et supprimer des objets blob." 
	services="storage" 
	documentationCenter="python" 
	authors="huguesv" 
	manager="wpickett" 
	editor=""/>

<tags 
	ms.service="storage" 
	ms.workload="storage" 
	ms.tgt_pltfrm="na" 
	ms.devlang="python" 
	ms.topic="article" 
	ms.date="03/11/2015" 
	ms.author="huvalo"/>

# Utilisation du stockage d'objets blob à partir de Python

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Vue d'ensemble

Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du
service de stockage d'objets blob Azure. Les exemples sont écrits en Python et utilisent le [package Azure Python][]. Les scénarios traités incluent le **chargement**, l'**énumération**,
le **téléchargement** et la **suppression** d'objets blob.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Procédure : Création d'un conteneur

> [AZURE.NOTE] Si vous devez installer Python ou le [package Azure Python][], veuillez consulter le [Guide d'installation de Python] (python-how-to-install.md).


L'objet **BlobService** permet d'utiliser des conteneurs et des objets blob. Le code suivant crée un objet **BlobService**. Ajoutez ce qui suit vers le début de chaque fichier Python dans lequel vous souhaitez accéder à Azure Storage par programme :

	from azure.storage import BlobService

Le code suivant permet de créer un objet **BlobService** en utilisant le nom et la clé du compte de stockage.  Remplacez 'myaccount' et 'mykey' par le compte et la clé réels.

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

Tous les objets blob de stockage résident dans un conteneur. Si le conteneur n'existe pas, vous pouvez utiliser un objet **BlobService** pour le créer :

	blob_service.create_container('mycontainer')

Le nouveau conteneur est privé par défaut et vous devez indiquer votre clé d'accès de stockage (comme précédemment) pour télécharger des objets blob depuis ce conteneur. Si vous voulez que les fichiers du conteneur soient publics, vous pouvez créer le conteneur et transmettre le niveau d'accès public en utilisant le code suivant :

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

Vous pouvez également modifier un conteneur déjà créé à l'aide du code suivant :

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

Après cette modification, tous les utilisateurs d'Internet peuvent afficher les objets blob d'un conteneur public, mais vous êtes le seul à pouvoir les modifier ou les supprimer.

## Procédure : Téléchargement d'un objet blob dans un conteneur

Pour charger des données dans un objet blob, utilisez les méthodes **put_block_blob_from_path**, **put_block_blob_from_file**, **put_block_blob_from_bytes** ou **put_block_blob_from_text**. Il s'agit de méthodes de haut niveau qui effectuent la segmentation nécessaire lorsque la taille des données est supérieure à 64 Mo.

**put_block_blob_from_path** charge le contenu d'un fichier à partir du chemin d'accès spécifié ; **put_block_blob_from_file** charge le contenu à partir d'un flux/fichier déjà ouvert. **put_block_blob_from_bytes** charge un tableau d'octets ; **put_block_blob_from_text** charge la valeur de texte spécifiée à l'aide de l'encodage spécifié (par défaut, UTF-8).

L'exemple suivant charge le contenu du fichier **sunset.png** dans l'objet blob **myblob**.

	blob_service.put_block_blob_from_path(
        'mycontainer',
        'myblob',
        'sunset.png',
        x_ms_blob_content_type='image/png'
    )

## Procédure : Création d'une liste d'objets blob dans un conteneur

Pour énumérer les objets blob dans un conteneur, utilisez la méthode **list_blobs** avec une boucle
**for** pour afficher le nom de chaque objet blob du conteneur. Le code suivant génère le nom (**name**) et l'URL (**url**) de chaque objet blob d'un conteneur sur la console.

	blobs = blob_service.list_blobs('mycontainer')
	for blob in blobs:
		print(blob.name)
		print(blob.url)

## Procédure : Téléchargement d'objets blob

Pour télécharger des données à partir d'un objet blob, utilisez **get_blob_to_path**, **get_blob_to_file**, **get_blob_to_bytes** ou **get_blob_to_text**. Il s'agit de méthodes de haut niveau qui effectuent la segmentation nécessaire lorsque la taille des données est supérieure à 64 Mo.

L'exemple suivant illustre l'utilisation de **get_blob_to_path** pour télécharger le contenu de l'objet blob **myblob** et le stocker dans le fichier **out-sunset.png** :

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-sunset.png')

## Procédure : Suppression d'un objet blob

Pour supprimer un objet blob, appelez **delete_blob**.

	blob_service.delete_blob('mycontainer', 'myblob') 

## Étapes suivantes

Maintenant que vous connaissez les bases du stockage des objets blob, consultez les liens suivants pour apprendre à exécuter les tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure][]
-   Visiter le [Blog de l'équipe Azure Storage][]

[Stockage et accessibilité des données dans Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
[Package Azure Python]: https://pypi.python.org/pypi/azure  

<!--HONumber=49--> 