<properties 
	pageTitle="Utiliser Azure Media Services pour diffuser en continu votre contenu HLS protégé avec Apple FairPlay" 
	description="Cette rubrique fournit une vue d’ensemble et montre comment utiliser Azure Media Services pour chiffrer dynamiquement votre contenu HTTP Live Streaming (HLS) avec Apple FairPlay. Elle montre également comment utiliser le service de distribution de licences Media Services pour fournir des licences FairPlay aux clients." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="erikre" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="06/22/2016"
	ms.author="juliako"/>

#Utiliser Azure Media Services pour diffuser en continu votre contenu HLS protégé avec Apple FairPlay 

Azure Media Services vous permet de chiffrer dynamiquement votre contenu HTTP Live Streaming (HLS) avec les formats suivants :

- **Clé en clair de l’enveloppe AES-128** : le segment entier est chiffré à l’aide du mode **AES-128 CBC**. Le déchiffrement du flux est pris en charge par iOS et le lecteur OSX en mode natif. Pour plus d’informations, consultez [cet article](media-services-protect-with-aes128.md).

- **Apple FairPlay** - Les échantillons audio et vidéo individuels sont chiffrés à l'aide du mode **AES-128 CBC**. **FairPlay Streaming** (FPS) est intégré dans les systèmes d'exploitation de l’appareil, avec prise en charge native sur iOS et Apple TV. Safari sur OS X active FPS à l'aide de la prise en charge d'interface Encrypted Media Extensions (EME).

	>[AZURE.NOTE]
	L’utilisation d’AMS pour fournir du contenu HLS chiffré avec FairPlay est actuellement en version préliminaire.


L'image suivante montre le flux de travail « Chiffrement dynamique FairPlay ».

![Protéger avec FairPlay](./media/media-services-content-protection-overview/media-services-content-protection-with-fairplay.png)

Cette rubrique montre comment utiliser Azure Media Services pour chiffrer dynamiquement votre contenu HLS avec Apple FairPlay. Elle montre également comment utiliser le service de distribution de licences Media Services pour fournir des licences FairPlay aux clients.

	
## Conditions requises et éléments à prendre en compte

- Les éléments suivants sont nécessaires pour utiliser AMS afin de fournir du contenu HLS chiffré avec FairPlay et des licences FairPlay.

	- Un compte Azure. Pour plus d'informations, consultez la page [Version d'évaluation gratuite d'Azure](/pricing/free-trial/?WT.mc_id=A261C142F).
	- Un compte Media Services. Pour créer un compte Media Services, consultez [Créer un compte](media-services-create-account.md).
	- S’inscrire au programme [Apple Developer Program](https://developer.apple.com/).
	- Apple exige que le propriétaire du contenu se procure le [package de déploiement](https://developer.apple.com/contact/fps/). Dans votre demande, indiquez que vous déjà implémenté le module de sécurité des clés (KSM) et que vous avez besoin du package FPS final. Le package FPS final contiendra les instructions à suivre pour générer la certification et obtenir la clé secrète de l’application (ASK), qui vous servira à configurer FairPlay.

	- Kit de développement logiciel (SDK) .NET Azure Media Services version **3.6.0** ou ultérieure.

- Les éléments suivants doivent être définis du côté de la remise de clé AMS :
	- **App Cert (AC)** : fichier .pfx qui contient la clé privée. Ce fichier est créé par le client et chiffré avec un mot de passe par le même client.
		
	 	Lorsque le client configure une stratégie de remise de clé, il doit fournir ce mot de passe et le fichier .pfx au format base64.

		Les étapes suivantes décrivent comment générer un certificat pfx pour FairPlay.
		
		1. Installation d’OpenSSL depuis https://slproweb.com/products/Win32OpenSSL.html
		
			Accédez au dossier dans lequel se situent le certificat FairPlay et les autres fichiers fournis par Apple.
		
		2. Ligne de commande pour convertir cer en pem :
		
			"C:\\OpenSSL-Win32\\bin\\openssl.exe" x509 -inform der -in fairplay.cer -out fairplay-out.pem
		
		3. Ligne de commande pour convertir pem en pfx avec la clé privée (le mot de passe du fichier pfx est ensuite demandé par OpenSSL).
		
			"C:\\OpenSSL-Win32\\bin\\openssl.exe" pkcs12 -export -out fairplay-out.pfx -inkey privatekey.pem -in fairplay-out.pem -passin file:privatekey-pem-pass.txt
		
	- **Mot de passe App Cert** : mot de passe du client pour créer le fichier .pfx.
	- **ID de mot de passe App Cert** : le client doit télécharger le mot de passe de la même manière que les autres clés AMS, avec la valeur enum **ContentKeyType.FairPlayPfxPassword**. Dans le résultat, il obtiendra l’ID AMS qu'il doit utiliser dans l'option de stratégie de remise de clé.
	- **iv** : valeur aléatoire de 16 octets, doit correspondre au vecteur d’initialisation de la stratégie de distribution de ressources. Le client génère le vecteur d'initialisation et le place aux deux endroits : la stratégie de distribution de ressources et l’option de stratégie de remise de clé.
	- **ASK** : l’ASK (clé secrète de l’application) est reçue lorsque vous générez la certification à l’aide du portail des développeurs Apple. Chaque équipe de développement recevra une ASK unique. Enregistrez une copie de l’ASK et stockez-la dans un endroit sûr. Vous devrez configurer l’ASK comme FairPlayAsk pour Azure Media Services plus tard.
	-  **ID d’ASK** : obtenu lorsque le client télécharge l’ASK dans AMS. Le client doit télécharger l’ASK avec la valeur enum **ContentKeyType.FairPlayASk**. L’ID AMS sera retourné dans le résultat. C’est cet ID qui doit être utilisé lors de la définition de l’option de stratégie de remise de clé.

- Les éléments suivants doivent être définis par FPS côté client :
 	- **App Cert (AC)** : fichier .cer/.der contenant la clé publique que le système d’exploitation utilise pour chiffrer une charge utile. AMS doit le connaître, car il est requis par le lecteur. Le service de remise de clé le déchiffre à l'aide de la clé privée correspondante.

- Pour lire un flux chiffré FairPlay, vous devez obtenir l’ASK réelle en premier, puis générer un certificat réel. Ce processus créera les 3 parties :

	-  .der,
	-  .pfx et
	-  le mot de passe du fichier .pfx.
 
- Clients qui prennent en charge HLS avec chiffrement **AES-128 CBC** : Safari sur OS X, Apple TV, iOS.

##Étapes de configuration du chiffrement dynamique FairPlay et des services de distribution de licences

Voici les étapes générales que vous aurez à exécuter lors de la protection de vos ressources avec FairPlay, en utilisant le service de distribution des licences Media Services, ainsi que le chiffrement dynamique.

1. Créer un élément multimédia et télécharger des fichiers dans l'élément multimédia.
1. Encoder l'élément multimédia contenant le fichier selon le débit binaire MP4 défini.
1. Créer une clé de contenu et l'associer à l'élément multimédia encodé.
1. Configurer la stratégie d'autorisation de la clé de contenu. Lorsque vous créez la stratégie d'autorisation de la clé de contenu, vous devez spécifier les éléments suivants :
	
	- la méthode de remise (dans ce cas, FairPlay),
	- la configuration des options de stratégie FairPlay. Pour plus d’informations sur la configuration de FairPlay, consultez la méthode ConfigureFairPlayPolicyOptions() dans l’exemple ci-dessous.
	
		>[AZURE.NOTE] Dans la plupart des cas, vous n’aurez à configurer les options de stratégie FairPlay qu’une seule fois, étant donné que vous n’aurez qu’un seul jeu de certification et d’ASK.
	- les restrictions (ouvert ou jeton),
	- et les informations propres au type de remise de clé qui définit la façon dont la clé est envoyée au client.
	
2. Configurez la stratégie de distribution de ressources. La configuration de la stratégie de distribution inclut :

	- le protocole de distribution (HLS),
	- le type de chiffrement dynamique (Common CBC Encryption),
	- l’URL d’acquisition de licence.
	
	>[AZURE.NOTE]Si vous souhaitez fournir un flux chiffré avec FairPlay + un autre système de DRM, vous devez configurer des stratégies de remise distinctes
	>
	>- Une stratégie IAssetDeliveryPolicy pour configurer DASH avec CENC (PlayReady + WideVine) et Smooth avec PlayReady.
	>- Une autre stratégie IAssetDeliveryPolicy pour configurer FairPlay pour HLS

1. Créer un localisateur à la demande afin d'obtenir une URL de diffusion en continu.

##Utilisation de la distribution de clés FairPlay par les applications clientes et les lecteurs

Les clients peuvent développer des lecteurs à l’aide du SDK iOS. Pour pouvoir lire le contenu FairPlay, les clients doivent implémenter le protocole d’échange de licence. Le protocole d’échange de licence n’est pas spécifié par Apple. Chaque application doit décider comment envoyer les requêtes de distribution de clés. Les services de distribution de clés AMS FairPlay s’attendent à ce que le certificat SPC soit fourni en tant que message encodé www-form-url au format suivant :

	spc=<Base64 encoded SPC>

>[AZURE.NOTE] Azure Media Player ne prend pas en charge la lecture FairPlay dès le départ. Les clients doivent se procurer l’exemple de lecteur à partir du compte de développeur Apple pour mettre en œuvre la lecture FairPlay sur MAC OS X.
 

##Exemple .NET


L'exemple suivant illustre la fonctionnalité introduite dans le Kit de développement logiciel (SDK) Azure Media Services pour .NET version 3.6.0 (la possibilité d'utiliser Azure Media Services pour fournir votre contenu chiffré avec FairPlay). La commande de package Nuget suivante a été utilisée pour installer le package :

	PM> Install-Package windowsazure.mediaservices -Version 3.6.0


1. Créez un projet Console.
1. Utilisez NuGet pour installer et ajouter les extensions du kit de développement logiciel .NET Azure Media Services.
2. Ajouter des références : System.Configuration.
2. Ajoutez le fichier de configuration qui contient le nom du compte et les informations sur la clé :
	
		<?xml version="1.0" encoding="utf-8"?>
		<configuration>
		    <startup> 
		        <supportedRuntime version="v4.0" sku=".NETFramework,Version=v4.5" />
		    </startup>
			  <appSettings>
			
			    <add key="MediaServicesAccountName" value="AccountName"/>
			    <add key="MediaServicesAccountKey" value="AccountKey"/>
			
			    <add key="Issuer" value="http://testacs.com"/>
			    <add key="Audience" value="urn:test"/>
			  </appSettings>
		</configuration>

1. Obtenir au moins une unité de diffusion pour le point de terminaison de diffusion à partir duquel vous envisagez de distribuer votre contenu. Pour plus d’informations, consultez [configurer les points de terminaison de diffusion en continu](media-services-dotnet-get-started.md#configure-streaming-endpoint-using-the-portal).

1. Remplacez le code dans votre fichier Program.cs par le code présenté dans cette section.
			
		
		using System;
		using System.Collections.Generic;
		using System.Configuration;
		using System.IO;
		using System.Linq;
		using System.Threading;
		using Microsoft.WindowsAzure.MediaServices.Client;
		using Microsoft.WindowsAzure.MediaServices.Client.ContentKeyAuthorization;
		using Microsoft.WindowsAzure.MediaServices.Client.DynamicEncryption;
		using Microsoft.WindowsAzure.MediaServices.Client.FairPlay;
		using Newtonsoft.Json;
		using System.Security.Cryptography.X509Certificates;
		
		namespace DynamicEncryptionWithFairPlay
		{
		    class Program
		    {
		        // Read values from the App.config file.
		        private static readonly string _mediaServicesAccountName =
		            ConfigurationManager.AppSettings["MediaServicesAccountName"];
		        private static readonly string _mediaServicesAccountKey =
		            ConfigurationManager.AppSettings["MediaServicesAccountKey"];
		
		        private static readonly Uri _sampleIssuer =
		            new Uri(ConfigurationManager.AppSettings["Issuer"]);
		        private static readonly Uri _sampleAudience =
		            new Uri(ConfigurationManager.AppSettings["Audience"]);
		
		        // Field for service context.
		        private static CloudMediaContext _context = null;
		        private static MediaServicesCredentials _cachedCredentials = null;
		
		        private static readonly string _mediaFiles =
		            Path.GetFullPath(@"../..\Media");
		
		        private static readonly string _singleMP4File =
		            Path.Combine(_mediaFiles, @"BigBuckBunny.mp4");
		
		        static void Main(string[] args)
		        {
		            // Create and cache the Media Services credentials in a static class variable.
		            _cachedCredentials = new MediaServicesCredentials(
		                            _mediaServicesAccountName,
		                            _mediaServicesAccountKey);
		            // Used the cached credentials to create CloudMediaContext.
		            _context = new CloudMediaContext(_cachedCredentials);
		
		            bool tokenRestriction = false;
		            string tokenTemplateString = null;
		
		            IAsset asset = UploadFileAndCreateAsset(_singleMP4File);
		            Console.WriteLine("Uploaded asset: {0}", asset.Id);
		
		            IAsset encodedAsset = EncodeToAdaptiveBitrateMP4Set(asset);
		            Console.WriteLine("Encoded asset: {0}", encodedAsset.Id);
		
		            IContentKey key = CreateCommonCBCTypeContentKey(encodedAsset);
		            Console.WriteLine("Created key {0} for the asset {1} ", key.Id, encodedAsset.Id);
		            Console.WriteLine("FairPlay License Key delivery URL: {0}", key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay));
		            Console.WriteLine();
		
		            if (tokenRestriction)
		                tokenTemplateString = AddTokenRestrictedAuthorizationPolicy(key);
		            else
		                AddOpenAuthorizationPolicy(key);
		
		            Console.WriteLine("Added authorization policy: {0}", key.AuthorizationPolicyId);
		            Console.WriteLine();
		
		            CreateAssetDeliveryPolicy(encodedAsset, key);
		            Console.WriteLine("Created asset delivery policy. \n");
		            Console.WriteLine();
		
		            if (tokenRestriction && !String.IsNullOrEmpty(tokenTemplateString))
		            {
		                // Deserializes a string containing an Xml representation of a TokenRestrictionTemplate
		                // back into a TokenRestrictionTemplate class instance.
		                TokenRestrictionTemplate tokenTemplate =
		                    TokenRestrictionTemplateSerializer.Deserialize(tokenTemplateString);
		
		                // Generate a test token based on the the data in the given TokenRestrictionTemplate.
		                // Note, you need to pass the key id Guid because we specified 
		                // TokenClaim.ContentKeyIdentifierClaim in during the creation of TokenRestrictionTemplate.
		                Guid rawkey = EncryptionUtils.GetKeyIdAsGuid(key.Id);
		                string testToken = TokenRestrictionTemplateSerializer.GenerateTestToken(tokenTemplate, null, rawkey,
		                                                                        DateTime.UtcNow.AddDays(365));
		                Console.WriteLine("The authorization token is:\nBearer {0}", testToken);
		                Console.WriteLine();
		            }
		
		            string url = GetStreamingOriginLocator(encodedAsset);
		            Console.WriteLine("Encrypted HLS URL: {0}/manifest(format=m3u8-aapl)", url);
		
		            Console.ReadLine();
		        }
		
		        static public IAsset UploadFileAndCreateAsset(string singleFilePath)
		        {
		            if (!File.Exists(singleFilePath))
		            {
		                Console.WriteLine("File does not exist.");
		                return null;
		            }
		
		            var assetName = Path.GetFileNameWithoutExtension(singleFilePath);
		            IAsset inputAsset = _context.Assets.Create(assetName, AssetCreationOptions.None);
		
		            var assetFile = inputAsset.AssetFiles.Create(Path.GetFileName(singleFilePath));
		
		            Console.WriteLine("Created assetFile {0}", assetFile.Name);
		
		            var policy = _context.AccessPolicies.Create(
		                                    assetName,
		                                    TimeSpan.FromDays(30),
		                                    AccessPermissions.Write | AccessPermissions.List);
		
		            var locator = _context.Locators.CreateLocator(LocatorType.Sas, inputAsset, policy);
		
		            Console.WriteLine("Upload {0}", assetFile.Name);
		
		            assetFile.Upload(singleFilePath);
		            Console.WriteLine("Done uploading {0}", assetFile.Name);
		
		            locator.Delete();
		            policy.Delete();
		
		            return inputAsset;
		        }
		
		        static public IAsset EncodeToAdaptiveBitrateMP4Set(IAsset inputAsset)
		        {
		            var encodingPreset = "H264 Multiple Bitrate 720p";
		
		            IJob job = _context.Jobs.Create(String.Format("Encoding into Mp4 {0} to {1}",
		                                    inputAsset.Name,
		                                    encodingPreset));
		
		            var mediaProcessors =
		                _context.MediaProcessors.Where(p => p.Name.Contains("Media Encoder Standard")).ToList();
		
		            var latestMediaProcessor =
		                mediaProcessors.OrderBy(mp => new Version(mp.Version)).LastOrDefault();
		
		            ITask encodeTask = job.Tasks.AddNew("Encoding", latestMediaProcessor, encodingPreset, TaskOptions.None);
		            encodeTask.InputAssets.Add(inputAsset);
		            encodeTask.OutputAssets.AddNew(String.Format("{0} as {1}", inputAsset.Name, encodingPreset), 	AssetCreationOptions.StorageEncrypted);
		
		            job.StateChanged += new EventHandler<JobStateChangedEventArgs>(JobStateChanged);
		            job.Submit();
		            job.GetExecutionProgressTask(CancellationToken.None).Wait();
		
		            return job.OutputMediaAssets[0];
		        }
		
		        static public IContentKey CreateCommonCBCTypeContentKey(IAsset asset)
		        {
		            // Create HLS SAMPLE AES encryption content key
		            Guid keyId = Guid.NewGuid();
		            byte[] contentKey = GetRandomBuffer(16);
		
		            IContentKey key = _context.ContentKeys.Create(
		                                    keyId,
		                                    contentKey,
		                                    "ContentKey",
		                                    ContentKeyType.CommonEncryptionCbcs);
		
		            // Associate the key with the asset.
		            asset.ContentKeys.Add(key);
		
		            return key;
		        }
		
		
		        static public void AddOpenAuthorizationPolicy(IContentKey contentKey)
		        {
		            // Create ContentKeyAuthorizationPolicy with Open restrictions 
		            // and create authorization policy          
		
		            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
		                    {
		                        new ContentKeyAuthorizationPolicyRestriction
		                        {
		                            Name = "Open",
		                            KeyRestrictionType = (int)ContentKeyRestrictionType.Open,
		                            Requirements = null
		                        }
		                    };
		
		
		            // Configure FairPlay policy option.
		            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
		
		            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
		                _context.ContentKeyAuthorizationPolicyOptions.Create("",
		                ContentKeyDeliveryType.FairPlay,
		                restrictions,
		                FairPlayConfiguration);
		
		
		            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
		                        ContentKeyAuthorizationPolicies.
		                        CreateAsync("Deliver Common CBC Content Key with no restrictions").
		                        Result;
		
		            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
		
		            // Associate the content key authorization policy with the content key.
		            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
		            contentKey = contentKey.UpdateAsync().Result;
		        }
		
		        public static string AddTokenRestrictedAuthorizationPolicy(IContentKey contentKey)
		        {
		            string tokenTemplateString = GenerateTokenRequirements();
		
		            List<ContentKeyAuthorizationPolicyRestriction> restrictions = new List<ContentKeyAuthorizationPolicyRestriction>
		                    {
		                        new ContentKeyAuthorizationPolicyRestriction
		                        {
		                            Name = "Token Authorization Policy",
		                            KeyRestrictionType = (int)ContentKeyRestrictionType.TokenRestricted,
		                            Requirements = tokenTemplateString,
		                        }
		                    };
		
		            // Configure FairPlay policy option.
		            string FairPlayConfiguration = ConfigureFairPlayPolicyOptions();
		
		
		            IContentKeyAuthorizationPolicyOption FairPlayPolicy =
		                _context.ContentKeyAuthorizationPolicyOptions.Create("Token option",
		                       ContentKeyDeliveryType.FairPlay,
		                       restrictions,
		                       FairPlayConfiguration);
		
		            IContentKeyAuthorizationPolicy contentKeyAuthorizationPolicy = _context.
		                        ContentKeyAuthorizationPolicies.
		                        CreateAsync("Deliver Common CBC Content Key with token restrictions").
		                        Result;
		
		            contentKeyAuthorizationPolicy.Options.Add(FairPlayPolicy);
		
		            // Associate the content key authorization policy with the content key
		            contentKey.AuthorizationPolicyId = contentKeyAuthorizationPolicy.Id;
		            contentKey = contentKey.UpdateAsync().Result;
		
		            return tokenTemplateString;
		        }
		
		        private static string ConfigureFairPlayPolicyOptions()
		        {
		            // For testing you can provide all zeroes for ASK bytes together with the cert from Apple FPS SDK. 
		            // However, for production you must use a real ASK from Apple bound to a real prod certificate.
		            byte[] askBytes = Guid.NewGuid().ToByteArray();
		            var askId = Guid.NewGuid();
		            // Key delivery retrieves askKey by askId and uses this key to generate the response.
		            IContentKey askKey = _context.ContentKeys.Create(
		                                    askId,
		                                    askBytes,
		                                    "askKey",
		                                    ContentKeyType.FairPlayASk);
		
		            //Customer password for creating the .pfx file.
		            string pfxPassword = "<customer password for creating the .pfx file>";
		            // Key delivery retrieves pfxPasswordKey by pfxPasswordId and uses this key to generate the response.
		            var pfxPasswordId = Guid.NewGuid();
		            byte[] pfxPasswordBytes = System.Text.Encoding.UTF8.GetBytes(pfxPassword);
		            IContentKey pfxPasswordKey = _context.ContentKeys.Create(
		                                    pfxPasswordId,
		                                    pfxPasswordBytes,
		                                    "pfxPasswordKey",
		                                    ContentKeyType.FairPlayPfxPassword);
		
		            // iv - 16 bytes random value, must match the iv in the asset delivery policy.
		            byte[] iv = Guid.NewGuid().ToByteArray();
		
		            //Specify the .pfx file created by the customer.
		            var appCert = new X509Certificate2("path to the .pfx file created by the customer", pfxPassword, X509KeyStorageFlags.Exportable);
		
		            string FairPlayConfiguration =
		                Microsoft.WindowsAzure.MediaServices.Client.FairPlay.FairPlayConfiguration.CreateSerializedFairPlayOptionConfiguration(
		                    appCert,
		                    pfxPassword,
		                    pfxPasswordId,
		                    askId,
		                    iv);
		
		            return FairPlayConfiguration;
		        }
		
		        static private string GenerateTokenRequirements()
		        {
		            TokenRestrictionTemplate template = new TokenRestrictionTemplate(TokenType.SWT);
		
		            template.PrimaryVerificationKey = new SymmetricVerificationKey();
		            template.AlternateVerificationKeys.Add(new SymmetricVerificationKey());
		            template.Audience = _sampleAudience.ToString();
		            template.Issuer = _sampleIssuer.ToString();
		            template.RequiredClaims.Add(TokenClaim.ContentKeyIdentifierClaim);
		
		            return TokenRestrictionTemplateSerializer.Serialize(template);
		        }
		
		        static public void CreateAssetDeliveryPolicy(IAsset asset, IContentKey key)
		        {
		            var kdPolicy = _context.ContentKeyAuthorizationPolicies.Where(p => p.Id == key.AuthorizationPolicyId).Single();
		
		            var kdOption = kdPolicy.Options.Single(o => o.KeyDeliveryType == ContentKeyDeliveryType.FairPlay);
		
		            FairPlayConfiguration configFP = JsonConvert.DeserializeObject<FairPlayConfiguration>(kdOption.KeyDeliveryConfiguration);
		
		            // Get the FairPlay license service URL.
		            Uri acquisitionUrl = key.GetKeyDeliveryUrl(ContentKeyDeliveryType.FairPlay);
		
					// The reason the below code replaces "https://" with "skd://" is because
					// in the IOS player sample code which you obtained in Apple developer account, 
					// the player only recognizes a Key URL that starts with skd://. 
					// However, if you are using a customized player, 
					// you can choose whatever protocol you want. 
					// For example, "https". 

		            Dictionary<AssetDeliveryPolicyConfigurationKey, string> assetDeliveryPolicyConfiguration =
		                new Dictionary<AssetDeliveryPolicyConfigurationKey, string>
		                {
		                    {AssetDeliveryPolicyConfigurationKey.FairPlayLicenseAcquisitionUrl, acquisitionUrl.ToString().Replace("https://", "skd://")},
		                    {AssetDeliveryPolicyConfigurationKey.CommonEncryptionIVForCbcs, configFP.ContentEncryptionIV}
		                };
		
		            var assetDeliveryPolicy = _context.AssetDeliveryPolicies.Create(
		                    "AssetDeliveryPolicy",
		                AssetDeliveryPolicyType.DynamicCommonEncryptionCbcs,
		                AssetDeliveryProtocol.HLS,
		                assetDeliveryPolicyConfiguration);
		
		            // Add AssetDelivery Policy to the asset
		            asset.DeliveryPolicies.Add(assetDeliveryPolicy);
		
		        }
		
		
		        /// <summary>
		        /// Gets the streaming origin locator.
		        /// </summary>
		        /// <param name="assets"></param>
		        /// <returns></returns>
		        static public string GetStreamingOriginLocator(IAsset asset)
		        {
		
		            // Get a reference to the streaming manifest file from the  
		            // collection of files in the asset. 
		
		            var assetFile = asset.AssetFiles.Where(f => f.Name.ToLower().
		                                         EndsWith(".ism")).
		                                         FirstOrDefault();
		
		            // Create a 30-day readonly access policy. 
		            IAccessPolicy policy = _context.AccessPolicies.Create("Streaming policy",
		                TimeSpan.FromDays(30),
		                AccessPermissions.Read);
		
		            // Create a locator to the streaming content on an origin. 
		            ILocator originLocator = _context.Locators.CreateLocator(LocatorType.OnDemandOrigin, asset,
		                policy,
		                DateTime.UtcNow.AddMinutes(-5));
		
		            // Create a URL to the manifest file. 
		            return originLocator.Path + assetFile.Name;
		        }
		
		        static private void JobStateChanged(object sender, JobStateChangedEventArgs e)
		        {
		            Console.WriteLine(string.Format("{0}\n  State: {1}\n  Time: {2}\n\n",
		                ((IJob)sender).Name,
		                e.CurrentState,
		                DateTime.UtcNow.ToString(@"yyyy_M_d__hh_mm_ss")));
		        }
		
		        static private byte[] GetRandomBuffer(int length)
		        {
		            var returnValue = new byte[length];
		
		            using (var rng =
		                new System.Security.Cryptography.RNGCryptoServiceProvider())
		            {
		                rng.GetBytes(returnValue);
		            }
		
		            return returnValue;
		        }
		    }
		}


##Étapes suivantes : Parcours d’apprentissage Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

<!---HONumber=AcomDC_0629_2016-->