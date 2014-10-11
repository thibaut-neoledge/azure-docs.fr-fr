<properties linkid="develop-media-services-how-to-guides-set-up-computer" urlDisplayName="Set Up Computer for Media Services" pageTitle="How to Set Up Computer for Media Services - Azure" metaKeywords="" description="Learn about the prerequisites for Media Services using the Media Services SDK for .NET. Also learn how to create a Visual Studio app." metaCanonical="" services="media-services" documentationCenter="" title="Setting up your computer for Media Services development" authors="migree" solutions="" manager="" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="migree"></tags>

# <a name="setup-dev"> </a><span class="short header">Configuration de votre ordinateur pour le développement Media Services</span>

Cette section contient les conditions préalables générales pour le développement Media Services à l'aide du Kit de développement logiciel (SDK) Media Services pour .NET. Elle montre également aux développeurs comment créer une application Visual Studio pour le développement à l'aide du Kit de développement logiciel (SDK) Media Services.

### Configuration requise

-   Un compte Media Services dans un abonnement Azure nouveau ou existant. Consultez la rubrique [Création d'un compte Media Services][].
-   Systèmes d'exploitation : Windows 7, Windows 2008 R2 ou Windows 8.
-   .NET Framework 4.5 ou .NET Framework 4.
-   Visual Studio 2012 ou Visual Studio 2010 SP1 (Professional, Premium, Ultimate ou Express).
-   Utilisez le package [NuGet windowsazure.mediaservices][] pour installer le Kit de développement logiciel (SDK) Azure pour .NET. La section suivante montre comment utiliser **NuGet** pour installer le Kit de développement logiciel (SDK) Azure.

## <a name="setup-account"></a><span class="short header">Configuration d'un compte Azure pour Media Services</span>

Pour configurer votre compte Media Services, utilisez le portail de gestion Azure (recommandé). Consultez la rubrique [Création d'un compte Media Services][]. Une fois votre compte créé dans le portail de gestion, vous êtes prêt à configurer votre ordinateur pour le développement Media Services.

### Création d'une application dans Visual Studio

Cette section vous montre comment créer un projet dans Visual Studio et le configurer pour le développement Media Services. Dans ce cas, le projet est une application console C# Windows, mais les étapes de configuration présentées ici s'appliquent aux autres types de projets que vous pouvez créer pour les applications Media Services (par exemple, une application Windows Forms ou Web ASP.NET).

1.  Créez une **application console** C# dans Visual Studio 2012 ou Visual Studio 2010 SP1. Entrez le **nom**, l'**emplacement** et le **nom de solution**, puis cliquez sur **OK**.
2.  Ajoutez une référence à l'assembly **System.Configuration**. Pour ajouter une référence à **System.Configuration**, dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le nœud **Références**, puis sélectionnez **Ajouter une référence...**. Dans la boîte de dialogue **Gérer les références**, sélectionnez **System.Configuration**, puis cliquez sur **OK**.
3.  Ajoutez les références aux bibliothèques **Kit de développement logiciel (SDK) Azure pour .NET** (Microsoft.WindowsAzure.StorageClient.dll), **Kit de développement logiciel (SDK) d'Azure Media Services pour .NET** (Microsoft.WindowsAzure.MediaServices.Client.dll) et **Services de données WCF 5.0 pour OData V3** (Microsoft.Data.OData.dll) à l'aide du package [NuGet windowsazure.mediaservices][].

    Pour ajouter des références à l'aide de NuGet, procédez comme suit. In Visual Studio **Main Menu**, select **TOOLS** -\> **Library Package Manager** -\> **Package Manager Console**. In the console window type **Install-Package windowsazure.mediaservices** and press **Enter**.

4.  Remplacez les instructions using existantes au début du fichier Program.cs par le code suivant.

        using System;
        using System.Linq;
        using System.Configuration;
        using System.IO;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using System.Collections.Generic;
        using Microsoft.WindowsAzure;
        using Microsoft.WindowsAzure.MediaServices.Client;

À ce stade, vous êtes prêt à commencer le développement d'une application Media Services.

## <a name="setup-account"></a><span class="short header">Connexion à Media Services</span>

La quasi-totalité des actions que vous effectuez dans la programmation Media Services nécessite une référence à l'objet de contexte du serveur. Le contexte de serveur vous offre un accès par programme à tous les objets de programmation Media Services.

Pour obtenir une référence au contexte de serveur, renvoyez une nouvelle instance du type de contexte comme dans l'exemple de code suivant. Transmettez le nom de compte et la clé de compte de votre compte Media Services (que vous avez obtenus lors du processus d'installation du compte) au constructeur.

    static CloudMediaContext GetContext()
    {
        // Gets the service context. 
        return new CloudMediaContext(_accountName, _accountKey);
    } 

Il est souvent utile de définir une variable au niveau du module de type **CloudMediaContext** pour conserver une référence au contexte de serveur renvoyé par une méthode comme **GetContext**. The rest of the code examples in this topic use a variable called \*\*\_context\*\* to refer to the server context.

## Étapes suivantes

Now that you have set up your computer and created a Visual Studio solution for programming Media Services, go to the [How to Create an Encrypted Asset and Upload to Storage][] topic.
[How to Create a Media Services Account]: <http://go.microsoft.com/fwlink/?linkid=256662>
[How to Create an Encrypted Asset and Upload to Storage]:<http://go.microsoft.com/fwlink/?LinkID=301733&clcid=0x409>

  [Création d'un compte Media Services]: http://www.windowsazure.com/en-us/manage/services/media-services/how-to-create-a-media-services-account/
  [NuGet windowsazure.mediaservices]: http://nuget.org/packages/windowsazure.mediaservices
