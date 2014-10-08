<properties pageTitle="Use Mobile Services to upload images to blob storage (Windows Store) | Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to upload images to Azure Blob Storage." metaCanonical="" disqusComments="0" umbracoNaviHide="1" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" writer="glenga" services="mobile-services, storage" />

<tags ms.service="mobile-services" ms.workload="mobile" ms.tgt_pltfrm="mobile-windows-store" ms.devlang="dotnet" ms.topic="article" ms.date="01/01/1900" ms.author="glenga"></tags>

# Téléchargement d'images vers Azure Storage à l'aide de Mobile Services

<div class="dev-center-tutorial-selector sublanding"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage" title="Windows Store C#" class="current">Windows Store C#</a><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage" title="Windows Phone">Windows Phone</a></div>

<div class="dev-center-tutorial-subselector"><a href="/en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage" title=".NET backend" class="current">.NET backend</a> | <a href="/en-us/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage"  title="JavaScript backend">JavaScript backend</a></div>

Cette rubrique vous montre comment utiliser Azure Mobile Services pour permettre à votre application de télécharger et de stocker les images générées par l'utilisateur dans Azure Storage. Mobile Services utilise une base de données SQL pour stocker les données. Toutefois, les données BLOB (Binary Large Object) sont stockées avec plus d'efficacité dans le service de stockage d'objets blob Azure.

Vous ne pouvez pas distribuer de manière sécurisée les informations d'identification nécessaires à un téléchargement sécurisé de données vers le service de stockage d'objets blob avec l'application cliente. Au lieu de cela, vous devez stocker ces informations d'identification dans votre service mobile et vous en servir pour générer une signature d'accès partagé qui sera utilisée pour télécharger une nouvelle image. La signature d'accès partagé, information d'identification dont le délai d'expiration est très court (dans ce cas, 5 minutes), est renvoyée de manière sécurisée par Mobile Services à l'application cliente. L'application utilise ensuite cette information d'identification provisoire pour télécharger l'image. Dans cet exemple, les téléchargements à partir du service BLOB sont publics.

Dans ce didacticiel, vous allez ajouter une fonctionnalité à l'application de démarrage rapide Mobile Services pour prendre des photos et télécharger les images vers Azure en utilisant une signature d'accès partagé générée par Mobile Services. Ce didacticiel vous guide tout au long des étapes de base suivantes pour mettre à jour le démarrage rapide de Mobile Services en vue de télécharger des images vers le service de stockage d'objets blob :

1.  [Installation de la bibliothèque du client de stockage][]
2.  [Mise à jour de l'application cliente pour capturer des images][]
3.  [Installation du client de stockage dans le projet de service mobile][]
4.  [Mise à jour de la définition TodoItem dans le modèle de données][]
5.  [Mise à jour du contrôleur de table pour générer une signature d'accès partagé][]
6.  [Téléchargement des images pour tester l'application][]

Ce didacticiel requiert les éléments suivants :

-   Microsoft Visual Studio 2013 ou une version ultérieure.
-   Gestionnaire de package Nuget pour Microsoft Visual Studio ;
-   [un compte Azure Storage ;][]

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez effectuer le didacticiel [Prise en main de Mobile Services][].

[WACOM.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage][]]

## <a name="install-storage-client"></a>Installation du client de stockage pour les applications du Windows Store

Avant de pouvoir utiliser une signature d'accès partagé en vue de télécharger des images depuis votre application vers un stockage d'objets blob, vous devez ajouter le package NuGet qui installe la bibliothèque du client de stockage pour les applications du Windows Store.

1.  Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le projet d'application cliente, puis sélectionnez **Gérer les packages NuGet**.

2.  Dans le volet gauche, sélectionnez la catégorie **Online**, sélectionnez **Include Prerelease**, recherchez **WindowsAzure.Storage-Preview**, cliquez sur **Install** au niveau du package **Azure Storage**, puis acceptez les contrats de licence.

    ![][]

    La bibliothèque du client des services de stockage Azure est alors ajoutée au projet.

Dans la prochaine étape, vous allez mettre à jour l'application de démarrage rapide pour capturer et télécharger des images.

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage][]]



  [Windows Store C\#]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage "Windows Store C#"
  [Windows Phone]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage "Windows Phone"
  [.NET backend]: /en-us/documentation/articles/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage ".NET backend"
  [JavaScript backend]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage "JavaScript backend"
  [Installation de la bibliothèque du client de stockage]: #install-storage-client
  [Mise à jour de l'application cliente pour capturer des images]: #add-select-images
  [Installation du client de stockage dans le projet de service mobile]: #storage-client-server
  [Mise à jour de la définition TodoItem dans le modèle de données]: #update-data-model
  [Mise à jour du contrôleur de table pour générer une signature d'accès partagé]: #update-scripts
  [Téléchargement des images pour tester l'application]: #test
  [un compte Azure Storage ;]: /en-us/documentation/articles/storage-create-storage-account/
  [Prise en main de Mobile Services]: /en-us/documentation/articles/mobile-services-windows-store-dotnet-get-started
  [mobile-services-dotnet-backend-configure-blob-storage]: ../includes/mobile-services-dotnet-backend-configure-blob-storage.md
  []: ./media/mobile-services-dotnet-backend-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png
  [mobile-services-windows-store-dotnet-upload-to-blob-storage]: ../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md
