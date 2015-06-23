<properties 
	pageTitle="Utilisation de Mobile Services pour télécharger des images dans le stockage d'objets blob (Windows Phone) | Mobile Services" 
	description="Découvrez comment utiliser Mobile Services pour télécharger des images dans le stockage d'objets blob Azure." 
	documentationCenter="windows" 
	authors="wesmc7777" 
	writer="wesmc" 
	services="mobile-services" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="mobile-services" 
	ms.workload="mobile" 
	ms.tgt_pltfrm="" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/23/2015" 
	ms.author="wesmc"/>

# Téléchargement d'images vers Azure Storage à l'aide de Mobile Services

[AZURE.INCLUDE [mobile-services-selector-upload-data-blob-storage](../../includes/mobile-services-selector-upload-data-blob-storage.md)]

##Vue d'ensemble

Cette rubrique vous montre comment utiliser Azure Mobile Services pour permettre à votre application de télécharger et de stocker les images générées par l'utilisateur dans Azure Storage. Mobile Services utilise une base de données SQL pour stocker les données. Toutefois, les données BLOB (Binary Large Object) sont stockées avec plus d'efficacité dans le service de stockage d'objets blob Azure.

Vous ne pouvez pas distribuer de manière sécurisée les informations d'identification nécessaires à un téléchargement sécurisé de données vers le service de stockage d'objets blob avec l'application cliente. Au lieu de cela, vous devez stocker ces informations d'identification dans votre service mobile et vous en servir pour générer une signature d'accès partagé qui sera utilisée pour télécharger une nouvelle image. La signature d'accès partagé, information d'identification dont le délai d'expiration est très court (dans ce cas, 5 minutes), est renvoyée de manière sécurisée par Mobile Services à l'application cliente. L'application utilise ensuite cette information d'identification provisoire pour télécharger l'image. Dans cet exemple, les téléchargements à partir du service BLOB sont publics.

Dans ce didacticiel, vous allez ajouter une fonctionnalité à l'[exemple de projet d'application GetStartedWithData](mobile-services-windows-phone-get-started-data.md) pour prendre des photos et télécharger les images vers Azure en utilisant une signature d'accès partagé (SAP) générée par Mobile Services.


##Configuration requise

Ce didacticiel requiert les éléments suivants :

+ Microsoft Visual Studio 2012 Express pour Windows 8 ou version ultérieure ;
+ [Kit de développement logiciel (SDK) Windows Phone 8.0] ou supérieur ;
+ Gestionnaire de package Nuget pour Microsoft Visual Studio ;
+ [Compte de stockage Azure][How To Create a Storage Account]
+ Suivre le didacticiel [Ajouter Mobile Services à une application existante](mobile-services-windows-phone-get-started-data.md)  


##Installation du client de stockage pour les applications Windows Phone

Avant de pouvoir utiliser une signature d'accès partagé en vue de télécharger des images vers un stockage d'objets blob, vous devez ajouter le package NuGet qui installe la bibliothèque du client de stockage pour les applications Windows Phone.

1. Dans l'**Explorateur de solutions** de Visual Studio, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez **Manage NuGet Packages**.

2. Dans le volet gauche, sélectionnez la catégorie **En ligne**, sélectionnez **Inclure la version préliminaire**, recherchez **WindowsAzure.Storage-Preview**, cliquez sur **Installer** au niveau du package **Azure Storage**, puis acceptez les contrats de licence.

  	![][2]

  	La bibliothèque du client des services de stockage Azure est alors ajoutée au projet.

Dans la prochaine étape, vous allez mettre à jour l'application de démarrage rapide pour capturer et télécharger des images.

##Mise à jour du script de la fonction insert inscrite dans le portail de gestion


[AZURE.INCLUDE [mobile-services-configure-blob-storage](../../includes/mobile-services-configure-blob-storage.md)]

>[AZURE.NOTE]Pour ajouter de nouvelles propriétés à l'objet TodoItem, le schéma dynamique doit être activé dans votre service mobile. Lorsque le schéma dynamique est activé, de nouvelles colonnes sont automatiquement ajoutées à la table TodoItem. Celles-ci sont mappées vers ces nouvelles propriétés.

[AZURE.INCLUDE [mobile-services-windows-phone-upload-to-blob-storage](../../includes/mobile-services-windows-phone-upload-to-blob-storage.md)]


##Étapes suivantes

Maintenant que vous avez intégré votre service mobile au service BLOB et que vous êtes en mesure de télécharger des images en toute sécurité, consultez les autres rubriques ci-dessous relatives au service principal et à l'intégration :

+ [Envoyer un courrier électronique à partir de Mobile Services avec SendGrid]
 
  Découvrez comment ajouter la fonctionnalité de messagerie électronique à votre service mobile en utilisant le service de messagerie SendGrid. Cette rubrique montre comment ajouter des scripts côté serveur pour envoyer du courrier électronique à l'aide de SendGrid.

+ [Planifier les tâches principales dans Mobile Services]

  Découvrez comment utiliser la fonctionnalité de planification de travaux Mobile Services pour définir du code de script serveur exécuté selon une planification que vous définissez.

+ [Référence de script serveur Mobile Services]

  Rubriques de référence pour l'utilisation de scripts serveur en vue d'effectuer des tâches côté serveur et une intégration à d'autres composants Azure et ressources externes.
 
+ [Guide de fonctionnement Mobile Services .NET]

  Découvrez plus en détail comment utiliser Mobile Services avec .NET
  
 

<!-- Images. -->


[2]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-add-storage-nuget-package-dotnet.png


[5]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-WMAppmanifest-wp8.png
[6]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-wp8.png
[7]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-camera-wp8.png
[8]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-save-wp8.png
[9]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-final-wp8.png

[11]: ./media/mobile-services-windows-phone-upload-data-blob-storage/mobile-upload-blob-app-view-camera-accept-wp8.png

<!-- URLs. -->
[Envoyer un courrier électronique à partir de Mobile Services avec SendGrid]: /develop/mobile/tutorials/send-email-with-sendgrid/
[Planifier les tâches principales dans Mobile Services]: /develop/mobile/tutorials/schedule-backend-tasks/
[Référence de script serveur Mobile Services]: http://go.microsoft.com/fwlink/p/?LinkId=262293
[Get started with Mobile Services]: ../mobile-services-windows-phone-get-started.md

[Azure Management Portal]: https://manage.windowsazure.com/
[How To Create a Storage Account]: /manage/services/storage/how-to-create-a-storage-account
[Azure Storage Client library for Store apps]: http://go.microsoft.com/fwlink/p/?LinkId=276866
[Guide de fonctionnement Mobile Services .NET]: /develop/mobile/how-to-guides/work-with-net-client-library
[Kit de développement logiciel (SDK) Windows Phone 8.0]: http://www.microsoft.com/download/details.aspx?id=35471



<!--HONumber=54--> 