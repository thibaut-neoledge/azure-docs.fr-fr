---
title: "Développer pour le stockage de fichiers Azure avec Python | Microsoft Docs"
description: "Apprenez à développer des services et applications Python qui utilisent le stockage de fichiers Azure pour stocker les données de fichiers."
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
ms.translationtype: HT
ms.sourcegitcommit: 83f19cfdff37ce4bb03eae4d8d69ba3cbcdc42f3
ms.openlocfilehash: 3dd14e8d3ea7d1e50f41633a7920a6d36becf789
ms.contentlocale: fr-fr
ms.lasthandoff: 08/21/2017

---

# <a name="develop-for-azure-file-storage-with-python"></a>Développer pour le stockage de fichiers Azure avec Python
[!INCLUDE [storage-selector-file-include](../../../includes/storage-selector-file-include.md)]

[!INCLUDE [storage-try-azure-tools-files](../../../includes/storage-try-azure-tools-files.md)]

## <a name="about-this-tutorial"></a>À propos de ce didacticiel
Ce didacticiel vous montre les principes fondamentaux de l’utilisation de Python pour développer des applications ou services qui utilisent le stockage de fichiers Azure pour stocker les données de fichiers. Dans ce didacticiel, nous allons créer une application de console simple et effectuer des actions de base avec Python et le stockage de fichiers Azure :

* Créer des partages de fichiers Azure
* Créer des répertoires
* Énumérer des fichiers et répertoires dans un partage de fichiers Azure
* Charger, télécharger et supprimer un fichier

> [!Note]  
> Étant donné que le stockage de fichiers Azure est accessible sur SMB, il est possible d’écrire de simples applications qui accèdent au partage de fichiers Azure à l’aide des fonctions et des classes d’E/S Python standard. Cet article indique comment écrire des applications qui utilisent le kit de développement logiciel (SDK) Python de stockage Azure, lequel utilise [l’API REST de stockage de fichiers Azure](https://docs.microsoft.com/en-us/rest/api/storageservices/fileservices/file-service-rest-api) pour communiquer avec le stockage de fichiers Azure.

### <a name="set-up-your-application-to-use-azure-file-storage"></a>Configuration de votre application pour l’utilisation du stockage de fichiers Azure
Ajoutez ce qui suit vers le début de chaque fichier source Python dans lequel vous souhaitez accéder au stockage Azure Storage par programme.

```python
from azure.storage.file import FileService
```

### <a name="set-up-a-connection-to-azure-file-storage"></a>Configuration d’une connexion au stockage de fichiers Azure 
L’objet `FileService` vous permet d’utiliser des partages, des répertoires et des fichiers. Le code suivant crée un objet `FileService` à l’aide du nom et de la clé du compte de stockage. Remplacez `<myaccount>` et `<mykey>` par le nom et la clé de votre compte.

```python
file_service = FileService(account_name='myaccount', account_key='mykey')
```

### <a name="create-an-azure-file-share"></a>Création d’un partage de fichiers Azure
Dans l’exemple de code suivant, vous pouvez utiliser un objet `FileService` pour créer le partage s’il n’existe pas.

```python
file_service.create_share('myshare')
```

### <a name="create-a-directory"></a>Créer un répertoire
Vous pouvez également organiser le stockage en plaçant des fichiers dans des sous-répertoires, plutôt que de tous les mettre dans le répertoire racine. Le stockage de fichiers Azure vous permet de créer autant de répertoires que le permet votre compte. Le code ci-dessous crée un sous-répertoire nommé **sampledir** sous le répertoire racine.

```python
file_service.create_directory('myshare', 'sampledir')
```

### <a name="enumerate-files-and-directories-in-an-azure-file-share"></a>Énumérer des fichiers et répertoires dans un partage de fichiers Azure
Pour répertorier les fichiers et répertoires d’un partage, utilisez la méthode **list\_directories\_and\_files**. Cette méthode retourne un générateur. Le code suivant sort le **nom** de chaque fichier et répertoire d'un partage sur la console.

```python
generator = file_service.list_directories_and_files('myshare')
for file_or_dir in generator:
    print(file_or_dir.name)
```

### <a name="upload-a-file"></a>Charger un fichier 
Un partage de fichiers Azure contient au minimum un répertoire racine dans lequel les fichiers peuvent résider. Cette section décrit comment télécharger un fichier du stockage local vers le répertoire racine d’un partage.

Pour créer un fichier et charger des données, utilisez les méthodes `create_file_from_path`, `create_file_from_stream`, `create_file_from_bytes` ou `create_file_from_text`. Il s'agit de méthodes de haut niveau qui effectuent la segmentation nécessaire lorsque la taille des données est supérieure à 64 Mo.

`create_file_from_path` charge le contenu d’un fichier à partir du chemin spécifié, et `create_file_from_stream` charge le contenu à partir d’un flux/fichier déjà ouvert. `create_file_from_bytes` charge un tableau d’octets, et `create_file_from_text` charge la valeur texte spécifiée à l’aide de l’encodage spécifié (par défaut UTF-8).

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

### <a name="download-a-file"></a>Téléchargement d’un fichier
Pour télécharger des données à partir d’un fichier, utilisez `get_file_to_path`, `get_file_to_stream`, `get_file_to_bytes`, ou `get_file_to_text`. Il s'agit de méthodes de haut niveau qui effectuent la segmentation nécessaire lorsque la taille des données est supérieure à 64 Mo.

L’exemple suivant illustre l’utilisation de `get_file_to_path` pour télécharger le contenu du fichier **myfile** et le stocker dans le fichier **out-sunset.png**.

```python
file_service.get_file_to_path('myshare', None, 'myfile', 'out-sunset.png')
```

### <a name="delete-a-file"></a>Supprimer un fichier
Enfin, pour supprimer un fichier, appelez `delete_file`.

```python
file_service.delete_file('myshare', None, 'myfile')
```

## <a name="next-steps"></a>Étapes suivantes
Maintenant que vous avez appris comment manipuler le stockage de fichiers Azure avec Python, suivez ces liens pour en savoir plus.

* [Centre de développement Python](/develop/python/)
* [API REST des services d’Azure Storage](http://msdn.microsoft.com/library/azure/dd179355)
* [Kit de développement logiciel (SDK) Microsoft Azure Storage pour Python](https://github.com/Azure/azure-storage-python)
