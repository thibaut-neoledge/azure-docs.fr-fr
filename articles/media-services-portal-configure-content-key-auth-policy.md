<properties 
	pageTitle="Configuration de la stratégie d'autorisation de clé de contenu à l'aide du portail" 
	description="Apprenez à configurer une stratégie d'autorisation pour une clé de contenu." 
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
	ms.date="02/20/2015" 
	ms.author="juliako"/>



#Configuration de la stratégie d'autorisation de clé de contenu 
[AZURE.INCLUDE [media-services-selector-content-key-auth-policy](../includes/media-services-selector-content-key-auth-policy.md)]

Cet article fait partie de la série [workflow de vidéo à la demande Media Services](media-services-video-on-demand-workflow.md) et [workflow de vidéo en flux continu Media Services](media-services-live-streaming-workflow.md) . 


##Vue d'ensemble

Microsoft Azure Media Services permet de transmettre un contenu chiffré avec la norme AES (Advanced Encryption Standard) (à l'aide de clés de chiffrement 128 bits) et le DRM PlayReady. Media Services fournit également un **service de remise de clés/de licences** à partir duquel les clients peuvent obtenir une clé ou une licence pour lire le contenu chiffré. 

Cette rubrique montre comment utiliser le **portail de gestion Azure** pour configurer la stratégie d'autorisation de clé de contenu. Elles peuvent ensuite être utilisées pour chiffrer dynamiquement votre contenu. Notez que vous pouvez actuellement chiffrer la diffusion en continu des formats suivants : TLS, MPEG DASH et diffusion en continu lisse. Vous ne pouvez pas chiffrer le format de diffusion en continu HDS ni les téléchargements progressifs.
 
Lorsqu'un lecteur demande un flux de données devant être chiffré dynamiquement, Media Services utilise la clé configurée pour chiffrer dynamiquement votre contenu à l'aide du chiffrement AES ou PlayReady. Pour déchiffrer le flux de données, le lecteur demande la clé au service de remise de clé. Pour déterminer si l'utilisateur est autorisé à obtenir la clé, le service évalue les stratégies d'autorisation que vous avez spécifiées pour la clé.


Si vous prévoyez de disposer de plusieurs clés de contenu ou souhaitez spécifier une URL de **service de remise de clés/de licences** autre que le service de remise de clé Media Services, utilisez le Kit de développement logiciel (SDK) .NET Media Services ou des API REST.

[Configurer la stratégie d'autorisation de clé de contenu à l'aide du Kit de développement logiciel (SDK).NET Media Services](media-services-dotnet-configure-content-key-auth-policy.md)

[Configurer la stratégie d'autorisation de clé de contenu à l'aide de l'API REST Media Services](media-services-rest-configure-content-key-auth-policy.md)

###Certaines considérations s'appliquent :

- Pour pouvoir utiliser l'empaquetage et le chiffrement dynamiques, vous devez vous assurer d'avoir au moins une unité d'échelle (également appelée unité de diffusion). Pour plus d'informations, consultez [Mise à l'échelle d'un service de média](media-services-manage-origins#scale_streaming_endpoints.md). 
- Votre ressource doit contenir un ensemble de MP4 à débit adaptatif ou des fichiers de diffusion en continu lisse à débit adaptatif. Pour plus d'informations, consultez [Encodage d'une ressource](media-services-encode-asset.md).  
- Le service de remise de clé met en cache ContentKeyAuthorizationPolicy et ses objets connexes (options de stratégie et restrictions) pendant 15 minutes.  Si vous créez une ContentKeyAuthorizationPolicy et que vous spécifiez l'utilisation d'une restriction " Jeton ", puis la testez avant de mettre à jour la stratégie de restriction vers " Ouverte ", vous devrez attendre environ 15 minutes avant que la stratégie bascule vers la version " Ouverte ".


##Configuration de la stratégie d'autorisation de clé

Pour configurer la stratégie d'autorisation de clé, sélectionnez la page **PROTECTION DU CONTENU**.
	
Media Services prend en charge plusieurs méthodes d'authentification des utilisateurs effectuant des demandes de clé. La stratégie d'autorisation de clé de contenu peut disposer de restrictions d'autorisation de type **ouvert**, **jeton** ou **IP** (**IP** peut être configuré avec REST ou le Kit de développement logiciel (SDK) .NET). 

###Restriction ouverte

La restriction **ouverte** signifie que le système fournira la clé à toute personne effectuant une demande de clé. Cette restriction peut être utile à des fins de test.

![OpenPolicy][open_policy]

###Restriction à jeton

Pour choisir la stratégie de restriction à jeton, cliquez sur le bouton **JETON**.

La stratégie de restriction à **jeton** doit être accompagnée d'un jeton émis par un **service de jeton sécurisé** (STS). Media Services prend en charge les jetons aux formats **jeton Web simple** ([SWT](https://msdn.microsoft.com/library/gg185950.aspx#BKMK_2)) et **jeton Web JSON** (JWT). Pour plus d'informations, consultez [Authentification à jeton JWT](http://www.gtrifonov.com/2015/01/03/jwt-token-authentication-in-azure-media-services-and-dynamic-encryption/).

Media Services ne fournit pas de **Service de jeton sécurisé** (STS). Vous pouvez créer un STS personnalisé ou utiliser l'ACS Microsoft Azure pour émettre des jetons. Le STS doit être configuré pour créer un jeton signé avec la clé spécifiée et émettre les revendications spécifiées dans la configuration de restriction de jeton. Le service de remise de clé Media Services retourne la clé de chiffrement pour le client si le jeton est valide et que les revendications du jeton correspondent à celles configurées pour la clé de contenu. Pour plus d'informations, consultez [Utilisation de de l'ACS Azure pour émettre des jetons](http://mingfeiy.com/acs-with-key-services).

Lorsque vous configurez la stratégie de restriction **JETON**, vous devez définir des valeurs pour **clé de vérification**, **émetteur** et **public**. La clé de vérification principale contient la clé utilisée pour signer le jeton, l'émetteur est le service de jeton sécurisé qui émet le jeton. Le public (parfois appelé l'étendue) décrit l'objectif du jeton ou la ressource à laquelle le jeton autorise l'accès. Le service de remise de clé Media Services valide le fait que les valeurs du jeton correspondent aux valeurs du modèle.  

###PlayReady

Lorsque vous protégez votre contenu avec **PlayReady**, l'une des choses à spécifier dans votre stratégie d'autorisation est une chaîne XML qui définit le modèle de licence PlayReady. Par défaut, la stratégie suivante est définie :
		
	<PlayReadyLicenseResponseTemplate xmlns:i="http://www.w3.org/2001/XMLSchema-instance" xmlns="http://schemas.microsoft.com/Azure/MediaServices/KeyDelivery/PlayReadyTemplate/v1">
	  <LicenseTemplates>
	    <PlayReadyLicenseTemplate><AllowTestDevices>true</AllowTestDevices>
	      <ContentKey i:type="ContentEncryptionKeyFromHeader" />
	      <LicenseType>Nonpersistent</LicenseType>
	      <PlayRight>
	        <AllowPassingVideoContentToUnknownOutput>Allowed</AllowPassingVideoContentToUnknownOutput>
	      </PlayRight>
	    </PlayReadyLicenseTemplate>
	  </LicenseTemplates>
	</PlayReadyLicenseResponseTemplate>

Vous pouvez cliquer sur le bouton **importer le xml de la stratégie** et fournir un autre XML conforme au schéma XML défini [ici](https://msdn.microsoft.com/library/azure/dn783459.aspx).

##Étapes suivantes
Maintenant que vous avez configuré la stratégie d'autorisation de la clé de contenu, consultez [ Utilisez le portail de gestion Azure pour activer le chiffrement](media-services-manage-content#encrypt.md) .


[open_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-open-restriction.png
[token_policy]: ./media/media-services-key-authorization-policy/media-services-protect-content-with-token-restriction.png


<!--HONumber=47-->
