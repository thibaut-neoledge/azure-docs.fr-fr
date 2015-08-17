<properties 
	pageTitle="Gestion de contenu avec Azure Media Services à l’aide du portail de gestion Azure" 
	description="Apprenez à gérer votre contenu multimédia dans Azure Media Services. Notamment : le téléchargement, l’indexation, l’encodage, le chiffrement et la publication." 
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
	ms.date="07/23/2015" 
	ms.author="juliako"/>



# Gestion de contenu avec Azure Media Services à l’aide du portail de gestion Azure


Cette rubrique présente l’utilisation du portail de gestion Azure pour gérer du contenu multimédia dans votre compte Media Services.

Cette rubrique explique comment effectuer les opérations suivantes directement à partir du portail :

- Afficher les informations de contenu comme l’état publié, l’URL publiée, la taille, la date et l’heure de la dernière mise à jour, et si l’élément multimédia est chiffré
- Télécharger un nouveau contenu
- Indexer le contenu
- Encodage de contenu
- Chiffrer
- Publier/annuler la publication d’un contenu
- Lire le contenu


##<a id="upload"></a>Téléchargement de contenu 


[AZURE.INCLUDE [media-services-selector-upload-files](../../includes/media-services-selector-upload-files.md)]


1. Sur le [portail de gestion](http://go.microsoft.com/fwlink/?LinkID=256666&clcid=0x409), cliquez sur **Media Services**, puis sur le nom du compte Media Services.
2. Sélectionnez la page CONTENU. 
3. Cliquez sur le bouton **Télécharger** disponible sur cette page ou en bas du portail. 
4. Dans la boîte de dialogue **Télécharger le contenu**, accédez au fichier correspondant à l’élément multimédia de votre choix. Cliquez sur le fichier, puis sur **Ouvrir**, ou appuyez sur **Entrée**.

	![UploadContentDialog][uploadcontent]

5. Dans la boîte de dialogue Télécharger le contenu, cliquez sur le bouton de vérification pour accepter le nom du fichier et du contenu.
6. Le téléchargement démarre et vous pouvez suivre la progression en bas du portail.  

	![JobStatus][status]

Une fois le téléchargement terminé, le nouvel élément multimédia est répertorié dans la liste Contenu. Par convention, la mention « **-Source** » est ajoutée à la fin du nom afin de faciliter le suivi des nouveaux contenus sources pour les tâches d'encodage.

![ContentPage][contentpage]

Si la valeur relative à la taille du fichier n’est pas mise à jour après l’arrêt du processus de téléchargement, cliquez sur le bouton **Synchroniser les métadonnées**. La taille du fichier multimédia est alors synchronisée avec la taille réelle du fichier stocké et la valeur est actualisée sur la page Contenu.

##<a id="index"></a>Indexation de contenu

> [AZURE.SELECTOR]
- [.NET](media-services-index-content.md)
- [Portal](media-services-manage-content.md#index)

Azure Media Indexer permet de rendre le contenu de vos fichiers multimédias consultable et de générer une transcription en texte intégral de sous-titrages et de mots-clés. Vous pouvez indexer votre contenu à l’aide du portail de gestion en suivant les étapes présentées ci-dessous. Toutefois, si vous souhaitez davantage de contrôle sur les fichiers et sur la tâche d’indexation, vous pouvez utiliser le Kit de développement logiciel (SDK) Media Services pour .NET ou les API REST. Pour plus d’informations, consultez la page [Indexation des fichiers multimédias avec Azure Media Indexer](media-services-index-content.md).

Les étapes qui suivent présentent comment utiliser le portail de gestion pour indexer votre contenu.

1. Sélectionnez le fichier que vous souhaitez indexer. Si l’indexation est prise en charge pour ce type de fichier, le bouton de traitement sera activé en bas de la page de contenu.
1. Appuyez sur le bouton de traitement.
2. Dans la boîte de dialogue de **traitement**, choisissez le processeur **Azure Media Indexer**.
3. Ensuite, complétez dans la boîte de dialogue de traitement le **titre** et la **description** du fichier multimédia d’entrée.
	
	![Process][process]

##<a id="encode"></a>Encodage de contenu

> [AZURE.SELECTOR]
- [.NET](media-services-dotnet-encode-asset.md)
- [REST](media-services-rest-encode-asset.md)
- [Portal](media-services-manage-content.md#encode)

Pour fournir une vidéo numérique sur Internet, vous devez compresser le contenu multimédia. Media Services propose un encodeur multimédia qui vous permet de spécifier comment vous souhaitez que votre contenu soit encodé (par exemple, le codec à utiliser, le format du fichier, la résolution et le débit).

Lorsque vous travaillez avec Azure Media Services, un des scénarios les plus courants est la diffusion de contenu à débit adaptatif à vos clients. Avec la diffusion à débit binaire adaptatif, le client peut basculer vers un flux à débit binaire supérieur ou inférieur, car la vidéo est affichée en fonction de la bande passante réseau actuelle, de l’utilisation de l’UC et d’autres facteurs. Media Services prend en charge les technologies de diffusion en continu à débit binaire adaptatif suivantes : HTTP Live Streaming (HLS), Smooth Streaming, MPEG DASH et HDS (pour licences Adobe PrimeTime/Access uniquement).

Media Services fournit l’empaquetage dynamique qui permet de distribuer un contenu en diffusion continue en MP4 ou Smooth Streaming dans un format pris en charge par Media Services (MPEG DASH, HLS, Smooth Streaming, HDS) sans avoir à recréer de nouveaux packages dans ces formats.

Pour tirer parti de l’empaquetage dynamique, vous devez effectuer les opérations suivantes :

- encoder votre fichier mezzanine (source) dans un ensemble de fichiers mp4 à débit adaptatif ou de fichiers Smooth Streaming à débit adaptatif (les étapes de codage sont décrites plus loin dans ce didacticiel).
- obtenir au moins une unité de diffusion à la demande pour le point de terminaison de diffusion à partir duquel vous envisagez de distribuer votre contenu. Pour plus d’informations, consultez la page [Extension des unités réservées de diffusion en continu à la demande](media-services-manage-origins.md#scale_streaming_endpoints/).

Avec l’empaquetage dynamique, vous devez stocker et payer les fichiers dans un seul format de stockage. Ensuite, Media Services crée et fournit la réponse appropriée en fonction des demandes des clients.

Notez qu’en plus d’utiliser les fonctionnalités d’empaquetage dynamique, les unités réservées de diffusion en continu à la demande vous offrent une capacité de sortie dédiée qui peut être achetée par incréments de 200 Mbit/s. Par défaut, la diffusion en continu à la demande est configurée dans un modèle d’instance partagée, pour lequel les ressources du serveur (calcul, sortie, capacité, etc.) sont partagées avec tous les autres utilisateurs. Afin d’améliorer la vitesse de diffusion en continu à la demande, il est recommandé d’acheter des unités réservées de diffusion en continu à la demande.

Cette section décrit les étapes à suivre pour encoder votre contenu avec l’Encodeur multimédia Azure à l’aide du portail de gestion.

1.  Sélectionnez le fichier que vous souhaitez encoder. Si l’encodage est pris en charge pour ce type de fichier, le bouton de traitement sera activé en bas de la page de contenu.
4. Dans la boîte de dialogue de **traitement**, choisissez le processeur **Azure Media Encoder**.
5. Choisissez une des **configurations d’encodage**.

	![Process2][process2]

		
	La rubrique [Chaînes de présélection des tâches pour Encodeur multimédia Azure](https://msdn.microsoft.com/library/azure/dn619392.aspx) explique ce que signifie chaque présélection des catégories **Présélections de diffusion adaptative (empaquetage dynamique)**, **Présélections pour le téléchargement progressif** et **Présélections héritées pour la diffusion adaptative**.


	Les **Autres** configurations sont décrites ci-après :

	+ **Encodage avec la protection de contenu PlayReady**. Cette présélection produit un élément encodé avec la protection de contenu PlayReady.  
	
	
		Le service de licences PlayReady de Media Services est utilisé par défaut. Si vous souhaitez spécifier un autre service à partir duquel les clients puissent obtenir une licence pour lire le contenu chiffré avec PlayReady, utilisez les API REST ou les API du Kit de développement logiciel (SDK) Media Services pour .NET. Pour plus d’informations, consultez la page [Utilisation du chiffrement statique pour protéger votre contenu]() et définissez la propriété **licenseAcquisitionUrl** dans la présélection de Media Encryptor. Vous pouvez également utiliser le chiffrement dynamique et définir la propriété **PlayReadyLicenseAcquisitionUrl** comme décrit sur la page [Utilisation du chiffrement dynamique PlayReady et du service de distribution de licences](http://go.microsoft.com/fwlink/?LinkId=507720). 
	+ **Lecture sur PC/Mac (via Flash/Silverlight)**. Cette présélection produit un élément multimédia de diffusion en continu lisse doté des caractéristiques suivantes : CBR audio stéréo 44,1 kHz 16 bits/échantillon encodé à 96 Kbit/s via AAC, et CBR vidéo 720p encodé selon 6 débits binaires allant de 3 400 Kbit/s à 400 Kbit/s via le profil principal H.264, et GOP de deux secondes.
	+ **Lecture via HTML5 (IE/Chrome/Safari)**. Cette présélection produit un fichier MP4 doté des caractéristiques suivantes : CBR audio stéréo 44,1 kHz 16 bits/échantillon encodé à 128 Kbit/s via AAC, et CBR vidéo 720p encodé à 4500 Kbit/s via le profil principal H.264.
	+ **Lecture sur appareils iOS et PC/Mac**. Cette présélection produit un élément multimédia doté des mêmes caractéristiques que l’élément multimédia de diffusion en continu lisse (décrit ci-dessus), mais sous un format qui peut être utilisé pour une diffusion en continu HTTP Apple (HLS) aux appareils iOS. 

5. Ensuite, entrez un nom convivial pour le contenu de sortie ou acceptez le nom par défaut. Cliquez ensuite sur le bouton de vérification pour lancer l’opération d’encodage et suivez la progression en bas du portail.
6. Appuyez sur OK.

	Une fois l’encodage effectué, la page de contenu contient le fichier encodé.

	Pour afficher la progression de la tâche d’encodage, basculez vers la page **TRAVAUX**.

	Si la valeur relative à la taille du fichier n’est pas mise à jour à l’issue de l’encodage, appuyez sur le bouton **Synchroniser les métadonnées**. La taille du fichier multimédia de sortie est alors synchronisée avec la taille réelle du fichier stocké et la valeur est actualisée sur la page Contenu.

##<a id="encrypt"></a>Chiffrement du contenu

Si vous souhaitez que Media Services chiffre dynamiquement votre ressource avec une clé AES ou la DRM PlayReady, veillez à effectuer les opérations suivantes :

- encoder vos fichiers votre fichier mezzanine (source) en un ensemble de fichiers mp4 à débit adaptatif ou de fichiers Smooth Streaming à débit adaptatif (les étapes de codage sont décrites dans la section [Encodage](#encode)).
- obtenir au moins une unité de diffusion à la demande pour le point de terminaison de diffusion à partir duquel vous envisagez de distribuer votre contenu. Pour plus d’informations, consultez la page [Extension des unités réservées de diffusion en continu à la demande](media-services-manage-origins.md#scale_streaming_endpoints/).
- Configurez « stratégie de service de clé en clair aes par défaut » ou « stratégie de service de licence playready par défaut ». Pour plus d’informations, consultez la page [Configuration de la stratégie d’autorisation de clé de contenu](media-services-portal-configure-content-key-auth-policy.md).  


	Lorsque vous êtes prêt à activer le chiffrement, appuyez sur le bouton **CRYPTAGE** au bas de la page **CONTENU**.

	![Chiffrer][encrypt]

	Après avoir activé le chiffrement, lorsqu’un lecteur demande un flux de données, Media Services utilise la clé spécifiée pour chiffrer dynamiquement votre contenu à l’aide du chiffrement AES ou PlayReady. Pour déchiffrer le flux de données, le lecteur demande la clé au service de remise de clé. Pour déterminer si l’utilisateur est autorisé à obtenir la clé, le service évalue les stratégies d’autorisation que vous avez spécifiées pour la clé.

##<a id="publish"></a>Publication de contenu

> [AZURE.SELECTOR]
- [.NET](media-services-deliver-streaming-content.md)
- [REST](media-services-rest-deliver-streaming-content.md)
- [Portal](media-services-manage-content.md#publish)

###Vue d’ensemble

Pour fournir aux utilisateurs une URL pouvant être utilisée pour diffuser en continu ou télécharger votre contenu, vous devez d’abord « publier » votre élément multimédia en créant un localisateur. Les localisateurs assurent l’accès aux fichiers contenus dans l’élément multimédia. Media Services prend en charge deux types de localisateurs : les localisateurs OnDemandOrigin, utilisés pour diffuser du contenu multimédia (par exemple, MPEG DASH, HLS ou Smooth Streaming) et les localisateurs d’URL SAS (signature d’accès partagé), utilisés pour télécharger des fichiers multimédias.

Lorsque vous utilisez le portail de gestion Azure pour publier vos éléments multimédias, les localisateurs sont créés pour vous et une URL basée sur le localisateur OnDemandOrigin (si votre élément multimédia contient un fichier .ism) ou une URL SAP vous est fournie.

Une URL SAS a le format suivant :

	{blob container name}/{asset name}/{file name}/{SAS signature}

Les URL de diffusion en continu, que vous pouvez utiliser pour lire des éléments multimédias Smooth Streaming, ont le format suivant :

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest

Pour créer une URL de diffusion en continu HLS, ajoutez (format=m3u8-aapl) à l’URL.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=m3u8-aapl)

Pour créer une URL de diffusion en continu MPEG DASH, ajoutez (format=mpd-time-csf) à l’URL.

	{streaming endpoint name-media services account name}.streaming.mediaservices.windows.net/{locator ID}/{filename}.ism/Manifest(format=mpd-time-csf)


Les localisateurs ont une date d’expiration. Lorsque vous utilisez le portail pour publier vos ressources, des localisateurs présentant une date d’expiration de 100 ans sont créés.

>[AZURE.NOTE]Si vous avez utilisé le portail pour créer des localisateurs avant mars 2015, des localisateurs présentant une date d’expiration de deux ans ont été créés.

Pour mettre à jour la date d’expiration d’un localisateur, utilisez l’[API REST](http://msdn.microsoft.com/library/azure/hh974308.aspx#update_a_locator) ou [.NET](http://go.microsoft.com/fwlink/?LinkID=533259). Notez que lorsque vous mettez à jour la date d’expiration d’un localisateur SAS, l’URL est modifiée.

###Publier

Pour utiliser le portail pour publier un élément multimédia, procédez comme suit :

1. Sélectionnez l’élément multimédia. 
2. Puis, cliquez sur le bouton Publier. 
	
 ![PublishedContent][publishedcontent]


## Lecture de contenu sur le portail

Le **portail de gestion Azure** propose un lecteur de contenu que vous pouvez utiliser pour tester vos vidéos.

Cliquez sur la vidéo de votre choix, puis sur le bouton **Lire** situé en bas du portail.
 
Certaines considérations s’appliquent :

- Assurez-vous que la vidéo a été publiée.
- Le **lecteur de contenu de Media Services** assure la lecture depuis le point de terminaison de diffusion en continu par défaut. Si vous souhaitez lire à partir d’un autre point de terminaison de diffusion en continu que celui par défaut, utilisez un autre lecteur, par exemple, le [lecteur Azure Media Services](http://amsplayer.azurewebsites.net/azuremediaplayer.html).
 

![AMSPlayer][AMSPlayer]

<!-- Images -->
[portaloverview]: ./media/media-services-manage-content/media-services-content-page.png
[publishedcontent]: ./media/media-services-manage-content/media-services-upload-content-published.png
[uploadcontent]: ./media/media-services-manage-content/UploadContent.png
[status]: ./media/media-services-manage-content/Status.png
[encoder]: ./media/media-services-manage-content/EncoderDialog2.png
[branding]: ./media/branding-reporting.png
[contentpage]: ./media/media-services-manage-content/media-services-content-page.png
[process]: ./media/media-services-manage-content/media-services-process-video.png
[process2]: ./media/media-services-manage-content/media-services-process-video2.png
[encrypt]: ./media/media-services-manage-content/media-services-encrypt-content.png
[AMSPlayer]: ./media/media-services-manage-content/media-services-portal-player.png

<!---HONumber=August15_HO6-->