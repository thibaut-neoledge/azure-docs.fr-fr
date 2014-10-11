<properties linkid="develop-media-services-how-to-guides-media-services-java" urlDisplayName="Media Services" pageTitle="How to use Media Services (Java) - Azure feature guide" metaKeywords="Azure Media Services, Azure media, Azure streaming, azure media, azure streaming, azure encoding" description="Describes how to use Azure Media Services to perform common tasks including encoding, encrypting, and streaming resources." metaCanonical="" services="media-services" documentationCenter="Java" title="How to Use Media Services" authors="robmcm" solutions="" manager="wpickett" editor="mollybos" scriptId="" videoId="" />

<tags ms.service="media-services" ms.workload="media" ms.tgt_pltfrm="na" ms.devlang="na" ms.topic="article" ms.date="01/01/1900" ms.author="robmcm"></tags>

# Utilisation de Media Services

Ce guide montre comment commencer à programmer avec Azure Media Services en utilisant Java. Il inclut une présentation technique de Media Services, les étapes de configuration de votre compte Azure pour Media Services et du code qui présente des tâches de programmation classiques.

## Sommaire

-   [Présentation de Media Services][]
-   [Configuration d'un compte Azure pour Media Services][]
-   [Configuration pour le développement Media Services][]
-   [Utilisation de Media Services avec Java][]
-   [Ressources supplémentaires][]

## <a name="what-are"></a><span class="short header">Présentation de Media Services</span>Présentation de Media Services

Azure Media Services est une plateforme multimédia extensible qui intègre la plateforme multimédia Microsoft et les composants multimédias tiers les plus fiables au sein d'Azure. Media Services fournit dans le cloud un pipeline multimédia qui permet aux partenaires du secteur d'étendre ou de remplacer les technologies relatives aux composants. Les éditeurs de logiciels indépendants et les fournisseurs de médias peuvent utiliser Media Services pour créer des solutions multimédias de bout en bout. Cette présentation décrit l'architecture générale et les scénarios de développement courants relatifs à Media Services.

Le schéma suivant illustre l'architecture Media Services de base.

![Architecture Media Services][]

### Prise en charge des fonctionnalités de Media Services

Les fonctionnalités suivantes de la version actuelle de Media Services permettent de développer des applications multimédias dans le cloud.

-   **Réception**. Les opérations de réception permettent d'intégrer des éléments multimédias au système, par exemple en les téléchargeant et en les chiffrant avant de les placer dans Azure Storage. À partir de la version finale, Media Services proposera l'intégration avec des composants partenaires pour fournir des solutions de téléchargement UDP (User Datagram Protocol) rapides.
-   **Encodage**. Les opérations d'encodage englobent l'encodage proprement dit ainsi que la transformation et la conversion des éléments multimédias. Vous pouvez exécuter des tâches d'encodage dans le cloud en utilisant l'encodeur multimédia qui est inclus dans Media Services. Les options d'encodage disponibles sont les suivantes :
	-   Utilisez l'Encodeur multimédia Azure ainsi que différents codecs et formats standard, comme la diffusion en continu lisse IIS, MP4 et la conversion au format de diffusion en continu HTTP Apple.
	-   Convertissez des bibliothèques entières ou des fichiers individuels avec un contrôle total sur les entrées et sorties.
	-   Un grand nombre de types de fichiers, de formats et de codecs sont pris en charge (voir [Types de fichiers pris en charge pour Media Services][]).
	-   Conversions aux formats pris en charge. Media Services vous permet de convertir le format ISO MP4 (.mp4) au format de fichier de diffusion en continu lisse (PIFF 1.3) (.ismv; .isma). Vous pouvez également convertir le format de fichier de diffusion en continu lisse (PIFF) au format de diffusion en continu HTTP Apple (.msu8, .ts).
-   **Protection**. La protection de contenu consiste à chiffrer le contenu diffusé en continu ou à la demande afin de sécuriser son transport, son stockage et sa fourniture. Media Services fournit une solution à technologie DRM non spécifiée pour protéger le contenu. Les technologies de gestion des droits numériques (DRM) actuellement prises en charge sont Microsoft PlayReady Protection et MPEG Common Encryption. D'autres technologies DRM seront bientôt disponibles.
-   **Diffusion en continu**. La diffusion d'un contenu en continu implique de l'envoyer en direct ou à la demande aux clients. Vous pouvez également récupérer ou télécharger des fichiers multimédias spécifiques sur le cloud. Media Services fournit une solution au format non spécifié pour du contenu de diffusion en continu. Media Services prend en charge l'origine de la diffusion en continu pour les formats de diffusion en continu lisse, de diffusion en continu HTTP Apple et MP4. D'autres formats seront bientôt disponibles. Vous pouvez aussi diffuser du contenu en continu en utilisant le CDN Azure ou un CDN tiers, qui offre la possibilité de passer à plusieurs millions d'utilisateurs.

### Scénarios de développement de Media Services

Media Services prend en charge différents scénarios de développement multimédia courants comme décrit dans le tableau suivant.

<table border="2" cellspacing="0" cellpadding="5" style="border: 2px solid #000000;">

<thead>

<tr>

<th>
Scénario

</th>

<th>
Description

</th>

</tr>

</thead>

<tbody>

<tr>

<td>
Création de workflows de bout en bout

</td>

<td>
Créez des workflows multimédias complets dans le cloud. Du téléchargement de fichiers multimédias à la distribution de contenu, Media Services fournit un large éventail de composants qui peuvent être combinés pour gérer les workflows d'applications spécifiques. Les fonctionnalités actuellement disponibles sont le téléchargement, le stockage, l'encodage, la conversion de format, la protection de contenu et la diffusion de contenu en continu à la demande.

</td>

</tr>

<tr>

<td>
Création de workflows hybrides

</td>

<td>
Vous pouvez intégrer Media Services à des outils et processus existants. Par exemple, encodez un contenu local, puis téléchargez-le dans Media Services pour un transcodage en différents formats et une diffusion via le réseau de distribution de contenu (CDN) Azure ou via un CDN tiers. Media Services peut être appelé individuellement via des API REST standard pour une intégration à des applications et services externes.

</td>

</tr>

<tr>

<td>
Prise en charge du cloud pour les lecteurs multimédias

</td>

<td>
Vous pouvez créer, gérer et fournir des fichiers multimédias pour différents appareils (iOS, Android et Windows, par exemple) et plateformes.

</td>

</tr>

</tbody>

</table>
</p>
### Développement de clients Media Services

Élargissez la portée de votre solution Media Services en utilisant des Kits de développement logiciel (SDK) et des infrastructures de lecture afin de créer des clients multimédias. Ces clients sont destinés aux développeurs qui souhaitent créer des applications Media Services offrant des expériences utilisateur intéressantes sur un large éventail d'appareils et de plateformes. Selon les périphériques pour lesquels vous voulez créer les applications clientes, il est possible d'utiliser les Kits de développement logiciel (SDK) et les infrastructures de lecteur proposés par Microsoft et des partenaires tiers.

Vous trouverez ci-dessous la liste des Kits de développement logiciel (SDK) et des infrastructures de lecture disponibles. Pour plus d'informations sur ce sujet, sur les Kits de développement logiciel (SDK) et les infrastructures de lecteur planifiés et sur les fonctionnalités prises en charge, consultez la rubrique [Développement de clients Media Services][].

#### Prise en charge des clients Mac et PC

Pour les PC et Mac, vous pouvez envisager une expérience de diffusion en continu à l'aide de Microsoft Silverlight ou d'Adobe Open Source Media Framework.

-   [Client de diffusion en continu lisse pour Silverlight][]
-   [Plateforme multimédia Microsoft : Infrastructure de lecture pour Silverlight][]
-   [Plug-in Smooth Streaming pour OSMF 2.0][]. Pour plus d'informations sur l'utilisation de ce plug-in, consultez la rubrique [Utilisation du plug-in Smooth Streaming pour Adobe Open Source Media Framework][].

#### Applications Windows 8

Pour Windows 8, vous pouvez créer des applications Windows Store en utilisant les langages et constructions de développement pris en charge, comme HTML, Javascript, XAML, C# et C+.

-   [Kits de développement logiciel (SDK) pour clients de diffusion en continu lisse Windows 8][]. Pour plus d'informations sur la création d'une application Windows Store à l'aide de ce Kit de développement logiciel (SDK), consultez la rubrique [Création d'une application de diffusion en continu lisse Windows Store][]. Pour plus d'informations sur la création d'un lecteur de diffusion en continu lisse en HTML5, consultez la rubrique [Procédure pas à pas : génération de votre premier lecteur de diffusion en continu lisse HTML5][].

-   [Plateforme multimédia Microsoft : Infrastructure de lecture pour applications Windows Store Windows 8][]

#### Xbox

Xbox prend en charge les applications Xbox LIVE capables d'utiliser du contenu de diffusion en continu lisse. Le Kit de développement d'applications Xbox LIVE (ADK) comprend ce qui suit :

-   Client de diffusion en continu lisse pour ADK Xbox LIVE
-   Plateforme multimédia Microsoft : Infrastructure de lecture pour ADK Xbox LIVE

#### Appareils intégrés ou dédiés

Appareils tels que les téléviseurs connectés, les décodeurs, les lecteurs Blu-Ray, les boîtiers TV OTT et les appareils mobiles dotés d'une infrastructure de développement d'applications personnalisée et d'un pipeline multimédia personnalisé. Microsoft fournit les kits de portage suivants, qui peuvent faire l'objet d'une licence, et permet aux partenaires de procéder au portage de la diffusion en continu lisse pour la plateforme.

-   [Kit de portage de client de diffusion en continu lisse][]
-   [Kit de portage d'appareil Microsoft PlayReady][]

#### Windows Phone

Microsoft fournit un Kit de développement logiciel (SDK) qui permet de créer des applications vidéo premium pour Windows Phone.

-   [Client de diffusion en continu lisse pour Silverlight][]
-   [Plateforme multimédia Microsoft : Infrastructure de lecture pour Silverlight][]

#### Appareils iOS

Pour les appareils iOS tels que les iPhone, iPod et iPad, Microsoft fournit un Kit de développement logiciel (SDK) grâce auquel vous pouvez créer des applications qui permettent à ces plateformes de fournir du contenu vidéo premium : Kit de développement logiciel (SDK) de diffusion en continu lisse pour appareils iOS avec PlayReady. Ce Kit de développement logiciel (SDK) est uniquement accessible aux détenteurs de licences. Pour plus d'informations, veuillez adresser un [courrier électronique à Microsoft][]. Pour plus d'informations sur le développement iOS, consultez la rubrique [Centre de développement iOS][].

#### Appareils Android

Différents partenaires Microsoft fournissent des Kits de développement logiciel (SDK) pour la plateforme Android. Ceux-ci permettent de lire des fichiers de diffusion en continu lisse sur un appareil Android. Pour en savoir plus sur ces partenaires, veuillez adresser un [courrier électronique à Microsoft][1].

## <a name="setup-account"></a><span class="short header">Configuration d'un compte</span>Configuration d'un compte Azure pour Media Services

Pour configurer votre compte Media Services, utilisez le portail de gestion Azure. Consultez la rubrique [Création d'un compte Media Services][]. Une fois votre compte créé dans le portail de gestion, vous êtes prêt à configurer votre ordinateur pour le développement Media Services.

## <a name="setup-dev"> </a><span class="short header">Configuration pour le développement Media Services</span>

Cette section contient la configuration requise générale pour le développement Media Services avec le Kit de développement logiciel (SDK) Media Services pour Java.

### Configuration requise

-   Un compte Media Services dans un abonnement Azure nouveau ou existant. Consultez la rubrique [Création d'un compte Media Services][].
-   Les bibliothèques Azure pour Java, que vous pouvez installer à partir du [Centre de développement Java pour Azure][].

## <a name="connect"> </a><span class="short header">Utilisation de Media Services avec Java</span> Utilisation de Media Services avec Java

Le code suivant montre comment créer un élément multimédia, télécharger un fichier multimédia sur l'élément multimédia, exécuter une tâche pour transformer l'élément multimédia et télécharger les fichiers de sortie de l'élément multimédia transformé.

Avant d'utiliser ce code, vous devez configurer un compte Media Services. Pour plus d'informations sur la configuration d'un compte, consultez la rubrique [Création d'un compte Media Services][2].

Remplacez les valeurs des variables `clientId` et `clientSecret`. Le code repose également sur un fichier stocké localement, `c:/media/MPEG4-H264.mp4`. Vous devez indiquer votre fichier à utiliser. Le code doit comporter un dossier de sortie, `c:/output`, dans lequel les fichiers de sortie sont téléchargés.

    import java.io.*;
    import java.net.URI;
    import java.net.URISyntaxException;
    import java.security.NoSuchAlgorithmException;
    import java.util.EnumSet;
    import java.util.List;

    import com.microsoft.windowsazure.services.blob.client.*;
    import com.microsoft.windowsazure.services.core.Configuration;
    import com.microsoft.windowsazure.services.core.ServiceException;
    import com.microsoft.windowsazure.services.core.storage.StorageException;
    import com.microsoft.windowsazure.services.media.*;
    import com.microsoft.windowsazure.services.media.models.*;

    public class HelloMediaServices 
    {

        private static MediaContract mediaService;
        private static AssetInfo assetToEncode, encodedAsset;

        public static void main(String[] args) 
        {
            try 
            {

                // Set up the MediaContract object to call into the media services.
                Init();
                
                // Upload a local file to a media asset.
                Upload();

                // Transform the asset.
                Transform();

                // Retrieve the URL of the asset's transformed output.
                Download();

                // Delete all assets. 
                // When you want to delete the assets that have been uploaded, 
                // comment out the calls to Upload(), Transfer(), and Download(), 
                // and uncomment the following call to Cleanup().
                //Cleanup();

                System.out.println("Application completed.");
            }
            catch (ServiceException se) 
            {
                System.out.println("ServiceException encountered.");
                System.out.println(se.getMessage());
            }
            catch (Exception e) 
            {
                System.out.println("Exception encountered.");
                System.out.println(e.getMessage());
            }
        }

        // Initialize the server context to get programmatic access to the Media Services programming objects.
        // The media services URI, OAuth URI and scope can be used exactly as shown.
        // Substitute your media service account name and access key for the clientId and clientSecret variables.
        // You can obtain your media service account name and access key from the Media Services section
        // of the Azure Management portal, https://manage.windowsazure.com.
        private static void Init() throws ServiceException 
        {
            String mediaServiceUri = "https://media.windows.net/API/";
            String oAuthUri = "https://wamsprodglobal001acs.accesscontrol.windows.net/v2/OAuth2-13";
            String clientId = "your_client_id";  // Use your media service account name.
            String clientSecret = "your_client_secret"; // Use your media service access key. 
            String scope = "urn:WindowsAzureMediaServices";

            // Specify the configuration values to use with the MediaContract object.
            Configuration configuration = MediaConfiguration
                    .configureWithOAuthAuthentication(mediaServiceUri, oAuthUri, clientId, clientSecret, scope);

            // Create the MediaContract object using the specified configuration.
            mediaService = MediaService.create(configuration);
            
        }

        // Upload a media file to your Media Services account.
        // This code creates an asset, an access policy (using Write access) and a locator, 
        // and uses those objects to upload a local file to the asset.
        private static void Upload() throws ServiceException, FileNotFoundException, NoSuchAlgorithmException 
        {
            
            WritableBlobContainerContract uploader;
            
            AccessPolicyInfo uploadAccessPolicy;
            LocatorInfo uploadLocator = null;
            
            // Create an asset.
            assetToEncode = mediaService.create(Asset.create().setName("myAsset").setAlternateId("altId"));
            System.out.println("Created asset with id: " + assetToEncode.getId());

            // Create an access policy that provides Write access for 15 minutes.
            uploadAccessPolicy = mediaService.create(AccessPolicy.create("uploadAccessPolicy", 
                                                                         15.0, 
                                                                         EnumSet.of(AccessPolicyPermission.WRITE)));
            System.out.println("Created upload access policy with id: "
                    + uploadAccessPolicy.getId());

            // Create a locator using the access policy and asset.
            // This will provide the location information needed to add files to the asset.
            uploadLocator = mediaService.create(Locator.create(uploadAccessPolicy.getId(),
                    assetToEncode.getId(), LocatorType.SAS));
            System.out.println("Created upload locator with id: " + uploadLocator.getId());

            // Create the blob writer using the locator.
            uploader = mediaService.createBlobWriter(uploadLocator);

            // The name of the file as it will exist in your Media Services account.
            String fileName = "MPEG4-H264.mp4";  

            // The local file that will be uploaded to your Media Services account.
            InputStream input = new FileInputStream(new File("c:/media/" + fileName));

            // Upload the local file to the asset.
            uploader.createBlockBlob(fileName, input);

            // Inform Media Services about the uploaded files.
            mediaService.action(AssetFile.createFileInfos(assetToEncode.getId()));
            System.out.println("File uploaded.");
            
           
            System.out.println("Deleting upload locator and access policy.");
            mediaService.delete(Locator.delete(uploadLocator.getId()));
            mediaService.delete(AccessPolicy.delete(uploadAccessPolicy.getId()));
            
        }

        // Create a job that contains a task to transform the asset.
        // In this example, the asset will be transformed using the Azure Media Encoder.
        private static void Transform() throws ServiceException, InterruptedException 
        {
            // Use the Azure Media Encoder, by specifying it by name.
            // Retrieve the list of media processors that match this name.      
            ListResult<MediaProcessorInfo> mediaProcessors = mediaService
                    .list(MediaProcessor.list()
                    .set("$filter", "Name eq 'Azure Media Encoder'"));
            
            // Use the latest version of the media processor.
            MediaProcessorInfo mediaProcessor = null;
            for (MediaProcessorInfo info : mediaProcessors)
            {
                if (null == mediaProcessor || info.getVersion().compareTo(mediaProcessor.getVersion()) > 0)
                {
                    mediaProcessor = info;
                }
            }

            System.out.println("Using processor: " + mediaProcessor.getName() +
                    " " + mediaProcessor.getVersion());

            // Create a task with the specified media processor, in this case to transform the original asset to the H264 Broadband 720p preset.
            // Information on the various configurations can be found at
            // http://msdn.microsoft.com/en-us/library/windowsazure/jj129582.aspx.
            // This example uses only one task, but others could be added.
            Task.CreateBatchOperation task = Task.create(
                    mediaProcessor.getId(),
                    "<taskBody><inputAsset>JobInputAsset(0)</inputAsset><outputAsset>JobOutputAsset(0)</outputAsset></taskBody>")
                    .setConfiguration("H264 Broadband 720p").setName("MyTask");

            // Create a job creator that specifies the asset, priority and task for the job. 
            Job.Creator jobCreator = Job.create()
                .setName("myJob")
                .addInputMediaAsset(assetToEncode.getId())
                .setPriority(2)
                .addTaskCreator(task);

            // Create the job within your Media Services account.
            // Creating the job automatically schedules and runs it.
            JobInfo jobInfo = mediaService.create(jobCreator);
            String jobId = jobInfo.getId();
            System.out.println("Created job with id: " + jobId);
            // Check to see if the job has completed.
            CheckJobStatus(jobId);
            // Done with the job.

            // Retrieve the output asset.
            ListResult<AssetInfo> outputAssets = mediaService.list(Asset.list(jobInfo.getOutputAssetsLink()));
            encodedAsset = outputAssets.get(0);
        }

        // Download the output assets of the transformed asset.
        private static void Download() throws ServiceException, URISyntaxException, FileNotFoundException, StorageException, IOException 
        {
            
            AccessPolicyInfo downloadAccessPolicy = null;

            downloadAccessPolicy =
                    mediaService.create(AccessPolicy.create("Download", 15.0, EnumSet.of(AccessPolicyPermission.READ)));
            System.out.println("Created download access policy with id: "
                    + downloadAccessPolicy.getId());
            
            LocatorInfo downloadLocator = null;
            downloadLocator = mediaService.create(
                    Locator.create(downloadAccessPolicy.getId(), encodedAsset.getId(), LocatorType.SAS));
            System.out.println("Created download locator with id: " + downloadLocator.getId());        

            System.out.println("Accessing the output files of the encoded asset.");
            // Iterate through the files associated with the encoded asset.
            for(AssetFileInfo assetFile: mediaService.list(AssetFile.list(encodedAsset.getAssetFilesLink())))
            {
                String fileName = assetFile.getName();
                
                System.out.print("Downloading file: " + fileName + ". ");
                String locatorPath = downloadLocator.getPath();
                int startOfSas = locatorPath.indexOf("?");

                String blobPath = locatorPath + fileName;
                if (startOfSas >= 0) 
                {
                    blobPath = locatorPath.substring(0, startOfSas) + "/" + fileName + locatorPath.substring(startOfSas);
                }
                URI baseuri = new URI(blobPath);
                CloudBlobClient blobClient;
                blobClient = new CloudBlobClient(baseuri);
                
                // Ensure that you have a c:\output folder, or modify the path specified in the following statement.
                String localFileName = "c:/output/" + fileName;
                
                CloudBlockBlob sasBlob;
                sasBlob = new CloudBlockBlob(baseuri, blobClient);
                File fileTarget = new File(localFileName);
                
                sasBlob.download(new FileOutputStream(fileTarget));
                System.out.println("Download complete.");
                
            }

            System.out.println("Deleting download locator and access policy.");
            mediaService.delete(Locator.delete(downloadLocator.getId()));
            mediaService.delete(AccessPolicy.delete(downloadAccessPolicy.getId()));
          
        }
        
        // Remove all assets from your Media Services account.
        // You could instead remove assets by name or ID, etc., but for 
        // simplicity this example removes all of them.
        private static void Cleanup() throws ServiceException 
        {
            // Retrieve a list of all assets.
            List<AssetInfo> assets = mediaService.list(Asset.list());

            // Iterate through the list, deleting each asset.
            for (AssetInfo asset: assets)
            {
                System.out.println("Deleting asset named " + asset.getName() + " (" + asset.getId() + ")");
                mediaService.delete(Asset.delete(asset.getId()));
            }
        }

        // Helper function to check to on the status of the job.
        private static void CheckJobStatus(String jobId) throws InterruptedException, ServiceException
        {
            int maxRetries = 12; // Number of times to retry. Small jobs often take 2 minutes.
            JobState jobState = null;
            while (maxRetries > 0) 
            {
                Thread.sleep(10000);  // Sleep for 10 seconds, or use another interval.
                // Determine the job state.
                jobState = mediaService.get(Job.get(jobId)).getState();
                System.out.println("Job state is " + jobState);

                if (jobState == JobState.Finished || 
                    jobState == JobState.Canceled || 
                    jobState == JobState.Error) 
                {
                    // The job is done.
                    break;
                }
                // The job is not done. Sleep and loop if max retries 
                // has not been reached.
                maxRetries--;
            }
      
        }

    }

Les éléments multimédias que vous créez sont stockés dans le stockage Azure. Toutefois, utilisez uniquement les API Azure Media Services (pas celles du stockage Azure) pour ajouter, mettre à jour ou supprimer des éléments multimédias.

### Détermination des processeurs multimédias disponibles

Le code ci-dessus a utilisé un processeur multimédia en y accédant via un nom de processeur multimédia spécifique (en présence de différentes versions, la plus récente est utilisée). Pour déterminer quels processeurs multimédias sont disponibles, utilisez le code suivant.

    for (MediaProcessorInfo mediaProcessor:  mediaService.list(MediaProcessor.list()))
    {
        System.out.print(mediaProcessor.getName() + ", ");
        System.out.print(mediaProcessor.getId() + ", ");  
        System.out.print(mediaProcessor.getVendor() + ", ");  
        System.out.println(mediaProcessor.getVersion());  
    }

Sinon, le code suivant montre comment extraire l'ID d'un processeur multimédia par son nom.

    String mediaProcessorName = "Storage Decryption"; 
    EntityListOperation<MediaProcessorInfo> operation;
    MediaProcessorInfo processor;

    operation = MediaProcessor.list();
    operation.getQueryParameters().putSingle("$filter", "Name eq '" + mediaProcessorName + "'");
    processor = mediaService.list(operation).get(0); 
    System.out.println("Processor named " + mediaProcessorName + 
                       " has ID of " + processor.getId());

### Annulation d'une tâche

Si vous devez annuler une tâche dont le traitement n'est pas terminé, le code suivant montre comment annuler une tâche sélectionnée par son ID.

    mediaService.action(Job.cancel(jobId));

## <a name="additional-resources"></a><span class="short header">Ressources supplémentaires</span>Ressources supplémentaires

Pour obtenir de la documentation Media Services Javadoc, consultez la rubrique [Bibliothèques Azure contenant de la documentation Java][].

  [Présentation de Media Services]: #what-are
  [Configuration d'un compte Azure pour Media Services]: #setup-account
  [Configuration pour le développement Media Services]: #setup-dev
  [Utilisation de Media Services avec Java]: #connect
  [Ressources supplémentaires]: #additional-resources
  [Architecture Media Services]: ./media/media-services-dotnet-how-to-use/wams-01.png
  [Développement de clients Media Services]: http://msdn.microsoft.com/en-us/library/windowsazure/dn223283.aspx
  [Client de diffusion en continu lisse pour Silverlight]: http://www.iis.net/download/smoothclient
  [Plateforme multimédia Microsoft : Infrastructure de lecture pour Silverlight]: http://smf.codeplex.com/documentation
  [Plug-in Smooth Streaming pour OSMF 2.0]: http://go.microsoft.com/fwlink/?LinkId=275022
  [Utilisation du plug-in Smooth Streaming pour Adobe Open Source Media Framework]: http://go.microsoft.com/fwlink/?LinkId=275034
  [Kits de développement logiciel (SDK) pour clients de diffusion en continu lisse Windows 8]: http://go.microsoft.com/fwlink/?LinkID=246146
  [Création d'une application de diffusion en continu lisse Windows Store]: http://go.microsoft.com/fwlink/?LinkId=271647
  [Procédure pas à pas : génération de votre premier lecteur de diffusion en continu lisse HTML5]: http://msdn.microsoft.com/en-us/library/jj573656.aspx
  [Plateforme multimédia Microsoft : Infrastructure de lecture pour applications Windows Store Windows 8]: http://playerframework.codeplex.com/wikipage?title=Player%20Framework%20for%20Windows%208%20Metro%20Style%20Apps&referringTitle=Home
  [Kit de portage de client de diffusion en continu lisse]: http://www.microsoft.com/en-us/mediaplatform/sspk.aspx
  [Kit de portage d'appareil Microsoft PlayReady]: http://www.microsoft.com/PlayReady/Licensing/device_technology.mspx
  [courrier électronique à Microsoft]: mailto:askdrm@microsoft.com
  [Centre de développement iOS]: https://developer.apple.com/devcenter/ios/index.action
  [1]: mailto:sspkinfo@microsoft.com?subject=Partner%20SDKs%20for%20Android%20Devices
  [Création d'un compte Media Services]: http://go.microsoft.com/fwlink/?linkid=256662
  [Centre de développement Java pour Azure]: http://www.windowsazure.com/en-us/develop/java/
  [2]: http://www.windowsazure.com/en-us/manage/services/media-services/how-to-create-a-media-services-account/
  [Bibliothèques Azure contenant de la documentation Java]: http://dl.windowsazure.com/javadoc/
