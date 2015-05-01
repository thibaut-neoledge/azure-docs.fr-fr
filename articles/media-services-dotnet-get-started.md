<properties 
	pageTitle="Prise en main du Kit de développement logiciel (SDK) Media Services pour .NET - Azure" 
	description="Ce didacticiel vous présente les étapes d'implémentation d'une application de diffusion de contenu vidéo à la demande (VoD) avec Azure Media Services pour .NET." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="dotnet" 
	ms.topic="article" 
	ms.date="02/24/2015" 
	ms.author="juliako"/>


# Démarrage rapide : Distribution de vidéo à la demande avec le Kit de développement logiciel (SDK) Media Services pour .NET 

[AZURE.INCLUDE [media-services-selector-get-started](../includes/media-services-selector-get-started.md)]


>[AZURE.NOTE]
> Pour suivre ce didacticiel, vous avez besoin d'un compte Azure. Si vous ne possédez pas de compte, vous pouvez créer un compte d'évaluation gratuit en quelques minutes. Pour plus d'informations, consultez la page <a href="http://www.windowsazure.com/pricing/free-trial/?WT.mc_id=A8A8397B5" target="_blank">Version d'évaluation gratuite d'Azure</a>.

Ce didacticiel vous présente les étapes d'implémentation d'une application de diffusion de contenu vidéo à la demande (VoD) avec le Kit de développement logiciel (SDK) Azure Media Services (AMS) pour .NET. 


Il présente le workflow Media Services de base et les objets et tâches de programmation les plus courants requis pour le développement Media Services. À la fin de ce didacticiel, vous pourrez lire en continu ou télécharger de façon progressive un exemple de fichier multimédia que vous aurez chargé, encodé et téléchargé.  

## Conditions préalables
Les conditions préalables suivantes sont requises pour commencer à développer avec le Kit de développement logiciel (SDK) Media Services pour .NET.

- Systèmes d'exploitation : Windows 7, Windows 2008 R2, Windows 8 ou une version ultérieure.
- .NET Framework 4.5 ou .NET Framework 4.0
- Visual Studio 2013, Visual Studio 2012 Visual Studio 2010 SP1 (Professional, Premium, Ultimate ou Express).


Les tâches suivantes sont expliquées dans cette rubrique de démarrage rapide.

1.  Créer un compte Media Services grâce à la méthode Création rapide (à l'aide du portail)
2.  Configurer un point de terminaison de diffusion en continu (à l'aide du portail)
3.  Créer et configurer un projet Visual Studio
5.  Se connecter au compte Media Services
1.  Créer un nouvel élément et charger un fichier vidéo
1.  Encoder le fichier source en un ensemble de fichiers MP4 à débit adaptatif
1.  Configurer la stratégie de distribution de l'élément encodé
1.  Publier les éléments et obtenir les URL de diffusion et de téléchargement progressif  
1.  Lire votre contenu 


## Création d'un compte Media Services grâce à la méthode Création rapide

1. Dans le [portail de gestion][], cliquez sur **Nouveau**, sur **Service de média**, puis sur **Création rapide**.
   
	![Media Services - Création rapide](./media/media-services-create-account/wams-QuickCreate.png)

2. Dans **NOM**, entrez le nom du nouveau compte. Un nom de compte Media Services se compose de chiffres ou de lettres en minuscules, sans espaces. Sa longueur est comprise entre 3 et 24 caractères. 

3. Dans **RÉGION**, sélectionnez la région géographique qui sera utilisée pour stocker les enregistrements de métadonnées pour votre compte Media Services. Seules les régions Media Services disponibles s'affichent dans la liste déroulante. 

4. Dans **COMPTE DE STOCKAGE**, sélectionnez un compte de stockage pour fournir un stockage d'objets blob du contenu multimédia à partir de votre compte Media Services. Vous pouvez sélectionner un compte de stockage existant dans la même région géographique que votre compte Media Services ou en créer un. Ce dernier sera créé dans la même région. 

5. Si vous avez créé un compte de stockage, entrez un nom dans **NOUVEAU NOM DU COMPTE DE STOCKAGE**. Les règles des noms de compte de stockage sont identiques à celles des comptes Media Services.

6. Cliquez sur **Création rapide**, en bas du formulaire.

	Vous pouvez surveiller l'état du processus dans la zone de message en bas de la fenêtre.

	Une fois le compte créé, l'état est défini sur Actif. 
	
	Au bas de la page, le bouton **GÉRER LES CLÉS** s'affiche. Lorsque vous cliquez sur ce bouton, une boîte de dialogue avec le nom du compte Media Services et les clés primaires et secondaires s'affiche. Vous devez disposer du nom de compte et des informations de clé primaire pour accéder par programme au compte Media Services. 

	
	![Media Services Page](./media/media-services-create-account/wams-mediaservices-page.png)

	Lorsque vous double-cliquez sur le nom de compte, la page Démarrage rapide s'affiche par défaut. Elle vous permet d'effectuer des tâches de gestion également disponibles sur d'autres pages du portail. Par exemple, vous pouvez télécharger un fichier vidéo depuis cette page ou depuis la page CONTENU.

	 
## Configurer un point de terminaison de diffusion en continu à l'aide du portail

Lorsque vous travaillez avec Azure Media Services, un des scénarios les plus courants est la diffusion de contenu à débit adaptatif à vos clients. Avec la diffusion à débit binaire adaptatif, le client peut basculer vers un flux à débit binaire supérieur ou inférieur, car la vidéo est affichée en fonction de la bande passante réseau actuelle, de l'utilisation de l'UC et d'autres facteurs. Media Services prend en charge les technologies de diffusion en continu à débit binaire adaptatif suivantes : HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH et HDS (pour licences Adobe PrimeTime/Access uniquement). 

Media Services fournit l'empaquetage dynamique qui permet de distribuer un contenu en diffusion continue en MP4 ou Smooth Streaming dans un format pris en charge par Media Services (MPEG DASH, HLS, Smooth Streaming, HDS) sans avoir à créer de nouveaux packages dans ces formats. 

Pour tirer parti de l'empaquetage dynamique, vous devez effectuer les opérations suivantes :

- Coder ou transcoder vos fichiers votre fichier mezzanine (source) en un ensemble de fichiers mp4 à débit adaptatif ou de fichiers Smooth Streaming à débit adaptatif (les étapes de codage sont décrites plus loin dans ce didacticiel).  
- Obtenir au moins une unité de diffusion en continu pour le **point de terminaison de diffusion en continu** à partir duquel vous envisagez de distribuer votre contenu.

Avec l'empaquetage dynamique, vous devez stocker et payer les fichiers dans un seul format de stockage. Ensuite, Media Services crée et fournit la réponse appropriée en fonction des demandes des clients. 

Pour changer le nombre d'unités réservées de diffusion en continu, procédez comme suit :

1. Dans le [portail de gestion](https://manage.windowsazure.com/), cliquez sur **Media Services**. Cliquez ensuite sur le nom du service multimédia.

2. Sélectionnez la page POINTS DE TERMINAISON DE DIFFUSION EN CONTINU. Cliquez ensuite sur le point de terminaison de diffusion en continu que vous souhaitez modifier.

3. Pour spécifier le nombre d'unités de diffusion en continu, sélectionnez l'onglet METTRE À L'ÉCHELLE et déplacez le curseur de **capacité réservée**.

	![Scale page](./media/media-services-how-to-scale/media-services-origin-scale.png)

4. Appuyez sur le bouton ENREGISTRER pour enregistrer vos modifications.

	L'allocation de nouvelles unités prend environ 20 minutes. 

	 
	>[AZURE.NOTE] Actuellement, le fait de passer d'une valeur positive à zéro pour le nombre d'unités de diffusion en continu peut désactiver la diffusion en continu pendant une heure.
	>
	> C'est le plus grand nombre d'unités spécifiées sur 24 heures qui est utilisé pour calculer le coût. Pour des informations détaillées sur la tarification, consultez la page [Détails de la tarification des services de média](http://go.microsoft.com/fwlink/?LinkId=275107).



## Créer et configurer un projet Visual Studio

1. Créez une nouvelle application console C# dans Visual Studio 2013, Visual Studio 2012 ou Visual Studio 2010 SP1. Entrez le **nom**, l'**emplacement** et le **nom de solution**, puis cliquez sur OK. 

2. Utilisez le package Nuget [windowsazure.mediaservices.extensions](https://www.nuget.org/packages/windowsazure.mediaservices.extensions) pour installer les **extensions du Kit de développement logiciel (SDK) Azure Media Services pour .NET**.  Les extensions du Kit de développement logiciel (SDK) Media Services pour .NET sont un ensemble de méthodes d'extension et de fonctions d'assistance qui simplifient votre code et le développement avec les Services de média. L'installation de ce package installe également le **Kit de développement logiciel (SDK) Media Services pour .NET** et ajoute toutes les autres dépendances requises.
 
3. Ajoutez une référence à l'assembly System.Configuration. Cet assembly contient la classe System.Configuration.ConfigurationManager qui est utilisée pour accéder aux fichiers de configuration (par exemple, App.config). 

4. Ouvrez le fichier App.config (ajoutez le fichier à votre projet s'il n'a pas été ajouté par défaut) et ajoutez une section *appSettings* au fichier. Définissez les valeurs pour le nom et la clé de votre compte Azure Media Services, comme illustré dans l'exemple suivant. Pour obtenir le nom de compte et les informations sur clé, ouvrez le portail de gestion Azure, sélectionnez votre compte Media Services et cliquez sur le **GÉRER LES CLÉS** bouton.


	<pre><code>
	&lt;configuration&gt;
        &lt;appSettings&gt;
    	&lt;add key="MediaServicesAccountName" value="Media-Services-Account-Name" /&gt;
        	&lt;add key="MediaServicesAccountKey" value="Media-Services-Account-Key" /&gt;
  	    &lt;/appSettings&gt;
	&lt;/configuration&gt;
	</code></pre>


5. Remplacez les instructions using existantes au début du fichier Program.cs par le code suivant.

		using System;
		using System.Collections.Generic;
		using System.Linq;
		using System.Text;
		using System.Threading.Tasks;
		using System.Configuration;
		using System.Threading;
		using System.IO;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;

6. Créez un dossier sous le répertoire de projets et copiez-y le fichier .mp4 ou .wmv à encoder et à diffuser en continu ou télécharger. Dans cet exemple, le chemin d'accès " C:\VideoFiles " est utilisé. 

## Se connecter au compte Media Services

Lorsque vous utilisez Media Services avec .NET, vous devez utiliser la classe **CloudMediaContext** pour la plupart des tâches de programmation Media Services : connexion au compte Media Services, création, mise à jour, accès et suppression des objets suivants : éléments multimédia, fichiers multimédias, travaux, stratégies d'accès, localisateurs, etc. 
 
Remplacez la classe Program par défaut par le code ci-dessous. Le code montre comment lire les valeurs de connexion à partir du fichier App.config et comment créer l'objet CloudMediaContext pour se connecter aux Services de média. Pour plus d'informations sur la connexion à Media Services, consultez la page [Connexion à Media Services avec le Kit de développement logiciel (SDK) Media Services pour .NET](http://msdn.microsoft.com/library/azure/jj129571.aspx).

La fonction **Main** appelle des méthodes qui seront définies ultérieurement dans cette section.
	
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
                // Créez et mettez en cache les informations d'identification Media Services dans une variable de classe statique.
                _cachedCredentials = new MediaServicesCredentials(
                                _mediaServicesAccountName,
                                _mediaServicesAccountKey);
                // Les informations d'identification en cache ont été utilisées pour créer CloudMediaContext.
                _context = new CloudMediaContext(_cachedCredentials);

                // Ajoutez des appels aux méthodes définies dans cette section.

                IAsset inputAsset =
                    UploadFile(@"C:\VideoFiles\BigBuckBunny.mp4", AssetCreationOptions.None);

                IAsset encodedAsset =
                    EncodeToAdaptiveBitrateMP4s(inputAsset, AssetCreationOptions.None);

                ConfigureClearAssetDeliveryPolicy(encodedAsset);

                PublishAssetGetURLs(encodedAsset);
            }
            catch (Exception exception)
            {
                // Analysez le message d'erreur XML dans la réponse de Media Services et créez une nouvelle 
                // exception avec son contenu.
                exception = MediaServicesExceptionParser.Parse(exception);

                Console.Error.WriteLine(exception.Message);
            }
            finally
            {
                Console.ReadLine();
            }
        }

## Créer un nouvel élément et charger un fichier vidéo

Dans Media Services, vous téléchargez (ou réceptionnez) vos fichiers numériques dans un élément multimédia. L'entité **Asset** peut contenir des fichiers vidéo et audio, des images, des collections de miniatures, des pistes textuelles et des légendes (et les métadonnées concernant ces fichiers).  Une fois les fichiers téléchargés, votre contenu est stocké en toute sécurité dans le cloud et peut faire l'objet d'un traitement et d'une diffusion en continu. Les fichiers de l'élément multimédia sont appelés **fichiers d'élément multimédia**.

La méthode **UploadFile** définie ci-dessous appelle **CreateFromFile** (défini dans les extensions du Kit de développement logiciel (SDK) .NET). **CreateFromFile** crée un nouvel élément multimédia dans lequel le fichier source spécifié est téléchargé. 

La méthode **CreateFromFile** prend **AssetCreationOptions**, qui vous permet de spécifier les options de création suivantes :
 
- **None** - Aucun chiffrement. Il s'agit de la valeur par défaut. À noter que quand vous utilisez cette option, votre contenu n'est pas protégé pendant le transit ou le repos dans le stockage.
Si vous prévoyez de fournir un MP4 sous forme de téléchargement progressif, utilisez cette option. 
- **StorageEncrypted** : permet de chiffrer votre contenu en clair localement en utilisant le chiffrement AES-256 bits, puis de le télécharger vers Azure Storage où il est chiffré pour le stockage, au repos. Les éléments multimédias protégés par le chiffrement de stockage sont automatiquement déchiffrés et placés dans un système de fichiers chiffré avant d'être encodés, puis éventuellement rechiffrés avant d'être rechargés sous la forme d'un nouvel élément multimédia de sortie. Le principal cas d'utilisation du chiffrement de stockage concerne la sécurisation de fichiers multimédias d'entrée de haute qualité avec un chiffrement renforcé au repos sur le disque.
- **CommonEncryption** : utilisez cette option lorsque vous téléchargez du contenu qui a déjà été chiffré et protégé par chiffrement commun ou gestion des droits numériques (DRM) PlayReady (par exemple, une diffusion en continu lisse, " Smooth Streaming ", protégée par gestion des droits numériques (DRM) PlayReady).
- **EnvelopeEncrypted** : utilisez cette option lorsque vous téléchargez du contenu au format HLS chiffré avec AES. Notez que les fichiers doivent avoir été encodés et chiffrés par le gestionnaire de transformation Transform Manager.

La méthode **CreateFromFile** vous permet également de spécifier un rappel pour signaler la progression du téléchargement du fichier.

Dans l'exemple suivant, nous spécifions **aucun** pour les options de l'élément multimédia.

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


## Encoder le fichier source en un ensemble de fichiers MP4 à débit adaptatif

Après avoir reçu des éléments multimédias dans Media Services, vous pouvez encoder un média, modifier le format de ce dernier, lui appliquer un filigrane, etc. avant de le livrer à des clients. Afin de garantir des performances et une disponibilité optimales, ces activités sont planifiées et exécutées dans de nombreuses instances de rôle en arrière-plan. Ces activités s'appellent des travaux et chaque travail se compose de tâches atomiques qui effectuent le travail à proprement parler sur le fichier de ressource. 

Comme mentionné précédemment, lorsque vous travaillez avec Azure Media Services, un des scénarios les plus courants est la diffusion de contenu à débit adaptatif à vos clients. Media Services peut regrouper de façon dynamique un ensemble de fichiers MP4 à débit adaptatif dans un des formats suivants : HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH et HDS (pour licences Adobe PrimeTime/Access uniquement). 

Pour tirer parti de l'empaquetage dynamique, vous devez effectuer les opérations suivantes :

- Coder ou transcoder vos fichiers votre fichier mezzanine (source) en un ensemble de fichiers mp4 à débit adaptatif ou de fichiers Smooth Streaming à débit adaptatif.  
- Obtenir au moins une unité de diffusion en continu pour le point de terminaison de diffusion en continu à partir duquel vous envisagez de distribuer votre contenu. 

Le code suivant vous explique comment effectuer envoyer une tâche d'encodage. La tâche contient une tâche qui spécifie le fichier mezzanine à transcoder en un ensemble de MP4 à débit adaptatif à l'aide d'**Azure Media Encoder**. Le code envoie la tâche et attend qu'elle soit terminée. 

Une fois la tâche terminée, vous pourrez diffuser votre élément multimédia ou télécharger progressivement les fichiers MP4 qui ont été créés après le transcodage.
Notez que vous n'avez pas besoin d'unité de diffusion en continu pour télécharger progressivement les fichiers MP4. 


Ajoutez la méthode suivante à la classe Program.

	static public IAsset EncodeToAdaptiveBitrateMP4s(IAsset asset, AssetCreationOptions options)
	{
		// Préparez un travail avec une tâche unique pour transcoder l'élément multimédia spécifié
        // into a multi-bitrate asset.

	    IJob job = _context.Jobs.CreateWithSingleTask(
	        MediaProcessorNames.AzureMediaEncoder,
	        MediaEncoderTaskPresetStrings.H264AdaptiveBitrateMP4Set720p,
	        asset,
	        "Adaptive Bitrate MP4",
	        options);
	
		Console.WriteLine("Submitting transcoding job...");
	

	    // Envoyez la tâche et attendez qu'elle soit terminée.
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

## Configurer la stratégie de distribution de l'élément encodé

Une des étapes du processus de distribution de contenu Media Services est la configuration des stratégies de distribution des éléments multimédias. Voici ce que comprend la configuration de stratégie de distribution de l'élément : les types de protocoles qui peuvent être utilisés pour la distribution (par exemple, MPEG DASH, HLS, HDS, Smooth Streaming ou tous), si vous souhaitez chiffrer dynamiquement votre élément multimédia et le type de chiffrement à utiliser (d'enveloppe ou commun). 

La méthode **ConfigureClearAssetDeliveryPolicy** qui suit indique ne pas appliquer le chiffrement dynamique et de distribuer le flux via un des protocoles suivants :  MPEG DASH, HLS et Smooth Streaming. 
  
Ajoutez la méthode suivante à la classe Program.

    static public void ConfigureClearAssetDeliveryPolicy(IAsset asset)
    {
        IAssetDeliveryPolicy policy =
            _context.AssetDeliveryPolicies.Create("Clear Policy",
            AssetDeliveryPolicyType.NoDynamicEncryption, 
            AssetDeliveryProtocol.HLS | AssetDeliveryProtocol.SmoothStreaming | AssetDeliveryProtocol.Dash, null);

        asset.DeliveryPolicies.Add(policy);
    }


## Publier les éléments et obtenir les URL de diffusion et de téléchargement progressif

Pour diffuser en continu ou télécharger un élément multimédia, vous devez tout d'abord le " publier " en créant un localisateur. Les localisateurs assurent l'accès aux fichiers contenus dans l'élément multimédia. Media Services prend en charge deux types de localisateurs : les localisateurs OnDemandOrigin, utilisés pour diffuser du contenu multimédia (par exemple, MPEG DASH, HLS ou Smooth Streaming) et les localisateurs d'URL SAP (signature d'accès partagé), utilisés pour télécharger des fichiers multimédias.

Une fois que vous avez créé les localisateurs, vous pouvez générer les URL utilisées pour transmettre en continu ou télécharger les fichiers. 


Les URL de diffusion en continu pour Smooth Streaming ont le format suivant :

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Les URL de diffusion en continu pour HLS ont le format suivant :

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Les URL de diffusion en continu pour MPEG DASH ont le format suivant :

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)

Les URL SAS permettant de télécharger les fichiers ont le format suivant :

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

        // Obtenez l'URL de téléchargement progressif de chaque fichier MP4 généré suite
		// à l'encodage.
		List<Uri> mp4ProgressiveDownloadUris = mp4AssetFiles.Select(af => af.GetSasUri()).ToList();


        // Affichez les URL de diffusion en continu.
        Console.WriteLine("Use the following URLs for adaptive streaming: ");
        Console.WriteLine(smoothStreamingUri);
        Console.WriteLine(hlsUri);
        Console.WriteLine(mpegDashUri);
        Console.WriteLine();

		// Affichez les URL de téléchargement progressif.
        Console.WriteLine("Use the following URLs for progressive download.");
        mp4ProgressiveDownloadUris.ForEach(uri => Console.WriteLine(uri + "\n"));
        Console.WriteLine();

        // Téléchargez l'élément multimédia de sortie dans un dossier local.
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

## Lire votre contenu  

Une fois que vous exécutez le programme défini dans la section précédente, les URL similaires à celles qui suivent seront affichées dans la fenêtre de la console.

URL de diffusion adaptative :

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


Pour tester la diffusion Smooth Streaming, utilisez [http://amsplayer.azurewebsites.net/](http://amsplayer.azurewebsites.net/) ou [http://smf.cloudapp.net/healthmonitor](http://smf.cloudapp.net/healthmonitor).

Pour tester MPEG DASH, utilisez [http://dashif.org](http://dashif.org/reference/players/javascript/).

Pour tester TLS, utilisez un appareil iOS ou Safari ou un lecteur [3ivx-hls-player](http://apps.microsoft.com/windows/app/3ivx-hls-player/f79ce7d0-2993-4658-bc4e-83dc182a0614). 


Pour tester le téléchargement progressif, collez l'URL dans un navigateur (par exemple, Internet Explorer, Chrome ou Safari).


### Ressources supplémentaires
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-101-Get-your-video-online-now-">Azure Media Services 101 - Mettez votre vidéo en ligne dès maintenant</a>
- <a href="http://channel9.msdn.com/Shows/Azure-Friday/Azure-Media-Services-102-Dynamic-Packaging-and-Mobile-Devices">Azure Media Services 102 - Empaquetage dynamique et appareils mobiles</a>


<!-- Anchors. -->


<!-- URLs. -->
  [Web Platform Installer]: http://go.microsoft.com/fwlink/?linkid=255386
  [Portail de gestion]: http://manage.windowsazure.com/



<!--HONumber=52-->