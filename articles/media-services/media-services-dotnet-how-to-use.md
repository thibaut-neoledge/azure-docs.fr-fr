---
title: Configuration de l’ordinateur pour le développement Media Services avec .NET
description: Familiarisez-vous avec les conditions préalables pour Media Services à l’aide du Kit de développement logiciel (SDK) Media Services pour .NET. Apprenez également à créer une application Visual Studio.
services: media-services
documentationcenter: ''
author: juliako
manager: erikre
editor: ''

ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 10/10/2016
ms.author: juliako

---
# <a name="media-services-development-with-.net"></a>Développement Media Services avec .NET
[!INCLUDE [media-services-selector-setup](../../includes/media-services-selector-setup.md)]

Cette rubrique explique comment commencer à développer des applications Media Services à l’aide de .NET.

La bibliothèque du **Kit de développement logiciel (SDK) Azure Media Services pour .NET** permet de programmer pour Media Services à l'aide de .NET. Pour que le développement avec .NET soit encore plus simple, la bibliothèque des **extensions du Kit de développement logiciel (SDK) Azure Media Services pour .NET** est fournie. Cette bibliothèque contient un ensemble de méthodes d’extension et de fonctions d’assistance qui simplifieront votre code .NET. Les deux bibliothèques sont disponibles par le biais de **NuGet** et de **GitHub**.

## <a name="prerequisites"></a>Conditions préalables
* Un compte Media Services dans un abonnement Azure nouveau ou existant. Consultez la rubrique [Comment créer un compte Media Services](media-services-portal-create-account.md).
* Systèmes d’exploitation : Windows 10, Windows 7, Windows 2008 R2 ou Windows 8.
* .NET Framework 4.5.
* Visual Studio 2015, Visual Studio 2013, Visual Studio 2012 ou Visual Studio 2010 SP1 (Professional, Premium, Ultimate ou Express).

## <a name="create-and-configure-a-visual-studio-project"></a>Créer et configurer un projet Visual Studio
Cette section vous montre comment créer un projet dans Visual Studio et le configurer pour le développement Media Services.  Dans ce cas, le projet est une application console C# Windows, mais les étapes de configuration présentées ici s’appliquent aux autres types de projets que vous pouvez créer pour les applications Media Services (par exemple, une application Windows Forms ou Web ASP.NET).

Cette section montre comment utiliser **NuGet** pour ajouter le Kit de développement logiciel (SDK) Media Services pour .NET et d'autres bibliothèques dépendantes.

Vous pouvez également obtenir les dernières informations relatives au Kit de développement logiciel (SDK) Media Services pour .NET à partir de GitHub ([github.com/Azure/azure-sdk-for-media-services](https://github.com/Azure/azure-sdk-for-media-services) et [github.com/Azure/azure-sdk-for-media-services-extensions](https://github.com/Azure/azure-sdk-for-media-services-extensions)), générer la solution et ajouter les références au projet client. Notez que toutes les dépendances nécessaires sont téléchargées et extraites automatiquement.

1. Créez une nouvelle application console C# dans Visual Studio 2013, Visual Studio 2012 ou Visual Studio 2010 SP1. Entrez le **nom**, l’**emplacement** et le **nom de solution**, puis cliquez sur OK.
2. Générez la solution.
3. Utilisez **NuGet** pour installer et ajouter les **extensions du SDK Azure Media Services pour .NET**. L'installation de ce package installe également le **Kit de développement logiciel (SDK) Media Services pour .NET** et ajoute toutes les autres dépendances requises.
4. Assurez-vous que la version la plus récente de NuGet est installée. Pour obtenir des informations supplémentaires et des instructions relatives à l'installation, consultez la page [NuGet](http://nuget.codeplex.com/).
5. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet, puis sélectionnez Gérer les packages NuGet ….

La boîte de dialogue Gérer les packages NuGet apparaît.

1. Dans la galerie en ligne, recherchez Extensions Azure MediaServices, choisissez Extensions du Kit de développement logiciel (SDK) Azure Media Services pour .NET, puis cliquez sur le bouton Installer.

Le projet est modifié et des références aux extensions du SDK Media Services pour .NET, au SDK Media Services pour .NET et à d’autres assemblys dépendants sont ajoutées.

1. Pour promouvoir un environnement de développement plus propre, envisagez d’activer la restauration du package NuGet. Pour plus d'informations, consultez le document [Restauration du package NuGet](http://docs.nuget.org/consume/package-restore).
2. Ajoutez une référence à l'assembly **System.Configuration** . Cet assembly contient la classe System.Configuration.**ConfigurationManager** qui est utilisée pour accéder aux fichiers de configuration (par exemple, App.config).

Pour ajouter des références à l’aide de la boîte de dialogue Gérer les références, procédez comme suit :

1. Dans l’Explorateur de solutions, cliquez avec le bouton droit sur le nom du projet. Sélectionnez ensuite Ajouter et Références.

La boîte de dialogue Gérer les références s’affiche.

1. Sous les assemblys .NET framework, recherchez et sélectionnez l’assembly System.Configuration.
2. Appuyez sur OK.
3. Ouvrez le fichier App.config (ajoutez le fichier à votre projet s'il n'a pas été ajouté par défaut) et ajoutez une section *appSettings* au fichier.     
   Définissez les valeurs pour le nom et la clé de votre compte Azure Media Services, comme illustré dans l’exemple suivant.
   
    Pour rechercher les valeurs Nom et Clé, accédez au portail Azure et sélectionnez votre compte. La fenêtre Paramètres s’affiche sur la droite. Dans la fenêtre Paramètres, sélectionnez Clés. Cliquer sur l'icône en regard de chaque zone de texte copie la valeur dans le Presse-papiers du système.

        <configuration>
        ...
            <appSettings>
              <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
              <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
            </appSettings>

        </configuration>

1. Remplacez les instructions using existantes au début du fichier Program.cs par le code suivant.
   
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;

À ce stade, vous êtes prêt à commencer le développement d’une application Media Services.    

## <a name="media-services-learning-paths"></a>Parcours d’apprentissage de Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!--HONumber=Oct16_HO2-->


