<properties 
	pageTitle="Configuration de stratégies de remise de ressources à l'aide de REST" 
	description="Cette rubrique montre comment configurer différentes stratégies de remise de ressources." 
	services="media-services" 
	documentationCenter="" 
	authors="Juliako" 
	manager="dwrede" 
	editor=""/>

<tags 
	ms.service="media-services" 
	ms.workload="media" 
	ms.tgt_pltfrm="na" 
	ms.devlang="na" 
	ms.topic="article" 
	ms.date="02/06/2015" 
	ms.author="juliako"/>

# Procédure : Configuration de stratégies de remise de ressources
[AZURE.INCLUDE [media-services-selector-asset-delivery-policy](../includes/media-services-selector-asset-delivery-policy.md)]

Cet article fait partie des séries [workflow à la demande de vidéo Media Services](media-services-video-on-demand-workflow.md) et [workflow de vidéo en flux continu Media Services](media-services-live-streaming-workflow.md). 

L'une des étapes du workflow de remise de contenu Media Services consiste à configurer les stratégies de remise pour les ressources que vous souhaitez diffuser en continu. La stratégie de remise de ressources indique à Media Services comment vous souhaitez distribuer vos ressources : dans quel protocole de diffusion en continu votre ressource doit être empaquetée dynamiquement (par exemple, MPEG DASH, HLS, diffusion en continu lisse ou tous), si vous souhaitez chiffrer dynamiquement votre ressource ou non et comment (chiffrement commun ou d'enveloppe). 

Cette rubrique explique pourquoi et comment créer et configurer des stratégies de remise de ressources. 

>[AZURE.NOTE]Pour pouvoir utiliser l'empaquetage et le chiffrement dynamiques, vous devez vous assurer d'avoir au moins une unité d'échelle (également appelée unité de diffusion). Pour plus d'informations, consultez la page [Mise à l'échelle d'un service de média](media-services-manage-origins.md#scale_streaming_endpoints). 
>
>De plus, votre ressource doit contenir un ensemble de MP4 à débit adaptatif ou des fichiers de diffusion en continu lisse à débit adaptatif.  

Vous pouvez appliquer des stratégies différentes à la même ressource. Par exemple, vous pouvez appliquer le chiffrement PlayReady pour la diffusion en continu lisse et AES en enveloppe pour MPEG DASH et TLS. Tous les protocoles qui ne sont pas définis dans une stratégie de remise (par exemple, en cas d'ajout d'une stratégie unique qui spécifie uniquement TLS comme protocole) seront bloqués de la diffusion en continu. Cela ne s'applique toutefois pas si vous n'avez défini aucune stratégie de remise de ressources. Tous les protocoles seront alors autorisés.

Notez que si vous souhaitez remettre une ressource à chiffrement de stockage, vous devez configurer la stratégie de remise de la ressource. Avant de pouvoir diffuser votre ressource en continu, le serveur de diffusion supprime le chiffrement de stockage et transmet en continu votre contenu à l'aide de la stratégie de remise spécifiée. Par exemple, pour remettre votre ressource chiffrée avec la clé de chiffrement d'enveloppe AES, définissez le type de stratégie sur **DynamicEnvelopeEncryption**. Pour supprimer le chiffrement de stockage et diffuser la ressource en clair, définissez le type de stratégie sur **NoDynamicEncryption**. Vous trouverez des exemples qui montrent comment configurer ces types de stratégie ci-dessous. 

Selon la configuration de la stratégie de remise de ressources, vous pourrez empaqueter dynamiquement, chiffrer dynamiquement et diffuser les protocoles de diffusion en continu suivants : Diffusion en continu lisse, TLS, MPEG DASH et HDS.  

La liste suivante présente les formats utilisés pour diffuser en continu lisse, TLS, DASH et HDS.  

Smooth Streaming :

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

HLS:

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

MPEG DASH

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf) 

HDS

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=f4m-f4f)

Pour savoir comment publier une ressource et générer une URL de diffusion en continu, consultez [Générer une URL de diffusion en continu](media-services-deliver-streaming-content.md).

>[AZURE.NOTE] Lorsque vous utilisez l'API REST de Media Services, les considérations suivantes s'appliquent :
>
>Lors de l'accès aux entités dans Media Services, vous devez définir les valeurs et les champs d'en-tête spécifiques dans vos requêtes HTTP. Pour plus d'informations, consultez [Configuration du développement de l'API REST Media Services](media-services-rest-how-to-use.md).

>Après vous être connecté à https://media.windows.net, vous recevrez une redirection 301 spécifiant un autre URI Media Services. Vous devez effectuer les appels suivants au nouvel URI comme décrit dans [Connexion à Media Services à l'aide de l'API REST](media-services-rest-connect_programmatically.md). 


## Stratégie de remise de ressources 

### <a id="create_asset_delivery_policy"></a>Création d'une stratégie de remise de ressources
La requête HTTP suivante permet de créer une stratégie de remise de ressources qui précise de ne pas appliquer de chiffrement dynamique et de remettre le flux avec l'un des protocoles suivants :  MPEG DASH, HLS et Smooth Streaming. 

Pour plus d'informations sur les valeurs que vous pouvez spécifier lors de la création d'une AssetDeliveryPolicy, consultez [Types utilisés lors de la définition d'AssetDeliveryPolicy](#types) .   


Demande :
	  
	POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-e769-2233-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423397827&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Szo6lbJAvL3dyecAeVmyAnzv3mGzfUNClR5shk9Ivbk%3d
	x-ms-version: 2.8
	x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
	Host: media.windows.net
	
	{"Name":"Clear Policy",
	"AssetDeliveryProtocol":7,
	"AssetDeliveryPolicyType":2,
	"AssetDeliveryConfiguration":null}

Réponse :
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 363
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 4651882c-d7ad-4d5e-86ab-f07f47dcb41e
	request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
	x-ms-request-id: 6aedbf93-4bc2-4586-8845-fd45590136af
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	X-Powered-By: ASP.NET
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sun, 08 Feb 2015 06:21:27 GMT
	
	{"odata.metadata":"https://media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element",
	"Id":"nb:adpid:UUID:92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd",
	"Name":"Clear Policy",
	"AssetDeliveryProtocol":7,
	"AssetDeliveryPolicyType":2,
	"AssetDeliveryConfiguration":null,
	"Created":"2015-02-08T06:21:27.6908329Z",
	"LastModified":"2015-02-08T06:21:27.6908329Z"}
	
### <a id="link_asset_with_asset_delivery_policy"></a>Liaison d'une ressource à la stratégie de remise de ressources

La demande HTTP suivante lie la ressource spécifiée à la stratégie de remise de ressources.

Demande :

	POST https://media.windows.net/api/Assets('nb%3Acid%3AUUID%3A86933344-9539-4d0c-be7d-f842458693e0')/$links/DeliveryPolicies HTTP/1.1
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Content-Type: application/json
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-e769-3344-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423397827&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=Szo6lbJAvL3dyecAeVmyAnzv3mGzfUNClR5shk9Ivbk%3d
	x-ms-version: 2.8
	x-ms-client-request-id: 56d2763f-6e72-419d-ba3c-685f6db97e81
	Host: media.windows.net
	
	{"uri":"https://media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3A92b0f6ba-3c9f-49b6-a5fa-2a8703b04ecd')"}

Réponse :

	HTTP/1.1 204 No Content


## Stratégie de remise de ressources DynamicEnvelopeEncryption 

### Création de la clé de contenu de type EnvelopeEncryption et liaison à la ressource

Lorsque vous spécifiez la stratégie de remise DynamicEnvelopeEncryption, vous devez veiller à lier votre ressource à une clé de contenu de type EnvelopeEncryption. Pour plus d'informations, consultez : [Création d'une clé de contenu](media-services-rest-create-contentkey.md)).


### <a id="get_delivery_url"></a>Obtenir l'URL de remise

Obtenez l'URL de remise pour la méthode de remise spécifiée de la clé de contenu créée à l'étape précédente. Un client utilise l'URL retournée pour demander une clé AES ou une licence PlayReady afin de lire le contenu protégé.

Spécifiez le type d'URL à obtenir dans le corps de la demande HTTP. Si vous protégez votre contenu avec PlayReady, demandez une URL d'acquisition de licence PlayReady Media Services, en utilisant 1 pour keyDeliveryType : {"keyDeliveryType":1}. Si vous protégez votre contenu avec le chiffrement d'enveloppe, demandez une URL d'acquisition de clé en spécifiant 2 pour keyDeliveryType : {"keyDeliveryType":2}.

Demande :
	
	POST https://media.windows.net/api/ContentKeys('nb:kid:UUID:dc88f996-2859-4cf7-a279-c52a9d6b2f04')/GetKeyDeliveryUrl HTTP/1.1
	Content-Type: application/json
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423452029&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=IEXV06e3drSIN5naFRBdhJZCbfEqQbFZsGSIGmawhEo%3d
	x-ms-version: 2.8
	x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
	Host: media.windows.net
	Content-Length: 21
	
	{"keyDeliveryType":2}

Réponse :
	
	HTTP/1.1 200 OK
	Cache-Control: no-cache
	Content-Length: 198
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: 569d4b7c-a446-4edc-b77c-9fb686083dd8
	request-id: d26f65d2-fe65-4136-8fcf-31545be68377
	x-ms-request-id: d26f65d2-fe65-4136-8fcf-31545be68377
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Sun, 08 Feb 2015 21:42:30 GMT
	
	{"odata.metadata":"media.windows.net/api/$metadata#Edm.String","value":"https://amsaccount1.keydelivery.mediaservices.windows.net/?KID=dc88f996-2859-4cf7-a279-c52a9d6b2f04"}


### Création d'une stratégie de remise de ressources

La demande HTTP suivante crée l'**AssetDeliveryPolicy** configurée pour appliquer le chiffrement dynamique en enveloppe (**DynamicEnvelopeEncryption**) au protocole **TLS** (dans cet exemple, les autres protocoles ne pourront pas être diffusés en continu). 


Pour plus d'informations sur les valeurs que vous pouvez spécifier lors de la création d'une AssetDeliveryPolicy, consultez [Types utilisés lors de la définition d'AssetDeliveryPolicy](#types) .   

Demande :

	POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423480651&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=T2FG3tIV0e2ETzxQ6RDWxWAsAzuy3ez2ruXPhrBe62Y%3d
	x-ms-version: 2.8
	x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
	Host: media.windows.net
	
	{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{"Key":2,"Value":"https://amsaccount1.keydelivery.mediaservices.windows.net/"}]"}

	
Réponse :
	
	HTTP/1.1 201 Created
	Cache-Control: no-cache
	Content-Length: 460
	Content-Type: application/json;odata=minimalmetadata;streaming=true;charset=utf-8
	Location: media.windows.net/api/AssetDeliveryPolicies('nb%3Aadpid%3AUUID%3Aec9b994e-672c-4a5b-8490-a464eeb7964b')
	Server: Microsoft-IIS/8.5
	x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
	request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
	x-ms-request-id: c2a1ac0e-9644-474f-b38f-b9541c3a7c5f
	X-Content-Type-Options: nosniff
	DataServiceVersion: 3.0;
	Strict-Transport-Security: max-age=31536000; includeSubDomains
	Date: Mon, 09 Feb 2015 05:24:38 GMT
	
	{"odata.metadata":"media.windows.net/api/$metadata#AssetDeliveryPolicies/@Element","Id":"nb:adpid:UUID:ec9b994e-672c-4a5b-8490-a464eeb7964b","Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":4,"AssetDeliveryPolicyType":3,"AssetDeliveryConfiguration":"[{"Key":2,"Value":"https://amsaccount1.keydelivery.mediaservices.windows.net/"}]","Created":"2015-02-09T05:24:38.9167436Z","LastModified":"2015-02-09T05:24:38.9167436Z"}


### Liaison d'une ressource à la stratégie de remise de ressources

Consultez [Liaison d'une ressource à la stratégie de remise de ressources](#link_asset_with_asset_delivery_policy)

## Stratégie de remise de ressources DynamicCommonEncryption 

### Création de la clé de contenu de type CommonEncryption et liaison à la ressource

Lorsque vous spécifiez la stratégie de remise DynamicCommonEncryption, vous devez veiller à lier votre ressource à une clé de contenu de type CommonEncryption. Pour plus d'informations, consultez : [Création d'une clé de contenu](media-services-rest-create-contentkey.md)).


### Obtention de l'URL de remise

Obtenez l'URL de remise pour la méthode de remise PlayReady de la clé de contenu créée à l'étape précédente. Un client utilise l'URL retournée pour demander une licence PlayReady afin de lire le contenu protégé. Pour plus d'informations, consultez [Obtention de l'URL de remise](#get_delivery_url).

### Création d'une stratégie de remise de ressources

La demande HTTP suivante crée l'**AssetDeliveryPolicy** configurée pour appliquer le chiffrement dynamique courant (**DynamicCommonEncryption**) au protocole **de diffusion en continu lisse** (dans cet exemple, les autres protocoles ne pourront pas être diffusés en continu). 

Pour plus d'informations sur les valeurs que vous pouvez spécifier lors de la création d'une AssetDeliveryPolicy, consultez [Types utilisés lors de la définition d'AssetDeliveryPolicy](#types) .   


Demande :

	POST https://media.windows.net/api/AssetDeliveryPolicies HTTP/1.1
	Content-Type: application/json
	DataServiceVersion: 1.0;NetFx
	MaxDataServiceVersion: 3.0;NetFx
	Accept: application/json
	Accept-Charset: UTF-8
	User-Agent: Microsoft ADO.NET Data Services
	Authorization: Bearer http%3a%2f%2fschemas.xmlsoap.org%2fws%2f2005%2f05%2fidentity%2fclaims%2fnameidentifier=amsaccount1&urn%3aSubscriptionId=zbbef702-2233-477b-9f16-bc4d3aa97387&http%3a%2f%2fschemas.microsoft.com%2faccesscontrolservice%2f2010%2f07%2fclaims%2fidentityprovider=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&Audience=urn%3aWindowsAzureMediaServices&ExpiresOn=1423480651&Issuer=https%3a%2f%2fwamsprodglobal001acs.accesscontrol.windows.net%2f&HMACSHA256=T2FG3tIV0e2ETzxQ6RDWxWAsAzuy3ez2ruXPhrBe62Y%3d
	x-ms-version: 2.8
	x-ms-client-request-id: fff319f6-71dd-4f6c-af27-b675c0066fa7
	Host: media.windows.net
	
	{"Name":"AssetDeliveryPolicy","AssetDeliveryProtocol":1,"AssetDeliveryPolicyType":4,"AssetDeliveryConfiguration":"[{"Key":2,"Value":"https://amsaccount1.keydelivery.mediaservices.windows.net/PlayReady/"}]"}


### Liaison d'une ressource à la stratégie de remise de ressources

Consultez [Liaison d'une ressource à la stratégie de remise de ressources](#link_asset_with_asset_delivery_policy)


## <a id="types"></a>Types utilisés durant la définition de AssetDeliveryPolicy

### AssetDeliveryProtocol 

    /// <summary>
    /// Delivery protocol for an asset delivery policy.
    /// </summary>
    [Flags]
    public enum AssetDeliveryProtocol
    {
        /// <summary>
        /// No protocols.
        /// </summary>
        None = 0x0,

        /// <summary>
        /// Smooth streaming protocol.
        /// </summary>
        SmoothStreaming = 0x1,

        /// <summary>
        /// MPEG Dynamic Adaptive Streaming over HTTP (DASH)
        /// </summary>
        Dash = 0x2,

        /// <summary>
        /// Apple HTTP Live Streaming protocol.
        /// </summary>
        HLS = 0x4,

        /// <summary>
        /// Adobe HTTP Dynamic Streaming (HDS)
        /// </summary>
        Hds = 0x8,

        /// <summary>
        /// Include all protocols.
        /// </summary>
        All = 0xFFFF
    }

### AssetDeliveryPolicyType

    /// <summary>
    /// Policy type for dynamic encryption of assets.
    /// </summary>
    public enum AssetDeliveryPolicyType
    {
        /// <summary>
        /// Delivery Policy Type not set.  An invalid value.
        /// </summary>
        None,

        /// <summary>
        /// The Asset should not be delivered via this AssetDeliveryProtocol. 
        /// </summary>
        Blocked, 

        /// <summary>
        /// Do not apply dynamic encryption to the asset.
        /// </summary>
        /// 
        NoDynamicEncryption,  

        /// <summary>
        /// Apply Dynamic Envelope encryption.
        /// </summary>
        DynamicEnvelopeEncryption,

        /// <summary>
        /// Apply Dynamic Common encryption.
        /// </summary>
        DynamicCommonEncryption
    }

### ContentKeyDeliveryType

    /// <summary>
    /// Delivery method of the content key to the client.
    /// </summary>
    public enum ContentKeyDeliveryType
    {
        /// <summary>
        /// None.
        /// </summary>
        None,

        /// <summary>
        /// Use PlayReady License acquistion protocol
        /// </summary>
        PlayReadyLicense,

        /// <summary>
        /// Use MPEG Baseline HTTP key protocol.
        /// </summary>
        BaselineHttp
    }

### AssetDeliveryPolicyConfigurationKey

    /// <summary>
    /// Keys used to get specific configuration for an asset delivery policy.
    /// </summary>
    public enum AssetDeliveryPolicyConfigurationKey
    {
        /// <summary>
        /// No policies.
        /// </summary>
        None,

        /// <summary>
        /// Exact Envelope key URL.
        /// </summary>
        EnvelopeKeyAcquisitionUrl,

        /// <summary>
        /// Base key url that will have KID=<Guid> appended for Envelope.
        /// </summary>
        EnvelopeBaseKeyAcquisitionUrl,
        
        /// <summary>
        /// The initialization vector to use for envelope encryption in Base64 format.
        /// </summary>
        EnvelopeEncryptionIVAsBase64,

        /// <summary>
        /// The PlayReady License Acquisition Url to use for common encryption.
        /// </summary>
        PlayReadyLicenseAcquisitionUrl,

        /// <summary>
        /// The PlayReady Custom Attributes to add to the PlayReady Content Header
        /// </summary>
        PlayReadyCustomAttributes,

        /// <summary>
        /// The initialization vector to use for envelope encryption.
        /// </summary>
        EnvelopeEncryptionIV,
    }


<!--HONumber=52-->