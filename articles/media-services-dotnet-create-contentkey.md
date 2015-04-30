<properties 
	pageTitle="Création de ContentKeys avec .NET" 
	description="Apprenez à créer des clés de contenu qui fournissent un accès sécurisé aux ressources." 
	services="media-services" 
	documentationCenter="" 
	authors="juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/15/2015" 
	ms.author="juliako"/>


# Création de ContentKeys avec .NET

Cet article fait partie des séries [workflow de vidéo à la demande Media Services](media-services-video-on-demand-workflow.md) et [workflow de vidéo en flux continu Media Services](media-services-live-streaming-workflow.md).  

Media Services vous permet de créer et de fournir des ressources chiffrées. Une **ContentKey** fournit un accès sécurisé à vos **Ressources**. 

Lorsque vous créez une nouvelle ressource (par exemple, avant de [télécharger des fichiers](media-services-dotnet-upload-files.md)), vous pouvez spécifier les options de chiffrement suivantes : **StorageEncrypted**, **CommonEncryptionProtected** ou **EnvelopeEncryptionProtected**. 

Lors de la fourniture des ressources à vos clients, vous pouvez [configurer les ressources devant être chiffrées dynamiquement](media-services-dotnet-configure-asset-delivery-policy.md) avec un des deux chiffrements suivants : **DynamicEnvelopeEncryption** ou **DynamicCommonEncryption**.

Les ressources chiffrées doivent être associées à des **ContentKey**. Cet article décrit comment créer une clé de contenu.

>[AZURE.NOTE] Lorsque vous créez un élément multimédia **StorageEncrypted** à l'aide du Kit de développement logiciel (SDK) Media Services pour .NET, les **ContentKey** sont automatiquement créées et liées à l'élément multimédia.

## ContentKeyType

Une des valeurs que vous devez définir lors de la création d'une clé de contenu est le type de clé de contenu. Choisissez une des valeurs suivantes. 

    /// <summary>
    /// Specifies the type of a content key.
    /// </summary>
    public enum ContentKeyType
    {
        /// <summary>
        /// Specifies a content key for common encryption.
        /// </summary>
        /// <remarks>This is the default value.</remarks>
        CommonEncryption = 0,

        /// <summary>
        /// Specifies a content key for storage encryption.
        /// </summary>
        StorageEncryption = 1,

        /// <summary>
        /// Specifies a content key for encrypting encoding configuration data that may contain sensitive preset information. 
        /// </summary>
        ConfigurationEncryption = 2,

        /// <summary>
        /// Specifies a content key for url encryption.  Only used internally.
        /// </summary>
        UrlEncryption = 3,

        /// <summary>
        /// Specifies a content key for Envelope encryption.  Only used internally.
        /// </summary>
        EnvelopeEncryption = 4
    }

## <a id="envelope_contentkey"></a>Créer une ContentKey de type enveloppe

L'extrait de code suivant crée une clé de contenu du type de chiffrement d'enveloppe. Il associe ensuite la clé à l'élément multimédia spécifié.

    static public IContentKey CreateEnvelopeTypeContentKey(IAsset asset)
    {
        // Create envelope encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.EnvelopeEncryption);

        asset.ContentKeys.Add(key);

        return key;
    }

    static private byte[] GetRandomBuffer(int size)
    {
        byte[] randomBytes = new byte[size];
        using (RNGCryptoServiceProvider rng = new RNGCryptoServiceProvider())
        {
            rng.GetBytes(randomBytes);
        }

        return randomBytes;
    }

call

	IContentKey key = CreateEnvelopeTypeContentKey(encryptedsset);



## <a id="common_contentkey"></a>Créer une ContentKey de type commun    

L'extrait de code suivant crée une clé de contenu du type de chiffrement commun. Il associe ensuite la clé à l'élément multimédia spécifié.

    static public IContentKey CreateCommonTypeContentKey(IAsset asset)
    {
        // Create common encryption content key
        Guid keyId = Guid.NewGuid();
        byte[] contentKey = GetRandomBuffer(16);

        IContentKey key = _context.ContentKeys.Create(
                                keyId,
                                contentKey,
                                "ContentKey",
                                ContentKeyType.CommonEncryption);

        // Associate the key with the asset.
        asset.ContentKeys.Add(key);

        return key;
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
call

	IContentKey key = CreateCommonTypeContentKey(encryptedsset);

<!--HONumber=52-->