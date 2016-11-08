---
title: Charger des images vers le stockage d’objets blob Azure à partir d’une application Windows universelle | Microsoft Docs
description: Découvrez comment utiliser un service mobile principal .NET pour charger des images vers le stockage d’objets blob Azure et y accéder à partir de votre application Windows universelle.
documentationcenter: windows
author: ggailey777
services: mobile-services,storage
manager: dwrede
editor: ''

ms.service: mobile-services
ms.workload: mobile
ms.tgt_pltfrm: mobile-windows-store
ms.devlang: dotnet
ms.topic: article
ms.date: 07/21/2016
ms.author: glenga

---
# Téléchargement d'images vers Azure Storage à l'aide de Mobile Services
[!INCLUDE [mobile-service-note-mobile-apps](../../includes/mobile-services-note-mobile-apps.md)]

&nbsp;

[!INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

## Vue d'ensemble
Cette rubrique vous montre comment utiliser Azure Mobile Services pour permettre à votre application de télécharger et de stocker les images générées par l'utilisateur dans Azure Storage. Mobile Services utilise une base de données SQL pour stocker les données. Toutefois, les données BLOB (Binary Large Object) sont stockées avec plus d'efficacité dans le service de stockage d'objets blob Azure.

Vous ne pouvez pas distribuer de manière sécurisée les informations d'identification nécessaires à un téléchargement sécurisé de données vers le service de stockage d'objets blob avec l'application cliente. Au lieu de cela, vous devez stocker ces informations d'identification dans votre service mobile et vous en servir pour générer une signature d'accès partagé qui sera utilisée pour télécharger une nouvelle image. La signature d'accès partagé, information d'identification dont le délai d'expiration est très court (dans ce cas, 5 minutes), est renvoyée de manière sécurisée par Mobile Services à l'application cliente. L'application utilise ensuite cette information d'identification provisoire pour télécharger l'image. Dans cet exemple, les téléchargements à partir du service BLOB sont publics.

Dans ce didacticiel, vous allez ajouter une fonctionnalité à l'application de démarrage rapide Mobile Services pour prendre des photos et télécharger les images vers Azure en utilisant une signature d'accès partagé générée par Mobile Services.

## Composants requis
Ce didacticiel requiert les éléments suivants :

* Microsoft Visual Studio 2013 Update 3, ou version ultérieure
* [Compte de stockage Azure](../storage/storage-create-storage-account.md)
* un appareil photo ou tout autre appareil de capture d'image raccordé à votre ordinateur.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez effectuer le didacticiel [Prise en main de Mobile Services].

[!INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

[!INCLUDE [mobile-services-windows-universal-dotnet-upload-to-blob-storage](../../includes/mobile-services-windows-universal-dotnet-upload-to-blob-storage.md)]

## Étapes suivantes
Maintenant que vous avez intégré votre service mobile au service BLOB et que vous êtes en mesure de télécharger des images en toute sécurité, consultez les autres rubriques ci-dessous relatives au service backend et à l'intégration :

* [Planifier les tâches du backend dans Mobile Services](mobile-services-dotnet-backend-schedule-recurring-tasks.md)
  
     Découvrez comment utiliser la fonctionnalité de planification de travaux Mobile Services pour définir du code de script serveur exécuté selon une planification que vous définissez.
* [Guide de fonctionnement Mobile Services .NET](mobile-services-dotnet-how-to-use-client-library.md)
  
     Découvrez plus en détail comment utiliser Mobile Services avec .NET

<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Install the storage client in the mobile service project]: #storage-client-server
[Update the TodoItem definition in the data model]: #update-data-model
[Update the table controller to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->

<!-- URLs. -->
[Prise en main de Mobile Services]: mobile-services-windows-store-dotnet-get-started.md
[How To Create a Storage Account]: ../storage/storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866

<!---HONumber=AcomDC_0727_2016-->