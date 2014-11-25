<properties urlDisplayName="Blob Service" pageTitle="Utilisation du stockage d'objets blob (Python) | Microsoft Azure" metaKeywords="Azure blob service Python, Azure blobs Python" description="Learn how to use the Azure Blob service to upload, list, download, and delete blobs." metaCanonical="" disqusComments="1" umbracoNaviHide="0" services="storage" documentationCenter="Python" title="How to use the Blob service from Python" authors="huvalo" videoId="" scriptId="" manager="wpickett" />

<tags ms.service="storage" ms.workload="storage" ms.tgt_pltfrm="na" ms.devlang="python" ms.topic="article" ms.date="09/19/2014" ms.author="huvalo" />

# Utilisation du service de stockage d'objets blob à partir de Python
Ce guide décrit le déroulement de scénarios courants dans le cadre de l'utilisation du
service de stockage d'objets blob Azure. Les exemples sont écrits en utilisant
l'API Python. Les scénarios traités incluent **le téléchargement (vers une cible)**, **l'énumération**,
**le téléchargement (à partir d'une source)** et **la suppression** d'objets blobs. Pour plus d'informations sur les objets blob,
consultez la section [Étapes suivantes][Étapes suivantes].

## Sommaire

[Présentation du stockage d'objets blob][Présentation du stockage d'objets blob]   
 [Concepts][Concepts]   
 [Création d'un compte de stockage Azure][Création d'un compte de stockage Azure]   
 [ Création d'un conteneur][ Création d'un conteneur]   
 [ Téléchargement d'un objet blob dans un conteneur][ Téléchargement d'un objet blob dans un conteneur]   
 [ Création d'une liste d'objets blob dans un conteneur][ Création d'une liste d'objets blob dans un conteneur]   
 [ Téléchargement d'objets blob][ Téléchargement d'objets blob]   
 [ Suppression d'un objet blob][ Suppression d'un objet blob]   
 [ Chargement et téléchargement d'objets blob volumineux][ Chargement et téléchargement d'objets blob volumineux]   
 [Étapes suivantes][Étapes suivantes]

[WACOM.INCLUDE [howto-blob-storage](../includes/howto-blob-storage.md)]

## <a name="create-account"> </a>Création d'un compte de stockage Azure

[WACOM.INCLUDE [create-storage-account](../includes/create-storage-account.md)]

## <a name="create-container"> </a> Création d'un conteneur

**Remarque :** si vous devez installer Python ou les bibliothèques clientes, consultez le [Guide d'installation de Python](../python-how-to-install/).


L'objet **BlobService** permet d'utiliser des conteneurs et des objets blob. Le
code suivant crée un objet **BlobService**. Ajoutez ce qui suit vers
le début de chaque fichier Python dans lequel vous souhaitez accéder à Azure Storage par programme :

	from azure.storage import BlobService

Le code suivant permet de créer un objet **BlobService** en utilisant le nom et la clé du compte de stockage.  Remplacez " myaccount " et " mykey " par le compte et la clé réels.

	blob_service = BlobService(account_name='myaccount', account_key='mykey')

Tous les objets blob de stockage résident dans un conteneur. Si le conteneur n'existe pas, vous pouvez utiliser un objet **BlobService** pour le créer :

	blob_service.create_container('mycontainer')

Le nouveau conteneur est privé par défaut et vous devez indiquer votre clé d'accès de stockage (comme précédemment) pour télécharger des objets blob depuis ce conteneur. Si vous voulez que les fichiers du conteneur soient publics, vous pouvez créer le conteneur et transmettre le niveau d'accès public en utilisant le code suivant :

	blob_service.create_container('mycontainer', x_ms_blob_public_access='container') 

Vous pouvez également modifier un conteneur déjà créé à l'aide du code suivant :

	blob_service.set_container_acl('mycontainer', x_ms_blob_public_access='container')

Une fois cette modification effectuée, tous les utilisateurs d'Internet peuvent afficher les objets blobs d'un conteneur
public, mais vous seul pouvez les modifier ou les supprimer.

## <a name="upload-blob"> </a> Téléchargement d'un objet blob dans un conteneur

Pour télécharger des données dans un objet blob, utilisez l'une de ces méthodes : **put\_block\_blob\_from\_path**, **put\_block\_blob\_from\_file**, **put\_block\_blob\_from\_bytes** ou **put\_block\_blob\_from\_text**. Il s'agit de méthodes de haut niveau qui effectuent la segmentation nécessaire lorsque la taille des données est supérieure à 64 Mo.

**put\_block\_blob\_from\_path** télécharge le contenu d'un fichier à partir du chemin spécifié. **put\_block\_blob\_from\_file** télécharge le contenu à partir d'un fichier/flux déjà ouvert. **put\_block\_blob\_from\_bytes** télécharge une série d'octets. **put\_block\_blob\_from\_text** télécharge la valeur de texte spécifiée à l'aide du codage indiqué (UTF-8 par défaut).

L'exemple suivant télécharge le contenu du fichier **task1.txt** dans l'objet blob **myblob**.

	blob_service.put_block_blob_from_path('mycontainer', 'myblob', 'task1.txt')

## <a name="list-blob"> </a> Création d'une liste d'objets blob dans un conteneur

Pour énumérer les objets blob d'un conteneur, utilisez la méthode **list\_blobs** avec une
boucle **for** pour afficher le nom de chaque objet blob du conteneur. Le
code suivant génère le **nom** et l'**url** de chaque objet blob d'un conteneur sur la
console.

	blobs = blob_service.list_blobs('mycontainer')
	for blob in blobs:
		print(blob.name)
		print(blob.url)

## <a name="download-blobs"> </a> Téléchargement d'objets blob

Pour télécharger les données d'un objet blob, utilisez **get\_blob\_to\_path**, **get\_blob\_to\_file**, **get\_blob\_to\_bytes** ou **get\_blob\_to\_text**. Il s'agit de méthodes de haut niveau qui effectuent la segmentation nécessaire lorsque la taille des données est supérieure à 64 Mo.

L'exemple suivant présente l'utilisation de **get\_blob\_to\_path** pour télécharger le contenu de l'objet blob **myblob** et le stocker dans le fichier **out-task1.txt** :

	blob_service.get_blob_to_path('mycontainer', 'myblob', 'out-task1.txt')

## <a name="delete-blobs"> </a> Suppression d'un objet blob

Pour supprimer un objet blob, appelez **delete_blob**.

	blob_service.delete_blob('mycontainer', 'myblob') 

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous connaissez les bases du stockage d'objets blob, consultez les liens suivants
pour apprendre à réaliser des tâches de stockage plus complexes.

-   Consultez la référence MSDN suivante : [Stockage et accessibilité des données dans Azure][Stockage et accessibilité des données dans Azure]
-   Consultez le [blog de l'équipe Azure Storage][blog de l'équipe Azure Storage]

  [Étapes suivantes]: #next-steps
  [Présentation du stockage d'objets blob]: #what-is
  [Concepts]: #concepts
  [ Création d'un conteneur]: #create-container
  [ Téléchargement d'un objet blob dans un conteneur]: #upload-blob
  [ Création d'une liste d'objets blob dans un conteneur]: #list-blob
  [ Téléchargement d'objets blob]: #download-blobs
  [ Suppression d'un objet blob]: #delete-blobs
  [ Chargement et téléchargement d'objets blob volumineux]: #large-blobs
