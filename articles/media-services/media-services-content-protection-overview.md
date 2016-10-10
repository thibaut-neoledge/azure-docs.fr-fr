<properties 
	pageTitle="Vue d’ensemble de la protection du contenu | Microsoft Azure" 
	description="Cet article donne une vue d’ensemble de la protection du contenu avec Media Services." 
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
	ms.date="09/27/2016" 
	ms.author="juliako"/>

#Vue d’ensemble de la protection du contenu


Microsoft Azure Media Services vous permet de sécuriser votre contenu multimédia dès lors qu’il quitte votre ordinateur via le stockage, le traitement et la remise. Media Services permet de transmettre un contenu dynamique ou à la demande chiffré dynamiquement avec la norme AES (Advanced Encryption Standard) (à l’aide de clés de chiffrement 128 bits) ou un des DRM principaux : Microsoft PlayReady, Google Widevine, et Apple FairPlay. Media Services fournit également un service de distribution de clés AES et licences (PlayReady, Widevine et FairPlay) DRM aux clients autorisés.

L’image suivante montre les flux de travail de protection du contenu pris en charge par AMS.

![Protéger avec PlayReady](./media/media-services-content-protection-overview/media-services-content-protection-with-multi-drm.png)

>[AZURE.NOTE]Pour pouvoir utiliser le chiffrement dynamique, vous devez obtenir au moins une unité réservée de diffusion en continu pour le point de terminaison de diffusion en continu à partir duquel vous prévoyez de diffuser votre contenu chiffré.

Cette rubrique explique les [concepts et la terminologie](media-services-content-protection-overview.md) pertinentes pour comprendre la protection du contenu avec AMS. Cette rubrique contient également des [liens](media-services-content-protection-overview.md#common-scenarios) vers des rubriques qui montrent comment effectuer les tâches de protection du contenu.

##Chiffrement dynamique

Microsoft Azure Media Services vous permet de transmettre du contenu chiffré de manière dynamique avec le chiffrement de clé en clair AES ou DRM : Microsoft PlayReady, Google Widevine, et Apple FairPlay.

Actuellement, vous pouvez chiffrer les formats de diffusion en continu suivants : HLS, MPEG DASH et Smooth Streaming. Vous ne pouvez pas chiffrer le format de diffusion en continu HDS ni les téléchargements progressifs.

Si vous souhaitez que Media Services chiffre un élément multimédia, vous devez associer une clé de chiffrement (CommonEncryption ou EnvelopeEncryption) à votre élément multimédia et configurer des stratégies d’autorisation pour la clé.

Vous devez également configurer la stratégie de remise de l’élément multimédia. Si vous souhaitez diffuser un élément multimédia avec chiffrement de stockage, assurez-vous de spécifier comment vous souhaitez le remettre en configurant la stratégie de remise d’élément multimédia.

Lorsqu’un flux est demandé par un lecteur, Media Services utilise la clé spécifiée pour chiffrer dynamiquement votre contenu à l’aide du chiffrement de clé en clair AES ou DRM. Pour déchiffrer le flux de données, le lecteur demande la clé au service de remise de clé. Pour déterminer si l’utilisateur est autorisé à obtenir la clé, le service évalue les stratégies d’autorisation que vous avez spécifiées pour la clé.

>[AZURE.NOTE]Pour tirer parti du chiffrement dynamique, vous devez d’abord obtenir au moins une unité de diffusion en continu à la demande pour le point de terminaison de diffusion en continu à partir duquel vous envisagez de distribuer votre contenu chiffré. Pour plus d'informations, consultez [Mise à l'échelle de Media Services](media-services-portal-manage-streaming-endpoints.md).

##Chiffrement du stockage

Utilisez le chiffrement du stockage pour chiffrer votre contenu localement à l’aide du chiffrement AES 256 bits, puis chargez-le vers Azure Storage où il est stocké au repos sous forme chiffrée. Les éléments multimédias protégés par le chiffrement de stockage sont automatiquement déchiffrés et placés dans un système de fichiers chiffré avant d’être encodés, puis éventuellement rechiffrés avant d’être rechargés sous la forme d’un nouvel élément multimédia de sortie. Le principal cas d’utilisation du chiffrement de stockage concerne la sécurisation des fichiers multimédias d’entrée de haute qualité avec un chiffrement renforcé au repos sur le disque.

Pour fournir un élément multimédia avec chiffrement de stockage, vous devez configurer la stratégie de remise de l'élément multimédia afin que Media Services sache comment vous souhaitez remettre votre contenu. Pour que votre élément multimédia puisse être diffusé en continu, le serveur de diffusion supprime le chiffrement de stockage et diffuse votre contenu à l'aide de la stratégie de remise spécifiée (par exemple AES, chiffrement commun ou aucun chiffrement).

###Informations d’implémentation 

Le chiffrement de stockage AMS applique le mode de chiffrement **AES-CTR** à la totalité du fichier. Le mode AES-CTR est un chiffrement par blocs qui permet de chiffrer des données de longueur arbitraire sans avoir besoin de remplissage. Il fonctionne en chiffrant un bloc de compteur avec l'algorithme AES, puis en appliquant l’opération XOR à la sortie d’AES avec les données à chiffrer ou déchiffrer. Le bloc de compteur utilisé est construit en copiant la valeur InitializationVector sur les octets 0 à 7 de la valeur du compteur et les octets 8 à 15 de la valeur du compteur ont la valeur zéro. Dans le bloc de compteur de 16 octets, les octets 8 à 15 (c'est-à-dire les octets les moins significatifs) sont utilisés comme simple entier non signé de 64 bits, incrémenté de un pour chacun des blocs suivants de données traitées et conservé dans l'ordre des octets du réseau. Notez que, si cet entier atteint la valeur maximale (0xFFFFFFFFFFFFFFFF), son incrémentation réinitialise le compteur de blocs à zéro (octets 8 à 15) sans affecter les autres 64 bits du compteur (c'est-à-dire les octets 0 à 7). Pour maintenir la sécurité du mode de chiffrement AES-CTR, la valeur InitializationVector pour un KID donné doit être unique pour chaque fichier et les fichiers doivent avoir une longueur inférieure à 2^64 blocs. Cela permet de faire en sorte qu'aucune valeur de compteur ne soit jamais réutilisée avec une clé donnée. Pour plus d'informations sur le mode CTR, consultez [cette page wiki](https://en.wikipedia.org/wiki/Block_cipher_mode_of_operation#CTR) (l'article wiki utilise le terme « Nonce » au lieu de « InitializationVector »).

Si vous voulez voir comment fonctionne l'algorithme de base, consultez l'implémentation .NET AMS des méthodes suivantes :

- [ApplyEncryptionTransform](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.BlobTransfer/BlobTransferBase.cs)
- [AesCtr](https://github.com/Azure/azure-sdk-for-media-services/blob/dev/src/net/Client/Common/Common.FileEncryption/FileEncryptionTransform.cs)


## Chiffrement commun (CENC)

Le chiffrement commun est utilisé pour chiffrer votre contenu avec PlayReady ou/et Widewine.

## Utilisation du chiffrement cbcs-aapl

cbcs-aapl est utilisé pour chiffrer votre contenu avec FairPlay.

## Chiffrement d’enveloppe 

Utilisez cette option si vous souhaitez protéger votre contenu avec la clé en clair AES-128. Si vous souhaitez une option plus sécurisée, choisissez parmi les DRM répertoriées dans cette rubrique.

##Service de remise de licences et de clés

Media Services fournit un service de remise de licences DRM (PlayReady, Widevine, FairPlay) et de clés en clair AES aux clients autorisés. Vous pouvez utiliser [le portail Azure Classic](media-services-portal-protect-content.md), l’API REST ou le kit de développement logiciel (SDK) Media Services pour .NET pour configurer des stratégies d’authentification et d’autorisation pour vos licences et vos clés.

##Restriction à jeton

La stratégie d’autorisation des clés de contenu peut comporter une ou plusieurs restrictions d’autorisation : ouverte ou restriction à jeton. La stratégie de restriction à jeton doit être accompagnée d’un jeton émis par un service de jeton sécurisé (STS). Media Services prend en charge les jetons aux formats SWT (Simple Web Tokens) et JWT (JSON Web Token). Media Services ne fournit pas de services de jeton sécurisé. Vous pouvez créer un STS personnalisé ou utiliser l’ACS Microsoft Azure pour émettre des jetons. Le STS doit être configuré pour créer un jeton signé avec la clé spécifiée et émettre les revendications spécifiées dans la configuration de restriction de jeton. Le service de remise de clé Media Services retourne la clé (ou licence) demandée au client si le jeton est valide et que les revendications du jeton correspondent à celles configurées pour la clé (ou licence).

Lorsque vous configurez la stratégie de restriction par jeton, vous devez définir les paramètres principaux de clé de vérification, émetteur et public. La clé de vérification principale contient la clé utilisée pour signer le jeton, l’émetteur est le service de jeton sécurisé qui émet le jeton. Le public (parfois appelé l’étendue) décrit l’objectif du jeton ou la ressource à laquelle le jeton autorise l’accès. Le service de remise de clé Media Services valide le fait que les valeurs du jeton correspondent aux valeurs du modèle.

##URL de diffusion

Si votre ressource a été chiffrée avec plusieurs DRM, vous devez utiliser une balise de chiffrement dans l’URL de diffusion en continu : (format=’m3u8-aapl’, encryption=’xxx’).

Les considérations suivantes s'appliquent :

- Seul zéro ou un type de chiffrement peut être spécifié.
- Le type de chiffrement ne doit pas être spécifié dans l’url si un seul chiffrement a été appliqué à l’élément multimédia.
- Le type de chiffrement ne tient pas compte de la casse.
- Les types de chiffrement suivants peuvent être spécifiés :
	- **cenc** : chiffrement commun (Playready ou Widevine)
	- **cbcs-aapl** : Fairplay
	- **cbc** : chiffrement de l’enveloppe AES.

##Scénarios courants

Les rubriques suivantes montrent comment protéger le contenu stocké et diffuser du contenu multimédia chiffré dynamiquement en continu, utiliser le service AMS de fourniture de clé/licence

- [Offrir une protection basée sur AES](media-services-protect-with-aes128.md)
- [Protection avec PlayReady et/ou Widevine](media-services-protect-with-drm.md)
- [Diffuser en continu votre contenu HLS protégé avec Apple FairPlay et/ou PlayReady](media-services-protect-hls-with-fairplay.md)

### Autres cas de figure

- [Intégration du service de remise de licences PlayReady d’Azure à votre propre chiffreur/serveur de diffusion en continu](http://mingfeiy.com/integrate-azure-playready-license-service-encryptorstreaming-server).
- [Utilisation de castLabs pour fournir des licences DRM à Azure Media Services](media-services-castlabs-integration.md)
 
##Parcours d’apprentissage de Media Services

[AZURE.INCLUDE [media-services-learning-paths-include](../../includes/media-services-learning-paths-include.md)]

##Fournir des commentaires

[AZURE.INCLUDE [media-services-user-voice-include](../../includes/media-services-user-voice-include.md)]

##Liens connexes

[Annonce de PlayReady en tant que service et du chiffrement AES dynamique avec Azure Media Services](http://mingfeiy.com/playready)

[Explication de la tarification de la remise de licences PlayReady d’Azure Media Services](http://mingfeiy.com/playready-pricing-explained-in-azure-media-services)

[Débogage d’un flux de données chiffré utilisant le chiffrement AES dans Azure Media Services](http://mingfeiy.com/debug-aes-encrypted-stream-azure-media-services)

[Authentification par jeton JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/)

[Intégration d'une application Azure Media Services basée sur OWIN MVC avec Azure Active Directory et une remise de clé de contenu basée sur les revendications JWT](http://www.gtrifonov.com/2015/01/24/mvc-owin-azure-media-services-ad-integration/)

[Utilisation d'ACS Azure pour émettre des jetons](http://mingfeiy.com/acs-with-key-services)

[content-protection]: ./media/media-services-content-protection-overview/media-services-content-protection.png

<!---HONumber=AcomDC_0928_2016-->