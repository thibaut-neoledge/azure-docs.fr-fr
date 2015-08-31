<properties 
	pageTitle="Utilisation de Mobile Services pour télécharger des images dans le stockage d&#39;objets blob (Windows Phone) | Microsoft Azure" 
	description="Découvrez comment utiliser Mobile Services pour télécharger des images dans le stockage d&#39;objets blob Azure." 
	documentationCenter="windows" 
	authors="ggailey777" 
	services="mobile-services,storage" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="mobile-windows-phone" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="06/04/2015" 
	ms.author="glenga"/>

# Téléchargement d'images vers Azure Storage à l'aide de Mobile Services

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

##Vue d'ensemble
Cette rubrique vous montre comment utiliser Azure Mobile Services pour permettre à votre application Silverlight pour Windows Phone 8 ou Windows Phone 8.1 de télécharger et de stocker les images générées par l'utilisateur dans Azure Storage. Mobile Services utilise une base de données SQL pour stocker les données. Toutefois, les données BLOB (Binary Large Object) sont stockées avec plus d'efficacité dans le service de stockage d'objets blob Azure.

Vous ne pouvez pas distribuer de manière sécurisée les informations d'identification nécessaires à un téléchargement sécurisé de données vers le service de stockage d'objets blob avec l'application cliente. Au lieu de cela, vous devez stocker ces informations d'identification dans votre service mobile et vous en servir pour générer une signature d'accès partagé qui sera utilisée pour télécharger une nouvelle image. La signature d'accès partagé, information d'identification dont le délai d'expiration est très court (dans ce cas, 5 minutes), est renvoyée de manière sécurisée par Mobile Services à l'application cliente. L'application utilise ensuite cette information d'identification provisoire pour télécharger l'image. Dans cet exemple, les téléchargements à partir du service BLOB sont publics.

Dans ce didacticiel, vous allez ajouter une fonctionnalité à l'[exemple de projet d'application GetStartedWithData](mobile-services-dotnet-backend-windows-phone-get-started-data.md) pour prendre des photos et télécharger les images vers Azure en utilisant une signature d'accès partagé (SAP) générée par Mobile Services.

##Configuration requise 

Ce didacticiel requiert les éléments suivants :

+ Microsoft Visual Studio 2013 ou une version ultérieure.
+ [Kit de développement logiciel (SDK) Windows Phone 8.0] ou supérieur ;
+ Gestionnaire de package Nuget pour Microsoft Visual Studio ;
+ [Compte de stockage Azure][How To Create a Storage Account]
+ suivre le didacticiel [Ajout de Mobile Services à une application existante](mobile-services-dotnet-backend-windows-phone-get-started-data.md).  

>[AZURE.NOTE]Ce didacticiel ne concerne que les applications Silverlight pour Windows Phone 8 et Windows Phone 8.1. Il ne prend pas en charge les applications Windows Phone Store 8.1 ou les applications Windows 8.1 universelles.

[AZURE.INCLUDE [mobile-services-dotnet-backend-configure-blob-storage](../../includes/mobile-services-dotnet-backend-configure-blob-storage.md)]

##<a name="install-storage-client"></a>Installation du client de stockage pour les applications du Windows Store

Avant de pouvoir utiliser une signature d'accès partagé en vue de télécharger des images depuis votre application vers un stockage d'objets blob, vous devez ajouter le package NuGet qui installe la bibliothèque du client de stockage pour les applications du Windows Store.

1. Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le projet d'application cliente, puis sélectionnez **Gérer les packages NuGet**.

2. Dans le volet gauche, sélectionnez la catégorie **En ligne**, sélectionnez **Inclure la version préliminaire**, recherchez **WindowsAzure.Storage-Preview**, cliquez sur **Installer** au niveau du package **Azure Storage**, puis acceptez les contrats de licence.

  	![][2]

  	La bibliothèque du client des services de stockage Azure est alors ajoutée au projet.

[AZURE.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]
 
<!-- Anchors. -->
[Install the Storage Client library]: #install-storage-client
[Update the client app to capture images]: #add-select-images
[Install the storage client in the mobile service project]: #storage-client-server
[Update the TodoItem definition in the data model]: #update-data-model
[Update the table controller to generate an SAS]: #update-scripts
[Upload images to test the app]: #test
[Next Steps]: #next-steps

<!-- Images. -->
[2]: ./media/mobile-services-dotnet-backend-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png

<!-- URLs. -->
[Send email from Mobile Services with SendGrid]: store-sendgrid-mobile-services-send-email-scripts.md
[Schedule backend jobs in Mobile Services]: mobile-services-dotnet-backend-schedule-recurring-tasks.md
[Get started with Mobile Services]: ../mobile-services-windows-phone-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: ../storage-create-storage-account.md
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Mobile Services .NET How-to Conceptual Reference]: mobile-services-windows-dotnet-how-to-use-client-library.md
[Kit de développement logiciel (SDK) Windows Phone 8.0]: http://www.microsoft.com/download/details.aspx?id=35471


 

<!---HONumber=August15_HO8-->