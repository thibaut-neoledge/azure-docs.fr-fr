<properties
	pageTitle="Utilisation du stockage d’objets blob Azure à partir de Python | Microsoft Azure"
	description="Découvrez comment utiliser le service BLOB Azure à partir de Python pour charger, répertorier, télécharger et supprimer des objets blob."
	services="storage"
	documentationCenter="python"
	authors="emgerner-msft"
	manager="wpickett"
	editor=""/>

<tags
	ms.service="storage"
	ms.workload="storage"
	ms.tgt_pltfrm="na"
	ms.devlang="python"
	ms.topic="article"
	ms.date="08/25/2015"
	ms.author="emgerner"/>

# Utilisation du stockage d’objets blob Azure à partir de Python

[AZURE.INCLUDE [storage-selector-blob-include](../../includes/storage-selector-blob-include.md)]

## Vue d'ensemble

Cet article décrit le déroulement de scénarios courants dans le cadre de l’utilisation du service de stockage d’objets blob. Les exemples sont écrits en Python et utilisent le [package Python Azure Storage][]. Les scénarios traités incluent le chargement, l'énumération, le téléchargement et la suppression d'objets blob.

[AZURE.INCLUDE [storage-blob-concepts-include](../../includes/storage-blob-concepts-include.md)]

[AZURE.INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

## Création d’un conteneur

> [AZURE.NOTE]Si vous devez installer Python ou le [package Azure Python][], consultez le [Guide d’installation de Python](../python-how-to-install.md).

L'objet **BlobService** permet d'utiliser des conteneurs et des objets blob. Le code suivant crée un objet **BlobService**. Ajoutez ce qui suit vers le début de tout fichier Python dans lequel vous souhaitez accéder à Azure Storage par programme.

	from azure.storage.blob import BlobService

Le code suivant crée un objet **BlobService** en utilisant le nom et la clé du compte de stockage. Remplacez « myaccount » et « mykey » par le compte et la clé réels.

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

[AZURE.INCLUDE [storage-container-naming-rules-include](../../includes/storage-container-naming-rules-include.md)]

Dans l’exemple de code suivant, vous pouvez utiliser un objet **BlobService** pour créer le conteneur s’il n’existe pas.

	blob_service.create_container('mycontainer')

Par défaut, le nouveau conteneur est privé. Vous devez donc indiquer votre clé d’accès au stockage (comme précédemment) pour télécharger des objets blob depuis ce conteneur. Si vous voulez que les fichiers du conteneur soient accessibles à tout le monde, vous pouvez créer le conteneur et lui attribuer le niveau d’accès public en utilisant le code suivant.

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container')

L’autre possibilité consiste à modifier un conteneur déjà créé, à l’aide du code suivant.

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

Après cette modification, tous les utilisateurs d'Internet peuvent afficher les objets blob d'un conteneur public, mais vous êtes le seul à pouvoir les modifier ou les supprimer.

## Charger un objet blob dans un conteneur

Pour télécharger des données dans un objet blob, utilisez la méthode **put\_block\_blob\_from\_path**, **put\_block\_blob\_from\_file**, **put\_block\_blob\_from\_bytes** ou **put\_block\_blob\_from\_text**. Il s'agit de méthodes de haut niveau qui effectuent la segmentation nécessaire lorsque la taille des données est supérieure à 64 Mo.

**put\_block\_blob\_from\_path** télécharge le contenu d’un fichier à partir du chemin spécifié. **put\_block\_blob\_from\_file** télécharge le contenu à partir d’un fichier/flux déjà ouvert. **put\_block\_blob\_from\_bytes** télécharge une série d’octets. **put\_block\_blob\_from\_text** télécharge la valeur de texte spécifiée à l’aide du codage indiqué (UTF-8 par défaut).

L’exemple suivant charge le contenu du fichier **sunset.png** dans l’objet blob **myblob**.

	blob_service.put_block_blob_from_path(
        'mycontainer',
        'myblob',
        'sunset.png',
        x_ms_blob_content_type='image/png'
    )

## Création d'une liste d'objets blob dans un conteneur

Pour répertorier les objets blob dans un conteneur, utilisez la méthode **list\\_blobs**. Chaque appel à **list\_blobs** renvoie un segment de résultats. Pour obtenir tous les résultats, recherchez **next\_marker** dans les résultats et appelez **list\_blobs** à nouveau si nécessaire. Le code suivant sort le **nom** de chaque objet blob d'un conteneur sur la console.

	blobs = []
	marker = None
	while True:
		batch = blob_service.list_blobs('mycontainer', marker=marker)
		blobs.extend(batch)
		if not batch.next_marker:
			break
		marker = batch.next_marker
	for blob in blobs:
		print(blob.name)

## Télécharger des blobs.

Chaque segment de résultats peut contenir un nombre variable d’objets blob, pouvant aller jusqu’à 5000. Si **next\_marker** existe pour un segment particulier, il y a probablement d’autres objets blob dans le conteneur.

Pour télécharger les données d’un objet blob, utilisez **get\_blob\_to\_path**, **get\_blob\_to\_file**, **get\_blob\_to\_bytes** ou **get\_blob\_to\_text**. Il s'agit de méthodes de haut niveau qui effectuent la segmentation nécessaire lorsque la taille des données est supérieure à 64 Mo.

L’exemple suivant illustre l’utilisation de **get\_blob\_to\_path** pour télécharger le contenu de l’objet blob **myblob** et le stocker dans le fichier **out-sunset.png**.

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-sunset.png')

## Supprimer un blob.

Pour supprimer un objet blob, appelez **delete\_blob**.

	blob_service.delete_blob('mycontainer', 'myblob')

## Étapes suivantes

Maintenant que vous connaissez les bases du stockage des objets blob, consultez les liens suivants pour apprendre à exécuter les tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure][].
-   Consultez le [blog de l'équipe Azure Storage][]

[Stockage et accessibilité des données dans Azure]: http://msdn.microsoft.com/library/azure/gg433040.aspx
[blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
[package Azure Python]: https://pypi.python.org/pypi/azure
[package Python Azure Storage]: https://pypi.python.org/pypi/azure-storage

<!---HONumber=August15_HO9-->