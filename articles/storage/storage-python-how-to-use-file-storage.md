---
title: "Utilisation du stockage de fichiers Azure à partir de Python | Microsoft Docs"
description: "Découvrez comment utiliser le stockage de fichiers Azure à partir de Python pour charger, répertorier, télécharger et supprimer des fichiers."
services: storage
documentationcenter: python
author: robinsh
manager: timlt
editor: tysonn
ms.assetid: 297f3a14-6b3a-48b0-9da4-db5907827fb5
ms.service: storage
ms.workload: storage
ms.tgt_pltfrm: na
ms.devlang: python
ms.topic: article
ms.date: 12/08/2016
ms.author: robinsh
ms.translationtype: Human Translation
ms.sourcegitcommit: fefebeae665ccd14f15b0197241b30d33830fd09
ms.openlocfilehash: 9973da827ea5a9311904d7d6d4c22d59b5e2d0ce
ms.contentlocale: fr-fr
ms.lasthandoff: 11/17/2016


---
<a id="how-to-use-azure-file-storage-from-python" class="xliff"></a>

# Utilisation du stockage de fichiers Azure à partir de Python
[!INCLUDE [storage-selector-file-include](../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../includes/storage-try-azure-tools-files.md)]

<a id="overview" class="xliff"></a>

## Vue d'ensemble
Cet article décrit le déroulement de scénarios courants dans le cadre de l’utilisation du stockage de fichiers. Les exemples sont écrits en Python et utilisent le [Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]. Les scénarios traités incluent le téléchargement (vers une cible), la création de listes, le téléchargement (à partir d'une source) et la suppression de fichiers.

[!INCLUDE [storage-file-concepts-include](../../includes/storage-file-concepts-include.md)]

[!INCLUDE [storage-create-account-include](../../includes/storage-create-account-include.md)]

<a id="create-a-share" class="xliff"></a>

## Créer un partage
L’objet **FileService** permet d'utiliser des partages, des répertoires et des fichiers. Le code suivant permet de créer un objet **FileService** . Ajoutez ce qui suit vers le début de tout fichier Python dans lequel vous souhaitez accéder à Azure Storage par programme.

```python
from azure.storage.file import FileService
```

Le code suivant permet de créer un objet **FileService** en utilisant le nom et la clé du compte de stockage.  Remplacez « myaccount » et « mykey » par le nom et la clé réels de votre compte.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

Dans l’exemple de code suivant, vous pouvez utiliser un objet **FileService** pour créer le partage s’il n’existe pas.

```python
file_service.create_share('myshare')
```

<a id="upload-a-file-into-a-share" class="xliff"></a>

## Télécharger un fichier dans un partage
Un partage de fichiers de stockage Azure contient au minimum un répertoire racine dans lequel les fichiers peuvent résider. Cette section décrit comment télécharger un fichier du stockage local vers le répertoire racine d’un partage.

Pour créer un fichier et télécharger des données, utilisez les méthodes **create\_file\_from\_path**, **create\_file\_from\_stream**, **create\_file\_from\_bytes** ou **create\_file\_from\_text**. Il s'agit de méthodes de haut niveau qui effectuent la segmentation nécessaire lorsque la taille des données est supérieure à 64 Mo.

**create\_file\_from\_path** télécharge le contenu d’un fichier à partir du chemin spécifié, et **create\_file\_from\_stream** télécharge le contenu à partir d’un flux/fichier déjà ouvert. **create\_file\_from\_bytes** télécharge un tableau d’octets, et **create\_file\_from\_text** télécharge la valeur texte spécifiée à l’aide de l’encodage spécifié (par défaut UTF-8).

L’exemple suivant charge le contenu du fichier **sunset.png** dans le fichier **myfile**.

```python
from azure.storage.file import ContentSettings
file_service.create_file_from_path(
    'myshare',
    None, # We want to create this blob in the root directory, so we specify None for the directory_name
    'myfile',
    'sunset.png',
    content_settings=ContentSettings(content_type='image/png'))
```

<a id="how-to-create-a-directory" class="xliff"></a>

## Création d’un répertoire
Vous pouvez également organiser le stockage en plaçant des fichiers dans des sous-répertoires, plutôt que de tous les mettre dans le répertoire racine. Le service de stockage de fichiers Azure permet de créer autant de répertoires que le permet votre compte. Le code ci-dessous crée un sous-répertoire nommé **sampledir** sous le répertoire racine.

```python
file_service.create_directory('myshare', 'sampledir')
```

<a id="how-to-list-files-and-directories-in-a-share" class="xliff"></a>

## Obtention d’une liste des fichiers et répertoires d’un partage
Pour répertorier les fichiers et répertoires d’un partage, utilisez la méthode **list\_directories\_and\_files**. Cette méthode retourne un générateur. Le code suivant sort le **nom** de chaque fichier et répertoire d'un partage sur la console.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

<a id="download-files" class="xliff"></a>

## Téléchargement de fichiers
Pour télécharger des données d’un fichier, utilisez **get\_file\_to\_path**, **get\_file\_to\_stream**, **get\_file\_to\_bytes** ou **get\_file\_to\_text**. Il s'agit de méthodes de haut niveau qui effectuent la segmentation nécessaire lorsque la taille des données est supérieure à 64 Mo.

L’exemple suivant illustre l’utilisation de **get\_file\_to\_path** pour télécharger le contenu du fichier **myfile** et le stocker dans le fichier **out-sunset.png**.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

<a id="delete-a-file" class="xliff"></a>

## Supprimer un fichier
Enfin, pour supprimer un fichier, appelez **delete_file**.

```python
file_service.delete_file('myshare', None, 'myfile')
```

<a id="next-steps" class="xliff"></a>

## Étapes suivantes
Maintenant que vous connaissez les bases du stockage de fichiers, consultez les liens suivants pour en savoir plus.

* [Centre de développement Python](/develop/python/)
* [API REST des services d’Azure Storage](http://msdn.microsoft.com/library/azure/dd179355)
* [Blog de l'équipe Azure Storage]
* [Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]

[Blog de l'équipe Azure Storage]: http://blogs.msdn.com/b/windowsazurestorage/
[Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python]: https://github.com/Azure/azure-storage-python

