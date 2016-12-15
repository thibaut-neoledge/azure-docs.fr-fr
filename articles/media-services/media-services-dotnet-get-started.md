---
title: Utilisation de Media Services avec .NET | Microsoft Docs
description: "Ce didacticiel vous présente les étapes d’implémentation d’une application de diffusion de contenu à la demande avec Azure Media Services pour .NET."
services: media-services
documentationcenter: 
author: Juliako
manager: erikre
editor: 
ms.assetid: 388b8928-9aa9-46b1-b60a-a918da75bd7b
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: hero-article
ms.date: 11/07/2016
ms.author: juliako
translationtype: Human Translation
ms.sourcegitcommit: 4fc33ba185122496661f7bc49d14f7522d6ee522
ms.openlocfilehash: 645fa2574efb9501da173f8ac8aea146d1e79ff8


---
# <a name="get-started-with-delivering-content-on-demand-using-net-sdk"></a>Prendre en main la diffusion de contenus à la demande à l’aide du Kit de développement logiciel (SDK) .NET
[!INCLUDE [media-services-selector-get-started](../../includes/media-services-selector-get-started.md)]

> [!NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). 
> 
> 

## <a name="overview"></a>Vue d'ensemble
Ce didacticiel vous présente les étapes d'implémentation d'une application de diffusion de contenu vidéo à la demande (VoD) avec le Kit de développement logiciel (SDK) Azure Media Services (AMS) pour .NET.

Il présente le workflow Media Services de base et les objets et tâches de programmation les plus courants requis pour le développement Media Services. À la fin de ce didacticiel, vous pourrez lire en continu ou télécharger de façon progressive un exemple de fichier multimédia que vous aurez chargé, encodé et téléchargé.

## <a name="what-youll-learn"></a>Ce que vous allez apprendre
Ce didacticiel montre comment effectuer les tâches suivantes :

1. Création d'un compte Media Services (avec le portail Azure).
2. Configurer un point de terminaison de diffusion en continu (à l’aide du portail Azure).
3. Créer et configurer un projet Visual Studio
4. Se connecter au compte Media Services.
5. Créer un nouvel élément et charger un fichier vidéo.
6. Encoder le fichier source en un ensemble de fichiers MP4 à débit adaptatif.
7. Publier les éléments et obtenir les URL de diffusion et de téléchargement progressif
8. Testez en lisant votre contenu.

## <a name="prerequisites"></a>Conditions préalables
Les éléments suivants sont requis pour suivre le didacticiel.

* Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. 
  
    Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](https://azure.microsoft.com/pricing/free-trial/?WT.mc_id=A261C142F). Vous obtenez des crédits que vous pouvez utiliser pour essayer des services Azure payants. Une fois que les crédits sont épuisés, vous pouvez quand même conserver le compte et utiliser les services et fonctionnalités Azure gratuits, comme la fonction Web Apps dans Azure App Service.
* Systèmes d’exploitation : Windows 8 ou ultérieur, Windows 2008 R2, Windows 7.
* .NET Framework 4.0 ultérieur
* Visual Studio 2010 SP1 (Professional, Premium, Ultimate ou Express) ou une version ultérieure.

## <a name="download-sample"></a>Charger l’exemple
Obtenez et exécutez un exemple [ici](https://azure.microsoft.com/documentation/samples/media-services-dotnet-on-demand-encoding-with-media-encoder-standard/).

## <a name="create-an-azure-media-services-account-using-the-azure-portal"></a>Création d’un compte Azure Media Services à l’aide du portail Azure
Cette section montre comment créer un compte AMS.

1. Connectez-vous au [portail Azure](https://portal.azure.com/).
2. Cliquez sur **+Nouveau** > **Média + CDN** > **Media Services**.
   
    ![Media Services Créer](./media/media-services-portal-vod-get-started/media-services-new1.png)
3. Dans **CREATE MEDIA SERVICES ACCOUNT** (CRÉER UN COMPTE MEDIA SERVICES), entrez les valeurs requises.
   
    ![Media Services Créer](./media/media-services-portal-vod-get-started/media-services-new3.png)
   
   1. Dans **Nom du compte**, entrez le nom du nouveau compte AMS. Un nom de compte Media Services se compose de chiffres ou de lettres en minuscules, sans espaces. Sa longueur est comprise entre 3 et 24 caractères.
   2. Dans Abonnement, sélectionnez l’un des abonnements Azure auxquels vous avez accès.
   3. Dans **Groupe de ressources**, sélectionnez la ressource (nouvelle ou existante).  Un groupe de ressources désigne une collection de ressources qui partagent un cycle de vie, des autorisations et des stratégies. En savoir plus [ici](../azure-resource-manager/resource-group-overview.md#resource-groups).
   4. Dans **Emplacement**, sélectionnez la région géographique utilisée pour stocker les enregistrements multimédias et les métadonnées de votre compte Media Services. Cette région servira à traiter et diffuser vos médias. Seules les régions Media Services disponibles s’affichent dans la liste déroulante. 
   5. Dans **Compte de stockage**, sélectionnez le compte de stockage qui fournira le stockage d’objets blob du contenu multimédia provenant de votre compte Media Services. Vous pouvez sélectionner un compte de stockage dans la même région géographique que votre compte Media Services ou en créer un. Ce dernier sera créé dans la même région. Les règles des noms de compte de stockage sont identiques à celles des comptes Media Services.
      
       Pour en savoir plus sur le stockage, cliquez [ici](../storage/storage-introduction.md).
   6. Sélectionnez **Épingler au tableau de bord** pour voir la progression du déploiement du compte.
4. Cliquez sur **Créer** en bas du formulaire.
   
    Une fois créé, le compte prend l’état **En cours d’exécution**. 
   
    ![Media Services Paramètres](./media/media-services-portal-vod-get-started/media-services-settings.png)
   
    Pour gérer votre compte AMS (par exemple, charger des vidéos, encoder des éléments multimédias ou surveiller la progression de tâches), utilisez la fenêtre **Paramètres** .

## <a name="configure-streaming-endpoints-using-the-azure-portal"></a>Configurer les points de terminaison de diffusion en continu à l’aide du portail Azure
Lorsque vous utilisez Azure Media Services, la diffusion à vos clients de vidéos en continu à débit binaire adaptatif constitue l’un des scénarios les plus courants. Media Services prend en charge les technologies de diffusion en continu à débit binaire adaptatif suivantes : HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH et HDS (pour licences Adobe PrimeTime/Access uniquement).

Media Services assure l’empaquetage dynamique, qui permet de distribuer un contenu encodé en MP4 à un débit binaire adaptatif dans un format pris en charge par Media Services (MPEG DASH, HLS, Smooth Streaming, HDS), sans avoir à stocker de versions pré-empaquetées de chacun de ces formats de diffusion en continu.

Pour tirer parti de l’empaquetage dynamique, vous devez effectuer les opérations suivantes :

* Encoder votre fichier mezzanine (source) dans un ensemble de fichiers MP4 à débit adaptatif (les étapes de codage sont décrites plus loin dans ce didacticiel).  
* Créez au moins une unité de diffusion pour le *point de terminaison de diffusion en continu* à partir duquel vous envisagez de distribuer votre contenu. La procédure ci-dessous explique comment modifier le nombre d’unités de diffusion en continu.

L’empaquetage dynamique vous permet de ne stocker et payer les fichiers que dans un seul format de stockage. Ensuite, Media Services crée et fournit la réponse appropriée en fonction des demandes des clients.

Pour créer et modifier le nombre d’unités réservées de diffusion en continu, procédez comme suit :

1. Dans la fenêtre **Paramètres**, cliquez sur **Points de terminaison de diffusion en continu**. 
2. Cliquez sur le point de terminaison de diffusion en continu par défaut. 
   
    La fenêtre **DEFAULT STREAMING ENDPOINT DETAILS** (DÉTAILS DU POINT DE TERMINAISON DE DIFFUSION EN CONTINU PAR DÉFAUT) s’affiche.
3. Pour spécifier le nombre d’unités de diffusion en continu, faites glisser le curseur **Unités de diffusion en continu** .
   
    ![Unités de diffusion en continu](./media/media-services-portal-vod-get-started/media-services-streaming-units.png)
4. Cliquez sur le bouton **Enregistrer** pour enregistrer vos modifications.
   
   > [!NOTE]
   > L’allocation de nouvelles unités peut prendre environ 20 minutes.
   > 
   > 

## <a name="create-and-configure-a-visual-studio-project"></a>Créer et configurer un projet Visual Studio

1. Créez une nouvelle application console C# dans Visual Studio 2013, Visual Studio 2012 ou Visual Studio 2010 SP1. Entrez le **nom**, **l’emplacement** et le **nom de solution**, puis cliquez sur **OK**.
2. Utilisez le package NuGet [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) pour installer les **extensions du Kit de développement logiciel (SDK) Azure Media Services pour .NET**.  Les extensions du Kit de développement logiciel (SDK) Media Services pour .NET sont un ensemble de méthodes d'extension et de fonctions d'assistance qui simplifient votre code et le développement avec les Services de média. L'installation de ce package installe également le **Kit de développement logiciel (SDK) Media Services pour .NET** et ajoute toutes les autres dépendances requises.

    Pour ajouter des références à l’aide de NuGet, procédez comme suit : dans l’Explorateur de solutions, cliquez avec le bouton droit de la souris sur le nom du projet, puis sélectionnez **Gérer les packages NuGet**. Ensuite, recherchez **windowsazure.mediaservices.extensions** et cliquez sur **Installer**.

3. Ajoutez une référence à l’assembly System.Configuration. Cet assembly contient la classe **System.Configuration.ConfigurationManager** qui est utilisée pour accéder aux fichiers de configuration, par exemple App.config.

    Pour ajouter une référence, procédez comme suit : dans l’Explorateur de solutions, cliquez avec le bouton droit de la souris sur le nom du projet, puis sélectionnez **Ajouter** > **une référence...** et saisissez la configuration dans la zone de recherche. 

4. Ouvrez le fichier App.config (ajoutez le fichier à votre projet s'il n'a pas été ajouté par défaut) et ajoutez une section *appSettings* au fichier. Définissez les valeurs pour le nom et la clé de votre compte Azure Media Services, comme illustré dans l’exemple suivant. Pour obtenir le nom du compte et les informations sur la clé, accédez au [portail Azure](https://portal.azure.com/) et sélectionnez votre compte AMS. Sélectionnez ensuite **Paramètres** > **Clés**. La fenêtre Gérer les clés affiche le nom du compte ainsi que les clés primaires et secondaires. Copiez les valeurs du nom du compte et de la clé primaire.
   
        <configuration>
        ...
          <appSettings>
            <add key="MediaServicesAccountName" value="Media-Services-Account-Name" />
            <add key="MediaServicesAccountKey" value="Media-Services-Account-Key" />
          </appSettings>
   
        </configuration>
5. Remplacez les instructions **using** existantes au début du fichier Program.cs par le code suivant.
   
        using System;
        using System.Collections.Generic;
        using System.Linq;
        using System.Text;
        using System.Threading.Tasks;
        using System.Configuration;
        using System.Threading;
        using System.IO;
        using Microsoft.WindowsAzure.MediaServices.Client;
6. Créez un dossier sous le répertoire de projets et copiez-y le fichier .mp4 ou .wmv à encoder et à diffuser en continu ou télécharger. Dans cet exemple, le chemin d'accès « C:\VideoFiles » est utilisé.

## <a name="connect-to-the-media-services-account"></a>Se connecter au compte Media Services

Lorsque vous utilisez Media Services avec .NET, vous devez utiliser la classe **CloudMediaContext** pour la plupart des tâches de programmation Media Services : connexion au compte Media Services, création, mise à jour, accès et suppression des objets suivants : éléments multimédia, fichiers multimédias, travaux, stratégies d'accès, localisateurs, etc.

Remplacez la classe Program par défaut par le code ci-dessous. Le code montre comment lire les valeurs de connexion à partir du fichier App.config et comment créer l’objet **CloudMediaContext** pour se connecter à Media Services. Pour plus d’informations sur la connexion à Media Services, consultez la page [Connexion à Media Services avec le Kit de développement logiciel (SDK) Media Services pour .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).


La fonction **Main** appelle des méthodes qui seront définies ultérieurement dans cette section.

> [!NOTE]
> Vous obtiendrez des erreurs de compilation tant que vous n’aurez pas ajouté des définitions pour toutes les fonctions.

    class Program
    {
        // Read values from the App.config file.
        private static readonly string _mediaServicesAccountName =
            ConfigurationManager.AppSettings["MediaServicesAccountName"];
        private static readonly string _mediaServicesAccountKey =
            ConfigurationManager.AppSettings["MediaServicesAccountKey"];

        // Field for service context.
        private static CloudMediaContext _context = null;
        private static MediaServicesCredentials _cachedCredentials = null;

        static void Main(string[] args)
        {
            try
            {
                // Create and cache the Media Services credentials in a static class variable.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Used the chached credentials to create CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Add calls to methods defined in this section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Parse the XML error message in the Media Services response and create a new
                // exception with its content.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }
    }

## <a name="create-a-new-asset-and-upload-a-video-file"></a>Créer un nouvel élément et charger un fichier vidéo

Dans Media Services, vous téléchargez (ou réceptionnez) vos fichiers numériques dans un élément multimédia. L’entité **Asset** peut contenir des fichiers vidéo et audio, des images, des collections de miniatures, des pistes textuelles et des légendes (et les métadonnées concernant ces fichiers).  Une fois les fichiers téléchargés, votre contenu est stocké en toute sécurité dans le cloud et peut faire l’objet d’un traitement et d’une diffusion en continu. Les fichiers de l'élément multimédia sont appelés **fichiers d'élément multimédia**.

La méthode **UploadFile** définie ci-dessous appelle **CreateFromFile** (défini dans les extensions du Kit de développement logiciel (SDK) .NET). **CreateFromFile** crée un nouvel élément multimédia dans lequel le fichier source spécifié est téléchargé.

La méthode **CreateFromFile** prend **AssetCreationOptions**, qui vous permet de spécifier les options de création suivantes :

* **None** : aucun chiffrement. Il s’agit de la valeur par défaut. À noter que quand vous utilisez cette option, votre contenu n'est pas protégé pendant le transit ou le repos dans le stockage.
  Si vous prévoyez de fournir un MP4 sous forme de téléchargement progressif, utilisez cette option.
* **StorageEncrypted** : utilisez cette option pour chiffrer votre contenu localement à l’aide du chiffrement Advanced Encryption Standard (AES) 256 bits, qui est ensuite chargé sur Azure Storage, où il est stocké au repos sous forme chiffrée. Les éléments multimédias protégés par le chiffrement de stockage sont automatiquement déchiffrés et placés dans un système de fichiers chiffré avant d’être encodés, puis éventuellement rechiffrés avant d’être rechargés sous la forme d’un nouvel élément multimédia de sortie. Le principal cas d'utilisation du chiffrement de stockage concerne la sécurisation de fichiers multimédias d'entrée de haute qualité avec un chiffrement renforcé au repos sur le disque.
* **CommonEncryptionProtected** : utilisez cette option quand vous chargez du contenu qui a déjà été chiffré et protégé avec la DRM Common Encryption ou PlayReady (par exemple Smooth Streaming protégé avec la DRM PlayReady).
* **EnvelopeEncryptionProtected** : utilisez cette option lorsque vous téléchargez un contenu au format TLS chiffré avec AES. Notez que les fichiers doivent avoir été encodés et chiffrés par le gestionnaire de transformation Transform Manager.

La méthode **CreateFromFile** vous permet également de spécifier un rappel pour indiquer la progression du chargement du fichier.

Dans l’exemple suivant, nous spécifions **None** pour les options de l’élément multimédia.

Ajoutez la méthode suivante à la classe Program.

    static public IAsset UploadFile(string fileName, AssetCreationOptions options)
    {
        IAsset inputAsset = _context.Assets.CreateFromFile(
            fileName,
            options,
            (af, p) =>
            {
                Console.WriteLine("Uploading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Asset {0} created.", inputAsset.Id);

        return inputAsset;
    }


## <a name="encode-the-source-file-into-a-set-of-adaptive-bitrate-mp4-files"></a>Encoder le fichier source en un ensemble de fichiers MP4 à débit adaptatif
Après avoir reçu des éléments multimédias dans Media Services, vous pouvez encoder un média, modifier le format de ce dernier, lui appliquer un filigrane, etc. avant de le livrer à des clients. Afin de garantir des performances et une disponibilité optimales, ces activités sont planifiées et exécutées dans de nombreuses instances de rôle en arrière-plan. Ces activités s’appellent des travaux et chaque Travail se compose de tâches atomiques qui effectuent le travail à proprement parler sur le fichier de ressource.

Comme mentionné précédemment, lorsque vous travaillez avec Azure Media Services, un des scénarios les plus courants est la diffusion de contenu à débit adaptatif à vos clients. Media Services peut regrouper de façon dynamique un ensemble de fichiers MP4 à débit adaptatif dans un des formats suivants :HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH et HDS (pour licences Adobe PrimeTime/Access uniquement).

Pour tirer parti de l’empaquetage dynamique, vous devez effectuer les opérations suivantes :

* Coder ou transcoder vos fichiers votre fichier mezzanine (source) en un ensemble de fichiers mp4 à débit adaptatif ou de fichiers Smooth Streaming à débit adaptatif.  
* Obtenir au moins une unité de diffusion pour le point de terminaison de diffusion à partir duquel vous envisagez de distribuer votre contenu.

Le code suivant vous explique comment effectuer envoyer une tâche d'encodage. Le travail contient une tâche qui spécifie le fichier mezzanine à transcoder en un ensemble de MP4 à débit adaptatif à l’aide de **Media Encoder Standard**. Le code envoie la tâche et attend qu'elle soit terminée.

Une fois la tâche terminée, vous pourrez diffuser votre élément multimédia ou télécharger progressivement les fichiers MP4 qui ont été créés après le transcodage.
Notez que vous n’avez pas besoin d’unité de diffusion en continu pour télécharger progressivement les fichiers MP4.

Ajoutez la méthode suivante à la classe Program.

    static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
    {

        // Prepare a job with a single task to transcode the specified asset
        // into a multi-bitrate asset.

        IJob job = _context.Jobs.CreateWithSingleTask(
            "Media Encoder Standard",
            "H264 Multiple Bitrate 720p",
            asset,
            "Adaptive Bitrate MP4",
            options);

        Console.WriteLine("Submitting transcoding job...");


        // Submit the job and wait until it is completed.
        job.Submit();

        job = job.StartExecutionProgressTask(
            j =>
            {
                Console.WriteLine("Job state: {0}", j.State);
                Console.WriteLine("Job progress: {0:0.##}%", j.GetOverallProgress());
            },
            CancellationToken.None).Result;

        Console.WriteLine("Transcoding job finished.");

        IAsset outputAsset = job.OutputMediaAssets[0];

        return outputAsset;
    }

## <a name="publish-the-asset-and-get-urls-for-streaming-and-progressive-download"></a>Publier l'élément et obtenir les URL de diffusion et de téléchargement progressif

Pour diffuser en continu ou télécharger un élément multimédia, vous devez tout d'abord le « publier » en créant un localisateur. Les localisateurs assurent l’accès aux fichiers contenus dans l’élément multimédia. Media Services prend en charge deux types de localisateurs : les localisateurs OnDemandOrigin, utilisés pour diffuser du contenu multimédia (par exemple, MPEG DASH, HLS ou Smooth Streaming) et les localisateurs SAP (signature d’accès partagé), utilisés pour télécharger des fichiers multimédias (pour plus d’informations sur les localisateurs SAP, consultez [ce blog](http://southworks.com/blog/2015/05/27/reusing-azure-media-services-locators-to-avoid-facing-the-5-shared-access-policy-limitation/)).

Une fois que vous avez créé les localisateurs, vous pouvez générer les URL utilisées pour transmettre en continu ou télécharger les fichiers.

Les URL de diffusion en continu pour Smooth Streaming ont le format suivant :

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Les URL de diffusion en continu pour HLS ont le format suivant :

     {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Les URL de diffusion en continu pour MPEG DASH ont le format suivant :

    {streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Les URL SAS permettant de télécharger les fichiers ont le format suivant :

    {blob container name}/{asset name}/{file name}/{SAS signature}

Les extensions du Kit de développement logiciel (SDK) Media Services pour .NET fournissent des méthodes d'assistance pratiques qui retournent des URL formatées pour la ressource publiée.

Le code suivant utilise les extensions du Kit de développement logiciel (SDK) Media Services pour .NET pour créer des localisateurs, obtenir la diffusion en continu et les URL de téléchargement progressif. Le code montre également comment télécharger les fichiers vers un dossier local.

Ajoutez la méthode suivante à la classe Program.

    static public void PublishAssetGetURLs(IAsset asset)
    {
        // Publish the output asset by creating an Origin locator for adaptive streaming,
        // and a SAS locator for progressive download.

        _context.Locators.Create(
            LocatorType.OnDemandOrigin,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));

        _context.Locators.Create(
            LocatorType.Sas,
            asset,
            AccessPermissions.Read,
            TimeSpan.FromDays(30));


        IEnumerable<IAssetFile> mp4AssetFiles = asset
                .AssetFiles
                .ToList()
                .Where(af => af.Name.EndsWith(".mp4", StringComparison.OrdinalIgnoreCase));

        // Get the Smooth Streaming, HLS and MPEG-DASH URLs for adaptive streaming,
        // and the Progressive Download URL.
        Uri smoothStreamingUri = asset.GetSmoothStreamingUri();
        Uri hlsUri = asset.GetHlsUri();
        Uri mpegDashUri = asset.GetMpegDashUri();

        // Get the URls for progressive download for each MP4 file that was generated as a result
        // of encoding.
        List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Display  the streaming URLs.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

        // Display the URLs for progressive download.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Download the output asset to a local folder.
        string outputFolder = "job-output";
        if (!Directory.Exists(outputFolder))
        {
            Directory.CreateDirectory(outputFolder);
        }

        Console.WriteLine();
        Console.WriteLine("Downloading output asset files to a local folder...");
        asset.DownloadToFolder(
            outputFolder,
            (af, p) =>
            {
                Console.WriteLine("Downloading '{0}' - Progress: {1:0.##}%", af.Name, p.Progress);
            });

        Console.WriteLine("Output asset files available at '{0}'.", Path.GetFullPath(outputFolder));
    }

## <a name="test-by-playing-your-content"></a>Tester en lisant votre contenu
Une fois que vous exécutez le programme défini dans la section précédente, les URL similaires à celles qui suivent seront affichées dans la fenêtre de la console.

URL de diffusion adaptative :

Smooth Streaming

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest

HLS

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=m3u8-aapl)

MPEG DASH

    http://amstestaccount001.streaming.mediaservices.windows.net/ebf733c4-3e2e-4a68-b67b-cc5159d1d7f2/BigBuckBunny.ism/manifest(format=mpd-time-csf)

URL de téléchargement progressif (audio et vidéo).

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_650kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_3400kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_2250kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1500kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_H264_1000kbps_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_96kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z

    https://storagetestaccount001.blob.core.windows.net/asset-38058602-a4b8-4b33-b9f0-6880dc1490ea/BigBuckBunny_AAC_und_ch2_56kbps.mp4?sv=2012-02-12&sr=c&si=166d5154-b801-410b-a226-ee2f8eac1929&sig=P2iNZJAvAWpp%2Bj9yV6TQjoz5DIIaj7ve8ARynmEM6Xk%3D&se=2015-02-14T01:13:05Z


Pour tester votre vidéo, utilisez le [lecteur Azure Media Services](http://amsplayer.azurewebsites.net/azuremediaplayer.html).

Pour tester le téléchargement progressif, collez l'URL dans un navigateur (par exemple, Internet Explorer, Chrome ou Safari).

## <a name="next-steps-media-services-learning-paths"></a>Étapes suivantes : Parcours d’apprentissage Media Services
[!INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

## <a name="provide-feedback"></a>Fournir des commentaires
[!INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

### <a name="looking-for-something-else"></a>Vous recherchez quelque chose d’autre ?
Si cette rubrique ne répond pas à vos attentes ou besoins, ou ne contient pas les informations recherchées, faites-nous part de vos commentaires à l’aide du fil de discussion Disqus ci-dessous.

<!-- Anchors. -->


<!-- URLs. -->
[Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
[Portal]: http://manage.windowsazure.com/



<!--HONumber=Dec16_HO1-->


