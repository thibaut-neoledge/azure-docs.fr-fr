<properties urlDisplayName="Set Up Computer for Media Services" pageTitle="Configuration de l'ordinateur pour Media Services - Azure" metaKeywords="" description="Familiarisez-vous avec les conditions pr&eacute;alables pour Media Services &agrave; l'aide du Kit de d&eacute;veloppement logiciel (SDK) Media Services pour .NET. Apprenez &eacute;galement &agrave; cr&eacute;er une application Visual Studio." metaCanonical="" services="media-services" documentationCenter="" title="Configuration de votre ordinateur pour le d&eacute;veloppement Media Services" authors="juliako" solutions="" manager="dwrede" editor="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="juliako" />

# <a name="setup-dev"> </a><span class="short header">Configuration de votre ordinateur pour le développement Media Services</span>

Cette section contient les conditions préalables générales pour le développement Media Services à l'aide du Kit de développement logiciel (SDK) Media Services pour .NET. Elle montre également aux développeurs comment créer une application Visual Studio pour le développement à l'aide du Kit de développement logiciel (SDK) Media Services.

### Configuration requise

-   Un compte Media Services dans un abonnement Azure nouveau ou existant. Consultez la rubrique [Création d'un compte Media Services][Création d'un compte Media Services].
-   Systèmes d'exploitation : Windows 7, Windows 2008 R2 ou Windows 8.
-   .NET Framework 4.
-   Visual Studio 2013, Visual Studio 2012 ou Visual Studio 2010 SP1 (Professional, Premium, Ultimate ou Express).
-   Utilisez le package [NuGet windowsazure.mediaservices][NuGet windowsazure.mediaservices] pour installer le Kit de développement logiciel (SDK) Azure pour .NET. La section suivante montre comment utiliser **NuGet** pour installer le Kit de développement logiciel (SDK) Azure.

## <a name="setup-account"></a><span class="short header">Configuration d'un compte Azure pour Media Services</span>

Pour configurer votre compte Media Services, utilisez le portail de gestion Azure (recommandé). Consultez la rubrique [Création d'un compte Media Services][Création d'un compte Media Services]. Une fois votre compte créé dans le portail de gestion, vous êtes prêt à configurer votre ordinateur pour le développement Media Services.

### Création d'une application dans Visual Studio

Cette section vous montre comment créer un projet dans Visual Studio et le configurer pour le développement Media Services. Dans ce cas, le projet est une application console C# Windows, mais les étapes de configuration présentées ici s'appliquent aux autres types de projets que vous pouvez créer pour les applications Media Services (par exemple, une application Windows Forms ou Web ASP.NET).

1.  Créez une **application console** C# dans Visual Studio 2013, Visual Studio 2012 ou Visual Studio 2010 SP1. Entrez le **nom**, l'**emplacement** et le **nom de solution**, puis cliquez sur **OK**.
2.  Vérifiez que l'infrastructure cible sélectionnée est .NET Framework 4. À cet effet, cliquez avec le bouton droit sur le projet Visual Studio et sélectionnez Propriétés. Sous l'onglet Application, sélectionnez .NET Framework 4 comme infrastructure cible.
3.  Ajoutez une référence à l'assembly **System.Configuration**. Pour ajouter une référence à **System.Configuration**, dans l'**Explorateur de solutions**, cliquez avec le bouton droit sur le nœud **Références**, puis sélectionnez **Ajouter une référence...**. Dans la boîte de dialogue **Gérer les références**, sélectionnez **System.Configuration**, puis cliquez sur **OK**.
4.  Utilisez le package [NuGet windowsazure.mediaservices][NuGet windowsazure.mediaservices] pour ajouter des références au **Kit de développement logiciel (SDK) Azure pour .NET** (tous les autres assemblys dépendants sont installés également).

    Pour ajouter des références à l'aide de NuGet, procédez comme suit. Dans le **menu principal** de Visual Studio, sélectionnez **OUTILS** -\> **Gestionnaire de package de bibliothèques** -\> **Console du Gestionnaire de package**. Dans la fenêtre de la console, tapez **Install-Package windowsazure.mediaservices**, puis appuyez sur **Entrée**.

5.  Remplacez les instructions using existantes au début du fichier Program.cs par le code suivant.

        using System;
        using System.Linq;
        using System.Configuration;
        using System.IO;
        using System.Text;
        using System.Threading;
        using System.Threading.Tasks;
        using System.Collections.Generic;
        using Microsoft.WindowsAzure.MediaServices.Client;

À ce stade, vous êtes prêt à commencer le développement d'une application Media Services.

## <a name="setup-account"></a><span class="short header">Connexion à Media Services</span>

La quasi-totalité des actions que vous effectuez dans la programmation Media Services nécessite une référence à l'objet de contexte du serveur. Le contexte de serveur vous offre un accès par programme à tous les objets de programmation Media Services.

Pour obtenir une référence au contexte de serveur, créez une instance du type de contexte comme dans l'exemple de code suivant. Transmettez le nom de compte et la clé de compte de votre compte Media Services (que vous avez obtenus lors du processus d'installation du compte) au constructeur.

    // Create and cache the Media Services credentials in a static class variable.
    _cachedCredentials = new MediaServicesCredentials(
                    _mediaServicesAccountName,
                    _mediaServicesAccountKey);

    // Use the cached credentials to create CloudMediaContext.
    _context = new CloudMediaContext(_cachedCredentials);

Il est souvent utile de définir une variable au niveau du module de type **CloudMediaContext** pour conserver une référence au contexte de serveur. Pour plus d'informations, consultez la page [Connexion à Media Services avec le Kit de développement logiciel (SDK) Media Services pour .NET][Connexion à Media Services avec le Kit de développement logiciel (SDK) Media Services pour .NET].

Le reste des exemples de code de cette rubrique utilise une variable appelée \*\*\_context\*\* comme référence au contexte de serveur.

## Étapes suivantes

Maintenant que vous avez configuré votre ordinateur et créé une solution Visual Studio pour la programmation de Media Services, accédez à la rubrique [Création d'un élément multimédia chiffré et téléchargement dans le stockage][Création d'un élément multimédia chiffré et téléchargement dans le stockage].
[Création d'un compte Media Services] : ../media-services-create-account/
[Création d'un élément multimédia chiffré et téléchargement dans le stockage] : ../media-services-create-encrypted-asset-upload-storage/

  [Création d'un compte Media Services]: ../media-services-create-account/
  [NuGet windowsazure.mediaservices]: http://nuget.org/packages/windowsazure.mediaservices
  [Connexion à Media Services avec le Kit de développement logiciel (SDK) Media Services pour .NET]: http://msdn.microsoft.com/fr-fr/library/azure/jj129571.aspx
