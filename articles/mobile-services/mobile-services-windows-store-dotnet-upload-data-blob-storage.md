<properties 
	pageTitle="Utilisation de Mobile Services pour télécharger des images dans le stockage d'objets blob (Windows Store) | Mobile Services" 
	description="Découvrez comment utiliser Mobile Services pour télécharger des images vers le stockage d'objets blob Azure et y accéder à partir de votre application Windows Store." 
	services="mobile-services" 
	documentationCenter="windows" 
	authors="ggailey777" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-store" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="05/28/2015" 
	ms.author="glenga"/>

# Téléchargement d'images vers Azure Storage à l'aide de Mobile Services

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

Cette rubrique vous montre comment utiliser Azure Mobile Services pour permettre à votre application de télécharger et de stocker les images générées par l'utilisateur dans Azure Storage. Mobile Services utilise une base de données SQL pour stocker les données. Toutefois, les données BLOB (Binary Large Object) sont stockées avec plus d'efficacité dans le service de stockage d'objets blob Azure.

Vous ne pouvez pas distribuer de manière sécurisée les informations d'identification nécessaires à un téléchargement sécurisé de données vers le service de stockage d'objets blob avec l'application cliente. Au lieu de cela, vous devez stocker ces informations d'identification dans votre service mobile et vous en servir pour générer une signature d'accès partagé qui sera utilisée pour télécharger une nouvelle image. La signature d'accès partagé, information d'identification dont le délai d'expiration est très court (dans ce cas, 5 minutes), est renvoyée de manière sécurisée par Mobile Services à l'application cliente. L'application utilise ensuite cette information d'identification provisoire pour télécharger l'image. Dans cet exemple, les téléchargements à partir du service BLOB sont publics.

Dans ce didacticiel, vous allez ajouter une fonctionnalité à l'application de démarrage rapide Mobile Services pour prendre des photos et télécharger les images vers Azure en utilisant une signature d'accès partagé générée par Mobile Services.

##Composants requis

Ce didacticiel requiert les éléments suivants :

+ Microsoft Visual Studio 2012 Express pour Windows 8 ou version ultérieure ;
+ [Compte de stockage Azure](../storage-create-storage-account.md)
+ un appareil photo ou tout autre appareil de capture d'image raccordé à votre ordinateur.

Ce didacticiel est basé sur le démarrage rapide de Mobile Services. Avant de commencer, vous devez effectuer le didacticiel [Prise en main de Mobile Services].

##<a name="install-storage-client"></a>Installer le client de stockage pour les applications du Windows Store

Avant de pouvoir utiliser une signature d'accès partagé en vue de télécharger des images vers un stockage d'objets blob, vous devez ajouter le package NuGet qui installe la bibliothèque du client de stockage pour les applications du Windows Store.

1. Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Manage NuGet Packages**.

2. Dans le volet gauche, sélectionnez la catégorie **En ligne**, recherchez `WindowsAzure.Storage`, cliquez sur **Installer** au niveau du package **Azure Storage**, puis acceptez les contrats de licence.

  	![][2]

  	La bibliothèque du client des services de stockage Azure est alors ajoutée au projet.

Dans la prochaine étape, vous allez mettre à jour l'application de démarrage rapide pour capturer et télécharger des images.

##<a name="update-scripts"></a>Mettre à jour le script d'insertion inscrit dans le portail de gestion

[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]

[AZURE.INCLUDE [mobile-services-windows-store-dotnet-upload-to-blob-storage](../../includes/mobile-services-windows-store-dotnet-upload-to-blob-storage.md)]

## <a name="next-steps"> </a>Étapes suivantes

Maintenant que vous avez intégré votre service mobile au service BLOB et que vous êtes en mesure de télécharger des images en toute sécurité, consultez les autres rubriques ci-dessous relatives au service backend et à l'intégration :

+ [Envoyer un courrier électronique à partir de Mobile Services avec SendGrid]
 
  Découvrez comment ajouter la fonctionnalité de messagerie électronique à votre service mobile en utilisant le service de messagerie SendGrid. Cette rubrique montre comment ajouter des scripts côté serveur pour envoyer du courrier électronique à l'aide de SendGrid.

+ [Planifier les tâches du backend dans Mobile Services]

  Découvrez comment utiliser la fonctionnalité de planification de travaux Mobile Services pour définir du code de script serveur exécuté selon une planification que vous définissez.

+ [Référence de script serveur Mobile Services]

  Rubriques de référence pour l'utilisation de scripts serveur en vue d'effectuer des tâches côté serveur et une intégration à d'autres composants Azure et ressources externes.
 
+ [Guide de fonctionnement Mobile Services .NET]

  Découvrez plus en détail comment utiliser Mobile Services avec .NET
  
 
<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Update the insert script to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->

[2]: ./media/mobile-services-windows-store-dotnet-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


<!-- URLs. -->
[Envoyer un courrier électronique à partir de Mobile Services avec SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Planifier les tâches du backend dans Mobile Services]: mobile-services-schedule-recurring-tasks.md
[Send push notifications to Windows Store apps using Service Bus from a .NET back-end]: http://go.microsoft.com/fwlink/?LinkId=277073&clcid=0x409
[Référence de script serveur Mobile Services]: mobile-services-how-to-use-server-scripts.md
[Prise en main de Mobile Services]: mobile-services-javascript-backend-windows-store-dotnet-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Guide de fonctionnement Mobile Services .NET]: mobile-services-windows-dotnet-how-to-use-client-library.md
[App settings]: http://msdn.microsoft.com/library/windowsazure/b6bb7d2d-35ae-47eb-a03f-6ee393e170f7
 

<!---HONumber=July15_HO3-->