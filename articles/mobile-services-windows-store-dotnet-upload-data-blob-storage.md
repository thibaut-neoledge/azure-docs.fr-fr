<properties pageTitle="Use Mobile Services to upload images to blob storage (Windows Store) | Mobile Services" metaKeywords="" description="Learn how to use Mobile Services to upload images to Azure Blob Storage and access the images from your Windows Store app." metaCanonical="" services="" documentationCenter="Mobile" title="Upload images to Azure Storage by using Mobile Services" authors="glenga" solutions="mobile" manager="" editor="" />

Téléchargement d'images vers Azure Storage à l'aide de Mobile Services
======================================================================

[Windows Store C\#](/en-us/documentation/articles/mobile-services-windows-store-dotnet-upload-data-blob-storage "Windows Store C#")[Windows Phone](/en-us/documentation/articles/mobile-services-windows-phone-upload-data-blob-storage "Windows Phone")

Cette rubrique vous montre comment utiliser Azure Mobile Services pour permettre à votre application de télécharger et de stocker les images générées par l'utilisateur dans Azure Storage. Mobile Services utilise une base de données SQL pour stocker les données. Toutefois, les données BLOB (Binary Large Object) sont stockées avec plus d'efficacité dans le service de stockage d'objets blob Azure.

Vous ne pouvez pas distribuer de manière sécurisée les informations d'identification nécessaires à un téléchargement sécurisé de données vers le service de stockage d'objets blob avec l'application cliente. Au lieu de cela, vous devez stocker ces informations d'identification dans votre service mobile et vous en servir pour générer une signature d'accès partagé qui sera utilisée pour télécharger une nouvelle image. La signature d'accès partagé, information d'identification dont le délai d'expiration est très court (dans ce cas, 5 minutes), est renvoyée de manière sécurisée par Mobile Services à l'application cliente. L'application utilise ensuite cette information d'identification provisoire pour télécharger l'image. Dans cet exemple, les téléchargements à partir du service BLOB sont publics.

Dans ce didacticiel, vous allez ajouter une fonctionnalité à l'application de démarrage rapide Mobile Services pour prendre des photos et télécharger les images vers Azure en utilisant une signature d'accès partagé générée par Mobile Services. Ce didacticiel vous guide tout au long des étapes de base suivantes pour mettre à jour le démarrage rapide de Mobile Services en vue de télécharger des images vers le service de stockage d'objets blob :

1.  [Installation de la bibliothèque du client de stockage](#install-storage-client)
2.  [Mise à jour du script d'insertion pour générer une signature d'accès partagé](#update-scripts)
3.  [Mise à jour de l'application cliente pour capturer des images](#add-select-images)
4.  [Téléchargement des images pour tester l'application](#test)

Ce didacticiel requiert les éléments suivants :

-   Microsoft Visual Studio 2012 Express pour Windows 8 ou version ultérieure ;
-   [un compte Azure Storage ;](/en-us/manage/services/storage/how-to-create-a-storage-account)
-   un appareil photo ou tout autre appareil de capture d'image raccordé à votre ordinateur.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer ce didacticiel, vous devez effectuer le didacticiel [Prise en main de Mobile Services](/en-us/documentation/articles/mobile-services-windows-store-get-started).

Installation du client de stockage pour les applications du Windows Store
-------------------------------------------------------------------------

Avant de pouvoir utiliser une signature d'accès partagé en vue de télécharger des images vers un stockage d'objets blob, vous devez ajouter le package NuGet qui installe la bibliothèque du client de stockage pour les applications du Windows Store.

1.  Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Manage NuGet Packages**.

2.  Dans le volet gauche, sélectionnez la catégorie **Online**, recherchez `WindowsAzure.Storage`, cliquez sur **Install** au niveau du package **Azure Storage**, puis acceptez les contrats de licence.

 ![](./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png)

 La bibliothèque du client des services de stockage Azure est alors ajoutée au projet.

Dans la prochaine étape, vous allez mettre à jour l'application de démarrage rapide pour capturer et télécharger des images.

Mise à jour du script d'insertion inscrit dans le portail de gestion
--------------------------------------------------------------------

[WACOM.INCLUDE [mobile-services-configure-blob-storage](../includes/mobile-services-configure-blob-storage.md)]

[WACOM.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage](../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md)]

Étapes suivantes
----------------

Maintenant que vous avez intégré votre service mobile au service BLOB et que vous êtes en mesure de télécharger des images en toute sécurité, consultez les autres rubriques ci-dessous relatives au service principal et à l'intégration :

-   [Envoi de courrier électronique à partir de Mobile Services avec SendGrid](/en-us/develop/mobile/tutorials/send-email-with-sendgrid/)

    Découvrez comment ajouter la fonctionnalité de messagerie électronique à votre service mobile en utilisant le service de messagerie SendGrid. Cette rubrique montre comment ajouter des scripts côté serveur pour envoyer du courrier électronique à l'aide de SendGrid.

-   [Planification des travaux principaux dans Mobile Services](/en-us/documentation/articles/mobile-services-schedule-recurring-tasks)

    Découvrez comment utiliser la fonctionnalité de planification de travaux Mobile Services pour définir du code de script serveur exécuté selon une planification que vous définissez.

-   [Référence de script serveur Mobile Services](http://go.microsoft.com/fwlink/p/?LinkId=262293)

    Rubriques de référence pour l'utilisation de scripts serveur en vue d'effectuer des tâches côté serveur et une intégration à d'autres composants Azure et ressources externes.

-   [Guide de fonctionnement Mobile Services .NET](/en-us/develop/mobile/how-to-guides/work-with-net-client-library)

    Découvrez plus en détail comment utiliser Mobile Services avec .NET


